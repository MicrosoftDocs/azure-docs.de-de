---
title: Überwachen und Verwalten von Azure Stream Analytics-Aufträgen mithilfe von PowerShell
description: In diesem Artikel erfahren Sie, wie Sie Azure Stream Analytics-Aufträge mithilfe von Azure PowerShell und Cmdlets überwachen und verwalten können.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/28/2017
ms.openlocfilehash: 4d41e8231f5bd186408ea511c0f096a6a5f26c83
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123641"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-azure-powershell-cmdlets"></a>Überwachen und Verwalten von Stream Analytics-Aufträgen mit PowerShell-Cmdlets
Erfahren Sie, wie Sie Stream Analytics-Ressourcen mit Azure PowerShell-Cmdlets und -Skripts, die grundlegende Stream Analytics-Aufgaben ausführen, überwachen und verwalten.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites-for-running-azure-powershell-cmdlets-for-stream-analytics"></a>Voraussetzungen für das Ausführen von Azure PowerShell-Cmdlets für Stream Analytics
* Erstellen Sie in Ihrem Abonnement eine Azure-Ressourcengruppe. Nachfolgend ist ein Azure PowerShell-Beispielskript aufgeführt. Informationen zu Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).  

Azure PowerShell 0.9.8:  

```powershell
# Log in to your Azure account
Add-AzureAccount
# Select the Azure subscription you want to use to create the resource group if you have more han one subscription on your account.
Select-AzureSubscription -SubscriptionName <subscription name>
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzureProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzureResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```

Azure PowerShell 1.0:

```powershell
# Log in to your Azure account
Connect-AzAccount
# Select the Azure subscription you want to use to create the resource group.
Get-AzSubscription -SubscriptionName "your sub" | Select-AzSubscription
# If Stream Analytics has not been registered to the subscription, remove remark symbol below (#)to run the Register-AzureProvider cmdlet to register the provider namespace.
#Register-AzResourceProvider -Force -ProviderNamespace 'Microsoft.StreamAnalytics'
# Create an Azure resource group
New-AzResourceGroup -Name <YOUR RESOURCE GROUP NAME> -Location <LOCATION>
```


> [!NOTE]
> Für programmgesteuert erstellte Stream Analytics-Aufträge ist die Überwachung in der Standardeinstellung nicht aktiviert.  Sie können die Überwachung manuell im Azure-Portal aktivieren, indem Sie zur Überwachungsseite des Auftrags wechseln und auf die Schaltfläche „Aktivieren“ klicken. Zudem können Sie programmgesteuert vorgehen, indem Sie die Schritte unter [Programmgesteuertes Überwachen von Stream Analytics-Aufträgen](stream-analytics-monitor-jobs.md) durchführen.
> 
> 

## <a name="azure-powershell-cmdlets-for-stream-analytics"></a>Azure PowerShell-Cmdlets für Stream Analytics
Mit den folgenden PowerShell-Cmdlets können Azure Stream Analytics-Aufträge überwacht und verwaltet werden. Beachten Sie, dass es verschiedene Versionen von Azure PowerShell gibt. 
**In den aufgeführten Beispielen gilt der erste Befehl für Azure PowerShell 0.9.8 und der zweite Befehl für Azure PowerShell 1.0.** In den Befehlen für Azure PowerShell 1.0 muss immer „Az“ enthalten sein.

### <a name="get-azurestreamanalyticsjob--get-azstreamanalyticsjob"></a>Get-AzureStreamAnalyticsJob | Get-AzStreamAnalyticsJob
Listet alle Stream Analytics-Aufträge auf, die im Azure-Abonnement oder in der Ressourcengruppe definiert sind, oder ruft Auftragsinformationen zu einem bestimmten Auftrag innerhalb einer Ressourcengruppe ab.

**Beispiel 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob
```

Dieser PowerShell-Befehl gibt Informationen zu allen Stream Analytics-Aufträgen im Azure-Abonnement zurück.

**Beispiel 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
```

Dieser PowerShell-Befehl gibt Informationen zu allen Stream Analytics-Aufträgen in der Ressourcengruppe "StreamAnalytics-Default-Central-US" zurück.

**Beispiel 3**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
```

Dieser PowerShell-Befehl gibt Informationen zum Stream Analytics-Auftrag "StreamingJob" in der Ressourcengruppe "StreamAnalytics-Default-Central-US" zurück.

### <a name="get-azurestreamanalyticsinput--get-azstreamanalyticsinput"></a>Get-AzureStreamAnalyticsInput | Get-AzStreamAnalyticsInput
Listet alle Eingaben auf, die in einem angegebenen Stream Analytics-Auftrag definiert sind, oder ruft Informationen zu einer bestimmten Eingabe ab.

**Beispiel 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Dieser PowerShell-Befehl gibt Informationen zu allen Eingaben zurück, die für den Auftrag "StreamingJob" definiert sind.

**Beispiel 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Dieser PowerShell-Befehl gibt Informationen zu der Eingabe "EntryStream" zurück, die für den Auftrag "StreamingJob" definiert ist.

### <a name="get-azurestreamanalyticsoutput--get-azstreamanalyticsoutput"></a>Get-AzureStreamAnalyticsOutput | Get-AzStreamAnalyticsOutput
Listet alle Ausgaben auf, die in einem angegebenen Stream Analytics-Auftrag definiert sind, oder ruft Informationen zu einer bestimmten Ausgabe ab.

**Beispiel 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
```

Dieser PowerShell-Befehl gibt Informationen zu Ausgaben zurück, die für den Auftrag "StreamingJob" definiert sind.

**Beispiel 2**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Dieser PowerShell-Befehl gibt Informationen zu der Ausgabe "Output" zurück, die für den Auftrag "StreamingJob" definiert ist.

### <a name="get-azurestreamanalyticsquota--get-azstreamanalyticsquota"></a>Get-AzureStreamAnalyticsQuota | Get-AzStreamAnalyticsQuota
Ruft Informationen zum Streamingeinheitenkontingent einer angegebenen Region ab.

**Beispiel 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsQuota -Location "Central US" 
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsQuota -Location "Central US" 
```

Dieser PowerShell-Befehl gibt Informationen zum Streamingeinheitenkontingent und zur Nutzung in der Region &quot;USA, Mitte&quot; zurück.

### <a name="get-azurestreamanalyticstransformation--get-azstreamanalyticstransformation"></a>Get-AzureStreamAnalyticsTransformation | Get-AzStreamAnalyticsTransformation
Ruft Informationen zu einer bestimmten Transformation ab, die im Stream Analytics-Auftrag definiert ist.

**Beispiel 1**

Azure PowerShell 0.9.8:  

```powershell
Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Azure PowerShell 1.0:  

```powershell
Get-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name StreamingJob
```

Dieser PowerShell-Befehl gibt Informationen zu der Transformation "StreamingJob" im Auftrag "StreamingJob" zurück.

### <a name="new-azurestreamanalyticsinput--new-azstreamanalyticsinput"></a>New-AzureStreamAnalyticsInput | New-AzStreamAnalyticsInput
Erstellt eine neue Eingabe in einem Stream Analytics-Auftrag oder aktualisiert eine vorhandene angegebene Eingabe.

Der Name der Eingabe kann in der JSON-Datei oder in der Befehlszeile angegeben werden. Wenn beide Angaben erfolgen, muss der Name in der Befehlszeile mit dem Namen in der Datei übereinstimmen.

Wenn Sie eine bereits vorhandene Eingabe angeben und den Parameter „-Force“ nicht verwenden, fragt das Cmdlet, ob die vorhandene Eingabe ersetzt werden soll.

Wenn Sie den Parameter „-Force“ und einen vorhandenen Eingabenamen angeben, wird die Eingabe ohne Bestätigung ersetzt.

Ausführliche Informationen zu Struktur und Inhalten von JSON-Dateien finden Sie im Abschnitt [Erstellen von Eingaben (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-input] der Bibliothek [Referenz zur Stream Analytics-Verwaltungs-REST-API][stream.analytics.rest.api.reference].

**Beispiel 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" 
```

Dieser PowerShell-Befehl erstellt eine neue Eingabe aus der Datei "Input.json". Wenn eine vorhandene Eingabe mit dem in der Eingabedefinitionsdatei angegebenen Namen bereits definiert ist, fragt das Cmdlet, ob diese ersetzt werden soll.

**Beispiel 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
```

Dieser PowerShell-Befehl erstellt eine neue Eingabe für den Auftrag "EntryStream". Wenn eine vorhandene Eingabe mit diesem Namen bereits definiert ist, fragt das Cmdlet, ob diese ersetzt werden soll.

**Beispiel 3**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
```

Dieser PowerShell-Befehl ersetzt die Definition der vorhandenen Eingabequelle "EntryStream" durch die Definition aus der Datei.

### <a name="new-azurestreamanalyticsjob--new-azstreamanalyticsjob"></a>New-AzureStreamAnalyticsJob | New-AzStreamAnalyticsJob
Erstellt einen neuen Stream Analytics-Auftrag in Microsoft Azure oder aktualisiert die Definition eines vorhandenen angegebenen Auftrags.

Der Name des Auftrags kann in der JSON-Datei oder in der Befehlszeile angegeben werden. Wenn beide Angaben erfolgen, muss der Name in der Befehlszeile mit dem Namen in der Datei übereinstimmen.

Wenn Sie einen bereits vorhandenen Auftragsnamen angeben und den Parameter „-Force“ nicht verwenden, fragt das Cmdlet, ob der vorhandene Auftrag ersetzt werden soll.

Wenn Sie den Parameter „-Force“ und einen vorhandenen Auftragsnamen angeben, wird die Auftragsdefinition ohne Bestätigung ersetzt.

Ausführliche Informationen zu Struktur und Inhalten von JSON-Dateien finden Sie im Abschnitt [Erstellen eines Stream Analytics-Auftrags][msdn-rest-api-create-stream-analytics-job] der Bibliothek [Referenz zur Stream Analytics-Verwaltungs-REST-API][stream.analytics.rest.api.reference].

**Beispiel 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" 
```

Dieser PowerShell-Befehl erstellt einen neuen Auftrag aus der Definition in der Datei "JobDefinition.json". Wenn ein vorhandener Auftrag mit dem in der Auftragsdefinitionsdatei angegebenen Namen bereits definiert ist, fragt das Cmdlet, ob dieser ersetzt werden soll.

**Beispiel 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
```

Dieser PowerShell-Befehl ersetzt die Auftragsdefinition für "StreamingJob".

### <a name="new-azurestreamanalyticsoutput--new-azstreamanalyticsoutput"></a>New-AzureStreamAnalyticsOutput | New-AzStreamAnalyticsOutput
Erstellt eine neue Ausgabe in einem Stream Analytics-Auftrag oder aktualisiert eine vorhandene Ausgabe.  

Der Name der Ausgabe kann in der JSON-Datei oder in der Befehlszeile angegeben werden. Wenn beide Angaben erfolgen, muss der Name in der Befehlszeile mit dem Namen in der Datei übereinstimmen.

Wenn Sie eine bereits vorhandene Ausgabe angeben, den Parameter „-Force“ aber nicht verwenden, fragt das Cmdlet, ob die vorhandene Ausgabe ersetzt werden soll.

Wenn Sie den Parameter „-Force“ und einen vorhandenen Ausgabenamen angeben, wird die Ausgabe ohne Bestätigung ersetzt.

Ausführliche Informationen zu Struktur und Inhalten von JSON-Dateien finden Sie im Abschnitt [Erstellen von Ausgaben (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-output] der Bibliothek [Referenz zur Stream Analytics-Verwaltungs-REST-API][stream.analytics.rest.api.reference].

**Beispiel 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output
```

Dieser PowerShell-Befehl erstellt eine neue Ausgabe namens "output" im Auftrag "StreamingJob". Wenn eine vorhandene Ausgabe mit diesem Namen bereits definiert ist, fragt das Cmdlet, ob diese ersetzt werden soll.

**Beispiel 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
```

Dieser PowerShell-Befehl ersetzt die Definition für "output" im Auftrag "StreamingJob".

### <a name="new-azurestreamanalyticstransformation--new-azstreamanalyticstransformation"></a>New-AzureStreamAnalyticsTransformation | New-AzStreamAnalyticsTransformation
Erstellt eine neue Transformation in einem Stream Analytics-Auftrag oder aktualisiert die vorhandene Transformation.

Der Name der Transformation kann in der JSON-Datei oder in der Befehlszeile angegeben werden. Wenn beide Angaben erfolgen, muss der Name in der Befehlszeile mit dem Namen in der Datei übereinstimmen.

Wenn Sie eine bereits vorhandene Transformation angeben und den Parameter „-Force“ nicht verwenden, fragt das Cmdlet, ob die vorhandene Transformation ersetzt werden soll.

Wenn Sie den Parameter „-Force“ und einen vorhandenen Transformationsnamen angeben, wird die Transformation ohne Bestätigung ersetzt.

Ausführliche Informationen zu Struktur und Inhalten von JSON-Dateien finden Sie im Abschnitt [Erstellen von Transformationen (Azure Stream Analytics)][msdn-rest-api-create-stream-analytics-transformation] der Bibliothek [Referenz zur Stream Analytics-Verwaltungs-REST-API][stream.analytics.rest.api.reference].

**Beispiel 1**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
```

Dieser PowerShell-Befehl erstellt eine neue Transformation namens "StreamingJobTransform" im Auftrag "StreamingJob". Wenn eine vorhandene Transformation mit diesem Namen bereits definiert ist, fragt das Cmdlet, ob diese ersetzt werden soll.

**Beispiel 2**

Azure PowerShell 0.9.8:  

```powershell
New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

Azure PowerShell 1.0:  

```powershell
New-AzStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
```

 Dieser PowerShell-Befehl ersetzt die Definition von "StreamingJobTransform" im Auftrag "StreamingJob".

### <a name="remove-azurestreamanalyticsinput--remove-azstreamanalyticsinput"></a>Remove-AzureStreamAnalyticsInput | Remove-AzStreamAnalyticsInput
Löscht asynchron eine bestimmte Eingabe aus einem Stream Analytics-Auftrag in Microsoft Azure.  
Wenn Sie den Parameter „-Force“ angeben, wird die Eingabe ohne Bestätigung gelöscht.

**Beispiel 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EventStream
```

Dieser PowerShell-Befehl entfernt die Eingabe "EventStream" im Auftrag "StreamingJob".  

### <a name="remove-azurestreamanalyticsjob--remove-azstreamanalyticsjob"></a>Remove-AzureStreamAnalyticsJob | Remove-AzStreamAnalyticsJob
Löscht asynchron einen bestimmten Stream Analytics-Auftrag in Microsoft Azure.  
Wenn Sie den Parameter „-Force“ angeben, wird der Auftrag ohne Bestätigung gelöscht.

**Beispiel 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Dieser PowerShell-Befehl entfernt den Auftrag "StreamingJob".  

### <a name="remove-azurestreamanalyticsoutput--remove-azstreamanalyticsoutput"></a>Remove-AzureStreamAnalyticsOutput | Remove-AzStreamAnalyticsOutput
Löscht asynchron eine bestimmte Ausgabe aus einem Stream Analytics-Auftrag in Microsoft Azure.  
Wenn Sie den Parameter „-Force“ angeben, wird die Ausgabe ohne Bestätigung gelöscht.

**Beispiel 1**

Azure PowerShell 0.9.8:  

```powershell
Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Remove-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Dieser PowerShell-Befehl entfernt die Ausgabe "Output" im Auftrag "StreamingJob".  

### <a name="start-azurestreamanalyticsjob--start-azstreamanalyticsjob"></a>Start-AzureStreamAnalyticsJob | Start-AzStreamAnalyticsJob
Stellt asynchron einen Stream Analytics-Auftrag in Microsoft Azure bereit und startet diesen.

**Beispiel 1**

Azure PowerShell 0.9.8:  

```powershell
Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Azure PowerShell 1.0:  

```powershell
Start-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z
```

Dieser PowerShell-Befehl startet den Auftrag "StreamingJob" am 12. Dezember 2012 mit der benutzerdefinierten Ausgabestartzeit 12:12:12 UTC.

### <a name="stop-azurestreamanalyticsjob--stop-azstreamanalyticsjob"></a>Stop-AzureStreamAnalyticsJob | Stop-AzStreamAnalyticsJob
Beendet die Ausführung eines Stream Analytics-Auftrags in Microsoft Azure asynchron und hebt die Zuordnung der Ressourcen auf, die verwendet wurden. Die Auftragsdefinition und die Metadaten bleiben in Ihrem Abonnement über das Azure-Portal und die Verwaltungs-APIs verfügbar, damit der Auftrag bearbeitet und neu gestartet werden kann. Es entstehen keine Kosten für einen Auftrag mit dem Zustand "Beendet".

**Beispiel 1**

Azure PowerShell 0.9.8:  

```powershell
Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Azure PowerShell 1.0:  

```powershell
Stop-AzStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob 
```

Dieser PowerShell-Befehl beendet den Auftrag "StreamingJob".  

### <a name="test-azurestreamanalyticsinput--test-azstreamanalyticsinput"></a>Test-AzureStreamAnalyticsInput | Test-AzStreamAnalyticsInput
Testet, ob Stream Analytics eine Verbindung mit einer angegebenen Eingabe herstellen kann.

**Beispiel 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name EntryStream
```

Dieser PowerShell-Befehl testet den Verbindungsstatus der Eingabe "EntryStream" in "StreamingJob".  

### <a name="test-azurestreamanalyticsoutput--test-azstreamanalyticsoutput"></a>Test-AzureStreamAnalyticsOutput | Test-AzStreamAnalyticsOutput
Testet, ob Stream Analytics eine Verbindung mit einer angegebenen Ausgabe herstellen kann.

**Beispiel 1**

Azure PowerShell 0.9.8:  

```powershell
Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Azure PowerShell 1.0:  

```powershell
Test-AzStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob -Name Output
```

Dieser PowerShell-Befehl testet den Verbindungsstatus der Ausgabe "Output" in "StreamingJob".  

## <a name="get-support"></a>Support
Weitere Unterstützung finden Sie auf der [Frageseite von Microsoft Q&A (Fragen und Antworten) zu Azure Stream Analytics](/answers/topics/azure-stream-analytics.html). 

## <a name="next-steps"></a>Nächste Schritte
* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](/rest/api/streamanalytics/)

[msdn-switch-azuremode]: /previous-versions/azure/dn722470(v=azure.100)
[powershell-install]: /powershell/azure/
[msdn-rest-api-create-stream-analytics-job]: ./stream-analytics-quick-create-portal.md
[msdn-rest-api-create-stream-analytics-input]: ./stream-analytics-define-inputs.md
[msdn-rest-api-create-stream-analytics-output]: ./stream-analytics-define-outputs.md
[msdn-rest-api-create-stream-analytics-transformation]: /cli/azure/ext/stream-analytics/stream-analytics/transformation

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/