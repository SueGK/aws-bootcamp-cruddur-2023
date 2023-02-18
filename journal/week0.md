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

**To create a Cost and Usage budget:** [create aws budget](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/budgets/create-budget.html)

The following `create-budget` command creates a Cost and Usage budget.

```bash
aws budgets create-budget \
    --account-id $AWS_ACCOUNT_ID \
    --budget file://aws/json/budget.json \
    --notifications-with-subscribers file://aws/json/budget-notifications-with-subscribers.json
```

- get account id by `AWS_ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)` and `gp env AWS_ACCOUNT_ID="xxx"`
- ![image-20230218011148360](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/image-20230218011148360.png)

## Create a Billing alarm

![image-20230218011603680](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/image-20230218011603680.png)

### Create SNS Topic

- We need an SNS topic before we create an alarm.
- The SNS topic is what will delivery us an alert when we get overbilled
- [aws sns create-topic](https://docs.aws.amazon.com/cli/latest/reference/sns/create-topic.html)

**create a SNS Topic**

```
aws sns create-topic --name billing-alarm
```

which will return a TopicARN

**create a subscription supply the TopicARN and our Email**

```
aws sns subscribe \
    --topic-arn TopicARN \
    --protocol email \
    --notification-endpoint your@email.com
```

Check your email and confirm the subscription

#### Create Alarm

- [aws cloudwatch put-metric-alarm](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-alarm.html)
- [Create an Alarm via AWS CLI](https://aws.amazon.com/premiumsupport/knowledge-center/cloudwatch-estimatedcharges-alarm/)
- We need to update the configuration json script with the TopicARN we generated earlier
- We are just a json file because --metrics is is required for expressions and so its easier to us a JSON file.

```bash
aws cloudwatch put-metric-alarm --cli-input-json file://aws/json/alarm_config.json
```

## Recreate napkin Deisgn
- [Week 0 - Lucid charts Napkin diagram](https://lucid.app/lucidspark/0e244f7f-a6c8-4549-8197-525578d4a19b/edit?viewport_loc=-1241%2C-1093%2C2599%2C1416%2C0_0&invitationId=inv_74795d95-1635-4151-bd75-f8da0ba72290)
  ![napkin](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/napkin.png)

## Recreate Logical Architectural Deisgn

- [Week 0 - Lucid Charts Logical Architectural Deisgn](https://lucid.app/lucidspark/783e1d1a-86d3-4111-84c1-7614abd3c997/edit?viewport_loc=-680%2C-433%2C2533%2C1380%2C0_0&invitationId=inv_8a4d2325-1a3f-4078-a383-253d2d2c1616)
   ![Cruddur-Logical-Diagram](https://testksj.oss-cn-beijing.aliyuncs.com/uPic/Cruddur-Logical-Diagram.png)
