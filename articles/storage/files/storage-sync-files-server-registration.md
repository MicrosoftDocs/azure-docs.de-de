---
title: Verwalten registrierter Server mit der Azure-Dateisynchronisierung | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Windows Server bei einem Azure-Dateisynchronisierungsdienst oder Speichersynchronisierungsdienst registrieren bzw. die Registrierung aufheben.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 194b0f2ff94197fe11c189e97dbc65c9d0367932
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013920"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Verwalten registrierter Server mit der Azure-Dateisynchronisierung
Mit der Azure-Dateisynchronisierung können Sie Dateifreigaben Ihrer Organisation in Azure Files zentralisieren, ohne auf die Flexibilität, Leistung und Kompatibilität eines lokalen Dateiservers verzichten zu müssen. Dies erfolgt durch Umwandeln der Windows-Server in einen Schnellcache der Azure-Dateifreigabe. Sie können alle unter Windows Server verfügbaren Protokolle für den lokalen Zugriff auf Ihre Daten (einschließlich SMB, NFS und FTPS) sowie beliebig viele Caches weltweit verwenden.

Im folgenden Artikel wird beschrieben, wie Sie einen Server bei einem Speichersynchronisierungsdienst registrieren und verwalten. Informationen zur End-to-End-Bereitstellung der Azure-Dateisynchronisierung finden Sie unter [Bereitstellen der Azure-Dateisynchronisierung (Vorschau)](storage-sync-files-deployment-guide.md).

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Registrieren eines Servers beim Speichersynchronisierungsdienst und Aufheben der Registrierung
Beim Registrieren eines Servers bei der Azure-Dateisynchronisierung wird eine Vertrauensstellung zwischen Windows Server und Azure eingerichtet. Mithilfe dieser Beziehung können dann *Serverendpunkte* auf dem Server erstellt werden, die bestimmte Ordner darstellen, die mit einer Azure-Dateifreigabe (auch als *Cloudendpunkt* bezeichnet) synchronisiert werden sollen. 

### <a name="prerequisites"></a>Voraussetzungen
Bevor Sie einen Server bei einem Speichersynchronisationsdienst registrieren können, müssen die erforderlichen Voraussetzungen auf dem Server erfüllt sein:

* Ihr Server muss unter einer unterstützten Version von Windows Server ausgeführt werden. Weitere Informationen finden Sie unter [Systemanforderungen und Interoperabilität der Azure-Dateisynchronisierung](storage-sync-files-planning.md#windows-file-server-considerations).
* Stellen Sie sicher, dass der Speichersynchronisierungsdienst bereitgestellt wurde. Weitere Informationen zum Bereitstellen eines Speichersynchronisierungsdiensts finden Sie unter [Bereitstellen der Azure-Dateisynchronisierung](storage-sync-files-deployment-guide.md).
* Stellen Sie sicher, dass der Server mit dem Internet verbunden ist und dass der Zugriff auf Azure möglich ist.
* Deaktivieren Sie die verstärkte Sicherheitskonfiguration für IE über die Server-Manager-Benutzeroberfläche.
    
    ![Server-Manager-Benutzeroberfläche, auf der die Option „Verstärkte Sicherheitskonfiguration für IE“ hervorgehoben ist](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Stellen Sie sicher, dass das Azure PowerShell-Modul auf dem Server installiert ist. Wenn der Server Mitglied eines Failoverclusters ist, ist das Az-Modul für jeden Knoten im Cluster erforderlich. Weitere Informationen zur Installation des Az-Moduls finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/install-Az-ps).

    > [!Note]  
    > Es wird empfohlen, die neueste Version des Az-PowerShell-Moduls zu verwenden, um einen Server zu registrieren bzw. die Registrierung aufzuheben. Wenn das Az-Paket zuvor auf diesem Server installiert wurde (und die PowerShell-Version auf diesem Server 5.* oder höher ist), können Sie das Paket mit dem Cmdlet `Update-Module` aktualisieren. 
* Wenn Sie einen Netzwerkproxyserver in Ihrer Umgebung nutzen, konfigurieren Sie auf dem Server Proxyeinstellungen, die für den Synchronisierungs-Agent verwendet werden.
    1. Bestimmen Sie die Proxy-IP-Adresse und die zugehörige Portnummer.
    2. Bearbeiten Sie diese beiden Dateien:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Fügen Sie in diese beiden Dateien die in Abbildung 1 (unterhalb dieses Abschnitt) aufgeführten Zeilen unter „/System.ServiceModel“ ein. Ändern Sie dabei „127.0.0.1:8888“ in die richtige IP-Adresse (ersetzen Sie „127.0.0.1“) und die richtige Portnummer (ersetzen Sie „8888“):
    4. Legen Sie die WinHTTP-Proxyeinstellungen per Befehlszeile fest:
        * Anzeigen des Proxys: netsh winhttp show proxy
        * Festlegen des Proxys: netsh winhttp set proxy 127.0.0.1:8888
        * Zurücksetzen des Proxys: netsh winhttp reset proxy
        * Erfolgt diese Einrichtung, nachdem der Agent installiert wurde, starten Sie den Synchronisierungs-Agent neu: netstop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Registrieren eines Servers beim Speichersynchronisierungsdienst
Damit ein Server als *Serverendpunkt* in einer *Synchronisierungsgruppe* von der Azure-Dateisynchronisierung verwendet werden kann, muss er bei einem *Speichersynchronisierungsdienst* registriert werden. Ein Server kann immer nur bei einem Speichersynchronisierungsdienst registriert sein.

#### <a name="install-the-azure-file-sync-agent"></a>Installieren des Azure-Dateisynchronisierungs-Agents
1. [Laden Sie den Azure-Dateisynchronisierungs-Agent herunter](https://go.microsoft.com/fwlink/?linkid=858257).
2. Starten Sie den Installer für den Azure-Dateisynchronisierungs-Agent.
    
    ![Erste Seite des Installers für den Azure-Dateisynchronisierungs-Agent](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Sie müssen Updates für den Azure-Dateisynchronisierungs-Agent mit Microsoft Update aktivieren. Dies ist deshalb von Bedeutung, weil wichtige Sicherheitspatches und Verbesserungen an Funktionen des Serverpakets über Microsoft Update ausgeliefert werden.

    ![Stellen Sie sicher, dass Microsoft Update im Bereich „Microsoft Update“ des Installers für den Azure-Dateisynchronisierungs-Agent aktiviert ist](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Wenn der Server noch nicht registriert wurde, wird die Benutzeroberfläche für die Serverregistrierung nach Abschluss der Installation geöffnet.

> [!Important]  
> Wenn der Server Mitglied eines Failoverclusters ist, muss der Azure-Dateisynchronisierungs-Agent auf jedem Knoten im Cluster installiert werden.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Registrieren des Servers mit der Benutzeroberfläche für die Serverregistrierung
1. Wenn die Benutzeroberfläche für die Serverregistrierung nicht unmittelbar nach Abschluss der Installation des Azure-Dateisynchronisierungs-Agents gestartet wird, können Sie sie manuell starten, indem Sie `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe` ausführen.
2. Klicken Sie auf *Anmelden*, um auf Ihr Azure-Abonnement zuzugreifen. 

    ![Öffnen des Dialogfelds mit der Benutzeroberfläche für die Serverregistrierung](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Wählen Sie im Dialogfeld das richtige Abonnement, die Ressourcengruppe und den Speicher-Synchronisierungsdienst aus.

    ![Angaben zum Speichersynchronisierungsdienst](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. In der Vorschauversion ist eine weitere Anmeldung erforderlich, um den Vorgang abzuschließen. 

    ![Anmeldedialogfeld](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Wenn der Server Mitglied eines Failoverclusters ist, muss die Serverregistrierung für jeden einzelnen Server ausgeführt werden. Wenn Sie die registrierten Server im Azure-Portal anzeigen, werden alle Knoten von der Azure-Dateisynchronisierung automatisch als Mitglied des gleichen Failoverclusters erkannt und entsprechend gruppiert.

#### <a name="register-the-server-with-powershell"></a>Registrieren des Servers mit PowerShell
Sie können auch Serverregistrierung auch über PowerShell ausführen. 

```powershell
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Aufheben der Registrierung des Servers beim Speichersynchronisierungsdienst
Zum Aufheben der Registrierung eines Servers bei einem Speichersynchronisierungsdienst sind mehrere Schritte erforderlich. Im Folgenden wird das ordnungsgemäße Aufheben der Serverregistrierung behandelt.

> [!Warning]  
> Versuchen Sie nicht, Probleme mit der Synchronisierung, dem Cloudtiering oder anderen Aspekten der Azure-Dateisynchronisierung zu behandeln, indem Sie die Registrierung eines Servers aufheben und diesen wieder registrieren oder die Serverendpunkte entfernen und neu erstellen, es sei denn, Sie werden ausdrücklich von einem Microsoft-Techniker dazu aufgefordert. Das Aufheben der Registrierung eines Servers und das Entfernen von Serverendpunkten ist ein destruktiver Vorgang. Mehrstufige Dateien auf den Volumes mit Serverendpunkten werden nicht erneut mit ihren Speicherorten in der Azure-Dateifreigabe verbunden, nachdem die registrierten Server und Serverendpunkte neu erstellt wurden. Dies führt zu Synchronisierungsfehlern. Beachten Sie, dass mehrstufige Dateien, die sich außerhalb eines Endpunktnamespace befinden, dauerhaft verloren gehen. Auf Serverendpunkten sind möglicherweise auch dann mehrstufige Dateien vorhanden, wenn das Cloudtiering nie aktiviert wurde.

#### <a name="optional-recall-all-tiered-data"></a>(Optional) Abrufen aller ausgelagerten Daten
Wenn Dateien, die derzeit mehrstufig sind, nach dem Entfernen der Azure-Dateisynchronisierung verfügbar sein sollen (d.h., es handelt sich nicht um eine Testumgebung, sondern um eine Produktionsumgebung), rufen Sie alle Dateien auf jedem Volume mit Serverendpunkten ab. Deaktivieren Sie das Cloudtiering für alle Serverendpunkte, und führen Sie das folgende PowerShell-Cmdlet aus:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Wenn das lokale Volume, auf dem der Serverendpunkt gehostet wird, nicht genügend freien Speicherplatz aufweist, um alle ausgelagerten Daten abzurufen, führt das Cmdlet `Invoke-StorageSyncFileRecall` zu einem Fehler.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Entfernen des Servers aus allen Synchronisierungsgruppen
Vor dem Aufheben der Registrierung des Servers beim Speichersynchronisierungsdienst müssen alle Serverendpunkte für diesen Server entfernt werden. Hierzu können Sie das Azure-Portal verwenden:

1. Navigieren Sie zu dem Speichersynchronisierungsdienst, bei dem der Server registriert ist.
2. Entfernen Sie alle Serverendpunkte für diesen Server aus allen Synchronisierungsgruppen im Speichersynchronisierungsdienst. Klicken Sie hierfür im Bereich „Synchronisierungsgruppe“ mit der rechten Maustaste auf den entsprechenden Serverendpunkt.

    ![Entfernen eines Serverendpunkts aus einer Synchronisierungsgruppe](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Sie können hierzu auch ein einfaches PowerShell-Skript verwenden:

```powershell
Connect-AzAccount

$storageSyncServiceName = "<your-storage-sync-service>"
$resourceGroup = "<your-resource-group>"

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $storageSyncServiceName | ForEach-Object { 
    $syncGroup = $_; 
    Get-AzStorageSyncServerEndpoint -ParentObject $syncGroup | Where-Object { $_.ServerEndpointName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzStorageSyncServerEndpoint -InputObject $_ 
    } 
}
```

#### <a name="unregister-the-server"></a>Aufheben der Serverregistrierung
Nachdem alle Daten abgerufen wurden und der Server aus allen Synchronisierungsgruppen entfernt wurde, kann die Registrierung des Servers aufgehoben werden. 

1. Navigieren Sie im Azure-Portal zum Abschnitt *Registrierte Server* des Speichersynchronisierungsdiensts.
2. Klicken Sie mit der rechten Maustaste auf den Server, dessen Registrierung Sie aufheben möchten, und klicken Sie auf „Serverregistrierung aufheben“.

    ![Serverregistrierung aufheben](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Sicherstellen, dass die Azure-Dateisynchronisierung in Ihrem Rechenzentrum ein guter Nachbar ist 
Da die Azure-Dateisynchronisierung selten der einzige Dienst ist, der in Ihrem Rechenzentrum ausgeführt wird, empfiehlt es sich, die Netzwerk- und Speichernutzung der Azure-Dateisynchronisierung zu beschränken.

> [!Important]  
> Das Festlegen von zu niedrigen Grenzwerten wirkt sich auf die Leistung der Azure-Dateisynchronisierung und auf Rückrufe aus.

### <a name="set-azure-file-sync-network-limits"></a>Festlegen von Netzwerklimits für die Azure-Dateisynchronisierung
Sie können die Netzwerknutzung der Azure-Dateisynchronisierung mithilfe der `StorageSyncNetworkLimit`-Cmdlets drosseln.

> [!Note]  
> Netzwerkgrenzwerte gelten nicht, wenn auf eine mehrstufige Datei zugegriffen wird.

Sie können beispielsweise ein neues Drossellimit erstellen, um sicherzustellen, dass die Azure-Dateisynchronisierung an Werktagen zwischen 9:00 und 17:00 Uhr nicht mehr als 10 MBit/s verbraucht: 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Mit dem folgenden Cmdlet können Sie Ihr Limit anzeigen:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Verwenden Sie zum Entfernen von Netzwerklimits `Remove-StorageSyncNetworkLimit`. Mit dem folgenden Befehl werden z.B. alle Netzwerklimits entfernt:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Verwenden von QoS für Speicher für Windows Server 
Wenn die Azure-Dateisynchronisierung auf einem virtuellen Computer auf einem Windows Server-Virtualisierungshost ausgeführt wird, können Sie Quality of Service für Speicher verwenden, um die Speicher-E/A-Nutzung zu steuern. Die Richtlinie für QoS für Speicher kann entweder als Maximum (oder Grenzwert, wie das StorageSyncNetwork-Limit oben erzwungen wird) oder als Minimum (bzw. Reservierung) festgelegt werden. Das Festlegen eines Minimums anstelle eines Maximums ermöglicht der Azure-Dateisynchronisierung die Nutzung der verfügbaren Speicherbandbreite, wenn diese nicht von anderen Workloads genutzt wird. Weitere Informationen finden Sie unter [Quality of Service für Speicher](/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Weitere Informationen
- [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](storage-sync-files-planning.md)
- [Bereitstellen der Azure-Dateisynchronisierung](storage-sync-files-deployment-guide.md)
- [Überwachen der Azure-Dateisynchronisierung](storage-sync-files-monitoring.md)
- [Problembehandlung für die Azure-Dateisynchronisierung](storage-sync-files-troubleshoot.md)