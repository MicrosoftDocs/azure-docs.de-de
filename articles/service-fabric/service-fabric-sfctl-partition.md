---
title: Azure Service Fabric CLI – sfctl partition | Microsoft-Dokumentation
description: Beschreibt die sfctl partition-Befehle der Service Fabric-Befehlszeilenschnittstelle (Command Line Interface, CLI).
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
ms.openlocfilehash: f7c9bcc51757100cb1fc957dee12213bc8bf2eec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60556604"
---
# <a name="sfctl-partition"></a>sfctl partition
Ermöglicht es, Partitionen für jeden Dienst abzufragen und zu verwalten.

## <a name="commands"></a>Befehle

|Get-Help|BESCHREIBUNG|
| --- | --- |
| data-loss | Diese API verursacht einen Datenverlust für die angegebene Partition. |
| data-loss-status | Ruft den Status eines Partitionsdatenverlustvorgangs ab, der über die StartDataLoss-API gestartet wurde. |
| health | Ruft die Integrität der angegebenen Service Fabric-Partition ab. |
| info | Ruft die Informationen zu einer Service Fabric-Partition ab. |
| list | Ruft die Liste der Partitionen eines Service Fabric-Diensts ab. |
| load | Ruft die Lastinformationen der angegebenen Service Fabric-Partition ab. |
| load-reset | Setzt die aktuelle Last einer Service Fabric-Partition zurück. |
| quorum-loss | Löst einen Quorumverlust für eine bestimmte zustandsbehaftete Dienstpartition aus. |
| quorum-loss-status | Ruft für eine Partition den Status eines Quorumverlustvorgangs ab, der über die StartQuorumLoss-API gestartet wurde. |
| recover | Veranlasst den Service Fabric-Cluster zu versuchen, eine bestimmte Partition wiederherzustellen, die derzeit in Quorumverlust gebunden ist. |
| recover-all | Veranlasst den Service Fabric-Cluster zu versuchen, alle Dienste (einschließlich der Systemdienste) wiederherzustellen, die derzeit in Quorumverlust gebunden sind. |
| report-health | Sendet einen Integritätsbericht zu der Service Fabric-Partition. |
| restart | Diese API startet einige oder alle Replikate oder Instanzen der angegebenen Partition neu. |
| restart-status | Ruft den Status eines PartitionRestart-Vorgangs ab, der über StartPartitionRestart gestartet wurde. |
| svc-name | Ruft den Namen des Service Fabric-Diensts für eine Partition ab. |

## <a name="sfctl-partition-data-loss"></a>sfctl partition data-loss
Diese API verursacht einen Datenverlust für die angegebene Partition.

Sie löst einen Aufruf der OnDataLossAsync-API der Partition aus.  Diese API verursacht einen Datenverlust für die angegebene Partition. Sie löst einen Aufruf der OnDataLoss-API der Partition aus. Der tatsächliche Datenverlust hängt vom angegebenen DataLossMode ab.  <br> – PartialDataLoss: Es wird nur ein Quorum der Replikate entfernt und OnDataLoss für die Partition ausgelöst, jedoch hängt der tatsächliche Datenverlust vom Vorhandensein ausgeführter Replikationen ab.  <br> – FullDataLoss: Alle Replikate werden entfernt, weshalb alle Daten verloren gehen und OnDataLoss ausgelöst wird. Diese API sollte nur mit einem zustandsbehafteten Dienst als Ziel aufgerufen werden. Es wird davon abgeraten, diese API mit einem Systemdienst als Ziel aufzurufen.

> [!NOTE] 
> Sobald diese API aufgerufen wurde, kann es nicht rückgängig gemacht werden. Durch Aufrufen von CancelOperation wird lediglich die Ausführung beendet und der interne Systemzustand bereinigt. Es werden keine Daten wiederhergestellt, wenn der Befehl weit genug fortgeschritten ist, um einen Datenverlust zu verursachen. Rufen Sie die GetDataLossProgress-API mit der gleichen OperationId ab, um Informationen für den mit dieser API gestarteten Vorgang zurückzugeben.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --data-loss-mode [erforderlich] | Diese Enumeration wird an die StartDataLoss-API zurückgegeben, um anzugeben, welche Art von Datenverlust verursacht wird. |
| --operation-id [erforderlich] | Eine GUID, die einen Aufruf dieser API kennzeichnet.  Diese GUID wird in die entsprechende GetProgress-API übergeben. |
| --partition-id [erforderlich] | Die Identität (ID) der Partition. |
| --service-id [erforderlich] | Die Identität (ID) des Diensts. Diese ID ist üblicherweise der vollständige Name des Diensts ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat der Dienst beispielsweise den Namen „fabric\:/meineapp/app1/svc1“, weist die Dienstidentität in 6.0 und höher den Wert „meineapp\~app1\~svc1“ und in früheren Versionen den Wert „meineapp/app1/svc1“ auf. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-partition-data-loss-status"></a>sfctl partition data-loss-status
Ruft den Status eines Partitionsdatenverlustvorgangs ab, der über die StartDataLoss-API gestartet wurde.

Ruft über die OperationId den Status eines Datenverlustvorgangs ab, der mit StartDataLoss gestartet wurde.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --operation-id [erforderlich] | Eine GUID, die einen Aufruf dieser API kennzeichnet.  Diese GUID wird in die entsprechende GetProgress-API übergeben. |
| --partition-id [erforderlich] | Die Identität (ID) der Partition. |
| --service-id [erforderlich] | Die Identität (ID) des Diensts. Diese ID ist üblicherweise der vollständige Name des Diensts ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat der Dienst beispielsweise den Namen „fabric\:/meineapp/app1/svc1“, weist die Dienstidentität in 6.0 und höher den Wert „meineapp\~app1\~svc1“ und in früheren Versionen den Wert „meineapp/app1/svc1“ auf. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-partition-health"></a>sfctl partition health
Ruft die Integrität der angegebenen Service Fabric-Partition ab.

Verwenden Sie „EventsHealthStateFilter“, um die Sammlung der Integritätsereignisse (HealthEvents), die für den Dienst gemeldet wurden, anhand des Integritätsstatus zu filtern. Verwenden Sie „ReplicasHealthStateFilter“, um die Auflistung der ReplicaHealthState-Objekte für die Partition zu filtern. Geben Sie eine Partition an, die nicht im Integritätsspeicher vorhanden ist, gibt diese Anforderung einen Fehler zurück.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --partition-id [erforderlich] | Die Identität (ID) der Partition. |
| --events-health-state-filter | Ermöglicht das Filtern der Collection zurückgegebener HealthEvent-Objekte anhand des Integritätsstatus. Die möglichen Werte für diesen Parameter entsprechen dem jeweiligen ganzzahligen Wert von einem der folgenden Integritätsstatus. Es werden nur Ereignisse zurückgegeben, die dem Filter entsprechen. Alle Ereignisse werden verwendet, um den aggregierten Integritätsstatus auszuwerten. Ist kein Filter angegeben, werden alle Einträge zurückgegeben. Ein Statuswert ist eine flagbasierte Enumeration, sodass der Wert eine Kombination der Werte sein kann, die mit dem bitweisen OR-Operator abgerufen werden. Ist der angegebene Wert beispielsweise gleich „6“, werden alle Ereignisse zurückgegeben, für die „HealthState“ den Wert für OK (2) oder Warnung (4) hat.  <br> – Default: Standardwert. Stimmt mit jedem Integritätsstatus (HealthState) überein. Der Wert ist gleich null.  <br> – None: Filter, der mit keinem Wert für „HealthState“ übereinstimmt. Wird verwendet, um keine Ergebnisse für eine angegebene Statussammlung zurückzugeben. Der Wert ist gleich „1“.  <br> – Ok: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Ok“ hat. Der Wert ist gleich „2“.  <br> – Warning: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Warning“ hat. Der Wert ist gleich „4“.  <br> – Error: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Error“ hat. Der Wert ist gleich „8“.  <br> – All: Filter, der mit Eingaben übereinstimmt, die einen beliebigen Wert für „HealthState“ haben. Der Wert ist gleich „65535“. |
| --exclude-health-statistics | Gibt an, ob die Integritätsstatistiken als Bestandteil des Abfrageergebnisses zurückgegeben werden sollen. Der Standardwert ist gleich „False“. Die Statistiken zeigen die Anzahl von untergeordneten Entitäten, die einen der Integritätszustände „Ok“, „Warning“ oder „Error“ aufweisen. |
| --replicas-health-state-filter | Ermöglicht das Filtern der Sammlung der ReplicaHealthState-Objekte für die Partition. Der Wert kann von Membern oder bitweisen Operationen für HealthStateFilter-Member abgerufen werden. Es werden nur Replikate zurückgegeben, die dem Filter entsprechen. Alle Replikate werden verwendet, um den aggregierten Integritätszustand auszuwerten. Ist kein Filter angegeben, werden alle Einträge zurückgegeben. Bei den Statuswerten handelt es sich um eine flagbasierte Enumeration, sodass der Wert eine Kombination der Werte sein kann, die mit dem bitweisen OR-Operator abgerufen werden. Ist der angegebene Wert beispielsweise gleich „6“, werden alle Ereignisse zurückgegeben, für die „HealthState“ den Wert „OK (2)“ oder „Warning (4)“ aufweist. Die möglichen Werte für diesen Parameter entsprechen dem jeweiligen ganzzahligen Wert von einem der folgenden Integritätsstatus.  <br> – Default: Standardwert. Stimmt mit jedem Integritätsstatus (HealthState) überein. Der Wert ist gleich null.  <br> – None: Filter, der mit keinem Wert für „HealthState“ übereinstimmt. Wird verwendet, um keine Ergebnisse für eine angegebene Statussammlung zurückzugeben. Der Wert ist gleich „1“.  <br> – Ok: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Ok“ hat. Der Wert ist gleich „2“.  <br> – Warning: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Warning“ hat. Der Wert ist gleich „4“.  <br> – Error: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Error“ hat. Der Wert ist gleich „8“.  <br> – All: Filter, der mit Eingaben übereinstimmt, die einen beliebigen Wert für „HealthState“ haben. Der Wert ist gleich „65535“. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-partition-info"></a>sfctl partition info
Ruft die Informationen zu einer Service Fabric-Partition ab.

Ruft Informationen über die angegebene Partition ab. Die Antwort enthält die Partitions-ID, die Partitionierungsschemainformationen, die Schlüssel, die von der Partition unterstützt werden, den Status, die Integrität und weitere Details zur Partition.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --partition-id [erforderlich] | Die Identität (ID) der Partition. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-partition-list"></a>sfctl partition list
Ruft die Liste der Partitionen eines Service Fabric-Diensts ab.

Die Antwort enthält die Partitions-ID, die Partitionierungsschemainformationen, die Schlüssel, die von der Partition unterstützt werden, den Status, die Integrität und weitere Details zur Partition.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --service-id [erforderlich] | Die Identität (ID) des Diensts. Diese ID ist üblicherweise der vollständige Name des Diensts ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat der Dienst beispielsweise den Namen „fabric\:/meineapp/app1/svc1“, weist die Dienstidentität in 6.0 und höher den Wert „meineapp\~app1\~svc1“ und in früheren Versionen den Wert „meineapp/app1/svc1“ auf. |
| --continuation-token | Der Parameter „continuation-token“ (Fortsetzungstoken) wird dazu verwendet, den nächsten Satz von Ergebnissen abzurufen. Ein Fortsetzungstoken mit einem nicht leeren Wert wird in die Antwort der API eingefügt, wenn die Ergebnisse aus dem System nicht in eine einzige Antwort passen. Wird dieser Wert an den nächsten API-Aufruf übergeben, gibt die API den nächsten Satz von Ergebnissen zurück. Gibt es keine weiteren Ergebnisse, enthält das Fortsetzungstoken keinen Wert. Der Wert dieses Parameters darf nicht als URL codiert sein. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-partition-load"></a>sfctl partition load
Ruft die Lastinformationen der angegebenen Service Fabric-Partition ab.

Gibt Informationen über die Last einer angegebenen Partition zurück. Die Antwort enthält eine Liste von Berichten über die Last einer Service Fabric-Partition. Jeder Bericht enthält den Namen und den Wert der Lastmetrik sowie die zuletzt gemeldete Zeit in UTC.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --partition-id [erforderlich] | Die Identität (ID) der Partition. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-partition-load-reset"></a>sfctl partition load-reset
Setzt die aktuelle Last einer Service Fabric-Partition zurück.

Setzt die aktuelle Last einer Service Fabric-Partition auf die Standardlast des Diensts zurück.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --partition-id [erforderlich] | Die Identität (ID) der Partition. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-partition-quorum-loss"></a>sfctl partition quorum-loss
Löst einen Quorumverlust für eine bestimmte zustandsbehaftete Dienstpartition aus.

Diese API ist bei einem temporären Quorumverlust in Ihrem Dienst nützlich. Rufen Sie die GetQuorumLossProgress-API mit der gleichen OperationId ab, um Informationen für den mit dieser API gestarteten Vorgang zurückzugeben. Diese kann nur für zustandsbehaftete persistente Dienste (HasPersistedState==true) aufgerufen werden.  Verwenden Sie diese API nicht bei zustandslosen Diensten oder reinen zustandsbehafteten In-Memory-Diensten.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --operation-id [erforderlich] | Eine GUID, die einen Aufruf dieser API kennzeichnet.  Diese GUID wird in die entsprechende GetProgress-API übergeben. |
| --partition-id [erforderlich] | Die Identität (ID) der Partition. |
| --quorum-loss-duration [erforderlich] | Die Zeitspanne, für die die Partition in Quorumverlust gebunden ist.  Diese muss in Sekunden angegeben sein. |
| --quorum-loss-mode [erforderlich] | Diese Enumeration wird an die StartQuorumLoss-API zurückgegeben, um anzugeben, welche Art von Quorumverlust verursacht wird. |
| --service-id [erforderlich] | Die Identität (ID) des Diensts. Diese ID ist üblicherweise der vollständige Name des Diensts ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat der Dienst beispielsweise den Namen „fabric\:/meineapp/app1/svc1“, weist die Dienstidentität in 6.0 und höher den Wert „meineapp\~app1\~svc1“ und in früheren Versionen den Wert „meineapp/app1/svc1“ auf. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl partition quorum-loss-status
Ruft für eine Partition den Status eines Quorumverlustvorgangs ab, der über die StartQuorumLoss-API gestartet wurde.

Ruft über die angegebene OperationId den Status eines Quorumverlustvorgangs ab, der mit StartQuorumLoss gestartet wurde.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --operation-id [erforderlich] | Eine GUID, die einen Aufruf dieser API kennzeichnet.  Diese GUID wird in die entsprechende GetProgress-API übergeben. |
| --partition-id [erforderlich] | Die Identität (ID) der Partition. |
| --service-id [erforderlich] | Die Identität (ID) des Diensts. Diese ID ist üblicherweise der vollständige Name des Diensts ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat der Dienst beispielsweise den Namen „fabric\:/meineapp/app1/svc1“, weist die Dienstidentität in 6.0 und höher den Wert „meineapp\~app1\~svc1“ und in früheren Versionen den Wert „meineapp/app1/svc1“ auf. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-partition-recover"></a>sfctl partition recover
Veranlasst den Service Fabric-Cluster zu versuchen, eine bestimmte Partition wiederherzustellen, die derzeit in Quorumverlust gebunden ist.

Dieser Vorgang sollte nur ausgeführt werden, wenn bekannt ist, dass die Replikate, die ausgefallen sind, nicht wiederhergestellt werden können. Eine falsche Verwendung dieser API kann dazu führen, dass Daten verloren gehen.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --partition-id [erforderlich] | Die Identität (ID) der Partition. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-partition-recover-all"></a>sfctl partition recover-all
Veranlasst den Service Fabric-Cluster zu versuchen, alle Dienste (einschließlich der Systemdienste) wiederherzustellen, die derzeit in Quorumverlust gebunden sind.

Dieser Vorgang sollte nur ausgeführt werden, wenn bekannt ist, dass die Replikate, die ausgefallen sind, nicht wiederhergestellt werden können. Eine falsche Verwendung dieser API kann dazu führen, dass Daten verloren gehen.

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

## <a name="sfctl-partition-report-health"></a>sfctl partition report-health
Sendet einen Integritätsbericht zu der Service Fabric-Partition.

Meldet den Integritätszustand der angegebenen Service Fabric-Partition. Der Bericht muss die Informationen über die Quelle des Integritätsberichts und die Eigenschaft enthalten, auf die sich der Bericht bezieht. Der Bericht wird an eine Service Fabric-Gatewaypartition gesendet, die diesen an den Integritätsspeicher weiterleitet. Der Bericht wird möglicherweise vom Gateway akzeptiert, aber nach zusätzlicher Überprüfung vom Integritätsspeicher abgelehnt. Beispielsweise kann der Integritätsspeicher den Bericht aufgrund eines ungültigen Parameters wie etwa einer veralteten Sequenznummer ablehnen. Um festzustellen, ob der Bericht im Integritätsspeicher angewendet wurde, überprüfen Sie, ob der Bericht im Ereignisabschnitt angezeigt wird.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --health-property [erforderlich] | Die Eigenschaft der Integritätsinformationen. <br><br> Eine Entität kann Integritätsberichte für verschiedene Eigenschaften enthalten. Die Eigenschaft ist eine Zeichenfolge und keine feste Enumeration, um dem Reporter Flexibilität zu ermöglichen und entsprechend die Zustandsbedingung zu kategorisieren, die den Bericht auslöst. Beispielsweise kann ein Reporter mit der SourceId „LocalWatchdog“ den Zustand des verfügbaren Datenträgers auf einem Knoten überwachen und folglich die AvailableDisk-Eigenschaft auf diesem Knoten melden. Der gleiche Reporter kann die Knotenkonnektivität überwachen und folglich eine Connectivity-Eigenschaft auf dem gleichen Knoten melden. Im Integritätsspeicher werden diese Berichte als separate Integritätsereignisse für den angegebenen Knoten behandelt. Zusammen mit der SourceId identifiziert die Eigenschaft eindeutig die Integritätsinformationen. |
| --health-state [erforderlich] | Mögliche Werte sind\: „Invalid“, „Ok“, „Warning“, „Error“, „Unknown“. |
| --partition-id [erforderlich] | Die Identität (ID) der Partition. |
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

## <a name="sfctl-partition-restart"></a>sfctl partition restart
Diese API startet einige oder alle Replikate oder Instanzen der angegebenen Partition neu.

Diese API ist hilfreich zum Testen von Failover. Wird diese API für eine zustandslose Dienstpartition verwendet, muss „RestartPartitionMode“ gleich „AllReplicasOrInstances“ sein. Rufen Sie die GetPartitionRestartProgress-API mit der Vorgangs-ID (OperationId) auf, mit der Sie den Status (Fortschritt) abgerufen haben.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --operation-id [erforderlich] | Eine GUID, die einen Aufruf dieser API kennzeichnet.  Diese GUID wird in die entsprechende GetProgress-API übergeben. |
| --partition-id [erforderlich] | Die Identität (ID) der Partition. |
| --restart-partition-mode [erforderlich] | Beschreibt, welche Partitionen neu gestartet werden. |
| --service-id [erforderlich] | Die Identität (ID) des Diensts. Diese ID ist üblicherweise der vollständige Name des Diensts ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat der Dienst beispielsweise den Namen „fabric\:/meineapp/app1/svc1“, weist die Dienstidentität in 6.0 und höher den Wert „meineapp\~app1\~svc1“ und in früheren Versionen den Wert „meineapp/app1/svc1“ auf. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-partition-restart-status"></a>sfctl partition restart-status
Ruft den Status eines PartitionRestart-Vorgangs ab, der über StartPartitionRestart gestartet wurde.

Ruft über die angegebene OperationId den Status eines PartitionRestart ab, der mit StartPartitionRestart gestartet wurde.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --operation-id [erforderlich] | Eine GUID, die einen Aufruf dieser API kennzeichnet.  Diese GUID wird in die entsprechende GetProgress-API übergeben. |
| --partition-id [erforderlich] | Die Identität (ID) der Partition. |
| --service-id [erforderlich] | Die Identität (ID) des Diensts. Diese ID ist üblicherweise der vollständige Name des Diensts ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat der Dienst beispielsweise den Namen „fabric\:/meineapp/app1/svc1“, weist die Dienstidentität in 6.0 und höher den Wert „meineapp\~app1\~svc1“ und in früheren Versionen den Wert „meineapp/app1/svc1“ auf. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-partition-svc-name"></a>sfctl partition svc-name
Ruft den Namen des Service Fabric-Diensts für eine Partition ab.

Ruft den Namen des Diensts für die angegebene Partition ab. Ein 404-Fehler wird zurückgegeben, wenn die Partitions-ID nicht im Cluster vorhanden ist.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --partition-id [erforderlich] | Die Identität (ID) der Partition. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

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
