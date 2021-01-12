# Hackathon Starter Ansible Playbook

The example makes use of out of the box Ansible roles to install the open-source [‘Hackathon Starter’]( https://github.com/sahat/hackathon-starter.git) application. It deploys the application onto a pair of ‘frontend’ nginx app servers and deploys mongodb as a ‘backend’ database server. It assumes that a suitable target VPC environment has been deployed using the example [VPC with SSH access and Bastion Host for Redhat Ansible](https://github.com/Cloud-Schematics/multitier-vpc-bastion-host).
This playbook demonstrate how to use the Ansible to deploy the Hackathon Starter, A boilerplate for Node.js web applications to IKS cluster using ansible k8 roles. 
Github URL: https://github.com/sahat/hackathon-starter/blob/master/README.md

Upon successful execution, which will take 10 to 15 minutes, the Hackathon Starter website will be accessible at the DNS address of the VPC load balancer. This is available in the output of the Schematics Apply operation   

This example was written for use with IBM Cloud Schematics and assumes that the target VPC environment created by the [VPC with SSH access and Bastion Host for Redhat Ansible](https://github.com/Cloud-Schematics/multitier-vpc-bastion-host) was also deployed using Schematics. 



## Steps to run with IBM Cloud Schematics. 

## Actions CLI:

1. Create `action.json` with `Action` defination. Edit the payload as per your defination and run IBM Cloud CLI `ibmcloud schematics action create -f payload.json`

```
{
  "name": "Hackathon_Starter_Action",
  "description": "This Action will deploy boiler plate code for Hackathon Starter",
  "location": "us-east",
  "resource_group": "Default",
   "source": {
       "source_type" : "git",
       "git" : {
            "git_repo_url": "https://github.com/rvsingh011/RoleBasedAnsible.git"
       }
  },
  "command_parameter": "ansible_playbook.yaml",
  "tags": [
    "string"
  ],
  "inputs": [
    {
      "name": "cluster_id",
      "value": <You-Cluster-ID>,
      "metadata": {
        "type": "string",
        "default_value": <Your-Default-Cluster-ID>,
      }
    }
  ],
  "source_type": "GitHub" 
}
```

2. Create Job to run the above action. Create `job.json` with `job` defination. Edit the `Action ID` from step 1 and run `ibmcloud schematics job create -f payload.json`

```
{
  "command_object": "action",
  "command_object_id": <Action-ID>,
  "command_name": "ansible_playbook_run",
  "command_parameter": "ansible_playbook.yaml"
}
```

3. Once the job complete browse  

![](./welcome_screen.png)


## Debug 

For debugging purpose create action with verbose settings.

```
"inputs": [...],
"settings":[
      {
      "name": "verbose",
      "value": "4",
       "metadata": {
        "type": "string",
        "default_value": "0",
        "secure": false,
        "immutable": false,
        "hidden": false
      }
      }
  ],
  "tags": [
    "string"
  ]
  ```

## Actions UI: (WIP)
