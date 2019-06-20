---
title: 'Azure Service Fabric CLI: sfctl mesh secret| Microsoft-Dokumentation'
description: Beschreibt die sfctl mesh secret-Befehle der Service Fabric CLI.
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
ms.openlocfilehash: acc5862a8794da4fde991025eacfcccf6e16916a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60607681"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh secret
Ruft die Mesh-Geheimnisressourcen ab bzw. löscht diese.

## <a name="commands"></a>Befehle

|Get-Help|BESCHREIBUNG|
| --- | --- |
| delete | Löscht die Geheimnisressource. |
| list | Listet alle Geheimnisressourcen auf. |
| show | Ruft die Geheimnisressource mit dem angegebenen Namen ab. |

## <a name="sfctl-mesh-secret-delete"></a>sfctl mesh secret delete
Löscht die Geheimnisressource.

Löscht die angegebene Geheimnisressource und alle ihre benannten Werte.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --name -n [Erforderlich] | Der Name des Geheimnisses. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-mesh-secret-list"></a>sfctl mesh secret list
Listet alle Geheimnisressourcen auf.

Ruft die Informationen zu allen Geheimnisressourcen in einer bestimmten Ressourcengruppe ab. Die Informationen umfassen die Beschreibung und andere Eigenschaften des Geheimnisses.

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-mesh-secret-show"></a>sfctl mesh secret show
Ruft die Geheimnisressource mit dem angegebenen Namen ab.

Ruft die Informationen zur Geheimnisressource mit dem angegebenen Namen ab. Die Informationen umfassen die Beschreibung und andere Eigenschaften des Geheimnisses.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --name -n [Erforderlich] | Der Name des Geheimnisses. |

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