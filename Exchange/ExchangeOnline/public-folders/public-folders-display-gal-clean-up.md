---
title: Public folders are displayed in GAL despite having been cleaned up
description: Public folders remain on the global address list (GAL) even though you have removed them. This article provides a resolution.
author: TobyTu
ms.author: Bhalchandra.Atre
manager: dcscontentpm
audience: ITPro 
ms.topic: article 
ms.service: exchange-online
localization_priority: Normal
ms.custom: 
- CI 116313
- CSSTroubleshoot
ms.reviewer: Bhalchandra.Atre
appliesto:
- Exchange Online
search.appverid: 
- MET150
---

# Public folders are displayed in GAL despite having been cleaned up

## Symptoms

Public folder entry may still appear in the global address list (GAL) after removing the public folder mailboxes and/or the public folders.

## Cause

Mail enabled public folders were not removed properly. The mail enabled public folder (MEPF) objects may still be present.

## Resolution

First, check if the stale entry shows in OWA or Outlook online mode. If the entry is showing only in cached mode of Outlook and not in OWA or Outlook online mode, the Offline Address Book (OAB) may not be updated on the Outlook cached mode client yet.

If the stale entry shows in OWA and Outlook online mode as well:

1. Connect to Exchange Online PowerShell.
2. Run the following command to list mail enabled public folders:

    ```powershell
    Get-MailPublicFolder
    ```
3. Notice if the stale public folder entry shows up here.


4. If step 3 showed the Run following command to remove the mail enabled public folders:

    ```powershell
    Get-MailPublicFolder <name of stale pf> | Disable-MailPublicFolder
    ```

    > [!NOTE]
    > Make Sure the MEPF's are no longer needed before removing them.

After removing the entry, use OWA to verify the entry doesn't show up in GAL anymore.

5. If Get-MailPublicFolder does not show stale entry, run following command to check if there's any other object with same name/email address:

Search by name:
 ```powershell
Get-Recipient |?{$_.Name -like "*pub*"}'
```

Search by email address:
```powershell
Get-Recipient |?{$_.EmailAddresses -like "*pub*"}
```

If you do find another object with same email address/name, remove it by using the respective command, i.e. remove-mailbox if its a mailbox type of object

6. Use Outlook on the web to verify if the entries were removed.

    > [!NOTE]
    > Address list changes take a while to be reflected in Outlook client .
