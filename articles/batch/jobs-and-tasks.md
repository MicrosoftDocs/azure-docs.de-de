---
title: Aufträge und Tasks in Azure Batch
description: Erfahren Sie mehr über Aufträge und Tasks und deren Verwendung in einem Azure Batch-Workflow aus Entwicklersicht.
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1ca721ec7527d9d042c129c22cf0266e57c32e9
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95808587"
---
# <a name="jobs-and-tasks-in-azure-batch"></a>Aufträge und Tasks in Azure Batch

In Azure Batch stellt ein *Task* eine Berechnungseinheit dar. Ein *Auftrag* entspricht einer Sammlung dieser Tasks. Weitere Informationen zu Aufträgen und Tasks und deren Verwendung in einem Azure Batch-Workflow finden Sie unten.

## <a name="jobs"></a>Aufträge

Ein Auftrag ist eine Sammlung von Aufgaben. Er verwaltet, wie die Berechnung der Tasks auf Computeknoten in einem Pool durchgeführt wird.

Ein Auftrag gibt den [Pool](nodes-and-pools.md#pools) an, in dem der Task ausgeführt werden soll. Sie können einen neuen Pool für jeden Auftrag erstellen oder einen Pool für viele Aufträge verwenden. Sie können einen Pool für jeden Auftrag erstellen, der einem [Auftragszeitplan](#scheduled-jobs) zugeordnet ist, oder einen Pool für alle dem Auftragszeitplan zugeordneten Aufträge gemeinsam.

### <a name="job-priority"></a>Auftragspriorität

Sie können den von Ihnen erstellten Aufträgen eine optionale Auftragspriorität zuweisen. Der Batch-Dienst verwendet den Prioritätswert des Auftrags, um die Reihenfolge der Planung (für alle Tasks innerhalb des Auftrags) für jeden Pool zu bestimmen.

Rufen Sie zum Aktualisieren der Priorität eines Auftrags den Vorgang [Eigenschaften eines Auftrags aktualisieren](/rest/api/batchservice/job/update) (Batch REST) auf, oder ändern Sie die [CloudJob.Priority](/dotnet/api/microsoft.azure.batch.cloudjob) (Batch .NET). Prioritätswerte liegen zwischen -1000 (niedrigste Priorität) und 1000 (höchste Priorität).

Innerhalb eines Pools haben Aufträge mit höherer Priorität bei der Planung Vorrang vor Aufträgen mit niedrigerer Priorität. Aufgaben in Aufträgen mit niedrigerer Priorität, die bereits ausgeführt werden, werden für Aufgaben in einem Auftrag mit höherer Priorität nicht zurückgestellt. Aufträge mit gleicher Prioritätsstufe verfügen über eine identische Planungswahrscheinlichkeit, und die Reihenfolge der Aufgabenausführung ist nicht definiert.

Ein Auftrag mit hohem Prioritätswert, der in einem Pool ausgeführt wird, hat keine Auswirkungen auf die Planung von Aufträgen, die in einem separaten Pool oder einem anderen Batch-Konto ausgeführt werden. Die Auftragspriorität gilt nicht für [automatische Pools](nodes-and-pools.md#autopools), die erstellt werden, wenn der Auftrag übermittelt wird.

### <a name="job-constraints"></a>Auftragseinschränkungen

Mithilfe von Einschränkungen für Aufträge können Sie bestimmte Grenzwerte für Ihre Aufträge angeben:

- Sie können eine **Gesamtbetrachtungszeit** festlegen. Wenn ein Auftrag länger ausgeführt wird, werden der Auftrag und alle seine Tasks beendet.
- Sie können die **maximale Anzahl von Taskwiederholungen** als Einschränkung angeben. Hierzu gehört auch, ob ein Task immer oder niemals wiederholt werden soll. Die Wiederholung eines Tasks bedeutet, dass der Task bei einem Fehler erneut in die Warteschlange gestellt wird, um erneut ausgeführt zu werden.

### <a name="job-manager-tasks-and-automatic-termination"></a>Tasks des Auftrags-Managers und automatische Beendigung

Ihre Clientanwendung kann einem Auftrag Tasks hinzufügen. Alternativ können Sie einen [Auftrags-Manager-Task](#job-manager-task) angeben. Ein Auftrags-Manager-Task enthält die Informationen, die zum Erstellen der erforderlichen Tasks für einen Auftrag benötigt werden, wobei der Auftrags-Manager-Task auf einem der Computeknoten innerhalb des Pools ausgeführt wird. Der Auftrags-Manager-Task wird von Batch speziell behandelt. Er wird sofort nach der Auftragserstellung der Warteschlange hinzugefügt und erneut gestartet, falls er nicht erfolgreich ausgeführt werden konnte. Ein Auftrags-Manager-Task ist für durch einen [Auftragszeitplan](#scheduled-jobs) erstellte Aufträge obligatorisch da sich Tasks nur so vor der Auftragsinstanziierung definieren lassen.

Standardmäßig bleiben Aufträge im aktiven Zustand, wenn alle Tasks innerhalb des Auftrags abgeschlossen sind. Sie können dieses Verhalten ändern, sodass der Auftrag automatisch beendet wird, wenn alle Aufgaben im Auftrag abgeschlossen sind. Legen Sie die Eigenschaft **onAllTasksComplete** des Auftrags ([OnAllTasksComplete](/dotnet/api/microsoft.azure.batch.cloudjob) in Batch .NET) auf `terminatejob`*` fest, damit der Auftrag automatisch beendet wird, wenn alle Aufgaben abgeschlossen sind.

Ein Auftrag *ohne* Tasks wird vom Batch-Dienst als Auftrag angesehen, bei dem alle Tasks abgeschlossen sind. Diese Option wird daher mit einer [Auftrags-Manager-Aufgabe](#job-manager-task)am häufigsten verwendet. Wenn Sie die automatische Autragsbeendigung ohne Auftrags-Manager verwenden möchten, müssen Sie zunächst die Eigenschaft **onAllTasksComplete** des Auftrags auf `noaction` festlegen, und erst nachdem Sie dem Auftrag Aufgaben hinzugefügt haben, auf `terminatejob`*`.

### <a name="scheduled-jobs"></a>Geplante Aufträge

Mit [Auftragszeitplänen](/rest/api/batchservice/jobschedule) können Sie wiederkehrende Aufträge im Batch-Dienst erstellen. Ein Auftragszeitplan gibt an, wann Aufträge ausgeführt werden, und enthält die Spezifikationen für die auszuführenden Aufträge. Sie können die Dauer des Zeitplans festlegen (also wann und für wie lange der Zeitplan in Kraft sein soll) und angeben, wie oft während des geplanten Zeitraums Aufträge erstellt werden sollen.

## <a name="tasks"></a>Aufgaben

Ein Task ist eine Berechnungseinheit, die einem Auftrag zugeordnet ist. Er wird auf einem Knoten ausgeführt. Tasks werden einem Knoten zur Ausführung zugewiesen oder der Warteschlange hinzugefügt, bis ein Knoten verfügbar wird. Einfach ausgedrückt: Ein Task führt mindestens ein Programm oder Skript auf einem Computeknoten aus, um die erforderlichen Arbeitsschritte zu erledigen.

Beim Erstellen eines Tasks können Sie Folgendes angeben:

- Die **Befehlszeile** für den Task. Dies ist die Befehlszeile, mit der die Anwendung oder das Skript auf dem Computeknoten ausgeführt wird.

    Beachten Sie, dass die Befehlszeile nicht unter einer Shell ausgeführt wird. Aus diesem Grund kann sie nicht einfach Shell-Features wie die Erweiterung von[ Umgebungsvariablen](#environment-settings-for-tasks) nutzen. (Dies gilt auch für `PATH`.) Um solche Features nutzen zu können, müssen Sie die Shell über die Befehlszeile aufrufen, z. B. durch Starten von `cmd.exe` (Windows-Knoten) oder von `/bin/sh` (Linux):

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Wenn Ihre Tasks eine Anwendung oder ein Skript ausführen müssen, die bzw. das sich nicht unter dem `PATH` oder in den Referenzumgebungsvariablen des Knotens befindet, können Sie die Shell explizit in der Taskbefehlszeile aufrufen.
- **Ressourcendateien** , die die zu verarbeitenden Daten enthalten. Diese Dateien werden automatisch aus Blob Storage in einem Azure-Speicherkonto auf den Knoten kopiert, bevor die Befehlszeile des Tasks ausgeführt wird. Weitere Informationen finden Sie unter [Starttask](#start-task) und [Dateien und Verzeichnisse](files-and-directories.md).
- Die von Ihrer Anwendung benötigten **Umgebungsvariablen** . Weitere Informationen finden Sie unter [Umgebungseinstellungen für Tasks](#environment-settings-for-tasks).
- Die **Einschränkungen** , mit denen der Task ausgeführt werden soll. Beispielsweise enthalten Einschränkungen die maximale Ausführungsdauer des Tasks, die maximale Anzahl von Wiederholungen für einen nicht erfolgreichen Task und die maximal zulässige Beibehaltungsdauer für Dateien im Arbeitsverzeichnis des Tasks.
- **Anwendungspakete** für die Bereitstellung auf dem Computeknoten, auf dem der Task ausgeführt werden soll. [Anwendungspakete](batch-application-packages.md) bieten eine vereinfachte Bereitstellung und Versionsverwaltung der Anwendungen, die von Ihren Tasks ausgeführt werden. Anwendungspakete auf Task-Ebene sind insbesondere in Umgebungen mit gemeinsam genutzten Pools praktisch, bei denen verschiedene Aufträge in einem Pool ausgeführt werden und der Pool nach Abschluss des Auftrags nicht gelöscht wird. Wenn Ihr Auftrag über weniger Tasks als Knoten im Pool verfügt, können Task-Anwendungspakete die Datenübertragung minimieren, da Ihre Anwendung nur auf den Knoten bereitgestellt wird, die Tasks ausführen.
- Ein Verweis auf ein **Containerimage** in Docker Hub oder eine private Registrierung und zusätzliche Einstellungen zum Erstellen eines Docker-Containers, in dem die Aufgabe für den Knoten ausgeführt wird. Diese Informationen werden nur angegeben, wenn der Pool mit einer Containerkonfiguration eingerichtet ist.

> [!NOTE]
> Die maximale Lebensdauer eines Tasks (vom Hinzufügen zum Auftrag bis zum Abschluss) beträgt 180 Tage. Abgeschlossene Tasks bleiben sieben Tage lang erhalten. Daten für Tasks, die nicht innerhalb der maximalen Lebensdauer abgeschlossen wurden, stehen hingegen nicht zur Verfügung.

Zusätzlich zu Tasks, die Sie zur Berechnung auf einem Knoten definieren, werden vom Batch-Dienst verschiedene spezielle Tasks bereitgestellt:

- [Startaufgabe](#start-task)
- [Auftrags-Manager-Aufgabe](#job-manager-task)
- [Tasks zur Auftragsvorbereitung und -freigabe](#job-preparation-and-release-tasks)
- [Tasks mit mehreren Instanzen](#multi-instance-task)
- [Abhängigkeiten von Aufgaben](#task-dependencies)

### <a name="start-task"></a>Startaufgabe

Durch Verknüpfen eines Starttasks mit einem Pool können Sie die Betriebssystemumgebung der dazugehörigen Knoten vorbereiten. So können Sie beispielsweise Aktionen wie das Installieren der Anwendungen, die von Ihren Tasks ausgeführt werden, oder das Starten von Hintergrundprozessen durchführen. Der Starttask wird bei jedem Start eines Knotens ausgeführt, solange dieser im Pool verbleibt. Dies schließt den Zeitpunkt ein, zu dem der Knoten dem Pool zum ersten Mal hinzugefügt wird, und wenn er neu gestartet oder für ihn ein Reimaging durchgeführt wird.

Ein wesentlicher Vorteil von Starttasks besteht darin, dass sie alle Informationen enthalten können, die zum Konfigurieren eines Computeknotens und zum Installieren der für die Taskausführung benötigten Anwendungen erforderlich sind. Das Erhöhen der Anzahl von Knoten in einem Pool ist daher so einfach wie das Angeben der neuen Zielknotenanzahl. Der Starttask stellt die für den Batch-Dienst erforderlichen Informationen zur Verfügung, um die neuen Knoten zu konfigurieren und zur Annahme von Tasks vorzubereiten.

Wie bei jedem anderen Azure Batch-Task kann zusätzlich zu einer auszuführenden Befehlszeile eine Liste mit Ressourcendateien in [Azure Storage](../storage/index.yml) angegeben werden. Der Batch-Dienst kopiert zunächst die Ressourcendateien aus Azure Storage auf den Knoten und führt dann die Befehlszeile aus. Bei einem poolbezogenen Starttask enthält die Dateiliste üblicherweise die Taskanwendung und die damit verbundenen Abhängigkeiten.

Der Starttask kann aber auch Referenzdaten für alle Tasks enthalten, die auf dem Computeknoten ausgeführt werden. Die Befehlszeile eines Starttasks kann etwa einen `robocopy`-Vorgang ausführen, um Anwendungsdateien (die als Ressourcendateien angegeben und auf den Knoten heruntergeladen wurden) aus dem [Arbeitsverzeichnis](files-and-directories.md) des Starttasks in den **freigegebenen Ordner** zu kopieren, und anschließend eine MSI-Datei oder `setup.exe` ausführen.

In der Regel empfiehlt es sich, dass der Batch-Dienst auf den Abschluss des Starttasks wartet und erst dann davon ausgeht, dass der Knoten nun für die Taskzuweisung bereit ist. Dieses Verhalten ist jedoch konfigurierbar.

Kann ein Starttask für einen Computeknoten nicht erfolgreich ausgeführt werden, wird der Status des Knotens entsprechend aktualisiert, und dem Knoten werden keine Tasks zugewiesen. Ein Starttask wird nicht erfolgreich durchgeführt, wenn ein Problem beim Kopieren der Ressourcendateien aus dem Speicher auftritt oder wenn der Prozess, der von der Befehlszeile ausgeführt wird, einen Exitcode ungleich NULL zurückgibt.

Wenn Sie den Starttask einem vorhandenen Pool hinzufügen oder ihn für diesen aktualisieren, müssen Sie dessen Computeknoten für den Starttask, der auf die Knoten angewendet wird, neu starten.

>[!NOTE]
> Batch beschränkt die Gesamtgröße eines Starttasks (einschließlich Ressourcendateien und Umgebungsvariablen). Wenn Sie die Größe eines Starttasks verringern möchten, haben Sie zwei Möglichkeiten:
>
> 1. Sie können Anwendungspakete zum Verteilen von Anwendungen oder Daten auf jedem Knoten in Ihrem Batch-Pool verwenden. Weitere Informationen zu Anwendungspaketen finden Sie unter [Anwendungsbereitstellung mit Azure Batch-Anwendungspaketen](batch-application-packages.md).
> 2. Sie können manuell ein ZIP-Archiv erstellen, das Ihre Anwendungsdateien enthält. Laden Sie Ihr ZIP-Archiv als Blob in Azure Storage hoch. Geben Sie das ZIP-Archiv als Ressourcendatei für Ihre Startaufgabe an. Entpacken Sie das Archiv über die Befehlszeile, bevor Sie die Befehlszeile für Ihre Startaufgabe ausführen. 
>
>    Zum Extrahieren des Archivs können Sie ein Archivierungstool Ihrer Wahl verwenden. Sie müssen das zum Entpacken des Archivs verwendete Tool als Ressourcendatei für die Startaufgabe angeben.

### <a name="job-manager-task"></a>Auftrags-Manager-Aufgabe

In der Regel verwenden Sie einen Auftrags-Manager-Task zur Steuerung und/oder Überwachung der Auftragsausführung. Beispielsweise werden Auftrags-Manager-Tasks häufig dazu verwendet, die Tasks für einen Auftrag zu erstellen und zu übermitteln, zusätzliche auszuführende Tasks festzulegen und zu bestimmen, wann die Arbeit abgeschlossen ist.

Ein Auftrags-Manager-Task ist jedoch nicht auf diese Aktivitäten beschränkt. Vielmehr handelt es sich um einen vollwertigen Task, der jegliche für den Auftrag erforderliche Aktionen ausführen kann. So kann ein Auftrags-Manager-Task beispielsweise eine als Parameter angegebene Datei herunterladen, den Inhalt der Datei analysieren und basierend auf diesem Inhalt zusätzliche Tasks übermitteln.

Eine Auftrags-Manager-Aufgabe wird vor allen anderen Aufgaben gestartet. Er zeichnet sich durch Folgendes aus:

- Er wird automatisch vom Batch-Dienst als Task übermittelt, wenn der Auftrag erstellt wird.
- Seine Ausführung wird so geplant, dass er vor den anderen Tasks in einem Auftrag ausgeführt wird.
- Der zugeordnete Knoten wird als letzter aus einem Pool entfernt, wenn der Pool verkleinert wird.
- Die Beendigung kann an die Beendigung aller Aufgaben im Auftrag gebunden sein.
- Ein neu zu startender Auftrags-Manager-Task hat höchste Priorität. Wenn kein Knoten im Leerlauf verfügbar ist, kann der Batch-Dienst einen der anderen ausgeführten Tasks im Pool beenden, um Platz für die Ausführung des Auftrags-Manager-Tasks zu schaffen.
- Ein Auftrags-Manager-Task in einem Auftrag besitzt keine höhere Priorität als die Tasks in anderen Aufträgen. Auftragsübergreifend werden nur Prioritäten auf Auftragsebene beachtet.

### <a name="job-preparation-and-release-tasks"></a>Tasks zur Auftragsvorbereitung und -freigabe

Batch bietet Auftragsvorbereitungstasks für die Einrichtung vor der Auftragsausführung und Auftragsfreigabetasks für die Wartung oder Bereinigung nach der Auftragsausführung.

Wird vor der Ausführung der anderen Tasks eines Auftrags auf allen Computeknoten ausgeführt, für die eine Taskausführung geplant ist. Mithilfe eines Tasks zur Auftragsvorbereitung können Sie z. B. auftragsspezifische Daten kopieren, die von allen Tasks gemeinsam genutzt werden.

Wird nach Abschluss eines Auftrags auf jedem Knoten im Pool ausgeführt, auf dem mindestens ein Task ausgeführt wurde. Mit einem Task zur Auftragsfreigabe können Sie z. B. Daten löschen, die vom Task zur Auftragsvorbereitung kopiert wurden, oder Diagnoseprotokolldaten komprimieren und hochladen.

Sowohl für Tasks zur Auftragsvorbereitung als auch für Tasks zur Auftragsfreigabe können Sie eine Befehlszeile angeben, die ausgeführt werden soll, wenn der Task aufgerufen wird. Sie ermöglichen beispielsweise das Herunterladen von Dateien, das Ausführen mit erhöhten Rechten, die Verwendung benutzerdefinierter Umgebungsvariablen sowie das Angeben der maximalen Ausführungsdauer, der Anzahl von Wiederholungsversuchen und der Dateiaufbewahrungsdauer.

Weitere Informationen zu Auftragsvorbereitungs- und -freigabetasks finden Sie unter [Ausführen von Auftragsvorbereitungs- und Auftragsabschlusstasks auf Azure Batch-Computeknoten](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Task mit mehreren Instanzen

Ein [Task mit mehreren Instanzen](batch-mpi.md) ist ein Task, der für die gleichzeitige Ausführung auf mehreren Computeknoten konfiguriert ist. Tasks mit mehreren Instanzen ermöglichen Computeszenarien mit hohem Leistungsbedarf, in denen eine einzelne Workload von einer gemeinsam zugeordneten Gruppe von Computeknoten verarbeitet werden muss, z. B. bei MPI (Message Passing Interface).

Ausführliche Informationen zum Ausführen von MPI-Aufträgen in Batch mithilfe der Batch .NET-Bibliothek finden Sie unter [Verwendung von Tasks mit mehreren Instanzen zum Ausführen von MPI-Anwendungen (Message Passing Interface) in Azure Batch](batch-mpi.md).

### <a name="task-dependencies"></a>Abhängigkeiten von Aufgaben

Mithilfe von [Abhängigkeiten von Aufgaben](batch-task-dependencies.md) können Sie – wie der Name schon vermuten lässt – angeben, dass die Ausführung einer Aufgabe vom Abschluss einer anderen Aufgaben abhängig ist. Dieses Feature ist hilfreich, wenn ein Downstreamtask die Ausgabe eines Upstreamtasks nutzt oder ein Upstreamtask eine erforderliche Initialisierung für einen Downstreamtask vornimmt.

Um dieses Feature verwenden zu können, müssen Sie zuerst [Taskabhängigkeiten](batch-task-dependencies.md#enable-task-dependencies
) für den Batch-Auftrag aktivieren. Geben Sie dann für jede Aufgabe, die von einer (oder mehreren) anderen abhängt, die übergeordneten Aufgaben an.

Mit Abhängigkeiten von Aufgaben können Sie beispielsweise folgende Szenarien konfigurieren:

- *TaskB* hängt von *TaskA* ab. (*TaskB* kann erst ausgeführt werden, wenn *TaskA* abgeschlossen ist.)
- *TaskC* hängt sowohl von *TaskA* als auch von *TaskB* ab.
- Die Ausführung von *TaskD* hängt von mehreren Tasks (beispielsweise von den Tasks *1* bis *10*) ab.

Weitere Informationen finden Sie unter [Taskabhängigkeiten in Azure Batch](batch-task-dependencies.md) und im Codebeispiel [TaskDependencies](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) im GitHub-Repository [azure-batch-samples](https://github.com/Azure-Samples/azure-batch-samples).

### <a name="environment-settings-for-tasks"></a>Umgebungseinstellungen für Tasks

Jeder Task, der vom Batch-Dienst ausgeführt wird, hat Zugriff auf die Umgebungsvariablen, die für Computeknoten festgelegt werden. Dies gilt auch für Umgebungsvariablen, die vom Batch-Dienst definiert werden ([dienstdefiniert](./batch-compute-node-environment-variables.md)), und für benutzerdefinierte Umgebungsvariablen, die Sie für Ihre Tasks definieren können. Die Anwendungen und Skripts, die von Ihren Tasks ausgeführt werden, haben während der Ausführung Zugriff auf diese Umgebungsvariablen.

Sie können benutzerdefinierte Umgebungsvariablen auf Task- oder Auftragsebene festlegen, indem Sie die Eigenschaft *Umgebungseinstellungen* für diese Entitäten auffüllen. Weitere Informationen finden Sie unter dem Vorgang [Hinzufügen eines Tasks zu einem Auftrag](/rest/api/batchservice/task/add?) (Batch REST-API) oder unter den Eigenschaften [CloudTask.EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) und [CloudJob.CommonEnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudjob) in Batch .NET.

Die Clientanwendung bzw. der Dienst kann die Umgebungsvariablen eines Tasks abrufen – sowohl dienstdefiniert als auch benutzerdefiniert –, indem der Vorgang [Abrufen von Informationen zu einer Aufgabe](/rest/api/batchservice/task/get) (Batch REST) verwendet oder auf die Eigenschaft [CloudTask.EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) (Batch .NET) zugegriffen wird. Prozesse, die auf einem Computeknoten ausgeführt werden, können auf diese und andere Umgebungsvariablen auf dem Knoten zugreifen – beispielsweise mit der vertrauten `%VARIABLE_NAME%`-Syntax (Windows) oder mit der `$VARIABLE_NAME`-Syntax (Linux).

Eine vollständige Liste mit allen vom Dienst definierten Umgebungsvariablen finden Sie unter [Compute node environment variables](batch-compute-node-environment-variables.md) (Computeknoten-Umgebungsvariablen).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Dateien und Verzeichnissen](files-and-directories.md).
