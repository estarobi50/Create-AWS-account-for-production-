# aws-account-setup

## Overview
Initial AWS account setup including root account security, IAM user creation,
CLI access, billing alerts, and account alias configuration.

## Architecture
Root Account (locked down, MFA protected)
  → IAM User: estarobi50 (day-to-day access, MFA protected)
      → Access Keys for CLI access
      → Permission Policies for S3, Admin, and Billing monitoring
  → Billing & Cost Management
      → Budget alert at $0.01 threshold
      → SNS email notification
  → Account Alias: digitalcultureit (easy sign-in URL)

## Technologies Used
- AWS Root Account        — account owner, locked down after setup
- AWS IAM                 — user, permissions, and access key management
- AWS CLI                 — terminal access via access keys
- AWS Budgets             — free tier cost monitoring and alerts
- AWS SNS                 — email alert notifications
- Microsoft Authenticator — MFA for both root and IAM user

## Deployment Steps

  1. Create AWS account
       - Go to https://aws.amazon.com → Create an AWS account
       - Enter email, set root password
       - Complete phone and payment verification

  2. Set up MFA for root account
       - Sign in as root → top right menu → Security credentials
       - Under Multi-factor authentication (MFA) → Assign MFA device
       - Select Authenticator app → click Next
       - Open Microsoft Authenticator → + → Other account
       - Scan the QR code shown in AWS Console
       - Enter two consecutive OTP codes to confirm
       - MFA is now active on root account — store backup codes safely

  3. Create IAM user
       - Go to IAM → Users → Create user
       - Username: estarobi50
       - Enable AWS Management Console access
       - Set a custom password

  4. Set up MFA for IAM user
       - Go to IAM → Users → estarobi50 → Security credentials
       - Under Multi-factor authentication → Assign MFA device
       - Select Authenticator app → click Next
       - Open Microsoft Authenticator → + → Other account
       - Scan the QR code → enter two consecutive OTP codes to confirm

  5. Create access keys for CLI access
       - Go to IAM → Users → estarobi50 → Security credentials
       - Under Access keys → Create access key
       - Use case: Command Line Interface (CLI)
       - Add tags as needed
       - Download or copy Access Key ID and Secret Access Key
       - Run: aws configure
           AWS Access Key ID: <your key>
           AWS Secret Access Key: <your secret>
           Default region: us-east-1
           Default output format: json

  6. Attach permission policies
       - Go to IAM → Users → estarobi50 → Permissions → Add permissions
       - Attach the following policies:
           AdministratorAccess      — full AWS access
           AmazonS3FullAccess       — full S3 access
           EZ-Billing-Free-Tier     — custom billing read policy (see below)

       Custom policy: EZ-Billing-Free-Tier
       {
           "Version": "2012-10-17",
           "Statement": [
               {
                   "Effect": "Allow",
                   "Action": [
                       "budgets:ViewBudget",
                       "budgets:DescribeBudgetAction",
                       "ce:Get*",
                       "ce:List*",
                       "aws-portal:ViewBilling",
                       "aws-portal:ViewAccount",
                       "aws-portal:ViewUsage",
                       "aws-portal:ViewPaymentMethods",
                       "organizations:DescribeOrganization",
                       "organizations:ListAccounts"
                   ],
                   "Resource": "*"
               }
           ]
       }

  7. Set up billing budget and alert
       - Go to Billing and Cost Management → Budgets → Create budget
       - Budget type: Cost budget
       - Budget amount: $0.25 (free tier safety net)
       - Alert condition: Actual cost greater than $0.01 (4.00% of budget)
       - Add SNS alert → enter your email address to receive notifications
       - Add Cost and Usage widget to the billing dashboard for visibility

  8. Create account alias
       - Go to IAM → Dashboard → Account Alias → Create
       - Alias: digitalcultureit
       - Sign-in URL becomes:
           https://digitalcultureit.signin.aws.amazon.com/console
         (instead of using the 12-digit account number)

## Key Learnings
- Never use the root account for day-to-day work — create an IAM user
  immediately and lock root behind MFA
- Microsoft Authenticator works with AWS MFA — scan the QR code under
  "Other account" in the app
- Access keys are only shown once at creation — download or copy them
  immediately as they cannot be retrieved again
- AdministratorAccess on an IAM user is fine for personal projects but
  use least-privilege policies in team or production environments
- The EZ-Billing-Free-Tier custom policy allows monitoring costs without
  granting any spend or config permissions — safe to attach to any user
- AWS Budgets alerts trigger on actual cost exceeding the threshold —
  setting it at $0.01 catches any unexpected charges immediately
- Account aliases replace the 12-digit account ID in the sign-in URL
  making it much easier to remember and share
