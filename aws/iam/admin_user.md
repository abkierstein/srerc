# IAM

## Reusble admin user
```
resource "aws_iam_user" "reusable-admin" {
  name = "reusable-admin"
  tags = {
    name  = "example"
  }
}
resource "aws_iam_access_key" "reusable-admin" {
  user    = aws_iam_user.reusable-admin.name
}
resource "aws_iam_user_policy_attachment" "attach-admin" {
  user       = aws_iam_user.reusable-admin.name
  policy_arn = "arn:aws:iam::aws:policy/AdministratorAccess"
}
```