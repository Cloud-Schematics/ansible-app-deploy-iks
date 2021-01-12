# Hackathon Starter Ansible Playbook

This playbook demonstrae how to use the Ansible to deploy the Hackathon Starter, A boilerplate for Node.js web applications to IKS cluster using ansible k8 roles. 
Github URL: https://github.com/sahat/hackathon-starter/blob/master/README.md

## Steps to run with IBM Cloud Schematics. 

1. Create `action.json` with `Action` defination. Edit the payload as per your defination and run IBM Cloud CLI `ibmcloud schematics action create -f action.json`

```
{
  "name": "Hackathon_Starter_Action",
  "description": "This Action will deploy boiler plate code for Hackathon Starter",
  "location": "us-east",
  "resource_group": "Default",
   "source": {
       "source_type" : "git",
       "git" : {
            "git_repo_url": "https://github.com/Cloud-Schematics/ansible-app-deploy-iks.git"
       }
  },
  "command_parameter": "site.yaml",
  "tags": [
    "string"
  ],
  "inputs": [
    {
      "name": "cluster_id",
      "value": <You-Cluster-ID>,
      "metadata": {
        "type": "string",
        "default_value": <Your-Default-Cluster-ID>
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
  "command_name": "ansible_playbook_run"
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
