# Identity Access Management

* Universal AWS service that deals with identity and authorization when interacting with all other AWS services
* All actions on AWS go through IAM, which determines user's identity and allows or denies the action depending on rights granted to the account by an administrator
* Validates logons to the AWS web console and checks permissions before carrying out any actions in the web console

### Accounts

* Environment owned by an organization, team or individual
* 12 digit number
* Most organizations possess a number of AWS accounts in order to separate different environments

### Users

* AWS entity associated with an individual or application
* Username can be up to 64 characters, supports some non-alphanumeric characters like "\_" and "+"
* Username is associated with a password pair or access key
* Users may optionally belong to groups in order to facilitate RBAC
* New accounts contain a single user: the root.
  * Root should not be used for day-to-day operations.
  * Use root account to create users with proper permissions to carry out day-to-day operations

### Requests and Policies

#### Requests

* Every AWS action from the web console, AWS CLI, or software utilities creates a request sent to AWS
* IAM service compares incoming request against list of permissions
  * Policies defined by account administrator that determine which actions are acceptable
* Consists of three fields
  * Principal: User or role who requested permission
  * Action: Operation principal requested to make on the resource. Name of action is composed of three letter service name and then the name of the action, which begins with a verb
    * `ec2:StartInstances`
    * `iam:ChangePassword`
  * Resource: Target of Action
* Resource identifiers are different depending on if service is trans-regional or not
  * Some services have multiple resource types and require a specific resource to be identified
  * Amazon Resource Names are created to identify a resource uniquely in as short of a manner as possible

#### Policies

* Rule controlling user, group or a role access to a resource
* Defines Action and Resource fields, along with Effect field indicating if Action should be allowed or denied
* Policy only takes effect when attached to an IAM entity
  * User
  * Group
  * Role
* Policy specifying a Resource is attached to Principal of the Action 
  * Policy specifying a Principal is attached to the Resource
* Deny actions are more powerful than allow actions

### Roles

* AWS entity that can be compared to a user, but usually not associated with an individual or precise application
* Many users are given right to assume a role \(temporarily use rights associated with a role\)
* Roles do not have passwords and use temporary identification information
* Generic roles already created on IAM are directly available, or new custom roles can be created to address precise needs.
* May be used to allow users from a different account to act on resources from role's account.

