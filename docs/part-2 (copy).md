# Part 2 - Configure GitHub & Jenkins 

Before we begin to configure Github & Jenkins, let's look at our lab environment. 

Open your Linux workstation terminal where the git repository is located. Run the command below to show your current lab environment. This shows you all information you need to configure your Jenkins in this section.

```
./show-mylab-env.sh
```

The output should look like below.

```
Your Rancher Server URL: https://52.221.224.193

My Harbor Instance ...
URL: https://18.138.241.59:30443
User: admin
Password: V4P6Z8NA3yXpEjrpIjPl9O05ITnXhM

Your Jenkins instance is ready ...
http://13.251.59.142:31409/login
Username: admin
Password: DIY1aHNzqfn3Hk79L7UeQ8

Your Jenkins Github webhook Payload URL:
http://13.251.59.142:31409/github-webhook/

Your Anchore is now successfully provisioned.
URL: http://anchore-anchore-engine-api.anchore.svc.cluster.local:8228/v1/
User: admin
Password: ZuhWUXq0BvKfnCgHP4eqIIIVVR6oKzko

Your Sonarqube instance is ready ...
http://13.251.59.142:30612/login
username: admin
initial password: admin


My Github personal access token:

My SonarQube token:

```

A file `mylab_env.txt` should also have created for you. Use text editor to open this file and get ready to record down your further tokens to be collected in this part.

## 1 - Fork the Spring-Petclinic Project into your own Github Account

Open a new Browser & past the below link

```
https://github.com/dsohk/spring-petclinic
```

Click on Fork Icon on top right hand window pane & select your own account where the repo will be forked.

![Forked repo visibility in your GitHub Account](./images/step3-part2-forking-spring-petclinic.png)

### Adapt Jenkinsfile for workshop
In GitHub, navigate to your forked repoistory. Find the code in `Jenkinsfile` and replace `yourname` with your github account name.

Click on the Jenkinsfile to open & click on `pencil/pen` like icon next to words `Raw | Blame` on right hand top page to edit the file to make changes to you forked repo in Github
![Forked repo visibility in your GitHub Account](./images/step3-part2-forking-spring-petclinic-how-to-edit-jenkinsfile.png)

Look for section below in Jenkin files & replace `yourname` to `Your Github ID`
![Code change after forked repo](./images/github-repo-code-change-yourname.png)

Sample Output for reference.
![Code change after forked repo](./images/step3-part2-forking-spring-petclinic-editing-jenkinsfile-changing-userid.png)

Once the changes are made, scroll down to the bottom of the page & hit `Commit changes` which will commit your change to the `Main` branch of your forked repo. 

![Saving changes to forked repo](./images/step3-part2-forking-spring-petclinic-making-changing-userid-save.png)


## 2 - Fork the Spring-Petclinic-Helmchart Project into your own Github Account

Open a new Browser & past the below link

```
https://github.com/dsohk/spring-petclinic-helmchart
```
Click on Fork Icon on top right hand window pane & select your own account where the repo will be forked.

Once the above 2 Repo are forked, those repos are available in your GitHub account. You can check your Repositories to validate. 

![Saving changes to forked repo](./images/step3-part2-2-fork-repo-success.png)

## 3 - Configure Github with Personal Access Token & Webook

### 1. Generate Personal Access Token

In order to integrate Jenkins with your github account, we have to generate your personal access token for this.

1. Login to your github account
2. Under your avatar icon, pull down the menu and choose `Settings` menu item.
3. Choose `Developer Settings` menu on the left, choose `Personal Access Tokens`
4. Click `Generate new token` button.
5. Enter `workshop` (or whatever you like) in the name field.
6. Choose `repo` and `user:email` in the privieged for this token.

![GitHub Personal Access Token](./images/part3-setup-my-github-personal-access-token-repo.png)

![GitHub Personal Access Token](./images/part3-setup-my-github-personal-access-token-user-email.png)

7. Save and record down the generated token in the `mylab_env.txt` file for configuring CI Pipeline in Jenkins later.

### 2. Create WebHook for forked repository `

Setup git webhook for spring-petclinic repo to your Jenkins server

1. Click `Settings` in your spring-petclinic github repo.
2. Choose `Webhooks` from the left menu.
3. Click `Add Webhook` button
4. Enter Payload URL: http://<YOUR_JENKINS_IPADDRESS>:<YOUR_JENKINS_PORT>/github-webhook/
5. Choose `Send me everything` for events to trigger this webhook.
6. Click `Add Webhook` button.

![Setup Github webhook](./images/github-webhook.png)


## 4 - Generate Sonarqube Token

1. Login to your Sonarqube instance. Upon successful login, you will be prompt to change your initial password. 
2. Choose `Add a Project`. 

![Add Project in Sonarqube](./images/sonarqube-add-project.png)

3. Select `Manually` to continue
3. Enter `spring-petclinic` in Project Key and Display Name input field and hit `Set up`
4. In the Provide a token input field, enter `spring-petclinic` and click `Generate Token` button.
5. Record the generate token in your `mylab_env.txt` file for configuring Jenkins later.

## 5 - Configure Jenkins 

### 1. GitHub Credentials. 

1. Login to Jenkins
2. Navigate to the Jenkins Dashboard.
2. Choose `Manage Jenkins` on the left menu
3. Choose `Manage Credentials` on the security section.
4. Under Stores scoped to `Jenkins`, click the `(global)` dropdown menu. Choose `Add credentials`.
5. In the `Add Credentials` form, choose `Secret text` in `Kind` field.
6. Enter your Github's personal access token in the `Secret` field.
7. Enter `my-github` in the `ID` field. Please MAKE SURE this is correct as to match the value in our Jenkins Pipeline.
8. Click `OK` button to continue
9. Navigate back to the Jenkins Dashboard.

### 2. Sonarqube Credentials 

1. Login to Jenkins
2. Navigate to the Jenkins Dashboard.
2. Choose `Manage Jenkins` on the left menu
3. Choose `Manage Credentials` on the security section.
4. Under Stores scoped to `Jenkins`, click the `(global)` dropdown menu. Choose `Add credentials`.
5. In the `Add Credentials` form, choose `Secret text` in `Kind` field.
6. Enter your Sonarqube token in the `Secret` field. Token is stored in the `mylab_env.txt`
7. Enter `sonarqube-spring-petclinic` in the `ID` field. 
8. Click `OK` button to continue
9. Navigate back to the Jenkins Dashboard.

### Configure Jenkins System 

1. Login to Jenkins
2. Navigate to `Managing Jenkins`, then choose `Configure System`.

#### Setup Global Environment variables

1. Login to Jenkins
2. Navigate to `Managing Jenkins`, then choose `Configure System`.

Go to `Global Properties` section.

1. Enable `Environment Variables` checkbox
2. Click `Add` button to continue.
3. Add New Environment Variable
   * Key: `HARBOR_URL`
   * Value: (Your Harbor_URL) (just IP:PORT - no http:// or https://)

![Configure Global Variables in Jenkins](./images/jenkins-configure-global-vars.png)

#### Sonarqube

Enable `Environment variables` checkbox

In the `Sonarqube installations` click on `Add SonarQube`
section, enter the following.

* Name: `My SonarQube` Note name must spelled excatly as mentioned here.
* Server URL: (Your SonarQube URL)

Under `Server authentication token` use the drop down option and you should see `sonarqube-spring-petclinic`. Select it. 

![Configure Sonarqube integration in Jenkins](./images/jenkins-configure-sonarqube.png)


#### Git plugin

Specify the github username and email account in this section. It can be any arbitrary account. It will be showing up the commits to your forked helm chart repository later.

1. Global Config user.name : jenkins
2. Global Config user.email: jenkins@example.com

#### Anchore Container Image Scanner

1. Engine URL: (Your Anchore URL)
2. Engine Username: (Your Anchore username)
3. Engine Password: Click on `change Password` and replace with your Anchore password)

Click `Save` button to save the Jenkins configuration settings.

With this, we are ready to move to the [Step 3 - Build Your Jenkins pipelines](part-3.md)








































# Part 2 - My First Jenkins Pipeline

With Jenkins, Sonarqube, Anchore running on the Rancher-managed devsecops cluster, let's build our very first Jenkins pipeline.

 
In your linux terminal, run the below command from your directory where you have GitRepo cloned. 

This shows you all information you need to configure your Jenkins in this section.

```
./show-mylab-env.sh
```

## Build my first Jenkins Pipeline

1. Login to Jenkins with the URL and credentials generated from Part 1.
2. Click `Create Job`

![Create Job in Jenkins](./images/jenkins-create-job.png)

3. Enter a name `first`
4. Choose `Pipeline` and click OK.

![Create Pipeline in Jenkins](./images/jenkins-create-pipeline.png)

5. Jenkins will navigate to the Configure Pipeline page, navigate to Pipeline section

![Create Job in Jenkins](./images/jenkins-configure-first-pipeline.png)

6. Choose `try sample Pipeline` pulldown menu.
7. Choose `Declarative (Kubernetes)`

![Create Job in Jenkins](./images/part2-step-build-my-firest-pipeline-jenkins-configure-first-pipeline-declarative-kubernetes.png)

8. Click `Save` button
9. On the left menu, click `Build Now` to trigger the first jenkins pipeline.
10. With the first Jenkins job we are building an simple contianer with Ubuntu Image & deploying on Kubernetes Cluster `devsecops`. Once the job is completed the Container get terminated 

Click on Status inside the job to view job status.

![ First Job pos in Rancher UI](./images/part2-jenkins-ui-job-build-status.png)

In the above illustration, under `build history` you will find `#1` which indicated our first build job is completed and green check mark indicates job is build successfully.

During the Pipeline build stage, you can also toggle to Rancher UI to see the container spining up & terminating once the job is done, 

In Rancher UI, Toggle to `devsecops` cluster. Click on `Cluster Explorer` & in `Pods` section you can see the progress of Jenkins jobs under Jenkins Namespace.

Below images illustrates the Jenkins pipeline has successfully create the pods & later you can see it getting terminated once the job is completed successfully

![ First Job pos in Rancher UI](./images/part2-step-build-my-firest-pipeline-pod-running-status.png)


![Create Job in Jenkins](./images/part2-step-build-my-firest-pipeline-pod-terminating-post-jobrun.png)


## Build my second Jenkins Pipeline

Like the first pipeline, create the `second` pipeline but this time choose `Maven (Kubernetes)` from `try sample Pipeline` pulldown menu.

![Create Job in Jenkins](./images/part2-step-build-my-second-pipeline-maven-kubernetes.png)

Click `Build Now` to trigger the second Jenkins pipeline.

With our 2nd job, we are building a simple `Maven` container.

![Job Status in Jenkins and Rancher UI's ](./images/part2-step-build-my-second-pipeline-maven-kubernetes-success.png)

You should now have 2 pipelines created in Jenkins which will create Kubernetes pods on Rancher-managed cluster to run each job. 

![Pipeline list in Jenkins](./images/jenkins-pipeline-list.png)

## Examine Cluster Explorer in Rancher

Now, let's examine in the pods in Rancher. You will notice the pods will be created on demand everytime when the Pipeline runs and got terminated once it's finished.

Rancher UI select DevSecOps Cluster & click explorer 

In the left hand navigation plane select Pods

Select Jenkins Namespace from dropdown. 

In the Pod Plane you should see Jenkins and your pipeline pods been created & then once the job is over getting teminated 

Image below will illustrate the same.

![Jenkins Pipeline Pods in RKE](./images/jenkins-pods-in-rke.png)

With the Jenkins being verified to work well with Rancher-managed Kubernetes, let's move on to 
[Step 3 - Build CI Pipeline for spring-petclinic](part-3.md).
