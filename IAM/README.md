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
