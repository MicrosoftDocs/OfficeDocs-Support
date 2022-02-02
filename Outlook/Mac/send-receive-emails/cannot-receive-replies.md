---
title: You are getting complaints that users unable reply on your emails
description: Describes an issue in where the recipients get a bounce back messages if they reply on email sent from Outlook for Mac version 15.20. Provides a resolution.
ms.author: luche
author: yuriy.samorodov
manager: 
audience: ITPro
ms.topic: troubleshooting
ms.prod: exchange-server-it-pro
localization_priority: Normal
ms.reviewer: sercast, tasitae
ms.custom: 
- Outlook for Mac
- CSSTroubleshoot
search.appverid:
- MET150
appliesto:
- Exchange Online
- Outlook 2016 for Mac
---
# You are getting complaints that users unable reply on your emails


## Symptoms

- You're using Outlook 2016 for Mac version 15.20 or later
- Your mailbox is located in Exchange Online
- Your account is synchronized from on-premises to the cloud through federation services
- Modern authentication is enabled
- Microsoft 365 Activity Logs show Operation **Send As** as if the user have been sending on behalf of some other account. Here is how the logs might look like:

  ![image](https://user-images.githubusercontent.com/5260172/152075684-a59d1945-f276-40ef-ad3e-3f1d2a184f29.png)



## Scenario

- You send a message to any recipient
- The recipient responds to your message
- The recipient immediately gets a bounce back saying


## Cause

This issue occurs if **all** the conditions below are true:

- Your email address differs from your user principal name (UPN).
- Your login address (User Principal Name, in short UPN) is not on the list of your email addresses
- Your  UPN is not the same as your Primary Email Address
- Both *E-mail Address* and *User name* fields in Outlook settings contain UPN

If this is the case Outlook for Mac would send a message sucessfully using UPN as the from address. However, since your account does not have UPN among the available email addresses (remember, it's just a login name), your customers and partners would be unable to reply and eventually would get a bounce back message in return

## Resolution

In this situation, there are a couple waus to fix the problem.

* Manually **replace** UPN in the *E-mail Address* field with the correct email. Here is how you do it:
  1. Open your Outlook for Mac
  2. Expand **Settings** menu
  3. Proceed to **Account**

    ![image](https://user-images.githubusercontent.com/5260172/152073829-76b2e7e3-c1ea-448a-9af1-12a53fe262bc.png)

  4. Modify **E-mail Address** field accordingly

    ![image](https://user-images.githubusercontent.com/5260172/152074130-d0f18bae-ad65-4c7d-8623-cabd3525151d.png)


  5. Hit **Save**
  6. Press *Cmd+Q* to exit Outlook for Mac

* Ask your adminisstrator to add your login name (UPN) to the list of email addresses

## Testing

1. Start Outlook
2. Compose and send a **new** message to the recipient of your choice
3. Confirm with the the recipient the message there is a correct email address in the **From** field, and not your UPN
4. Ask the recipient to reply on the test message
5. Make sure the recipient does not get a message andn you receive the reply

Enter your UPN in the User name field of the **Accounts** dialog box, and then quit and restart Outlook 2016 for Mac.


## More details

If you don't know your UPN, contact your administrator.

For more information about UPNs, see [User name formats](/windows/win32/secauthn/user-name-formats).
