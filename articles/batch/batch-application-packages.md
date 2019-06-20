---
title: Installieren von Anwendungspaketen auf Computeknoten – Azure Batch | Microsoft-Dokumentation
description: Verwenden Sie das Feature „Anwendungspakete“ von Azure Batch zur einfachen Verwaltung mehrerer Anwendungen und Versionen für die Installation auf Batch-Serverknoten.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/26/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c17835a4155e97395e8ae1b8e9ba6d2a42433f71
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66298746"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Bereitstellen von Anwendungen auf Computeknoten mit Batch-Anwendungspaketen

Das Feature „Anwendungspakete“ von Azure Batch ermöglicht eine einfache Verwaltung und Bereitstellung von Task-Anwendungen für die Serverknoten in Ihrem Pool. Mit Anwendungspaketen können Sie mehrere Versionen der von Ihren Tasks ausgeführten Anwendungen hochladen und verwalten, einschließlich der dazugehörigen Unterstützungsdateien. Die Anwendungen können Sie dann für die Computeknoten in Ihrem Pool bereitstellen.

In diesem Artikel erfahren Sie, wie Sie Anwendungspakete über das Azure-Portal hochladen und verwalten. Außerdem erfahren Sie, wie Sie diese mit der [Batch .NET][api_net]-Bibliothek auf den Computeknoten eines Pools installieren.

> [!NOTE]
> Anwendungspakete werden für alle Batch-Pools unterstützt, die nach dem 5. Juli 2017 erstellt wurden. Für Batch-Pools, die zwischen dem 10. März 2016 und dem 5. Juli 2017 erstellt wurden, werden sie nur unterstützt, wenn der Pool mit einer Clouddienstkonfiguration erstellt wurde. Batch-Pools, die vor dem 10. März 2016 erstellt wurden, unterstützen keine Anwendungspakete.
>
> Die APIs zum Erstellen und Verwalten von Anwendungspaketen sind Teil der [Batch Management .NET][api_net_mgmt]-Bibliothek. Die APIs für die Installation von Anwendungspaketen auf einem Computeknoten sind Teil der Bibliothek [Batch .NET][api_net]. In den verfügbaren Batch-APIs für andere Sprachen finden Sie vergleichbare Features. 
>
> Das hier beschriebene Feature für Anwendungspakete ersetzt das Feature für Batch-Apps, das in früheren Versionen des Diensts verfügbar war.

## <a name="application-package-requirements"></a>Anforderungen für Anwendungspakete
Vor der Verwendung von Anwendungspaketen müssen Sie [ein Azure Storage-Konto mit Ihrem Batch-Konto verknüpfen](#link-a-storage-account).

## <a name="about-applications-and-application-packages"></a>Informationen zu Anwendungen und Anwendungspaketen
In Azure Batch ist eine *Anwendung* ein Satz von Binärdateien mit Versionsangabe, die automatisch auf die Computeknoten in Ihrem Pool heruntergeladen werden können. Ein *Anwendungspaket* ist ein *bestimmter Satz* dieser Binärdateien und stellt eine bestimmte *Version* der Anwendung dar.

![Übersichtsdiagramm zu Anwendungen und Anwendungspaketen][1]

### <a name="applications"></a>ANWENDUNGEN
Eine Anwendung in Batch enthält mindestens ein Anwendungspaket und gibt Konfigurationsoptionen für die Anwendung an. So kann eine Anwendung etwa die Standardversion des Anwendungspakets festlegen, die auf Computeknoten installiert werden soll, und sie gibt an, ob die dazugehörigen Pakete aktualisiert oder gelöscht werden können.

### <a name="application-packages"></a>Anwendungspakete
Ein Anwendungspaket ist eine ZIP-Datei mit Binärdateien der Anwendung und unterstützenden Dateien, die für Ihre Aufgaben zur Ausführung der Anwendung erforderlich sind. Jedes Anwendungspaket stellt eine bestimmte Version der Anwendung dar.

Sie können Anwendungspakete auf Pool- und Aufgabenebene angeben. Wenn Sie im einen Pool oder einen Task erstellen, können Sie ein oder mehrere Anwendungspakete und (optional) eine Version angeben.

* **Pool-Anwendungspakete** werden auf *jedem* Knoten im Pool bereitgestellt. Anwendungen werden bereitgestellt, wenn ein Knoten mit einem Pool verknüpft oder neu gestartet wird oder wenn ein Reimaging durchgeführt wird.
  
    Pool-Anwendungspakete sind geeignet, wenn alle Knoten in einem Pool die Tasks eines Auftrags durchführen. Sie können ein oder mehrere Anwendungspakete angeben, wenn Sie einen Pool erstellen, und Sie können die Pakete eines vorhandenen Pools hinzufügen oder aktualisieren. Wenn Sie die Anwendungspakete eines vorhandenen Pools aktualisieren, müssen sie dessen Knoten neu starten, um das neue Paket zu installieren.
* **Task-Anwendungspakete** werden nur für einen Serverknoten ausgeführt, für den die Ausführung des Tasks geplant ist, bevor die Befehlszeile des Tasks ausgeführt wird. Wenn das angegebene Anwendungspaket und die Version auf dem Knoten bereits vorhanden sind, erfolgt keine erneute Bereitstellung, und das vorhandene Paket wird verwendet.
  
    Task-Anwendungspakete sind in Umgebungen mit gemeinsam genutzten Pools praktisch, bei denen verschiedene Aufträge in einem Pool ausgeführt werden und der Pool nach Abschluss des Auftrags nicht gelöscht wird. Wenn Ihr Auftrag über weniger Tasks als Knoten im Pool verfügt, können Task-Anwendungspakete die Datenübertragung minimieren, da Ihre Anwendung nur auf den Knoten bereitgestellt wird, die Tasks ausführen.
  
    Andere Szenarien, die von Aufgabenanwendungspaketen profitieren können, sind Aufträge, die eine große Anwendung für nur einige wenige Aufgaben nutzen. Beispielsweise kann die Verwendung von Aufgabenanwendungspaketen bei einer Vorverarbeitungs- oder Zusammenführungsaufgabe von Vorteil sein, bei der die Vorverarbeitungs- oder Zusammenführungsanwendung umfangreich ist.

> [!IMPORTANT]
> Es gibt Einschränkungen für die Anzahl der Anwendungen und Anwendungspakete in einem Batch-Konto sowie bezüglich der maximalen Anwendungspaketgröße. Ausführlichere Informationen zu diesen Einschränkungen finden Sie unter [Kontingente und Limits für den Azure Batch-Dienst](batch-quota-limit.md) .
> 
> 

### <a name="benefits-of-application-packages"></a>Vorteile von Anwendungspaketen
Anwendungspakete können sowohl den Code in Ihrer Batch-Lösung vereinfachen als auch den Verwaltungsaufwand für die Anwendungen verringern, die Ihre Tasks ausführen.

Bei Anwendungspaketen muss die Startaufgabe Ihres Pools keine lange Liste einzelner, auf dem Knoten zu installierender Ressourcendateien angeben. Sie müssen nicht manuell mehrere Versionen der Anwendungsdateien in Azure Storage oder auf Ihren Knoten verwalten. Und Sie müssen sich auch keine Gedanken über das Generieren von [SAS-URLs](../storage/common/storage-dotnet-shared-access-signature-part-1.md) für den Zugriff auf die Dateien in Ihrem Speicherkonto machen. Batch funktioniert im Hintergrund mit Azure Storage zum speichern von Anwendungspaketen und zum Bereitstellen der Pakete auf Serverknoten.

> [!NOTE] 
> Die Gesamtgröße einer Startaufgabe darf einschließlich Ressourcendateien und Umgebungsvariablen höchstens 32768 Zeichen betragen. Wenn Ihre Startaufgabe diesen Grenzwert überschreitet, stellt die Verwendung von Anwendungspaketen eine weitere Option dar. Sie können auch ein ZIP-Archiv mit Ihren Ressourcendateien erstellen, diesen als Blob in Azure Storage hochladen und sie dann über die Befehlszeile Ihrer Startaufgabe entzippen. 
>
>

## <a name="upload-and-manage-applications"></a>Hochladen und Verwalten von Anwendungen
Sie können das [Azure-Portal][portal] oder die Batch Management-APIs verwenden, um die Anwendungspakete in Ihrem Batch-Konto zu verwalten. In den folgenden Abschnitten wird zunächst gezeigt, wie ein Speicherkonto verknüpft wird. Anschließend wird besprochen, wie Sie Anwendungen und Pakete hinzufügen und mit dem Portal verwalten.

### <a name="link-a-storage-account"></a>Verknüpfen eines Storage-Kontos
Damit Sie Anwendungspakete verwenden können, müssen Sie zuerst ein [Azure Storage-Konto](batch-api-basics.md#azure-storage-account) mit Ihrem Batch-Konto verknüpfen. Falls Sie noch kein Storage-Konto konfiguriert haben, wird im Azure-Portal eine Warnung angezeigt, wenn Sie in Ihrem Batch-Konto zum ersten Mal auf **Anwendungen** klicken.



![Warnung im Azure-Portal mit dem Hinweis, dass kein Speicherkonto konfiguriert ist][9]

Der Batch-Dienst verwendet das zugeordnete Speicherkonto zum Speichern Ihrer Anwendungspakete. Nachdem Sie die beiden Konten verknüpft haben, kann Batch die im verknüpften Storage-Konto gespeicherten Pakete automatisch für Ihre Computeknoten bereitstellen. Um ein Speicherkonto mit Ihrem Batch-Konto zu verknüpfen, klicken Sie im Fenster **Warnung** auf **Speicherkonto**, und klicken Sie dann erneut auf **Speicherkonto**.

![Blatt zum Auswählen des Speicherkontos im Azure-Portal][10]

Es wird empfohlen, ein Speicherkonto *speziell* für die Verwendung mit Ihrem Batch-Konto zu erstellen und hier auszuwählen. Nachdem Sie ein Speicherkonto erstellt haben, können Sie es im Fenster **Speicherkonto** mit Ihrem Batch-Konto verknüpfen.

> [!NOTE] 
> In einem Azure Storage-Konto, das mit [Firewallregeln](../storage/common/storage-network-security.md) konfiguriert ist, können derzeit keine Anwendungspakete verwendet werden.
> 

Der Batch-Dienst verwendet das Azure Storage-Konto, um Ihre Anwendungspakete als Blockblobs zu speichern. Blockblobdaten werden [in der üblichen Höhe in Rechnung gestellt][storage_pricing], und die Größe jedes Pakets darf die [maximale Blockblobgröße](../storage/common/storage-scalability-targets.md#azure-blob-storage-scale-targets) nicht überschreiten. Beachten Sie unbedingt Größe und Anzahl der Anwendungspakete, und entfernen Sie in regelmäßigen Abständen veraltete Pakete, um Kosten zu minimieren.
> 
> 

### <a name="view-current-applications"></a>Anzeigen aktueller Anwendungen
Klicken Sie zum Anzeigen der Anwendungen in Ihrem Batch-Konto im linken Menü auf das Menüelement **Anwendungen**, während Ihr **Batch-Konto** angezeigt wird.

![Kachel „Anwendungen“][2]

Durch Auswählen dieser Menüoption wird das Fenster **Anwendungen** geöffnet:

![Auflisten von Anwendungen][3]

In diesem Fenster werden die IDs aller Anwendungen in Ihrem Konto und die folgenden Eigenschaften angezeigt:

* **Pakete**: Die Anzahl von Versionen, die dieser Anwendung zugeordnet sind.
* **Standardversion**: Die Anwendungsversion, die installiert wird, wenn Sie beim Festlegen der Anwendung für einen Pool keine Version angeben. Diese Einstellung ist optional.
* **Updates zulassen**: Gibt an, ob Pakete aktualisiert, gelöscht und hinzugefügt werden dürfen. Ist diese Option auf **Nein**festgelegt, sind Paketaktualisierungen und -löschungen für die Anwendung deaktiviert. In diesem Fall können nur neue Anwendungspaketversionen hinzugefügt werden. Die Option ist standardmäßig auf **Ja**festgelegt.

Wenn Sie die Dateistruktur des Anwendungspakets auf Ihrem Serverknoten anzeigen möchten, navigieren Sie im Portal zu Ihrem Batch-Konto. Navigieren Sie von Ihrem Batch-Konto aus zu **Pools**. Wählen Sie den Pool aus, der die für Sie interessanten Serverknoten enthält.

![Knoten im Pool][13]

Nachdem Sie Ihren Pool ausgewählt haben, navigieren Sie zu dem Serverknoten, auf dem das Anwendungspaket installiert ist. Von dort aus befinden sich die Details des Anwendungspakets im Ordner **applications**. Zusätzliche Ordner auf dem Serverknoten enthalten andere Dateien, z. B. Startaufgaben, Ausgabedateien, Fehlerausgabe usw.

![Dateien auf Knoten][14]

### <a name="view-application-details"></a>Anzeigen von Anwendungsdetails
Wenn Sie die Details einer Anwendung anzeigen möchten, wählen Sie im Fenster**Anwendungen** die entsprechende Anwendung aus.

![Anwendungsdetails][4]

In den Anwendungsdetails können Sie die folgenden Einstellungen für Ihre Anwendung konfigurieren.

* **Updates zulassen**: Gibt an, ob die Anwendungspakete aktualisiert oder gelöscht werden können. Weitere Informationen finden Sie weiter unten in diesem Artikel unter „Aktualisieren oder Löschen eines Anwendungspakets“.
* **Standardversion**: Gibt ein Standardanwendungspaket für die Bereitstellung auf Computeknoten an.
* **Anzeigename**: Geben Sie einen Anzeigenamen an, den Ihre Batch-Lösung zur Anzeige von anwendungsbezogenen Informationen verwenden kann (beispielsweise auf der Benutzeroberfläche eines Diensts, den Sie für Ihre Kunden über Batch bereitstellen).

### <a name="add-a-new-application"></a>Hinzufügen einer neuen Anwendung
Fügen Sie zum Erstellen einer neuen Anwendung ein Anwendungspaket mit einer neuen, eindeutigen ID hinzu. Mit dem ersten Anwendungspaket, das Sie mit der neuen Anwendungs-ID hinzufügen, wird auch die neue Anwendung erstellt.

Klicken Sie auf **Anwendungen** > **Hinzufügen**.

![Blatt „Neue Anwendung“ im Azure-Portal][5]

Das Fenster **Neue Anwendung** enthält die folgenden Felder zur Angabe der Einstellungen Ihrer neuen Anwendung und des neuen Anwendungspakets.

**Anwendungs-ID**

Dieses Feld gibt die ID Ihrer neuen Anwendung an und unterliegt den standardmäßigen Gültigkeitsprüfungsregeln für Azure Batch-IDs. Die Regeln für die Bereitstellung einer Anwendungs-ID lauten wie folgt:

* Bei Windows-Knoten kann die ID jede Kombination alphanumerischer Zeichen, Bindestriche und Unterstriche enthalten. Bei Linux-Knoten sind nur alphanumerische Zeichen und Unterstriche erlaubt.
* Sie darf nicht mehr als 64 Zeichen enthalten.
* Sie muss innerhalb des Batch-Kontos eindeutig sein.
* Groß-/Kleinschreibung wird beibehalten und nicht unterschieden.

**Version**

Dieses Feld gibt die Version des Anwendungspakets an, das Sie hochladen. Für die Versionszeichenfolge gelten die folgenden Gültigkeitsprüfungsregeln:

* Bei Windows-Knoten kann die Versionszeichenfolge jede Kombination alphanumerischer Zeichen, Bindestriche, Unterstriche und Punkte enthalten. Bei Linux-Knoten darf die Versionszeichenfolge nur alphanumerische Zeichen und Unterstriche enthalten.
* Sie darf nicht mehr als 64 Zeichen enthalten.
* Sie muss innerhalb der Anwendung eindeutig sein.
* Groß-/Kleinschreibung wird beibehalten und nicht unterschieden.

**Anwendungspaket**

Dieses Feld gibt die ZIP-Datei an, die die Binärdateien der Anwendung und jegliche unterstützenden Dateien enthält, die für die Ausführung der Anwendung erforderlich sind. Klicken Sie auf das Feld **Datei auswählen** oder auf das Symbol zum Durchsuchen, um zum gewünschten Speicherort zu navigieren und eine ZIP-Datei mit den Anwendungsdateien auszuwählen.

Klicken Sie nach dem Auswählen einer Datei auf **OK**, um mit dem Hochladen in Azure Storage zu beginnen. Wenn der Uploadvorgang abgeschlossen ist, wird im Portal eine Benachrichtigung angezeigt. Dieser Vorgang kann je nach Größe der hochzuladenden Datei und der Geschwindigkeit Ihrer Netzwerkverbindung einige Zeit dauern.

> [!WARNING]
> Schließen Sie das Fenster **Neue Anwendung** erst nach Abschluss des Uploadvorgangs. Andernfalls wird der Uploadvorgang beendet.
> 
> 

### <a name="add-a-new-application-package"></a>Hinzufügen eines neuen Anwendungspakets
Wenn Sie für eine vorhandene Anwendung eine Anwendungspaketversion hinzufügen möchten, wählen Sie im Fenster **Anwendungen** eine Anwendung aus, und klicken Sie dann auf **Pakete** > **Hinzufügen**.

![Blatt zum Hinzufügen von Anwendungspaketen im Azure-Portal][8]

Wie Sie sehen können, entsprechen die Felder den Feldern des Fensters **Neue Anwendung**, wobei jedoch das Feld **Anwendungs-ID** deaktiviert ist. Geben Sie wie bei der neuen Anwendung die **Version** für das neue Paket ein, navigieren Sie zu Ihrer ZIP-Datei mit dem **Anwendungspaket**, und klicken Sie anschließend zum Hochladen des Pakets auf **OK**.

### <a name="update-or-delete-an-application-package"></a>Aktualisieren oder Löschen eines Anwendungspakets
Öffnen Sie zum Aktualisieren oder Löschen eines vorhandenen Anwendungspakets die Details für die Anwendung, klicken Sie auf **Pakete**, klicken Sie in der Zeile des zu ändernden Anwendungspakets auf die **Auslassungspunkte**, und wählen Sie die Aktion aus, die Sie ausführen möchten.

![Aktualisieren oder Löschen des Pakets im Azure-Portal][7]

**Aktualisieren**

Wenn Sie auf **Aktualisieren** klicken, wird das Fenster **Paket aktualisieren** angezeigt. Dieses Fenster ist mit dem Fenster **Neues Anwendungspaket** vergleichbar. Hier ist jedoch nur das Paketauswahlfeld aktiviert, sodass Sie eine neue ZIP-Datei zum Hochladen angeben können.

![Blatt „Paket aktualisieren“ im Azure-Portal][11]

**Löschen**

Wenn Sie auf **Löschen**klicken, werden Sie aufgefordert, das Löschen der Paketversion zu bestätigen, und Batch löscht das Paket aus Azure Storage. Wenn Sie die Standardversion einer Anwendung löschen, wird die Einstellung **Standardversion** für die Anwendung entfernt.

![Löschen der Anwendung][12]

## <a name="install-applications-on-compute-nodes"></a>Installieren von Anwendungen auf Serverknoten
Da Sie nun erfahren haben, wie Anwendungspakete mit dem Azure-Portal verwaltet werden, zeigen wir Ihnen, wie Sie diese auf Computeknoten bereitstellen und mit Batch-Aufgaben ausführen.

### <a name="install-pool-application-packages"></a>Installieren von Pool-Anwendungspaketen
Geben Sie mindestens einen *Verweis* auf ein Anwendungspaket für den Pool an, um ein Anwendungspaket auf allen Computeknoten in einem Pool zu installieren. Die Anwendungspakete, die Sie für einen Pool angeben, werden auf jedem Computeknoten installiert, wenn der jeweilige Knoten dem Pool beitritt sowie wenn der Knoten neu erstellt oder ein Re-Imaging durchgeführt wird.

Legen Sie in Batch .NET Verweise vom Typ [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref] fest – entweder beim Erstellen des Pools oder für einen bereits vorhandenen Pool. Die [ApplicationPackageReference][net_pkgref]-Klasse gibt eine Anwendungs-ID und eine Version für die Installation auf den Computeknoten eines Pools an.

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
> Falls bei der Bereitstellung eines Anwendungspakets aus irgendeinem Grund ein Fehler auftritt, markiert der Batch-Dienst den Knoten als [Nicht verwendbar][net_nodestate], sodass keine Aufgaben zur Ausführung auf diesem Knoten geplant werden. In diesem Fall empfiehlt es sich, einen **Neustart** des Knotens durchzuführen, um die Initiierung der Paketbereitstellung zu wiederholen. Durch Neustarten des Knotens können erneut Aufgaben auf dem Knoten geplant werden.
> 
> 

### <a name="install-task-application-packages"></a>Installieren von Task-Anwendungspaketen
Geben Sie wie bei einem Pool *Verweise* zu Anwendungspaketen für einen Task an. Wenn ein Task zur Ausführung auf einem Knoten geplant ist, wird das Paket heruntergeladen und extrahiert, bevor die Task-Befehlszeile ausgeführt wird. Wenn ein angegebenes Paket und die Version bereits auf dem Knoten installiert sind, wird das Paket wird nicht heruntergeladen, und das vorhandene Paket wird verwendet.

Um ein Task-Anwendungspaket zu installieren, konfigurieren Sie die Task-Eigenschaft [CloudTask][net_cloudtask].[ApplicationPackageReferences][net_cloudtask_pkgref]:

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
> Im Artikel [Übersicht über Azure Batch-Features](batch-api-basics.md) finden Sie unter [Umgebungseinstellungen für Tasks](batch-api-basics.md#environment-settings-for-tasks) weitere Informationen zu Umgebungseinstellungen für Computeknoten.
> 
> 

## <a name="update-a-pools-application-packages"></a>Aktualisieren der Anwendungspakete eines Pools
Wenn ein vorhandener Pool bereits mit einem Anwendungspaket konfiguriert wurde, können Sie ein neues Paket für den Pool angeben. Wenn Sie einen neuen Paketverweis für einen Pool angeben, gilt Folgendes:

* Der Batch-Dienst installiert das neue angegebene Paket auf allen neuen Knoten, die dem Pool hinzugefügt werden, sowie jegliche bereits vorhandene Knoten, die neu gestartet werden bzw. für die ein Reimaging durchgeführt wird.
* Serverknoten, die sich bereits im Pool befinden, wenn Sie die Paketverweise aktualisieren, werden nicht automatisch im neuen Anwendungspaket installiert. Für diese Computeknoten muss ein Neustart oder ein Reimaging durchgeführt werden, um das neue Paket zu erhalten.
* Wenn ein neues Paket bereitgestellt wird, spiegeln die neu erstellten Umgebungsvariablen die neuen Anwendungspaketverweise wider.

In diesem Beispiel ist als einer der [CloudPool][net_cloudpool].[ApplicationPackageReferences][net_cloudpool_pkgref]-Verweise des vorhandenen Pools die Version 2.7 der Anwendung *Blender* konfiguriert. Wenn Sie die Knoten des Pools mit der Version 2.76b aktualisieren möchten, geben Sie einen neuen [ApplicationPackageReference][net_pkgref]-Verweis mit der neuen Version an, und committen Sie die Änderung.

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

Nachdem die neue Version konfiguriert wurde, installiert der Batch-Dienst Version 2.76b auf jedem *neuen* Knoten, der dem Pool beitritt. Um 2.76b auf den Knoten zu installieren, die sich *bereits im Pool befinden* , muss für die Knoten ein Neustart oder Reimaging durchgeführt werden. Beachten Sie, dass neu gestartete Knoten die Dateien aus vorherigen Paketbereitstellungen beibehalten.

## <a name="list-the-applications-in-a-batch-account"></a>Auflisten der Anwendungen in einem Batch-Konto
Sie können die Anwendungen und ihre Pakete mit der Methode [ApplicationOperations][net_appops].[ListApplicationSummaries][net_appops_listappsummaries] in einem Batch-Konto auflisten.

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

## <a name="wrap-up"></a>Zusammenfassung
Mit Anwendungspaketen können Sie Ihre Kunden dabei unterstützen, die passende Anwendungen für ihre Aufträge auszuwählen und genau anzugeben, welche Version beim Verarbeiten von Aufträgen mit Ihrem Batch-fähigen Dienst verwendet werden soll. Sie könnten Ihren Kunden auch die Möglichkeit bieten, ihre eigenen Anwendungen in Ihren Dienst hochzuladen und dort zu verfolgen.

## <a name="next-steps"></a>Nächste Schritte
* Die [Batch-REST-API][api_rest] unterstützt auch die Verwendung von Anwendungspaketen. Unter [Hinzufügen eines Pools zu einem Konto][rest_add_pool] finden Sie unter dem Element [applicationPackageReferences][rest_add_pool_with_packages] beispielsweise Informationen zum Angeben von zu installierenden Paketen mithilfe der REST-API. Unter [Anwendungen][rest_applications] finden Sie ausführliche Informationen zum Abrufen von Anwendungsinformationen mit der Batch-REST-API.
* Informieren Sie sich über das programmgesteuerte [Verwalten von Azure Batch-Konten und -Kontingenten mit Batch Management .NET](batch-management-dotnet.md). Die [Batch Management .NET][api_net_mgmt]-Bibliothek ermöglicht die Verwendung von Features zum Erstellen und Löschen von Konten für Ihre Batch-Anwendung oder Ihren Dienst.

[api_net]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/client?view=azure-dotnet
[api_net_mgmt]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/management?view=azure-dotnet
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Übersichtsdiagramm für Anwendungspakete"
[2]: ./media/batch-application-packages/app_pkg_02.png "Kachel „Anwendungen“ im Azure-Portal"
[3]: ./media/batch-application-packages/app_pkg_03.png "Blatt „Anwendungen“ im Azure-Portal"
[4]: ./media/batch-application-packages/app_pkg_04.png "Blatt „Anwendungsdetails“ im Azure-Portal"
[5]: ./media/batch-application-packages/app_pkg_05.png "Blatt „Neue Anwendung“ im Azure-Portal"
[7]: ./media/batch-application-packages/app_pkg_07.png "Dropdownliste zum Aktualisieren oder Löschen von Paketen im Azure-Portal"
[8]: ./media/batch-application-packages/app_pkg_08.png "Blatt „Neues Anwendungspaket“ im Azure-Portal"
[9]: ./media/batch-application-packages/app_pkg_09.png "Warnung mit dem Hinweis, dass kein verknüpftes Storage-Konto vorhanden ist"
[10]: ./media/batch-application-packages/app_pkg_10.png "Blatt „Speicherkonto auswählen“ im Azure-Portal"
[11]: ./media/batch-application-packages/app_pkg_11.png "Blatt „Paket aktualisieren“ im Azure-Portal"
[12]: ./media/batch-application-packages/app_pkg_12.png "Dialogfeld zum Bestätigen der Paketlöschung im Azure-Portal"
[13]: ./media/batch-application-packages/package-file-structure.png "Serverknoteninformationen im Azure-Portal"
[14]: ./media/batch-application-packages/package-file-structure-node.png "Dateien auf dem Serverknoten, die im Azure-Portal angezeigt werden"
