---
title: Performance issues for too many items or folders
description: Describes a performance issue that occurs if there are too many items or too many folders in a cached mode .ost file or .pst file folders. Provides a resolution.
author: helenclu
ms.author: luche
manager: dcscontentpm
audience: ITPro
ms.topic: troubleshooting
localization_priority: Normal
ms.custom: 
  - Outlook for Windows
  - CSSTroubleshoot
  - CI 163089
ms.reviewer: gregmans, gbratton, tasitae
appliesto: 
  - Outlook for Microsoft 365
  - Outlook 2019
  - Outlook 2016
  - Outlook 2013
  - Outlook 2010
search.appverid: MET150
ms.date: 12/6/2022
---
# Outlook performance issues if there are too many items or folders in a cached mode .ost or .pst file

_Original KB number:_ &nbsp; 2768656

## Symptoms

If you have a large number of items in any single folder, you might experience symptoms such as the following in Microsoft Outlook:

- When you use Cached Exchange Mode or an Outlook data (.pst) file, you notice performance issues when you do certain actions.
- You experience decreased performance in Outlook if the Inbox, Calendar, Tasks, Sent Items, or Deleted Items folders contain a large number of items.
- Calendar performance may become inconsistent. For example, meeting updates might not be reflected in the primary, shared, or delegated calendar.

If you have a large number of mail folders, you might experience performance issues such as the following:

- Folders may take a long time to appear or may not appear correctly.
- If your Outlook profile contains shared mailboxes and has caching enabled (**Download Shared Folders** is selected) then you may encounter folder synchronization issues, performance issues, or other problems if the number of shared folders per mailbox exceeds 500, as described in [Performance and synchronization problems when you work with folders in a secondary mailbox in Outlook](https://support.microsoft.com/help/3115602). Additionally, errors will be logged in the Sync Issues folder and "9646" events will be logged in the Application log.
- In extreme cases, if there are more than 10,000 folders, Outlook can become very slow to open. This behavior occurs because of the time it takes to enumerate this large number of folders.

## Cause

These problems may occur if you have a large number of folders or a large number of items in any single folder. There is no hard limit, but generally you may experience increasing performance issues as the number of items approaches 10,000 calendar items, 10,000 folders, or 100,000 mail items per folder. Large numbers of recurring meetings, or long-lived recurring meetings can also have an outsized impact on performance.

## Resolution

> [!IMPORTANT]
> Follow the steps in this section carefully. Serious problems might occur if you modify the registry incorrectly. Before you modify it, [back up the registry for restoration](https://support.microsoft.com/help/322756) in case problems occur.

### Resolutions for Calendar-Related Issues

#### Managing the Growth of Exceptions

Not all calendar items have equal impact on performance. One-time meetings have relatively low impact, whereas long-lived recurring meetings and particularly recurring meetings with changes to the subject, body, location, or time have more performance cost. You can address this higher cost by using Outlook options to set an end time or maximum recurrence count, and then by creating a new recurring meeting instead of extending the existing recurrence.

#### Activating Shared Calendar Improvements

Activating the Shared Calendar Improvements option for primary calendar users and users with delegated access will improve performance. This improvement comes from the fact that calendar actions are sent directly to the server instead of having to be synchronized from local storage, and conflict resolution can be done more efficiently.

You can learn more about activating and managing the shared calendar improvements by reading [How to enable and disable the Outlook calendar sharing updates - Microsoft Support](https://support.microsoft.com/en-us/office/how-to-enable-and-disable-the-outlook-calendar-sharing-updates-c3aec5d3-55ce-4cea-84b0-80aab6d8dc26)

#### Limiting the Sync Window

Limiting the synchronization window used for calendar folders will reduce the number of items stored locally in your calendar folder(s) and can improve performance.

In Sync Window Settings, adjust the number of months of data that are synced for the primary shared calendars. To do this, add the following registry keys.

> [!NOTE]
> This method applies to [Version 1810](/officeupdates/monthly-channel-2018#version-1810-october-29) or later version of Microsoft 365 Apps for enterprise (Click-to-Run).

|Description|Setting to enable Calendar Sync window|
|---|---|
|Registry path|HKEY_CURRENT_USER\Software\Microsoft\Office\16.0\Outlook\Cached Mode|
|Name|CalendarSyncWindowSetting|
|Type|REG_DWORD|
|Value|Value = 0 Inactive<br/>Value = 1 Primary Calendar folder<br/>Value = 2 All Calendar folders<br/>Defaults to 0 if not set|

|Description|Setting to control the number of months in the Calendar Sync window|
|---|---|
|Registry path|HKEY_CURRENT_USER\Software\Microsoft\Office\16.0\Outlook\Cached Mode|
|Name|CalendarSyncWindowSettingMonths|
|Type|REG_DWORD|
|Value|Value = Choose decimal value to select the number of months in the Calendar sync window, such as 1, 3, 6, or 12<br/>Defaults to 6 if not set|

|Description|Setting to control whether to keep all recurring items instead of filtering them|
|---|---|
|Registry path|HKEY_CURRENT_USER\Software\Microsoft\Office\16.0\Outlook\Cached Mode|
|Name|CalendarSyncWindowAllRecurring|
|Type|REG_DWORD|
|Value|Value = 0 - Only recurring meeting series that have an end date that is either in the future or falls within the current calendar sync window setting will be synced. <br/>Value = 1 - All recurring meeting series will be synced, regardless of the end date. <br/>Defaults to 0 if not set|
|Explanation|By default (if the value isn't set or is set to 0), recurring meeting series that have an end date that is either in the future or falls within the current calendar sync window setting will be synced. For example, if today's date is May 3, 2022, and the calendar sync window is set to 1 month, all recurring meeting series that have an end date on or after April 3, 2022, will be synced. If the end date falls outside the sync window setting, the recurring meeting series will be removed from the .ost file.<br/>To sync all recurring meeting series regardless of the end date, set the `CalendarSyncWindowAllRecurring` value to **1**.|

These registry keys update the sync restriction so that the Cached Mode client downloads fewer Calendar items, even for a Calendar that has multiple years of history on the server. These keys don't clean up older Calendar content that was already downloaded. However, this method can be effective if you're willing to *clear offline items* and resync your Calendar (instead of bulk deleting old items). If you already have the Calendar in the profile, you have to clear the Offline Items after you set the registry keys and restart Outlook.

To clear offline Calendar items, follow these steps:

1. Open the **Calendar** pane in Outlook, then right-click the **Calendar**  folder.
2. Select **Properties**.
3. On the **General** tab, select **Clear Offline Items**.
4. Select **OK**.

### Resolutions for Mail-Related issues

If you have folders with a quantity of items approaching the quantities mentioned above, and stored in an Outlook data (.pst) file or an offline Outlook data (`.ost`) file, move items from the larger folders to separate or smaller folders in the same mailbox or data file. Optionally, if you have a Microsoft Exchange online archive, you can move items to that archive or create retention policies to automatically dispose of older items.

#### Archiving Mail Items

If you have enabled Online Archive Mailbox, archive items to the Online Archive Mailbox.

#### Applying a Retention Policy

Apply a Retention Policy on Mail or Calendar folder to delete older items from this folder. (For example: Any item that is not modified within one year moves to the Deleted Items folder.)

### Additional Diagnostics

Additionally, you can use the Microsoft Support and Recovery Assistant to diagnose issues that affect Outlook. This tool applies to both calendar and mail issues.

To download and install the tool, go to the following Microsoft website:

[About the Microsoft Support and Recovery Assistant](https://support.microsoft.com/office/about-the-microsoft-support-and-recovery-assistant-e90bb691-c2a7-4697-a94f-88836856c72f)

In the tool, run the Outlook Diagnostic under the Advanced Diagnostics section to determine the cause of the performance issue.

## More information

To view the Calendar item count, use the **Calendar Properties** dialog box to check how many items are in a Calendar folder in Outlook Cached Mode.

To do this, follow these steps:

1. Open the Calendar pane in Outlook, then right-click the **Calendar** folder.
2. Select **Properties**.
3. On the **General** tab, select **Show total number of items**.
4. Select the **Synchronization** tab.
5. View the count under **View statistics for this folder**.

Outlook uses an `.ost` file only if the Exchange email account is configured to use Cached Exchange Mode. When Outlook is configured to connect to the Exchange mailbox in online mode, an `.ost` file is not used. If your Outlook client is connected to Exchange in online mode, and you do not have high-item-count folders in a .pst file, any performance issues might be occurring on the server.

For more information about Outlook performance issues, see [You may experience application pauses if you have a large Outlook data file](https://support.microsoft.com/help/2759052/).
