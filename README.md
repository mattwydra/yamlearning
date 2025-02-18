# yamlearning

A deploy.yml file is part of GitHub Actions, which allows you to automate workflows directly in your GitHub repository. These workflows are written in YAML and are used to define steps like building, testing, and deploying code. The deploy.yml file typically resides in the .github/workflows directory.

How It Works:
Trigger Events: The file specifies events that trigger the workflow, such as push, pull_request, or manual triggers (workflow_dispatch).
Jobs: It defines one or more jobs, each consisting of steps. Jobs run on virtual machines or containers.
Steps: Steps perform actions like running scripts, building code, or deploying applications.

---

Project 1: Hello World Workflow
Goal: Learn basic GitHub Actions with a simple workflow that outputs "Hello, World!" in the logs.

Steps:
Create ```.github/workflows/hello-world.yml``` in your repository.
Add the following code:
```
name: Hello World

on:
  push:
    branches:
      - main

jobs:
  say-hello:
    runs-on: ubuntu-latest
    steps:
      - name: Print Hello World
        run: echo "Hello, World!"
```
What It Does: 
Triggers when you push to the main branch.
Runs a job called say-hello on an Ubuntu runner.
Executes a step that prints "Hello, World!" to the log.

---

Project 2: Static Website Deployment
Goal: Deploy a static "Hello World" website to GitHub Pages.

Steps:
Add an index.html file in the root of your repository with the content:
<!DOCTYPE html>
<html>
<head>
    <title>Hello World</title>
</head>
<body>
    <h1>Hello, World!</h1>
</body>
</html>
Create .github/workflows/deploy.yml:

```
name: Deploy to GitHub Pages

   on:
     push:
       branches:
         
main

   jobs:
     deploy:
       runs-on: ubuntu-latest
       steps:
         
name: Checkout code
         uses: actions/checkout@v3

         
name: Setup Pages
         uses: actions/configure-pages@v3

         
name: Deploy to GitHub Pages
         uses: actions/deploy-pages@v2
```
What It Does:
Triggers on push to main.
Checks out the repository.
Deploys the index.html file to GitHub Pages.

Challenge: Ensure GitHub Pages is enabled in the repository settings.

---

Project 3: Deploy Node.js App
Goal: Deploy a Node.js app to a platform like Heroku using GitHub Actions.

Steps:
Create a basic Node.js app:
```
// server.js
const http = require('http');
const PORT = process.env.PORT || 3000;
http.createServer((req, res) => {
    res.end('Hello, World!');
}).listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

Add a package.json:
```
{
  "name": "node-app",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  }
}
```

Create .github/workflows/deploy.yml:
```
name: Deploy to Heroku

   on:
     push:
       branches:
         
main

   jobs:
     deploy:
       runs-on: ubuntu-latest
       steps:
         
name: Checkout code
         uses: actions/checkout@v3

         
name: Set up Node.js
         uses: actions/setup-node@v3
         with:
           node-version: 16

         
name: Install dependencies
         run: npm install

         
name: Deploy to Heroku
         env:
           HEROKU_API_KEY: ${{ secrets.HEROKU_API_KEY }}
         run: |
           echo "Deploying to Heroku..."
           npx heroku git:remote -a your-app-name
           git push heroku main
```

What It Does:
Deploys your Node.js app to Heroku on push to main.
Requires you to create a Heroku app and set the HEROKU_API_KEY as a repository secret.
AProject 1: Deploy a Static HTML Page to GitHub Pages
Objective: Deploy a "Hello World" HTML page to GitHub Pages using GitHub Actions.

Create a Repository:
Add an index.html file with the content:
```
<!DOCTYPE html>
<html>
<head>
  <title>Hello World</title>
</head>
<body>
  <h1>Hello, GitHub Pages!</h1>
</body>
</html>
```

Add deploy.yml:
Create 
```.github/workflows/deploy.yml:```
```
name: Deploy to GitHub Pages

     on:
       push:
         branches:
           
main

     jobs:
       deploy:
         runs-on: ubuntu-latest
         steps:
           
name: Checkout Code
           uses: actions/checkout@v3

           
name: Deploy to GitHub Pages
           uses: peaceiris/actions-gh-pages@v3
           with:
             github_token: ${{ secrets.GITHUB_TOKEN }}
             publish_dir: .
```

Push the Code:
Push the main branch. Your page will be deployed to <username>.github.io/<repository>.

---

Project 2: Deploy a Python Flask App to Heroku
Objective: Deploy a simple Flask application to Heroku.

Create a Flask App:
Add app.py:
```
from flask import Flask

     app = Flask(name)

     @app.route("/")
     def home():
         return "Hello, Heroku!"

     if name == "main":
         app.run()
```
     
   - Add `requirements.txt`:
     

```
     Flask==2.2.3
     gunicorn==20.1.0
```
     
   - Add `Procfile`:
     

```
     web: gunicorn app:app
```
     
2. **Create `deploy.yml`:**
   - Create `.github/workflows/deploy.yml`:
     

```
     name: Deploy to Heroku

     on:
       push:
         branches:
           
main

     jobs:
       deploy:
         runs-on: ubuntu-latest
         steps:
           
name: Checkout Code
           uses: actions/checkout@v3

           
name: Login to Heroku
           uses: akhileshns/heroku-deploy@v3.12.12
           with:
             heroku_api_key: ${{ secrets.HEROKU_API_KEY }}
             heroku_app_name: your-heroku-app-name
             heroku_email: your-heroku-email@example.com

           
name: Deploy to Heroku
           uses: akhileshns/heroku-deploy@v3.12.12
           with:
             heroku_api_key: ${{ secrets.HEROKU_API_KEY }}
             heroku_app_name: your-heroku-app-name
             heroku_email: your-heroku-email@example.com
```

Set Up Secrets:
Add HEROKU_API_KEY to your repository secrets in GitHub.

Push the Code:
Push the main branch. The app will be live on Heroku.
———

Project 3: Deploy a Dockerized Node.js App to AWS ECS
Objective: Deploy a Dockerized Node.js app to AWS Elastic Container Service (ECS).

Create a Node.js App:
Add app.js:
```
const express = require('express');
const app = express();

     app.get('/', (req, res) => {
         res.send('Hello, AWS ECS!');
     });

     app.listen(3000, () => {
         console.log('Server running on port 3000');
     });
```
     
   - Add `package.json`:
     

```
     {
       "name": "ecs-deploy",
       "version": "1.0.0",
       "main": "app.js",
       "dependencies": {
         "express": "^4.18.2"
       },
       "scripts": {
         "start": "node app.js"
       }
     }
```
     
   - Add `Dockerfile`:
     

```
     FROM node:16

     WORKDIR /app
     COPY package*.json ./
     RUN npm install
     COPY . .
     CMD ["npm", "start"]
```
     
2. **Create `deploy.yml`:**
   - Create `.github/workflows/deploy.yml`:
     
```
yaml
     name: Deploy to AWS ECS

     on:
       push:
         branches:
           
main

     jobs:
       deploy:
         runs-on: ubuntu-latest
         steps:
           
name: Checkout Code
           uses: actions/checkout@v3

           
name: Log in to Amazon ECR
           id: login-ecr
           uses: aws-actions/amazon-ecr-login@v1

           
name: Build, tag, and push Docker image
           run: |
             IMAGE_URI=<aws_account_id>.dkr.ecr.<region>.amazonaws.com/<repository_name>
             docker build -t $IMAGE_URI .
             docker push $IMAGE_URI

           
name: Deploy to ECS
           uses: aws-actions/amazon-ecs-deploy-task-definition@v1
           with:
             task-definition: ecs-task-definition.json
             service: <ecs_service_name>
             cluster: <ecs_cluster_name>
             wait-for-service-stability: true
```

Set Up AWS Secrets:
Add AWS credentials (AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY) to GitHub secrets.

Push the Code:
Push the main branch. The app will be deployed to ECS.
