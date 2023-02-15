# Week 0 — Billing and Architecture

## First napkin diagram

First sketch of my logical diagram.
![Napkin img](/_docs/assets/napkin-one.png)

## 2nd sketch logical diagram

![cruddar logical](/_docs/assets/cruddar_logical.jpeg)
Link to my Lucid repo: [lucid diagram](https://lucid.app/lucidchart/9976aa80-82e1-4c4b-8740-abcb62d1e1f2/edit?view_items=zvexuFBbVCbE&invitationId=inv_c19e2de0-4fb7-43aa-9297-f757a0dd3cbf)

This week0 :Bootcamp Overview and Introduction to Cloud Spending

## Technical Tasks

## Business Scenario

## Security Considerations

## Spend consideration

- AWS Bill Walkthrough
  Root used to set up IAM user

1. IAM user by default does not have billing access. Use root user to create access for IAM user.
2. IAM.
   Setting billing budget
   Go to root account, get IAM User and add role access to billiing information.
   Activate it
   IAM users who are admin to access the billing dashboard.

- Billing Alert (Cloud watch & Budget)

#### Pricing of services vary by region- Amount in USD and local currency on dashboard

Free tier utilization - Current and forecasted usages- check from time to time bases

#### Billing alert

managebillingalerts -

- This is a region-based service managed by cloud watch
- Go to alarm, create alarm, select metric, go to billing by total estimated charges
- select metric
- fill in the metric details
- define threshold to any value you want, not greater than c $5 is what I chose
- create an sns topic- chose email as my end point.
- Next name your billing alarm. 10 alarms are free for free tier . Wait a couple of mins to set up.

#### Budget

- Go to budget.Check your console and you will be see the global icon. Shows it can be set globally unlike the billing alert that needs to be in a region.
- Buggets are advanced features
- Set only one or two budgets. More than two could be chargeable

- Cost Explorer
- Calculate AWS estimates cost for service
- Check AWS credits
- Cost Allocation tags
- Free forever vs free for 12 months.

#### Homework Challenges

Only use the root for AWS account setup

- Destroy your root account credentials, set MFA, IAM role

1. I registered my account with AWS with the free tier account, Set up the root account
   with MFA(AUTHY, DUO and the likes).
   Created two IAM users with Password policy role for interchange and whateverview.
2. IAM role is gloabal on AWS
3. Used IAM account to create security credentials. Do not use root user!

- Use EventBridge to hookup Health Dashboard to SNS and send notification when there is a service health issue.
- Review all the questions of each pillars in the Well Architected Tool (No specialized lens)
- Create an architectural diagram (to the best of your ability) the CI/CD logical pipeline in Lucid Charts
- Research the technical and service limits of specific services and how they could impact the technical path for technical flexibility.
- Open a support ticket and request a service limit

## Problems (Technical and Business)
