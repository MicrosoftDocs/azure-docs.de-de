---
title: 'Azure Service Fabric CLI: sfctl mesh code-package-log | Microsoft-Dokumentation'
description: Beschreibt die sfctl mesh code-package-log-Befehle der Service Fabric CLI.
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
ms.openlocfilehash: e7bc8491071946eaa2e322517e5d36d681a49130
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837639"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Ruft die Protokolle für den Container des angegebenen Codepakets für das angegebene Dienstreplikat ab.

## <a name="commands"></a>Befehle

|Get-Help|BESCHREIBUNG|
| --- | --- |
| get | Ruft die Protokolle aus dem Container ab. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
Ruft die Protokolle aus dem Container ab.

Ruft die Protokolle für den Container des angegebenen Codepakets des Dienstreplikats ab.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --app-name --application-name [erforderlich] | Der Namen der Anwendung. |
| --code-package-name           [erforderlich] | Der Name des Codepakets des Diensts. |
| --replica-name                [erforderlich] | Der Service Fabric-Replikatname. |
| --service-name                [erforderlich] | Der Name des Diensts. |
| --tail | Die Anzahl der Zeilen, die am Ende der Protokolle angezeigt werden sollen. Der Standardwert ist 100. „all“, um die vollständige Protokolle anzuzeigen. |

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