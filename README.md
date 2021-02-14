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

### Step5: Kubernetes labs - Cloud Shell setup (Accessing the cloud shell)
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

Getting your cluster access details:

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






