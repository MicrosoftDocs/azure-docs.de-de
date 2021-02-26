---
title: Verwenden von benutzerdefinierten Aktivitäten in einer Pipeline
description: Erfahren Sie, wie Sie benutzerdefinierte Aktivitäten mithilfe von .NET erstellen und dann die Aktivitäten in einer Azure Data Factory-Pipeline verwenden.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2018
ms.openlocfilehash: ec1e7c77c44cf1969e472a6e7288d1af5d6640e1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374795"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Verwenden von benutzerdefinierten Aktivitäten in einer Azure Data Factory-Pipeline

> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-use-custom-activities.md)
> * [Aktuelle Version](transform-data-using-dotnet-custom-activity.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Es existieren zwei Aktivitätstypen, die Sie in einer Azure Data Factory-Pipeline verwenden können.

- [Datenverschiebungsaktivitäten](copy-activity-overview.md) zum Verschieben von Daten zwischen [unterstützten Quell- und Senkendatenspeichern](copy-activity-overview.md#supported-data-stores-and-formats).
- [Datentransformationsaktivitäten](transform-data.md) zum Transformieren von Daten mithilfe von Computediensten wie Azure HDInsight, Azure Batch und Azure Machine Learning.

Wenn Sie Daten in einen bzw. aus einem von Data Factory nicht unterstützten Datenspeicher verschieben oder auf eine Weise transformieren/verarbeiten müssen, die von Data Factory nicht unterstützt wird, können Sie auch eine **benutzerdefinierte Aktivität** mit Ihrer eigenen Datenverschiebungs- bzw. -transformationslogik erstellen und in einer Pipeline verwenden. Die benutzerdefinierte Aktivität führt Ihre angepasste Codelogik in einem **Azure Batch**-Pool mit virtuellen Computern aus.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Lesen Sie die folgenden Artikel, wenn Sie noch nicht mit dem Azure Batch-Dienst vertraut sind:

* [Azure Batch – Grundlagen](../batch/batch-technical-overview.md) finden Sie eine Übersicht über den Azure Batch-Dienst.
* Erstellen Sie ein Azure Batch-Konto mit dem Cmdlet [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount), oder erstellen Sie das Azure Batch-Konto über das [Azure-Portal](../batch/batch-account-create-portal.md). Im Artikel [Verwenden von PowerShell zum Verwalten eines Azure Batch-Kontos](/archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account) ist die Verwendung dieses Cmdlets im Detail beschrieben.
* [New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool), um einen Azure Batch-Pool zu erstellen.

## <a name="azure-batch-linked-service"></a>Verknüpfter Azure Batch-Dienst

Der folgende JSON-Code definiert einen verknüpften Azure Batch-Beispieldienst. Einzelheiten finden Sie unter [Von Azure Data Factory unterstützte Compute-Umgebungen](compute-linked-services.md).

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
}
```

 Weitere Informationen zu diesem verknüpften Azure Batch-Dienst finden Sie im Artikel [Von Azure Data Factory unterstützten Compute-Umgebungen](compute-linked-services.md).

## <a name="custom-activity"></a>Benutzerdefinierte Aktivität

Der folgende JSON-Ausschnitt definiert eine Pipeline mit einer einfachen benutzerdefinierten Aktivität. Die Aktivitätsdefinition enthält einen Verweis auf den verknüpften Azure Batch-Dienst.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "helloworld.exe",
        "folderPath": "customactv2/helloworld",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }]
  }
}
```

In diesem Beispiel ist „helloworld. exe“ eine benutzerdefinierte Anwendung, die im Ordner „customactv2/helloworld“ des Azure Storage-Kontos gespeichert ist, das im „resourceLinkedService“ verwendet wird. Die benutzerdefinierte Aktivität übermittelt diese benutzerdefinierte Anwendung an Azure Batch. Sie können den Befehl in jede beliebige Anwendung ändern, die im Zielbetriebssystem auf den Knoten im Azure Batch-Pool ausgeführt werden kann.

Die folgende Tabelle beschreibt die Namen und Eigenschaften, die für diese Aktivität spezifisch sind.

| Eigenschaft              | BESCHREIBUNG                              | Erforderlich |
| :-------------------- | :--------------------------------------- | :------- |
| name                  | Name der Aktivität in der Pipeline     | Ja      |
| description           | Ein Text, der beschreibt, was mit der Aktivität ausgeführt wird.  | Nein       |
| type                  | Für die benutzerdefinierte Aktivität ist der Aktivitätstyp **Custom**. | Ja      |
| linkedServiceName     | Mit Azure Batch verknüpfter Dienst. Weitere Informationen zu diesem verknüpften Dienst finden Sie im Artikel [Von Azure Data Factory unterstützten Compute-Umgebungen](compute-linked-services.md).  | Ja      |
| command               | Befehl der benutzerdefinierten Anwendung, der ausgeführt werden soll. Wenn die Anwendung bereits auf dem Knoten des Azure Batch-Pools verfügbar ist, können „resourceLinkedService“ und „folderPath“ übersprungen werden. Sie können beispielsweise den Befehl `cmd /c dir` angeben, was vom Knoten des Azure Batch-Pools nativ unterstützt wird. | Ja      |
| resourceLinkedService | Mit dem Speicherkonto verknüpfter Azure Storage-Dienst, in dem die benutzerdefinierte Anwendung gespeichert wird. | Nein&#42;       |
| folderPath            | Pfad zum Ordner der benutzerdefinierten Anwendung und allen ihren abhängigen Elementen<br/><br/>Wenn Sie Abhängigkeiten in Unterordnern gespeichert haben (also in einer hierarchischen Ordnerstruktur unter *folderPath*), wird die Ordnerstruktur zurzeit abgeflacht, wenn die Dateien nach Azure Batch kopiert werden. Das heißt, alle Dateien werden in einen einzigen Ordner ohne Unterordner kopiert. Um dieses Verhalten zu umgehen, sollten Sie die Dateien komprimieren, die komprimierte Datei kopieren und dann mit benutzerdefiniertem Code am gewünschten Speicherort entpacken. | Nein&#42;       |
| referenceObjects      | Array vorhandener verknüpfter Dienste und Datasets. Die referenzierten verknüpften Dienste und Datasets werden im JSON-Format an die benutzerdefinierte Anwendung übergeben, sodass Ihr benutzerdefinierter Code auf Data Factory-Ressourcen verweisen kann. | Nein       |
| extendedProperties    | Benutzerdefinierte Eigenschaften, die im JSON-Format an die benutzerdefinierte Anwendung übergeben werden können, sodass Ihr benutzerdefinierter Code auf zusätzliche Eigenschaften verweisen kann. | Nein       |
| retentionTimeInDays | Die Vermerkdauer für die Dateien, die für die benutzerdefinierte Aktivität übermittelt werden. Der Standardwert ist 30 Tage. | Nein |

&#42; Die Eigenschaften `resourceLinkedService` und `folderPath` müssen entweder beide angegeben oder beide weggelassen werden.

> [!NOTE]
> Wenn Sie in der benutzerdefinierten Aktivität verknüpfte Dienste als referenceObjects übergeben, wird aus Sicherheitsgründen empfohlen, einen Azure Key Vault-fähigen verknüpften Dienst zu übergeben (da keine sicheren Zeichenfolgen enthalten sind) und die Anmeldeinformationen anhand des Geheimnisnamens direkt aus dem Code von Key Vault abzurufen. Sie finden [hier](https://github.com/nabhishek/customactivity_sample/tree/linkedservice) ein Beispiel, das auf einen mit Azure Key Vault (AKV) verknüpften Dienst verweist, die Anmeldeinformationen von Key Vault abruft und dann auf den Speicher im Code zugreift.

## <a name="custom-activity-permissions"></a>Berechtigungen für benutzerdefinierte Aktivitäten

Die benutzerdefinierte Aktivität legt das automatische Benutzerkonto von Azure Batch auf *Zugriff ohne Administratorrechte mit Aufgabenbereich* (die Standardspezifikation für den automatischen Benutzer) fest. Sie können die Berechtigungsebene für das automatische Benutzerkonto nicht ändern. Weitere Informationen finden Sie unter [Ausführen von Aufgaben unter Benutzerkonten in Batch | Automatische Benutzerkonten](../batch/batch-user-accounts.md#auto-user-accounts).

## <a name="executing-commands"></a>Ausführen von Befehlen

Mithilfe einer benutzerdefinierten Aktivität können Sie direkt einen Befehl ausführen. Im folgenden Beispiel wird der „Echo Hello World“-Befehl auf dem Azure Batch-Poolzielknoten ausgeführt und in „stdout“ ausgegeben.

```json
{
  "name": "MyCustomActivity",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "cmd /c echo hello world"
      }
    }]
  }
}
```

## <a name="passing-objects-and-properties"></a>Übergeben von Objekten und Eigenschaften

Dieses Beispiel zeigt, wie Sie „referenceObjects“ und „extendedProperties“ verwenden können, um Data Factory-Objekte und benutzerdefinierte Eigenschaften an Ihre benutzerdefinierte Anwendung zu übergeben.

```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {          
          "connectionString": {
            "type": "SecureString",
            "value": "aSampleSecureString"
          },
          "PropertyBagPropertyName1": "PropertyBagValue1",
          "propertyBagPropertyName2": "PropertyBagValue2",
          "dateTime1": "2015-04-12T12:13:14Z"
        }
      }
    }]
  }
}
```

Wenn die Aktivität ausgeführt wird, werden „referenceObjects“ und „extendedProperties“ in folgenden Dateien gespeichert, die im selben Ausführungsordner von „SampleApp.exe“ bereitgestellt werden:

- `activity.json`

  Speichert „extendedProperties“ und Eigenschaften der benutzerdefinierten Aktivität.

- `linkedServices.json`

  Speichert ein Array verknüpfter Dienste, die in der „referenceObjects“-Eigenschaft definiert sind.

- `datasets.json`

  Speichert ein Array von Datasets, die in der „referenceObjects“-Eigenschaft definiert sind.

Der folgende Beispielcode veranschaulicht, wie „SampleApp.exe“ auf die erforderlichen Informationen in JSON-Dateien zugreifen kann:

```csharp
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.accountName);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>Abrufen von Ausführungsausgaben

Sie können mit dem folgenden PowerShell-Befehl eine Pipelineausführung starten:

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
```

Wenn die Pipeline ausgeführt wird, können Sie mit den folgenden Befehlen die Ausgabe der Ausführung überprüfen:

```powershell
while ($True) {
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

    if(!$result) {
        Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
    }
    elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
        Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
    }
    else {
        Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
        $result
        break
    }
    ($result | Format-List | Out-String)
    Start-Sleep -Seconds 15
}

Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
$result.Output -join "`r`n"

Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
$result.Error -join "`r`n"
```

Die Angaben für **stdout** und **stderr** Ihrer benutzerdefinierten Anwendung werden im Container **adfjobs** im verknüpften Azure Storage-Dienst gespeichert, den Sie beim Erstellen des verknüpften Azure Batch-Diensts mit einer GUID der Aufgabe definiert haben. Den detaillierten Pfad finden Sie in der Ausgabe der Aktivitätsausführung, wie im folgenden Ausschnitt gezeigt:

```
Pipeline ' MyCustomActivity' run finished. Result:

ResourceGroupName : resourcegroupname
DataFactoryName   : datafactoryname
ActivityName      : MyCustomActivity
PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
PipelineName      : MyCustomActivity
Input             : {command}
Output            : {exitcode, outputs, effectiveIntegrationRuntime}
LinkedServiceName :
ActivityRunStart  : 10/5/2017 3:33:06 PM
ActivityRunEnd    : 10/5/2017 3:33:28 PM
DurationInMs      : 21203
Status            : Succeeded
Error             : {errorCode, message, failureType, target}

Activity Output section:
"exitcode": 0
"outputs": [
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
]
"effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
Activity Error section:
"errorCode": ""
"message": ""
"failureType": ""
"target": "MyCustomActivity"
```

Wenn Sie den Inhalt von „stdout.txt“ in nachgelagerten Aktivitäten nutzen möchten, können Sie den Pfad zur Datei „stdout.txt“ im Ausdruck „\@activity('MyCustomActivity').output.outputs[0]“ abrufen.

> [!IMPORTANT]
> - Die Dateien „activity.json“, „linkedServices.json“ und „datasets.json“ werden im Ordner „runtime“ der Batch-Aufgabe gespeichert. In diesem Beispiel werden die Dateien „activity.json“, „linkedServices.json“ und „datasets.json“ im Pfad `"https://adfv2storage.blob.core.windows.net/adfjobs/\<GUID>/runtime/"` gespeichert. Bei Bedarf müssen diese separat bereinigt werden.
> - Für verknüpfte Dienste, die die selbstgehostete Integration Runtime verwenden, werden vertrauliche Informationen wie Schlüssel oder Kennwörter von der selbstgehosteten Integration Runtime verschlüsselt, um sicherzustellen, dass die Anmeldeinformationen in der vom Kunden definierten privaten Netzwerkumgebung verbleiben. Einige sensible Felder können fehlen, wenn auf sie von Ihrem eigenen Anwendungscode auf diese Weise verwiesen wird. Verwenden Sie bei Bedarf „SecureString“ in „extendedProperties“ anstelle des Verweises auf den verknüpften Dienst.

## <a name="pass-outputs-to-another-activity"></a>Übergeben von Ausgaben an eine andere Aktivität

Sie können benutzerdefinierte Werte aus Ihrem Code in einer benutzerdefinierten Aktivität an Azure Data Factory zurückgeben. Schreiben Sie sie hierzu von Ihrer Anwendung aus in `outputs.json`. Data Factory kopiert den Inhalt von `outputs.json` und fügt ihn als Wert der Eigenschaft `customOutput` an die Aktivitätsausgabe an. (Die maximale Größe beträgt 2 MB.) Wenn Sie den Inhalt von `outputs.json` in Downstreamaktivitäten nutzen möchten, können Sie den Wert unter Verwendung des Ausdrucks `@activity('<MyCustomActivity>').output.customOutput` abrufen.

## <a name="retrieve-securestring-outputs"></a>Abrufen von SecureString-Ausgaben

Vertrauliche Eigenschaftswerte, die als Typ *SecureString* definiert sind (wie in einigen der Beispiele in diesem Artikel gezeigt), werden auf der Registerkarte „Überwachung“ in der Benutzeroberfläche von Data Factory ausgeblendet.  Bei der tatsächlichen Ausführung der Pipeline wird jedoch eine *SecureString*-Eigenschaft als JSON innerhalb der `activity.json`-Datei als Nur-Text serialisiert. Beispiel:

```json
"extendedProperties": {
  "connectionString": {
    "type": "SecureString",
    "value": "aSampleSecureString"
  }
}
```

Diese Serialisierung ist nicht wirklich sicher und soll auch nicht sicher sein. Die Absicht ist, Data Factory darauf hinzuweisen, den Wert auf der Registerkarte „Überwachung“ zu maskieren.

Um auf Eigenschaften vom Typ *SecureString* von einer benutzerdefinierten Aktivität aus zuzugreifen, lesen Sie die Datei `activity.json`, die sich im gleichen Ordner wie Ihre EXE-Datei befindet, deserialisieren Sie den JSON-Code, und greifen Sie dann auf die JSON-Eigenschaft zu (extendedProperties => [eigenschaftenName] => Wert).

## <a name="compare-v2-custom-activity-and-version-1-custom-dotnet-activity"></a><a name="compare-v2-v1"></a> Vergleich: Benutzerdefinierte V2-Aktivität und Version 1 der DotNet-Aktivität (benutzerdefiniert)

In Azure Data Factory Version 1 implementieren Sie eine (benutzerdefinierte) DotNet-Aktivität, indem Sie ein .NET-Klassenbibliotheksprojekt mit einer Klasse erstellen, die die `Execute`-Methode der `IDotNetActivity`-Schnittstelle implementiert. Die verknüpften Dienste, Datasets und erweiterten Eigenschaften in der JSON-Nutzlast einer (benutzerdefinierten) DotNet-Aktivität werden als stark typisierte Objekte an die Ausführungsmethode übergeben. Ausführliche Informationen zum Verhalten von Version 1 finden Sie unter [(Benutzerdefinierte) DotNet-Aktivität in Version 1](v1/data-factory-use-custom-activities.md). Aufgrund dieser Implementierung muss Ihr Code von Version 1 auf die DotNet-Aktivität auf .NET Framework 4.5.2 ausgerichtet sein. Version 1 der DotNet-Aktivität muss auch auf Windows-basierten Knoten des Azure Batch-Pools ausgeführt werden.

In der benutzerdefinierten Aktivität von Azure Data Factory V2 muss keine .NET-Schnittstelle implementiert werden. Sie können jetzt Befehle, Skripts und Ihren eigenen benutzerdefinierten, als ausführbare Datei kompilierten Code direkt ausführen. Zum Konfigurieren dieser Implementierung geben Sie die `Command`-Eigenschaft zusammen mit der `folderPath`-Eigenschaft an. Die benutzerdefinierte Aktivität lädt die ausführbare Datei und ihre Abhängigkeiten in `folderpath` hoch und führt den Befehl für Sie aus.

Ihre ausführbare Datei kann auf die verknüpften Dienste, Datasets (definiert in referenceObjects) und erweiterten Eigenschaften, die in der JSON-Nutzlast einer benutzerdefinierten Data Factory V2-Aktivität definiert sind, als JSON-Dateien zugreifen. Mit einem JSON-Serialisierungsmodul können Sie auf die gewünschten Eigenschaften zugreifen, wie im obigen Codebeispiel von „SampleApp.exe“ gezeigt wird.

Mit den Änderungen, die in der benutzerdefinierten Aktivität von Data Factory V2 eingeführt wurden, können Sie eine eigene benutzerdefinierte Codelogik in Ihrer bevorzugten Programmiersprache schreiben und diese unter den von Azure Batch unterstützten Betriebssystemen Windows und Linux ausführen.

In der folgenden Tabelle ist der Unterschied zwischen der benutzerdefinierten Aktivität in Azure Data Factory V2 und der (benutzerdefinierten) DotNet-Aktivität in Azure Data Factory Version 1 beschrieben:

|Unterschiede      | Benutzerdefinierte Aktivität      | (Benutzerdefinierte) DotNet-Aktivität in Version 1      |
| ---- | ---- | ---- |
|Definition benutzerdefinierter Logik      |Durch Bereitstellen einer ausführbaren Datei      |Durch Implementieren einer .NET-DLL      |
|Ausführungsumgebung der benutzerdefinierten Logik      |Windows oder Linux      |Windows (.NET Framework 4.5.2)      |
|Ausführung von Skripts      |Direkte Unterstützung der Ausführung von Skripts (z.B. „cmd /c echo hello world“ auf Windows-VM)      |Erfordert Implementierung in die .NET-DLL      |
|Dataset erforderlich      |Optional      |Erforderlich, um Aktivitäten zu verketten und Informationen weiterzugeben      |
|Übergeben von Informationen von der Aktivität an benutzerdefinierte Logik      |Über „ReferenceObjects“ („LinkedServices“ und „Datasets“) und „ExtendedProperties“ (benutzerdefinierte Eigenschaften)      |Über „ExtendedProperties“ (benutzerdefinierte Eigenschaften), Eingabe und Ausgabedatasets      |
|Abrufen von Informationen in benutzerdefinierte Logik      |Analysieren der Dateien „activity.json“, „linkedServices.json“ und „datasets.json“, die in demselben Ordner wie die ausführbare Datei gespeichert sind      |Über .NET SDK (.NET Framework 4.5.2)      |
|Protokollierung      |Schreibt direkt in STDOUT      |Implementieren der Protokollierung in eine .NET-DLL      |

Wenn Sie über .NET-Code verfügen, der für eine (benutzerdefinierte) DotNet-Aktivität in Version 1 geschrieben wurde, müssen Sie Ihren Code ändern, damit er für die aktuelle Version der benutzerdefinierten Aktivität funktioniert. Aktualisieren Sie den Code, indem Sie sich an die folgenden allgemeinen Richtlinien halten:

  - Ändern Sie das Projekt von einer .NET-Klassenbibliothek in eine Konsolen-App.
  - Starten Sie die Anwendung mit der `Main`-Methode. Die `Execute`-Methode der `IDotNetActivity`-Schnittstelle ist nicht mehr erforderlich.
  - Lesen und analysieren Sie die verknüpften Dienste, Datasets und Aktivitäten mit einem JSON-Serialisierungsmodul und nicht als stark typisierte Objekte. Übergeben Sie die Werte von erforderlichen Eigenschaften an Ihre eigene benutzerdefinierte Codelogik. Ein Beispiel hierfür finden Sie im obigen Code von „SampleApp.exe“.
  - Das Logger-Objekt wird nicht mehr unterstützt. Die Ausgabe Ihrer ausführbaren Datei kann auf der Konsole ausgegeben werden und wird in „stdout.txt“ gespeichert.
  - Das NuGet-Paket „Microsoft.Azure.Management.DataFactories“ ist nicht mehr erforderlich.
  - Kompilieren Sie Ihren Code, laden Sie ausführbare Dateien und die dazugehörigen Abhängigkeiten in Azure Storage hoch, und definieren Sie den Pfad in der `folderPath`-Eigenschaft.

Ein vollständiges Beispiel dafür, wie das Beispiel mit der End-to-End-DLL und der Pipeline aus dem Artikel [Verwenden von benutzerdefinierten Aktivitäten in einer Azure Data Factory-Pipeline](./v1/data-factory-use-custom-activities.md) zu Data Factory Version 1 in eine benutzerdefinierte Data Factory-Aktivität umgeschrieben werden kann, finden Sie im [Beispiel zur benutzerdefinierten Data Factory-Aktivität](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFv2CustomActivitySample).

## <a name="auto-scaling-of-azure-batch"></a>Automatische Skalierung von Azure Batch

Sie können einen Azure Batch-Pool auch mit dem Feature **Automatisch skalieren** erstellen. Sie können z.B. einen Azure Batch-Pool ohne dedizierte VM erstellen und dabei eine Formel für die automatische Skalierung angeben, die von der Anzahl der ausstehenden Aufgaben abhängig ist.

Mit dieser Beispielformel wird folgendes Verhalten erreicht: Nachdem der Pool erstellt wurde, wird er mit einer VM gestartet. Die Metrik „$PendingTasks“ legt die Anzahl der Aufgaben im ausgeführten und im aktiven (in der Warteschlange) Zustand fest. Die Formel sucht nach der durchschnittlichen Anzahl ausstehender Aufgaben in den letzten 180 Sekunden und legt TargetDedicated auf den entsprechenden Wert fest. Dadurch wird sichergestellt, dass TargetDedicated nie die Anzahl von 25 virtuellen Computern überschreitet. Wenn also neue Aufgaben gesendet werden, wächst der Pool automatisch an. Beim Abschluss von Aufgaben werden virtuelle Computer nacheinander frei, und durch die automatische Skalierung werden diese virtuellen Computer reduziert. startingNumberOfVMs und maxNumberofVMs können entsprechend den jeweiligen Anforderungen angepasst werden.

Formel für die automatische Skalierung:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Weitere Informationen hierzu finden Sie unter [Automatisches Skalieren von Computeknoten in einem Azure Batch-Pool](../batch/batch-automatic-scaling.md) .

Wenn der Pool die Standardeinstellung für [autoScaleEvaluationInterval](/rest/api/batchservice/pool/enableautoscale)verwendet, kann es 15 bis 30 Minuten dauern, bis der Batch-Dienst den virtuellen Computer vorbereitet hat und die benutzerdefinierte Aktivität ausgeführt wird. Wenn der Pool eine andere Einstellung für „autoScaleEvaluationInterval“ nutzt, könnte der Batch-Dienst „autoScaleEvaluationInterval“ + 10 Minuten verwenden.

## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln erfahren Sie, wie Daten auf andere Weisen transformiert werden:

* [U-SQL-Aktivität](transform-data-using-data-lake-analytics.md)
* [Hive-Aktivität](transform-data-using-hadoop-hive.md)
* [Pig-Aktivität](transform-data-using-hadoop-pig.md)
* [MapReduce-Aktivität](transform-data-using-hadoop-map-reduce.md)
* [Hadoop-Streamingaktivität](transform-data-using-hadoop-streaming.md)
* [Spark-Aktivität](transform-data-using-spark.md)
* [Batchausführungsaktivität für Azure Machine Learning Studio (klassisch)](transform-data-using-machine-learning.md)
* [Aktivität „Gespeicherte Prozedur“](transform-data-using-stored-procedure.md)