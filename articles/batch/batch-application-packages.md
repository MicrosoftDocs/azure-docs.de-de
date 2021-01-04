---
title: Bereitstellen von Anwendungspaketen für Computeknoten
description: Verwenden Sie das Feature „Anwendungspakete“ von Azure Batch zur einfachen Verwaltung mehrerer Anwendungen und Versionen für die Installation auf Batch-Serverknoten.
ms.topic: how-to
ms.date: 09/24/2020
ms.custom:
- H1Hack27Feb2017
- devx-track-csharp
- contperf-fy21q1
ms.openlocfilehash: b4c6ab99b0fa93e5fdea4256d6f232cc0fe4218e
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033730"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Bereitstellen von Anwendungen auf Computeknoten mit Batch-Anwendungspaketen

Anwendungspakete können sowohl den Code in Ihrer Azure Batch-Lösung vereinfachen als auch die Verwaltung der von Ihren Tasks ausgeführten Anwendungen erleichtern. Mit Anwendungspaketen können Sie mehrere Versionen dieser Anwendungen hochladen und verwalten, einschließlich der zugehörigen Unterstützungsdateien. Die Anwendungen können Sie dann für die Computeknoten in Ihrem Pool bereitstellen.

Die APIs zum Erstellen und Verwalten von Anwendungspaketen sind Teil der Bibliothek [Batch Management .NET](/dotnet/api/overview/azure/batch/management). Die APIs für die Installation von Anwendungspaketen auf einem Computeknoten sind Teil der Bibliothek [Batch .NET](/dotnet/api/overview/azure/batch/client). In den verfügbaren Batch-APIs für andere Sprachen finden Sie vergleichbare Features.

In diesem Artikel wird erläutert, wie Sie Anwendungspakete über das Azure-Portal hochladen und verwalten. Außerdem wird gezeigt, wie Sie diese Pakete mit der Bibliothek [Batch .NET](/dotnet/api/overview/azure/batch/client) auf den Computeknoten eines Pools installieren.

## <a name="application-package-requirements"></a>Anforderungen für Anwendungspakete

Vor der Verwendung von Anwendungspaketen müssen Sie [ein Azure Storage-Konto mit Ihrem Batch-Konto verknüpfen](#link-a-storage-account).

Es gibt Einschränkungen für die Anzahl der Anwendungen und Anwendungspakete in einem Batch-Konto sowie bezüglich der maximalen Anwendungspaketgröße. Weitere Informationen finden Sie unter [Kontingente und Einschränkungen für den Azure Batch-Dienst](batch-quota-limit.md).

> [!NOTE]
> Anwendungspakete werden von Batch-Pools, die vor dem 5. Juli 2017 erstellt wurden, nicht unterstützt (es sei denn, sie wurden nach dem 10. März 2016 mit Clouddienstkonfiguration erstellt). Das hier beschriebene Feature für Anwendungspakete ersetzt das Feature für Batch-Apps, das in früheren Versionen des Diensts verfügbar war.

## <a name="understand-applications-and-application-packages"></a>Grundlegendes zu Anwendungen und Anwendungspaketen

In Azure Batch ist eine *Anwendung* ein Satz von Binärdateien mit Versionsangabe, die automatisch auf die Computeknoten in Ihrem Pool heruntergeladen werden können. Eine Anwendung enthält mindestens ein *Anwendungspaket*, das verschiedene Anwendungsversionen darstellt.

Jedes *Anwendungspaket* ist eine ZIP-Datei mit den Anwendungsbinärdateien und allen unterstützenden Dateien. Nur das ZIP-Format wird unterstützt.

:::image type="content" source="media/batch-application-packages/app_pkg_01.png" alt-text="Abbildung: Übersicht über Anwendungen und Anwendungspakete":::

Sie können Anwendungspakete auf Pool- oder Taskebene angeben.

- **Pool-Anwendungspakete** werden auf jedem Knoten im Pool bereitgestellt. Anwendungen werden bereitgestellt, wenn ein Knoten mit einem Pool verknüpft oder neu gestartet wird oder wenn ein Reimaging durchgeführt wird.
  
    Pool-Anwendungspakete sind geeignet, wenn alle Knoten in einem Pool die Tasks eines Auftrags ausführen werden. Beim Erstellen eines Pools können Sie Anwendungspakete angeben, die bereitgestellt werden sollen. Sie können auch die Pakete eines vorhandenen Pools hinzufügen oder aktualisieren. Zum Installieren eines neuen Pakets in einem vorhandenen Pool müssen Sie dessen Knoten neu starten.

- **Task-Anwendungspakete** werden nur für einen Serverknoten ausgeführt, für den die Ausführung des Tasks geplant ist, bevor die Befehlszeile des Tasks ausgeführt wird. Wenn das angegebene Anwendungspaket und die Version auf dem Knoten bereits vorhanden sind, erfolgt keine erneute Bereitstellung, und das vorhandene Paket wird verwendet.
  
    Task-Anwendungspakete sind in Umgebungen mit gemeinsam genutzten Pools praktisch, bei denen verschiedene Aufträge in einem einzigen Pool ausgeführt werden und der Pool nach Abschluss eines Auftrags nicht gelöscht wird. Wenn es bei Ihrem Auftrag weniger Tasks als Knoten im Pool gibt, können Task-Anwendungspakete die Datenübertragung minimieren, da Ihre Anwendung nur auf den Knoten bereitgestellt wird, die Tasks ausführen.
  
    Andere Szenarien, die von Aufgabenanwendungspaketen profitieren können, sind Aufträge, die eine große Anwendung für nur einige wenige Aufgaben nutzen. Aufgabenanwendungen können z. B. für eine komplexe Vorverarbeitungsstufe oder eine Zusammenführungsaufgabe nützlich sein.

Bei Anwendungspaketen muss die Startaufgabe Ihres Pools keine lange Liste einzelner, auf dem Knoten zu installierender Ressourcendateien angeben. Sie müssen nicht manuell mehrere Versionen der Anwendungsdateien in Azure Storage oder auf Ihren Knoten verwalten. Und Sie müssen sich auch keine Gedanken über das Generieren von [SAS-URLs](../storage/common/storage-sas-overview.md) für den Zugriff auf die Dateien in Ihrem Speicherkonto machen. Batch funktioniert im Hintergrund mit Azure Storage zum speichern von Anwendungspaketen und zum Bereitstellen der Pakete auf Serverknoten.

> [!NOTE]
> Die Gesamtgröße einer Startaufgabe darf einschließlich Ressourcendateien und Umgebungsvariablen höchstens 32768 Zeichen betragen. Wenn Ihre Startaufgabe diesen Grenzwert überschreitet, ist die Verwendung von Anwendungspaketen eine weitere Option. Sie können auch eine ZIP-Datei mit Ihren Ressourcendateien erstellen, diese als Blob in Azure Storage hochladen und dann über die Befehlszeile Ihres Starttasks entzippen.

## <a name="upload-and-manage-applications"></a>Hochladen und Verwalten von Anwendungen

Sie können die Anwendungspakete in Ihrem Batch-Konto über das [Azure-Portal](https://portal.azure.com) oder die Batch Management-APIs verwalten. In den folgenden Abschnitten wird erläutert, wie Sie ein Speicherkonto verknüpfen sowie Anwendungen und Anwendungspakete im Azure-Portal hinzufügen und verwalten.

### <a name="link-a-storage-account"></a>Verknüpfen eines Storage-Kontos

Damit Sie Anwendungspakete verwenden können, müssen Sie zunächst ein [Azure Storage-Konto](accounts.md#azure-storage-accounts) mit Ihrem Batch-Konto verknüpfen. Der Batch-Dienst verwendet das zugeordnete Speicherkonto zum Speichern Ihrer Anwendungspakete. Es wird empfohlen, ein Speicherkonto speziell für die Verwendung mit Ihrem Batch-Konto zu erstellen.

Falls Sie noch kein Storage-Konto konfiguriert haben, wird im Azure-Portal eine Warnung angezeigt, wenn Sie in Ihrem Batch-Konto zum ersten Mal **Anwendungen** auswählen. Um ein Speicherkonto mit Ihrem Batch-Konto zu verknüpfen, wählen Sie im Fenster **Warnung** die Option **Speicherkonto** aus, und klicken Sie dann noch mal auf **Speicherkonto**.

Nachdem Sie die beiden Konten verknüpft haben, kann Batch die im verknüpften Storage-Konto gespeicherten Pakete automatisch für Ihre Computeknoten bereitstellen.

> [!IMPORTANT]
> In einem Azure Storage-Konto, das mit [Firewallregeln](../storage/common/storage-network-security.md) konfiguriert wurde oder für das **Hierarchischer Namespace** auf **Aktiviert** festgelegt wurde, können keine Anwendungspakete verwendet werden.

Der Batch-Dienst verwendet das Azure Storage-Konto, um Ihre Anwendungspakete als Blockblobs zu speichern. Blockblobdaten werden [in der üblichen Höhe in Rechnung gestellt](https://azure.microsoft.com/pricing/details/storage/), und die Größe jedes Pakets darf die maximale Blockblobgröße nicht überschreiten. Weitere Informationen zu Speicherkontogrenzwerten finden Sie unter [Skalierbarkeits- und Leistungsziele für Speicherkonten in Azure Storage](../storage/blobs/scalability-targets.md). Beachten Sie zur Minimierung der Kosten unbedingt Größe und Anzahl Ihrer Anwendungspakete, und entfernen Sie veraltete Pakete in regelmäßigen Abständen.

### <a name="view-current-applications"></a>Anzeigen aktueller Anwendungen

Wenn Sie die Anwendungen in Ihrem Batch-Konto anzeigen möchten, wählen Sie im linken Navigationsmenü **Anwendungen** aus.

:::image type="content" source="media/batch-application-packages/app_pkg_02.png" alt-text="Screenshot: Menüelement „Anwendungen“ im Azure-Portal":::

Durch Auswählen dieser Menüoption wird das Fenster **Anwendungen** geöffnet. In diesem Fenster werden die IDs aller Anwendungen in Ihrem Konto und die folgenden Eigenschaften angezeigt:

- **Pakete**: Die Anzahl von Versionen, die dieser Anwendung zugeordnet sind.
- **Standardversion**: Gegebenenfalls die Anwendungsversion, die installiert wird, wenn bei der Bereitstellung der Anwendung keine Version angegeben wird.
- **Updates zulassen**: Gibt an, ob Pakete aktualisiert und gelöscht werden dürfen.

Wenn Sie die [Dateistruktur](files-and-directories.md) des Anwendungspakets auf einem Serverknoten anzeigen möchten, navigieren Sie im Azure-Portal zu Ihrem Batch-Konto. Wählen Sie **Pools** aus. Wählen Sie danach den Pool aus, der den Serverknoten enthält. Wählen Sie den Serverknoten aus, auf dem das Anwendungspaket installiert wurde, und öffnen Sie den Ordner **Anwendungen**.

### <a name="view-application-details"></a>Anzeigen von Anwendungsdetails

Wenn Sie die Details einer Anwendung anzeigen möchten, wählen Sie im Fenster **Anwendungen** die entsprechende Anwendung aus. Sie können für Ihre Anwendung die folgenden Einstellungen konfigurieren.

- **Updates zulassen**: Gibt an, ob Anwendungspakete [aktualisiert oder gelöscht](#update-or-delete-an-application-package) werden können. Die Option ist standardmäßig auf **Ja** festgelegt. Wenn sie auf **Nein** festgelegt wird, können vorhandene Anwendungspakete nicht aktualisiert oder gelöscht, neue Anwendungspaketversionen aber noch hinzugefügt werden.
- **Standardversion**: Das Standardanwendungspaket, das bei der Bereitstellung der Anwendung verwendet werden soll, wenn keine Anwendung angegeben wurde.
- **Anzeigename**: Ein Anzeigename, der von Ihrer Batch-Lösung beim Anzeigen von Informationen zur Anwendung verwendet werden kann. Dieser Name kann beispielsweise in der Benutzeroberfläche eines Diensts verwendet werden, den Sie Ihren Kunden über Batch bereitstellen.

### <a name="add-a-new-application"></a>Hinzufügen einer neuen Anwendung

Zum Erstellen einer neuen Anwendung fügen Sie ein Anwendungspaket hinzu und geben eine eindeutige Anwendungs-ID an.

Wählen Sie in Ihrem Batch-Konto **Anwendungen** und anschließend **Hinzufügen** aus.

:::image type="content" source="media/batch-application-packages/app_pkg_05.png" alt-text="Screenshot: Prozess zum Erstellen einer neuen Anwendung im Azure-Portal":::

Geben Sie die folgenden Informationen ein:

- **Anwendungs-ID**: Die ID Ihrer neuen Anwendung.
- **Version**: Die Version des Anwendungspakets, das Sie hochladen.
- **Anwendungspaket**: Die ZIP-Datei, die die Binärdateien der Anwendung und jegliche unterstützenden Dateien enthält, die für die Ausführung der Anwendung erforderlich sind.

Die eingegebene **Anwendungs-ID** und **Version** müssen folgende Anforderungen erfüllen:

- Bei Windows-Knoten kann die ID jede Kombination alphanumerischer Zeichen, Bindestriche und Unterstriche enthalten. Bei Linux-Knoten sind nur alphanumerische Zeichen und Unterstriche erlaubt.
- Darf nicht mehr als 64 Zeichen umfassen.
- Sie muss innerhalb des Batch-Kontos eindeutig sein.
- Bei IDs wird die Groß-/Kleinschreibung beibehalten, aber nicht unterschieden.

Wählen Sie **Senden** aus, wenn Sie fertig sind. Nach dem Hochladen der ZIP-Datei in Ihr Azure Storage-Konto wird im Portal eine Benachrichtigung angezeigt. Dieser Vorgang kann je nach Größe der hochzuladenden Datei und der Geschwindigkeit Ihrer Netzwerkverbindung einige Zeit dauern.

### <a name="add-a-new-application-package"></a>Hinzufügen eines neuen Anwendungspakets

Wenn Sie für eine vorhandene Anwendung eine Anwendungspaketversion hinzufügen möchten, wählen Sie im Abschnitt **Anwendungen** Ihres Batch-Kontos die Anwendung und dann **Hinzufügen** aus.

Geben Sie wie bei der neuen Anwendung die **Version** für das neue Paket ein, laden Sie die ZIP-Datei in das Feld **Anwendungspaket** hoch, und wählen Sie dann **Übermitteln** aus.

### <a name="update-or-delete-an-application-package"></a>Aktualisieren oder Löschen eines Anwendungspakets

Wenn Sie ein vorhandenes Anwendungspaket aktualisieren oder löschen möchten, wählen Sie die Anwendung im Abschnitt **Anwendungen** in Ihrem Batch-Konto aus. Wählen Sie die Auslassungspunkte in der Zeile des Anwendungspakets aus, das Sie ändern möchten, und wählen Sie dann die Aktion aus, die Sie durchführen möchten.

:::image type="content" source="media/batch-application-packages/app_pkg_07.png" alt-text="Screenshot: Optionen zum Aktualisieren und Löschen von Anwendungspaketen im Azure-Portal":::

Wenn Sie **Aktualisieren** auswählen, können Sie eine neue ZIP-Datei hochladen. Dadurch wird die vorherige ZIP-Datei ersetzt, die Sie für diese Version hochgeladen haben.

Wenn Sie **Löschen** auswählen, werden Sie aufgefordert, den Löschvorgang für diese Version zu bestätigen. Nachdem Sie **OK** ausgewählt haben, wird die ZIP-Datei im Azure Storage-Konto gelöscht. Wenn Sie die Standardversion einer Anwendung löschen, wird die Einstellung **Standardversion** für diese Anwendung entfernt.

## <a name="install-applications-on-compute-nodes"></a>Installieren von Anwendungen auf Serverknoten

Nachdem Sie nun wissen, wie Anwendungspakete im Azure-Portal verwaltet werden, zeigen wir Ihnen, wie Sie diese auf Serverknoten bereitstellen und mit Batch-Tasks ausführen.

### <a name="install-pool-application-packages"></a>Installieren von Pool-Anwendungspaketen

Geben Sie mindestens einen Verweis auf ein Anwendungspaket für den Pool an, um ein Anwendungspaket auf allen Computeknoten in einem Pool zu installieren. Die Anwendungspakete, die Sie für einen Pool angeben, werden auf jedem mit dem Pool verknüpften Serverknoten und jedem Knoten, der neu gestartet oder auf dem ein Reimaging durchgeführt wird, installiert.

Legen Sie in Batch .NET Verweise vom Typ [CloudPool.ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences) fest – entweder beim Erstellen des Pools oder für einen bereits vorhandenen Pool. Die [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference)-Klasse gibt eine Anwendungs-ID und eine Version für die Installation auf den Computeknoten eines Pools an.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> Falls bei der Bereitstellung eines Anwendungspakets ein Fehler auftritt, markiert der Batch-Dienst den Knoten als [nicht verwendbar](/dotnet/api/microsoft.azure.batch.computenode.state), und es werden keine Aufgaben zur Ausführung auf diesem Knoten geplant. Starten Sie in diesem Fall den Knoten neu, um die Initiierung der Paketbereitstellung zu wiederholen. Durch Neustarten des Knotens können erneut Aufgaben auf dem Knoten geplant werden.

### <a name="install-task-application-packages"></a>Installieren von Task-Anwendungspaketen

Geben Sie wie bei einem Pool Verweise zu Anwendungspaketen für einen Task an. Wenn ein Task zur Ausführung auf einem Knoten geplant ist, wird das Paket heruntergeladen und extrahiert, bevor die Task-Befehlszeile ausgeführt wird. Wenn ein angegebenes Paket und die Version bereits auf dem Knoten installiert sind, wird das Paket wird nicht heruntergeladen, und das vorhandene Paket wird verwendet.

Um ein Task-Anwendungspaket zu installieren, konfigurieren Sie die Task-Eigenschaft [CloudTask.ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudtask.applicationpackagereferences):

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>Ausführen der installierten Anwendungen

Die Pakete, die Sie für einen Pool oder Task angegeben haben, werden heruntergeladen, und ein benanntes Verzeichnis wird innerhalb von `AZ_BATCH_ROOT_DIR` des Knotens extrahiert. Batch erstellt auch eine Umgebungsvariable mit dem Pfad zu dem benannten Verzeichnis. Ihre Task-Befehlszeilen verwenden diese Umgebungsvariable beim Verweisen auf die Anwendung auf dem Knoten.

Bei Windows-Knoten weist die Variable folgendes Format auf:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Bei Linux-Knoten weicht das Format geringfügig ab. Punkte (.), Bindestriche (-) und Nummernzeichen (#) werden in der Umgebungsvariablen zu Unterstrichen vereinfacht. Beachten Sie, dass die Groß-/Kleinschreibung der Anwendungs-ID beibehalten wird. Beispiel:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` und `version` sind Werte, die der Anwendung und Paketversion entsprechen, die Sie für die Bereitstellung angegeben haben. Wenn Sie beispielsweise festgelegt haben, dass Version 2.7 der Anwendung *Blender* auf Windows-Knoten installiert werden soll, verwenden Ihre Taskbefehlszeilen diese Umgebungsvariable, um auf die Dateien zuzugreifen:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Geben Sie die Umgebungsvariable bei Linux-Knoten im folgenden Format an. Ersetzen Sie Punkte (.), Bindestriche (-) und Nummernzeichen (#) durch Unterstriche, und behalten Sie die Groß-/Kleinschreibung der Anwendungs-ID bei:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
```

Wenn Sie ein Anwendungspaket hochladen, können Sie eine Standardversion festlegen, die für Ihre Serverknoten bereitgestellt werden soll. Wenn Sie eine Standardversion für eine Anwendung angegeben haben, können Sie beim Verweisen auf die Anwendung das Versionssuffix weglassen. Sie können die Standardanwendungsversion im Azure-Portal im Fenster **Anwendungen** angeben, wie unter [Hochladen und Verwalten von Anwendungen](#upload-and-manage-applications) gezeigt.

Wenn Sie beispielsweise „2.7“ als Standardversion für die Anwendung *Blender* angegeben haben, verweisen Ihre Aufgaben auf die folgende Umgebungsvariable, und Ihre Windows-Knoten führen dann Version 2.7 aus:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Der folgende Codeausschnitt zeigt ein Beispiel für eine Task-Befehlszeile, die die Standardversion der Anwendung *Blender* startet:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Weitere Informationen zu Umgebungseinstellungen für Computeknoten finden Sie unter [Umgebungseinstellungen für Tasks](jobs-and-tasks.md#environment-settings-for-tasks).

## <a name="update-a-pools-application-packages"></a>Aktualisieren der Anwendungspakete eines Pools

Wenn ein vorhandener Pool bereits mit einem Anwendungspaket konfiguriert wurde, können Sie ein neues Paket für den Pool angeben. Dies bedeutet Folgendes:

- Der Batch-Dienst installiert das neue angegebene Paket auf allen neuen Knoten, die dem Pool hinzugefügt werden, sowie jegliche bereits vorhandene Knoten, die neu gestartet werden bzw. für die ein Reimaging durchgeführt wird.
- Serverknoten, die sich bereits im Pool befinden, wenn Sie die Paketverweise aktualisieren, werden nicht automatisch im neuen Anwendungspaket installiert. Für diese Computeknoten muss ein Neustart oder ein Reimaging durchgeführt werden, um das neue Paket zu erhalten.
- Wenn ein neues Paket bereitgestellt wird, spiegeln die neu erstellten Umgebungsvariablen die neuen Anwendungspaketverweise wider.

In diesem Beispiel ist als einer der [CloudPool.ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences)-Verweise des vorhandenen Pools die Version 2.7 der Anwendung *Blender* konfiguriert. Wenn Sie die Knoten des Pools mit der Version 2.76b aktualisieren möchten, geben Sie einen neuen [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference)-Verweis mit der neuen Version an, und committen Sie die Änderung.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Nachdem die neue Version konfiguriert wurde, installiert der Batch-Dienst Version 2.76b auf jedem neuen Knoten, der dem Pool beitritt. Um 2.76b auf den Knoten zu installieren, die sich bereits im Pool befinden , muss für die Knoten ein Neustart oder Reimaging durchgeführt werden. Beachten Sie, dass neu gestartete Knoten die Dateien aus vorherigen Paketbereitstellungen beibehalten.

## <a name="list-the-applications-in-a-batch-account"></a>Auflisten der Anwendungen in einem Batch-Konto

Sie können die Anwendungen und ihre Pakete mit der Methode [ApplicationOperations.ListApplicationSummaries](/dotnet/api/microsoft.azure.batch.applicationoperations.listapplicationsummaries) in einem Batch-Konto auflisten.

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

- Die [Batch-REST-API](/rest/api/batchservice) unterstützt auch die Verwendung von Anwendungspaketen. Informationen zum Angeben von zu installierenden Paketen finden Sie unter dem Element [applicationPackageReferences](/rest/api/batchservice/pool/add#applicationpackagereference). Informationen zum Abrufen von Anwendungsinformationen finden Sie unter [Anwendungen](/rest/api/batchservice/application).
- Informieren Sie sich über das programmgesteuerte [Verwalten von Azure Batch-Konten und -Kontingenten mit Batch Management .NET](batch-management-dotnet.md). Die [Batch Management .NET](/dotnet/api/overview/azure/batch/management)-Bibliothek ermöglicht die Verwendung von Features zum Erstellen und Löschen von Konten für Ihre Batch-Anwendung oder Ihren Dienst.
