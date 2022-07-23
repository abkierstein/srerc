# IAM Policies

### Policy Generation
I use [AWS's policy generator](https://awspolicygen.s3.amazonaws.com/policygen.html) as a frequent starting point with new policies.

### Linting
Duo-labs has a tool for policy linting called: [Parliament](https://github.com/duo-labs/parliament)


## Explict Access Restruction
### Limit access to a single user w/full permissions

```
{
  "Id": "Example 1",
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "example1",
      "Effect": "Deny",
      "Principal": "*",
      "Action": "<ACTION>:*",
      "Resource": "*",
      "Condition": {
        "ArnNotEquals": {
          "aws:PrincipalArn": "arn:aws:iam::123456789:user/example_user"
        }
      }
    }
  ]
}
```

### Limit AssumeRole Access to all members of a group
Note: requires defining group as a datasource
```
assume_role_policy = jsonencode(
  {
    Version : "2012-10-17",
    Statement : [
      {
        Effect : "Allow",
        Principal : {
          AWS : "*"
        },
        Action : "sts:AssumeRole",
        Condition = {
          ArnEquals = {
            "aws:PrincipalArn" : data.aws_iam_group.example_group.users.*.arn
          }
        }
      }
    ]
  }
)
```
```
data "aws_iam_group" "example_group" {
  group_name = "example_group"
}
```

## EKS
### EKS Basic User Access
```
  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Sid: "Stmt1234567890",
        Action: [
          "eks:AccessKubernetesApi",
          "eks:Describe*",
          "eks:List*"
        ],
        Effect: "Allow",
        Resource: "*"
      }
    ]
  })
```

