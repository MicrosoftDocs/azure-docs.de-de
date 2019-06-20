---
title: End-to-End-Ausführung von Aufträgen mithilfe von Vorlagen – Azure Batch | Microsoft-Dokumentation
description: Erstellen Sie Batch-Pools, -Aufträge und -Aufgaben mit Vorlagendateien und der Azure CLI.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.workload: big-compute
ms.date: 12/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 80d2e995a18a2d6dafbb8d92fdd5996b10eab17c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60783737"
---
# <a name="use-azure-batch-cli-templates-and-file-transfer"></a>Verwenden von Azure Batch-CLI-Vorlagen und Dateiübertragung

Indem Sie eine Azure Batch-Erweiterung für die Azure CLI verwenden, können Sie Batch-Aufträge ausführen, ohne Code schreiben zu müssen.

Erstellen und verwenden Sie die JSON-Vorlagendateien mit der Azure CLI zum Erstellen von Batch-Pools, -Aufträgen und -Aufgaben. Verwenden Sie die Befehle der CLI-Erweiterung, um problemlos Auftragseingabedateien in das Speicherkonto hochzuladen, das dem Batch-Konto zugeordnet ist, und Auftragsausgabedateien herunterzuladen.

## <a name="overview"></a>Übersicht

Durch eine Erweiterung für die Azure CLI kann Batch nahtlos von Benutzern verwendet werden, die keine Entwickler sind. Allein mit CLI-Befehlen können Sie einen Pool erstellen, Eingabedaten hochladen, Aufträge und zugehörige Aufgaben erstellen und die resultierenden Ausgabedaten herunterladen. Es ist kein zusätzlicher Code erforderlich. Führen Sie die CLI-Befehle direkt aus, oder integrieren Sie sie in Skripts.

Batch-Vorlagen basieren auf der [vorhandenen Unterstützung für Batch in der Azure CLI](batch-cli-get-started.md#json-files-for-resource-creation) für JSON-Dateien, um beim Erstellen von Pools, Aufträgen, Aufgaben und anderen Elementen Eigenschaftswerte anzugeben. Mit Batch-Vorlagen werden die folgenden Funktionen hinzugefügt:

-   Parameter können definiert werden. Bei der Verwendung von Vorlagen werden nur die Parameterwerte für die Erstellung des Elements angegeben, während die anderen Elementeigenschaftenwerte im Vorlagentext angegeben werden. Ein Benutzer, der mit Batch und den von Batch ausgeführten Anwendungen vertraut ist, kann Vorlagen erstellen, die Eigenschaftenwerte für Pools, Aufträge und Aufgaben angeben. Ein Benutzer, dem Batch und/oder die Anwendungen weniger vertraut ist, muss lediglich die Werte für die definierten Parameter angeben.

-   Durch Auftragsaufgabenfactorys können mehrere Aufgaben für einen Auftrag erstellt werden, sodass keine zahlreichen Aufgabendefinitionen erstellt werden müssen und die Auftragsübermittlung drastisch vereinfacht wird.


Aufträge nutzen in der Regel Eingabedatendateien und generieren Ausgabedatendateien. Ein Speicherkonto ist standardmäßig mit jedem Batch-Konto verknüpft. Übertragen Sie mithilfe der CLI ohne Codierung und Speicheranmeldeinformationen Dateien in dieses und aus diesem Speicherkonto.

Eine gängige Anwendung für die Verarbeitung von Audio- und Videodateien ist beispielsweise [ffmpeg](https://ffmpeg.org/). Nachfolgend sind die Schritte aufgeführt, die Sie in der Azure Batch-CLI ausführen, um ffmpeg aufzurufen und Videoquelldateien in anderen Auflösungen zu transcodieren.

-   Erstellen Sie eine Poolvorlage. Der Benutzer, der die Vorlage erstellt, weiß, wie die ffmpeg-Anwendung und deren Anforderungen aufgerufen werden. Der Benutzer gibt das entsprechende BS, die VM-Größe, die Methode für die Installation von ffmpeg (z.B. über ein Anwendungspaket oder mithilfe eines Paket-Managers) und andere Pooleigenschaftenwerte an. Parameter werden bei der Verwendung der Vorlage erstellt, nur die Pool-ID und die Anzahl der VMs müssen angegeben werden.

-   Erstellen Sie eine Auftragsvorlage. Der Benutzer, der die Vorlage erstellt, weiß, wie ffmpeg aufgerufen werden muss, um ein Quellvideo in einer anderen Auflösung zu transcodieren, und gibt die Taskbefehlszeile an. Zudem ist ihm bekannt, dass ein Ordner mit den Quellvideodateien vorhanden ist, für die eine Aufgabe pro Eingabedatei erforderlich ist.

-   Ein Endbenutzer, der einen Satz von Videodateien transcodieren muss, erstellt zunächst einen Pool anhand der Poolvorlage, die nur die Pool-ID und die Anzahl der erforderlichen VMs angibt. Danach kann der Benutzer die zu transcodierenden Quelldateien hochladen. Anschließend kann ein Auftrag mithilfe der Auftragsvorlage angegeben werden, die nur die Pool-ID und den Speicherort der hochgeladenen Quelldateien angibt. Der Batch-Auftrag wird erstellt, wobei pro Eingabedatei eine Aufgabe generiert wird. Schließlich können die transcodierten Ausgabedateien heruntergeladen werden.

## <a name="installation"></a>Installation

Führen Sie vor dem Installieren der Azure Batch-CLI-Erweiterung zuerst die [Installation von Azure CLI 2.0](/cli/azure/install-azure-cli) durch, oder führen Sie die Azure CLI in [Azure Cloud Shell](../cloud-shell/overview.md) aus.

Installieren Sie die aktuelle Version der Batch-Erweiterung, indem Sie den folgenden Azure CLI-Befehl verwenden:

```azurecli
az extension add --name azure-batch-cli-extensions
```

Weitere Informationen zur Batch-CLI-Erweiterung und zu zusätzlichen Installationsoptionen finden Sie im [GitHub-Repository](https://github.com/Azure/azure-batch-cli-extensions).


Zum Verwenden der CLI-Erweiterungsfeatures benötigen Sie ein Azure Batch-Konto, und für die Befehle, mit denen Dateien in und aus dem Speicher übertragen werden, ist ein verknüpftes Speicherkonto erforderlich.

Informationen zum Anmelden an einem Batch-Konto mit der Azure CLI finden Sie unter [Verwalten von Batch-Ressourcen mit der Azure CLI](batch-cli-get-started.md).

## <a name="templates"></a>Vorlagen

Azure Batch-Vorlagen sind Azure Resource Manager-Vorlagen in Bezug auf Funktionalität und Syntax ähnlich. Dabei handelt es sich um JSON-Dateien, die Elementeigenschaftennamen und -werte enthalten, jedoch folgende zusätzliche Hauptkonzepte aufweisen:

-   **Parameter**

    -   Sie ermöglichen die Angabe von Eigenschaftenwerten in einem Textabschnitt, wobei Parameterwerte nur bei der Verwendung der Vorlage angegeben werden müssen. Beispielsweise könnte die vollständige Definition für einen Pool in den Text eingefügt und nur ein Parameter für die Pool-ID definiert werden. Für die Erstellung eines Pools müsste dann nur eine Pool-ID-Zeichenfolge angegeben werden.
        
    -   Der Vorlagentext kann von einem Benutzer mit Kenntnissen zu Batch und den von Batch ausgeführten Anwendungen erstellt werden. Bei der Verwendung der Vorlage müssen nur Werte für die vom Autor definierten Parameter angegeben werden. Daher kann ein Benutzer ohne fundierte Kenntnisse zu Batch und/oder den Anwendungen die Vorlagen verwenden.

-   **Variablen**

    -   Durch Variablen können einfache oder komplexe Parameterwerte an einer Stelle angegeben und an einer oder mehreren Stellen im Vorlagentext verwendet werden. Durch Variablen können Vorlagen vereinfacht, deren Größe reduziert und die Verwaltung vereinfacht werden, da Eigenschaften nur an einem Ort geändert werden müssen.

-   **Konstrukte auf höheren Ebenen**

    -   Einige in der Vorlage vorhandenen Konstrukte auf höheren Ebenen sind noch nicht in den Batch-APIs verfügbar. Beispielsweise kann eine Aufgabenfactory in einer Auftragsvorlage definiert werden, die mittels einer allgemeinen Aufgabendefinition mehrere Aufgaben für den Auftrag erstellt. Diese Konstrukte machen eine Codierung hinfällig, sodass mehrere JSON-Dateien dynamisch erstellt werden können (z.B. eine Datei pro Aufgabe). Zudem können Skriptdateien für die Installation von Anwendungen über einen Paket-Manager erstellt werden.

    -   Diese Konstrukte können dem Batch-Dienst hinzugefügt werden und sind in den Batch-APIs, auf den Benutzeroberflächen usw. verfügbar.

### <a name="pool-templates"></a>Poolvorlagen

Poolvorlagen unterstützen die Standardvorlagenfunktionen von Parametern und Variablen. Außerdem unterstützen sie das folgende Konstrukt auf höherer Ebene:

-   **Paketverweise**

    -   Optional kann mithilfe des Paket-Managers Software auf Poolknoten kopiert werden. Der Paket-Manager und die Paket-ID werden angegeben. Da Sie ein oder mehrere Pakete deklarieren können, müssen keine Skripts zum Abrufen der erforderlichen Pakete erstellt, Skripts installiert und Skripts in jedem Poolknoten ausgeführt werden.

Nachfolgend sehen Sie ein Beispiel für eine Vorlage, die einen Pool mit virtuellen Linux-Computern mit installierter ffmpeg-Anwendung erstellt. Geben Sie zur Verwendung nur eine Pool-ID-Zeichenfolge und die Anzahl von virtuellen Computern im Pool an:

```json
{
    "parameters": {
        "nodeCount": {
            "type": "int",
            "metadata": {
                "description": "The number of pool nodes"
            }
        },
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The pool ID "
            }
        }
    },
    "pool": {
        "type": "Microsoft.Batch/batchAccounts/pools",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('poolId')]",
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "Canonical",
                    "offer": "UbuntuServer",
                    "sku": "16.04-LTS",
                    "version": "latest"
                },
                "nodeAgentSKUId": "batch.node.ubuntu 16.04"
            },
            "vmSize": "STANDARD_D3_V2",
            "targetDedicatedNodes": "[parameters('nodeCount')]",
            "enableAutoScale": false,
            "maxTasksPerNode": 1,
            "packageReferences": [
                {
                    "type": "aptPackage",
                    "id": "ffmpeg"
                }
            ]
        }
    }
}
```

Wenn die Vorlagendatei unter dem Namen _pool-ffmpeg.json_ gespeichert wurde, rufen Sie die Vorlage wie folgt auf:

```azurecli
az batch pool create --template pool-ffmpeg.json
```

Sie werden von der CLI aufgefordert, Werte für die Parameter `poolId` und `nodeCount` anzugeben. Sie können die Parameter auch in einer JSON-Datei angeben. Beispiel:

```json
{
  "poolId": {
    "value": "mypool"
  },
  "nodeCount": {
    "value": 2
  }
}
```

Wenn die JSON-Datei mit den Parametern den Namen *pool-parameters.json* hat, können Sie die Vorlage wie folgt aufrufen:

```azurecli
az batch pool create --template pool-ffmpeg.json --parameters pool-parameters.json
```

### <a name="job-templates"></a>Auftragsvorlagen

Auftragsvorlagen unterstützen die Standardvorlagenfunktionen von Parametern und Variablen. Außerdem unterstützen sie das folgende Konstrukt auf höherer Ebene:

-   **Aufgabenfactory**

    -   Erstellt mehrere Aufgaben aus einer Aufgabendefinition. Es werden drei Arten von Aufgabenfactorys unterstützt – parametrische Sweeps, Aufgaben pro Datei und Aufgabenauflistung.

Im Folgenden wird ein Beispiel für eine Vorlage vorgestellt, die einen Auftrag zum Transcodieren von MP4-Videodateien in eine von zwei niedrigeren Auflösungen mit ffmpeg erstellt. Pro Quellvideodatei wird eine Aufgabe erstellt. Weitere Informationen zu Dateigruppen für die Auftragsein- und -ausgabe finden Sie unter [Dateigruppen und Dateiübertragung](#file-groups-and-file-transfer).

```json
{
    "parameters": {
        "poolId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch pool which runs the job"
            }
        },
        "jobId": {
            "type": "string",
            "metadata": {
                "description": "The name of Azure Batch job"
            }
        },
        "resolution": {
            "type": "string",
            "defaultValue": "428x240",
            "allowedValues": [
                "428x240",
                "854x480"
            ],
            "metadata": {
                "description": "Target video resolution"
            }
        }
    },
    "job": {
        "type": "Microsoft.Batch/batchAccounts/jobs",
        "apiVersion": "2016-12-01",
        "properties": {
            "id": "[parameters('jobId')]",
            "constraints": {
                "maxWallClockTime": "PT5H",
                "maxTaskRetryCount": 1
            },
            "poolInfo": {
                "poolId": "[parameters('poolId')]"
            },
            "taskFactory": {
                "type": "taskPerFile",
                "source": { 
                    "fileGroup": "ffmpeg-input"
                },
                "repeatTask": {
                    "commandLine": "ffmpeg -i {fileName} -y -s [parameters('resolution')] -strict -2 {fileNameWithoutExtension}_[parameters('resolution')].mp4",
                    "resourceFiles": [
                        {
                            "blobSource": "{url}",
                            "filePath": "{fileName}"
                        }
                    ],
                    "outputFiles": [
                        {
                            "filePattern": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                            "destination": {
                                "autoStorage": {
                                    "path": "{fileNameWithoutExtension}_[parameters('resolution')].mp4",
                                    "fileGroup": "ffmpeg-output"
                                }
                            },
                            "uploadOptions": {
                                "uploadCondition": "TaskSuccess"
                            }
                        }
                    ]
                }
            },
            "onAllTasksComplete": "terminatejob"
        }
    }
}
```

Wenn die Vorlagendatei unter dem Namen _job-ffmpeg.json_ gespeichert wurde, rufen Sie die Vorlage wie folgt auf:

```azurecli
az batch job create --template job-ffmpeg.json
```

Wie zuvor auch, werden Sie von der CLI aufgefordert, Werte für die Parameter anzugeben. Sie können die Parameter auch in einer JSON-Datei angeben.

### <a name="use-templates-in-batch-explorer"></a>Verwenden von Vorlagen in Batch Explorer

Sie können eine Batch-CLI-Vorlage in die [Batch Explorer](https://github.com/Azure/BatchExplorer)-Desktopanwendung (früher als BatchLabs bezeichnet) hochladen, um einen Batch-Pool oder -Auftrag zu erstellen. Sie können auch im Batch Explorer-Katalog einen vordefinierten Pool und Auftragsvorlagen auswählen.

Laden Sie eine Vorlage wie folgt hoch:

1. Wählen Sie in Batch Explorer die Optionen **Katalog** > **Lokale Vorlagen**.

2. Wählen Sie einen lokalen Pool oder eine Auftragsvorlage aus, oder verwenden Sie Drag & Drop.

3. Wählen Sie **Diese Vorlage verwenden**, und befolgen Sie die Anweisungen auf dem Bildschirm.

## <a name="file-groups-and-file-transfer"></a>Dateigruppen und Dateiübertragung

Die meisten Aufträge und Aufgaben erfordern Eingabedateien und erzeugen Ausgabedateien. Eingabe- und Ausgabedateien werden in der Regel vom Client zum Knoten oder vom Knoten zum Client übertragen. Die Azure Batch-CLI-Erweiterung abstrahiert die Dateiübertragung und nutzt das Speicherkonto, das Sie jedem Batch-Konto zuordnen können.

Eine Dateigruppe entspricht einem im Azure Storage-Konto erstellten Container. Die Dateigruppe kann Unterordner enthalten.

Die Batch-CLI-Erweiterung unterstützt Befehle, mit denen Dateien von einem Client in eine bestimmte Dateigruppe hochgeladen und Dateien aus der angegebenen Dateigruppe auf einen Client heruntergeladen werden können.

```azurecli
az batch file upload --local-path c:\source_videos\*.mp4 
    --file-group ffmpeg-input

az batch file download --file-group ffmpeg-output --local-path
    c:\output_lowres_videos
```

Mit Pool- und Auftragsvorlagen können in Dateigruppen gespeicherte Dateien angegeben werden, die auf Poolknoten oder aus Poolknoten wieder in eine Dateigruppe kopiert werden sollen. In der oben angegebenen Auftragsvorlage wird die Dateigruppe *ffmpeg-input* beispielsweise für die Aufgabenfactory als Speicherort der Quellvideodateien angegeben, die zur Transcodierung auf den Knoten kopiert werden. Die Dateigruppe *ffmpeg-output* ist der Speicherort, an den die transcodierten Ausgabedateien vom Knoten, auf dem eine Aufgabe ausgeführt wird, kopiert werden.

## <a name="summary"></a>Zusammenfassung

Unterstützung für Vorlagen und die Dateiübertragung wurde derzeit nur zur Azure CLI hinzugefügt. Das Ziel besteht darin, die Zielgruppe, die Batch verwenden kann, auf Benutzer zu erweitern, die keinen Code mithilfe der Batch-APIs entwickeln müssen, z.B. Forscher und IT-Benutzer. So können Benutzer mit Kenntnissen in Azure, Batch und den von Batch auszuführenden Anwendungen ohne Codierung Vorlagen für die Erstellung von Pools und Aufträgen erstellen. Mit Vorlagenparametern können Benutzer ohne fundierte Kenntnisse in Batch und den Anwendungen Vorlagen verwenden.

Testen Sie die Batch-Erweiterung für die Azure CLI, und teilen Sie uns Ihr Feedback und Ihre Vorschläge mit. Dies können Sie entweder über die Kommentarfunktion dieses Artikels oder das [Batch-Community-Repository](https://github.com/Azure/Batch) tun.

## <a name="next-steps"></a>Nächste Schritte

- Ausführliche Dokumente, Beispiele und Quellcodes zur Installation und Nutzung finden Sie im [Azure GitHub-Repository](https://github.com/Azure/azure-batch-cli-extensions).

- Erfahren Sie mehr zur Verwendung von [Batch Explorer](https://github.com/Azure/BatchExplorer) zum Erstellen und Verwalten von Batch-Ressourcen.
