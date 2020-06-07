# Mastering Ansible

## Config Sources

* ANSIBLE\_CFG environment variables
* ansible.cfg file in current directory
* ~/.ansible.cfg file
* /etc/ansible/ansible.cfg file

## Inventory

* Even ad-hoc commands require a target taken from the inventory
* Must exist for successful operation of _ansible_ and _ansible-playbook_ commands
  * May be specified at runtime using `--inventory file (-i)`

### Static Inventory

* Most basic inventory configuration, typical format is single _ini_ format configuration
  * Hosts are listed within files
  * Special group _all_ is implicitly created
  * Best practice is to order hosts into hierarchies of groups
    * Based on expected functionality
    * Systems can exist in multiple groups and groups may be composed of other groups
    * Hosts may be listed outside of any group \(must be listed before first group declaration to avoid association\)

### Inventory Ordering

* By default, Ansible processes hosts in order specified within inventory file
* 2.4 introduced `order` keyword, although file order is still default
* Values allowed for `order` keyword:
  * `inventory` Ansible processes hosts in file order
  * `reverse_inventory` Hosts processed in reverse of inventory file order
  * `sorted` Hosts sorted alphabetically by name
  * `reverse_sorted` Floats sorted by inverse-alphabetical search
  * `shuffle` Hosts processed in random order which is randomized on each run

### Adding Variables

* Nearly everything in Ansible can include a variable reference
* Not all data can e discovered during system setup phase
  * Inventory data expands this knowledge
  * Variables take precedence depending on location of supplied value
* Values may be defined upon entire groups
  * Using all group results in value being set for all machines in the inventory

### Inventory Parameters

* ansible\_host:: DNS name or Docker container name which Ansible will initiate connection to.
* ansible\_port:: Port number Ansible will use to connect to host, default is 22
* ansible\_user:: Username Ansible will coonect to the inventory host with, regardless of connection type
* ansible\_ssh\_pass:: Provides Ansible with the password for authentication to the inventory host specified by ansible\_user
* ansible\_ssh\_private\_key\_file: Specify private key file to use for connection to host, if not using default private key or ssh-agent
* ansible\_ssh\_common\_args:: Defines SSH arguments to append to default arguments for `ssh`, `sftp`, and `scp`
* ansible\_sftp\_extra\_args:: Extra args passed to `sftp` binary when cakked by Ansible.
* ansible\_scp\_extra\_args:: Additional arguments passed to `scp` binary when called by Ansible
* ansible\_ssh\_extra\_args:: Specify additional arguments passed to `ssh` binary
* ansible\_ssh\_pipelining:: Boolean definining whether SSH pipelining should be used for host.
* ansible\_ssh\_executable:: Overrides path to SSH executable for this host
* ansible\_become:: Defines whether privilege escalation \(sudo\) should be used with this host
* ansible\_become\_method:: Method to use for privilege escalation -- sudo, su, pbrun, pfexec, doas, dzdo. ksu
* ansible\_become\_user: User to become via privilege escalation
* ansible\_become\_pass:: Password to use for escalation
* ansible\_sudo\_pass:: SUdo password to use; insecure and should be defined with `--ask-sudo-pass` instead
* ansible\_connection: Connection type of host. Candidates are `local`, `smart`, `paramiko`, `docker` or `winrm`. Defaults to `smart` which uses SSH if supported with fallback to Paramiko
* ansible\_docker\_extra\_args:: Specify extra argument that will be passed to remote Docker daemon on given host
* ansible\_shell\_type:: Determine shell type on inventory host in question. Defaults to POSIX _sh_ style syntax, but can set to _csh_ or _fish_ if installed on system
* ansible\_shell\_executable:: Determines shell type on inventory host in question. Defaults to POSIX _sh_, but can set to _fish_ or _cah_ if supported by host.
* ansible\_python\_interpreter: Manually sets paath to Python on a given host
* ansible\_\*\_interpreter:: Used to pass arguments to other interpreted language\(s\) that Ansible might depend upon \(perl or Ruby\).

### Dynamic Inventory

* Used when inventory data already exosts om a different system
  * LDAP
  * Cloud computing provider
  * Configuration Management Database
* Prevents duplication of inventory file when playbook collection grows
* Relies upon a plugin executable called by the Ansible runtime to discover real-time inventory data.
  * Can reach out to external data sources and return data
  * Can parse local data that already exists but may not be in the Ansible inventory _ini_ format.
* Provided Plug-Ins:
  * OpenStack Nova
  * Rackspace Public Cloud
  * DigitalOcean
  * Linode
  * Amazon EC2
  * Google Compute Engine
  * Microsoft Azire 
  * Docker
  * Vagrant
* Notice how majority of plugins handle cloud systems and thus will require configuration to provide login information
  * Additional arguments cannot be passed to the inventory plugin at runtime, so must use environment variables or a well-known location hosting an _ini_ configuration file to grab credentials.
* Invoked by passing executable file for inventory source
  * Ansible runs executable with a single argument provided, `--list` which requests a listing of entire inventory so that internal representation may be constructed
  * Ansible then executes script with a different argument for every host in the data to discover variable data via `--host <hostname>`

### Caching of Inventory Data

* Static or dynamic inventory is only discovered and parsed a single time, per `ansivble` or `ansible-playbook` execution
* If a playbook creates new cloud resources, the new resources will not be accessible further down the same run
* `add_host` module allows temporarily adding an inventory to the in-memory inventory object to circumvent issue
  * Requires **name** and **groups** to be defined for all new hosts to set hostname and associate new resource with existing groups
  * Any other option passed to module becomes host variable data for this host. Can define both custom variables and _ansible_ variables
* After run, new resources must be added to inventory file if static in order to avoid running `add-host` in later playbook executions; dynamic inventory should pick up resources automatically next run

### Limiting

* Every execution of `ansible` or `ansible_playvook` will parse entire inventory it has been directed at
* True even when a limit has been applied by using `--limit` flag at runtime and supplying a pattern which serves as a mask for inventory filtering
* Because of this parsing, it is still possible to lookup variable values for machines filtered out of current playbook
  * Allows tasks to be directed at a host that is otherwise limited out
  * Can be used to manipulate a load balancer to put systems into maintenance mode while being upgraded without including the load balancer system within the limit mask

## Playbooks

### Parsing

#### Order of Operations

* Nearly everything can safely be assumed to operate in top to bottom order
* Playbooks can either run a pla or include another playbook from somewhere else on filesystem
* While operations are executed in order, entire playbook and any imports are parsed completely before any executions occur
  * Included playbook imports must exist at the time of the playbook parsing and cannot be generated by a playbook operation
* Play order of operations
  * Variable loading
  * Fact gathering
  * `pre_tasks` execution
  * Handlers notified from the `pre_tasks` execution
  * Roles execution
  * Tasks Execution
  * Handlers notified from roles or tasks execution
  * The `post_tasks` execution
  * Handlers notified from `post_tasks` execution

```yaml
---
- hosts: localhost
  gather_facts: false
  
  vars:
    - a_var: derp
    
  pre_tasks:
    - name: pretask
    debug:
      msg: "a pre task"
    changed_when: true
    notify: say hi
    
  roles:
  - role: simple
    derp: newval
    
tasks:
  - name: task
    debug:
      msg: "a task"
    changed_when: true
    notify: say hi
    
post_tasks:
  - name: posttask
    debug: 
      msg: "a post task"
    changed_when: true
    notify: say hi
```

* Handlers can be triggered at any point using the utility module `meta` to run `flush_triggers` at a given point in the script
  * Instructs Ansible to process any pending handlers at that point before continuing on with the next task or next block of actions within a play
  * Provides a method of influencing playbook order when orchestration requires complicated actions and when service restarts are very sensitive to order
  * Consider the rollout of a new service
    * Tasks modifying config files indicate service should be restarted when these files change
    * Play will also indicate that the service should be running
    * In order to minimize disruptions, the handlers should be flushed right before a final task to ensure service is running

#### Play Behavior Directives

* Written at same level as the `hosts:` directive
* Common keys used
  * any\_errors\_fatal:: Instructs Ansible to treat any failure as a fatal error to prevent further tasks from being attempted. Default is to continue until all tasks are complete or all hosts have failed
  * connection:: Defines which connection system to use for a given play. Common choice is `local` to instruct Ansible to do all operations locally, but with the context of the system from the inventory
  * gather\_facts:: Controls whether or not Ansible will perform fact-gathering phase of the operation, which runs a special task on a host to uncover data about the system.
    * Skipping when you are certain none of the discovered data is relevant to the task at time can save significant time in a larger environment
  * max\_fail\_percentage:: Allows you to define what percentage of hosts are allowed to fail before the whole operation is halted
  * no\_log:: Controls whether Ansible will log to the screen or a configured log file the command given or the results received from a task Crucial when dealing with secrets. Can also be applied directly to tasks.
  * port:: Defines SSH or other remote connection plugin port unless otherwise configured within inventory
  * remote\_user:: Defines which user to log in with on remote system. Default is to connect as the same user that ansible-playbook was started with.
  * serial:: Takes a number and controls how many systems Ansible will execute a task on befoire moving to the next task in a play. Drastically changes normal order of operation where a task is executed across every system in a play before moving to the next task. Best-suited for rolling updates.
  * become:: Configures whether privilege escalation should be used on remote host to execute tasks, can also apply at task level. Related keys are `become_user`, `become_method` and `become_flags`
  * strategy:: Sets execution strategy to be used for play.
    * Defined as plugins, **linear** and **free** available after install
    * Linear strategy is default: as play is executed, all hosts for a given play execute the first task. Once all are complete, Ansible moves to the next task.
    * Free strategy breaks free from traditional behavior and allows hosts to execute next task regardless of if other hosts have completed the previous task. Minimizes execution time of each specific host.
    * Free strategy is useful with a large set of hosts, where it could prevent downtime by leading to staggered service restarts.
    * Free strategy cannot be used if there are dependencies between hosts in a task's data. Anothjer host's variable values cannot be assumed to be present or contain the latest value.



