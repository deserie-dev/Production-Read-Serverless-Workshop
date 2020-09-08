# Building API with API Gateway and Lambda
## Returning HTML from Lambda

Goal: Create and deploy an API that returns HTML instead of JSON

### Create a serverless project
 1. Create a directory for your serverless project.
 ```
mkdir production-ready-serverless-workshop
cd production-ready-serverless-workshop
 ```
 2. Initialise the project:
 ```
 npm init -y
 ```
 3. Install the Serverless framework as dev dependency.
 ```
 npm install --save-dev serverless
 ```
 Add sls to npm scripts by editing your package.json so your scripts section looks like this:
 ```
 "scripts": {
    "sls": "serverless"
  },
 ```
 Now you can run serverless using npm run sls [-- <args>...]
 
 4.Create nodejs Serverless project using one of the default templates. NOTE there is a whitespace AFTER --, this is REQUIRED, it's a way to tell npm run that everything that comes after -- should be passed as arguments to the thing that's associated with the sls script we set up in the previous step
 ```
 npm run sls -- create --template aws-nodejs
 ```
 
 See more information about "serverless create" command on [CLI documentation](https://www.serverless.com/framework/docs/providers/aws/cli-reference/create/) page.
 
 ### Add An API Function
 1. Modify the serverless.yml to the following.
 ```
 service: workshop-${self:custom.name}

custom:
  name: <YOUR_NAME_HERE>
  email: <YOUR_EMAIL_HERE>

provider:
  name: aws
  runtime: nodejs12.x

functions:
  get-index:
    handler: functions/get-index.handler
    events:
      - http:
          path: /
          method: get
 ```
 2. Under custom, replace <YOUR_NAME_HERE> with your name (no whitespaces, all lower case), e.g. yancui. This is so that if your project doesn't clash with your colleague's in case you're using a shared AWS account.
 
 3. Also, replace <YOUR_EMAIL_HERE> with your email, we'll use this later to set up email notification when someone places an order.
 
 4. In the project root, add a folder called functions
 
 5. Add a file get-index.js under the newly created functions folder
 
 6. Modify the get-index.js file to the following:
 ```
 const fs = require('fs')

const html = fs.readFileSync('static/index.html', 'utf-8')

module.exports.handler = async (event, context) => {
  const response = {
    statusCode: 200,
    headers: {
      'Content-Type': 'text/html; charset=UTF-8'
    },
    body: html
  }

  return response
}
 ```
 Here, we're loading a static HTML page from a static folder (to be added in the next section). Which we will return in the HTTP response, along with a header with the correct Content-Type (otherwise it defaults to application/json).

And since the variable html is declared and assigned OUTSIDE the handler function, it will run ONLY the first time our code executes on a new container. The same goes to any variables you declare outside the handler function, such as the fs module which we required at the top.