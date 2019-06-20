---
title: Azure Service Fabric-CLI – sfctl property | Microsoft-Dokumentation
description: Beschreibt die sfctl property-Befehle der Service Fabric-CLI.
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
ms.openlocfilehash: 54cb9f604e9d1b817947990e657390387df6c881
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60556590"
---
# <a name="sfctl-property"></a>sfctl property
Speichern und Abfragen von Eigenschaften unter Service Fabric-Namen.

## <a name="commands"></a>Befehle

|Get-Help|BESCHREIBUNG|
| --- | --- |
| delete | Löscht die angegebene Service Fabric-Eigenschaft. |
| get | Ruft die angegebene Service Fabric-Eigenschaft ab. |
| list | Ruft Informationen zu allen Service Fabric-Eigenschaften unter einem bestimmten Namen ab. |
| put | Erstellt oder aktualisiert eine Service Fabric-Eigenschaft. |

## <a name="sfctl-property-delete"></a>sfctl property delete
Löscht die angegebene Service Fabric-Eigenschaft.

Löscht die angegebene Service Fabric-Eigenschaft unter einem bestimmten Namen. Eine Eigenschaft muss erstellt worden sein, bevor sie gelöscht werden kann.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --name-id [erforderlich] | Der Service Fabric-Name ohne das URI-Schema „fabric\:“. |
| --property-name [erforderlich] | Gibt den Namen der abzurufenden Eigenschaft ab. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-property-get"></a>sfctl property get
Ruft die angegebene Service Fabric-Eigenschaft ab.

Ruft die angegebene Service Fabric-Eigenschaft unter einem bestimmten Namen ab. Dieser Befehl gibt immer einen Wert und Metadaten zurück.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --name-id [erforderlich] | Der Service Fabric-Name ohne das URI-Schema „fabric\:“. |
| --property-name [erforderlich] | Gibt den Namen der abzurufenden Eigenschaft ab. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-property-list"></a>sfctl property list
Ruft Informationen zu allen Service Fabric-Eigenschaften unter einem bestimmten Namen ab.

Ein Service Fabric-Name kann eine oder mehrere benannte Eigenschaften haben, in denen benutzerdefinierten Informationen gespeichert sind. Dieser Vorgang ruft die Informationen zu diesen Eigenschaften in einer seitenbasierte Liste ab. Zu diesen Informationen zählen Name, Wert und Metadaten der einzelnen Eigenschaften.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --name-id [erforderlich] | Der Service Fabric-Name ohne das URI-Schema „fabric\:“. |
| --continuation-token | Der Parameter „continuation-token“ (Fortsetzungstoken) wird dazu verwendet, den nächsten Satz von Ergebnissen abzurufen. Ein Fortsetzungstoken mit einem nicht leeren Wert wird in die Antwort der API eingefügt, wenn die Ergebnisse aus dem System nicht in eine einzige Antwort passen. Wird dieser Wert an den nächsten API-Aufruf übergeben, gibt die API den nächsten Satz von Ergebnissen zurück. Gibt es keine weiteren Ergebnisse, enthält das Fortsetzungstoken keinen Wert. Der Wert dieses Parameters darf nicht als URL codiert sein. |
| --include-values | Ermöglicht die Angabe, ob die Werte der zurückgegebenen Eigenschaften eingeschlossen werden sollen. Mit „true“ wird festgelegt, dass die Werte mit den Metadaten zurückgegeben werden sollen, während „false“ angibt, dass nur Eigenschaftsmetadaten zurückgeben werden sollen. |
| --timeout -t | Servertimeout in Sekunden.  Standardwert\: 60. |

### <a name="global-arguments"></a>Globale Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --debug | Erhöht die Protokollierungsausführlichkeit, sodass alle Debugprotokolle angezeigt werden. |
| --help -h | Zeigt diese Hilfemeldung an und beendet. |
| --output -o | Das Ausgabeformat.  Zulässige Werte\: „json“, „jsonc“, „table“, „tsv“.  Standardwert\: „json“. |
| --query | JMESPath-Abfragezeichenfolge. Weitere Informationen und Beispiele finden Sie unter „http\://jmespath.org/“. |
| --verbose | Erhöht die Protokollierungsausführlichkeit. Verwenden Sie „--debug“, wenn Sie vollständige Debugprotokolle wünschen. |

## <a name="sfctl-property-put"></a>sfctl property put
Erstellt oder aktualisiert eine Service Fabric-Eigenschaft.

Erstellt oder aktualisiert die angegebene Service Fabric-Eigenschaft unter einem bestimmten Namen.

### <a name="arguments"></a>Argumente

|Argument|BESCHREIBUNG|
| --- | --- |
| --name-id [erforderlich] | Der Service Fabric-Name ohne das URI-Schema „fabric\:“. |
| --property-name [erforderlich] | Der Name der Service Fabric-Eigenschaft. |
| --value [erforderlich] | Beschreibt einen Service Fabric-Eigenschaftswert. Hierbei handelt es sich um eine JSON-Zeichenfolge. <br><br> Die JSON-Zeichenfolge verfügt über zwei Felder, die die Werte „Kind“ und „Value“ der Daten angeben. Der Wert „Kind“ muss als erstes Element in der JSON-Zeichenfolge angezeigt werden und kann die Werte „Binary“, „Int64“, „Double“, „String“ und „Guid“ enthalten. Der Wert sollte für-die angegebenen Typen serialisiert werden können. Die Werte „Kind“ und „Value“ der Daten sollten als Zeichenfolgen angegeben werden. |
| --custom-id-type | Die benutzerdefinierte Typ-ID der Eigenschaft. Mithilfe dieser Eigenschaft kann der Benutzer den Typ des Eigenschaftswerts kennzeichnen. |
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