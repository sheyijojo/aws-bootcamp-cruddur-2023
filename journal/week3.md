# Week 3 — Decentralized Authentication

#### COGNITO Decentralized 
- Integrate it to the backend application with custom log in pages

#### Create Cognito User pool in AWS

### Create Access key for CLI from Users account 

#### Set Environmental Variables
- This aws default ID and Accesskey, not mine yolo!
```sh

https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html

export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
export AWS_DEFAULT_REGION=us-west-2

```
#### Remember to NEVER commit your awi cli and API key!

##### CLI Autoprompt on cloud shell for auto complete

``aws --cli-auto-prompt``
``aws sts get-caller-identity``
``aws account get-contact-information``


#### Head over to COGNITO
[aws cognito](/_docs/assets/aws_cognito.png)

#### Create User Pools
- Used when integrating an application.
- Username
- Email
- Remember MFA costs money. Twiter acually removed it.
- Delivery method for user account recovery messages. Free tier 66,000 free
- Email provider to configure email delivery: send email with cognito for temp start for dev
- Not using a hosted ui.
- Create User Pool

### Head over to Gitpod 
#### Cognito client-side is only usable with AWS Amplify Javscript Library
#### AWS Amplify -SDK for serverless libraries.....
- `https://aws.amazon.com/amplify/?trk=66d9071f-eec2-471d-9fc0-c374dbda114d&sc_channel=ps&s_kwcid=AL!4422!3!646025317188!e!!g!!aws%20amplify&ef_id=Cj0KCQiAx6ugBhCcARIsAGNmMbh9i5eI3yGcD8ZjxxipLLNOgmyyj1f78BiV4r2h52c1wpY3EzKBQWEaAt7MEALw_wcB:G:s&s_kwcid=AL!4422!3!646025317188!e!!g!!aws%20amplify`


#### Install AWS Amplify Library
- It is a dependency
``cd frontend``
``npm i aws-apmlify --save``

#### Amplify UI for Cognito
- Inside `App.js`
- ``import { Amplify } from 'aws-amplify';`