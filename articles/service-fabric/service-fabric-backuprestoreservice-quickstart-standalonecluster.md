---
title: Regelmäßiges Sichern/Wiederherstellen in eigenständigem Azure Service Fabric
description: Verwenden Sie das Feature für regelmäßige Sicherungen und Wiederherstellungen eines eigenständigen Service Fabric, um eine regelmäßige Datensicherung Ihrer Anwendungsdaten zu ermöglichen.
ms.topic: conceptual
ms.date: 5/24/2019
ms.openlocfilehash: d4abf1cd4561a40aaafa5c01865eb12882884422
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927946"
---
# <a name="periodic-backup-and-restore-in-a-standalone-service-fabric"></a>Regelmäßiges Sichern und Wiederherstellen in einer eigenständigen Service Fabric-Umgebung
> [!div class="op_single_selector"]
> * [Cluster in Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Eigenständige Cluster](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric ist eine Plattform für verteilte Systeme, die das Entwickeln und Verwalten zuverlässiger verteilter Cloudanwendungen vereinfacht, die auf Microservices basieren. Sie ermöglicht die Ausführung von zustandslosen und zustandsbehafteten Microservices. Zustandsbehaftete Dienste können einen änderbaren, autoritativen Zustand über die Anforderung und die Antwort oder eine vollständige Transaktion hinaus beibehalten. Wenn ein zustandsbehafteter Dienst für längere Zeit ausfällt oder Informationen aufgrund eines Notfalls verloren gehen, muss der Dienst möglicherweise mit einer aktuellen Sicherung des Zustands wiederhergestellt werden, damit er nach dem Starten wieder verfügbar ist.

Service Fabric repliziert den Zustand über mehrere Knoten, um sicherzustellen, dass der Dienst hoch verfügbar ist. Auch wenn ein Knoten im Cluster ausfällt, bleibt der Dienst verfügbar. In bestimmten Fällen ist es jedoch noch immer wünschenswert, dass die Dienstdaten auch bei größeren Ausfällen zuverlässig bleiben.
 
Ein Dienst muss seine Daten möglicherweise z. B. zum Schutz vor folgenden Szenarios sichern:
- Dauerhafter Verlust eines gesamten Service Fabric-Clusters.
- Dauerhafter Verlust eines Großteils der Replikate einer Dienstpartition
- Administrative Fehler, durch die der Zustand versehentlich gelöscht oder beschädigt wird. Ein Administrator mit ausreichenden Berechtigungen löscht z.B. versehentlich den Dienst.
- Fehler im Dienst, die zu einer Beschädigung von Daten führen. Dies kann beispielsweise bei einem Dienstcode-Upgrade geschehen, bei dem fehlerhafte Daten in eine Reliable Collection geschrieben werden. In diesem Fall müssen unter Umständen der Code und die Daten in einen früheren Zustand zurückversetzt werden.
- Offline-Datenverarbeitung. Es kann zweckmäßig sein, Daten für Business Intelligence separat von dem Dienst, der die Daten generiert, offline zu verarbeiten.

Service Fabric bietet eine integrierte API für [Zeitpunktsicherung und -wiederherstellung](service-fabric-reliable-services-backup-restore.md). Anwendungsentwickler können diese APIs verwenden, um den Zustand des Diensts in regelmäßigen Abständen zu sichern. Wenn Dienstadministratoren eine Sicherung zu einem bestimmten Zeitpunkt von außerhalb des Diensts auslösen möchten (z. B. vor dem Upgrade der Anwendung), müssen Entwickler zudem das Sichern (und Wiederherstellen) über den Dienst mithilfe einer API ermöglichen. Für das Verwalten von Sicherungen fallen zusätzliche Kosten an. Sie möchten beispielsweise jede halbe Stunde fünf inkrementelle Sicherungen und dann eine vollständige Sicherung erstellen. Nach der vollständigen Sicherung können Sie die vorherigen inkrementellen Sicherungen löschen. Dieser Ansatz erfordert zusätzlichen Code, der zu zusätzlichen Kosten während der Anwendungsentwicklung führt.

Die Sicherung der Anwendungsdaten in regelmäßigen Abständen ist eine grundlegende Notwendigkeit beim Verwalten einer verteilten Anwendung und zum Schutz vor Datenverlusten oder einer längeren Beeinträchtigung der Verfügbarkeit des Diensts. Service Fabric bietet einen optionalen Dienst für Sicherungen und Wiederherstellungen, mit dem Sie die regelmäßige Sicherung der statusbehafteten zuverlässigen Dienste (einschließlich der Actordienste) konfigurieren können, ohne zusätzlichen Code schreiben zu müssen. Damit wird auch das Wiederherstellen der zuvor erstellten Sicherungen vereinfacht. 

Service Fabric stellt einen Satz von APIs für die folgende Funktionalität im Zusammenhang mit dem Feature für regelmäßige Sicherungen und Wiederherstellungen bereit:

- Planen regelmäßiger Sicherungen der statusbehafteten zuverlässigen Dienste und der Reliable Actors mit Unterstützung zum Hochladen von Sicherungen in (externe) Speicherorte. Unterstützte Speicherorte
    - Azure Storage
    - Dateifreigabe (lokal)
- Auflisten von Sicherungen
- Auslösen einer ungeplanten Sicherung einer Partition
- Wiederherstellen einer Partition mithilfe der vorherigen Sicherung
- Zeitweiliges Aussetzen von Sicherungen
- Verwalten der Aufbewahrung von Sicherungen (demnächst)

## <a name="prerequisites"></a>Voraussetzungen
* Service Fabric-Cluster mit Fabric-Version 6.4 oder höher. Informieren Sie sich in diesem [Artikel](service-fabric-cluster-creation-for-windows-server.md) über die Schritte zum Herunterladen des erforderlichen Pakets.
* X.509-Zertifikat für die Verschlüsselung der Geheimnisse, die für die Verbindung mit dem Speicher zum Speichern von Sicherungen benötigt werden. Informieren Sie sich in diesem [Artikel](service-fabric-windows-cluster-x509-security.md) darüber, wie Sie ein selbstsigniertes X.509-Zertifikat abrufen oder erstellen.

* Service Fabric-Anwendung für statusbehaftete zuverlässige Dienste, die mit dem Service Fabric SDK, Version 3.0 oder höher, erstellt wurde. Anwendungen für .NET Core 2.0 sollten mit Version 3.1 oder höher des Service Fabric-SDK erstellt worden sein.
* Installieren Sie das Modul Microsoft.ServiceFabric.PowerShell.Http (Vorschau), um Konfigurationsaufrufe vorzunehmen.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.PowerShell.Http -AllowPrerelease
```

> [!NOTE]
> Wenn Ihre PowerShellGet-Version kleiner als 1.6.0 ist, müssen Sie ein Update ausführen, um Unterstützung für das *-AllowPrerelease*-Flag hinzuzufügen:
>
> `Install-Module -Name PowerShellGet -Force`

* Stellen Sie mit dem Befehl `Connect-SFCluster` sicher, dass eine Verbindung mit dem Cluster besteht, bevor Sie Konfigurationsanforderungen mit dem Modul „Microsoft.ServiceFabric.PowerShell.Http“ vornehmen.

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

4. Wenn Sie die Clusterkonfigurationsdatei mit den vorstehenden Änderungen aktualisiert haben, wenden Sie die Änderungen an, und schließen die Bereitstellung/das Upgrade ab. Anschließend wird der _Dienst für Sicherungen und Wiederherstellungen_ im Cluster gestartet. Der URI für diesen Dienst lautet `fabric:/System/BackupRestoreService`, und Sie finden den Dienst im Abschnitt mit Systemdiensten im Service Fabric Explorer. 



## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Aktivieren der regelmäßigen Sicherung für den zuverlässigen zustandsbehafteten Dienst und Reliable Actors
Jetzt erläutern wir schrittweise das Aktivieren der regelmäßigen Sicherung für den zuverlässigen zustandsbehafteten Dienst und Reliable Actors. Diese Schritte setzen Folgendes voraus:
- Der Cluster wurde mit dem Sicherungs- und Wiederherstellungsdienst konfiguriert.
- Ein zuverlässiger zustandsbehafteter Dienst wurde im Cluster bereitgestellt. Für diese Schnellstartanleitung lautet der Anwendungs-URI `fabric:/SampleApp`, und der URI für den zuverlässigen zustandsbehafteten Dienst, der zu dieser Anwendung gehört, lautet `fabric:/SampleApp/MyStatefulService`. Dieser Dienst wird mit einer einzelnen Partition bereitgestellt, und die Partitions-ID ist `23aebc1e-e9ea-4e16-9d5c-e91a614fefa7`.  

### <a name="create-backup-policy"></a>Erstellen der Sicherungsrichtlinie

Der erste Schritt ist das Erstellen der Sicherungsrichtlinie, die den Sicherungszeitplan, den Zielspeicher für Sicherungsdaten, den Richtliniennamen und die maximal zulässige Anzahl inkrementeller Sicherungen vor dem Auslösen einer vollständigen Sicherung und die Aufbewahrungsrichtlinie für den Sicherungsspeicher beschreibt. 

Erstellen Sie die Dateifreigabe für den Sicherungsspeicher, und gewähren Sie allen Computern von Service Fabric-Knoten Lese-/Schreibzugriff auf diese Dateifreigabe. In diesem Beispiel wird vorausgesetzt, dass die Freigabe mit dem Namen `BackupStore` auf `StorageServer` vorhanden ist.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell mit dem Modul „Microsoft.ServiceFabric.PowerShell.Http“

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -FileShare -Path '\\StorageServer\BackupStore' -Basic -RetentionDuration '10.00:00:00'

```
#### <a name="rest-call-using-powershell"></a>Rest-Aufruf mithilfe von PowerShell

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

#### <a name="using-service-fabric-explorer"></a>Verwenden von Service Fabric Explorer

1. Navigieren Sie in Service Fabric Explorer zur Registerkarte „Sicherungen“, und wählen Sie „Aktionen“ > „Sicherungsrichtlinie erstellen“ aus.

    ![Sicherungsrichtlinie erstellen][6]

2. Geben Sie alle Informationen ein. Bei eigenständigen Clustern sollte „FileShare“ ausgewählt werden.

    ![Erstellen einer Dateifreigabe für eine Sicherungsrichtlinie][7]

### <a name="enable-periodic-backup"></a>Aktivieren der regelmäßigen Sicherung
Nach dem Definieren der Richtlinie zum Erfüllen der Datenschutzanforderungen der Anwendung muss die Sicherungsrichtlinie mit der Anwendung verknüpft werden. Je nach Anforderungen kann die Sicherungsrichtlinie einer Anwendung, einem Dienst oder einer Partition zugeordnet werden.


#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell mit dem Modul „Microsoft.ServiceFabric.PowerShell.Http“

```powershell
Enable-SFApplicationBackup -ApplicationId 'SampleApp' -BackupPolicyName 'BackupPolicy1'
```

#### <a name="rest-call-using-powershell"></a>Rest-Aufruf mithilfe von PowerShell
Führen Sie das folgende PowerShell-Skript zum Aufrufen der erforderlichen REST-API aus, um die Sicherungsrichtlinie mit dem Namen `BackupPolicy1`, die im obigen Schritt erstellt wurde, der Anwendung `SampleApp` zuzuordnen.

```powershell
$BackupPolicyReference = @{
    BackupPolicyName = 'BackupPolicy1'
}

$body = (ConvertTo-Json $BackupPolicyReference)
$url = "http://localhost:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json'
``` 

#### <a name="using-service-fabric-explorer"></a>Verwenden von Service Fabric Explorer

1. Wählen Sie eine Anwendung aus, und navigieren Sie zur Aktion. Klicken Sie auf „Anwendungssicherung aktivieren/aktualisieren“.

    ![Aktivieren der Anwendungssicherung][3] 

2. Wählen Sie schließlich die gewünschte Richtlinie aus, und klicken Sie auf *Sicherung aktivieren*.

    ![Auswählen der Richtlinie][4]

### <a name="verify-that-periodic-backups-are-working"></a>Sicherstellen, dass die regelmäßigen Sicherungen funktionieren

Nach Aktivieren der Sicherung für die Anwendung werden alle Partitionen, die zu zuverlässigen statusbehafteten Diensten und Reliable Actors unter der Anwendung gehören, in regelmäßigen Abständen gemäß der zugeordneten Sicherungsrichtlinie gesichert.

![Integritätsereignis der gesicherten Partition][0]

### <a name="list-backups"></a>Auflisten von Sicherungen

Sicherungen, die mit allen Partitionen verknüpft sind, die zu zuverlässigen statusbehafteten Diensten und Reliable Actors der Anwendung gehören, können mithilfe der _GetBackups_-API aufgelistet werden. Je nach Anforderung können die Sicherungen für eine Anwendung, einen Dienst oder eine Partition aufgelistet werden.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell mit dem Modul „Microsoft.ServiceFabric.PowerShell.Http“

```powershell
    Get-SFApplicationBackupList -ApplicationId WordCount     
```

#### <a name="rest-call-using-powershell"></a>Rest-Aufruf mithilfe von PowerShell

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

#### <a name="using-service-fabric-explorer"></a>Verwenden von Service Fabric Explorer

Um Sicherungen in Service Fabric Explorer anzuzeigen, navigieren Sie zu einer Partition, und wählen Sie die Registerkarte „Sicherungen“ aus.

![Aufzählen von Sicherungen][5]

## <a name="limitation-caveats"></a>Einschränkungen/ Vorbehalte
- Service Fabric PowerShell-Cmdlets befinden sich im Vorschaumodus.
- Keine Unterstützung für Service Fabric-Cluster unter Linux.

## <a name="next-steps"></a>Nächste Schritte
- [Grundlegendes zur Konfiguration der regelmäßigen Sicherung](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [REST-API-Referenz zu Sicherung/Wiederherstellung](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/create-bp-fileshare.png
