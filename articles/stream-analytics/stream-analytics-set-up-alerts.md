---
title: Einrichten von Überwachungswarnungen für Azure Stream Analytics-Aufträge
description: In diesem Artikel wird beschrieben, wie Sie die Überwachung und Warnungen für Azure Stream Analytics-Aufträge mithilfe des Azure-Portals einrichten.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.custom: contperf-fy21q1
ms.date: 06/21/2019
ms.openlocfilehash: 8d591bb9c4b2ef8100946082f19115b99b30dde1
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027491"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Einrichten von Warnungen für Azure Stream Analytics-Aufträge

Es ist wichtig, einen Azure Stream Analytics-Auftrag zu überwachen, um sicherzustellen, dass der Auftrag fortlaufend und problemlos ausgeführt wird. In diesem Artikel wird beschrieben, wie Sie Warnungen für gängige Szenarien einrichten, die überwacht werden sollen. 

Sie können Regeln für Metriken zu Vorgangsprotokolldaten entweder über das Portal oder [programmgesteuert](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) definieren.

## <a name="set-up-alerts-in-the-azure-portal"></a>Einrichten von Warnungen im Azure-Portal
### <a name="get-alerted-when-a-job-stops-unexpectedly"></a>Empfangen von Warnungen beim unerwartetem Beenden eines Auftrags

Im folgenden Beispiel wird veranschaulicht, wie Warnungen für den Fall eingerichtet werden, dass ein Auftrag in den fehlerhaften Zustand übergeht. Die Einrichtung dieser Warnung wird für alle Aufträge empfohlen.

1. Öffnen Sie im Azure-Portal den Stream Analytics-Auftrag, für den Sie eine Warnung erstellen möchten.

2. Navigieren Sie auf der Seite **Auftrag** zum Abschnitt **Überwachung**.  

3. Klicken Sie erst auf **Metriken** und dann auf **Neue Warnungsregel**.

   ![Konfigurieren von Stream Analytics-Warnungen im Azure-Portal](./media/stream-analytics-set-up-alerts/stream-analytics-set-up-alerts.png)  

4. Der Name des Stream Analytics-Auftrags sollte automatisch unter **RESSOURCE** angezeigt werden. Klicken Sie auf **Bedingung hinzufügen**, und wählen Sie **All Administrative operations** (Alle Verwaltungsvorgänge) unter **Signallogik konfigurieren** aus.

   ![Auswählen eines Signalnamens für die Stream Analytics-Warnung](./media/stream-analytics-set-up-alerts/stream-analytics-condition-signal.png)  

5. Ändern Sie unter **Signallogik konfigurieren** den Wert für **Ereignisstufe** in **Alle** und den Wert für **Status** in **Fehler**. Lassen Sie das Feld **Ereignis initiiert von** leer, und klicken Sie auf **Fertig**.

   ![Konfigurieren der Signallogik für die Stream Analytics-Warnung](./media/stream-analytics-set-up-alerts/stream-analytics-configure-signal-logic.png) 

6. Wählen Sie eine vorhandene Aktionsgruppe aus, oder erstellen Sie eine neue Gruppe. In diesem Beispiel wurde die neue Aktionsgruppe **TIDashboardGroupActions** mit der Aktion **E-Mails** erstellt, die eine E-Mail an Benutzer mit der Azure Resource Manager-Rolle **Besitzer** sendet.

   ![Einrichten einer Warnung für einen Azure Stream Analytics-Auftrag](./media/stream-analytics-set-up-alerts/stream-analytics-add-group-email-action.png)

7. Unter **RESSOURCE**, **BEDINGUNG** und **AKTIONSGRUPPEN** sollte jeweils ein Eintrag angezeigt werden. Beachten Sie, dass Warnungen nur ausgelöst werden können, wenn die definierten Bedingungen erfüllt sind. Sie können beispielsweise für eine Metrik alle fünf Minuten den Durchschnittswert der letzten 15 Minuten messen.

   ![Der Screenshot zeigt das Dialogfeld „Regel erstellen“ mit RESSOURCE, BEDINGUNG und AKTIONSGRUPPE.](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule-2.png)

   Fügen Sie unter **WARNUNGSDETAILS** den **Namen der Warnungsregel**, eine **Beschreibung** und Ihre **Ressourcengruppe** hinzu, und klicken Sie auf **Warnungsregel erstellen**, um die Regel für den Stream Analytics-Auftrag zu erstellen.

   ![Der Screenshot zeigt das Dialogfeld „Regel erstellen“ mit WARNUNGSDETAILS.](./media/stream-analytics-set-up-alerts/stream-analytics-create-alert-rule.png)
   
## <a name="scenarios-to-monitor"></a>Szenarien für die Überwachung

Zur Überwachung der Leistung des Stream Analytics-Auftrags werden die folgenden Warnungen empfohlen. Diese Metriken sollten in den letzten 5 Minuten einmal pro Minute ausgewertet werden.

|Metrik|Bedingung|Zeitaggregation|Schwellenwert|Korrekturmaßnahmen|
|-|-|-|-|-|
|Nutzung der Speichereinheit in %|Größer als|Maximum|80|Die SU-Nutzung in Prozent lässt sich durch mehrere Faktoren erhöhen. Sie können eine Skalierung mit Abfrageparallelisierung durchführen oder die Anzahl der Streamingeinheiten erhöhen. Weitere Informationen finden Sie unter [Nutzen der Parallelisierung von Abfragen in Azure Stream Analytics](stream-analytics-parallelization.md).|
|Laufzeitfehler|Größer als|Gesamt|0|Überprüfen Sie die Aktivitäts- oder Ressourcenprotokolle, und nehmen Sie entsprechende Änderungen an den Eingaben, der Abfrage oder den Ausgaben vor.|
|Wasserzeichenverzögerung|Größer als|Maximum|Wenn der durchschnittliche Wert dieser Metrik innerhalb der letzten 15 Minuten größer ist als die Toleranz für Eingangsverzögerung (in Sekunden). Wenn Sie die Toleranz für Eingangsverzögerung nicht geändert haben, ist der Standardwert auf 5 Sekunden festgelegt.|Versuchen Sie, die Anzahl der Streamingeinheiten zu erhöhen oder die Abfrage zu parallelisieren. Weitere Informationen zu Streamingeinheiten finden Sie unter [Übersicht über Streamingeinheiten und Informationen zu Anpassungen](stream-analytics-streaming-unit-consumption.md#how-many-sus-are-required-for-a-job). Weitere Informationen zur Parallelisierung der Abfrage finden Sie unter [Nutzen der Parallelisierung von Abfragen in Azure Stream Analytics](stream-analytics-parallelization.md).|
|Eingabefehler bei Deserialisierung|Größer als|Gesamt|0|Überprüfen Sie die Aktivitäts- oder Ressourcenprotokolle, und nehmen Sie entsprechende Änderungen an der Eingabe vor. Weitere Informationen zu Ressourcenprotokollen finden Sie unter [Problembehandlung bei Azure Stream Analytics mit Ressourcenprotokollen](stream-analytics-job-diagnostic-logs.md)|

## <a name="next-steps"></a>Nächste Schritte

* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](/stream-analytics-query/stream-analytics-query-language-reference)