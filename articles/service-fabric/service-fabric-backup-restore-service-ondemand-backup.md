---
title: Bedarfsgesteuerte Sicherung in Azure Service Fabric
description: Verwenden Sie das Feature für Sicherungen und Wiederherstellungen in Service Fabric, um eine Sicherung Ihrer Anwendungsdaten auf Bedarfsbasis zu ermöglichen.
author: aagup
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: aagup
ms.openlocfilehash: d7986c8cd8d0714215c7b4dc57170be346e627ed
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928031"
---
# <a name="on-demand-backup-in-azure-service-fabric"></a>Bedarfsgesteuerte Sicherung in Azure Service Fabric

Sie können die Daten zuverlässiger zustandsbehafteter Dienste sowie von Reliable Actors zur Verwendung in Notfall- oder Datenverlustszenarios sichern.

Azure Service Fabric verfügt über Features zur [regelmäßigen Sicherung von Daten](service-fabric-backuprestoreservice-quickstart-azurecluster.md) und zur Sicherung von Daten auf Bedarfsbasis. Die bedarfsgesteuerte Sicherung ist nützlich, da sie vor _Datenverlust_/_Datenbeschädigung_ aufgrund geplanter Änderungen im zugrunde liegenden Dienst oder dessen Umgebung schützt.

Die Features zur bedarfsgesteuerten Sicherung sind für die Erfassung von Dienstzuständen hilfreich, noch bevor Sie einen Vorgang im Zusammenhang mit dem Dienst oder der Dienstumgebung manuell auslösen. Sie nehmen z.B. beim Upgrade oder Downgrade des Diensts eine Änderung in Dienstbinärdateien vor. In diesem Fall kann eine bedarfsgesteuerte Sicherung die Daten vor Beschädigung durch Fehler im Anwendungscode schützen.
## <a name="prerequisites"></a>Voraussetzungen

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


## <a name="triggering-on-demand-backup"></a>Auslösen der bedarfsgesteuerten Sicherung

Bedarfsgesteuerte Sicherung erfordert Speicherdetails zum Hochladen von Sicherungsdateien. Sie geben den Speicherort für die bedarfsgesteuerte Sicherung entweder in der Richtlinie für die regelmäßige Sicherung oder in einer Anforderung einer bedarfsgesteuerten Sicherung an.

### <a name="on-demand-backup-to-storage-specified-by-a-periodic-backup-policy"></a>In einer Richtlinie für die regelmäßige Sicherung festgelegte bedarfsgesteuerte Sicherung

Sie können die Richtlinie für die regelmäßige Sicherung zur Verwendung einer Partition eines zuverlässigen zustandsbehafteten Diensts oder Reliable Actors für zusätzliche bedarfsgesteuerte Sicherung im Speicher konfigurieren.

Das folgende Beispiel ist die Fortsetzung des Szenarios in [Aktivieren der regelmäßigen Sicherung für den zuverlässigen zustandsbehafteten Dienst und Reliable Actors](service-fabric-backuprestoreservice-quickstart-azurecluster.md#enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors). In diesem Fall aktivieren Sie eine Sicherungsrichtlinie für die Verwendung einer Partition, und eine Sicherung wird mit einer bestimmten Häufigkeit in Azure Storage durchgeführt.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell mit dem Microsoft.ServiceFabric.Powershell.Http-Modul

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' 

```

#### <a name="rest-call-using-powershell"></a>REST-Aufruf mithilfe von Powershell

Richten Sie mit der [BackupPartition](/rest/api/servicefabric/sfclient-api-backuppartition)-API die Auslösung für die bedarfsgesteuerte Sicherung für Partitions-ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22` ein.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Aktivieren Sie mit der [GetBackupProgress](/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress)-API die Nachverfolgung des [Fortschritts der bedarfsgesteuerten Sicherung](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress).

### <a name="on-demand-backup-to-specified-storage"></a>Bedarfsgesteuerte Sicherung auf dem angegebenen Speicher

Sie können die bedarfsgesteuerte Sicherung für eine Partition eines zuverlässigen zustandsbehafteten Diensts oder Reliable Actor anfordern. Stellen Sie die Speicherinformationen als Teil der Anforderung der bedarfsgesteuerten Sicherung bereit.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell mit dem Microsoft.ServiceFabric.Powershell.Http-Modul

```powershell

Backup-SFPartition -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22' -AzureBlobStore -ConnectionString  'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container'

```

#### <a name="rest-call-using-powershell"></a>REST-Aufruf mithilfe von Powershell

Richten Sie mit der [BackupPartition](/rest/api/servicefabric/sfclient-api-backuppartition)-API die Auslösung für die bedarfsgesteuerte Sicherung für Partitions-ID `974bd92a-b395-4631-8a7f-53bd4ae9cf22` ein. Beziehen Sie die folgenden Azure Storage-Informationen ein:

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$OnDemandBackupRequest = @{
    BackupStorage = $StorageInfo
}

$body = (ConvertTo-Json $OnDemandBackupRequest)
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/Backup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
```

Sie können mit der [GetBackupProgress](/rest/api/servicefabric/sfclient-api-getpartitionbackupprogress)-API die Nachverfolgung des [Fortschritts der bedarfsgesteuerten Sicherung](service-fabric-backup-restore-service-ondemand-backup.md#tracking-on-demand-backup-progress) einrichten.

### <a name="using-service-fabric-explorer"></a>Verwenden von Service Fabric Explorer
Stellen Sie sicher, dass der erweiterte Modus in den Service Fabric Explorer-Einstellungen aktiviert wurde.
1. Wählen Sie die gewünschten Partitionen aus, und klicken Sie auf „Aktionen“. 
2. Wählen Sie die Option zum Auslösen der Sicherung von Partitionen aus, und füllen Sie die Informationen für Azure aus:

    ![Partitionssicherung auslösen][0]

    oder FileShare:

    ![Partitionssicherung auslösen FileShare][1]

## <a name="tracking-on-demand-backup-progress"></a>Nachverfolgung des Prozesses der bedarfsgesteuerten Sicherung

Eine Partition eines zuverlässigen zustandsbehafteten Diensts oder Reliable Actor-Diensts akzeptiert nur eine Anforderung für die bedarfsgesteuerte Sicherung auf einmal. Eine andere Anforderung kann nur akzeptiert werden, wenn die aktuelle Anforderung der bedarfsgesteuerten Sicherung abgeschlossen ist.

Unterschiedliche Partitionen können gleichzeitig Anforderungen der bedarfsgesteuerten Sicherung auslösen.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell mit dem Microsoft.ServiceFabric.Powershell.Http-Modul

```powershell

Get-SFPartitionBackupProgress -PartitionId '974bd92a-b395-4631-8a7f-53bd4ae9cf22'

```
#### <a name="rest-call-using-powershell"></a>REST-Aufruf mithilfe von Powershell

```powershell
$url = "https://mysfcluster-backup.southcentralus.cloudapp.azure.com:19080/Partitions/974bd92a-b395-4631-8a7f-53bd4ae9cf22/$/GetBackupProgress?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3' 
$backupResponse = (ConvertFrom-Json $response.Content) 
$backupResponse
```

Anforderungen der bedarfsgesteuerten Sicherung können die folgenden Zustände aufweisen:

- **Akzeptiert**: Die Sicherung wurde auf der Partition gestartet und wird nun ausgeführt.
  ```
  BackupState             : Accepted
  TimeStampUtc            : 0001-01-01T00:00:00Z
  BackupId                : 00000000-0000-0000-0000-000000000000
  BackupLocation          :
  EpochOfLastBackupRecord :
  LsnOfLastBackupRecord   : 0
  FailureError            :
  ```
- **Erfolg**, **Fehler** oder **Timeout**: Eine angeforderte bedarfsgesteuerte Sicherung kann in jedem der folgenden Status abgeschlossen werden:
  - **Erfolg**: Der Sicherungsstatus _Erfolg_ gibt an, dass der Partitionsstatus erfolgreich gesichert wurde. In der Antwort werden _BackupEpoch_ und _BackupLSN_ für die Partition zusammen mit der Zeitangabe in UTC bereitgestellt.
    ```
    BackupState             : Success
    TimeStampUtc            : 2018-11-21T20:00:01Z
    BackupId                : 5d64b697-6acd-45a4-adbd-3d75e0078081
    BackupLocation          : SampleApp\MyStatefulService\974bd92a-b395-4631-8a7f-53bd4ae9cf22\2018-11-21 20.00.01.zip
    EpochOfLastBackupRecord : @{DataLossNumber=131873018908156893; ConfigurationNumber=8589934592}
    LsnOfLastBackupRecord   : 36
    FailureError            :
    ```
  - **Fehler**: Der Sicherungsstatus _Fehler_ gibt an, dass während der Sicherung des Status der Partition ein Fehler aufgetreten ist. Die Ursache des Fehlers wird in der Antwort angegeben.
    ```
    BackupState             : Failure
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_BACKUPCOPIER_UNEXPECTED_ERROR; Message=An error occurred during this operation.  Please check the trace logs for more details.}
    ```
  - **Timeout**: Ein Sicherungsstatus _Timeout_ gibt an, dass die Partitionsstatussicherung nicht in einem bestimmten Zeitraum erstellt werden konnte. Der Standardtimeoutwert beträgt zehn Minuten. Initiieren Sie in diesem Szenario eine neue Anforderung einer bedarfsgesteuerten Sicherung mit längerem [BackupTimeout](/rest/api/servicefabric/sfclient-api-backuppartition#backuptimeout).
    ```
    BackupState             : Timeout
    TimeStampUtc            : 0001-01-01T00:00:00Z
    BackupId                : 00000000-0000-0000-0000-000000000000
    BackupLocation          :
    EpochOfLastBackupRecord :
    LsnOfLastBackupRecord   : 0
    FailureError            : @{Code=FABRIC_E_TIMEOUT; Message=The request of backup has timed out.}
    ```

## <a name="next-steps"></a>Nächste Schritte

- [Grundlegendes zur Konfiguration der regelmäßigen Sicherung](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [BackupRestore-REST-API-Referenz](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/trigger-partition-backup.png
[1]: ./media/service-fabric-backuprestoreservice/trigger-backup-fileshare.png
