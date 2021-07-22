---
title: Error (Can't connect. Please try again later) when searching people in OWA in Exchange Server 2019
description: Describes an issue in which you are unable to search people in OWA in Exchange Server 2019. Provides a solution.
author: simonxjx
ms.author: jcoiffin
manager: dcscontentpm
audience: ITPro 
ms.topic: article 
ms.prod: exchange-server-it-pro
localization_priority: Normal
ms.custom: 
- Exchange Server
- CI 116313
- CSSTroubleshoot
ms.reviewer: jcoiffin
appliesto:
- Exchange Server 2019
search.appverid: 
- MET150
---
# Error (Can't connect. Please try again later) when searching people in OWA in Exchange Server 2019

## Symptoms

With Microsoft Exchange Server 2019 installed on a Windows Server 2019 (Server Core), when trying to find a contact in the global address list (GAL) in Outlook on the web, you receive the following error message:

> Can't connect. Please try again later

## Cause

The default language and locale of Windows Server 2019 aren't US English. The Advanced Query Syntax (AQS) Windows module requires the installation of the corresponding language pack on the server.

## Resolution

Change "Format" to EN-US for current user and default user
![image](https://user-images.githubusercontent.com/43539433/126606787-0050851e-9c5c-4251-a651-b4f08f102736.png)
