---
title: 'Gewusst wie: Senden geplanter Benachrichtigungen | Microsoft Docs'
description: In diesem Thema wird beschrieben, wie Sie geplante Benachrichtigungen mit Azure Notification Hubs verwenden.
services: notification-hubs
documentationcenter: .net
keywords: Pushbenachrichtigungen,Pushbenachrichtigung,Planen von Pushbenachrichtigungen
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6b718c75-75dd-4c99-aee3-db1288235c1a
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: af0de9e8c18644f4ae200f6546c0dd0a41320f9f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61457684"
---
# <a name="how-to-send-scheduled-notifications"></a>Anleitung: Senden von geplanten Benachrichtigungen

Stellen Sie sich ein Szenario vor, bei dem Sie zu einem späteren Zeitpunkt eine Benachrichtigung senden möchten, aber keine einfache Möglichkeit zur Aktivierung des Back-End-Codes zum Senden der Benachrichtigung besteht. Notification Hubs des Standard-Tarifs unterstützen ein Feature, mit dem Sie Benachrichtigungen bis zu sieben Tage im Voraus planen können.


## <a name="schedule-your-notifications"></a>Planen Ihrer Benachrichtigungen
Verwenden Sie beim Senden einer Benachrichtigung einfach die [`ScheduledNotification`-Klasse](https://msdn.microsoft.com/library/microsoft.azure.notificationhubs.schedulednotification.aspx) im Notification Hubs SDK, wie im folgenden Beispiel veranschaulicht:

```c#
Notification notification = new AppleNotification("{\"aps\":{\"alert\":\"Happy birthday!\"}}");
var scheduled = await hub.ScheduleNotificationAsync(notification, new DateTime(2014, 7, 19, 0, 0, 0));
```

## <a name="cancel-scheduled-notifications"></a>Abbrechen geplanter Benachrichtigungen
Sie können eine zuvor geplante Benachrichtigung auch abbrechen, indem Sie die entsprechende notificationId verwenden:

```c#
await hub.CancelNotificationAsync(scheduled.ScheduledNotificationId);
```

Es gibt keine Beschränkung der Anzahl von geplanten Benachrichtigungen, die Sie senden können.

## <a name="next-steps"></a>Nächste Schritte

Arbeiten Sie die folgenden Tutorials durch:

 - [Senden von Pushbenachrichtigungen an alle registrierten Geräte](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
 - [Senden von Pushbenachrichtigungen an bestimmte Geräte](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)
 - [Senden von lokalisierten Pushbenachrichtigungen](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
 - [Senden von Pushbenachrichtigungen an bestimmte Benutzer](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) 
 - [Senden standortbasierter Pushbenachrichtigungen](notification-hubs-push-bing-spatial-data-geofencing-notification.md)
