---
title: Continuous Integration (CI) und Continuous Devolpment (CD) mit dem Azure Stream Analytics CI/CD npm-Paket
description: In diesem Artikel wird beschrieben, wie Sie Continuous Integration und Continuous Deployment mithilfe des Azure Stream Analytics CI/CD npm-Pakets einrichten.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: fa5a57afa379c6bbe027be80f400fc176800d289
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158507"
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-cicd-npm-package"></a>Continuous Integration (CI) und Continuous Devolpment (CD) mit dem Stream Analytics CI/CD npm-Paket
In diesem Artikel wird beschrieben, wie Sie Continuous Integration und Continuous Deployment mithilfe des Azure Stream Analytics CI/CD npm-Pakets einrichten.

## <a name="build-the-vs-code-project"></a>Erstellen des VS CodeProjekts

Sie können Continuous Integration und Continuous Deployment für Azure Stream Analytics-Aufträge mithilfe des npm-Pakets **asa-streamanalytics-cicd** aktivieren. Das npm-Paket stellt die Tools für die Generierung von Azure Resource Manager-Vorlagen von [Stream Analytics-Projekten in Visual Studio Code](quick-create-vs-code.md) bereit. Es kann ohne die Installation von Visual Studio Code unter Windows, macOS und Linux verwendet werden.

Nachdem Sie [das Paket heruntergeladen haben](https://www.npmjs.com/package/azure-streamanalytics-cicd), verwenden Sie den folgenden Befehl, um die Azure Resource Manager-Vorlagen auszugeben. Das **scriptPath**-Argument ist der absolute Pfad zu der **asaql**-Datei in Ihrem Projekt. Stellen Sie sicher, dass sich die Dateien „asaproj.json“ und „JobConfig.json“ im selben Ordner wie die Skriptdatei befinden. Wenn **outputPath** nicht angegeben ist, werden die Vorlagen in den Ordner **Bereitstellen** im **bin**-Ordner des Projekts platziert.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Beispiel (unter macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Wenn ein Stream Analytics Visual Studio Code-Projekt erfolgreich erstellt wurde, generiert es die folgenden beiden Azure Resource Manager-Vorlagendateien im Ordner **bin/[Debug/Retail]/Deploy**: 

*  Resource Manager-Vorlagendatei

       [ProjectName].JobTemplate.json 

*  Resource Manager-Parameterdatei

       [ProjectName].JobTemplate.parameters.json   

Die Standardparameter in der „parameters.json“-Datei werden aus den Einstellungen im Visual Studio Code-Projekt entnommen. Wenn Sie die Bereitstellung in einer anderen Umgebung ausführen möchten, ersetzen Sie die Parameter entsprechend.

> [!NOTE]
> Für alle Anmeldeinformationen sind die Standardwerte auf NULL festgelegt. Sie **müssen** die Werte festlegen, bevor Sie eine Bereitstellung in der Cloud ausführen.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Erfahren Sie mehr über das [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Weitere Informationen finden Sie unter [Use an object as a parameter in an Azure Resource Manager template](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters) (Verwenden eines Objekts als Parameter in einer Azure Resource Manager-Vorlage).

Wenn Sie die verwaltete Identität für Azure Data Lake Store Gen1 als Ausgabesenke verwenden möchten, müssen Sie vor der Bereitstellung in Azure per PowerShell den Zugriff auf den Dienstprinzipal ermöglichen. Weitere Informationen hierzu finden Sie im Artikel zum Thema [Bereitstellen von ADLS Gen1 mit verwalteter Identität per Resource Manager-Vorlage](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).
## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erstellen eines Azure Stream Analytics-Cloudauftrags in Visual Studio Code (Vorschauversion)](quick-create-vs-code.md)
* [Lokales Testen von Stream Analytics-Abfragen mit Visual Studio Code (Vorschauversion)](vscode-local-run.md)
* [Erkunden von Azure Stream Analytics mit Visual Studio Code (Vorschauversion)](vscode-explore-jobs.md)
