---
title: Verwenden des CI/CD-NuGet-Pakets von Azure Stream Analytics
description: In diesem Artikel wird beschrieben, wie Sie Continuous Integration und Continuous Deployment mithilfe des Azure Stream Analytics CI/CD NuGet-Pakets einrichten.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/15/2019
ms.openlocfilehash: c05db2d9ba184da89665a236994c851355cc2644
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96019853"
---
# <a name="use-the-azure-stream-analytics-cicd-nuget-package-for-integration-and-development"></a>Verwenden des CI/CD-NuGet-Pakets von Azure Stream Analytics für die Integration und Entwicklung 
In diesem Artikel wird beschrieben, wie Sie Continuous Integration und Continuous Deployment mithilfe des Azure Stream Analytics CI/CD NuGet-Pakets einrichten.

Verwenden Sie Version 2.3.0000.0 oder höher der [Stream Analytics-Tools für Visual Studio](./stream-analytics-quick-create-vs.md), um Unterstützung für MSBuild zu erhalten.

Ein NuGet-Paket ist verfügbar: [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Es bietet Tools für MSBuild, lokales Ausführen und Bereitstellung zur Unterstützung von Continuous Integration und Continuous Deployment für [Stream Analytics Visual Studio-Projekte](stream-analytics-vs-tools.md). 
> [!NOTE]
> Das NuGet-Paket kann nur mit Version 2.3.0000.0 oder höher der Stream Analytics-Tools für Visual Studio verwendet werden. Wenn einige Ihrer Projekte in früheren Versionen von Visual Studio-Tools erstellt wurden, öffnen Sie sie einfach mit Version 2.3.0000.0 oder einer höheren Version, und speichern Sie sie. Dann werden die neuen Funktionen aktiviert. 

Weitere Informationen finden Sie unter [Verwenden der Azure Stream Analytics-Tools für Visual Studio](./stream-analytics-quick-create-vs.md).

## <a name="msbuild"></a>MSBuild
Wie bei der standardmäßigen Visual Studio MSBuild-Benutzeroberfläche können Sie zwischen zwei Optionen wählen, um ein Projekt zu erstellen. Sie können mit der rechten Maustaste auf das Projekt klicken und dann **Erstellen** wählen. Sie können auch **MSBuild** im NuGet-Paket von der Befehlszeile aus verwenden.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Wenn ein Stream Analytics Visual Studio-Projekt erfolgreich erstellt wurde, generiert es die folgenden beiden Azure Resource Manager-Vorlagendateien im Ordner **bin/[Debug/Retail]/Deploy**: 

* Resource Manager-Vorlagendatei

   `[ProjectName].JobTemplate.json`

* Resource Manager-Parameterdatei
   
   `[ProjectName].JobTemplate.parameters.json`

Die Standardparameter in der „parameters.json“-Datei werden aus den Einstellungen im Visual Studio-Projekt entnommen. Wenn Sie die Bereitstellung in einer anderen Umgebung ausführen möchten, ersetzen Sie die Parameter entsprechend.

> [!NOTE]
> Für alle Anmeldeinformationen sind die Standardwerte auf NULL festgelegt. Sie **müssen** die Werte festlegen, bevor Sie eine Bereitstellung in der Cloud ausführen.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Erfahren Sie mehr über das [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md). Weitere Informationen finden Sie unter [Use an object as a parameter in an Azure Resource Manager template](/azure/architecture/building-blocks/extending-templates/objects-as-parameters) (Verwenden eines Objekts als Parameter in einer Azure Resource Manager-Vorlage).

Wenn Sie die verwaltete Identität für Azure Data Lake Store Gen1 als Ausgabesenke verwenden möchten, müssen Sie vor der Bereitstellung in Azure per PowerShell den Zugriff auf den Dienstprinzipal ermöglichen. Weitere Informationen hierzu finden Sie im Artikel zum Thema [Bereitstellen von ADLS Gen1 mit verwalteter Identität per Resource Manager-Vorlage](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="command-line-tool"></a>Befehlszeilentool

### <a name="build-the-project"></a>Erstellen des Projekts
Im NuGet-Paket befindet sich ein Befehlszeilentool mit dem Namen **SA.exe**. Es unterstützt Projektbuilds und lokale Tests auf beliebigen Computern. Dies können Sie in Ihrem Continuous Integration- und Continuous Deployment-Verfahren nutzen. 

Die Bereitstellungsdateien befinden sich standardmäßig unter dem aktuellen Verzeichnis. Sie können den Ausgabepfad mithilfe des folgenden OutputPath-Parameters angeben:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Lokales Testen des Skripts

Wenn in Ihrem Projekt lokale Eingabedateien in Visual Studio angegeben werden, können Sie mit dem Befehl *localrun* einen automatischen Skripttest ausführen. Das Ausgabeergebnis wird im aktuellen Verzeichnis abgelegt.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Generieren Sie eine Auftragsdefinitionsdatei für die Verwendung mit der Stream Analytics-PowerShell-API.

Der Befehl *arm* nimmt die generierte Auftragsvorlage und die Parameterdateien für die Auftragsvorlage als Eingabe entgegen. Kombinieren Sie diese dann in einer Auftragsdefinition-JSON-Datei, die mit der Stream Analytics-PowerShell-API verwendet werden kann.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Beispiel:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```



## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erstellen eines Azure Stream Analytics-Cloudauftrags in Visual Studio](stream-analytics-quick-create-vs.md)
* [Lokales Testen von Stream Analytics-Abfragen mit Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Erkunden von Azure Stream Analytics-Aufträgen mit Visual Studio](stream-analytics-vs-tools.md)