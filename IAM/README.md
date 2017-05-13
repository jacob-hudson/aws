# Identity and Access Management (IAM)

## Principals
- A principal is an IAM entity that is allowed to interact with AWS resources
- It can be permanent or temporary and can represent a human or application
- Three types
  - Root users
  - IAM users
  - roles/temporary security tokens

### Root User
- Is the only default sign-in principal and has complete access to all AWS Cloud services and resources
- As long as the AWS account is open, the root user will persist
- The AWS root is similar in concept to the Unix root - it can do anything, including closing the account
- It is strongly recommended to not use root for every day tasks, best practice is to use the root user to create the first IAM user and then secure the root user credentials

### IAM Users
- Users are persistent identities set up through IAM to represent people or applications
- Policies for users can be very granular so that the principle of least privilage can be used

## Roles/Temporary Security Tokens
- Sets specific privileges to specific actors for a set time
- Can be authenticated by AWS or a trusted external system
- When an actor assumes a role, AWS provides it with a temporary security token from the AWS STS that the actor uses to access services
- Use Cases
  - EC2 roles
  - Cross-account access
  - Federation

## Authentication
- Authentication options
  - User/Pass
    - Represents a human interacting with the console
    - Password policies can be set
  - Access Key
    - Combination of a 20 character ID and 40 character secret
    - Underlying REST calls will be signed with the key should the API be used
  - Access Key/Session Token
    - Assumed roles also need session tokens in addition to the ID/secret in order to authenticate
- When an IAM user is created, it lacks both a password and an access key (admins can set up either or both)

## Authorization
- The process of specifying exactly what actions a principal can and cannot perform

### Policies
- JSON document that fully defines a set of permissions to access and manipulate AWS resources
- Permission Definitions
  - Effect
    - A single word - Allow or Deny
    - Service
      - What service is allowed or denied
    - Resource
      - Specifies the AWS infrastructure for which this applies (listed as an ARN)
    - Action
      - Specifies the allowed or denied subset of actions within a service
    - Condition
      - - Optionally defines one or more additional restrictions that limit the actions allowed by the permission

### Associating Principles with Policies
- Common ways of association
  - User Policy
    - Exists only in the context of the user to which they are attached
  - Managed policies
    - Exist independently of any individual user
    - Many users can be associated with the same policy
  - Assuming a role
    - Can be an authenticated IAM user, the user must have the rights to assume the role
    - A person or process authenticated by a trusted service outside AWS, the AWS service will assume the role on the actor's behalf and return a token to the actor

## Other Key Features
- These are all additional services to realize the full benefits of IAM

### Multi-Factor Authentication (MFA)
- Authentication is done through a password and OTP from a device such as a Yubikey
- MFA can be assigned to any IAM user account
  - A person will be prompted for the OTP
  - An application will query the app user to provide the current OTP, which will get passed to the API
- MFA on the root user is strongly recommended

### Rotating Keys
- IAM allows two active keys at one time
- The process
  - Create a new key
  - Reconfigure applications to use the new key
  - Disable the original key (not deleting it, to allow for rollback)
  - Verify operation of apps
  - Delete the old key

### Resolving Multiple Permissions
- Conflicts between competing permissions are resolved
  - Initially, the request is denied by default
  - All appropriate policies are evaluated; the request is denied if an explicit deny is found in any policy
  - If no explicit deny is found *and* an explicit allow is found, the request is allowed
  - If there are no explicity allow or deny permissions, the default deny is maintained the the request is denied
- The only exception to the rule is if an `AssumeRole` call includes a role and a policy, the policy cannot expand the privileges of the role (cannot override denies)
