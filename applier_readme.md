# EC Applier Inventory


## Overview
This is an ansible inventory which identifies all application, deployed to run EC. When the application is run as noted in the Usage section all elements of EC will be deployed to a project. By default the project name is ec. 

Currently, the following components are included in this inventory:
* elasticsearch
* mariadb (ephemeral)
* ec-console
* ec-api
* ec-broker
* ec-vpn - requires elevated privileges for pod to run - essentially privileged

## Prerequisites
* [Ansible](http://docs.ansible.com/ansible/latest/intro_installation.html)
* [OpenShift CLI Tools](https://docs.openshift.com/container-platform/3.7/cli_reference/get_started_cli.html)
* Access to the OpenShift cluster (https://****.com:8443)

## Usage
1. Log on to an OpenShift server `oc login -u <user> https://<server>:<port>/`
    1. User needs permissions to deploy ProjectRequest objects
2. Clone this repository
3. Install the required [casl-ansible](https://github.com/redhat-cop/casl-ansible) dependency
    1. `[iac]$ ansible-galaxy install -r requirements.yml --roles-path=.`
4. Run the ansible playbook provided by the casl-ansible
    1. `[iac]$ ansible-playbook casl-ansible/playbooks/openshift-cluster-seed.yml -i inventory --connection local`

## Notes

There are certificates and credentials in the param and secret folders. These are for demonstration purposes only. Please use really credentials for non-trivial applications

## Layout
- `inventory`: a standard [ansible inventory](http://docs.ansible.com/ansible/latest/intro_inventory.html). 
  - the `group_vars` are written according to [the convention defined by the openshift-applier role](https://github.com/redhat-cop/casl-ansible/tree/master/roles/openshift-applier#sourcing-openshift-object-definitions).
  -  the `hosts` file reflects the fact that the playbook will use the OpenShift CLI on your localhost to interact with the cluster
- `openshift-templates`: a set [OpenShift templates](https://docs.openshift.com/container-platform/3.7/dev_guide/templates.html) to be sourced from the inventory. OpenShift provides a lot of templates out of the box, and [the Labs team curates a repository](https://github.com/rht-labs/labs-ci-cd/tree/master/templates) as well. These should be favored before writing custom/new templates to be kept here.
- `params`: a set of [parameter files](https://docs.openshift.com/container-platform/3.7/dev_guide/templates.html#templates-parameters) to be processed along with their respective OpenShift template. the convention here is to group files by their application.
- `projectrequests`: processing templates for `ProjectRequest` objects in OpenShift requires elevated privileges, so we process `ProjectRequests` without templates because we want normal users to be able to run these playbooks. this directory contains the object definitions.
