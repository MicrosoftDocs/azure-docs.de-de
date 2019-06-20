---
title: Azure Service Fabric CLI – sfctl node | Microsoft-Dokumentation
description: Beschreibt die sfctl node-Befehle der Service Fabric-Befehlszeilenschnittstelle (Command Line Interface, CLI).
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
ms.openlocfilehash: 1e5b5876fa6277d1bad0989c543de667f75a066c
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258729"
---
# <a name="sfctl-node"></a>sfctl node
Ermöglicht es, Knoten zu verwalten, die einen Cluster bilden.

## <a name="commands"></a>Befehle

|Get-Help|BESCHREIBUNG|
| --- | --- |
| disable | Deaktivieren Sie einen Service Fabric-Clusterknoten mit der angegebenen Deaktivierungsabsicht. |
| enable | Aktiviert einen Service Fabric-Clusterknoten, der momentan deaktiviert ist. |
| health | Ruft die Integrität eines Service Fabric-Knotens ab. |
| info | Ruft die Informationen zu einem bestimmten Knoten im Service Fabric-Cluster ab. |
| list | Ruft die Liste der Knoten im Service Fabric-Cluster ab. |
| load | Ruft die Ladeinformationen eines Service Fabric-Knotens ab. |
| remove-state | Benachrichtigt Service Fabric, dass der persistente Zustand auf einem Knoten dauerhaft entfernt wurde oder verloren gegangen ist. |
| report-health | Sendet einen Integritätsbericht zu dem Service Fabric-Knoten. |
| restart | Startet einen Service Fabric-Clusterknoten neu. |
| transition | Startet oder beendet einen Clusterknoten. |
| transition-status | Ruft den Status eines Vorgangs ab, der mit „StartNodeTransition“ gestartet wurde. |

## <a name="sfctl-node-disable"></a>sfctl node disable
Deaktivieren Sie einen Service Fabric-Clusterknoten mit der angegebenen Deaktivierungsabsicht.

Deaktivieren Sie einen Service Fabric-Clusterknoten mit der angegebenen Deaktivierungsabsicht. Sobald die Deaktivierung ausgeführt wird, kann die Deaktivierungsabsicht erhöht, aber nicht verringert werden (z. B. kann ein Knoten, der mit der „Pause“-Absicht deaktiviert ist, mit „Restart“ weiter deaktiviert werden, aber nicht umgekehrt). Knoten können über den Vorgang zum Aktivieren eines Knotens jederzeit erneut aktiviert werden, nachdem sie deaktiviert wurden. Ist die Deaktivierung nicht abgeschlossen, bricht dies die Deaktivierung ab. Ein Knoten, der ausgefallen und wieder in Betrieb gegangen ist, während er deaktiviert war, muss weiterhin erneut aktiviert werden, bevor Dienste auf ihm platziert werden.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --node-name [erforderlich] | Der Name des Knotens. |
| --deactivation-intent | Beschreibt die Absicht oder den Grund für die Deaktivierung des Knotens. Die folgenden Werte sind möglich. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-node-enable"></a>sfctl node enable
Aktiviert einen Service Fabric-Clusterknoten, der momentan deaktiviert ist.

Aktiviert einen Service Fabric-Clusterknoten, der momentan deaktiviert ist. Sobald der Knoten aktiviert ist, wird er wieder ein geeignetes Ziel zum Platzieren neuer Replikate, und alle deaktivierten Replikate, die auf dem Knoten verblieben sind, werden erneut aktiviert.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --node-name [erforderlich] | Der Name des Knotens. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-node-health"></a>sfctl node health
Ruft die Integrität eines Service Fabric-Knotens ab.

Ruft die Integrität eines Service Fabric-Knotens ab. Verwenden Sie „EventsHealthStateFilter“, um die Sammlung der Integritätsereignisse (HealthEvents), die für den Knoten gemeldet wurden, anhand des Integritätsstatus zu filtern. Ist der Knoten, dessen Name Sie angegeben haben, im Integritätsspeicher nicht vorhanden, wird ein Fehler zurückgegeben.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --node-name [erforderlich] | Der Name des Knotens. |
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

## <a name="sfctl-node-info"></a>sfctl node info
Ruft die Informationen zu einem bestimmten Knoten im Service Fabric-Cluster ab.

Die Antwort enthält den Namen, den Status, die ID, die Integrität, die Betriebszeit und weitere Details über den Knoten.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --node-name [erforderlich] | Der Name des Knotens. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-node-list"></a>sfctl node list
Ruft die Liste der Knoten im Service Fabric-Cluster ab.

Die Antwort enthält den Namen, den Status, die ID, die Integrität, die Betriebszeit und weitere Details über die Knoten.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --continuation-token | Der Parameter „continuation-token“ (Fortsetzungstoken) wird dazu verwendet, den nächsten Satz von Ergebnissen abzurufen. Ein Fortsetzungstoken mit einem nicht leeren Wert wird in die Antwort der API eingefügt, wenn die Ergebnisse aus dem System nicht in eine einzige Antwort passen. Wird dieser Wert an den nächsten API-Aufruf übergeben, gibt die API den nächsten Satz von Ergebnissen zurück. Gibt es keine weiteren Ergebnisse, enthält das Fortsetzungstoken keinen Wert. Der Wert dieses Parameters darf nicht als URL codiert sein. |
| --max-results | Die maximale Anzahl von Ergebnissen, die als Teil der seitenweisen Abfragen zurückgegeben werden sollen. Dieser Parameter definiert die obere Grenze für die Anzahl von zurückgegebenen Ergebnissen. Es können weniger Ergebnisse zurückgegeben werden, als dieser maximalen Anzahl entspricht. Dies ist der Fall, wenn die Ergebnisse wegen der Größenbeschränkungen, die für Meldungen in der Konfiguration definiert sind, nicht in die jeweilige Meldung passen. Ist dieser Parameter gleich null oder nicht angegeben, enthält die seitenweise Abfrage so viele Ergebnisse, wie in die Rückgabemeldung passen. |
| --node-status-filter | Ermöglicht ein Filtern des Knotens anhand des Knotenstatus (NodeStatus). Es werden nur die Knoten zurückgegeben, die dem angegebenen Filterwert entsprechen. Der Filterwert kann einer der folgenden Werte sein.  Standard\: default. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-node-load"></a>sfctl node load
Ruft die Lastinformationen eines Service Fabric-Knotens ab.

Ruft die Informationen zum Laden eines Service Fabric-Knotens für alle Metriken ab, für die Last oder Kapazität definiert wurden.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --node-name [erforderlich] | Der Name des Knotens. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-node-remove-state"></a>sfctl node remove-state
Benachrichtigt Service Fabric, dass der persistente Zustand auf einem Knoten dauerhaft entfernt wurde oder verloren gegangen ist.

Dies bedeutet, dass es nicht möglich, den persistenten Zustand dieses Knotens wiederherzustellen. Dies geschieht in der Regel, wenn eine Festplatte zurückgesetzt wurde oder eine Festplatte abstürzt. Der Knoten muss ausgeschaltet sein, damit dieser Vorgang erfolgreich durchgeführt wird. Durch diesen Vorgang wird Service Fabric darauf hingewiesen, dass die Replikate auf diesem Knoten nicht mehr vorhanden sind, und dass Service Fabric nicht auf die Wiederherstellung dieser Replikate warten soll. Führen Sie dieses Cmdlet nicht aus, wenn der Zustand auf dem Knoten nicht entfernt wurde und der Knoten nicht wieder mit korrektem Zustand wiederhergestellt werden kann.

Damit Sie dieses Cmdlet für Startknoten verwenden können, ändern Sie ab Service Fabric 6.5 die Startknoten in normale (Nicht-Start-) Knoten, und rufen Sie dann dieses Cmdlet auf, um den Knotenzustand zu entfernen. Wenn der Cluster unter Azure ausgeführt wird, versucht Service Fabric, nachdem der Startknoten heruntergefahren ist, ihn automatisch in einen Nicht-Startknoten zu ändern. Stellen Sie dazu sicher, dass die Anzahl der Nicht-Startknoten im primären Knotentyp nicht kleiner ist als die Anzahl der Down-Startknoten. Fügen Sie zu diesem Zweck bei Bedarf weitere Knoten zum primären Knotentyp hinzu. Wenn für einen eigenständigen Cluster nicht erwartet wird, dass der Down-Startknoten mit intaktem Zustand zurückkehrt, entfernen Sie den Knoten aus dem Cluster. Weitere Informationen finden Sie unter [Entfernen von Knoten aus einem eigenständigen Service Fabric-Cluster](/azure/service-fabric/service-fabric-cluster-windows-server-add-remove-nodes). 

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --node-name [erforderlich] | Der Name des Knotens. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-node-report-health"></a>sfctl node report-health
Sendet einen Integritätsbericht zu dem Service Fabric-Knoten.

Meldet den Integritätszustand des angegebenen Service Fabric-Knotens. Der Bericht muss die Informationen über die Quelle des Integritätsberichts und die Eigenschaft enthalten, auf die sich der Bericht bezieht. Der Bericht wird an einen Service Fabric-Gatewayknoten gesendet, der diesen an den Integritätsspeicher weiterleitet. Der Bericht wird möglicherweise vom Gateway akzeptiert, aber nach zusätzlicher Überprüfung vom Integritätsspeicher abgelehnt. Beispielsweise kann der Integritätsspeicher den Bericht aufgrund eines ungültigen Parameters wie etwa einer veralteten Sequenznummer ablehnen. Um festzustellen, ob der Bericht im Integritätsspeicher angewendet wurde, überprüfen Sie, ob der Bericht im Abschnitt „HealthEvents“ angezeigt wird.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --health-property [erforderlich] | Die Eigenschaft der Integritätsinformationen. <br><br> Eine Entität kann Integritätsberichte für verschiedene Eigenschaften enthalten. Die Eigenschaft ist eine Zeichenfolge und keine feste Enumeration, um dem Reporter Flexibilität zu ermöglichen und entsprechend die Zustandsbedingung zu kategorisieren, die den Bericht auslöst. Beispielsweise kann ein Reporter mit der SourceId „LocalWatchdog“ den Zustand des verfügbaren Datenträgers auf einem Knoten überwachen und folglich die AvailableDisk-Eigenschaft auf diesem Knoten melden. Der gleiche Reporter kann die Knotenkonnektivität überwachen und folglich eine Connectivity-Eigenschaft auf dem gleichen Knoten melden. Im Integritätsspeicher werden diese Berichte als separate Integritätsereignisse für den angegebenen Knoten behandelt. Zusammen mit der SourceId identifiziert die Eigenschaft eindeutig die Integritätsinformationen. |
| --health-state [erforderlich] | Mögliche Werte sind\: „Invalid“, „Ok“, „Warning“, „Error“, „Unknown“. |
| --node-name [erforderlich] | Der Name des Knotens. |
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

## <a name="sfctl-node-restart"></a>sfctl node restart
Startet einen Service Fabric-Clusterknoten neu.

Startet einen Service Fabric-Clusterknoten neu, der bereits gestartet ist.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --node-name [erforderlich] | Der Name des Knotens. |
| --create-fabric-dump | Geben Sie „True“ an, um ein Speicherabbild des Fabric-Knotenprozesses zu erstellen. Hierbei muss die Groß-/Kleinschreibung beachtet werden.  Standardwert\: „false“. |
| --node-instance-id | Die Instanz-ID des Zielknotens. Ist die Instanz-ID angegeben, wird der Knoten nur dann neu gestartet, wenn die ID mit der aktuellen Instanz des Knotens übereinstimmt. Der Standardwert „0“ stimmt mit jeder Instanz-ID überein. Die Instanz-ID kann mithilfe einer Abfrage zum Abrufen des Knotens (getNode) abgerufen werden.  Standardwert\: „0“. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-node-transition"></a>sfctl node transition
Startet oder beendet einen Clusterknoten.

Startet oder beendet einen Clusterknoten.  Ein Clusterknoten ist ein Prozess, nicht die Betriebssysteminstanz selbst.  Um einen Knoten zu starten, übergeben Sie „Start“ an den Parameter „NodeTransitionType“. Um einen Knoten zu beenden, übergeben Sie „Stop“ an den Parameter „NodeTransitionType“. Diese API startet den Vorgang. Wenn die API zurückkehrt, ist der Übergang des Knotens möglicherweise noch nicht abgeschlossen. Rufen Sie „GetNodeTransitionProgress“ mit derselben „OperationId“ auf, um den Fortschritt des Vorgangs abzurufen.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --node-instance-id [erforderlich] | Die Knoteninstanz-ID des Zielknotens. Diese ID kann über die GetNodeInfo-API bestimmt werden. |
| --node-name [erforderlich] | Der Name des Knotens. |
| --node-transition-type [erforderlich] | Gibt den Typ des auszuführenden Übergangs an.  „NodeTransitionType.Start“ startet einen angehaltenen Knoten. „NodeTransitionType.Stop“ beendet einen Knoten, der betriebsbereit ist. |
| --operation-id [erforderlich] | Eine GUID, die einen Aufruf dieser API kennzeichnet.  Diese GUID wird in die entsprechende GetProgress-API übergeben. |
| --stop-duration-in-seconds [erforderlich] | Die Dauer in Sekunden, die der Knoten angehalten bleiben soll.  Der Mindestwert ist 600, der Höchstwert 14400.  Nach Ablauf dieser Zeit ist der Knoten automatisch wieder betriebsbereit. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-node-transition-status"></a>sfctl node transition-status
Ruft den Status eines Vorgangs ab, der mit „StartNodeTransition“ gestartet wurde.

Ruft den Status eines Vorgangs ab, der mit StartNodeTransition mithilfe der bereitgestellten OperationId gestartet wurde.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --node-name [erforderlich] | Der Name des Knotens. |
| --operation-id [erforderlich] | Eine GUID, die einen Aufruf dieser API kennzeichnet.  Diese GUID wird in die entsprechende GetProgress-API übergeben. |
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