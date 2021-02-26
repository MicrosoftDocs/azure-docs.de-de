---
title: 'ML Studio (Classic): Erneutes Trainieren eines Webdiensts (Azure)'
description: Erfahren Sie, wie Sie einen Webdienst aktualisieren, sodass er das neu trainierte Machine Learning-Modell in Azure Machine Learning Studio (klassisch) verwendet.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-csharp
ms.date: 02/14/2019
ms.openlocfilehash: a4fe9e54e5e03a8dbf2a727b22f784c36d6c65f9
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517585"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Erneutes Trainieren und Bereitstellen eines Machine Learning-Modells

**GILT FÜR:**  ![Gilt für ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic) ![Gilt nicht für ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Das erneute Training von Machine Learning-Modellen ist eine Möglichkeit sicherzustellen, dass sie fehlerfrei bleiben und auf den relevantesten verfügbaren Daten basieren. Dieser Artikel veranschaulicht das erneute Trainieren und Bereitstellen eines Machine Learning-Modells als neuer Webdienst in Studio (klassisch). Wenn Sie einen klassischen Webdienst erneut trainieren möchten, [lesen Sie diese Anleitung](retrain-classic-web-service.md).

In diesem Artikel wird davon ausgegangen, dass Sie bereits einen Vorhersagewebdienst bereitgestellt haben. Wenn Sie noch nicht über einen Vorhersagewebdienst verfügen, [erfahren Sie hier, wie Sie einen (klassischen) Studio-Webdienst bereitstellen](deploy-a-machine-learning-web-service.md).

Befolgen Sie diese Schritte, um einen neuen Machine Learning-Webdienst zu trainieren und bereitzustellen:

1. Bereitstellen eines **Webdiensts zum erneuten Trainieren**
1. Trainieren eines neuen Modells mit Ihrem **Webdienst zum erneuten Trainieren**
1. Aktualisieren Ihres **vorhandenen Vorhersageexperiments** zum Verwenden des neuen Modells

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>Bereitstellen des Webdiensts zum erneuten Trainieren

Mit einem Webdienst zum erneuten Trainieren können Sie Ihr Modell erneut mit einem neuen Satz von Parametern trainieren – z.B. neuen Daten – und zur späteren Verwendung speichern. Wenn Sie die Verbindung eines **Web Service Output** mit einem **Train Model** herstellen, gibt das Trainingsexperiment ein neues Modell aus, das Sie verwenden können.

Stellen Sie mit folgenden Schritten einen Webdienst zum erneuten Trainieren bereit:

1. Stellen Sie die Verbindung eines **Web Service Input**-Moduls mit Ihrer Dateneingabe her. Dadurch wird sichergestellt, dass Ihre Daten für das erneute Training genau so verarbeitet werden wie Ihre ursprünglichen Trainingsdaten.
1. Stellen Sie die Verbindung eines **Web Service Output**-Moduls mit der Ausgabe Ihres **Train Model** her.
1. Wenn Sie ein **Evaluate Model**-Modul haben, können Sie die Verbindung eines **Web Service Output**-Moduls zur Ausgabe der Auswertungsergebnisse herstellen.
1. Führen Sie das Experiment aus.

    Nachdem Sie Ihr Experiment ausgeführt haben, sollte der resultierende Workflow dem folgenden Image ähneln:

    ![Resultierender Workflow](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    Stellen Sie als Nächstes das Trainingsexperiment als Webdienst zum erneuten Trainieren bereit, der ein trainiertes Modell und Modellauswertungsergebnisse ausgibt.

1. Klicken Sie am unteren Rand des Experimentbereichs auf **Set Up Web Service (Webdienst einrichten)** .
1. Wählen Sie **Deploy Web Service [New] (Webdienst bereitstellen [Neu])** aus. Das Azure Machine Learning-Webdienstportal wird mit der Seite **Webdienst bereitstellen** geöffnet.
1. Geben Sie einen Namen für Ihren Webdienst ein, und wählen Sie einen Zahlungsplan aus.
1. Klicken Sie auf **Bereitstellen**.

## <a name="retrain-the-model"></a>Erneutes Trainieren des Modells

In diesem Beispiel dient C# zum Erstellen der Anwendung für erneutes Trainieren. Für diese Aufgabe können Sie auch Python- oder R-Beispielcode verwenden.

Rufen Sie mit den folgenden Schritten die APIs zum erneuten Trainieren auf:

1. Erstellen Sie in Visual Studio eine C#-Konsolenanwendung: **Neu** > **Projekt** > **Visual C#**  > **Klassischer Windows-Desktop** > **Konsolen-App (.NET Framework)** .
1. Melden Sie sich beim Machine Learning-Webdienstportal an.
1. Klicken Sie auf den verwendeten Webdienst.
1. Klicken Sie auf **Consume**.
1. Klicken Sie am unteren Rand der Seite **Consume** im Abschnitt **Sample Code** auf **Batch**.
1. Kopieren Sie den C#-Beispielcode für die Batchausführung, und fügen Sie ihn in die Datei „Program.cs“ ein. Achten Sie dabei darauf, dass der Namespace intakt bleibt.

Fügen Sie das NuGet-Paket „Microsoft.AspNet.WebApi.Client“ hinzu, wie in den Kommentaren angegeben. Vor dem Hinzufügen des Verweises auf „Microsoft.WindowsAzure.Storage.dll“ müssen Sie unter Umständen die [Clientbibliothek für Azure Storage Services](https://www.nuget.org/packages/WindowsAzure.Storage) installieren.

Der folgende Screenshot zeigt die Seite **Consume** im Azure Machine Learning-Webdienste-Portal.

![Seite Consume](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>Aktualisieren der apiKey-Deklaration

Suchen Sie die **apiKey**-Deklaration:

```csharp
const string apiKey = "abc123"; // Replace this with the API key for the web service
```

Kopieren Sie auf der Seite **Consume** (Verbrauchen) im Abschnitt **Basic consumption info** (Grundlegende Verbrauchsinformationen) den Primärschlüssel, und fügen Sie ihn in die **apikey**-Deklaration ein.

### <a name="update-the-azure-storage-information"></a>Aktualisieren der Azure Storage-Informationen

Der BES-Beispielcode lädt eine Datei von einem lokalen Laufwerk (z.B. „C:\temp\CensusInput.csv“) in Azure Storage hoch, verarbeitet sie und schreibt die Ergebnisse zurück in Azure Storage.

1. Anmelden beim Azure-Portal
1. Klicken Sie in der linken Navigationsspalte auf **Weitere Dienste**, suchen Sie nach **Speicherkonten**, und wählen Sie die Option aus.
1. Wählen Sie in der Speicherkontenliste ein Speicherkonto zum Speichern des neu trainierten Modells aus.
1. Klicken Sie in der linken Navigationsspalte auf **Zugriffsschlüssel**.
1. Kopieren und speichern Sie den Wert für **Primärer Zugriffsschlüssel**.
1. Klicken Sie im linken Navigationsbereich auf **Blobs**.
1. Wählen Sie einen vorhandenen Container aus, oder erstellen Sie einen neuen, und speichern Sie den Namen.

Suchen Sie die Deklarationen *StorageAccountName*, *StorageAccountKey* und *StorageContainerName*, und aktualisieren Sie die Werte mit den gespeicherten Werten aus dem Portal.

```csharp
const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name
```

Außerdem müssen Sie sicherstellen, dass die Eingabedatei an dem im Code angegebenen Speicherort verfügbar ist.

### <a name="specify-the-output-location"></a>Angeben des Ausgabespeicherorts

Wenn Sie den Ausgabespeicherort in der Anforderungsnutzlast angeben, muss die Erweiterung der in *RelativeLocation* angegebenen Datei als `ilearner` angegeben werden.

```csharp
Outputs = new Dictionary<string, AzureBlobDataReference>() {
    {
        "output1",
        new AzureBlobDataReference()
        {
            ConnectionString = storageConnectionString,
            RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
        }
    },
```

Hier ist eine Beispielausgabe zum erneuten Trainieren:

![Ausgabe des erneuten Trainings](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>Auswerten der Ergebnisse des erneuten Trainings

Wenn Sie die Anwendung ausführen, enthält die Ausgabe die URL und das erforderliche SAS-Token für den Zugriff auf die Auswertungsergebnisse.

Sie können die Leistungsergebnisse des erneut trainierten Modells anzeigen, indem Sie *BaseLocation*, *RelativeLocation* und *SasBlobToken* aus den Ausgabeergebnissen für *output2* kombinieren und die vollständige URL in die Adressleiste des Browsers einfügen.

Überprüfen Sie die Ergebnisse, um festzustellen, ob das neu trainierte Modell besser ist als das vorhandene.

Speichern Sie die Werte für *BaseLocation*, *RelativeLocation* und *SasBlobToken* aus den Ausgabeergebnissen.

## <a name="update-the-predictive-experiment"></a>Aktualisieren des Vorhersageexperiments

### <a name="sign-in-to-azure-resource-manager"></a>Anmelden bei Azure Resource Manager

Melden Sie sich innerhalb Ihrer PowerShell-Umgebung zunächst mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) bei Ihrem Azure-Konto an.

### <a name="get-the-web-service-definition-object"></a>Abrufen des Webdienstdefinition-Objekts

Rufen Sie als nächstes mithilfe des Cmdlets [Get-AzMlWebService](/powershell/module/az.machinelearning/get-azmlwebservice) das Webdienstdefinition-Objekt ab.

```azurepowershell
$wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'
```

Führen Sie zum Ermitteln des Ressourcengruppennamens eines vorhandenen Webdiensts das Cmdlet „Get-AzMlWebService“ ohne Parameter aus, um die Webdienste in Ihrem Abonnement anzuzeigen. Suchen Sie den Webdienst, und sehen Sie sich die Webdienst-ID an. Der Ressourcengruppenname ist das vierte Element in der ID (direkt nach dem *resourceGroups* -Element). Im folgenden Beispiel lautet der Name der Ressourcengruppe „Default-MachineLearning-SouthCentralUS“.

```azurepowershell
Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
Name : RetrainSamplePre.2016.8.17.0.3.51.237
Location : South Central US
Type : Microsoft.MachineLearning/webServices
Tags : {}
```

Alternativ können Sie sich zur Ermittlung des Ressourcengruppennamens eines vorhandenen Webdiensts beim Azure Machine Learning-Webdiensteportal anmelden. Wählen Sie den Webdienst aus. Der Ressourcengruppenname ist das fünfte Element in der Webdienst-URL (direkt nach dem *resourceGroups* -Element). Im folgenden Beispiel lautet der Name der Ressourcengruppe „Default-MachineLearning-SouthCentralUS“.

`https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237`

### <a name="export-the-web-service-definition-object-as-json"></a>Exportieren des Webdienstdefinition-Objekts als JSON-Code

Um die Definition so zu ändern, dass das neu trainierte Modell verwendet wird, müssen Sie sie zunächst mithilfe des Cmdlets [Export-AzMlWebService](/powershell/module/az.machinelearning/export-azmlwebservice) in eine Datei im JSON-Format exportieren.

```azurepowershell
Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"
```

### <a name="update-the-reference-to-the-ilearner-blob"></a>Aktualisieren des Verweises auf das iLearner-Blob

Suchen Sie in den Assets nach „[trained model]“, und aktualisieren Sie den Wert *uri* im Knoten *locationInfo* mit dem URI des iLearner-Blobs. Der URI wird auf der Grundlage von *BaseLocation* und *RelativeLocation* aus der Ausgabe des BES-Aufrufs für das erneute Training generiert.

```json
"asset3": {
    "name": "Retrain Sample [trained model]",
    "type": "Resource",
    "locationInfo": {
        "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
    },
    "outputPorts": {
        "Results dataset": {
            "type": "Dataset"
        }
    }
},
```

### <a name="import-the-json-into-a-web-service-definition-object"></a>Importieren des JSON-Codes in ein Webdienstdefinition-Objekt

Konvertieren Sie die geänderte JSON-Datei mithilfe des Cmdlets [Import-AzMlWebService](/powershell/module/az.machinelearning/import-azmlwebservice) wieder in ein Webdienstdefinition-Objekt, mit dem Sie das Vorhersageexperiment aktualisieren können.

```azurepowershell
$wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"
```

### <a name="update-the-web-service"></a>Aktualisieren des Webdiensts

Verwenden Sie abschließend das Cmdlet [Update-AzMlWebService](/powershell/module/az.machinelearning/update-azmlwebservice), um das Vorhersageexperiment zu aktualisieren.

```azurepowershell
Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'
```

## <a name="next-steps"></a>Nächste Schritte

Um mehr darüber zu erfahren, wie Sie Webdienste verwalten oder mehrere Experimentausführungen verfolgen können, lesen Sie die folgenden Artikel:

* [Erkunden des Web Services-Portals](manage-new-webservice.md)
* [Verwalten von Experimentiterationen](manage-experiment-iterations.md)