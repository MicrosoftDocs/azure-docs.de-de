---
title: Konfigurieren von Google Firebase Cloud Messaging in Azure Notification Hubs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie einen Azure Notification Hub mit Google Firebase Cloud Messaging-Einstellungen konfigurieren.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 0da103c11e2412108535ca322917632f5d95559d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003564"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurieren von Google Firebase-Einstellungen für einen Notification Hub im Azure Portal

In diesem Artikel wird gezeigt, wie Sie Google Firebase Cloud Messaging-Einstellungen (FCM) für einen Azure Notification Hub über das Azure Portal konfigurieren.  

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie noch keinen Notification Hub erstellt haben, erstellen Sie ihn jetzt. Weitere Informationen finden Sie unter [Erstellen einer Azure Notification Hub-Instanz über das Azure-Portal](create-notification-hub-portal.md).

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Konfigurieren von Google Firebase Cloud Messaging (FCM)

Die folgende Vorgehensweise beschreibt die Schritte zum Konfigurieren der Google Firebase Cloud Messaging-Einstellungen (FCM) für einen Notification Hub:

1. Wählen Sie im Azure-Portal auf der Seite **Notification Hub** im Menü auf der linken Seite die Option **Google (GCM/FCM)** aus.
2. Fügen Sie den **API-Schlüssel** für das FCM-Projekt ein, das Sie zuvor gespeichert haben.
3. Wählen Sie **Speichern** aus.

   ![Screenshot, der die Konfiguration von Notification Hubs für Google FCM zeigt](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Nächste Schritte

Ein Tutorial mit Schrittanleitungen zum Senden von Benachrichtigungen an Anwendungen für Android-Geräte mithilfe von Azure Notification Hubs und Google Firebase Cloud Messaging finden Sie unter [Senden von Pushbenachrichtigungen an Android-Geräte mit Notification Hubs und Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).
