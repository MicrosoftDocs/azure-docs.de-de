---
title: Wiederherstellen von Sicherungsdaten in Azure Service Fabric
description: Verwenden Sie das Feature zum regelmäßigen Sichern und Wiederherstellen in Service Fabric, um gesicherte Anwendungsdaten wiederherzustellen.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: 8566d82ef0d91caff47ff17a9cb12fcdc8241884
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928028"
---
# <a name="restoring-backup-in-azure-service-fabric"></a>Wiederherstellen von Sicherungsdaten in Azure Service Fabric

In Azure Service Fabric können zustandsbehaftete Reliable Services sowie Reliable Actors nach Abschluss einer Transaktion mit Anforderung und Antwort einen veränderlichen, autoritativen Zustand beibehalten. Bei einem zustandsbehafteten Dienst kann es aufgrund eines Notfalls zu einem längeren Ausfall oder zum Verlust von Informationen kommen. In diesem Fall muss der Dienst auf der Grundlage der letzten akzeptablen Sicherung wiederhergestellt werden, damit er weiter verwendet werden kann.

Hierzu können Sie einen Dienst beispielsweise so konfigurieren, dass er seine Daten sichert, um für folgende Szenarien gewappnet zu sein:

- **Notfallwiederherstellung:** Dauerhafter Verlust eines gesamten Service Fabric-Clusters.
- **Datenverlust:** Dauerhafter Verlust eines Großteils der Replikate einer Dienstpartition.
- **Datenverlust:** Versehentliche Löschung oder Beschädigung des Diensts. Beispiel: Ein Administrator löscht versehentlich den Dienst.
- **Datenbeschädigung:** Fehler im Dienst, die zu einer Beschädigung von Daten führen. Eine Datenbeschädigung kann beispielsweise auftreten, wenn ein Dienstcodeupgrade fehlerhafte Daten in eine zuverlässige Sammlung schreibt. In diesem Fall müssen unter Umständen sowohl der Code als auch die Daten auf einen früheren Zustand zurückgesetzt werden.

## <a name="prerequisites"></a>Voraussetzungen

- Zum Auslösen einer Wiederherstellung muss der _Fault Analysis Service (FAS)_ (Fehleranalysedienst) für den Cluster aktiviert sein.
- Der _Backup Restore Service (BRS)_ (Sicherungswiederherstellungsdienst) hat die Sicherung erstellt.
- Die Wiederherstellung kann nur auf einer Partition ausgelöst werden.
- Installieren Sie das Modul Microsoft.ServiceFabric.PowerShell.Http (Vorschau), um Konfigurationsaufrufe vorzunehmen.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

> [!NOTE]
> Wenn Ihre PowerShellGet-Version kleiner als 1.6.0 ist, müssen Sie ein Update ausführen, um Unterstützung für das *-AllowPrerelease*-Flag hinzuzufügen:
>
> `Install-Module -Name PowerShellGet -Force`

- Stellen Sie mit dem Befehl `Connect-SFCluster` sicher, dass der Cluster verbunden ist, bevor Sie Konfigurationsanforderungen mit dem Microsoft.ServiceFabric.Powershell.Http-Modul vornehmen.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```


## <a name="triggered-restore"></a>Ausgelöste Wiederherstellung

Eine Wiederherstellung kann für jedes der folgenden Szenarien ausgelöst werden:

- Datenwiederherstellung für die _Notfallwiederherstellung_
- Datenwiederherstellung im Falle von _Datenbeschädigung/Datenverlust_

### <a name="data-restore-in-the-case-of-disaster-recovery"></a>Datenwiederherstellung für die Notfallwiederherstellung

Sollte ein gesamter Service Fabric-Cluster verloren gehen, können Sie die Daten für die Partitionen des zuverlässigen zustandsbehafteten Diensts und der Reliable Actors wiederherstellen. Die gewünschte Sicherung kann bei Verwendung der [GetBackup-API mit Sicherungsspeicherdetails](/rest/api/servicefabric/sfclient-api-getbackupsfrombackuplocation) aus der Liste ausgewählt werden. Die Sicherungen können für eine Anwendung, einen Dienst oder eine Partition aufgelistet werden.

Im folgenden Beispiel wird davon ausgegangen, dass es sich bei dem verloren gegangenen Cluster um den in [Aktivieren der regelmäßigen Sicherung für den zuverlässigen zustandsbehafteten Dienst und Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors) erwähnten Cluster handelt. In diesem Fall wurde `SampleApp` mit aktivierter Sicherungsrichtlinie bereitgestellt, und als Ziel für die Sicherungen wird Azure Storage verwendet.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell mit dem Microsoft.ServiceFabric.Powershell.Http-Modul

```powershell
Get-SFBackupsFromBackupLocation -Application -ApplicationName 'fabric:/SampleApp' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>REST-Aufruf mithilfe von Powershell

Führen Sie ein PowerShell-Skript aus, um unter Verwendung der REST-API eine Liste mit den Sicherungen zurückzugeben, die für alle Partitionen in der Anwendung `SampleApp` erstellt wurden. Die API benötigt die Sicherungsspeicherinformationen, um die verfügbaren Sicherungen auflisten zu können.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$BackupEntity = @{
    EntityKind = 'Application'
    ApplicationName='fabric:/SampleApp'
}

$BackupLocationAndEntityInfo = @{
    Storage = $StorageInfo
    BackupEntity = $BackupEntity
}

$body = (ConvertTo-Json $BackupLocationAndEntityInfo)
$url = "https://myalternatesfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Beispielausgabe für die oben genannte Ausführung:

```
BackupId                : b9577400-1131-4f88-b309-2bb1e943322c
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 20.55.16.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3334
CreationTimeUtc         : 2018-04-06T20:55:16Z
FailureError            : 
*
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
*
BackupId                : 69436834-c810-4163-9386-a7a800f78359
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.25.36.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3764
CreationTimeUtc         : 2018-04-06T21:25:36Z
FailureError            :
```

Wählen Sie zum Auslösen der Wiederherstellung eine der Sicherungen aus. Bei der aktuellen Sicherung für die Notfallwiederherstellung kann es sich beispielsweise um folgende Sicherung handeln:

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

Für die Wiederherstellungs-API müssen die Sicherungs-ID (_BackupId_) und der Sicherungsspeicherort (_BackupLocation_) angegeben werden.

Darüber hinaus muss eine Zielpartition im alternativen Cluster ausgewählt werden, wie im [Partitionsschema](service-fabric-concepts-partitioning.md#get-started-with-partitioning) dargestellt. Die alternative Clustersicherung wird in der Partition wiederhergestellt, die im Partitionsschema des ursprünglichen, verloren gegangenen Clusters angegeben ist.

Wenn die Partitions-ID für den alternativen Cluster `1c42c47f-439e-4e09-98b9-88b8f60800c6` lautet, können Sie sie der Partitions-ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22` des ursprünglichen Clusters zuordnen, indem Sie den hohen und niedrigen Schlüssel für _Ranged Partitioning (UniformInt64Partition)_ (Bereichsbasierte Partitionierung (UniformInt64Partition)) vergleichen.

Für _Named Partitioning_ (Namensbasierte Partitionierung) wird der Name-Wert verglichen, um die Zielpartition im alternativen Cluster zu identifizieren.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell mit dem Microsoft.ServiceFabric.Powershell.Http-Modul

```powershell

Restore-SFPartition  -PartitionId '1c42c47f-439e-4e09-98b9-88b8f60800c6' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip' -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>REST-Aufruf mithilfe von Powershell

Verwenden Sie die folgende [Wiederherstellungs-API](/rest/api/servicefabric/sfclient-api-restorepartition), um die Wiederherstellung anhand der Sicherungsclusterpartition anzufordern:

```powershell

$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4'
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
    BackupStorage  = $StorageInfo
}

$body = (ConvertTo-Json $RestorePartitionReference) 
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/1c42c47f-439e-4e09-98b9-88b8f60800c6/$/Restore?api-version=6.4" 

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Den Status einer Wiederherstellung können Sie mithilfe von TrackRestoreProgress nachverfolgen.

> [!NOTE]
> Wenn Sie PowerShell zum Wiederherstellen einer Partition verwenden und „backuplocation“ das Zeichen „$“ enthält, versehen Sie es wie folgt mit Escapezeichen: ~.
>

### <a name="using-service-fabric-explorer"></a>Verwenden von Service Fabric Explorer
Sie können eine Wiederherstellung aus Service Fabric Explorer auslösen. Stellen Sie sicher, dass der erweiterte Modus in den Service Fabric Explorer-Einstellungen aktiviert wurde.
1. Wählen Sie die gewünschten Partitionen aus, und klicken Sie auf „Aktionen“. 
2. Wählen Sie die Option zum Auslösen der Wiederherstellung von Partitionen aus, und füllen Sie die Informationen für Azure aus:

    ![Partitionswiederherstellung auslösen][2]

    oder FileShare:

    ![Partitionswiederherstellung FileShare auslösen][3]

### <a name="data-restore-for-_data-corruption__data-loss_"></a>Datenwiederherstellung im Falle von _Datenbeschädigung_/_Datenverlust_

Bei einem _Datenverlust_ oder einer _Datenbeschädigung_ können gesicherte Partitionen für den zuverlässigen zustandsbehafteten Dienst und Reliable Actors auf der Grundlage einer der ausgewählten Sicherungen wiederhergestellt werden.

Das folgende Beispiel baut auf [Aktivieren der regelmäßigen Sicherung für den zuverlässigen zustandsbehafteten Dienst und Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors) auf. In diesem Beispiel ist für die Partition eine Sicherungsrichtlinie aktiviert, und der Dienst erstellt im gewünschten Intervall Sicherungen in Azure Storage.

Wählen Sie eine Sicherung aus der Ausgabe der [GetBackup-API](service-fabric-backuprestoreservice-quickstart-azurecluster.md#list-backups) aus. In diesem Szenario basiert die Sicherung auf dem gleichen Cluster wie zuvor.

Wählen Sie zum Auslösen der Wiederherstellung eine Sicherung aus der Liste aus. Wählen Sie für den aktuellen Fall von _Datenverlust_/_Datenbeschädigung_ die folgende Sicherung aus:

```
BackupId                : b0035075-b327-41a5-a58f-3ea94b68faa4
BackupChainId           : b9577400-1131-4f88-b309-2bb1e943322c
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=974bd92a-b395-4631-8a7f-53bd4ae9cf22}
BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 3552
CreationTimeUtc         : 2018-04-06T21:10:27Z
FailureError            :
```

Geben Sie für die Wiederherstellungs-API die Sicherungs-ID (_BackupId_) und den Sicherungsspeicherort (_BackupLocation_) an. Da für den Cluster die Sicherung aktiviert ist, identifiziert der _Backup Restore Service (BRS)_ (Sicherungswiederherstellungsdienst) in Service Fabric den korrekten Speicherort anhand der zugeordneten Sicherungsrichtlinie.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell mit dem Microsoft.ServiceFabric.Powershell.Http-Modul

```powershell
Restore-SFPartition  -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -BackupId 'b0035075-b327-41a5-a58f-3ea94b68faa4' -BackupLocation 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'

```

#### <a name="rest-call-using-powershell"></a>REST-Aufruf mithilfe von Powershell

```powershell
$RestorePartitionReference = @{
    BackupId = 'b0035075-b327-41a5-a58f-3ea94b68faa4',
    BackupLocation = 'SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-04-06 21.10.27.zip'
}

$body = (ConvertTo-Json $RestorePartitionReference)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Restore?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Den Status des Wiederherstellungsvorgangs können Sie mithilfe von TrackRestoreProgress nachverfolgen.

> [!NOTE]
> Wenn Sie PowerShell zum Wiederherstellen einer Partition verwenden und „backuplocation“ das Zeichen „$“ enthält, versehen Sie es wie folgt mit Escapezeichen: ~.
>

## <a name="track-restore-progress"></a>Nachverfolgen des Wiederherstellungsstatus

Eine Partition eines zuverlässigen zustandsbehafteten Diensts oder Reliable Actor-Diensts akzeptiert nicht mehrere Anforderungen für eine Wiederherstellung gleichzeitig. Weitere Anforderungen werden von einer Partition erst nach Abschluss der aktuellen Wiederherstellungsanforderung akzeptiert. Für unterschiedliche Partitionen können gleichzeitig mehrere Wiederherstellungsanforderungen ausgelöst werden.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell mit dem Microsoft.ServiceFabric.Powershell.Http-Modul

```powershell
    Get-SFPartitionRestoreProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'
```

#### <a name="rest-call-using-powershell"></a>REST-Aufruf mithilfe von Powershell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetRestoreProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

$restoreResponse = (ConvertFrom-Json $response.Content)
$restoreResponse | Format-List
```

Die Wiederherstellungsanforderung durchläuft folgende Zustände in der angegebenen Reihenfolge:

1. **Akzeptiert**: Der Wiederherstellungszustand _Accepted_ (Akzeptiert) gibt an, dass die angeforderte Partition mit korrekten Anforderungsparametern ausgelöst wurde.
    ```
    RestoreState  : Accepted
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
2. **InProgress** (In Bearbeitung): Der Wiederherstellungszustand _InProgress_ (In Bearbeitung) gibt an, dass die Partition mit der in der Anforderung genannten Sicherung wiederhergestellt wird. Die Partition meldet mit dem Zustand _dataloss_ einen Datenverlust.
    ```
    RestoreState  : RestoreInProgress
    TimeStampUtc  : 0001-01-01T00:00:00Z
    RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
    RestoredLsn   : 3552
    ```
    
3. **Erfolg**, **Fehler** oder **Timeout**: Eine angeforderte Wiederherstellung kann mit einem der folgenden Zustände abgeschlossen werden. Im Anschluss werden die Bedeutung und die Antwortdetails der einzelnen Zustände erläutert:
    - **Erfolg**: Der Wiederherstellungszustand _Success_ (Erfolgreich) gibt an, dass ein Partitionszustand wiederhergestellt wurde. Die Partition meldet die Zustände _RestoredEpoch_ und _RestoredLSN_ sowie die Zeit im UTC-Format.

        ```
        RestoreState  : Success
        TimeStampUtc  : 2018-11-22T11:22:33Z
        RestoredEpoch : @{DataLossNumber=131675205859825409; ConfigurationNumber=8589934592}
        RestoredLsn   : 3552
        ```        
    - **Fehler**: Der Wiederherstellungszustand _Failure_ (Fehler) gibt an, dass die Wiederherstellungsanforderung nicht erfolgreich war. Die Ursache des Fehlers wird gemeldet.

        ```
        RestoreState  : Failure
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```
    - **Timeout**: Der Wiederherstellungszustand _Timeout_ gibt an, dass für die Anforderung ein Timeout aufgetreten ist. Erstellen Sie eine neue Wiederherstellungsanforderung mit einem höheren Wert für [RestoreTimeout](/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout). Das Standardtimeout beträgt zehn Minuten. Vergewissern Sie sich, dass sich die Partition nicht in einem Datenverlustzustand befindet, bevor Sie die Wiederherstellung erneut anfordern.
     
        ```
        RestoreState  : Timeout
        TimeStampUtc  : 0001-01-01T00:00:00Z
        RestoredEpoch : 
        RestoredLsn   : 0
        ```

## <a name="automatic-restore"></a>Automatische Wiederherstellung

Für die Partitionen des zuverlässigen zustandsbehafteten Diensts und der Reliable Actors im Service Fabric-Cluster kann eine _automatische Wiederherstellung_ konfiguriert werden. Legen Sie `AutoRestore` in der Sicherungsrichtlinie auf _true_ fest. Bei aktivierter _automatischer Wiederherstellung_ werden die Daten im Falle eines gemeldeten Datenverlusts anhand der neuesten Sicherung wiederhergestellt. Weitere Informationen finden Sie unter

- [Auto Restore Enablement in Backup Policy (Aktivieren der automatischen Wiederherstellung in einer Sicherungsrichtlinie)](service-fabric-backuprestoreservice-configure-periodic-backup.md#auto-restore-on-data-loss)
- [Restore Partition](/rest/api/servicefabric/sfclient-api-restorepartition) (Wiederherstellen einer Partition)
- [Get Partition Restore Progress](/rest/api/servicefabric/sfclient-api-getpartitionrestoreprogress) (Abrufen des Status der Partitionswiederherstellung)

## <a name="next-steps"></a>Nächste Schritte
- [Grundlegendes zur Konfiguration der regelmäßigen Sicherung](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [REST-API-Referenz zu Sicherung/Wiederherstellung](/rest/api/servicefabric/sfclient-index-backuprestore)

[2]: ./media/service-fabric-backuprestoreservice/restore-partition-backup.png
[3]: ./media/service-fabric-backuprestoreservice/restore-partition-fileshare.png
