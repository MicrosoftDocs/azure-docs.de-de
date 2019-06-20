---
title: In Azure Functions unterstützte Sprachen
description: Erfahren Sie, welche Sprachen für allgemeine Verfügbarkeit und welche experimentell und in Vorschauversionen unterstützt werden.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/02/2018
ms.author: glenga
ms.openlocfilehash: 8839fc0fb9f19a1cfa95a4191213503dba7602c6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148824"
---
# <a name="supported-languages-in-azure-functions"></a>In Azure Functions unterstützte Sprachen

In diesem Artikel werden die Unterstützungsebenen für Sprachen erläutert, die Sie mit Azure Functions verwenden können.

## <a name="levels-of-support"></a>Unterstützungsebenen

Es gibt drei Unterstützungsebenen:

* **Allgemein verfügbar (generally available, GA)** : Vollständige Unterstützung und Freigabe für die Verwendung in Produktionsumgebungen.
* **Vorschau**: Noch nicht unterstützt, die Erreichung der allgemeinen Verfügbarkeit wird aber für die Zukunft erwartet.
* **Experimentell**: Nicht unterstützt und möglicherweise in der Zukunft wieder aufgegeben; die eventuelle Erreichung des Vorschaustatus oder der allgemeinen Verfügbarkeit ist nicht garantiert.

## <a name="languages-in-runtime-1x-and-2x"></a>Sprachen in der Runtime 1.x und 2.x

Es sind [zwei Versionen der Azure Functions-Runtime](functions-versions.md) verfügbar. Die folgende Tabelle gibt an, welche Sprachen in den beiden Runtimeversionen unterstützt werden.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Experimentelle Sprachen

Die experimentellen Sprachen in Version 1.x sind nicht gut skalierbar und unterstützen nicht alle Bindungen.

Verwenden Sie keine experimentellen Funktionen für Bereiche, die für Sie sehr wichtig sind, da es dafür keinen offiziellen Support gibt. Für Probleme mit experimentellen Sprachen sollten keine Supportfälle erstellt werden. 

Für Version 2.x der Runtime werden keine experimentellen Sprachen unterstützt. Die Unterstützung für neue Sprachen wird nur hinzugefügt, wenn die Sprache in der Produktion unterstützt werden kann. 

### <a name="language-extensibility"></a>Spracherweiterbarkeit

Die 2.x-Runtime ist für [Spracherweiterbarkeit](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility) ausgelegt. Die Sprachen JavaScript und Java in der Runtime 2.x verfügen über diese Erweiterbarkeit.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Verwenden einer der Sprachen in allgemeiner Verfügbarkeit oder im Vorschaustatus in Azure Functions finden Sie in den folgenden Ressourcen:

> [!div class="nextstepaction"]
> [C#](functions-reference-csharp.md)

> [!div class="nextstepaction"]
> [F#](functions-reference-fsharp.md)

> [!div class="nextstepaction"]
> [JavaScript](functions-reference-node.md)

> [!div class="nextstepaction"]
> [Java](functions-reference-java.md)

> [!div class="nextstepaction"]
> [Python](functions-reference-python.md)
