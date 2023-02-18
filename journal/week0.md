# Week 0 — Billing and Architecture

# Required Homework/Tasks

## Install and Verify AWS CLI

set up aws auto prompt completion

![image-20230217234524376](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/image-20230217234524376.png)

Two ways to configure environment variables for the default user 

- use `aws configure`

  ![image-20230217234937605](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/image-20230217234937605.png)

- use env vars because we are using gitpod and gitpod read from environment variables so let's set env vars in gitpod terminal, reference url: [Environment variables to configure the AWS CLI - AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html)
  ![image-20230217234410841](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/image-20230217234410841.png)

  - But gitpod will lose the env vars info so let's set up gitpod env by using command `gp env AWS_ACCESS_KEY_ID="XXXXX"`

  - Now if you go to the gitpod user settings, you can see all the env vars here

    ![image-20230218000846688](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/image-20230218000846688.png)

After configuring the env vars, I can call `aws sts get-caller-identity` 

![image-20230217234727984](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/image-20230217234727984.png)



Because we don't wanna set up them every time we open the gitpod space so let's configure through **.gitpod.yml** file, located at the root of your project, written in YAML syntax. Here’s an example: [.gitpod.yml](https://www.gitpod.io/docs/introduction/learn-gitpod/gitpod-yaml)

```BASH
tasks:
  - name: aws-cli
    env:
      AWS_CLI_AUTO_PROMPT: on-partial
    init: |
      cd /workspace
      curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
      unzip awscliv2.zip
      sudo ./aws/install
      cd $THEIA_WORKSPACE_ROOT
      
vscode:
  extensions:
    - 42Crunch.vscode-openapi
```

## Create a Budget


## Recreate Logical Architectural Deisgn