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




