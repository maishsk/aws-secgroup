# Ansible AWS Security Group
An Ansible role that will create a security Group in a VPC

## Requirements
- AWS credentials and the correct permissions to create the resources
- An existing VPC

## Role Variables

The variables uses in this role are

| Variable Name | Required | Description | 
|----|----|----|
| `region`| **Yes** | The region that you will deploy into |

## Dependencies

None

## Example Playbook

### Download dependencies

#### Create requirements file

Create a `requirements.yml` file with the following contents
```
- src: https://github.com/maishsk/<REPO_NAME>
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
      - secgroup
    tags: [ 'never', 'create' ]

  - name: Rollback Process
    include_role:
      name: "{{ item }}"
    with_items:
      - secgroup
    tags: [ 'never', 'rollback' ]
```

Create a `vars/vars.yml` with the content similar to:
```
region: us-east-2
aws_tags:
 - foo: bar
 - blah: meh
 - ami: 3syllables
```

## Running the playbook

To create the <ENTITY>

`ansible-playbook main.yml --tags create`

To remove the <ENTITY>

`ansible-playbook main.yml --tags rollback`

## License

BSD

## Author Information
This role was created by [Maish Saidel-Keesing](https://www.maishsk.com/), author of [The Cloud Walkabout](http://cloudwalkabout.com/).


