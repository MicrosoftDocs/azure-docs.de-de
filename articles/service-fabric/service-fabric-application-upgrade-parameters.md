---
title: 'Anwendungsupgrade: Upgradeparameter'
description: Beschreibt Parameter im Zusammenhang mit dem Upgrade einer Service Fabric-Anwendung, einschließlich durchzuführender Integritätsprüfungen und Richtlinien für das automatische Rückgängigmachen des Upgrades.
ms.topic: conceptual
ms.date: 11/08/2018
ms.openlocfilehash: 6b6116bf1188fcf191b2d672e6c698bb3c050e6c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018476"
---
# <a name="application-upgrade-parameters"></a>Parameter für Anwendungsupgrades
In diesem Artikel werden die verschiedenen Parameter beschrieben, die während des Upgrades einer Azure Service Fabric-Anwendung gelten. Parameter für Anwendungsupgrades steuern bei Upgrades angewendete Timeouts und Integritätsprüfungen, und geben die Richtlinien an, die bei einem Upgradefehler angewendet werden. Anwendungsparameter gelten für Upgrades mit:
- PowerShell
- Visual Studio
- SFCTL
- [REST](/rest/api/servicefabric/sfclient-api-startapplicationupgrade)

Anwendungsupgrades werden über einen von drei Upgrademodi initiiert, die Benutzer auswählen können. Jeder Modus verfügt über einen eigenen Satz von Anwendungsparametern:
- Monitored
- UnmonitoredAuto
- UnmonitoredManual

Die entsprechenden erforderlichen und optionalen Parameter sind unten in den einzelnen Abschnitten beschrieben.

## <a name="visual-studio-and-powershell-parameters"></a>Visual Studio- und PowerShell-Parameter

Service Fabric-Anwendungsupgrades mit PowerShell verwenden den Befehl [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade). Der Upgrademodus wird ausgewählt, indem entweder der Parameter **Monitored**, **UnmonitoredAuto** oder **UnmonitoredManual** an [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) übergeben wird.

Die Parameter für ein Service Fabric-Anwendungsupgrade in Visual Studio werden über das Visual Studio-Dialogfeld „Upgradeeinstellungen“ festgelegt. Der Visual Studio-Upgrademodus wird über das Dropdownfeld **Upgrademodus** ausgewählt. Es gibt die Optionen **Monitored**, **UnmonitoredAuto** und **UnmonitoredManual**. Weitere Informationen finden Sie unter [Konfigurieren von Upgrades für eine Service Fabric-Anwendung in Visual Studio](service-fabric-visualstudio-configure-upgrade.md).

### <a name="required-parameters"></a>Erforderliche Parameter
(PS = PowerShell, VS = Visual Studio)

| Parameter | Gilt für | BESCHREIBUNG |
| --- | --- | --- |
ApplicationName |PS| Der Name der Anwendung, die aktualisiert wird. Beispiele: fabric:/VisualObjects, fabric:/ClusterMonitor. |
ApplicationTypeVersion|PS|Die Version des Anwendungstyps, für den das Upgrade durchgeführt wird. |
FailureAction |PS, VS|Zulässige Werte sind **Rollback**, **Manual** und **Invalid**. Dies beschreibt die ersetzende Aktion, die ausgeführt werden soll, wenn ein Upgrade vom Typ *Monitored* Verstöße gegen die Überwachungs- oder Integritätsrichtlinie erkennt. <br>**Rollback** gibt an, dass das Upgrade automatisch auf die Version vor dem Upgrade zurückgesetzt wird. <br>**Manual** gibt an, dass das Upgrade in den Upgrademodus *UnmonitoredManual* wechselt. <br>**Invalid** gibt an, dass die Fehleraktion ungültig ist.|
Monitored |PS|Gibt an, dass das Upgrade überwacht wird. Nachdem das Cmdlet ein Upgrade für eine Upgradedomäne abgeschlossen hat, aktualisiert Service Fabric die nächste Upgradedomäne, wenn die Integrität der Upgradedomäne und des Clusters Ihren definierten Integritätsrichtlinien entspricht. Wenn die Upgradedomäne oder der Cluster die Integritätsrichtlinien nicht erfüllt, schlägt das Upgrade fehl, und Service Fabric setzt das Upgrade für die Upgradedomäne zurück oder stellt den manuellen Modus gemäß der angegebenen Richtlinie wieder her. Dies ist der empfohlene Modus für Anwendungsupgrades in einer Produktionsumgebung. |
UpgradeMode | VS | Zulässige Werte sind **Monitored** (Standard), **UnmonitoredAuto** und **UnmonitoredManual**. Weitere Informationen finden Sie in der Beschreibung der PowerShell-Parameter für den jeweiligen Modus. |
UnmonitoredAuto | PS | Gibt an, dass das Upgrade automatisch ausgeführt und nicht überwacht wird. Nachdem Service Fabric eine Upgradedomäne aktualisiert hat, aktualisiert Service Fabric die nächste Upgradedomäne unabhängig vom Integritätszustand der Anwendung. Dieser Modus wird für die Produktion nicht empfohlen und ist nur beim Entwickeln einer Anwendung nützlich. |
UnmonitoredManual | PS | Gibt an, dass das Upgrade manuell ausgeführt und nicht überwacht wird. Nachdem Service Fabric eine Upgradedomäne aktualisiert hat, wartet die Plattform darauf, dass Sie die nächste Upgradedomäne mit dem Cmdlet *Resume-ServiceFabricApplicationUpgrade* aktualisieren. |

### <a name="optional-parameters"></a>Optionale Parameter

Die Kriterien für die Integritätsevaluierung sind optional. Wenn beim Starten eines Upgrades keine Kriterien für die Integritätsevaluierung angegeben werden, verwendet Service Fabric die Anwendungsintegritätsrichtlinien, die in der Datei „ApplicationManifest.xml“ der Anwendungsinstanz angegeben sind.

> [!div class="mx-tdBreakAll"]
> | Parameter | Gilt für | BESCHREIBUNG |
> | --- | --- | --- |
> | ApplicationParameter |PS, VS| Gibt die Außerkraftsetzungen für Anwendungsparameter an.<br>PowerShell-Anwendungsparameter werden als Name/Wert-Paare in einer Hashtabelle angegeben. Beispiel: @{ „VotingData_MinReplicaSetSize“ = „3“; „VotingData_PartitionCount“ = „1“ }.<br>Visual Studio-Anwendungsparameter können im Dialogfeld „Service Fabric-Anwendung veröffentlichen“ im Feld **Anwendungsparameterdatei** angegeben werden.
> | Confirm |PS| Zulässige Werte sind **TRUE** und **FALSE**. Fordert vor der Ausführung des Cmdlets zur Bestätigung auf. |
> | ConsiderWarningAsError |PS, VS |Zulässige Werte sind **TRUE** und **FALSE**. Der Standardwert ist **False**. Bei der Evaluierung der Integrität der Anwendung während des Upgrades werden die Warnereignisse für die Anwendung als Fehler behandelt. In der Standardeinstellung bewertet Service Fabric Warnereignisse in Bezug auf die Integrität nicht als Fehler, sodass das Upgrade auch bei Warnereignissen fortgesetzt werden kann. |
> | DefaultServiceTypeHealthPolicy | PS, VS |Gibt die Integritätsrichtlinie für den Standarddiensttyp an, der für das überwachte Upgrade im Format MaxPercentUnhealthyPartitionsPerService, MaxPercentUnhealthyReplicasPerPartition, MaxPercentUnhealthyServices verwendet werden soll. Beispielsweise zeigen 5, 10 und 15 die folgenden Werte an: MaxPercentUnhealthyPartitionsPerService = 5, MaxPercentUnhealthyReplicasPerPartition = 10, MaxPercentUnhealthyServices = 15. |
> | Force | PS, VS | Zulässige Werte sind **TRUE** und **FALSE**. Gibt an, dass der Upgradevorgang die Warnmeldung überspringt und das Upgrade erzwingt, auch wenn sich die Versionsnummer nicht geändert hat. Dies eignet sich für lokale Tests, jedoch nicht für den Einsatz in Produktionsumgebungen, weil die vorhandene Bereitstellung entfernt werden muss, und das zu Downtime und ggf. zu Datenverlust führt. |
> | ForceRestart |PS, VS |Wenn Sie ein Konfigurations- oder Datenpaket aktualisieren, ohne den Dienstcode zu aktualisieren, wird der Dienst nur dann neu gestartet, wenn die „ForceRestart“-Eigenschaft auf **TRUE** festgelegt ist. Wenn das Upgrade abgeschlossen ist, benachrichtigt Service Fabric den Dienst, dass ein neues Konfigurations- oder Datenpaket verfügbar ist. Der Dienst muss dann die Änderungen anwenden. Bei Bedarf kann sich der Dienst neu starten. |
> | HealthCheckRetryTimeoutSec |PS, VS |Die Zeit (in Sekunden), in der Service Fabric die Integritätsevaluierung fortsetzt, bevor das Upgrade als fehlgeschlagen deklariert wird. Der Standardwert ist 600 Sekunden. Diese Zeitdauer beginnt, nachdem *HealthCheckWaitDurationSec* erreicht wurde. Innerhalb von *HealthCheckRetryTimeout* kann Service Fabric möglicherweise mehrere Integritätsprüfungen der Anwendungsintegrität ausführen. Der Standardwert ist 10 Minuten, und es wird empfohlen, diesen Wert Ihrer Anwendung entsprechend anzupassen. |
> | HealthCheckStableDurationSec |PS, VS |Die Dauer (in Sekunden), in der überprüft wird, ob die Anwendung stabil ist, bevor mit der nächsten Upgradedomäne fortgefahren oder das Upgrade abgeschlossen wird. Diese Wartezeit wird verwendet, um nicht erkannte Integritätsänderungen direkt nach dem Durchführen der Integritätsprüfung zu verhindern. Der Standardwert ist 120 Minuten, und es wird empfohlen, diesen Wert Ihrer Anwendung entsprechend anzupassen. |
> | HealthCheckWaitDurationSec |PS, VS | Die Wartezeit (in Sekunden) nach dem Abschluss des Upgrades in der Upgradedomäne, bevor Service Fabric die Integrität der Anwendung evaluiert. Diese Dauer kann auch als die Zeit betrachtet werden, die eine Anwendung ausgeführt werden soll, bevor sie als fehlerfrei gilt. Wenn die Integritätsprüfung erfolgreich ist, wird der Upgradevorgang bei der nächsten Upgradedomäne fortgesetzt.  Wenn die Integritätsprüfung fehlschlägt, wartet Service Fabric auf [UpgradeHealthCheckInterval](./service-fabric-cluster-fabric-settings.md#clustermanager), bevor die Integritätsprüfung wiederholt wird, bis *HealthCheckRetryTimeoutSec* erreicht ist. Der empfohlene Standardwert ist 0 Sekunden. |
> | MaxPercentUnhealthyDeployedApplications|PS, VS |Der empfohlene Standardwert ist 0. Geben Sie die maximale Anzahl der bereitgestellten Anwendungen an (siehe dazu den [Abschnitt zur Integrität](service-fabric-health-introduction.md)), die fehlerhaft sein können, bevor die Anwendung als fehlerhaft gilt und das Upgrade abgebrochen wird. Dieser Parameter definiert die Anwendungsintegrität für den Knoten und hilft bei der Ermittlung von Problemen während des Upgrades. Typischerweise erfolgt ein Lastenausgleich der Replikate der Anwendung auf dem anderen Knoten, sodass die Anwendung fehlerfrei zu sein scheint und das Upgrade so fortgesetzt werden kann. Durch Angeben eines strikten Werts für *MaxPercentUnhealthyDeployedApplications* für die Integrität kann Service Fabric ein Problem mit dem Anwendungspaket schnell erkennen und ein Fail-Fast-Upgrade erzeugen. |
> | MaxPercentUnhealthyServices |PS, VS |Ein Parameter für *DefaultServiceTypeHealthPolicy* und *ServiceTypeHealthPolicyMap*. Der empfohlene Standardwert ist 0. Geben Sie die maximale Anzahl der Dienste in der Anwendungsinstanz an, die fehlerhaft sein können, bevor die Anwendung als fehlerhaft gilt und das Upgrade abgebrochen wird. |
> | MaxPercentUnhealthyPartitionsPerService|PS, VS |Ein Parameter für *DefaultServiceTypeHealthPolicy* und *ServiceTypeHealthPolicyMap*. Der empfohlene Standardwert ist 0. Geben Sie die maximale Anzahl der Partitionen in einem Dienst an, die fehlerhaft sein können, bevor der Dienst als fehlerhaft gilt. |
> | MaxPercentUnhealthyReplicasPerPartition|PS, VS |Ein Parameter für *DefaultServiceTypeHealthPolicy* und *ServiceTypeHealthPolicyMap*. Der empfohlene Standardwert ist 0. Geben Sie die maximale Anzahl der Replikate in einer Partition an, die fehlerhaft sein können, bevor die Partition als fehlerhaft gilt. |
> | ServiceTypeHealthPolicyMap | PS, VS | Stellt die Integritätsrichtlinie dar, die zum Bewerten der Integrität von Diensten eines Diensttyps verwendet wird. Nimmt eine Hashtabelleneingabe im folgenden Format vor: @{"ServiceTypeName" : "MaxPercentUnhealthyPartitionsPerService,MaxPercentUnhealthyReplicasPerPartition,MaxPercentUnhealthyServices"} Beispiel: @{ "ServiceTypeName01" = "5,10,5"; "ServiceTypeName02" = "5,5,5" } |
> | TimeoutSec | PS, VS | Gibt die Timeoutperiode in Sekunden für den Vorgang an. |
> | UpgradeDomainTimeoutSec |PS, VS |Maximale Zeit (in Sekunden) für das Aktualisieren einer einzelnen Upgradedomäne. Wenn dieses Timeout erreicht wird, wird das Upgrade beendet und basierend auf der Einstellung für *FailureAction* fortgesetzt. Der Standardwert ist „never“ (unendlich), und es wird empfohlen, diesen Wert Ihrer Anwendung entsprechend anzupassen. |
> | UpgradeReplicaSetCheckTimeoutSec |PS, VS |Dieser Wert wird in Sekunden gemessen.<br>**Statusfreier Dienst**: Service Fabric versucht innerhalb einer Upgradedomäne sicherzustellen, dass zusätzliche Instanzen des Diensts verfügbar sind. Wenn die Anzahl der Zielinstanzen größer als 1 ist, wartet Service Fabric bis zu einem maximalen Timeoutwert darauf, dass mehrere Instanzen verfügbar sind. Dieses Timeout wird mit der *UpgradeReplicaSetCheckTimeoutSec*-Eigenschaft angegeben. Wenn das Timeout überschritten wird, fährt Service Fabric unabhängig von der Anzahl der Dienstinstanzen mit dem Upgrade fort. Wenn die Anzahl der Zielinstanzen gleich 1 ist, wartet Service Fabric nicht, sondern setzt das Upgrade direkt fort.<br><br>**Statusbehafteter Dienst**: Service Fabric versucht innerhalb einer Upgradedomäne sicherzustellen, dass die Replikatgruppe über ein Quorum verfügt. Service Fabric wartet bis zu einem maximalen Timeoutwert (angegeben durch die *UpgradeReplicaSetCheckTimeoutSec*-Eigenschaft) darauf, dass ein Quorum verfügbar ist. Wenn das Timeout überschritten wird, fährt Service Fabric unabhängig vom Quorum mit dem Upgrade fort. Diese Einstellung ist für Rollforwards auf „never“ (unbegrenzt) und für Rollbacks auf 1.200 Sekunden festgelegt. |
> | UpgradeTimeoutSec |PS, VS |Ein Timeout (in Sekunden), das für das gesamte Upgrade gilt. Wenn dieses Timeout erreicht wird, wird das Upgrade beendet und *FailureAction* wird ausgelöst. Der Standardwert ist „never“ (unendlich), und es wird empfohlen, diesen Wert Ihrer Anwendung entsprechend anzupassen. |
> | WhatIf | PS | Zulässige Werte sind **TRUE** und **FALSE**. Zeigt, was geschieht, wenn das Cmdlet ausgeführt wird. Das Cmdlet wird nicht ausgeführt. |

Die Kriterien *MaxPercentUnhealthyServices*, *MaxPercentUnhealthyPartitionsPerService* und *MaxPercentUnhealthyReplicasPerPartition* können pro Diensttyp für eine Anwendungsinstanz angegeben werden. Das dienstbezogene Festlegen dieser Parameter für eine Anwendung ermöglicht, dass diese verschiedene Diensttypen mit unterschiedlichen Auswertungsrichtlinien enthält. Ein zustandsloser Gatewaydiensttyp kann beispielsweise einen *MaxPercentUnhealthyPartitionsPerService*-Wert aufweisen, der sich vom Wert für einen zustandsbehafteten Engine-Diensttyp für eine bestimmte Anwendungsinstanz unterscheidet.

## <a name="sfctl-parameters"></a>SFCTL-Parameter

Verwenden Sie für Service Fabric-Anwendungsupgrades mit der Service Fabric-CLI den Befehl [sfctl application upgrade](./service-fabric-sfctl-application.md#sfctl-application-upgrade) und die folgenden erforderlichen und optionalen Parameter.

### <a name="required-parameters"></a>Erforderliche Parameter

| Parameter | BESCHREIBUNG |
| --- | --- |
| application-id  |Die ID der Anwendung, die aktualisiert wird. <br> Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat der Dienst beispielsweise den Namen „fabric/meineapp/app1“, weist die Dienstidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf.|
application-version |Die Version des Anwendungstyps, für den das Upgrade durchgeführt wird.|
parameters  |Eine JSON-codierte Liste mit Außerkraftsetzungen von Anwendungsparametern, die beim Upgrade der Anwendung angewendet werden sollen.|

### <a name="optional-parameters"></a>Optionale Parameter

| Parameter | BESCHREIBUNG |
| --- | --- |
default-service-health-policy | Eine [JSON](/rest/api/servicefabric/sfclient-model-servicetypehealthpolicy)-codierte Spezifikation der Integritätsrichtlinie, die standardmäßig zur Auswertung der Integrität eines Diensttyps verwendet wird. Der Überblick ist standardmäßig leer. |
failure-action | Zulässige Werte sind **Rollback**, **Manual** und **Invalid**. Dies beschreibt die ersetzende Aktion, die ausgeführt werden soll, wenn ein Upgrade vom Typ *Monitored* Verstöße gegen die Überwachungs- oder Integritätsrichtlinie erkennt. <br>**Rollback** gibt an, dass das Upgrade automatisch auf die Version vor dem Upgrade zurückgesetzt wird. <br>**Manual** gibt an, dass das Upgrade in den Upgrademodus *UnmonitoredManual* wechselt. <br>**Invalid** gibt an, dass die Fehleraktion ungültig ist.|
force-restart | Wenn Sie ein Konfigurations- oder Datenpaket aktualisieren, ohne den Dienstcode zu aktualisieren, wird der Dienst nur dann neu gestartet, wenn die „ForceRestart“-Eigenschaft auf **TRUE** festgelegt ist. Wenn das Upgrade abgeschlossen ist, benachrichtigt Service Fabric den Dienst, dass ein neues Konfigurations- oder Datenpaket verfügbar ist. Der Dienst muss dann die Änderungen anwenden. Bei Bedarf kann sich der Dienst neu starten. |
health-check-retry-timeout | Die Zeitspanne, in der Integritätsauswertungen wiederholt werden, wenn die Anwendung oder der Cluster vor der Ausführung von *FailureAction* fehlerhaft ist. Wird zuerst als Zeichenfolge interpretiert, die für eine ISO 8601-Dauer steht. Wenn dies nicht erfolgreich ist, erfolgt die Interpretation als Zahl, die für die Gesamtzahl an Millisekunden steht. Standardwert: PT0H10M0S. |
health-check-stable-duration | Die Zeitspanne, während der die Anwendung oder der Cluster fehlerfrei bleiben muss, bevor das Upgrade mit der nächsten Upgradedomäne fortgesetzt wird. Wird zuerst als Zeichenfolge interpretiert, die für eine ISO 8601-Dauer steht. Wenn dies nicht erfolgreich ist, erfolgt die Interpretation als Zahl, die für die Gesamtzahl an Millisekunden steht. Standardwert: PT0H2M0S. |
health-check-wait-duration | Die Zeitspanne, während der nach dem Abschließen einer Upgradedomäne gewartet werden soll, bevor Integritätsrichtlinien angewendet werden. Wird zuerst als Zeichenfolge interpretiert, die für eine ISO 8601-Dauer steht. Wenn dies nicht erfolgreich ist, erfolgt die Interpretation als Zahl, die für die Gesamtzahl an Millisekunden steht. Standardwert: 0.|
max-unhealthy-apps | Der empfohlene Standardwert ist 0. Geben Sie die maximale Anzahl der bereitgestellten Anwendungen an (siehe dazu den [Abschnitt zur Integrität](service-fabric-health-introduction.md)), die fehlerhaft sein können, bevor die Anwendung als fehlerhaft gilt und das Upgrade abgebrochen wird. Dieser Parameter definiert die Anwendungsintegrität für den Knoten und hilft bei der Ermittlung von Problemen während des Upgrades. Typischerweise erfolgt ein Lastenausgleich der Replikate der Anwendung auf dem anderen Knoten, sodass die Anwendung fehlerfrei zu sein scheint und das Upgrade so fortgesetzt werden kann. Durch das Angeben eines strikten Werts für *max-unhealthy-apps* für die Integrität kann Service Fabric ein Problem mit dem Anwendungspaket schnell erkennen und ein Fail-Fast-Upgrade erzeugen. Dieser wird als Zahl zwischen 0 und 100 dargestellt. |
Modus | Zulässige Werte sind **Monitored**, **UpgradeMode**, **UnmonitoredAuto** und **UnmonitoredManual**. Der Standardwert ist **UnmonitoredAuto**. Der Abschnitt *Erforderliche Parameter* von Visual Studio und PowerShell enthält Beschreibungen dieser Werte.|
replica-set-check-timeout |Dieser Wert wird in Sekunden gemessen. <br>**Statusfreier Dienst**: Service Fabric versucht innerhalb einer Upgradedomäne sicherzustellen, dass zusätzliche Instanzen des Diensts verfügbar sind. Wenn die Anzahl der Zielinstanzen größer als 1 ist, wartet Service Fabric bis zu einem maximalen Timeoutwert darauf, dass mehrere Instanzen verfügbar sind. Dieses Timeout wird mit der *replica-set-check-timeout*-Eigenschaft angegeben. Wenn das Timeout überschritten wird, fährt Service Fabric unabhängig von der Anzahl der Dienstinstanzen mit dem Upgrade fort. Wenn die Anzahl der Zielinstanzen gleich 1 ist, wartet Service Fabric nicht, sondern setzt das Upgrade direkt fort.<br><br>**Statusbehafteter Dienst**: Service Fabric versucht innerhalb einer Upgradedomäne sicherzustellen, dass die Replikatgruppe über ein Quorum verfügt. Service Fabric wartet bis zu einem maximalen Timeoutwert (angegeben durch die *replica-set-check-timeout*-Eigenschaft) darauf, dass ein Quorum verfügbar ist. Wenn das Timeout überschritten wird, fährt Service Fabric unabhängig vom Quorum mit dem Upgrade fort. Diese Einstellung ist für Rollforwards auf „never“ (unbegrenzt) und für Rollbacks auf 1.200 Sekunden festgelegt. |
service-health-policy | JSON-codierte Zuordnung mit einer Diensttypintegritätsrichtlinie pro Diensttypname. Der Überblick ist standardmäßig leer. [Parameter-JSON-Format](/rest/api/servicefabric/sfclient-model-applicationhealthpolicy#servicetypehealthpolicymap). Der JSON-Code für den Teil „Value“ enthält **MaxPercentUnhealthyServices**, **MaxPercentUnhealthyPartitionsPerService** und **MaxPercentUnhealthyReplicasPerPartition**. Der Abschnitt „Optionale Parameter“ von Visual Studio und PowerShell enthält Beschreibungen dieser Parameter.
timeout | Gibt die Timeoutperiode in Sekunden für den Vorgang an. Standardwert: 60. |
upgrade-domain-timeout | Die Zeitspanne, während der jede Upgradedomäne abgeschlossen werden muss, bevor *FailureAction* ausgeführt wird. Wird zuerst als Zeichenfolge interpretiert, die für eine ISO 8601-Dauer steht. Wenn dies nicht erfolgreich ist, erfolgt die Interpretation als Zahl, die für die Gesamtzahl an Millisekunden steht. Der Standardwert ist „never“ (unendlich), und es wird empfohlen, diesen Wert Ihrer Anwendung entsprechend anzupassen. Standardwert: P10675199DT02H48M05.4775807S. |
upgrade-timeout | Die Zeitspanne, während der jede Upgradedomäne abgeschlossen werden muss, bevor *FailureAction* ausgeführt wird. Wird zuerst als Zeichenfolge interpretiert, die für eine ISO 8601-Dauer steht. Wenn dies nicht erfolgreich ist, erfolgt die Interpretation als Zahl, die für die Gesamtzahl an Millisekunden steht. Der Standardwert ist „never“ (unendlich), und es wird empfohlen, diesen Wert Ihrer Anwendung entsprechend anzupassen. Standardwert: P10675199DT02H48M05.4775807S.|
warning-as-error | Zulässige Werte sind **TRUE** und **FALSE**. Der Standardwert ist **False**. Kann als Flag übergeben werden. Bei der Evaluierung der Integrität der Anwendung während des Upgrades werden die Warnereignisse für die Anwendung als Fehler behandelt. In der Standardeinstellung bewertet Service Fabric Warnereignisse in Bezug auf die Integrität nicht als Fehler, sodass das Upgrade auch bei Warnereignissen fortgesetzt werden kann. |

## <a name="next-steps"></a>Nächste Schritte
[Ihre Anwendung mit Visual Studio upgraden](service-fabric-application-upgrade-tutorial.md) beschreibt das Upgraden von Anwendungen mit Visual Studio.

[Ihre Anwendung mit PowerShell upgraden](service-fabric-application-upgrade-tutorial-powershell.md) beschreibt das Upgraden von Anwendungen mit PowerShell.

[Upgraden Ihrer Anwendung mithilfe der Service Fabric CLI unter Linux](service-fabric-application-lifecycle-sfctl.md#upgrade-application) führt Sie in einzelnen Schritten durch ein Anwendungsupgrade mit der Service Fabric CLI.

[Upgrade your Service Fabric Java application](service-fabric-get-started-eclipse.md#upgrade-your-service-fabric-java-application) (Aktualisieren Ihrer Service Fabric-Java-Anwendung)

Machen Sie Ihre Anwendungsupgrades kompatibel, indem Sie sich mit der [Datenserialisierung](service-fabric-application-upgrade-data-serialization.md)vertraut machen.

Erfahren Sie, wie Sie erweiterte Funktionen beim Upgrade Ihrer Anwendung nutzen, indem Sie sich mit den [weiterführenden Themen](service-fabric-application-upgrade-advanced.md)beschäftigen.

Informationen zum Beheben gängiger Probleme bei Anwendungsupgrades finden Sie in den Anweisungen unter [Problembehandlung bei Anwendungsupgrades](service-fabric-application-upgrade-troubleshooting.md).
