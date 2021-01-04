---
title: Problembehandlung bei Anwendungsupgrades
description: In diesem Artikel werden einige bekannte Probleme beim Upgrade einer Service Fabric-Anwendung und ihre Behebung behandelt.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d462f2c2482e0fbb4d252967754a9675ed362674
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009346"
---
# <a name="troubleshoot-application-upgrades"></a>Problembehandlung bei Anwendungsupgrades

In diesem Artikel werden einige bekannte Probleme beim Upgrade einer Azure Service Fabric-Anwendung und ihre Behebung behandelt.

## <a name="troubleshoot-a-failed-application-upgrade"></a>Problembehandlung bei einem fehlgeschlagenen Anwendungsupgrade

Wenn ein Upgrade fehlschlägt, enthält die Ausgabe des Befehls **Get-ServiceFabricApplicationUpgrade** zusätzliche Informationen zum Debuggen des Fehlers.  Die folgende Liste gibt an, wie die zusätzlichen Informationen verwendet werden können:

1. Identifizieren des Fehlertyps
2. Identifizieren der Fehlerursache
3. Isolieren fehlerhafter Komponenten zur weiteren Untersuchung

Diese Informationen sind verfügbar, sobald Service Fabric einen Fehler erkennt, unabhängig davon, ob die **FailureAction** darin besteht, das Upgrade per Rollback rückgängig zu machen oder anzuhalten.

### <a name="identify-the-failure-type"></a>Identifizieren des Fehlertyps

In der Ausgabe von **Get-ServiceFabricApplicationUpgrade** gibt **FailureTimestampUtc** den Zeitstempel (in UTC) an, bei dem ein Upgradefehler von Service Fabric erkannt und die **FailureAction** ausgelöst wurde. **FailureReason** gibt eine der drei potenziellen allgemeinen Ursachen des Fehlers an:

1. **UpgradeDomainTimeout** : Gibt an, dass der Abschluss einer bestimmten Upgradedomäne zu lange dauerte und dass "UpgradeDomainTimeout" abgelaufen ist.
2. **OverallUpgradeTimeout** : Gibt an, dass das gesamte Upgrade zu lange dauerte und dass "UpgradeTimeout" abgelaufen ist.
3. HealthCheck: Gibt an, dass die Anwendung nach dem Upgrade einer Updatedomäne entsprechend den angegebenen Integritätsrichtlinien fehlerhaft ist und dass **HealthCheckRetryTimeout** abgelaufen ist.

Diese Einträge werden in der Ausgabe nur angezeigt, wenn das Upgrade fehlschlägt und ein Rollback ausgeführt wird. Weitere Informationen werden je nach Art des Fehlers angezeigt.

### <a name="investigate-upgrade-timeouts"></a>Untersuchen von Timeouts bei Upgrades

Timeoutfehler bei Upgrades sind am häufigsten auf Probleme mit der Dienstverfügbarkeit zurückzuführen. Die Ausgabe im Anschluss an diesen Absatz ist typisch für Upgrades, bei denen beim Starten von Dienstreplikaten oder -instanzen in der neuen Codeversion Fehler auftreten. Im Feld **UpgradeDomainProgressAtFailure** wird eine Momentaufnahme der ausstehenden Upgradevorgänge zum Zeitpunkt des Fehlers erfasst.

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                : fabric:/DemoApp
ApplicationTypeName            : DemoAppType
TargetApplicationTypeVersion   : v2
ApplicationParameters          : {}
StartTimestampUtc              : 4/14/2015 9:26:38 PM
FailureTimestampUtc            : 4/14/2015 9:27:05 PM
FailureReason                  : UpgradeDomainTimeout
UpgradeDomainProgressAtFailure : MYUD1

                                 NodeName            : Node4
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 744c8d9f-1d26-417e-a60e-cd48f5c098f0

                                 NodeName            : Node1
                                 UpgradePhase        : PostUpgradeSafetyCheck
                                 PendingSafetyChecks :
                                     WaitForPrimaryPlacement - PartitionId: 4b43f4d8-b26b-424e-9307-7a7a62e79750
UpgradeState                   : RollingBackCompleted
UpgradeDuration                : 00:00:46
CurrentUpgradeDomainDuration   : 00:00:00
NextUpgradeDomain              :
UpgradeDomainsStatus           : { "MYUD1" = "Completed";
                                 "MYUD2" = "Completed";
                                 "MYUD3" = "Completed" }
UpgradeKind                    : Rolling
RollingUpgradeMode             : UnmonitoredAuto
ForceRestart                   : False
UpgradeReplicaSetCheckTimeout  : 00:00:00
```

Bei diesem Beispiel ist das Upgrade bei der Upgradedomäne *MYUD1* fehlgeschlagen, und die beiden Partitionen (*744c8d9f-1d26-417e-a60e-cd48f5c098f0* und *4b43f4d8-b26b-424e-9307-7a7a62e79750*) sind hängen geblieben. Die Partitionen sind hängen geblieben, da die Laufzeit die primären Replikate (*WaitForPrimaryPlacement*) nicht auf den Zielknoten *Node1* und *Node4* platzieren konnte.

Mit dem Befehl **Get-ServiceFabricNode** kann überprüft werden, ob sich diese beiden Knoten in der Upgradedomäne *MYUD1* befinden. Die *UpgradePhase* gibt *PostUpgradeSafetyCheck* aus, was bedeutet, dass diese Sicherheitsprüfungen nach dem Aktualisieren aller Knoten in der Upgradedomäne stattfinden. Alle diese Informationen lassen auf ein mögliches Problem mit der neuen Version des Anwendungscodes schließen. Die häufigsten Probleme sind Dienstfehler im Vordergrund oder bei der Heraufstufung auf primäre Codepfade.

*PreUpgradeSafetyCheck* als *UpgradePhase* bedeutet, dass vor der Durchführung des Upgrades Probleme bei der Vorbereitung der Upgradedomäne aufgetreten sind. In diesem Fall sind die häufigsten Probleme Fehler im Hintergrund oder bei der Herabstufung von primären Codepfaden.

Der aktuelle **UpgradeState** ist *RollingBackCompleted*, d.h., das ursprüngliche Upgrade muss mit einer Rollback-**FailureAction** durchgeführt worden sein, sodass nach dem Fehler automatisch ein Rollback des Upgrades ausgeführt wurde. Wenn das ursprüngliche Upgrade mit einer manuellen **FailureAction** durchgeführt worden wäre, befände sich das Upgrade stattdessen in einem angehaltenen Zustand, um das Livedebuggen der Anwendung zuzulassen.

In seltenen Fällen kann das Feld **UpgradeDomainProgressAtFailure** leer sein, wenn das gesamte Upgrade gerade in dem Moment ein Zeitlimit erreicht, in dem das System alle Aufgaben für die aktuelle Upgradedomäne erledigt hat. Versuchen Sie in diesem Fall, die Werte der Upgradeparameter **UpgradeTimeout** und **UpgradeDomainTimeout** zu erhöhen und dann das Upgrade zu wiederholen.

### <a name="investigate-health-check-failures"></a>Untersuchen von Fehlern bei der Integritätsprüfung

Fehler bei der Integritätsprüfung können durch eine Vielzahl von Problemen ausgelöst werden, die auftreten können, nachdem alle Knoten in einer Upgradedomäne aktualisiert und alle Sicherheitsprüfungen erfolgreich durchgeführt wurden. Die Ausgabe im Anschluss an diesen Absatz ist typisch für einen Upgradefehler aufgrund fehlerhafter Integritätsprüfungen. Im Feld **UnhealthyEvaluations** wird entsprechend der angegebenen [Integritätsrichtlinie](service-fabric-health-introduction.md)eine Momentaufnahme aller fehlgeschlagenen Integritätsprüfungen zum Zeitpunkt des Upgradefehlers erfasst.

```powershell
Get-ServiceFabricApplicationUpgrade fabric:/DemoApp
```

```Output
ApplicationName                         : fabric:/DemoApp
ApplicationTypeName                     : DemoAppType
TargetApplicationTypeVersion            : v4
ApplicationParameters                   : {}
StartTimestampUtc                       : 4/24/2015 2:42:31 AM
UpgradeState                            : RollingForwardPending
UpgradeDuration                         : 00:00:27
CurrentUpgradeDomainDuration            : 00:00:27
NextUpgradeDomain                       : MYUD2
UpgradeDomainsStatus                    : { "MYUD1" = "Completed";
                                          "MYUD2" = "Pending";
                                          "MYUD3" = "Pending" }
UnhealthyEvaluations                    :
                                          Unhealthy services: 50% (2/4), ServiceType='PersistedServiceType', MaxPercentUnhealthyServices=0%.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc3', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='3a9911f6-a2e5-452d-89a8-09271e7e49a8', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

                                          Unhealthy service: ServiceName='fabric:/DemoApp/Svc2', AggregatedHealthState='Error'.

                                              Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                              Unhealthy partition: PartitionId='744c8d9f-1d26-417e-a60e-cd48f5c098f0', AggregatedHealthState='Error'.

                                                  Error event: SourceId='Replica', Property='InjectedFault'.

UpgradeKind                             : Rolling
RollingUpgradeMode                      : Monitored
FailureAction                           : Manual
ForceRestart                            : False
UpgradeReplicaSetCheckTimeout           : 49710.06:28:15
HealthCheckWaitDuration                 : 00:00:00
HealthCheckStableDuration               : 00:00:10
HealthCheckRetryTimeout                 : 00:00:10
UpgradeDomainTimeout                    : 10675199.02:48:05.4775807
UpgradeTimeout                          : 10675199.02:48:05.4775807
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

Die Untersuchung von Fehlern bei der Integritätsprüfung erfordert zunächst ein umfassendes Verständnis des Service Fabric-Integritätsmodells. Aber auch ohne diese detaillierten Kenntnisse können wir sehen, dass zwei Dienste fehlerhaft sind: *fabric:/DemoApp/Svc3* und *fabric:/DemoApp/Svc2*. Wir sehen auch den Fehlerintegritätsbericht (in diesem Fall „InjectedFault“). In diesem Beispiel sind zwei von vier Diensten fehlerhaft. Dies liegt unter dem Standardziel von 0% Fehlern (*MaxPercentUnhealthyServices*).

Das Upgrade wurde nach dem Fehlschlagen angehalten, indem die **FailureAction** „Manual“ beim Starten des Upgrades angegeben wurde. Dieser Modus ermöglicht uns, das Livesystem im fehlerhaften Zustand zu untersuchen, bevor eine weitere Aktion erfolgt.

### <a name="recover-from-a-suspended-upgrade"></a>Wiederherstellen eines angehaltenen Upgrades

Wenn als **FailureAction**„Rollback“ angegeben ist, ist keine Wiederherstellung erforderlich, da nach dem Fehler automatisch ein Rollback für das Upgrade ausgeführt wird. Wenn als **FailureAction**"Manual" angegeben ist, gibt es mehrere Möglichkeiten zur Wiederherstellung:

1.  Auslösen eines Rollbacks
2. Manuelles Durchlaufen des restlichen Upgrades
3. Fortsetzen des überwachten Upgrades

Der Befehl **Start ServiceFabricApplicationRollback** kann jederzeit verwendet werden, um einen Rollback für die Anwendung auszuführen. Wenn der Befehl erfolgreich zurückgegeben wird, wurde die Rollbackanforderung im System registriert und wird bald darauf gestartet.

Mit dem Befehl **Resume-ServiceFabricApplicationUpgrade** kann das restliche Upgrade manuell fortgesetzt werden. Dabei werden die einzelnen Upgradedomänen nacheinander durchlaufen. In diesem Modus werden vom System nur Sicherheitsprüfungen durchgeführt. Weitere Integritätsprüfungen erfolgen nicht. Dieser Befehl kann nur verwendet werden, wenn *UpgradeState* den Wert *RollingForwardPending* aufweist, was bedeutet, dass das Upgrade der aktuellen Upgradedomäne abgeschlossen ist, die nächste Upgradedomäne jedoch noch nicht gestartet wurde (ausstehend ist).

Der Befehl **Update-ServiceFabricApplicationUpgrade** kann zum Fortsetzen des überwachten Upgrades mit Sicherheits- und Integritätsprüfungen verwendet werden.

```powershell
Update-ServiceFabricApplicationUpgrade fabric:/DemoApp -UpgradeMode Monitored
```

```Output
UpgradeMode                             : Monitored
ForceRestart                            :
UpgradeReplicaSetCheckTimeout           :
FailureAction                           :
HealthCheckWaitDuration                 :
HealthCheckStableDuration               :
HealthCheckRetryTimeout                 :
UpgradeTimeout                          :
UpgradeDomainTimeout                    :
ConsiderWarningAsError                  :
MaxPercentUnhealthyPartitionsPerService :
MaxPercentUnhealthyReplicasPerPartition :
MaxPercentUnhealthyServices             :
MaxPercentUnhealthyDeployedApplications :
ServiceTypeHealthPolicyMap              :
```

Das Upgrade wird ab der Upgradedomäne fortgesetzt, in der es zuletzt angehalten wurde. Dabei werden die gleichen Upgradeparameter und Integritätsrichtlinien wie zuvor verwendet. Bei Bedarf können beim Fortsetzen des Upgrades alle in der vorstehenden Ausgabe angegebenen Upgradeparameter und Integritätsrichtlinien im gleichen Befehl geändert werden. In diesem Beispiel wurde das Upgrade im überwachten Modus mit unveränderten Parametern und Integritätsrichtlinien fortgesetzt.

## <a name="further-troubleshooting"></a>Weitere Problembehandlungen

### <a name="service-fabric-is-not-following-the-specified-health-policies"></a>Service Fabric befolgt nicht die angegebenen Integritätsrichtlinien

Mögliche Ursache 1:

Service Fabric übersetzt für die Integritätsevaluierung alle Prozentsätze in die tatsächliche Anzahl der Entitäten (z. B. Replikate, Partitionen und Dienste) und rundet immer auf ganze Zahlen auf. Wenn für *MaxPercentUnhealthyReplicasPerPartition* beispielsweise maximal 21 % angegeben wurde und fünf Replikate vorhanden sind, lässt Service Fabric bis zu zwei fehlerhafte Replikate zu (d.h. `Math.Ceiling (5*0.21)`). Integritätsrichtlinien müssen also entsprechend festgelegt werden.

Mögliche Ursache 2:

Integritätsrichtlinien werden als Prozentsätze aller Dienste und nicht für bestimmte Dienstinstanzen angegeben. Angenommen, vor einem Upgrade umfasst eine Anwendung beispielsweise die vier Dienstinstanzen A, B, C und D, wobei Dienst D fehlerhaft ist, jedoch mit nur geringen Auswirkungen auf die Anwendung. Der bekanntermaßen fehlerhafte Dienst D soll beim Upgrade ignoriert werden. Also legen wir den Parameter *MaxPercentUnhealthyServices* auf 25 % fest und setzen voraus, dass nur A, B und C fehlerfrei sein müssen.

Allerdings kann während des Upgrades D fehlerfrei und C fehlerhaft werden. Das Upgrade wäre immer noch erfolgreich, da nur 25 % der Dienste fehlerhaft sind. Dies kann jedoch zu unvorhergesehenen Fehlern führen, wenn unerwarteterweise C anstatt D fehlerhaft ist. In diesem Fall sollte D als anderer Diensttyp als A, B und C modelliert werden. Da Integritätsrichtlinien pro Diensttyp angegeben werden, können unterschiedliche Fehlerschwellenwerte in Prozent auf verschiedene Dienste angewendet werden. 

### <a name="i-did-not-specify-a-health-policy-for-application-upgrade-but-the-upgrade-still-fails-for-some-time-outs-that-i-never-specified"></a>Ich habe keine Integritätsrichtlinie für das Anwendungsupgrade angegeben, beim Upgrade treten jedoch Fehler aufgrund einiger Timeouts auf, die ich nie angegeben habe

Wenn der Upgradeanforderung keine Integritätsrichtlinien bereitgestellt werden, werden diese aus der Datei *ApplicationManifest.xml* der aktuellen Anwendungsversion übernommen. Wenn Sie beispielsweise Anwendung X von Version 1.0 auf Version 2.0 aktualisieren, werden die für Anwendung X in Version 1.0 angegebenen Richtlinien zur Anwendungsintegrität verwendet. Wenn für das Upgrade eine andere Integritätsrichtlinie verwendet werden soll, muss die Richtlinie als Teil des API-Aufrufs für das Anwendungsupgrade angegeben werden. Die Richtlinien, die als Teil des API-Aufrufs angegeben werden, gelten nur für die Dauer des Upgrades. Nach Abschluss des Upgrades werden wieder die in der Datei *ApplicationManifest.xml* angegebenen Richtlinien verwendet.

### <a name="incorrect-time-outs-are-specified"></a>Es werden falsche Timeouts angegeben

Sie haben sich vielleicht gefragt, was passiert, wenn Timeouts uneinheitlich festgelegt sind. Angenommen, Sie haben ein *UpgradeTimeout*, das kleiner als das *UpgradeDomainTimeout* ist. Die Antwort ist einfach: Es wird ein Fehler zurückgegeben. In den folgenden Fällen werden Fehler zurückgegeben: *UpgradeDomainTimeout* ist kleiner als die Summe aus *HealthCheckWaitDuration* und *HealthCheckRetryTimeout*, und *UpgradeDomainTimeout* ist kleiner als die Summe aus *HealthCheckWaitDuration* und *HealthCheckStableDuration*.

### <a name="my-upgrades-are-taking-too-long"></a>Die Upgrades dauern zu lange

Die Dauer eines Upgrades hängt von den angegebenen Integritätsprüfungen und Timeouts ab. Integritätsprüfungen und Timeouts hängen davon ab, wie lange es dauert, die Anwendung zu kopieren, bereitzustellen und zu stabilisieren. Wenn Timeouts zu kurz angesetzt werden, führt dies möglicherweise zu einer größeren Zahl fehlerhafter Upgrades. Daher empfiehlt sich eine konservative Vorgehensweise mit längeren Timeouts.

Eine kurze Erinnerung dazu, wie Timeouts und Upgradezeiten interagieren:

Upgrades für eine Upgradedomäne können nicht schneller abgeschlossen werden als *HealthCheckWaitDuration* + *HealthCheckStableDuration*.

Upgradefehler können nicht schneller auftreten als *HealthCheckWaitDuration* + *HealthCheckRetryTimeout*.

Die Upgradezeit für eine Upgradedomäne wird durch *UpgradeDomainTimeout* begrenzt.  Wenn *HealthCheckRetryTimeout* und *HealthCheckStableDuration* ungleich null sind und die Integrität der Anwendung wechselt, wird für das Upgrade schließlich bei *UpgradeDomainTimeout* das Timeout überschritten. *UpgradeDomainTimeout* zählt rückwärts ab dem Moment, an dem das Upgrade für die aktuelle Upgradedomäne gestartet wird.

## <a name="next-steps"></a>Nächste Schritte

[Ihre Anwendung mit Visual Studio upgraden](service-fabric-application-upgrade-tutorial.md) beschreibt das Upgraden von Anwendungen mit Visual Studio.

[Ihre Anwendung mit PowerShell upgraden](service-fabric-application-upgrade-tutorial-powershell.md) beschreibt das Upgraden von Anwendungen mit PowerShell.

Steuern Sie die Upgrades von Anwendungen mithilfe von [Upgradeparametern](service-fabric-application-upgrade-parameters.md).

Machen Sie Ihre Anwendungsupgrades kompatibel, indem Sie sich mit der [Datenserialisierung](service-fabric-application-upgrade-data-serialization.md)vertraut machen.

Erfahren Sie, wie Sie erweiterte Funktionen beim Upgrade Ihrer Anwendung nutzen, indem Sie sich mit den [weiterführenden Themen](service-fabric-application-upgrade-advanced.md)beschäftigen.