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
    | Resource group | the name of a new resource group **RG-HA** |
    | Virtual machine name | **VMWEB01** and **VMWEB02** |
    | Region | select one of the regions that support availability zones and where you can provision Azure virtual machines | 
    | Availability options | **Availability zone** |
    | Availability zone | **1** and **2** |
    | Image | **Ubuntu Server 18.04 LTS - Gen1** |
    | Azure Spot instance | **No** |
    | Size | **Standard B1ms** |
    | Username | **admaz** |
    | Password | **Arqu1t3t0Cl0ud**** |
    | Public inbound ports | **SSH (22) and HTTP (80)** |
    | Would you like to use an existing Windows Server license? | **No** |

1. Click **Next: Disks >** and, on the **Disks** tab of the **Create a virtual machine** blade, specify the following settings (leave others with their default values):

    | Setting | Value | 
    | --- | --- |
    | OS disk type | **Standard SSD** |
    | Enable Ultra Disk compatibility | **No** |

1. Click **OK** and, back on the **Networking** tab of the **Create a virtual machine** blade, specify the following settings (leave others with their default values):

    | Setting | Value | 
    | --- | --- |
    | Virtual Network | the name of a virtual network **VNET-Hub** |
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
    | Resource group | **RG-HA** |
    | Name | **ALB-WEB** |
    | Region| name of the Azure region into which you deployed all other resources in this lab |
    | Type | **Public** |
    | SKU | **Standard** |
    | Public IP address | **Create new** |
    | Public IP address name | **ALB-WEB-PI** |
    | Availability zone | **Zone-redundant** |
    | Add a public IPv6 address | **No** |

    > **Note**: Wait for the Azure load balancer to be provisioned. This should take about 2 minutes. 

1. On the deployment blade, click **Go to resource**.

1. On the **ALB-WEB** load balancer blade, click **Backend pools** and click **+ Add**.

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
    | Resource Group | **RG-HA** |
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

## Lab #02 - Azure Application Gateway (30 minutes)

1. On the Azure portal menu or from the **Home** page, select **Create a resource**. The **New** window appears.

2. Select **Networking** and then select **Application Gateway** in the **Featured** list.

1. On the **Basics** tab, enter these values for the following application gateway settings:

   - **Resource group**: Select **RG-HA** for the resource group. If it doesn't exist, select **Create new** to create it.
   - **Application gateway name**: Enter **AAGWEB** for the name of the application gateway.
   - **Tier**: Select **Standard v2**.
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

1. On the Azure portal menu, select **All resources** or search for and select **ALB-WEB**.

1. Disassociate VMs, **VMWEB01** and **VMWEB02** on the Load Balancer, in the Backend pool.

Add Backend targets:

1. On the Azure portal menu, select **All resources** or search for and select *All resources*. Then select **AAGWEB**.

2. Select **Backend pools** from the left menu.

3. Select **BP-WEB**.

4. Under **Backend targets**, **Target type**, select **Virtual machine** from the drop-down list.

5. Under **Target**, select the **VMWEB01** and **VMWEB02** virtual machines and their associated network interfaces from the drop-down lists.

6. Select **Save**.

7. Wait for the deployment to complete before proceeding to the next step.

1. Test the Application gateway.

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

1. In **Select a load balancer**, create a new Azure Load Balancer.

1. For **Select a backend pool**, select **Create new**, type **BP-VMSSWEB**, then select **Create**.

1. On the **Scaling** page, under **Instance**, select **2** and **Scaling policy**, select **Manual**. 

1. On the **Management** page, under **Monitoring**, select **Enable with custom storage account**.

1. When you are done, select **Review + create**. 

1. After it passes validation, select **Create** to deploy the scale set.

    > **Note**: You might need to wait a few minutes.

1. Sign in to the [**Azure portal**](http://portal.azure.com) and open Azure Cloud Shell.

1. In Cloud Shell, start the code editor and execute command **code add-customextension-vmss.ps1**.

1. Add the following text to the script file:

  ```powershell
    # Set Script 
$customConfig = @{ 
  "fileUris" = (,"https://raw.githubusercontent.com/maiaacademy/azureexpert/master/Mod06-AllFiles/VMSS-Install-IIS_v1.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File VMSS-Install-IIS_v1.ps1" 
} 

    # Set VMSS variables
$rgname = "rg-vmss"
$vmssname = "vmssweb"
 
    # Get VMSS object 
$vmss = Get-AzVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
 
    # Add VMSS extension 
$vmss = Add-AzVmssExtension -Name "CustomScript" -VirtualMachineScaleSet $vmss -Publisher "Microsoft.Compute" -Type "CustomScriptExtension" -TypeHandlerVersion "1.9" -Setting $customConfig

    # Update VMSS 
Update-AzVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $vmss
   ```

1. Press Ctrl+S to save the file. Then press Ctrl+Q to close the code editor.

1. Run the following command **./add-customextension-vmss.ps1**.

1. In **VMSSWEB**, select **Extensions** and check a new extension.

1. Select **Instances** in **VMSSWEB**, click **Upgrade** for all instances.

    > **Note**: You might need to wait a few minutes.

1. Test the Public IP address in Browser.

1. In the Azure portal, navigate to the **Virtual Machine Scale Sets** entry, and on the **VMSSWEB** blade, select **Scaling**. 

1. On the **Scaling** blade, select the **Custom autoscale** option.

1. In the **Custom autoscale** section, specify the following settings (leave others with their default values):

    | Setting | Value | 
    | --- | --- |
    | Scaling mode | **Scale based on a metric** |
    | Instance limits Minimum | **1** |
    | Instance limits Maximum | **3** |
    | Instance limits Default | **1** |

1. Select **+ Add a rule**.

1. On the **Scale rule** blade, specify the following settings and select **Add** (leave others with their default values):

    | Setting | Value | 
    | --- | --- |
    | Time aggregation | **Maximum** |
    | Metric namespace | **Virtual Machine Host** |
    | Metric name | **Percentage CPU** |
    | VMName Operator | **=** |
    | Operator | **Greater than** |
    | Metric threshold to trigger scale action | **1** |
    | Duration (in minutes) | **1** |
    | Time grain statistics | **Maximum** |
    | Operation | **Increase count by** |
    | Instance count | **1** |
    | Cool down (minutes) | **5** |

1. Back on the **Scaling** blade, select **+ Add a rule**.

1. On the **Scale rule** blade, specify the following settings and select **Add** (leave others with their default values):

    | Setting | Value | 
    | --- | --- |
    | Time aggregation | **Average** |
    | Metric namespace | **Virtual Machine Host** |
    | Metric name | **Percentage CPU** |
    | VMName Operator | **=** |
    | Operator | **Less than** |
    | Metric threshold to trigger scale action | **1** |
    | Duration (in minutes) | **1** |
    | Time grain statistics | **Minimum** |
    | Operation | **Decrease count by** |
    | Instance count | **1** |
    | Cool down (minutes) | **5** |

1. Back on the **Scaling** blade, select **Save**.

1. In the Azure portal, start a new **Bash** session in the Cloud Shell pane. 

1. From the Cloud Shell pane, run the following to trigger autoscaling of the Azure VM Scale Set instances in the backend pool of the Azure Application Gateway (replace the `<lb_IP_address>` placeholder with the IP address of the front end of the gateway you identified earlier):

   ```Bash
   for (( ; ; )); do curl -s <lb_IP_address>?[1-10]; done
   ```
1. In the Azure portal, on the **VMSSWEB** blade, review the **CPU (average)** chart and verify that the CPU utilization of the Application Gateway increased sufficiently to trigger scaling out.

    > **Note**: You might need to wait a few minutes.


## Project - High Availability Web Application (60 minutes)

Implement a High Availability Web Application

   ![Screenshot of HAWA](/AllFiles/Images/High-Availability.png)

1. Deploy the Contoso Insurance application, which you will use in this project.

1.  Select the **Deploy to Azure** button below to open the Azure portal and launch the template deployment for the Contoso sample application. Log in to the Azure portal using your subscription credentials if you are prompted to do so.

    [![Button to deploy the Contoso Insurance application template to Azure.](https://aka.ms/deploytoazurebutton "Deploy the Contoso sample application template to Azure")](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmaiaacademy%2Fworkshop-alta-disponibilidade-azure%2Fmaster%2FAllFiles%2FScripts%2Ftemplate.json)

1.  Select the **Deploy to Azure** button below to open the Azure portal and launch the template deployment for the additional infrastructure components that will be used to enable high availability for the Contoso application.

    [![Button to deploy the Contoso High Availability resource template to Azure.](https://aka.ms/deploytoazurebutton "Deploy the Contoso HA resources to Azure")](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmaiaacademy%2Fworkshop-alta-disponibilidade-azure%2Fmaster%2FAllFiles%2FScripts%2Ftemplate-ha.json)

**Important Notes**
- Azure Load Balancer or Application Gateway (optional enable to Web Application Firewall);
- Azure Bastion is optional

References: [HA N-tier application](https://docs.microsoft.com/en-us/azure/architecture/high-availability/ref-arch-iaas-web-and-db)

### Configure High-Availability (HA) for the SQL Server with Always On

In this task, you will build a Windows Failover Cluster and configure SQL Always On Availability Groups to create a high-availability database tier.

1. From the Azure portal home page, select **+ Create a resource**. Select **Storage account**.

2. Complete the **Create storage account** form using the following details:

    - **Resource group**: Use existing / ContosoRG1
    - **Storage account name**: Unique name starting with `###sqlwitness`
    - **Location**: Any location in your area that is **NOT** your Primary or Secondary site.
    - **Performance**: Standard
    - **Replication**: Zone-redundant storage (ZRS)
    - **Access tier (default)**: Hot

3. Switch to the **Advanced** tab. Change the **Minimum TLS version** to **Version 1.0**. Then select **Review + Create**, followed by **Create**.


    > **Note**: To promote the use of the latest and most secure standards, by default, Azure storage accounts require TLS version 1.2. This storage account will be used as a Cloud Witness for our SQL Server cluster. SQL Server requires TLS version 1.0 for the Cloud Witness.

4. Once the storage account is created, navigate to the storage account blade. Select **Access keys** under **Security + networking**. Toggle the **Show/Hide keys button** (Shown as hide keys in the screenshot), copy the **storage account name** and the **first access key**, and paste them into your text editor of choice - you will need these values later.


5. Return to the Azure portal and navigate to the **Load balancer** blade. Select **Backend pools** and open **BackEndPool1**.


6. In the **BackendPool1** blade, select **+ Add** and choose the two SQL VMs. Select **Add** to close. Select **Save** to add these SQL VMs to **BackEndPool1**.

    > **Note**: The load-balancing rule in the load balancer has been created with **Floating IP (direct server return)** enabled. This is important when using the Azure load balancer for SQL Server AlwaysOn Availability Groups.

7. From the Azure portal, navigate to the **SQLVM1** virtual machine. Select **Connect**, then choose **RDP or Bastion**.

8. Connect to the machine using the following credentials:

    - **Username**: `demouser@contoso.com`
    - **Password**: `Demo!pass123`

    > **Note**: When using Azure Bastion to connect to a VM using domain credentials, the username must be specified in the format `user@domain-fqdn` and **not** in the format `domain\user`.

9. On **SQLVM1**, select **Start** and then choose **Windows PowerShell**.

10. Copy and paste the following command into PowerShell and execute it. This will create the Windows Failover Cluster and add all the SQL VMs as nodes in the cluster. It will also assign a static IP address of **10.0.2.99** to the new Cluster named **AOGCLUSTER**.

    ```PowerShell
    New-Cluster -Name AOGCLUSTER -Node SQLVM1,SQLVM2 -StaticAddress 10.0.2.99
    ```

    >**Note**: It is possible to use a wizard for this task, but the resulting cluster will require additional configuration to set the static IP address in Azure.

11. After the cluster has been created, select **Start** and then **Windows Administrative Tools**. Locate and open the **Failover Cluster Manager**.


12. When the cluster opens, select **Nodes**, and the SQL Server VMs will show as nodes of the cluster and show their status as **Up**.

13. If you select **Roles**, you will notice that currently, there aren't any roles assigned to the cluster.

14. Select Networks, and you will see **Cluster Network 1** with status **Up**. If you navigate to the network, you will see the IP address space, and on the lower tab, you can select **Network Connections** and review the nodes.

15. Right-click **AOGCLUSTER,** then select **More Actions**, **Configure Cluster Quorum Settings**.

16. On **Before you Begin** in the wizard, select **Next**. Then choose **Select the quorum witness**. Then, select **Next** again.


17. Select **Configure a cloud witness** and **Next**.


18. Copy the **storage account name** and **storage account key** values you noted earlier and paste them into their respective fields on the form. Leave the Azure Service endpoint as configured. Then, select **Next**.


19. Select **Next** on the Confirmation.


20. Select **Finish**.

21. Select the name of the Cluster again, and the **Cloud Witness** should now appear in the **Cluster Resources**. It is important to always use a third data center; in your case, a third Azure Region is used for your Cloud Witness.

22. Select **Start** and launch **SQL Server 2017 Configuration Manager**.


23. Select **SQL Server Services**, then right-click **SQL Server (MSSQLSERVER)** and select **Properties**.


24. Select the **AlwaysOn High Availability** tab and check the box for **Enable Always OnAvailability Groups**. Select **Apply** and then select **OK** on the message that notifies you that changes won't take effect until after the server is restarted.

25. On the **Log On** tab, change the service account to `contoso\demouser` with the password `Demo!pass123`. Select **OK** to accept the changes, and then select **Yes** to confirm the restart of the server.

26. Return to the Azure portal and open a new Azure Bastion session to **SQLVM2**. Launch **SQL Server 2017 Configuration Manager** and repeat the steps above to **Enable SQL AlwaysOn** and change the **Log On** username. Make sure that you have restarted the SQL Service.

27. Return to your session with **SQLVM1**. Use the Start menu to launch **Microsoft SQL Server Management Studio 18** and connect to the local instance of SQL Server. (Located in the Microsoft SQL Server Tools folder).

28. Select **Connect** to sign on to **SQLVM1**. **Note**: The username for your lab should show **CONTOSO\demouser**.

29. Right-click **Always On High Availability**, then select **New Availability Group Wizard**.

30. Select **Next** on the Wizard.

31. Provide the name **BCDRAOG** for the **Availability group name**, then select **Next**.

32. Select the **ContosoInsurance Database**, then select **Next**.

33. On the **Specify Replicas** screen next to **SQLVM1**, select **Automatic Failover**.

34. Select **Add Replica**.

35. On the **Connect to Server** dialog box, enter the Server Name of **SQLVM2**and select **Connect**. **Note**: The username for your lab should show **CONTOSO\demouser**.

36. For **SQLVM2**, select Automatic Failover and Availability Mode of Synchronous commit.

37. Select **Endpoints** and review these that the wizard has created.

38. Next, select **Listener**. Then, select the **Create an availability group listener**.

39. Add the following details:

    - **Listener DNS Name**: BCDRAOG
    - **Port**: 1433
    - **Network Mode**: Static IP

40. Next, select **Add**.

41. Select the Subnet of **10.0.2.0/24** and then add IPv4 **10.0.2.100** and select **OK**. This is the IP address of the Internal Load Balancer that is in front of the **SQLVM1** and **SQLVM2** in the **Data** subnet running in the **Primary** Site.

42. Select **Next**.

43. On the **Select Initial Data Synchronization** screen, ensure that **Automatic seeding** is selected and select **Next**.

44. On the **Validation** screen, you should see all green. Select **Next**.

45. On the Summary page, select **Finish**.

46. Once the AOG is built, check that each task was successful and select **Close**.

47. Move back to **SQL Management Studio** on **SQLVM1** and expand the **Always On High Availability** item in the tree view. Under Availability Groups, expand the **BCDRAOG (Primary)** item.

48. Right-click **BCDRAOG (Primary)** and then select **Show Dashboard**. You should see that all the nodes have been added and are now "Green".

49. Next, select **Connect** and then **Database Engine** in SQL Management Studio.

50. Enter **BCDRAOG** as the Server Name. This will be connected to the listener of the group that you created. **Note**: The username for your lab should show **CONTOSO\demouser**.

51. Once connected to the **BCDRAOG**, you can select **Databases** and will be able to see the database there. Notice that you do not know directly which server this is running on.

52. Move back to **PowerShell** on **SQLVM1**. Open a new file, paste in the following script, and select the **Play** button. This will update the Failover cluster with the IP address of the Listener that you created for the AOG.

    ```Powershell
    $ClusterNetworkName = "Cluster Network 1"
    $IPResourceName = "BCDRAOG_10.0.2.100"
    $ILBIP = "10.0.2.100"
    Import-Module FailoverClusters
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    Stop-ClusterResource -Name $IPResourceName
    Start-ClusterResource -Name "BCDRAOG"
    ```

53. Move back to SQL Management Studio, select **Connect**, and then **Database Engine**.

54. This time, put the following into the IP address of the Internal Load balancer of the **Primary** Site AOG Load Balancer: **10.0.2.100**. You again will be able to connect to the server, which is up and running as the master. **Note**: The username for your lab should show **CONTOSO\demouser**.

55. Once connected to **10.0.2.100**, you can select **Databases** and will be able to see the database there. Notice that you do not know directly which server this is running on.

    > **Note**: It could take a minute to connect the first time as this goes through the Azure Internal Load Balancer.

56. Move back to Failover Cluster Manager on **SQLVM1**, and you can review the IP Addresses that were added by selecting Roles and **BCDRAOG** and viewing the Resources. Notice how the **10.0.2.100** is Online.

You have now successfully set up the SQL Server VMs to use Always On Availability Groups with a Cloud Witness storage account located in another region.

1. End of day and **Workshop Day 03**.

1. Continue in the **Mentoria Arquiteto Cloud**.










