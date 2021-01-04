---
title: Konfigurieren von Baidu Cloud Push in Azure Notification Hubs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Baidu-Einstellungen für einen Azure Notification Hub konfigurieren.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 759e35ba353f470ea3abc5f5d4182fa2b2ea0e73
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003581"
---
# <a name="deprecated-configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Veraltet: Konfigurieren von Baidu Cloud Push-Einstellungen (ADM) für einen Notification Hub im Azure Portal

In diesem Artikel wird gezeigt, wie Sie Baidu Cloud Push-Einstellungen für einen Azure Notification Hub über das Azure-Portal konfigurieren.

> [!IMPORTANT]
> Dieses Tutorial ist veraltet. 

## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie noch keinen Notification Hub erstellt haben, erstellen Sie ihn jetzt. Weitere Informationen finden Sie unter [Erstellen einer Azure Notification Hub-Instanz über das Azure-Portal](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Konfigurieren von Baidu Cloud Push
Die folgende Vorgehensweise beschreibt die Schritte zum Konfigurieren der Baidu Cloud Push-Einstellungen für einen Notification Hub:

1. Wählen Sie im Azure-Portal auf der Seite **Notification Hub** im Menü auf der linken Seite die Option **Baidu (Android China)** aus. 
2. Geben Sie den **API-Schlüssel** aus der Baidu-Konsole in das Baidu Cloud Push-Projekt ein. 
3. Geben Sie den **Geheimen Schlüssel** aus der Baidu-Konsole in das Baidu Cloud Push-Projekt ein. 
4. Wählen Sie **Speichern** aus. 

    ![Screenshot von Notification Hubs, der die Konfiguration von Pushbenachrichtigungen für Baidu (Android China) anzeigt](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Nächste Schritte
Ein Tutorial mit Schrittanleitungen zum Pushen von Benachrichtigungen an Baidu mithilfe von Azure Notification Hubs und Baidu Cloud Push finden Sie unter [Erste Schritte mit Notification Hubs mithilfe von Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
