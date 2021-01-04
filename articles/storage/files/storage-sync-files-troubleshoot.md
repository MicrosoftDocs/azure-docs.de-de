---
title: Problembehandlung bei der Azure-Dateisynchronisierung | Microsoft-Dokumentation
description: Beheben von häufigen Problemen in einer Bereitstellung in der Azure-Dateisynchronisierung, mit der Sie Windows Server in einen schnellen Cache Ihrer Azure-Dateifreigabe umwandeln können.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 6/12/2020
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: c7405ada800bd5fb9161e9d96bd4c8b0484be620
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005319"
---
# <a name="troubleshoot-azure-file-sync"></a>Problembehandlung für Azure-Dateisynchronisierung
Mit der Azure-Dateisynchronisierung können Sie die Dateifreigaben Ihrer Organisation in Azure Files zentralisieren, ohne auf die Flexibilität, Leistung und Kompatibilität eines lokalen Dateiservers verzichten zu müssen. Mit der Azure-Dateisynchronisierung werden Ihre Windows Server-Computer zu einem schnellen Cache für Ihre Azure-Dateifreigabe. Sie können ein beliebiges Protokoll verwenden, das unter Windows Server verfügbar ist, um lokal auf Ihre Daten zuzugreifen, z.B. SMB, NFS und FTPS. Sie können weltweit so viele Caches wie nötig nutzen.

Dieser Artikel enthält Informationen zur Behebung von Fehlern und Lösung von Problemen, die möglicherweise bei ihrer Bereitstellung der Azure-Dateisynchronisierung auftreten. Wir beschreiben außerdem, wie Sie wichtige Protokolle aus dem System erfassen, wenn eine detailliertere Untersuchung des Problems erforderlich ist. Wenn Sie hier keine Antwort auf Ihre Frage finden, können Sie sich über die folgenden Kanäle an uns wenden (Eskalationsreihenfolge):

1. [Frageseite von Microsoft Q&A (Fragen und Antworten) zu Azure Storage](/answers/products/azure?product=storage)
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

<a id="parameter-is-incorrect"></a>**Beim Zugriff auf ein Volume unter Windows Server 2012 R2 tritt ein Fehler auf: Der Parameter ist falsch.**  
Nachdem Sie einen Serverendpunkt unter Windows Server 2012 R2 erstellt haben, tritt beim Zugriff auf das Volume der folgende Fehler auf:

Auf „Laufwerkbuchstabe:\“ kann nicht zugegriffen werden.  
„Der Parameter ist falsch.“

Installieren Sie zum Beheben des Fehlers [KB2919355](https://support.microsoft.com/help/2919355/windows-rt-8-1-windows-8-1-windows-server-2012-r2-update-april-2014), und starten Sie den Server neu. Wenn dieses Update nicht installiert wird, weil bereits ein späteres Update installiert ist, navigieren Sie zu Windows Update, installieren Sie die neuesten Updates für Windows Server 2012 R2, und starten Sie den Server neu.

<a id="server-registration-missing-subscriptions"></a>**Bei der Serverregistrierung werden nicht alle Azure-Abonnements aufgelistet.**  
Beim Registrieren eines Servers mithilfe von „ServerRegistration.exe“ fehlen Abonnements, wenn Sie auf die Dropdownliste für das Azure-Abonnement klicken.

Dieses Problem tritt auf, da „ServerRegistration.exe“ derzeit keine Umgebungen mit mehreren Mandanten unterstützt. Dieses Problem wird in einem zukünftigen Agent-Update für die Azure-Dateisynchronisierung behoben.

Um dieses Problem zu umgehen, verwenden Sie die folgenden PowerShell-Befehle, um den Server zu registrieren:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**Die Serverregistrierung zeigt die folgende Meldung an: „Voraussetzungen fehlen.“**  
Diese Meldung wird angezeigt, wenn das Az- oder AzureRM-PowerShell-Modul in PowerShell 5.1 nicht installiert ist. 

> [!Note]  
> Die ServerRegistration.exe-Datei unterstützt PowerShell 6.x nicht. Sie können das Register-AzStorageSyncServer-Cmdlet in PowerShell 6.x verwenden, um den Server zu registrieren.

Um das Az- oder AzureRM-Module in PowerShell 5.1 zu installieren, führen Sie die folgenden Schritte aus:

1. Geben Sie an einer Eingabeaufforderung mit erhöhten Rechten die Zeichenfolge **powershell** ein, und drücken Sie die EINGABETASTE.
2. Installieren Sie das neueste Az- oder AzureRM-Modul anhand der folgenden Dokumentation:
    - [Az-Modul (erfordert .NET 4.7.2)](/powershell/azure/install-az-ps?viewFallbackFrom=azps-1.1.0)
    - [AzureRM-Modul]( https://go.microsoft.com/fwlink/?linkid=856959)
3. Führen Sie „ServerRegistration.exe“ aus, und schließen Sie den Assistenten ab, um den Server bei einem Storage-Synchronisierungsdienst zu registrieren.

<a id="server-already-registered"></a>**Die Serverregistrierung zeigt die folgende Meldung an: „Dieser Server ist bereits registriert.“** 

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

<a id="server-registration-missing"></a>**Der Server ist im Azure-Portal nicht unter „Registrierte Server“ aufgeführt.**  
Wenn ein Server für einen Speichersynchronisierungsdienst nicht unter **Registrierte Server** aufgeführt wird:
1. Melden Sie sich bei dem Server an, den Sie registrieren möchten.
2. Öffnen Sie den Datei-Explorer, und wechseln Sie dann zum Installationsverzeichnis des Storage-Synchronisierungs-Agents (der Standardspeicherort ist C:\Programme\Azure\StorageSyncAgent). 
3. Führen Sie „ServerRegistration.exe“ aus, und schließen Sie den Assistenten ab, um den Server bei einem Storage-Synchronisierungsdienst zu registrieren.

## <a name="sync-group-management"></a>Verwaltung von Synchronisierungsgruppen

### <a name="cloud-endpoint-creation-errors"></a>Fehler beim Erstellen von Cloudendpunkten

<a id="cloud-endpoint-using-share"></a>**Fehler beim Erstellen des Cloudendpunkts: „The specified Azure FileShare is already in use by a different CloudEndpoint“ (Die angegebene Azure-Dateifreigabe wird bereits von einem anderen Cloudendpunkt verwendet)**  
Dieser Fehler tritt auf, wenn die Azure-Dateifreigabe bereits von einem anderen Cloudendpunkt verwendet wird. 

Wenn diese Nachricht erscheint und die Azure-Dateifreigabe derzeit von keinem Cloudendpunkt verwendet wird, sollten Sie die folgenden Schritte ausführen, um die Metadaten der Azure-Dateisynchronisierung auf der Azure-Dateifreigabe zu löschen:

> [!Warning]  
> Wenn die Metadaten auf einer Azure-Dateifreigabe gelöscht werden, die derzeit von einem Cloudendpunkt verwendet wird, treten bei Vorgängen der Azure-Dateisynchronisierung Fehler auf. 

1. Navigieren Sie im Azure-Portal zu Ihrer Azure-Dateifreigabe.  
2. Klicken Sie mit der rechten Maustaste auf die Azure-Dateifreigabe, und wählen Sie dann **Metadaten bearbeiten** aus.
3. Klicken Sie mit der rechten Maustaste auf **SyncService**, und wählen Sie dann **Löschen** aus.

<a id="cloud-endpoint-authfailed"></a>**Fehler beim Erstellen des Cloudendpunkts: „AuthorizationFailed“**  
Dieser Fehler tritt auf, wenn Ihr Benutzerkonto nicht über die erforderlichen Rechte verfügt, um einen Cloudendpunkt zu erstellen. 

Für die Erstellung eines Cloudendpunkts muss Ihr Benutzerkonto über die folgenden Microsoft-Autorisierungsberechtigungen verfügen:  
* Lesen: Abrufen der Rollendefinition
* Schreiben: Erstellen oder Aktualisieren von benutzerdefinierten Rollendefinition
* Lesen: Beziehe Rollenzuweisung
* Schreiben: Erstellen von Rollenzuweisungen

Die folgenden integrierten Rollen verfügen über die erforderlichen Microsoft-Autorisierungsberechtigungen:  
* Besitzer
* Benutzerzugriffsadministrator

So bestimmen Sie, ob Ihr Benutzerkonto über die erforderlichen Berechtigungen verfügt:  
1. Wählen Sie im Azure-Portal **Ressourcengruppen** aus.
2. Wählen Sie die Ressourcengruppe aus, in der sich das Speicherkonto befindet, und wählen Sie dann **Zugriffssteuerung (IAM)** aus.
3. Klicken Sie auf die Registerkarte **Rollenzuweisungen**.
4. Wählen Sie die **Rolle** (z.B. Besitzer oder Mitwirkender) für Ihr Benutzerkonto aus.
5. Wählen Sie in der Liste **Ressourcenanbieter** die Option **Microsoft-Autorisierung** aus. 
    * **Rollenzuweisung** muss die Berechtigungen **Lesen** und **Schreiben** aufweisen.
    * **Rollendefinition** muss die Berechtigungen **Lesen** und **Schreiben** aufweisen.

### <a name="server-endpoint-creation-and-deletion-errors"></a>Fehler beim Erstellen und Löschen von Serverendpunkten

<a id="-2134375898"></a>**Fehler beim Erstellen des Serverendpunkts: „MgmtServerJobFailed“ (Fehlercode: -2134375898 oder 0x80c80226)**  
Dieser Fehler tritt auf, wenn sich der Serverendpunktpfad auf dem Systemvolume befindet und Cloudtiering aktiviert ist. Das Cloudtiering wird auf dem Systemvolume nicht unterstützt. Um einen Serverendpunkt auf dem Systemvolume zu erstellen, deaktivieren Sie Cloudtiering, wenn Sie den Serverendpunkt erstellen.

<a id="-2147024894"></a>**Fehler beim Erstellen des Serverendpunkts: „MgmtServerJobFailed“ (Fehlercode: -2147024894 oder 0x80070002)**  
Dieser Fehler tritt auf, wenn der angegebene Pfad zum Serverendpunkt ungültig ist. Vergewissern Sie sich, dass der angegebene Serverendpunktpfad ein lokal verknüpftes NTFS-Volume ist. Beachten Sie, dass die Azure-Dateisynchronisierung keine zugeordneten Laufwerke als Serverendpunktpfad unterstützt.

<a id="-2134375640"></a>**Fehler beim Erstellen des Serverendpunkts: „MgmtServerJobFailed“ (Fehlercode: -2134375640 oder 0x80c80328)**  
Dieser Fehler tritt auf, wenn der angegebene Pfad zum Serverendpunkt kein NTFS-Volume ist. Vergewissern Sie sich, dass der angegebene Serverendpunktpfad ein lokal verknüpftes NTFS-Volume ist. Beachten Sie, dass die Azure-Dateisynchronisierung keine zugeordneten Laufwerke als Serverendpunktpfad unterstützt.

<a id="-2134347507"></a>**Fehler beim Erstellen des Serverendpunkts: „MgmtServerJobFailed“ (Fehlercode: -2134347507 oder 0x80c8710d)**  
Dieser Fehler tritt auf, weil die Azure-Dateisynchronisierung keine Serverendpunkte auf Volumes mit einem komprimierten Systemvolumeinformationen-Ordner unterstützt. Dekomprimieren Sie den Ordner „Systemvolumeinformationen“, um dieses Problem zu beheben. Wenn der Ordner „Systemvolumeinformationen“ der einzige komprimierte Ordner auf dem Volume ist, führen Sie die folgenden Schritte aus:

1. Laden Sie das [PsExec](/sysinternals/downloads/psexec)-Tool herunter.
2. Führen Sie den folgenden Befehl an einer Eingabeaufforderung mit erhöhten Rechten aus, um eine Eingabeaufforderung unter dem Systemkonto zu starten: **PsExec.exe -i -s -d cmd**
3. Geben Sie an der Eingabeaufforderung unter dem Systemkonto die folgenden Befehle ein, und drücken Sie die EINGABETASTE:   
    **cd /d „Laufwerkbuchstabe“:\Systemvolumeinformationen“**  
    **compact /u /s**

<a id="-2134376345"></a>**Fehler beim Erstellen des Serverendpunkts: „MgmtServerJobFailed“ (Fehlercode: -2134376345 oder 0x80C80067)**  
Dieser Fehler tritt auf, wenn das Limit für die Serverendpunkte pro Server erreicht wurde. Die Azure-Dateisynchronisierung unterstützt derzeit bis zu 30 Serverendpunkte pro Server. Weitere Informationen finden Sie unter [Skalierbarkeitsziele für die Azure-Dateisynchronisierung](./storage-files-scale-targets.md#azure-file-sync-scale-targets).

<a id="-2134376427"></a>**Fehler beim Erstellen des Serverendpunkts: „MgmtServerJobFailed“ (Fehlercode: -2134376427 oder 0x80c80015)**  
Dieser Fehler tritt auf, wenn der angegebene Serverendpunktpfad bereits von einem anderen Serverendpunkt synchronisiert wird. Azure-Dateisynchronisierung unterstützt nicht mehrere Serverendpunkte, die dasselbe Verzeichnis oder Volume synchronisieren.

<a id="-2160590967"></a>**Fehler beim Erstellen des Serverendpunkts: „MgmtServerJobFailed“ (Fehlercode: -2160590967 oder 0x80c80077)**  
Dieser Fehler tritt auf, wenn der Pfad zum Serverendpunkt verwaiste mehrstufige Dateien enthält. Wenn ein Serverendpunkt vor Kurzem entfernt wurde, warten Sie, bis der Bereinigungsvorgang für verwaiste mehrstufige Dateien abgeschlossen ist. Im Telemetrieereignisprotokoll wird ein Ereignis mit der ID 6662 protokolliert, sobald der Bereinigungsvorgang für verwaiste mehrstufige Dateien gestartet wurde. Ein Ereignis mit der ID 6661 wird protokolliert, sobald der Bereinigungsvorgang für verwaiste mehrstufige Dateien abgeschlossen ist und ein Serverendpunkt mit der Pfad neu erstellt werden kann. Wenn nach Abschluss der Bereinigung der mehrstufigen Dateien beim Erstellen des Serverendpunkts ein Fehler auftritt oder wenn aufgrund eines Ereignisprotokollrollovers kein Ereignis mit der ID 6661 im Telemetrieereignisprotokoll gefunden werden kann, entfernen Sie die verwaisten mehrstufigen Dateien. Führen Sie hierzu die Schritte aus, die im Abschnitt [Auf Tieringdateien kann nach dem Löschen eines Serverendpunkts nicht zugegriffen werden](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) dokumentiert sind.

<a id="-2134347757"></a>**Fehler beim Löschen des Serverendpunkts: „MgmtServerJobExpired“ (Fehlercode: -2134347757 oder 0x80c87013)**  
Dieser Fehler tritt auf, wenn der Server offline ist oder keine Netzwerkkonnektivität aufweist. Ist der Server nicht mehr verfügbar, heben Sie die Registrierung des Servers im Portal auf, wodurch die Serverendpunkte gelöscht werden. Um die Serverendpunkte zu löschen, führen Sie die Schritte aus, die unter [Aufheben der Registrierung eines Servers mit der Azure-Dateisynchronisierung](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service) beschrieben sind.

### <a name="server-endpoint-health"></a>Integrität der Serverendpunkte

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

Dieses Problem kann auftreten, wenn der Überwachungsprozess für die Speichersynchronisierung (AzureStorageSyncMonitor.exe) nicht ausgeführt wird oder der Server nicht auf den Azure-Dateisynchronisierungsdienst zugreifen kann.

Überprüfen Sie auf dem Server, der im Portal mit „Als offline angezeigt“ angegeben ist, die Ereignis-ID 9301 im Telemetrieereignisprotokoll (unter „Anwendungen und Dienste\Microsoft\FileSync\Agent“ in der Ereignisanzeige), um zu ermitteln, warum der Server nicht auf den Azure-Dateisynchronisierungsdienst zugreifen kann. 

- Wenn **GetNextJob abgeschlossen mit dem Status: 0** protokolliert ist, kann der Server mit dem Azure-Dateisynchronisierungsdienst kommunizieren. 
    - Öffnen Sie den Task-Manager auf dem Server und überprüfen Sie, ob der Überwachungsprozess für die Speichersynchronisierung (AzureStorageSyncMonitor.exe) ausgeführt wird. Wenn der Prozess nicht ausgeführt wird, versuchen Sie zunächst, den Server neu zu starten. Wenn der Neustart des Servers das Problem nicht behebt, führen Sie ein Upgrade auf die [neueste Version](./storage-files-release-notes.md) des Azure-Dateisynchronisierungs-Agents aus. 

- Wenn **GetNextJob abgeschlossen mit dem Status: -2134347756** protokolliert ist, kann der Server aufgrund einer Firewall oder eines Proxys nicht mit dem Azure-Dateisynchronisierungsdienst kommunizieren. 
    - Wenn sich der Server hinter einer Firewall befindet, überprüfen Sie, ob Port 443 für ausgehenden Datenverkehr zulässig ist. Wenn die Firewall den Datenverkehr auf bestimmte Domänen einschränkt, bestätigen Sie, dass die in der Firewall-[Dokumentation](./storage-sync-files-firewall-and-proxy.md#firewall) aufgeführten Domänen zugänglich sind.
    - Wenn sich der Server hinter einem Proxy befindet, konfigurieren Sie die computerweiten oder App-spezifischen Proxyeinstellungen, indem Sie den Schritten in der Proxy-[Dokumentation](./storage-sync-files-firewall-and-proxy.md#proxy) folgen.
    - Verwenden Sie das Test-Cmdlet „StorageSyncNetworkConnectivity“ zum Überprüfen der Netzwerkkonnektivität mit den Dienstendpunkten. Wenn Sie weitere Informationen benötigen, lesen Sie [Testen der Netzwerkkonnektivität mit Dienstendpunkten](./storage-sync-files-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints).

- Wenn **GetNextJob abgeschlossen mit dem Status: -2134347764** protokolliert ist, kann der Server aufgrund eines abgelaufenen oder gelöschten Zertifikats nicht mit dem Azure-Dateisynchronisierungsdienst kommunizieren.  
    - Führen Sie den folgenden PowerShell-Befehl auf dem Server aus, um das für die Authentifizierung verwendete Zertifikat zurückzusetzen:
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**Serverendpunkt hat den Integritätsstatus „Keine Aktivität“, und der Serverstatus auf dem Blatt mit den registrierten Servern lautet „Online“**  

Ein Integritätsstatus „Keine Aktivität“ des Serverendpunkts bedeutet, dass der Serverendpunkt innerhalb der letzten zwei Stunden keine Synchronisierungsaktivität protokolliert hat.

Um die aktuelle Synchronisierungsaktivität auf einem Server zu überprüfen, lesen Sie [Wie überwache ich den Fortschritt einer aktuellen Synchronisierungssitzung?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

Möglicherweise protokolliert ein Serverendpunkt aufgrund eines Fehlers oder unzureichender Systemressourcen mehrere Stunden lang keine Synchronisierungsaktivität. Überprüfen Sie, ob die neueste [Version des Azure-Dateisynchronisierungs-Agents](./storage-files-release-notes.md) installiert ist. Wenn das Problem weiterhin auftritt, öffnen Sie eine Supportanfrage.

> [!Note]  
> Wenn der Serverstatus auf dem Blatt mit den registrierten Servern „Als Offline angezeigt" lautet, führen Sie die im Abschnitt [Serverendpunkt hat den Integritätsstatus „Keine Aktivität“ oder „Ausstehend“, und der Serverstatus auf dem Blatt mit den registrierten Servern lautet „Als offline angezeigt“](#server-endpoint-noactivity) aufgeführten Schritte aus.

## <a name="sync"></a>Synchronisierung
<a id="afs-change-detection"></a>**Wie lange dauert es, bis eine Datei auf Servern in der Synchronisierungsgruppe synchronisiert wird, wenn ich die Datei direkt auf meiner Azure-Dateifreigabe mithilfe von SMB oder über das Portal erstellt habe?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**Die Serverendpunktintegrität befindet sich seit mehreren Stunden im Status „Ausstehend“.**  
Dieses Problem ist zu erwarten, wenn Sie einen Cloudendpunkt erstellen und eine Azure-Dateifreigabe verwenden, die Daten enthält. Der Änderungsenumerationsauftrag, der auf Änderungen in der Azure-Dateifreigabe prüft, muss abgeschlossen sein, bevor Dateien zwischen der Cloud und den Serverendpunkten synchronisiert werden können. Der Zeitaufwand für die Auftragsausführung hängt von der Größe des Namespace in der Azure-Dateifreigabe ab. Sobald der Änderungsenumerationsauftrag abgeschlossen ist, sollte die Serverendpunktintegrität aktualisiert werden.

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>Wie überwache ich die Integrität der Synchronisierung?
# <a name="portal"></a>[Portal](#tab/portal1)
In den einzelnen Synchronisierungsgruppen können Sie ein Drilldown auf die jeweiligen Serverendpunkte ausführen, um den Status der zuletzt abgeschlossenen Synchronisierungssitzungen anzuzeigen. Ein grünes Symbol in der Spalte „Integrität“ und der Wert „0“ unter „Dateien ohne Synchronisierung“ geben an, dass die Synchronisierung wie erwartet funktioniert. Wenn dies nicht der Fall sein sollte, sehen Sie sich nachfolgend die Liste mit allgemeinen Synchronisierungsfehlern und die Informationen an, wie Sie Dateien, die nicht synchronisiert wurden, behandeln. 

![Ein Screenshot des Azure-Portals](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[Server](#tab/server)
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
# <a name="portal"></a>[Portal](#tab/portal1)
Navigieren Sie in Ihrer Synchronisierungsgruppe zum betreffenden Serverendpunkt und dann zum Abschnitt „Synchronisierungsaktivität“. Dort wird die Anzahl der Dateien angezeigt, die Sie in der aktuellen Synchronisierungssitzung hoch- oder heruntergeladen haben. Beachten Sie, dass dieser Status mit einer Verzögerung von ca. 5 Minuten angezeigt wird. Wenn Ihre Synchronisierungssitzung so kurz ist, dass sie innerhalb dieses Zeitraums abgeschlossen wird, wird sie eventuell nicht im Portal gemeldet. 

# <a name="server"></a>[Server](#tab/server)
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
# <a name="portal"></a>[Portal](#tab/portal1)
Stellen Sie bei jedem Server in einer bestimmten Synchronisierungsgruppe Folgendes sicher:
- Die Zeitstempel für „Letzter Synchronisierungsversuch“ für Uploads und Downloads sind aktuell.
- Der Status ist sowohl für Uploads als auch für Downloads grün.
- Das Feld „Synchronisierungsaktivität“ zeigt, dass nur sehr wenige oder keine Dateien mehr synchronisiert werden müssen.
- Das Feld „Dateien ohne Synchronisierung“ ist für Uploads und Downloads „0“.

# <a name="server"></a>[Server](#tab/server)
Sehen Sie sich die durchgeführten Synchronisierungssitzungen an, die von 9102-Ereignissen im Telemetrieereignisprotokoll für die einzelnen Server gekennzeichnet sind (in der Ereignisanzeige unter `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`). 

1. Auf dem betreffenden Server sollten Sie sicherstellen, dass die neuesten Upload- und Downloadsitzungen erfolgreich abgeschlossen wurden. Überprüfen Sie hierfür, ob das HResult und die PerItemErrorCount für Uploads und Downloads „0“ lauten (das Feld „SyncDirection“ gibt an, ob es sich bei einer Sitzung um eine Upload- oder eine Downloadsitzung handelt). Hinweis: Wenn eine kürzlich abgeschlossene Synchronisierungssitzung nicht angezeigt wird, wird aller Wahrscheinlichkeit nach momentan eine Synchronisierungssitzung ausgeführt. Dies ist normal, wenn Sie gerade eine große Menge von Daten hinzugefügt oder geändert haben.
2. Wenn ein Server vollständig mit der Cloud synchronisiert ist und es in beiden Richtungen keine zu synchronisierenden Änderungen gibt, werden leere Synchronisierungssitzungen angezeigt. Diese werden durch Upload- und Downloadereignisse angegeben, bei denen alle Felder, die mit „Sync*“ beginnen (SyncFileCount, SyncDirCount, SyncTombstoneCount und SyncSizeBytes), NULL sind. Dies bedeutet, dass es keine zu synchronisierenden Änderungen gab. Beachten Sie, dass diese leeren Synchronisierungssitzungen möglicherweise nicht auf Servern mit hohem Änderungsumfang angezeigt werden, da es laufend zu synchronisierende Änderungen gibt. Liegt keine Synchronisierungsaktivität vor, sollte sie alle 30 Minuten ausgeführt werden. 
3. Wenn alle Server mit der Cloud synchronisiert wurden (d.h., die aktuellen Upload- und Downloadsitzungen sind leere Synchronisierungssitzungen), können Sie mit hoher Wahrscheinlichkeit davon ausgehen, dass das gesamte System synchron ist. 
    
Beachten Sie, dass die Azure-Dateisynchronisierung Änderungen, die Sie direkt in Ihrer Azure-Dateifreigabe vorgenommen haben, erst erkennt, wenn die Änderungsenumeration ausgeführt wird (tritt einmal alle 24 Stunden auf). Es kann vorkommen, dass ein Server als mit der Cloud synchron angegeben wird, obwohl in Wirklichkeit in letzter Zeit keine Änderungen direkt in der Azure-Dateifreigabe vorgenommen wurden. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Woran erkenne ich, dass bestimmte Dateien oder Ordnern nicht synchronisiert wurden?
Wenn im Portal die PerItemErrorCount auf dem Server oder „Dateien ohne Synchronisierung“ für eine bestimmte Synchronisierungssitzung einen größeren Wert als „0“ enthält, bedeutet dies, dass bei einigen Elementen ein Synchronisierungsfehler aufgetreten ist. Dateien und Ordner können Merkmale aufweisen, die eine Synchronisierung verhindern. Diese Merkmale können dauerhaft sein und eine explizite Aktion zum Fortsetzen der Synchronisierung erfordern, z.B. das Entfernen von nicht unterstützten Zeichen aus einem Datei- oder Ordnernamen. Sie können auch vorübergehend sein, d.h., die Synchronisierung der Datei oder des Ordners wird automatisch fortgesetzt. Beispielsweise wird die Synchronisierung von Dateien mit offenen Handles automatisch fortgesetzt, wenn die Datei geschlossen ist. Wenn die Azure-Dateisynchronisierungs-Engine ein solches Problem erkennt, wird ein Fehlerprotokoll erstellt, das für die Auflistung der Elemente, die derzeit nicht ordnungsgemäß synchronisiert werden, analysiert werden kann.

Um diese Fehler anzuzeigen, führen Sie das PowerShell-Skript **FileSyncErrorsReport.ps1** aus (im Agent-Installationsverzeichnis des Azure-Dateisynchronisierungs-Agents), um Dateien zu identifizieren, bei denen die Synchronisierung aufgrund von offenen Handles, nicht unterstützten Zeichen oder anderen Problemen zu Fehlern führte. Das ItemPath-Feld gibt den Speicherort der Datei in Bezug auf das Stammverzeichnis für die Synchronisierung an. Eine Liste von allgemeinen Synchronisierungsfehlern mit Schritten zur Fehlerbehebung finden Sie weiter unten.

> [!Note]  
> Wenn das Skript „ FileSyncErrorsReport.ps1“ die Meldung „Es wurden keine Dateifehler gefunden“ zurückgibt oder darin keine Fehler auf Elementebene für die Synchronisierungsgruppe aufgelistet werden, ist die Ursache eine der folgenden:
>
>- Ursache 1: Bei der letzten abgeschlossenen Synchronisierungssitzung gab es keine Fehler auf Elementebene. Das Portal sollte bald aktualisiert werden, damit „0 Dateien ohne Synchronisierung“ angezeigt wird. 
>    - Überprüfen Sie die [Ereignis-ID 9102](?tabs=server%252cazure-portal#broken-sync) im Telemetrieereignisprotokoll, um zu bestätigen, dass „PerItemErrorCount“ gleich „0“ ist. 
>
>- Ursache 2: Das Ereignisprotokoll „ItemResults“ wurde auf dem Server aufgrund von zu vielen Fehlern auf Elementebene überschrieben, und das Protokoll enthält keine Fehler mehr für diese Synchronisierungsgruppe.
>    - Zur Verhinderung dieses Problems vergrößern Sie das Ereignisprotokoll „ItemResults“. Das Ereignisprotokoll „ItemResults“ ist in der Ereignisanzeige unter „Applications and Services Logs\Microsoft\FileSync\Agent“ zu finden. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Behandlung von Synchronisierungsfehlern nach Dateien und Verzeichnissen
**ItemResults-Protokoll – Synchronisierungsfehler nach Element**  

| HRESULT | HRESULT (dezimal) | Fehlerzeichenfolge | Problem | Wiederherstellung |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | Auf die mehrstufige Datei auf dem Server kann nicht zugegriffen werden. Dieses Problem tritt auf, wenn die mehrstufige Datei vor dem Löschen eines Serverendpunkts nicht zurückgerufen wurde. | Informationen zur Behebung dieses Problems finden Sie unter [Auf Tieringdateien kann nach dem Löschen eines Serverendpunkts nicht zugegriffen werden](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | Die Datei- oder Verzeichnisänderung kann noch nicht synchronisiert werden, da ein abhängiger Ordner noch nicht synchronisiert ist. Dieses Element wird synchronisiert, sobald die abhängigen Änderungen synchronisiert wurden. | Keine weiteren Maßnahmen erforderlich. Wenn der Fehler mehrere Tage auftritt, ermitteln Sie mithilfe des PowerShell-Skripts „FileSyncErrorsReport.ps1“, weshalb der abhängige Ordner noch nicht synchronisiert wurde. |
| 0x80C8028A | -2134375798 | ECS_E_SYNC_CONSTRAINT_CONFLICT_ON_FAILED_DEPENDEE | Die Datei- oder Verzeichnisänderung kann noch nicht synchronisiert werden, da ein abhängiger Ordner noch nicht synchronisiert ist. Dieses Element wird synchronisiert, sobald die abhängigen Änderungen synchronisiert wurden. | Keine weiteren Maßnahmen erforderlich. Wenn der Fehler mehrere Tage auftritt, ermitteln Sie mithilfe des PowerShell-Skripts „FileSyncErrorsReport.ps1“, weshalb der abhängige Ordner noch nicht synchronisiert wurde. |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | Die Datei- oder Verzeichnisänderung kann noch nicht synchronisiert werden, da ein abhängiger Ordner noch nicht synchronisiert ist und ein Fehler bei der Synchronisierungssitzung auftrat. Dieses Element wird synchronisiert, sobald die abhängigen Änderungen synchronisiert wurden. | Keine weiteren Maßnahmen erforderlich. Wenn der Fehler weiterhin auftritt, untersuchen Sie den Fehler in der Synchronisierungssitzung. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | Der Datei- oder Verzeichnisname ist ungültig. | Benennen Sie die jeweiligen Datei oder das betreffende Verzeichnis um. Weitere Informationen finden Sie unter [Behandlung von nicht unterstützten Zeichen](?tabs=portal1%252cazure-portal#handling-unsupported-characters). |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | Der Datei- oder Verzeichnisname ist ungültig. | Benennen Sie die jeweiligen Datei oder das betreffende Verzeichnis um. Weitere Informationen finden Sie unter [Behandlung von nicht unterstützten Zeichen](?tabs=portal1%252cazure-portal#handling-unsupported-characters). |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Die Datei kann nicht synchronisiert werden, da sie momentan verwendet wird. Die Datei wird synchronisiert, wenn sie nicht mehr verwendet wird. | Keine weiteren Maßnahmen erforderlich. Die Azure-Dateisynchronisierung erstellt einmal pro Tag eine temporäre VSS-Momentaufnahme auf dem Server, um Dateien mit offenen Handles zu synchronisieren. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Die Datei wurde geändert, doch die Änderung wurde noch nicht von der Synchronisierung erkannt. Die Synchronisierung wird aktualisiert, sobald diese Änderung erkannt wurde. | Keine weiteren Maßnahmen erforderlich. |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | Die Datei wurde gelöscht, und die Synchronisierung erkennt die Änderung nicht. | Keine weiteren Maßnahmen erforderlich. Die Synchronisierung protokolliert diesen Fehler nicht mehr, sobald die Änderungserkennung erkennt, dass die Datei gelöscht wurde. |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | Das Löschen einer Datei oder eines Verzeichnisses kann nicht synchronisiert werden, da das Element bereits im Ziel gelöscht wurde und die Synchronisierung die Änderung nicht erkennt. | Keine weiteren Maßnahmen erforderlich. Die Synchronisierung protokolliert diesen Fehler nicht mehr, sobald die Änderungserkennung auf dem Ziel ausgeführt wird und die Synchronisierung erkennt, dass das Element gelöscht wurde. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | Die Datei oder das Verzeichnis wurde übersprungen, wird aber in der nächsten Synchronisierungssitzung synchronisiert. Wenn dieser Fehler beim Herunterladen des Elements gemeldet wird, ist höchstwahrscheinlich der Datei- oder Verzeichnisname ungültig. | Es ist keine Aktion erforderlich, wenn dieser Fehler beim Hochladen der Datei gemeldet wird. Wird der Fehler beim Herunterladen der Datei gemeldet, benennen Sie die betreffende Datei oder das betreffende Verzeichnis um. Weitere Informationen finden Sie unter [Behandlung von nicht unterstützten Zeichen](?tabs=portal1%252cazure-portal#handling-unsupported-characters). |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | Die Erstellung einer Datei oder eines Verzeichnisses kann nicht synchronisiert werden, da das Element bereits im Ziel vorhanden ist und die Synchronisierung die Änderung nicht erkennt. | Keine weiteren Maßnahmen erforderlich. Die Synchronisierung protokolliert diesen Fehler nicht mehr, sobald die Änderungserkennung auf dem Ziel ausgeführt wird und die Synchronisierung dieses neue Element erkennt. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Die Datei kann nicht synchronisiert werden, da das Limit für die Azure-Dateifreigabe erreicht ist. | Um dieses Problem zu beheben, lesen Sie den Abschnitt [Sie haben das Speicherlimit für die Azure-Dateifreigabe erreicht](?tabs=portal1%252cazure-portal#-2134351810) in diesem Leitfaden zur Problembehandlung. |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | Die Datei ist durch eine nicht unterstützte Lösung (z.B. NTFS EFS) verschlüsselt. | Entschlüsseln Sie die Datei, und verwenden Sie eine unterstützte Verschlüsselungslösung. Eine Liste der unterstützten Lösungen finden Sie im Abschnitt [Verschlüsselungslösungen](./storage-sync-files-planning.md#encryption) im Planungshandbuch. |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | Die Datei befindet sich in einem schreibgeschützten DFS-R-Replikationsordner. | Die Datei befindet sich in einem schreibgeschützten DFS-R-Replikationsordner. Die Azure-Dateisynchronisierung unterstützt keine Serverendpunkte in schreibgeschützten DFS-R-Replikationsordnern. Weitere Informationen finden Sie im [Planungshandbuch](./storage-sync-files-planning.md#distributed-file-system-dfs). |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Die Datei weist einen ausstehenden Löschzustand auf. | Keine weiteren Maßnahmen erforderlich. Die Datei wird gelöscht, sobald alle offenen Dateihandles geschlossen sind. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | Die Datei kann nicht synchronisiert werden, weil die Einstellungen für Firewall und virtuelles Netzwerk im Speicherkonto aktiviert sind und der Server keinen Zugriff auf das Speicherkonto hat. | Fügen Sie die Server-IP-Adresse oder das virtuelle Netzwerk hinzu, indem Sie die im Abschnitt [Konfigurieren der Einstellung für Firewall und virtuelles Netzwerk](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) des Bereitstellungsleitfadens angegebenen Schritte ausführen. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | Die Datei kann nicht synchronisiert werden, weil die Größe der Sicherheitsbeschreibung das Limit von 64 KiB überschreitet. | Um dieses Problem zu beheben, entfernen Sie Zugriffssteuerungseinträge (ACEs) für die Datei, um die Größe der Sicherheitsbeschreibung zu verringern. |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | Die Datei kann aufgrund eines unerwarteten Fehlers nicht synchronisiert werden. | Wenn der Fehler mehrere Tage lang besteht, erstellen Sie eine Supportanfrage. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | Die Datei kann nicht synchronisiert werden, da sie momentan verwendet wird. Die Datei wird synchronisiert, wenn sie nicht mehr verwendet wird. | Keine weiteren Maßnahmen erforderlich. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Die Datei wurde während der Synchronisierung geändert, deshalb muss sie erneut synchronisiert werden. | Keine weiteren Maßnahmen erforderlich. |
| 0x80070017 | -2147024873 | ERROR_CRC | Die Datei kann aufgrund eines CRC-Fehlers nicht synchronisiert werden. Dieser Fehler kann auftreten, wenn eine Tieringdatei vor dem Löschen eines Serverendpunkts nicht abgerufen wurde oder wenn die Datei beschädigt ist. | Zur Behebung dieses Problems informieren Sie sich unter [Auf Tieringdateien kann nach dem Löschen eines Serverendpunkts nicht zugegriffen werden](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint), wie Sie verwaiste Tieringdateien entfernen. Sollte der Fehler nach dem Entfernen verwaister Tieringdateien weiterhin auftreten, führen Sie [chkdsk](/windows-server/administration/windows-commands/chkdsk) auf dem Volume aus. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | Die Datei kann nicht synchronisiert werden, da die maximale Anzahl von Konfliktdateien erreicht wurde. Die Azure-Dateisynchronisierung unterstützt 100 Konfliktdateien pro Datei. Weitere Informationen zu Dateikonflikten finden Sie unter den [Häufig gestellten Fragen (FAQ)](./storage-files-faq.md#afs-conflict-resolution) zur Azure-Dateisynchronisierung. | Um dieses Problem zu beheben, reduzieren Sie die Anzahl der Konfliktdateien. Die Datei wird synchronisiert, sobald die Anzahl der Konfliktdateien weniger als 100 beträgt. |

#### <a name="handling-unsupported-characters"></a>Behandlung von nicht unterstützten Zeichen
Wenn das PowerShell-Skript **FileSyncErrorsReport.ps1** Synchronisierungsfehler pro Element aufgrund von nicht unterstützten Zeichen (Fehlercode 0x8007007b oder 0x80c80255) anzeigt, sollten Sie diese Zeichen aus den entsprechenden Dateinamen entfernen oder darin ändern. PowerShell gibt diese Zeichen wahrscheinlich als Fragezeichen oder leere Rechtecke aus, da die meisten dieser Zeichen keine standardisierte visuelle Codierung aufweisen. 
> [!Note]  
> Mit dem [Auswertungstool](storage-sync-files-planning.md#evaluation-cmdlet) können Sie nicht unterstützte Zeichen identifizieren. Wenn das Dataset mehrere Dateien mit ungültigen Zeichen enthält, verwenden Sie das Skript [ScanUnsupportedChars](https://github.com/Azure-Samples/azure-files-samples/tree/master/ScanUnsupportedChars), um Dateien umzubenennen, die nicht unterstützte Zeichen enthalten.

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

Es ist keine Aktion erforderlich. Der Server wiederholt den Vorgang. Wenn dieser Fehler mehrere Stunden anhält, erstellen Sie eine Supportanfrage.

<a id="-2134364043"></a>**Synchronisierung wird blockiert, bis die Änderungserkennung nach der Wiederherstellung abgeschlossen ist**  

| | |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (dezimal)** | -2134364043 |
| **Fehlerzeichenfolge** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Korrektur erforderlich** | Nein |

Keine Aktion erforderlich. Wenn eine Datei oder Dateifreigabe (Cloudendpunkt) mithilfe von Azure Backup wiederhergestellt wird, wird die Synchronisierung so lange blockiert, bis die Änderungserkennung auf der Azure-Dateifreigabe abgeschlossen ist. Unmittelbar nach Abschluss der Wiederherstellung wird die Änderungserkennung ausgeführt, deren Dauer auf der Anzahl der Dateien in der Dateifreigabe basiert.

<a id="-2147216747"></a>**Fehler bei der Synchronisierung, weil die Synchronisierungsdatenbank entladen wurde.**  

| | |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT (dezimal)** | -2147216747 |
| **Fehlerzeichenfolge** | SYNC_E_METADATA_INVALID_OPERATION |
| **Korrektur erforderlich** | Nein |

Dieser Fehler tritt normalerweise auf, wenn eine Sicherungsanwendung eine VSS-Momentaufnahme erstellt, und die Synchronisierungsdatenbank entladen wird. Wenn dieser Fehler mehrere Stunden anhält, erstellen Sie eine Supportanfrage.

<a id="-2134364065"></a>**Die Synchronisierung kann nicht auf die im Cloudendpunkt angegebene Azure-Dateifreigabe zugreifen.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (dezimal)** | -2134364065 |
| **Fehlerzeichenfolge** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Korrektur erforderlich** | Ja |

Dieser Fehler tritt auf, da der Azure-Dateisynchronisierungs-Agent nicht auf die Azure-Dateifreigabe zugreifen kann. Dies ist möglicherweise darauf zurückzuführen, dass die Azure-Dateifreigabe oder das Speicherkontohosting nicht mehr vorhanden ist. Sie können diesen Fehler beheben, indem Sie die folgenden Schritte durchführen:

1. [Überprüfen Sie, ob das Speicherkonto vorhanden ist.](#troubleshoot-storage-account)
2. [Stellen Sie sicher, dass die Azure-Dateifreigabe vorhanden ist.](#troubleshoot-azure-file-share)
3. [Stellen Sie sicher, dass die Azure-Dateisynchronisierung über Zugriff auf das Speicherkonto verfügt.](#troubleshoot-rbac)
4. [Überprüfen Sie, ob die Einstellungen für die Firewall und das virtuelle Netzwerk im Speicherkonto ordnungsgemäß konfiguriert sind (sofern aktiviert).](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**Fehler bei der Synchronisierung, weil die Anforderung nicht berechtigt zum Ausführen dieses Vorgangs ist.**  

| | |
|-|-|
| **HRESULT** | 0x80c86044 |
| **HRESULT (dezimal)** | -2134351804 |
| **Fehlerzeichenfolge** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **Korrektur erforderlich** | Ja |

Dieser Fehler tritt auf, weil der Azure-Dateisynchronisierungs-Agent nicht berechtigt für den Zugriff auf die Azure-Dateifreigabe ist. Sie können diesen Fehler beheben, indem Sie die folgenden Schritte durchführen:

1. [Überprüfen Sie, ob das Speicherkonto vorhanden ist.](#troubleshoot-storage-account)
2. [Stellen Sie sicher, dass die Azure-Dateifreigabe vorhanden ist.](#troubleshoot-azure-file-share)
3. [Überprüfen Sie, ob die Einstellungen für die Firewall und das virtuelle Netzwerk im Speicherkonto ordnungsgemäß konfiguriert sind (sofern aktiviert).](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
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
3. [Überprüfen Sie, ob die Einstellungen für die Firewall und das virtuelle Netzwerk im Speicherkonto ordnungsgemäß konfiguriert sind (sofern aktiviert).](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Unbekannter Fehler beim Zugriff auf das Speicherkonto.**  

| | |
|-|-|
| **HRESULT** | 0x80c8308a |
| **HRESULT (dezimal)** | -2134364022 |
| **Fehlerzeichenfolge** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Korrektur erforderlich** | Ja |

1. [Überprüfen Sie, ob das Speicherkonto vorhanden ist.](#troubleshoot-storage-account)
2. [Überprüfen Sie, ob die Einstellungen für die Firewall und das virtuelle Netzwerk im Speicherkonto ordnungsgemäß konfiguriert sind (sofern aktiviert).](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**Fehler bei der Synchronisierung aufgrund eines gesperrten Speicherkontos.**  

| | |
|-|-|
| **HRESULT** | 0x80c83092 |
| **HRESULT (dezimal)** | -2134364014 |
| **Fehlerzeichenfolge** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **Korrektur erforderlich** | Ja |

Dieser Fehler tritt auf, da für das Speicherkonto eine schreibgeschützte [Ressourcensperre](../../azure-resource-manager/management/lock-resources.md) gilt. Um dieses Problem zu beheben, heben Sie die schreibgeschützte Ressourcensperre für das Speicherkonto auf. 

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

Dieser Fehler tritt auf, wenn das Azure-Abonnement ausgesetzt ist. Die Synchronisierung wird erneut aktiviert, wenn das Azure-Abonnement wiederhergestellt wurde. Weitere Informationen finden Sie unter [Warum ist mein Azure-Abonnement deaktiviert, und wie reaktiviere ich es?](../../cost-management-billing/manage/subscription-disabled.md).

<a id="-2134375618"></a>**Für das Speicherkonto wurden eine Firewall oder virtuelle Netzwerke konfiguriert.**  

| | |
|-|-|
| **HRESULT** | 0x80c8033e |
| **HRESULT (dezimal)** | -2134375618 |
| **Fehlerzeichenfolge** | ECS_E_SERVER_BLOCKED_BY_NETWORK_ACL |
| **Korrektur erforderlich** | Ja |

Dieser Fehler tritt auf, wenn aufgrund einer Firewall oder der Zugehörigkeit des Speicherkontos zu einem virtuellen Netzwerk nicht auf die Azure-Dateifreigabe zugegriffen werden kann. Überprüfen Sie, ob die Einstellungen für die Firewall und das virtuelle Netzwerk im Speicherkonto ordnungsgemäß konfiguriert sind. Weitere Informationen finden Sie unter [Konfigurieren der Einstellungen für die Firewall und das virtuelle Netzwerk](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings). 

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

Dieser Fehler kann vorkommen, wenn Ihre Organisation einen TLS-Terminierungsproxy verwendet oder eine schädliche Entität den Datenverkehr zwischen Ihrem Server und der Azure-Dateisynchronisierung abfängt. Wenn Sie sicher sind, dass dies normal ist (da Ihre Organisation einen TLS-Terminierungsproxy verwendet), überspringen Sie die Zertifikatüberprüfung durch eine Außerkraftsetzung der Registrierung.

1. Erstellen Sie den Registrierungswert „SkipVerifyingPinnedRootCertificate“.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Starten Sie den Synchronisierungsdienst auf dem registrierten Server neu.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Durch das Festlegen dieses Registrierungswerts akzeptiert der Azure-Dateisynchronisierungs-Agent alle vertrauenswürdigen lokalen TLS/SSL-Zertifikate, wenn Daten zwischen dem Server und dem Clouddienst übertragen werden.

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

Dieser Fehler tritt normalerweise auf, weil die Serverzeit falsch ist. Wenn der Server auf einem virtuellen Computer ausgeführt wird, überprüfen Sie, ob die Uhrzeit auf dem Host richtig ist.

<a id="-2134364040"></a>**Fehler bei der Synchronisierung aufgrund eines abgelaufenen Zertifikats.**  

| | |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT (dezimal)** | -2134364040 |
| **Fehlerzeichenfolge** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Korrektur erforderlich** | Ja |

Dieser Fehler tritt auf, weil das für die Authentifizierung verwendete Zertifikat abgelaufen ist.

Um zu überprüfen, ob das Zertifikat abgelaufen ist, führen Sie die folgenden Schritte aus:  
1. Öffnen Sie das Zertifikat-MMC-Snap-In, wählen Sie das Computerkonto aus, und navigieren Sie zu den Zertifikaten: „(Lokaler Computer)\Benutzer\Certificates“.
2. Überprüfen Sie, ob das Clientauthentifizierungszertifikat abgelaufen ist.

Wenn das Clientauthentifizierungszertifikat abgelaufen ist, führen Sie die folgenden Schritte aus, um das Problem zu beheben:

1. Überprüfen Sie, ob die Version 4.0.1.0 oder höher des Azure-Dateisynchronisierungs-Agents installiert ist.
2. Führen Sie dem folgenden PowerShell-Befehl auf dem Server aus:

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**Fehler bei der Synchronisierung, weil das Authentifizierungszertifikat nicht gefunden wurde.**  

| | |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT (dezimal)** | -2134375896 |
| **Fehlerzeichenfolge** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Korrektur erforderlich** | Ja |

Dieser Fehler tritt auf, weil das für die Authentifizierung verwendete Zertifikat nicht gefunden wird.

Führen Sie die folgenden Schritte aus, um das Problem zu beheben:

1. Überprüfen Sie, ob die Version 4.0.1.0 oder höher des Azure-Dateisynchronisierungs-Agents installiert ist.
2. Führen Sie dem folgenden PowerShell-Befehl auf dem Server aus:

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**Fehler bei der Synchronisierung, weil die Authentifizierungsidentität nicht gefunden wurde.**  

| | |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT (dezimal)** | -2134364039 |
| **Fehlerzeichenfolge** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Korrektur erforderlich** | Ja |

Dieser Fehler tritt auf, weil das Löschen des Serverendpunkts fehlgeschlagen ist und sich der Endpunkt nun in einem teilweise gelöschten Zustand befindet. Um dieses Problem zu beheben, versuchen Sie erneut, den Serverendpunkt zu löschen.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Das Volume, auf dem sich der Serverendpunkt befindet, weist nicht genügend Speicherplatz auf dem Datenträger auf.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (dezimal)** | -1906441711 |
| **Fehlerzeichenfolge** | JET_errLogDiskFull |
| **Korrektur erforderlich** | Ja |

| | |
|-|-|
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

Dieser Fehler tritt auf, weil der Cloudendpunkt mit Inhalten erstellt wurde, die bereits in der Azure-Dateifreigabe vorhanden sind. Die Azure-Dateisynchronisierung muss die Azure-Dateifreigabe nach allen Inhalten durchsuchen, bevor der Serverendpunkt mit der Erstsynchronisierung fortfahren kann.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Fehler bei der Synchronisierung aufgrund von Problemen mit einer Vielzahl einzelner Dateien.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (dezimal)** | –2.134.375.877 |
| **Fehlerzeichenfolge** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Korrektur erforderlich** | Ja |

| | |
|-|-|
| **HRESULT** | 0x80c8021c |
| **HRESULT (dezimal)** | -2134375908 |
| **Fehlerzeichenfolge** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Korrektur erforderlich** | Ja |

| | |
|-|-|
| **HRESULT** | 0x80c80253 |
| **HRESULT (dezimal)** | -2134375853 |
| **Fehlerzeichenfolge** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Korrektur erforderlich** | Ja |

Synchronisierungssitzungen scheitern mit einem dieser Fehler, wenn es viele Dateien gibt, die aufgrund von Fehlern auf Elementebene nicht synchronisiert werden können. Führen Sie die im Abschnitt [Woran erkenne ich, dass bestimmte Dateien oder Ordner nicht synchronisiert wurden?](?tabs=portal1%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) dokumentierten Schritte aus, um Fehler auf Elementebene aufzulösen. Für den Synchronisierungsfehler ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED öffnen Sie bitte einen Supportfall.

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

<a id="-2146233088"></a>**Fehler bei der Synchronisierung aufgrund einer Ausnahme.**  

| | |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT (dezimal)** | -2146233088 |
| **Fehlerzeichenfolge** | COR_E_EXCEPTION |
| **Korrektur erforderlich** | Nein |

Dieser Fehler tritt auf, weil die Synchronisierung aufgrund einer Ausnahme fehlgeschlagen ist. Wenn der Fehler mehrere Stunden anhält, erstellen Sie eine Supportanfrage.

<a id="-2134364045"></a>**Fehler bei der Synchronisierung, weil für das Speicherkonto ein Failover in eine andere Region ausgeführt wurde.**  

| | |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT (dezimal)** | -2134364045 |
| **Fehlerzeichenfolge** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Korrektur erforderlich** | Ja |

Dieser Fehler tritt auf, weil für das Speicherkonto ein Failover in eine andere Region durchgeführt wurde. Das Feature „Speicherkontofailover“ wird von der Azure-Dateisynchronisierung nicht unterstützt. Für Speicherkonten, die Azure-Dateifreigaben enthalten, die als Cloud-Endpunkte in der Azure-Dateisynchronisierung verwendet werden, sollte kein Failover durchgeführt werden. Dies würde das Funktionieren der Synchronisierung beenden und könnte außerdem bei neu einbezogenen Dateien zu unerwartetem Datenverlust führen. Um dieses Problem zu beheben, verschieben Sie das Speicherkonto in die primäre Region.

<a id="-2134375922"></a>**Fehler bei der Synchronisierung aufgrund eines vorübergehenden Problems mit der Synchronisierungsdatenbank.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (dezimal)** | -2134375922 |
| **Fehlerzeichenfolge** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Korrektur erforderlich** | Nein |

Dieser Fehler tritt aufgrund eines internen Problems mit der Synchronisierungsdatenbank auf. Dieser Fehler wird automatisch aufgelöst, wenn die Synchronisierung erneut versucht wird. Wenn dieser Fehler für einen längeren Zeitraum anhält, erstellen Sie eine Supportanfrage, und wir kontaktieren Sie, um Sie bei der Problemlösung zu unterstützen.

<a id="-2134364024"></a>**Fehler bei der Synchronisierung aufgrund einer Änderung im Azure Active Directory-Mandanten**  

| | |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT (dezimal)** | -2134364024 | 
| **Fehlerzeichenfolge** | ECS_E_INVALID_AAD_TENANT |
| **Korrektur erforderlich** | Ja |

Stellen Sie sicher, dass Sie über den neuesten Agent für die Azure-Dateisynchronisierung verfügen. Ab Agent-Version 10 unterstützt die Azure-Dateisynchronisierung das Verschieben eines Abonnements in einen anderen Azure Active Directory-Mandanten.
 
Sobald Sie über die neueste Agent-Version verfügen, müssen Sie der Anwendung Microsoft.StorageSync Zugriff auf das Speicherkonto gewähren (weitere Informationen finden Sie unter [Stellen Sie sicher, dass die Azure-Dateisynchronisierung über Zugriff auf das Speicherkonto verfügt](#troubleshoot-rbac)).

<a id="-2134364010"></a>**Fehler bei der Synchronisierung aufgrund einer nicht konfigurierten Ausnahme für Firewall und virtuelles Netzwerk**  

| | |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT (dezimal)** | -2134364010 | 
| **Fehlerzeichenfolge** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Korrektur erforderlich** | Ja |

Dieser Fehler tritt auf, wenn die Einstellungen für Firewall und virtuelles Netzwerk im Speicherkonto aktiviert sind, aber die Ausnahme „Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben“ nicht aktiviert ist. Um dieses Problem zu lösen, führen Sie die im Abschnitt [Konfigurieren der Einstellung für Firewall und virtuelles Netzwerk](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) des Bereitstellungsleitfadens angegebenen Schritte aus.

<a id="-2147024891"></a>**Fehler bei der Synchronisierung, weil die Berechtigungen für den Ordner „Systemvolumeinformationen“ falsch sind.**  

| | |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT (dezimal)** | -2147024891 |
| **Fehlerzeichenfolge** | ERROR_ACCESS_DENIED |
| **Korrektur erforderlich** | Ja |

Dieser Fehler kann auftreten, wenn das Konto NT AUTHORITY\SYSTEM nicht über die Berechtigungen für den Ordner „Systemvolumeinformationen“ auf dem Volume verfügt, auf dem sich der Serverendpunkt befindet. Beachten Sie Folgendes: Wenn einzelne Dateien nicht synchronisiert werden können und der Fehler ERROR_ACCESS_DENIED eintritt, führen Sie die im Abschnitt [Behandlung von Synchronisierungsfehlern nach Dateien und Verzeichnissen](?tabs=portal1%252cazure-portal#troubleshooting-per-filedirectory-sync-errors) dokumentierten Schritte aus.

Führen Sie die folgenden Schritte aus, um das Problem zu beheben:

1. Laden Sie das [Psexec](/sysinternals/downloads/psexec)-Tool herunter.
2. Führen Sie den folgenden Befehl an einer Eingabeaufforderung mit erhöhten Rechten aus, um eine Eingabeaufforderung unter Verwendung des Systemkontos zu starten: **PsExec.exe -i -s -d cmd** 
3. Führen Sie an der Eingabeaufforderung unter dem Systemkonto den folgenden Befehl aus, um zu bestätigen, dass das Konto „NT AUTHORITY\SYSTEM“ keinen Zugriff auf den Ordner „Systemvolumeinformationen“ hat: **cacls „Laufwerkbuchstabe:\Systemvolumeinformationen“ /T /C**.
4. Wenn das Konto „NT AUTHORITY\SYSTEM“ keinen Zugriff auf den Ordner „Systemvolumeinformationen“ hat, führen Sie den folgenden Befehl aus: **cacls „Laufwerkbuchstabe:\Systemvolumeinformationen“ /T /E /G „NT AUTHORITY\SYSTEM:F“** .
    - Wenn Schritt 4 aufgrund von verweigertem Zugriff fehlschlägt, führen Sie den folgenden Befehl aus, um den Besitz des Ordners „Systemvolumeinformationen“ zu übernehmen: **takeown /A /R /F „Laufwerkbuchstabe:\Systemvolumeinformationen“** . Wiederholen Sie dann Schritt 4.

<a id="-2134375810"></a>**Fehler bei der Synchronisierung, weil die Azure-Dateifreigabe gelöscht und neu erstellt wurde.**  

| | |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT (dezimal)** | -2134375810 |
| **Fehlerzeichenfolge** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Korrektur erforderlich** | Ja |

Dieser Fehler tritt auf, weil Azure-Dateisynchronisierung das Löschen und Neuerstellen einer Azure-Dateifreigabe in derselben Synchronisierungsgruppe nicht unterstützt. 

Zur Behebung dieses Problems müssen Sie die Synchronisierungsgruppe löschen und neu erstellen, indem Sie die folgenden Schritte ausführen:

1. Löschen Sie alle Serverendpunkte in der Synchronisierungsgruppe.
2. Löschen Sie den Cloudendpunkt. 
3. Löschen Sie die Synchronisierungsgruppe.
4. Wenn das Cloudtiering auf einem Serverendpunkt aktiviert wurde, löschen Sie die verwaisten mehrstufigen Dateien auf dem Server, indem Sie die Schritte ausführen, die im Abschnitt [Auf Tieringdateien kann nach dem Löschen eines Serverendpunkts nicht zugegriffen werden](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) dokumentiert sind.
5. Erstellen Sie die Synchronisierungsgruppe neu.

<a id="-2145844941"></a>**Die Synchronisierung war fehlerhaft, weil die HTTP-Anforderung umgeleitet wurde.**  

| | |
|-|-|
| **HRESULT** | 0x80190133 |
| **HRESULT (dezimal)** | -2145844941 |
| **Fehlerzeichenfolge** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **Korrektur erforderlich** | Ja |

Dieser Fehler tritt auf, weil die Azure-Dateisynchronisierung keine HTTP-Umleitung (Statuscode 3xx) unterstützt. Um das Problem zu beheben, deaktivieren Sie die HTTP-Umleitung auf Ihrem Proxyserver oder Netzwerkgerät.

<a id="-2134364027"></a>**Timeout beim Übertragen der Offlinedaten. Der Vorgang wird jedoch weiterhin ausgeführt.**  

| | |
|-|-|
| **HRESULT** | 0x80c83085 |
| **HRESULT (dezimal)** | -2134364027 |
| **Fehlerzeichenfolge** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **Korrektur erforderlich** | Nein |

Dieser Fehler tritt auf, wenn ein Datenerfassungsvorgang das Timeout überschreitet. Dieser Fehler kann ignoriert werden, wenn die Synchronisierung fortgesetzt wird („AppliedItemCount“ ist größer als 0). Informationen finden Sie unter [Wie überwache ich den Fortschritt einer aktuellen Synchronisierungssitzung?](#how-do-i-monitor-the-progress-of-a-current-sync-session)

### <a name="common-troubleshooting-steps"></a>Allgemeine Schritte zur Problembehandlung
<a id="troubleshoot-storage-account"></a>**Überprüfen Sie, ob das Speicherkonto vorhanden ist.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Navigieren Sie zu der Synchronisierungsgruppe im Speichersynchronisierungsdienst.
2. Wählen Sie den Cloudendpunkt innerhalb der Synchronisierungsgruppe aus.
3. Beachten Sie den Namen der Azure-Dateifreigabe im geöffneten Bereich.
4. Wählen Sie das verknüpfte Speicherkonto aus. Wenn bei dieser Verknüpfung ein Fehler auftritt, wurde das Speicherkonto, auf das verwiesen wurde, entfernt.
    ![Ein Screenshot, das den Bereich mit Details zum Cloudendpunkt und einem Link zum Speicherkonto zeigt](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
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

<a id="troubleshoot-azure-file-share"></a>**Stellen Sie sicher, dass die Azure-Dateifreigabe vorhanden ist.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Klicken Sie auf der linken Seite im Inhaltsverzeichnis auf **Übersicht**, um zur Hauptseite des Speicherkontos zurückzukehren.
2. Wählen Sie **Dateien** zum Anzeigen der Liste von Dateifreigaben aus.
3. Vergewissern Sie sich, dass die Dateifreigabe, auf die durch den Cloudendpunkt verwiesen wird, in der Liste der Dateifreigaben angezeigt wird (Sie sollten diese bei Schritt 1 notiert haben).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
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
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Klicken Sie links im Inhaltsverzeichnis auf **Zugriffssteuerung (IAM)** .
1. Klicken Sie auf die Registerkarte **Rollenzuweisungen**, um die Liste der Benutzer und Anwendungen (*Dienstprinzipale*) anzuzeigen, die Zugriff auf Ihr Speicherkonto besitzen.
1. Vergewissern Sie sich, dass in der Liste **Microsoft.StorageSync** oder der alte Anwendungsname **Hybrid File Sync Service** (Hybrid-Dateisynchronisierungsdienst) mit der Rolle **Lese- und Datenzugriff** angezeigt wird. 

    ![Ein Screenshot des Dienstprinzipals des hybriden Dateisynchronisierungsdiensts auf der Registerkarte zur Zugriffssteuerung des Speicherkontos](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Wird **Microsoft.StorageSync** oder **Hybrid File Sync Service** (Hybrid-Dateisynchronisierungsdienst) nicht in der Liste angezeigt, führen Sie die folgenden Schritte aus:

    - Klicken Sie auf **Hinzufügen**.
    - Wählen Sie im Feld **Rolle** die Option **Lese- und Datenzugriff** aus.
    - Geben Sie im Feld **Auswählen** die Zeichenfolge **Microsoft.StorageSync** ein, wählen Sie die Rolle aus, und klicken Sie auf **Speichern**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Microsoft.StorageSync" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Wie verhindere ich, dass Benutzer Dateien auf dem Server erstellen, die nicht unterstützte Zeichen enthalten?
Mit den [Dateiprüfungen des Ressourcen-Managers für Dateiserver (File Server Resource Manager, FSRM)](/windows-server/storage/fsrm/file-screening-management) können Sie verhindern, dass Dateien mit nicht unterstützten Zeichen in deren Namen auf dem Server erstellt werden. Möglicherweise müssen Sie dazu PowerShell verwenden, da der Großteil der nicht unterstützten Zeichen nicht druckbar ist, und daher zuerst ihre hexadezimalen Darstellungen in Zeichen umwandeln.

Erstellen Sie zunächst mit dem [Cmdlet „New-FsrmFileGroup“](/powershell/module/fileserverresourcemanager/new-fsrmfilegroup) eine FSRM-Dateigruppe. Dieses Beispiel definiert, dass die Gruppe nur zwei der nicht unterstützten Zeichen enthalten darf, Sie können jedoch so viele Zeichen wie erforderlich in Ihrer Dateigruppe einschließen.

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

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>Gewusst wie: Überwachen der Tieringaktivität auf einem Server  
Um die Tieringaktivität auf einem Server zu überwachen, verwenden Sie die Ereignis-IDs 9003, 9016 und 9029 im Telemetrieereignisprotokoll (in der Ereignisanzeige unter „Anwendungen und Dienste\Microsoft\FileSync\Agent“).

- Die Ereignis-ID 9003 ermöglicht die Fehlerverteilung für einen Serverendpunkt. Beispiele: Gesamtfehlerzahl, ErrorCode usw. Beachten Sie, dass ein Ereignis pro Fehlercode protokolliert wird.
- Die Ereignis-ID 9016 stellt Ghostingergebnisse für ein Volume bereit. Beispiele: Freier Speicherplatz in Prozent, Anzahl der Dateien in der Sitzung, für die ein Ghosting durchgeführt wurde, Anzahl von Dateien, bei denen beim Ghosting ein Fehler aufgetreten ist usw.
- Die Ereignis-ID 9029 bietet Informationen zu Ghostingsitzungen für einen Serverendpunkt. Beispiele: Anzahl der in der Sitzung herangezogenen Dateien, Anzahl der Dateien in der Sitzung, für die ein Tiering durchgeführt wurde, Anzahl der Dateien, für die bereits ein Tiering durchgeführt ist usw.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>Gewusst wie: Überwachen der Rückrufaktivität auf einem Server
Um die Rückrufaktivität auf einem Server zu überwachen, verwenden Sie die Ereignis-IDs 9005, 9006, 9009 und 9059 im Telemetrieereignisprotokoll (in der Ereignisanzeige unter „Anwendungen und Dienste\Microsoft\FileSync\Agent“).

- Die Ereignis-ID 9005 bietet Zuverlässigkeit beim Rückruf für einen Serverendpunkt. Beispiele: Gesamtanzahl eindeutiger Dateien, auf die zugegriffen wird, und Gesamtanzahl eindeutiger Dateien, bei denen beim Zugriff ein Fehler aufgetreten ist.
- Die Ereignis-ID 9006 ermöglicht die Rückruffehlerverteilung für einen Serverendpunkt. Beispiele: Fehlerhafte Anforderungen insgesamt, ErrorCode usw. Beachten Sie, dass ein Ereignis pro Fehlercode protokolliert wird.
- Die Ereignis-ID 9009 bietet Informationen zu Rückrufsitzungen für einen Serverendpunkt. Hierzu zählen beispielsweise DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed usw.
- Die Ereignis-ID 9059 bietet Informationen zur Anwendungsrückrufverteilung für einen Serverendpunkt. Hierzu zählen beispielsweise ShareId, Anwendungsname und TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>So beheben Sie Probleme bei Dateien, bei denen kein Tiering möglich ist
Wenn Tieringfehler von Dateien auf Azure Files auftreten:

1. Überprüfen Sie in der Ereignisanzeige die Telemetrie-, Betriebs- und Diagnoseereignisprotokolle unter „Anwendungen und Dienste\Microsoft\FileSync\Agent“. 
   1. Stellen Sie sicher, dass die Dateien in der Azure-Dateifreigabe vorhanden sind.

      > [!NOTE]
      > Eine Datei muss mit einer Azure-Dateifreigabe synchronisiert werden, bevor ein Tiering möglich ist.

   2. Überprüfen Sie, ob der Server über Internetkonnektivität verfügt. 
   3. Überprüfen Sie, ob die Filtertreiber der Azure-Dateisynchronisierung („StorageSync.sys“ und „StorageSyncGuard.sys“) ausgeführt werden:
       - Geben Sie an einer Eingabeaufforderung mit erhöhten Rechten `fltmc` ein. Überprüfen Sie, ob die Dateisystem-Filtertreiber „StorageSync.sys“ und „StorageSyncGuard.sys“ aufgelistet sind.

> [!NOTE]
> Eine Ereignis-ID 9003 wird einmal pro Stunde im Telemetrieereignisprotokoll protokolliert, wenn beim Tiering einer Datei ein Fehler auftritt (pro Fehlercode wird ein Ereignis protokolliert). Informieren Sie sich im Abschnitt [Tieringfehler und deren Behebung](#tiering-errors-and-remediation), ob für den Fehlercode Schritte zur Behebung aufgeführt sind.

### <a name="tiering-errors-and-remediation"></a>Tieringfehler und deren Behebung

| HRESULT | HRESULT (dezimal) | Fehlerzeichenfolge | Problem | Wiederherstellung |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86045 | -2134351803 | ECS_E_INITIAL_UPLOAD_PENDING | Tieringfehler bei der Datei, da der erste Upload gerade läuft. | Keine weiteren Maßnahmen erforderlich. Das Tiering erfolgt für die Datei nach Abschluss des ersten Uploads. |
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | Bei der Datei ist ein Tieringfehler aufgetreten, weil sie gerade verwendet wird. | Keine weiteren Maßnahmen erforderlich. Bei der Datei wird das Tiering durchgeführt, wenn sie nicht mehr verwendet wird. |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | Bei der Datei ist ein Tieringfehler aufgetreten, weil sie von der Synchronisierung ausgeschlossen ist. | Keine weiteren Maßnahmen erforderlich. Für Dateien in der Synchronisierungsausschlussliste kann kein Tiering durchgeführt werden. |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | Bei der Datei ist ein Tieringfehler aufgetreten, weil sie auf dem Server nicht gefunden wurde. | Keine weiteren Maßnahmen erforderlich. Wenn der Fehler weiterhin auftritt, überprüfen Sie, ob die Datei auf dem Server vorhanden ist. |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | Bei der Datei ist ein Tieringfehler aufgetreten, weil sie in der Azure-Dateifreigabe gelöscht wurde. | Keine weiteren Maßnahmen erforderlich. Die Datei sollte bei Ausführung der nächsten Downloadsynchronisierungssitzung auf dem Server gelöscht werden. |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | Bei der Datei ist aufgrund eines Netzwerkproblems ein Tieringfehler aufgetreten. | Keine weiteren Maßnahmen erforderlich. Falls der Fehler weiterhin auftritt, überprüfen Sie die Netzwerkverbindung mit der Azure-Dateifreigabe. |
| 0x80072ee7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | Bei der Datei ist aufgrund eines Netzwerkproblems ein Tieringfehler aufgetreten. | Keine weiteren Maßnahmen erforderlich. Falls der Fehler weiterhin auftritt, überprüfen Sie die Netzwerkverbindung mit der Azure-Dateifreigabe. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Bei der Datei ist aufgrund des Fehlers „Zugriff verweigert“ ein Tieringfehler aufgetreten. Dieser Fehler kann auftreten, wenn sich die Datei in einem schreibgeschützten DFS-R-Replikationsordner befindet. | Die Azure-Dateisynchronisierung unterstützt keine Serverendpunkte in schreibgeschützten DFS-R-Replikationsordnern. Weitere Informationen finden Sie im [Planungshandbuch](./storage-sync-files-planning.md#distributed-file-system-dfs). |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | Bei der Datei ist aufgrund eines Netzwerkproblems ein Tieringfehler aufgetreten. | Keine weiteren Maßnahmen erforderlich. Falls der Fehler weiterhin auftritt, überprüfen Sie die Netzwerkverbindung mit der Azure-Dateifreigabe. |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | Bei der Datei ist ein Tieringfehler aufgetreten, weil die Dateigröße kleiner als die unterstützte Größe ist. | Wenn die Agentversion kleiner als 9.0 ist, beträgt die minimal unterstützte Dateigröße 64 KB. Wenn die Agentversion 9.0 oder höher ist, basiert die unterstützte minimale Dateigröße auf der Größe des Dateisystemclusters (doppelte Größe des Dateisystemclusters). Wenn die Größe des Dateisystemclusters z. B. 4 KB beträgt, ist die minimale Dateigröße 8 KB. |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | Bei der Datei ist aufgrund eines Azure-Speicherproblems ein Tieringfehler aufgetreten. | Wenn der Fehler weiterhin auftritt, öffnen Sie eine Supportanfrage. |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | Bei der Datei ist ein Tieringfehler aufgetreten, weil sie gleichzeitig rückgerufen wurde. | Keine weiteren Maßnahmen erforderlich. Das Tiering der Datei wird nach Abschluss des Rückrufs durchgeführt, wenn die Datei nicht mehr verwendet wird. |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | Bei der Datei ist ein Tieringfehler aufgetreten, weil sie mit der Azure-Dateifreigabe nicht synchronisiert wurde. | Keine weiteren Maßnahmen erforderlich. Bei der Datei wird das Tiering durchgeführt, sobald sie mit der Azure-Dateifreigabe synchronisiert wurde. |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | Bei der Datei ist ein Tieringfehler aufgetreten, weil der Cloudtiering-Filtertreiber (storagesync.sys) nicht ausgeführt wird. | Öffnen Sie zur Behebung dieses Problems eine Eingabeaufforderung mit erhöhten Rechten, und führen Sie den folgenden Befehl aus: `fltmc load storagesync`<br>Wenn das Laden des Filtertreibers „storagesync“ bei Ausführung des Befehls „fltmc“ fehlschlägt, deinstallieren Sie den Azure-Dateisynchronisierungs-Agent, starten Sie den Server neu, und installieren Sie den Agent neu. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Bei der Datei ist aufgrund von unzureichendem Speicherplatz auf dem Volume, auf dem sich der Serverendpunkt befindet, ein Tieringfehler aufgetreten. | Um dieses Problem zu beheben, geben Sie mindestens 100 MB Speicherplatz auf dem Volume frei, auf dem sich der Serverendpunkt befindet. |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | Bei der Datei ist ein Tieringfehler aufgetreten, weil sie mit der Azure-Dateifreigabe nicht synchronisiert wurde. | Keine weiteren Maßnahmen erforderlich. Bei der Datei wird das Tiering durchgeführt, sobald sie mit der Azure-Dateifreigabe synchronisiert wurde. |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | Bei der Datei ist ein Tieringfehler aufgetreten, weil es sich dabei um einen nicht unterstützten Analysepunkt handelt. | Wenn es sich bei der Datei um einen Analysepunkt für Datendeduplizierung handelt, führen Sie die Schritte im [Planungshandbuch](./storage-sync-files-planning.md#data-deduplication) aus, um den Support für Datendeduplizierung zu aktivieren. Dateien mit anderen Analysepunkten als Datendeduplizierung werden nicht unterstützt, und es kann damit kein Tiering durchgeführt werden.  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | Bei der Datei ist ein Tieringfehler aufgetreten, weil sie geändert wurde. | Keine weiteren Maßnahmen erforderlich. Bei der Datei wird das Tiering durchgeführt, sobald sie mit der Azure-Dateifreigabe synchronisiert wurde. |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | Bei der Datei ist ein Tieringfehler aufgetreten, weil sie mit der Azure-Dateifreigabe nicht synchronisiert wurde. | Keine weiteren Maßnahmen erforderlich. Bei der Datei wird das Tiering durchgeführt, sobald sie mit der Azure-Dateifreigabe synchronisiert wurde. |
| 0x80072ee2 | -2147012894 | WININET_E_TIMEOUT | Bei der Datei ist aufgrund eines Netzwerkproblems ein Tieringfehler aufgetreten. | Keine weiteren Maßnahmen erforderlich. Falls der Fehler weiterhin auftritt, überprüfen Sie die Netzwerkverbindung mit der Azure-Dateifreigabe. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Bei der Datei ist ein Tieringfehler aufgetreten, weil sie geändert wurde. | Keine weiteren Maßnahmen erforderlich. Bei der Datei wird das Tiering durchgeführt, sobald sie mit der Azure-Dateifreigabe synchronisiert wurde. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Bei der Datei ist aufgrund unzureichender Systemressourcen ein Tieringfehler aufgetreten. | Wenn der Fehler weiterhin auftritt, überprüfen Sie, welche Anwendung oder welcher Kernelmodustreiber zu viele Systemressourcen beansprucht. |
| 0x8e5e03fe | -1906441218 | JET_errDiskIO | Tieringfehler für die Datei aufgrund eines E/A-Fehlers beim Schreiben in die Cloudtieringdatenbank. | Wenn der Fehler weiterhin auftritt, führen Sie CHKDSK auf dem Volume aus, und überprüfen Sie die Speicherhardware. |
| 0x8e5e0442 | -1906441150 | JET_errInstanceUnavailable | Tieringfehler bei der Datei, da die Cloudtieringdatenbank nicht ausgeführt wird. | Um dieses Problem zu beheben, starten Sie den FileSyncSvc-Dienst oder -Server neu. Wenn der Fehler weiterhin auftritt, führen Sie CHKDSK auf dem Volume aus, und überprüfen Sie die Speicherhardware. |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Beheben von Rückruffehlern bei Dateien  
Wenn bei Dateien Rückruffehler auftreten:
1. Überprüfen Sie in der Ereignisanzeige die Telemetrie-, Betriebs- und Diagnoseereignisprotokolle unter „Anwendungen und Dienste\Microsoft\FileSync\Agent“.
    1. Stellen Sie sicher, dass die Dateien in der Azure-Dateifreigabe vorhanden sind.
    2. Überprüfen Sie, ob der Server über Internetkonnektivität verfügt. 
    3. Öffnen Sie das Dienst-MMC-Snap-In, und stellen Sie sicher, dass der Agent für die Speichersynchronisierung (FileSyncSvc) ausgeführt wird.
    4. Überprüfen Sie, ob die Filtertreiber der Azure-Dateisynchronisierung („StorageSync.sys“ und „StorageSyncGuard.sys“) ausgeführt werden:
        - Geben Sie an einer Eingabeaufforderung mit erhöhten Rechten `fltmc` ein. Überprüfen Sie, ob die Dateisystem-Filtertreiber „StorageSync.sys“ und „StorageSyncGuard.sys“ aufgelistet sind.

> [!NOTE]
> Eine Ereignis-ID 9006 wird einmal pro Stunde im Telemetrieereignisprotokoll protokolliert, wenn beim Rückruf einer Datei ein Fehler auftritt (pro Fehlercode wird ein Ereignis protokolliert). Informieren Sie sich im Abschnitt [Abruffehler und deren Behebung](#recall-errors-and-remediation), ob für den Fehlercode Schritte zur Behebung aufgeführt sind.

### <a name="recall-errors-and-remediation"></a>Abruffehler und deren Behebung

| HRESULT | HRESULT (dezimal) | Fehlerzeichenfolge | Problem | Wiederherstellung |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | Die Datei konnte aufgrund einer E/A-Zeitüberschreitung nicht abgerufen werden. Dieses Problem kann aus verschiedenen Gründen auftreten: Ressourceneinschränkungen auf dem Server, eine schlechte Netzwerkverbindung oder ein Azure Storage-Problem (z. B. Drosselung). | Keine weiteren Maßnahmen erforderlich. Wenn der Fehler mehrere Stunden anhält, erstellen Sie eine Supportanfrage. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | Die Datei konnte aufgrund eines Netzwerkproblems nicht abgerufen werden.  | Falls der Fehler weiterhin auftritt, überprüfen Sie die Netzwerkverbindung mit der Azure-Dateifreigabe. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | Die Datei konnte nicht abgerufen werden, weil der Serverendpunkt gelöscht wurde. | Informationen zur Behebung dieses Problems finden Sie unter [Auf Tieringdateien kann nach dem Löschen eines Serverendpunkts nicht zugegriffen werden](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Die Datei konnte nicht abgerufen werden, weil der Zugriff verweigert wurde. Dieses Problem kann auftreten, wenn die Einstellungen für Firewall und virtuelles Netzwerk im Speicherkonto aktiviert sind und der Server keinen Zugriff auf das Speicherkonto hat. | Um das Problem zu beheben, fügen Sie die Server-IP-Adresse oder das virtuelle Netzwerk hinzu, indem Sie die im Abschnitt [Konfigurieren der Einstellung für Firewall und virtuelles Netzwerk](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) des Bereitstellungsleitfadens angegebenen Schritte ausführen. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | Die Datei konnte nicht abgerufen werden, weil in der Azure-Dateifreigabe nicht darauf zugegriffen werden kann. | Stellen Sie sicher, dass die Datei in der Azure-Dateifreigabe vorhanden ist, um das Problem zu beheben. Wenn die Datei in der Azure-Dateifreigabe vorhanden ist, führen Sie ein Upgrade auf die neueste [Version](./storage-files-release-notes.md#supported-versions) des Azure-Dateisynchronisierungs-Agents aus. |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | Die Datei konnte aufgrund eines Autorisierungsfehlers beim Speicherkonto nicht abgerufen werden. | Um dieses Problem zu beheben, stellen Sie sicher, dass die [Azure-Dateisynchronisierung Zugriff auf das Speicherkonto hat](?tabs=portal1%252cazure-portal#troubleshoot-rbac). |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | Die Datei konnte nicht abgerufen werden, weil nicht auf die Azure-Dateifreigabe zugegriffen werden kann. | Vergewissern Sie sich, dass die Dateifreigabe vorhanden und zugänglich ist. Wenn die Dateifreigabe gelöscht und neu erstellt wurde, führen Sie die Schritte im Abschnitt [Fehler bei der Synchronisierung, weil die Azure-Dateifreigabe gelöscht und neu erstellt wurde](?tabs=portal1%252cazure-portal#-2134375810) aus, um die Synchronisierungsgruppe zu löschen und neu zu erstellen. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Die Datei konnte aufgrund unzureichender Systemressourcen nicht abgerufen werden. | Wenn der Fehler weiterhin auftritt, überprüfen Sie, welche Anwendung oder welcher Kernelmodustreiber zu viele Systemressourcen beansprucht. |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | Die Datei konnte aufgrund von unzureichendem Arbeitsspeicher nicht abgerufen werden. | Wenn der Fehler weiterhin auftritt, überprüfen Sie, welche Anwendung oder welcher Kernelmodustreiber zu viel Arbeitsspeicher beansprucht. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Die Datei konnte aufgrund von unzureichendem Speicherplatz auf dem Datenträger nicht abgerufen werden. | Um dieses Problem zu beheben, geben Sie Speicherplatz auf dem Volume frei, indem Sie Dateien auf ein anderes Volume verschieben, die Größe des Volumes erhöhen oder mit dem Invoke-StorageSyncCloudTiering-Cmdlet ein Dateitiering erzwingen. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>Auf Tieringdateien kann nach dem Löschen eines Serverendpunkts nicht zugegriffen werden
Auf mehrstufige Dateien auf einem Server kann nicht mehr zugegriffen werden, wenn für die Dateien vor dem Löschen eines Serverendpunkts kein Rückruf erfolgt.

Protokollierte Fehler, wenn auf mehrstufige Dateien nicht zugegriffen werden kann
- Beim Synchronisieren einer Datei wird der Fehlercode -2147942467 (0x80070043 – ERROR_BAD_NET_NAME) im ItemResults-Ereignisprotokoll protokolliert.
- Beim Zurückrufen einer Datei wird der Fehlercode -2134376393 (0x80c80037 – ECS_E_SYNC_SHARE_NOT_FOUND) im RecallResults-Ereignisprotokoll protokolliert.

Die Wiederherstellung des Zugriffs auf Ihre mehrstufigen Dateien ist möglich, wenn die folgenden Bedingungen erfüllt sind:
- Serverendpunkt wurde innerhalb der letzten 30 Tage gelöscht
- Cloudendpunkt wurde nicht gelöscht 
- Dateifreigabe wurde nicht gelöscht
- Synchronisierungsgruppe wurde nicht gelöscht

Wenn die obigen Bedingungen erfüllt sind, können Sie den Zugriff auf die Dateien auf dem Server wiederherstellen, indem Sie den Serverendpunkt auf dem Server innerhalb von 30 Tagen unter demselben Pfad in derselben Synchronisierungsgruppe neu erstellen. 

Falls die obigen Bedingungen nicht erfüllt sind, ist das Wiederherstellen des Zugriffs nicht möglich. Der Grund ist, dass diese mehrstufigen Dateien auf dem Server jetzt verwaist sind. Befolgen Sie die Anleitung unten, um die verwaisten mehrstufigen Dateien zu entfernen.

**Hinweise**
- Wenn auf mehrstufige Dateien auf dem Server nicht zugegriffen werden kann, sollte die vollständige Datei weiterhin zugänglich sein, wenn Sie direkt auf die Azure-Dateifreigabe zugreifen.
- Führen Sie beim Löschen eines Serverendpunkts die Schritte unter [Entfernen eines Serverendpunkts](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint) aus, um verwaiste mehrstufige Dateien in Zukunft zu verhindern.

<a id="get-orphaned"></a>**Abrufen der Liste mit den verwaisten mehrstufigen Dateien** 

1. Überprüfen Sie, ob Version v5.1 oder höher des Azure-Dateisynchronisierungs-Agents installiert ist.
2. Führen Sie die folgenden PowerShell-Befehle aus, um verwaiste mehrstufige Dateien aufzulisten:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Speichern Sie die Ausgabedatei „OrphanTieredFiles.txt“, falls Dateien nach dem Löschen aus der Sicherung wiederhergestellt werden müssen.

<a id="remove-orphaned"></a>**Entfernen von verwaisten mehrstufigen Dateien** 

*Option 1: Löschen der verwaisten mehrstufigen Dateien*

Bei dieser Option werden die verwaisten mehrstufigen Dateien auf der Windows Server-Instanz gelöscht. Es ist aber erforderlich, den Serverendpunkt zu entfernen, falls er vorhanden ist (aufgrund der Neuerstellung nach 30 Tagen oder der Verbindung mit einer anderen Synchronisierungsgruppe). Es kommt zu Dateikonflikten, wenn Dateien auf der Windows Server-Instanz oder der Azure-Dateifreigabe aktualisiert werden, bevor der Serverendpunkt neu erstellt wurde.

1. Überprüfen Sie, ob Version v5.1 oder höher des Azure-Dateisynchronisierungs-Agents installiert ist.
2. Sichern Sie die Azure-Dateifreigabe und den Serverendpunkt-Speicherort.
3. Entfernen Sie den Serverendpunkt in der Synchronisierungsgruppe (falls vorhanden), indem Sie die Schritte unter [Entfernen eines Serverendpunkts](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint) ausführen.

> [!Warning]  
> Wenn der Serverendpunkt vor der Verwendung des Cmdlets „Remove-StorageSyncOrphanedTieredFiles“ nicht entfernt wird, wird durch das Löschen der verwaisten mehrstufigen Datei auf dem Server die gesamte Datei auf der Azure-Dateifreigabe gelöscht. 

4. Führen Sie die folgenden PowerShell-Befehle aus, um verwaiste mehrstufige Dateien aufzulisten:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Speichern Sie die Ausgabedatei „OrphanTieredFiles.txt“, falls Dateien nach dem Löschen aus der Sicherung wiederhergestellt werden müssen.
6. Führen Sie die folgenden PowerShell-Befehle aus, um verwaiste mehrstufige Dateien zu löschen:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Hinweise** 
- Auf dem Server geänderte mehrstufige Dateien, die nicht mit der Azure-Dateifreigabe synchronisiert werden, werden gelöscht.
- Mehrstufige Dateien, auf die zugegriffen werden kann (nicht verwaist), werden nicht gelöscht.
- Nicht mehrstufige Dateien verbleiben auf dem Server.

7. Optional: Erstellen Sie den Serverendpunkt neu, wenn er in Schritt 3 gelöscht wurde.

*Option 2: Bereitstellen der Azure-Dateifreigabe und Kopieren der Dateien, die auf dem Server verwaist sind, in die lokale Umgebung*

Bei dieser Option muss der Serverendpunkt nicht entfernt werden, aber es muss genügend freier Speicherplatz auf dem Datenträger vorhanden sein, um die gesamten Dateien in die lokale Umgebung kopieren zu können.

1. Führen Sie die [Bereitstellung](./storage-how-to-use-files-windows.md) der Azure-Dateifreigabe auf der Windows Server-Instanz durch, die über verwaiste mehrstufige Dateien verfügt.
2. Führen Sie die folgenden PowerShell-Befehle aus, um verwaiste mehrstufige Dateien aufzulisten:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Verwenden Sie die Ausgabedatei „OrphanTieredFiles.txt“, um verwaiste mehrstufige Dateien auf dem Server zu identifizieren.
4. Überschreiben Sie die verwaisten mehrstufigen Dateien, indem Sie die vollständige Datei von der Azure-Dateifreigabe auf die Windows Server-Instanz kopieren.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Durchführen der Problembehandlung für Dateien, die unerwartet auf einem Server zurückgerufen werden  
Virenschutz, Sicherung und andere Anwendungen, die viele Dateien lesen, können zu unbeabsichtigten Rückrufen führen, wenn sie das Offline-überspringen-Attribut nicht berücksichtigen und das Lesen des Inhalts dieser Dateien nicht überspringen. Das Überspringen von Offlinedateien für Produkte, die diese Option unterstützen, hilft, unbeabsichtigte Rückrufe während Vorgängen wie Virusscans oder Sicherungsaufträgen zu verhindern.

Wenden Sie sich an den Softwareanbieter, um weitere Informationen zu den erforderlichen Konfigurationsschritten zu erhalten, damit die Lösung das Lesen von Offlinedateien überspringt.

Unbeabsichtigte Rückrufe können auch in anderen Szenarien auftreten, z.B. beim Durchsuchen von Dateien im Datei-Explorer. Das Öffnen eines Ordners, der Dateien mit Cloudtiering enthält, im Datei-Explorer auf dem Server kann zu unbeabsichtigten Rückrufen führen. Die Wahrscheinlichkeit dafür steigt, wenn auf dem Server eine Virenschutzlösung aktiviert ist.

> [!NOTE]
>Verwenden Sie Ereignis-ID 9059 im Telemetrieereignisprotokoll, um zu bestimmen, welche Anwendungen Rückrufe erzeugen. Dieses Ereignis stellt Informationen zur Anwendungsrückrufverteilung für einen Serverendpunkt bereit und wird einmal pro Stunde protokolliert.

### <a name="tls-12-required-for-azure-file-sync"></a>TLS 1.2 für Azure-Dateisynchronisierung erforderlich

Sie können die TLS-Einstellungen auf Ihrem Server anzeigen, indem Sie sich die [Registrierungseinstellungen](/windows-server/security/tls/tls-registry-settings) ansehen. 

Wenn Sie einen Proxy verwenden, sehen Sie in der Dokumentation zu Ihrem Proxy nach, und stellen Sie sicher, dass er für die Verwendung von TLS 1.2 konfiguriert ist.

## <a name="general-troubleshooting"></a>Allgemeine Problembehandlung
Wenn Probleme mit der Azure-Dateisynchronisierung auf einem Server auftreten, führen Sie zunächst die folgenden Schritte aus:
1. Überprüfen Sie in der Ereignisanzeige die Telemetrie-, Betriebs- und Diagnoseereignisprotokolle.
    - Probleme mit der Synchronisierung, dem Tiering und dem Rückrufen werden in den Telemetrie-, Diagnose- und Betriebsereignisprotokollen unter „Anwendungen und Dienste\Microsoft\FileSync\Agent“ protokolliert.
    - Probleme im Zusammenhang mit der Serververwaltung (z. B. Konfigurationseinstellungen) werden in den Betriebs- und Diagnoseereignisprotokollen unter „Anwendungen“ und „Dienste\Microsoft\FileSync\Management“ protokolliert.
2. Stellen Sie sicher, dass der Azure-Dateisynchronisierungsdienst auf dem Server ausgeführt wird:
    - Öffnen Sie das Dienste-MMC-Snap-In, und stellen Sie sicher, dass der Agent für die Speichersynchronisierung (FileSyncSvc) ausgeführt wird.
3. Überprüfen Sie, ob die Filtertreiber der Azure-Dateisynchronisierung („StorageSync.sys“ und „StorageSyncGuard.sys“) ausgeführt werden:
    - Geben Sie an einer Eingabeaufforderung mit erhöhten Rechten `fltmc` ein. Überprüfen Sie, ob die Dateisystem-Filtertreiber „StorageSync.sys“ und „StorageSyncGuard.sys“ aufgelistet sind.

Wenn das Problem nicht behoben werden kann, führen Sie das Tool AFSDiag aus, und übermitteln Sie die Ausgabe der ZIP-Datei zur weiteren Untersuchung an den für Ihren Fall zuständigen Supportmitarbeiter.

Führen Sie zum Ausführen von AFSDiag die Schritte unten aus.

Ab Agent-Version v11:
1. Öffnen Sie ein PowerShell-Fenster mit erhöhten Rechten, und führen Sie die folgenden Befehle aus (drücken Sie nach jedem Befehl die EINGABETASTE):

    > [!NOTE]
    >AFSDiag erstellt das Ausgabeverzeichnis und einen temporären Ordner darin, bevor Protokolle gesammelt werden, und löscht den temporären Ordner nach der Ausführung. Geben Sie einen Speicherort für die Ausgaben an, der keine Daten enthält.
    
    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-AFS -OutputDirectory C:\output -KernelModeTraceLevel Verbose -UserModeTraceLevel Verbose
    ```

2. Reproduzieren Sie das Problem. Klicken Sie abschließend auf **D**.
3. Eine ZIP-Datei, die Protokolle und Ablaufverfolgungsdateien enthält, wird im angegebenen Ausgabeverzeichnis gespeichert. 

Bis Agent-Version v10:
1. Erstellen Sie eine Verzeichnis zum Speichern der Ausgabe von AFSDiag (z.B. „C:\Ausgabe“).
    > [!NOTE]
    >AFSDiag löscht vor der Protokollerfassung den gesamten Inhalt des Ausgabeverzeichnisses. Geben Sie einen Speicherort für die Ausgaben an, der keine Daten enthält.
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
