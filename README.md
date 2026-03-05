<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="150" />

# Build a Security Monitoring System

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-security-monitoring)

**Author:** shinee  

---

<img width="1492" height="632" alt="Screenshot 2026-03-05 at 4 52 18 PM" src="https://github.com/user-attachments/assets/806fbc9c-301b-44db-8609-b022b4949545" />


---

## Introducing Today's Project!

In this project, I will demonstrate how to set up a monitoring system in AWS using CloudTrail, CloudWatch, and SNS. I'm doing this project to learn how security and monitoring services in AWS work, along wit a working system that sends an email.

### Tools and concepts

Services I used were CloudTrail, CloudWatch and SNS. I also used Secrets Manager, IAM Roles and S3 buckets. Key concepts I learnt include secret storing, CloudWatch vs Cloudtrail, wha are notifications and different kinds of endpoints, how to create CloudWatch filter and alarm.

### Project reflection

This project took me approximately just under 3 hours, including demo. The most challenging part was troubleshooting why the email was not delivering in our first test. It would be frustrating when an error happens but there were no error messages - so that was difficult to track.
It was most rewarding to compare CloudTrail SNS notifications, it made me realise the relevance of CloudWatch.

---

## Create a Secret

AWS Secrets Manager helps protect access to applications, services and IT resources. It is used to store secrest i.e. database credentials, account IDs, API keys or any sensitive information that would cause damage it it got leaked. These information should not be in code.

To set up for my project, I created a secret (secret_nextwork) in secrets manager which is a string, that contains a fun fact about me.

![Image](http://learn.nextwork.org/encouraged_purple_mysterious_cardamom/uploads/aws-security-monitoring_o5p6q7r8)

---

## Set Up CloudTrail

CloudTrail is a monitoring serviced used to track events and activities in my AWS Account. 
It documents every action taken, like who, when and where they did it. 
This continuous recording of Cloud Trail logs are helpful for security, (i.e. detecting suspicious activity), compliance (i.e. proving that you are following rules and troubleshooting (i.e. identifying what happened/ changed if something breaks).

I set up a trail to monitor activity on the secret created.

CloudTrail events include types like:
1) Management events (FOC)
2) Data events (Cost)
3) Insights events (Cost)
4) Network Activity events (Cost)

In this project, I have set up our trail to track Management events as accessing a secret falls into that category, it is not a Data event which captures high volume actions performed on resources because Management events are free to track and AWS lets us track security operations like this at no cost.

### Read vs Write Activity

Read API activity involves accessing, reading, opening a resource. Write API activity involves creating, deleting, updating a resource. For this project, we need both to learn about both types of activities, but we really only need the write activity. 
FYI: Accessing a secret is considered a write activity because of its importance!

---

## Verifying CloudTrail

I retrieved the secret in two ways: First through, Secrets Manager console, where we could just easily just select a "Retrieve Secrets value" button. Second using the AWS CLI i.e. running a command (get-secret-value) via CloudShell.

To analyze my CloudTrail events, (i.e. view the events where we got our secrets value). I visited the "Events history" page on Cloud Trail. I found that there was a GetSecretValue event tracked, regardless of whether we did it over the CLI or over the AWS console This tells me that CloudTrail can definitely see and track when we open the Secrets Manager Key.

![Image](http://learn.nextwork.org/encouraged_purple_mysterious_cardamom/uploads/aws-security-monitoring_s8t9u0v1)

---

## CloudWatch Metrics

CloudWatch Logs is a monitoring service that brings together logs from other AWS services, including CloudTrail to help us analyse and create alarms for.

It's important for monitoring because you get to create insights and get alerted based on events that happen in my account.

CloudTrail's Event History is useful for quickly reading (management) events that happened in the last 90 days while CloudWatch Logs are better for analysing logs from different sources, accessing logs for longer than 90 days, and advanced filtering.

A CloudWatch metric is a specific way that we count or track events that are in a log group. When setting up a metric, the metric value represents how I increment or 'count' an event when it passes my filters (in this case, I want to increment the metric value by 1 whenever the secret is accessed. The Default value is used for when the event I am tracking does not occur.

![Image](http://learn.nextwork.org/encouraged_purple_mysterious_cardamom/uploads/aws-security-monitoring_a9b0c1d2)

---

## CloudWatch Alarm

A CloudWatch alarm is a feature and alert system in CloudWatch that is designed to go off" i.e. indicate when certain conditions have. been met in our log group.

I set my CloudWatch alarm threshold to be about how many times the GetSecretValue event happens in a 5 minute period, the alarm will trigger when the average number of times is above 1.

I created an SNS topic along the way. An SNS topic is like a newsletter/ broadcast channel that emails, phone numbers, functions, apps can subscribe to so that they get notified when SNS has a new update to share.

My SNS topic is set up to send us an email when our secret gets accessed!

AWS requires email confirmation because it will not automatically start emailing addresses that we subscribe to an SNS topic.

This helps prevent any unwanted subscriptions who are receiving those events.

![Image](http://learn.nextwork.org/encouraged_purple_mysterious_cardamom/uploads/aws-security-monitoring_fsdghstt)

---

## Troubleshooting Notification Errors

To test my monitoring system, I opened and accessed the secret again.
The results were not successful, we didn't get any emails/notifications about our secret getting accessed.

When troubleshooting the notification issues I investigated every single part of the monitoring system - whether CloudTrail is picking up on events that are happening when we access our secret, whether CloudTrail is sending logs to CloudWatch, we also verified whether the filter is accidentally rejecting the correct events, whether the alarm gets triggered, whether the triggering of the alarm sends an email. 

I initially didn't receive an email before because CloudWatch was configured to use the wrong threshold. Intead of calculating the AVERAGE number of times a secret was accessed in a time peroipd, it should have been the SUM.

---

## Success!

To validate that our monitoring system can successfully detect andn alert when my secret is accessed, I checked my secrets's value one more time. I received an email within 1-2 minutes of the event. Our alarm in CloudWatch is also in "State=Alarm"

<img width="818" height="751" alt="Screenshot 2026-03-05 at 4 36 54 PM" src="https://github.com/user-attachments/assets/b6bbee6e-9e65-44f2-b189-850de2cdf899" />


---

## Comparing CloudWatch with CloudTrail Notifications

In a project extension, I enabled SNS notification delivery in CloudTrail because this lets us evaluate CloudTrail vs CloudWatch for notifying us about events like our secret getting accessed.

After enabling CloudTrail SNS notifications, my inbox was very quickly flooded with new emails from SNS as it was notified by CloudTrail. 

In terms of the usefulness of these emails, I thought that we were receiving lots while the logs itself does not show what happened in terms of management events that occured. 
** We only see that new logs have been stored on the S3 bucket, it does not tell us anything insightful.

![Image](http://learn.nextwork.org/encouraged_purple_mysterious_cardamom/uploads/aws-security-monitoring_d7e8f9g0)

---

---
