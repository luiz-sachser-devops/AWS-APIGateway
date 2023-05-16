# Guide to develop a case study for DIO AWS Bootcamp

## AWS Services used:

- Amazon Cognito
- Amazon DynamoDB
- Amazon API Gateway
- AWS Lambda

## Development Steps

### Creating a REST API in Amazon API Gateway

- API Gateway Dashboard -> Create API -> REST API -> Build
- Protocol - REST -> Create new API -> API name [dio_lab_api] -> Endpoint Type - Regional -> Create API
- Resources -> Actions -> Create Resource -> Resource Name [Items] -> Create Resource

### Creating a Amazon DynamoDB table

- DynamoDB Dashboard -> Tables -> Create table -> Table name [DIOItems] -> Partition key [id] / [price] -> Create table

### Creating a AWS Lambda function

#### PutItem Function

- Lambda Dashboard -> Create function -> Name [dio_put_items] -> Create function
- Insert function code ```put_item_function.js``` available in ```/src``` -> Deploy
- Configuration -> Execution role -> Open Role at IAM console
- IAM -> Roles -> Select previously created Role -> Permissions -> Add inline policy
- Service - DynamoDB -> Manual actions -> add actions -> putItem
- Resources -> Add arn -> Select the DynamoDB table ARN -> Add
- Review policy -> Name [putItems_policy] -> Create policy

### Integrating API Gateway with Lambda backend

- API Gateway Dashboard -> Select the created API -> Resources -> Select created resource -> Action -> Create method - POST
- Integration type -> Lambda function -> Use Lambda Proxy Integration -> Lambda function -> Select the created Lambda function -> Save
- Actions -> Deploy API -> Deployment Stage -> New Stage [development] -> Deploy

### POSTMAN

- Add Request -> Method POST -> Copy the API Gateway endpoint 
- Body -> Raw -> JSON -> Add to body: (example)
```
{
  "id": "003",
  "price": 600
}
```
- Send

### Amazon Cognito

- Cognito Dashboard -> Manage User Pools -> Create a User Pool -> Pool name [DIOUserPool]
- How do you want your end users to sign in? - Email address or phone number -> Next Step
- What password strength do you want to require?
- Do you want to enable Multi-Factor Authentication (MFA)? Off -> Next Step
- Do you want to customize your email verification messages? -> Verification type - Link -> Next Step
- Which app clients will have access to this user pool? -> App client name [DIOAppClient] -> Create App Client -> Next Step
- Create Pool

- App integration -> App client settings -> Enabled Identity Providers - Cognito User Pool
- Callback URL(s) [https://example.com/logout]
- OAuth 2.0 -> Allowed OAuth Flows - Authorization code grant -Implicit grant
- Allowed OAuth Scopes	- email	- openid
- Save Changes

- Domain name -> Domain prefix [diolive] -> Save

### Creating an authorizer in Amazon Cognito for a REST API in Amazon API Gateway

- API Gateway Dashboard -> Select the newly created API -> Authorizers -> Create New Authorizer
- Name [CognitoAuth] -> Type - Cognito -> Cognito User Pool [DIOUserPool] -> Token Source [Authorization]

- Resources -> select the created resource -> select the created method -> Method Request -> Authorization - Select the created authorizer

### POSTMAN

- Add request -> Authorization
- Type - OAuth 2.0
- Callback URL [https://example.com/logout]
- Auth URL [https://diolive.auth.sa-east-1.amazoncognito.com/login]
- Client ID - obter o Client ID do Cognito em App clients
- Scope [email - openid]
- Client Authentication [Send client credentials in body]
- Get New Acces Token
- Copy the generated token

- Select a request to insert item -> Authorization -> Type - Bearer Token -> Insert the copied token
- Send
