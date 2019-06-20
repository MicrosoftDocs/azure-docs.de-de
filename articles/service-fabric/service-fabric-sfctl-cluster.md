---
title: Azure Service Fabric CLI – sfctl cluster | Microsoft-Dokumentation
description: Beschreibt die sfctl cluster-Befehle der Service Fabric-Befehlszeilenschnittstelle (Command Line Interface, CLI).
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 7bb399472d7e0ab14e6399fc8652d2eb132a866a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837317"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Ermöglicht es, Service Fabric-Cluster auszuwählen, zu verwalten und zu betreiben.

## <a name="commands"></a>Befehle

|Get-Help|BESCHREIBUNG|
| --- | --- |
| code-versions | Ruft eine Liste der Fabric-Codeversionen ab, die in einem Service Fabric-Cluster bereitgestellt werden. |
| config-versions | Ruft eine Liste der Fabric-Konfigurationsversionen ab, die in einem Service Fabric-Cluster bereitgestellt werden. |
| health | Ruft die Integrität eines Service Fabric-Clusters ab. |
| manifest | Ruft das Service Fabric-Clustermanifest ab. |
| operation-cancel | Bricht einen von einem Benutzer veranlassten Fehlervorgang ab. |
| operation-list | Ruft eine Liste der von Benutzern veranlassten Fehlervorgänge ab, die nach der bereitgestellten Eingabe gefiltert sind. |
| provision | Stellt die Code- oder Konfigurationspakete eines Service Fabric-Clusters bereit. |
| recover-system | Veranlasst den Service Fabric-Cluster zu versuchen, die Systemdienste wiederherzustellen, die derzeit in Quorumverlust gebunden sind. |
| report-health | Sendet einen Integritätsbericht zu dem Service Fabric-Cluster. |
| select | Stellt eine Verbindung mit einem Service Fabric-Clusterendpunkt her. |
| show-connection | Zeigt an, mit welchem Service Fabric-Cluster diese sfctl-Instanz verbunden ist. |
| unprovision | Hebt die Bereitstellung der Code- oder Konfigurationspakete eines Service Fabric-Clusters auf. |
| upgrade | Startet ein Upgraden der Code- oder Konfigurationsversion eines Service Fabric-Clusters. |
| upgrade-resume | Bewirkt, dass das Clusterupgrade zur nächsten Upgradedomäne wechselt. |
| upgrade-rollback | Führt einen Rollback für das Upgrade eines Service Fabric-Cluster aus. |
| upgrade-status | Ruft den Status des aktuellen Clusterupgrade ab. |
| upgrade-update | Aktualisiert die Upgradeparameter eines Service Fabric-Clusterupgrades. |

## <a name="sfctl-cluster-code-versions"></a>sfctl cluster code-versions
Ruft eine Liste der Fabric-Codeversionen ab, die in einem Service Fabric-Cluster bereitgestellt werden.

Ruft eine Liste von Informationen über Fabric-Codeversionen ab, die im Cluster bereitgestellt werden. Der Parameter „CodeVersion“ kann verwendet werden, um die Ausgabe optional nur auf eine bestimmte Version zu filtern.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --code-version | Die Produktversion von Service Fabric. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-cluster-config-versions"></a>sfctl cluster config-versions
Ruft eine Liste der Fabric-Konfigurationsversionen ab, die in einem Service Fabric-Cluster bereitgestellt werden.

Ruft eine Liste von Informationen über Fabric-Konfigurationsversionen ab, die im Cluster bereitgestellt werden. Der Parameter „ConfigVersion“ kann verwendet werden, um die Ausgabe optional nur auf eine bestimmte Version zu filtern.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --config-version | Die Konfigurationsversion von Service Fabric. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-cluster-health"></a>sfctl cluster health
Ruft die Integrität eines Service Fabric-Clusters ab.

Verwenden Sie „EventsHealthStateFilter“, um die Sammlung der Integritätsereignisse (HealthEvents), die für den Cluster gemeldet wurden, anhand des Integritätsstatus zu filtern. In ähnlicher Weise können Sie „NodesHealthStateFilter“ und „ApplicationsHealthStateFilter“ verwenden, um die Auflistung der zurückgegebenen Knoten und Anwendungen anhand ihres aggregierten Integritätsstatus zu filtern.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --applications-health-state-filter | Ermöglicht es, die Anwendungsintegritäts-Zustandsobjekte, die im Ergebnis einer Clusterintegritätsabfrage zurückgegeben werden, anhand des Integritätszustands zu filtern. Mögliche Werte für diesen Parameter sind ganzzahlige Werte, die von Membern oder über bitweise Operationen bei Membern der Enumeration „HealthStateFilter“ abgerufen werden können. Es werden nur Anwendungen zurückgegeben, die dem Filter entsprechen. Alle Anwendungen werden verwendet, um den aggregierten Integritätszustand auszuwerten. Ist kein Filter angegeben, werden alle Einträge zurückgegeben. Ein Statuswert ist eine kennzeichenbasierte Enumeration, sodass der Wert eine Kombination der Werte sein kann, die mit dem bitweisen ODER-Operator abgerufen werden. Ist der angegebene Wert beispielsweise gleich „6“, wird der Integritätszustand von Anwendungen zurückgegeben, für die „HealthState“ den Wert „OK (2)“ oder „Warning (4)“ aufweist.  <br> – Default: Standardwert. Stimmt mit jedem Integritätsstatus (HealthState) überein. Der Wert ist gleich null.  <br> – None: Filter, der mit keinem Wert für „HealthState“ übereinstimmt. Wird verwendet, um keine Ergebnisse für eine angegebene Statussammlung zurückzugeben. Der Wert ist gleich „1“.  <br> – Ok: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Ok“ hat. Der Wert ist gleich „2“.  <br> – Warning: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Warning“ hat. Der Wert ist gleich „4“.  <br> – Error: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Error“ hat. Der Wert ist gleich „8“.  <br> – All: Filter, der mit Eingaben übereinstimmt, die einen beliebigen Wert für „HealthState“ haben. Der Wert ist gleich „65535“. |
| --events-health-state-filter | Ermöglicht das Filtern der Collection zurückgegebener HealthEvent-Objekte anhand des Integritätsstatus. Die möglichen Werte für diesen Parameter entsprechen dem jeweiligen ganzzahligen Wert von einem der folgenden Integritätsstatus. Es werden nur Ereignisse zurückgegeben, die dem Filter entsprechen. Alle Ereignisse werden verwendet, um den aggregierten Integritätsstatus auszuwerten. Ist kein Filter angegeben, werden alle Einträge zurückgegeben. Ein Statuswert ist eine flagbasierte Enumeration, sodass der Wert eine Kombination der Werte sein kann, die mit dem bitweisen OR-Operator abgerufen werden. Ist der angegebene Wert beispielsweise gleich „6“, werden alle Ereignisse zurückgegeben, für die „HealthState“ den Wert für OK (2) oder Warnung (4) hat.  <br> – Default: Standardwert. Stimmt mit jedem Integritätsstatus (HealthState) überein. Der Wert ist gleich null.  <br> – None: Filter, der mit keinem Wert für „HealthState“ übereinstimmt. Wird verwendet, um keine Ergebnisse für eine angegebene Statussammlung zurückzugeben. Der Wert ist gleich „1“.  <br> – Ok: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Ok“ hat. Der Wert ist gleich „2“.  <br> – Warning: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Warning“ hat. Der Wert ist gleich „4“.  <br> – Error: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Error“ hat. Der Wert ist gleich „8“.  <br> – All: Filter, der mit Eingaben übereinstimmt, die einen beliebigen Wert für „HealthState“ haben. Der Wert ist gleich „65535“. |
| --exclude-health-statistics | Gibt an, ob die Integritätsstatistiken als Bestandteil des Abfrageergebnisses zurückgegeben werden sollen. Der Standardwert ist gleich „False“. Die Statistiken zeigen die Anzahl von untergeordneten Entitäten, die einen der Integritätszustände „Ok“, „Warning“ oder „Error“ aufweisen. |
| --include-system-application-health-statistics | Gibt an, ob die Integritätsstatistiken in die Anwendungsintegritätsstatistik von „fabric\:/System“ einbezogen werden sollen. Der Standardwert ist gleich „False“. Ist „IncludeSystemApplicationHealthStatistics“ auf „true“ festgelegt, enthalten die Integritätsstatistiken die Entitäten, die zur „fabric\:/System“-Anwendung gehören. Andernfalls enthält das Abfrageergebnis nur Integritätsstatistiken für Benutzeranwendungen. Die Integritätsstatistiken müssen für den anzuwendenden Parameter in das Abfrageergebnis einbezogen werden. |
| --nodes-health-state-filter | Ermöglicht es, die Knotenintegritäts-Zustandsobjekte, die im Ergebnis einer Dienstintegritätsabfrage zurückgegeben werden, anhand des Integritätszustands zu filtern. Die möglichen Werte für diesen Parameter entsprechen dem jeweiligen ganzzahligen Wert von einem der folgenden Integritätsstatus. Es werden nur Knoten zurückgegeben, die dem Filter entsprechen. Alle Knoten werden verwendet, um den aggregierten Integritätsstatus auszuwerten. Ist kein Filter angegeben, werden alle Einträge zurückgegeben. Ein Statuswert ist eine kennzeichenbasierte Enumeration, sodass der Wert eine Kombination der Werte sein kann, die mit dem bitweisen ODER-Operator abgerufen werden. Ist der angegebene Wert beispielsweise „6“, wird der Integritätszustand von Knoten zurückgegeben, für die „HealthState“ den Wert „OK (2)“ oder „Warning (4)“ aufweist.  <br> – Default: Standardwert. Stimmt mit jedem Integritätsstatus (HealthState) überein. Der Wert ist gleich null.  <br> – None: Filter, der mit keinem Wert für „HealthState“ übereinstimmt. Wird verwendet, um keine Ergebnisse für eine angegebene Statussammlung zurückzugeben. Der Wert ist gleich „1“.  <br> – Ok: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Ok“ hat. Der Wert ist gleich „2“.  <br> – Warning: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Warning“ hat. Der Wert ist gleich „4“.  <br> – Error: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Error“ hat. Der Wert ist gleich „8“.  <br> – All: Filter, der mit Eingaben übereinstimmt, die einen beliebigen Wert für „HealthState“ haben. Der Wert ist gleich „65535“. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-cluster-manifest"></a>sfctl cluster manifest
Ruft das Service Fabric-Clustermanifest ab.

Ruft das Service Fabric-Clustermanifest ab. Das Clustermanifest enthält Eigenschaften des Clusters, die unterschiedliche Knotentypen im Cluster, Sicherheitskonfigurationen, Fehler- und Upgradedomänentopologien usw. enthalten. Diese Eigenschaften werden als Teil der Datei „ClusterConfig.JSON“ angegeben, während ein eigenständiger Cluster bereitgestellt wird. Die meisten Informationen im Clustermanifest werden jedoch intern von Service Fabric während der Clusterbereitstellung in anderen Bereitstellungsszenarios generiert (z.B. bei Verwendung des Azure-Portals). Der Inhalt des Clustermanifests ist nur zu Informationszwecken vorgesehen, und es wird davon ausgegangen, dass es für Benutzer keinerlei Abhängigkeit vom Format des Dateiinhalts oder dessen Interpretation gibt.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-cluster-operation-cancel"></a>sfctl cluster operation-cancel
Bricht einen von einem Benutzer veranlassten Fehlervorgang ab.

Die folgenden APIs starten Fehlervorgänge, die mit CancelOperation abgebrochen werden können\: StartDataLoss, StartQuorumLoss, StartPartitionRestart und StartNodeTransition. Wenn „force“ auf „false“ festgelegt ist, wird der angegebene vom Benutzer veranlasste Vorgang ordnungsgemäß beendet und bereinigt.  Wenn „force“ auf „true“ festgelegt ist, wird der Befehl abgebrochen und möglicherweise wird ein interner Zustand beibehalten.  „force“ sollte nur mit Bedacht auf „true“ festgelegt werden. Aufrufe dieser API mit „true“ als Wert für „force“ sind nur dann zulässig, wenn diese API mit dem gleichen Testbefehl aufgerufen wurde, bei dem „false“ zuerst auf „false“ festgelegt war, oder wenn beim Testbefehl bereits OperationState.RollingBack für OperationState angegeben ist. 

Hinweis\: OperationState.RollingBack bedeutet, dass das System den internen Systemzustand bereinigt oder bereinigen wird, der durch Ausführung des Befehls verursacht wurde.  Es werden keine Daten wiederhergestellt, wenn der Testbefehl einen Datenverlust verursacht hätte.  Wenn Sie beispielsweise StartDataLoss aufrufen und dann diese API aufrufen, bereinigt das System nur den internen Zustand durch Ausführung des Befehls. Die Daten der Zielpartition werden nicht wiederhergestellt, wenn der Befehl weit genug fortgeschritten ist, um einen Datenverlust zu verursachen. 

> [!NOTE]
> Wenn diese API mit „force==true“ aufgerufen wird, bleibt möglicherweise der interne Zustand zurück.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --operation-id [erforderlich] | Eine GUID, die einen Aufruf dieser API kennzeichnet.  Diese GUID wird in die entsprechende GetProgress-API übergeben. |
| --force | Gibt an, ob für den internen Systemzustand ordnungsgemäß ein Rollback ausgeführt und es bereinigt wird, das durch Ausführung des vom Benutzer veranlassten Vorgangs geändert wird. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-cluster-operation-list"></a>sfctl cluster operation-list
Ruft eine Liste der von Benutzern veranlassten Fehlervorgänge ab, die nach der bereitgestellten Eingabe gefiltert sind.

Ruft die Liste der von Benutzern veranlassten Fehlervorgänge ab, die nach der bereitgestellten Eingabe gefiltert sind.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --state-filter | Dient zum Filtern nach OperationState für vom Benutzer veranlasste Vorgänge. <br> 65535 – Wählt „All“ aus <br> 1 – Wählt „Running“ aus. <br> 2 – Wählt „RollingBack“ aus. <br>8 – Wählt „Completed“ aus. <br>16 – Wählt „Faulted“ aus. <br>32 – Wählt „Cancelled“ aus. <br>64 – Wählt „ForceCancelled“ aus.  <br>Standardwert\: „65535“. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |
| --type-filter | Dient zum Filtern nach OperationType für vom Benutzer veranlasste Vorgänge. <br> 65535 – Wählt „All“ aus <br> 1 – Wählt „PartitionDataLoss“ aus. <br> 2 – Wählt „PartitionQuorumLoss“ aus. <br> 4 – Wählt „PartitionRestart“ aus. <br> 8 – Wählt „NodeTransition“ aus.  <br> Standardwert\: „65535“. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-cluster-provision"></a>sfctl cluster provision
Stellt die Code- oder Konfigurationspakete eines Service Fabric-Clusters bereit.

Überprüfen und Bereitstellen der Code- oder Konfigurationspakete eines Service Fabric-Clusters.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --cluster-manifest-file-path | Der Dateipfad des Clustermanifests. |
| --code-file-path | Der Dateipfad des Clustercodepakets. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-cluster-recover-system"></a>sfctl cluster recover-system
Veranlasst den Service Fabric-Cluster zu versuchen, die Systemdienste wiederherzustellen, die derzeit in Quorumverlust gebunden sind.

Veranlasst den Service Fabric-Cluster zu versuchen, die Systemdienste wiederherzustellen, die derzeit in Quorumverlust gebunden sind. Dieser Vorgang sollte nur ausgeführt werden, wenn bekannt ist, dass die Replikate, die ausgefallen sind, nicht wiederhergestellt werden können. Eine falsche Verwendung dieser API kann dazu führen, dass Daten verloren gehen.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-cluster-report-health"></a>sfctl cluster report-health
Sendet einen Integritätsbericht zu dem Service Fabric-Cluster.

Der Bericht muss die Informationen über die Quelle des Integritätsberichts und die Eigenschaft enthalten, auf die sich der Bericht bezieht. Der Bericht wird an einen Service Fabric-Gatewayknoten gesendet, der diesen an den Integritätsspeicher weiterleitet. Der Bericht wird möglicherweise vom Gateway akzeptiert, aber nach zusätzlicher Überprüfung vom Integritätsspeicher abgelehnt. Beispielsweise kann der Integritätsspeicher den Bericht aufgrund eines ungültigen Parameters wie etwa einer veralteten Sequenznummer ablehnen. Um festzustellen, ob der Bericht im Integritätsspeicher angewendet wurde, überprüfen Sie, ob der Bericht im Abschnitt „HealthEvents“ des Clusters angezeigt wird.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --health-property [erforderlich] | Die Eigenschaft der Integritätsinformationen. <br><br> Eine Entität kann Integritätsberichte für verschiedene Eigenschaften enthalten. Die Eigenschaft ist eine Zeichenfolge und keine feste Enumeration, um dem Reporter Flexibilität zu ermöglichen und entsprechend die Zustandsbedingung zu kategorisieren, die den Bericht auslöst. Beispielsweise kann ein Reporter mit der SourceId „LocalWatchdog“ den Zustand des verfügbaren Datenträgers auf einem Knoten überwachen und folglich die AvailableDisk-Eigenschaft auf diesem Knoten melden. Der gleiche Reporter kann die Knotenkonnektivität überwachen und folglich eine Connectivity-Eigenschaft auf dem gleichen Knoten melden. Im Integritätsspeicher werden diese Berichte als separate Integritätsereignisse für den angegebenen Knoten behandelt. Zusammen mit der SourceId identifiziert die Eigenschaft eindeutig die Integritätsinformationen. |
| --health-state [erforderlich] | Mögliche Werte sind\: „Invalid“, „Ok“, „Warning“, „Error“, „Unknown“. |
| --source-id [erforderlich] | Der Quellenname, der die Client-, Watchdog- oder Systemkomponente identifiziert, die die Integritätsinformationen generiert hat. |
| --description | Die Beschreibung der Integritätsinformationen. <br><br> Hierbei handelt es sich um Freitext, mit dem von Menschen lesbare Informationen über den Bericht hinzugefügt werden können. Die maximale Zeichenlänge für die Beschreibung beträgt 4096 Zeichen. Wenn die angegebene Zeichenfolge länger ist, wird sie automatisch abgeschnitten. Wird diese abgeschnitten, enthalten die letzten Zeichen der Beschreibung einen Marker „[abgeschnitten]“, und die gesamte Zeichenfolgenlänge beträgt 4096 Zeichen. Das Vorhandensein des Markers ist ein Hinweis für Benutzer, dass abgeschnittene Zeichenfolgen vorkommen. Beachten Sie, dass die Beschreibung bei abgeschnittenen Zeichenfolgen weniger als 4096 Zeichen von der ursprünglichen Zeichenfolge enthält. |
| --immediate | Ein Flag, das angibt, ob der Bericht sofort gesendet werden soll. <br><br> Ein Integritätsbericht wird an eine Service Fabric-Gatewayanwendung gesendet, die diesen an den Integritätsspeicher weiterleitet. Wenn „Immediate“ auf „true“ festgelegt ist, wird der Bericht sofort vom HTTP-Gateway an den Integritätsspeicher gesendet, unabhängig von den Fabric-Clienteinstellungen, die die HTTP-Gatewayanwendung verwendet. Dies ist für kritische Berichte hilfreich, die schnellstmöglich gesendet werden sollen. Abhängig von der zeitlichen Steuerung und anderen Bedingungen können beim Senden des Berichts trotzdem möglicherweise Fehler auftreten, z.B., wenn das HTTP-Gateway geschlossen ist oder die Meldung das Gateway nicht erreicht. Wenn „Immediate“ auf „false“ festgelegt ist, wird der Bericht basierend auf den Integritätsclienteinstellungen vom HTTP-Gateway gesendet. Aus diesem Grund wird der Bericht entsprechend der HealthReportSendInterval-Konfiguration batchweise verarbeitet. Dies ist die empfohlene Einstellung, da der Integritätsclient die Optimierung von Meldungen des Integritätsberichts für den Integritätsspeicher sowie die Verarbeitung von Integritätsberichten ermöglicht. Standardmäßig werden Berichte nicht sofort gesendet. |
| --remove-when-expired | Ein Wert, der angibt, ob der Bericht aus dem Integritätsspeicher entfernt wird, wenn er abläuft. <br><br> Wenn dieser Wert auf „true“ festgelegt ist, wird der Bericht aus dem Integritätsspeicher entfernt, nachdem er abgelaufen ist. Wenn dieser Wert auf „false“ festgelegt ist, wird der Bericht als Fehler behandelt, wenn er abgelaufen ist. Der Wert dieser Eigenschaft ist standardmäßig auf „false“ festgelegt. Wenn Clients in regelmäßigen Abständen Berichte erstellen, sollte RemoveWhenExpired auf „false“ (Standardwert) festgelegt sein. Denn falls der Reporter Probleme (z.B. Deadlocks) haben und keine Berichte erstellen können sollte, wird die Entität entsprechend auf Fehler ausgewertet, wenn der Integritätsbericht abläuft. Hierdurch wird die Entität nicht als in einem fehlerhaften Integritätszustand befindlich gekennzeichnet. |
| --sequence-number | Die Sequenznummer für diesen Integritätsbericht als numerische Zeichenfolge. <br><br> Die Sequenznummer des Berichts wird vom Integritätsspeicher zur Erkennung veralteter Berichte verwendet. Wenn diese nicht angegeben ist, wird beim Hinzufügen eines Berichts automatisch eine Sequenznummer vom Integritätsclient generiert. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |
| --ttl | Die Dauer, für die dieser Integritätsbericht gültig ist. Bei diesem Feld wird das ISO8601-Format zur Angabe der Dauer verwendet. <br><br> Wenn Clients in regelmäßigen Abständen Berichte erstellen, sollten sie Berichte in einem höherer Intervall als die Gültigkeitsdauer senden. Wenn Clients Berichte zur Übertragung erstellen, können sie die Gültigkeitsdauer auf einen unbegrenzten Wert festlegen. Wenn die Gültigkeitsdauer abläuft, wird das Integritätsereignis, das die Integritätsinformationen enthält, entweder aus dem Integritätsspeicher entfernt, sofern RemoveWhenExpired auf „true“ festgelegt ist, oder auf Fehler ausgewertet, sofern RemoveWhenExpired auf „false“ festgelegt ist. Wenn kein Wert angegeben ist, wird die Gültigkeitsdauer standardmäßig auf einen unbegrenzten Wert festgelegt. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-cluster-select"></a>sfctl cluster select
Stellt eine Verbindung mit einem Service Fabric-Clusterendpunkt her.

Wird eine Verbindung mit einem sicheren Cluster hergestellt, geben Sie einen absoluten Pfad zu einer Zertifikat- (CRT) und einer Schlüsseldatei (KEY) oder einer einzelnen Datei mit beidem (PEM) an. Geben Sie nicht beides an. Optional können Sie in diesem Fall auch einen absoluten Pfad zu einer Bundledatei einer Zertifizierungsstelle oder zu einem Verzeichnis mit Zertifikaten einer vertrauenswürdigen Zertifizierungsstelle angeben. Wenn ein Verzeichnis mit Zertifizierungsstellenzertifikaten verwendet wird, muss `c_rehash <directory>` von OpenSSL zuerst ausgeführt werden, um die Zertifikathashes zu berechnen und die entsprechenden symbolischen Links zu erstellen.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --endpoint [erforderlich] | Clusterendpunkt-URL einschließlich Port und HTTP- oder HTTPS-Präfix. |
| --aad | Die Authentifizierung erfolgt über Azure Active Directory. |
| --ca | Absoluter Pfad zu dem Verzeichnis mit Zertifikaten einer Zertifizierungsstelle, das als gültige Bundledatei oder Bundledatei einer Zertifizierungsstelle behandelt werden soll. |
| --cert | Absoluter Pfad zu einer Clientzertifikatdatei. |
| --key | Absoluter Pfad zu einer Schlüsseldatei eines Clientzertifikats. |
| --no-verify | Die Überprüfung auf Zertifikate wird deaktiviert, wenn HTTPS verwendet wird. Hinweis\: Da dies eine unsichere Option ist, sollte sie nicht für Produktionsumgebungen verwendet werden. |
| --pem | Absoluter Pfad zu einem Clientzertifikat, das als PEM-Datei angegeben ist. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-cluster-show-connection"></a>sfctl cluster show-connection
Zeigt an, mit welchem Service Fabric-Cluster diese sfctl-Instanz verbunden ist.

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-cluster-unprovision"></a>sfctl cluster unprovision
Hebt die Bereitstellung der Code- oder Konfigurationspakete eines Service Fabric-Clusters auf.

Das separate Aufheben der Bereitstellung von Code und Konfiguration wird unterstützt.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --code-version | Die Version des Clustercodepakets. |
| --config-version | Die Version des Clustermanifests. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-cluster-upgrade"></a>sfctl cluster upgrade
Startet ein Upgraden der Code- oder Konfigurationsversion eines Service Fabric-Clusters.

Überprüfung der bereitgestellten Upgradeparameter und Starten des Upgrades des Codes oder der Konfigurationsversion eines Service Fabric-Clusters, wenn die Parameter zulässig sind

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --app-health-map | JSON-codiertes Wörterbuch von Paaren aus Anwendungsname und dem Höchstprozentsatz an Integritätseinbußen vor der Auslösung eines Fehlers |
| --app-type-health-map | JSON-codiertes Wörterbuch von Paaren aus Anwendungstyp und dem Höchstprozentsatz an Integritätseinbußen vor der Auslösung eines Fehlers |
| --code-version | Die Clustercodeversion |
| --config-version | Die Clusterkonfigurationsversion |
| --delta-health-evaluation | Ermöglicht die Bewertung von relativen Integritätsänderungen anstelle von absoluten Integritätswerten nachdem jede Upgradedomäne erfolgreich abgeschlossen wurde. |
| --delta-unhealthy-nodes | Der während eines Clusterupgrades zulässige Höchstprozentsatz der Integritätsminderung von Knoten.  Standardwert\: „10“. <br><br> Das Delta wird zwischen den Status der Knoten am Anfang des Upgrades und den Status der Knoten zum Zeitpunkt der Integritätsbewertung gemessen. Diese Überprüfung wird nach jedem erfolgreichen Upgrade einer Upgradedomäne durchgeführt, um sicherzustellen, dass sich der globale Clusterstatus innerhalb eines zulässigen Rahmens befindet. |
| --failure-action | Folgende Werte sind möglich\: „Invalid“, „Rollback“, „Manual“. |
| --force-restart | Während eines Upgrades wird ein erzwungener Neustart der Prozesse durchgeführt, auch wenn sich die Codeversion nicht geändert hat. <br><br> Das Upgrade ändert nur die Konfiguration oder die Daten. |
| --health-check-retry | Die Zeitspanne zwischen den Versuchen, Integritätsprüfungen durchzuführen, wenn die Anwendung oder der Cluster nicht fehlerfrei ist. |
| --health-check-stable | Die Zeitspanne, während der die Anwendung oder der Cluster fehlerfrei bleiben muss, bevor das Upgrade mit der nächsten Upgradedomäne fortgesetzt wird. <br><br> Wird zuerst als Zeichenfolge interpretiert, die für eine ISO 8601-Dauer steht. Wenn dies nicht erfolgreich ist, erfolgt die Interpretation als Zahl, die für die Gesamtzahl an Millisekunden steht. |
| --health-check-wait | Die Zeitspanne, während der nach dem Abschließen einer Upgradedomäne gewartet werden soll, bevor der Integritätsprüfungsprozess gestartet wird. |
| --replica-set-check-timeout | Die maximale Zeitspanne, während der die Verarbeitung einer Upgradedomäne blockiert und Verfügbarkeitsverlust verhindert wird, wenn es unerwartete Probleme gibt. <br><br> Wenn dieses Timeout abläuft, wird die Verarbeitung der Upgradedomäne unabhängig von Verfügbarkeitsverlustproblemen fortgesetzt. Das Timeout wird beim Start jeder Upgradedomäne zurückgesetzt. Gültige Werte liegen zwischen 0 und 42.949.672.925 (einschließlich). |
| --rolling-upgrade-mode | Folgende Werte sind möglich\: „Invalid“, „UnmonitoredAuto“, „UnmonitoredManual“, „Monitored“.  Standardwert\: „UnmonitoredAuto“. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |
| --unhealthy-applications | Der maximal zulässige Prozentsatz fehlerhafter Anwendungen, bevor ein Fehler gemeldet wird. <br><br> Soll es z. B.zulässig sein, dass 10 % der Anwendungen fehlerhaft sind, muss dieser Wert gleich „10“ sein. Der Prozentsatz entspricht dem maximalen tolerierten Prozentsatz an Anwendungen, die fehlerhaft sein können, bevor der Cluster als fehlerhaft behandelt wird. Wird der Prozentsatz eingehalten, gibt es aber mindestens eine fehlerhafte Anwendung, wird die Integrität als „Warning“ ausgewertet. Dies wird berechnet, indem die Anzahl von fehlerhaften Anwendungen durch die Gesamtanzahl von Anwendungsinstanzen im Cluster dividiert wird, wobei Anwendungen mit Anwendungstypen ausgeschlossen werden, die in „ApplicationTypeHealthPolicyMap“ enthalten sind. Die Berechnung wird aufgerundet, um einen Fehler bei einer kleinen Anzahl von Anwendungen zu tolerieren. |
| --unhealthy-nodes | Der maximal zulässige Prozentsatz fehlerhafter Knoten, bevor ein Fehler gemeldet wird. <br><br> Soll es z. B.zulässig sein, dass 10 % der Knoten fehlerhaft sind, muss dieser Wert gleich „10“ sein. Der Prozentsatz entspricht dem maximalen tolerierten Prozentsatz an Knoten, die fehlerhaft sein können, bevor der Cluster als fehlerhaft behandelt wird. Wird der Prozentsatz eingehalten, gibt es aber mindestens einen fehlerhaften Knoten, wird die Integrität als „Warning“ ausgewertet. Der Prozentsatz wird berechnet, indem die Anzahl von fehlerhaften Knoten durch die Gesamtanzahl von Knoten im Cluster dividiert wird. Die Berechnung wird aufgerundet, um einen Fehler auf einer kleinen Anzahl von Knoten zu tolerieren. Beim Konfigurieren dieses Prozentsatzes muss berücksichtigt werden, dass in großen Clustern immer einige Knoten inaktiv oder aufgrund von Wartungsarbeiten nicht verfügbar sind. |
| --upgrade-domain-delta-unhealthy-nodes | Der während eines Clusterupgrades zulässige Höchstprozentsatz der Integritätsminderung von Upgradedomänenknoten.  Stadardwert\: „15“. <br><br> Das Delta wird zwischen den Status der Upgradedomänenknoten am Anfang des Upgrades und den Status der Upgradedomänenknoten zum Zeitpunkt der Integritätsbewertung gemessen. Diese Überprüfung wird nach jedem erfolgreichen Upgrade einer Upgradedomäne für alle abgeschlossenen Upgradedomänen durchgeführt, um sicherzustellen, dass sich der globale Clusterstatus innerhalb eines zulässigen Rahmens befindet. |
| --upgrade-domain-timeout | Die Zeitspanne, während der jede Upgradedomäne abgeschlossen werden muss, bevor die Fehleraktion (FailureAction) ausgeführt wird. <br><br> Wird zuerst als Zeichenfolge interpretiert, die für eine ISO 8601-Dauer steht. Wenn dies nicht erfolgreich ist, erfolgt die Interpretation als Zahl, die für die Gesamtzahl an Millisekunden steht. |
| --upgrade-timeout | Die Zeitspanne, während der das gesamte Upgrade abgeschlossen werden muss, bevor die Fehleraktion (FailureAction) ausgeführt wird. <br><br> Wird zuerst als Zeichenfolge interpretiert, die für eine ISO 8601-Dauer steht. Wenn dies nicht erfolgreich ist, erfolgt die Interpretation als Zahl, die für die Gesamtzahl an Millisekunden steht. |
| --warning-as-error | Gibt an, ob Warnungen mit demselben Schweregrad wie Fehler berücksichtigt werden. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-cluster-upgrade-resume"></a>sfctl cluster upgrade-resume
Bewirkt, dass das Clusterupgrade zur nächsten Upgradedomäne wechselt.

Veranlasst, dass das Upgrade für den Clustercode bzw. die Clusterkonfiguration ggf. mit der nächsten Upgradedomäne fortfährt.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --upgrade-domain [erforderlich] | Die nächste Upgradedomäne für dieses Clusterupgrade. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-cluster-upgrade-rollback"></a>sfctl cluster upgrade-rollback
Führt einen Rollback für das Upgrade eines Service Fabric-Cluster aus.

Führt ein Rollback für das Code- oder Konfigurationsupgrade eines Service Fabric-Clusters aus.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-cluster-upgrade-status"></a>sfctl cluster upgrade-status
Ruft den Status des aktuellen Clusterupgrade ab.

Ruft den aktuellen Status des laufenden Clusterupgrades ab. Wenn zum jeweiligen Zeitpunkt kein Upgrade ausgeführt wird, wird der letzte Status des vorherigen Clusterupgrades abgerufen.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-cluster-upgrade-update"></a>sfctl cluster upgrade-update
Aktualisiert die Upgradeparameter eines Service Fabric-Clusterupgrades.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --app-health-map | JSON-codiertes Wörterbuch von Paaren aus Anwendungsname und dem Höchstprozentsatz an Integritätseinbußen vor der Auslösung eines Fehlers |
| --app-type-health-map | JSON-codiertes Wörterbuch von Paaren aus Anwendungstyp und dem Höchstprozentsatz an Integritätseinbußen vor der Auslösung eines Fehlers |
| --delta-health-evaluation | Ermöglicht die Bewertung von relativen Integritätsänderungen anstelle von absoluten Integritätswerten nachdem jede Upgradedomäne erfolgreich abgeschlossen wurde. |
| --delta-unhealthy-nodes | Der während eines Clusterupgrades zulässige Höchstprozentsatz der Integritätsminderung von Knoten.  Standardwert\: „10“. <br><br> Das Delta wird zwischen den Status der Knoten am Anfang des Upgrades und den Status der Knoten zum Zeitpunkt der Integritätsbewertung gemessen. Diese Überprüfung wird nach jedem erfolgreichen Upgrade einer Upgradedomäne durchgeführt, um sicherzustellen, dass sich der globale Clusterstatus innerhalb eines zulässigen Rahmens befindet. |
| --failure-action | Folgende Werte sind möglich\: „Invalid“, „Rollback“, „Manual“. |
| --force-restart | Während eines Upgrades wird ein erzwungener Neustart der Prozesse durchgeführt, auch wenn sich die Codeversion nicht geändert hat. <br><br> Das Upgrade ändert nur die Konfiguration oder die Daten. |
| --health-check-retry | Die Zeitspanne zwischen den Versuchen, Integritätsprüfungen durchzuführen, wenn die Anwendung oder der Cluster nicht fehlerfrei ist. |
| --health-check-stable | Die Zeitspanne, während der die Anwendung oder der Cluster fehlerfrei bleiben muss, bevor das Upgrade mit der nächsten Upgradedomäne fortgesetzt wird. <br><br> Wird zuerst als Zeichenfolge interpretiert, die für eine ISO 8601-Dauer steht. Wenn dies nicht erfolgreich ist, erfolgt die Interpretation als Zahl, die für die Gesamtzahl an Millisekunden steht. |
| --health-check-wait | Die Zeitspanne, während der nach dem Abschließen einer Upgradedomäne gewartet werden soll, bevor der Integritätsprüfungsprozess gestartet wird. |
| --replica-set-check-timeout | Die maximale Zeitspanne, während der die Verarbeitung einer Upgradedomäne blockiert und Verfügbarkeitsverlust verhindert wird, wenn es unerwartete Probleme gibt. <br><br> Wenn dieses Timeout abläuft, wird die Verarbeitung der Upgradedomäne unabhängig von Verfügbarkeitsverlustproblemen fortgesetzt. Das Timeout wird beim Start jeder Upgradedomäne zurückgesetzt. Gültige Werte liegen zwischen 0 und 42.949.672.925 (einschließlich). |
| --rolling-upgrade-mode | Folgende Werte sind möglich\: „Invalid“, „UnmonitoredAuto“, „UnmonitoredManual“, „Monitored“.  Standardwert\: „UnmonitoredAuto“. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |
| --unhealthy-applications | Der maximal zulässige Prozentsatz fehlerhafter Anwendungen, bevor ein Fehler gemeldet wird. <br><br> Soll es z. B.zulässig sein, dass 10 % der Anwendungen fehlerhaft sind, muss dieser Wert gleich „10“ sein. Der Prozentsatz entspricht dem maximalen tolerierten Prozentsatz an Anwendungen, die fehlerhaft sein können, bevor der Cluster als fehlerhaft behandelt wird. Wird der Prozentsatz eingehalten, gibt es aber mindestens eine fehlerhafte Anwendung, wird die Integrität als „Warning“ ausgewertet. Dies wird berechnet, indem die Anzahl von fehlerhaften Anwendungen durch die Gesamtanzahl von Anwendungsinstanzen im Cluster dividiert wird, wobei Anwendungen mit Anwendungstypen ausgeschlossen werden, die in „ApplicationTypeHealthPolicyMap“ enthalten sind. Die Berechnung wird aufgerundet, um einen Fehler bei einer kleinen Anzahl von Anwendungen zu tolerieren. |
| --unhealthy-nodes | Der maximal zulässige Prozentsatz fehlerhafter Knoten, bevor ein Fehler gemeldet wird. <br><br> Soll es z. B.zulässig sein, dass 10 % der Knoten fehlerhaft sind, muss dieser Wert gleich „10“ sein. Der Prozentsatz entspricht dem maximalen tolerierten Prozentsatz an Knoten, die fehlerhaft sein können, bevor der Cluster als fehlerhaft behandelt wird. Wird der Prozentsatz eingehalten, gibt es aber mindestens einen fehlerhaften Knoten, wird die Integrität als „Warning“ ausgewertet. Der Prozentsatz wird berechnet, indem die Anzahl von fehlerhaften Knoten durch die Gesamtanzahl von Knoten im Cluster dividiert wird. Die Berechnung wird aufgerundet, um einen Fehler auf einer kleinen Anzahl von Knoten zu tolerieren. Beim Konfigurieren dieses Prozentsatzes muss berücksichtigt werden, dass in großen Clustern immer einige Knoten inaktiv oder aufgrund von Wartungsarbeiten nicht verfügbar sind. |
| --upgrade-domain-delta-unhealthy-nodes | Der während eines Clusterupgrades zulässige Höchstprozentsatz der Integritätsminderung von Upgradedomänenknoten.  Stadardwert\: „15“. <br><br> Das Delta wird zwischen den Status der Upgradedomänenknoten am Anfang des Upgrades und den Status der Upgradedomänenknoten zum Zeitpunkt der Integritätsbewertung gemessen. Diese Überprüfung wird nach jedem erfolgreichen Upgrade einer Upgradedomäne für alle abgeschlossenen Upgradedomänen durchgeführt, um sicherzustellen, dass sich der globale Clusterstatus innerhalb eines zulässigen Rahmens befindet. |
| --upgrade-domain-timeout | Die Zeitspanne, während der jede Upgradedomäne abgeschlossen werden muss, bevor die Fehleraktion (FailureAction) ausgeführt wird. <br><br> Wird zuerst als Zeichenfolge interpretiert, die für eine ISO 8601-Dauer steht. Wenn dies nicht erfolgreich ist, erfolgt die Interpretation als Zahl, die für die Gesamtzahl an Millisekunden steht. |
| --upgrade-kind | Mögliche Werte sind\: „Invalid“, „Rolling“, „Rolling_ForceRestart“.  Standardwert\: „Rolling“. |
| --upgrade-timeout | Die Zeitspanne, während der das gesamte Upgrade abgeschlossen werden muss, bevor die Fehleraktion (FailureAction) ausgeführt wird. <br><br> Wird zuerst als Zeichenfolge interpretiert, die für eine ISO 8601-Dauer steht. Wenn dies nicht erfolgreich ist, erfolgt die Interpretation als Zahl, die für die Gesamtzahl an Millisekunden steht. |
| --warning-as-error | Gibt an, ob Warnungen mit demselben Schweregrad wie Fehler berücksichtigt werden. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |


## <a name="next-steps"></a>Nächste Schritte
- [Einrichten](service-fabric-cli.md) der Service Fabric-Befehlszeilenschnittstelle
- Informationen zum Verwenden der Service Fabric-Befehlszeilenschnittstelle mit den [Beispielskripts](/azure/service-fabric/scripts/sfctl-upgrade-application)