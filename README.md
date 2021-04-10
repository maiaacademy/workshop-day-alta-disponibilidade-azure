# Workshop Day Alta Disponibilidade no Azure 

Mentoria Arquiteto Cloud - Hands-on Lab

## Workshop Day
## Lab #01 - Azure Load Balancer (30 minutes)

1. Sign in to the [Azure portal](http://portal.azure.com).

1. In the Azure portal, search for and select **Virtual machines** and, on the **Virtual machines** blade, click **+ Add**.

1. On the **Basics** tab of the **Create a virtual machine** blade, specify the following settings (leave others with their default values):

    | Setting | Value | 
    | --- | --- |
    | Subscription | the name of the Azure subscription you will be using in this lab |
    | Resource group | the name of a new resource group **RG-Network** |
    | Virtual machine name | **VMWEB01** and **VMWEB02** |
    | Region | select one of the regions that support availability zones and where you can provision Azure virtual machines | 
    | Availability options | **Availability zone** |
    | Availability zone | **1** and **2** |
    | Image | **Ubuntu Server 18.04 LTS - Gen1** |
    | Azure Spot instance | **No** |
    | Size | **Standard B1ms** |
    | Username | **admaz** |
    | Password | **Azur3Exp3rt*** |
    | Public inbound ports | **SSH (22)** |
    | Would you like to use an existing Windows Server license? | **No** |

1. Click **Next: Disks >** and, on the **Disks** tab of the **Create a virtual machine** blade, specify the following settings (leave others with their default values):

    | Setting | Value | 
    | --- | --- |
    | OS disk type | **Standard SSD** |
    | Enable Ultra Disk compatibility | **No** |

1. Click **OK** and, back on the **Networking** tab of the **Create a virtual machine** blade, specify the following settings (leave others with their default values):

    | Setting | Value | 
    | --- | --- |
    | Virtual Network | **VNET-Hub** |
    | Subnet | **Default** |
    | Public IP | **VMWEB01-PI** |
    | NIC network security group | **None** |
    | Accelerated networking | **Off** |
    | Place this virtual machine behind an existing load balancing solution? | **No** |

1. Click **Next: Management >** and, on the **Management** tab of the **Create a virtual machine** blade, specify the following settings (leave others with their default values):

    | Setting | Value | 
    | --- | --- |
    | Boot diagnostics | **Enable with custom storage account** |
    | Diagnostics storage account | create new |
    | Properties storage account | Name: **sadiag####**, Account kind: StorageV2, Performance: Standard, Replication: Locally-redundant-storage (LRS) |
    | Enable auto-shutdown | off |   

1. Click **Next: Advanced >**, on the **Advanced** tab of the **Custom data and cloud init** blade, add script for install NGINX Web Server.

1. Copy and paste script, on the Create cloud-init config, [click here](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/tutorial-load-balancer#create-virtual-machines)

1. Click **Review + Create**.

1. On the **Review + Create** blade, click **Create**.

1. Repeat a new Virtual Machine **VMWEB02**

1. Check two Virtual machines create successful.

1. Connect the Virtual machines.

**Note**
Test open Browser to IP Address the Virtual machines.

1. In the Azure portal, search and select **Load balancers** and, on the **Load balancers** blade, click **+ Add**.

1. Create a load balancer with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Subscription | the name of the Azure subscription you are using in this lab |
    | Resource group | **RG-Network** |
    | Name | **ALBWEB** |
    | Region| name of the Azure region into which you deployed all other resources in this lab |
    | Type | **Public** |
    | SKU | **Standard** |
    | Public IP address | **Create new** |
    | Public IP address name | **ALBWEB-PI** |
    | Availability zone | **Zone-redundant** |
    | Add a public IPv6 address | **No** |

    > **Note**: Wait for the Azure load balancer to be provisioned. This should take about 2 minutes. 

1. On the deployment blade, click **Go to resource**.

1. On the **ALBWEB** load balancer blade, click **Backend pools** and click **+ Add**.

1. Add a backend pool with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Name | **BP-WEB** |
    | Virtual network | **VNET-Hub** |
    | IP version | **IPv4** |
    | Virtual machine | **VMWEB01** | 
    | Virtual machine IP address | associate IP address |
    | Virtual machine | **VMWEB02** |
    | Virtual machine IP address | associate IP address |

1. Wait for the backend pool to be created, click **Health probes**, and then click **+ Add**.

1. Add a health probe with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Name | **HP-WEB** |
    | Protocol | **TCP** |
    | Port | **80** |
    | Interval | **5** |
    | Unhealthy threshold | **2** |

1. Wait for the health probe to be created, click **Load balancing rules**, and then click **+ Add**.

1. Add a load balancing rule with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Name | **LBR-WEB** |
    | IP Version | **IPv4** |
    | Protocol | **TCP** |
    | Port | **80** |
    | Backend port | **80** |
    | Backend pool | **BP-WEB** |
    | Health probe | **HP-WEB** |
    | Session persistence | **None** |
    | Idle timeout (minutes) | **4** |
    | TCP reset | **Disabled** |
    | Floating IP (direct server return) | **Disabled** |
    | Use outbound rules to provide backend pool members access to the internet. | **Enabled** |

1. Wait for the load balancing rule to be created, click **Overview**, and note the value of the **Public IP address**.

1. In the Azure portal, search for and select **Network security groups**, and, on the **Network security groups** blade, click **+ Add**.

1. Create a network security group with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Subscription | the name of the Azure subscription you are using in this lab |
    | Resource Group | **RG-Network** |
    | Name | **NSG-ALB-WEB** |
    | Region | the name of the Azure region where you deployed all other resources in this lab |

1. On the deployment blade, click **Go to resource** to open the **NSG-ALB-WEB** network security group blade. 

1. On the **NSG-ALB-WEB** network security group blade, in the **Settings** section, click **Inbound security rules**. 

1. Add an inbound rule with the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | Source | **Any** |
    | Source port ranges | * |
    | Destination | **Any** |
    | Destination port ranges | **80** |
    | Protocol | **TCP** |
    | Action | **Allow** |
    | Priority | **100** |
    | Name | **Allow-Port_80** |

1. On the **NSG-ALB-WEB** network security group blade, in the **Settings** section, click **Network interfaces** and then click **+ Associate**.

1. Associate the **NSG-ALB-WEB** network security group with the Network interfaces **VMWEB01 and VMWEB02**.

    >**Note**: It may take up to 5 minutes for the rules from the newly created Network Security Group to be applied to the Network Interface Card.

1. Go to the Azure portal to view your **Network security groups**. Search for and select Network security groups.

1. Select the name of your Network security group.

1. Start browser window and navigate to the IP address you identified in the previous step.

1. Verify that the browser window displays the message **Static page and servername**.

1. In Virtual machines, select **VMWEB01 or VMWEB02** and click **Stop**. 

1. Open another browser window but this time by using InPrivate mode and verify whether the target vm changes (as indicated by the message).

## Lab #02 - Azure Application Gateway

1. On the Azure portal menu or from the **Home** page, select **Create a resource**. The **New** window appears.

2. Select **Networking** and then select **Application Gateway** in the **Featured** list.

1. On the **Basics** tab, enter these values for the following application gateway settings:

   - **Resource group**: Select **RG-Network** for the resource group. If it doesn't exist, select **Create new** to create it.
   - **Application gateway name**: Enter **AAGWEB** for the name of the application gateway.
   - **Tier**: Select **Standard v2***.
   - **Enable autoscaling**: Select **No**.
   - **Instance count**: Select **Two**.
   - **Availabity zone**: Select **Zone 1, Zone 2, Zone 3**.

 2. Under **Configure virtual network**, select **VNET-Hub**, enter the following values to create the subnet:

    - **Subnet name**: Change the name of this subnet to **ApplicationGateway**. The application gateway subnet can contain only application gateways. No other resources are allowed.

    - **Address range** (###.###.###.#/24): In the second row of the **Subnets** Grid, enter an address range that doesn't overlap with the address range of subnet.

    Select **OK** to close the **Virtual network** window and save the virtual network settings.
    
3. On the **Basics** tab, accept the default values for the other settings and then select **Next: Frontends**.

1. On the **Frontends** tab, verify **Frontend IP address type** is set to **Public**.

2. Select **Add new** for the **Public IP address** and enter **AAGWEB-PI** for the public IP address name, and then select **OK**. 

3. Select **Next: Backends**.

1. On the **Backends** tab, select **Add a backend pool**.

2. In the **Add a backend pool** window that opens, enter the following values to create an empty backend pool:

    - **Name**: Enter **BP-WEB** for the name of the backend pool.
    - **Add backend pool without targets**: Select **Yes** to create a backend pool with no targets. You'll add backend targets after creating the application gateway.

3. In the **Add a backend pool** window, select **Add** to save the backend pool configuration and return to the **Backends** tab.

4. On the **Backends** tab, select **Next: Configuration**.

1. On the **Configuration** tab, you'll connect the frontend and backend pool you created using a routing rule.

1. Select **Add a routing rule** in the **Routing rules** column.

2. In the **Add a routing rule** window that opens, enter **RR-WEB** for the **Rule name**.

3. A routing rule requires a listener. On the **Listener** tab within the **Add a routing rule** window, enter the following values for the listener:

    - **Listener name**: Enter **L-WEB** for the name of the listener.
    - **Frontend IP**: Select **Public** to choose the public IP you created for the frontend.
  
      Accept the default values for the other settings on the **Listener** tab, then select the **Backend targets** tab to configure the rest of the routing rule.

4. On the **Backend targets** tab, select **BP-WEB** for the **Backend target**.

5. For the **HTTP setting**, select **Add new** to add a new HTTP setting. The HTTP setting will determine the behavior of the routing rule. In the **Add an HTTP setting** window that opens, enter **HTTPS-WEB** for the **HTTP setting name** and *80* for the **Backend port**. Accept the default values for the other settings in the **Add an HTTP setting** window, then select **Add** to return to the **Add a routing rule** window. 

6. On the **Add a routing rule** window, select **Add** to save the routing rule and return to the **Configuration** tab.

7. Select **Next: Tags** and then **Next: Review + create**.

1. Disassociate VMs on the Load Balancer Standard.

To do this, you'll:

1. On the Azure portal menu, select **All resources** or search for and select **ALBWEB**.

1. Disassociate VMs, **VMWEB01** and **VMWEB02** on the Load Balancer, in the Backend pool.

Add Backend targets:

1. On the Azure portal menu, select **All resources** or search for and select *All resources*. Then select **AAGWEB**.

2. Select **Backend pools** from the left menu.

3. Select **BP-WEB**.

4. Under **Backend targets**, **Target type**, select **Virtual machine** from the drop-down list.

5. Under **Target**, select the **VMWEB01** and **VMWEB02** virtual machines and their associated network interfaces from the drop-down lists.

6. Select **Save**.

7. Wait for the deployment to complete before proceeding to the next step.

1. Test the application gateway

2. Find the public IP address for the Application gateway on its **Overview** page. 
2. Copy the public IP address, and then paste it into the address bar of your browser to browse that IP address.
3. Check the response. A valid response verifies that the application gateway was successfully created and can successfully connect with the backend.

   Refresh the browser multiple times and you should see connections to both **VMWEB01** and **VMWEB02**.

## Lab #03 - Azure Virtual Machine Scale Sets (45 minutes)

1. Log in to the **Azure portal** at https://portal.azure.com.

1. Type **Scale set** in the search box. In the results, under **Marketplace**, select **Virtual machine scale sets**. Select **Create** on the **Virtual machine scale sets** page, which will open the **Create a virtual machine scale set** page.

1. In the **Basics** tab, under **Project details**, make sure the correct subscription is selected and then choose to **Create new** resource group. Type **RG-VMSS** for the name and then select **OK**.

1. Type **VMSSWEB** as the name for your scale set.

1. In **Region**, select a region that is close to your area.

1. Leave the default value of **Uniform** for **Orchestration mode**.

1. Select a marketplace image **Windows Server 2019 Datacenter - Gen1** for **Image**.

1. Enter your desired username, and password.

1. Select **Next** to move the the other pages. 

1. Leave the defaults for the **Instance** and **Disks** pages.

1. On the **Networking** page, under **Load balancing**, select **Yes** to put the scale set instances behind a load balancer. 

1. In **Load balancing options**, select **Azure load balancer**.

1. In **Select a load balancer**, select **ALBWEB** that you created earlier.

1. For **Select a backend pool**, select **Create new**, type **BP-VMSS-WEB*, then select **Create**.

1. On the **Scaling** page, under **Instance**, select **2** and **Scaling policy**, select **Manual**. 

1. On the **Management** page, under **Monitoring**, select **Enable with custom storage account**.

1. When you are done, select **Review + create**. 

1. After it passes validation, select **Create** to deploy the scale set.

1. In **Virtual Machine Scale Sets**, select **Instances** and connect on Virtual machines.

1. Install the Web-Server feature in the all Virtual machines by running the following command in the **Administrator Windows PowerShell** command prompt. You can copy and paste this command.

   ```powershell
   Install-WindowsFeature -name Web-Server -IncludeManagementTools
   ```
1. Back in the portal, navigate back to the Overview blade of **Virtual Machine Scale Sets** and, use the Click to clipboard button to copy the public IP address.

1. In a **Browser**, navigate to the Public IP Address the VMSS.

1. Sign in to the [**Azure portal**](http://portal.azure.com) and open Azure Cloud Shell.

1. In Cloud Shell, start the code editor and create a file named **VMSS-Install-WebServer.ps1**.

1. Add the following text to the file:

  ```powershell
   # Set Script 
$customConfig = @{ 
  "fileUris" = (,"https://raw.githubusercontent.com///VMSS-Install-IIS_v1.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File VMSS-Install-IIS_v1.ps1" 
} 

    # Set VMSS variables
$rgname = "rgname"
$vmssname = "vmssname"
 
    # Get VMSS object 
$vmss = Get-AzVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
 
    # Add VMSS extension 
$vmss = Add-AzVmssExtension -Name "CustomScript" -VirtualMachineScaleSet $vmss -Publisher "Microsoft.Compute" -Type "CustomScriptExtension" -TypeHandlerVersion "1.9" -Setting $customConfig

    # Update VMSS 
Update-AzVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss
   ```

1. Press Ctrl+S to save the file. Then press Ctrl+Q to close the code editor.

1. Run the following command **./VMSS-Install-WebServer.ps1**

## Lab #03 - Azure App Service (30 minutes)

1. Sign in to the [**Azure portal**](http://portal.azure.com).

1. In the Azure portal, search for and select **App services**, and, on the **App Services** blade, click **+ Add**.

1. On the **Basics** tab of the **Web App** blade, specify the following settings (leave others with their default values):

    | Setting | Value |
    | --- | ---|
    | Subscription | the name of the Azure subscription you are using in this lab |
    | Resource group | the name of a new resource group **RGNAME** |
    | Web app name | any globally unique name |
    | Publish | **Code** |
    | Runtime stack | **PHP 7.3** |
    | Operating system | **Windows** |
    | Region | the name of an Azure region where you can provision Azure web apps |
    | App service plan | accept the default configuration |

1. Click **Next : Monitoring >**, on the **Monitoring** tab of the **Web App** blade, set the **Enable Application Insights** switch to **No**, click **Review + create**, and then click **Create**. 

    >**Note**: Typically, you would want to enable **Application Insights**, however, its functionality is not used in this lab.

    >**Note**: Wait until the web app is created before you proceed to the next task. This should take about a minute. 

1. On the deployment blade, click **Go to resource**.

1. On the blade of the newly deployed web app, click the **URL** link to display the default web page in a new browser tab.

1. Close the new browser tab and, back in the Azure portal, in the **Deployment** section of the web app blade, click **Deployment slots**. 

    >**Note**: The web app, at this point, has a single deployment slot labeled **PRODUCTION**. 

1. Click **+ Add slot**, and add a new slot with the following settings: 

    | Setting | Value |
    | --- | ---|
    | Name | **staging** |
    | Clone settings from | **Do not clone settings**|

1. Back on the **Deployment slots** blade of the web app, click the entry representing the newly created staging slot. 

    >**Note**: This will open the blade displaying the properties of the staging slot. 

1. Review the staging slot blade and note that its URL differs from the one assigned to the production slot.

1. On the staging deployment slot blade, in the **Deployment** section, click **Deployment Center**.

    >**Note:** Make sure you are on the staging slot blade (rather than the production slot).

1. In the **Continuous Deployment (CI/CD)** section, select **Local Git**, and then click **Continue**.

1. Select **App Service build service**, click **Continue**, and then click **Finish**. 

1. Copy the resulting **Git Clone Url** to Notepad.

    >**Note:** You will need the Git Clone Url value in the next task of this lab.

1. Click **Deployment Credentials** toolbar icon to display **Deployment Credentials** pane. 

1. Click **User credentials**.

1. Complete the required information, and then click **Save Credentials**. 

    | Setting | Value |
    | --- | ---|
    | User name | any unique name (must not contain `@` character) |
    | Password | any password that satisfies complexity requirements |
    
    >**Note:** The password must be at least eight characters long, with two of the following three elements: letters, numbers, and non-alphanumeric characters.

    >**Note:** You will need these credentials in the next task of this lab.

1. In the Azure portal, open the **Azure Cloud Shell** by clicking on the icon in the top right of the Azure Portal.

1. If prompted to select either **Bash** or **PowerShell**, select **PowerShell**. 

    >**Note**: If this is the first time you are starting **Cloud Shell** and you are presented with the **You have no storage mounted** message, select the subscription you are using in this lab, and click **Create storage**. 

1. From the Cloud Shell pane, run the following to clone the remote repository containing the code for the web app.

   ```pwsh
   git clone https://github.com/Azure-Samples/php-docs-hello-world
   ```
 
1. From the Cloud Shell pane, run the following to set the current location to the newly created clone of the local repository containing the sample web app code.

   ```
   Set-Location -Path $HOME/php-docs-hello-world/
   ```

1. From the Cloud Shell pane, run the following to add the remote git (make sure to replace the `[deployment_user_name]` and `[git_clone_url]` placeholders with the value of the **Deployment Credentials** user name and **Git Clone Url**, respectively, which you identified in previous task):

   ```
   git remote add [deployment_user_name] [git_clone_url]
   ```

    >**Note**: The value following `git remote add` does not have to match the **Deployment Credentials** user name, but has to be unique

1. From the Cloud Shell pane, run the following to push the sample web app code from the local repository to the Azure web app staging deployment slot (make sure to replace the `[deployment_user_name]` placeholder with the value of the **Deployment Credentials** user name, which you identified in previous task):
   ```
   git push [deployment_user_name] master
   ```

1. If prompted to authenticate, type the `[deployment_user_name]` and the corresponding password (which you set in the previous task).

1. Close the Cloud Shell pane.

1. On the staging slot blade, click **Overview** and then click the **URL** link to display the default web page in a new browser tab.

1. Verify that the browser page displays the **Hello World!** message and close the new tab.

In this task, you will swap the staging slot with the production slot

1. Navigate back to the blade displaying the production slot of the web app.

1. In the **Deployment** section, click **Deployment slots** and then, click **Swap** toolbar icon.

1. On the **Swap** blade, review the default settings and click **Swap**. 

1. Click **Overview** on the production slot blade of the web app and then click the **URL** link to display the web site home page in a new browser tab.

1. Verify the default web page has been replaced with the **Hello World!** page. 

## Lab #04 - Azure Container Instances (30 minutes)

1. Sign in to the [Azure portal](https://portal.azure.com).

1. In the Azure portal, search for locate **Container instances** and then, on the **Container instances** blade, click **+ Add**. 

1. On the **Basics** tab of the **Create container instance** blade, specify the following settings (leave others with their default values):

    | Setting | Value |
    | ---- | ---- |
    | Subscription | the name of the Azure subscription you are using in this lab |
    | Resource group | the name of a new resource group **RGNAME** |
    | Container name | **aciname** |
    | Region | the name of a region where you can provision Azure container instances |
    | Image Source | **Quickstart images** |
    | Image | **microsoft/aci-helloworld (Linux)** |

1. Click **Next: Networking >** and, on the **Networking** tab of the **Create container instance** blade, specify the following settings (leave others with their default values):

    | Setting | Value |
    | --- | --- |
    | DNS name label | any valid, globally unique DNS host name |
	
    >**Note**: Your container will be publicly reachable at dns-name-label.region.azurecontainer.io. If you receive a **DNS name label not available** error message, specify a different value.

1. Click **Next: Advanced >**, review the settings on the **Advanced** tab of the **Create container instance** blade without making any changes, click **Review + Create**, and then click **Create**. 

    >**Note**: Wait for the deployment to complete. This should take about 3 minutes.

    >**Note**: While you wait, you may be interested in viewing the [code behind the sample application](https://github.com/Azure-Samples/aci-helloworld). To view it, browse the \app folder. 

1. On the deployment blade, click the **Go to resource** link.

1. On the **Overview** blade of the container instance, verify that **Status** is reported as **Running**. 

1. Copy the value of the container instance **FQDN**, open a new browser tab, and navigate to the corresponding URL.

1. Verify that the **Welcome to Azure Container Instance** page is displayed.

1. Close the new browser tab, back in the Azure portal, in the **Settings** section of the container instance blade, click **Containers**, and then click **Logs**. 

1. Verify that you see the log entries representing the HTTP GET request generated by displaying the application in the browser.

## Exercise #05 - Azure Kubernetes Service (60 minutes)

2. Kubernetes architecture.

   ![Screenshot of the Kubernetes archicture](/AllFiles/Images/IMG03.png)

#### Register the Microsoft Kubernetes resource providers.

1. Sign in to the [Azure portal](https://portal.azure.com).

1. In the Azure portal, open the **Azure Cloud Shell** by clicking on the icon in the top right of the Azure Portal.

1. If prompted to select either **Bash** or **PowerShell**, select **PowerShell**. 

    >**Note**: If this is the first time you are starting **Cloud Shell** and you are presented with the **You have no storage mounted** message, select the subscription you are using in this lab, and click **Create storage**. 

1. From the Cloud Shell pane, run the following to register the Microsoft.Kubernetes and Microsoft.KubernetesConfiguration resource providers.

   ```pwsh
   Register-AzResourceProvider -ProviderNamespace Microsoft.Kubernetes
   
   Register-AzResourceProvider -ProviderNamespace Microsoft.KubernetesConfiguration
   ```

1. Close the Cloud Shell pane.

#### Deploy an Azure Kubernetes Service cluster

1. In the Azure portal, search for locate **Kubernetes services** and then, on the **Kubernetes services** blade, click **+ Add**, and then click **+ Add Kubernetes cluster**. 

1. On the **Basics** tab of the **Create Kubernetes cluster** blade, specify the following settings (leave others with their default values):

    | Setting | Value |
    | ---- | ---- |
    | Subscription | the name of the Azure subscription you are using in this lab |
    | Resource group | the name of a new resource group **RGNAME** |
    | Kubernetes cluster name | **aksclname** |
    | Region | the name of a region where you can provision a Kubernetes cluster |
    | Kubernetes version | accept the default |
    | Node size | accept the default |
    | Node count | **1** |

1. Click **Next: Node Pools >** and, on the **Node Pools** tab of the **Create Kubernetes cluster** blade, specify the following settings (leave others with their default values):

    | Setting | Value |
    | ---- | ---- |
    | Virtual nodes | **Disabled** |
    | VM scale sets | **Enabled** |
	
1. Click **Next: Authentication >** and, on the **Authentication** tab of the **Create Kubernetes cluster** blade, specify the following settings (leave others with their default values):

    | Setting | Value |
    | ---- | ---- |
    | Service principal | accept the default |
    | Enable RBAC | **Yes** |

1. Click **Next: Networking >** and, on the **Networking** tab of the **Create Kubernetes cluster** blade, specify the following settings (leave others with their default values):

    | Setting | Value |
    | ---- | ---- |
    | Network configuration | **kubenet** |
    | DNS name prefix | any valid, globally unique DNS host name |

1. Click **Next: Integration >**, on the **Integration** tab of the **Create Kubernetes cluster** blade, set **Container monitoring** to **Disabled**, click **Review + create** and then click **Create**. 

    >**Note**: In production scenarios, you would want to enable monitoring. Monitoring is disabled in this case since it is not covered in the lab. 

    >**Note**: Wait for the deployment to complete. This should take about 10 minutes.

#### Deploy pods into the Azure Kubernetes Service cluster

1. On the deployment blade, click the **Go to resource** link.

1. On the **aksclname** Kubernetes service blade, in the **Settings** section, click **Node pools**.

1. On the **aksclname - Node pools** blade, verify that the cluster consists of a single pool with one node.

1. In the Azure portal, open the **Azure Cloud Shell** by clicking on the icon in the top right of the Azure Portal.

1. Switch the **Azure Cloud Shell** to **Bash** (black background).

1. From the Cloud Shell pane, run the following to retrieve the credentials to access the AKS cluster:

    ```sh
    RESOURCE_GROUP='rgname'

    AKS_CLUSTER='aksclnamev'

    az aks get-credentials --resource-group $rgname--name $aksclname
    ``` 

1. From the **Cloud Shell** pane, run the following to verify connectivity to the AKS cluster:

    ```sh
    kubectl get nodes
    ```

1. In the **Cloud Shell** pane, review the output and verify that the one node which the cluster consists of at this point is reporting the **Ready** status. 

1. From the **Cloud Shell** pane, run the following to deploy the **nginx** image from the Docker Hub:

    ```sh
    kubectl create deployment nginx-deployment --image=nginx
    ```

    > **Note**: Make sure to use lower case letters when typing the name of the deployment (nginx-deployment)

1. From the **Cloud Shell** pane, run the following to verify that a Kubernetes pod has been created:

    ```sh
    kubectl get pods
    ```

1. From the **Cloud Shell** pane, run the following to identify the state of the deployment:

    ```sh
    kubectl get deployment
    ```

1. From the **Cloud Shell** pane, run the following to make the pod available from Internet:

    ```sh
    kubectl expose deployment nginx-deployment --port=80 --type=LoadBalancer
    ```

1. From the **Cloud Shell** pane, run the following to identify whether a public IP address has been provisioned:

    ```sh
    kubectl get service
    ```

1. Re-run the command until the value in the **EXTERNAL-IP** column for the **nginx-deployment** entry changes from **\<pending\>** to a public IP address. Note the public IP address in the **EXTERNAL-IP** column for **nginx-deployment**.

1. Open a browser window and navigate to the IP address you obtained in the previous step. Verify that the browser page displays the **Welcome to nginx!** message.

#### Scale containerized workloads in the Azure Kubernetes service cluster

1. From the **Cloud Shell** pane, run the following to scale the deployment by increasing of the number of pods to 2:

    ```sh
    kubectl scale --replicas=2 deployment/nginx-deployment
    ```

1. From the **Cloud Shell** pane, run the following to verify the outcome of scaling the deployment:

    ```sh
    kubectl get pods
    ```

    > **Note**: Review the output of the command and verify that the number of pods increased to 2.

1. From the **Cloud Shell** pane, run the following to scale out the cluster by increasing the number of nodes to 2:

    ```sh
    az aks scale --resource-group $rgname --name $aksclname --node-count 2
    ```

    > **Note**: Wait for the provisioning of the additional node to complete. This might take about 3 minutes. If it fails, rerun the `az aks scale` command.

1. From the **Cloud Shell** pane, run the following to verify the outcome of scaling the cluster:

    ```sh
    kubectl get nodes
    ```

    > **Note**: Review the output of the command and verify that the number of nodes increased to 2.

1. From the **Cloud Shell** pane, run the following to scale the deployment:

    ```
    kubectl scale --replicas=10 deployment/nginx-deployment
    ```

1. From the **Cloud Shell** pane, run the following to verify the outcome of scaling the deployment:

    ```
    kubectl get pods
    ```

    > **Note**: Review the output of the command and verify that the number of pods increased to 10.

1. From the **Cloud Shell** pane, run the following to review the pods distribution across cluster nodes:

    ```
    kubectl get pod -o=custom-columns=NODE:.spec.nodeName,POD:.metadata.name
    ```

    > **Note**: Review the output of the command and verify that the pods are distributed across both nodes.

1. From the **Cloud Shell** pane, run the following to delete the deployment:

    ```
    kubectl delete deployment nginx-deployment
    ```
1. Close the **Cloud Shell** pane.

## Lab #06 - Azure Resource Manager (30 minutes)

1. Sign in to the [**Azure portal**](https://portal.azure.com).

1. In the Azure portal, search for and select **Resource groups**. 

1. In the list of resource groups, click **RGNAME**.

1. On the **RGNAME** resource group blade, in the **Settings** section, click **Deployments**.

1. On the **RGNAME - Deployments** blade, click the first entry in the list of deployments.

1. On the **Microsoft.ManagedDisk-*XXXXXXXXX* \| Overview** blade, click **Template**.

    >**Note**: Review the content of the template and note that you have the option to **Download** it to the local computer, **Add to library**, or **Deploy** it again.

1. Click **Download** and save the compressed file containing the template and parameters files to the **Downloads** folder on your lab computer.

1. Extract the content of the downloaded file into the **Downloads** folder on your lab computer.
 
1. Close all **File Explorer** windows.

1. In the Azure portal, search for and select **Deploy a custom template**.

1. On the **Custom deployment** blade, click **Build your own template in the editor**.

1. On the **Edit template** blade, click **Load file** and upload the **template.json** file you downloaded in the previous task.

1. Within the editor pane, remove the following lines:

   ```json
   "sourceResourceId": {
       "type": "String"
   },
   "sourceUri": {
       "type": "String"
   },
   "osType": {
       "type": "String"
   },
   ```

   ```json
   "hyperVGeneration": {
       "defaultValue": "V1",
       "type": "String"
   },      
   ```

   ```json
   "osType": "[parameters('osType')]",
   ```

    >**Note**: These parameters are removed since they are not applicable to the current deployment. In particular, sourceResourceId, sourceUri, osType, and hyperVGeneration parameters are applicable to creating an Azure disk from an existing VHD file.

1. **Save** the changes.

1. Back on the **Custom deployment** blade, click **Edit parameters**. 

1. On the **Edit parameters** blade, click **Load file** and upload the **parameters.json** file you downloaded in the previous task, and **Save** the changes.

1. Back on the **Custom deployment** blade, specify the following settings:

    | Setting | Value |
    | --- |--- |
    | Subscription | *the name of the Azure subscription you are using in this lab* |
    | Resource Group | the name of a **new** resource group **RGNAME** |
    | Region | the name of any Azure region available in the subscription you are using in this lab |
    | Disk Name | **VMNAME-Disk01** |
    | Location | *accept the default value* |
    | Sku | **Standard_LRS** |
    | Disk Size Gb | **32** |
    | Create Option | **empty** |
    | Disk Encryption Set Type | **EncryptionAtRestWithPlatformKey** |
    | Network Access Policy | **AllowAll** |

1. Select **Review + Create** and then select **Create**.

1. Verify that the deployment completed successfully.

1. In the Azure portal, search for and select **Resource groups**. 

1. In the list of resource groups, click **RGNAME**.

1. On the **RGNAME** resource group blade, in the **Settings** section, click **Deployments**.

1. From the **RGNAME - Deployments** blade, click the first entry in the list of deployments and review the content of the **Input** and **Template** blades.

1. End of day 2.

## Day 3

## Lab #01 - Azure Backup (30 minutes)

1. In the Azure portal, search for and select **Recovery Services vaults** and, on the **Recovery Services vaults** blade, click **+ Add**.

1. On the **Create Recovery Services vault** blade, specify the following settings:

    | Settings | Value |
    | --- | --- |
    | Subscription | the name of the Azure subscription you are using in this lab |
    | Resource group | the name of a new resource group **RGNAME** |
    | Name | **RSVNAME** |
    | Region | the name of a region where you deployed the two virtual machines in the previous task |

    >**Note**: Make sure that you specify the same region into which you deployed virtual machines.

1. Click **Review + Create** and then click **Create**.

    >**Note**: Wait for the deployment to complete. The deployment should take less than 1 minute.

1. When the deployment is completed, click **Go to Resource**. 

1. On the **RSVNAME** Recovery Services vault blade, in the **Settings** section, click **Properties**.

1. On the **RSVNAME - Properties** blade, click the **Update** link under **Backup Configuration** label.

1. On the **Backup Configuration** blade, note that you can set the **Storage replication type** to either **Locally-redundant** or **Geo-redundant**. Leave the default setting of **Geo-redundant** in place and close the blade.

    >**Note**: This setting can be configured only if there are no existing backup items.

1. Back on the **RSVNAME - Properties** blade, click the **Update** link under **Security Settings** label. 

1. On the **Security Settings** blade, note that **Soft Delete (For Azure Virtual Machines)** is **Enabled**.

1. Close the **Security Settings** blade and, back on the **RSVNAME** Recovery Services vault blade, click **Overview**.

1. On the **RSVNAME** Recovery Services vault blade, click **+ Backup**.

1. On the **Backup Goal** blade, specify the folowing settings:

    | Settings | Value |
    | --- | --- |
    | Where is your workload running? | **Azure** |
    | What do you want to backup? | **Virtual machine** |

1. On the **Backup Goal** blade, click **Backup**.

1. On the **Backup policy**, review the **DefaultPolicy** settings and select **Create a new policy**.

1. Define a new backup policy with the following settings (leave others with their default values):

    | Setting | Value |
    | ---- | ---- |
    | Policy name | **BP-VMS** |
    | Frequency | **Daily** |
    | Time | **22:00 PM** |
    | Timezone | the name of your local time zone |
    | Retain instant recovery snapshot(s) for | **2** Days(s) |

1. Click **OK** to create the policy and then, in the **Virtual Machines** section, select **Add**.

1. On the **Select virtual machines** blade, select **VMNAME**, click **OK**, and, back on the **Backup** blade, click **Enable backup**.

    >**Note**: Wait for the backup to be enabled. This should take about 2 minutes. 

1. Navigate back to the **RSVNAME** Recovery Services vault blade, in the **Protected items** section, click **Backup items**, and then click the **Azure virtual machines** entry.

1. On the **Backup Items (Azure Virtual Machine)** blade of **VMNAME**, review the values of the **Backup Pre-Check** and **Last Backup Status** entries, and click the **VMNAME** entry.

1. On the **VMNAME** Backup Item blade, click **Backup now**, accept the default value in the **Retain Backup Till** drop-down list, and click **OK**.

 >**Note**: Do not wait for the backup to complete but instead proceed.

## Project #03 - Azure Site Recovery (45 minutes)

1. In a browser, navigate to the [Disaster Recovery with Azure Site Recovery](https://docs.microsoft.com/en-us/learn/modules/protect-infrastructure-with-site-recovery/) webpage and start lab.

2. Disaster Recovery architecture.

   ![Screenshot of the application archicture](/AllFiles/Images/IMG04.png)

## Lab #02 - Azure AD (15 minutes)

1. In the Azure portal, search for and select **Azure Active Directory**.

1. Click **+ Create a tenant** and specify the following setting:

    | Setting | Value |
    | --- | --- |
    | Directory type | **Azure Active Directory** |
    | Organization name | **Azure Expert** |
    | Initial domain name | any valid DNS name consisting of lower case letters and digits and starting with a letter | 
    | Country/Region | **United States** |

   > **Note**: The green check mark in the **Initial domain name** text box will indicate that the domain name you typed in is valid and unique.

1. Click **Review + create** and then click **Create**.

1. Display the blade of the newly created Azure AD tenant by using the **Click here to navigate to your new directory: Contoso Lab** link or the **Directory + Subscription** button (directly to the right of the Cloud Shell button) in the Azure portal toolbar.

 Navigate back to the **Users - All users** blade, and then click **+ New user**.

1. Create a new user with the following settings (leave others with their defaults):

    | Setting | Value |
    | --- | --- |
    | User name | **AzureExpert** |
    | Name | **Azure Expert** |
    | Let me create the password | enabled |
    | Initial password | **Pa55w.rd124** |
    | Usage location | **United States** |
    | Job title | **Azure Expert** |
    | Department | **IT** |

    >**Note**: **Copy to clipboard** the full **User Principal Name** (user name plus domain). You will need it later in this task.

1. In the list of users, click the newly created user account to display its blade.

1. Review the options available in the **Manage** section and note that you can identify the Azure AD roles assigned to the user account as well as the user account's permissions to Azure resources.

1. In the **Manage** section, click **Assigned roles**, then click **+ Add assignment** button and assign the **User administrator** role to user.

    >**Note**: You also have the option of assigning Azure AD roles when provisioning a new user.

1. Open an **InPrivate** browser window and sign in to the [Azure portal](https://portal.azure.com) using the newly created user account. When prompted to update the password, change the password for the user.

    >**Note**: Rather than typing the user name (including the domain name), you can paste the content of Clipboard.

1. In the **InPrivate** browser window, in the Azure portal, search for and select **Azure Active Directory**.

1. In the **InPrivate** browser window, on the Azure AD blade, scroll down to the **Manage** section, click **User settings**, and note that you do not have permissions to modify any configuration options.

1. In the Azure portal, navigate back to the Azure AD tenant blade and click **Groups**.

1. Use the **+ New group** button to create a new group with the following settings:

    | Setting | Value |
    | --- | --- |
    | Group type | **Security** |
    | Group name | **GS-AzureExpert** |
    | Group description | **Azure Expert Team** |
    | Membership type | **Assigned** |

1. Click **No members selected**.

1. From the **Add members** blade, search and select the **AzureExpert** appears in the list of user members.

1. Enable MFA to users [Manual Leires](https://1drv.ms/p/s!AqofFXIc7oj5gqZUTUsIuxogr96vRQ)

## Lab #03 - Azure RBAC (20 minutes)

1. In the Azure portal, search for and select **Azure Active Directory**, on the Azure Active Directory blade, click **Users**, and then click **+ New user**.

1. Create a new user with the following settings (leave others with their defaults):

    | Setting | Value |
    | --- | --- |
    | User name | **azure-support**|
    | Name | **azure-support**|
    | Let me create the password | enabled |
    | Initial password | **Pa55w.rd124** |

    >**Note**: **Copy to clipboard** the full **User name**. You will need it later in this lab.

1. In the Azure portal, navigate back to the **Subscritions** and display its **details**.

1. Click **Access control (IAM)**, click **+ Add** followed by **Role assignment**, and assign the **Support Request Contributor (Custom)** role to the newly created user account.

1. Open an **InPrivate** browser window and sign in to the [Azure portal](https://portal.azure.com) using the newly created user account. When prompted to update the password, change the password for the user.

    >**Note**: Rather than typing the user name, you can paste the content of Clipboard.

1. In the **InPrivate** browser window, in the Azure portal, search and select **Resource groups** to verify that the user can see all resource groups.

1. In the **InPrivate** browser window, in the Azure portal, search and select **All resources** to verify that the user cannot see any resources.

1. In the **InPrivate** browser window, in the Azure portal, search and select **Help + support** and then click **+ New support request**. 

1. In the **InPrivate** browser window, on the **Basic** tab of the **Help + support - New support request** blade, select the **Service and subscription limits (quotas)** issue type and note that the subscription you are using in this lab is listed in the **Subscription** drop-down list.

    >**Note**: The presence of the subscription you are using in this lab in the **Subscription** drop-down list indicates that the account you are using has the permissions required to create the subscription-specific support request.

    >**Note**: If you do not see the **Service and subscription limits (quotas)** option, sign out from the Azure portal and sign in back.

1. Do not continue with creating the support request. Instead, sign out as the user from the Azure portal and close the InPrivate browser window.

## Lab #04 - Azure Policy (30 minutes)

1. In the Azure portal, search for and select **Policy**. 

1. In the **Authoring** section, click **Definitions**. Take a moment to browse through the list of built-in policy definitions that are available for you to use. List all built-in policies that involve the use of tags by selecting the **Tags** entry (and de-selecting all other entries) in the **Category** drop-down list. 

1. Click the entry representing the **Require a tag and its value on resources** built-in policy and review its definition.

1. On the **Require a tag and its value on resources** built-in policy definition blade, click **Assign**.

1. Specify the **Scope** by clicking the ellipsis button and selecting the following values:

    | Setting | Value |
    | --- | --- |
    | Subscription | the name of the Azure subscription you are using in this lab |
    | Resource Group | the name of the resource group |

    >**Note**: A scope determines the resources or resource groups where the policy assignment takes effect. You could assign policies on the management group, subscription, or resource group level. You also have the option of specifying exclusions, such as individual subscriptions, resource groups, or resources (depending on the assignment scope). 

1. Configure the **Basics** properties of the assignment by specifying the following settings (leave others with their defaults):

    | Setting | Value |
    | --- | --- |
    | Assignment name | **Require Role tag with Azure Expert value**|
    | Description | **Require Role tag with Azure Expert value for all resources**|
    | Policy enforcement | Enabled |

    >**Note**: The **Assignment name** is automatically populated with the policy name you selected, but you can change it. You can also add an optional **Description**. **Assigned by** is automatically populated based on the user name creating the assignment. 

1. Click **Next** and set **Parameters** to the following values:

    | Setting | Value |
    | --- | --- |
    | Tag Name | **Role** |
    | Tag Value | **AzureExpert** |

1. Click **Next** and review the **Remediation** tab. Leave the **Create a Managed Identity** checkbox unchecked. 

    >**Note**: This setting can be used when the policy or initiative includes the **deployIfNotExists** or **Modify** effect.

1. Click **Review + Create** and then click **Create**.

    >**Note**: Now you will verify that the new policy assignment is in effect by attempting to create another Azure Storage account in the resource group without explicitly adding the required tag. 
    
    >**Note**: It might take between 5 and 15 minutes for the policy to take effect.

1. On the resource group blade, click **+ Add**.

1. On the **New** blade, search for and select **Storage account**, and click **Create**. 

1. On the **Basics** tab of the **Create storage account** blade, specify the following settings (leave others with their defaults) and click **Review + create**:

    | Setting | Value |
    | --- | --- |
    | Storage account name | any globally unique combination of between 3 and 24 lower case letters and digits, starting with a letter |

1. Note that the validation failed. Click the link **Validation failed. Click here to view details** to display the **Errors** blade and identify the reason for the failure. 

    >**Note**: The error message states that the resource deployment was disallowed by the policy. 

    >**Note**: By clicking the **Raw Error** tab, you can find more details about the error, including the name of the role definition **Require Role tag with Infra value**. The deployment failed because the storage account you attempted to create did not have a tag named **Role** with its value set to **AzureExpert**.

## Lab #05 - Azure Monitor (20 minutes)

1. From the Cloud Shell pane, run the following to register the Microsoft.Insights resource providers.

   ```pwsh
   Register-AzResourceProvider -ProviderNamespace Microsoft.Insights
    ```

1. In the Azure portal, search for and select **Log Analytics workspaces** and, on the **Log Analytics workspaces** blade, click **+ Add**.

1. On the **Basics** tab of the **Create Log Analytics workspace** blade, the following settings, click **Review + Create** and then click **Create**:

    | Settings | Value |
    | --- | --- |
    | Subscription | the name of the Azure subscription you are using in this lab |
    | Resource group | the name of a new resource group **RG-LA** |
    | Log Analytics Workspace | any unique name |    
    | Region | the name of the Azure region into which you deployed the virtual machine in the previous task |

    >**Note**: Wait for the deployment to complete. The deployment should take about 1 minute.

1. In the Azure portal, search for and select **Virtual machines**, and on the **Virtual machines** blade, click **VMANME**.

1. On the **VMANME** blade, in the **Monitoring** section, click **Metrics**.

1. On the **VMNAME | Metrics** blade, on the default chart, note that the only available **Metrics Namespace** is **Virtual Machine Host**.

    >**Note**: This is expected, since no guest-level diagnostic settings have been configured yet. You do have, however, the option of enabling guest memory metrics directly from the **Metrics Namespace** drop down-list. You will enable it later in this exercise.

1. In the **Metric** drop-down list, review the list of available metrics.

    >**Note**: The list includes a range of CPU, disk, and network-related metrics that can be collected from the virtual machine host, without having access into guest-level metrics.

1. In the **Metric** drop-down list, select **Percentage CPU**, in the **Aggregation** drop-down list, select **Avg**, and review the resulting chart. 

1. On the **VMNAME** blade, in the **Monitoring** section, click **Diagnostic settings**.

1. On the **Overview** tab of the **VMNAME | Diagnostic settings** blade, click **Enable guest-level monitoring**.

    >**Note**: Wait for the operation to take effect. This might take about 3 minutes.

1. Switch to the **Performance counters** tab of the **VMNAME | Diagnostic settings** blade and review the available counters.

    >**Note**: By default, CPU, memory, disk, and network counters are enabled. You can switch to the **Custom** view for more detailed listing.

1. Switch to the **Logs** tab of the **VMNAME | Diagnostic settings** blade and review the available event log collection options.

    >**Note**: By default, log collection includes critical, error, and warning entries from the Application Log and System log, as well as Audit failure entries from the Security log. Here as well you can switch to the **Custom** view for more detailed configuration settings.

1. On the **VMNAME** blade, in the **Monitoring** section, click **Logs** and then click **Enable**. 

1. On the **VMNAME - Logs** blade, ensure that the Log Analytics workspace you created earlier in this lab is selected in the **Choose a Log Analytics Workspace** drop-down list and click **Enable**.

    >**Note**: Do not wait for the operation to complete but instead proceed to the next step. The operation might take about 5 minutes.

1. On the **VMNAME | Logs** blade, in the **Monitoring** section, click **Metrics**.

1. On the **VMNAME | Metrics** blade, on the default chart, note that at this point, the **Metrics Namespace** drop-down list, in addition to the **Virtual Machine Host** entry includes also the **Guest (classic)** entry.

    >**Note**: This is expected, since you enabled guest-level diagnostic settings. You also have the option to **Enable new guest memory metrics**.

1. In the **Metrics Namespace** drop-down list, select  the **Guest (classic)** entry.

1. In the **Metric** drop-down list, review the list of available metrics.

    >**Note**: The list includes additional guest-level metrics not available when relying on the host-level monitoring only. 

1. In the **Metric** drop-down list, select **Memory\Available Bytes**, in the **Aggregation** drop-down list, select **Max**, and review the resulting chart. 
   
1. In the Azure portal, search for and select **Monitor** and, on the **Monitor | Overview** blade, click **Metrics**.

1. On the **Select a scope** blade, on the **Browse** tab, navigate to the Virtual machine.

1. In the **Metric** drop-down list, select **Percentage CPU**, in the **Aggregation** drop-down list, select **Avg**, and review the resulting chart.

In the Azure portal, navigate back to the **Monitor** blade, click **Logs**. 

    >**Note**: You might need to click **Get Started** if this is the first time you access Log Analytics.

1. If necessary, click **Select scope**, on the **Select a scope** blade, select the **Recent** tab, select **VMNAME**, and click **Apply**.

1. In the query window, paste the following query, click **Run**, and review the resulting chart:

   ```
   // Virtual Machine available memory
   // Chart the VM's available memory over the last hour.
   InsightsMetrics
   | where TimeGenerated > ago(1h)
   | where Name == "AvailableMB"
   | project TimeGenerated, Name, Val
   | render timechart
   ```

1. Click **Queries** in the toolbar, on the **Queries** pane, locate the **Track VM availability** tile, click the **Run** command button in the tile, and review the results. 

1. On the **New Query 1** tab, select the **Tables** header, and review the list of tables in the **Virtual machines** section.

    >**Note**: The names of several tables correspond to the solutions you installed earlier in this lab.

1. Hover the mouse over the **VMComputer** entry and click the **Preview data** icon.  

1. If any data is available, in the **Update** pane, click **See in query editor**.

    >**Note**: You might need to wait a few minutes before the update data becomes available.

## Project #03 - Azure SQL Database (45 minutes)

1. In a browser, navigate to the [Deploy and configure servers, instances, and databases for Azure SQL](https://docs.microsoft.com/en-us/learn/modules/azure-sql-deploy-configure/) webpage and start lab.

1. End of day 3 and **Imersao Azure Azure Expert**.

1. Continue in the **Mentoria Arquiteto Cloud**.










