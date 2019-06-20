---
title: 'Azure Service Fabric CLI: sfctl mesh network | Microsoft-Dokumentation'
description: Beschreibt die sfctl mesh network-Befehle der Service Fabric CLI.
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
ms.openlocfilehash: feec5c4796c025c1707b4eb93bfe34b8d384ef3a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60694520"
---
# <a name="sfctl-mesh-network"></a>sfctl mesh network
Ruft die Mesh-Netzwerkressourcen ab bzw. löscht diese.

## <a name="commands"></a>Befehle

|Get-Help|BESCHREIBUNG|
| --- | --- |
| delete | Löscht die Netzwerkressource. |
| list | Listet alle Netzwerkressourcen auf. |
| show | Ruft die Netzwerkressource mit dem angegebenen Namen ab. |

## <a name="sfctl-mesh-network-delete"></a>sfctl mesh network delete
Löscht die Netzwerkressource.

Löscht die Netzwerkressource, die durch den Namen identifiziert wird.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --name -n [Erforderlich] | Der Name des Netzwerks. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-mesh-network-list"></a>sfctl mesh network list
Listet alle Netzwerkressourcen auf.

Ruft die Informationen zu allen Netzwerkressourcen in einer bestimmten Ressourcengruppe ab. Die Informationen umfassen die Beschreibung und andere Eigenschaften des Netzwerks.

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-mesh-network-show"></a>sfctl mesh network show
Ruft die Netzwerkressource mit dem angegebenen Namen ab.

Ruft die Informationen zur Netzwerkressource mit dem angegebenen Namen ab. Die Informationen umfassen die Beschreibung und andere Eigenschaften des Netzwerks.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --name -n [Erforderlich] | Der Name des Netzwerks. |

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