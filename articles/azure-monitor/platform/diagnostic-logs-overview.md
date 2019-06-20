---
title: Übersicht über Azure-Diagnoseprotokolle
description: Hier erfahren Sie, worum es sich bei Azure-Diagnoseprotokollen in Azure Monitor handelt, und wie Sie mithilfe von Diagnoseprotokollen Ereignisse innerhalb einer Azure-Ressource nachvollziehen.
author: nkiest
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: nikiest
ms.subservice: logs
ms.openlocfilehash: 8902e29baa5802e3416bcda97ca59a5576d41829
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244876"
---
# <a name="overview-of-azure-diagnostic-logs"></a>Übersicht über Azure-Diagnoseprotokolle

**Diagnoseprotokolle** liefern regelmäßig umfassende Daten zum Betrieb einer Azure-Ressource. Azure Monitor stellt zwei Typen von Diagnoseprotokollen zur Verfügung:

* **Mandantenprotokolle**: Diese Protokolle stammen aus Diensten auf Mandantenebene, die außerhalb eines Azure-Abonnements vorhanden sind, z. B. Azure Active Directory-Protokolle.
* **Ressourcenprotokollen**: Diese Protokolle stammen aus Azure-Diensten, die Ressourcen in einem Azure-Abonnement bereitstellen, z. B. Netzwerksicherheitsgruppen oder Speicherkonten.

    ![Ressourcendiagnoseprotokolle im Vergleich zu anderen Protokolltypen](media/diagnostic-logs-overview/Diagnostics_Logs_vs_other_logs_v5.png)

Der Inhalt dieser Protokolle variiert je nach Azure-Dienst und Ressourcentyp. Beispielweise sind Netzwerksicherheitsgruppen-Regelzähler und Key Vault-Überwachungen zwei Typen von Diagnoseprotokollen.

Diese Protokolle unterscheiden sich vom [Aktivitätsprotokoll](activity-logs-overview.md). Das Aktivitätsprotokoll ermöglicht Einblicke in die Vorgänge, die für Ressourcen Ihres Abonnements mit dem Resource Manager durchgeführt wurden, z.B. das Erstellen eines virtuellen Computers oder das Löschen einer Logik-App. Das Aktivitätsprotokoll ist ein Protokoll auf Abonnementebene. Diagnoseprotokolle auf Ressourcenebene ermöglichen Einblicke in Vorgänge, die für die Ressource selbst durchgeführt wurden, z. B. das Abrufen eines Geheimnisses aus Key Vault.

Diese Protokolle unterscheiden sich auch von Diagnoseprotokollen auf Gastbetriebssystemebene. Diagnoseprotokolle auf Gastbetriebssystemebene werden von einem Agent erfasst, der auf einem virtuellen Computer oder einem anderen Ressourcentyp ausgeführt wird. Für Diagnoseprotokolle auf Ressourcenebene ist kein Agent erforderlich, und ressourcenspezifische Daten werden von der Azure-Plattform selbst erfasst. Für Diagnoseprotokolle auf Gastbetriebssystemebene werden Daten dagegen aus dem Betriebssystem und den Anwendungen erfasst, die auf einem virtuellen Computer ausgeführt werden.

Nicht alle Dienste unterstützen die hier beschriebenen Diagnoseprotokolle. [Dieser Artikel bietet eine Übersicht über Dienste, die Diagnoseprotokolle unterstützen.](./../../azure-monitor/platform/diagnostic-logs-schema.md)

## <a name="what-you-can-do-with-diagnostic-logs"></a>Verwenden von Diagnoseprotokollen
Hier sind einige Verwendungsmöglichkeiten für Diagnoseprotokolle:

![Logische Position von Diagnoseprotokollen](./media/diagnostic-logs-overview/Diagnostics_Logs_Actions.png)

* Diagnoseprotokolle können zur Überwachung oder manuellen Überprüfung in einem [**Speicherkonto**](../../azure-monitor/platform/archive-diagnostic-logs.md) gespeichert werden. Mithilfe der **Diagnoseeinstellungen für Ressourcen** können Sie eine Aufbewahrungsdauer (in Tagen) angeben.
* Sie können Diagnoseprotokolle zur Erfassung durch einen Drittanbieterdienst oder durch eine benutzerdefinierte Analyselösung wie Power BI [an **Event Hubs** streamen](diagnostic-logs-stream-event-hubs.md).
* Analysieren Sie sie mit [Azure Monitor](../../azure-monitor/platform/collect-azure-metrics-logs.md), wobei die Daten sofort in Azure Monitor geschrieben werden, ohne sie zuerst in den Speicher schreiben zu müssen.  

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Sie können ein Speicherkonto oder Event Hubs-Namespace verwenden, das sich nicht im gleichen Abonnement befindet wie das, das Protokolle angibt. Der Benutzer, der die Einstellung konfiguriert, benötigt den entsprechenden RBAC-Zugriff auf beide Abonnements.

> [!NOTE]
>  Sie können derzeit keine Netzwerkflussprotokolle in einem Speicherkonto archivieren, das sich hinter einem geschützten virtuellen Netzwerk befindet.

## <a name="diagnostic-settings"></a>Diagnoseeinstellungen

Ressourcendiagnoseprotokolle werden mithilfe von Diagnoseeinstellungen für Ressourcen konfiguriert. Mandantendiagnoseprotokolle werden mithilfe einer Diagnoseeinstellung für Mandanten konfiguriert. Mit **Diagnoseeinstellungen** für eine Dienststeuerung können Sie Folgendes festlegen:

* Wohin Diagnoseprotokolle und Metriken gesendet werden (Speicherkonto, Event Hubs und/oder Azure Monitor).
* Welche Protokollkategorien gesendet werden, und ob auch Metrikdaten gesendet werden.
* Wie lange die einzelnen Protokollkategorien in einem Speicherkonto beibehalten werden sollen.
    - Wenn für die Beibehaltungsdauer 0 Tage festgelegt sind, bedeutet dies, dass Protokolle unbegrenzt beibehalten werden. Andernfalls kann als Wert die Anzahl von Tagen (1 bis 365) festgelegt werden.
    - Wenn Aufbewahrungsrichtlinien festgelegt wurden, aber das Speichern von Protokollen in einem Speicherkonto deaktiviert ist (etwa, wenn nur die Optionen „Event Hubs“ oder „Log Analytics“ ausgewählt sind), werden die Aufbewahrungsrichtlinien ignoriert.
    - Aufbewahrungsrichtlinien werden pro Tag angewendet, sodass Protokolle am Ende eines Tages (UTC) ab dem Tag, der nun außerhalb der Aufbewahrungsrichtlinie liegt, gelöscht werden. Beispiel: Wenn Sie eine Aufbewahrungsrichtlinie für einen Tag verwenden, werden heute am Anfang des Tages die Protokolle von vorgestern gelöscht. Der Löschvorgang beginnt um Mitternacht (UTC), jedoch kann es bis zu 24 Stunden dauern, bis die Protokolle aus Ihrem Speicherkonto gelöscht werden.

Diese Einstellungen können über die Diagnoseeinstellungen im Portal mit Azure PowerShell, CLI-Befehlen oder der [Azure Monitor-REST-API](https://docs.microsoft.com/rest/api/monitor/) konfiguriert werden.

> [!NOTE]
> Das Senden mehrdimensionaler Metriken über die Diagnoseeinstellungen wird derzeit nicht unterstützt. Metriken mit Dimensionen werden als vereinfachte eindimensionale Metriken exportiert und dimensionswertübergreifend aggregiert.
>
> *Beispiel*: Die Metrik „Eingehende Nachrichten“ in einem Event Hub kann auf Warteschlangenebene untersucht und in einem Diagramm dargestellt werden. Wenn Sie die Metrik allerdings über die Diagnoseeinstellungen exportieren, umfasst die Darstellung alle eingehenden Nachrichten für alle Warteschlangen im Event Hub.
>
>

## <a name="supported-services-categories-and-schemas-for-diagnostic-logs"></a>Unterstützte Dienste, Kategorien und Schemas für Diagnoseprotokolle

[In diesem Artikel](../../azure-monitor/platform/diagnostic-logs-schema.md) finden Sie eine vollständige Liste der unterstützten Dienste, Protokollkategorien und Schemas, die von diesen Diensten verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

* [Streamen von Diagnoseprotokollen für Ressourcen an **Event Hubs**](diagnostic-logs-stream-event-hubs.md)
* Ändern der Diagnoseeinstellungen für Ressourcen mithilfe der Azure Monitor-REST-API ([Service Diagnostic Settings](https://docs.microsoft.com/rest/api/monitor/) (Diagnoseeinstellungen für Dienste))
* [Analysieren von Protokollen aus Azure Storage mit Azure Monitor](collect-azure-metrics-logs.md)
