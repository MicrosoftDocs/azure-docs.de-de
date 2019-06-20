---
title: Azure Service Fabric CLI – sfctl application | Microsoft-Dokumentation
description: Beschreibt die sfctl application-Befehle der Service Fabric-Befehlszeilenschnittstelle (Command Line Interface, CLI).
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
ms.openlocfilehash: d4fec5d8131d269d3df229360066452c37a92430
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837486"
---
# <a name="sfctl-application"></a>sfctl application
Ermöglicht es, Anwendungen und Anwendungstypen zu erstellen, zu löschen und zu verwalten.

## <a name="commands"></a>Befehle

|Get-Help|BESCHREIBUNG|
| --- | --- |
| create | Erstellt eine Service Fabric-Anwendung mithilfe der angegebenen Beschreibung. |
| delete | Löscht eine vorhandene Service Fabric-Anwendung. |
| deployed | Ruft die Informationen zu einer Anwendung ab, die auf einem Service Fabric-Knoten bereitgestellt wird. |
| deployed-health | Ruft die Informationen über die Integrität einer Anwendung ab, die auf einem Service Fabric-Knoten bereitgestellt ist. |
| deployed-list | Ruft die Liste der Anwendungen ab, die auf einem Service Fabric-Knoten bereitgestellt werden. |
| health | Ruft die Integrität der Service Fabric-Anwendung ab. |
| info | Ruft Informationen zu einer Service Fabric-Anwendung ab. |
| list | Ruft die Liste der Anwendungen ab, die in dem Service Fabric-Cluster erstellt wurden und mit den angegebenen Filtern übereinstimmen. |
| load | Ruft Lastinformationen zu einer Service Fabric-Anwendung ab. |
| manifest | Ruft das Manifest ab, das einen Anwendungstyp beschreibt. |
| provision | Stellt einen Service Fabric-Anwendungstyp mithilfe des SFPKG-Pakets im externen Speicher oder mithilfe des Anwendungspakets im Imagespeicher im Cluster bereit oder registriert ihn. |
| report-health | Sendet einen Integritätsbericht zu der Service Fabric-Anwendung. |
| type | Ruft die Liste der Anwendungstypen im Service Fabric-Cluster ab, die genau mit dem angegebenen Namen übereinstimmen. |
| type-list | Ruft die Liste der Anwendungstypen im Service Fabric-Cluster ab. |
| unprovision | Entfernt einen Service Fabric-Anwendungstyp aus dem Cluster oder hebt die Registrierung eines Service Fabric-Anwendungstyps für den Cluster auf. |
| upgrade | Startet ein Aktualisieren einer Anwendung im Service Fabric-Cluster. |
| upgrade-resume | Setzt ein Aktualisieren einer Anwendung im Service Fabric-Cluster fort. |
| upgrade-rollback | Führt ein Rollback des derzeit laufenden Upgrades einer Anwendung im Service Fabric-Cluster aus. |
| upgrade-status | Ruft Details über das neueste Upgrade ab, das für diese Anwendung ausgeführt wurde. |
| upload | Kopiert ein Service Fabric-Anwendungspaket in den Imagespeicher. |

## <a name="sfctl-application-create"></a>sfctl application create
Erstellt eine Service Fabric-Anwendung mithilfe der angegebenen Beschreibung.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --app-name [erforderlich] | Der Name der Anwendung, einschließlich des URI-Schemas „fabric\:“. |
| -- app-type [erforderlich] | Der Anwendungstypname, der im Anwendungsmanifest gefunden wurde. |
| -- app-version [erforderlich] | Die Version des Anwendungstyps, wie sie im Anwendungsmanifest definiert ist. |
| --max-node-count | Die maximale Anzahl von Knoten, für die Service Fabric Kapazität für diese Anwendung reserviert. Hinweis: Dies bedeutet nicht, dass die Dienste dieser Anwendung auf allen dieser Knoten platziert werden. |
| --metrics | Eine JSON-codierte Liste der Kapazitätsmetrikbeschreibungen einer Anwendung. Eine Metrik ist als Name definiert, der einem Satz von Kapazitäten für jeden Knoten zugeordnet ist, auf dem die Anwendung vorhanden ist. |
| --min-node-count | Die minimale Anzahl von Knoten, für die Service Fabric Kapazität für diese Anwendung reserviert wird. Hinweis: Dies bedeutet nicht, dass die Dienste dieser Anwendung auf allen dieser Knoten platziert werden. |
| --parameters | Eine JSON-codierte Liste mit Anwendungsparameterwerten, die beim Erstellen der Anwendung angewendet werden sollen. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-application-delete"></a>sfctl application delete
Löscht eine vorhandene Service Fabric-Anwendung.

Eine Anwendung muss erstellt worden sein, bevor sie gelöscht werden kann. Wird eine Anwendung gelöscht, werden alle Dienste gelöscht, die Bestandteil der Anwendung sind. Service Fabric versucht standardmäßig, Dienstreplikate auf ordnungsgemäße Weise zu schließen und den Dienst dann zu löschen. Wenn es aber Probleme mit dem ordnungsgemäßen Schließen eines Replikats gibt, kann der Löschvorgang sehr lange dauern oder abstürzen. Verwenden Sie das optionale Flag „ForceRemove“, um die ordnungsgemäße Schließsequenz zu überspringen und das Löschen der Anwendung samt aller ihrer Dienste zu erzwingen.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich] | Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
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

## <a name="sfctl-application-deployed"></a>sfctl application deployed
Ruft die Informationen zu einer Anwendung ab, die auf einem Service Fabric-Knoten bereitgestellt wird.

Diese Abfrage gibt Systemanwendungsinformationen zurück, wenn die angegebene Anwendungs-ID für eine Systemanwendung steht. Die Ergebnisse umfassen bereitgestellte Anwendungen mit den Status „Aktiv“, „Aktiviert“ und „Herunterladen“. Für diese Abfrage muss der Knotenname einem Knoten im Cluster entsprechen. Die Abfrage führt zu einem Fehler, wenn der angegebene Knotenname auf keinen aktiven Service Fabric-Knoten im Cluster verweist.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich] | Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
| --node-name [erforderlich] | Der Name des Knotens. |
| --include-health-state | Schließen Sie den Integritätszustand einer Entität ein. Wenn dieser Parameter auf „false“ festgelegt oder nicht angegeben ist, wird der Integritätszustand „Unknown“ zurückgegeben. Bei „true“ wird die Abfrage parallel zum Knoten- und Integritätssystemdienst, bevor die Ergebnisse zusammengeführt werden. Daher ist die Abfrage teurer und kann längere Zeit in Anspruch nehmen. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-application-deployed-health"></a>sfctl application deployed-health
Ruft die Informationen über die Integrität einer Anwendung ab, die auf einem Service Fabric-Knoten bereitgestellt ist.

Ruft die Informationen über die Integrität einer Anwendung ab, die auf einem Service Fabric-Knoten bereitgestellt ist. Verwenden Sie EventsHealthStateFilter, um optional basierend auf dem Integritätszustand nach der Sammlung von HealthEvent-Objekten zu filtern, die für die bereitgestellte Anwendung gemeldet wurden. Verwenden Sie DeployedServicePackagesHealthStateFilter, um basierend auf dem Integritätszustand optional untergeordnete DeployedServicePackageHealth-Elemente zu filtern.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich] | Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
| --node-name [erforderlich] | Der Name des Knotens. |
| --deployed-service-packages-health-state-filter | Ermöglicht es, die im Ergebnis der bereitgestellten Anwendungsintegritätsabfrage zurückgegebenen Integritätszustandsobjekte der bereitgestellten Dienstpakete anhand des Integritätszustand zu filtern. Die möglichen Werte für diesen Parameter entsprechen dem jeweiligen ganzzahligen Wert von einem der folgenden Integritätsstatus. Es werden nur Dienstpakete bereitgestellt, die dem Filter entsprechen. Alle bereitgestellten Dienstpakete werden verwendet, um den aggregierten Integritätszustand der bereitgestellten Anwendung auszuwerten. Ist kein Filter angegeben, werden alle Einträge zurückgegeben. Die Statuswerte sind eine flagbasierte Enumeration, sodass der Wert eine Kombination der Werte sein kann, die mit dem bitweisen OR-Operator abgerufen werden. Ist der angegebene Wert beispielsweise 6, wird der Integritätszustand von Dienstpaketen zurückgegeben, für die „HealthState“ den Wert „OK (2)“ oder „Warning (4)“ aufweist.  <br> – Default: Standardwert. Stimmt mit jedem Integritätsstatus (HealthState) überein. Der Wert ist gleich null.  <br> – None: Filter, der mit keinem Wert für „HealthState“ übereinstimmt. Wird verwendet, um keine Ergebnisse für eine angegebene Statussammlung zurückzugeben. Der Wert ist gleich „1“.  <br> – Ok: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Ok“ hat. Der Wert ist gleich „2“.  <br> – Warning: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Warning“ hat. Der Wert ist gleich „4“.  <br> – Error: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Error“ hat. Der Wert ist gleich „8“.  <br> – All: Filter, der mit Eingaben übereinstimmt, die einen beliebigen Wert für „HealthState“ haben. Der Wert ist gleich „65535“. |
| --events-health-state-filter | Ermöglicht das Filtern der Collection zurückgegebener HealthEvent-Objekte anhand des Integritätsstatus. Die möglichen Werte für diesen Parameter entsprechen dem jeweiligen ganzzahligen Wert von einem der folgenden Integritätsstatus. Es werden nur Ereignisse zurückgegeben, die dem Filter entsprechen. Alle Ereignisse werden verwendet, um den aggregierten Integritätsstatus auszuwerten. Ist kein Filter angegeben, werden alle Einträge zurückgegeben. Ein Statuswert ist eine flagbasierte Enumeration, sodass der Wert eine Kombination der Werte sein kann, die mit dem bitweisen OR-Operator abgerufen werden. Ist der angegebene Wert beispielsweise gleich „6“, werden alle Ereignisse zurückgegeben, für die „HealthState“ den Wert für OK (2) oder Warnung (4) hat.  <br> – Default: Standardwert. Stimmt mit jedem Integritätsstatus (HealthState) überein. Der Wert ist gleich null.  <br> – None: Filter, der mit keinem Wert für „HealthState“ übereinstimmt. Wird verwendet, um keine Ergebnisse für eine angegebene Statussammlung zurückzugeben. Der Wert ist gleich „1“.  <br> – Ok: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Ok“ hat. Der Wert ist gleich „2“.  <br> – Warning: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Warning“ hat. Der Wert ist gleich „4“.  <br> – Error: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Error“ hat. Der Wert ist gleich „8“.  <br> – All: Filter, der mit Eingaben übereinstimmt, die einen beliebigen Wert für „HealthState“ haben. Der Wert ist gleich „65535“. |
| --exclude-health-statistics | Gibt an, ob die Integritätsstatistiken als Bestandteil des Abfrageergebnisses zurückgegeben werden sollen. Der Standardwert ist gleich „False“. Die Statistiken zeigen die Anzahl von untergeordneten Entitäten, die einen der Integritätszustände „Ok“, „Warning“ oder „Error“ aufweisen. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-application-deployed-list"></a>sfctl application deployed-list
Ruft die Liste der Anwendungen ab, die auf einem Service Fabric-Knoten bereitgestellt werden.

Ruft die Liste der Anwendungen ab, die auf einem Service Fabric-Knoten bereitgestellt werden. Die Ergebnisse enthalten keine Informationen zu bereitgestellten Systemanwendungen, es sei denn, dies wurde explizit durch die ID abgefragt Die Ergebnisse umfassen bereitgestellte Anwendungen mit den Status „Aktiv“, „Aktiviert“ und „Herunterladen“. Für diese Abfrage muss der Knotenname einem Knoten im Cluster entsprechen. Die Abfrage führt zu einem Fehler, wenn der angegebene Knotenname auf keinen aktiven Service Fabric-Knoten im Cluster verweist.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --node-name [erforderlich] | Der Name des Knotens. |
| --continuation-token | Der Parameter „continuation-token“ (Fortsetzungstoken) wird dazu verwendet, den nächsten Satz von Ergebnissen abzurufen. Ein Fortsetzungstoken mit einem nicht leeren Wert wird in die Antwort der API eingefügt, wenn die Ergebnisse aus dem System nicht in eine einzige Antwort passen. Wird dieser Wert an den nächsten API-Aufruf übergeben, gibt die API den nächsten Satz von Ergebnissen zurück. Gibt es keine weiteren Ergebnisse, enthält das Fortsetzungstoken keinen Wert. Der Wert dieses Parameters darf nicht als URL codiert sein. |
| --include-health-state | Schließen Sie den Integritätszustand einer Entität ein. Wenn dieser Parameter auf „false“ festgelegt oder nicht angegeben ist, wird der Integritätszustand „Unknown“ zurückgegeben. Bei „true“ wird die Abfrage parallel zum Knoten- und Integritätssystemdienst, bevor die Ergebnisse zusammengeführt werden. Daher ist die Abfrage teurer und kann längere Zeit in Anspruch nehmen. |
| --max-results | Die maximale Anzahl von Ergebnissen, die als Teil der seitenweisen Abfragen zurückgegeben werden sollen. Dieser Parameter definiert die obere Grenze für die Anzahl von zurückgegebenen Ergebnissen. Es können weniger Ergebnisse zurückgegeben werden, als dieser maximalen Anzahl entspricht. Dies ist der Fall, wenn die Ergebnisse wegen der Größenbeschränkungen, die für Meldungen in der Konfiguration definiert sind, nicht in die jeweilige Meldung passen. Ist dieser Parameter gleich null oder nicht angegeben, enthält die seitenweise Abfrage so viele Ergebnisse, wie in die Rückgabemeldung passen. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-application-health"></a>sfctl application health
Ruft die Integrität der Service Fabric-Anwendung ab.

Gibt den Integritätsstatus der Service Fabric-Anwendung zurück. In der Antwort wird der Integritätsstatus als „Ok“, „Error“ oder „Warning“ gemeldet. Wird die Entität nicht im Integritätsspeicher gefunden, wird ein Fehler zurückgegeben.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich] | Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
| --deployed-applications-health-state-filter | Ermöglicht es, die im Ergebnis der Anwendungsintegritätsabfrage zurückgegebenen Integritätsstatusobjekte der bereitgestellten Anwendungen anhand des Integritätsstatus zu filtern. Die möglichen Werte für diesen Parameter entsprechen dem jeweiligen ganzzahligen Wert von einem der folgenden Integritätsstatus. Es werden nur bereitgestellte Anwendungen zurückgegeben, die dem Filter entsprechen. Alle bereitgestellten Anwendungen werden verwendet, um den aggregierten Integritätsstatus auszuwerten. Ist kein Filter angegeben, werden alle Einträge zurückgegeben. Ein Statuswert ist eine flagbasierte Enumeration, sodass der Wert eine Kombination der Werte sein kann, die mit dem bitweisen OR-Operator abgerufen werden. Ist der angegebene Wert beispielsweise gleich „6“, wird der Integritätsstatus der bereitgestellten Anwendungen zurückgegeben, für die „HealthState“ den Wert für OK (2) oder Warning (4) hat.  <br> – Default: Standardwert. Stimmt mit jedem Integritätsstatus (HealthState) überein. Der Wert ist gleich null.  <br> – None: Filter, der mit keinem Wert für „HealthState“ übereinstimmt. Wird verwendet, um keine Ergebnisse für eine angegebene Statussammlung zurückzugeben. Der Wert ist gleich „1“.  <br> – Ok: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Ok“ hat. Der Wert ist gleich „2“.  <br> – Warning: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Warning“ hat. Der Wert ist gleich „4“.  <br> – Error: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Error“ hat. Der Wert ist gleich „8“.  <br> – All: Filter, der mit Eingaben übereinstimmt, die einen beliebigen Wert für „HealthState“ haben. Der Wert ist gleich „65535“. |
| --events-health-state-filter | Ermöglicht das Filtern der Collection zurückgegebener HealthEvent-Objekte anhand des Integritätsstatus. Die möglichen Werte für diesen Parameter entsprechen dem jeweiligen ganzzahligen Wert von einem der folgenden Integritätsstatus. Es werden nur Ereignisse zurückgegeben, die dem Filter entsprechen. Alle Ereignisse werden verwendet, um den aggregierten Integritätsstatus auszuwerten. Ist kein Filter angegeben, werden alle Einträge zurückgegeben. Ein Statuswert ist eine flagbasierte Enumeration, sodass der Wert eine Kombination der Werte sein kann, die mit dem bitweisen OR-Operator abgerufen werden. Ist der angegebene Wert beispielsweise gleich „6“, werden alle Ereignisse zurückgegeben, für die „HealthState“ den Wert für OK (2) oder Warnung (4) hat.  <br> – Default: Standardwert. Stimmt mit jedem Integritätsstatus (HealthState) überein. Der Wert ist gleich null.  <br> – None: Filter, der mit keinem Wert für „HealthState“ übereinstimmt. Wird verwendet, um keine Ergebnisse für eine angegebene Statussammlung zurückzugeben. Der Wert ist gleich „1“.  <br> – Ok: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Ok“ hat. Der Wert ist gleich „2“.  <br> – Warning: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Warning“ hat. Der Wert ist gleich „4“.  <br> – Error: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Error“ hat. Der Wert ist gleich „8“.  <br> – All: Filter, der mit Eingaben übereinstimmt, die einen beliebigen Wert für „HealthState“ haben. Der Wert ist gleich „65535“. |
| --exclude-health-statistics | Gibt an, ob die Integritätsstatistiken als Bestandteil des Abfrageergebnisses zurückgegeben werden sollen. Der Standardwert ist gleich „False“. Die Statistiken zeigen die Anzahl von untergeordneten Entitäten, die einen der Integritätszustände „Ok“, „Warning“ oder „Error“ aufweisen. |
| --services-health-state-filter | Ermöglicht es, die Integritätsstatusobjekte der Dienste, die im Ergebnis einer Dienstintegritätsabfrage zurückgegeben werden, anhand des Integritätsstatus zu filtern. Die möglichen Werte für diesen Parameter entsprechen dem jeweiligen ganzzahligen Wert von einem der folgenden Integritätsstatus. Es werden nur Dienste zurückgegeben, die dem Filter entsprechen. Alle Dienste werden verwendet, um den aggregierten Integritätsstatus auszuwerten. Ist kein Filter angegeben, werden alle Einträge zurückgegeben. Ein Statuswert ist eine flagbasierte Enumeration, sodass der Wert eine Kombination der Werte sein kann, die mit dem bitweisen OR-Operator abgerufen werden. Ist der angegebene Wert beispielsweise „6“, wird der Integritätsstatus der Dienste zurückgegeben, für die „HealthState“ den Wert für OK (2) oder Warning (4) hat.  <br> – Default: Standardwert. Stimmt mit jedem Integritätsstatus (HealthState) überein. Der Wert ist gleich null.  <br> – None: Filter, der mit keinem Wert für „HealthState“ übereinstimmt. Wird verwendet, um keine Ergebnisse für eine angegebene Statussammlung zurückzugeben. Der Wert ist gleich „1“.  <br> – Ok: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Ok“ hat. Der Wert ist gleich „2“.  <br> – Warning: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Warning“ hat. Der Wert ist gleich „4“.  <br> – Error: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Error“ hat. Der Wert ist gleich „8“.  <br> – All: Filter, der mit Eingaben übereinstimmt, die einen beliebigen Wert für „HealthState“ haben. Der Wert ist gleich „65535“. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-application-info"></a>sfctl application info
Ruft Informationen zu einer Service Fabric-Anwendung ab.

Gibt die Informationen zu der Anwendung zurück, die im Service Fabric-Cluster erstellt wurde oder gerade erstellt wird und deren Name mit dem übereinstimmt, der als Parameter angegebenen wurde. Die Antwort enthält den Namen, den Typ, den Status, die Parameter und weitere Details über die Anwendung.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich] | Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
| --exclude-application-parameters | Das Flag, das angibt, ob die Anwendungsparameter aus dem Ergebnis ausgeschlossen werden sollen. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-application-list"></a>sfctl application list
Ruft die Liste der Anwendungen ab, die in dem Service Fabric-Cluster erstellt wurden und mit den angegebenen Filtern übereinstimmen.

Ruft die Informationen zu den Anwendungen ab, die im Service Fabric-Cluster erstellt wurden oder gerade erstellt werden und mit den angegebenen Filtern übereinstimmen. Die Antwort enthält den Namen, den Typ, den Status, die Parameter und weitere Details über die Anwendung. Wenn die Anwendungen nicht auf eine Seite passen, werden sowohl eine Seite mit Ergebnissen als auch ein Fortsetzungstoken zurückgegeben, über das zur nächsten Seite gewechselt werden kann. Die Filter ApplicationTypeName und ApplicationDefinitionKindFilter können nicht gleichzeitig angegeben werden.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-definition-kind-filter | Wird zum Filtern nach ApplicationDefinitionKind verwendet, um eine Service Fabric-Anwendung zu definieren.  <br> - Standard: Standardwert, der die gleiche Funktion hat wie die Auswahl von „All“. Der Wert ist „0“.  <br> – All: Filter, der mit Eingaben eines beliebigen Werts „ApplicationDefinitionKind“ übereinstimmt. Der Wert ist gleich „65535“.  <br> – ServiceFabricApplicationDescription: Filter, der mit Eingaben übereinstimmt, für die „ApplicationDefinitionKind“ den Wert „ServiceFabricApplicationDescription“ hat. Der Wert ist gleich „1“.  <br> – Compose: Filter, der mit Eingaben übereinstimmt, für die „ApplicationDefinitionKind“ den Wert „Compose“ hat. Der Wert ist gleich „2“. |
| --application-type-name | Der Anwendungstypname, der zum Filtern der Anwendungen verwendet wird, die abgefragt werden sollen. Dieser Wert darf nicht die Version des Anwendungstyps enthalten. |
| --continuation-token | Der Parameter „continuation-token“ (Fortsetzungstoken) wird dazu verwendet, den nächsten Satz von Ergebnissen abzurufen. Ein Fortsetzungstoken mit einem nicht leeren Wert wird in die Antwort der API eingefügt, wenn die Ergebnisse aus dem System nicht in eine einzige Antwort passen. Wird dieser Wert an den nächsten API-Aufruf übergeben, gibt die API den nächsten Satz von Ergebnissen zurück. Gibt es keine weiteren Ergebnisse, enthält das Fortsetzungstoken keinen Wert. Der Wert dieses Parameters darf nicht als URL codiert sein. |
| --exclude-application-parameters | Das Flag, das angibt, ob die Anwendungsparameter aus dem Ergebnis ausgeschlossen werden sollen. |
| --max-results | Die maximale Anzahl von Ergebnissen, die als Teil der seitenweisen Abfragen zurückgegeben werden sollen. Dieser Parameter definiert die obere Grenze für die Anzahl von zurückgegebenen Ergebnissen. Es können weniger Ergebnisse zurückgegeben werden, als dieser maximalen Anzahl entspricht. Dies ist der Fall, wenn die Ergebnisse wegen der Größenbeschränkungen, die für Meldungen in der Konfiguration definiert sind, nicht in die jeweilige Meldung passen. Ist dieser Parameter gleich null oder nicht angegeben, enthält die seitenweise Abfrage so viele Ergebnisse, wie in die Rückgabemeldung passen. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-application-load"></a>sfctl application load
Ruft Lastinformationen zu einer Service Fabric-Anwendung ab.

Gibt die Lastinformationen der Anwendung zurück, die im Service Fabric-Cluster erstellt wurde oder gerade erstellt wird und deren Name mit dem Namen übereinstimmt, der als Parameter angegebenen wurde. Die Antwort enthält den Namen, die minimale Anzahl der Knoten, die maximale Anzahl der Knoten, die Anzahl der Knoten, die die Anwendung aktuell belegt sowie die Metrikinformation der Anwendungslast der Anwendung.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich] | Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-application-manifest"></a>sfctl application manifest
Ruft das Manifest ab, das einen Anwendungstyp beschreibt.

Die Antwort enthält den XML-Code des Anwendungsmanifests als Zeichenfolge.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-type-name [erforderlich] | Der Name des Anwendungstyps. |
| --application-type-version [erforderlich] | Die Version des Anwendungstyps. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-application-provision"></a>sfctl application provision
Stellt einen Service Fabric-Anwendungstyp mithilfe des SFPKG-Pakets im externen Speicher oder mithilfe des Anwendungspakets im Imagespeicher im Cluster bereit oder registriert ihn.

Stellt einen Service Fabric-Anwendungstyp im Cluster bereit. Dies ist erforderlich, bevor neue Anwendungen instanziiert werden können. Der Bereitstellungsvorgang kann mit dem Anwendungspaket, das durch relativePathInImageStore angegeben wird, oder mithilfe des URI des externen SFPKG-Pakets ausgeführt werden. Sofern nicht „--external-provision“ festgelegt wurde, erwartet dieser Befehl die Bereitstellung eines Imagespeichers.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-package-download-uri | Der Pfad zum SFPKG-Anwendungspaket, unter dem das Anwendungspaket über das HTTP- oder HTTPS-Protokoll heruntergeladen werden kann. <br><br> Gilt nur für die Bereitstellung aus einem externen Speicher. Das Anwendungspaket kann in einem externen Speicher gespeichert werden, der den GET-Vorgang zum Herunterladen der Datei bereitstellt. Die Protokolle HTTP und HTTPS werden unterstützt, und der Pfad muss Lesezugriff ermöglichen. |
| --application-type-build-path | Gilt nur für Imagespeicher zur Bereitstellung. Der relative Pfad für das Anwendungspaket im Imagespeicher, der während des vorherigen Uploadvorgangs angegeben wurde. |
| --application-type-name | Gilt nur für die Bereitstellung aus einem externen Speicher. Der Anwendungstypname stellt den Namen des Anwendungstyps im Anwendungsmanifest dar. |
| --application-type-version | Gilt nur für die Bereitstellung aus einem externen Speicher. Die Anwendungstypversion stellt die Version des Anwendungstyps im Anwendungsmanifest dar. |
| --external-provision | Der Speicherort, von dem das Anwendungspaket registriert oder bereitgestellt werden kann. Gibt an, dass die Bereitstellung über ein Anwendungspaket erfolgt, das zuvor in einen externen Speicher hochgeladen wurde. Das Anwendungspaket endet mit der Erweiterung „*.sfpkg“. |
| --no-wait | Gibt an, ob die Bereitstellung asynchron erfolgen soll. <br><br> Bei Festlegung auf „true“ wird der Bereitstellungsvorgang beendet, wenn die Anforderung vom System akzeptiert wurde. Der Bereitstellungsvorgang wird dann ohne Zeitlimit fortgesetzt. Der Standardwert ist „false“. Bei großen Anwendungspaketen wird empfohlen, den Wert auf „true“ festzulegen. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-application-report-health"></a>sfctl application report-health
Sendet einen Integritätsbericht zu der Service Fabric-Anwendung.

Meldet den Integritätszustand der angegebenen Service Fabric-Anwendung. Der Bericht muss die Informationen über die Quelle des Integritätsberichts und die Eigenschaft enthalten, auf die sich der Bericht bezieht. Der Bericht wird an eine Service Fabric-Gatewayanwendung gesendet, die diesen an den Integritätsspeicher weiterleitet. Der Bericht wird möglicherweise vom Gateway akzeptiert, aber nach zusätzlicher Überprüfung vom Integritätsspeicher abgelehnt. Beispielsweise kann der Integritätsspeicher den Bericht aufgrund eines ungültigen Parameters wie etwa einer veralteten Sequenznummer ablehnen. Um festzustellen, ob der Bericht im Integritätsspeicher angewendet wurde, rufen Sie die Anwendungsintegrität ab, und überprüfen Sie, ob der Bericht angezeigt wird.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich] | Die Identität (ID) der Anwendung. <br><br> Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat der Dienst beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Dienstidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
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

## <a name="sfctl-application-type"></a>sfctl application type
Ruft die Liste der Anwendungstypen im Service Fabric-Cluster ab, die genau mit dem angegebenen Namen übereinstimmen.

Gibt die Informationen über die Anwendungstypen zurück, die im Service Fabric-Cluster bereitgestellt oder zum Bereitstellen vorbereitet werden. Diese Ergebnisse bestehen aus Anwendungstypen, deren Namen genau mit dem übereinstimmen, der als Parameter angegeben ist, und die den angegebenen Abfrageparametern entsprechen. Es werden alle Versionen des Anwendungstyps zurückgegeben, der mit dem Namen des Anwendungstyps übereinstimmt. Dabei wird jede Version als ein Anwendungstyp zurückgegeben. Die Antwort enthält den Namen, die Version, den Status und weitere Details über den Anwendungstyp. Dies ist eine seitenweise Abfrage, d. h., wenn nicht alle Anwendungstypen auf eine Seite passen, werden sowohl eine Seite mit Ergebnissen als auch ein Fortsetzungstoken zurückgegeben, über das zur nächsten Seite gewechselt werden kann. Wenn beispielsweise zehn Anwendungstypen vorhanden sind, aber auf eine Seite nur die ersten drei Anwendungstypen passen, oder die maximale Anzahl von Ergebnissen auf „3“ festgelegt ist, werden drei Ergebnisse zurückgegeben. Wenn Sie auf den Rest der Ergebnisse zugreifen möchten, rufen Sie die anschließenden Seiten ab, indem Sie in der nächsten Abfrage das zurückgegebene Fortsetzungstoken verwenden. Es wird ein leeres Fortsetzungstoken zurückgegeben, wenn keine anschließenden Seiten vorhanden sind.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-type-name [erforderlich] | Der Name des Anwendungstyps. |
| --application-type-version | Die Version des Anwendungstyps. |
| --continuation-token | Der Parameter „continuation-token“ (Fortsetzungstoken) wird dazu verwendet, den nächsten Satz von Ergebnissen abzurufen. Ein Fortsetzungstoken mit einem nicht leeren Wert wird in die Antwort der API eingefügt, wenn die Ergebnisse aus dem System nicht in eine einzige Antwort passen. Wird dieser Wert an den nächsten API-Aufruf übergeben, gibt die API den nächsten Satz von Ergebnissen zurück. Gibt es keine weiteren Ergebnisse, enthält das Fortsetzungstoken keinen Wert. Der Wert dieses Parameters darf nicht als URL codiert sein. |
| --exclude-application-parameters | Das Flag, das angibt, ob die Anwendungsparameter aus dem Ergebnis ausgeschlossen werden sollen. |
| --max-results | Die maximale Anzahl von Ergebnissen, die als Teil der seitenweisen Abfragen zurückgegeben werden sollen. Dieser Parameter definiert die obere Grenze für die Anzahl von zurückgegebenen Ergebnissen. Es können weniger Ergebnisse zurückgegeben werden, als dieser maximalen Anzahl entspricht. Dies ist der Fall, wenn die Ergebnisse wegen der Größenbeschränkungen, die für Meldungen in der Konfiguration definiert sind, nicht in die jeweilige Meldung passen. Ist dieser Parameter gleich null oder nicht angegeben, enthält die seitenweise Abfrage so viele Ergebnisse, wie in die Rückgabemeldung passen. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-application-type-list"></a>sfctl application type-list
Ruft die Liste der Anwendungstypen im Service Fabric-Cluster ab.

Gibt die Informationen über die Anwendungstypen zurück, die im Service Fabric-Cluster bereitgestellt oder zum Bereitstellen vorbereitet werden. Jede Version eines Anwendungstyps wird als ein Anwendungstyp zurückgegeben. Die Antwort enthält den Namen, die Version, den Status und weitere Details über den Anwendungstyp. Dies ist eine seitenweise Abfrage, d. h., wenn nicht alle Anwendungstypen auf eine Seite passen, werden sowohl eine Seite mit Ergebnissen als auch ein Fortsetzungstoken zurückgegeben, über das zur nächsten Seite gewechselt werden kann. Wenn beispielsweise zehn Anwendungstypen vorhanden sind, aber auf eine Seite nur die ersten drei Anwendungstypen passen, oder die maximale Anzahl von Ergebnissen auf „3“ festgelegt ist, werden drei Ergebnisse zurückgegeben. Wenn Sie auf den Rest der Ergebnisse zugreifen möchten, rufen Sie die anschließenden Seiten ab, indem Sie in der nächsten Abfrage das zurückgegebene Fortsetzungstoken verwenden. Es wird ein leeres Fortsetzungstoken zurückgegeben, wenn keine anschließenden Seiten vorhanden sind.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-type-definition-kind-filter | Wird zum Filtern nach ApplicationTypeDefinitionKind verwendet, um einen Service Fabric-Anwendungstyp zu definieren.  <br> - Standard: Standardwert, der die gleiche Funktion hat wie die Auswahl von „All“. Der Wert ist „0“.  <br> - All: Filter, der mit Eingaben eines beliebigen ApplicationTypeDefinitionKind-Werts übereinstimmt. Der Wert ist gleich „65535“.  <br> - ServiceFabricApplicationPackage: Filter, der mit Eingaben übereinstimmt, für die ApplicationTypeDefinitionKind den Wert „ServiceFabricApplicationPackage“ enthält. Der Wert ist gleich „1“.  <br> - Compose: Filter, der mit Eingaben übereinstimmt, für die „ApplicationTypeDefinitionKind“ den Wert „Compose“ hat. Der Wert ist gleich „2“. |
| --continuation-token | Der Parameter „continuation-token“ (Fortsetzungstoken) wird dazu verwendet, den nächsten Satz von Ergebnissen abzurufen. Ein Fortsetzungstoken mit einem nicht leeren Wert wird in die Antwort der API eingefügt, wenn die Ergebnisse aus dem System nicht in eine einzige Antwort passen. Wird dieser Wert an den nächsten API-Aufruf übergeben, gibt die API den nächsten Satz von Ergebnissen zurück. Gibt es keine weiteren Ergebnisse, enthält das Fortsetzungstoken keinen Wert. Der Wert dieses Parameters darf nicht als URL codiert sein. |
| --exclude-application-parameters | Das Flag, das angibt, ob die Anwendungsparameter aus dem Ergebnis ausgeschlossen werden sollen. |
| --max-results | Die maximale Anzahl von Ergebnissen, die als Teil der seitenweisen Abfragen zurückgegeben werden sollen. Dieser Parameter definiert die obere Grenze für die Anzahl von zurückgegebenen Ergebnissen. Es können weniger Ergebnisse zurückgegeben werden, als dieser maximalen Anzahl entspricht. Dies ist der Fall, wenn die Ergebnisse wegen der Größenbeschränkungen, die für Meldungen in der Konfiguration definiert sind, nicht in die jeweilige Meldung passen. Ist dieser Parameter gleich null oder nicht angegeben, enthält die seitenweise Abfrage so viele Ergebnisse, wie in die Rückgabemeldung passen. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-application-unprovision"></a>sfctl application unprovision
Entfernt einen Service Fabric-Anwendungstyp aus dem Cluster oder hebt die Registrierung eines Service Fabric-Anwendungstyps für den Cluster auf.

Dieser Vorgang kann nur ausgeführt werden, wenn alle Anwendungsinstanzen des Anwendungstyps gelöscht wurden. Sobald die Registrierung des Anwendungstyps aufgehoben ist, kann keine neue Anwendungsinstanz mehr für diesen bestimmten Anwendungstyp erstellt werden.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-type-name [erforderlich] | Der Name des Anwendungstyps. |
| --application-type-version [erforderlich] | Die Version des Anwendungstyps, wie sie im Anwendungsmanifest definiert ist. |
| --async-parameter | Das Flag gibt an, ob das Aufheben der Bereitstellung asynchron erfolgen soll. Bei Festlegung auf „true“ wird der Aufhebungsvorgang der Bereitstellung beendet, wenn die Anforderung vom System akzeptiert wurde. Der Vorgang der Aufhebung der Bereitstellung wird dann ohne Zeitlimit fortgesetzt. Der Standardwert ist „false“. Es wird jedoch empfohlen, den Wert für große bereitgestellte Anwendungspakete auf „true“ festzulegen. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-application-upgrade"></a>sfctl application upgrade
Startet ein Aktualisieren einer Anwendung im Service Fabric-Cluster.

Überprüft die angegebenen Upgradeparameter der Anwendung und beginnt mit dem Aktualisieren der Anwendung, wenn die Parameter gültig sind. Beim Aktualisieren wird die vorhandene Anwendungsbeschreibung durch die Upgradebeschreibung ersetzt. Dies bedeutet, dass die vorhandenen Parameter mit der leeren Liste der Parameter überschrieben werden, wenn die Parameter nicht angegeben sind. Dies würde dazu führen, dass für die Anwendung die Standardwerte der Parameter aus dem Anwendungsmanifest verwendet werden.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich] | Die Identität (ID) der Anwendung. <br><br> Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
| --application-version [erforderlich] | Die Version des Zielanwendungstyps (im Anwendungsmanifest enthalten) für das Anwendungsupgrade. |
| --parameters [erforderlich] | Eine JSON-codierte Liste mit Außerkraftsetzungen von Anwendungsparametern, die beim Upgrade der Anwendung angewendet werden sollen. |
| --default-service-health-policy | Eine JSON-codierte Spezifikation der Integritätsrichtlinie, die standardmäßig zur Auswertung der Integrität eines Diensttyps verwendet wird. |
| --failure-action | Die Aktion, die ausgeführt werden soll, wenn ein überwachtes Upgrade Verstöße gegen die Überwachungsrichtlinie oder Integritätsrichtlinie erkennt. |
| --force-restart | Während eines Upgrades wird ein erzwungener Neustart durchgeführt, auch wenn sich die Codeversion nicht geändert hat. |
| --health-check-retry-timeout | Die Zeitspanne zwischen den Versuchen, Integritätsprüfungen durchzuführen, wenn die Anwendung oder der Cluster nicht fehlerfrei ist.  Standardwert\: „PT0H10M0S“. |
| --health-check-stable-duration | Die Zeitspanne, während der die Anwendung oder der Cluster fehlerfrei bleiben muss, bevor das Upgrade mit der nächsten Upgradedomäne fortgesetzt wird.  Standardwert\: „PT0H2M0S“. <br><br> Wird zuerst als Zeichenfolge interpretiert, die für eine ISO 8601-Dauer steht. Wenn dies nicht erfolgreich ist, erfolgt die Interpretation als Zahl, die für die Gesamtzahl an Millisekunden steht. |
| --health-check-wait-duration | Die Zeitspanne, während der nach dem Abschließen einer Upgradedomäne gewartet werden soll, bevor der Integritätsprüfungsprozess gestartet wird.  Standardwert\: „0“. |
| --max-unhealthy-apps | Der maximal zulässige Prozentsatz von bereitgestellten Anwendungen, die fehlerhaft sind. Dieser wird als Zahl zwischen 0 und 100 dargestellt. |
| --mode | Der Modus, der zum Überwachen der Integrität während eines parallelen Upgrades verwendet wird.  Standardwert\: „UnmonitoredAuto“. |
| --replica-set-check-timeout | Die maximale Zeitspanne, während der die Verarbeitung einer Upgradedomäne blockiert und Verfügbarkeitsverlust verhindert wird, wenn es unerwartete Probleme gibt. Dieser Wert wird in Sekunden gemessen. |
| --service-health-policy | JSON-codierte Zuordnung mit einer Diensttypintegritätsrichtlinie pro Diensttypname. Der Überblick ist standardmäßig leer. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |
| --upgrade-domain-timeout | Die Zeitspanne, während der jede Upgradedomäne abgeschlossen werden muss, bevor die Fehleraktion (FailureAction) ausgeführt wird.  Standardwert\: „P10675199DT02H48M05.4775807S“. <br><br> Wird zuerst als Zeichenfolge interpretiert, die für eine ISO 8601-Dauer steht. Wenn dies nicht erfolgreich ist, erfolgt die Interpretation als Zahl, die für die Gesamtzahl an Millisekunden steht. |
| --upgrade-timeout | Die Zeitspanne, während der das gesamte Upgrade abgeschlossen werden muss, bevor die Fehleraktion (FailureAction) ausgeführt wird.  Standardwert\: „P10675199DT02H48M05.4775807S“. <br><br> Wird zuerst als Zeichenfolge interpretiert, die für eine ISO 8601-Dauer steht. Wenn dies nicht erfolgreich ist, erfolgt die Interpretation als Zahl, die für die Gesamtzahl an Millisekunden steht. |
| --warning-as-error | Gibt an, ob Warnungen mit demselben Schweregrad wie Fehler berücksichtigt werden. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-application-upgrade-resume"></a>sfctl application upgrade-resume
Setzt ein Aktualisieren einer Anwendung im Service Fabric-Cluster fort.

Setzt ein nicht überwachtes manuelles Upgrade für eine Service Fabric-Anwendung fort. Bei Service Fabric wird ein Upgrade für jeweils eine Upgradedomäne durchgeführt. Bei nicht überwachten manuellen Upgrades wartet Service Fabric nach Abschluss einer Upgradedomäne darauf, dass Sie diese API aufrufen. Erst dann wird das Upgrade mit der nächsten Upgradedomäne fortgesetzt.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich] | Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
| --upgrade-domain-name [erforderlich] | Der Name der Upgradedomäne, mit der das Upgrade fortgesetzt werden soll. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-application-upgrade-rollback"></a>sfctl application upgrade-rollback
Führt ein Rollback des derzeit laufenden Upgrades einer Anwendung im Service Fabric-Cluster aus.

Startet das Rollback des aktuellen Anwendungsupgrades auf die vorherige Version. Diese API kann nur dazu verwendet werden, ein Rollback für das aktuelle laufende Upgrade auszuführen, mit dem ein Rollforward auf die neue Version ausgeführt wird. Wenn für die Anwendung derzeit kein Upgrade durchgeführt wird, verwenden Sie die StartApplicationUpgrade-API, um ein Upgrade auf die gewünschte Version durchzuführen und ein Rollback auf eine ältere Version auszuführen.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich] | Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-application-upgrade-status"></a>sfctl application upgrade-status
Ruft Details über das neueste Upgrade ab, das für diese Anwendung ausgeführt wurde.

Gibt die Informationen über den Status des aktuellen Anwendungsupgrades zusammen mit Details zurück, die beim Debuggen von Anwendungsfehlern unterstützen.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich] | Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-application-upload"></a>sfctl application upload
Kopiert ein Service Fabric-Anwendungspaket in den Imagespeicher.

Optional kann der Uploadfortschritt für jede Datei im Paket angezeigt werden. Der jeweilige Uploadfortschritt wird an `stderr` gesendet.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --path [erforderlich] | Pfad zum lokalen Anwendungspaket. |
| --imagestore-string | Zielimagespeicher, in den das Anwendungspaket hochgeladen werden soll.  Standardwert\: „fabric\:ImageStore“. |
| --show-progress | Zeigt den Fortschritt des Dateiuploads für große Pakete an. |

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
