---
title: Problembehandlung bei der Azure-Dateisynchronisierung | Microsoft-Dokumentation
description: Beheben von häufigen Problemen bei der Azure-Dateisynchronisierung
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 01/31/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 26055727e308f8c05aece31746434d7e9a0a5abd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65555943"
---
# <a name="troubleshoot-azure-file-sync"></a>Problembehandlung für Azure-Dateisynchronisierung
Mit der Azure-Dateisynchronisierung können Sie die Dateifreigaben Ihrer Organisation in Azure Files zentralisieren, ohne auf die Flexibilität, Leistung und Kompatibilität eines lokalen Dateiservers verzichten zu müssen. Mit der Azure-Dateisynchronisierung werden Ihre Windows Server-Computer zu einem schnellen Cache für Ihre Azure-Dateifreigabe. Sie können ein beliebiges Protokoll verwenden, das unter Windows Server verfügbar ist, um lokal auf Ihre Daten zuzugreifen, z.B. SMB, NFS und FTPS. Sie können weltweit so viele Caches wie nötig nutzen.

Dieser Artikel enthält Informationen zur Behebung von Fehlern und Lösung von Problemen, die möglicherweise bei ihrer Bereitstellung der Azure-Dateisynchronisierung auftreten. Wir beschreiben außerdem, wie Sie wichtige Protokolle aus dem System erfassen, wenn eine detailliertere Untersuchung des Problems erforderlich ist. Wenn Sie hier keine Antwort auf Ihre Frage finden, können Sie sich über die folgenden Kanäle an uns wenden (Eskalationsreihenfolge):

1. [Azure Storage-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
2. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)
3. Microsoft-Support. Wählen Sie zum Erstellen einer neuen Supportanfrage im Azure-Portal auf der Registerkarte **Hilfe** die Schaltfläche **Hilfe und Support** und anschließend die Option **Neue Supportanfrage**.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Es besteht ein Problem mit der Azure-Dateisynchronisierung auf dem Server (Synchronisierung, Cloudtiering usw.). Soll der Serverendpunkt entfernt und neu erstellt werden?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Agent-Installation und Serverregistrierung
<a id="agent-installation-failures"></a>**Beheben von Fehlern bei der Agent-Installation**  
Wenn bei der Installation des Azure-Dateisynchronisierungs-Agents ein Fehler auftritt, führen Sie den folgenden Befehl an einer Eingabeaufforderung mit erhöhten Rechten aus, um die Protokollierung während der Installation des Agents zu aktivieren:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Überprüfen Sie „installer.log“, um die Ursache des Installationsfehlers zu bestimmen.

<a id="agent-installation-on-DC"></a>**Bei der Agent-Installation tritt ein Fehler auf dem Active Directory-Domänencontroller auf.**  
Wenn Sie versuchen, den Synchronisierungs-Agent auf einem Active Directory-Domänencontroller zu installieren, bei dem sich der Besitzer der PDC-Rolle unter Windows Server 2008 R2 oder einer älteren Version des Betriebssystems befindet, tritt bei der Installation des Synchronisierungs-Agents möglicherweise ein Fehler auf.

Um dieses Problem zu beheben, übertragen Sie die PDC-Rolle auf einen anderen Domänencontroller unter Windows Server 2012 R2 oder höher und installieren dann den Synchronisierungsdienst.

<a id="server-registration-missing"></a>**Der Server ist im Azure-Portal nicht unter „Registrierte Server“ aufgeführt.**  
Wenn ein Server für einen Speichersynchronisierungsdienst nicht unter **Registrierte Server** aufgeführt wird:
1. Melden Sie sich bei dem Server an, den Sie registrieren möchten.
2. Öffnen Sie den Datei-Explorer, und wechseln Sie dann zum Installationsverzeichnis des Storage-Synchronisierungs-Agents (der Standardspeicherort ist C:\Programme\Azure\StorageSyncAgent). 
3. Führen Sie „ServerRegistration.exe“ aus, und schließen Sie den Assistenten ab, um den Server bei einem Storage-Synchronisierungsdienst zu registrieren.

<a id="server-already-registered"></a>**Bei der Serverregistrierung wird während der Installation des Azure-Dateisynchronisierungs-Agents die folgende Meldung angezeigt: „Dieser Server ist bereits registriert.“** 

![Screenshot des Dialogfelds für die Serverregistrierung mit der Fehlermeldung „Dieser Server wurde bereits registriert“](media/storage-sync-files-troubleshoot/server-registration-1.png)

Diese Meldung wird angezeigt, wenn der Server zuvor bei einem Speichersynchronisierungsdienst registriert wurde. Wenn Sie die Registrierung des Servers beim aktuellen Speichersynchronisierungsdienst aufheben und ihn bei einem neuen Speichersynchronisierungsdienst registrieren möchten, führen Sie die unter [Aufheben der Registrierung eines Servers bei der Azure-Dateisynchronisierung](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service) beschriebenen Schritte aus.

Wenn der Server nicht im Speichersynchronisierungsdienst unter **Registrierte Server** aufgeführt ist, können Sie auf dem Server, dessen Registrierung Sie aufheben möchten, die folgenden PowerShell-Befehle ausführen:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Wenn der Server Bestandteil eines Clusters ist, können Sie den optionalen Parameter *Reset-StorageSyncServer -CleanClusterRegistration* verwenden, um die Clusterregistrierung ebenfalls aufzuheben.

<a id="web-site-not-trusted"></a>**Beim Registrieren eines Servers erhalte ich zahlreiche Antworten, dass eine Website nicht vertrauenswürdig sei. Warum?**  
Dieses Problem tritt auf, wenn die Richtlinie **Erhöhte Internet Explorer-Sicherheit** bei der Serverregistrierung aktiviert war. Weitere Informationen zum ordnungsgemäßen Deaktivieren der Richtlinie **Erhöhte Internet Explorer-Sicherheit** finden Sie unter [Vorbereiten von Windows Server für die Verwendung mit der Azure-Dateisynchronisierung](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) und [Bereitstellen der Azure-Dateisynchronisierung (Vorschau)](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Verwaltung von Synchronisierungsgruppen
<a id="cloud-endpoint-using-share"></a>**Fehler beim Erstellen des Cloudendpunkts: „The specified Azure FileShare is already in use by a different CloudEndpoint“ (Die angegebene Azure-Dateifreigabe wird bereits von einem anderen Cloudendpunkt verwendet)**  
Dieses Problem tritt auf, wenn die Azure-Dateifreigabe bereits von einem anderen Cloudendpunkt verwendet wird. 

Wenn diese Nachricht erscheint und die Azure-Dateifreigabe derzeit von keinem Cloudendpunkt verwendet wird, sollten Sie die folgenden Schritte ausführen, um die Metadaten der Azure-Dateisynchronisierung auf der Azure-Dateifreigabe zu löschen:

> [!Warning]  
> Wenn die Metadaten auf einer Azure-Dateifreigabe gelöscht werden, die derzeit von einem Cloudendpunkt verwendet wird, treten bei Vorgängen der Azure-Dateisynchronisierung Fehler auf. 

1. Navigieren Sie im Azure-Portal zu Ihrer Azure-Dateifreigabe.  
2. Klicken Sie mit der rechten Maustaste auf die Azure-Dateifreigabe, und wählen Sie dann **Metadaten bearbeiten** aus.
3. Klicken Sie mit der rechten Maustaste auf **SyncService**, und wählen Sie dann **Löschen** aus.

<a id="cloud-endpoint-authfailed"></a>**Fehler beim Erstellen des Cloudendpunkts: „AuthorizationFailed“**  
Dieses Problem tritt auf, wenn Ihr Benutzerkonto nicht über die erforderlichen Rechte verfügt, um einen Cloudendpunkt zu erstellen. 

Für die Erstellung eines Cloudendpunkts muss Ihr Benutzerkonto über die folgenden Microsoft-Autorisierungsberechtigungen verfügen:  
* Lesen: Abrufen der Rollendefinition
* Schreiben: Erstellen oder Aktualisieren von benutzerdefinierten Rollendefinition
* Lesen: Beziehe Rollenzuweisung
* Schreiben: Erstellen von Rollenzuweisungen

Die folgenden integrierten Rollen verfügen über die erforderlichen Microsoft-Autorisierungsberechtigungen:  
* Owner (Besitzer)
* Benutzerzugriffsadministrator

So bestimmen Sie, ob Ihr Benutzerkonto über die erforderlichen Berechtigungen verfügt:  
1. Wählen Sie im Azure-Portal **Ressourcengruppen** aus.
2. Wählen Sie die Ressourcengruppe aus, in der sich das Speicherkonto befindet, und wählen Sie dann **Zugriffssteuerung (IAM)** aus.
3. Klicken Sie auf die Registerkarte **Rollenzuweisungen**.
4. Wählen Sie die **Rolle** (z.B. Besitzer oder Mitwirkender) für Ihr Benutzerkonto aus.
5. Wählen Sie in der Liste **Ressourcenanbieter** die Option **Microsoft-Autorisierung** aus. 
    * **Rollenzuweisung** muss die Berechtigungen **Lesen** und **Schreiben** aufweisen.
    * **Rollendefinition** muss die Berechtigungen **Lesen** und **Schreiben** aufweisen.

<a id="server-endpoint-createjobfailed"></a>**Fehler beim Erstellen des Serverendpunkts: „MgmtServerJobFailed“ (Fehlercode: -2134375898)**  
Dieses Problem tritt auf, wenn sich der Serverendpunktpfad auf dem Systemvolume befindet und Cloudtiering aktiviert ist. Das Cloudtiering wird auf dem Systemvolume nicht unterstützt. Um einen Serverendpunkt auf dem Systemvolume zu erstellen, deaktivieren Sie Cloudtiering, wenn Sie den Serverendpunkt erstellen.

<a id="server-endpoint-deletejobexpired"></a>**Fehler beim Löschen des Serverendpunkts: „MgmtServerJobExpired“**                 
Dieses Problem tritt auf, wenn der Server offline ist oder keine Netzwerkkonnektivität aufweist. Ist der Server nicht mehr verfügbar, heben Sie die Registrierung des Servers im Portal auf, wodurch die Serverendpunkte gelöscht werden. Um die Serverendpunkte zu löschen, führen Sie die Schritte aus, die unter [Aufheben der Registrierung eines Servers mit der Azure-Dateisynchronisierung](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service) beschrieben sind.

<a id="server-endpoint-provisioningfailed"></a>**Die Seite „Eigenschaften des Serverendpunkts“ kann nicht geöffnet werden, oder die Cloudtiering-Richtlinie kann nicht aktualisiert werden.**  
Dieses Problem kann auftreten, wenn bei einem Verwaltungsvorgang auf dem Serverendpunkt ein Fehler auftritt. Wenn die Seite „Eigenschaften des Serverendpunkts“ nicht im Azure-Portal geöffnet wird, kann ein Aktualisieren des Serverendpunkts mithilfe von PowerShell-Befehlen auf dem Server dieses Problem beheben. 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**Serverendpunkt hat den Integritätsstatus „Keine Aktivität“ oder „Ausstehend“, und der Serverstatus auf dem Blatt mit den registrierten Servern lautet „Als offline angezeigt“**  

Dieses Problem kann auftreten, wenn der Überwachungsprozess für die Speichersynchronisierung nicht ausgeführt wird oder der Server aufgrund eines Proxy oder einer Firewall nicht mit dem Azure-Dateisynchronisierungsdienst kommunizieren kann.

Führen Sie die folgenden Schritte aus, um das Problem zu beheben:

1. Öffnen Sie den Task-Manager auf dem Server und überprüfen Sie, ob der Überwachungsprozess für die Speichersynchronisierung (AzureStorageSyncMonitor.exe) ausgeführt wird. Wenn der Prozess nicht ausgeführt wird, versuchen Sie zunächst, den Server neu zu starten. Wenn der Neustart des Servers das Problem nicht behebt, führen Sie ein Upgrade auf die [neueste Version](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes) des Azure-Dateisynchronisierungs-Agents aus.
2. Überprüfen Sie, ob Firewall- und Proxy-Einstellungen ordnungsgemäß konfiguriert sind:
    - Wenn sich der Server hinter einer Firewall befindet, überprüfen Sie, ob Port 443 für ausgehenden Datenverkehr zulässig ist. Wenn die Firewall den Datenverkehr auf bestimmte Domänen einschränkt, bestätigen Sie, dass die in der Firewall-[Dokumentation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall) aufgeführten Domänen zugänglich sind.
    - Wenn sich der Server hinter einem Proxy befindet, konfigurieren Sie die computerweiten oder App-spezifischen Proxyeinstellungen, indem Sie den Schritten in der Proxy-[Dokumentation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy) folgen.

<a id="endpoint-noactivity-sync"></a>**Serverendpunkt hat den Integritätsstatus „Keine Aktivität“, und der Serverstatus auf dem Blatt mit den registrierten Servern lautet „Online“**  

Ein Integritätsstatus „Keine Aktivität“ des Serverendpunkts bedeutet, dass der Serverendpunkt innerhalb der letzten zwei Stunden keine Synchronisierungsaktivität protokolliert hat.

Ein Serverendpunkt protokolliert die Synchronisierungsaktivität aus den folgenden Gründen möglicherweise nicht:

- Die Agent-Version 4.3.0.0 oder älter ist installiert, und auf dem Server gibt es eine aktive VSS-Synchronisierungssitzung (SnapshotSync). Wenn eine VSS-Synchronisierungssitzung für einen Serverendpunkt aktiv ist, können andere Serverendpunkte auf dem selben Volume eine Synchronisierungssitzung erst starten, nachdem die VSS-Synchronisierungssitzung abgeschlossen wurde. Um dieses Problem zu beheben, installieren Sie die Agent-Version 5.0.2.0 oder höher, die die Synchronisierung für mehrere Serverendpunkte auf einem Volume unterstützt, wenn eine VSS-Synchronisierungssitzung aktiv ist.

    Um die aktuelle Synchronisierungsaktivität auf einem Server zu überprüfen, lesen Sie [Wie überwache ich den Fortschritt einer aktuellen Synchronisierungssitzung?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

- Der Server hat die maximale Anzahl gleichzeitiger Synchronisierungssitzungen erreicht. 
    - Agent-Version 4.x und höher: Limit variiert basierend auf verfügbaren Systemressourcen.
    - Agent-Version 3.x: 2 aktive Synchronisierungssitzungen pro Prozessor oder maximal 8 aktive Synchronisierungssitzungen pro Server.

> [!Note]  
> Wenn der Serverstatus auf dem Blatt mit den registrierten Servern „Als Offline angezeigt" lautet, führen Sie die im Abschnitt S[erverendpunkt weist einen Integritätsstatus „Keine Aktivität“ oder „Ausstehend“ auf, und der Serverstatus auf dem Blatt mit den registrierten Servern lautet „Als Offline angezeigt“](#server-endpoint-noactivity) aufgeführten Schritte aus.

## <a name="sync"></a>Synchronisierung
<a id="afs-change-detection"></a>**Wie lange dauert es, bis eine Datei auf Servern in der Synchronisierungsgruppe synchronisiert wird, wenn ich die Datei direkt auf meiner Azure-Dateifreigabe mithilfe von SMB oder über das Portal erstellt habe?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**Die Serverendpunktintegrität befindet sich seit mehreren Stunden im Status „Ausstehend“.**  
Dieses Problem ist zu erwarten, wenn Sie einen Cloudendpunkt erstellen und eine Azure-Dateifreigabe verwenden, die Daten enthält. Der Änderungsenumerationsauftrag, der auf Änderungen in der Azure-Dateifreigabe prüft, muss abgeschlossen sein, bevor Dateien zwischen der Cloud und den Serverendpunkten synchronisiert werden können. Der Zeitaufwand für die Auftragsausführung hängt von der Größe des Namespace in der Azure-Dateifreigabe ab. Sobald der Änderungsenumerationsauftrag abgeschlossen ist, sollte die Serverendpunktintegrität aktualisiert werden.

### <a id="broken-sync"></a>Wie überwache ich die Integrität der Synchronisierung?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
In den einzelnen Synchronisierungsgruppen können Sie ein Drilldown auf die jeweiligen Serverendpunkte ausführen, um den Status der zuletzt abgeschlossenen Synchronisierungssitzungen anzuzeigen. Ein grünes Symbol in der Spalte „Integrität“ und der Wert „0“ unter „Dateien ohne Synchronisierung“ geben an, dass die Synchronisierung wie erwartet funktioniert. Wenn dies nicht der Fall sein sollte, sehen Sie sich nachfolgend die Liste mit allgemeinen Synchronisierungsfehlern und die Informationen an, wie Sie Dateien, die nicht synchronisiert wurden, behandeln. 

![Ein Screenshot des Azure-Portals](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="servertabserver"></a>[Server](#tab/server)
Navigieren Sie zu den Telemetrieprotokollen des Servers, die Sie in der Ereignisanzeige unter `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` finden. Das Ereignis 9102 entspricht einer abgeschlossenen Synchronisierungssitzung. Um den aktuellen Status einer Synchronisierung anzuzeigen, suchen Sie das aktuelle Ereignis mit der ID 9102. Über SyncDirection erfahren Sie, ob es sich bei dieser Sitzung um einen Upload oder Download gehandelt hat. Wenn HResult „0“ lautet, war eine Synchronisierungssitzung erfolgreich. Ein HResult ungleich „0“ bedeutet, dass während der Synchronisierung ein Fehler aufgetreten ist. Eine Liste von häufigen Fehlern finden Sie unten. Wenn die PerItemErrorCount größer als 0 ist, bedeutet dies, dass bestimmte Dateien oder Ordner nicht ordnungsgemäß synchronisiert wurden. Es kann vorkommen, dass ein HResult von „0“ angibt, aber eine PerItemErrorCount angegeben wird, die größer als 0 ist.

Im Folgenden wird ein Beispiel für einen erfolgreichen Upload vorgestellt. Der Übersichtlichkeit halber werden nachfolgend nur einige der Werte aufgeführt, die in den einzelnen 9102-Ereignissen enthalten sind. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Im Gegensatz dazu kann ein fehlerhafter Upload wie folgt aussehen:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Gelegentlich tritt bei allen Synchronisierungssitzungen ein Fehler auf, oder PerItemErrorCount ist ungleich NULL, obwohl trotzdem ein Fortschritt angezeigt wird und einige Dateien dabei erfolgreich synchronisiert werden. Dies kann bei den Feldern beobachtet werden, die mit „Applied*“ beginnen (AppliedFileCount AppliedDirCount, AppliedTombstoneCount und AppliedSizeBytes). Diese geben den Anteil an, zu dem die Sitzung zum jeweiligen Zeitpunkt durchgeführt ist. Wenn mehrere Synchronisierungssitzungen in einer Zeile angezeigt werden, bei denen Fehler auftreten, die jedoch eine steigende Zahl in einem „Applied*“-Feld angeben, sollten Sie warten, bis die Synchronisierung abgeschlossen ist und es erneut versuchen. Erst dann sollten Sie auf die Möglichkeit zum Öffnen eines Supporttickets zurückgreifen.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Wie überwache ich den Fortschritt einer aktuellen Synchronisierungssitzung?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
Navigieren Sie in Ihrer Synchronisierungsgruppe zum betreffenden Serverendpunkt und dann zum Abschnitt „Synchronisierungsaktivität“. Dort wird die Anzahl der Dateien angezeigt, die Sie in der aktuellen Synchronisierungssitzung hoch- oder heruntergeladen haben. Beachten Sie, dass dieser Status mit einer Verzögerung von ca. 5 Minuten angezeigt wird. Wenn Ihre Synchronisierungssitzung so kurz ist, dass sie innerhalb dieses Zeitraums abgeschlossen wird, wird sie eventuell nicht im Portal gemeldet. 

# <a name="servertabserver"></a>[Server](#tab/server)
Suchen Sie das aktuelle Ereignis 9302 im Telemetrieprotokoll auf dem Server (in der Ereignisanzeige unter „Anwendungs- und Dienstprotokolle\Microsoft\FileSync\Agent\Telemetry“). Dieses Ereignis gibt den Zustand der aktuellen Synchronisierungssitzung an. TotalItemCount gibt die Anzahl der zu synchronisierenden Dateien, AppliedItemCount die Anzahl der bisher synchronisierten Dateien und PerItemErrorCount die Anzahl der Dateien an, bei denen die Synchronisierung zu einem Fehler führte (weitere Informationen diesbezüglich finden Sie unten).

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Wie erkenne ich, ob meine Server miteinander synchronisiert sind?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
Stellen Sie bei jedem Server in einer bestimmten Synchronisierungsgruppe Folgendes sicher:
- Die Zeitstempel für „Letzter Synchronisierungsversuch“ für Uploads und Downloads sind aktuell.
- Der Status ist sowohl für Uploads als auch für Downloads grün.
- Das Feld „Synchronisierungsaktivität“ zeigt, dass nur sehr wenige oder keine Dateien mehr synchronisiert werden müssen.
- Das Feld „Dateien ohne Synchronisierung“ ist für Uploads und Downloads „0“.

# <a name="servertabserver"></a>[Server](#tab/server)
Sehen Sie sich die durchgeführten Synchronisierungssitzungen an, die von 9102-Ereignissen im Telemetrieereignisprotokoll für die einzelnen Server gekennzeichnet sind (in der Ereignisanzeige unter `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`). 

1. Auf dem betreffenden Server sollten Sie sicherstellen, dass die neuesten Upload- und Downloadsitzungen erfolgreich abgeschlossen wurden. Überprüfen Sie hierfür, ob das HResult und die PerItemErrorCount für Uploads und Downloads „0“ lauten (das Feld „SyncDirection“ gibt an, ob es sich bei einer Sitzung um eine Upload- oder eine Downloadsitzung handelt). Hinweis: Wenn eine kürzlich abgeschlossene Synchronisierungssitzung nicht angezeigt wird, wird aller Wahrscheinlichkeit nach momentan eine Synchronisierungssitzung ausgeführt. Dies ist normal, wenn Sie gerade eine große Menge von Daten hinzugefügt oder geändert haben.
2. Wenn ein Server vollständig mit der Cloud synchronisiert ist und es in beiden Richtungen keine zu synchronisierenden Änderungen gibt, werden leere Synchronisierungssitzungen angezeigt. Diese werden durch Upload- und Downloadereignisse angegeben, bei denen alle Felder, die mit „Sync*“ beginnen (SyncFileCount, SyncDirCount, SyncTombstoneCount und SyncSizeBytes), NULL sind. Dies bedeutet, dass es keine zu synchronisierenden Änderungen gab. Beachten Sie, dass diese leeren Synchronisierungssitzungen möglicherweise nicht auf Servern mit hohem Änderungsumfang angezeigt werden, da es laufend zu synchronisierende Änderungen gibt. Liegt keine Synchronisierungsaktivität vor, sollte sie alle 30 Minuten ausgeführt werden. 
3. Wenn alle Server mit der Cloud synchronisiert wurden (d.h., die aktuellen Upload- und Downloadsitzungen sind leere Synchronisierungssitzungen), können Sie mit hoher Wahrscheinlichkeit davon ausgehen, dass das gesamte System synchron ist. 
    
Beachten Sie, dass die Azure-Dateisynchronisierung Änderungen, die Sie direkt in Ihrer Azure-Dateifreigabe vorgenommen haben, erst erkennt, wenn die Änderungsenumeration ausgeführt wird (tritt einmal alle 24 Stunden auf). Es kann vorkommen, dass ein Server als mit der Cloud synchron angegeben wird, obwohl in Wirklichkeit in letzter Zeit keine Änderungen direkt in der Azure-Dateifreigabe vorgenommen wurden. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Woran erkenne ich, dass bestimmte Dateien oder Ordnern nicht synchronisiert wurden?
Wenn im Portal die PerItemErrorCount auf dem Server oder „Dateien ohne Synchronisierung“ für eine bestimmte Synchronisierungssitzung einen größeren Wert als „0“ enthält, bedeutet dies, dass bei einigen Elementen ein Synchronisierungsfehler aufgetreten ist. Dateien und Ordner können Merkmale aufweisen, die eine Synchronisierung verhindern. Diese Merkmale können dauerhaft sein und eine explizite Aktion zum Fortsetzen der Synchronisierung erfordern, z.B. das Entfernen von nicht unterstützten Zeichen aus einem Datei- oder Ordnernamen. Sie können auch vorübergehend sein, d.h., die Synchronisierung der Datei oder des Ordners wird automatisch fortgesetzt. Beispielsweise wird die Synchronisierung von Dateien mit offenen Handles automatisch fortgesetzt, wenn die Datei geschlossen ist. Wenn die Azure-Dateisynchronisierungs-Engine ein solches Problem erkennt, wird ein Fehlerprotokoll erstellt, das für die Auflistung der Elemente, die derzeit nicht ordnungsgemäß synchronisiert werden, analysiert werden kann.

Um diese Fehler anzuzeigen, führen Sie das PowerShell-Skript **FileSyncErrorsReport.ps1** aus (im Agent-Installationsverzeichnis des Azure-Dateisynchronisierungs-Agents), um Dateien zu identifizieren, bei denen die Synchronisierung aufgrund von offenen Handles, nicht unterstützten Zeichen oder anderen Problemen zu Fehlern führte. Das ItemPath-Feld gibt den Speicherort der Datei in Bezug auf das Stammverzeichnis für die Synchronisierung an. Eine Liste von allgemeinen Synchronisierungsfehlern mit Schritten zur Fehlerbehebung finden Sie weiter unten.

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Behandlung von Synchronisierungsfehlern nach Dateien und Verzeichnissen
**ItemResults-Protokoll – Synchronisierungsfehler nach Element**  

| HRESULT | HRESULT (dezimal) | Fehlerzeichenfolge | Problem | Wiederherstellung |
|---------|-------------------|--------------|-------|-------------|
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | Eine Datei- oder Verzeichnisänderung kann noch nicht synchronisiert werden, da ein abhängiger Ordner noch nicht synchronisiert ist. Dieses Element wird synchronisiert, sobald die abhängigen Änderungen synchronisiert wurden. | Keine weiteren Maßnahmen erforderlich. |
| 0x7b | 123 | ERROR_INVALID_NAME | Der Datei- oder Verzeichnisname ist ungültig. | Benennen Sie die jeweiligen Datei oder das betreffende Verzeichnis um. Weitere Informationen finden Sie unter [Behandlung von nicht unterstützten Zeichen](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters). |
| 0x8007007b | -2147024773 | STIERR_INVALID_DEVICE_NAME | Der Datei- oder Verzeichnisname ist ungültig. | Benennen Sie die jeweiligen Datei oder das betreffende Verzeichnis um. Weitere Informationen finden Sie unter [Behandlung von nicht unterstützten Zeichen](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters). |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Eine Datei kann nicht synchronisiert werden, da sie momentan verwendet wird. Die Datei wird synchronisiert, wenn sie nicht mehr verwendet wird. | Keine weiteren Maßnahmen erforderlich. Die Azure-Dateisynchronisierung erstellt einmal pro Tag eine temporäre VSS-Momentaufnahme auf dem Server, um Dateien mit offenen Handles zu synchronisieren. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Eine Datei wurde geändert, doch die Änderung wurde noch nicht von der Synchronisierung erkannt. Die Synchronisierung wird aktualisiert, sobald diese Änderung erkannt wurde. | Keine weiteren Maßnahmen erforderlich. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Die Datei kann nicht synchronisiert werden, da das Limit für die Azure-Dateifreigabe erreicht ist. | Um dieses Problem zu beheben, lesen Sie den Abschnitt [Sie haben das Speicherlimit für die Azure-Dateifreigabe erreicht](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810) in diesem Leitfaden zur Problembehandlung. |
| 0x80070005 | -2147024891 | E_ACCESSDENIED | Dieser Fehler kann aus den folgenden Gründen auftreten: Die Datei ist über eine nicht unterstützte Lösung (z.B. NTFS EFS) verschlüsselt, ihr Löschstatus steht aus, oder sie befindet sich in einem schreibgeschützten DFS-R-Replikationsordner. | Wenn die Datei von einer nicht unterstützten Lösung verschlüsselt wird, entschlüsseln Sie die Datei, und verwenden Sie eine unterstützte Verschlüsselungslösung. Eine Liste der unterstützten Lösungen finden Sie im Abschnitt [Verschlüsselungslösungen](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption-solutions) im Planungshandbuch. Wenn sich die Datei im Status „Zum Löschen ausstehend“ befindet, wird die Datei gelöscht, sobald alle offenen Dateihandles geschlossen sind. Wenn die Datei sich in einem schreibgeschützten DFS-R-Replikationsordner befindet, unterstützt Azure Files Sync keine Serverendpunkte in schreibgeschützten DFS-R-Replikationsordnern. Weitere Informationen finden Sie im [Planungshandbuch](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs).
| 0x20 | 32 | ERROR_SHARING_VIOLATION | Eine Datei kann nicht synchronisiert werden, da sie momentan verwendet wird. Die Datei wird synchronisiert, wenn sie nicht mehr verwendet wird. | Keine weiteren Maßnahmen erforderlich. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Eine Datei wurde während der Synchronisierung geändert, deshalb muss sie erneut synchronisiert werden. | Keine weiteren Maßnahmen erforderlich. |

#### <a name="handling-unsupported-characters"></a>Behandlung von nicht unterstützten Zeichen
Wenn das PowerShell-Skript **FileSyncErrorsReport.ps1** Fehler aufgrund von nicht unterstützten Zeichen (Fehlercodes 0x7b und 0x8007007b) anzeigt, sollten Sie die jeweiligen Zeichen aus den entsprechenden Dateinamen entfernen oder ändern. PowerShell gibt diese Zeichen wahrscheinlich als Fragezeichen oder leere Rechtecke aus, da die meisten dieser Zeichen keine standardisierte visuelle Codierung aufweisen. Mit dem [Auswertungstool](storage-sync-files-planning.md#evaluation-tool) können Sie nicht unterstützte Zeichen identifizieren.

Die folgende Tabelle enthält alle Unicode-Zeichen, die die Azure-Dateisynchronisierung noch nicht unterstützt.

| Zeichensatz | Zeichenanzahl |
|---------------|-----------------|
| <ul><li>0x0000009D (Operating System Command, OSC)</li><li>0x00000090 (Device Control String, DCS)</li><li>0x0000008F (Single Shift Three, SS3)</li><li>0x00000081 (High Octet Preset)</li><li>0x0000007F (Delete, DEL)</li><li>0x0000008D (Reverse Line Feed, RI)</li></ul> | 6 |
| 0x0000FDD0–0x0000FDEF (arabische Darstellungsformen) | 32 |
| 0x0000FFF0–0x0000FFFF (Sonderzeichen) | 16 |
| <ul><li>0x0001FFFE–0x0001FFFF = 2 (Nicht-Zeichen)</li><li>0x0002FFFE–0x0002FFFF = 2 (Nicht-Zeichen)</li><li>0x0003FFFE–0x0003FFFF = 2 (Nicht-Zeichen)</li><li>0x0004FFFE–0x0004FFFF = 2 (Nicht-Zeichen)</li><li>0x0005FFFE–0x0005FFFF = 2 (Nicht-Zeichen)</li><li>0x0006FFFE–0x0006FFFF = 2 (Nicht-Zeichen)</li><li>0x0007FFFE–0x0007FFFF = 2 (Nicht-Zeichen)</li><li>0x0008FFFE–0x0008FFFF = 2 (Nicht-Zeichen)</li><li>0x0009FFFE–0x0009FFFF = 2 (Nicht-Zeichen)</li><li>0x000AFFFE–0x000AFFFF = 2 (Nicht-Zeichen)</li><li>0x000BFFFE–0x000BFFFF = 2 (Nicht-Zeichen)</li><li>0x000CFFFE–0x000CFFFF = 2 (Nicht-Zeichen)</li><li>0x000DFFFE–0x000DFFFF = 2 (Nicht-Zeichen)</li><li>0x000EFFFE–0x000EFFFF = 2 (nicht definiert)</li><li>0x000FFFFE–0x000FFFFF = 2 (zusätzlicher privater Verwendungsbereich)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Allgemeine Synchronisierungsfehler
<a id="-2147023673"></a>**Die Synchronisierungssitzung wurde abgebrochen.**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (dezimal)** | -2147023673 | 
| **Fehlerzeichenfolge** | ERROR_CANCELLED |
| **Korrektur erforderlich** | Nein |

Bei Synchronisierungssitzungen kann aus verschiedenen Gründen ein Fehler auftreten, z.B. aufgrund eines Serverneustarts oder einer Serveraktualisierung oder aufgrund von VSS-Momentaufnahmen. Auch wenn dieser Fehler den Anschein erweckt, als ob eine Nachverfolgung erforderlich wäre, kann dieser ignoriert werden, es sei denn, er bleibt über einen Zeitraum von mehreren Stunden bestehen.

<a id="-2147012889"></a>**Eine Verbindung mit dem Dienst konnte nicht hergestellt werden.**    

| | |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT (dezimal)** | -2147012889 | 
| **Fehlerzeichenfolge** | WININET_E_NAME_NOT_RESOLVED |
| **Korrektur erforderlich** | Ja |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**Die Benutzeranforderung wurde durch den Dienst gedrosselt.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (dezimal)** | -2134376372 |
| **Fehlerzeichenfolge** | ECS_E_USER_REQUEST_THROTTLED |
| **Korrektur erforderlich** | Nein |

Es ist keine Aktion erforderlich. Der Server wiederholt den Vorgang. Erstellen Sie nur dann eine Supportanfrage, wenn dieser Fehler länger als ein paar Stunden anhält.

<a id="-2134364043"></a>**Synchronisierung wird blockiert, bis die Änderungserkennung nach der Wiederherstellung abgeschlossen ist**  

| | |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (dezimal)** | -2134364043 |
| **Fehlerzeichenfolge** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Korrektur erforderlich** | Nein |

Es ist keine Aktion erforderlich. Wenn eine Datei oder Dateifreigabe (Cloudendpunkt) mithilfe von Azure Backup wiederhergestellt wird, wird die Synchronisierung so lange blockiert, bis die Änderungserkennung auf der Azure-Dateifreigabe abgeschlossen ist. Unmittelbar nach Abschluss der Wiederherstellung wird die Änderungserkennung ausgeführt, deren Dauer auf der Anzahl der Dateien in der Dateifreigabe basiert.

<a id="-2134364065"></a>**Die Synchronisierung kann nicht auf die im Cloudendpunkt angegebene Azure-Dateifreigabe zugreifen.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (dezimal)** | -2134364065 |
| **Fehlerzeichenfolge** | ECS_E_CANNOT_ACCESS_EXTERNAL_STORAGE_ACCOUNT |
| **Korrektur erforderlich** | Ja |

Dieser Fehler tritt auf, da der Azure-Dateisynchronisierungs-Agent nicht auf die Azure-Dateifreigabe zugreifen kann. Dies ist möglicherweise darauf zurückzuführen, dass die Azure-Dateifreigabe oder das Speicherkontohosting nicht mehr vorhanden ist. Sie können diesen Fehler beheben, indem Sie die folgenden Schritte durchführen:

1. [Überprüfen Sie, ob das Speicherkonto vorhanden ist.](#troubleshoot-storage-account)
2. [Stellen Sie sicher, dass das Speicherkonto keine Netzwerkregeln enthält.](#troubleshoot-network-rules)
3. [Stellen Sie sicher, dass die Azure-Dateifreigabe vorhanden ist.](#troubleshoot-azure-file-share)
4. [Stellen Sie sicher, dass die Azure-Dateisynchronisierung über Zugriff auf das Speicherkonto verfügt.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**Der verwendete Speicherkontoname konnte nicht aufgelöst werden.**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (dezimal)** | -2134364064 |
| **Fehlerzeichenfolge** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Korrektur erforderlich** | Ja |

1. Überprüfen Sie, ob Sie den Speicher-DNS-Namen vom Server auflösen können.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Überprüfen Sie, ob das Speicherkonto vorhanden ist.](#troubleshoot-storage-account)
3. [Stellen Sie sicher, dass das Speicherkonto keine Netzwerkregeln enthält.](#troubleshoot-network-rules)

<a id="-1906441138"></a>**Fehler bei der Synchronisierung aufgrund eines Problems mit der Synchronisierungsdatenbank.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (dezimal)** | -1906441138 |
| **Fehlerzeichenfolge** | JET_errWriteConflict |
| **Korrektur erforderlich** | Ja |

Dieser Fehler tritt auf, wenn ein Problem mit der internen Datenbank besteht, die von der Azure-Dateisynchronisierung verwendet wird. Wenn dieses Problem auftritt, erstellen Sie eine Supportanfrage, und wir kontaktieren Sie, um Sie bei der Problemlösung zu unterstützen.

<a id="-2134364053"></a>**Die auf dem Server installierte Version des Azure-Dateisynchronisierungs-Agents wird nicht unterstützt.**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (dezimal)** | -2134364053 |
| **Fehlerzeichenfolge** | ECS_E_AGENT_VERSION_BLOCKED |
| **Korrektur erforderlich** | Ja |

Dieser Fehler tritt auf, wenn die auf dem Server installierte Version des Azure-Dateisynchronisierungs-Agents nicht unterstützt wird. Um dieses Problem zu beheben, führen Sie ein [Upgrade]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) auf eine [unterstützte Agent-Version]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions) aus.

<a id="-2134351810"></a>**Sie haben das Speicherlimit für die Azure-Dateifreigabe erreicht.**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (dezimal)** | -2134351810 |
| **Fehlerzeichenfolge** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Korrektur erforderlich** | Ja |

Dieser Fehler tritt auf, wenn das Speicherlimit für die Azure Dateifreigabe erreicht wurde. Dies kann vorkommen, wenn ein Kontingent für eine Azure-Dateifreigabe angewendet wird oder die Nutzung die Grenzwerte einer Azure-Dateifreigabe überschreitet. Weitere Informationen finden Sie in den [aktuellen Grenzwerten für eine Azure-Dateifreigabe](storage-files-scale-targets.md).

1. Navigieren Sie zu der Synchronisierungsgruppe im Speichersynchronisierungsdienst.
2. Wählen Sie den Cloudendpunkt innerhalb der Synchronisierungsgruppe aus.
3. Beachten Sie den Namen der Azure-Dateifreigabe im geöffneten Bereich.
4. Wählen Sie das verknüpfte Speicherkonto aus. Wenn bei dieser Verknüpfung ein Fehler auftritt, wurde das Speicherkonto, auf das verwiesen wurde, entfernt.

    ![Ein Screenshot, das den Bereich mit Details zum Cloudendpunkt und einem Link zum Speicherkonto zeigt](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Wählen Sie **Dateien** zum Anzeigen der Liste von Dateifreigaben aus.
6. Klicken Sie auf die drei Punkte am Ende der Zeile für die Azure-Dateifreigabe, auf die vom Cloudendpunkt verwiesen wird.
7. Überprüfen Sie, ob die **Nutzung** unterhalb des **Kontingents** liegt. Beachten Sie, dass das Kontingent der [maximalen Größe der Azure-Dateifreigabe](storage-files-scale-targets.md) entspricht, es sei denn, ein alternatives Kontingent wurde festgelegt.

    ![Ein Screenshot der Eigenschaften einer Azure Dateifreigabe](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Wenn die Freigabe ausgeschöpft ist und kein Kontingent festgelegt ist, besteht eine Möglichkeit zur Behebung dieses Problems darin, jeden Unterordner des aktuellen Serverendpunkts in einen eigenen-Serverendpunkt in separaten Synchronisierungsgruppen umzuwandeln. Dadurch wird jeder Unterordner mit individuellen Azure-Dateifreigaben synchronisiert.

<a id="-2134351824"></a>**Die gelöschte Azure-Dateifreigabe kann nicht gefunden werden.**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (dezimal)** | -2134351824 |
| **Fehlerzeichenfolge** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Korrektur erforderlich** | Ja |

Dieser Fehler tritt auf, wenn auf die Azure-Dateifreigabe nicht zugegriffen werden kann. Führen Sie zur Problembehandlung folgende Schritte durch:

1. [Überprüfen Sie, ob das Speicherkonto vorhanden ist.](#troubleshoot-storage-account)
2. [Stellen Sie sicher, dass die Azure-Dateifreigabe vorhanden ist.](#troubleshoot-azure-file-share)

Falls die Azure-Dateifreigabe gelöscht wurde, müssen Sie eine neue Dateifreigabe erstellen und die Synchronisierungsgruppe anschließend neu erstellen. 

<a id="-2134364042"></a>**Die Synchronisierung wird angehalten, solange dieses Azure-Abonnement ausgesetzt ist.**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (dezimal)** | -2134364042 |
| **Fehlerzeichenfolge** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Korrektur erforderlich** | Ja |

Dieser Fehler tritt auf, wenn das Azure-Abonnement ausgesetzt ist. Die Synchronisierung wird erneut aktiviert, wenn das Azure-Abonnement wiederhergestellt wurde. Weitere Informationen finden Sie unter [Warum ist mein Azure-Abonnement deaktiviert, und wie reaktiviere ich es?](../../billing/billing-subscription-become-disable.md).

<a id="-2134364052"></a>**Für das Speicherkonto wurden eine Firewall oder virtuelle Netzwerke konfiguriert.**  

| | |
|-|-|
| **HRESULT** | 0x80c8306c |
| **HRESULT (dezimal)** | -2134364052 |
| **Fehlerzeichenfolge** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **Korrektur erforderlich** | Ja |

Dieser Fehler tritt auf, wenn aufgrund einer Firewall oder der Zugehörigkeit des Speicherkontos zu einem virtuellen Netzwerk nicht auf die Azure-Dateifreigabe zugegriffen werden kann. Die Azure-Dateisynchronisierung bietet noch keine Unterstützung für dieses Feature. Führen Sie zur Problembehandlung folgende Schritte durch:

1. [Überprüfen Sie, ob das Speicherkonto vorhanden ist.](#troubleshoot-storage-account)
2. [Stellen Sie sicher, dass das Speicherkonto keine Netzwerkregeln enthält.](#troubleshoot-network-rules)

Entfernen Sie diese Regeln, um dieses Problem zu beheben. 

<a id="-2134375911"></a>**Fehler bei der Synchronisierung aufgrund eines Problems mit der Synchronisierungsdatenbank.**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (dezimal)** | -2134375911 |
| **Fehlerzeichenfolge** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Korrektur erforderlich** | Nein |

Dieser Fehler löst sich im Allgemeinen von selbst und kann folgende Ursachen haben:

* Eine hohe Anzahl von Dateiänderungen zwischen den Servern in der Synchronisierungsgruppe
* Eine hohe Anzahl von Fehlern in einzelnen Dateien und Verzeichnissen

Wenn dieser Fehler länger anhält als ein paar Stunden, erstellen Sie eine Supportanfrage, und wir kontaktieren Sie, um Sie bei der Problemlösung zu unterstützen.

<a id="-2146762487"></a>**Der Server konnte keine sichere Verbindung herstellen. Der Clouddienst hat ein unerwartetes Zertifikat empfangen.**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (dezimal)** | -2146762487 |
| **Fehlerzeichenfolge** | CERT_E_UNTRUSTEDROOT |
| **Korrektur erforderlich** | Ja |

Dieser Fehler kann vorkommen, wenn Ihre Organisation einen SSL-Terminierungsproxy verwendet oder eine schädliche Entität den Datenverkehr zwischen Ihrem Server und der Azure-Dateisynchronisierung abfängt. Wenn Sie sicher sind, dass dies normal ist (da Ihre Organisation einen SSL-Terminierungsproxy verwendet), überspringen Sie die Zertifikatüberprüfung durch eine Außerkraftsetzung der Registrierung.

1. Erstellen Sie den Registrierungswert „SkipVerifyingPinnedRootCertificate“.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Starten Sie den Synchronisierungsdienst auf dem registrierten Server neu.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Durch das Festlegen dieses Registrierungswerts akzeptiert der Azure-Dateisynchronisierungs-Agent alle vertrauenswürdigen lokalen SSL-Zertifikate, wenn Daten zwischen dem Server und dem Clouddienst übertragen werden.

<a id="-2147012894"></a>**Eine Verbindung mit dem Dienst konnte nicht hergestellt werden.**  

| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (dezimal)** | -2147012894 |
| **Fehlerzeichenfolge** | WININET_E_TIMEOUT |
| **Korrektur erforderlich** | Ja |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**Fehler bei der Synchronisierung aufgrund eines Problems mit der Authentifizierung.**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (dezimal)** | -2134375680 |
| **Fehlerzeichenfolge** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Korrektur erforderlich** | Ja |

Dieser Fehler kann die folgenden Ursachen haben:

- Die Serverzeit ist falsch.
- Fehler beim Löschen des Serverendpunkts.
- Das für die Authentifizierung verwendete Zertifikat ist abgelaufen. 
    Um festzustellen, ob das Zertifikat abgelaufen ist, führen Sie die folgenden Schritte aus:  
    1. Öffnen Sie das Zertifikat-MMC-Snap-In, wählen Sie das Computerkonto aus, und navigieren Sie zu den Zertifikaten: „(Lokaler Computer)\Benutzer\Certificates“.
    2. Überprüfen Sie, ob das Clientauthentifizierungszertifikat abgelaufen ist.

Wenn die Serverzeit richtig ist, führen Sie die folgenden Schritte aus, um das Problem zu beheben:

1. Überprüfen Sie, ob die Version 4.0.1.0 oder höher des Azure-Dateisynchronisierungs-Agents installiert ist.
2. Führen Sie die folgenden PowerShell-Befehle auf dem Server aus:

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Das Volume, auf dem sich der Serverendpunkt befindet, weist nicht genügend Speicherplatz auf dem Datenträger auf.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (dezimal)** | -1906441711 |
| **Fehlerzeichenfolge** | JET_errLogDiskFull |
| **Korrektur erforderlich** | Ja |
| | |
| **HRESULT** | 0x80c8031a |
| **HRESULT (dezimal)** | -2134375654 |
| **Fehlerzeichenfolge** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Korrektur erforderlich** | Ja |

Dieser Fehler tritt auf, da sich das Volume gefüllt hat. Dieser Fehler tritt häufig auf, da Dateien außerhalb des Serverendpunkts auf dem Volume Speicherplatz belegen. Geben Sie Speicherplatz auf dem Volume frei, indem Sie zusätzliche Serverendpunkte hinzufügen, Dateien in ein anderes Volume verschieben oder die Größe des Volumes, in dem sich der Serverendpunkt befindet, erhöhen.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**Der Dienst ist für die Synchronisierung mit diesem Serverendpunkt noch nicht bereit.**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (dezimal)** | -2134364145 |
| **Fehlerzeichenfolge** | ECS_E_REPLICA_NOT_READY |
| **Korrektur erforderlich** | Nein |

Dieser Fehler tritt auf, weil direkt Änderungen an der Azure-Dateifreigabe vorgenommen wurden und die Erkennung von Änderungen momentan ausgeführt wird. Die Synchronisierung beginnt nach Abschluss der Änderungserkennung.

[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Fehler bei der Synchronisierung aufgrund von Problemen mit einer Vielzahl einzelner Dateien.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (dezimal)** | -2134364145 |
| **Fehlerzeichenfolge** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Korrektur erforderlich** | Ja |
| | |
| **HRESULT** | 0x80c8021c |
| **HRESULT (dezimal)** | -2134375908 |
| **Fehlerzeichenfolge** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Korrektur erforderlich** | Ja |
| | |
| **HRESULT** | 0x80c80253 |
| **HRESULT (dezimal)** | -2134375853 |
| **Fehlerzeichenfolge** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Korrektur erforderlich** | Ja |

In Fällen, in denen viele Synchronisierungsfehler pro Datei auftreten, treten bei den Synchronisierungssitzungen Fehler auf. <!-- To troubleshoot this state, see [Troubleshooting per file/directory sync errors]().-->

> [!NOTE]
> Die Azure-Dateisynchronisierung erstellt einmal pro Tag eine temporäre VSS-Momentaufnahme auf dem Server, um Dateien mit offenen Handles zu synchronisieren.

<a id="-2134376423"></a>**Fehler bei der Synchronisierung aufgrund eines Problems mit dem Serverendpunktpfad.**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (dezimal)** | -2134376423 |
| **Fehlerzeichenfolge** | ECS_E_SYNC_INVALID_PATH |
| **Korrektur erforderlich** | Ja |

Stellen Sie sicher, dass der Pfad vorhanden ist, sich in einem lokalen NTFS-Volume befindet und kein Analysepunkt oder vorhandener Serverendpunkt ist.

<a id="-2134375817"></a>**Fehler beim Synchronisieren, da die Version des Filtertreibers nicht mit der Agentversion kompatibel ist**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (dezimal)** | -2134375817 |
| **Fehlerzeichenfolge** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Korrektur erforderlich** | Ja |

Dieser Fehler tritt auf, da die geladene Version des Cloudtiering-Filtertreibers (StorageSync.sys) nicht mit dem Storage Sync Agent (FileSyncSvc)-Dienst kompatibel ist. Wenn der Azure-Dateisynchronisierungs-Agent aktualisiert wurde, starten Sie den Server neu, um die Installation abzuschließen. Wenn der Fehler weiterhin auftritt, deinstallieren Sie den Agent, starten Sie den Server neu und installieren Sie den Azure-Dateisynchronisierungs-Agent neu.

<a id="-2134376373"></a>**Der Dienst ist derzeit nicht verfügbar.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (dezimal)** | -2134376373 |
| **Fehlerzeichenfolge** | ECS_E_SERVICE_UNAVAILABLE |
| **Korrektur erforderlich** | Nein |

Dieser Fehler tritt auf, da der Azure-Dateisynchronisierungsdienst nicht verfügbar ist. Dieser Fehler wird automatisch aufgelöst, wenn der Azure-Dateisynchronisierungsdienst wieder verfügbar ist.

<a id="-2134375922"></a>**Fehler bei der Synchronisierung aufgrund eines vorübergehenden Problems mit der Synchronisierungsdatenbank.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (dezimal)** | -2134375922 |
| **Fehlerzeichenfolge** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Korrektur erforderlich** | Nein |

Dieser Fehler tritt aufgrund eines internen Problems mit der Synchronisierungsdatenbank auf. Dieser Fehler wird automatisch aufgelöst, wenn die Azure-Dateisynchronisierung die Synchronisierung wiederholt. Wenn dieser Fehler für einen längeren Zeitraum anhält, erstellen Sie eine Supportanfrage, und wir kontaktieren Sie, um Sie bei der Problemlösung zu unterstützen.

### <a name="common-troubleshooting-steps"></a>Allgemeine Schritte zur Problembehandlung
<a id="troubleshoot-storage-account"></a>**Überprüfen Sie, ob das Speicherkonto vorhanden ist.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Navigieren Sie zu der Synchronisierungsgruppe im Speichersynchronisierungsdienst.
2. Wählen Sie den Cloudendpunkt innerhalb der Synchronisierungsgruppe aus.
3. Beachten Sie den Namen der Azure-Dateifreigabe im geöffneten Bereich.
4. Wählen Sie das verknüpfte Speicherkonto aus. Wenn bei dieser Verknüpfung ein Fehler auftritt, wurde das Speicherkonto, auf das verwiesen wurde, entfernt.
    ![Ein Screenshot, das den Bereich mit Details zum Cloudendpunkt und einem Link zum Speicherkonto zeigt](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-network-rules"></a>**Stellen Sie sicher, dass das Speicherkonto keine Netzwerkregeln enthält.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Wählen Sie im Speicherkonto auf der linken Seite **Firewalls und virtuelle Netzwerke** aus.
2. Innerhalb des Speicherkontos sollte das Optionsfeld **Zugriff von allen Netzwerken zulassen** aktiviert sein.
    ![Ein Screenshot, der die deaktivierte Firewall und Netzwerkregeln des Speicherkontos zeigt](media/storage-sync-files-troubleshoot/file-share-inaccessible-2.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
if ($storageAccount.NetworkRuleSet.DefaultAction -ne 
    [Microsoft.Azure.Commands.Management.Storage.Models.PSNetWorkRuleDefaultActionEnum]::Allow) {
    throw [System.Exception]::new("The storage account referenced contains network " + `
        "rules which are not currently supported by Azure File Sync.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Stellen Sie sicher, dass die Azure-Dateifreigabe vorhanden ist.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Klicken Sie auf der linken Seite im Inhaltsverzeichnis auf **Übersicht**, um zur Hauptseite des Speicherkontos zurückzukehren.
2. Wählen Sie **Dateien** zum Anzeigen der Liste von Dateifreigaben aus.
3. Vergewissern Sie sich, dass die Dateifreigabe, auf die durch den Cloudendpunkt verwiesen wird, in der Liste der Dateifreigaben angezeigt wird (Sie sollten diese bei Schritt 1 notiert haben).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**Stellen Sie sicher, dass die Azure-Dateisynchronisierung über Zugriff auf das Speicherkonto verfügt.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Klicken Sie links im Inhaltsverzeichnis auf **Zugriffssteuerung (IAM)** .
1. Klicken Sie auf die Registerkarte **Rollenzuweisungen**, um die Liste der Benutzer und Anwendungen (*Dienstprinzipale*) anzuzeigen, die Zugriff auf Ihr Speicherkonto besitzen.
1. Stellen Sie sicher, dass der **hybride Dateisynchronisierungsdienst** in der Liste mit der Rolle **Lese- und Datenzugriff** angezeigt wird. 

    ![Ein Screenshot des Dienstprinzipals des hybriden Dateisynchronisierungsdiensts auf der Registerkarte zur Zugriffssteuerung des Speicherkontos](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Wird **Hybrid-Dateisynchronisierungsdienst** nicht in der Liste angezeigt, führen Sie die folgenden Schritte aus:

    - Klicken Sie auf **Hinzufügen**.
    - Wählen Sie im Feld **Rolle** die Option **Lese- und Datenzugriff** aus.
    - Geben Sie im Feld **Auswählen** die Zeichenfolge **Hybrid-Dateisynchronisierungsdienst** ein, wählen Sie die Rolle aus, und klicken Sie auf **Speichern**.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Hybrid File Sync Service" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Wie verhindere ich, dass Benutzer Dateien auf dem Server erstellen, die nicht unterstützte Zeichen enthalten?
Mit den [Dateiprüfungen des Ressourcen-Managers für Dateiserver (File Server Resource Manager, FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management) können Sie verhindern, dass Dateien mit nicht unterstützten Zeichen in deren Namen auf dem Server erstellt werden. Möglicherweise müssen Sie dazu PowerShell verwenden, da der Großteil der nicht unterstützten Zeichen nicht druckbar ist, und daher zuerst ihre hexadezimalen Darstellungen in Zeichen umwandeln.

Erstellen Sie zunächst mit dem [Cmdlet „New-FsrmFileGroup“](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup) eine FSRM-Dateigruppe. Dieses Beispiel definiert, dass die Gruppe nur zwei der nicht unterstützten Zeichen enthalten darf, Sie können jedoch so viele Zeichen wie erforderlich in Ihrer Dateigruppe einschließen.

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

Nachdem Sie eine FSRM-Dateigruppe definiert haben, können Sie mit dem Cmdlet „New-FsrmFileScreen“ eine FSRM-Dateiprüfung erstellen.

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Beachten Sie, dass Dateiprüfungen nur dazu verwendet werden sollten, die Erstellung von Zeichen zu unterbinden, die von der Azure-Dateisynchronisierung nicht unterstützt werden. Wenn Dateiprüfungen in anderen Szenarios verwendet werden, wird bei der Synchronisierung fortlaufend versucht, die Dateien von der Azure-Dateifreigabe auf dem Server herunterzuladen. Die Synchronisierung wird aufgrund der Dateiprüfung dann gesperrt, wodurch ein hohes Volumen an ausgehenden Daten entsteht. 

## <a name="cloud-tiering"></a>Cloudtiering 
Es gibt beim Cloudtiering zwei Fehlerpfade:

- Für Dateien können Tieringfehler auftreten. Dies bedeutet, dass die Azure-Dateisynchronisierung erfolglos versucht, für eine Datei in Azure Files das Tiering durchzuführen.
- Für Dateien können Fehler beim Rückruf auftreten. Dies bedeutet, dass der Azure-Dateisynchronisierungs-Dateisystemfilter (StorageSync.sys) keine Daten herunterlädt, wenn ein Benutzer auf eine Datei zugreifen möchte, für die das Tiering durchgeführt wurde.

Es gibt zwei Hauptklassen von Fehlern, die für jeden Fehlerpfad auftreten können:

- Cloudspeicherfehler
    - *Vorübergehende Probleme mit der Speicherdienstverfügbarkeit*: Weitere Informationen finden Sie unter [SLA für Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Nicht zugängliche Azure-Dateifreigabe*: Dieser Fehler tritt normalerweise auf, wenn Sie die Azure-Dateifreigabe löschen und es sich dabei noch um einen Cloudendpunkt in einer Synchronisierungsgruppe handelt.
    - *Nicht zugängliches Speicherkonto*: Dieser Fehler tritt normalerweise auf, wenn Sie das Speicherkonto löschen, während es noch über eine Azure-Dateifreigabe verfügt, bei der es sich um einen Cloudendpunkt in einer Synchronisierungsgruppe handelt. 
- Serverfehler 
  - *Azure-Dateisynchronisierungs-Dateisystemfilter (StorageSync.sys) wird nicht geladen.* Der Azure-Dateisynchronisierungs-Dateisystemfilter muss geladen werden, um auf das Tiering bzw. Rückrufanforderungen reagieren zu können. Es kann mehrere Gründe haben, warum der Filter nicht geladen wird. Der häufigste Grund ist, dass das Laden von einem Administrator manuell rückgängig gemacht wurde. Der Azure-Dateisynchronisierungs-Dateisystemfilter muss immer geladen sein, damit die Azure-Dateisynchronisierung richtig funktioniert.
  - *Fehlender, beschädigter oder anderweitig fehlerhafter Analysepunkt*: Ein Analysepunkt ist eine spezielle Datenstruktur in einer Datei, die aus zwei Teilen besteht:
    1. Einer Analysenkennung, die für das Betriebssystem angibt, dass der Azure-Dateisynchronisierungs-Dateisystemfilter (StorageSync.sys) für die Datei unter Umständen Aktionen in Bezug auf E/A-Abläufe durchführen muss. 
    2. Analysedaten, mit denen für den Dateisystemfilter der URI der Datei auf dem zugeordneten Cloudendpunkt (Azure-Dateifreigabe) angegeben wird. 
        
       Am häufigsten kommt es zu einer Beschädigung eines Analysepunkts, wenn ein Administrator versucht, entweder die Kennung oder die dazugehörigen Daten zu ändern. 
  - *Probleme mit der Netzwerkverbindung*: Der Server muss über eine Internetverbindung verfügen, damit für eine Datei das Tiering oder der Rückruf durchgeführt werden kann.

In den folgenden Abschnitten wird beschrieben, wie Sie Probleme mit dem Cloudtiering behandeln und ermitteln, ob ein Problem ein Cloudspeicherproblem oder ein Serverproblem ist.

<a id="monitor-tiering-activity"></a>**Gewusst wie: Überwachen der Tieringaktivität auf einem Server**  
Um die Tieringaktivität auf einem Server zu überwachen, verwenden Sie die Ereignis-IDs 9003, 9016 und 9029 im Telemetrieereignisprotokoll (in der Ereignisanzeige unter „Anwendungen und Dienste\Microsoft\FileSync\Agent“).

- Die Ereignis-ID 9003 ermöglicht die Fehlerverteilung für einen Serverendpunkt. Beispiele: Gesamtfehlerzahl, ErrorCode usw. Beachten Sie, dass ein Ereignis pro Fehlercode protokolliert wird.
- Die Ereignis-ID 9016 stellt Ghostingergebnisse für ein Volume bereit. Beispiele: Freier Speicherplatz in Prozent, Anzahl der Dateien in der Sitzung, für die ein Ghosting durchgeführt wurde, Anzahl von Dateien, bei denen beim Ghosting ein Fehler aufgetreten ist usw.
- Die Ereignis-ID 9029 bietet Informationen zu Ghostingsitzungen für einen Serverendpunkt. Beispiele: Anzahl der in der Sitzung herangezogenen Dateien, Anzahl der Dateien in der Sitzung, für die ein Tiering durchgeführt wurde, Anzahl der Dateien, für die bereits ein Tiering durchgeführt ist usw.

<a id="monitor-recall-activity"></a>**Gewusst wie: Überwachen der Rückrufaktivität auf einem Server**  
Um die Rückrufaktivität auf einem Server zu überwachen, verwenden Sie die Ereignis-IDs 9005, 9006, 9009 und 9059 im Telemetrieereignisprotokoll (in der Ereignisanzeige unter „Anwendungen und Dienste\Microsoft\FileSync\Agent“).

- Die Ereignis-ID 9005 bietet Zuverlässigkeit beim Rückruf für einen Serverendpunkt. Beispiele: Gesamtanzahl eindeutiger Dateien, auf die zugegriffen wird, und Gesamtanzahl eindeutiger Dateien, bei denen beim Zugriff ein Fehler aufgetreten ist.
- Die Ereignis-ID 9006 ermöglicht die Rückruffehlerverteilung für einen Serverendpunkt. Beispiele: Fehlerhafte Anforderungen insgesamt, ErrorCode usw. Beachten Sie, dass ein Ereignis pro Fehlercode protokolliert wird.
- Die Ereignis-ID 9009 bietet Informationen zu Rückrufsitzungen für einen Serverendpunkt. Hierzu zählen beispielsweise DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed usw.
- Die Ereignis-ID 9059 bietet Informationen zur Anwendungsrückrufverteilung für einen Serverendpunkt. Hierzu zählen beispielsweise ShareId, Anwendungsname und TotalEgressNetworkBytes.

<a id="files-fail-tiering"></a>**Beheben von Problemen bei Dateien, bei denen kein Tiering möglich ist**  
Wenn Tieringfehler von Dateien auf Azure Files auftreten:

1. Überprüfen Sie in der Ereignisanzeige die Telemetrie-, Betriebs- und Diagnoseereignisprotokolle unter „Anwendungen und Dienste\Microsoft\FileSync\Agent“. 
   1. Stellen Sie sicher, dass die Dateien in der Azure-Dateifreigabe vorhanden sind.

      > [!NOTE]
      > Eine Datei muss mit einer Azure-Dateifreigabe synchronisiert werden, bevor ein Tiering möglich ist.

   2. Überprüfen Sie, ob der Server über Internetkonnektivität verfügt. 
   3. Überprüfen Sie, ob die Filtertreiber der Azure-Dateisynchronisierung („StorageSync.sys“ und „StorageSyncGuard.sys“) ausgeführt werden:
       - Geben Sie an einer Eingabeaufforderung mit erhöhten Rechten `fltmc` ein. Überprüfen Sie, ob die Dateisystem-Filtertreiber „StorageSync.sys“ und „StorageSyncGuard.sys“ aufgelistet sind.

> [!NOTE]
> Eine Ereignis-ID 9003 wird einmal pro Stunde im Telemetrieereignisprotokoll protokolliert, wenn beim Tiering einer Datei ein Fehler auftritt (pro Fehlercode wird ein Ereignis protokolliert). Die Betriebs- und Diagnoseereignisprotokolle sollten verwendet werden, wenn zusätzliche Informationen zum Diagnostizieren eines Problems benötigt werden.

<a id="files-fail-recall"></a>**Beheben von Rückruffehlern bei Dateien**  
Wenn bei Dateien Rückruffehler auftreten:
1. Überprüfen Sie in der Ereignisanzeige die Telemetrie-, Betriebs- und Diagnoseereignisprotokolle unter „Anwendungen und Dienste\Microsoft\FileSync\Agent“.
    1. Stellen Sie sicher, dass die Dateien in der Azure-Dateifreigabe vorhanden sind.
    2. Überprüfen Sie, ob der Server über Internetkonnektivität verfügt. 
    3. Öffnen Sie das Dienst-MMC-Snap-In, und stellen Sie sicher, dass der Agent für die Speichersynchronisierung (FileSyncSvc) ausgeführt wird.
    4. Überprüfen Sie, ob die Filtertreiber der Azure-Dateisynchronisierung („StorageSync.sys“ und „StorageSyncGuard.sys“) ausgeführt werden:
        - Geben Sie an einer Eingabeaufforderung mit erhöhten Rechten `fltmc` ein. Überprüfen Sie, ob die Dateisystem-Filtertreiber „StorageSync.sys“ und „StorageSyncGuard.sys“ aufgelistet sind.

> [!NOTE]
> Eine Ereignis-ID 9006 wird einmal pro Stunde im Telemetrieereignisprotokoll protokolliert, wenn beim Rückruf einer Datei ein Fehler auftritt (pro Fehlercode wird ein Ereignis protokolliert). Die Betriebs- und Diagnoseereignisprotokolle sollten verwendet werden, wenn zusätzliche Informationen zum Diagnostizieren eines Problems benötigt werden.

<a id="files-unexpectedly-recalled"></a>**Fehlerbehebung bei Dateien, die unerwartet auf einem Server zurückgerufen werden**  
Virenschutz, Sicherung und andere Anwendungen, die viele Dateien lesen, können zu unbeabsichtigten Rückrufen führen, wenn sie das Offline-überspringen-Attribut nicht berücksichtigen und das Lesen des Inhalts dieser Dateien nicht überspringen. Das Überspringen von Offlinedateien für Produkte, die diese Option unterstützen, hilft, unbeabsichtigte Rückrufe während Vorgängen wie Virusscans oder Sicherungsaufträgen zu verhindern.

Wenden Sie sich an den Softwareanbieter, um weitere Informationen zu den erforderlichen Konfigurationsschritten zu erhalten, damit die Lösung das Lesen von Offlinedateien überspringt.

Unbeabsichtigte Rückrufe können auch in anderen Szenarien auftreten, z.B. beim Durchsuchen von Dateien im Datei-Explorer. Das Öffnen eines Ordners, der Dateien mit Cloudtiering enthält, im Datei-Explorer auf dem Server kann zu unbeabsichtigten Rückrufen führen. Die Wahrscheinlichkeit dafür steigt, wenn auf dem Server eine Virenschutzlösung aktiviert ist.

> [!NOTE]
>Verwenden Sie Ereignis-ID 9059 im Telemetrieereignisprotokoll, um zu bestimmen, welche Anwendungen Rückrufe erzeugen. Dieses Ereignis stellt Informationen zur Anwendungsrückrufverteilung für einen Serverendpunkt bereit und wird einmal pro Stunde protokolliert.

## <a name="general-troubleshooting"></a>Allgemeine Problembehandlung
Wenn Probleme mit der Azure-Dateisynchronisierung auf einem Server auftreten, führen Sie zunächst die folgenden Schritte aus:
1. Überprüfen Sie in der Ereignisanzeige die Telemetrie-, Betriebs- und Diagnoseereignisprotokolle.
    - Probleme mit der Synchronisierung, dem Tiering und dem Rückrufen werden in den Telemetrie-, Diagnose- und Betriebsereignisprotokollen unter „Anwendungen und Dienste\Microsoft\FileSync\Agent“ protokolliert.
    - Probleme im Zusammenhang mit der Serververwaltung (z. B. Konfigurationseinstellungen) werden in den Betriebs- und Diagnoseereignisprotokollen unter „Anwendungen“ und „Dienste\Microsoft\FileSync\Management“ protokolliert.
2. Stellen Sie sicher, dass der Azure-Dateisynchronisierungsdienst auf dem Server ausgeführt wird:
    - Öffnen Sie das Dienste-MMC-Snap-In, und stellen Sie sicher, dass der Agent für die Speichersynchronisierung (FileSyncSvc) ausgeführt wird.
3. Überprüfen Sie, ob die Filtertreiber der Azure-Dateisynchronisierung („StorageSync.sys“ und „StorageSyncGuard.sys“) ausgeführt werden:
    - Geben Sie an einer Eingabeaufforderung mit erhöhten Rechten `fltmc` ein. Überprüfen Sie, ob die Dateisystem-Filtertreiber „StorageSync.sys“ und „StorageSyncGuard.sys“ aufgelistet sind.

Wenn das Problem nicht behoben wird, führen Sie das AFSDiag-Tool aus:
1. Erstellen Sie eine Verzeichnis zum Speichern der Ausgabe von AFSDiag (z.B. „C:\Ausgabe“).
2. Öffnen Sie ein PowerShell-Fenster mit erhöhten Rechten, und führen Sie die folgenden Befehle aus (drücken Sie nach jedem Befehl die EINGABETASTE):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Geben Sie für die Kernelmodus-Ablaufverfolgungsebene der Azure-Dateisynchronisierung **1** ein (sofern nicht anders angegeben, um ausführlichere Ablaufverfolgungen zu erstellen), und drücken Sie die EINGABETASTE.
4. Geben Sie für die Benutzermodus-Ablaufverfolgungsebene der Azure-Dateisynchronisierung **1** ein (sofern nicht anders angegeben, um ausführlichere Ablaufverfolgungen zu erstellen), und drücken Sie die EINGABETASTE.
5. Reproduzieren Sie das Problem. Klicken Sie abschließend auf **D**.
6. Eine ZIP-Datei, die Protokolle und Ablaufverfolgungsdateien enthält, wird im angegebenen Ausgabeverzeichnis gespeichert.

## <a name="see-also"></a>Weitere Informationen
- [Überwachen der Azure-Dateisynchronisierung](storage-sync-files-monitoring.md)
- [Häufig gestellte Fragen zu Azure Files](storage-files-faq.md)
- [Behandeln von Azure Files-Problemen unter Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Behandeln von Azure Files-Problemen unter Linux](storage-troubleshoot-linux-file-connection-problems.md)
