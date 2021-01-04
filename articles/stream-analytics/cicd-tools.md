---
title: Automatisieren von Builds, Tests und Bereitstellungen eines Azure Stream Analytics-Auftrags mit CI/CD-Tools
description: In diesem Artikel wird beschrieben, wie Sie mit CI/CD-Tools von Azure Stream Analytics ein Azure Stream Analytics-Projekt automatisch erstellen, testen und bereitstellen.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: aa75a553ffc131f4827aa045849f1317d894ddc5
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123149"
---
# <a name="automate-builds-tests-and-deployments-of-an-azure-stream-analytics-job-using-cicd-tools"></a>Automatisieren von Builds, Tests und Bereitstellungen eines Azure Stream Analytics-Auftrags mit CI/CD-Tools

Sie können mit dem CI/CD-npm-Paket von Azure Stream Analytics Ihre Visual Studio Code- oder Visual Studio-Projekte für Azure Stream Analytics automatisch erstellen, testen und bereitstellen. Die Projekte können mithilfe von Entwicklungstools erstellt oder aus vorhandenen Stream Analytics-Aufträgen exportiert werden. In diesem Artikel wird beschrieben, wie Sie das npm-Paket mit einem beliebigen CI/CD-System verwenden. Informationen zum Bereitstellen mit Azure Pipelines finden Sie unter [Erstellen einer CI/CD-Pipeline für einen Stream Analytics-Auftrag mit Azure DevOps](set-up-cicd-pipeline.md).

## <a name="installation"></a>Installation

Sie können das [Paket direkt herunterladen](https://www.npmjs.com/package/azure-streamanalytics-cicd) oder es mit dem `npm install -g azure-streamanalytics-cicd`-Befehl [global](https://docs.npmjs.com/downloading-and-installing-packages-globally) installieren. Es wird empfohlen, den Befehl zu verwenden. Dieser kann auch in einem PowerShell- oder Azure CLI-Skripttask einer Buildpipeline in **Azure Pipelines** verwendet werden.

## <a name="build-the-project"></a>Erstellen des Projekts

Das npm-Paket **asa-streamanalytics-cicd** stellt die Tools für die Generierung von Azure Resource Manager-Vorlagen von [Visual Studio Code-Projekten](./quick-create-visual-studio-code.md) oder [Visual Studio-Projekten](stream-analytics-quick-create-vs.md) für Stream Analytics bereit. Sie können das npm-Paket auch unter Windows, macOS und Linux verwenden, ohne Visual Studio Code oder Visual Studio zu installieren.

Nachdem Sie das Paket installiert haben, verwenden Sie den folgenden Befehl, um die Stream Analytics-Projekte zu erstellen.

```powershell
azure-streamanalytics-cicd build -project <projectFullPath> [-outputPath <outputPath>]
```

Der *build* -Befehl führt eine Überprüfung der Schlüsselwortsyntax durch und gibt die Azure Resource Manager-Vorlage aus.

| Parameter | Beschreibung |
|---|---|
| `-project` | Der absolute Pfad der Datei **asaproj.json** für das Visual Studio Code-Projekt oder der Datei **[Name Ihres Projekts].asaproj** für das Visual Studio-Projekt. |
| `-outputPath` | Der Pfad des Ausgabeordners für Azure Resource Manager-Vorlagen. Wenn er nicht angegeben ist, werden die Vorlagen im aktuellen Verzeichnis abgelegt. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/samplejob.asaproj"
```

--- 

Wenn ein Stream Analytics-Projekt erfolgreich erstellt wurde, werden im Ausgabeordner die folgenden beiden Dateien generiert:

* Azure Resource Manager-Vorlagendatei

   `[ProjectName].JobTemplate.json`

* Azure Resource Manager-Parameterdatei

   `[ProjectName].JobTemplate.parameters.json`

Die Standardparameter in der Datei „parameters.json“ werden aus den Einstellungen im Visual Studio Code- oder Visual Studio-Projekt übernommen. Wenn Sie die Bereitstellung in einer anderen Umgebung ausführen möchten, ersetzen Sie die Parameter entsprechend.

Die Standardwerte für alle Anmeldeinformationen sind **NULL**. Sie müssen die Werte festlegen, bevor Sie eine Bereitstellung in Azure ausführen.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

Wenn Sie die verwaltete Identität für Azure Data Lake Store Gen1 als Ausgabesenke verwenden möchten, müssen Sie vor der Bereitstellung in Azure per PowerShell den Zugriff auf den Dienstprinzipal ermöglichen. Weitere Informationen hierzu finden Sie im Artikel zum Thema [Bereitstellen von ADLS Gen1 mit verwalteter Identität per Resource Manager-Vorlage](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).

## <a name="local-run"></a>Lokaler Testlauf

Wenn in Ihrem Projekt lokale Eingabedateien angegeben sind, können Sie ein Stream Analytics-Skript lokal ausführen, indem Sie den `localrun`-Befehl verwenden.

```powershell
azure-streamanalytics-cicd localrun -project <projectFullPath> [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| Parameter | Beschreibung |
|---|---|
| `-project` | Der Pfad der Datei **asaproj.json** für das Visual Studio Code-Projekt oder der Datei **[Name Ihres Projekts].asaproj** für das Visual Studio-Projekt. |
| `-outputPath` | Der Pfad des Ausgabeordners. Wenn er nicht angegeben ist, werden die Ausgabeergebnisdateien im aktuellen Verzeichnis abgelegt. |
| `-customCodeZipFilePath` | Der Pfad der ZIP-Datei für benutzerdefinierten C#-Code, z. B. eine UDF oder ein Deserialisierer, sofern verwendet. Verpacken Sie die DLLs in einer ZIP-Datei, und geben Sie diesen Pfad an. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```

---

> [!Note] 
> JavaScript-UDFs können nur unter Windows verwendet werden.

## <a name="automated-test"></a>Automatisierter Test

Sie können mit dem CI/CD-npm-Paket automatisierte Tests für Ihr Stream Analytics-Skript konfigurieren und ausführen.

### <a name="add-a-test-case"></a>Hinzufügen eines Testfalls

Die Testfälle werden in einer Testkonfigurationsdatei beschrieben. Verwenden Sie zum Einstieg den `addtestcase`-Befehl, um der Testkonfigurationsdatei eine Testfallvorlage hinzuzufügen. Wenn die Testkonfigurationsdatei nicht vorhanden ist, wird sie standardmäßig erstellt.

```powershell
azure-streamanalytics-cicd addtestcase -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>]
```

| Parameter | Beschreibung |
|---|---|
| `-project` | Der Pfad der Datei **asaproj.json** für das Visual Studio Code-Projekt oder der Datei **[Name Ihres Projekts].asaproj** für das Visual Studio-Projekt. |
| `-testConfigPath` | Der Pfad der Testkonfigurationsdatei. Wenn er nicht angegeben ist, wird die Datei in **\test** unter dem aktuellen Verzeichnis der Datei **asaproj.json** gesucht. Der Standarddateiname lautet **testConfig.json**. Falls die Datei noch nicht vorhanden ist, wird eine neue Datei erstellt. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```
---

Wenn die Testkonfigurationsdatei leer ist, wird der folgende Inhalt in die Datei geschrieben. Andernfalls wird dem Array von **TestCases** ein Testfall hinzugefügt. Erforderliche Eingabekonfigurationen werden automatisch entsprechend den Eingabekonfigurationsdateien festgelegt, sofern vorhanden. Andernfalls werden Standardwerte konfiguriert. Vor dem Ausführen des Tests muss der **FilePath** jeder Eingabe und erwarteten Ausgabe angegeben werden. Sie können die Konfiguration manuell ändern.

Wenn bei der Testvalidierung eine bestimmte Ausgabe ignoriert werden soll, legen Sie das Feld **Required** der erwarteten Ausgabe auf **false** fest.

```json
{
  "Script": "",
  "TestCases": [
    {
      "Name": "Case 1",
      "Inputs": [
        {
          "InputAlias": [Input alias string],
          "Type": "Data Stream",
          "Format": "JSON",
          "FilePath": [Required],
          "ScriptType": "InputMock"
        }
      ],
      "ExpectedOutputs": [
        {
          "OutputAlias": [Output alias string],
          "FilePath": "Required",
          "Required": true
        }
      ]
    }
  ]
}
```

### <a name="run-a-unit-test"></a>Ausführen eines Komponententests

Sie können mit dem folgenden Befehl mehrere Testfälle für Ihr Projekt ausführen. Im Ausgabeordner wird eine Zusammenfassung der Testergebnisse generiert. Für alle erfolgreichen Tests wird der Prozess mit dem Code **0** beendet. Wenn eine Ausnahme aufgetreten ist, wird er mit dem Code **–1** beendet, und für Tests, bei denen ein Fehler aufgetreten ist, wird der Prozess mit dem Code **2** beendet.

```powershell
azure-streamanalytics-cicd test -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>] [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| Parameter | Beschreibung |
|---|---|
| `-project` | Der Pfad der Datei **asaproj.json** für das Visual Studio Code-Projekt oder der Datei **[Name Ihres Projekts].asaproj** für das Visual Studio-Projekt. |
| `-testConfigPath` | Der Pfad zur Testkonfigurationsdatei. Wenn er nicht angegeben ist, wird die Datei in **\test** unter dem aktuellen Verzeichnis der Datei **asaproj.json** gesucht. Der Standarddateiname lautet **testConfig.json**.
| `-outputPath` | Der Pfad des Ausgabeordners für Testergebnisse. Wenn er nicht angegeben ist, werden die Ausgabeergebnisdateien im aktuellen Verzeichnis abgelegt. |
| `-customCodeZipFilePath` | Der Pfad der ZIP-Datei für benutzerdefinierten Code, z. B. eine UDF oder ein Deserialisierer, sofern verwendet. |

Wenn alle Tests abgeschlossen sind, wird im Ausgabeordner eine Zusammenfassung der Testergebnisse im JSON-Format generiert. Der Name der Zusammenfassungsdatei lautet **testResultSummary.json**.

```json
{
  "Total": (integer) total_number_of_test_cases,
  "Passed": (integer) number_of_passed_test_cases,
  "Failed": (integer) number_of_failed_test_cases,
  "Script": (string) absolute_path_to_asaql_file,
  "Results": [ (array) detailed_results_of_test_cases
    {
      "Name": (string) name_of_test_case,
      "Status": (integer) 0(passed)_or_1(failed),
      "Time": (string) time_span_of_running_test_case,
      "OutputMatched": [ (array) records_of_actual_outputs_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputNotEqual": [ (array) records_of_actual_outputs_not_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputMissing": [ (array) records_of_actual_outputs_missing
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": ""
        }
      ],
      "OutputUnexpected": [ (array) records_of_actual_outputs_unexpected
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": "",
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputUnrequired": [ (array) records_of_actual_outputs_unrequired_to_be_checked
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ]
    }
  ],
  "Time": (string) time_span_of_running_all_test_cases,
}
```

## <a name="deploy-to-azure"></a>In Azure bereitstellen

Sie können mit der Azure Resource Manager-Vorlage und den vom Build generierten Parameterdateien [den Auftrag in Azure bereitstellen](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md?tabs=azure-powershell#deploy-template).

## <a name="next-steps"></a>Nächste Schritte

* [Continuous Integration und Continuous Deployment für Azure Stream Analytics](cicd-overview.md)
* [Einrichten der CI/CD-Pipeline für einen Stream Analytics-Auftrag mithilfe von Azure Pipelines](set-up-cicd-pipeline.md)