---
title: Referenz zur Standardregel-Engine für Azure CDN | Microsoft-Dokumentation
description: Referenzdokumentation für Übereinstimmungsbedingungen und Aktionen in der Standardregel-Engine für Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: 1a0f4456f38939632026645500dd48acbf7dbc88
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93242207"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Referenz zur Standardregel-Engine für Azure CDN

In der [Standardregel-Engine](cdn-standard-rules-engine.md) für Azure Content Delivery Network (Azure CDN) besteht eine Regel aus mindestens einer Übereinstimmungsbedingung und einer Aktion. In diesem Artikel finden Sie ausführliche Beschreibungen der Übereinstimmungsbedingungen und Features, die in der Standardregel-Engine für Azure CDN verfügbar sind.

Die Regel-Engine fungiert als endgültige Autorität dafür, wie bestimmte Typen von Anforderungen von Azure CDN Standard verarbeitet werden.

**Allgemeine Anwendungsfälle für die Regeln:**

- Außerkraftsetzen oder Definieren einer benutzerdefinierten Cacherichtlinie
- Umleiten von Anforderungen
- Anpassen von HTTP-Anforderungen und Antwortheadern

## <a name="terminology"></a>Begriff

Legen Sie [Übereinstimmungsbedingungen](cdn-standard-rules-engine-match-conditions.md) und [Aktionen](cdn-standard-rules-engine-actions.md) fest, um eine Regel in der Regel-Engine zu definieren:

 ![Azure CDN-Regelstruktur](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

Jede Regel kann bis zu zehn Übereinstimmungsbedingungen und fünf Aktionen aufweisen. Jeder Azure CDN-Endpunkt kann bis zu 25 Regeln enthalten. 

Dieser Grenzwert schließt eine *globale Standardregel* mit ein. Die globale Regel verfügt über keine Übereinstimmungsbedingungen. Die in einer globalen Regel definierten Aktionen werden immer ausgelöst.

   > [!IMPORTANT]
   > Die Reihenfolge, in der mehrere Regeln aufgelistet sind, beeinflusst die Verarbeitung der Regeln. Die in einer Regel angegebenen Aktionen werden möglicherweise durch eine nachfolgende Regel überschrieben.

## <a name="limits-and-pricing"></a>Limits und Preise 

Jeder Azure CDN-Endpunkt kann bis zu 25 Regeln enthalten. Jede Regel kann bis zu zehn Übereinstimmungsbedingungen und fünf Aktionen aufweisen. Die Preise für die Regel-Engine folgen den folgenden Dimensionen: 
- Regeln: 1 US-Dollar pro Regel und Monat 
- Verarbeitete Anforderungen: 0,60 US-Dollar pro Million Anforderungen
- Die ersten fünf Regeln bleiben weiterhin kostenlos.

## <a name="syntax"></a>Syntax

Wie Sonderzeichen in einer Regel behandelt werden, hängt davon ab, wie verschiedene Übereinstimmungsbedingungen und Aktionen Textwerte verarbeiten. Eine Übereinstimmungsbedingung oder Aktion kann Text auf folgende Weisen interpretieren:

- [Literalwerte](#literal-values)
- [Platzhalterwerte](#wildcard-values)


### <a name="literal-values"></a>Literalwerte

Text, der als Literalwert interpretiert wird, behandelt alle Sonderzeichen ( *mit Ausnahme des Zeichens „%“* ) als Teil des Werts, für den eine Übereinstimmung in der Regel gesucht wird. Eine literale Übereinstimmungsbedingung, die auf `'*'` festgelegt ist, wird nur erfüllt, wenn der exakte Wert `'*'` ermittelt wird.

Ein Prozentzeichen wird zum Angeben der URL-Codierung verwendet (z. B. `%20`).

### <a name="wildcard-values"></a>Platzhalterwerte

Zurzeit wird in der Standardregel-Engine das Platzhalterzeichen in der **UrlPath-Übereinstimmungsbedingung** unterstützt. Das Zeichen \* ist ein Platzhalter, der für ein oder mehrere Zeichen steht. 

## <a name="next-steps"></a>Nächste Schritte

- [Übereinstimmungsbedingungen in der Standardregel-Engine](cdn-standard-rules-engine-match-conditions.md)
- [Aktionen in der Standardregel-Engine](cdn-standard-rules-engine-actions.md)
- [Erzwingen von HTTPS mithilfe der Standardregel-Engine](cdn-standard-rules-engine.md)
- [Übersicht über das Azure Content Delivery Network](cdn-overview.md)
