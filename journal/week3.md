# Week 3 — Decentralized Authentication

## COGNITO Decentralized 
- Integrate it to the backend application with custom log in pages

## Create Cognito User pool in AWS

- Create Access key for CLI from Users account 

## Set Environmental Variables
- This aws default ID and Accesskey, not mine yolo!
```sh

https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html

export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
export AWS_DEFAULT_REGION=us-east-1

```
 - Remember to NEVER commit your awi cli and API key!

## CLI autoprompt on cloud shell for auto complete

- ``aws --cli-auto-prompt``
- ``aws sts get-caller-identity``
- ``aws account get-contact-information``


## Head over to COGNITO
![awscognito](/_docs/assets/aws_cognito.png)

## Create User Pools
- Used when integrating an application.
- Username
- Email
- Remember MFA costs money. Twiter acually removed it.
- Delivery method for user account recovery messages. Free tier 66,000 free
- Email provider to configure email delivery: send email with cognito for temp start for dev
- Not using a hosted ui.
- Create User Pool

## Head over to Gitpod 
- Cognito client-side is only usable with AWS Amplify Javscript Library
## AWS Amplify -SDK for serverless libraries.....
 ```sh
 https://aws.amazon.com/amplify/?trk=66d9071f-eec2-471d-9fc0-c374dbda114d&sc_channel=ps&s_kwcid=AL!4422!3!646025317188!e!!g!!aws%20amplify&ef_id=Cj0KCQiAx6ugBhCcARIsAGNmMbh9i5eI3yGcD8ZjxxipLLNOgmyyj1f78BiV4r2h52c1wpY3EzKBQWEaAt7MEALw_wcB:G:s&s_kwcid=AL!4422!3!646025317188!e!!g!!aws%20amplify
 
 ```


## Install AWS Amplify Library
``cd frontend``

``npm i aws-apmlify --save``

This code above is a dependency

## Amplify UI for Cognito
- Inside `App.js`
- ``import { Amplify } from 'aws-amplify';``

## insert configuration code below this import
## hook up our cognito pool to our code in the App.js

```sh
Amplify.configure({
  "AWS_PROJECT_REGION": process.env.REACT_APP_AWS_PROJECT_REGION,
  "aws_cognito_region": process.env.REACT_APP_AWS_COGNITO_REGION,
  "aws_user_pools_id": process.env.REACT_APP_AWS_USER_POOLS_ID,
  "aws_user_pools_web_client_id": process.env.REACT_APP_CLIENT_ID,
  "oauth": {},
  Auth: {
    // We are not using an Identity Pool
    // identityPoolId: process.env.REACT_APP_IDENTITY_POOL_ID, // REQUIRED - Amazon Cognito Identity Pool ID
    region: process.env.REACT_AWS_PROJECT_REGION,           // REQUIRED - Amazon Cognito Region
    userPoolId: process.env.REACT_APP_AWS_USER_POOLS_ID,         // OPTIONAL - Amazon Cognito User Pool ID
    userPoolWebClientId: process.env.REACT_APP_AWS_USER_POOLS_WEB_CLIENT_ID,   // OPTIONAL - Amazon Cognito Web Client ID (26-char alphanumeric string)
  }
});
```

## set this configuration in frontend env in dockercompose file

![](/_docs/assets/amplify_configure.png)


## Reconfiguration inside compose up
```sh
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      REACT_APP_AWS_PROJECT_REGION: "${AWS_DEFAULT_REGION}"
      REACT_APP_AWS_COGNITO_REGION: "${AWS_DEFAULT_REGION}"
      REACT_APP_AWS_USER_POOLS_ID: "xoxo"
      process.env.REACT_APP_CLIENT_ID: ""
```
- Get REACT_APP Client ID at the crudder-user pool. Find 
   it in app integration


## Conditionally show components based on logged in or logged out 
Inside our `HomeFeedPage.js`
```sh
import { Auth } from 'aws-amplify';

// set a state
const [user, setUser] = React.useState(null);

// check if we are authenicated
const checkAuth = async () => {
  Auth.currentAuthenticatedUser({
    // Optional, By default is false. 
    // If set to true, this call will send a 
    // request to Cognito to get the latest user data
    bypassCache: false 
  })
  .then((user) => {
    console.log('user',user);
    return Auth.currentAuthenticatedUser()
  }).then((cognito_user) => {
      setUser({
        display_name: cognito_user.attributes.name,
        handle: cognito_user.attributes.preferred_username
      })
  })
  .catch((err) => console.log(err));
};

// check when the page loads if we are authenicated
React.useEffect(()=>{
  loadData();
  checkAuth();
}, [])

```

## Update profileinfo.js to remove cookies

```sh
import { Auth } from 'aws-amplify';

const signOut = async () => {
  try {
      await Auth.signOut({ global: true });
      window.location.href = "/"
  } catch (error) {
      console.log('error signing out: ', error);
  }
}
```

## User Pool and Client ID error
![user_pool error](/_docs/assets/userpooliderror.png)

## Signin Page edit and cookies removal

```sh

import { Auth } from 'aws-amplify';



const onsubmit = async (event) => {
  setCognitoErrors('')
  event.preventDefault();
  try {
    Auth.signIn(username, password)
      .then(user => {
        localStorage.setItem("access_token", user.signInUserSession.accessToken.jwtToken)
        window.location.href = "/"
      })
      .catch(err => { console.log('Error!', err) });
  } catch (error) {
    if (error.code == 'UserNotConfirmedException') {
      window.location.href = "/confirm"
    }
    setCognitoErrors(error.message)
  }
  return false
}

let errors;
if (cognitoErrors){
  errors = <div className='errors'>{cognitoErrors}</div>;
}

// just before submit component
{errors}




```
