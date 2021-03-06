<properties
	pageTitle="Connect to Azure Stack | Microsoft Azure"
	description="Learn how to connect Azure Stack"
	services="azure-stack"
	documentationCenter=""
	authors="ErikjeMS"
	manager="byronr"
	editor=""/>

<tags
	ms.service="azure-stack"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="10/18/2016"
	ms.author="erikje"/>

# Connect to Azure Stack
After completing the installation of Azure Stack, you must connect to deploy and manage resources.  This guide provides connectivity options and steps for connecting to your Azure Stack POC.

## Connectivity options
Much like public cloud resources have different connectivity options, Azure Stack resources have similar options even though they reside on your local network.  This document walks through two approaches for connecting to Azure Stack.  Each of the following options has advantages and disadvantages, and you are not limited using one single method.

 - Virtual Private Network (VPN) connection allows multiple users to connect from a client outside of the Azure Stack infrastructure but does require configuration.
 - Remote Desktop allows you to connect to a client VM running on Azure Stack.


## Remote Desktop
Remote Desktop connects you to a VM running on Azure Stack where you can install tools, work within the portal, and perform administrative actions.  This option is limited to a single user at a time, which makes it a good option if you are the only person evaluating Azure Stack services. To connect with Remote Desktop, use the following steps:

1.  Log in to the Azure Stack POC physical machine.

2.  Open a Remote Desktop Connection and connect to MAS-CON01. Enter **AzureStack\AzureStackAdmin** as the username, and the administrative password you provided during Azure Stack setup.  

3.  On the MAS-CON01 desktop, double-click **Microsoft Azure Stack Portal** icon (https://portal.azurestack.local/) to open the [portal](azure-stack-key-features.md#portal).

    ![Azure stack portal icon](media/azure-stack-connect-azure-stack/image2.png)

4.  Log in using the Azure Active Directory credentials specified during installation.

## VPN
Virtual Private Network connections allow you to connect from a client outside of the Azure Stack infrastructure.  The advantages to this option are that you can use the same management and development tools from your local client, and the ability to support multiple simultaneous users. To connect to Azure Stack with a VPN, follow these steps:

1.  Download the Azure Stack Tools scripts.  These support files can be downloaded by either browsing to the [GitHub repository](https://github.com/Azure/AzureStack-Tools), or running the following Windows PowerShell script as an administrator:
    
	>[AZURE.NOTE]  The following steps require Windows Management Framework (WMF) 5.0.  You can check for your version by executing $PSVersionTable.PSVersion and comparing the "Major" version.  

    ```PowerShell
       
       #Download the tools archive
       invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip -OutFile master.zip

       #Expand the downloaded files. 
       expand-archive master.zip -DestinationPath . -Force

       #Change to the tools directory
       cd AzureStack-Tools-master
    ````

2.  After you have downloaded the tools, and in the same Windows PowerShell session, navigate to the **Connect** folder, and import the AzureStack.Connect.psm1 module:

    ```PowerShell
    cd Connect
    import-module .\AzureStack.Connect.psm1
    ```

3.  To create and then connect the Azure Stack VPN connection, run the following Windows PowerShell.  Before running, populate the admin password and Azure Stack host address fields. 
    
    ```PowerShell
    #Change the IP Address below to match your Azure Stack host
    $hostIP = "<Azure Stack IP Address>"
    
    # Change password below to reference the password provided for administrator during Azure Stack installation
    $Password = ConvertTo-SecureString "<admin password>" -AsPlainText -Force

     # Add Azure Stack One Node host to the trusted hosts on your client computer
    Set-Item wsman:\localhost\Client\TrustedHosts -Value $hostIP -Concatenate 

    # Update Azure Stack host address to be the IP Address of the Azure Stack POC Host
    $natIp = Get-AzureStackNatServerAddress -HostComputer $hostIP -Password $Password

    # Create VPN connection entry for the current user
    Add-AzureStackVpnConnection -ServerAddress $natIp -Password $Password

    # Connect to the Azure Stack instance. This command (or the GUI steps in step 5) can be used to reconnect
    Connect-AzureStackVpn -Password $Password
    ```

    >[AZURE.NOTE] During execution, you are prompted to trust the Azure Stack host.  You are also prompted to install a certificate, which appears behind the Powershell session window.

4.  Test connecting to the Azure Stack portal by navigating to *https://portal.azurestack.local* using an Internet browser.  

5.  You can also view and manage the status of your Azure Stack connection by viewing the network connections on your client:

    ![Image of the network connect menu in Windows 10](media/azure-stack-connect-azure-stack/image1.png)

>[AZURE.NOTE] This VPN connection does not provide connectivity to VMs or other resources.  For information on connectivity to resources, see [One Node VPN Connection](azure-stack-create-vpn-connection-one-node-tp2.md)


## Next steps
[First tasks](azure-stack-first-scenarios.md)

[Install and connect with PowerShell](azure-stack-connect-powershell.md)

[Install and connect with CLI](azure-stack-connect-cli.md)


