# Automating web application deployment on IBM Cloud Kubernetes Service

IBM Cloud Schematics actions uses Hackathon Starter web application to create an Ansible playbook template. Ansible playbook is preconfigured with the tools and softwares required to deploy the Hackathon Starter application. All that you need to do is create an IBM Cloud Kubernetes service, create Indentity and Access Management (IAM) and IAM refresh token to use the powerful Ansible playbook to automate the web application deployment by using IBM Cloud Kubernetes Service and Schematics action.

[Hackathon Starter](https://github.com/sahat/hackathon-starter) is a web application developed by using Node.js, MongoDB, command line tools supporting multiple Operating System, and distributed in Git. 

IBM Cloud Schematics provides powerful tools to automate your cloud infrastructure provisioning and management process, the configuration and operation of your cloud resources, and the deployment of your app workloads. To do so, Schematics leverages open source projects, such as Terraform, Ansible, OpenShift, Operators, and Helm, and delivers these capabilities to you as a managed service. Rather than installing each open source project on your machine, and learning the API or CLI, you declare the tasks that you want to run in IBM Cloud and watch Schematics run these tasks for you. For more information about Schematics, see [About IBM Cloud Schematics](https://cloud.ibm.com/docs/schematics?topic=schematics-about-schematics).

## About this playbook

This playbook demonstrate how to use the Ansible playbook in IBM Cloud Schematics action to deploy the Hackathon Starter web application. To run this playbook, you must have a Cluster ID for Classic that you want to deploy the web application. When you run this playbook, Schematics securely connects to the target cluster by using the IAM and Refresh token that you configured when you created an action.

## Prerequisites

1. Provision an IBM Cloud Kubernetes cluster by following these steps. You can also refer to [creating a free classic cluster](https://cloud.ibm.com/docs/containers#clusters_gs).
    
    - In the [IBM Cloud Catalog](https://cloud.ibm.com/kubernetes/clusters). Login to IBM Cloud account to view the **Kubernetes** page.
    - Click the `Create Cluster` button.
    - Select the `Standard` or `Free` plan.
    - Select Kubernetes as your container platform and select the Kubernetes version `1.19.9` or later.
    - Select `Classic` infrastructure.
    - Enter unique `Cluster name`.
    - Select `Availability` as `SingleZone`.
    - Select `Worker nodes per zone` as `1`.
    - Select the **Resource group** that you want to create your cluster in. For example `default`. You cannot change the resource group later.
    - In the **Summary** pane, review the order summary and then click **Create**. A worker pool is created that contains one worker node in the default resource group.

   You need to wait cluster the creation, and cluster ID is generated successfully. Once the cluster is created. From the **Clusters** page, select **Overview** to view the Cluster ID in the **Summary** pane. Make a note of the Cluster ID.

2. Input variables for the template.

|Input variable|Required/ optional|Data type|Description|
|--|--|--|--|
|cluster_id|Required|String|[Cluster ID]((https://cloud.ibm.com/catalog?category=containers#services)) from your IBM Cloud account.|
|IC_IAM_TOKEN|Required|String|Enter [IAM token](https://cloud.ibm.com/docs/account?topic=account-serviceauth) from  your IBM Cloud your account.|
|IC_IAM_REFRESH_TOKEN| Required| String |Enter your IAM refresh token. From your terminal run `cat ~/.bluemix/config.json` to view your IAM refresh token.|

## Running the playbook in Schematics by using console

1. Open the [Schematics action configuration page](https://cloud.ibm.com/schematics/actions/create?name=deployapp&url=https://github.com/Cloud-Schematics/ansible-app-deploy-iks).
2. Review the name for your action, and Repository URL are pre-populated, optionally, you can enter an the unique name for an **Action name**.
3. Review the resource group and location where you want to create an action. 
4. Click the **Create** button, and allow to retrieve your playbook details.
5. Click **Retrieve playbook** button to view the playbook list.
6. Select the `site.yml` playbook.
7. Select the **Verbosity** level to control the depth of log information to display. For example, enter `4` to check detailed log information. Refer to [create an action](https://cloud.ibm.com/docs/schematics?topic=schematics-action-setup#create-action) for more information.
5. Click the **Advanced options** tab. then click **Add input value** to add the variables.
6. Enter the input variables in the **Key** and **Value** format. Check **Sensitive**, to protect the text value.

| Key | Value |
| ---- | ---- |
| cluster_id | `Enter your cluster ID` |
| IC_IAM_TOKEN | `Enter your IAM token` |
| IC_IAM_REFRESH_TOKEN | `Enter your Refresh token` |

7. Click **Save**.
8. Click **Check action** validates your action details. In case of any issues, you can recheck the configuration and [edit the settings](https://cloud.ibm.com/docs/schematics?topic=schematics-action-setup#action-settings) and recheck an action.
9. Click **Run action** to deploy the Hackathon Starter. You can monitor the progress of an action from the [job logs](https://cloud.ibm.com/docs/schematics?topic=schematics-action-setup#action-jobs) from the Jobs page.

To verify your deployment, continue with verification steps.

## Verification

1. From the IBM Cloud **Kubernetes > Cluster** console page, click your cluster name.
2. Click the **Kubernetes dashboard** button to view the  **Kubernetes Overview** page.
3. Select **Services > Services** to view the **External Endpoints** for your webapp-service. 

**Sample screen capture**

![External endpoint for webapp service](/images/external_ip_webapp.png)

4. Click the external link icon to view your web application page. Or optionally, browse by using your `External endpoint` IP address, for example, `https://155.155.155.155` to view the output as shown in the screen capture.

**Sample output**

![Successfully deployed web application](/images/hackathon_webapp.png)

## Running the playbook in Schematics by using the command line

1. Create the Schematics action create command by using the example syntax. You need to enter all the input variable values that was mentioned in the prerequisites. Replace the input values with your values. 

  **Example** 
   ```
   ibmcloud schematics action create --name testdeployapp2 --location us-south --resource-group Default --template https://github.com/Cloud-Schematics/ansible-app-deploy-iks --playbook-name site.yml  --input cluster_id=123456 --input IC_IAM_TOKEN=Bearer 123123adsfadfa123123 --input IC_IAM_REFRESH_TOKEN=asdfadsf123etsdfds131234
   ```
2. Run the command, you are prompted to **Enter github-token**, enter the return key to skip this prompt. Your action is created and action ID details are displayed. If you find any error, see `Troubleshooting` section to debug.

   **Example output**
   ```
   ID               us-south.ACTION.testdeployapp2.90064b12
   Name             testdeployapp2
   Description
   Resource Group   Default
   user State       live

   OK
   ```
2. Verify that your Schematics action is created and note your action ID that is assigned to your action. You can run following list command to view your action ID.
   ```
   ibmcloud schematics action list
   ```
3. Create a job to validate the action configuration is successful. Replace `<action_ID>` with your action ID.
   ```
   ibmcloud schematics job create --command-object action --command-object-id <action_ID> --command-name ansible_playbook_check
   ```
4. Create a job to run your action. **Note** Replace `<action_ID>` with the action ID that you retrieved. In your CLI output, note the **ID** that was assigned to your job.
   ```
   ibmcloud schematics job create --command-object action --command-object-id <action_ID> --command-name ansible_playbook_run
   ```
4. Verify that your job ran successfully by retrieving the logs. You can run `ibmcloud schematics job list` to list your job IDs.
   ```
   ibmcloud schematics job logs --id <job_ID>
   ```

To verify your deployment, continue with verification steps.

## Verification

You can view the webapp service details that are created in your Cluster ID. Follow these command to view the webapp service and external IP address or external endpoints to access your deployed web application.

1. Login to IBM Cloud account on command, where you have created the clusters.
2. Run `ic ks clusters` to list all cluster to view the output in this format.
   ```
   NAME             TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
   kubernetes       ClusterIP      122.22.0.1        <none>          443/TCP        14h
   mongo            ClusterIP      121.121.121.121   <none>          27017/TCP      57m
   webapp-service   LoadBalancer   141.141.141.141   155.155.155.155 80:30170/TCP   57m
   ```
3. Run `ic ks cluster config -c <ClusterID>` to set kubectl context. You can see the `OK The configuration for <your cluster ID> was downloaded successfully.`
4. Run `kubectl describe svc webapp` to view details about webapp service. 
   ```
   Name:                     webapp-service
   Namespace:                default
   Labels:                   <none>
   Annotations:              <none>
   Selector:                 app=webapp
   Type:                     LoadBalancer
   IP:                       141.141.141.141
   LoadBalancer Ingress:     155.155.155.155
   Port:                     <unset>  80/TCP
   TargetPort:               8080/TCP
   NodePort:                 <unset>  30170/TCP
   Endpoints:                130.130.155.155:8080
   Session Affinity:         None
   External Traffic Policy:  Cluster
   Events:                   <none>
   ```
5. From the `kubectl describe svc webapp` output, note the value of the `LoadBalancer Ingress` variable is generated. This IP address the external endpoint to test  Hackathon Starter web application.
6. Browse by using your `LoadBalancer Ingress` IP address, for example, https://155.155.155.155 to view the output as shown in the screen capture.

![Successfully deployed web application](/images/hackathon_webapp.png)

## Troubleshooting

### How to debug and get verbose logs while running actions?

For debugging purpose create action with [higher verbosity level](https://cloud.ibm.com/docs/schematics?topic=schematics-action-setup#create-action) settings. 

###  How to get the public IP of webapp service in your kubernetes cluster?

1. Login to IBM Cloud account on CLI. 
2. Run `ic ks clusters` to list all cluster. 
3. Run `ic ks cluster config -c <ClusterID>` to set kubectl context. 
4. Run `kubectl describe svc webapp` to get details about webapp service. 
5. Find the field `LoadBalancer Ingress` to get the external IP. 

### Why my load balancer does not have a public IP? 

This can be because of subnet or network policies which were used while cluster creation. 

### Why I am getting error Action name Should be Unique?

In Schematics no two action can have the same name in an account. Please change the action name. 

## Delete an action

1. From the [Schematics actions dashboard](https://cloud.ibm.com/schematics/actions){: external}, find the action that you want to delete.
2. From the actions menu, click **Delete**.

## Reference

Review the following links to find more information about Schematics and Hackathon Starter

- [IBM Cloud Schematics documentation](https://cloud.ibm.com/docs/schematics)
- [Hackathon Starter](https://github.com/sahat/hackathon-starter/blob/master/README.md)

## Getting help

For help and support with using this template in IBM Cloud Schematics, see [Getting help and support](https://cloud.ibm.com/docs/schematics?topic=schematics-schematics-help).
