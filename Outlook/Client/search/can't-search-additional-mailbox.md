---
title: “Something went wrong and your search couldn’t be completed” error message when searching from an additional mailbox in Outlook
description: When you perform a search from a secondary mailbox that you have Full Access permissions in Outlook, the operation will fail. This is a known limitation of the Exchange Online search service. 
author: TobyTu
ms.author: aruiz
manager: dcscontentpm
audience: ITPro 
ms.topic: article 
ms.service: o365-proplus-itpro
localization_priority: Normal
ms.custom: 
- CI 114104
- CSSTroubleshoot
ms.reviewer: aruiz
appliesto:
- Outlook for Office 365
- Exchange Online
search.appverid: 
- MET150
---

# “Something went wrong and your search couldn’t be completed” error message when searching from an additional mailbox in Outlook

## Symptoms

Consider the following scenario:

- You are granted Full Access permissions to a secondary mailbox.
- You add the mailbox as an additional Exchange account to your Microsoft Outlook for Office 365 profile.
- You run a search from that mailbox.

In this scenario, you receive an error message that resembles the following:

*Something went wrong and your search couldn’t be completed.*

*It looks like there’s a problem with your network connection.*

When this error occurs, clicking the **Let’s look on your computer instead** option may display the expected search results.

## Known causes

- Default **Exchange Server** response time  threshold is too low;
- This is a known limitation of the **Exchange Online** search service. The issue occurs if an Outlook client uses the primary mailbox user’s credentials to run a search from a secondary mailbox.

## Workaround

> [!IMPORTANT]
> Follow the steps in this section carefully. Serious problems might occur if you modify the registry incorrectly. Before you modify it, [back up the registry for restoration](https://support.microsoft.com/help/322756) in case problems occur.

To work around this issue, I suggest that you apply, as needed, each one of the following methods:

## Method 1 ##
Lets start by setting set Outlook's default **ServerAssistedSearchTimeout**, the maximum time in milliseconds that Outlook should wait for Exchange to provide search results before falling back to use local search), to **5000** ms (5 seconds)

Steps do define server assisted search timeout:
1. Using REGEDIT.EXE, create **ServerAssistedSearchTimeout** according the details below.
2. Restart Outlook.
3. Check if your search problem has beend fixed. 

| | |
|---------|---------|
|Registry Path|`HKEY_CURRENT_USER\software\microsoft\office\16.0\outlook\search`|
|Value Name|ServerAssistedSearchTimeout|
|Value Type|REG_DWORD|
|Value Data|5000|

In case you've successfully solved your problem using the previous instructions, you should consider defining it thru a **Group Policy**. For further details, check [How Outlook 2016 utilizes Exchange Server 2016 FAST Search](https://techcommunity.microsoft.com/t5/outlook-global-customer-service/how-outlook-2016-utilizes-exchange-server-2016-fast-search/ba-p/381195)

## Method 2 ##
Set Outlook to ignore **Exchange Online Search**, and imediately, behave as if **Let’s look on your computer instead** link has been pressed, so it will use **Windows Desktop Search**'s local index diretly. To do achieve that, create a **DisableServerAssistedSearch** registry key. For more information, see this [Outlook Global Customer Service & Support Team Blog article](https://techcommunity.microsoft.com/t5/outlook-global-customer-service/how-outlook-2016-utilizes-exchange-server-2016-fast-search/ba-p/381195).

Steps do disable server assisted search feature:
1. Using REGEDIT.EXE, create **DisableServerAssistedSearch** according the details below.
2. Restart Outlook.
3. Check if your search problem has beend fixed. 


| | |
|---------|---------|
|Registry Path|`HKEY_CURRENT_USER\software\microsoft\office\16.0\outlook\search`|
|Value Name|DisableServerAssistedSearch|
|Value Type|REG_DWORD|
|Value Data|1|

In case you've successfully solved your problem using the previous instructions, you should consider defining it thru a **Group Policy**. For further details, check [How Outlook 2016 utilizes Exchange Server 2016 FAST Search](https://techcommunity.microsoft.com/t5/outlook-global-customer-service/how-outlook-2016-utilizes-exchange-server-2016-fast-search/ba-p/381195)

## Method 3 ##
Set the additional mailbox to stop using Cached Exchange Mode, see this [Microsoft Support article](https://support.office.com/article/Turn-on-Cached-Exchange-Mode-7885AF08-9A60-4EC3-850A-E221C1ED0C1C).

## Method 4 ##
Create a shared mailbox as an additional mailbox. For more information, see [Create a shared mailbox](https://docs.microsoft.com/office365/admin/email/create-a-shared-mailbox?view=o365-worldwide).
