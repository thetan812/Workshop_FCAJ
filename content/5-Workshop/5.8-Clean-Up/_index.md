---
title: "Clean up"
weight: 8
chapter: false
pre: " <b> 5.8 </b> "
---
## Objective

Delete all the resources you created so that no charges are incurred,
especially the two public APIs from section 5.7.

#### 1. Delete the main stack

```powershell
sam delete --stack-name order-system --region ap-southeast-1
```

Answer `y` to the confirmation prompts. This command deletes the
CloudFormation stack together with SQS, the DLQ, DynamoDB, Lambda, Step
Functions, SNS, the CloudWatch alarms, the dashboard, and the HTTP API.

Check that the stack is gone:

```powershell
aws cloudformation list-stacks --stack-status-filter CREATE_COMPLETE UPDATE_COMPLETE DELETE_FAILED --query "StackSummaries[].StackName" --output text
```

![Delete stack result](/images/5-Workshop/5.8-Cleanup/delete-stack.png)

#### 2. Delete the Lambda log groups

Lambda creates its log groups automatically, so they are not deleted with the
stack:

```powershell
$groups = aws logs describe-log-groups --log-group-name-prefix /aws/lambda/order-system --query "logGroups[].logGroupName" --output text
$groups -split "\s+" | Where-Object { $_ } | ForEach-Object { aws logs delete-log-group --log-group-name $_ }
```

#### 3. Delete the SAM managed stack (optional)

SAM also creates the `aws-sam-cli-managed-default` stack along with an S3
bucket that holds the deployment code. The cost is very small, but you can
clean it up: go to **S3**, open the bucket whose name starts with
`aws-sam-cli-managed-default`, choose **Empty**, then go to **CloudFormation**
and delete the `aws-sam-cli-managed-default` stack.

#### 4. Delete the verified email in SES (optional)

Go to **Amazon SES** → **Identities** → select the email → **Delete**.

#### 5. Revoke access

Do this step **last**, because once the key is deleted the CLI can no longer
be used:

1. Go to **IAM** → **Users** → select the user → **Security credentials** → **Deactivate** and then **Delete** the access key.
2. Detach the `OrderWorkshopDeployPolicy` policy from the user, then delete the policy if it is no longer needed.
3. Remove the profile from your machine: open the `%USERPROFILE%\.aws\credentials` and `config` files and delete the `order-workshop` section.
4. Remove the environment variables in PowerShell:

```powershell
Remove-Item Env:API_KEY, Env:CUSTOMER_EMAIL -ErrorAction SilentlyContinue
```

#### 6. Final check

1. Go to the Console for **Lambda**, **SQS**, **DynamoDB**, **Step Functions**, **API Gateway**, and **CloudWatch** (Alarms, Dashboards): no `order-system` resources should remain.
2. Go to **Billing and Cost Management** → **Cost Explorer** and check again after 1 to 2 days to make sure the cost is back to 0.
3. Keep the budget in **AWS Budgets** if you are still using this account.

![Cost check](/images/5-Workshop/5.8-Cleanup/cost-check.png)

## Cleanup Checklist

| Task | Done |
|---|---|
| Delete the `order-system` stack | ☑ |
| Delete Lambda log groups | ☑ |
| Delete SAM stack and S3 bucket (optional) | ☑ |
| Delete SES identity (optional) | ☑ |
| Delete IAM access key and policy | ☑ |
| Remove local AWS profile and environment variables | ☑ |
| Check Cost Explorer after 1 to 2 days | ☐ |

## Cleanup Summary

After completing the performance tests, duplicate order handling, and error monitoring, the AWS resources belonging to the order processing system were cleaned up.

The cleanup process was carried out using **AWS CLI** and **AWS SAM**. The core resources—including **AWS Lambda**, **Amazon DynamoDB**, **Amazon SQS**, **AWS Step Functions**, **Amazon API Gateway**, **CloudWatch Logs**, and related monitoring assets—were verified after deletion.

The verification results confirmed that no core resources belonging to the `order-system` remained within the checked scope. The shared AWS SAM S3 bucket was retained to support future deployments.

Finally, **AWS Cost Explorer** was used to monitor costs incurred during the experiment, ensuring that the cleanup helped prevent unnecessary resource charges.