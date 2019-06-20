---
title: Umgebungsvariablen für Aufgabenlaufzeit – Azure Batch | Microsoft-Dokumentation
description: Anleitung und Referenz zu Umgebungsvariablen für Aufgabenlaufzeit für Azure Batch-Analysen.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/23/2019
ms.author: lahugh
ms.openlocfilehash: c46f75c447becc8b15d4a6b8f979330db7ab95c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64575590"
---
# <a name="azure-batch-runtime-environment-variables"></a>Umgebungsvariablen der Azure Batch-Laufzeit

Der [Azure Batch-Dienst](https://azure.microsoft.com/services/batch/) legt die folgenden Umgebungsvariablen für Computeknoten fest. Sie können auf diese Umgebungsvariablen in Taskbefehlszeilen sowie in den Programmen und Skripts verweisen, die über die Befehlszeilen ausgeführt werden.

Weitere Informationen zur Verwendung von Umgebungsvariablen mit dem Batch-Dienst finden Sie unter [Umgebungseinstellungen für Tasks](https://docs.microsoft.com/azure/batch/batch-api-basics#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Sichtbarkeit von Umgebungsvariablen

Diese Umgebungsvariablen sind nur sichtbar im Kontext des **Taskbenutzers**, d.h. des Benutzerkontos im Knoten, unter dem ein Task ausgeführt wird. Sie werden *nicht* angezeigt, wenn Sie über RDP (Remotedesktopprotokoll) oder SSH (Secure Shell) eine [Remoteverbindung](https://azure.microsoft.com/documentation/articles/batch-api-basics/#connecting-to-compute-nodes) herstellen und die Umgebungsvariablen auflisten. Das liegt daran, dass das Benutzerkonto, das für die Remoteverbindung verwendet wird, nicht dem Konto entspricht, das vom Task verwendet wird.

Um den aktuellen Wert einer Umgebungsvariablen abzurufen, starten Sie `cmd.exe` auf einem Windows-Computeknoten oder `/bin/sh` auf einem Linux-Knoten:

`cmd /c set <ENV_VARIABLE_NAME>`

`/bin/sh printenv <ENV_VARIABLE_NAME>`

## <a name="command-line-expansion-of-environment-variables"></a>Befehlszeilenerweiterung von Umgebungsvariablen

Die von Tasks auf Computeknoten angewendeten Befehlszeilen können nicht unter einer Shell ausgeführt werden. Aus diesem Grund können diese Befehlszeilen Shellfeatures wie die Erweiterung von Umgebungsvariablen nicht nativ nutzen. (Dies gilt auch für `PATH`.) Um solche Features nutzen zu können, muss das **Aufrufen der Shell** an der Befehlszeile erfolgen. Starten Sie `cmd.exe` beispielsweise auf Windows-Computeknoten oder `/bin/sh` auf Linux-Knoten:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Umgebungsvariablen

| Variablenname                     | BESCHREIBUNG                                                              | Verfügbarkeit | Beispiel |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | Der Name des Batch-Kontos, zu dem der Task gehört.                  | Alle Tasks.   | mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | Die URL des Batch-Kontos. | Alle Tasks. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | Ein Präfix aller Umgebungsvariablen des App-Pakets. Wenn beispielsweise von der Anwendung „FOO“ die Version „1“ in einem Pool installiert wird, lautet die Umgebungsvariable AZ_BATCH_APP_PACKAGE_FOO_1. AZ_BATCH_APP_PACKAGE_FOO_1 verweist auf den Speicherort, an den das Paket heruntergeladen wurde (ein Ordner). | Jede Aufgabe mit einem zugeordneten App-Paket. Auch für alle Aufgaben verfügbar, wenn der Knoten selbst über Anwendungspakete verfügt. | AZ_BATCH_APP_PACKAGE_FOO_1 |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Ein Authentifizierungstoken für den Zugriff auf eine begrenzte Gruppe von Batch-Dienstvorgängen. Diese Umgebungsvariable ist nur vorhanden, wenn [authenticationTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) beim [Hinzufügen der Aufgabe](/rest/api/batchservice/task/add#request-body) festgelegt wird. Der Tokenwert wird in den Batch-APIs als Anmeldeinformationen für die Erstellung eines Batchclients verwendet (wie etwa in der [.NET-API „BatchClient.Open()“](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_)). | Alle Tasks. | OAuth2-Zugriffstoken |
| AZ_BATCH_CERTIFICATES_DIR       | Ein Verzeichnis im [Taskarbeitsverzeichnis][files_dirs], in dem Zertifikate für Linux-Computeknoten gespeichert werden. Beachten Sie, die diese Umgebungsvariable nicht für Windows-Computeknoten gilt.                                                  | Alle Tasks.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | Die Liste der Knoten, die einem [Tasks mit mehreren Instanzen][multi_instance] zugeordnet sind, im Format `nodeIP,nodeIP`. | Primäre und Untertasks mit mehreren Instanzen. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Gibt an, ob der aktuelle Knoten der Masterknoten eines [Tasks mit mehreren Instanzen][multi_instance] ist. Mögliche Werte sind `true` und `false`.| Primäre und Untertasks mit mehreren Instanzen. | `true` |
| AZ_BATCH_JOB_ID                 | Die ID des Auftrags, zu dem der Task gehört. | Alle Tasks mit Ausnahme des Starttasks. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | Der vollständige Pfad des [Taskverzeichnisses][files_dirs] für die Auftragsvorbereitung auf dem Knoten. | Alle Tasks mit Ausnahme des Starttasks und Auftragsvorbereitungstasks. Nur verfügbar, wenn der Auftrag mit einem Auftragsvorbereitungstask konfiguriert ist. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | Der vollständige Pfad des [Taskarbeitsverzeichnisses][files_dirs] für die Auftragsvorbereitung auf dem Knoten. | Alle Tasks mit Ausnahme des Starttasks und Auftragsvorbereitungstasks. Nur verfügbar, wenn der Auftrag mit einem Auftragsvorbereitungstask konfiguriert ist. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | IP-Adresse und Port des Computeknotens, auf dem der Haupttasks eines [Tasks mit mehreren Instanzen][multi_instance] ausgeführt wird. | Primäre und Untertasks mit mehreren Instanzen. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | Die ID des Knotens, dem der Task zugewiesen ist | Alle Tasks. | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | Wenn `true`, ist der aktuelle Knoten ein dedizierter-Knoten. Bei `false` handelt es sich um einen [Knoten mit niedriger Priorität](batch-low-pri-vms.md). | Alle Tasks. | `true` |
| AZ_BATCH_NODE_LIST              | Die Liste der Knoten, die einem [Tasks mit mehreren Instanzen][multi_instance] zugeordnet sind, im Format `nodeIP;nodeIP`. | Primäre und Untertasks mit mehreren Instanzen. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_ROOT_DIR          | Der vollständige Pfad zum Stamm aller [Batch-Verzeichnisse][files_dirs] auf dem Knoten. | Alle Tasks. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | Der vollständige Pfad des [freigegebenen Verzeichnisses][files_dirs] auf dem Knoten. Alle Tasks, die auf einem Knoten ausgeführt werden, haben Lese-/Schreibzugriff auf dieses Verzeichnis. Tasks, die auf anderen Knoten ausgeführt werden, können nicht remote auf dieses Verzeichnis zugreifen (es ist kein „freigegebenes“ Netzwerkverzeichnis). | Alle Tasks. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | Der vollständige Pfad des [Starttaskverzeichnisses][files_dirs] auf dem Knoten. | Alle Tasks. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | Die ID des Pools, in dem der Task ausgeführt wird. | Alle Tasks. | batchpool001 |
| AZ_BATCH_TASK_DIR               | Der vollständige Pfad des [Taskverzeichnisses][files_dirs] auf dem Knoten. Dieses Verzeichnis enthält `stdout.txt` und `stderr.txt` für den Task und AZ_BATCH_TASK_WORKING_DIR. | Alle Tasks. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | Die ID des aktuellen Tasks | Alle Tasks mit Ausnahme des Starttasks. | task001 |
| AZ_BATCH_TASK_SHARED_DIR | Verzeichnispfad, der für den primären Task und alle Untertasks eines [Tasks mit mehreren Instanzen][multi_instance] identisch ist. Der Pfad ist auf allen Knoten vorhanden, auf denen der Tasks mit mehreren Instanzen ausgeführt wird. Es besteht ein Lese-/Schreibzugriff für die Taskbefehle, die auf diesem Knoten ausgeführt werden ([Koordinierungsbefehl][coord_cmd] und [Anwendungsbefehl][app_cmd]). Untertasks oder ein primärer Task, der auf anderen Knoten ausgeführt wird, können nicht remote auf dieses Verzeichnis zugreifen (es ist kein „freigegebenes“ Netzwerkverzeichnis). | Primäre und Untertasks mit mehreren Instanzen. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_TASK_WORKING_DIR       | Der vollständige Pfad des [Taskarbeitsverzeichnisses][files_dirs] auf dem Knoten. Der aktuell ausgeführte Tasks hat Lese-/Schreibzugriff auf dieses Verzeichnis. | Alle Tasks. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | Die Liste der Knoten und Anzahl der Kerne pro Knoten, die einem [Tasks mit mehreren Instanzen][multi_instance] zugeordnet sind. Knoten und Kerne werden im Format `numNodes<space>node1IP<space>node1Cores<space>` aufgeführt.<br/>`node2IP<space>node2Cores<space> ...`, wobei auf die Anzahl der Knoten eine oder mehrere IP-Adressen von Knoten und die jeweilige Anzahl der Kerne folgen. |  Primäre und Untertasks mit mehreren Instanzen. |`2 10.0.0.4 1 10.0.0.5 1` |

[files_dirs]: https://azure.microsoft.com/documentation/articles/batch-api-basics/#files-and-directories
[multi_instance]: https://azure.microsoft.com/documentation/articles/batch-mpi/
[coord_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#coordination-command
[app_cmd]: https://azure.microsoft.com/documentation/articles/batch-mpi/#application-command
