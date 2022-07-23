# AWS CLI Commands


## Secrets Manager
### Upload a ssh key to secrets manager
```
$ aws secretsmanager put-secret-value --secret-id <EXAMPLE> --secret-string "$(cat <EXAMPLE KEY> | base64)"
```
### Add a secret manager stored ssh-key to your SSH agent
Note: assumes you've base64 encoded the private key
```
$ ssh-add - <<<  "$(aws secretsmanager get-secret-value --secret-id=<EXAMPLE> | jq -r '.SecretString' | base64 -d)"
Identity added: (stdin) (EXAMPLE)
```

## Role Assumption
### Assume role alias
```
alias assume_<EXAMPLE>=$(aws sts assume-role --role-arn arn:aws:iam::1234567890:role/<EXAMPLE ROLE NAME> --role-session-name <EXAMPLE SESSION NAME> | jq -r '.Credentials | "export AWS_ACCESS_KEY_ID=\(.AccessKeyId)\nexport AWS_SECRET_ACCESS_KEY=\(.SecretAccessKey)\nexport AWS_SESSION_TOKEN=\(.SessionToken)\n"')
```