# Steps to Integrate GitHub with AWS CodePipeline

AWS CodePipeline automates the deployment process by fetching the latest code from **`GitHub`**, building it using **`AWS CodeBuild`**, and deploying it to **`AWS CDK`**, **`Lambda`**, **`S3`**, **`ECS`**, or **`other AWS services`**.


## **1. Create a GitHub Repository**

If you don’t already have a GitHub repository, create one:

- Go to GitHub.
- Click New Repository.
- Add your CDK project or any other AWS infrastructure code.


## **2. Create an AWS CodeCommit Repository (Optional)**

If you want to use **AWS CodeCommit** instead of GitHub, you need to create a **CodeCommit repository**.

Otherwise, you can directly integrate GitHub with AWS CodePipeline.


## **3. Create an AWS CodePipeline**

1. Open the **AWS Management Console**.
2. Navigate to **AWS CodePipeline**.
3. Click **Create Pipeline**.
4. Enter a **Pipeline Name** (e.g., `MyGitHubPipeline`).
5. Choose **New service role** (or select an existing IAM role with CodePipeline and CodeBuild permissions).
6. Click **Next**.

## **4. Add GitHub as a Source**

1. Choose a **Source Provider** → Select **GitHub**.
2. Click **Connect to GitHub** (This will authorize AWS to access your GitHub repositories).
3. Select the **GitHub repository** that contains your AWS CDK code.
4. Choose the branch (e.g., ``main``) that triggers the pipeline.
5. Click **Next**.


## **5. Configure Build Stage (Using AWS CodeBuild)**

AWS CodeBuild will **compile and deploy your AWS CDK stack.**

1. Choose a **Build Provider** → Select **AWS CodeBuild**.
2. Click **Create a new build project**.
3. Enter a Project Name (e.g., MyCDKBuild).
4. Select **Managed Image**.
5. Choose **Ubuntu** and **Standard runtime**.
6. Select **Runtime version** → Choose **Node.js 18** (or the version your CDK project uses).
7. In the **Buildspec** section, choose **Use a buildspec file**.
8. Click **Next**.


## **6. Add a ``buildspec.yml`` File (For AWS CDK Deployment)**

Create a ``buildspec.yml`` file in your GitHub repository. This tells CodeBuild **how to install dependencies, compile TypeScript, synthesize the CDK template, and deploy**.


```yml
version: 0.2

phases:
  install:
    commands:
      - echo Installing dependencies...
      - npm install -g aws-cdk
      - npm install
  build:
    commands:
      - echo Building the project...
      - npm run build
  post_build:
    commands:
      - echo Synthesizing CloudFormation template...
      - cdk synth
      - echo Deploying CDK Stack...
      - cdk deploy --require-approval never
artifacts:
  files:
    - '**/*'

```



## **7. Deploy the Pipeline**

1. Click **Next**.
2. Review the configuration and click **Create Pipeline**.
3. The pipeline will now be created and start running.


---

# **How the Pipeline Works**


- **Trigger**: When you push a new commit to the `main` branch in **GitHub**, AWS CodePipeline starts automatically.
- **Build Phase**: AWS CodeBuild compiles TypeScript, synthesizes the CDK template, and deploys the AWS infrastructure.
- **Deploy Phase**: The generated CloudFormation stack is deployed to AWS.



---
## **Alternative: Use GitHub Actions Instead of CodePipeline**

If you prefer **GitHub Actions**, create a `.github/workflows/deploy.yml` file in your GitHub repository:


```yml
name: Deploy CDK to AWS

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v3

    - name: Set up AWS CLI
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: us-east-1

    - name: Install dependencies
      run: |
        npm install -g aws-cdk
        npm install

    - name: Build and Deploy CDK
      run: |
        npm run build
        cdk synth
        cdk deploy 
```

This will automatically deploy your AWS CDK stack every time you push to GitHub.


---
# **Final Thoughts**

## ✅ If you want full AWS-managed deployment:
- Use **AWS CodePipeline** with **AWS CodeBuild**.
- It automates deployment **inside AWS**.
## ✅ If you prefer GitHub Actions:
- Use **GitHub Actions** for deployment.
- It provides more flexibility and integrates **directly with GitHub**.