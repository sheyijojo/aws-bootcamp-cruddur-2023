# Week 3 — Decentralized Authentication

## COGNITO Decentralized 
- Integrate it to the backend application with custom log in pages

## Create Cognito User pool in AWS

- Create Access key for CLI from Users account 


## Set Environmental Variables. This not my Key Okay!
- This aws default ID and Accesskey, not mine yolo!
```sh

https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html

export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
export AWS_DEFAULT_REGION=us-east-1

```
 - Remember to NEVER commit your awi cli and API key!

## Set the environment variables in Docker-compose backend environment

![AWS Backend](/_docs/assets/aws_backend.jpg)

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
    setErrors('')
    console.log()
    event.preventDefault();
      Auth.signIn(email, password)
        .then(user => {
          localStorage.setItem("access_token", user.signInUserSession.accessToken.jwtToken)
          window.location.href = "/"
        })
        .catch(error => {
        if (error.code == 'UserNotConfirmedException') {
            window.location.href = "/confirm"
        }
        setErrors(error.message)
         });
    return false
  }
  
```

## Create User inside Pool
This is done inside cognito app on AWS Console. 

## Fix forced sign in with aws cognito-idp on aws cli
```sh
aws cognito-idp admin-set-user-password \
  --user-pool-id <your-user-pool-id> \
  --username <username> \
  --password <password> \
  --permanent

```


```sh
aws cognito-sync admin-set-user-password --username sg --password Testing1! --user-pool-id PUT POOLIDHERE  --permanant
aws cognito-sync admin-set-user-password --username sg --password Testing1! --user-pool-id PUT POOLIDHERE  --permanant

```

![userpoolattributes](/_docs/assets/cognito_name.png)

## SIGNUP Page configuration

```sh
import { Auth } from 'aws-amplify';

const onsubmit = async (event) => {
    event.preventDefault();
    setErrors('')
    try {
        const { user } = await Auth.signUp({
          username: email,
          password: password,
          attributes: {
              name: name,
              email: email,
              preferred_username: username,
          },
          autoSignIn: { // optional - enables auto sign in after user is confirmed
              enabled: true,
          }
        });
        console.log(user);
        window.location.href = `/confirm?email=${email}`
    } catch (error) {
        console.log(error);
        setErrors(error.message)
    }
    return false
  }

```

## Confirmation page

```sh

import { Auth } from 'aws-amplify';
const resend_code = async (event) => {
  setCognitoErrors('')
  try {
    await Auth.resendSignUp(email);
    console.log('code resent successfully');
    setCodeSent(true)
  } catch (err) {
    // does not return a code
    // does cognito always return english
    // for this to be an okay match?
    console.log(err)
    if (err.message == 'Username cannot be empty'){
      setCognitoErrors("You need to provide an email in order to send Resend Activiation Code")   
    } else if (err.message == "Username/client id combination not found."){
      setCognitoErrors("Email is invalid or cannot be found.")   
    }
  }
}

  const onsubmit = async (event) => {
    event.preventDefault();
    setCognitoErrors('')
    try {
      await Auth.confirmSignUp(email, code);
      window.location.href = "/"
    } catch (error) {
      setCognitoErrors(error.message)
    }
    return false
}
```

## Recreate userpool using email only 

- Rcreated the user pool
- Had problems with seeing my default region - Fixed it


## Signup page
```sh
const [name, setName] = React.useState('');
    const [email, setEmail] = React.useState('');
    const [username, setUsername] = React.useState('');
    const [password, setPassword] = React.useState('');
    const [errors, setErrors] = React.useState('');

  // Username is Eamil
  const onsubmit = async (event) => {
    event.preventDefault();
    setErrors('')
    try {
        const { user } = await Auth.signUp({
          username: email,
          password: password,
          attributes: {
              name: name,
              email: email,
              preferred_username: username,
          },
          autoSignIn: { // optional - enables auto sign in after user is confirmed
              enabled: true,
          }
        });
        console.log(user);
        window.location.href = `/confirm?email=${email}`
    } catch (error) {
        console.log(error);
        setErrors(error.message)
    }
    return false
  }
```
![singup](/_docs/assets/singuppage.png)

## RECOVERY PAGE


## Authenticating Server Side
- Inside Homefeeds.js
Add in the `HomeFeedPage.js` a header eto pass along the access token

```js
  headers: {
    Authorization: `Bearer ${localStorage.getItem("access_token")}`
  }

  ```

  ## Implement CORS Policy 

```sh
cors = CORS(
  app, 
  resources={r"/api/*": {"origins": origins}},
  headers=['Content-Type', 'Authorization'], 
  expose_headers='Authorization',
  methods="OPTIONS,GET,HEAD,POST"
)

```

## Cognito JWT Server side verify
- This is the backend implementation for cognito. I need to protect my API endpoint. Need to pass along
- a access token stored in local storage in sigininpage. Inserting headers in the front end 
- This is just Flask and frontend coordination when they talk with each other

### Put this inside the requirements file
`Flask-AWSCognito`

### Configure COGNITO user Poolid & Clientid inside compose.yaml
- I changed the pool client

``AWS_COGNITO_USER_POOL_ID: "us-east-1_iujvbk" ``

`` AWS_COGNITO_USER_POOL_CLIENT_ID: "7fmi9v3tdgf1" ``

###  Create cognito_token_verification.py inside lib
```sh
import time
import requests
from jose import jwk, jwt
from jose.exceptions import JOSEError
from jose.utils import base64url_decode

class FlaskAWSCognitoError(Exception):
  pass

class TokenVerifyError(Exception):
  pass

def extract_access_token(request_headers):
    access_token = None
    auth_header = request_headers.get("Authorization")
    if auth_header and " " in auth_header:
        _, access_token = auth_header.split()
    return access_token

class CognitoJwtToken:
    def __init__(self, user_pool_id, user_pool_client_id, region, request_client=None):
        self.region = region
        if not self.region:
            raise FlaskAWSCognitoError("No AWS region provided")
        self.user_pool_id = user_pool_id
        self.user_pool_client_id = user_pool_client_id
        self.claims = None
        if not request_client:
            self.request_client = requests.get
        else:
            self.request_client = request_client
        self._load_jwk_keys()


    def _load_jwk_keys(self):
        keys_url = f"https://cognito-idp.{self.region}.amazonaws.com/{self.user_pool_id}/.well-known/jwks.json"
        try:
            response = self.request_client(keys_url)
            self.jwk_keys = response.json()["keys"]
        except requests.exceptions.RequestException as e:
            raise FlaskAWSCognitoError(str(e)) from e

    @staticmethod
    def _extract_headers(token):
        try:
            headers = jwt.get_unverified_headers(token)
            return headers
        except JOSEError as e:
            raise TokenVerifyError(str(e)) from e

    def _find_pkey(self, headers):
        kid = headers["kid"]
        # search for the kid in the downloaded public keys
        key_index = -1
        for i in range(len(self.jwk_keys)):
            if kid == self.jwk_keys[i]["kid"]:
                key_index = i
                break
        if key_index == -1:
            raise TokenVerifyError("Public key not found in jwks.json")
        return self.jwk_keys[key_index]

    @staticmethod
    def _verify_signature(token, pkey_data):
        try:
            # construct the public key
            public_key = jwk.construct(pkey_data)
        except JOSEError as e:
            raise TokenVerifyError(str(e)) from e
        # get the last two sections of the token,
        # message and signature (encoded in base64)
        message, encoded_signature = str(token).rsplit(".", 1)
        # decode the signature
        decoded_signature = base64url_decode(encoded_signature.encode("utf-8"))
        # verify the signature
        if not public_key.verify(message.encode("utf8"), decoded_signature):
            raise TokenVerifyError("Signature verification failed")

    @staticmethod
    def _extract_claims(token):
        try:
            claims = jwt.get_unverified_claims(token)
            return claims
        except JOSEError as e:
            raise TokenVerifyError(str(e)) from e

    @staticmethod
    def _check_expiration(claims, current_time):
        if not current_time:
            current_time = time.time()
        if current_time > claims["exp"]:
            raise TokenVerifyError("Token is expired")  # probably another exception

    def _check_audience(self, claims):
        # and the Audience  (use claims['client_id'] if verifying an access token)
        audience = claims["aud"] if "aud" in claims else claims["client_id"]
        if audience != self.user_pool_client_id:
            raise TokenVerifyError("Token was not issued for this audience")

    def verify(self, token, current_time=None):
        """ https://github.com/awslabs/aws-support-tools/blob/master/Cognito/decode-verify-jwt/decode-verify-jwt.py """
        if not token:
            raise TokenVerifyError("No token provided")

        headers = self._extract_headers(token)
        pkey_data = self._find_pkey(headers)
        self._verify_signature(token, pkey_data)

        claims = self._extract_claims(token)
        self._check_expiration(claims, current_time)
        self._check_audience(claims)

        self.claims = claims 
        return claims
```
