---
title: The URL is invalid error when you upload a file to a SharePoint list
description: This article describes an issue where the URL is invalid when you upload a file to a SharePoint list, and provides a solution.
author: helenclu
manager: dcscontentpm
localization_priority: Normal
search.appverid: 
- MET150
audience: ITPro
ms.prod: sharepoint-server-itpro
ms.topic: article
ms.author: luche
ms.custom: CSSTroubleshoot
appliesto:
- SharePoint Online
- SharePoint Server
---

# "The URL is invalid" error message when you upload a file to a SharePoint list

## Problem

When you upload a file to a SharePoint Online or SharePoint Server 2013 list, you receive the following error message:

> The URL \<file name> is invalid. It may refer to a nonexistent file or folder, or refer to a valid file or folder that is not in the current Web.

> [!NOTE]
> The placeholder \<file name> represents the name of the file that you uploaded to the SharePoint list.

## Cause

This issue occurs if one of the following conditions is true:

- The **Version** column is configured as an indexed column
- The content database doesn't have enough space in SharePoint Server 2013
- You don't have the enough permission to the content database in SharePoint Server 2013

## Solution

To fix this issue, use one of the following methods:

### Remove the indexed Version column

To remove the **Version** column from the list of indexed columns for the list that has this issue, follow these steps:

1. Browse to the list where the issue exists.
1. In the ribbon, select Settings :::image type="icon" source="https://support.content.office.net/en-US/media/96dc60c0-3b6b-4dd7-b246-7d1750653462.png" border="false":::, and then select **Library settings**.
1. Under the **Columns** section, select **Indexed columns**.
1. In the **Indexed Columns** list, select **Version** > **Delete** > **OK**.
1. Upload any files that you previously experienced the issue with.

### Configure the content database storage in SharePoint Server 2013

You can check the error details in the Event Viewer on the SharePoint application server. If the error is caused by the databases or logs storage, configure the storage limit of the following content databases and logs accordingly.

- SharePoint content databases
- SharePoint content database logs
- Temp database logs
- SharePoint service databases
- SharePoint service databases logs
- SharePoint service databases temp logs

### Check the permission of the content database

in Central Administration, select **Manage content databases** and make sure that the related content database has the db_owner role of the SharePoint farm service account.

Still need help? Go to [SharePoint Community](https://techcommunity.microsoft.com/t5/sharepoint/ct-p/SharePoint).
