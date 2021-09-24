# Tutorial
## Project Selection
<walkthrough-project-setup></walkthrough-project-setup>

## Setting up GKE cluster and Agones

<!-- ### Run a Shell Script
In the [shell script](.   ????   ), you will create a webhook trigger, and make a POST request to the webhook URL to trigger the cloud build.
You will use a [cloud build config file](https://gitlab.endpoints.cn-gaming-cicd.cloud.goog/gaming-ci-cd-automation/core/-/blob/main/cloud-build/cloud_build_terraform.yaml) to apply terraform scripts. It includes the following steps:
1. SSH authentication and clone the Gitlab repository
2. Replace the project id field
3. Replace the cluster name field
4. Terraform init, plan and apply

Copy the script to the cloud shell, to run the script, you need to provide the following parameters: -->

### Create a webhook trigger

Go to the Cloud Build, create a webhook trigger:
<walkthrough-menu-navigation sectionId="CLOUD_BUILD_SECTION">Cloud Build</walkthrough-menu-navigation>

#### 1. Click **Create trigger**.
#### 2. Enter the following trigger settings:
1. **Name**: A name for your trigger.
2. **Event**: Select **Webhook event** to set up your trigger to start builds in response to incoming webhook events.
3. **Webhook URL**: Use the webhook URL to authenticate incoming webhook events.
4. Select **Create Secret**: Create a new secret.

#### 3. **Configuration**: 
Copy and paste the [cloud build config file](https://gitlab.endpoints.cn-gaming-cicd.cloud.goog/gaming-ci-cd-automation/core/-/blob/main/cloud-build/cloud_build_terraform.yaml). You will use it to apply terraform scripts. 

#### 4. **Substitutions**: define 2 substitution variables using this field.
**Variable 1**
```
_CLUSTER_NAME
```
Value
```
$(body.cluster_name)
```
**Variable 2**
```
_REGION
```
Value
```
$(body.region)
```
\
It should look like this:
![alt_text](https://lh4.googleusercontent.com/yr0xBiBJC3xn_FxX9OfyCcMilj6bNYHwKmZC-fsMGIeXdcYVQB4VDAwAArolk0mrwM0r9MOVGrq_s_2jJ7y97zcbnpE4XG0fbQjhZIFzJtZ0cUc5EG8XlYSG_n_NJsDNpYGIBImxSnrkAdM1vISA5DROKGiWfzunrdFdbTt5MKybKwBH=s0 "image_tooltip")


## Trigger the build
Open the cloud shell <walkthrough-cloud-shell-icon></walkthrough-cloud-shell-icon>.

Select the trigger you created, copy the webhook URL and paste it in the following command. \
Specify the <CLUSTER_NAME> and <REGION>.

```bash
curl -X POST -H "application/json" "<WEBHOOK_URL>" -d '{"cluster_name":"<CLUSTER_NAME>", "region":"<REGION>"}'
```

The “curl” command will trigger cloud build, by specifying the cluster name and the region using the JSON format, the parameters will be replaced dynamically in the cloud build config file.

### Test the build
Go to the **Cloud Build** :
<walkthrough-menu-navigation sectionId="CLOUD_BUILD_SECTION">Cloud Build</walkthrough-menu-navigation>
Go to the **history**, you should see a new build is running.

**Please wait until the GKE cluster is created before going to the next step.**

Go to the **Kubernetes Enginee**, you should see a new cluster is created.
<walkthrough-menu-navigation sectionId="KUBERNETES SECTION">Cloud Build</walkthrough-menu-navigation>


## Step 2. Setting up game server
### Create a webhook trigger
Go to the Cloud Build, create a webhook trigger:
<walkthrough-menu-navigation sectionId="CLOUD_BUILD_SECTION">Cloud Build</walkthrough-menu-navigation>
#### 1. Click **Create trigger**.
#### 2. Enter the following trigger settings:
1. **Name**: A name for your trigger.
2. **Event**: Select **Webhook event** to set up your trigger to start builds in response to incoming webhook events.
3. **Webhook URL**: Use the webhook URL to authenticate incoming webhook events.
4. Select **Create Secret**: Create a new secret.

#### 3. **Configuration**: Copy and paste the inline build config file.
You will use the [cloud build config file](https://gitlab.endpoints.cn-gaming-cicd.cloud.goog/gaming-ci-cd-automation/core/-/blob/main/cloud-build/cloud_build_fleet_configs.yaml) to apply the fleet configurations. 
#### 4. **Substitutions**: define 3 substitution variables using this field.
<!-- ```bash
_CLUSTER_NAME='$(body.cluster_name)'
_REGION='$(body.region)'
_CONFIG_FILE='$(body.config_file)'
``` -->
**Variable 1**
```
_CLUSTER_NAME
```
Value
```
$(body.cluster_name)
```
**Variable 2**
```
_REGION
```
Value
```
$(body.region)
```
**Variable 3**
```
_CONFIG_FILE
```
Value
```
$(body.config_file)
```
It should look like this:
![alt_text](https://lh3.googleusercontent.com/Qv6vlAcIdwEUO23cT8iJJIaM9k6BPVHqHsPyJoj2jXfyA9PAUoQojmq9SdQEhlzQrKVOOehrDrScJlGbpdSPFsspk14NwKmHH-9K6BT-n687iphEXDNTFoxPAmziwspcMVdokZQ901ARvl14wekgpbJIxvlKplgoh-LI8l7STFSqPVNb=s0 "image_tooltip")


## Trigger the build
Open the cloud shell <walkthrough-cloud-shell-icon></walkthrough-cloud-shell-icon>.
Select the trigger you created, copy the webhook URL and paste it in the following command. Specify the cluster name **<CLUSTER_NAME>**, region **<REGION>** and fleet config file path **<CONFIG_FILE>**.
```bash
curl -X POST -H "application/json" "<WEBHOOK_URL>" -d '{"cluster_name":"<CLUSTER_NAME>", "region":"<REGION>", "config_file":"fleet_configs_simple.yaml"}'
```
The “curl” command will trigger cloud build, it will apply the fleet configurations with specified file path in the GitLab repository.\
In this demo, we use the simple game server to test the Quilkin proxy, so “config_file” is **fleet_configs_simple.yaml**.

## Test the fleet config
Test the fleet creation:
1. Go to the Cloud Build, in Cloud Build history, check that it was successfully built.
<walkthrough-menu-navigation sectionId="CLOUD_BUILD_SECTION">Cloud Build</walkthrough-menu-navigation>
2. Open the cloud shell <walkthrough-cloud-shell-icon></walkthrough-cloud-shell-icon>, enter the following command. Verify that gameservers were created, and the state is ready.
```
Kubectl get gs
```

The output should look like this:
![alt_text](https://lh6.googleusercontent.com/S9xqnfKxvrsS4rxtHiBE-0DIht0huN0tJo0PfcFJErXkKlBLk-C98qrSQrHG84-xLKn8acuOg1M-OQYp2dD3-xkBBWRBOVL-GYONPnP0sfgK10zKhBUv5eaLjM-Xnya9qd2n7nR0HNNEEHbiRTfYQtt-kuhOlt3u1uk6KBUZLLFzSnW8=s0 "image_tooltip")

Take notes of the **ADDRESS** and **PORT** in the output of the above command. 

## Test the Quilkin proxy
Copy the [SimpleGameServerQuilkinTest.py](https://gitlab.endpoints.cn-gaming-cicd.cloud.goog/gaming-ci-cd-automation/core/-/blob/main/QuilkinTest/SimpleGameServerQuilkinTest.py), run the script using the following command:
```bash
python SimpleGameServerQuilkinTest.py <IP> <PORT>
```

The python script will send a UDP package of message “abcEXIT”, “abc” is the first 3 bytes of the message, and it matches the token, so it will be removed by the filter, and pass “EXIT” to the game server.\
The simple game server should echo the message back, and the message should be “EXIT”.

![alt_text](https://lh6.googleusercontent.com/nRr5fcxSAqIxuu3NFXZUM7SOu_3prci91C2WUTseGl9O0D5bux8vs7OOmefyym9BnqRFsfRfpOTWVrIfzdh6wT6Uof3xOMWz8hJDqEAqcey-WAxYoCGTsQUHcxrgSm3Nwj4tCnf5O4q2AVfIKxRQOkj9DIJifoA6BqsMQYtYmZ8SYQBr=s0 "image_tooltip")

After the game server “simple-game-server-7qsr8-tlmvh” receives an “EXIT” message, it will shut down the game server, and create a new one.\
You should see that a new game server “simple-game-server-7qsr8-gszqf” is created, the port is changed from “7040” to “7537”.

![alt_text](https://lh6.googleusercontent.com/4j-ItSw0pi-V-7loosXBChW4NMy6TjDOnIDYnSaiyYCVSYBZhnDKu-03ptKH7Mf4GcHiBI9gniTJXcOt-5kW82Z1AYWxric8AVmWezBF3opn99SrC9EXG7qYztiYk9HGG14bm8G9c_DJkL60-wVmr-o1w43vVQDi7O36QbR4LJkJzXN5=s0 "image_tooltip")



