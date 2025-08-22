<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Build a Security Monitoring System

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-security-monitoring)

**Author:** R  


---

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-security-monitoring_reghtjy)

---

## Introducing Today's Project!

In this project, I will demonstrate how to monitor and alert on access to sensitive data using AWS CloudTrail, CloudWatch, and SNS. I'm doing this project to learn how to detect and respond to secret access events in a secure AWS environment.

### Tools and concepts

Services I used were AWS CloudTrail, CloudWatch, SNS (Simple Notification Service), and Secrets Manager. Key concepts I learnt include log monitoring, metric filters, CloudWatch alarms, SNS for notifications, and troubleshooting CloudWatch and CloudTrail integrations.

### Project reflection

This project took me approximately 2 hours to complete. The most challenging part was troubleshooting the SNS notification setup and ensuring all components were working together correctly. It was most rewarding to see the alarm and email notifications working as intended after debugging the setup.

---

## Create a Secret

Secrets Manager is an AWS service that securely stores, manages, and retrieves sensitive information like API keys and passwords. You could use Secrets Manager to protect credentials and reduce the risk of exposing secrets in code or config files.

To set up for my project, I created a secret called TopSecretInfo that contains a key-value pair where the key is "The Secret is" and the value is a custom message used for monitoring access.

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-security-monitoring_o5p6q7r8)

---

## Set Up CloudTrail

CloudTrail is an AWS service that records account activity for auditing and security. I set up a trail to capture access events and store them in S3 so we can track when someone interacts with our secret.

CloudTrail events include types like management events (e.g., accessing secrets), data events (e.g., S3 object access), insights events (detect unusual behavior), and network activity events (e.g., VPC changes). Secret access is logged as a management event.

### Read vs Write Activity

Read API activity involves viewing data without making changes, such as listing S3 buckets or describing secrets. Write API activity involves modifying resources or retrieving secret values. For this project, we need to track Write activities like accessing the secret's value.

---

## Verifying CloudTrail

I retrieved the secret in two ways: First through the AWS Secrets Manager console by selecting "Retrieve secret value." Second using the AWS CLI with the command `aws secretsmanager get-secret-value --secret-id "TopSecretInfo"`.

To analyze my CloudTrail events, I visited the CloudTrail Event history page and filtered by the event source `secretsmanager.amazonaws.com`. I found events labeled `GetSecretValue`, which means my secret was retrieved or used. This tells me that CloudTrail successfully captured the events when I accessed the secret through both the console and the AWS CLI.

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-security-monitoring_s8t9u0v1)

---

## CloudWatch Metrics

CloudWatch Logs is a service that collects, monitors, and stores log data from various AWS services, including CloudTrail. It's important for monitoring because it allows you to centralize and analyze logs, create custom metrics, set up alarms, and trigger automated responses based on specific events or patterns in your logs. By integrating CloudTrail with CloudWatch Logs, we can efficiently monitor sensitive events like accessing our secret, and receive timely notifications or take actions if needed.

CloudTrail's Event History is useful for quickly reviewing recent events within the last 90 days, providing an immediate look at who did what in your AWS account. However, CloudWatch Logs are better for long-term storage, deeper analysis, and setting up proactive monitoring. With CloudWatch Logs, you can create alerts, automate responses, and filter logs more effectively, allowing for continuous and customized monitoring.

A CloudWatch metric is a way to track specific events in your logs. When setting up a metric, the metric value represents the occurrence of the event, like secret access (set to 1). Default value is used when no event matches the filter, showing 0 during inactive periods.

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-security-monitoring_a9b0c1d2)

---

## CloudWatch Alarm

A CloudWatch alarm is a monitoring tool that triggers notifications when a specific metric crosses a defined threshold. I set my CloudWatch alarm threshold to "Greater/Equal to 1" for the "SecretIsAccessed" metric, so the alarm will trigger when the secret is accessed at least once in a 5-minute period. This ensures I'll be alerted immediately whenever my secret is accessed.

I created an SNS topic along the way. An SNS topic is a broadcast channel for notifications where you can send messages to all subscribers. My SNS topic is set up to send email notifications to my address whenever the CloudWatch alarm is triggered, alerting me when my secret is accessed.

AWS requires email confirmation because it ensures that the person receiving the notifications has explicitly agreed to receive them. This helps prevent unauthorized subscriptions and protects against spam or unwanted notifications being sent to an email address without the recipient's consent.

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-security-monitoring_fsdghstt)

---

## Troubleshooting Notification Errors

To test my monitoring system, I retrieved the secret value again from Secrets Manager. The results were that the alarm was not triggered immediately, and I did not receive an email notification within the expected time frame. It seems like there might be an issue preventing the notification from being sent.

When troubleshooting the notification issues, I checked CloudTrail to confirm that it had recorded the GetSecretValue event, then verified that CloudTrail was correctly delivering logs to CloudWatch. I ensured that the CloudWatch metric filter was properly detecting the relevant logs and reviewed the CloudWatch alarm configuration to make sure it was set to trigger on the appropriate metric. Finally, I confirmed that the SNS subscription was active and correctly configured to send an email notification.

I initially didn't receive an email before because the SNS subscription hadn't been confirmed. The key solution was subscribing to the SNS topic via email and confirming the subscription, which allowed the notifications to be delivered.

---

## Success!

To validate that my monitoring system works, I accessed the secret again to generate a new event. Then, I checked the CloudWatch alarm and confirmed that it was in the "In alarm" state. After waiting a few minutes for the system to process the event, I verified that I received the email notification from AWS SNS with the subject "ALARM: 'SecretIsAccessedAlarm'". This confirmed that both the CloudWatch alarm and direct CloudTrail notifications were functioning correctly.

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-security-monitoring_ageraergearge)

---

## Comparing CloudWatch with CloudTrail Notifications

---

---
