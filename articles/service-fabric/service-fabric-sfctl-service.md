---
title: Azure Service Fabric CLI – sfctl service | Microsoft-Dokumentation
description: Beschreibt die sfctl service-Befehle der Service Fabric-Befehlszeilenschnittstelle (Command Line Interface, CLI).
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
ms.openlocfilehash: e0454d0124efba04434884fbac9056c5e324710d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60556371"
---
# <a name="sfctl-service"></a>sfctl service
Ermöglicht es, Dienste, Diensttypen und Dienstpakete zu erstellen, zu löschen und zu verwalten.

## <a name="commands"></a>Befehle

|Get-Help|BESCHREIBUNG|
| --- | --- |
| app-name | Ruft den Namen der Service Fabric-Anwendung für einen Dienst ab. |
| code-package-list | Ruft die Liste der Codepakete ab, die auf einem Service Fabric-Knoten bereitgestellt werden. |
| create | Erstellt den angegebenen Service Fabric-Dienst. |
| delete | Löscht einen vorhandene Service Fabric-Dienst. |
| deployed-type | Ruft die Informationen zu einem angegebenen Diensttyp der Anwendung ab, die auf einem Knoten in einem Service Fabric-Cluster bereitgestellt wird. |
| deployed-type-list | Ruft die Liste ab, die die Informationen zu den Diensttypen der Anwendungen enthält, die auf einem Knoten in einem Service Fabric-Cluster bereitgestellt werden. |
| description | Ruft die Beschreibung eines vorhandenen Service Fabric-Diensts ab. |
| get-container-logs | Ruft die Containerprotokolle für Container ab, die auf einem Service Fabric-Knoten bereitgestellt wurden. |
| health | Ruft die Integrität des angegebenen Service Fabric-Diensts ab. |
| info | Ruft die Informationen zu dem speziellen Dienst ab, der zur Service Fabric-Anwendung gehört. |
| list | Ruft die Informationen zu allen Diensten ab, die zu der Anwendung gehören, die durch die Anwendungs-ID angegeben ist. |
| manifest | Ruft das Manifest ab, in dem ein Diensttyp beschrieben ist. |
| package-deploy | Lädt Pakete herunter, die dem angegebenen Dienstmanifest im Imagecache auf dem angegebenen Knoten zugeordnet sind. |
| package-health | Ruft die Informationen über die Integrität eines Dienstpakets für eine bestimmte Anwendung ab, die für einen Service Fabric-Knoten und eine Service Fabric-Anwendung bereitgestellt wird. |
| package-info | Ruft die Liste von Dienstpaketen ab, die auf einem Service Fabric-Knoten bereitgestellt werden und genau mit dem angegebenen Namen übereinstimmen. |
| package-list | Ruft die Liste der Dienstpakete ab, die auf einem Service Fabric-Knoten bereitgestellt werden. |
| recover | Veranlasst den Service Fabric-Cluster zu versuchen, den angegebenen Dienst wiederherzustellen, der derzeit in Quorumverlust gebunden ist. |
| report-health | Sendet einen Integritätsbericht zu dem Service Fabric-Dienst. |
| resolve | Löst eine Service Fabric-Partition auf. |
| type-list | Ruft die Liste ab, die die Informationen zu den Diensttypen enthält, die von einem bereitgestellten Anwendungstyp in einem Service Fabric-Cluster unterstützt werden. |
| aktualisieren | Aktualisiert den angegebenen Dienst mithilfe der angegebenen Updatebeschreibung. |

## <a name="sfctl-service-app-name"></a>sfctl service app-name
Ruft den Namen der Service Fabric-Anwendung für einen Dienst ab.

Ruft den Namen der Anwendung für den angegebenen Dienst ab. Ein 404 FABRIC_E_SERVICE_DOES_NOT_EXIST-Fehler wird zurückgegeben, wenn ein Dienst mit der angegebenen Dienst-ID nicht vorhanden ist.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
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

## <a name="sfctl-service-code-package-list"></a>sfctl service code-package-list
Ruft die Liste der Codepakete ab, die auf einem Service Fabric-Knoten bereitgestellt werden.

Ruft die Liste der Codepakete ab, die auf einem Service Fabric-Knoten für die jeweilige Anwendung bereitgestellt werden.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich] | Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
| --node-name [erforderlich] | Der Name des Knotens. |
| --code-package-name | Der Name des Codepakets, das im Dienstmanifest angegeben ist, das als Bestandteil eines Anwendungstyps in einem Service Fabric-Cluster registriert ist. |
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

## <a name="sfctl-service-create"></a>sfctl service create
Erstellt den angegebenen Service Fabric-Dienst.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --app-id [erforderlich] | Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat der Dienst beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Dienstidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
| --name [erforderlich] | Der Name des Diensts. Dies muss ein untergeordnetes Element der Anwendungs-ID sein. Dies ist der vollständige Name einschließlich des URI `fabric\:`. Beispielsweise ist der Dienst `fabric\:/A/B` ein untergeordnetes Element der Anwendung `fabric\:/A`. |
| --service-type [erforderlich] | Den Name des Diensttyps. |
| --activation-mode | Der Aktivierungsmodus für das Dienstpaket. |
| --constraints | Die Platzierungseinschränkungen als Zeichenfolge. Platzierungseinschränkungen sind boolesche Ausdrücke für Knoteneigenschaften und ermöglichen es, einen Dienst entsprechend den Dienstanforderungen auf bestimmte Knoten einzuschränken. Soll ein Dienst z.B. auf Knoten platziert werden, für die Blau als Knotenfarbe (NodeType) festgelegt ist, geben Sie Folgendes an\: „NodeColor == blue“. |
| --correlated-service | Der Name des Zieldiensts, dem der Dienst zugeordnet werden soll. |
| --correlation | Ordnen Sie den Dienst über eine Ausrichtungsaffinität einem vorhandenen Dienst zu. |
| --dns-name | Der DNS-Name des Diensts, der erstellt werden soll. Für diese Einstellung muss der Service Fabric-DNS-Systemdienst aktiviert sein. |
| --instance-count | Die Anzahl von Instanzen. Dies gilt für nur zustandslose Dienste. |
| --int-scheme | Gibt an, dass der Dienst über einen Bereich von ganzen Zahlen ohne Vorzeichen einheitlich partitioniert werden muss. |
| --int-scheme-count | Die Anzahl von Partitionen, die im Ganzzahlschlüsselbereich erstellt werden sollen, wenn ein einheitliches ganzzahliges Partitionsschemas verwendet wird. |
| --int-scheme-high | Das Ende des Ganzzahlschlüsselbereichs, wenn ein einheitliches ganzzahliges Partitionsschema verwendet wird. |
| --int-scheme-low | Der Anfang des Ganzzahlschlüsselbereichs, wenn ein einheitliches ganzzahliges Partitionsschema verwendet wird. |
| --load-metrics | JSON-codierte Liste der Metriken, die zum knotenübergreifenden Lastenausgleich für Dienste verwendet werden. |
| --min-replica-set-size | Die Mindestgröße der Replikatgruppe als Zahl. Dies gilt nur für zustandsbehaftete Dienste. |
| --move-cost | Gibt die Verschiebungskosten für den Dienst an. Mögliche Werte sind\: „Zero“, „Low“, „Medium“, „High“. |
| --named-scheme | Gibt an, dass der Dienst mehrere benannte Partitionen haben muss. |
| --named-scheme-list | JSON-codierte Liste mit Namen, über die der Dienst partitioniert werden soll, wenn das benannte Partitionsschema verwendet wird. |
| --no-persisted-state | Bei „true“ gibt dieses Argument an, dass der Dienst keinen persistenten Zustand aufweist, der auf dem lokalen Datenträger gespeichert ist, oder den Zustand nur im Arbeitsspeicher speichert. |
| --placement-policy-list | JSON-codierte Liste mit Platzierungsrichtlinien für den Dienst und alle zugehörigen Domänennamen. Richtlinien können aus einer oder einer Kombination der folgenden Angaben bestehen\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --quorum-loss-wait | Die maximale Dauer (in Sekunden), für die eine Partition den Zustand für Quorumverlust haben darf. Dies gilt nur für zustandsbehaftete Dienste. |
| --replica-restart-wait | Die Dauer (in Sekunden) zwischen einem Ausfall eines Replikats und dem Erstellen eines neuen Replikats. Dies gilt nur für zustandsbehaftete Dienste. |
| --scaling-policies | JSON-codierte Liste von Skalierungsrichtlinien für diesen Dienst. |
| --singleton-scheme | Gibt an, dass der Dienst nur eine einzige Partition haben darf oder ein nicht partitionierter Dienst sein muss. |
| --stand-by-replica-keep | Die maximale Dauer (in Sekunden), für die StandBy-Replikate beibehalten werden, bevor sie entfernt werden. Dies gilt nur für zustandsbehaftete Dienste. |
| --stateful | Gibt an, dass der Dienst ein zustandsbehafteter Dienst ist. |
| --stateless | Gibt an, dass der Dienst ein zustandsloser Dienst ist. |
| --target-replica-set-size | Die Zielgröße der Replikatgruppe als Zahl. Dies gilt nur für zustandsbehaftete Dienste. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-service-delete"></a>sfctl service delete
Löscht einen vorhandene Service Fabric-Dienst.

Ein Dienst muss erstellt worden sein, bevor er gelöscht werden kann. Service Fabric versucht standardmäßig, Dienstreplikate auf ordnungsgemäße Weise zu schließen und den Dienst dann zu löschen. Wenn es aber Probleme mit dem ordnungsgemäßen Schließen eines Replikats gibt, kann der Löschvorgang sehr lange dauern oder abstürzen. Verwenden Sie das optionale Flag „ForceRemove“, um die ordnungsgemäße Schließsequenz zu überspringen und das Löschen des Diensts zu erzwingen.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --service-id [erforderlich] | Die Identität (ID) des Diensts. Diese ID ist üblicherweise der vollständige Name des Diensts ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat der Dienst beispielsweise den Namen „fabric\:/meineapp/app1/svc1“, weist die Dienstidentität in 6.0 und höher den Wert „meineapp\~app1\~svc1“ und in früheren Versionen den Wert „meineapp/app1/svc1“ auf. |
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

## <a name="sfctl-service-deployed-type"></a>sfctl service deployed-type
Ruft die Informationen zu einem angegebenen Diensttyp der Anwendung ab, die auf einem Knoten in einem Service Fabric-Cluster bereitgestellt wird.

Ruft die Liste ab, die die Informationen zu einem bestimmten Diensttyp der Anwendungen enthält, die auf einem Knoten in einem Service Fabric-Cluster bereitgestellt werden. Die Antwort enthält den Namen des Diensttyps, dessen Registrierungsstatus, das für dessen Registrierung verwendete Codepaket und die Aktivierungs-ID des Dienstpakets. Jeder Eintrag stellt eine Aktivierung eines Diensttyps dar, die durch die Aktivierung-ID unterschieden werden.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich] | Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
| --node-name [erforderlich] | Der Name des Knotens. |
| --service-type-name [erforderlich] | Gibt den Namen eines Service Fabric-Diensttyps an. |
| --service-manifest-name | Der Name des Dienstmanifests, um die Liste der bereitgestellten Diensttypinformationen zu filtern. Wenn dieser angegeben ist, enthält die Antwort nur die Informationen zu Diensttypen, die in diesem Dienstmanifest definiert sind. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-service-deployed-type-list"></a>sfctl service deployed-type-list
Ruft die Liste ab, die die Informationen zu den Diensttypen der Anwendungen enthält, die auf einem Knoten in einem Service Fabric-Cluster bereitgestellt werden.

Ruft die Liste ab, die die Informationen zu den Diensttypen der Anwendungen enthält, die auf einem Knoten in einem Service Fabric-Cluster bereitgestellt werden. Die Antwort enthält den Namen des Diensttyps, dessen Registrierungsstatus, das für dessen Registrierung verwendete Codepaket und die Aktivierungs-ID des Dienstpakets.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich] | Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
| --node-name [erforderlich] | Der Name des Knotens. |
| --service-manifest-name | Der Name des Dienstmanifests, um die Liste der bereitgestellten Diensttypinformationen zu filtern. Wenn dieser angegeben ist, enthält die Antwort nur die Informationen zu Diensttypen, die in diesem Dienstmanifest definiert sind. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-service-description"></a>sfctl service description
Ruft die Beschreibung eines vorhandenen Service Fabric-Diensts ab.

Ruft die Beschreibung eines vorhandenen Service Fabric-Diensts ab. Ein Dienst muss erstellt worden sein, bevor seine Beschreibung abgerufen werden kann.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
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

## <a name="sfctl-service-get-container-logs"></a>sfctl service get-container-logs
Ruft die Containerprotokolle für Container ab, die auf einem Service Fabric-Knoten bereitgestellt wurden.

Ruft die Containerprotokolle für Container ab, die auf einem Service Fabric-Knoten für das jeweilige Codepaket bereitgestellt wurden.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich] | Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
| --code-package-name [erforderlich] | Der Name des Codepakets, das im Dienstmanifest angegeben ist, das als Bestandteil eines Anwendungstyps in einem Service Fabric-Cluster registriert ist. |
| --node-name [erforderlich] | Der Name des Knotens. |
| --service-manifest-name [erforderlich] | Der Name eines Dienstmanifests, das als Bestandteil eines Anwendungstyps in einem Service Fabric-Cluster registriert ist. |
| --previous | Gibt an, ob Containerprotokolle von beendeten oder inaktiven Containern der Codepaketinstanz abgerufen werden. |
| --tail | Die Anzahl der Zeilen, die am Ende der Protokolle angezeigt werden sollen. Der Standardwert ist 100. „all“, um die vollständige Protokolle anzuzeigen. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-service-health"></a>sfctl service health
Ruft die Integrität des angegebenen Service Fabric-Diensts ab.

Ruft die Integritätsinformationen des angegebenen Diensts ab. Verwenden Sie „EventsHealthStateFilter“, um die Sammlung der Integritätsereignisse (HealthEvents), die für den Dienst gemeldet wurden, anhand des Integritätsstatus zu filtern. Verwenden Sie „PartitionsHealthStateFilter“, um die zurückgegebene Sammlung von Partitionen zu filtern. Geben Sie einen Dienst an, der nicht im Integritätsspeicher vorhanden ist, gibt diese Anforderung einen Fehler zurück.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --service-id [erforderlich] | Die Identität (ID) des Diensts. Diese ID ist üblicherweise der vollständige Name des Diensts ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat der Dienst beispielsweise den Namen „fabric\:/meineapp/app1/svc1“, weist die Dienstidentität in 6.0 und höher den Wert „meineapp\~app1\~svc1“ und in früheren Versionen den Wert „meineapp/app1/svc1“ auf. |
| --events-health-state-filter | Ermöglicht das Filtern der Collection zurückgegebener HealthEvent-Objekte anhand des Integritätsstatus. Die möglichen Werte für diesen Parameter entsprechen dem jeweiligen ganzzahligen Wert von einem der folgenden Integritätsstatus. Es werden nur Ereignisse zurückgegeben, die dem Filter entsprechen. Alle Ereignisse werden verwendet, um den aggregierten Integritätsstatus auszuwerten. Ist kein Filter angegeben, werden alle Einträge zurückgegeben. Ein Statuswert ist eine flagbasierte Enumeration, sodass der Wert eine Kombination der Werte sein kann, die mit dem bitweisen OR-Operator abgerufen werden. Ist der angegebene Wert beispielsweise gleich „6“, werden alle Ereignisse zurückgegeben, für die „HealthState“ den Wert für OK (2) oder Warnung (4) hat.  <br> – Default: Standardwert. Stimmt mit jedem Integritätsstatus (HealthState) überein. Der Wert ist gleich null.  <br> – None: Filter, der mit keinem Wert für „HealthState“ übereinstimmt. Wird verwendet, um keine Ergebnisse für eine angegebene Statussammlung zurückzugeben. Der Wert ist gleich „1“.  <br> – Ok: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Ok“ hat. Der Wert ist gleich „2“.  <br> – Warning: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Warning“ hat. Der Wert ist gleich „4“.  <br> – Error: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Error“ hat. Der Wert ist gleich „8“.  <br> – All: Filter, der mit Eingaben übereinstimmt, die einen beliebigen Wert für „HealthState“ haben. Der Wert ist gleich „65535“. |
| --exclude-health-statistics | Gibt an, ob die Integritätsstatistiken als Bestandteil des Abfrageergebnisses zurückgegeben werden sollen. Der Standardwert ist gleich „False“. Die Statistiken zeigen die Anzahl von untergeordneten Entitäten, die einen der Integritätszustände „Ok“, „Warning“ oder „Error“ aufweisen. |
| --partitions-health-state-filter | Ermöglicht es, die Partitions-Integritätszustandsobjekte, die im Ergebnis einer Dienstintegritätsabfrage zurückgegeben werden, anhand des Integritätszustands zu filtern. Die möglichen Werte für diesen Parameter entsprechen dem jeweiligen ganzzahligen Wert von einem der folgenden Integritätsstatus. Es werden nur Partitionen zurückgegeben, die dem Filter entsprechen. Alle Partitionen werden verwendet, um den aggregierten Integritätszustand auszuwerten. Ist kein Filter angegeben, werden alle Einträge zurückgegeben. Ein Statuswert ist eine flagbasierte Enumeration, sodass der Wert eine Kombination der Werte sein kann, die mit dem bitweisen OR-Operator abgerufen werden. Ist der angegebene Wert beispielsweise gleich „6“, wird der Integritätszustand von Partitionen zurückgegeben, für die „HealthState“ den Wert „OK (2)“ oder „Warnung (4)“ aufweist.  <br> – Default: Standardwert. Stimmt mit jedem Integritätsstatus (HealthState) überein. Der Wert ist gleich null.  <br> – None: Filter, der mit keinem Wert für „HealthState“ übereinstimmt. Wird verwendet, um keine Ergebnisse für eine angegebene Statussammlung zurückzugeben. Der Wert ist gleich „1“.  <br> – Ok: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Ok“ hat. Der Wert ist gleich „2“.  <br> – Warning: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Warning“ hat. Der Wert ist gleich „4“.  <br> – Error: Filter, der mit Eingaben übereinstimmt, für die „HealthState“ den Wert „Error“ hat. Der Wert ist gleich „8“.  <br> – All: Filter, der mit Eingaben übereinstimmt, die einen beliebigen Wert für „HealthState“ haben. Der Wert ist gleich „65535“. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-service-info"></a>sfctl service info
Ruft die Informationen zu dem speziellen Dienst ab, der zur Service Fabric-Anwendung gehört.

Gibt die Informationen zu dem angegebenen Dienst zurück, der zu der angegebenen Service Fabric-Anwendung gehört.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich] | Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
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

## <a name="sfctl-service-list"></a>sfctl service list
Ruft die Informationen zu allen Diensten ab, die zu der Anwendung gehören, die durch die Anwendungs-ID angegeben ist.

Gibt die Informationen zu allen Diensten zurück, die zu der Anwendung gehören, die durch die Anwendungs-ID angegeben ist.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich] | Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
| --continuation-token | Der Parameter „continuation-token“ (Fortsetzungstoken) wird dazu verwendet, den nächsten Satz von Ergebnissen abzurufen. Ein Fortsetzungstoken mit einem nicht leeren Wert wird in die Antwort der API eingefügt, wenn die Ergebnisse aus dem System nicht in eine einzige Antwort passen. Wird dieser Wert an den nächsten API-Aufruf übergeben, gibt die API den nächsten Satz von Ergebnissen zurück. Gibt es keine weiteren Ergebnisse, enthält das Fortsetzungstoken keinen Wert. Der Wert dieses Parameters darf nicht als URL codiert sein. |
| --service-type-name | Der Diensttypname, der zum Filtern der Dienste verwendet wird, die abgefragt werden sollen. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-service-manifest"></a>sfctl service manifest
Ruft das Manifest ab, in dem ein Diensttyp beschrieben ist.

Ruft das Manifest ab, in dem ein Diensttyp beschrieben ist. Die Antwort enthält den XML-Code des Dienstmanifests als Zeichenfolge.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-type-name [erforderlich] | Der Name des Anwendungstyps. |
| --application-type-version [erforderlich] | Die Version des Anwendungstyps. |
| --service-manifest-name [erforderlich] | Der Name eines Dienstmanifests, das als Bestandteil eines Anwendungstyps in einem Service Fabric-Cluster registriert ist. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-service-package-deploy"></a>sfctl service package-deploy
Lädt Pakete herunter, die dem angegebenen Dienstmanifest im Imagecache auf dem angegebenen Knoten zugeordnet sind.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --app-type-name [erforderlich] | Der Name des Anwendungsmanifests für das entsprechende angeforderte Dienstmanifest. |
| --app-type-version [erforderlich] | Die Version des Anwendungsmanifests für das entsprechende angeforderte Dienstmanifest. |
| --node-name [erforderlich] | Der Name des Knotens. |
| --service-manifest-name [erforderlich] | Der Name des Dienstmanifests, das den herunterzuladenden Paketen zugeordnet ist. |
| --share-policy | JSON-codierte Liste mit Freigaberichtlinien. Jedes Freigaberichtlinienelement besteht aus einem Name- und Scope-Element. Das Name-Element entspricht dem Namen des Code-, Konfigurations- oder Datenpakets, das für die Freigabe bestimmt ist. Das Scope-Element kann entweder „None“, „All“, „Code“, „Config“ oder „Data“ enthalten. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-service-package-health"></a>sfctl service package-health
Ruft die Informationen über die Integrität eines Dienstpakets für eine bestimmte Anwendung ab, die für einen Service Fabric-Knoten und eine Service Fabric-Anwendung bereitgestellt wird.

Ruft die Informationen über die Integrität eines Dienstpakets für eine bestimmte Anwendung ab, die für einen Service Fabric-Knoten bereitgestellt wird. Verwenden Sie EventsHealthStateFilter, um optional basierend auf dem Integritätszustand nach der Sammlung von HealthEvent-Objekten zu filtern, die für das bereitgestellte Dienstpaket gemeldet wurden.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich] | Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
| --node-name [erforderlich] | Der Name des Knotens. |
| --service-package-name [erforderlich] | Der Name des Dienstpakets. |
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

## <a name="sfctl-service-package-info"></a>sfctl service package-info
Ruft die Liste von Dienstpaketen ab, die auf einem Service Fabric-Knoten bereitgestellt werden und genau mit dem angegebenen Namen übereinstimmen.

Gibt die Informationen zu den Dienstpaketen zurück, die auf einem Service Fabric-Knoten für die jeweilige Anwendung bereitgestellt werden. Diese Ergebnisse sind Teil der Dienstpakete, deren Name genau dem als Parameter angegebenen Dienstpaketnamen entspricht.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich] | Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
| --node-name [erforderlich] | Der Name des Knotens. |
| --service-package-name [erforderlich] | Der Name des Dienstpakets. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-service-package-list"></a>sfctl service package-list
Ruft die Liste der Dienstpakete ab, die auf einem Service Fabric-Knoten bereitgestellt werden.

Gibt die Informationen zu den Dienstpaketen zurück, die auf einem Service Fabric-Knoten für die jeweilige Anwendung bereitgestellt werden.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id [erforderlich] | Die Identität (ID) der Anwendung. Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
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

## <a name="sfctl-service-recover"></a>sfctl service recover
Veranlasst den Service Fabric-Cluster zu versuchen, den angegebenen Dienst wiederherzustellen, der derzeit in Quorumverlust gebunden ist.

Veranlasst den Service Fabric-Cluster zu versuchen, den angegebenen Dienst wiederherzustellen, der derzeit in Quorumverlust gebunden ist. Dieser Vorgang sollte nur ausgeführt werden, wenn bekannt ist, dass die Replikate, die ausgefallen sind, nicht wiederhergestellt werden können. Eine falsche Verwendung dieser API kann dazu führen, dass Daten verloren gehen.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
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

## <a name="sfctl-service-report-health"></a>sfctl service report-health
Sendet einen Integritätsbericht zu dem Service Fabric-Dienst.

Meldet den Integritätszustand des angegebenen Service Fabric-Diensts. Der Bericht muss die Informationen über die Quelle des Integritätsberichts und die Eigenschaft enthalten, auf die sich der Bericht bezieht. Der Bericht wird an einen Service Fabric-Gatewaydienst gesendet, der diesen an den Integritätsspeicher weiterleitet. Der Bericht wird möglicherweise vom Gateway akzeptiert, aber nach zusätzlicher Überprüfung vom Integritätsspeicher abgelehnt. Beispielsweise kann der Integritätsspeicher den Bericht aufgrund eines ungültigen Parameters wie etwa einer veralteten Sequenznummer ablehnen. Um festzustellen, ob der Bericht im Integritätsspeicher angewendet wurde, überprüfen Sie, ob der Bericht in den Integritätsereignissen des Diensts angezeigt wird.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --health-property [erforderlich] | Die Eigenschaft der Integritätsinformationen. <br><br> Eine Entität kann Integritätsberichte für verschiedene Eigenschaften enthalten. Die Eigenschaft ist eine Zeichenfolge und keine feste Enumeration, um dem Reporter Flexibilität zu ermöglichen und entsprechend die Zustandsbedingung zu kategorisieren, die den Bericht auslöst. Beispielsweise kann ein Reporter mit der SourceId „LocalWatchdog“ den Zustand des verfügbaren Datenträgers auf einem Knoten überwachen und folglich die AvailableDisk-Eigenschaft auf diesem Knoten melden. Der gleiche Reporter kann die Knotenkonnektivität überwachen und folglich eine Connectivity-Eigenschaft auf dem gleichen Knoten melden. Im Integritätsspeicher werden diese Berichte als separate Integritätsereignisse für den angegebenen Knoten behandelt. Zusammen mit der SourceId identifiziert die Eigenschaft eindeutig die Integritätsinformationen. |
| --health-state [erforderlich] | Mögliche Werte sind\: „Invalid“, „Ok“, „Warning“, „Error“, „Unknown“. |
| --service-id [erforderlich] | Die Identität (ID) des Diensts. <br><br> Dies ist üblicherweise der vollständige Name des Diensts ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat der Dienst beispielsweise den Namen „fabric\:/meineapp/app1/svc1“, hat die Dienstidentität in 6.0 und höher den Wert „meineapp\~app1\~svc1“ und in früheren Versionen den Wert „meineapp/app1/svc1“. |
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

## <a name="sfctl-service-resolve"></a>sfctl service resolve
Löst eine Service Fabric-Partition auf.

Lösen Sie eine Service Fabric-Dienstpartition auf, um die Endpunkte der Dienstreplikate abzurufen.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --service-id [erforderlich] | Die Identität (ID) des Diensts. Diese ID ist üblicherweise der vollständige Name des Diensts ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat der Dienst beispielsweise den Namen „fabric\:/meineapp/app1/svc1“, weist die Dienstidentität in 6.0 und höher den Wert „meineapp\~app1\~svc1“ und in früheren Versionen den Wert „meineapp/app1/svc1“ auf. |
| --partition-key-type | Schlüsseltyp für die Partition. Dieser Parameter ist erforderlich, wenn das Partitionsschema für den Dienst gleich „Int64Range“ oder „Named“ ist. Die folgenden Werte sind möglich. – None (1): Gibt an, dass der Parameter „PartitionKeyValue“ nicht angegeben ist. Dies ist für die Partitionen gültig, deren Partitionsschema als „Singleton“ festgelegt ist. Dies ist der Standardwert. Der Wert ist gleich „1“. – Int64Range (2): Gibt an, dass der Wert des Parameters „PartitionKeyValue“ ein int64-Partitionsschlüssel ist. Dies ist für die Partitionen gültig, deren Partitionsschema als „Int64Range“ festgelegt ist. Der Wert ist gleich „2“. – Named (3): Gibt an, dass der Wert des Parameters „PartitionKeyValue“ ein Name der Partition ist. Dies ist für die Partitionen gültig, deren Partitionsschema als „Named“ festgelegt ist. Der Wert ist gleich „3“. |
| --partition-key-value | Der Partitionsschlüssel. Dieses Argument ist erforderlich, wenn das Partitionsschema für den Dienst gleich „Int64Range“ oder „Named“ ist. Dies ist nicht die Partitions-ID, sondern entweder der ganzzahlige Schlüsselwert oder der Name der Partitions-ID. Wenn Ihr Dienst beispielsweise Bereichspartitionen von 0 bis 10 verwendet, dann wäre PartitionKeyValue eine ganze Zahl in diesem Bereich. Fragen Sie die Dienstbeschreibung ab, auf den Bereich oder Namen anzuzeigen. |
| --previous-rsp-version | Der Wert im „Version“-Feld der Antwort, die zuvor empfangen wurde. Dieses Argument ist erforderlich, wenn der Benutzer weiß, dass das zuvor abgerufene Ergebnis veraltet ist. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-service-type-list"></a>sfctl service type-list
Ruft die Liste ab, die die Informationen zu den Diensttypen enthält, die von einem bereitgestellten Anwendungstyp in einem Service Fabric-Cluster unterstützt werden.

Ruft die Liste ab, die die Informationen zu den Diensttypen enthält, die von einem bereitgestellten Anwendungstyp in einem Service Fabric-Cluster unterstützt werden. Der angegebene Anwendungstyp muss vorhanden sein. Andernfalls wird der Status 404 zurückgegeben.

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

## <a name="sfctl-service-update"></a>sfctl service update
Aktualisiert den angegebenen Dienst mithilfe der angegebenen Updatebeschreibung.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --service-id [erforderlich] | Die Identität (ID) des Diensts. Dies ist üblicherweise der vollständige Name des Diensts ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat der Dienst beispielsweise den Namen „fabric\:/meineapp/app1/svc1“, hat die Dienstidentität in 6.0 und höher den Wert „meineapp\~app1\~svc1“ und in früheren Versionen den Wert „meineapp/app1/svc1“. |
| --constraints | Die Platzierungseinschränkungen als Zeichenfolge. Platzierungseinschränkungen sind boolesche Ausdrücke für Knoteneigenschaften und ermöglichen es, einen Dienst entsprechend den Dienstanforderungen auf bestimmte Knoten einzuschränken. Soll ein Dienst z. B. auf Knoten platziert werden, für die Blau als Knotenfarbe (NodeType) festgelegt ist, geben Sie Folgendes an\: „NodeColor == blue“. |
| --correlated-service | Der Name des Zieldiensts, dem der Dienst zugeordnet werden soll. |
| --correlation | Ordnen Sie den Dienst über eine Ausrichtungsaffinität einem vorhandenen Dienst zu. |
| --instance-count | Die Anzahl von Instanzen. Dies gilt für nur zustandslose Dienste. |
| --load-metrics | JSON-codierte Liste der Metriken, die zum knotenübergreifenden Lastenausgleich verwendet werden. |
| --min-replica-set-size | Die Mindestgröße der Replikatgruppe als Zahl. Dies gilt nur für zustandsbehaftete Dienste. |
| --move-cost | Gibt die Verschiebungskosten für den Dienst an. Mögliche Werte sind\: „Zero“, „Low“, „Medium“, „High“. |
| --placement-policy-list | JSON-codierte Liste mit Platzierungsrichtlinien für den Dienst und alle zugehörigen Domänennamen. Richtlinien können aus einer oder einer Kombination der folgenden Angaben bestehen\: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --quorum-loss-wait | Die maximale Dauer (in Sekunden), für die eine Partition den Zustand für Quorumverlust haben darf. Dies gilt nur für zustandsbehaftete Dienste. |
| --replica-restart-wait | Die Dauer (in Sekunden) zwischen einem Ausfall eines Replikats und dem Erstellen eines neuen Replikats. Dies gilt nur für zustandsbehaftete Dienste. |
| --scaling-policies | JSON-codierte Liste von Skalierungsrichtlinien für diesen Dienst. |
| --stand-by-replica-keep | Die maximale Dauer (in Sekunden), für die StandBy-Replikate beibehalten werden, bevor sie entfernt werden. Dies gilt nur für zustandsbehaftete Dienste. |
| --stateful | Gibt an, dass der Zieldienst ein zustandsbehafteter Dienst ist. |
| --stateless | Gibt an, dass der Zieldienst ein zustandsloser Dienst ist. |
| --target-replica-set-size | Die Zielgröße der Replikatgruppe als Zahl. Dies gilt nur für zustandsbehaftete Dienste. |
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
