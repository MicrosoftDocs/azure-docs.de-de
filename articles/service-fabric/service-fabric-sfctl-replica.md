---
title: Azure Service Fabric CLI – sfctl replica | Microsoft-Dokumentation
description: Beschreibt die sfctl replica-Befehle der Service Fabric-Befehlszeilenschnittstelle (Command Line Interface, CLI).
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
ms.openlocfilehash: d0a7199ff0e9cb17c3fbc179a9b37a6620f521f9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60544669"
---
# <a name="sfctl-replica"></a>sfctl replica
Ermöglicht es, Replikate zu verwalten, die zu Servicepartitionen gehören.

## <a name="commands"></a>Befehle

|Get-Help|BESCHREIBUNG|
| --- | --- |
| deployed | Ruft die Details eines Replikats ab, das auf einem Service Fabric-Knoten bereitgestellt wird. |
| deployed-list | Ruft die Liste der Replikate ab, die auf einem Service Fabric-Knoten bereitgestellt werden. |
| health | Ruft die Integrität eines zustandsbehafteten Service Fabric-Dienstreplikats oder einer zustandslosen Service Fabric-Dienstinstanz ab. |
| info | Ruft die Informationen zu einem Replikat einer Service Fabric-Partition ab. |
| list | Ruft die Informationen zu Replikaten einer Service Fabric-Dienstpartition ab. |
| remove | Entfernt ein Dienstreplikat, das auf einem Knoten ausgeführt wird. |
| report-health | Sendet einen Integritätsbericht zu dem Service Fabric-Replikat. |
| restart | Startet ein Dienstreplikat eines permanenten Diensts neu, das auf einem Knoten ausgeführt wird. |

## <a name="sfctl-replica-deployed"></a>sfctl replica deployed
Ruft die Details eines Replikats ab, das auf einem Service Fabric-Knoten bereitgestellt wird.

Ruft die Details des Replikats ab, das auf einem Service Fabric-Knoten bereitgestellt wird. Die Informationen umfassen Dienstart, Dienstname, aktueller Dienstvorgang, Startzeitpunkt des aktuellen Dienstvorgangs, Partitions-ID, Replikat/Instanz-ID, gemeldete Last und weitere Informationen.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --node-name [erforderlich] | Der Name des Knotens. |
| --partition-id [erforderlich] | Die Identität (ID) der Partition. |
| --replica-id [erforderlich] | Der Bezeichner (ID) des Replikats. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-replica-deployed-list"></a>sfctl replica deployed-list
Ruft die Liste der Replikate ab, die auf einem Service Fabric-Knoten bereitgestellt werden.

Ruft die Liste mit den Informationen über die Replikate ab, die auf einem Service Fabric-Knoten bereitgestellt sind. Die Informationen umfassen Partitions-ID, Replikat-ID, Status des Replikats, Name des Diensts, Name des Diensttyps und andere Informationen. Verwenden Sie die Abfrageparameter „PartitionId“ oder „ServiceManifestName“, um Informationen über die bereitgestellte Replikate zurückzugeben, die den angegebenen Werten für diese Parameter entsprechen.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich] | Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
| --node-name [erforderlich] | Der Name des Knotens. |
| --partition-id | Die Identität (ID) der Partition. |
| --service-manifest-name | Der Name eines Dienstmanifests, das als Bestandteil eines Anwendungstyps in einem Service Fabric-Cluster registriert ist. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-replica-health"></a>sfctl replica health
Ruft die Integrität eines zustandsbehafteten Service Fabric-Dienstreplikats oder einer zustandslosen Service Fabric-Dienstinstanz ab.

Ruft die Integrität eines Service Fabric-Replikats ab. Verwenden Sie „EventsHealthStateFilter“, um die Sammlung der Integritätsereignisse (HealthEvents), die für das Replikat gemeldet wurden, anhand des Integritätsstatus zu filtern.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --partition-id [erforderlich] | Die Identität (ID) der Partition. |
| --replica-id [erforderlich] | Der Bezeichner (ID) des Replikats. |
| --events-health-state-filter | Ermöglicht das Filtern der Collection zurückgegebener HealthEvent-Objekte anhand des Integritätsstatus. Die möglichen Werte für diesen Parameter entsprechen dem jeweiligen ganzzahligen Wert von einem der folgenden Integritätsstatus. Es werden nur Ereignisse zurückgegeben, die dem Filter entsprechen. Alle Ereignisse werden verwendet, um den aggregierten Integritätsstatus auszuwerten. Ist kein Filter angegeben, werden alle Einträge zurückgegeben. Ein Statuswert ist eine flagbasierte Enumeration, sodass der Wert eine Kombination der Werte sein kann, die mit dem bitweisen OR-Operator abgerufen werden. Ist der angegebene Wert beispielsweise gleich „6“, werden alle Ereignisse zurückgegeben, für die „HealthState“ den Wert für OK (2) oder Warnung (4) hat.  <br> – Default: Standardwert. Stimmt mit jedem Integritätsstatus (HealthState) überein. Der Wert ist gleich null.  <br> – None: Filter, der mit keinem Wert für „HealthState“ übereinstimmt. Wird verwendet, um keine Ergebnisse für eine angegebene Statussammlung zurückzugeben. Der Wert ist gleich „1“.  <br> – Ok: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Ok“ hat. Der Wert ist gleich „2“.  <br> – Warning: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Warning“ hat. Der Wert ist gleich „4“.  <br> – Error: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Error“ hat. Der Wert ist gleich „8“.  <br> – All: Filter, der mit Eingaben übereinstimmt, die einen beliebigen Wert für „HealthState“ haben. Der Wert ist gleich „65535“. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-replica-info"></a>sfctl replica info
Ruft die Informationen zu einem Replikat einer Service Fabric-Partition ab.

Die Antwort enthält die ID, die Rolle, den Status, die Integrität, den Knotennamen, die Betriebszeit und andere Details zu dem Replikat.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --partition-id [erforderlich] | Die Identität (ID) der Partition. |
| --replica-id [erforderlich] | Der Bezeichner (ID) des Replikats. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-replica-list"></a>sfctl replica list
Ruft die Informationen zu Replikaten einer Service Fabric-Dienstpartition ab.

Der GetReplicas-Endpunkt gibt Informationen zu den Replikaten der angegebenen Partition zurück. Die Antwort enthält die ID, die Rolle, den Status, die Integrität, den Knotennamen, die Betriebszeit und andere Details zu dem Replikat.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --partition-id [erforderlich] | Die Identität (ID) der Partition. |
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

## <a name="sfctl-replica-remove"></a>sfctl replica remove
Entfernt ein Dienstreplikat, das auf einem Knoten ausgeführt wird.

Diese API simuliert einen Service Fabric-Replikatfehler durch Entfernen eines Replikats aus einem Service Fabric-Cluster. Beim Entfernen wird das Replikat geschlossen und anschließend in die Rolle „None“ überführt, und dann werden alle Statusinformationen des Replikats aus dem Cluster entfernt. Diese API testet den Pfad für ein Entfernen des Replikatstatus und simuliert den dauerhaften Pfad für Berichten von Fehlern über Client-APIs. Warnung: Es werden keine Sicherheitsüberprüfungen ausgeführt, wenn diese API verwendet wird. Eine falsche Verwendung dieser API kann zum Verlust der Daten von zustandsbehafteten Diensten führen. Darüber hinaus wirkt sich das forceRemove-Flag auf alle anderen Repliken aus, die im selben Prozess gehostet werden.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --node-name [erforderlich] | Der Name des Knotens. |
| --partition-id [erforderlich] | Die Identität (ID) der Partition. |
| --replica-id [erforderlich] | Der Bezeichner (ID) des Replikats. |
| --force-remove | Erzwingt ein Entfernen einer Service Fabric-Anwendung oder eines Service Fabric-Diensts, ohne den Ablauf für das ordnungsgemäße Herunterfahren zu durchlaufen. Dieser Parameter kann für ein erzwungenes Löschen einer Anwendung oder eines Diensts verwendet werden, für die oder den beim Löschen ein Timeout aufgetreten ist, weil Probleme im Dienstcode ein ordnungsgemäßes Schließen von Replikaten verhindern. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-replica-report-health"></a>sfctl replica report-health
Sendet einen Integritätsbericht zu dem Service Fabric-Replikat.

Meldet den Integritätszustand des angegebenen Service Fabric-Replikats. Der Bericht muss die Informationen über die Quelle des Integritätsberichts und die Eigenschaft enthalten, auf die sich der Bericht bezieht. Der Bericht wird an ein Service Fabric-Gatewayreplikat gesendet, das diesen an den Integritätsspeicher weiterleitet. Der Bericht wird möglicherweise vom Gateway akzeptiert, aber nach zusätzlicher Überprüfung vom Integritätsspeicher abgelehnt. Beispielsweise kann der Integritätsspeicher den Bericht aufgrund eines ungültigen Parameters wie etwa einer veralteten Sequenznummer ablehnen. Um festzustellen, ob der Bericht im Integritätsspeicher angewendet wurde, führen Sie „get replica health“ aus, und überprüfen Sie, ob der Bericht im Abschnitt „HealthEvents“ angezeigt wird.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --health-property [erforderlich] | Die Eigenschaft der Integritätsinformationen. <br><br> Eine Entität kann Integritätsberichte für verschiedene Eigenschaften enthalten. Die Eigenschaft ist eine Zeichenfolge und keine feste Enumeration, um dem Reporter Flexibilität zu ermöglichen und entsprechend die Zustandsbedingung zu kategorisieren, die den Bericht auslöst. Beispielsweise kann ein Reporter mit der SourceId „LocalWatchdog“ den Zustand des verfügbaren Datenträgers auf einem Knoten überwachen und folglich die AvailableDisk-Eigenschaft auf diesem Knoten melden. Der gleiche Reporter kann die Knotenkonnektivität überwachen und folglich eine Connectivity-Eigenschaft auf dem gleichen Knoten melden. Im Integritätsspeicher werden diese Berichte als separate Integritätsereignisse für den angegebenen Knoten behandelt. Zusammen mit der SourceId identifiziert die Eigenschaft eindeutig die Integritätsinformationen. |
| --health-state [erforderlich] | Mögliche Werte sind\: „Invalid“, „Ok“, „Warning“, „Error“, „Unknown“. |
| --partition-id [erforderlich] | Die Identität (ID) der Partition. |
| --replica-id [erforderlich] | Die Identität (ID) der Partition. |
| --source-id [erforderlich] | Der Quellenname, der die Client-, Watchdog- oder Systemkomponente identifiziert, die die Integritätsinformationen generiert hat. |
| --description | Die Beschreibung der Integritätsinformationen. <br><br> Hierbei handelt es sich um Freitext, mit dem von Menschen lesbare Informationen über den Bericht hinzugefügt werden können. Die maximale Zeichenlänge für die Beschreibung beträgt 4096 Zeichen. Wenn die angegebene Zeichenfolge länger ist, wird sie automatisch abgeschnitten. Wird diese abgeschnitten, enthalten die letzten Zeichen der Beschreibung einen Marker „[abgeschnitten]“, und die gesamte Zeichenfolgenlänge beträgt 4096 Zeichen. Das Vorhandensein des Markers ist ein Hinweis für Benutzer, dass abgeschnittene Zeichenfolgen vorkommen. Beachten Sie, dass die Beschreibung bei abgeschnittenen Zeichenfolgen weniger als 4096 Zeichen von der ursprünglichen Zeichenfolge enthält. |
| --immediate | Ein Flag, das angibt, ob der Bericht sofort gesendet werden soll. <br><br> Ein Integritätsbericht wird an eine Service Fabric-Gatewayanwendung gesendet, die diesen an den Integritätsspeicher weiterleitet. Wenn „Immediate“ auf „true“ festgelegt ist, wird der Bericht sofort vom HTTP-Gateway an den Integritätsspeicher gesendet, unabhängig von den Fabric-Clienteinstellungen, die die HTTP-Gatewayanwendung verwendet. Dies ist für kritische Berichte hilfreich, die schnellstmöglich gesendet werden sollen. Abhängig von der zeitlichen Steuerung und anderen Bedingungen können beim Senden des Berichts trotzdem möglicherweise Fehler auftreten, z.B., wenn das HTTP-Gateway geschlossen ist oder die Meldung das Gateway nicht erreicht. Wenn „Immediate“ auf „false“ festgelegt ist, wird der Bericht basierend auf den Integritätsclienteinstellungen vom HTTP-Gateway gesendet. Aus diesem Grund wird der Bericht entsprechend der HealthReportSendInterval-Konfiguration batchweise verarbeitet. Dies ist die empfohlene Einstellung, da der Integritätsclient die Optimierung von Meldungen des Integritätsberichts für den Integritätsspeicher sowie die Verarbeitung von Integritätsberichten ermöglicht. Standardmäßig werden Berichte nicht sofort gesendet. |
| --remove-when-expired | Ein Wert, der angibt, ob der Bericht aus dem Integritätsspeicher entfernt wird, wenn er abläuft. <br><br> Wenn dieser Wert auf „true“ festgelegt ist, wird der Bericht aus dem Integritätsspeicher entfernt, nachdem er abgelaufen ist. Wenn dieser Wert auf „false“ festgelegt ist, wird der Bericht als Fehler behandelt, wenn er abgelaufen ist. Der Wert dieser Eigenschaft ist standardmäßig auf „false“ festgelegt. Wenn Clients in regelmäßigen Abständen Berichte erstellen, sollte RemoveWhenExpired auf „false“ (Standardwert) festgelegt sein. Denn falls der Reporter Probleme (z.B. Deadlocks) haben und keine Berichte erstellen können sollte, wird die Entität entsprechend auf Fehler ausgewertet, wenn der Integritätsbericht abläuft. Hierdurch wird die Entität nicht als in einem fehlerhaften Integritätszustand befindlich gekennzeichnet. |
| --sequence-number | Die Sequenznummer für diesen Integritätsbericht als numerische Zeichenfolge. <br><br> Die Sequenznummer des Berichts wird vom Integritätsspeicher zur Erkennung veralteter Berichte verwendet. Wenn diese nicht angegeben ist, wird beim Hinzufügen eines Berichts automatisch eine Sequenznummer vom Integritätsclient generiert. |
| --service-kind | Die Art des Dienstreplikats (zustandsbehaftet oder zustandslos), für die die Integrität gemeldet wird. Folgende Werte sind möglich\: „Stateless“, „Stateful“.  Standardwert\: Stateful. |
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

## <a name="sfctl-replica-restart"></a>sfctl replica restart
Startet ein Dienstreplikat eines permanenten Diensts neu, das auf einem Knoten ausgeführt wird.

Startet ein Dienstreplikat eines permanenten Diensts neu, das auf einem Knoten ausgeführt wird. Warnung: Es werden keine Sicherheitsüberprüfungen ausgeführt, wenn diese API verwendet wird. Eine falsche Verwendung dieser API kann zum Verlust der Verfügbarkeit von zustandsbehafteten Diensten führen.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --node-name [erforderlich] | Der Name des Knotens. |
| --partition-id [erforderlich] | Die Identität (ID) der Partition. |
| --replica-id [erforderlich] | Der Bezeichner (ID) des Replikats. |
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
