---
title: Users in a hybrid deployment can't access a shared mailbox in Exchange Online
description: Describes an issue in which users in an Exchange hybrid deployment are unable to access, view free/busy information, or send mail to a shared mailbox that was created in Exchange Online.
author: simonxjx
audience: ITPro
ms.prod: exchange-server-it-pro
ms.topic: troubleshooting
ms.author: v-six
ms.custom: 
- Exchange Hybrid
- CSSTroubleshoot
manager: dcscontentpm
localization_priority: Normal
search.appverid: 
- MET150
appliesto:
- Exchange Online
- Exchange Server 2016 Enterprise Edition
- Exchange Server 2016 Standard Edition
- Exchange Server 2013 Enterprise
- Exchange Server 2013 Standard Edition
- Exchange Server 2010 Enterprise
- Exchange Server 2010 Standard
---
# Users in a hybrid deployment can't access a shared mailbox that was created in Exchange Online

> [!NOTE]
> The Hybrid Configuration wizard that's included in the Exchange Management Console in Microsoft Exchange Server 2010 is no longer supported. Therefore, you should no longer use the old Hybrid Configuration wizard. Instead, use the Office 365 Hybrid Configuration wizard. For more information, see [Office 365 Hybrid Configuration wizard for Exchange 2010](https://blogs.technet.com/b/exchange/archive/2016/02/17/office-365-hybrid-configuration-wizard-for-exchange-2010.aspx).

## Problem

Consider the following scenario:

- You have a hybrid deployment of On-premises Microsoft Exchange Server and Microsoft Exchange Online in Office 365.
- You create a shared mailbox directly in Exchange Online.
- You assign Full Access permissions to one or more users.

In this scenario, you experience one or more of the following issues:

- Users can't open the shared mailbox in Outlook.
- Users can't view free/busy information for the shared mailbox.
- Users can't send mail to the shared mailbox.
  
## Cause

These issues can occur if the shared mailbox is created by using the Exchange Online management tools. In this situation, the On-premises Exchange environment has no object to reference for the shared mailbox. Therefore, all queries for that SMTP address fail.

## Solution

Create a remote mailbox in the On-premises environment to match the object in Exchange Online. To do this, follow these steps.

> [!NOTE]
> For On-premises environments that use older versions of Exchange Server 2013 (cumulative update 20 or earlier versions) or Exchange Server 2016 (cumulative update 9 or earlier versions), please skip the following steps and proceed to the steps under the [Alternative method](#alternative-method) section instead.

For On-premises environments that use a current version of Exchange 2013, 2016 or 2019:

1. Create an On-premises object for the cloud mailbox by using the `New-RemoteMailbox` cmdlet in the Exchange Management Shell.

> [!NOTE]
> This object must have the same name, alias, and user principal name (UPN) as the cloud mailbox. For more information, see [New-RemoteMailbox](/powershell/module/exchange/new-remotemailbox).

For example, run the following command:  

        ```powershell
        New-Remotemailbox -Name "Shared mailbox" -Alias sharedmailbox -UserPrincipalName sharedmailbox@contoso.com -Remoteroutingaddress sharedmailbox@contoso.mail.onmicrosoft.com -Shared
        ```

   For more information, see [New-RemoteMailbox](/powershell/module/exchange/new-remotemailbox).

2. Set the ExchangeGuid property on the new On-premises object that you created in step 1 to match the cloud mailbox. To do this, follow these steps:  
   1. Connect to Exchange Online by using a remote session of Windows PowerShell.

      For more information about how to do this, see [Connect to Exchange Online PowerShell](/powershell/exchange/connect-to-exchange-online-powershell).
	  
   2. Use the `Get-Mailbox` cmdlet to retrieve the value of the `ExchangeGuid` property of the cloud mailbox. For example, run the following command:

        ```powershell
        Get-Mailbox <MailboxName> | FL ExchangeGuid  
        ```

      For more information, see [Get-Mailbox](/powershell/module/exchange/get-mailbox).
	  
   3. Open the Exchange Management Shell on the On-premises Exchange server.
   4. Use the `Set-RemoteMailbox` cmdlet to set the value of the `ExchangeGuid` property on the On-premises object to the value that you retrieved in step 3b. For example, run the following command:

        ```powershell
        Set-RemoteMailbox <MailboxName> -ExchangeGuid <GUID>
        ```

      For more information, see [Set-RemoteMailbox](/powershell/module/exchange/set-remotemailbox).

3. Since the new AD object will not be set with the correct Remote Recipient Type, we need to update the On-premises AD attribute to reflect the type associated with a migrated Shared mailbox in Exchange Online.

For example, run the following command:  

        ```powershell
        Set-ADUser -Identity ((Get-Recipient sharedmailbox@contoso.com).samaccountname) -Replace @{msExchRemoteRecipientType=100}
        ```

   For more information, see [Shared mailboxes are unexpectedly converted to user mailboxes after directory synchronization runs in an Exchange hybrid deployment](exchange/troubleshoot/user-and-shared-mailboxes/shared-mailboxes-unexpectedly-converted-to-user-mailboxes).

### Alternative method for Older Versions of Exchange Server 2013 (cumulative update 20 or earlier versions) or Exchange Server 2016 (cumulative update 9 or earlier versions)

1. Convert the shared mailbox to a regular mailbox by using the Exchange admin center in Exchange Online. To do this, follow these steps:  
   1. Open the Exchange admin center in Exchange Online.
   2. Click **recipients**, and then click **shared**.
   3. Select the shared mailbox, and then click **Convert**.
   4. On the Warning page, select **Yes** to convert the shared mailbox.

2. Create an On-premises object for the cloud mailbox by using the `New-RemoteMailbox` cmdlet in the Exchange Management Shell.

    > [!NOTE]
    > This object must have the same name, alias, and user principal name (UPN) as the cloud mailbox.

For example, run the following command:  

        ```powershell
        New-Remotemailbox -Name "Shared mailbox" -Alias sharedmailbox -UserPrincipalName sharedmailbox@contoso.com -Remoteroutingaddress sharedmailbox@contoso.mail.onmicrosoft.com
        ```
	
   For more information, see [New-RemoteMailbox](/powershell/module/exchange/new-remotemailbox).

3. Set the ExchangeGuid property on the new On-premises object that you created in step 2 to match the cloud mailbox. To do this, follow these steps:  
   1. Connect to Exchange Online by using a remote session of Windows PowerShell.

      For more information about how to do this, see [Connect to Exchange Online PowerShell](/powershell/exchange/connect-to-exchange-online-powershell).
	  
   2. Use the `Get-Mailbox` cmdlet to retrieve the value of the `ExchangeGuid` property of the cloud mailbox. For example, run the following command:

        ```powershell
        Get-Mailbox <MailboxName> | FL ExchangeGuid  
        ```

      For more information, see [Get-Mailbox](/powershell/module/exchange/get-mailbox).
	  
   3. Open the Exchange Management Shell on the On-premises Exchange server.
   4. Use the `Set-RemoteMailbox` cmdlet to set the value of the `ExchangeGuid` property on the On-premises object to the value that you retrieved in step 3b. For example, run the following command:

        ```powershell
        Set-RemoteMailbox <MailboxName> -ExchangeGuid <GUID>
        ```

      For more information, see [Set-RemoteMailbox](/powershell/module/exchange/set-remotemailbox).

4. Wait for directory synchronization to occur. Or, force directory synchronization.

    For more information, see [Synchronize your directories](/azure/active-directory/hybrid/whatis-hybrid-identity).

5. Make sure that the Office 365 user object is displayed as **Synced with Active Directory**.

6. Convert the mailbox to a shared mailbox by using the `Set-Mailbox` cmdlet in the Exchange Online Management Shell. For example, run the following command:

    ```powershell
    Set-Mailbox <MailboxName> -Type Shared
    ```

   For more information, see [Set-Mailbox](/powershell/module/exchange/set-mailbox).

7. Since the new On-premises AD object will not be set with the correct properties, we need to update the On-premises AD attributes to reflect the type associated with a migrated Shared mailbox in Exchange Online.

For example, run the following command:  

        ```powershell
        Set-ADUser -Identity ((Get-Recipient PrimarySmtpAddress).samaccountname) -Replace @{msExchRemoteRecipientType=100;msExchRecipientTypeDetails=34359738368}
        ``` 

   For more information, see [Shared mailboxes are unexpectedly converted to user mailboxes after directory synchronization runs in an Exchange hybrid deployment](exchange/troubleshoot/user-and-shared-mailboxes/shared-mailboxes-unexpectedly-converted-to-user-mailboxes).

## More information

For more information about remote shared mailboxes, see [Cmdlets to create or modify a remote shared mailbox in an On-premises Exchange environment](https://support.microsoft.com/help/4133605/).  

Still need help? Go to [Microsoft Community](https://answers.microsoft.com/) or the [Exchange TechNet Forums](/answers/topics/office-exchange-server-itpro.html).
