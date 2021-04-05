# Hackathon Starter Ansible Playbook

Deploying Hackathon Starter application to IBM Cloud Kubernetes cluster.


## About this playbook

This playbook demonstrate how to use the Ansible to deploy the Hackathon Starter, A boilerplate for Node.js web applications to IKS cluster using ansible k8 roles.

## Prerequisites

1.  IBM Cloud Kubernetes cluster

    Steps to provision cluster
    
    * Login to your IBM Account
    * Select Kubernetes -> clusters from the navigation menu. (https://cloud.ibm.com/kubernetes/clusters)
    * Select `Create Cluster`. 
    * Select `Pricing Plan` as `Standard`, `Infrastructure` as `Classic`. 
    * Select `Location` and `Worker Zone` as per your choice.
    * Select `Availability` as `SingleZone`. 
    * Select `Worker nodes per zone` as `1`. 
    * Give the required `Cluster name` and click `Create`.

## Input variables

|Input variable|Required/ optional|Data type|Description|
|--|--|--|--|
|cluster_id|Required|String|ID of the cluster in IBM cloud account|

## Running the playbook in Schematics by using UI

1. Open the [Schematics action configuration page](https://cloud.ibm.com/schematics/actions/create?name=deployapp&url=https://github.com/Cloud-Schematics/ansible-app-deploy-iks).
2. Review the name for your action, and the resource group and region where you want to create the action. Then, click **Create**.
3. Select the `site.yml` playbook.
4. Select the **Verbosity** level to control the depth of information that will be shown when you run the playbook in Schematics.
5. Expand the **Advanced options**.
6. Enter all required input variables as key-value pairs. Then, click **Next**.
7. Click **Check action** to verify your action details. The **Jobs** page opens automatically. You can view the results of this check by looking at the logs.
8. Click **Run action** to deploy the Hackathon Starter. You can monitor the progress of this action by reviewing the logs on the **Jobs** page.

## Running the playbook in Schematics by using the command line

1. Create the Schematics action. Enter all the input variable values that you retrieved earlier. When you run this command and are prompted to enter a GitHub token, enter the return key to skip this prompt.
   ```
   ibmcloud schematics action create --name deployapp --location us-south --resource-group default --template https://github.com/Cloud-Schematics/ansible-app-deploy-iks --playbook-name site.yml --input "mysql_port": "<mysql_port>" --input "httpd_port": "<httpd_port>" --input "dbuser": "<dbuser>" --input "upassword": "<db_password>"
   ```

   Example output:
   ```
   Enter github-token>
   The given --inputs option region: is not correctly specified. Must be a variable name and value separated by an equals sign, like --inputs key=value.

   ID               us-south.ACTION.deployapp.1aa11a1a
   Name             deployapp
   Description
   Resource Group   default
   user State       live

   OK
   ```

2. Verify that your Schematics action is created and note the ID that was assigned to your action.
   ```
   ibmcloud schematics action list
   ```

3. Create a job to run a check for your action. Replace `<action_ID>` with the action ID that you retrieved. In your CLI output, note the **ID** that was assigned to your job.
   ```
   ibmcloud schematics job create --command-object action --command-object-id <action_ID> --command-name ansible_playbook_check
   ```

   Example output:
   ```
   ID                  us-south.JOB.deployapp.fedd2fab
   Command Object      action
   Command Object ID   us-south.ACTION.deployapp.1aa11a1a
   Command Name        ansible_playbook_check
   Name                JOB.deployapp.ansible_playbook_check.2
   Resource Group      a1a12aaad12b123bbd1d12ab1a123ca1
   ```

4. Verify that your job ran successfully by retrieving the logs.
   ```
   ibmcloud schematics job logs --id <job_ID>
   ```

5. Create another job to run the action. Replace `<action_ID>` with your action ID.
   ```
   ibmcloud schematics job create --command-object action --command-object-id <action_ID> --command-name ansible_playbook_run
   ```

6. Verify that your job ran successfully by retrieving the logs.
   ```
   ibmcloud schematics job logs --id <job_ID>
   ```

## Troubleshooting

### How to debug and get verbose logs while running actions?


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

###  How to get the public IP of webapp service in your kubernetes cluster?

1. Login to IBM Cloud account on CLI. 
2. Run `ic ks clusters` to list all cluster. 
3. Run `ic ks cluster config -c <ClusterID>` to set kubectl context. 
4. Run `kubectl describe svc webapp` to get details about webapp service. 
5. Find the field `LoadBalancer Ingress` to get the external IP. 

### Why my load balancer does not have a public IP? 
This can be because of subnet or network policies which were used while cluster creation. 

### Why I am getting error `Action Name Should be Unique`?
In schematics no two action can have the same name in an account. Please change the action name. 



## Verification

Browse the application on the public IP of webapp service in your kubernetes cluster.

![](./welcome_screen.png)

## Delete an action

1. From the [Schematics actions dashboard](https://cloud.ibm.com/schematics/actions){: external}, find the action that you want to delete.
2. From the actions menu, click **Delete**.

## Reference

Review the following links to find more information about Schematics and Hackathon Starter

- [IBM Cloud Schematics documentation](https://cloud.ibm.com/docs/schematics)
- [Hackathon Starter](https://github.com/sahat/hackathon-starter/blob/master/README.md)

## Getting help

For help and support with using this template in IBM Cloud Schematics, see [Getting help and support](https://cloud.ibm.com/docs/schematics?topic=schematics-schematics-help).