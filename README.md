# org-governance
Automations and policies applied from the Organization account to enforce best practices

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
