---
title: Erstellen von Tasks zum Vorbereiten und Abschließen von Aufträgen auf Computeknoten – Azure Batch | Microsoft-Dokumentation
description: Verwenden Sie Vorbereitungs- und Freigabeaufgaben auf Auftragsebene, um Datenübertragungen auf Azure Batch-Computeknoten zu minimieren und Knoten nach Abschluss des Auftrags zu bereinigen.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 63d9d4f1-8521-4bbb-b95a-c4cad73692d3
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 517ac0f612b9e5fc5909a7f0fe2ce088c9b367d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60776193"
---
# <a name="run-job-preparation-and-job-release-tasks-on-batch-compute-nodes"></a>Ausführen von Tasks zum Vorbereiten und Freigeben von Aufträgen auf Azure Batch-Computeknoten

 Für einen Azure Batch-Auftrag ist häufig ein bestimmtes Setup erforderlich, bevor die entsprechenden Aufgaben ausgeführt werden, sowie ein Wartungsschritt nach der Durchführung der Aufgaben. Sie müssen unter Umständen allgemeine Eingabedaten für die Aufgaben auf Ihre Computeknoten herunterladen oder Ausgabedaten der Aufgaben in Azure Storage hochladen, nachdem der Auftrag abgeschlossen wurde. Sie können Aufgaben vom Typ **Auftragsvorbereitung** und **Auftragsfreigabe** verwenden, um diese Vorgänge durchzuführen.

## <a name="what-are-job-preparation-and-release-tasks"></a>Was sind Aufgaben zur Auftragsvorbereitung und -freigabe?
Bevor die Aufgaben eines Auftrags ausgeführt werden, wird die Auftragsvorbereitungsaufgabe auf allen Computeknoten ausgeführt, für die die Ausführung von mindestens einer Aufgabe geplant ist. Nach Abschluss des Auftrags wird die Auftragsfreigabeaufgabe auf jedem Knoten im Pool ausgeführt, der mindestens eine Aufgabe ausgeführt hat. Wie bei normalen Batch-Aufgaben können Sie eine Befehlszeile angeben, die aufgerufen wird, wenn eine Aufgabe zur Auftragsvorbereitung oder -freigabe ausgeführt wird.

Aufgaben zur Auftragsvorbereitung und -freigabe verfügen über vertraute Batch-Aufgabenfunktionen, z.B. Herunterladen von Dateien ([Ressourcendateien][net_job_prep_resourcefiles]), Ausführen mit höheren Rechten, benutzerdefinierte Umgebungsvariablen, maximale Ausführungsdauer, Anzahl von Wiederholungsversuchen und Dateiaufbewahrungsdauer.

In den folgenden Abschnitten erfahren Sie, wie Sie die Klassen [JobPreparationTask][net_job_prep] und [JobReleaseTask][net_job_release] in der [Batch .NET][api_net]-Bibliothek verwenden.

> [!TIP]
> Aufgaben zur Auftragsvorbereitung und -freigabe sind insbesondere in Umgebungen mit einem gemeinsam genutzten Pool hilfreich, in denen ein Pool mit Computeknoten zwischen Auftragsausführungen beibehalten und von vielen verschiedenen Aufträgen genutzt wird.
> 
> 

## <a name="when-to-use-job-preparation-and-release-tasks"></a>Verwendungsmöglichkeiten für Auftragsvorbereitungs- und Auftragsfreigabeaufgaben
Auftragsvorbereitungs- und Auftragsfreigabeaufgaben eignen sich gut für die folgenden Situationen:

**Download von allgemeinen Aufgabendaten**

Batchaufträge erfordern oft einen gemeinsamen Satz von Daten als Eingabe für die Aufgaben des Auftrags. Bei täglich durchgeführten Berechnungen zur Risikoanalyse sind Marktdaten z.B. auftragsspezifisch, gelten aber trotzdem für alle Aufgaben im Auftrag. Diese Marktdaten, die oft mehrere GB groß sind, sollten auf jeden Computeknoten nur einmal heruntergeladen werden, sodass sie von jeder auf dem Knoten ausgeführten Aufgabe verwendet werden können. Verwenden Sie eine **Auftragsvorbereitungsaufgabe**, um diese Daten vor der Ausführung anderer Aufgaben im Auftrag auf jeden Knoten herunterzuladen.

**Löschen der Auftrags- und Aufgabenausgabe**

In einer Umgebung mit einem gemeinsam genutzten Pool, in der die Computeknoten eines Pools zwischen Aufträgen nicht außer Betrieb genommen werden, müssen Sie Auftragsdaten zwischen den Ausführungen unter Umständen löschen. Es kann sein, dass Sie auf den Knoten Speicherplatz sparen oder die Sicherheitsrichtlinien Ihrer Organisation einhalten müssen. Verwenden Sie eine **Auftragsfreigabeaufgabe** , um Daten zu löschen, die von einer Auftragsvorbereitungsaufgabe heruntergeladen oder während der Aufgabenausführung generiert wurden.

**Protokollaufbewahrung**

Eventuell wollen Sie eine Kopie der von den Aufgaben generierten Protokolle oder die von fehlerhaften Anwendungen generierten Absturzabbilddateien aufbewahren. Verwenden Sie in diesen Fällen eine **Auftragsfreigabeaufgabe**, um die Daten zu komprimieren und in ein [Azure Storage][azure_storage]-Konto hochzuladen.

> [!TIP]
> Eine andere Möglichkeit zum Beibehalten von Protokollen und anderen Auftrags- und Aufgabenausgabedaten ist die Verwendung der Bibliothek [Azure Batch File Conventions](batch-task-output.md) (Azure Batch-Dateikonventionen).
> 
> 

## <a name="job-preparation-task"></a>Auftragsvorbereitungsaufgabe
Vor der Ausführung der Aufgaben eines Auftrags führt Batch die Auftragsvorbereitungsaufgabe auf jedem Computeknoten aus, der zum Ausführen einer Aufgabe eingeplant ist. Standardmäßig wartet der Batch-Dienst, bis die Auftragsvorbereitungsaufgabe abgeschlossen ist, bevor er die geplanten Aufgaben auf dem Knoten ausführt. Sie können den Dienst allerdings auch dahingehend konfigurieren, dass er nicht wartet. Nach dem Neustart des Knotens wird die Auftragsvorbereitungsaufgabe erneut ausgeführt, aber Sie können dieses Verhalten auch deaktivieren.

Die Auftragsvorbereitungsaufgabe wird nur auf Knoten ausgeführt, die zum Ausführen einer Aufgabe eingeplant sind. Das verhindert die unnötige Ausführung von Auftragsvorbereitungsaufgaben an Knoten, denen keine Aufgaben zugewiesen sind. Dies kann eintreten, wenn die Anzahl der Aufgaben für einen Auftrag geringer ist, als die Anzahl der Knoten in einem Pool. Dies gilt auch, wenn die [gleichzeitige Aufgabenausführung](batch-parallel-node-tasks.md) aktiviert ist. Einige Knoten bleiben unbeschäftigt, wenn die Gesamtzahl von Aufgaben geringer als die Gesamtzahl der möglichen zeitgleich ausgeführten Aufgaben ist. Indem Sie die Auftragsvorbereitungsaufgabe nicht auf Knoten im Leerlauf ausführen, können Sie bei den Gebühren für Datenübertragungen sparen.

> [!NOTE]
> [JobPreparationTask][net_job_prep_cloudjob] unterscheidet sich vom [CloudPool.StartTask][pool_starttask] insofern, dass JobPreparationTask beim Start jedes Auftrags ausgeführt wird, während StartTask nur ausgeführt wird, wenn ein Serverknoten einem Pool erstmals hinzugefügt oder neu gestartet wird.
> 
> 

## <a name="job-release-task"></a>Auftragsfreigabeaufgabe
Nachdem ein Auftrag als abgeschlossen markiert wurde, wird die Auftragsfreigabeaufgabe auf jedem Knoten im Pool ausgeführt, der mindestens eine Aufgabe ausgeführt hat. Um einen Auftrag als abgeschlossen zu markieren, geben Sie eine Terminate-Anforderung aus. Der Batch-Dienst legt anschließend den Status des Auftrags auf *Wird beendet* fest, beendet alle aktiven bzw. ausgeführten Aufgaben im Zusammenhang mit dem Auftrag und führt die Auftragsfreigabeaufgabe aus. Danach wird der Status des Auftrags in *Abgeschlossen* geändert.

> [!NOTE]
> Beim Löschen eines Auftrags wird die Auftragsfreigabeaufgabe ebenfalls ausgeführt. Wurde ein Auftrag jedoch bereits zuvor beendet, wird die Freigabeaufgabe kein zweites Mal ausgeführt, wenn der Auftrag später gelöscht wird.
> 
> 

## <a name="job-prep-and-release-tasks-with-batch-net"></a>Aufgaben zur Auftragsvorbereitung und -freigabe mit Batch .NET
Zum Verwenden einer Auftragsvorbereitungsaufgabe weisen Sie der [CloudJob.JobPreparationTask][net_job_prep_cloudjob]-Eigenschaft des Auftrags ein [JobPreparationTask][net_job_prep]-Objekt zu. Auf ähnliche Weise initialisieren Sie einen [JobReleaseTask][net_job_release], den Sie der [CloudJob.JobReleaseTask][net_job_prep_cloudjob]-Eigenschaft zuweisen, um die Freigabeaufgabe des Auftrags festzulegen.

In diesem Codeausschnitt ist `myBatchClient` eine Instanz von [BatchClient][net_batch_client], und `myPool` ist ein vorhandener Pool im Batch-Konto.

```csharp
// Create the CloudJob for CloudPool "myPool"
CloudJob myJob =
    myBatchClient.JobOperations.CreateJob(
        "JobPrepReleaseSampleJob",
        new PoolInformation() { PoolId = "myPool" });

// Specify the command lines for the job preparation and release tasks
string jobPrepCmdLine =
    "cmd /c echo %AZ_BATCH_NODE_ID% > %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";
string jobReleaseCmdLine =
    "cmd /c del %AZ_BATCH_NODE_SHARED_DIR%\\shared_file.txt";

// Assign the job preparation task to the job
myJob.JobPreparationTask =
    new JobPreparationTask { CommandLine = jobPrepCmdLine };

// Assign the job release task to the job
myJob.JobReleaseTask =
    new JobReleaseTask { CommandLine = jobReleaseCmdLine };

await myJob.CommitAsync();
```

Wie oben erwähnt, wird die Freigabeaufgabe ausgeführt, wenn ein Auftrag beendet oder gelöscht wird. Beenden Sie einen Auftrag mit [JobOperations.TerminateJobAsync][net_job_terminate]. Löschen Sie einen Auftrag mit [JobOperations.DeleteJobAsync][net_job_delete]. Normalerweise beenden oder löschen Sie einen Auftrag, wenn dessen Aufgaben abgeschlossen sind oder wenn eine Zeitüberschreitung erreicht wird, die Sie festgelegt haben.

```csharp
// Terminate the job to mark it as Completed; this will initiate the
// Job Release Task on any node that executed job tasks. Note that the
// Job Release Task is also executed when a job is deleted, thus you
// need not call Terminate if you typically delete jobs after task completion.
await myBatchClient.JobOperations.TerminateJobAsync("JobPrepReleaseSampleJob");
```

## <a name="code-sample-on-github"></a>Codebeispiel auf GitHub
Sehen Sie sich das Beispielprojekt [JobPrepRelease][job_prep_release_sample] auf GitHub an, um Auftragsvorbereitungs- und Auftragsfreigabeaufgaben in Aktion zu erleben. Diese Konsolenanwendung führt folgende Schritte aus:

1. Sie erstellt einen Pool mit zwei Knoten.
2. Sie erstellt einen Auftrag mit Auftragsvorbereitungs-, Auftragsfreigabe- und Standardaufgaben.
3. Sie führt die Auftragsvorbereitungsaufgabe aus, die zunächst die Knoten-ID in eine Textdatei im „freigegebenen“ Verzeichnis des Knotens schreibt.
4. Sie führt auf jedem Knoten eine Aufgabe aus, welche die Aufgaben-ID in dieselbe Textdatei schreibt.
5. Nach dem Abschluss aller Aufgaben (oder Erreichen des Timeouts) gibt sie den Inhalt der Textdatei jedes Knotens an die Konsole aus.
6. Sie führt die Auftragsfreigabeaufgabe aus, um die Datei aus dem Knoten zu löschen, wenn der Auftrag abgeschlossen ist.
7. Sie gibt die Exitcodes der Auftragsvorbereitungs- und Auftragsfreigabeaufgaben für jeden Knoten aus, auf dem diese ausgeführt wurden.
8. Sie hält die Ausführung an, um auf die Löschbestätigung für den Auftrag und/oder den Pool zu warten.

Die Ausgabe der Beispielanwendung sieht in etwa wie folgt aus:

```
Attempting to create pool: JobPrepReleaseSamplePool
Created pool JobPrepReleaseSamplePool with 2 nodes
Checking for existing job JobPrepReleaseSampleJob...
Job JobPrepReleaseSampleJob not found, creating...
Submitting tasks and awaiting completion...
All tasks completed.

Contents of shared\job_prep_and_release.txt on tvm-2434664350_1-20160623t173951z:
-------------------------------------------
tvm-2434664350_1-20160623t173951z tasks:
  task001
  task004
  task005
  task006

Contents of shared\job_prep_and_release.txt on tvm-2434664350_2-20160623t173951z:
-------------------------------------------
tvm-2434664350_2-20160623t173951z tasks:
  task008
  task002
  task003
  task007

Waiting for job JobPrepReleaseSampleJob to reach state Completed
...

tvm-2434664350_1-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

tvm-2434664350_2-20160623t173951z:
  Prep task exit code:    0
  Release task exit code: 0

Delete job? [yes] no
yes
Delete pool? [yes] no
yes

Sample complete, hit ENTER to exit...
```

> [!NOTE]
> Die tatsächliche Ausgabe kann sich aufgrund der variablen Erstellungs- und Startzeit von Knoten in einem neuen Pool unterscheiden. (Einige Knoten sind schneller für Aufgaben bereit als andere.) Aufgrund der schnellen Ausführung der Aufgaben werden unter Umständen alle Aufgaben des Auftrags von einem einzelnen Knoten des Pools ausgeführt. In diesem Fall werden Sie feststellen, dass die Aufgaben zur Auftragsvorbereitung und -freigabe für den Knoten, die keine Aufgaben ausgeführt hat, nicht vorhanden sind.
> 
> 

### <a name="inspect-job-preparation-and-release-tasks-in-the-azure-portal"></a>Überprüfen von Aufgaben zur Auftragsvorbereitung und -freigabe im Azure-Portal
Wenn Sie die Beispielanwendung ausführen, können Sie die Eigenschaften des Auftrags und dessen Aufgaben im [Azure-Portal][portal] anzeigen und sogar die durch die Aufgaben des Auftrags geänderte, freigegebene Textdatei herunterladen.

Der folgende Screenshot zeigt das Blatt **Vorbereitungstasks** im Azure-Portal nach der Ausführung der Beispielanwendung. Navigieren Sie nach Abschluss der Aufgaben (aber noch vor dem Löschen des Auftrags und Pools) zu den Eigenschaften für *JobPrepReleaseSampleJob*, und klicken Sie auf **Vorbereitungstasks** oder **Freigabetasks**, um die entsprechenden Eigenschaften anzuzeigen.

![Aufgabenvorbereitungseigenschaften im Azure-Portal][1]

## <a name="next-steps"></a>Nächste Schritte
### <a name="application-packages"></a>Anwendungspakete
Neben der Aufgabe zur Auftragsvorbereitung können Sie auch das Batch-Feature [Anwendungspakete](batch-application-packages.md) verwenden, um Computeknoten für die Aufgabenausführung vorzubereiten. Dieses Feature ist besonders bei der Bereitstellung von Anwendungen hilfreich, für die kein Installationsprogramm ausgeführt werden muss, sowie bei Anwendungen, die mehr als 100 Dateien umfassen oder eine strenge Versionskontrolle erfordern.

### <a name="installing-applications-and-staging-data"></a>Installieren von Anwendungen und Bereitstellen von Daten (Staging)
Dieser Beitrag im MSDN-Forum enthält eine Übersicht über mehrere Methoden zum Vorbereiten der Knoten für die Ausführung von Aufgaben:

[Installing applications and staging data on Batch compute nodes][forum_post] (Installieren von Anwendungen und Bereitstellen von Daten auf Batch-Serverknoten)

Der Beitrag wurde von einem Mitglied des Azure Batch-Teams geschrieben und enthält Beschreibungen mehrerer Verfahren, die Sie zum Bereitstellen von Anwendungen und Daten auf Computeknoten verwenden können.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_listjobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[azure_storage]: https://azure.microsoft.com/services/storage/
[portal]: https://portal.azure.com
[job_prep_release_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/JobPrepRelease
[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]:https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_prep]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.aspx
[net_job_prep_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_job_prep_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobpreparationtask.resourcefiles.aspx
[net_job_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.deletejobasync.aspx
[net_job_terminate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_job_release]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobreleasetask.aspx
[net_job_release_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx

[net_list_certs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.listcertificates.aspx
[net_list_compute_nodes]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listcomputenodes.aspx
[net_list_job_schedules]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobschedules.aspx
[net_list_jobprep_status]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobpreparationandreleasetaskstatus.aspx
[net_list_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listjobs.aspx
[net_list_nodefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listnodefiles.aspx
[net_list_pools]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.listpools.aspx
[net_list_schedule_jobs]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobscheduleoperations.listjobs.aspx
[net_list_task_files]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.listnodefiles.aspx
[net_list_tasks]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.listtasks.aspx

[1]: ./media/batch-job-prep-release/portal-jobprep-01.png
