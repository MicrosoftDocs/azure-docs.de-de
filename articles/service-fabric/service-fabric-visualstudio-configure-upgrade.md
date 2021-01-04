---
title: Konfigurieren von Upgrades für eine Service Fabric-Anwendung
description: Erfahren Sie, wie Sie die Einstellungen für das Upgrade einer Service Fabric-Anwendung mithilfe von Microsoft Visual Studio konfigurieren.
ms.topic: conceptual
ms.date: 06/29/2017
ms.openlocfilehash: 11df0387db07669ebacdba2e3d850e32903da133
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96573679"
---
# <a name="configure-the-upgrade-of-a-service-fabric-application-in-visual-studio"></a>Konfigurieren von Upgrades für eine Service Fabric-Anwendung in Visual Studio
Die Service Fabric-Tools für Azure Visual Studio bieten Upgradeunterstützung für die Veröffentlichung auf lokalen Clustern oder Remoteclustern. Es gibt drei Szenarios, bei denen Sie die Anwendung auf eine neuere Version aktualisieren sollten, anstatt die Anwendung beim Testen und Debuggen zu ersetzen:

* Anwendungsdaten gehen während des Upgrades nicht verloren.
* Die Verfügbarkeit bleibt hoch, sodass es während des Upgrades nicht zu Dienstunterbrechungen kommt, sofern genügend Dienstinstanzen auf die Upgradedomänen verteilt sind.
* Für eine Anwendung können während des Upgrades Tests ausgeführt werden.

## <a name="parameters-needed-to-upgrade"></a>Erforderliche Upgradeparameter
Sie können zwischen zwei Arten der Bereitstellung wählen: normal oder Upgrade. Eine normale Bereitstellung löscht alle vorherigen Informationen zur Bereitstellung und die Daten auf dem Cluster, während eine Upgradebereitstellung diese Daten beibehält. Beim Upgrade einer Service Fabric-Anwendung in Visual Studio müssen Sie Parameter für das Anwendungsupgrade und Richtlinien zur Integritätsprüfung angeben. Mit Parametern für das Anwendungsupgrade wird das Upgrade gesteuert, und die Richtlinien zur Integritätsprüfung bestimmen, ob die Aktualisierung erfolgreich war. Weitere Informationen finden Sie unter [Service Fabric-Anwendungsupgrade: Upgradeparameter](service-fabric-application-upgrade-parameters.md) .

Es gibt drei Upgrademodi: *Monitored*, *UnmonitoredAuto* und *UnmonitoredManual*.

* Bei einem Upgrade im Modus „Monitored“ werden das Upgrade und die Anwendungsintegritätsprüfung automatisiert.
* Bei einem Upgrade im Modus „UnmonitoredAuto“ wird das Upgrade automatisiert, aber die Anwendungsintegritätsprüfung wird übersprungen.
* Bei einem Upgrade im Modus „UnmonitoredManual“ müssen Sie jede Upgradedomäne manuell aktualisieren.

Jeder Upgrademodus erfordert verschiedene Parametersätze. Weitere Informationen zu den verfügbaren Upgradeoptionen finden Sie unter [Anwendungsupgradeparameter](service-fabric-application-upgrade-parameters.md) .

## <a name="upgrade-a-service-fabric-application-in-visual-studio"></a>Upgrade einer Service Fabric-Anwendung in Visual Studio
Wenn Sie die Service Fabric-Tools von Visual Studio zum Aktualisieren einer Service Fabric-Anwendung verwenden, können Sie einen Veröffentlichungsvorgang als Upgrade anstatt als normale Bereitstellung festlegen, indem Sie das Kontrollkästchen **Anwendung aktualisieren** aktivieren.

### <a name="to-configure-the-upgrade-parameters"></a>So konfigurieren Sie die Upgradeparameter
1. Klicken Sie auf die Schaltfläche **Einstellungen** neben dem Kontrollkästchen. Das Dialogfeld **Upgradeparameter bearbeiten** wird angezeigt. Das Dialogfeld **Upgradeparameter bearbeiten** unterstützt die Upgrademodi Monitored, UnmonitoredAuto und UnmonitoredManual.
2. Wählen Sie den gewünschten Upgrademodus aus, und füllen Sie dann das Parameterraster aus.

    Jeder Parameter verfügt über Standardwerte. Der optionale Parameter *DefaultServiceTypeHealthPolicy* akzeptiert Hashtabelleneingaben. Im Folgenden finden Sie ein Beispiel für das Hashtabellen-Eingabeformat für *DefaultServiceTypeHealthPolicy*:

    ```
    @{ ConsiderWarningAsError = "false"; MaxPercentUnhealthyDeployedApplications = 0; MaxPercentUnhealthyServices = 0; MaxPercentUnhealthyPartitionsPerService = 0; MaxPercentUnhealthyReplicasPerPartition = 0 }
    ```

    *ServiceTypeHealthPolicyMap* ist ein weiterer optionaler Parameter, der Hashtabelleneingaben im folgenden Format verwendet:

    ```    
    @ {"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"}
    ```

    Im Folgenden finden Sie ein realistisches Beispiel:

    ```
    @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" }
    ```
3. Wenn Sie den Upgrademodus UnmonitoredManual verwenden, müssen Sie manuell eine PowerShell-Konsole öffnen, um den Upgradevorgang fortzusetzen und zu beenden. Weitere Informationen zur Funktionsweise manueller Upgrades finden Sie unter [Service Fabric-Anwendungsupgrade: Weiterführende Themen](service-fabric-application-upgrade-advanced.md) .

## <a name="upgrade-an-application-by-using-powershell"></a>Upgrade einer Anwendung mithilfe von PowerShell
Sie können PowerShell-Cmdlets zum Upgrade einer Service Fabric-Anwendung verwenden. Ausführliche Informationen dazu finden Sie unter [Tutorial für Service Fabric-Anwendungsupgrades](service-fabric-application-upgrade-tutorial.md) und [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade).

## <a name="specify-a-health-check-policy-in-the-application-manifest-file"></a>Festlegen einer Integritätsprüfungsrichtlinie in der Anwendungsmanifestdatei
Jeder Dienst in einer Service Fabric-Anwendung kann über eigene Richtlinienparameter für die Integritätsprüfung verfügen, die die Standardwerte überschreiben. Sie können diese Parameterwerte in der Anwendungsmanifestdatei angeben.

Das folgende Beispiel zeigt, wie eine eindeutige Integritätsprüfungsrichtlinie für jeden Dienst im Anwendungsmanifest angewendet wird.

```xml
<Policies>
    <HealthPolicy ConsiderWarningAsError="false" MaxPercentUnhealthyDeployedApplications="20">
        <DefaultServiceTypeHealthPolicy MaxPercentUnhealthyServices="20"               
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />
        <ServiceTypeHealthPolicy ServiceTypeName="ServiceTypeName1"
                MaxPercentUnhealthyServices="20"
                MaxPercentUnhealthyPartitionsPerService="20"
                MaxPercentUnhealthyReplicasPerPartition="20" />      
    </HealthPolicy>
</Policies>
```
## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Upgraden einer Anwendung finden Sie im [Tutorial für das Upgraden von Service Fabric-Anwendungen mithilfe von Visual Studio](service-fabric-application-upgrade-tutorial.md).
