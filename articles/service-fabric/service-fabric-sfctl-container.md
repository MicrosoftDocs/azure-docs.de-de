---
title: Azure Service Fabric-CLI – sfctl container | Microsoft-Dokumentation
description: Beschreibt die sfctl-Befehle der Service Fabric-Befehlszeilenschnittstelle (Command Line Interface, CLI) für Container.
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
ms.openlocfilehash: a5037c535737946a50d8af6fa60d0815120276d9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837309"
---
# <a name="sfctl-container"></a>sfctl container
Führt containerbezogene Befehle auf einem Clusterknoten aus.

## <a name="commands"></a>Befehle

|Get-Help|BESCHREIBUNG|
| --- | --- |
| invoke-api | Ruft die Container-API für einen Container auf, der auf einem Service Fabric-Knoten für das jeweilige Codepaket bereitgestellt wurde. |
| logs | Ruft die Containerprotokolle für Container ab, die auf einem Service Fabric-Knoten für das jeweilige Codepaket bereitgestellt wurden. |

## <a name="sfctl-container-invoke-api"></a>sfctl container invoke-api
Ruft die Container-API für einen Container auf, der auf einem Service Fabric-Knoten für das jeweilige Codepaket bereitgestellt wurde.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id           [erforderlich] | Die Identität (ID) der Anwendung. <br><br> Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
| --code-package-instance-id [erforderlich] | Eine ID, die eine Codepaketinstanz eindeutig identifiziert, die auf einem Service Fabric-Knoten bereitgestellt wird. <br><br> Kann über „service code-package-list“ abgerufen werden. |
| --code-package-name        [erforderlich] | Der Name des Codepakets, das im Dienstmanifest angegeben ist, das als Bestandteil eines Anwendungstyps in einem Service Fabric-Cluster registriert ist. |
| --container-api-uri-path   [erforderlich] | Pfad zum URI der Container-REST-API: Verwenden Sie „{id}“ anstatt des Namens bzw. der ID des Containers. |
| --node-name [erforderlich] | Der Name des Knotens. |
| --service-manifest-name [erforderlich] | Der Name eines Dienstmanifests, das als Bestandteil eines Anwendungstyps in einem Service Fabric-Cluster registriert ist. |
| --container-api-body | HTTP-Anforderungstext für die Container-REST-API. |
| --container-api-content-type | Inhaltstyp der Container-REST-API, Standardwert ist „application/json“. |
| --container-api-http-verb | HTTP-Verb für die Container-REST-API, Standardwert ist GET. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-container-logs"></a>sfctl container logs
Ruft die Containerprotokolle für Container ab, die auf einem Service Fabric-Knoten für das jeweilige Codepaket bereitgestellt wurden.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --application-id           [erforderlich] | Die Identität (ID) der Anwendung. <br><br> Dies ist üblicherweise der vollständige Name der Anwendung ohne das URI-Schema „fabric\:“. Ab Version 6.0 wird für hierarchische Namen das Zeichen „\~“ als Trennzeichen verwendet. Hat eine Anwendung beispielsweise den Namen „fabric\:/meineapp/app1“, weist die Anwendungsidentität in 6.0 und höher den Wert „meineapp\~app1“ und in früheren Versionen den Wert „meineapp/app1“ auf. |
| --code-package-instance-id [erforderlich] | Instanz-ID des Codepakets, die mit „service code-package-list“ abgerufen werden kann. |
| --code-package-name        [erforderlich] | Der Name des Codepakets, das im Dienstmanifest angegeben ist, das als Bestandteil eines Anwendungstyps in einem Service Fabric-Cluster registriert ist. |
| --node-name [erforderlich] | Der Name des Knotens. |
| --service-manifest-name [erforderlich] | Der Name eines Dienstmanifests, das als Bestandteil eines Anwendungstyps in einem Service Fabric-Cluster registriert ist. |
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


## <a name="next-steps"></a>Nächste Schritte
- [Einrichten](service-fabric-cli.md) der Service Fabric-Befehlszeilenschnittstelle
- Informationen zum Verwenden der Service Fabric-Befehlszeilenschnittstelle mit den [Beispielskripts](/azure/service-fabric/scripts/sfctl-upgrade-application)