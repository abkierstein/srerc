
# AWS Secrets Manager

## Define a secret and secret policy to secure access
```
resource "aws_secretsmanager_secret" "example" {
  name                    = "example"
  description             = "Example secret"
  kms_key_id              = aws_kms_key.key.arn
  recovery_window_in_days = "0"
  tags = {
    Name = "example"
  }
}
```
```
resource "aws_secretsmanager_secret_policy" "example" {
  secret_arn = aws_secretsmanager_secret.example.arn

  policy = jsonencode({
    Id : "example",
    Version : "2012-10-17",
    Statement : [
      {
        Sid : "AllowAllSecretManagerActionsToExampleUser",
        Effect : "Deny",
        Principal : "*",
        Action : [
          "secretsmanager:Create*",
          "secretsmanager:Put*",
          "secretsmanager:Update*",
          "secretsmanager:Delete*",
          "secretsmanager:Restore*",
          "secretsmanager:RotateSecret",
          "secretsmanager:CancelRotate*",
          "secretsmanager:TagResource",
          "secretsmanager:GetSecretValue",
          "secretsmanager:PutSecretValue",
          "secretsmanager:UntagResource"
        ]
        Resource : aws_secretsmanager_secret.example.arn
        Condition : {
          ArnNotEquals : {
            "aws:PrincipalArn" : [ data.aws_iam_user.example.arn ]
            )
          }
        }
      }
    ]
  })
}
```
```
data "aws_iam_user" "example" {
  user_name = "example"
}
```

## Define a KMS key for use with Secrets Manager
```
resource "aws_kms_key" "example" {
  description             = "Example key"
  deletion_window_in_days = 10
  policy = jsonencode({
    Version: "2012-10-17",
    Id: "example",
    Statement: [
      {
        Sid : "Allow access through AWS Secrets Manager for all principals in the account that are authorized to use AWS Screts Manager",
        Effect : "Allow",
        Principal : {
          AWS : "*"
        },
      Action : [ 
        "kms:Decrypt", 
        "kms:ReEncrypt*", 
        "kms:GenerateDataKey*", 
        "kms:CreateGrant", 
        "kms:DescribKey" 
        ],
      Resource : "*",
      Condition : {
        StringEquals : {
          "kms:ViaService": "secretsmanager.us-east-1.amazonaws.com",
          "kms:CallerAccount" : "1234567890"
          }
        }
      },
      {
        Sid : "Allow access to key metadata to the account",
        Effect : "Allow",
        Principal : {
          AWS : [ "arn:aws:iam::1234567890:root" ]
          },
        Action : [ 
          "kms:Describe*", 
          "kms:Get*", 
          "kms:List*", 
          "kms:RevokeGrant" 
          ],
        Resource : "*"
      },
      {
        Sid: "Allow All IAM User Actions for Example User",
        Effect: "Allow",
        Principal: {
          AWS: data.aws_iam_user.example
        },
        Action: "kms:*",
        Resource: "*"
      }
    ]
  })

}
```
```
resource "aws_kms_alias" "example" {
  name          = "alias/example"
  target_key_id = aws_kms_key.example.key_id
}
```
```
data "aws_iam_user" "example" {
  user_name = "example"
}
```