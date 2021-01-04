---
title: Unterschiede bei Azure Service Fabric zwischen Linux und Windows
description: Es wird beschrieben, welche Unterschiede zwischen Azure Service Fabric unter Linux und Azure Service Fabric unter Windows bestehen.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 4adae60ded804450c25809faa0edca6bb058adf7
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96574495"
---
# <a name="differences-between-service-fabric-on-linux-and-windows"></a>Unterschiede zwischen Service Fabric unter Linux und Windows

Einige Features werden unter Windows unterstützt, aber noch nicht unter Linux. Die Übereinstimmung der Featuresätze wird zu einem späteren Zeitpunkt erreicht sein, und mit jeder Version wird die Lücke kleiner. Zwischen den neuesten verfügbaren Versionen gibt es folgende Unterschiede:

* Envoy (Reverseproxy) befindet sich unter Linux in der Vorschauphase
* Eigenständiges Installationsprogramm für Linux ist unter Linux noch nicht verfügbar
* Konsolenumleitung (für Linux- oder Windows-Produktionscluster nicht unterstützt)
* Fault Analysis Service (FAS) unter Linux
* DNS-Dienst für Service Fabric-Dienste (DNS-Dienst wird für Container unter Linux unterstützt)
* CLI-Befehlsentsprechungen bestimmter PowerShell-Befehle (siehe Liste unten, wobei die meisten Einträge nur für eigenständige Cluster gelten)
* [Unterschiede bei der Protokollimplementierung, die die Skalierbarkeit beeinträchtigen können](service-fabric-concepts-scalability.md#choosing-a-platform)

## <a name="powershell-cmdlets-that-do-not-work-against-a-linux-service-fabric-cluster"></a>PowerShell-Cmdlets, die für einen Linux-Service Fabric-Cluster nicht funktionieren

* Invoke-ServiceFabricChaosTestScenario
* Invoke-ServiceFabricFailoverTestScenario
* Invoke-ServiceFabricPartitionDataLoss
* Invoke-ServiceFabricPartitionQuorumLoss
* Restart-ServiceFabricPartition
* Start-ServiceFabricNode
* Stop-ServiceFabricNode
* Get-ServiceFabricImageStoreContent
* Get-ServiceFabricChaosReport
* Get-ServiceFabricNodeTransitionProgress
* Get-ServiceFabricPartitionDataLossProgress
* Get-ServiceFabricPartitionQuorumLossProgress
* Get-ServiceFabricPartitionRestartProgress
* Get-ServiceFabricTestCommandStatusList
* Remove-ServiceFabricTestState
* Start-ServiceFabricChaos
* Start-ServiceFabricNodeTransition
* Start-ServiceFabricPartitionDataLoss
* Start-ServiceFabricPartitionQuorumLoss
* Start-ServiceFabricPartitionRestart
* Stop-ServiceFabricChaos
* Stop-ServiceFabricTestCommand
* Get-ServiceFabricNodeConfiguration
* Get-ServiceFabricClusterConfiguration
* Get-ServiceFabricClusterConfigurationUpgradeStatus
* Get-ServiceFabricPackageDebugParameters
* New-ServiceFabricPackageDebugParameter
* New-ServiceFabricPackageSharingPolicy
* Add-ServiceFabricNode
* Copy-ServiceFabricClusterPackage
* Get-ServiceFabricRuntimeSupportedVersion
* Get-ServiceFabricRuntimeUpgradeVersion
* New-ServiceFabricCluster
* New-ServiceFabricNodeConfiguration
* Remove-ServiceFabricCluster
* Remove-ServiceFabricClusterPackage
* Remove-ServiceFabricNodeConfiguration
* Test-ServiceFabricClusterManifest
* Test-ServiceFabricConfiguration
* Update-ServiceFabricNodeConfiguration
* Approve-ServiceFabricRepairTask
* Complete-ServiceFabricRepairTask
* Get-ServiceFabricRepairTask
* Invoke-ServiceFabricDecryptText
* Invoke-ServiceFabricEncryptSecret
* Invoke-ServiceFabricEncryptText
* Invoke-ServiceFabricInfrastructureCommand
* Invoke-ServiceFabricInfrastructureQuery
* Remove-ServiceFabricRepairTask
* Start-ServiceFabricRepairTask
* Stop-ServiceFabricRepairTask
* Update-ServiceFabricRepairTaskHealthPolicy

## <a name="next-steps"></a>Nächste Schritte

* [Vorbereiten Ihrer Entwicklungsumgebung unter Linux](service-fabric-get-started-linux.md)
* [Prepare your development environment on OSX (Vorbereiten Ihrer Entwicklungsumgebung unter OSX)](service-fabric-get-started-mac.md)
* [Erstellen und Bereitstellen Ihrer ersten Service Fabric-Java-Anwendung unter Linux mithilfe von Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Erstellen und Bereitstellen Ihrer ersten Service Fabric-Java-Anwendung unter Linux mithilfe des Service Fabric-Plug-Ins für Eclipse](service-fabric-get-started-eclipse.md)
* [Erstellen der ersten Java-Anwendung unter Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Verwalten von Anwendungen mit der Service Fabric-Befehlszeilenschnittstelle](service-fabric-application-lifecycle-sfctl.md)
