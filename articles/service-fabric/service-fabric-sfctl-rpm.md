---
title: Azure Service Fabric CLI – sfctl rpm | Microsoft-Dokumentation
description: Beschreibt die sfctl rpm-Befehle der Service Fabric-Befehlszeilenschnittstelle (Command Line Interface, CLI).
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
ms.openlocfilehash: 04080d75042bfa8a07533336936165e0abef051b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60556354"
---
# <a name="sfctl-rpm"></a>sfctl rpm
Ermöglicht es, Befehle an den Reparatur-Manager-Dienst abzufragen und zu senden.

## <a name="commands"></a>Befehle

|Get-Help|BESCHREIBUNG|
| --- | --- |
| approve-force | Erzwingt die Genehmigung das angegebenen Reparaturtasks. |
| delete | Löscht einen abgeschlossenen Reparaturtask. |
| list | Ruft eine Liste der Reparaturtasks ab, die dem angegebenen Filter entsprechen. |

## <a name="sfctl-rpm-approve-force"></a>sfctl rpm approve-force
Erzwingt die Genehmigung das angegebenen Reparaturtasks.

Diese API unterstützt die Service Fabric-Plattform. Sie ist nicht dazu vorgesehen, direkt aus Ihrem Code verwendet zu werden.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --task-id [erforderlich] | Die ID des Reparaturtasks. |
| --version | Die aktuelle Versionsnummer des Reparaturtasks. Ist der Wert ungleich null, ist die Anforderung nur dann erfolgreich, wenn dieser Wert mit der tatsächlichen aktuellen Version des Reparaturtasks übereinstimmt. Ist der Wert gleich null, wird keine Versionsüberprüfung ausgeführt. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-rpm-delete"></a>sfctl rpm delete
Löscht einen abgeschlossenen Reparaturtask.

Diese API unterstützt die Service Fabric-Plattform. Sie ist nicht dazu vorgesehen, direkt aus Ihrem Code verwendet zu werden.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --task-id [erforderlich] | Die ID des abgeschlossenen Reparaturtasks, der gelöscht werden soll. |
| --version | Die aktuelle Versionsnummer des Reparaturtasks. Ist der Wert ungleich null, ist die Anforderung nur dann erfolgreich, wenn dieser Wert mit der tatsächlichen aktuellen Version des Reparaturtasks übereinstimmt. Ist der Wert gleich null, wird keine Versionsüberprüfung ausgeführt. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-rpm-list"></a>sfctl rpm list
Ruft eine Liste der Reparaturtasks ab, die dem angegebenen Filter entsprechen.

Diese API unterstützt die Service Fabric-Plattform. Sie ist nicht dazu vorgesehen, direkt aus Ihrem Code verwendet zu werden.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --executor-filter | Der Name des Reparaturexecutors, dessen beanspruchte Tasks in der Liste enthalten sein sollen. |
| --state-filter | Eine bitweise OR-Operation für die folgenden Werte, die angibt, welche Taskstatus in der Ergebnisliste enthalten sein sollen. <br> 1 – „Created“ <br>2 – „Claimed“  <br>4 – „Preparing“  <br>8 – „Approved“  <br>16 – „Executing“  <br>32 – „Restoring“  <br>64 – „Completed“ |
| --task-id-filter | Das Reparaturtask-ID-Präfix, das abgeglichen werden soll. |

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