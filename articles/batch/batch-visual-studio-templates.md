---
title: Erstellen von Lösungen mit Visual Studio-Vorlagen – Azure Batch | Microsoft-Dokumentation
description: Es wird beschrieben, wie Visual Studio-Projektvorlagen Sie beim Implementieren und Ausführen von rechenintensiven Workloads in Azure Batch unterstützen können.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 085bfa582b676f34a02e4c1c5ae7e69c49e5cb4e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60550081"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Verwenden von Visual Studio-Vorlagen für den schnellen Einstieg in Batch-Lösungen

Die Visual Studio-Vorlagen vom Typ **Auftrags-Manager** und **Aufgabenprozessor** für Batch enthalten Code zum Implementieren und Ausführen Ihrer rechenintensiven Workloads in Batch mit dem geringstmöglichen Aufwand. In diesem Dokument werden diese Vorlagen beschrieben, und es enthält eine Anleitung zur Verwendung.

> [!IMPORTANT]
> Im Artikel geht es nur um Informationen zu diesen beiden Vorlagen, und es wird vorausgesetzt, dass Sie mit dem Batch-Dienst und seinen wichtigsten Begriffen vertraut sind: Pools, Computeknoten, Aufträge und Aufgaben, Auftrags-Manager-Aufgaben, Umgebungsvariablen und andere relevante Informationen. Weitere Informationen finden Sie unter [Grundlagen von Azure Batch](batch-technical-overview.md) und [Übersicht über Batch-Features für Entwickler](batch-api-basics.md).
> 
> 

## <a name="high-level-overview"></a>Allgemeine Übersicht
Sie können die Auftrags-Manager- und Aufgabenprozessorvorlagen verwenden, um zwei nützliche Komponenten zu erstellen:

* Eine Auftrags-Manager-Aufgabe, mit der eine Auftragsteilung implementiert wird. Hiermit kann ein Auftrag in mehrere Aufgaben unterteilt werden, die unabhängig voneinander parallel ausgeführt werden können.
* Ein Aufgabenprozessor, der zum Durchführen der Vor- und Nachverarbeitung für die Befehlszeile einer Anwendung verwendet werden kann.

Beim Rendern eines Films wird ein einzelner Filmauftrag per Auftragsteilung beispielsweise in Hunderte oder Tausende von separaten Aufgaben unterteilt, mit denen einzelne Bilder separat verarbeitet werden. Entsprechend ruft der Aufgabenprozessor dann die Renderinganwendung und alle abhängigen Prozesse auf, die zum Rendern der einzelnen Bilder benötigt werden, und führt alle zusätzlichen Aktionen aus (z.B. das Kopieren des gerenderten Bilds an einen Speicherort).

> [!NOTE]
> Die Auftrags-Manager- und Aufgabenprozessorvorlagen sind voneinander unabhängig. Sie können je nach den Anforderungen Ihres Computeauftrags und Ihrer Präferenz also entweder beide oder nur eine Vorlage verwenden.
> 
> 

Wie im Diagramm unten zu sehen, durchläuft ein Computeauftrag, für den diese Vorlagen verwendet werden, drei Phasen:

1. Der Clientcode (z.B. Anwendung, Webdienst usw.) übermittelt einen Auftrag an den Batch-Dienst in Azure und gibt dabei als Auftrags-Manager-Aufgabe das Auftrags-Manager-Programm an.
2. Der Batch-Dienst führt die Auftrags-Manager-Aufgabe auf einem Computeknoten aus, und die Auftragsteilung startet die angegebene Anzahl von Aufgabenprozessor-Aufgaben auf der erforderlichen Anzahl von Computeknoten. Dies basiert auf den Parametern und Spezifikationen im Code der Auftragsteilung.
3. Die Aufgabenprozessor-Aufgaben werden unabhängig voneinander parallel ausgeführt, um die Eingabedaten zu verarbeiten und die Ausgabedaten zu generieren.

![Diagramm zur Interaktion von Clientcode und Batch-Dienst][diagram01]

## <a name="prerequisites"></a>Voraussetzungen
Für die Verwendung der Batch-Vorlagen benötigen Sie Folgendes:

* Ein Computer mit installiertem Visual Studio 2015. Batchvorlagen werden derzeit nur für Visual Studio 2015 unterstützt.
* Die Batch-Vorlagen, die in der [Visual Studio Gallery][vs_gallery] als Visual Studio-Erweiterungen verfügbar sind. Es gibt zwei Möglichkeiten zum Abrufen der Vorlagen:
  
  * Installieren Sie die Vorlagen über das Dialogfeld **Erweiterungen und Updates** in Visual Studio (weitere Informationen finden Sie unter [Suchen und Verwenden von Visual Studio-Erweiterungen][vs_find_use_ext]). Suchen Sie im Dialogfeld **Erweiterungen und Updates** nach den folgenden beiden Erweiterungen, und laden Sie sie herunter:
    
    * Azure Batch-Auftrags-Manager mit Auftragsteilung
    * Azure Batch-Aufgabenprozessor
  * Laden Sie die Vorlagen aus dem Onlinekatalog für Visual Studio herunter: [Microsoft Azure Batch-Projektvorlagen][vs_gallery_templates]
* Wenn Sie die Nutzung der Funktion [Anwendungspakete](batch-application-packages.md) planen, um den Auftrags-Manager und Aufgabenprozessor für die Batch-Computeknoten bereitzustellen, müssen Sie ein Speicherkonto mit Ihrem Batch-Konto verknüpfen.

## <a name="preparation"></a>Vorbereitung
Wir empfehlen Ihnen die Erstellung einer Projektmappe, die Ihren Auftrags-Manager und Ihren Aufgabenprozessor enthalten kann. Dies kann Ihnen die gemeinsame Nutzung von Code zwischen Ihren Auftrags-Manager- und Aufgabenprozessor-Programmen erleichtern. Führen Sie die folgenden Schritte aus, um diese Projektmappe zu erstellen:

1. Öffnen Sie Visual Studio, und wählen Sie **Datei** > **Neu** > **Projekt** aus.
2. Erweitern Sie unter **Vorlagen** die Option **Andere Projekttypen**, klicken Sie auf **Visual Studio-Projektmappen**, und wählen Sie dann **Leere Projektmappe** aus.
3. Geben Sie einen Namen ein, der die Anwendung und den Zweck der Projektmappe beschreibt (z.B. „LitwareBatchTaskPrograms“).
4. Klicken Sie zum Erstellen der neuen Projektmappe auf **OK**.

## <a name="job-manager-template"></a>Auftrags-Manager-Vorlage
Die Auftrags-Manager-Vorlage dient Ihnen als Hilfe beim Implementieren einer Auftrags-Manager-Aufgabe, mit der die folgenden Aktionen durchgeführt werden können:

* Aufteilen eines Auftrags in mehrere Aufgaben
* Übermitteln der Aufgaben zur Ausführung in Batch

> [!NOTE]
> Weitere Informationen zu Auftrags-Manager-Aufgaben finden Sie unter [Übersicht über Batch-Features für Entwickler](batch-api-basics.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Erstellen eines Auftrags-Managers mit der Vorlage
Führen Sie diese Schritte aus, um einen Auftrags-Manager der zuvor erstellten Projektmappe hinzuzufügen:

1. Öffnen Sie die vorhandene Projektmappe in Visual Studio.
2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihre Projektmappe, und wählen Sie dann **Hinzufügen** > **Neues Projekt** aus.
3. Klicken Sie unter **Visual C#** auf **Cloud** und dann auf **Azure Batch Job Manager with Job Splitter** (Azure Batch-Auftrags-Manager mit Auftragsteilung).
4. Geben Sie einen Namen ein, der die Anwendung beschreibt und dieses Projekt als Auftrags-Manager identifiziert (z.B. „LitwareJobManager“).
5. Klicken Sie auf **OK**, um das Projekt zu erstellen.
6. Erstellen Sie abschließend das Projekt. So erzwingen Sie, dass von Visual Studio alle referenzierten NuGet-Pakete geladen werden, und stellen die Gültigkeit des Projekts sicher, bevor Sie mit der Durchführung von Änderungen beginnen.

### <a name="job-manager-template-files-and-their-purpose"></a>Auftrags-Manager-Vorlagendateien und ihr Zweck
Wenn Sie ein Projekt mit der Auftrags-Manager-Vorlage erstellen, werden drei Gruppen von Codedateien generiert:

* Die Hauptprogrammdatei (Program.cs). Sie enthält den Programmeinstiegspunkt und eine Ausnahmebehandlung auf oberster Ebene. Normalerweise sollte es nicht erforderlich sein, hieran Änderungen vorzunehmen.
* Das Frameworkverzeichnis. Es enthält die Dateien für die Standardaufgaben des Auftrags-Manager-Programms: Entpacken von Parametern, Hinzufügen von Aufgaben zum Batch-Auftrag usw. Normalerweise sollte es nicht erforderlich sein, diese Dateien zu ändern.
* Die Datei für die Auftragsteilung (JobSplitter.cs). In dieser Datei ordnen Sie die anwendungsspezifische Logik für das Aufteilen eines Auftrags in Aufgaben an.

Sie können natürlich je nach Bedarf weitere Dateien hinzufügen, um den Code für die Auftragsteilung zu unterstützen. Dies richtet sich nach der Komplexität der Auftragsteilungslogik.

Die Vorlage generiert auch .NET-Standardprojektdateien, z.B. eine CSPROJ-Datei, app.config, packages.config usw.

Im restlichen Teil dieses Abschnitts werden die unterschiedlichen Dateien und ihre Codestruktur beschrieben und die einzelnen Klassen erläutert.

![Visual Studio-Projektmappen-Explorer mit Projektmappe der Auftrags-Manager-Vorlage][solution_explorer01]

**Frameworkdateien**

* `Configuration.cs`: Kapselt das Laden von Auftragskonfigurationsdaten, z. B. Batch-Kontodetails, Anmeldeinformationen für verknüpfte Speicherkonten, Auftrags- und Aufgabeninformationen und Auftragsparameter. Außerdem wird der Zugriff auf von Batch definierte Umgebungsvariablen (siehe „Umgebungseinstellungen für Aufgaben“ in der Batch-Dokumentation) über die Configuration.EnvironmentVariable-Klasse ermöglicht.
* `IConfiguration.cs`: Abstrahiert die Implementierung der Configuration-Klasse, sodass Sie für Ihre Auftragsteilung einen Komponententest durchführen können, indem Sie ein „falsches“ Konfigurationsobjekt verwenden.
* `JobManager.cs`: Orchestriert die Komponenten des Auftrags-Manager-Programms. Sie ist für die Initialisierung der Auftragsteilung, das Aufrufen der Auftragsteilung und das Zustellen der Aufgaben zuständig, die von der Auftragsteilung an den Aufgabenabsender zurückgegeben werden.
* `JobManagerException.cs`: Stellt einen Fehler dar, aufgrund dessen der Auftrags-Manager beendet werden muss. JobManagerException wird zum Umschließen von „erwarteten“ Fehlern verwendet, wenn bestimmte Diagnoseinformationen im Rahmen der Beendigung bereitgestellt werden können.
* `TaskSubmitter.cs`: Diese Klasse ist für das Hinzufügen von Aufgaben, die von der Auftragsteilung zurückgegeben werden, zum Batch-Auftrag zuständig. Die JobManager-Klasse aggregiert die Sequenz der Aufgaben in Batches, um das effiziente und gleichzeitig rechtzeitige Hinzufügen zum Auftrag sicherzustellen. Anschließend wird „TaskSubmitter.SubmitTasks“ in einem Hintergrundthread für jeden Batch aufgerufen.

**Auftragsteilung**

`JobSplitter.cs`: Diese Klasse enthält anwendungsspezifische Logik für das Aufteilen des Auftrags in Aufgaben. Das Framework ruft die JobSplitter.Split-Methode auf, um eine Aufgabensequenz zu erhalten, die dem Auftrag bei der Rückgabe durch die Methode hinzugefügt wird. Dies ist die Klasse, in die Sie die Logik Ihres Auftrags injizieren. Implementieren Sie die Split-Methode, um eine Sequenz von CloudTask-Instanzen zurückzugeben, die für die Aufgaben stehen, in die Sie den Auftrag partitionieren möchten.

**.NET-Befehlszeilen-Standardprojektdateien**

* `App.config`: Standardmäßige .NET-Anwendungskonfigurationsdatei.
* `Packages.config`: Standardabhängigkeitsdatei des NuGet-Pakets.
* `Program.cs`: Enthält den Programmeinstiegspunkt und eine Ausnahmebehandlung auf oberster Ebene.

### <a name="implementing-the-job-splitter"></a>Implementieren der Auftragsteilung
Wenn Sie das Auftrags-Manager-Vorlagenprojekt öffnen, ist die Datei „JobSplitter.cs“ für das Projekt standardmäßig geöffnet. Sie können die Teilungslogik für die Aufgaben in Ihrer Workload implementieren, indem Sie wie hier gezeigt die Split()-Methode verwenden:

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

> [!NOTE]
> Der mit Anmerkungen versehene Abschnitt in der `Split()`-Methode ist der einzige Abschnitt des Auftrags-Manager-Vorlagencodes, der für Änderungen durch Sie vorgesehen ist, indem Sie die Logik zum Aufteilen der Aufträge auf unterschiedliche Aufgaben hinzufügen. Wenn Sie einen anderen Abschnitt der Vorlage ändern möchten, sollten Sie sich bereits mit der Funktionsweise von Batch vertraut gemacht haben und einige [Batch-Codebeispiele][github_samples] ausprobiert haben.
> 
> 

Mit der Split()-Implementierung besteht Zugriff auf Folgendes:

* Auftragsparameter über das Feld `_parameters`
* CloudJob-Objekt, das den Auftrag repräsentiert, über das Feld `_job`
* CloudTask-Objekt, das die Auftrags-Manager-Aufgabe repräsentiert, über das Feld `_jobManagerTask`

Für die `Split()`-Implementierung ist es nicht erforderlich, dem Auftrag direkt Aufgaben hinzuzufügen. Stattdessen sollte Ihr Code eine Sequenz von CloudTask-Objekten zurückgeben. Diese werden dem Auftrag automatisch von den Frameworkklassen hinzugefügt, die die Auftragsteilung aufrufen. Es ist üblich, die C#-Iteratorfunktion (`yield return`) zum Implementieren von Auftragsteilungen zu verwenden. So kann die Ausführung der Aufgaben so schnell wie möglich beginnen, und es muss nicht gewartet werden, bis alle Aufgaben berechnet wurden.

**Fehler bei der Auftragsteilung**

Wenn bei der Auftragsteilung ein Fehler auftritt, sollte einer dieser beiden Schritte ausgeführt werden:

* Beenden der Sequenz mit der C#-Anweisung `yield break`. Der Auftrags-Manager-Vorgang wird dann als erfolgreich angesehen.
* Auslösen einer Ausnahme. In diesem Fall wird der Auftrags-Manager-Vorgang als nicht erfolgreich angesehen, und unter Umständen wird ein neuer Versuch gestartet. Dies richtet sich nach der Konfiguration durch den Client.

In beiden Fällen können alle Aufgaben, die bereits von der Auftragsteilung zurückgegeben und dem Batch-Auftrag hinzugefügt wurden, ausgeführt werden. Falls dieses Verhalten nicht gewünscht ist, haben Sie folgende Möglichkeiten:

* Beenden des Auftrags vor der Rückgabe durch die Auftragsteilung
* Formulieren der gesamten Aufgabensammlung vor der Rückgabe (also Rückgabe von `ICollection<CloudTask>` oder `IList<CloudTask>`, anstatt die Auftragsteilung mit einem C#-Iterator zu implementieren)
* Verwenden von Aufgabenabhängigkeiten, um alle Aufgaben vom erfolgreichen Abschluss des Auftrags-Managers abhängig zu machen

**Wiederholungen des Auftrags-Managers**

Wenn für den Auftrags-Manager ein Fehler auftritt, kann je nach den Wiederholungseinstellungen des Clients vom Batch-Dienst ein Wiederholungsversuch durchgeführt werden. Im Allgemeinen ist dies sicher, da beim Hinzufügen von Aufgaben zum Auftrag durch das Framework alle bereits vorhandenen Aufgaben ignoriert werden. Falls die Berechnung von Aufgaben aber teuer ist, kann es sein, dass keine weiteren Kosten für die Neuberechnung der Aufgaben entstehen sollen, die dem Auftrag bereits hinzugefügt wurden. Umgekehrt gilt: Wenn für die erneute Ausführung nicht garantiert werden kann, dass die gleichen Aufgaben-IDs generiert werden, tritt das Verhalten „Duplikate ignorieren“ nicht ein. In diesen Fällen sollten Sie die Auftragsteilung so entwerfen, dass die bereits erledigte Arbeit erkannt und nicht wiederholt wird, indem vor Beginn der Aufgaben ein CloudJob.ListTasks-Vorgang durchgeführt wird.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Exitcodes und Ausnahmen in der Auftrags-Manager-Vorlage
Exitcodes und Ausnahmen bilden einen Mechanismus, mit dem das Ergebnis der Ausführung eines Programms ermittelt wird. Sie können auch zum Identifizieren von Problemen bei der Ausführung des Programms dienen. Mit der Auftrags-Manager-Vorlage werden die Exitcodes und Ausnahmen implementiert, die in diesem Abschnitt beschrieben werden.

Eine Auftrags-Manager-Aufgabe, die mit der Auftrags-Manager-Vorlage implementiert wird, kann drei mögliche Exitcodes zurückgeben:

| Code | BESCHREIBUNG |
| --- | --- |
| 0 |Der Auftrags-Manager wurde erfolgreich abgeschlossen. Der Code der Auftragsteilung wurde bis zum Ende ausgeführt, und alle Aufgaben wurden dem Auftrag hinzugefügt. |
| 1 |Die Auftrags-Manager-Aufgabe ist mit einer Ausnahme in einem „erwarteten“ Teil des Programms fehlgeschlagen. Die Ausnahme wurde in eine JobManagerException mit Diagnoseinformationen übersetzt und enthält nach Möglichkeit Vorschläge zur Behebung des Fehlers. |
| 2 |Die Auftrags-Manager-Aufgabe ist mit einer „unerwarteten“ Ausnahme fehlgeschlagen. Die Ausnahme wurde in der Standardausgabe protokolliert, aber der Auftrags-Manager konnte keine weiteren Informationen zur Diagnose oder Behebung hinzufügen. |

Wenn für eine Auftrags-Manager-Aufgabe ein Fehler auftritt, kann es sein, dass dem Dienst vor dem Auftreten des Fehlers noch einige Aufgaben hinzugefügt wurden. Diese Tasks werden wie gewohnt ausgeführt. Eine Beschreibung dieses Codepfads finden Sie oben unter „Fehler bei der Auftragsteilung“.

Alle Informationen, die von Ausnahmen zurückgegeben werden, werden in die Dateien „stdout.txt“ und „stderr.txt“ geschrieben. Weitere Informationen finden Sie unter [Fehlerbehandlung](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Clientaspekte
In diesem Abschnitt werden einige Anforderungen an die Clientimplementierung beschrieben, die erfüllt sein müssen, wenn ein Auftrags-Manager basierend auf dieser Vorlage aufgerufen wird. Ausführliche Informationen zur Übergabe von Parametern und Umgebungseinstellungen finden Sie unter [Übergeben von Parametern und Umgebungsvariablen aus dem Clientcode](#pass-environment-settings) .

**Erforderliche Anmeldeinformationen**

Damit dem Azure Batch-Auftrag Aufgaben hinzugefügt werden können, benötigen Sie Ihre Azure Batch-Konto-URL und den dazugehörigen Schlüssel. Diese Angaben müssen Sie in den Umgebungsvariablen YOUR_BATCH_URL und YOUR_BATCH_KEY übergeben. Sie können diese Variablen in den Umgebungseinstellungen für Auftrags-Manager-Aufgaben festlegen. Beispielsweise in einem C#-Client:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Speicheranmeldeinformationen**

Normalerweise muss der Client die Anmeldeinformationen für das verknüpfte Speicherkonto nicht für die Auftrags-Manager-Aufgabe bereitstellen, da (a) die meisten Auftrags-Manager nicht explizit auf das verknüpfte Speicherkonto zugreifen müssen und (b) das verknüpfte Speicherkonto häufig für alle Aufgaben als gemeinsame Umgebungseinstellung für den Auftrag bereitgestellt wird. Wenn Sie das verknüpfte Speicherkonto nicht über die gemeinsamen Umgebungseinstellungen bereitstellen und für den Auftrags-Manager der Zugriff auf den verknüpften Speicher erforderlich ist, sollten Sie die Anmeldeinformationen für den verknüpften Speicher wie folgt angeben:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Einstellungen für Auftrags-Manager-Aufgaben**

Der Client sollte das Auftrags-Manager-Flag *killJobOnCompletion* auf **false**festlegen.

In der Regel ist es kein Problem, dass der Client *runExclusive* auf **false**festlegt.

Der Client sollte die Sammlung *resourceFiles* oder *applicationPackageReferences* verwenden, damit die ausführbare Datei des Auftrags-Managers (und die erforderlichen DLLs) auf dem Computeknoten bereitgestellt wird.

Standardmäßig wird für den Auftrags-Manager bei einem Fehler kein erneuter Versuch durchgeführt. Je nach Auftrags-Manager-Logik kann es für den Client ratsam sein, Wiederholungen per *constraints*/*maxTaskRetryCount* durchzuführen.

**Auftragseinstellungen**

Wenn die Auftragsteilung Aufgaben mit Abhängigkeiten ausgibt, muss der Client das usesTaskDependencies-Element des Auftrags auf „true“ festlegen.

Beim Auftragsteilungsmodell ist es für Clients ungewöhnlich, dass Aufträgen zusätzlich zu den von der Auftragsteilung erstellten Elementen weitere Aufgaben hinzugefügt werden sollen. Normalerweise sollte der Client daher das *onAllTasksComplete* -Element des Auftrags auf **terminatejob**festlegen.

## <a name="task-processor-template"></a>Aufgabenprozessorvorlage
Eine Aufgabenprozessorvorlage unterstützt Sie beim Implementieren eines Aufgabenprozessors, mit dem die folgenden Aktionen durchgeführt werden können:

* Einrichten der Informationen, die von den einzelnen Batch-Aufgaben für die Ausführung benötigt werden
* Ausführen aller Aktionen, die für die einzelnen Batch-Aufgaben erforderlich sind
* Speichern von Aufgabenausgaben im dauerhaften Speicher

Zum Ausführen von Aufgaben in Batch ist zwar kein Aufgabenprozessor erforderlich, aber der Hauptvorteil der Verwendung eines Aufgabenprozessors besteht darin, dass ein Wrapper zum Implementieren aller Aktionen zur Aufgabenausführung an einem Ort bereitgestellt wird. Beispiele: Sie müssen mehrere Anwendungen im Kontext jeder Aufgabe ausführen oder Daten nach Abschluss einer Aufgabe jeweils in den dauerhaften Speicher kopieren.

Die vom Aufgabenprozessor durchgeführten Aktionen können einfach oder komplex sein und in geringer oder hoher Anzahl vorliegen. Dies richtet sich jeweils nach Ihrer Workload. Indem Sie außerdem alle Aufgabenaktionen in einem Aufgabenprozessor implementieren, können Sie basierend auf Änderungen an Anwendungen oder Workloadanforderungen problemlos Aktionen aktualisieren oder hinzufügen. In einigen Fällen ist ein Aufgabenprozessor aber unter Umständen nicht die optimale Lösung für Ihre Implementierung, da dies mit einer unnötigen Komplexität verbunden sein kann. Ein Beispiel hierfür ist die Ausführung von Aufträgen, die schnell über eine einfache Befehlszeile gestartet werden können.

### <a name="create-a-task-processor-using-the-template"></a>Erstellen eines Aufgabenprozessors mit der Vorlage
Führen Sie diese Schritte aus, um einen Aufgabenprozessor der zuvor erstellten Projektmappe hinzuzufügen:

1. Öffnen Sie die vorhandene Projektmappe in Visual Studio.
2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **Hinzufügen** und **Neues Projekt**.
3. Klicken Sie unter **Visual C#** auf **Cloud** und dann auf **Azure Batch-Aufgabenprozessor**.
4. Geben Sie einen Namen ein, der die Anwendung beschreibt und dieses Projekt als Aufgabenprozessor identifiziert (z.B. „LitwareTaskProcessor“).
5. Klicken Sie auf **OK**, um das Projekt zu erstellen.
6. Erstellen Sie abschließend das Projekt. So erzwingen Sie, dass von Visual Studio alle referenzierten NuGet-Pakete geladen werden, und stellen die Gültigkeit des Projekts sicher, bevor Sie mit der Durchführung von Änderungen beginnen.

### <a name="task-processor-template-files-and-their-purpose"></a>Aufgabenprozessor-Vorlagendateien und ihr Zweck
Wenn Sie ein Projekt mit der Aufgabenprozessorvorlage erstellen, werden drei Gruppen von Codedateien generiert:

* Die Hauptprogrammdatei (Program.cs). Sie enthält den Programmeinstiegspunkt und eine Ausnahmebehandlung auf oberster Ebene. Normalerweise sollte es nicht erforderlich sein, hieran Änderungen vorzunehmen.
* Das Frameworkverzeichnis. Es enthält die Dateien für die Standardaufgaben des Auftrags-Manager-Programms: Entpacken von Parametern, Hinzufügen von Aufgaben zum Batch-Auftrag usw. Normalerweise sollte es nicht erforderlich sein, diese Dateien zu ändern.
* Die Aufgabenprozessordatei (TaskProcessor.cs). In dieser Datei ordnen Sie Ihre anwendungsspezifische Logik zum Ausführen einer Aufgabe an (normalerweise per Aufruf einer vorhandenen ausführbaren Datei). Außerdem ist in dieser Datei der Code für die Vor- und Nachbearbeitung enthalten, z.B. zum Herunterladen von weiteren Daten oder Hochladen von Ergebnisdateien.

Sie können natürlich je nach Bedarf weitere Dateien hinzufügen, um den Code für den Aufgabenprozessor zu unterstützen. Dies richtet sich nach der Komplexität der Auftragsteilungslogik.

Die Vorlage generiert auch .NET-Standardprojektdateien, z.B. eine CSPROJ-Datei, app.config, packages.config usw.

Im restlichen Teil dieses Abschnitts werden die unterschiedlichen Dateien und ihre Codestruktur beschrieben und die einzelnen Klassen erläutert.

![Visual Studio-Projektmappen-Explorer mit Projektmappe der Aufgabenprozessorvorlage][solution_explorer02]

**Frameworkdateien**

* `Configuration.cs`: Kapselt das Laden von Auftragskonfigurationsdaten, z. B. Batch-Kontodetails, Anmeldeinformationen für verknüpfte Speicherkonten, Auftrags- und Aufgabeninformationen und Auftragsparameter. Außerdem wird der Zugriff auf von Batch definierte Umgebungsvariablen (siehe „Umgebungseinstellungen für Aufgaben“ in der Batch-Dokumentation) über die Configuration.EnvironmentVariable-Klasse ermöglicht.
* `IConfiguration.cs`: Abstrahiert die Implementierung der Configuration-Klasse, sodass Sie für Ihre Auftragsteilung einen Komponententest durchführen können, indem Sie ein „falsches“ Konfigurationsobjekt verwenden.
* `TaskProcessorException.cs`: Stellt einen Fehler dar, aufgrund dessen der Auftrags-Manager beendet werden muss. TaskProcessorException wird zum Umschließen von „erwarteten“ Fehlern verwendet, wenn bestimmte Diagnoseinformationen im Rahmen der Beendigung bereitgestellt werden können.

**Aufgabenprozessor**

* `TaskProcessor.cs`: Führt die Aufgabe aus. Das Framework ruft die TaskProcessor.Run-Methode auf. Dies ist die Klasse, in die Sie die anwendungsspezifische Logik Ihrer Aufgabe injizieren. Implementieren Sie die Run-Methode für folgende Zwecke:
  * Analysieren und Überprüfen von Aufgabenparametern
  * Zusammenstellen der Befehlszeile für alle externen Programme, die Sie aufrufen möchten
  * Protokollieren aller Diagnoseinformationen, die Sie ggf. zu Debugzwecken benötigen
  * Starten eines Prozesses über die Befehlszeile
  * Warten auf die Beendigung eines Prozesses
  * Erfassen des Exitcodes des Prozesses zur Ermittlung von Erfolg oder Fehler
  * Speichern aller Ausgabedateien, die beibehalten werden sollen, im dauerhaften Speicher

**.NET-Befehlszeilen-Standardprojektdateien**

* `App.config`: Standardmäßige .NET-Anwendungskonfigurationsdatei.
* `Packages.config`: Standardabhängigkeitsdatei des NuGet-Pakets.
* `Program.cs`: Enthält den Programmeinstiegspunkt und eine Ausnahmebehandlung auf oberster Ebene.

## <a name="implementing-the-task-processor"></a>Implementieren des Aufgabenprozessors
Wenn Sie das Aufgabenprozessor-Vorlagenprojekt öffnen, ist die Datei „TaskProcessor.cs“ standardmäßig geöffnet. Sie können die Ausführungslogik für die Aufgaben in Ihrer Workload implementieren, indem Sie wie hier gezeigt die Run()-Methode verwenden:

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
> [!NOTE]
> Der mit Anmerkungen versehene Abschnitt in der Run()-Methode ist der einzige Abschnitt des Aufgabenprozessor-Vorlagencodes, der für Änderungen durch Sie vorgesehen ist, indem Sie die Ausführungslogik für die Aufgaben in Ihrer Workload hinzufügen. Wenn Sie einen anderen Abschnitt der Vorlage ändern möchten, sollten Sie sich zuerst mit der Funktionsweise von Batch vertraut machen, indem Sie die Batch-Dokumentation lesen und einige Batch-Codebeispiele ausprobieren.
> 
> 

Die Run()-Methode ist für das Starten der Befehlszeile, Starten von einem oder mehreren Prozessen, Warten auf den Abschluss aller Prozesse, Speichern der Ergebnisse und schließlich für das Zurückgeben mit einem Exitcode zuständig. In der Run()-Methode implementieren Sie die Verarbeitungslogik für Ihre Aufgaben. Mit dem Aufgabenprozessor-Framework wird die Run()-Methode für Sie aufgerufen. Sie müssen sie nicht selbst aufrufen.

Mit der Run()-Implementierung besteht Zugriff auf Folgendes:

* Die Aufgabenparameter über das Feld `_parameters`.
* Die Auftrags- und Aufgaben-IDs über die Felder `_jobId` und `_taskId`.
* Die Aufgabenkonfiguration über das Feld `_configuration` .

**Aufgabenfehler**

Bei einem Fehler können Sie die Run()-Methode beenden, indem Sie eine Ausnahme auslösen. Der Ausnahmehandler der obersten Ebene hat dann aber die Kontrolle über den Exitcode der Aufgabe inne. Wenn Sie den Exitcode steuern müssen, um unterschiedliche Arten von Fehlern unterscheiden zu können (z.B. zu Diagnosezwecken oder weil einige Fehlermodi den Auftrag beenden und andere dieses Verhalten nicht zeigen sollen), sollten Sie die Run()-Methode durch das Zurückgeben eines Exitcodes beenden, der nicht null ist. Dies wird der Exitcode der Aufgabe.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Exitcodes und Ausnahmen in der Aufgabenprozessorvorlage
Exitcodes und Ausnahmen bilden einen Mechanismus, mit dem das Ergebnis der Ausführung eines Programms ermittelt wird. Sie können auch zum Identifizieren von Problemen bei der Ausführung des Programms dienen. Mit der Aufgabenprozessorvorlage werden die Exitcodes und Ausnahmen implementiert, die in diesem Abschnitt beschrieben werden.

Eine Aufgabenprozessoraufgabe, die mit der Aufgabenprozessorvorlage implementiert wird, kann drei mögliche Exitcodes zurückgeben:

| Code | BESCHREIBUNG |
| --- | --- |
| [Process.ExitCode][process_exitcode] |Der Aufgabenprozessor wurde bis zum Ende ausgeführt. Beachten Sie, dass dies nicht heißt, dass das aufgerufene Programm erfolgreich war. Es bedeutet lediglich, dass es vom Aufgabenprozessor erfolgreich aufgerufen wurde und alle Schritte der Nachverarbeitung ohne Ausnahmen durchgeführt wurden. Die Bedeutung des Exitcodes richtet sich nach dem aufgerufenen Programm. Normalerweise bedeutet der Exitcode 0, dass das Programm erfolgreich war, und alle anderen Exitcodes bedeuten, dass die Ausführung nicht erfolgreich war. |
| 1 |Der Aufgabenprozessor ist mit einer Ausnahme in einem „erwarteten“ Teil des Programms fehlgeschlagen. Die Ausnahme wurde in eine `TaskProcessorException` mit Diagnoseinformationen übersetzt und enthält nach Möglichkeit Vorschläge für die Behebung des Fehlers. |
| 2 |Der Aufgabenprozessor ist mit einer „unerwarteten“ Ausnahme fehlgeschlagen. Die Ausnahme wurde in der Standardausgabe protokolliert, aber der Aufgabenprozessor konnte keine weiteren Informationen zur Diagnose oder Behebung hinzufügen. |

> [!NOTE]
> Wenn das von Ihnen aufgerufene Programm die Exitcodes 1 und 2 verwendet, um bestimmte Fehlermodi anzugeben, ist die Verwendung der Exitcodes 1 und 2 für Aufgabenprozessorfehler mehrdeutig. Sie können diese Aufgabenprozessor-Fehlercodes in andere Exitcodes ändern, indem Sie die Ausnahmefälle in der Datei „Program.cs“ bearbeiten.
> 
> 

Alle Informationen, die von Ausnahmen zurückgegeben werden, werden in die Dateien „stdout.txt“ und „stderr.txt“ geschrieben. Weitere Informationen finden Sie in der Batch-Dokumentation unter „Fehlerbehandlung“.

### <a name="client-considerations"></a>Clientaspekte
**Speicheranmeldeinformationen**

Wenn Ihr Aufgabenprozessor Azure Blob Storage zum dauerhaften Speichern von Ausgaben verwendet (z.B. Nutzung der Hilfsbibliothek mit Dateikonventionen), benötigt er *entweder* Zugriff auf die Cloudspeicherkonto-Anmeldeinformationen *oder* eine Blobcontainer-URL mit einer Shared Access Signature (SAS). Die Vorlage umfasst die Unterstützung zum Bereitstellen von Anmeldeinformationen über allgemeine Umgebungsvariablen. Der Client kann die Speicheranmeldeinformationen wie folgt übergeben:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Das Speicherkonto ist dann in der TaskProcessor-Klasse über die `_configuration.StorageAccount`-Eigenschaft verfügbar.

Wenn Sie die Verwendung einer Container-URL mit SAS vorziehen, können Sie diese Angaben auch über eine allgemeine Umgebungseinstellung für Aufträge übergeben. Die Aufgabenprozessorvorlage umfasst derzeit aber noch keine integrierte Unterstützung für dieses Verhalten.

**Speichereinrichtung**

Es wird empfohlen, dass der Client oder die Auftrags-Manager-Aufgabe alle für die Aufgaben erforderlichen Container erstellt, bevor die Aufgaben dem Auftrag hinzugefügt werden. Dies ist obligatorisch, wenn Sie eine Container-URL mit SAS verwenden, da eine URL dieser Art keine Berechtigung zum Erstellen des Containers enthält. Dies ist auch zu empfehlen, wenn Sie Anmeldeinformationen für das Speicherkonto übergeben, da dann nicht jede Aufgabe „CloudBlobContainer.CreateIfNotExistsAsync“ für den Container aufrufen muss.

## <a name="pass-parameters-and-environment-variables"></a>Übergeben von Parametern und Umgebungsvariablen
### <a name="pass-environment-settings"></a>Übergeben von Umgebungseinstellungen
Ein Client kann Informationen in Form von Umgebungseinstellungen an die Auftrags-Manager-Aufgabe übergeben. Diese Informationen können dann von der Auftrags-Manager-Aufgabe beim Generieren der Aufgabenprozessor-Aufgaben verwendet werden, die im Rahmen des Computeauftrags ausgeführt werden. Beispiele für die Informationen, die Sie als Umgebungseinstellungen übergeben können, sind:

* Speicherkontoname und Kontoschlüssel
* Batch-Konto-URL
* Batch-Konto-Schlüssel

Der Batch-Dienst verfügt über einen einfachen Mechanismus zum Übergeben von Umgebungseinstellungen an eine Auftrags-Manager-Aufgabe, indem die `EnvironmentSettings`-Eigenschaft in [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask] verwendet wird.

Um beispielsweise die `BatchClient` -Instanz für ein Batch-Konto abzurufen, können Sie die URL und die Anmeldeinformationen eines gemeinsam verwendeten Schlüssels für das Batch-Konto als Umgebungsvariablen übergeben. Außerdem können Sie den Speicherkontonamen und den Speicherkontoschlüssel als Umgebungsvariablen übergeben, um auf das Speicherkonto zuzugreifen, das mit dem Batch-Konto verknüpft ist.

### <a name="pass-parameters-to-the-job-manager-template"></a>Übergeben von Parametern an die Auftrags-Manager-Vorlage
In vielen Fällen ist es sinnvoll, Parameter pro Auftrag an die Auftrags-Manager-Aufgabe zu übergeben, um entweder den Prozess der Auftragsteilung zu steuern oder die Aufgaben für den Auftrag zu konfigurieren. Hierzu können Sie eine JSON-Datei mit dem Namen „parameters.json“ als Ressourcendatei für die Auftrags-Manager-Aufgabe hochladen. Die Parameter können dann im Feld `JobSplitter._parameters` der Auftrags-Manager-Vorlage zur Verfügung gestellt werden.

> [!NOTE]
> Der Handler für integrierte Parameter unterstützt nur Wörterbücher vom Typ „Zeichenfolge-zu-Zeichenfolge“. Wenn Sie komplexe JSON-Werte als Parameterwerte übergeben möchten, muss die Übergabe in Form von Zeichenfolgen durchgeführt werden, und Sie müssen sie in der Auftragsteilung analysieren oder die `Configuration.GetJobParameters` -Methode des Frameworks ändern.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Übergeben von Parametern an die Aufgabenprozessorvorlage
Sie können Parameter auch an einzelne Aufgaben übergeben, die mit der Aufgabenprozessorvorlage implementiert werden. Wie bei der Auftrags-Manager-Vorlage auch, sucht die Aufgabenprozessorvorlage nach einer Ressourcendatei mit dem Namen

„parameters.json“. Wenn diese Datei gefunden wird, wird sie als Parameterwörterbuch geladen. Es gibt zwei Optionen für die Übergabe von Parametern an die Aufgabenprozessor-Aufgaben:

* Wiederverwenden des JSON-Codes für Auftragsparameter. Dies funktioniert gut, wenn nur Parameter vorhanden sind, die für den gesamten Auftrag gelten (z.B. eine Höhe und Breite für das Rendern). Fügen Sie hierzu beim Erstellen einer CloudTask in der Auftragsteilung einen Verweis auf das Ressourcendateiobjekt „parameters.json“ aus dem ResourceFiles-Element der Auftrags-Manager-Aufgabe (`JobSplitter._jobManagerTask.ResourceFiles`) der ResourceFiles-Sammlung von CloudTask hinzu.
* Generieren und Hochladen eines aufgabenspezifischen parameters.json-Dokuments im Rahmen der Auftragsteilungsausführung und Verweisen auf dieses Blob in der Ressourcendateisammlung der Aufgabe. Dies ist erforderlich, wenn Aufgaben über unterschiedliche Parameter verfügen. Ein Beispiel hierfür ist ein 3D-Renderingszenario, bei dem der Frameindex als Parameter an die Aufgabe übergeben wird.

> [!NOTE]
> Der Handler für integrierte Parameter unterstützt nur Wörterbücher vom Typ „Zeichenfolge-zu-Zeichenfolge“. Wenn Sie komplexe JSON-Werte als Parameterwerte übergeben möchten, muss die Übergabe in Form von Zeichenfolgen durchgeführt werden, und Sie müssen sie im Aufgabenprozessor analysieren oder die `Configuration.GetTaskParameters` -Methode des Frameworks ändern.
> 
> 

## <a name="next-steps"></a>Nächste Schritte
### <a name="persist-job-and-task-output-to-azure-storage"></a>Persistente Aufträge und Aufgabenausgabe in Azure Storage
Ein weiteres nützliches Tool bei der Entwicklung von Batch-Lösungen sind [Azure Batch-Dateikonventionen][nuget_package]. Verwenden Sie diese .NET-Klassenbibliothek (derzeit in der Vorschauphase) in Ihren Batch .NET-Anwendungen, um Aufgabenausgaben leicht speichern und für Azure Storage übermitteln zu können. [Beibehalten der Ausgabe von Azure Batch-Aufträgen und -Tasks](batch-task-output.md) enthält eine umfassende Beschreibung der Bibliothek und ihrer Verwendung.


[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
