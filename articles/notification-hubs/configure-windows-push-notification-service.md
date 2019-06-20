---
title: Konfigurieren von Windows Push Notification Service in Azure Notification Hubs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Windows Push Notification Service-Einstellungen für einen Azure Notification Hub konfigurieren.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: c3e3f1e7df5c90c690756375ff1e1b0350c72714
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60240278"
---
# <a name="configure-windows-push-notification-service-wns-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurieren von Windows Push Notification Service-Einstellungen (WNS) für einen Notification Hub im Azure-Portal
In diesem Artikel wird gezeigt, wie Sie Windows Push Notification Service-Einstellungen (WNS) für einen Azure Notification Hub über das Azure-Portal konfigurieren.  

## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie noch keinen Notification Hub erstellt haben, erstellen Sie ihn jetzt. Weitere Informationen finden Sie unter [Erstellen einer Azure Notification Hub-Instanz über das Azure-Portal](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Konfigurieren von Windows Push Notification Service (WNS)

Die folgende Vorgehensweise beschreibt die Schritte zum Konfigurieren der Windows Push Notification Service-Einstellungen (WNS) für einen Notification Hub: 

1. Wählen Sie im Azure-Portal auf der Seite **Notification Hub** im Menü auf der linken Seite die Option **Windows (WNS)** aus.
2. Geben Sie Werte für **Paket-SID** und **Sicherheitsschlüssel** ein.
3. Wählen Sie **Speichern** aus.

   ![Screenshot, der die Felder „Paket-SID“ und „Sicherheitsschlüssel“ zeigt](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Nächste Schritte
Ein Tutorial mit Schrittanleitungen zum Pushen von Benachrichtigungen an Anwendungen für Universelle Windows-Plattformen mithilfe von Azure Notification Hubs und Windows Push Notification Service (WNS) finden Sie unter [Senden von Benachrichtigungen an UWP-Apps mithilfe von Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


