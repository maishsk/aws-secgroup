# Ansible AWS Security Group
An Ansible role that will create a security Group in a VPC

## Requirements
- AWS credentials and the correct permissions to create the resources
- An existing VPC

## Role Variables

The variables uses in this role are:

| Variable Name | Required | Description |
|----|----|----|
| `region`| **Yes** | The region that you will deploy into |
| `vpc_name`| **Yes** | The Name of the VPC in which you will create the Security group |
| `secgroup_name`| **Yes** | The name of the Security group |
| `secgroup_desc`| **Yes** | The desciption for the Security group (required when creating security group) |
| `rules_ingress`| **No** | A set of ingress rules for the security group <br> (see example below) |
| `rules_egress`| **No** | A set of ingress rules for the security group  <br> - Default `allow 0.0.0.0/0 - ALL` |
| `aws_tags`| **No** | A list of tags to for the security group |
| `purge_rules` | **No** | Purge existing rules on security group that are not found in rules <br> - Default `Yes` |
| `purge_rules_egress` | **No** | Purge existing rules_egress on security group that are not found in rules_egress <br> - Default `Yes` |
| `purge_tags` | **No** | If yes, existing tags will be purged from the resource to match exactly what is defined by tags parameter. If the tags parameter is not set then tags will not be modified. <br> - Default `Yes` |
| `secgroup_id` | **No** | Required **only** for removal of security group |

## Dependencies

None

## Example Playbook

### Download dependencies

#### Create requirements file

Create a `requirements.yml` file with the following contents
```
- src: https://github.com/maishsk/aws-secgroup
  version: master
```

#### Download dependencies
Run the following command:
```
ansible-galaxy install -r requirements.yml --force -p .
```

### Create playbook
Create a `main.yaml` file with the following contents:
```
---
- name: Example playbook
  hosts: localhost
  connection: local
  gather_facts: false
  vars_files:
    - vars/vars.yml

  tasks:
  - name: Create Process
    include_role:
      name: "{{ item }}"
    with_items:
      - aws-secgroup
    tags: [ 'never', 'create' ]

  - name: Rollback Process
    include_role:
      name: "{{ item }}"
    with_items:
      - aws-secgroup
    tags: [ 'never', 'rollback' ]
```

Create a `vars/vars.yml` with the content similar to:
```
region: us-east-2
vpc_name: maish_test
secgroup_name: MySecgroup
secgroup_desc: Allow incoming ssh
secgroup_rules_ingress:
  - proto: tcp
    ports:
      - 22
    cidr_ip: 0.0.0.0/0
    rule_desc: Allow ssh traffic from ALL
secgroup_rules_egress:
  - proto: all
    cidr_ip: 0.0.0.0/0
    rule_desc: Allow ALL outgoing traffic
aws_tags:
 - Name: "{{ secgroup_name }}"
 - foo: bar
 - ami: 3syllables
```

## Running the playbook

To create the Security Group

`ansible-playbook main.yml --tags create`

To remove the Security Group

`ansible-playbook main.yml --tags rollback -e secgroup_id=sg-123456xyz`

## License

BSD

## Author Information
This role was created by [Maish Saidel-Keesing](https://www.maishsk.com/), author of [The Cloud Walkabout](http://cloudwalkabout.com/).