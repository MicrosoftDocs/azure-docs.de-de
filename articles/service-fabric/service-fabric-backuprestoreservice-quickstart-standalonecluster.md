---
title: Regelmäßiges Sichern und Wiederherstellen in Azure Service Fabric | Microsoft-Dokumentation
description: Verwenden Sie das Feature für regelmäßige Sicherungen und Wiederherstellungen von Service Fabric, um eine regelmäßige Datensicherung Ihrer Anwendungsdaten zu ermöglichen.
services: service-fabric
documentationcenter: .net
author: hrushib
manager: chackdan
editor: hrushib
ms.assetid: FAADBCAB-F0CF-4CBC-B663-4A6DCCB4DEE1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/24/2019
ms.author: hrushib
ms.openlocfilehash: 154efffcb1f86907fefecc060419c1d9450470f8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237341"
---
# <a name="periodic-backup-and-restore-in-azure-service-fabric"></a>Regelmäßiges Sichern und Wiederherstellen in Azure Service Fabric
> [!div class="op_single_selector"]
> * [Cluster in Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Eigenständige Cluster](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric ist eine Plattform für verteilte Systeme, die das Entwickeln und Verwalten zuverlässiger, verteilter auf Microservices basierender Cloudanwendungen vereinfacht. Sie ermöglicht die Ausführung von zustandslosen und zustandsbehafteten Microservices. Zustandsbehaftete Dienste können einen änderbaren, autoritativen Zustand über die Anforderung und die Antwort oder eine vollständige Transaktion hinaus beibehalten. Wenn ein zustandsbehafteter Dienst für längere Zeit ausfällt oder Informationen aufgrund eines Notfalls verloren gehen, muss der Dienst möglicherweise mit einer aktuellen Sicherung des Zustands wiederhergestellt werden, damit er wieder verfügbar ist, nachdem er erneut gestartet wurde.

Service Fabric repliziert den Zustand über mehrere Knoten, um sicherzustellen, dass der Dienst hoch verfügbar ist. Auch wenn ein Knoten im Cluster ausfällt, bleibt der Dienst verfügbar. In bestimmten Fällen ist es jedoch noch immer wünschenswert, dass die Dienstdaten auch bei größeren Ausfällen zuverlässig bleiben.
 
Ein Dienst könnte Daten beispielsweise zum Schutz vor folgenden Szenarien sichern:
- Dauerhafter Verlust eines gesamten Service Fabric-Clusters.
- Dauerhafter Verlust eines Großteils der Replikate einer Dienstpartition
- Administrative Fehler, durch die der Zustand versehentlich gelöscht oder beschädigt wird. Ein Administrator mit ausreichenden Berechtigungen löscht z.B. versehentlich den Dienst.
- Fehler im Dienst, die zu einer Beschädigung von Daten führen. Dies kann beispielsweise bei einem Dienstcode-Upgrade geschehen, bei dem fehlerhafte Daten in eine Reliable Collection geschrieben werden. In diesem Fall müssen unter Umständen der Code und die Daten in einen früheren Zustand zurückversetzt werden.
- Offline-Datenverarbeitung. Es kann zweckmäßig sein, Daten für Business Intelligence separat von dem Dienst, der die Daten generiert, offline zu verarbeiten.

Service Fabric bietet eine integrierte API zum [Sichern und Wiederherstellen](service-fabric-reliable-services-backup-restore.md) des Diensts zu einem bestimmten Zeitpunkt. Anwendungsentwickler können diese APIs verwenden, um den Zustand des Diensts in regelmäßigen Abständen zu sichern. Wenn Dienstadministratoren eine Sicherung zu einem bestimmten Zeitpunkt von außerhalb des Diensts auslösen möchten, wie z.B. vor dem Upgrade der Anwendung, müssen Entwickler zudem das Sichern (und Wiederherstellen) als API über den Dienst verfügbar machen. Für das Verwalten von Sicherungen fallen zusätzliche Kosten an. Sie möchten beispielsweise jede halbe Stunde fünf inkrementelle Sicherungen und dann eine vollständige Sicherung erstellen. Nach der vollständigen Sicherung können Sie die vorherigen inkrementellen Sicherungen löschen. Dieser Ansatz erfordert zusätzlichen Code, der zu zusätzlichen Kosten während der Anwendungsentwicklung führt.

Die Sicherung der Anwendungsdaten in regelmäßigen Abständen ist eine grundlegende Notwendigkeit beim Verwalten einer verteilten Anwendung und zum Schutz vor Datenverlusten oder einer längeren Beeinträchtigung der Verfügbarkeit des Diensts. Service Fabric bietet einen optionalen Dienst für Sicherungen und Wiederherstellungen, mit dem Sie die regelmäßige Sicherung der statusbehafteten zuverlässigen Dienste (einschließlich der Actordienste) konfigurieren können, ohne zusätzlichen Code schreiben zu müssen. Damit wird auch das Wiederherstellen der zuvor erstellten Sicherungen vereinfacht. 

Service Fabric stellt einen Satz von APIs für die folgende Funktionalität im Zusammenhang mit dem Feature für regelmäßige Sicherungen und Wiederherstellungen bereit:

- Planen regelmäßiger Sicherungen der statusbehafteten zuverlässigen Dienste und der Reliable Actors mit Unterstützung zum Hochladen von Sicherungen in (externe) Speicherorte. Unterstützte Speicherorte
    - Azure Storage
    - Dateifreigabe (lokal)
- Auflisten von Sicherungen
- Auslösen einer Ad-hoc-Sicherung einer Partition
- Wiederherstellen einer Partition mithilfe der vorherigen Sicherung
- Zeitweiliges Aussetzen von Sicherungen
- Verwalten der Aufbewahrung von Sicherungen (demnächst)

## <a name="prerequisites"></a>Voraussetzungen
* Service Fabric-Cluster mit Fabric-Version 6.2 und höher. Der Cluster muss unter Windows Server eingerichtet werden. Informieren Sie sich in diesem [Artikel](service-fabric-cluster-creation-for-windows-server.md) über die Schritte zum Herunterladen des erforderlichen Pakets.
* X.509-Zertifikat für die Verschlüsselung der Geheimnisse, die für die Verbindung mit dem Speicher zum Speichern von Sicherungen benötigt werden. Informieren Sie sich in diesem [Artikel](service-fabric-windows-cluster-x509-security.md) darüber, wie Sie ein selbstsigniertes X.509-Zertifikat abrufen oder erstellen.

* Service Fabric-Anwendung für statusbehaftete zuverlässige Dienste, die mit dem Service Fabric SDK, Version 3.0 oder höher, erstellt wurde. Für Anwendungen für .NET Core 2.0 muss die Anwendung mit dem Service Fabric SDK, Version 3.1 oder höher, erstellt werden.
* Installieren Sie das Microsoft.ServiceFabric.Powershell.Http-Modul [Vorschau], um Konfigurationsaufrufe vorzunehmen.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

* Stellen Sie mit dem Befehl `Connect-SFCluster` sicher, dass der Cluster verbunden ist, bevor Sie Konfigurationsanforderungen mit dem Microsoft.ServiceFabric.Powershell.Http-Modul vornehmen.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Aktivieren des Diensts für Sicherungen und Wiederherstellungen
Zuerst müssen Sie den _Dienst für Sicherungen und Wiederherstellungen_ in Ihrem Cluster aktivieren. Rufen Sie die Vorlage für den Cluster ab, den Sie bereitstellen möchten. Sie können die [Beispielvorlagen](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples) verwenden. Aktivieren Sie den _Dienst für Sicherungen und Wiederherstellungen_ mit den folgenden Schritten:

1. Überprüfen Sie, ob `apiversion` in der Clusterkonfigurationsdatei auf `10-2017` festgelegt ist. Wenn nicht, aktualisieren Sie sie wie im folgenden Codeausschnitt:

    ```json
    {
        "apiVersion": "10-2017",
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        ...
    }
    ```

2. Jetzt aktivieren Sie den _Dienst für Sicherungen und Wiederherstellungen_ wie im folgenden Codeausschnitt, indem Sie den folgenden `addonFeatures`-Abschnitt unter dem Abschnitt `properties` hinzufügen: 

    ```json
        "properties": {
            ...
            "addonFeatures": ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```

3. Konfigurieren Sie das X.509-Zertifikat für die Verschlüsselung der Anmeldeinformationen. Dies ist wichtig, um ggf. sicherzustellen, dass die Anmeldeinformationen, die für die Verbindung mit dem Speicher bereitgestellt wurden, vor dem Speichern verschlüsselt werden. Konfigurieren Sie das Verschlüsselungszertifikat wie im folgenden Codeausschnitt, indem Sie den folgenden `BackupRestoreService`-Abschnitt unter dem Abschnitt `fabricSettings` hinzufügen: 

    ```json
    "properties": {
        ...
        "addonFeatures": ["BackupRestoreService"],
        "fabricSettings": [{
            "name": "BackupRestoreService",
            "parameters":  [{
                "name": "SecretEncryptionCertThumbprint",
                "value": "[Thumbprint]"
            }]
        }
        ...
    }
    ```

4. Nachdem Sie die Clusterkonfigurationsdatei mit den vorhergehenden Änderungen aktualisiert haben, wenden Sie die Änderungen an und schließen die Bereitstellung/das Upgrade ab. Anschließend wird der _Dienst für Sicherungen und Wiederherstellungen_ im Cluster gestartet. Der URI für diesen Dienst lautet `fabric:/System/BackupRestoreService`, und Sie finden den Dienst im Abschnitt mit Systemdiensten im Service Fabric Explorer. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Aktivieren der regelmäßigen Sicherung für den zuverlässigen zustandsbehafteten Dienst und Reliable Actors
Jetzt erläutern wir schrittweise das Aktivieren der regelmäßigen Sicherung für den zuverlässigen zustandsbehafteten Dienst und Reliable Actors. Diese Schritte setzen Folgendes voraus:
- Der Cluster wurde mit dem _Dienst für Sicherungen und Wiederherstellungen_ eingerichtet.
- Ein zuverlässiger zustandsbehafteter Dienst wurde im Cluster bereitgestellt. Für diese Schnellstartanleitung lautet der Anwendungs-URI `fabric:/SampleApp`, und der URI für den zuverlässigen zustandsbehafteten Dienst, der zu dieser Anwendung gehört, lautet `fabric:/SampleApp/MyStatefulService`. Dieser Dienst wird mit einer einzelnen Partition bereitgestellt, und die Partitions-ID ist `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7`.  

### <a name="create-backup-policy"></a>Erstellen der Sicherungsrichtlinie

Der erste Schritt ist das Erstellen der Sicherungsrichtlinie, die den Sicherungszeitplan, den Zielspeicher für Sicherungsdaten, den Richtliniennamen und die maximal zulässige Anzahl inkrementeller Sicherungen vor dem Auslösen einer vollständigen Sicherung und die Aufbewahrungsrichtlinie für den Sicherungsspeicher beschreibt. 

Erstellen Sie die Dateifreigabe für den Sicherungsspeicher, und gewähren Sie allen Computern von Service Fabric-Knoten Lese-/Schreibzugriff auf diese Dateifreigabe. In diesem Beispiel wird vorausgesetzt, dass die Freigabe mit dem Namen `BackupStore` auf `StorageServer` vorhanden ist.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell mit dem Microsoft.ServiceFabric.Powershell.Http-Modul

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -FileShare -Path '\\StorageServer\BackupStore' -Basic -RetentionDuration '10.00:00:00'

```
#### <a name="rest-call-using-powershell"></a>REST-Aufruf mithilfe von Powershell

Führen Sie das folgende PowerShell-Skript zum Aufrufen der erforderlichen REST-API aus, um die neue Richtlinie zu erstellen.

```powershell
$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
}   

$StorageInfo = @{
    Path = '\\StorageServer\BackupStore'
    StorageKind = 'FileShare'
}

$RetentionPolicy = @{ 
    RetentionPolicyType = 'Basic'
    RetentionDuration =  'P10D'
}

$BackupPolicy = @{
    Name = 'BackupPolicy1'
    MaxIncrementalBackups = 20
    Schedule = $ScheduleInfo
    Storage = $StorageInfo
    RetentionPolicy = $RetentionPolicy
}

$body = (ConvertTo-Json $BackupPolicy)
$url = "http://localhost:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
```

### <a name="enable-periodic-backup"></a>Aktivieren der regelmäßigen Sicherung
Nach dem Definieren der Richtlinie zum Erfüllen der Datenschutzanforderungen der Anwendung muss die Sicherungsrichtlinie mit der Anwendung verknüpft werden. Je nach Anforderungen kann die Sicherungsrichtlinie einer Anwendung, einem Dienst oder einer Partition zugeordnet werden.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell mit dem Microsoft.ServiceFabric.Powershell.Http-Modul

```powershell
Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'
```

#### <a name="rest-call-using-powershell"></a>REST-Aufruf mithilfe von Powershell
Führen Sie das folgende PowerShell-Skript zum Aufrufen der erforderlichen REST-API aus, um die Sicherungsrichtlinie mit dem Namen `BackupPolicy1`, die im obigen Schritt erstellt wurde, der Anwendung `SampleApp` zuzuordnen.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

### <a name="verify-that-periodic-backups-are-working"></a>Sicherstellen, dass die regelmäßigen Sicherungen funktionieren

Nach Aktivieren der Sicherung für die Anwendung werden alle Partitionen, die zu zuverlässigen statusbehafteten Diensten und Reliable Actors unter der Anwendung gehören, in regelmäßigen Abständen gemäß der zugeordneten Sicherungsrichtlinie gesichert.

![Integritätsereignis der gesicherten Partition][0]

### <a name="list-backups"></a>Auflisten von Sicherungen

Sicherungen, die mit allen Partitionen verknüpft sind, die zu zuverlässigen statusbehafteten Diensten und Reliable Actors der Anwendung gehören, können mithilfe der _GetBackups_-API aufgelistet werden. Je nach Anforderung können die Sicherungen für eine Anwendung, einen Dienst oder eine Partition aufgelistet werden.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell mit dem Microsoft.ServiceFabric.Powershell.Http-Modul

```powershell
    Get-SFApplicationBackupList -ApplicationId WordCount     
```

#### <a name="rest-call-using-powershell"></a>REST-Aufruf mithilfe von Powershell

Führen Sie das folgende PowerShell-Skript zum Aufrufen der HTTP-API aus, um die für alle Partitionen in der Anwendung `SampleApp` erstellten Sicherungen aufzulisten.

```powershell
$url = "http://localhost:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get

$BackupPoints = (ConvertFrom-Json $response.Content)
$BackupPoints.Items
```

Beispielausgabe für die oben genannte Ausführung:

```
BackupId                : d7e4038e-2c46-47c6-9549-10698766e714
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.39.40.zip
BackupType              : Full
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2058
CreationTimeUtc         : 2018-04-01T19:39:40Z
FailureError            : 

BackupId                : 8c21398a-2141-4133-b4d7-e1a35f0d7aac
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 19.54.38.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2237
CreationTimeUtc         : 2018-04-01T19:54:38Z
FailureError            : 

BackupId                : fc75bd4c-798c-4c9a-beee-e725321f73b2
BackupChainId           : d7e4038e-2c46-47c6-9549-10698766e714
ApplicationName         : fabric:/SampleApp
ServiceName             : fabric:/SampleApp/MyStatefulService
PartitionInformation    : @{LowKey=-9223372036854775808; HighKey=9223372036854775807; ServicePartitionKind=Int64Range; Id=23aebc1e-e9ea-4e16-9d5c-e91a614fefa7}
BackupLocation          : SampleApp\MyStatefulService\23aebc1e-e9ea-4e16-9d5c-e91a614fefa7\2018-04-01 20.09.44.zip
BackupType              : Incremental
EpochOfLastBackupRecord : @{DataLossNumber=131670844862460432; ConfigurationNumber=8589934592}
LsnOfLastBackupRecord   : 2437
CreationTimeUtc         : 2018-04-01T20:09:44Z
FailureError            : 
```

## <a name="limitation-caveats"></a>Einschränkungen/ Vorbehalte
- Service Fabric PowerShell-Cmdlets befinden sich im Vorschaumodus.
- Keine Unterstützung für Service Fabric-Cluster unter Linux.

## <a name="next-steps"></a>Nächste Schritte
- [Grundlegendes zur Konfiguration der regelmäßigen Sicherung](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [REST-API-Referenz zu Sicherung/Wiederherstellung](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/PartitionBackedUpHealthEvent.png

