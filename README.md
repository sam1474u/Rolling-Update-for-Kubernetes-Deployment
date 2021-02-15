# Rolling-Update-for-Kubernetes-Deployment
Tutorial to Perform Rolling Update for Kubernetes Deployment

## Tenancy setup for Kubernetes Labs: Migration of Monolith to Cloud Native

### Step 1: Create the workshops compartment

Click the "Hamburger" menu (three bars on the upper left)

Scroll down the list to the Governance and Administration section

Under the Identity option chose Compartments we should see a screen that looks like this :

![image](https://user-images.githubusercontent.com/42166489/107868669-335b8c00-6eac-11eb-9f52-618fbe25a6bd.png)
Click the Create Compartment button

Provide a name, description
Chose root as the parent compartment

Click the Create Compartment button.

![image](https://user-images.githubusercontent.com/42166489/107868677-40787b00-6eac-11eb-8301-fe115d146c8d.png)

### Step 2: Setup the database

Use the "Hamburger" menu, and select the Oracle Database section, then Autonomous Transaction Processing.

![image](https://user-images.githubusercontent.com/42166489/107868692-55550e80-6eac-11eb-8377-a2967947044b.png)

This will switch to the database management page. Note that depending on your tenancy and if others are also using the list of existing database may be empty or differ from what's shown here.

- Make sure the workshops compartment is selected
- Click Create Autonomous Database button
- Fill in the form, and make sure to give the DB a unique name and display name for you (use your name or initials) in case multiple users are running the lab on this tenancy.
- Make the workload type Transaction Processing
- Set the deployment type Shared Infrastructure

![image](https://user-images.githubusercontent.com/42166489/107868698-6140d080-6eac-11eb-8131-2edd9d88a626.png)

- Chose the most recent option for the database version, allocate 1 OCPU and 1 GB of storage (this lab only requires a very small database)

![image](https://user-images.githubusercontent.com/42166489/107868706-6e5dbf80-6eac-11eb-937d-8f961622f582.png)

- Enter an admin password, be sure to remember it. The current rules are must have at least one number, one upper case, and one lower case letter, if the password does not meet the rules you will get a error displayed when you switch to the next field.

- Re-enter the password you chose in the confirm password box

![image](https://user-images.githubusercontent.com/42166489/107868755-d14f5680-6eac-11eb-8574-b0e74b5b9f16.png)

![image](https://user-images.githubusercontent.com/42166489/107868758-d4e2dd80-6eac-11eb-870e-b1b10987b002.png)

As a reminder - Be sure to remember the admin password, save it in a notes document for later reference.

Click the Create Autonomous Database button at the bottom of the page.

The database will start to be provisioned. This may take a short while, once it is running the orange box will turn green and the buttons will be enabled.

![image](https://user-images.githubusercontent.com/42166489/107868765-dd3b1880-6eac-11eb-9324-a05348c5393f.png)

Now our database is created and is available.

![image](https://user-images.githubusercontent.com/42166489/107868770-e5935380-6eac-11eb-93d4-fa307c35e757.png)

Once the instance is running click on the Copy button by the OCID, this will copy the OCID to your computers copy-and-paste buffer.

![image](https://user-images.githubusercontent.com/42166489/107868774-ef1cbb80-6eac-11eb-9da7-eb7b179798fb.png)

Open a document and paste the OCID, you will need this later in the setup.

### Setup your user in the database

We need to configure the database user so it matches the lab instructions
Now the instance is running click on the Tools tab, then Open SQL Developer Web button.

![image](https://user-images.githubusercontent.com/42166489/107868822-766a2f00-6ead-11eb-965d-1e3824431c04.png)

This will open the SQL Developer web login page
Login as ADMIN (it's case sensitive), using the password you chose earlier in the database setup.

![image](https://user-images.githubusercontent.com/42166489/107868825-7ff39700-6ead-11eb-80a9-e1605e2c4ae3.png)

Copy and paste the below SQL instructions into the worksheet (highlighted in red in the image below)

```
CREATE USER HelidonLabs IDENTIFIED BY H3lid0n_Labs;
GRANT CONNECT TO HelidonLabs;
GRANT CREATE SESSION TO HelidonLabs;
GRANT DWROLE TO HelidonLabs ;
GRANT UNLIMITED TABLESPACE TO HelidonLabs;
```

![image](https://user-images.githubusercontent.com/42166489/107868851-baf5ca80-6ead-11eb-83ee-e708deddb885.png)

Then Run the script (The Page of paper with the green "run" button)
If it works OK you will see a set of messages in the Script Output section of the screen (highlighted in red below) saying the User has been created and grants made.

![image](https://user-images.githubusercontent.com/42166489/107868854-c2b56f00-6ead-11eb-86c8-3b0256d7eeac.png)

Now we Create your Kubernetes cluster lab

### Step 4: Creating your Kubernetes cluster
Navigate to the Managed Kubernetes dashboard
Log into the OCI Console
Once on the OCI infrastructure page, click on the "Hamburger" menu
Scroll down to Solutions and Platform section, Click Developer services, then Container Clusters (OKE)

![image](https://user-images.githubusercontent.com/42166489/107868859-db258980-6ead-11eb-81ca-1bf957f06405.png)

In the List Scope section, use the dropdown to select the CTDOKE compartment

You may have to expand the tree nodes to locate this compartment.
Click the Create Cluster button at the top of the clusters list
Choose the option for the Quick Create, then click the Launch workflow button

![image](https://user-images.githubusercontent.com/42166489/107868865-e37dc480-6ead-11eb-9500-fe2ca116e3a7.png)

Fill in the form with following parameters:

Set the cluster Name to be something like Helidon-Lab-YOUR-INITIALS

Make sure the Compartment is CTDOKE

Make sure the Kubernetes version is the highest on the list (at the time of the last update of this document in November 2020 that was 1.18.10, but it may have been updated since then)

Check the Visibility type is Private

Set the Shape dropdown to VM.Standard2.1

Set the Number of nodes to be 2

There is no need to do anything in the Advanced Options section.

![image](https://user-images.githubusercontent.com/42166489/107868868-f42e3a80-6ead-11eb-902c-eb47c86aa983.png)

Click the Next button to go to the review page.
On the review page check the details you have provided are correct

Click the Create Cluster button.
We will be presented with a progress option, if you want read what's happening

Scroll to the bottom and click the Close button
The state will be "Creating" for a few minutes (usually 3-4 mins)

![image](https://user-images.githubusercontent.com/42166489/107868872-fd1f0c00-6ead-11eb-9cb0-3986be9536bb.png)

We can move on to the Cloud Shell Setup for the Kubernetes Labs module while the cluster continues to be provisioned.

### Step 5: Kubernetes labs - Cloud Shell setup (Accessing the cloud shell)
The OCI Cloud Shell is accessible through the Oracle Cloud GUI, and has a number of elements set up out of the box, like the Oracle Cloud Command Line Interface, and it also has quite some useful command-line tools pre-installed, like git, docker, kubectl, helm and more.

To access the OCI Cloud Shell, you can use the native browser on your laptop (you don't need to use the Linux desktop VM anymore).

Login to your Oracle Cloud Console
Click the icon on the top right of your screen: >_

![image](https://user-images.githubusercontent.com/42166489/107868876-1627bd00-6eae-11eb-86fd-34f308c04335.png)

Downloading the scripts and templates
Open the OCI Cloud Shell

![image](https://user-images.githubusercontent.com/42166489/107868878-2344ac00-6eae-11eb-92b6-b11e6e18a5c4.png)

Make sure you are in the top level directory

```
cd $HOME
```
Clone the repository with all scripts from github into your OCI Cloud Shell environment
git clone https://github.com/CloudTestDrive/helidon-kubernetes.git
To maximise the size of the OCI Cloud Shell window, click the "Arrows" button on the right of the console as indicated above:

![image](https://user-images.githubusercontent.com/42166489/107868894-42433e00-6eae-11eb-98ff-7bd7444cd864.png)

Downloading the database wallet file:

We will use the OCI Cloud Shell to download the database wallet file.

Make sure you are in the top level directory
cd $HOME

We need to replace the example ODIC below with the OCID of your database. If we didn't copy this when you created the database or don't have it then if you go to the database page for our instance (Hamburger -> Oracle Database -> Autonomous Transaction Processing -> your instance) 
it will be shown there with a copy link)

```
oci db autonomous-database generate-wallet --file Wallet.zip --password 'Pa$$w0rd' --autonomous-database-id 
```
ocid1.autonomousdatabase.oc1.ap-mumbai-1.abrg6ljrhsqzodklecyzxxmkbnbrltzhf5l2j35a3lhudivg45josgajz2uq

![image](https://user-images.githubusercontent.com/42166489/107868906-6010a300-6eae-11eb-91bb-8b461d700f9b.png)

Create the wallet directory and navigate to it:
```
mkdir -p $HOME/helidon-kubernetes/configurations/stockmanagerconf/Wallet_ATP
cd $HOME/helidon-kubernetes/configurations/stockmanagerconf/Wallet_ATP
```

Copy the wallet file to the directory

```
cp $HOME/Wallet.zip .
```

Unzip the wallet file
unzip Wallet.zip
Look at the contents of the tnsnames.ora file to get the database connection names
cat tnsnames.ora

![image](https://user-images.githubusercontent.com/42166489/107868917-7f0f3500-6eae-11eb-8e8d-d06f0f880af0.png)

We will see a list of the various connection types to your database.

Locate the "high" connection type to your database and take a note of the full name, in the example above that's “tclab_high” 
Be sure to write down the database connection name we have just found, we will need it later

Return to the home directory

```
cp $HOME
```
Save the changes to the file.

### Step 6: Configure the Helm repository:

Helm is the tool we will be using to install standard software into Kubernetes. While it's possible to load software into Kubernetes by hand Helm makes it much easier as it has pre-defined configurations (called charts) that it pulls from an internet based repository.

The OCI Cloud Shell has helm already installed for you, however it does not know what repositories to use for the helm charts. We need to tell helm what repositories to use.

Run the following command to add the dashboard repo to helm.

![image](https://user-images.githubusercontent.com/42166489/107868936-b54cb480-6eae-11eb-9a9b-3fba58cf1c2e.png)

To get the current list of repositories run the following command:

```
helm repo list
```
Lastly let's update the helm cache, run the following command :

```
Saikat_Dey@cloudshell:~ (ap-mumbai-1)$ helm repo list
NAME                    URL                                    
kubernetes-dashboard    https://kubernetes.github.io/dashboard/
Saikat_Dey@cloudshell:~ (ap-mumbai-1)$ helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "kubernetes-dashboard" chart repository
Update Complete. ⎈Happy Helming!⎈
Saikat_Dey@cloudshell:~ (ap-mumbai-1)$ 
```

![image](https://user-images.githubusercontent.com/42166489/107868822-766a2f00-6ead-11eb-965d-1e3824431c04.png)

This will open the SQL Developer web login page
Login as ADMIN (it's case sensitive), using the password you chose earlier in the database setup.

![image](https://user-images.githubusercontent.com/42166489/107868825-7ff39700-6ead-11eb-80a9-e1605e2c4ae3.png)

Copy and paste the below SQL instructions into the worksheet (highlighted in red in the image below)

```
CREATE USER HelidonLabs IDENTIFIED BY H3lid0n_Labs;
GRANT CONNECT TO HelidonLabs;
GRANT CREATE SESSION TO HelidonLabs;
GRANT DWROLE TO HelidonLabs ;
GRANT UNLIMITED TABLESPACE TO HelidonLabs;
```

![image](https://user-images.githubusercontent.com/42166489/107868851-baf5ca80-6ead-11eb-83ee-e708deddb885.png)

Then Run the script (The Page of paper with the green "run" button)
If it works OK you will see a set of messages in the Script Output section of the screen (highlighted in red below) saying the User has been created and grants made.

![image](https://user-images.githubusercontent.com/42166489/107868854-c2b56f00-6ead-11eb-86c8-3b0256d7eeac.png)

Now we Create your Kubernetes cluster lab

### Step 7: Creating your Kubernetes cluster
Navigate to the Managed Kubernetes dashboard
Log into the OCI Console
Once on the OCI infrastructure page, click on the "Hamburger" menu
Scroll down to Solutions and Platform section, Click Developer services, then Container Clusters (OKE)

![image](https://user-images.githubusercontent.com/42166489/107868859-db258980-6ead-11eb-81ca-1bf957f06405.png)

In the List Scope section, use the dropdown to select the CTDOKE compartment

You may have to expand the tree nodes to locate this compartment.
Click the Create Cluster button at the top of the clusters list
Choose the option for the Quick Create, then click the Launch workflow button

![image](https://user-images.githubusercontent.com/42166489/107868865-e37dc480-6ead-11eb-9500-fe2ca116e3a7.png)

Fill in the form with following parameters:

Set the cluster Name to be something like Helidon-Lab-YOUR-INITIALS

Make sure the Compartment is CTDOKE

Make sure the Kubernetes version is the highest on the list (at the time of the last update of this document in November 2020 that was 1.18.10, but it may have been updated since then)

Check the Visibility type is Private

Set the Shape dropdown to VM.Standard2.1

Set the Number of nodes to be 2

There is no need to do anything in the Advanced Options section.

![image](https://user-images.githubusercontent.com/42166489/107868868-f42e3a80-6ead-11eb-902c-eb47c86aa983.png)

Click the Next button to go to the review page.
On the review page check the details you have provided are correct

Click the Create Cluster button.
We will be presented with a progress option, if you want read what's happening

Scroll to the bottom and click the Close button
The state will be "Creating" for a few minutes (usually 3-4 mins)

![image](https://user-images.githubusercontent.com/42166489/107868872-fd1f0c00-6ead-11eb-9cb0-3986be9536bb.png)

We can move on to the Cloud Shell Setup for the Kubernetes Labs module while the cluster continues to be provisioned.

### Step 8: Kubernetes labs - Cloud Shell setup (Accessing the cloud shell)
The OCI Cloud Shell is accessible through the Oracle Cloud GUI, and has a number of elements set up out of the box, like the Oracle Cloud Command Line Interface, and it also has quite some useful command-line tools pre-installed, like git, docker, kubectl, helm and more.

To access the OCI Cloud Shell, you can use the native browser on your laptop (you don't need to use the Linux desktop VM anymore).

Login to your Oracle Cloud Console
Click the icon on the top right of your screen: >_

![image](https://user-images.githubusercontent.com/42166489/107868876-1627bd00-6eae-11eb-86fd-34f308c04335.png)

Downloading the scripts and templates
Open the OCI Cloud Shell

![image](https://user-images.githubusercontent.com/42166489/107868878-2344ac00-6eae-11eb-92b6-b11e6e18a5c4.png)

Make sure you are in the top level directory

```
cd $HOME
```
Clone the repository with all scripts from github into your OCI Cloud Shell environment
git clone https://github.com/CloudTestDrive/helidon-kubernetes.git
To maximise the size of the OCI Cloud Shell window, click the "Arrows" button on the right of the console as indicated above:

![image](https://user-images.githubusercontent.com/42166489/107868894-42433e00-6eae-11eb-98ff-7bd7444cd864.png)

### Step 9:Downloading the database wallet file:

We will use the OCI Cloud Shell to download the database wallet file.

Make sure you are in the top level directory
cd $HOME

We need to replace the example ODIC below with the OCID of your database. If we didn't copy this when you created the database or don't have it then if you go to the database page for our instance (Hamburger -> Oracle Database -> Autonomous Transaction Processing -> your instance) 
it will be shown there with a copy link)

```
oci db autonomous-database generate-wallet --file Wallet.zip --password 'Pa$$w0rd' --autonomous-database-id 
```
ocid1.autonomousdatabase.oc1.ap-mumbai-1.abrg6ljrhsqzodklecyzxxmkbnbrltzhf5l2j35a3lhudivg45josgajz2uq

![image](https://user-images.githubusercontent.com/42166489/107868906-6010a300-6eae-11eb-91bb-8b461d700f9b.png)

Create the wallet directory and navigate to it:
```
mkdir -p $HOME/helidon-kubernetes/configurations/stockmanagerconf/Wallet_ATP
cd $HOME/helidon-kubernetes/configurations/stockmanagerconf/Wallet_ATP
```

Copy the wallet file to the directory

```
cp $HOME/Wallet.zip .
```

Unzip the wallet file
unzip Wallet.zip
Look at the contents of the tnsnames.ora file to get the database connection names
cat tnsnames.ora

![image](https://user-images.githubusercontent.com/42166489/107868917-7f0f3500-6eae-11eb-8e8d-d06f0f880af0.png)

We will see a list of the various connection types to your database.

Locate the "high" connection type to your database and take a note of the full name, in the example above that's “tclab_high” 
Be sure to write down the database connection name we have just found, we will need it later

Return to the home directory

```
cp $HOME
```
Save the changes to the file.

### Step 10: Configure the Helm repository:

Helm is the tool we will be using to install standard software into Kubernetes. While it's possible to load software into Kubernetes by hand Helm makes it much easier as it has pre-defined configurations (called charts) that it pulls from an internet based repository.

The OCI Cloud Shell has helm already installed for you, however it does not know what repositories to use for the helm charts. We need to tell helm what repositories to use.

Run the following command to add the dashboard repo to helm.

![image](https://user-images.githubusercontent.com/42166489/107868936-b54cb480-6eae-11eb-9a9b-3fba58cf1c2e.png)

To get the current list of repositories run the following command:

```
helm repo list
```
Lastly let's update the helm cache, run the following command :

```
Saikat_Dey@cloudshell:~ (ap-mumbai-1)$ helm repo list
NAME                    URL                                    
kubernetes-dashboard    https://kubernetes.github.io/dashboard/
Saikat_Dey@cloudshell:~ (ap-mumbai-1)$ helm repo update
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "kubernetes-dashboard" chart repository
Update Complete. ⎈Happy Helming!⎈
Saikat_Dey@cloudshell:~ (ap-mumbai-1)$ 
```

### Step 11:Getting your cluster access details:

Open the OCI Console UI
Open the "Hamburger" menu on the upper left scroll down to the Solutions and Platform section
Click on the Developer Services menu option, then Kubernetes Clusters

Locate your cluster in the list, this will be the one you've been assigned or the one you just created. Click on the name to get to the cluster details.

You will be presented with a page with details for downloading the kubeconfig file. Make sure the OCI Cloud Shell Access is the selected option.

Look for the section with the download command, it will look like this 

![image](https://user-images.githubusercontent.com/42166489/107868990-65222200-6eaf-11eb-95de-525714102fad.png)

Open your OCI Cloud Shell window and paste the line to execute it.

```
Saikat_Dey@cloudshell:~ (ap-mumbai-1)$ oci ce cluster create-kubeconfig --cluster-id ocid1.cluster.oc1.ap-mumbai-1.aaaaaaaaaftgkodbmrswkmbumy4gkmtfhfrgknlbgvqtkntbmc2wkzdfgyzt --file $HOME/.kube/config --region ap-mumbai-1 --token-version 2.0.0
New config written to the Kubeconfig file /home/Saikat_Dey/.kube/config
Saikat_Dey@cloudshell:~ (ap-mumbai-1)$ 
```

Set the config file to be accessible only by you (This stops warnings from helm about it having the wrong permissions)
```
chmod 600 $HOME/.kube/config
```

Verify you can access the cluster:
```
kubectl get nodes
```

Helm 3 is installed within the OCI Cloud Shell, but if later on you want to use your own laptop to manage a Kubernetes cluster, here are the instructions for a local install of helm:
https://helm.sh/docs/intro/install/

Installing the Kubernetes dashboard

To install the dashboard run the following command :

```
helm install kubernetes-dashboard kubernetes-dashboard/kubernetes-dashboard --namespace kube-system --set service.type=LoadBalancer --version 2.8.3
```

![image](https://user-images.githubusercontent.com/42166489/107869027-add9db00-6eaf-11eb-96dd-adbffd43fd22.png)

Note that Helm does all the work needed here, it creates the service, deployment, replica set and pods for us and starts things running. Unless you need a very highly customised configuration using helm is way simpler than setting each of these individual elements up yourself.

Check the status of the Helm deployment
```
helm list --namespace kube-system
```

```
Saikat_Dey@cloudshell:~ (ap-mumbai-1)$ helm list --namespace kube-system
NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
kubernetes-dashboard    kube-system     1               2021-02-11 07:38:05.630998353 +0000 UTC deployed        kubernetes-dashboard-2.8.3      2.0.4  
```
Check the status of the objects created:
kubectl get all --namespace kube-system

![image](https://user-images.githubusercontent.com/42166489/107869038-d235b780-6eaf-11eb-84d4-5efc9e39225b.png)

Please note in my case I have 3 pods running.

We see all the elements of the dashboard: a pod, a replica set, a deployment and a service.

If you want more detailed information then you can extract it, for example to get the details on the pods do the following

Execute below command, replacing the ID with the ID of your pod
```
kubectl get pod kubernetes-dashboard-bfdf5fc85-djnvb -n kube-system -o yaml
```

Relace the pod name and use it.

![image](https://user-images.githubusercontent.com/42166489/107869041-e4175a80-6eaf-11eb-9dce-0ff3b7388999.png)

If you want the output in json then replace the -o yaml with -o json.

Get a specific element from a configuration, replacing the pod ID of course :

```
kubectl get pod kubernetes-dashboard-bfdf5fc85-djnvb -n kube-system -o=jsonpath='{.spec.containers[0].image}'
```
Saikat_Dey@cloudshell:~ (ap-mumbai-1)$ kubectl get pod kubernetes-dashboard-9b5b7f46d-gt6m5 -n kube-system -o=jsonpath='{.spec.containers[0].image}'
kubernetesui/dashboard:v2.0.4Saikat_Dey@cloudshell:~ (ap-mumbai-1)$ 

Run this to get the selectors used by the dashboard service:

```
kubectl get service kubernetes-dashboard -n kube-system -o=jsonpath='{.spec.selector}'
```

Get the list of pods providing the dashboard service by name (the service just goes by it's name, no random identifiers added to it)

```
kubectl get pod -n kube-system --selector=app.kubernetes.io/name=kubernetes-dashboard
```
Accessing the Kubernetes dashboard:
First we're going to need create a user to access the dashboard. This involves creating the user, then giving it the kubernetes-dashbaord role that helm created for us when it installed the dashbaord chart.

Go to the helidon-kubernetes project folder, then the base-kubernetes directory

```
cd $HOME/helidon-kubernetes/base-kubernetes
```
Create the user and role

```
kubectl apply -f dashboard-user.yaml
```

![image](https://user-images.githubusercontent.com/42166489/107869055-0dd08180-6eb0-11eb-8998-5f7eecbf4c77.png)

Get the token of the newly created user:
```
kubectl -n kube-system describe secret `kubectl -n kube-system get secret | grep dashboard-user | awk '{print $1}'`
```

![image](https://user-images.githubusercontent.com/42166489/107910707-f2817700-6f80-11eb-9718-180a6b211105.png)

Please save the token in a plain editor in you system. We may need it later.

As the OCI Cloud Shell runs in a web browser and is not itself a web browser we need to setup access so that the kubernetes-dashboard is available to your web browser on your laptop. This would normally be a problem as it would be running on a network that it internal to the cluster.

Fortunately for us helm is a very powerful mechanism for configuring services, and when we used the helm command to install the dashboard we told it that the service.type was LoadBalancer, this will automatically setup a load balancer for us, making the dashbaord service visible on the public internet, we just need the IP address to use.

To get the IP address of the dashboard load balancer run the following command
kubectl get service kubernetes-dashboard -n kube-system

```
Saikat_Dey@cloudshell:base-kubernetes (ap-mumbai-1)$ kubectl get service kubernetes-dashboard -n kube-system
NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)         AGE
kubernetes-dashboard   LoadBalancer   10.96.22.254   152.67.31.203   443:30576/TCP   138m
```

The IP address of the load balancer is in the EXTERNAL-IP column. Note that this can take a few minutes to be assigned, so it it's listed as just re-run the kubectl get command after a short while

### Step 12:Looking around the dashboard.

Open a web browser and using the IP address you got above and go to
```
https://<load balancer ip address>/#!/login
```

Eg: https://152.67.31.203/#/login
Note: I have tried this is Chrome and Safari but the browser did nit allow me. Tried on Firefox, it worked there.

In Firefox once the security risk page is displayed click on the "Advanced" button, then on the "Accept Risk and Continue" button.
![image](https://user-images.githubusercontent.com/42166489/107910921-6e7bbf00-6f81-11eb-8c73-0c8056cfa6a7.png)

You'll now be presented with the login screen for the dashboard.

Click the radio button for the Token
Enter the token for the admin-user you retrieved earlier
Accept to save the password if given the option, it'll make things easier on the next login
Press Sign In.

![image](https://user-images.githubusercontent.com/42166489/107910937-79365400-6f81-11eb-8ad5-ba0b0adae255.png)

Once signed in, a dashboard will be shown.

![image](https://user-images.githubusercontent.com/42166489/107910986-92d79b80-6f81-11eb-96d8-a499d0dc86cf.png)

In the Namespace section on the click the dropdown to select the kube-system namespace

![image](https://user-images.githubusercontent.com/42166489/107911005-9bc86d00-6f81-11eb-947d-ec9302dc9b36.png)

Select kube-system, precisely which page you'll go to will depend on what was selected in the left menu when you switched namespaces, but in my case it took me to an overview page.

Let's switch to see the details of the workspace, Click Workloads on the left menu

![image](https://user-images.githubusercontent.com/42166489/107911021-a420a800-6f81-11eb-8261-3eac907c835e.png)

You can use the Kubernetes dashboard to navigate the relationships between the resources. Let's start by looking at the services in the kube-system namespace

In the Service section on the left menu click Services

If you scroll down the page to services you'll see the kubentes-dashboard service listed,

![image](https://user-images.githubusercontent.com/42166489/107911046-af73d380-6f81-11eb-9779-80ee699cd4e5.png)

Click on the service name kubernetes-dashboard to get the details of the service, including the pods it's running on.

![image](https://user-images.githubusercontent.com/42166489/107911060-b6024b00-6f81-11eb-90e0-537109ce0a73.png)

If you click the Deployments in the Workloads section of the left menu you'll see the deployments list (the dashboard, coredns and auto-scaler services)

![image](https://user-images.githubusercontent.com/42166489/107911082-be5a8600-6f81-11eb-8b96-6a88112f94ca.png)

Click on the kubernetes-dashboard deployment to look into the detail of the deployment and you'll see the deployment details, including the list of replica sets that are in use. We'll look into the old / new distinction when we look at rolling upgrades)

![image](https://user-images.githubusercontent.com/42166489/107911097-c61a2a80-6f81-11eb-9f78-6540ad2b86db.png)

Scroll down until you can see the replica set section

Click on the replica set name (kubernetes-dashboard-699cc9f655 in this case) then scroll down a bit to see the pods in the replica set.

![image](https://user-images.githubusercontent.com/42166489/107911106-cdd9cf00-6f81-11eb-8602-16f1a819bd6e.png)

In this case there's only one pod (kubernetes-dashboard-699cc9f655-jz4ph in this case, yours will vary) so click on that to see the details of the pod.

![image](https://user-images.githubusercontent.com/42166489/107912857-4e4dff00-6f85-11eb-8dca-f455141c7e55.png)

Using kubernetes-dashboard to look at a pod provides several useful features, we can look at any log data it's generated (output the pod has written to stderr or stdout)

![image](https://user-images.githubusercontent.com/42166489/107912873-573ed080-6f85-11eb-875a-b42c65b31d28.png)

![image](https://user-images.githubusercontent.com/42166489/107912879-5a39c100-6f85-11eb-84aa-f4af9e73d60d.png)

This displays the log data which can be very useful when debugging. Of course it's also possible to use kubectl to download logs info if you wanted to rather than just displaying it in the browser.

There is also the ability to use the dashboard to connect to a running container in a pod. This could be useful for debugging, and later on we'll use this to trigger a simulated pod failure when we explore service availability.

Starting an Ingress controller for accepting external data:

There is one other core service we need to install before we can start running our microservices, the Ingress controller. An Ingress controller provides the actual ingress capability, but it also needs to be configured.

An Ingress in Kubernetes is one mechanism for external / public internet clients to access http / https connections (and thus REST API's) It is basically a web proxy which can process specific URL's forwarding data received to a particular microservice / URL on that microservice.

For this lab we're going to use an nginx based Ingress controller. The nginx based Ingress controller we use here is maintained by the Kubernetes team, but there are several others that could be used in your environments if you want. There are a list of commercial and open source Ingress controllers in the Kubernetes ingress documentation

Firstly we need to create a namespace for the ingress controller.

Run the following command :
```
kubectl create namespace ingress-nginx
```

Output:     namespace/ingress-nginx created

To enable the lab to complete in a reasonable time we will therefore be generating our own self-signed certificate. For a lab environment that's fine, but in a production environment you wouldn't do this.

Run the following command to generate a certificate.

```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=nginxsvc/O=nginxsvc"
```

Run the following command to save the certificate as a secret in the ingress-nginx namespace
```
kubectl create secret tls tls-secret --key tls.key --cert tls.crt -n ingress-nginx
```

![image](https://user-images.githubusercontent.com/42166489/107912911-6faeeb00-6f85-11eb-9378-f7b7e1ddfbc4.png)

Add the Kubernetes nginx based ingress repo to helm
```
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
```

Update the repositories with the new repo
```
helm repo update
```

Run the following command to install ingress-nginx using Helm 3:
helm install ingress-nginx ingress-nginx/ingress-nginx -n ingress-nginx --version 3.10.1 --set rbac.create=true --set controller.service.annotations."service\.beta\.kubernetes\.io/oci-load-balancer-tls-secret"=tls-secret --set controller.service.annotations."service\.beta\.kubernetes\.io/oci-load-balancer-ssl-ports"=443

![image](https://user-images.githubusercontent.com/42166489/107913552-a3d6db80-6f86-11eb-9fbd-c92144dea5de.png)

This will install the ingress controller in the default namespace.

Because the Ingress controller is a service, to make it externally available it still needs a load balancer with an external port. Load balancers are not provided by Kubernetes, instead Kubernetes requests that the external framework delivered by the environment provider create a load balancer. Creating such a load balancer may take some time for the external framework to provide.

To see the progress in creating the Ingress service type :
```
kubectl --namespace ingress-nginx get services -o wide ingress-nginx-controller
```

Saikat_Dey@cloudshell:~ (ap-mumbai-1)$ kubectl --namespace ingress-nginx get services -o wide ingress-nginx-controller
NAME                       TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE     SELECTOR
ingress-nginx-controller   LoadBalancer   10.96.176.225   <pending>     80:30682/TCP,443:32125/TCP   5m56s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=ingress-nginx,app.kubernetes.io/name=ingress-nginx

Locate the Resources section on the lower left side.

Click on the Listeners option.

![image](https://user-images.githubusercontent.com/42166489/107913566-ad604380-6f86-11eb-802b-baad61dff755.png)

Click on the Listeners option and select Edit.

![image](https://user-images.githubusercontent.com/42166489/107913585-b5b87e80-6f86-11eb-9e48-2508ae66eed5.png)

We need to change the port.
In the popup locate the BackendSet option, click on it and select the TCP-80 option

![image](https://user-images.githubusercontent.com/42166489/107913605-c0731380-6f86-11eb-8e10-55d16d7112a5.png)

Click the Update Listener.
Change the backend port to 80.

![image](https://user-images.githubusercontent.com/42166489/107913642-ce289900-6f86-11eb-8cf1-c8ecaaaa2c95.png)

### Namespace, Services and Ingress rules:

Run these commands-

```
cd $HOME/helidon-kubernetes/base-kubernetes
bash create-namespace.sh <your-initials>-helidon
kubectl get namespace
```

If we look into the namespace we've just created we'll see it contains nothing yet:

```
kubectl get all
```

The servicesClusterIP.yaml file in the defines the cluster services for us. We can apply it to make the changes

```
kubectl apply -f servicesClusterIP.yaml
```

To see the services we can use kubectl :
```
kubectl get services
```

Let's look at the Ingress services
```
kubectl get services -n ingress-nginx
```

Let's use kubectl to confirm we have no rules yet

```
kubectl get ingress
```

Let's create the Ingress rules by applying the Ingress Config file :

```
kubectl apply -f ingressConfig.yaml
kubectl get ingress
```

![image](https://user-images.githubusercontent.com/42166489/107913664-d84a9780-6f86-11eb-83c7-7666dd5674c7.png)

If you didn't write it down earlier find the external IP address the ingress controller is running on :
kubectl get service -n ingress-nginx

We now have a working endpoint, let's try accessing it using curl - expect an error!

```
curl -i -k -X GET https://<ip address>/sf
```

If we tried to go to a URL that's not defined we will as expected get a 404 error:

```
curl -i -k -X GET https://<ip address>/unknowningress
```

![image](https://user-images.githubusercontent.com/42166489/107913804-134ccb00-6f87-11eb-8672-85be64a6d722.png)

Switch to the config files directory

```
cd $HOME/helidon-kubernetes/configurations/stockmanagerconf
```

Edit the file databaseConnectionSecret.yaml

```
vi databaseConnectionSecret.yaml

url: jdbc:oracle:thin:@<database connection name>?TNS_ADMIN=./Wallet_ATP
```

![image](https://user-images.githubusercontent.com/42166489/107913824-1fd12380-6f87-11eb-9ffd-876692cb7c0d.png)

Switch back to the scripts directory
```
cd $HOME/helidon-kubernetes/base-kubernetes
```

Run the following command to create the secrets:

```
bash create-secrets.sh
kubectl get secrets
```

![image](https://user-images.githubusercontent.com/42166489/107913837-28295e80-6f87-11eb-8494-33af0b8b7b5d.png)

To see the content of a specific secret :

```
kubectl get secret sm-conf-secure -o yaml
```

To get the secret in plain test

```
echo <your secret payload> | base64 -d -i -
```

In the dashboard UI

Chose your namespace in the namespace selector (upper left) tg-helidon in my case, but yours may differ
Click on the Secrets choice in the Config and Store section of the left hand menu.
Select the sf-conf-secure entry to see the list of files in the secret
Click on the eye icon next to the storefront-security.yaml to see the contents of the secret.

![image](https://user-images.githubusercontent.com/42166489/107913846-2fe90300-6f87-11eb-82fa-2582dcbf36ef.png)

Run the script to create the config maps

```
bash create-configmaps.sh
```

To get the list of config maps we need to ask kubectl or look at the config maps in the dashboard:

```
kubectl get configmaps
```

We can get more details by getting the data in JSON or YAML, in this case I'm extracting it using YAML as that's the original data format:

```
kubectl get configmap sf-config-map -o=yaml
```

### Deploying the actual microservices

The deploy.sh script just does a sequence of commands to apply the deployment configuration files, for example kubectl apply -f zipkin-deployment.yaml --record=true You could of course issues these commands by hand if you liked, but we're using a script here to save typo probems, and also because it's good practice to script this type of thing, so you know exactly the command that was run - which can be useful if you need to exactly reproduce it (which of course if you were deploying in a production environment you would!)

Switch to the helidon-kubernetes directory:

```
cd $HOME/helidon-kubernetes
```

Now run the deploy.sh script

```
bash deploy.sh
```

![image](https://user-images.githubusercontent.com/42166489/107915873-55780b80-6f8b-11eb-9ed7-7b3e36a0a6d9.png)

Now lets look at the logs of the pods you have launched (replace the ID shown here with the exact ID of your pod)

```
kubectl logs --follow storefront-68bbb5dbd8-vp578
```

![image](https://user-images.githubusercontent.com/42166489/107915888-5f017380-6f8b-11eb-8d9b-8e628574d0b0.png)

If you can't remember it get the external IP address of the load balancer
```
kubectl get services -n ingress-nginx
```

Let's try to get some data - you might get an error (replace with the ingress controllers load ballancer you got earlier)

```
curl -i -k -X GET -u jack:password https://<external IP>/store/stocklevel
```

![image](https://user-images.githubusercontent.com/42166489/107915931-67f24500-6f8b-11eb-92a7-8a29e7baf64b.png)

If you get 424 failed dependency or timeouts it's because the services are doing their lazy initialization, wait a minute or so and retry the request.

Run : 

```
kubectl get endpoints
```

(ap-mumbai-1)$ kubectl get endpoints
NAME           ENDPOINTS                                AGE
stockmanager    10.244.0.140:8081,10.244.0.140:9081          15h
storefront        10.244.0.14:8080,10.244.0.14:9080             15h
zipkin            10.244.1.15:9411                            15h

And also on the stockmanager pod, you also need to replace the pod id !

```
kubectl logs stockmanager-d6cc5c9b7-bbjdp --tail=20
```

![image](https://user-images.githubusercontent.com/42166489/107915949-717bad00-6f8b-11eb-9d86-b85ff47a2750.png)

Open your browser

Go to the ingress end point for your cluster, for example http://<external IP>/zipkin (replace with your ingress controllers Load balancer IP address)

![image](https://user-images.githubusercontent.com/42166489/107915965-78a2bb00-6f8b-11eb-80d7-caebc87c3c3c.png)

Click the Run Query button to get the traces list

Updating your external configuration:

We've mounted the sf-config-map (which contains the contents of storefront-config.yaml file) onto /conf. Let's use a command to connect to the running pod (remember your storefront pod will have a different id so use kubectl get pods to retrieve that) and see how it looks in there, then exit the connection

Execute these commands :

```
kubectl exec -it storefront-588b4d69db-w244b -- /bin/bash
```

You are now inside the container. Let's look atround

Inside the container type

```
ls /conf
Inside the container type
cat /conf/storefront-config.yaml
```

Exit the pod :

```
exit
```

![image](https://user-images.githubusercontent.com/42166489/107915977-7fc9c900-6f8b-11eb-97a4-c71cf2bf1451.png)

Open your browser

Go to the ingress end point for your cluster, for example http://<external IP>/zipkin (replace with your ingress controllers Load balancer IP address)

![image](https://user-images.githubusercontent.com/42166489/107916007-87896d80-6f8b-11eb-83b3-0297541baec4.png)

Select the most recent trace (click on it) and retrieve the data from that.

![image](https://user-images.githubusercontent.com/42166489/107916026-8e17e500-6f8b-11eb-85f7-0ef96938aa1e.png)

Consult minimum change (replace with your address)

```
curl -i -k -X GET https://<external IP>/sf/minimumChange
```

![image](https://user-images.githubusercontent.com/42166489/107916152-d33c1700-6f8b-11eb-9250-2fe3f12b93fb.png)

### Updating your external configuration:

Get the status resource data

```
curl -i -k -X GET https://<external IP>/sf/status
```

![image](https://user-images.githubusercontent.com/42166489/107916911-177be700-6f8d-11eb-81f6-4ce4bfc2b123.png)

Incase you dont remember your storefront pod will have a different id so use kubectl get pods to retrieve that.

Execute these commands:

```
kubectl exec -it storefront-588b4d69db-w244b -- /bin/bash
```

You are now inside the container. Let's look atround. Inside the container type

```
ls /conf
```

Inside the container type

```
cat /conf/storefront-config.yaml
```

Exit the pod

```
exit
```

![image](https://user-images.githubusercontent.com/42166489/107916926-1f3b8b80-6f8d-11eb-806f-b913fb17fec1.png)

As expected we see the contents of our config firectory and the storefront-config.yaml. Let's use the dashboard to modify that data

Open the dashboard
Select your namespace in the selector on the upper left
Click on Config Maps in the Config and Storage section of the left menu

![image](https://user-images.githubusercontent.com/42166489/107916948-2a8eb700-6f8d-11eb-8cbf-03f9e8abda1e.png)

Then click on our config map (sf-config-map) to see the details and contents.

![image](https://user-images.githubusercontent.com/42166489/107916964-31b5c500-6f8d-11eb-90bf-6c2b9887ed5b.png)

As we'd expect it has our contents (You may have a different storename than My Shop if you changed the storefront-config.yaml file before creating the config map)

Click the Edit icon (upper right) dashboard-edit-icon to get an on-screen editor where we can change the yaml that represents the map. 

![image](https://user-images.githubusercontent.com/42166489/107916991-3c705a00-6f8d-11eb-9ea0-027fc5c3bbb9.png)

Locate the storename attribute in the data.storefront-config.yaml section.

Now edit the text and change the text My Shop to something else, here I've changed it to Tims shop . Be sure to change only the My Shop text, not the quote characters or other things (you don't want to create corrupt YAML which will be rejected).

![image](https://user-images.githubusercontent.com/42166489/107917011-44c89500-6f8d-11eb-9c1a-ce0ad9b9517f.png)

Click on the Update button to save your changes.

Now let's return to the pod and see what's happened

Re-connect to the pod

```
kubectl exec -it storefront-588b4d69db-w244b -- /bin/bash
```

In the pod, run

```
cat /conf/storefront-config.yaml
```

![image](https://user-images.githubusercontent.com/42166489/107917030-4d20d000-6f8d-11eb-8f6c-0ab428b10fed.png)

If we now get the status resource data again it's also updated
Query the status:
```
curl -i -k -X GET https://<external IP>/sf/status
```

![image](https://user-images.githubusercontent.com/42166489/107917054-53af4780-6f8d-11eb-9790-d754a398e7eb.png)

### Cloud Native - Health, Readines and liveness:

First let's make sure that the service is running, (replace with the external ip address of the ingress)
    In the OCI Cloud Shell
    
```
curl -i -X GET -u jack:password http://<External IP>:80/store/stocklevel
```

Lets look at the pods to check all is running fine:

```
kubectl get pods
```

![image](https://user-images.githubusercontent.com/42166489/107917086-5f027300-6f8d-11eb-8953-729fc7c01438.png)

We're going to simulate a crash in our program, this will cause the container to exit, and Kubernetes will identify this and start a replacement container in the pod for us.

Using the name of the storefront pod above let's connect to the container in the pod using kubectl:
kubectl exec storefront-65bd698bb4-cq26l -ti -- /bin/bash

Inside the pod simulate a major fault that causes a service failure by killing the process running our service :

```
kill -1 1
```

![image](https://user-images.githubusercontent.com/42166489/107917109-688bdb00-6f8d-11eb-9e36-4646dfc8ed56.png)

Let's look at the pod details again:

```
kubectl get pods
```

Let's look at the logs (use your storefront pod id of course)

```
kubectl logs storefront-65bd698bb4-cq26l
```

![image](https://user-images.githubusercontent.com/42166489/107917294-b3a5ee00-6f8d-11eb-93b3-a9d92ca862c3.png)

We now have mechanisms in place to restart a container if it fails, but it may be that the container does not actually fail, just that the program running in it ceases to behave properly, for example there is some kind of non fatal resource starvation such as a deadlock. In this case the pod cannot recognize the problem as the container is still running.

Fortunately Kubernetes provides a mechanism to handle this as well. This mechanism is called Liveness probes, if a pod fails a liveness probe then it will be automatically restarted.

You may recall in the Helidon labs (if you did them) we created a liveness probe, this is an example of Helidon is designed to work in cloud native environments.


* Try getting the data


```
curl -i -k -X GET -u jack:password https://<External IP>/store/stocklevel
```

curl -i -k -X GET -u jack:password http://52.67.28.51/store/stocklevel

## Liveness:

1: Navigate to the $HOME/helidon-kubernetes folder

```
    cd $HOME/helidon-kubernetes
```

2: Stop the existing deployments

```
    bash undeploy.sh
```

![image](https://user-images.githubusercontent.com/42166489/107917390-e51eb980-6f8d-11eb-80e2-5f56921d9a76.png)

Edit the file storefront-deployment.yaml using your prefered editor
Search for the Liveness probes section. This is under the spec.template.spec.containers section

![image](https://user-images.githubusercontent.com/42166489/107917418-ee0f8b00-6f8d-11eb-8b95-e0a49bd1c32e.png)

As you can see this section has been commented out.

On each line remove the # (only the first one, and only the # character, be sure not to remove any whitespace).
Save the file
The resulting section should look like this:

![image](https://user-images.githubusercontent.com/42166489/107917443-f667c600-6f8d-11eb-89b1-f01fdada0be8.png)

This is a pretty simple test to see if there is a running service, in this case we use the service to make an http get request (this is made by the framework and is done from outside the pod) on the 9080:/health/live url (we know it's on port 9080 as the port definition names it health-port). There are other types of liveness probe than just get requests, you can run a command in the container itself, or just see if it's possible to open a tcp/ip connection to a port in the container. Of course this is a simple definition, it doesn't look at the many options that are available.

The first thing to say is that whatever steps your actual liveness test does it needs to be sufficient to detect service problems like deadlocks, but also to use as few resources as possible so the check itself doesn't become a major load factor.

Let's look at some of these values.

As it may take a while to start up the container, we specify and initialDelaySeconds of 120, Kubernetes won't start checking if the pod is live until that period is elapsed. If we made that to short then we may never start the container as Kubernetes would always determine it was not alive before the container had a chance to start up properly.

The parameter timeoutSeconds specifies that for the http request to be considered failed it would not have responded in 5 seconds. As many http service implementations are initialized on first access we need to chose a value that is long enough for the framework to do it's lazy initialization.

The parameter periodSeconds defines how often Kubernetes will check the container to see if it's alive and responding. This is a balance, especially if the liveness check involved significant resources (e.g. making a RDBMS call) You need to check often enough that a non responding container will be detected quickly, but not check so often that the checking process itself uses to many resources.

Finally failureThreshold specifies how many consecutive failures are needed before it's deemed to have failed, in this case we need 3 failures to respond

Whatever your actual implementation you need to carefully consider the values above. Get them wrong and your service may never be allowed to start, or problems may not be detected.

Let's apply the changes we made in the deployment :

Deploy the updated version

```
bash deploy.sh
```

![image](https://user-images.githubusercontent.com/42166489/107917481-01225b00-6f8e-11eb-9065-2ddc90f321e6.png)

Note that as we have undeployed and then deployed again these are new pods and so the RESTART count is back to zero.

If we look at the logs for the storefront before the liveness probe has started (so before the 120 seconds from container creation) we see that it starts as we expect it to. 
Let's look at the logs:

```
kubectl logs storefront-5f7c84b85-8p6bx
```

![image](https://user-images.githubusercontent.com/42166489/107917553-1d25fc80-6f8e-11eb-94a0-30cc631d50a7.png)

Run the kubectl command again.

```
kubectl logs storefront-5f7c84b85-8p6bx
```

You will see multiple entries like the one below:
2020.01.02 16:21:11 INFO com.oracle.labs.helidon.storefront.health.LivenessChecker Thread[nioEventLoopGroup-3-1,10,main]: Not frozen, Returning alive status true, storename My Shop

Look at the pods detailed info to check the state is fine :

```
kubectl describe pod
```

![image](https://user-images.githubusercontent.com/42166489/107917612-36c74400-6f8e-11eb-979d-6c7fd5b29d0e.png)

It's started and no unexpected events!

Now is the time to explain that Not frozen ... text in the status. To enable us to actually simulate the service having a deadlock or resource starvation problem there's a bit of a cheat in the storefront LivenessChecker code :

```
    @Override
    public HealthCheckResponse call() {
        // don't test anything here, we're just reporting that we are running, not that
        // any of the underlying connections to thinks like the database are active

        // if there is a file /frozen then just lock up for 60 seconds
        // this let's us emulate a lockup which will trigger a pod restart
        // if we have enabled liveliness testing against this API
        if (new File("/frozen").exists()) {
            log.info("/frozen exists, locking for " + FROZEN_TIME + " seconds");
            try {
                Thread.sleep(FROZEN_TIME * 1000);
            } catch (InterruptedException e) {
                // ignore for now
            }
            return HealthCheckResponse.named("storefront-live").up()
                    .withData("uptime", System.currentTimeMillis() - startTime).withData("storename", storeName)
                    .withData("frozen", true).build();
        } else {
            log.info("Not frozen, Returning alive status true, storename " + storeName);
            return HealthCheckResponse.named("storefront-live").up()
                    .withData("uptime", System.currentTimeMillis() - startTime).withData("storename", storeName)
                    .withData("frozen", false).build();
        }
```

Every time it's called it checks to see it a file names /frozen exists in the root directory of the container. If it does then it will do a delay (about 60 seconds) before returning the response. Basically this means that by connecting to the container and creating the /frozen file we can simulate the container having a problem. The Not Frozen... is just text in the log data so we can see what's happening. Of course you wouldn't do this in a production system!

Let's see what happens in this case.

First let's start following the logs of your pod. Run the following command (replace the pod Id with yours)

```
kubectl logs -f --tail=10 storefront-b44457b4d-29jr7
```

![image](https://user-images.githubusercontent.com/42166489/107917674-52cae580-6f8e-11eb-83ae-532ca9d711e0.png)

You will see multiple entries like the one below:

2020.01.02 16:21:11 INFO com.oracle.labs.helidon.storefront.health.LivenessChecker Thread[nioEventLoopGroup-3-1,10,main]: Not frozen, Returning alive status true, storename Saki Shop

![image](https://user-images.githubusercontent.com/42166489/107917701-5c544d80-6f8e-11eb-9a3e-cc32f541529a.png)

Look at the pods detailed info to check the state is fine:

```
kubectl describe pod storefront-5f7c84b85-8p6bx
```

![image](https://user-images.githubusercontent.com/42166489/107917723-637b5b80-6f8e-11eb-8cac-bfa6cf195f2d.png)

It's started and no unexpected events!

Now is the time to explain that Not frozen ... text in the status. To enable us to actually simulate the service having a deadlock or resource starvation problem there's a bit of a cheat in the storefront LivenessChecker code :
Every time it's called it checks to see it a file names /frozen exists in the root directory of the container. If it does then it will do a delay (about 60 seconds) before returning the response. Basically this means that by connecting to the container and creating the /frozen file we can simulate the container having a problem. The Not Frozen... is just text in the log data so we can see what's happening. Of course you wouldn't do this in a production system!

Let's see what happens in this case.

First let's start following the logs of your pod. Run the following command (replace the pod Id with yours)

```
kubectl logs -f --tail=10 storefront-5f7c84b85-8p6bx
```

![image](https://user-images.githubusercontent.com/42166489/107917769-7a21b280-6f8e-11eb-91ea-4241617d86dd.png)

Open new browser window or tab
Go to your cloud account

Once in the cloud account open an OCI Cloud Shell in the new window

Log in to the your container and create the /frozen file (replace the pod Id with yours)

```
kubectl exec -ti storefront-b44457b4d-29jr7 -- /bin/bash
touch /frozen
```

Go back to the window running the logs

Kubernetes detected that the liveness probes were not responding in time, and after 3 failures it restarted the pod.
In the logs we see the following 

Other cloud shell:

![image](https://user-images.githubusercontent.com/42166489/107917801-860d7480-6f8e-11eb-83b1-b95fdf1a6ed2.png)

![image](https://user-images.githubusercontent.com/42166489/107917814-8a399200-6f8e-11eb-9d42-40011ca811a5.png)

Kubectl tells us there's been a problem and a pod has done a restart for us (the kubectl connection to the pod will have terminated when the pod restarted)
Check the pod status

```
kubectl get pods
```

Look at the deployment events for the pod

```
kubectl describe pod storefront-b44457b4d-29jr7
```

![image](https://user-images.githubusercontent.com/42166489/107917842-91f93680-6f8e-11eb-925b-ffc56691411b.png)

The pod became unhealthy, then the container was killed and a fresh new container restarted.
(Leave the extra window open as you'll be using it again later).

### Readiness:
The first two probes determine if a pod is alive and running, but it doesn't actually report if it's able to process events. That can be a problem if for example a pod has a problem connecting to a backend service, perhaps there is a network configuration issue and the pods path to a back end service like a database is not available.

In this situation restarting the pod / container won't do anything useful, it's not a problem with the container itself, but something outside the container, and hopefully once that problem is resolved the front end service will recover (it's it's been properly coded and doesn't crash, but in that case one of the other health mechanisms will kick in and restart it) BUT there is also no point in sending requests to that container as it can't process them.

Kubernetes supports a readiness probe that we can call to see is the container is ready. If the container is not ready then it's removed from the set of available containers that can provide the service, and any requests are routed to other containers that can provide the service.

Unlike a liveness probe, if a container fails it's not killed off, and calls to the readiness probe continue to be made, if the probe starts reporting the service in the container is ready then it's added back to the list of containers that can deliver the servcie and requests will be routed to it once more.

Make sure you are in the folder $HOME/helidon-kubernetes
Edit the file storefront-deployment.yaml
Look for the section (just after the Liveness probe) where we define the readiness probe.

![image](https://user-images.githubusercontent.com/42166489/107917907-b81ed680-6f8e-11eb-8529-6bc5d75590d9.png)

The various options for readiness are similar to those for Liveliness, except you see here we've got an exec instead of httpGet.

The exec means that we are going to run code inside the pod to determine if the pod is ready to serve requests. The command section defines the command that will be run and the arguments. In this case we run the /bin/bash shell, -c means to use the arguments as a command (so it won't try and be interactive) and 'curl -s http://localhost:9080/health/ready | grep "\"outcome\":\"UP\""' is the command.

Some points about 

```
'curl -s http://localhost:9080/health/ready | grep "\"outcome\":\"UP\""'
```

Firstly this is a command string that actually runs several commands connecting the output of one to the input of the other. If you exec to the pod you can actually run these by hand if you like

The first (the curl) gets the readiness data from the service (you may remember this in the Helidon labs) In this case as the code is running within the pod itself, so it's a localhost connection and as it'd direct to the micro-service it's http

```
root@storefront-b44457b4d-29jr7:/# curl -s http://localhost:9080/health/ready 
{"outcome":"UP","status":"UP","checks":[{"name":"storefront-ready","state":"UP","status":"UP","data":{"storename":"My Shop"}}]}
```

We can just use the final command the grep to look for a line containing "outcome":"UP" We do however have to be careful because " is actually part of what we want to look for (and if you ran the text thoguht a json formatter you may have space characters) So to define these as a constant we need to ensure it's a single string, we do this by enclosing the entire thing in quotes " and to prevent the " within the string being interpreted as end of string (and thus new argument) characters we need to escape them, hence we end up with "\"outcome\":\"UP\""

Now try it out:

Connect to the pod

```
kubectl exec -ti storefront-b44457b4d-29jr7 -- /bin/bash
```

Run the command in the pod:    

```
curl -s http://localhost:9080/health/ready | grep "\"outcome\":\"UP\""
```

{"outcome":"UP","status":"UP","checks":[{"name":"storefront-ready","state":"UP","status":"UP","data":{"storename":"My Shop"}}]}

In this case the pod is ready, so the grep command returns what it's found. We are not actually concerned with what the pod returns in terms of string output, we are looking for the exit code, interactively we can find that by looking in the $? variable:

Inside the pod look at the output of the previous command

```
echo $?
```

output: 0

And can see that the variable value (which is what's returned back to the Kubernetes readiness infrastructure) is 0. In Unix / Linux terms this means success.

If you want to see what it would do if the outcome was not UP try running the command changing the UP to DOWN like this (or actually anything other than UP). Important While you can run this command in the pods shell DO NOT modify the actual yaml files.

root@storefront-b44457b4d-29jr7:/# curl -s http://localhost:9080/health/ready | grep "\"outcome\":\"DOWN\""
root@storefront-b44457b4d-29jr7:/# echo $?
1

In this case the return value in $? is 1, not 0, and in Unix / Linux terms that means something's gone wrong.

The whole thing is held in single quotes 'curl -s http://localhost:9080/health/ready | grep "\"outcome\":\"UP\""' to that it's treated as a single string by the yaml file parser and when being handed to the bash shell.

Remember, in this case the command is executed inside the container, so you have to make sure that the commands you want to run will be available. This is especially important if you change the base image, you might find you are relying on a command that's no longer there, or works in a different way from your expectations.

That's all we're going to do with bash shell programming for now!
Having made the changes let's undeploy the existing configuration and then deploy the new one.

In the other OCI cloud shell:
![image](https://user-images.githubusercontent.com/42166489/107944768-a6e8c080-6fb4-11eb-9d7c-05b13a8f1705.png)

In the OCI Cloud Shell
Navigate to the $HOME/helidon-kubernetes folder
Let's stop the services running, run the undeploy.sh script

```
bash undeploy.sh
```

![image](https://user-images.githubusercontent.com/42166489/107944790-b23bec00-6fb4-11eb-95a6-b089a2ab936a.png)

Check only the services remain running :
```
kubectl get all
```

Now let's deploy them again, run the deploy.sh script, be prepared to run kubectl get all within a few seconds of the deploy finishing.
Run the deploy script

```
bash deploy.sh
```

Immediately run the command in the OCI cloud shell

```
kubectl get all
```

![image](https://user-images.githubusercontent.com/42166489/107944819-bbc55400-6fb4-11eb-8abe-4e02caf1e057.png)

Now everything is ready, but why the delay ? What's caused it ? And why didn't it happen before ?

Well the answer is simple. If there is a readiness probe enabled a pod is not considered ready until the readiness probe reports success. Prior to that the pod is not in the set of pods that can deliver a service.

As to why it didn't happen before, if a pod does not have a readiness probe specified then it is automatically assumed to be in a ready state as soon as it's running

What happens if a request is made to the service while before the pod is ready ? Well if there are other pods in the service (the selector for the service matches the labels and the pods are ready to respond) then the service requests are sent to those pods. If there are no pods ab.e to service the requests than a 503 "Service Temporarily Unavailable" is generated back to the caller

To see what happens if the readiness probe does not work we can simply undeploy the stock manager service.

    First let's check it's running fine (replace the with the one for your service, and be prepared for a short delay as we'd just restarted everything)

curl -i -k -X GET -u jack:password https://<external IP>/store/stocklevel
curl -i -k -X GET -u jack:password https://152.67.28.51/store/stocklevel

HTTP/1.1 424 Failed Dependency
Date: Fri, 12 Feb 2021 10:45:34 GMT
Content-Type: application/json
Transfer-Encoding: chunked
Connection: keep-alive

Now let's use kubectl to undeploy just the stockmanager service
kubectl delete -f stockmanager-deployment.yaml

Let's check the pods status
kubectl get pods

The stock manager service is being stopped (this is quite a fast process, so it may have completed before you ran the command). After 60 seconds or so if we run kubectl to get everything we see it's gone (note this is all, not pods here)

Make sure that the stockmanager pod and deployment are terminated
kubectl get all

Something else has also happened though, the storefront service has no pods in the ready state, neither does the storefront deployment and replica set. The readiness probe has run against the storefront pod and when the probe checked the results it found that the storefront pod was not in a position to operate, because the service it depended on (the stock manager) was no longer available.

Let's try accessing the service (replace with the one for your service)
curl -i -k -X GET -u jack:password https://<external IP>/store/stocklevel

![image](https://user-images.githubusercontent.com/42166489/107944852-c97ad980-6fb4-11eb-8068-09874cd992d8.png)

The service is giving us a 503 Service Temporarily Unavailable message. Well to be precise this is coming from the Kubernetes as it can't find a storefront service that is in the ready state.

Let's start the stockmager service using kubectl again
kubectl apply -f stockmanager-deployment.yaml

Immediately let's look at the situation

```
kubectl get all
```

Again Looking at the kubectl output about 120 seconds later:

```
kubectl get all
```

The storefront readiness probe has kicked in and the services are all back in the ready state once again (replace with the one for your service)

![image](https://user-images.githubusercontent.com/42166489/107944883-d26bab00-6fb4-11eb-8a14-2b501f16ba54.png)

Check the service is responding properly now

```
curl -i -k -X GET -u jack:password https://<external IP>/store/stocklevel
```

Startup probes:

You may have noticed above that we had to wait for the liveness probe to complete it's initial delay it started checking. As the liveness probe checks for the service running this means we can't start checking until liveness probe has started. But equally we don't want to set the initial delay of the liveness probe to be to low as it might start checking before the service is running, and then kill the service off before it's finished it's setup. In the case of the storefront this is not to much of a problem as the service starts up fast, but for a more complex service, especially a legacy service that may have a startup time that varies a lot depending on other factors, this could be a problem.

To solve this in Kubernetes 1.18 the concept of startup probes will be introduced as a beta feature. A startup probe is a very simple probe that tests to see if the service has started running, usually at a basic level, and then starts up the liveness probes. Effectively the startupProbe means there is no longer any need for the initialDelaySeconds on the liveness probe.

Let's enable this.
Edit the storefront-deployment.yaml file
Locate the startupProbe: section

Remove the # at the beginning of each line, only remove that character, be careful not to remove anything else
The result should look like this:

![image](https://user-images.githubusercontent.com/42166489/107944908-da2b4f80-6fb4-11eb-957e-1b31d6fd8fed.png)

Locate the initialDelaySeconds: in the livenessprobe section
Place a # just before the content

The result should look like this 

![image](https://user-images.githubusercontent.com/42166489/107944939-e44d4e00-6fb4-11eb-9a40-9788b0358457.png)

Restart the storefront

```
kubectl apply -f storefront-deployment.yaml
```

![image](https://user-images.githubusercontent.com/42166489/107944971-f3cc9700-6fb4-11eb-9583-94ee85954228.png)

There isn't anything obvious happening here from a user perspective, but now if there is a problem with the deployment the liveness probe will pick it up a lot faster.

### Cloud Native - Horizontal Scaling manually:

Kubernetes has built in support for easily managing the horizontal scaling of services.

In many of the labs when you've looked at the contents of the namespace you'll have seen things called replica sets, and may have wondered what they are. We can get this info using kubectl.

Let's look at the replica sets. In the OCI Cloud Shell, type
```
kubectl get all
```

```
Saikat_Dey@cloudshell:~ (ap-mumbai-1)$ kubectl get all
NAME                              READY   STATUS    RESTARTS   AGE
pod/stockmanager-58c6c94b-dwh9k   1/1     Running   0          58m
pod/storefront-6ffdd9cc6b-gc9vr   1/1     Running   0          41m
pod/storefront-6ffdd9cc6b-p4xr9   1/1     Running   0          9m9s
pod/storefront-6ffdd9cc6b-tlndv   1/1     Running   0          9m9s
pod/storefront-6ffdd9cc6b-xb2v8   1/1     Running   0          9m9s
pod/zipkin-7d6d5f96fd-bn2cx       1/1     Running   0          80m

NAME                   TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)             AGE
service/stockmanager   ClusterIP   10.96.60.86    <none>        8081/TCP,9081/TCP   23h
service/storefront     ClusterIP   10.96.26.210   <none>        8080/TCP,9080/TCP   23h
service/zipkin         ClusterIP   10.96.43.112   <none>        9411/TCP            23h

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/stockmanager   1/1     1            1           58m
deployment.apps/storefront     4/4     4            4           80m
deployment.apps/zipkin         1/1     1            1           80m

NAME                                    DESIRED   CURRENT   READY   AGE
replicaset.apps/stockmanager-58c6c94b   1         1         1       58m
replicaset.apps/storefront-6ffdd9cc6b   4         4         4       41m
replicaset.apps/storefront-7fcddb9d5b   0         0         0       80m
replicaset.apps/zipkin-7d6d5f96fd       1         1         1       80m
```

You can see that there is a replica set for each deployment. They are actually implicitly defined in the deployment yaml files, though they don't have an explicit section the replicas : 1 line tells the Kubernetes deployment to automatically create a replica set for us with one of the pods (as the pod is described later in the file). If we hadn't specified the replicas : 1 line it defaults to a single pod. Kubernetes will create a replica set automatically for us with a single pod for each deployment, and as we've seen in the health, liveness and readiness labs if there is a problem it will automatically restart the services so that there is one service available.

Deployments vs replica sets

We can if we want modify the number of replicas in the deployment by modifying the YAML and then re-applying it, or of course we could use the kubectl scale command to do it as well, but for this lab we're going to use the dashboard.
Open the dashboard and switch to your namespace (tg-helidon in my case)
In the left menu under the Workloads section chose Deployments.

![image](https://user-images.githubusercontent.com/42166489/107946512-24adcb80-6fb7-11eb-810d-d7c0ed501800.png)

(I know you can go direct to the replica sets, but I want to show how they are connected to the deployments)

You can see our three deployments (Zipkin, storefront and stock manager) and in the Pods column we can see that each has 1 / 1 pods (So one pod running out of a requested one pod to run).
Click on the storefront deployment for more details.

We'll see the details of the deployment, scroll down a bit to get to the Replica sets section
In the New replica set section we can see details of the current replica set, there is nothing in the Old Replica Sets section as we haven't made any rolling updates, we'll do that later in the lab.

In the New replica set section if we click on the replica set name we can see the details of it.

In the Pod Status section we can see that there is 1 pod running out of 1 pod desired.
Go back to the storefront deployment.

Scaling the deployment is simple, :
Click on the Scale Icon on the upper right of the deployment pagescaling-scale-icon
In the new pop up enter the desired number of pods you want. Enter 4.

![image](https://user-images.githubusercontent.com/42166489/107946538-2e373380-6fb7-11eb-8dc7-76458c540978.png)

Click the Scale button

Kubernetes immediately gets to work creating new pods for us, you can see this in the pod status section of the page.

![image](https://user-images.githubusercontent.com/42166489/107946575-3a22f580-6fb7-11eb-9e4b-fdb3727ec162.png)

Scroll down to the Replica sets section of the deployment.

And if we drill down into the replica set we can see the pods themselves being created
Click on the replica set name.

![image](https://user-images.githubusercontent.com/42166489/107946606-44dd8a80-6fb7-11eb-884f-e0ca4f36ac32.png)

Notice that most of these have a grey partially complete "pie graph" at the start of the line. That means that the pod is in the process of starting up (probably pulling the image). On your screen you may have a red warning circle, that means that the pod has started, but is not yet ready (I.e. the readiness probe is failing). The green check is on the original pod, which was of course running before we started the scaling operation. The Service will send requests to the pods marked green

Remember that the storefront uses a readiness probe, so it may be a while before those pods are reporting ready.
Click on the pod name of a pod that is not yet ready, Look at the Conditions section.

![image](https://user-images.githubusercontent.com/42166489/107946631-4dce5c00-6fb7-11eb-9757-e4316ae1cfe5.png)

You can see the details of the startup process, in this case the pod is not ready because the container within it is not yet ready.

After a short while the pod will become ready
Once they are ready return to the replica set.

Click the replica set name in the controlled by section (storefront-579968f88b in this case).
That returns you to the replica set page, you can see that they will show up in the replica set as ready

I had to scroll down a little to get this image.

![image](https://user-images.githubusercontent.com/42166489/107946660-5888f100-6fb7-11eb-83d6-09d573c89b64.png)

If you scroll down in the replica set page you'll see the Events section, and can see the pods have been created.

![image](https://user-images.githubusercontent.com/42166489/107946671-60489580-6fb7-11eb-9f58-0dec1a1d9357.png)

And if we go back to the deployments list we'll see the pods is now 4 of 4 ready
Click Deployments on the left menu.

![image](https://user-images.githubusercontent.com/42166489/107946708-69396700-6fb7-11eb-9d21-dbb3684063ec.png)

In the pods section we can see that we have 4 pods

If on the deployments page we scroll down to the Events section and see the list of events for that deployment, our scaling event is there! 

![image](https://user-images.githubusercontent.com/42166489/107946736-722a3880-6fb7-11eb-8bcc-d8c2816aa1ee.png)

If you are on a Kubernetes cluster with multiple physical nodes the scaling operation will try and place the pods on different nodes, protecting the service so if one node fails for any reason the other nodes can still be used to provide the service.

Reset the count:
To prepare for the following lab sections please go back to the storefront deployment and follow the approach described above to scale it back down to a single pod.

### Cloud Native - Auto scaling

This module explores how you can configure Kubernetes to automatically scale the number of pods for your service to handle changes in demand with no manual intervention.

We've seen how we can increase (or decrease) the number of pods underlying a service and that the service will automatically balance the load across the pods for us as the pod count changes.

This is great, but it required manual intervention to change the number of pods, and that means we need to keep an eye on what's happening. Fortunately for us Kubernetes has support for automating this process based on rules we define.

This is the simplest form of auto scaling, though it is also the least flexible as CPU and memory usage may not always be the most effective indicator of when scaling is required.

To gather the data we need to have installed the metrics server. This is a replacement for an older Kubernetes service called heapster.

Note that it's also possible to use Prometheus as a data source which will allow you to auto-scale on custom metrics, for example the number of requests to your service.

For now we are going to use the simplest approach of the metrics server.
Step 1a: Installing the metrics server

Install a helm repo that contains the chart for the metrics server

```
helm repo add bitnami https://charts.bitnami.com/bitnami
```

Update the repo:
```
helm repo update
```

In the OCI Cloud Shell install the metrics server by typing

helm install metrics-server bitnami/metrics-server --namespace kube-system --set apiService.create=true

![image](https://user-images.githubusercontent.com/42166489/107946783-82daae80-6fb7-11eb-907b-d4d438c2e420.png)

It will take a short time for the metrics server to start up, but you can check the progress using kubectl

In the OCI Cloud Shell type
```
kubectl get deployments -n kube-system
```

Using the captured metrics
Once the metrics server is running (it will have an AVAILABLE count of 1) you can get information on the state of the system
Let's look at how the nodes in the cluster are doing. In the OCI Cloud Shell type

```
kubectl top nodes
```

![image](https://user-images.githubusercontent.com/42166489/107946813-8bcb8000-6fb7-11eb-930f-06644c56e479.png)

Note that like the other times we've used kubectl this uses the namespace configured as the default when you ran the create-namespace.sh command

Let's have a look at what's happening in the kube-system namespace. In the OCI Cloud Shell type
kubectl top pods -n kube-system

![image](https://user-images.githubusercontent.com/42166489/107946852-94bc5180-6fb7-11eb-98e6-53c577109355.png)

You can see in the output above that all of the pods are using very small amounts of CPU here, this is because we're not really putting any load on them. Let's run a script that will put load on the services to see what's happening.

In the helidon-kubernetes project in the cloud-native-kubernetes/auto-scaling folder run the following. You must to replace here with the one for your ingress controller (see the expansion section above for details of how to get this if you've forgotten)

Switch to the auto scaling directory
```
cd $HOME/helidon-kubernetes/cloud-native-kubernetes/auto-scaling
```

Start a load generator. In the OCI Cloud Shell (As usual replace with the IP address of the load balancer).

```
bash generate-load.sh <external IP> 0.1
```

Note that the 0.1 controls how long the script waits, depending on how fast things respond below you may need to adjust the rate up (fewer requests) or down (more requests) If you chose an especially powerful processor you may need to open another cloud window in your browser and run a second load in that as well, or adjust the CPU available to the pod.

```
cd $HOME/helidon-kubernetes/cloud-native-kubernetes/auto-scaling
bash generate-load.sh 152.67.28.51 0.1
```

![image](https://user-images.githubusercontent.com/42166489/107946882-a3a30400-6fb7-11eb-8802-4700470f5b0d.png)

The script will just get the stock level data, attempting to do so about 10 times a second (the 0.1 above is the time in seconds to wait after the request returns). The returned data will be displayed in the […] (for clarity here it's been removed

Open up a new window on the OCI console
Open up the OCI Cloud shell in the new window

Let the script run for about 75 seconds (the iteration counter reaches over 750) This will let the load statistics level out.

This will have increased the load, to see the increased load
In the new OCI Cloud Shell type

```
kubectl top pods
```

Notice that in this particular example the CPU here for the storefront is at 251m (your number may be different). This is actually the limit allowed in the storefront deployment.yaml file, which has a resource restriction of 250 milli CPU specified.

If the load does not reach 250 then you may need to adjust the request rate, or open another cloud shell in a new browser tab / window and run another load generator to ensure you can hit the limit (and auto scaling will kick in once we've configured it).

We can also get the current resource level for the container using kubectl and the jsonpath capability
In the OCI Cloud Shell (substitute your storefront pod name) type

```
kubectl get pod storefront-79c465dc6b-x8fbl -o=jsonpath='{.spec.containers[0].resources.limits.cpu}'
```

![image](https://user-images.githubusercontent.com/42166489/107946919-aef62f80-6fb7-11eb-89f3-4e798e1f53e2.png)

In the OCI Cloud shell window running the load generator stop it using Control-C

Configuring the autoscaler:

That we have hit the limit is almost certainly a problem, it's quite likely that the performance of the service is limited out because of this. Of course it may be that you have made a deliberate decision to limit the service, possibly to avoid overloading the back end database (though as it's an ATP database it can scale automatically for you if the load gets high)

To fix this problem we need to add more pods, but we don't want to do this by hand, that would mean we'd have to be monitoring the system all the time. Let's use a the Kubernetes autoscale functionality to do this for us

Setup autoscale (normally of course this would be handled using modifications to the YAML file for the deployment)

If your CPU load was not exceeding 125 then reduce the --cpu-percent setting to a lower number so that it's below the required level. For example if the CPU load for the storefront was not exceeding 50 (so 20% of the allowed load on a single pod) then a setting of --cpu-percent=15 will trigger a scaling.

In the OCI Cloud Shell create an auto scaller
kubectl autoscale deployment storefront --min=2 --max=5 --cpu-percent=25

![image](https://user-images.githubusercontent.com/42166489/107947251-288e1d80-6fb8-11eb-9874-c0a306ef1ac2.png)

The autoscaler will attempt to achieve a target CPU load of 25%, (or whatevery you used) adding or removing pods to the deployment as needed to meet that goal, but never going below two pods or above 5

We can see what the system has found by looking in the Horizontal Pod Autoscalers

In the OCI Cloud Shell type
kubectl get horizontalpodautoscaler storefront

Note that because we told the auto scaler that we wanted a minimum of 2 pods the REPLICAS has already increased to 2. Because this deployment is "behind" the storefront service the service will automatically arrange for the load to be balanced across both pods for us.

Of course typing horizontalpodautoscaler takes some time, so kubectl has an alias setup for us, we can just type hpa instead, for example.

A few points on the output The TARGET column tells us what the current load is first, this is the average across all the pods in the deployment, Then the target load the autoscaler will aim to achieve by adding or removing pods.
You can get more detail on the autoscaler state

Getting the autoscaler details. In the OCI Cloud Shell type
kubectl describe hpa storefront

![image](https://user-images.githubusercontent.com/42166489/107947279-317eef00-6fb8-11eb-809e-b9a07d878559.png)


Now restart the load generator program. Note that you may need to change the sleep time from 0.1 to a different value if the load generated is not high enough to trigger an autoscale operation, but don't set it to low!

In the OCI Cloud Shell where you were running the load balancer previously (substitute the IP address for the ingress for your cluster) If needed run multiple in different cloud shells.

cd $HOME/helidon-kubernetes/cloud-native-kubernetes/auto-scaling
bash generate-load.sh 152.67.28.51 0.1

![image](https://user-images.githubusercontent.com/42166489/107947303-393e9380-6fb8-11eb-84ef-27bcd383917c.png)

Let's check there is a load. In the OCI Cloud Shell type
kubectl top pods

Let's look at the autoscaler. In the OCI Cloud Shell type
kubectl get horizontalpodautoscaler storefront

Let's look at the autoscaler details. In the OCI Cloud Shell type
kubectl describe hpa storefront

Let's look at those pods. In the OCI Cloud Shell type
```
kubectl top pods
```

All 5 pods are running and the service is distributing the load amongst them. Actually some of the storefront pods above are probably still in their startup phase as I gathered the above data immediately after getting the auto scale description.

Let's get the autoscaler summary again. In the OCI Cloud Shell type

```
kubectl get hpa storefront
```

![image](https://user-images.githubusercontent.com/42166489/107947339-43609200-6fb8-11eb-9c03-dd4461da8323.png)

Other cloud shell. While the pod was running the load was more hence was distributed among all..
In the second scenario the pod was stopped so load was reduced.

![image](https://user-images.githubusercontent.com/42166489/107947375-4c516380-6fb8-11eb-847e-84d83b01a089.png)

We can see that the average load across the deployment is still over 25%, if is had not reached the maximum number of pods then the auto scaler would be trying to meet our goal of no more than 50% average load by adding additional pods.

If you want you can also see the pods being added in the Kubernetes dashboard,

Open the Kubernetes Dashboard
Make sure you are in your namespace
In the left menu Under workloads select Deployments then click on the storefront deployment
In the Pods section you can see that in this case it's scaled to 4 pods.

![image](https://user-images.githubusercontent.com/42166489/107947401-54a99e80-6fb8-11eb-834a-490af93caeda.png)

![image](https://user-images.githubusercontent.com/42166489/107947410-58d5bc00-6fb8-11eb-9806-794c39a04e29.png)

You can see the pod details by opening the replica set.

![image](https://user-images.githubusercontent.com/42166489/107947426-5ffcca00-6fb8-11eb-9009-db187a15539f.png)

In the load generator window(s) stop the script by typing Control-C.

Note that the metrics server seems to operate on a decaying average basis, so stopping the load generating script will not immediately drop the per pod load. This means that it may take some time after stopping the load generator script for the autoscaler to start removing unneeded pods.

The autoscaler tries not to "thrash" the system by starting and stopping pods all the time. Because of this it will only remove pods every few minutes rather than immediately the load becomes low, additionally it will also only remove a few pods at a time. The autoscaler documentation describes the algorythm.

For now let's delete the autoscaler to we can proceed with the next part of the lab with a known configuration.

In the OCI Cloud Shell type

```
kubectl delete hpa storefront
```

![image](https://user-images.githubusercontent.com/42166489/107947452-67bc6e80-6fb8-11eb-9d89-45db42c84d26.png)

Note that this just stops changes to the number of pods, any existing pods will remain, even if there are more (or less) than specified in the deployment document. Of course now the auto scaler has been deleted regardless of the load that number will no longer change automatically.

To return to the numbers of replicas originally defined we'll use kubectl

In the OCI Cloud Shell type

```
kubectl scale --replicas=1 deployment storefront
```

Saikat_Dey@cloudshell:auto-scaling (ap-mumbai-1)$ kubectl scale --replicas=1 deployment storefront
deployment.apps/storefront scaled

Now let's check what's happening

In the OCI Cloud Shell type

```
kubectl get all pods
```

![image](https://user-images.githubusercontent.com/42166489/107947483-72770380-6fb8-11eb-8c8a-177787be5480.png)

Autoscaling on other metrics:

We have here looked at how to use CPU and memory to determine when to autoscale, that may be a good solution, or it may not. Kubernetes autoscaling can support the use of other metrics to manage autoscaling.

These other metrics can be other Kuberneties metrics (known as custom metrics) for example the number of requests to the ingress controller, or (with the provision of the Prometheus Adaptor (helm chart)) any metric that Prometheus gathers. This last is especially useful as it means you can autoscale on what are effectively business metrics.

It's also possible to autoscale on metrics provides from outside Kubernetes (these are known as external metrics). this is only recommended as a last resort however due to the security implications, and custom metrics are preferred.
Links: https://github.com/prometheus-community/helm-charts
https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/#autoscaling-on-multiple-metrics-and-custom-metrics

Autoscaler and rolling upgrades - The autoscaler will correctly operate in conjunction with rolling upgrades.

### Cloud Native - Rolling Updates:

One of the problems when deploying an application is how to update it while still delivering service, and perhaps more important (but usually given little consideration) how to revert the changes in the event that the update fails to work in some way.

Changes by the way come in multiple areas, it could be application code changes (Kubernetes sees these as a change in the container image) or it could be a change in the configuration defining a deployment (and it's replica sets / pods)

The update process for code changes involves using your development tooling to create a new container based on the changed code (You presumably have separate processes for managing your source code versions). As part of your container creation process you must give it a different version number so it's easy to identify which container comes from which version of your source code, and also to ensure you differentiate between different releases at the image level. You'd then update the service definition to use the new image by editing and applying the yaml file or using kubectl to directly change the container image.

The update process for configuration changes is to modify the yaml file that defines your service, for example defining different volume mounts, and then applying the change, or to issue a kubectl command to directly update the change.

For both approaches Kubernetes will keep track of the changes and will undertake a rolling upgrade strategy.

As a general observation though it may be tempting to just go in and modify the configuration directly with kubectl … this is a bad thing to do, it's likely to lead to unrecorded changes in your configuration management system so in the event that you had to do a complete restart of the system changes manually done with kubectl are likely to be forgotten. It is strongly recommended that you make changes by modifying your yaml file, and that the yaml file itself has a versioning scheme so you can identify exactly what versions of the service a given yaml file version provides. If you must make changes using kubectl (say you need to make a minor change in a test environment) then as soon as you decide it should be permanent then make the corresponding change in the yaml file and do a rolling upgrade using the yaml file to ensure you are using the correct configuration (after all, you may have made a typo in either the kubectl or yaml file).

How to do a rolling upgrade in our setup:
So far we've been stopping our services (the undeploy.sh script deletes the deployments) and then creating new ones (the deploy.sh script applies the deployment configurations for us) This results in service down time, and we don't want that. But before we can switch to properly using rolling upgrades there are a few bits of configuration we should do
Step 2a: Defining the rolling upgrade

Kubernetes aims to keep a service running during the rolling upgrade, it does this by starting new pods to run the service, then stopping old ones once the new ones are ready. Through the magic of services and using labels as selectors the Kubernetes run time adds and removed pods from the service. This will work with a deployment whose replica sets only contain a single pod (the new pod will be started before the old one is stopped) but if your service contains multiple pods it will use some configuration rules to try and manage the process in a more balanced manner and sticking reasonably closely to the number of pods you've asked for (or the auto scaler has).

We are going to once again edit the storefront-deployment.yaml file to give Kubernetes some rules to follow when doing a rolling upgrade. Importantly however we're going to edit a Copy of the file so we have a history.

In the OCI Cloud Shell navigate to the folder $HOME/helidon-kubernetes

Copy the storefront-deployment yaml file:

```
cp storefront-deployment.yaml storefront-deployment-v0.0.1.yaml
```

Edit the new file storefront-deployment-v0.0.1.yaml

The current contents of the section of the file looks like this:

![image](https://user-images.githubusercontent.com/42166489/107947720-ca156f00-6fb8-11eb-833e-781ebbe10bd8.png)

Set the number of replicas to 4:
After the replicas:4 line, add
    strategy:
      type: RollingUpdate

Finally we're going to tell Kubernetes what limits we want to place on the rolling upgrade.
    Under the type line above, and at the same indent add the following
rollingUpdate:
        maxSurge: 1
        maxUnavailable: 1

This limits the rollout process to having no more than 1 additional pods online above the normal replicas set, and only one pod below that specified in the replica set unavailable. So the roll out (in this case) allows us to have up to 5 pods running during the rollout and requires that at least 3 are running.

Note that unless you have very specific reasons don't change the default settings for strategy type and maxSurge / minUnavailable. We are setting these for two reasons. First to show that the settings are available, and secondly for the purposes of this lab to show the roll out process in a way that let's us actually see what's happening by slowing things down (of course in a production you'd want it to run as fast as possible, so think about the settings used if you do override the defaults)

The section of the file after the changes will look like this.

![image](https://user-images.githubusercontent.com/42166489/107947745-d7caf480-6fb8-11eb-9819-3cfe756baf12.png)

Save the changes.

Applying the rollout strategy:

To do the roll out we're just going to apply the new file. Kubernetes will compare that to the old config and update appropriately.

Apply the new config
```
kubectl apply -f storefront-deployment-v0.0.1.yaml --record
```

deployment.apps/storefront configured. We can have a look at the status of the rollout

```
 kubectl rollout status deployment storefront
```

deployment "storefront" successfully rolled out

If you get a message along the lines of Waiting for deployment "storefront" rollout to finish: 3 of 4 updated replicas are available... this just means that the roll out is still in progress, once it's complete you should see the success message.

Let's also look at the history of this and previous roll outs:

```
kubectl rollout history deployment storefront
```

deployment.apps/storefront 
REVISION  CHANGE-CAUSE
1         kubectl apply --filename=storefront-deployment.yaml --record=true

(The --record tells Kubernetes to keep track of the change)

We can see that the previous state of the deployment resulted from us doing the initial apply. Note that the filename is specified in the rollout, as long as we version our file names we will be able to know exactly what configuration was applied to different versions of the deployment.

One point to note here, these changes only modified the deployment roll out configuration, so there was no need for Kubernetes to actually restart any pods as those were unchanged, however additional pods may have needed to be started to meet the replica count.
Making a change that updates the pods

Of course normally you would make a change, test it and build it, then push to the registry, you would probably use some form of CI/CD tooling to manage the process, for example a pipeline built using the Oracle Developer Cloud Service (other options include the open source tools Jenkins / Hudson and Ansible).

For this lab we are focusing on Helidon and Kubernetes, not the entire CI/CD chain so like any good cooking program we're going to use a v0.0.2 image that we created for you. For the purposes of this module the image is basically the same as the v0.0.1 version, except it reports it's version as 0.0.2

Applying our new image

To apply the new v0.0.2 image we need to upgrade the configuration again. As discussed above this we would normally and following best practice do this by creating a new version of the deployment yaml file (say storefront-deploymentv0.0.2.yaml to match the container and code versions)

However … for the purpose of showing how this can be done using kubectl we are going to do this using the command line, not a configuration file change. This might be something you'd do in a test environment, but don't do it in a production environment or your change management processes will almost certainly end up damaged.

![image](https://user-images.githubusercontent.com/42166489/107947772-e44f4d00-6fb8-11eb-85d7-5788b7d421b3.png)


In the OCI cloud shell Execute the command

```
kubectl set image deployment storefront storefront=fra.ocir.io/oractdemeabdmnative/h-k8s_repo/storefront:0.0.2 --record
```

Let's look at the status of our setup during the roll out

```
kubectl get all
```

![image](https://user-images.githubusercontent.com/42166489/107948128-6f304780-6fb9-11eb-885b-33df249a6c71.png)

We're going to look at these in a different order to the output

Firstly the deployments info. We can see that 3 out of 4 pods are available, this is because we specified a maxUnavailable of 1, so as we have 4 replicas we must always have 3 of them available.

If we look at the replica sets we seem something unusual. There are two replica sets for the storefront. the original replica set (storefront-5f777cb4f5z) has 3 pods available and running, one of them was stopped as we allow one a maxUnavailable of 1. There is however an additional storefront replica set storefront-79d7d954d6 This has 2 pods in it, at the time the data was gathered neither of them was ready. But why 2 pods when we'd only specified a surge over the replicas count of 1 pod ? That's because we have one pod count "available" to us from the surge, and another "available" to us because we're allowed to kill of one pod below the replicas count, making a total of two new pods that can be started.

Finally if we look at the pods themselves we see that there are five storefront pods. A point on pod naming, the first part of the pod name is actually the replica set the pod is in, so the three pods starting storefront-5f777cb4f5- are actually in the replic set storefront-5f777cb4f5 (the old one) and the two pods starting storefront-79d7d954d6- are in the storefront-79d7d954d6 replica set (the new one)

Basically what Kuberntes has done is created a new replica set and started some new pods in it by adjusting the number of pod replicas in each set, maintaining the overall count of having 3 pods available at all times, and only one additional pod over the replica count set in the deployment. Over time as those new pods come online in the new replica set and pass their readiness test, then they can provide the service and the old replica set will be reduced by one pod, allowing another new pod to be started. At all times there are 3 pods running.

Rerun the status command a few times to see the changes

```
kubectl get all
```

If we look at the output again we can see the progress (note that the exact results will vary depending on how long after the previous kubectl get all command you ran this one).

Rerun the status command a few times to see the changes
```
kubectl get all
```

Kubectl provides an easier way to look at the status of our rollout

```
kubectl rollout status deployment storefront
```

![image](https://user-images.githubusercontent.com/42166489/107948161-77888280-6fb9-11eb-8b1f-7ec9d25cf43a.png)

Let's also look at the history of this and previous roll outs:

```
kubectl rollout history deployment storefront
```

![image](https://user-images.githubusercontent.com/42166489/107948175-7f482700-6fb9-11eb-8f24-dbb85bd0cf90.png)

Kubectl provides us with a monitor which updates over time. Once all of the deployment is updated then kubectl returns.

During the rollout if you had accessed the status page for the storefront (on /sf/status) you would sometimes have got a version 0.0.1 in the response, and other times 0.0.2 This is because during the rollout there are instances of both versions running.

If we look at the setup now we can see that the storefront is running only the new pods, and that there are 4 pods providing the service.

```
kubectl get all
```

![image](https://user-images.githubusercontent.com/42166489/107948201-896a2580-6fb9-11eb-8bfc-9d2ee5b6c7eb.png)

One important point is that you'll see that the old replica set is still around, even though it hasn't got any pods assigned to it. This is because it still holds the configuration that was in place before if we wanted to rollback (we'll see this later)

if we now look at the history we see that there have been two sets of changes

```
kubectl rollout history deployment storefront
```

![image](https://user-images.githubusercontent.com/42166489/107948221-90913380-6fb9-11eb-87a1-ab58547a535d.png)

Note that to get the detail of the change you have to use the --record flag

Let's check on our deployment to make sure that the image is the v0.0.2 we expect

```
kubectl describe deployment storefront
```

![image](https://user-images.githubusercontent.com/42166489/107948249-98e96e80-6fb9-11eb-8dcd-55f24c13b1b2.png)

![image](https://user-images.githubusercontent.com/42166489/107948260-9c7cf580-6fb9-11eb-9457-65e188666a4e.png)

We see the usual deployment info, the Image is indeed the new one we specified (in this case fra.ocir.io/oractdemeabdmnative/h-k8s_repo/storefront:0.0.2) and the events log section shows us the various stages of rolling out the update.

We should of course check that our update is correctly delivering a service (replace the IP address with one for your service)

```
curl -i -k -X GET -u jack:password https://<external IP>/store/stocklevel
curl -i -k -X GET -u jack:password https://152.67.28.51/store/stocklevel
```

Now let's check the output from the StatusResource (again replace the IP address with the one for your service)

```
curl -i -k -X GET https://<external IP>/sf/status
```

Now the rollout has completed and all the instances are running the updated image as expected it's reporting version 0.0.2

If you had checked this during the rollout you would have got 0.0.1 or 0.0.2 versions returned depending on which pod you connected to and what version it was running.

![image](https://user-images.githubusercontent.com/42166489/107948280-a43c9a00-6fb9-11eb-973c-e61bdb7bc627.png)

Rolling back a update:

In this case the update worked, but what would happen if it had for some reason failed. Fortunately for us Kubernetes keeps the old replica set around, which includes the config for just this reason.

Let's get the replica set list

```
kubectl get replicaset
```

And let's look at the latest storefront replica

```
kubectl describe replicaset storefront-bc8b44ccf
```

![image](https://user-images.githubusercontent.com/42166489/107948307-ad2d6b80-6fb9-11eb-846e-3db9ca6f82eb.png)

If we look at the Image we can see it's my v0.0.2 image. We can also see stuff like the pods being added during the update.
But let's look at the old replica set

```
kubectl describe replicaset storefront-6ffdd9cc6b
```

![image](https://user-images.githubusercontent.com/42166489/107948346-b61e3d00-6fb9-11eb-829f-64a69b36d395.png)

In this case we see it's showing the old 0.0.1 image.
So we can see how kuberntes keeps the old configurations around (the different revisions are tied to the replica sets)
If we undo the rollout Kubernetes will revert to the previous version

Undo the rollout: --------

```
kubectl rollout undo deployment storefront
```

The rollback process follows the same process as the update process, gradually moving resources between the replica sets by creating pods in one and once they are ready deleting in the other.

Let's monitor the status

```
kubectl rollout status deployment storefront
```

Once it's finished if we now look at the namespace
```
kubectl get all
```

We see that all of the pods are now the original replica set version, and there are no pods in the new one.
If we check this by going to the status we can see the rollback has worked (remember to replace with the one for your service) :

```
curl -i -k -X GET https://<external IP>/sf/status
```

![image](https://user-images.githubusercontent.com/42166489/107948386-c1716880-6fb9-11eb-9257-1c4429fd2e74.png)

Normally of course the testing of the pods would be linked into CI/CD automated tooling that would trigger the rollback if it detected a problem automatically, but here we're trying to show you the capabilities of Kubernetes rather than just run automation.
Important note on external services:

Kubernetes can manage changes and rollbacks within it's environment, provided the older versions of the changes are available. So don't delete your old container images unless you're sure you won't need them! Kubernetes can handle the older versions of the config itself, but always a good idea to keep an archive of them anyway, in case your cluster crashes and takes your change history with it.

However, Kubernetes itself cannot manage changes outside it's environment. It may seem obvious, but Kubernetes is about compute, not persistence, and in most cases the persistence layer is external to Kubernetes on the providers storage environments.

Persistence is by definition about making things persistent, they exist outside the compute operation, and that can cause problems if other elements also use the stored data.

This is especially critical for data in databases. You need to coordinate changes to the database, especially changes to the database schema (which is outside Kubernetes) with changes to the code in the services that access the database (usually running in Kubernetes). Kubernetes can handle the rolling upgrades of the services, but if different versions of your service have incompatible data requirements you've got a problem. Equally if you do an upgrade that changes the database scheme in a way that's incompatible with earlier versions of the service, and then you need to roll back to a previous version you've got a problem.

These issues are not unique to Kubernetes, they have always existed when a new version of some code that interacts with the persistence layer is deployed, but the very fast deployment cycle of microservices enabled my Kubernetes makes this more of a critical issue to consider (in 2016 Netflix were doing thousands of deployments a day, admittedly not all of them would involve persistence system changes).

One common approach used is the have the microservices support different versions of the scheme, and then only upgrade to the new version once all of the microservices that access the data have support for the new version, and sufficient testing has shown that there is likely to be no need to roll back to old versions which wouldn't support the updated schema. Than, and only then is the database (or other persistence) updates to reflect the new structure.

To assist with this you might like to consider and approach to limit access to the data to a single microservice (obviously for resillience there would be multiple instances) which then presents the data to the other consumers, potentially using different API endpoints to reflect the data structure. The new microservices use the new API and the old microservices can continue to use the old API's with the data microservice converting between them and the data. Of course for this to work there needs to be ways to map both API's on to a common data representation (e.g. by using default values for additional fields required by t he new API) and it may not always be possible.

The newer automated tooling integrating CI/CD with automated A/B resting means that even larger numbers of deployments are coming, and some of those will involve persistence changes.

The important thing is to have a strategy for combining microservice rollouts (and roll backs) with persistence (or other external to Kubernetes) changes is critical.

While writing these labs I came across many web pages. Ones that I think are especially useful are detailed in the sections below

### Helm
Helm has been used here to install a number of Kubernetes services. See Helm website for details and if you need to download it there are details in the Helm web page

If you download Helm version 3 it does not come with a pre-configured chart repository. The master repo used to be at kubernetes-charts.storage.googleapis.com however in late 2020 it was deprecated when the help project stopped maintaining it's chart repo, so you may experience access problems, as well as it not containing current charts. Charts are now managed by the individual projects / companies (e.g. https://kubernetes.github.io/dashboard/ for the dashboard). There are some companies that maintain a collection of helm charts from multiple other sources which may be suitable if you can't find the individual project charts (E.g. The Bitnami helm chart repo at https://charts.bitnami.com/bitnami)
Ingress

For more information on Nginx Ingress (this also looks in more detail at the benefits / disadvantages of using an Ingress vs Load Balancer)

If you don't want to use the nginx there is a list of other controllers. Note that different controllers may well use different annotations to the ones we've used in these labs, so for the Ingress rules your version of the yaml files will probably need to be modified.
Kubernetes

We have been using Kubernetes a lot, here is a link to the Kubernetes website:
https://kubernetes.io/

For more info on the structure of the components in Kubernetes see the Kubernetes documentation "What is Kubernetes" page

To learn more about how to define liveness and readiness probes (and if you have Kubernetes 1.16 or later also start up probes) see the probes documentation:
https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/

Managing database and kubernetes rollouts can be complex, here is a link to an external web page that discusses the problem. How to handle DB Scheme changes during Kubernetes rollouts:
https://www.weave.works/blog/how-to-correctly-handle-db-schemas-during-kubernetes-rollouts

### Kubectl
Kubectl is a very powerful tool, but it can be complex to use, fortunately there is a cheet sheet:
https://kubernetes.io/docs/reference/kubectl/cheatsheet

### Prometheus
More details of Prometheus can be find at the Prometheus web page.
https://prometheus.io/

If you want to look at using external storage for prometheus here is an overview.
https://prometheus.io/docs/prometheus/latest/storage/

The Prometheus query language is very powerful for fill details look at it's Query Basics page.
Metrics to gather.
https://prometheus.io/docs/prometheus/latest/querying/basics/

This article at golden metrics has some intereting information on what you should think about monitoring.
https://blog.appoptics.com/the-four-golden-signals-for-monitoring-distributed-systems/

### Service mesh
For more details on the service mesh concept the site servicemesh.io has lot's of interesting information, it is however run by one of the creators of linkerd so may be biased towards that.
https://servicemesh.io/

You have reached the end of this section of the lab and of the Kubernetes sections.





















































