---
title: Starten eines Azure Stream Analytics-Auftrags
description: In diesem Artikel wird beschrieben, wie Sie einen Stream Analytics-Auftrag über das Azure-Portal, PowerShell und Visual Studio starten.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/03/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f89b23d0c20aafeeb07a744545208f6056efa2c9
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130815"
---
# <a name="how-to-start-an-azure-stream-analytics-job"></a>Starten eines Azure Stream Analytics-Auftrags

Sie können Ihren Azure Stream Analytics-Auftrag über das Azure-Portal, Visual Studio oder PowerShell starten. Wenn Sie einen Auftrag starten, wählen Sie eine Uhrzeit aus, zu der der Auftrag mit dem Erstellen der Ausgabe beginnen soll. Azure-Portal, Visual Studio und PowerShell weisen jeweils unterschiedliche Methoden zum Festlegen der Startzeit auf. Diese Methoden werden nachstehend beschrieben.

## <a name="start-options"></a>Startoptionen
Zum Starten eines Auftrags stehen die drei folgenden Optionen zur Verfügung. Beachten Sie, dass alle unten aufgeführten Zeiten die in [TIMESTAMP BY](/stream-analytics-query/timestamp-by-azure-stream-analytics) angegebenen Zeiten sind. Wenn „TIMESTAMP BY“ nicht angegeben ist, wird die Eingangszeit verwendet.
* **Jetzt:** Legt den Startzeitpunkt des Ausgabeereignisstreams als den Zeitpunkt des Auftragsstarts fest. Wenn ein temporaler Operator verwendet wird (z. B. Zeitfenster, „LAG“ oder „JOIN“), werden in Azure Stream Analytics automatisch die in der Vergangenheit liegenden Daten in der Eingabequelle durchsucht. Wenn Sie beispielsweise einen Auftrag mit der Option „Jetzt“ starten und in der Abfrage ein rollierendes 5-Minuten-Fenster verwendet wird, werden in Azure Stream Analytics Daten von vor 5 Minuten in der Eingabe gesucht.
Das erste mögliche Ausgabeereignis hat einen Zeitstempel, der dem aktuellen Zeitpunkt entspricht oder zeitlich nach diesem liegt. ASA stellt sicher, dass alle Eingabeereignisse, die logisch zur Ausgabe beitragen können, berücksichtigt werden. Beispielsweise werden keine partiellen Aggregate im Fenstermodus generiert. Es wird immer der vollständige aggregierte Wert generiert.

* **Benutzerdefiniert** : Sie können den Startzeitpunkt der Ausgabe auswählen. Wie bei der Option **Jetzt** werden in Azure Stream Analytics automatisch die Daten vor diesem Zeitpunkt gelesen, wenn ein temporaler Operator verwendet wird. 

* **Zeitpunkt der letzten Beendigung:** Diese Option ist verfügbar, wenn der Auftrag bereits gestartet wurde, jedoch entweder manuell oder durch einen Fehler beendet wurde. Bei Auswahl dieser Option wird in Azure Stream Analytics der letzte Ausgabezeitpunkt verwendet, um den Auftrag neu zu starten, sodass keine Daten verloren gehen. Wie bei den anderen Optionen werden in Azure Stream Analytics automatisch die Daten vor diesem Zeitpunkt gelesen, wenn ein temporaler Operator verwendet wird. Da für mehrere Eingabepartitionen möglicherweise eine unterschiedliche Zeit festgelegt ist, wird die früheste Beendigungszeit aller Partitionen verwendet, daher kann die Ausgabe Duplikate enthalten. Weitere Informationen zur Exactly-Once-Verarbeitung finden Sie auf der Seite [Garantien zur Ereignisbereitstellung](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics).


## <a name="azure-portal"></a>Azure-Portal

Navigieren Sie im Azure-Portal zu Ihrem Auftrag, und wählen Sie auf der Übersichtsseite **Starten** aus. Wählen Sie eine **Startzeit der Auftragsausgabe** und anschließend **Starten** aus.

Wählen Sie eine der Optionen für **Startzeit der Auftragsausgabe** aus. Möglich sind die Optionen *Jetzt* , *Benutzerdefiniert* und, wenn der Auftrag zuvor ausgeführt wurde, *Zeitpunkt der letzten Beendigung*. Weitere Informationen zu diesen Optionen finden Sie weiter oben.

## <a name="visual-studio"></a>Visual Studio

Wählen Sie in der Auftragsansicht die Schaltfläche mit dem grünen Pfeil aus, um den Auftrag zu starten. Legen Sie **Startmodus der Auftragsausgabe** fest, und wählen Sie **Start** aus. Der Auftragsstatus wird in **Wird ausgeführt** geändert.

Es gibt drei Optionen für **Startmodus der Auftragsausgabe** : *JobStartTime* , *CustomTime* und *LastOutputEventTime*. Wenn diese Eigenschaft nicht angegeben wird, lautet der Standardwert *JobStartTime*. Weitere Informationen zu diesen Optionen finden Sie weiter oben.


## <a name="powershell"></a>PowerShell

Verwenden Sie das folgende Cmdlet, um Ihren Auftrag mithilfe von PowerShell zu starten:

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

Es gibt drei Optionen für **OutputStartMode** : *JobStartTime* , *CustomTime* und *LastOutputEventTime*. Wenn diese Eigenschaft nicht angegeben wird, lautet der Standardwert *JobStartTime*. Weitere Informationen zu diesen Optionen finden Sie weiter oben.

Weitere Informationen zum Cmdlet `Start-AzStreamAnalyitcsJob` finden Sie in der [Referenz zu Start-AzStreamAnalyticsJob](/powershell/module/az.streamanalytics/start-azstreamanalyticsjob).

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe des Azure-Portals](stream-analytics-quick-create-portal.md)
* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mit Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe des Azure Stream Analytics-Tools für Visual Studio](stream-analytics-quick-create-vs.md)