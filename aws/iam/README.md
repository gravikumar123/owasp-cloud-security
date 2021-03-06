# AWS Identity and Access Management (IAM) Threat Model

## See also

* https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started.html

# Assumptions

* A threat model exists for console and API access to service. This page does not include threat models of the TLS or authentication mechanism of the AWS API itself.

# Scope

This threat model is scoped to the IAM service itself, including for example roles and policies, but not to all the possible IAM actions of every single AWS service. The only IAM actions considered here are those of IAM itself (e.g. PassRole and AssumeRole).
# Threats

## OCST-1.3.1
### Name
Unprotected access keys
### Description
Attacker can gain unauthorised access to resources using unprotected AWS access keys.

If an AWS user doesn't sufficiently protect their access keys, for example by leaving them on a server, then an attacker could use those keys to gain access to any resources assigned to those keys.

Because the use of the API access keys is global, the attacker doesn't need to be an account already if the keys are exposed outside of AWS.  

### Service
AWS IAM
### Status
Confirmed
### Stride
* Information Disclosure
* Elevation of Privilege
### Components
* IAM user
* Access Key
### Mitigations
* Access key rotation. Either fixed time or dynamic using SSO
* Detection and clean up of unused access keys and users
* Assume roles where possible
### References
* https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html
* https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#lock-away-credentials

## OCST-1.3.2
### Name
Unprotected root access keys
### Description
Attacker can gain unauthorised access to ALL resources using unprotected root access keys.

The account root user has full access to all resources, including billing, and cannot be restricted.

### Service
AWS IAM
### Status
Confirmed
### Stride
* Information disclosure
* Elevation of privilege
### Components
* IAM User
* Access Key
* AWS Account (root user)
### Mitigations
* Do not use root access keys. Instead create separate administrative users or ideally users with the least privilege required for the use case.
### References
* https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html
* https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#lock-away-credentials

## OCST-1.3.3
### Name
Unexpected AWS ManagedPolicy updates
### Description
An attacker can gain elevated permissions through unexpected AWS ManagedPolicy updates.

If the organisation uses AWS provided ManagedPolicies, then they may not be aware of the updates made by AWS to those policies. If AWS introduces additional services or actions, then the organisation may have additional exposure that they're not aware of. An attacker that knows about the updates may be able to use this to their advantage.

### Service
AWS IAM
### Status
Confirmed
### Stride
* Elevation of privilege
### Components
* IAM ManagedPolicy
### Mitigations
* Use customer managed policies with non-wildcard actions.
### References
* https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html

## OCST-1.3.4
### Name
Weak password policy
### Description
An attacker can guess a user's AWS console password through weak password policy.

If an account password policy is not used, or is configured to be weak, then an attacker might be able to guess a user's password.

### Service
AWS IAM
### Status
Confirmed
### Stride
* Elevation of privilege
### Components
* IAM User
* Account Password Policy
### Mitigations
* Use a complex password policy such as having a minimum length and/or requiring specific character types.
* Use federation (SSO)
### References
* https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_passwords_account-policy.html

## OCST-1.3.5
### Name
Confused deputy
### Description
An attacker can perform the confused deputy attack by tricking a trusted third party into assuming the role of an ARN in another account.

### Service
AWS IAM
### Status
Confirmed
### Stride
* Elevation of privilege
### Components
* IAM AssumeRole
### Mitigations
* Use the optional ExternalId in a condition as a pre-shared key
### References
* https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user_externalid.html
* https://aws.amazon.com/blogs/security/tag/confused-deputy/

## OCST-1.3.6
### Name
Weak ExternalId
### Description
Where ExternalId is used, attacker can perform the confused deputy attack by tricking a trusted third party into assuming the role of an ARN in another account because the ExternalId lacked complexity and was easy to guess

### Service
AWS IAM
### Status
Confirmed
### Stride
* Elevation of privilege
### Components
* IAM AssumeRole
* ExternalId
### Mitigations
* Use a long, securely generated random ExternalId
### References
* https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user_externalid.html
* https://aws.amazon.com/blogs/security/tag/confused-deputy/

## OCST-1.3.7
### Name
Accounts used as principals
### Description
An attacker with an unprivileged user or role in a trusted (source) account may be able to gain elevated privileges in a trusting (destination) account by assuming a role in the trusting account that uses the trusted account (:root user) as a principal rather than using a specific role or user. This is because using an account as a principal exposes it to all principals in the trusted account.

### Service
AWS IAM
### Status
Confirmed
### Stride
* Elevation of privilege
### Components
* IAM AssumeRole
* Principals
* AWS Accounts
### Mitigations
* Use specific users or roles as principals
### References
* https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Principal

## OCST-1.3.8
### Name
Use of wildcard principals
### Description
Inappropriate use of wildcard ("*") principals may allow an attacker to 
escalate privileges. This is because the wildcard principal includes any 
principal, not just those in the same account. This will, unless strictly 
controlled, open the resource to any valid AWS principal.

### Service
AWS IAM
### Status
Confirmed
### Stride
* Elevation of privilege
### Components
* IAM AssumeRole
* Principals
### Mitigations
* [aws/iam/features/wildcard_principals_not_used.feature](https://github.com/owasp-cloud-security/owasp-cloud-security/blob/master/aws/iam/features/wildcard_principals_not_used.feature)
### References
* https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html
* http://docs.aws.amazon.com/AmazonS3/latest/dev/s3-bucket-user-policy-specifying-principal-intro.html

