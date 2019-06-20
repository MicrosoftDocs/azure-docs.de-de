---
title: Konfigurieren von Baidu Cloud Push in Azure Notification Hubs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Baidu-Einstellungen für einen Azure Notification Hub konfigurieren.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9942b1d4d8b5d538f5150a36e596753282039be7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60234266"
---
# <a name="configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurieren von Baidu Cloud Push-Einstellungen (ADM) für einen Notification Hub im Azure Portal
In diesem Artikel wird gezeigt, wie Sie Baidu Cloud Push-Einstellungen für einen Azure Notification Hub über das Azure-Portal konfigurieren. 

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
