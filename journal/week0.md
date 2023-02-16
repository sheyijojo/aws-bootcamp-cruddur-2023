# Week 0 — Billing and Architecture

Page under constrcution, please ignore all spelliing errors, I will update them!

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

Constraints- AWS keep updating their services every year.

Indentify and Inform the businesses of technical risks and vulnerablilites associated to cloud
environments and cloud resources.

- What is cloud security?
- Cybersecurity that protects the data apps and services assocated with cloud envs from both external and internal threats

- Reducing Human errors
- Reducing breach impact
- Protecting netwroks, apps, services in cloud envs agaist malicious data theft

###### Steps to security

- step 1: Add MFA to root user(Most Powerful- Has full admin access and used to create users)
  Hide information about your account ID
  To create MFA for your root user, go to account, go to security credentials.

- step 2 - Create an organization unit
  Note: Each account are different from each other. What governs all of them in an organzation? Then, here comes orgaization units.
- Root user account is the management account, dont add apps to it. Just used for creation of orgs, underneath, you create accounts needed in the org.
-
- Step 3 - Go to AWS organizations(No cost to create one) with your root account
  Create organizations- U will have root, and your rootuser name(management account)
  create new org unit using root, diff bwt root and management acc is not yet sepecified
  Good habit to create tags with new units or org created
  created IT dept, tag name Infotech, values IT.
  You can rename and delete orgs easilly.

another approach- falling approaach

For more mature env and diff from ceating BU such as Engineering or IT BU
BU org and IT org will be under stanby, when new BU asuch as IT is created , move from stanby to active.
name - standby accounts
key- account_status
value- standby

name - Active account
key - accounts actibe
value - active

Management account- Dont use to create accounts or resources. Just use to manage accounts

step 4 : Enable AWS Cloud Trail
This the audit service from AWS
Monitor data security & Residence
Understand the region vs AZ vs Global services

Go to cloudtrail(Use your IAM role with admin priviledges) or root acc(if you want to access all accounts)- But I wont use my root for now
create trail
Go to dashboard
create trail
Enable log gile SSE-encrytpion- click or unclick
Customer managed AWS key - choose new pr ise exisitng
use an alias
remember to tag as well.
Next

Step 5: Create IAM users
There are 3 kinds of users:Normal User(Least asccess permissions), IAM users, Sys users,  
Don't user Root user on a day-day to basis
Create IAM user
SetUp MFA in Security permissions. Especially if you have Admin access

step 6: IAM roles and IAM policies
They are different
IAM roles - Permissions of what can be done or not done. Can be only linked to a service in AWS.
e.g 3rd part user who can access the cloud env with roles, or ec2 gaining access to accoun to perform actions through roles
IAM policies- Can be attaced to a grp, user, admin roles..
Remember principles of least priviledge.

step 7:Roles

- Using custom roles to your advantage
- Keep roles very simple at first
  e.g create AWs service
  choose EC2
  choose adminaccess- Access to all aws services and resources
  choose a desc
  add a tag

step 8: Policies
policy
security audit(read only)
you can attach to a role, user or grps.

step 9: Create Group
Attach permissions policy
e.g security audit

step 10: SCP- Service Control Policies
IAM is global
S3 is global
search for SCP- Under IAM or AWS ORGS
Create policy. e.g using a yaml file

Version: '2012-10-17'
Statement: #Denies the Account from leaving the Organization

- Effect: Deny
  Action:
  - organizations:LeaveOrganization
    Resource: "\*"

add to the policy- transform yaml to json file
give it a name
give it a tag
attach this policy to your aws org

We did not cover amazon guarduty, aws config- I already have knowledge about this topic though.

**Security Best Practices**

1. Data Protection & Residency in accordance to Security Policy
2. Identity & Access Management with least priviledge
   Governance & compliance of AWS services being used
   gLOBAL VS rEGIONAL SERVOCES
3. Ccomplaint servies
4. Shared responsibilitt of threat detection
5. incident response plans to include cloud

## Spend consideration

AWS Bill Walkthrough

##### Root user(Most powerful) to set up IAM user

1. IAM user by default does not have billing access. Use root user to create access for IAM user.
2. IAM.
   Setting billing budget for IAM users
   Go to root account, get IAM User and add role access to billiing information.
   Activate it
   IAM users who are admin have access the billing dashboard.

Free tier utilization - Current and forecasted usages- check from time to time bases.
Pricing of services vary by region- Amount in USD and local currency on dashboard

#### Billing alert(Cloud Watch & Budget)

managebillingalerts

- This is a region-based service managed by cloud watch
- Go to alarm, create alarm, select metric, go to billing by total estimated charges
- select metric
- fill in the metric details
- define threshold to any value you want, not greater than c $5 is what I chose
- create an sns topic- chose email as my end point.
- Next name your billing alarm. 10 alarms are free for free tier . Wait a couple of mins to set up.

### Budget

- Highly recommended
- Go to budget.Check your console and you will be see the global icon. Shows it can be set globally unlike the billing alert that needs to be in a region.
- Buggets are advanced features
- Set only one or two budgets. Selecting more than two budgets could be chargeable
- Chose zero spend budget i.e 0.01$ speding budgte sld not exceed this amt
- Give it a name and your budgeted amount.

### Cost Allocation tags

For example having ec2 instance resources. You might want to know their cost

- It is good is good to tags for these resources
- Tags can be used at the billing-const allocation tags
- You will need to activate it

### Cost Explorer

Helpful for Financial cost management of your resources

- Go to Billing, clieck on cost explorer
- you could check for dauly, weekly and hourly.
- Male use of filters based on services, regions.

### Calculate AWS estimates cost for service

This helps to show cost estimates based on location, region, OS, instance type and so on

- Amz EC2 pricing. Instance name, rate, vCPU, Memory, Storgae, Network performance.
- Shows hourly cost based on
- E.g self calculator Free tier = 720 \* 0.12 = 86.4
- Aws calculator. Search for aws calc on web. create estimate. input the services.
- Aws estimated cost is 87.60(Provided for 730hours). Average of both months.

### Free forever vs free for 12 months.

- ESearch for AWS free tier and explore.
- ECis 750 hours, you could do the calculations your self on AWS calc to get the amount in dollars
- Always terminate your resources.

### Homework Challenges

Only use the root for AWS account setup

Destroy your root account credentials, set MFA, IAM role

1. I registered my account with AWS with the free tier account, Set up the root account
   with MFA(AUTHY, DUO and the likes....or virtual).
   Created two IAM users with Password policy role for interchange and whateverview.
2. IAM role is gloabal on AWS
3. Used IAM account to create security credentials. Do not use root user!

- Use EventBridge to hookup Health Dashboard to SNS and send notification when there is a service health issue.
- Review all the questions of each pillars in the Well Architected Tool (No specialized lens)
- Create an architectural diagram (to the best of your ability) the CI/CD logical pipeline in Lucid Charts
- Research the technical and service limits of specific services and how they could impact the technical path for technical flexibility.
- Open a support ticket and request a service limit

## Problems (Technical and Business)
