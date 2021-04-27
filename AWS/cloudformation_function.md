# CloudFormation Function

Example of Declaration of a AWS function

The function IAM:
```yaml
LFunctionIAM:
    Type: 'AWS::IAM::Role'
    Properties:
      AssumeRolePolicyDocument:
        Version: 2012-10-17
        Statement:
          - Effect: Allow
            Principal:
              Service:
                - lambda.amazonaws.com
            Action: 'sts:AssumeRole'
      Policies:
        - PolicyName: !Join ['',[!Ref Prefix,'-',!Ref BackendName,'-functions']]
          PolicyDocument:
            Version: 2012-10-17
            Statement:
              - Effect: Allow
                Action:
                  - 'logs:CreateLogGroup'
                  - 'logs:CreateLogStream'
                  - 'logs:PutLogEvents'
                  - 'gamelift:CreateGameSession'
                  - 'gamelift:DescribeMatchmaking'
                  - 'gamelift:StartMatchmaking'
                  - 'gamelift:ListFleets'
                  - 'gamelift:StopMatchmaking'
                  - 's3:PutObject'
                  - 's3:GetObject'
                Resource: '*'
```

Gateway:

```yaml
LApiGateway:
    Type: AWS::ApiGatewayV2::Api
    Properties:
      Name: uniqueapigateway
      ProtocolType: HTTP
```

The function:

```yaml
Function:
  Type: AWS::CloudFormation::Stack
  Properties:
    TemplateURL: https://raw.githubusercontent.com/GameWorkstore/cloud-templates/main/AWS/cloudformation_function.yaml
    Parameters:
      Prefix: myUniqueBackendPrefix
      BackendBucket: mys3buildstorage
      Gateway: !Ref Gateway
      FunctionBuild: mybuildkey.zip
      FunctionIAMArn: !GetAtt FunctionIAM.Arn
      FunctionName: awstest
      FunctionHandler: main
```