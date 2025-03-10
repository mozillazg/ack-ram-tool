# ack-ram-tool

A command line utility and library for using RAM in ACK.

## Installation

You can download the latest release from [Releases](https://github.com/AliyunContainerService/ack-ram-tool/releases) page.


## Credential

You can use ~/.alibabacloud/credentials file:

```
$ cat ~/.alibabacloud/credentials

[default]
type = access_key
access_key_id = foo
access_key_secret = bar
```

Or environment variables:

```
$ export ALIBABA_CLOUD_ACCESS_KEY_ID=foo
$ export ALIBABA_CLOUD_ACCESS_KEY_SECRET=bar
```

## Usage


### RAM Roles for Service Accounts (RRSA)

Enable RRSA feature:

```
$ ack-ram-tool rrsa enable -c <clusterId>

? Are you sure you want to enable RRSA feature? Yes
Enable RRSA feature for cluster c86fdd*** successfully

```


Check status of RRSA feature:

```
$ ack-ram-tool rrsa status -c <clusterId>

RRSA feature: enabled
OIDC Name:    ack-rrsa-c86fdd***
OIDC Arn:     acs:ram::18***:oidc-provider/ack-rrsa-c86fdd***
Issuer:       https://oidc-ack-***/c86fdd***

```

Diable RRSA feature:

```
$ ack-ram-tool rrsa disable -c <clusterId>

? Are you sure you want to disable RRSA feature? Yes
Disable RRSA feature for cluster c86fdd096c5ab482982cf33302f914af4 successfully

```

Associate an RAM Role to a service account:

```
$ ack-ram-tool rrsa associate-role -c <clusterId> -r <roleName> -n <namespace> -s <serviceAccount>

? Are you sure you want to associate RAM Role test-rrsa to service account test-serviceaccount (namespace: test-namespace)? Yes
Will change the assumeRolePolicyDocument of RAM Role test-rrsa with blow content:
{
  "Statement": [
   {
    "Action": "sts:AssumeRole",
    "Effect": "Allow",
    "Principal": {
     "RAM": [
      "acs:ram::18***:root"
     ]
    }
   },
   },
   {
    "Action": "sts:AssumeRole",
    "Condition": {
     "StringEquals": {
      "oidc:aud": "sts.aliyuncs.com",
      "oidc:iss": "https://oidc-ack-**/c86fdd***",
      "oidc:sub": "system:serviceaccount:test-namespace:test-serviceaccount"
     }
    },
    "Effect": "Allow",
    "Principal": {
     "Federated": [
      "acs:ram::18***:oidc-provider/ack-rrsa-c86fdd***"
     ]
    }
   }
  ],
  "Version": "1"
 }
? Are you sure you want to associate RAM Role test-rrsa to service account test-serviceaccount (namespace: test-namespace)? Yes
Associate RAM Role test-rrsa to service account test-serviceaccount (namespace: test-namespace) successfully

```

Testing assume role with give OIDC token:

```
$ ack-ram-tool rrsa assume-role -r <roleArn> -p <oidcProviderArn> -t <oidcTokenFile>

Retrieved a STS token:
AccessKeyId:       STS.***
AccessKeySecret:   7UVy***
SecurityToken:     CAIS***
Expiration:        2021-12-03T05:51:37Z

```
