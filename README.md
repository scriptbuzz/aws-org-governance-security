# org-governance
Automations and SCP policies I use frequently to apply from AWS Organization account to enforce best practices in sub-accounts

- SCP prevents actions against any role starting with OrgSec in sub-accounts

```
    "Statement": [
        {
            "Sid": "PreventOrgSecRoleActions",
            "Effect": "Deny",
            "Action": [
                "*"
            ],
            "Resource": [
                "arn:aws:iam::*:role/OrgSec*"
            ],
            "Condition": {
                "StringNotLike": {
                    "aws:PrincipalARN": "arn:aws:iam::*:role/OrgSec*"
                }
            }
        }
    ]
}
```

- SCP prevents creation of IAM users with login profies in sub-accounts. This ensures your corporate directory is the single source of truth for users with login credentials.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PreventIAMUserWithLoginActions",
            "Effect": "Deny",
            "Action": [
                "iam:ChangePassword",
                "iam:CreateLoginProfile",
                "iam:UpdateLoginProfile",
                "iam:UpdateAccountPasswordPolicy"
            ],
            "Resource": [
                "*"
            ]
     }]
}
```

- SCP to protect CloudTrails in sub-accounts from being tampered with.

```
{
            "Sid": "ProtectCloudTrails",
            "Effect": "Deny",
            "Action": [
                "cloudtrail:DeleteTrail",
                "cloudtrail:StopLogging",
		"cloudtrail:PutEventSelectors",
                "cloudtrail:UpdateTrail"
            ],
            "Resource": [
                "arn:aws:cloudtrail:*:*:trail/somecloudtrail"
            ]
}
```
- SCP to prevent public access to S3 in sub-accounts
```
{
            "Sid": "PreventAllS3PublicAccess",
            "Action": [
                "s3:PutAccountPublicAccessBlock"
            ],
            "Resource": "*",
            "Effect": "Deny"
}
```
- SCP to protect sub-accounts from leaving the root Organization account
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "ProtectAgainstLeavingOrganization",
            "Effect": "Deny",
            "Action": [
                "organizations:LeaveOrganization"
            ],
            "Resource": [
                "*"
            ]
        }
    ]
}
```

- SCP to protect GuardDuty in sub-accounts
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Deny",
            "Action": [ 
                "guardduty:AcceptInvitation",
                "guardduty:ArchiveFindings",
                "guardduty:CreateDetector",
                "guardduty:CreateFilter",
                "guardduty:CreateIPSet",
                "guardduty:CreateMembers",
                "guardduty:CreatePublishingDestination",
                "guardduty:CreateSampleFindings",
                "guardduty:CreateThreatIntelSet",
                "guardduty:DeclineInvitations",
                "guardduty:DeleteDetector",
                "guardduty:DeleteFilter",
                "guardduty:DeleteInvitations",
                "guardduty:DeleteIPSet",
                "guardduty:DeleteMembers",
                "guardduty:DeletePublishingDestination",
                "guardduty:DeleteThreatIntelSet",
                "guardduty:DisassociateFromMasterAccount",
                "guardduty:DisassociateMembers",
                "guardduty:InviteMembers",
                "guardduty:StartMonitoringMembers",
                "guardduty:StopMonitoringMembers",
                "guardduty:TagResource",
                "guardduty:UnarchiveFindings",
                "guardduty:UntagResource",
                "guardduty:UpdateDetector",
                "guardduty:UpdateFilter",
                "guardduty:UpdateFindingsFeedback",
                "guardduty:UpdateIPSet",
                "guardduty:UpdatePublishingDestination",
                "guardduty:UpdateThreatIntelSet"
            ],      
            "Resource": "*"
        }
    ]
}
```
- SCP to protect VPC Flowlogs
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": [
        "ec2:DeleteFlowLogs",
        "logs:DeleteLogGroup",
        "logs:DeleteLogStream"
      ],
      "Resource": "*"
    }
  ]
 }
```
- SCP to protect Confg in sub-accounts
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": [
        "config:DeleteConfigRule",
        "config:DeleteConfigurationRecorder",
        "config:DeleteDeliveryChannel",
        "config:StopConfigurationRecorder"
      ],
      "Resource": "*"
    }
  ]
}
```
- SCP to protect a private VPC from having an Internet Gateway attached to it
```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Deny",
      "Action": [
        "ec2:AttachInternetGateway",
        "ec2:CreateInternetGateway",
        "ec2:CreateEgressOnlyInternetGateway",
        "ec2:CreateVpcPeeringConnection",
        "ec2:AcceptVpcPeeringConnection",
        "globalaccelerator:Create*",
        "globalaccelerator:Update*"
      ],
      "Resource": "*"
    }
  ]
}
```
- SCP prevents api actions in regions not supported by your project, with the exception of global services. In this policy, only regions us-east-1 and us-east-2 are supported
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DenyAllOutsideMyRegions",
            "Effect": "Deny",
            "NotAction": [
               "a4b:*", "artifact:*", "aws-portal:*",
                "budgets:*",
                "ce:*", "chime:*", "cloudfront:*", "cur:*",
                "datapipeline:GetAccountLimits", "directconnect:",
                "globalaccelerator:*",
                "health:*",
                "iam:*", "importexport:*",
                "mobileanalytics:*",
                "organizations:*",
                "resource-groups:*", "route53:*", "route53domains:*",
                "s3:GetBucketLocation", "s3:ListAllMyBuckets", "shield:*", "support:*",
                "tag:*", "trustedadvisor:*",
                "waf:*",
                "wellarchitected:*"
            ],
            "Resource": "*",
            "Condition": {
                "StringNotEquals": {
                    "aws:RequestedRegion": [
                        "us-east-1",
                        "us-east-2"
                    ]
                }
            }
        }
    ]
}
```

