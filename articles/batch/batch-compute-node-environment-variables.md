---
title: Umgebungsvariablen der Aufgabenlaufzeit
description: Anleitung und Referenz zu Umgebungsvariablen für Aufgabenlaufzeit für Azure Batch-Analysen.
ms.topic: conceptual
ms.date: 12/30/2020
ms.openlocfilehash: dbdc13e28a3a0c772480d2602f147e0d3354ff48
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104669983"
---
# <a name="azure-batch-runtime-environment-variables"></a>Umgebungsvariablen der Azure Batch-Laufzeit

Der [Azure Batch-Dienst](https://azure.microsoft.com/services/batch/) legt die folgenden Umgebungsvariablen für Computeknoten fest. Sie können auf diese Umgebungsvariablen in Taskbefehlszeilen sowie in den Programmen und Skripts verweisen, die über die Befehlszeilen ausgeführt werden.

Weitere Informationen zum Verwenden von Umgebungsvariablen mit dem Batch-Dienst finden Sie unter [Umgebungseinstellungen für Tasks](jobs-and-tasks.md#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Sichtbarkeit von Umgebungsvariablen

Diese Umgebungsvariablen sind nur sichtbar im Kontext des **Taskbenutzers**, d. h. des Benutzerkontos auf dem Knoten, unter dem ein Task ausgeführt wird. Sie werden nicht angezeigt, wenn Sie über RDP (Remotedesktopprotokoll) oder SSH (Secure Shell) eine [Remoteverbindung](./error-handling.md#connect-to-compute-nodes) zum Serverknoten herstellen und die Umgebungsvariablen auflisten. Das liegt daran, dass das Benutzerkonto, das für die Remoteverbindung verwendet wird, nicht dem Konto entspricht, das vom Task verwendet wird.

Um den aktuellen Wert einer Umgebungsvariablen abzurufen, starten Sie `cmd.exe` auf einem Windows-Computeknoten oder `/bin/sh` auf einem Linux-Knoten:

`cmd /c set <ENV_VARIABLE_NAME>`

`/bin/sh -c "printenv <ENV_VARIABLE_NAME>"`

## <a name="command-line-expansion-of-environment-variables"></a>Befehlszeilenerweiterung von Umgebungsvariablen

Die von Tasks auf Serverknoten angewandten Befehlszeilen können nicht an einer Shell ausgeführt werden. Dies bedeutet, dass an diesen Befehlszeilen nativ keine Shellfeatures wie z. B. die Erweiterung der Umgebungsvariablen (einschließlich `PATH`) verwendet werden können. Um solche Features nutzen zu können, müssen Sie die Shell an der Befehlszeile aufrufen. Starten Sie `cmd.exe` beispielsweise auf Windows-Computeknoten oder `/bin/sh` auf Linux-Knoten:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c "MyTaskApplication $MY_ENV_VAR"`

## <a name="environment-variables"></a>Umgebungsvariablen

| Variablenname                     | BESCHREIBUNG                                                              | Verfügbarkeit | Beispiel |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | Der Name des Batch-Kontos, zu dem der Task gehört.                  | Alle Tasks.   | mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | Die URL des Batch-Kontos. | Alle Tasks. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | Ein Präfix aller Umgebungsvariablen des App-Pakets. Wenn beispielsweise von der Anwendung „FOO“ die Version „1“ in einem Pool installiert wird, lautet die Umgebungsvariable AZ_BATCH_APP_PACKAGE_FOO_1 (unter Linux) oder AZ_BATCH_APP_PACKAGE_FOO#1 (unter Windows). AZ_BATCH_APP_PACKAGE_FOO_1 verweist auf den Speicherort (einen Ordner), an den das Paket heruntergeladen wurde. Verwenden Sie bei Nutzung der Standardversion des App-Pakets die Umgebungsvariable AZ_BATCH_APP_PACKAGE ohne Versionsnummern. Wenn Sie Linux verwenden, der Name des Anwendungspakets „Agent-linux-x64“ lautet und die Versionsnummer „1.1.46.0“ ist, lautet der Umgebungsname wie folgt: AZ_BATCH_APP_PACKAGE_agent_linux_x64_1_1_46_0, einschließlich Unterstrichen und Kleinbuchstaben. Weitere Informationen finden Sie unter [Ausführen der installierten Anwendungen](batch-application-packages.md#execute-the-installed-applications). | Jede Aufgabe mit einem zugeordneten App-Paket. Auch für alle Aufgaben verfügbar, wenn der Knoten selbst über Anwendungspakete verfügt. | AZ_BATCH_APP_PACKAGE_FOO_1 (Linux) oder AZ_BATCH_APP_PACKAGE_FOO#1 (Windows) |
| AZ_BATCH_AUTHENTICATION_TOKEN   | Ein Authentifizierungstoken für den Zugriff auf eine begrenzte Gruppe von Batch-Dienstvorgängen. Diese Umgebungsvariable ist nur vorhanden, wenn [authenticationTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) beim [Hinzufügen der Aufgabe](/rest/api/batchservice/task/add#request-body) festgelegt wird. Der Tokenwert wird in den Batch-APIs als Anmeldeinformationen für die Erstellung eines Batchclients verwendet (wie etwa in der [.NET-API „BatchClient.Open()“](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_)). | Alle Tasks. | OAuth2-Zugriffstoken |
| AZ_BATCH_CERTIFICATES_DIR       | Ein Verzeichnis im [Taskarbeitsverzeichnis](files-and-directories.md), in dem Zertifikate für Linux-Computeknoten gespeichert werden. Diese Umgebungsvariable gilt nicht für Windows-Computeknoten.                                                  | Alle Tasks.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | Die Liste mit den Knoten, die einem [Task mit mehreren Instanzen](batch-mpi.md) zugeordnet sind (im Format `nodeIP,nodeIP`). | Primäre und Untertasks mit mehreren Instanzen. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Gibt an, ob der aktuelle Knoten der Masterknoten eines [Tasks mit mehreren Instanzen](batch-mpi.md) ist. Mögliche Werte sind `true` und `false`.| Primäre und Untertasks mit mehreren Instanzen. | `true` |
| AZ_BATCH_JOB_ID                 | Die ID des Auftrags, zu dem der Task gehört. | Alle Tasks mit Ausnahme des Starttasks. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | Der vollständige Pfad des [Taskverzeichnisses](files-and-directories.md) für die Auftragsvorbereitung auf dem Knoten. | Alle Tasks mit Ausnahme des Starttasks und Auftragsvorbereitungstasks. Nur verfügbar, wenn der Auftrag mit einem Auftragsvorbereitungstask konfiguriert ist. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | Der vollständige Pfad des [Taskarbeitsverzeichnisses](files-and-directories.md) für die Auftragsvorbereitung auf dem Knoten. | Alle Tasks mit Ausnahme des Starttasks und Auftragsvorbereitungstasks. Nur verfügbar, wenn der Auftrag mit einem Auftragsvorbereitungstask konfiguriert ist. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | IP-Adresse und Port des Computeknotens, auf dem der Haupttasks eines [Tasks mit mehreren Instanzen](batch-mpi.md) ausgeführt wird. Verwenden Sie den hier angegebenen Port nicht für MPI- oder NCCL-Kommunikation – er ist für den Azure Batch-Dienst reserviert. Verwenden Sie stattdessen die Variable MASTER_PORT, indem Sie sie entweder mit einem über das Befehlszeilenargument eingegebenen Wert festlegen (Port 6105 ist eine gute Standardwahl), oder indem Sie den von AML festgelegten Wert verwenden, wenn dieser verfügbar ist. | Primäre und Untertasks mit mehreren Instanzen. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | Die ID des Knotens, dem der Task zugewiesen ist | Alle Tasks. | tvm-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | Wenn `true`, ist der aktuelle Knoten ein dedizierter-Knoten. Bei `false` handelt es sich um einen [Knoten mit niedriger Priorität](batch-low-pri-vms.md). | Alle Tasks. | `true` |
| AZ_BATCH_NODE_LIST              | Die Liste mit den Knoten, die einem [Task mit mehreren Instanzen](batch-mpi.md) zugeordnet sind (im Format `nodeIP;nodeIP`). | Primäre und Untertasks mit mehreren Instanzen. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_MOUNTS_DIR        | Der vollständige Pfad des Speicherorts für die [Bereitstellung des Dateisystems](virtual-file-mount.md) auf Knotenebene, an dem sich alle Bereitstellungsverzeichnisse befinden. Windows-Dateifreigaben verwenden einen Laufwerkbuchstaben. Unter Windows ist das Bereitstellungslaufwerk daher Teil von Geräten und Laufwerken.  |  Alle Tasks (einschließlich Starttask) haben Zugriff auf den Benutzer, wenn der Benutzer die Bereitstellungsberechtigungen für das bereitgestellte Verzeichnis kennt. | Beispielsweise lautet der Speicherort in Ubuntu wie folgt: `/mnt/batch/tasks/fsmounts` |
| AZ_BATCH_NODE_ROOT_DIR          | Der vollständige Pfad zum Stamm aller [Batch-Verzeichnisse](files-and-directories.md) auf dem Knoten. | Alle Tasks. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | Der vollständige Pfad des [freigegebenen Verzeichnisses](files-and-directories.md) auf dem Knoten. Alle Tasks, die auf einem Knoten ausgeführt werden, haben Lese-/Schreibzugriff auf dieses Verzeichnis. Tasks, die auf anderen Knoten ausgeführt werden, können nicht remote auf dieses Verzeichnis zugreifen (es ist kein „freigegebenes“ Netzwerkverzeichnis). | Alle Tasks. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | Der vollständige Pfad des [Starttaskverzeichnisses](files-and-directories.md) auf dem Knoten. | Alle Tasks. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | Die ID des Pools, in dem der Task ausgeführt wird. | Alle Tasks. | batchpool001 |
| AZ_BATCH_TASK_DIR               | Der vollständige Pfad des [Taskverzeichnisses](files-and-directories.md) auf dem Knoten. Dieses Verzeichnis enthält `stdout.txt` und `stderr.txt` für den Task und AZ_BATCH_TASK_WORKING_DIR. | Alle Tasks. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | Die ID des aktuellen Tasks | Alle Tasks mit Ausnahme des Starttasks. | task001 |
| AZ_BATCH_TASK_SHARED_DIR | Verzeichnispfad, der für den primären Task und alle Untertasks eines [Tasks mit mehreren Instanzen](batch-mpi.md) identisch ist. Der Pfad ist auf jedem Knoten vorhanden, auf dem der Task mit mehreren Instanzen ausgeführt wird. Es besteht Lese-/Schreibzugriff für die Taskbefehle, die auf diesem Knoten ausgeführt werden ([Koordinierungsbefehl](batch-mpi.md#coordination-command) und [Anwendungsbefehl](batch-mpi.md#application-command)). Unteraufgaben oder primäre Tasks, die auf anderen Knoten ausgeführt werden, können nicht remote auf dieses Verzeichnis zugreifen (es ist kein „freigegebenes“ Netzwerkverzeichnis). | Primäre und Untertasks mit mehreren Instanzen. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_TASK_WORKING_DIR       | Der vollständige Pfad des [Taskarbeitsverzeichnisses](files-and-directories.md) auf dem Knoten. Der aktuell ausgeführte Tasks hat Lese-/Schreibzugriff auf dieses Verzeichnis. | Alle Tasks. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| AZ_BATCH_TASK_WORKING_DIR       | Der vollständige Pfad des [Taskarbeitsverzeichnisses](files-and-directories.md) auf dem Knoten. Der aktuell ausgeführte Tasks hat Lese-/Schreibzugriff auf dieses Verzeichnis. | Alle Tasks. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| AZ_BATCH_TASK_RESERVED_EPHEMERAL_DISK_SPACE_BYTES | Dies ist der aktuelle Schwellenwert für den Speicherplatz, ab dem die VM als `DiskFull` gekennzeichnet wird. | Alle Tasks. | 1000000 |
| CCP_NODES                       | Die Liste mit den Knoten und der Anzahl von Kernen pro Knoten, die einem [Task mit mehreren Instanzen](batch-mpi.md) zugeordnet sind. Knoten und Kerne werden im Format `numNodes<space>node1IP<space>node1Cores<space>` aufgeführt.<br/>`node2IP<space>node2Cores<space> ...`, wobei auf die Anzahl der Knoten eine oder mehrere IP-Adressen von Knoten und die jeweilige Anzahl der Kerne folgen. |  Primäre und Untertasks mit mehreren Instanzen. |`2 10.0.0.4 1 10.0.0.5 1` |

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Umgebungsvariablen mit Batch verwenden](jobs-and-tasks.md#environment-settings-for-tasks).
- Erfahren Sie mehr über [Dateien und Verzeichnisse in Batch](files-and-directories.md).
- Machen Sie sich mit [Tasks mit mehreren Instanzen](batch-mpi.md) vertraut.
