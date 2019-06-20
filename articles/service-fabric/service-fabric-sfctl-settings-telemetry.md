---
title: Azure Service Fabric-CLI – sfctl settings telemetry | Microsoft-Dokumentation
description: Beschreibt die sfctl settings telemetry-Befehle der Service Fabric-CLI.
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
ms.openlocfilehash: 42a82ab0be37f260a48a1da6cecab5120c24d293
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60556337"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Konfiguriert Telemetrieeinstellungen, die für diese Instanz von sfctl lokal sind.

sfctl telemetry erfasst den Befehlsnamen ohne angegebene Parameter oder deren Werte, sfctl-Version, Betriebssystemtyp, Python-Version, den Erfolg oder Fehler des Befehls oder die zurückgegebene Fehlermeldung.

## <a name="commands"></a>Befehle

|Get-Help|BESCHREIBUNG|
| --- | --- |
| set-telemetry | Aktiviert oder deaktiviert die Telemetrie. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>sfctl settings telemetry set-telemetry
Aktiviert oder deaktiviert die Telemetrie.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --off | Deaktiviert die Telemetrie. |
| --on | Aktiviert die Telemetrie. Dies ist der Standardwert. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

### <a name="examples"></a>Beispiele

Deaktiviert die Telemetrie.

```
sfctl settings telemetry set_telemetry --off
```

Aktiviert die Telemetrie.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Nächste Schritte
- [Einrichten](service-fabric-cli.md) der Service Fabric-Befehlszeilenschnittstelle
- Informationen zum Verwenden der Service Fabric-Befehlszeilenschnittstelle mit den [Beispielskripts](/azure/service-fabric/scripts/sfctl-upgrade-application)