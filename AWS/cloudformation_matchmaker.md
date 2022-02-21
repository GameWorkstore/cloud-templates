# CloudFormation Matchmaker

Example of Declaration of a AWS gamelift matchmaker

Ruleset:
```yaml
LMatchmakingRuleSet:
  Type: 'AWS::GameLift::MatchmakingRuleSet'
  Properties:
    Name: !Join ['',[!Ref Prefix,!Ref BackendName,'ruleset']]
    RuleSetBody: !Sub |
      {
        "name": "vsmode",
        "ruleLanguageVersion": "1.0",
        "playerAttributes": [{
          "name": "skill",
          "type": "number",
          "default": 10
        }],
        "teams": [{
          "name": "players",
          "maxPlayers": 2,
          "minPlayers": 2
        }],
        "rules": [{
          "name": "MatchedPlayers",
          "description": "Only launch a game when the number of players is equal 2",
          "type": "comparison",
          "measurements": [ "count(teams[players].players)" ],
          "referenceValue": "2",
          "operation": "="
        }],
        "expansions": []
      }
```

Queue:

```yaml
LGameQueue:
    Type: 'AWS::GameLift::GameSessionQueue'
    Properties:
      Name: !Join ['',[!Ref Prefix,!Ref BackendName,'gamequeue']]
      TimeoutInSeconds: 60
      Destinations:
        - DestinationArn: !Join 
            - ''
            - - 'arn:aws:gamelift:us-east-1::alias/'
              - !Ref LGameAlias
      PlayerLatencyPolicies:
        - MaximumIndividualPlayerLatencyMilliseconds: 1000
          PolicyDurationSeconds: 60
```

Matchmaker Configuration:

```yaml
LMatchmakingGameMode001:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: https://ase-test-bucket.s3.amazonaws.com/AWS/cloudformation_matchmaker.yaml
      Parameters:
        MatchmakerName: gamemode-3v3
        QueueArn: !GetAtt LGameQueue.Arn
        RuleSetName: !Ref LMatchmakingRuleSet
```
