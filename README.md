# yawsso

[![Pull Request Build Status](https://github.com/victorskl/yawsso/workflows/Pull%20Request%20Build/badge.svg)](https://github.com/victorskl/yawsso/actions) [![codecov.io](https://codecov.io/gh/victorskl/yawsso/coverage.svg?branch=master)](https://codecov.io/gh/victorskl/yawsso?branch=master) [![Build Status](https://travis-ci.org/victorskl/yawsso.svg?branch=master)](https://travis-ci.org/victorskl/yawsso) [![Coverage Status](https://coveralls.io/repos/github/victorskl/yawsso/badge.svg?branch=master)](https://coveralls.io/github/victorskl/yawsso?branch=master)

Yet Another AWS SSO - sync up AWS CLI v2 SSO login session to legacy CLI v1 credentials.

## Do I need it?

- See _Upstream Tracking_ at https://github.com/victorskl/yawsso/wiki

## Prerequisite

- Required [AWS CLI v2](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html)
- Assume you have already setup [AWS SSO](https://aws.amazon.com/single-sign-on/) for your organization

## TL;DR

- Install [latest from PyPI](https://pypi.org/project/yawsso/) like so:
```commandline
pip install yawsso
```

- Do your per normal SSO login and, have at least one org-level SSO login session cache:
```commandline
aws sso login --profile=dev
```

- To sync for all named profiles (e.g. dev, prod, stag, ...), then just:
```commandline
yawsso
```

- To sync default profile and all named profiles, do:
```commandline
yawsso --default
```

- To sync default profile only, do:
```commandline
yawsso --default-only
```

- To sync for selected named profile, do:
```commandline
yawsso -p dev
```

- To sync for multiple selected named profiles, do:
```commandline
yawsso -p dev prod
```

- To sync for default profile as well as multiple selected named profiles, do:
```commandline
yawsso --default -p dev prod
```

- Use `-e` flag if you want a temporary copy-paste-able time-gated access token for an instance or external machine. It use `default` profile if no additional arguments pass. The main use case is for those who use `default` profile, and would like to PIPE like this `aws sso login && yawsso -e | pbcopy`. Otherwise for named profile, do `yawsso -e -p dev`.

    > PLEASE USE THIS FEATURE WITH CARE SINCE **ENVIRONMENT VARIABLES USED ON SHARED SYSTEMS CAN GIVE UNAUTHORIZED ACCESS TO PRIVATE RESOURCES**:

```
yawsso -e
export AWS_ACCESS_KEY_ID=xxx
export AWS_SECRET_ACCESS_KEY=xxx
export AWS_SESSION_TOKEN=xxx
```

- You can also use `yawsso` subcommand `login` to SSO login then sync all in one go:
```commandline
yawsso login -h
yawsso login
yawsso login -e
yawsso login --this
yawsso login --profile dev
yawsso login --profile dev --this
```

- Print help to see other options:
```commandline
yawsso -h
```

- Then, continue per normal with your daily tools. i.e. 
    - `cdk deploy ...`
    - `terraform ...`
    - `cw ls -p dev groups`
    - `awsbw -L -P dev` 


## Why

AWS CLI v2 SSO login cache/store credentials is somewhat different to AWS CLI v1 i.e. no longer in `~/.aws/credentials`. There are many SDK and tools still depends on this legacy `~/.aws/credentials` format.

- boto3 - https://github.com/boto/boto3/issues/2091
- botocore - https://github.com/boto/botocore/issues/1988
- terraform aws provider - https://github.com/terraform-providers/terraform-provider-aws/issues/10851
- cdk - https://github.com/aws/aws-cdk/issues/5455
- cw - https://github.com/lucagrulla/cw/issues/119
- awsbw - https://github.com/jgolob/awsbw

And, https://github.com/aws/aws-cli/issues/4982 in CLI repo itself!!

This tool is originally based on [aws_sso.py](https://gist.github.com/sgtoj/af0ed637b1cc7e869b21a62ef56af5ac) script but take different approach and depends only on AWS CLI v2 for [get-role-credentials](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/sso/get-role-credentials.html). Well, everything else fail (including boto3) except CLI itself, so...

Someday, we won't need this anymore. But, until then this tool sync up AWS CLI v2 SSO login session to legacy format auto-magically!!

## Others

If this tools is not working for you, try the following:

- https://github.com/benkehoe/aws-sso-credential-process
- https://github.com/flyinprogrammer/aws-sso-fetcher
- https://gist.github.com/sgtoj/af0ed637b1cc7e869b21a62ef56af5ac

## Develop

- Create virtual environment and then:

```
pip install '.[dev,test]' .
pytest
python -m unittest
python -m yawsso --trace
```

- Create issue or pull request welcome

## License

MIT License

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
