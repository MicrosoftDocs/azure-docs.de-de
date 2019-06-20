---
title: Hinzufügen oder Entfernen von Knoten für einen eigenständigen Service Fabric-Cluster | Microsoft Docs
description: Enthält Informationen zum Hinzufügen oder Entfernen von Knoten für einen Azure Service Fabric-Cluster auf einem physischen oder virtuellen Computer mit Windows Server, der lokal oder in einer Cloud angeordnet sein kann.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: bc6b8fc0-d2af-42f8-a164-58538be38d02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: dekapur
ms.openlocfilehash: 585d918026ca40bc1a04c55e2bac454492c55936
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60711032"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Hinzufügen oder Entfernen von Knoten für einen eigenständigen Service Fabric-Cluster unter Windows Server
Nachdem Sie Ihren [eigenständigen Service Fabric-Cluster auf Windows Server-Computern erstellt haben](service-fabric-cluster-creation-for-windows-server.md), können sich Ihre geschäftlichen Anforderungen ändern, und Sie müssen Knoten zu Ihrem Cluster hinzufügen oder daraus entfernen. Dieser Artikel enthält die ausführlichen Schritte, die hierfür erforderlich sind. Bitte beachten Sie, dass die Funktion für das Hinzufügen/Entfernen von Knoten in lokalen Entwicklungsclustern nicht unterstützt wird.

## <a name="add-nodes-to-your-cluster"></a>Hinzufügen von Knoten zum Cluster

1. Bereiten Sie den virtuellen Computer bzw. den Computer, den Sie dem Cluster hinzufügen möchten, anhand der Schritte zum [Planen und Vorbereiten der Service Fabric-Clusterbereitstellung](service-fabric-cluster-creation-for-windows-server.md) vor.
2. Identifizieren Sie, welcher Fehlerdomäne und Upgradedomäne Sie diesen virtuellen bzw. physischen Computer hinzufügen werden.
3. Stellen Sie eine Remotedesktopverbindung mit dem virtuellen bzw. physischen Computer her, den Sie dem Cluster hinzufügen möchten.
4. [Laden Sie das eigenständige Paket für Service Fabric für Windows Server auf den (virtuellen) Computer herunter](https://go.microsoft.com/fwlink/?LinkId=730690) (bzw. kopieren Sie es), und entzippen Sie das Paket.
5. Führen Sie PowerShell mit erhöhten Rechten aus, und navigieren Sie zum Speicherort des entzippten Pakets.
6. Führen Sie das Skript *AddNode.ps1* mit den Parametern aus, die den hinzuzufügenden neuen Knoten beschreiben. Im folgenden Beispiel wird ein neuer Knoten mit dem Namen VM5, dem Typ NodeType0 und der IP-Adresse 182.17.34.52 für UD1 und fd:/dc1/r0 hinzugefügt. *ExistingClusterConnectionEndPoint* ist ein Verbindungsendpunkt für einen Knoten, der bereits im Cluster vorhanden ist. Dies kann die IP-Adresse *aller* Knoten im Cluster sein.

    ```
    .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
    ```
    Nachdem das Skript ausgeführt wurde, können Sie prüfen, ob der neue Knoten hinzugefügt wurde, indem Sie das Cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) ausführen.

7. Zur Gewährleistung der Konsistenz über verschiedene Knoten im Cluster müssen Sie ein Konfigurationsupgrade initiieren. Führen Sie [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) aus, um die neueste Konfigurationsdatei abzurufen, und fügen Sie den neu hinzugefügten Knoten zum Abschnitt „Knoten“ hinzu. Sie sollten zudem immer über die neueste Clusterkonfiguration verfügen, für den Fall, dass Sie einen Cluster mit der gleichen Konfiguration erneut bereitstellen müssen.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
8. Führen Sie [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) aus, um mit dem Upgrade zu beginnen.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Sie können den Fortschritt des Upgrades in Service Fabric Explorer überwachen. Alternativ können Sie [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) ausführen.

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Hinzufügen von Knoten zu mit Windows-Sicherheit über gMSA konfigurierten Clustern
Bei Clustern, die über ein gMSA (Group Managed Service Account, gruppenverwaltetes Dienstkonto) (https://technet.microsoft.com/library/hh831782.aspx) konfiguriert wurden, kann ein neuer Knoten mit einem Konfigurationsupgrade hinzugefügt werden:
1. Führen Sie auf allen vorhandenen Knoten [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) aus, um die neueste Konfigurationsdatei abzurufen und im Abschnitt „Knoten“ Details zum neuen Knoten hinzuzufügen. Stellen Sie sicher, dass der neue Knoten zu demselben gruppenverwalteten Konto gehört. Dieses Konto sollte ein Administratorkonto auf allen Computern sein.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Führen Sie [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) aus, um mit dem Upgrade zu beginnen.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    Sie können den Fortschritt des Upgrades in Service Fabric Explorer überwachen. Alternativ können Sie [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) ausführen.

### <a name="add-node-types-to-your-cluster"></a>Hinzufügen von Knotentypen zum Cluster
Ändern Sie Ihre Konfiguration, um im Abschnitt „NodeTypes“ unter „Eigenschaften“ einen neuen Knotentyp hinzuzufügen, und starten Sie über [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) ein Konfigurationsupgrade. Nachdem das Upgrade abgeschlossen wurde, können Sie mit diesem Knotentyp neue Knoten zum Cluster hinzufügen.

## <a name="remove-nodes-from-your-cluster"></a>Entfernen von Knoten aus dem Cluster
Ein Knoten kann auf folgende Weise mit einem Konfigurationsupgrade aus einem Cluster entfernt werden:

1. Führen Sie [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration?view=azureservicefabricps) aus, um die neueste Konfigurationsdatei abzurufen, und *entfernen* Sie den Knoten aus dem Abschnitt „Knoten“.
Fügen Sie im Abschnitt „FabricSettings“ den Parameter „NodesToBeRemoved“ zum Abschnitt „Setup“ hinzu. Der „Wert“ sollte eine durch Trennzeichen getrennte Liste mit Namen der zu entfernenden Knoten sein.

    ```
         "fabricSettings": [
            {
            "name": "Setup",
            "parameters": [
                {
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
                },
                {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
                },
                {
                "name": "NodesToBeRemoved",
                "value": "vm0, vm1"
                }
            ]
            }
        ]
    ```
2. Führen Sie [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) aus, um mit dem Upgrade zu beginnen.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    Sie können den Fortschritt des Upgrades in Service Fabric Explorer überwachen. Alternativ können Sie [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps) ausführen.

> [!NOTE]
> Durch das Entfernen von Knoten werden möglicherweise mehrere Upgrades initiiert. Einige Knoten sind mit dem Tag `IsSeedNode=”true”` gekennzeichnet und können durch Abfragen des Clustermanifests über `Get-ServiceFabricClusterManifest` ermittelt werden. Das Entfernen solcher Knoten dauert möglicherweise länger als bei anderen Knoten, da die Seed-Knoten in einem solchen Szenario verschoben werden müssen. Der Cluster muss mindestens 3 primäre Knotentypen verwalten.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Entfernen von Knotentypen aus dem Cluster
Überprüfen Sie vor dem Entfernen eines Knotentyps gründlich, ob andere Knoten auf den Knotentyp verweisen. Entfernen Sie diese Knoten, bevor Sie den entsprechenden Knotentyp entfernen. Nachdem alle zugehörigen Knoten entfernt wurden, können Sie den NodeType aus der Clusterkonfiguration entfernen und über [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade?view=azureservicefabricps) ein Konfigurationsupgrade starten.


### <a name="replace-primary-nodes-of-your-cluster"></a>Ersetzen primärer Knoten im Cluster
Das Ersetzen primärer Knoten muss für jeden Knoten einzeln ausgeführt werden. Das Entfernen und Hinzufügen in Batches ist nicht möglich.


## <a name="next-steps"></a>Nächste Schritte
* [Konfigurationseinstellungen für eigenständige Windows-Cluster](service-fabric-cluster-manifest.md)
* [Schützen des eigenständigen Windows-Clusters mit Zertifikaten](service-fabric-windows-cluster-x509-security.md)
* [Erstellen eines eigenständigen Service Fabric-Clusters mit drei Knoten und Azure-VMs mit Windows Server](service-fabric-cluster-creation-with-windows-azure-vms.md)

