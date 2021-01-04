---
title: Autoskalierung von Stream Analytics-Aufträgen
description: Dieser Artikel beschreibt die Autoskalierung von Stream Analytics-Aufträgen, basierend auf einem vordefinierten Zeitplan oder auf Werten von Auftragsmetriken.
author: sidramadoss
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/03/2020
ms.openlocfilehash: 8e5bcdaeaf1ec99387a708199f4353736b6bc60f
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129846"
---
# <a name="autoscale-stream-analytics-jobs-using-azure-automation"></a>Autoskalierung von Stream Analytics-Aufträgen mithilfe von Azure Automation

Sie können die Kosten Ihrer Stream Analytics-Aufträge optimieren, indem Sie die Autoskalierung konfigurieren. Durch die Autoskalierung werden die Streamingeinheiten (SUs) Ihres Auftrags entsprechend der Änderung Ihrer Eingabeauslastung erhöht oder verringert. Anstatt übermäßig Ressourcen für ihren Auftrag bereitzustellen, können Sie nach Bedarf hoch- oder herunterskalieren. Es gibt zwei Möglichkeiten, um die Autoskalierung für Ihre Aufträge zu konfigurieren:
1. **Definieren Sie vorab einen Zeitplan** , wenn Sie eine vorhersagbare Eingabeauslastung besitzen. Wenn Sie beispielsweise eine höhere Rate von Eingabeereignissen während des Tages erwarten und möchten, dass Ihr Auftrag mit mehr SUs ausgeführt wird.
2. **Lösen Sie die Hoch- bzw. Herunterskalierung auf Grundlage von Auftragsmetriken aus** , wenn Sie über keine vorhersagbare Eingabeauslastung verfügen. Sie können die Anzahl der SUs basierend auf Ihren Auftragsmetriken, z. B. die Anzahl der Eingabeereignisse oder der im Rückstand befindlichen Eingabeereignisse, dynamisch ändern.

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit der Konfiguration der Autoskalierung für Ihren Auftrag beginnen, führen Sie die folgenden Schritte aus.
1. Ihr Auftrag ist für die Ausführung in einer [parallelen Topologie](./stream-analytics-parallelization.md) optimiert. Wenn Sie die Skalierung Ihres Auftrags während seiner Ausführung ändern können, verfügt der Auftrag über eine parallele Topologie und kann für die Autoskalierung konfiguriert werden.
2. [Erstellen Sie ein Azure Automation-Konto](../automation/automation-create-standalone-account.md) mit der aktivierten Option „RunAsAccount“ (Ausführendes Konto). Dieses Konto muss über Berechtigungen zum Verwalten Ihrer Stream Analytics-Aufträge verfügen.

## <a name="set-up-azure-automation"></a>Einrichten von Azure Automation
### <a name="configure-variables"></a>Konfigurieren von Variablen
Fügen Sie die folgenden Variablen im Azure Automation-Konto hinzu. Diese Variablen werden in den Runbooks verwendet, die in den nächsten Schritten beschrieben werden.

| Name | type | Wert |
| --- | --- | --- |
| **jobName** | String | Benennen Sie Ihren Stream Analytics-Auftrag, den Sie autoskalieren möchten. |
| **resourceGroupName** | String | Name der Ressourcengruppe, in der sich Ihr Auftrag befindet. |
| **aboId** | String | Die ID des Abonnements, in dem sich Ihr Auftrag befindet. |
| **increasedSU** | Integer | Der höhere SU-Wert, auf den Ihr Auftrag in einem Zeitplan skaliert werden soll. Bei diesem Wert muss es sich um eine der gültigen SU-Optionen handeln, die in den Einstellungen für **Skalieren** Ihres Auftrags während der Ausführung angezeigt werden. |
| **decreasedSU** | Integer | Der niedrigere SU-Wert, auf den Ihr Auftrag in einem Zeitplan skaliert werden soll. Bei diesem Wert muss es sich um eine der gültigen SU-Optionen handeln, die in den Einstellungen für **Skalieren** Ihres Auftrags während der Ausführung angezeigt werden. |
| **maxSU** | Integer | Der maximale SU-Wert, auf den Ihr Auftrag in Schritten skaliert werden soll, wenn die bei Last autoskaliert wird. Bei diesem Wert muss es sich um eine der gültigen SU-Optionen handeln, die in den Einstellungen für **Skalieren** Ihres Auftrags während der Ausführung angezeigt werden. |
| **minSU** | Integer | Der minimale SU-Wert, auf den Ihr Auftrag in Schritten skaliert werden soll, wenn bei Last autoskaliert wird. Bei diesem Wert muss es sich um eine der gültigen SU-Optionen handeln, die in den Einstellungen für **Skalieren** Ihres Auftrags während der Ausführung angezeigt werden. |

![Hinzufügen von Variablen in Azure Automation](./media/autoscale/variables.png)

### <a name="create-runbooks"></a>Erstellen von Runbooks
Im nächsten Schritt werden zwei PowerShell-Runbooks erstellt. Eins für Hochskalierungsvorgänge, das andere für Herunterskalierungsvorgänge.
1. Wechseln Sie in Ihrem Azure Automation-Konto zu **Runbooks** unter **Prozessautomatisierung** , und wählen Sie **Runbook erstellen** aus.
2. Nennen Sie das erste Runbook *ScaleUpRunbook* , wobei Sie den Typ auf „PowerShell“ festlegen. Verwenden Sie das [PowerShell-Skript „ScaleUpRunbook“](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleUpRunbook.ps1), das auf GitHub verfügbar ist. Speichern und veröffentlichen Sie es.
3. Erstellen Sie ein weiteres Runbook namens Namen *ScaleDownRunbook* mit dem Typ „PowerShell“. Verwenden Sie das [PowerShell-Skript „ScaleDownRunbook“](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/ScaleDownRunbook.ps1), das auf GitHub verfügbar ist. Speichern und veröffentlichen Sie es.

![Autoskalierung von Runbooks in Azure Automation](./media/autoscale/runbooks.png)

Sie verfügen jetzt über Runbooks, die automatisch Vorgänge zum Hoch- bzw. Herunterskalieren für Ihren Stream Analytics-Auftrag auslösen können. Diese Runbooks können mithilfe eines vordefinierten Zeitplans ausgelöst oder basierend auf Auftragsmetriken dynamisch festgelegt werden.

## <a name="autoscale-based-on-a-schedule"></a>Automatisches Skalieren nach Zeitplan
Azure Automation ermöglicht es Ihnen, einen Zeitplan zum Auslösen Ihrer Runbooks zu konfigurieren.
1. Wählen Sie in Ihrem Automation-Konto unter **Freigegebene Ressourcen** die Option **Zeitpläne** aus. Wählen Sie dann **Zeitplan hinzufügen** aus.
2. Sie können beispielsweise zwei Zeitpläne erstellen. Einen, der darstellt, wann Ihr Auftrag hochskaliert werden soll, und einen anderen, der darstellt, wann Ihr Auftrag herunterskaliert werden soll. Sie können für diese Zeitpläne eine Wiederholungsserie definieren.

   ![Zeitpläne in Azure Automation](./media/autoscale/schedules.png)

3. Öffnen Sie Ihr **ScaleUpRunbook** , und wählen Sie dann **Zeitpläne** unter **Ressourcen** aus. Anschließend können Sie Ihr Runbook mit einem Zeitplan verknüpfen, den Sie in den vorherigen Schritten erstellt haben. Sie können mehrere Zeitpläne mit demselben Runbook verknüpfen. Dies kann hilfreich sein, wenn Sie denselben Skalierungsvorgang zu unterschiedlichen Tageszeiten ausführen möchten.

![Planen von Runbooks in Azure Automation](./media/autoscale/schedulerunbook.png)

1. Wiederholen Sie den vorherigen Schritt für das **ScaleDownRunbook**.

## <a name="autoscale-based-on-load"></a>Autoskalierung nach Auslastung
Möglicherweise gibt es Fälle, in denen Sie die Eingangslast nicht vorhersagen können. In solchen Fällen ist es besser, in Schritten innerhalb von minimalen und maximalen Grenzwerten hoch- oder herunterzuskalieren. Sie können Warnungsregeln in Ihren Stream Analytics-Aufträgen so konfigurieren, dass Runbooks ausgelöst werden, wenn Auftragsmetriken einen Schwellenwert über- oder unterschreiten.
1. Erstellen Sie in Ihrem Azure Automation-Konto zwei weitere ganzzahlige (integer) Variablen namens **minSU** und **maxSU**. Hiermit werden die Grenzen festgelegt, innerhalb derer Ihr Auftrag in Schritten skaliert wird.
2. Erstellen Sie zwei neue Runbooks. Sie können das [PowerShell-Skript „StepScaleUp“](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleUp.ps1) verwenden, das die SUs Ihres Auftrags in Schritten bis zum Wert von **maxSU** erhöht. Sie können auch das [PowerShell-Skript „StepScaleDown“](https://github.com/Azure/azure-stream-analytics/blob/master/Autoscale/StepScaleDown.ps1) verwenden, das die SUs Ihres Auftrags in Schritten bis zum Erreichen des Werts von **minSU** verringert. Alternativ können Sie die Runbooks aus dem vorherigen Abschnitt verwenden, wenn Sie über bestimmte SU-Werte verfügen, auf die Sie skalieren möchten.
3. Wählen Sie in Ihrem Stream Analytics-Auftrag **Warnungsregeln** unter **Überwachung** aus. 
4. Erstellen Sie zwei Aktionsgruppen. Eine, die für das Hochskalieren verwendet werden soll, und eine andere für das Herunterskalieren. Wählen Sie **Aktionen verwalten** aus, und klicken Sie dann auf **Aktionsgruppe hinzufügen**. 
5. Füllen Sie die erforderlichen Felder aus. Wählen Sie **Automation-Runbook** aus, wenn Sie den **Aktionstyp** auswählen. Wählen Sie das Runbook aus, das Sie auslösen möchten, wenn die Warnung ausgelöst wird. Erstellen Sie dann die Aktionsgruppe.

   ![Erstellen einer Aktionsgruppe](./media/autoscale/create-actiongroup.png)
6. Erstellen Sie [**Neue Warnungsregel**](./stream-analytics-set-up-alerts.md#set-up-alerts-in-the-azure-portal) in Ihrem Auftrag. Geben Sie eine Bedingung basierend auf einer Metrik Ihrer Wahl an. [*Eingabeereignisse* , *SU % Auslastung* oder *Eingabeereignisse mit Rückstand*](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics) sind empfohlene Metriken zum Definieren der Autoskalierungslogik. Es wird ferner empfohlen, eine *Aggregationsgranularität* und *Häufigkeit der Auswertungs* von 1 Minute zu verwenden, wenn Hochskalierungsvorgänge ausgelöst werden. Dadurch wird sichergestellt, dass Ihr Auftrag über ausreichende Ressourcen verfügt, um mit großen Spitzen des Eingabevolumens umzugehen.
7. Wählen Sie die im letzten Schritt erstellte Aktionsgruppe aus, und erstellen Sie die Warnung.
8. Wiederholen Sie die Schritte 2 bis 4 für alle zusätzlichen Skalierungsvorgänge, die Sie basierend auf der Bedingung von Auftragsmetriken ausführen möchten.

Es ist eine bewährte Methode, Skalierungstests auszuführen, bevor Sie Ihren Auftrag in der Produktion ausführen. Wenn Sie Ihren Auftrag mit unterschiedlichen Eingabelasten testen, erhalten Sie einen Eindruck davon, wie viele SUs Ihr Auftrag für verschiedene Eingabedurchsätze benötigt. Dadurch können Sie die von Ihnen in den Warnungsregeln definierten Bedingungen gesteuert werden, die Vorgänge zum Hoch- und Herunterskalieren auslösen. 

## <a name="next-steps"></a>Nächste Schritte
* [Erstellen von parallelisierbaren Abfragen in Azure Stream Analytics](stream-analytics-parallelization.md)
* [Skalieren von Azure Stream Analytics-Aufträgen zur Erhöhung des Durchsatzes](stream-analytics-scale-jobs.md)