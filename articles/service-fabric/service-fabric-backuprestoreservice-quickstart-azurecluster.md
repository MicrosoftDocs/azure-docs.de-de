---
title: Regelmäßiges Sichern und Wiederherstellen in Azure Service Fabric
description: Verwenden Sie das Feature für regelmäßige Sicherungen und Wiederherstellungen von Service Fabric, um eine regelmäßige Datensicherung Ihrer Anwendungsdaten zu ermöglichen.
ms.topic: conceptual
ms.date: 5/24/2019
ms.openlocfilehash: 2d167b261f9b5915a970b4c219113f0765c039cb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98927983"
---
# <a name="periodic-backup-and-restore-in-an-azure-service-fabric-cluster"></a>Regelmäßiges Sichern und Wiederherstellen in einem Azure Service Fabric-Cluster
> [!div class="op_single_selector"]
> * [Cluster in Azure](service-fabric-backuprestoreservice-quickstart-azurecluster.md) 
> * [Eigenständige Cluster](service-fabric-backuprestoreservice-quickstart-standalonecluster.md)
> 

Service Fabric ist eine Plattform für verteilte Systeme, die das Entwickeln und Verwalten zuverlässiger, verteilter auf Microservices basierender Cloudanwendungen vereinfacht. Sie ermöglicht die Ausführung von zustandslosen und zustandsbehafteten Microservices. Zustandsbehaftete Dienste können einen änderbaren, autoritativen Zustand über die Anforderung und die Antwort oder eine vollständige Transaktion hinaus beibehalten. Wenn ein zustandsbehafteter Dienst für längere Zeit ausfällt oder Informationen aufgrund eines Notfalls verloren gehen, muss der Dienst möglicherweise mit einer aktuellen Sicherung des Zustands wiederhergestellt werden, damit er wieder verfügbar ist, nachdem er erneut gestartet wurde.

Service Fabric repliziert den Zustand über mehrere Knoten, um sicherzustellen, dass der Dienst hoch verfügbar ist. Auch wenn ein Knoten im Cluster ausfällt, bleibt der Dienst verfügbar. In bestimmten Fällen ist es jedoch noch immer wünschenswert, dass die Dienstdaten auch bei größeren Ausfällen zuverlässig bleiben.
 
Ein Dienst könnte Daten beispielsweise zum Schutz vor folgenden Szenarien sichern:
- Im Fall des dauerhaften Verlusts eines gesamten Service Fabric-Clusters
- Dauerhafter Verlust eines Großteils der Replikate einer Dienstpartition
- Administrative Fehler, durch die der Zustand versehentlich gelöscht oder beschädigt wird. Ein Administrator mit ausreichenden Berechtigungen löscht z.B. versehentlich den Dienst.
- Fehler im Dienst, die zu einer Beschädigung von Daten führen. Dies kann beispielsweise bei einem Dienstcode-Upgrade geschehen, bei dem fehlerhafte Daten in eine Reliable Collection geschrieben werden. In diesem Fall müssen unter Umständen der Code und die Daten in einen früheren Zustand zurückversetzt werden.
- Offline-Datenverarbeitung. Es kann zweckmäßig sein, Daten für Business Intelligence separat von dem Dienst, der die Daten generiert, offline zu verarbeiten.

Service Fabric bietet eine integrierte API zum [Sichern und Wiederherstellen](service-fabric-reliable-services-backup-restore.md) des Diensts zu einem bestimmten Zeitpunkt. Anwendungsentwickler können diese APIs verwenden, um den Zustand des Diensts in regelmäßigen Abständen zu sichern. Wenn Dienstadministratoren eine Sicherung zu einem bestimmten Zeitpunkt von außerhalb des Diensts auslösen möchten, wie z.B. vor dem Upgrade der Anwendung, müssen Entwickler zudem das Sichern (und Wiederherstellen) als API über den Dienst verfügbar machen. Für das Verwalten von Sicherungen fallen zusätzliche Kosten an. Sie möchten beispielsweise jede halbe Stunde fünf inkrementelle Sicherungen und dann eine vollständige Sicherung erstellen. Nach der vollständigen Sicherung können Sie die vorherigen inkrementellen Sicherungen löschen. Dieser Ansatz erfordert zusätzlichen Code, der zu zusätzlichen Kosten während der Anwendungsentwicklung führt.

Der Sicherungs- und Wiederherstellungsdienst in Service Fabric ermöglicht die einfache und automatische Sicherung von Informationen, die in zustandsbehafteten Diensten gespeichert sind. Die regelmäßige Sicherung von Anwendungsdaten ist von grundlegender Bedeutung für den Schutz vor Datenverlust und Nichtverfügbarkeit von Diensten. Service Fabric bietet einen optionalen Dienst für Sicherungen und Wiederherstellungen, mit dem Sie die regelmäßige Sicherung der statusbehafteten zuverlässigen Dienste (einschließlich der Actordienste) konfigurieren können, ohne zusätzlichen Code schreiben zu müssen. Damit wird auch das Wiederherstellen der zuvor erstellten Sicherungen vereinfacht. 


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
* Service Fabric-Cluster mit Fabric-Version 6.4 oder höher. Schritte zum Erstellen eines Service Fabric-Clusters mit der Azure-Ressourcevorlage finden Sie in diesem [Artikel](service-fabric-cluster-creation-via-arm.md).
* X.509-Zertifikat für die Verschlüsselung der Geheimnisse, die für die Verbindung mit dem Speicher zum Speichern von Sicherungen benötigt werden. Informationen zum Abrufen oder Erstellen eines X.509-Zertifikats finden Sie in diesem [Artikel](service-fabric-cluster-creation-via-arm.md).
* Service Fabric-Anwendung für statusbehaftete zuverlässige Dienste, die mit dem Service Fabric SDK, Version 3.0 oder höher, erstellt wurde. Für Anwendungen für .NET Core 2.0 muss die Anwendung mit dem Service Fabric SDK, Version 3.1 oder höher, erstellt werden.
* Erstellen Sie ein Azure Storage-Konto zum Speichern von Anwendungssicherungen.
* Installieren Sie das Modul Microsoft.ServiceFabric.PowerShell.Http (Vorschau), um Konfigurationsaufrufe vorzunehmen.

```powershell
    Install-Module -Name Microsoft.ServiceFabric.Powershell.Http -AllowPrerelease
```

> [!NOTE]
> Wenn Ihre PowerShellGet-Version kleiner als 1.6.0 ist, müssen Sie ein Update ausführen, um Unterstützung für das *-AllowPrerelease*-Flag hinzuzufügen:
>
> `Install-Module -Name PowerShellGet -Force`

* Stellen Sie mit dem Befehl `Connect-SFCluster` sicher, dass der Cluster verbunden ist, bevor Sie Konfigurationsanforderungen mit dem Microsoft.ServiceFabric.Powershell.Http-Modul vornehmen.

```powershell

    Connect-SFCluster -ConnectionEndpoint 'https://mysfcluster.southcentralus.cloudapp.azure.com:19080'   -X509Credential -FindType FindByThumbprint -FindValue '1b7ebe2174649c45474a4819dafae956712c31d3' -StoreLocation 'CurrentUser' -StoreName 'My' -ServerCertThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'  

```

## <a name="enabling-backup-and-restore-service"></a>Aktivieren des Diensts für Sicherungen und Wiederherstellungen

### <a name="using-azure-portal"></a>Verwenden des Azure-Portals

Aktivieren Sie das Kontrollkästchen `Include backup restore service` unter `+ Show optional settings` auf der Registerkarte `Cluster Configuration`.

![Aktivieren des Sicherungswiederherstellungsdiensts mit dem Portal][1]


### <a name="using-azure-resource-manager-template"></a>Verwenden von Azure Resource Manager-Vorlagen
Zuerst müssen Sie den _Dienst für Sicherungen und Wiederherstellungen_ in Ihrem Cluster aktivieren. Rufen Sie die Vorlage für den Cluster ab, den Sie bereitstellen möchten. Sie können entweder die [Beispielvorlagen](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) verwenden oder eine Resource Manager-Vorlage erstellen. Aktivieren Sie den _Dienst für Sicherungen und Wiederherstellungen_ mit den folgenden Schritten:

1. Überprüfen Sie, ob `apiversion` für die `Microsoft.ServiceFabric/clusters`-Ressource auf **`2018-02-01`** festgelegt ist. Wenn nicht, aktualisieren Sie sie wie im folgenden Codeausschnitt:

    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Jetzt aktivieren Sie den _Dienst für Sicherungen und Wiederherstellungen_ wie im folgenden Codeausschnitt, indem Sie den folgenden `addonFeatures`-Abschnitt unter dem Abschnitt `properties` hinzufügen: 

    ```json
        "properties": {
            ...
            "addonFeatures":  ["BackupRestoreService"],
            "fabricSettings": [ ... ]
            ...
        }

    ```
3. Konfigurieren Sie das X.509-Zertifikat für die Verschlüsselung der Anmeldeinformationen. Dies ist wichtig, um sicherzustellen, dass die Anmeldeinformationen, die für die Verbindung mit dem Speicher bereitgestellt wurden, vor dem Speichern verschlüsselt werden. Konfigurieren Sie das Verschlüsselungszertifikat wie im folgenden Codeausschnitt, indem Sie den folgenden `BackupRestoreService`-Abschnitt unter dem Abschnitt `fabricSettings` hinzufügen: 

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

4. Nachdem Sie die Clustervorlage mit den vorhergehenden Änderungen aktualisiert haben, wenden Sie die Änderungen an und schließen die Bereitstellung/das Upgrade ab. Anschließend wird der _Dienst für Sicherungen und Wiederherstellungen_ im Cluster gestartet. Der URI für diesen Dienst lautet `fabric:/System/BackupRestoreService`, und Sie finden den Dienst im Abschnitt mit Systemdiensten im Service Fabric Explorer. 

## <a name="enabling-periodic-backup-for-reliable-stateful-service-and-reliable-actors"></a>Aktivieren der regelmäßigen Sicherung für den zuverlässigen zustandsbehafteten Dienst und Reliable Actors
Jetzt erläutern wir schrittweise das Aktivieren der regelmäßigen Sicherung für den zuverlässigen zustandsbehafteten Dienst und Reliable Actors. Diese Schritte setzen Folgendes voraus:
- Der Cluster wurde unter Verwendung von X.509-Sicherheit mit dem _Dienst für Sicherungen und Wiederherstellungen_ eingerichtet.
- Ein zuverlässiger zustandsbehafteter Dienst wurde im Cluster bereitgestellt. Für diese Schnellstartanleitung lautet der Anwendungs-URI `fabric:/SampleApp`, und der URI für den zuverlässigen zustandsbehafteten Dienst, der zu dieser Anwendung gehört, lautet `fabric:/SampleApp/MyStatefulService`. Dieser Dienst wird mit einer einzelnen Partition bereitgestellt, und die Partitions-ID ist `974bd92a-b395-4631-8a7f-53bd4ae9cf22`.
- Das Clientzertifikat mit der Administratorrolle wird im Speichernamen _My_ (_Personal_) im Speicherort des _CurrentUser_-Zertifikatspeichers auf dem Computer installiert, auf dem die Skripts aufgerufen werden. In diesem Beispiel wird `1b7ebe2174649c45474a4819dafae956712c31d3` als Fingerabdruck dieses Zertifikats verwendet. Weitere Informationen zu Clientzertifikaten finden Sie unter [Rollenbasierte Zugriffssteuerung für Service Fabric-Clients](service-fabric-cluster-security-roles.md).

### <a name="create-backup-policy"></a>Erstellen der Sicherungsrichtlinie

Der erste Schritt ist das Erstellen der Sicherungsrichtlinie, die den Sicherungszeitplan, den Zielspeicher für Sicherungsdaten, den Richtliniennamen und die maximal zulässige Anzahl inkrementeller Sicherungen vor dem Auslösen einer vollständigen Sicherung und die Aufbewahrungsrichtlinie für den Sicherungsspeicher beschreibt. 

Verwenden Sie für den Sicherungsspeicher das oben erstellte Azure Storage-Konto. Container `backup-container` ist zum Speichern von Sicherungen konfiguriert. Ein Container mit diesem Namen wird während des Sicherungsuploads erstellt, wenn er nicht bereits vorhanden ist. Füllen Sie `ConnectionString` mit einer gültigen Verbindungszeichenfolge für das Azure Storage-Konto auf, und ersetzen Sie dabei `account-name` durch den Namen Ihres Speicherkontos und `account-key` durch Ihren Speicherkontoschlüssel.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell mit dem Microsoft.ServiceFabric.Powershell.Http-Modul

Führen Sie die folgenden PowerShell-Cmdlets zum Erstellen einer neuen Sicherungsrichtlinie aus. Ersetzen Sie `account-name` durch den Namen Ihres Speicherkontos und `account-key` durch Ihren Speicherkontoschlüssel.

```powershell

New-SFBackupPolicy -Name 'BackupPolicy1' -AutoRestoreOnDataLoss $true -MaxIncrementalBackups 20 -FrequencyBased -Interval 00:15:00 -AzureBlobStore -ConnectionString 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' -ContainerName 'backup-container' -Basic -RetentionDuration '10.00:00:00'

```

#### <a name="rest-call-using-powershell"></a>Rest-Aufruf mithilfe von PowerShell

Führen Sie das folgende PowerShell-Skript zum Aufrufen der erforderlichen REST-API aus, um die neue Richtlinie zu erstellen. Ersetzen Sie `account-name` durch den Namen Ihres Speicherkontos und `account-key` durch Ihren Speicherkontoschlüssel.

```powershell
$StorageInfo = @{
    ConnectionString = 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net'
    ContainerName = 'backup-container'
    StorageKind = 'AzureBlobStore'
}

$ScheduleInfo = @{
    Interval = 'PT15M'
    ScheduleKind = 'FrequencyBased'
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
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/BackupRestore/BackupPolicies/$/Create?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

```

#### <a name="using-service-fabric-explorer"></a>Verwenden von Service Fabric Explorer

1. Navigieren Sie in Service Fabric Explorer zur Registerkarte „Sicherungen“, und wählen Sie „Aktionen“ > „Sicherungsrichtlinie erstellen“ aus.

    ![Sicherungsrichtlinie erstellen][6]

2. Geben Sie alle Informationen ein. Für Azure-Cluster muss „AzureBlobStore“ ausgewählt werden.

    ![Erstellen einer Sicherungsrichtlinie für Azure Blob Storage][7]

### <a name="enable-periodic-backup"></a>Aktivieren der regelmäßigen Sicherung
Nach dem Definieren der Sicherungsrichtlinie zum Erfüllen der Datenschutzanforderungen der Anwendung muss die Sicherungsrichtlinie mit der Anwendung verknüpft werden. Je nach Anforderungen kann die Sicherungsrichtlinie einer Anwendung, einem Dienst oder einer Partition zugeordnet werden.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell mit dem Microsoft.ServiceFabric.Powershell.Http-Modul

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
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/EnableBackup?api-version=6.4"

Invoke-WebRequest -Uri $url -Method Post -Body $body -ContentType 'application/json' -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'
``` 

#### <a name="using-service-fabric-explorer"></a>Verwenden von Service Fabric Explorer

1. Wählen Sie eine Anwendung aus, und navigieren Sie zur Aktion. Klicken Sie auf „Anwendungssicherung aktivieren/aktualisieren“.

    ![Aktivieren der Anwendungssicherung][3]

2. Wählen Sie schließlich die gewünschte Richtlinie aus, und klicken Sie auf „Sicherung aktivieren“.

    ![Auswählen der Richtlinie][4]


### <a name="verify-that-periodic-backups-are-working"></a>Sicherstellen, dass die regelmäßigen Sicherungen funktionieren

Nach der Aktivierung der Sicherung auf Anwendungsebene werden alle Partitionen, die zu zuverlässigen statusbehafteten Diensten und Reliable Actors unter der Anwendung gehören, in regelmäßigen Abständen gemäß der zugeordneten Sicherungsrichtlinie gesichert. 

![Integritätsereignis der gesicherten Partition][0]

### <a name="list-backups"></a>Auflisten von Sicherungen

Sicherungen, die mit allen Partitionen verknüpft sind, die zu zuverlässigen statusbehafteten Diensten und Reliable Actors der Anwendung gehören, können mithilfe der _GetBackups_-API aufgelistet werden. Sicherungen können für eine Anwendung, einen Dienst oder eine Partition aufgelistet werden.

#### <a name="powershell-using-microsoftservicefabricpowershellhttp-module"></a>PowerShell mit dem Microsoft.ServiceFabric.Powershell.Http-Modul

```powershell
    
Get-SFApplicationBackupList -ApplicationId WordCount
```

#### <a name="rest-call-using-powershell"></a>Rest-Aufruf mithilfe von PowerShell

Führen Sie das folgende PowerShell-Skript zum Aufrufen der HTTP-API aus, um die für alle Partitionen in der Anwendung `SampleApp` erstellten Sicherungen aufzulisten.

```powershell
$url = "https://mysfcluster.southcentralus.cloudapp.azure.com:19080/Applications/SampleApp/$/GetBackups?api-version=6.4"

$response = Invoke-WebRequest -Uri $url -Method Get -CertificateThumbprint '1b7ebe2174649c45474a4819dafae956712c31d3'

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

#### <a name="using-service-fabric-explorer"></a>Verwenden von Service Fabric Explorer

Um Sicherungen in Service Fabric Explorer anzuzeigen, navigieren Sie zu einer Partition, und wählen Sie die Registerkarte „Sicherungen“ aus.

![Aufzählen von Sicherungen][5]

## <a name="limitation-caveats"></a>Einschränkungen/ Vorbehalte
- Service Fabric PowerShell-Cmdlets befinden sich im Vorschaumodus.
- Keine Unterstützung für Service Fabric-Cluster unter Linux.

## <a name="next-steps"></a>Nächste Schritte
- [Grundlegendes zur Konfiguration der regelmäßigen Sicherung](./service-fabric-backuprestoreservice-configure-periodic-backup.md)
- [REST-API-Referenz zu Sicherung/Wiederherstellung](/rest/api/servicefabric/sfclient-index-backuprestore)

[0]: ./media/service-fabric-backuprestoreservice/partition-backedup-health-event-azure.png
[1]: ./media/service-fabric-backuprestoreservice/enable-backup-restore-service-with-portal.png
[3]: ./media/service-fabric-backuprestoreservice/enable-app-backup.png
[4]: ./media/service-fabric-backuprestoreservice/enable-application-backup.png
[5]: ./media/service-fabric-backuprestoreservice/backup-enumeration.png
[6]: ./media/service-fabric-backuprestoreservice/create-bp.png
[7]: ./media/service-fabric-backuprestoreservice/creation-bp.png
