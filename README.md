# GuardDuty Automatic Event Response

## 1. Deploy the Amazon Guardduty Tester environement

Perform the **Prerequisites** and **Step1** from [Amazon Guardduty Tester](https://github.com/awslabs/amazon-guardduty-tester)

## 2. Deploy Automatic Event Response Solution

**a.** Create a new CloudFormation stack using **guardduty-auto-event-response-cf.yaml** to 
deploy the various AWS components required to build Automatic Event Response. Refer
the below diagram for the solution.

![Automatic Event Response Solution](docs/guardduty-auto-event-response-solution.png)

Deploy Cloud formation via AWS Console

or

Deploy Cloud formation via AWS Cli
```
aws cloudformation deploy \
--template-file guardduty-auto-event-response-cf.yaml \
--stack-name guardduty-auto-response \
--capabilities CAPABILITY_NAMED_IAM \
--parameter-overrides \
QuarantineSecurityGroupName=guardduty-quarantine
```

**b.** Subcribe an email address to the SNS topic created.

## 3. Run the Test

Perform **Step2** and **Step3** from [Amazon Guardduty Tester](https://github.com/awslabs/amazon-guardduty-tester) 

## 4. Expected result

#### GuardDuty
Expect to see several findings( could take upto 15 mins )

#### Cloudwatch Event
The Cloudwatch event rule will trigger the SNS Notifcation and Lambda function

*Click for [sample GuardDuty cloudwatch event](docs/guardduty-cloudwatch-event-sample.json)*

#### SNS Notifcation
Expect to receive an Email notification to the email address subcribed to the SNS topic.
The body of the Email should use the input template specified in the CloudWatch rule.

#### Lambda
The lambda function should be executed which should,
1.  Create a Quarantine Security Group in the VPC (with the compromised EC2 instance) if it does not exist.
2.  Add a tag **GuardDuty Status: Quarantined** to the compromised EC2 instance.
3.  Remove the existing security groups on the compromised EC2 instance and replace it with the Quarantine Security Group.

## Clean Up

**Don't forget to clean up the resource you created to avoid**
1. Manually delete the SNS email subcription and the Quarantine Security Group.
2. Delete the **Automatic Event Response Solution cloudformation** stack.
3. Delete the **Amazon Guardduty Tester environement** stack.
4. [Suspend or Disable GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/guardduty_suspend-disable.html)

## License

This library is licensed under the Apache 2.0 License. 