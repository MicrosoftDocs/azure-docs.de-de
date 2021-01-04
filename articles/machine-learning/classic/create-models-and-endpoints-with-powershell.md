---
title: 'ML Studio (Classic): Erstellen von mehreren Modellen und Endpunkten – Azure'
description: Verwenden Sie PowerShell, um mehrere Machine Learning-Modelle und Webdienst-Endpunkte mit demselben Algorithmus, aber verschiedenen Trainingsdatasets, zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: ef9ea055f437b53313dc9ee11b0b91f095664f5e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322846"
---
# <a name="create-multiple-web-service-endpoints-from-one-experiment-with-ml-studio-classic-and-powershell"></a>Erstellen mehrerer Webdienst-Endpunkte von einem Experiment mit Machine Learning Studio (Classic) und PowerShell

**GILT FÜR:**  ![Gilt für ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic) ![Gilt nicht für ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Dies ist ein Beispiel für ein häufiges Machine Learning-Problem: Sie möchten viele Modelle erstellen, die denselben Trainingsworkflow haben und für die derselbe Algorithmus verwendet wird. Aber Sie möchten, dass die Modelle unterschiedliche Trainingsdatasets als Eingabe haben. In diesem Artikel erfahren Sie, wie Sie dies in größerem Umfang in Azure Machine Learning Studio (klassisch) mit nur einem Experiment durchführen.

Angenommen, Sie besitzen eine Fahrradvermietung als globales Franchise-Unternehmen. Sie möchten ein Regressionsmodell erstellen, um die Mietnachfrage anhand von bisherigen Daten vorherzusagen. Sie betreiben weltweit 1.000 Mietstandorte und haben für jeden Standort ein Dataset erfasst. Die Datasets enthalten wichtigen Eigenschaften, etwa Datum, Uhrzeit, Wetter und Verkehrssituation, die für jeden Standort individuell sind.

Sie können das Modell einmalig trainieren, indem Sie eine zusammengefasste Version mit allen Datasets aller Standorte verwenden. Aber jeder Ihrer Standorte hat eine einzigartige Umgebung. Daher besteht ein besserer Ansatz darin, das Regressionsmodell separat zu trainieren, indem das Dataset für jeden Standort genutzt wird. Auf diese Weise können in jedem trainierten Modell die unterschiedlichen Ladengrößen, das Volumen, die Geografie, die Einwohnerzahl, die Fahrradfreundlichkeit der Verkehrsinfrastruktur und weitere Aspekte berücksichtigt werden.

Dies wäre vermutlich der beste Ansatz. Sie möchten in Azure Machine Learning Studio (klassisch) aber nicht 1.000 Trainingsexperimente erstellen müssen, also ein Experiment pro Standort. Dies ist nicht nur viel zu aufwendig, sondern auch eine ineffiziente Vorgehensweise, da alle Experimente mit Ausnahme des Trainingsdatasets die gleichen Komponenten aufweisen würden.

Glücklicherweise können Sie hierfür die [Azure Machine Learning Studio-API (klassisch) für das erneute Trainieren](./retrain-machine-learning-model.md) verwenden und die Aufgabe mit [Azure Machine Learning Studio (klassisch) PowerShell](powershell-module.md) automatisieren.

> [!NOTE]
> Damit das Beispiel schneller ausgeführt wird, reduzieren Sie die Standortanzahl von 1.000 auf 10. Es gelten aber dieselben Prinzipien und Verfahren wie für 1.000 Standorte. Möchten Sie das Trainieren aber mit 1.000 Datasets vornehmen, sollten Sie die folgenden PowerShell-Skripts gleichzeitig ausführen. Die Erklärung hierzu würde den Rahmen dieses Artikels sprengen, aber im Internet finden Sie Beispiele für das PowerShell-Multithreading.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Einrichten des Trainingsexperiments
Verwenden Sie das Beispiel [Training Experiment](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1), das sich im [Cortana Intelligence-Katalog](https://gallery.azure.ai) befindet. Öffnen Sie dieses Experiment im [Azure Machine Learning Studio](https://studio.azureml.net) -Arbeitsbereich (klassisch).

> [!NOTE]
> Damit Sie dieses Beispiel nachvollziehen können, empfiehlt es sich, anstelle eines kostenlosen Arbeitsbereichs einen Standardarbeitsbereich zu verwenden. Sie erstellen einen Endpunkt pro Kunde, also insgesamt zehn Endpunkte, und dadurch ist ein Standardarbeitsbereich erforderlich, da ein kostenloser Arbeitsbereich auf drei Endpunkte beschränkt ist.
> 
> 

Im Experiment wird ein **Import Data** -Modul zum Importieren des Trainingsdatasets *customer001.csv* aus einem Azure-Speicherkonto verwendet. Angenommen, Sie haben Trainingsdatasets für alle Standorte der Fahrradvermietung erfasst und diese in demselben Blobspeicher in den Dateien *rentalloc001.csv* bis *rentalloc10.csv* gespeichert.

![Lesermodul importiert Daten aus einem Azure-Blob](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Beachten Sie, dass dem Modul **Train Model** das Modul **Web Service Output** hinzugefügt wurde.
Wenn dieses Experiment als Webdienst bereitgestellt wird, gibt der Endpunkt, der dieser Ausgabe zugeordnet ist, das trainierte Modell als ILEARNER-Datei zurück.

Beachten Sie auch, dass Sie einen Webdienstparameter einrichten, der die URL definiert, die vom **Import Data** -Modul verwendet wird. Dies ermöglicht Ihnen die Verwendung des Parameters zum Angeben individueller Trainingsdatasets, um das Modell für jeden Standort zu trainieren.
Es gibt weitere Möglichkeiten, wie Sie dies umsetzen könnten. Sie können eine SQL-Abfrage mit einem Webdienstparameter verwenden, um Daten aus einer Datenbank in Azure SQL-Datenbank abzurufen. Oder Sie können das **Web Service Input** -Modul verwenden, um das Dataset an den Webdienst zu übergeben.

![Ein trainiertes Modellmodul mit Ausgabe an ein Webdienstausgabemodul](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Wir führen dieses Trainingsexperiment jetzt aus, indem wir den Standardwert *rental001.csv* als Trainingsdataset verwenden. Wenn Sie die Ausgabe des Auswertungsmoduls ( **Evaluate** ) anzeigen, indem Sie auf die Ausgabe klicken und **Visualisieren** auswählen, sehen Sie, dass eine ordentliche Leistung von „ *AUC* = 0,91“ angezeigt wird. Sie sind jetzt so weit, dass Sie aus diesem Trainingsexperiment einen Webdienst bereitstellen können.

## <a name="deploy-the-training-and-scoring-web-services"></a>Bereitstellen der Webdienste für das Training und die Bewertung
Klicken Sie zum Bereitstellen des Webdiensts für das Training unter dem Experimentbereich auf die Schaltfläche **Set Up Web Service** (Webdienst einrichten), und wählen Sie die Option **Deploy Web Service** (Webdienst bereitstellen). Geben Sie dem Webdienst den Namen „Bike Rental Training“.

Als Nächstes müssen Sie den Webdienst für die Bewertung bereitstellen.
Hierzu klicken Sie unter dem Experimentbereich auf **Set Up Web Service** (Webdienst einrichten), und wählen Sie die Option **Predictive Web Service** (Vorhersagewebdienst) aus. Ein Bewertungsexperiment wird erstellt.
Sie müssen einige kleinere Anpassungen vornehmen, damit dies als Webdienst funktioniert. Entfernen Sie die Bezeichnungsspalte „cnt“ aus den Eingabedaten, und beschränken Sie die Ausgabe auf die Instanz-ID und den entsprechenden vorhergesagten Wert.

Damit Sie Ihre bisherige Arbeit speichern, können Sie das [Vorhersageexperiment](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) im Katalog öffnen, der bereits vorbereitet wurde.

Führen Sie zum Bereitstellen des Webdiensts das Vorhersageexperiment aus, und klicken Sie dann unter dem Experimentbereich auf die Schaltfläche **Deploy Web Service** (Webdienst bereitstellen). Geben Sie dem Webdienst für die Bewertung den Namen „Bike Rental Scoring“.

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Erstellen von zehn identischen Webdienst-Endpunkten mit PowerShell
Dieser Webdienst verfügt über einen Standardendpunkt. Der Standardendpunkt ist für Sie aber nicht von so großem Interesse, da er nicht aktualisiert werden kann. Sie müssen zehn zusätzliche Endpunkte erstellen, also einen für jeden Standort. Hierfür können Sie PowerShell verwenden.

Als Erstes richten Sie die PowerShell-Umgebung ein:

```powershell
Import-Module .\AzureMLPS.dll
# Assume the default configuration file exists and is properly set to point to the valid Workspace.
$scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
$trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'
```

Führen Sie anschließend den folgenden PowerShell-Befehl aus:

```powershell
# Create 10 endpoints on the scoring web service.
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    Write-Host ('adding endpoint ' + $endpointName + '...')
    Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
}
```

Sie haben jetzt zehn Endpunkte erstellt, die alle dasselbe trainierte Modell enthalten, das mit *customer001.csv* trainiert wurde. Sie können die Endpunkte im Azure-Portal anzeigen.

![Anzeigen der Liste der trainierten Modelle im Portal](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Aktualisieren der Endpunkte zur Verwendung separater Trainingsdatasets mit PowerShell
Der nächste Schritt besteht darin, die Endpunkte mit Modellen zu aktualisieren, die basierend auf den individuellen Daten der einzelnen Kunden eindeutig trainiert wurden. Zuerst müssen Sie diese Modelle aber über den Webdienst **Bike Rental Training** erzeugen. Wir kehren zum Webdienst **Bike Rental Training** zurück. Sie müssen seinen BES-Endpunkt zehnmal mit zehn verschiedenen Trainingsdatasets aufrufen, um zehn verschiedene Modelle zu erzeugen. Hierfür verwenden Sie das PowerShell-Cmdlet **InovkeAmlWebServiceBESEndpoint**.

Sie müssen außerdem Anmeldeinformationen für Ihr Blobspeicherkonto in `$configContent` bereitstellen. Dies erfolgt über die Felder `AccountName`, `AccountKey` und `RelativeLocation`. Der `AccountName` kann einer Ihrer Kontonamen sein, die im **Azure-Portal** angezeigt werden (Registerkarte *Speicher* ). Nachdem Sie auf ein Speicherkonto geklickt haben, können Sie auf den `AccountKey` zugreifen, indem Sie unten auf die Schaltfläche **Zugriffsschlüssel verwalten** klicken und den *Primären Zugriffsschlüssel* kopieren. `RelativeLocation` ist der Pfad relativ zu Ihrem Speicher, in dem ein neues Modell gespeichert wird. Der Pfad `hai/retrain/bike_rental/` im folgenden Skript verweist beispielsweise auf einen Container namens `hai`, und `/retrain/bike_rental/` sind Unterordner. Derzeit können Sie keine Unterordner über die Benutzeroberfläche des Portals erstellen, aber dies ist mit [mehreren Azure Storage-Explorern](../../storage/common/storage-explorers.md) möglich. Es wird empfohlen, im Speicher wie folgt einen neuen Container zum Speichern der neuen trainierten Modelle (ILEARNER-Dateien) zu erstellen: Klicken Sie unten auf der Speicherseite auf die Schaltfläche **Hinzufügen** , und geben Sie dem Container den Namen `retrain`. Zusammengefasst beziehen sich die erforderlichen Änderungen am folgenden Skript auf `AccountName`, `AccountKey` und `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

```powershell
# Invoke the retraining API 10 times
# This is the default (and the only) endpoint on the training web service
$trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
$submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
$apiKey = $trainingSvcEp.PrimaryKey;
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
    $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
    Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
    Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
}
```

> [!NOTE]
> Der BES-Endpunkt ist der einzige unterstützte Modus für diesen Vorgang. RRS kann für die Erzeugung von trainierten Modellen nicht verwendet werden.
> 
> 

Wie Sie oben sehen können, erstellen Sie die Konfigurationszeichenfolge dynamisch, anstatt zehn unterschiedliche JSON-Dateien für die BES-Auftragskonfiguration zu erstellen. Danach übergeben Sie die Konfigurationszeichenfolge an den *jobConfigString* -Parameter des **InvokeAmlWebServceBESEndpoint** -Cmdlets. Es ist tatsächlich nicht erforderlich, eine Kopie auf einem Datenträger zu speichern.

Wenn alles gut geht, sollten nach einer Weile in Ihrem Azure-Speicherkonto zehn ILEARNER-Dateien, von *model001.ilearner* bis *model010.ilearner* , angezeigt werden. Jetzt können Sie die zehn Endpunkte des Webdiensts für die Bewertung mit diesen Modellen aktualisieren, indem Sie das PowerShell-Cmdlet **Patch-AmlWebServiceEndpoint** verwenden. Denken Sie erneut daran, dass Sie nur die nicht standardmäßigen Endpunkte patchen können, die Sie zuvor programmgesteuert erstellt haben.

```powershell
# Patch the 10 endpoints with respective .ilearner models
$baseLoc = 'http://bostonmtc.blob.core.windows.net/'
$sasToken = '<my_blob_sas_token>'
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
    Write-Host ('Patching endpoint ' + $endpointName + '...');
    Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
}
```

Dieser Vorgang sollte relativ schnell ausgeführt werden. Wenn die Ausführung abgeschlossen ist, haben Sie erfolgreich zehn Vorhersagewebdienst-Endpunkte erstellt. Jeder dieser Punkte enthält ein trainiertes Modell, das einzigartig mit dem Dataset trainiert wurde, das speziell zu dem entsprechenden Vermietungsstandort gehört. Und all dies aus einem einzigen Trainingsexperiment. Zur Überprüfung können Sie versuchen, diese Endpunkte mit dem **InvokeAmlWebServiceRRSEndpoint** -Cmdlet aufzurufen, wobei Sie jeweils dieselben Eingabedaten bereitstellen. Sie sollten davon ausgehen, dass Sie unterschiedliche Vorhersageergebnisse sehen, da die Modelle mit unterschiedlichen Trainingsdatasets trainiert wurden.

## <a name="full-powershell-script"></a>Vollständiges PowerShell-Skript
Hier ist der vollständige Quellcode angegeben:

```powershell
Import-Module .\AzureMLPS.dll
# Assume the default configuration file exists and properly set to point to the valid workspace.
$scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
$trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

# Create 10 endpoints on the scoring web service
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    Write-Host ('adding endpoint ' + $endpontName + '...')
    Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
}

# Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
$trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
$submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
$apiKey = $trainingSvcEp.PrimaryKey;
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
    $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
    Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
    Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
}

# Patch the 10 endpoints with respective .ilearner models
$baseLoc = 'http://bostonmtc.blob.core.windows.net/'
$sasToken = '?test'
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
    Write-Host ('Patching endpoint ' + $endpointName + '...');
    Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
}
```