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
### Assume role funtion
Note: You must fill in the ACCOUNT_ID & SESSION_DURATION within the function definition.
```
function aws_assume_role() {
    USAGE="Usage:
    aws_assume_role <ROLE NAME>
    "

    ACCOUNT_ID=<ACCOUNT_ID>
    SESSION_DURATION=<SESSION_DURATION>

    if [ -z "${1}" ]
    then
        echo "${USAGE}"
    else

      if [ $AWS_SESSION_TOKEN ]
      then
          echo "Removing previously assumed role..."
          unset AWS_PROFILE AWS_SECRET_ACCESS_KEY AWS_ACCESS_KEY_ID AWS_SESSION_TOKEN
      fi
      EXPORT_COMMANDS=$(aws sts assume-role --role-arn arn:aws:iam::${ACCOUNT_ID}:role/"${1}" --role-session-name "${1}" --duration-seconds ${SESSION_DURATION} | jq -r '.Credentials | "AWS_ACCESS_KEY_ID=\(.AccessKeyId)\nAWS_SECRET_ACCESS_KEY=\(.SecretAccessKey)\nAWS_SESSION_TOKEN=\(.SessionToken)\n"')
      for i in ${EXPORT_COMMANDS};
        do 
          export "${i}"
      done

      echo "Caller Identity..."
      aws sts get-caller-identity

    fi
}

$ aws_assume_role foobar
Caller Identity...
{
    "UserId": "123454321:foobar",
    "Account": "1234567890",
    "Arn": "arn:aws:sts::1234567890:assumed-role/foobar/foobar"
}
```
