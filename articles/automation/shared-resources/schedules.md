---
title: Zeitpläne in Azure Automation
description: Automation-Zeitpläne werden verwendet, um die automatische Ausführung von Runbooks in Azure Automation zu planen. Beschreibt die Erstellung und Verwaltung eines Zeitplans, sodass ein Runbook automatisch zu einer bestimmten Uhrzeit oder nach einem sich wiederholenden Zeitplan gestartet wird.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 483f9092d29fc40937ed9d54510269af2af30872
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128803"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Planen eines Runbooks in Azure Automation

Um ein Runbook in Azure-Automation für die Ausführung zu einer bestimmten Uhrzeit zu planen, müssen Sie es mit einem oder mehreren Zeitplänen verknüpfen. Ein Zeitplan kann so konfiguriert werden, dass er entweder einmalig oder nach einem sich wiederholenden stündlichen oder täglichen Zeitplan für Runbooks im Azure-Portal ausgeführt wird. Sie können Zeitpläne auch wöchentlich, monatlich, für bestimmte Wochentage oder Tage des Monats oder einen bestimmten Tag des Monats festlegen. Ein Runbook kann mit mehreren Zeitplänen verknüpft werden, und mit einem Zeitplan können mehrere Runbooks verknüpft sein.

> [!NOTE]
> Zeitpläne unterstützen derzeit keine Azure Automation DSC-Konfigurationen.

## <a name="powershell-cmdlets"></a>PowerShell-Cmdlets

Die Cmdlets in der folgenden Tabelle werden zum Erstellen und Verwalten von Zeitplänen mit PowerShell in Azure Automation verwendet. Sie sind im [Azure PowerShell-Modul](/powershell/azure/overview)enthalten.

| Cmdlets | BESCHREIBUNG |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Ruft einen Zeitplan ab. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Erstellt einen neuen Zeitplan. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Entfernt einen Zeitplan. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Legt die Eigenschaften für einen vorhandenen Zeitplan fest. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Ruft geplante Runbooks ab. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Ordnet ein Runbook einem Zeitplan zu. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Hebt die Zuordnung eines Runbook zu einem Zeitplan auf. |

## <a name="creating-a-schedule"></a>Erstellen eines Zeitplans

Sie können im Azure-Portal oder mit PowerShell einen neuen Zeitplan für Runbooks erstellen.

> [!NOTE]
> In Azure Automation werden die neuesten Module in Ihrem Automation-Konto verwendet, wenn ein neuer geplanter Auftrag ausgeführt wird.  Zum Vermeiden von Auswirkungen auf Ihre Runbooks und die Prozesse, die Sie automatisieren, testen Sie zuerst alle Runbooks mit verknüpften Zeitplänen mit einem für Testzwecke vorgesehenen Automation-Konto.  Dadurch wird überprüft, ob Ihre geplanten Runbooks weiterhin einwandfrei funktionieren. Sollte dies nicht der Fall sein, können Sie alle zur Problembehandlung erforderlichen Änderungen vornehmen, bevor Sie die aktualisierte Runbookversion zur Produktion migrieren.
> Ihr Automation-Konto erhält nicht automatisch die neuen Versionen der Module. Dazu müssen Sie sie manuell aktualisieren, indem Sie unter **Module** die Option [Azure-Module aktualisieren](../automation-update-azure-modules.md) auswählen.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>So erstellen Sie einen neuen Zeitplan im Azure-Portal

1. Klicken Sie im Azure-Portal in Ihrem Automation-Konto im Abschnitt **Freigegebene Ressourcen** links auf **Zeitpläne**.
2. Klicken Sie oben auf der Seite auf **Zeitplan hinzufügen**.
3. Geben Sie im Bereich **Neuer Zeitplan** einen Wert für **Name** und optional eine **Beschreibung** für den neuen Zeitplan ein.
4. Wählen Sie aus, ob der Zeitplan einmalig oder wiederholt ausgeführt werden soll, indem Sie **Einmalig** oder **Wiederholt** auswählen. Geben Sie bei Auswahl von **Einmalig** eine **Startzeit** an, und klicken Sie auf **Erstellen**. Geben Sie bei Auswahl von **Wiederholt** eine **Startzeit** an, und wählen Sie für das **Wiederholungsintervall** die Häufigkeit für die wiederholte Ausführung des Runbooks aus: **Stunde**, **Tag**, **Woche** oder **Monat**.
    1. Wenn Sie **Woche** auswählen, wird eine Liste der zur Auswahl stehenden Wochentage angezeigt. Wählen Sie beliebig viele Tage aus. Die erste Ausführung Ihres Zeitplans erfolgt am ersten Tag, den Sie nach der Startzeit ausgewählt haben. Um z.B. einen Wochenendzeitplan anzugeben, wählen Sie **Samstag** und **Sonntag** aus.

       ![Festlegen eines sich wiederholenden Zeitplans für das Wochenende](../media/schedules/week-end-weekly-recurrence.png)

    2. Bei Auswahl von **Monat** werden verschiedene Optionen angezeigt. Wählen Sie für die Option **Monatliche Vorkommen** entweder **Tage im Monat** oder **Wochentage** aus. Bei Auswahl von **Tage im Monat** wird ein Kalender angezeigt, in dem Sie beliebig viele Tage auswählen können. Wenn Sie einen Tag wie den 31. auswählen, der im aktuellen Monat nicht vorkommt, wird der Zeitplan nicht ausgeführt. Wenn der Zeitplan am letzten Tag ausgeführt werden soll, wählen Sie unter **Am letzten Tag des Monats ausführen** die Option **Ja** aus. Bei Auswahl von **Wochentage** wird die Option **Wiederholen alle** angezeigt. Wählen Sie **Erster**, **Zweiter**, **Dritter**, **Vierter** oder **Letzter** aus. Wählen Sie schließlich einen Tag für die Wiederholung aus.

       ![Monatlicher Zeitplan am ersten, 15. und letzten Tag des Monats](../media/schedules/monthly-first-fifteenth-last.png)

5. Klicken Sie abschließend auf **Erstellen**.

### <a name="to-create-a-new-schedule-with-powershell"></a>So erstellen Sie einen neuen Zeitplan mit PowerShell

Verwenden Sie das Cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule), um Zeitpläne zu erstellen. Geben Sie die Startzeit für den Zeitplan und die Häufigkeit der Ausführung an. Die folgenden Beispiele zeigen, wie Sie verschiedene Zeitplanszenarien erstellen können.

#### <a name="create-a-one-time-schedule"></a>Erstellen eines einmaligen Zeitplans

Die folgenden Beispielbefehle erstellen einen einmaligen Zeitplan.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Erstellen eines sich wiederholenden Zeitplans

Die folgenden Beispielbefehle zeigen, wie Sie einen sich wiederholenden Zeitplan erstellen, der ein Jahr lang jeden Tag um 13:00 Uhr ausgeführt wird.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Erstellen eines wöchentlichen sich wiederholenden Zeitplans

Die folgenden Beispielbefehle zeigen, wie Sie einen wöchentlichen Zeitplan erstellen, der nur unter der Woche ausgeführt wird.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Erstellen eines wöchentlichen sich wiederholenden Zeitplans für Wochenenden

Die folgenden Beispielbefehle zeigen, wie Sie einen wöchentlichen Zeitplan erstellen, der nur an Wochenenden ausgeführt wird.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Erstellen eines sich wiederholenden Zeitplans für den ersten, den 15. und den letzten Tag des Monats

Die folgenden Beispielbefehle zeigen, wie Sie einen sich wiederholenden Zeitplan erstellen, der am ersten, am 15. und am letzten Tag eines Monats ausgeführt wird.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Verknüpfen eines Zeitplans mit einem Runbook

Ein Runbook kann mit mehreren Zeitplänen verknüpft werden, und mit einem Zeitplan können mehrere Runbooks verknüpft sein. Wenn ein Runbook über Parameter verfügt, können Sie Werte für diese bereitstellen. Sie müssen Werte für alle verbindlichen Parameter angeben; für optionale Parameter können Sie Werte angeben. Diese Werte werden jedes Mal verwendet, wenn das Runbook durch diesen Zeitplan gestartet wird. Sie können das gleiche Runbook einem anderen Zeitplan zuordnen und dabei andere Parameterwerte festlegen.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>So verknüpfen Sie einen Zeitplan mit einem Runbook mit dem klassischen Azure-Portal

1. Klicken Sie im Azure-Portal in Ihrem Automation-Konto im Abschnitt **Prozessautomatisierung** links auf **Runbooks**.
2. Klicken Sie auf den Namen des Runbooks, das Sie mit einem Zeitplan verknüpfen möchten.
3. Wenn das Runbook gegenwärtig nicht mit einem Zeitplan verknüpft ist, werden Ihnen Optionen zum Erstellen eines neuen Zeitplans oder zum Verknüpfen mit einem vorhandenen Zeitplan angeboten.
4. Wenn das Runbook über Parameter verfügt, können Sie die Option **Ausführungseinstellungen ändern (Standard: Azure)** auswählen. Der Bereich **Parameter** wird angezeigt, in dem Sie die Informationen eingeben können.

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>So verknüpfen Sie einen Zeitplan mit einem Runbook mithilfe von PowerShell

Mit dem Cmdlet [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) können Sie einen Zeitplan verknüpfen. Sie können Werte für die Runbookparameter im Parameter "Parameters" angeben. Weitere Informationen zum Angeben von Parameterwerten finden Sie unter [Starten eines Runbooks in Azure Automation](../automation-starting-a-runbook.md).
Die folgenden Beispielbefehle verdeutlichen, wie Sie einen Zeitplan mit einem Runbook verknüpfen, indem Sie ein Azure Resource Manager-Cmdlet mit Parametern verwenden.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Häufigeres Planen von Runbooks

Das am häufigsten auftretende Intervall, mit dem ein Zeitplan in Azure Automation konfiguriert werden kann, beträgt eine Stunde. Wenn Sie Zeitpläne häufiger ausführen müssen, stehen Ihnen zwei Optionen zur Verfügung:

* Erstellen Sie einen [Webhook](../automation-webhooks.md) für das Runbook, und rufen Sie mit dem [Azure Scheduler](../../scheduler/scheduler-get-started-portal.md) den Webhook auf. Der Azure Scheduler bietet eine differenziertere Granularität bei der Definition eines Zeitplans.

* Erstellen Sie vier Zeitpläne, die einmal pro Stunde alle mit einem Abstand von 15 Minuten voneinander beginnen. In diesem Szenario kann das Runbook alle 15 Minuten mit unterschiedlichen Zeitplänen ausgeführt werden.

## <a name="disabling-a-schedule"></a>Deaktivieren eines Zeitplans

Wenn Sie einen Zeitplan deaktivieren, werden sämtliche damit verknüpfte Runbooks nicht mehr nach diesem Zeitplan ausgeführt. Sie können einen Zeitplan manuell deaktivieren oder während der Erstellung für Zeitpläne eine Ablaufzeit festlegen. Sobald der Ablaufzeitpunkt erreicht ist, wird der Zeitplan deaktiviert.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>So deaktivieren Sie einen Zeitplan über das Azure-Portal

1. Klicken Sie im Azure-Portal in Ihrem Automation-Konto im Abschnitt **Freigegebene Ressourcen** links auf **Zeitpläne**.
2. Klicken Sie auf den Namen eines Zeitplans, um den Bereich mit den Details zu öffnen.
3. Ändern Sie **Aktiviert** in **Nein**.

> [!NOTE]
> Wenn Sie einen Zeitplan deaktivieren möchten, dessen Startuhrzeit in der Vergangenheit liegt, müssen Sie das Startdatum auf eine Uhrzeit in der Zukunft ändern, bevor Sie den Plan speichern.

### <a name="to-disable-a-schedule-with-powershell"></a>So deaktivieren Sie einen Zeitplan mithilfe von PowerShell

Mit dem Cmdlet [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) können Sie die Eigenschaften eines vorhandenen Zeitplans ändern. Um den Zeitplan zu deaktivieren, legen Sie für den Parameter **IsEnabled** den Wert **false** fest.

Die folgenden Beispielbefehle verdeutlichen, wie Sie einen Zeitplan für ein Runbook deaktivieren, indem Sie ein Azure Resource Manager-Cmdlet verwenden.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu den ersten Schritten mit Runbooks in Azure Automation finden Sie unter [Starten eines Runbooks in Azure Automation](../automation-starting-a-runbook.md)

