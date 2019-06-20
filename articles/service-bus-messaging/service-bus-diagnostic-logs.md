---
title: Azure Service Bus-Diagnoseprotokolle | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Diagnoseprotokolle für Service Bus in Azure einrichten.
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 7d4cb8e55c5d1561c09cf85122550a66e3671f17
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60714146"
---
# <a name="service-bus-diagnostic-logs"></a>Service Bus-Diagnoseprotokolle

Sie können zwei Typen von Protokollen für Azure Service Bus anzeigen:
* **[Aktivitätsprotokolle](../azure-monitor/platform/activity-logs-overview.md)** . Diese Protokolle enthalten Informationen zu Vorgängen, die für einen Auftrag ausgeführt werden. Diese Protokolle sind immer aktiviert.
* **[Diagnoseprotokolle](../azure-monitor/platform/diagnostic-logs-overview.md)** . Sie können Diagnoseprotokolle konfigurieren, um umfangreichere Informationen zu sämtlichen Vorgängen innerhalb des Auftrags zu erhalten. Diagnoseprotokolle enthalten Informationen zu Aktivitäten vom Erstellen bis zum Löschen des Auftrags, einschließlich Updates und Aktivitäten während der Auftragsausführung.

## <a name="turn-on-diagnostic-logs"></a>Aktivieren der Diagnoseprotokolle

Diagnoseprotokolle sind standardmäßig deaktiviert. Führen Sie die folgenden Schritte aus, um die Diagnoseprotokolle zu aktivieren:

1.  Klicken Sie im [Azure-Portal](https://portal.azure.com) unter **Überwachung und Verwaltung** auf **Diagnoseprotokolle**.

    ![Blatt „Navigation zu Diagnoseprotokollen“](./media/service-bus-diagnostic-logs/image1.png)

2. Klicken Sie auf die Ressource, die Sie überwachen möchten.  

3.  Klicken Sie auf **Diagnose aktivieren**.

    ![Aktivieren der Diagnoseprotokolle](./media/service-bus-diagnostic-logs/image2.png)

4.  Klicken Sie für **Status** auf **Ein**.

    ![Ändern des Status der Diagnoseprotokolle](./media/service-bus-diagnostic-logs/image3.png)

5.  Legen Sie das gewünschte Archivierungsziel fest, z.B. ein Speicherkonto, einen Event Hub oder Azure Monitor-Protokolle.

6.  Speichern Sie die neuen Diagnoseeinstellungen.

Neue Einstellungen werden in etwa zehn Minuten wirksam. Danach werden die Protokolle im gewünschten Archivierungsziel auf dem Blatt **Diagnoseprotokolle** angezeigt.

Weitere Informationen zum Konfigurieren der Diagnose finden Sie in der [Übersicht über Azure-Diagnoseprotokolle](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="diagnostic-logs-schema"></a>Schema „Diagnoseprotokolle“

Alle Protokolle werden im JavaScript Object Notation (JSON)-Format gespeichert. Jeder Eintrag enthält Zeichenfolgenfelder im nachfolgend beschriebenen Format.

## <a name="operational-logs-schema"></a>Schema „Betriebsprotokolle“

Protokolle in der Kategorie **OperationalLogs** erfassen, was während Service Bus-Vorgängen passiert. Diese Protokolle erfassen insbesondere den Vorgangstyp, darunter das Erstellen von Warteschlangen, verwendete Ressourcen und den Status des Vorgangs.

JSON-Zeichenfolgen im Betriebsprotokoll enthalten Elemente, die in der folgenden Tabelle aufgeführt sind:

NAME | BESCHREIBUNG
------- | -------
ActivityId | Interne ID zur Nachverfolgung
EventName | Vorgangsname           
resourceId | Azure Resource Manager-Ressourcen-ID
SubscriptionId | Abonnement-ID
EventTimeString | Vorgangsdauer
EventProperties | Vorgangseigenschaften
Status | Vorgangsstatus
Caller | Aufrufer des Vorgangs (Azure-Portal oder Verwaltungsclient)
category | OperationalLogs

Hier ein Beispiel für eine JSON-Zeichenfolge im Betriebsprotokoll:

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>Nächste Schritte

Unter den folgenden Links erfahren Sie mehr über Service Bus:

* [Einführung in Servicebus](service-bus-messaging-overview.md)
* [Erste Schritte mit Service Bus](service-bus-dotnet-get-started-with-queues.md)
