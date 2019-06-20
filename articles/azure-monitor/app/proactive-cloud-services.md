---
title: Warnung bei Problemen in Azure Cloud Services mit der Azure-Diagnoseintegration in Azure Application Insights | Microsoft-Dokumentation
description: Überwachen Sie Probleme wie Startfehler, Abstürze und Schleifen bei der Wiederverwendung von Rollen in Azure Cloud Services mit Azure Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/07/2018
ms.reviewer: harelbr
ms.author: mbullwin
ms.openlocfilehash: 219ba632d7688f1a428378309828b689698d2fe5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409498"
---
# <a name="alert-on-issues-in-azure-cloud-services-using-the-azure-diagnostics-integration-with-azure-application-insights"></a>Warnung bei Problemen in Azure Cloud Services mit der Azure-Diagnoseintegration in Azure Application Insights

In diesem Artikel wird beschrieben, wie Sie Warnungsregeln für die Überwachung von Problemen wie Startfehler, Abstürze und Schleifen bei der Wiederverwendung von Rollen in Azure Cloud Services (Web- und Workerrollen) einrichten.

Die in diesem Artikel beschriebene Methode basiert auf der [Integration der Azure-Diagnose in Application Insights](https://azure.microsoft.com/blog/azure-diagnostics-integration-with-application-insights/) und der kürzlich veröffentlichten Funktion der [Protokollwarnungen für Application Insights](https://azure.microsoft.com/blog/log-alerts-for-application-insights-preview/).

## <a name="define-a-base-query"></a>Definieren einer Basisabfrage

Im ersten Schritt wird eine Basisabfrage definiert, die die Ereignisse aus dem Windows-Ereignisprotokoll vom Windows Azure-Kanal abruft, die als Datensätze für die Ablaufverfolgung in Application Insights erfasst werden.
Diese Datensätze können zur Erkennung einer Vielzahl von Problemen in Azure Cloud Services verwendet werden, wie z.B. Startfehler, Laufzeitfehler und Wiederverwendungsschleifen.

> [!NOTE]
> Die folgende Basisabfrage prüft in einem Zeitfenster von 30 Minuten auf Probleme und geht von einer Wartezeit von 10 Minuten beim Einlesen der Telemetriedatensätze aus. Diese Standardwerte können bei Bedarf konfiguriert werden.

```
let window = 30m;
let endTime = ago(10m);
let EventLogs = traces
| where timestamp > endTime - window and timestamp < endTime
| extend channel = tostring(customDimensions.Channel), eventId = tostring(customDimensions.EventId)
| where channel == 'Windows Azure' and isnotempty(eventId)
| where tostring(customDimensions.DeploymentName) !contains 'deployment' // discard records captured from local machines
| project timestamp, channel, eventId, message, cloud_RoleInstance, cloud_RoleName, itemCount;
```

## <a name="check-for-specific-event-ids"></a>Überprüfen auf bestimmte Ereignis-IDs

Nach dem Abrufen der Ereignisse aus dem Windows-Ereignisprotokoll können bestimmte Probleme erkannt werden, indem die entsprechenden Ereignis-IDs und Nachrichteneigenschaften überprüft werden (siehe folgende Beispiele).
Kombinieren Sie einfach die obige Basisabfrage mit einer der folgenden Abfragen, und verwenden Sie diese kombinierte Abfrage bei der Definition der Protokollwarnungsregel.

> [!NOTE]
> In den folgenden Beispielen wird ein Problem erkannt, wenn mehr als drei Ereignisse im analysierten Zeitfenster gefunden werden. Diese Standardeinstellung kann konfiguriert werden, um die Empfindlichkeit der Warnungsregel zu ändern.

```
// Detect failures in the OnStart method
EventLogs
| where eventId == '2001'
| where message contains '.OnStart()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures during runtime
EventLogs
| where eventId == '2001'
| where message contains '.Run()'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect failures when running a startup task
EventLogs
| where eventId == '1000'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

```
// Detect recycle loops
EventLogs
| where eventId == '1006'
| summarize Failures = sum(itemCount) by cloud_RoleInstance, cloud_RoleName
| where Failures > 3
```

## <a name="create-an-alert"></a>Erstellen einer Warnung

Wechseln Sie im Navigationsmenü Ihrer Application Insights-Ressource zu **Warnungen**, und wählen Sie dann **Neue Warnungsregel** aus.

![Screenshot „Regel erstellen“](./media/proactive-cloud-services/001.png)

Klicken Sie im Fenster **Regel erstellen** im Abschnitt **Alarmbedingung definieren** auf **Kriterien hinzufügen**, und wählen Sie dann **Benutzerdefinierte Protokollsuche** aus.

![Screenshot der Definition von Bedingungskriterien für die Warnung](./media/proactive-cloud-services/002.png)

Fügen Sie im Feld **Suchabfrage** die kombinierte Abfrage ein, die Sie im vorherigen Schritt vorbereitet haben.

Fahren Sie dann mit dem Feld **Schwellenwert** fort, und setzen Sie den Wert auf 0, wobei Sie optional die Felder **Zeitraum** und **Häufigkeit** anpassen können.
Klicken Sie auf **Fertig**.

![Screenshot der Abfrage zum Konfigurieren der Signallogik](./media/proactive-cloud-services/003.png)

Geben Sie im Abschnitt **Warnungsdetails definieren** einen **Namen** und eine **Beschreibung** für die Warnungsregel ein, und legen Sie deren **Schweregrad** fest.
Stellen Sie außerdem sicher, dass die Schaltfläche **Regel beim Erstellen aktivieren** auf **Ja** gesetzt ist.

![Screenshot „Warnungsdetails“](./media/proactive-cloud-services/004.png)

Im Abschnitt **Aktionsgruppe definieren** können Sie eine vorhandene **Aktionsgruppe** auswählen oder eine neue erstellen.
Sie können wählen, ob die Aktionsgruppe mehrere Aktionen unterschiedlicher Art enthalten soll.

![Screenshot „Aktionsgruppe“](./media/proactive-cloud-services/005.png)

Nachdem Sie die Aktionsgruppe definiert haben, bestätigen Sie Ihre Änderungen, und klicken Sie auf **Warnungsregel erstellen**.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur automatischen Erkennung finden Sie hier:

[Fehleranomalien](../../azure-monitor/app/proactive-failure-diagnostics.md)
[Arbeitsspeicherverluste](../../azure-monitor/app/proactive-potential-memory-leak.md)
[Leistungsanomalien](../../azure-monitor/app/proactive-performance-diagnostics.md)

