---
title: Referenz zur Azure CDN-Regel-Engine | Microsoft-Dokumentation
description: Referenzdokumentation zu den Übereinstimmungsbedingungen und Features der Azure CDN-Regel-Engine.
services: cdn
author: mdgattuso
ms.service: cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: a04fcd3eaaed5c3e43f631ad1fbb6fed93ea29fb
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482032"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Referenz zur Regel-Engine für Azure CDN Premium von Verizon

Dieser Artikel enthält ausführliche Beschreibungen der verfügbaren Übereinstimmungsbedingungen und Features für die [Regel-Engine](cdn-verizon-premium-rules-engine.md) des Azure Content Delivery Network (CDN).

Die Regel-Engine fungiert als endgültige Autorität, wie vom CDN bestimmte Typen von Anforderungen verarbeitet werden.

**Allgemeine Zwecke**:

- Außerkraftsetzen oder Definieren einer benutzerdefinierten Cacherichtlinie
- Schützen oder Ablehnen von Anforderungen vertraulicher Inhalte
- Umleiten von Anforderungen
- Speichern benutzerdefinierter Protokolldaten

## <a name="terminology"></a>Begriff

Eine Regel wird mithilfe von [**bedingten Ausdrücken**](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md), [**Übereinstimmungsbedingungen**](cdn-verizon-premium-rules-engine-reference-match-conditions.md) und [**Features**](cdn-verizon-premium-rules-engine-reference-features.md) definiert. Diese Elemente sind in der folgenden Abbildung hervorgehoben:

 ![CDN-Übereinstimmungsbedingung](./media/cdn-rules-engine-reference/cdn-rules-engine-terminology.png)

## <a name="syntax"></a>Syntax

Die Art und Weise, in der Sonderzeichen behandelt werden, hängt davon ab, wie eine Übereinstimmungsbedingung Textwerte behandelt. Eine Übereinstimmungsbedingung (bzw. Feature) kann Text interpretieren als:

1. [**Literalwerte**](#literal-values)
2. [**Platzhalterwerte**](#wildcard-values)
3. [**Reguläre Ausdrücke**](#regular-expressions)

### <a name="literal-values"></a>Literalwerte

Text, der als Literalwert interpretiert wird, behandelt alle Sonderzeichen (mit Ausnahme des Zeichens „%“) als Teil des Werts, für den eine Übereinstimmung gefunden werden muss. Eine Literalübereinstimmungsbedingung, die auf `\'*'\` festgelegt ist, wird demnach nur erfüllt, wenn exakt dieser Wert (d.h. `\'*'\`) gefunden wird.

Ein Prozentzeichen wird zum Angeben der URL-Codierung verwendet (z.B. `%20`).

### <a name="wildcard-values"></a>Platzhalterwerte

Text, der als Platzhalterwert interpretiert wird, weist Sonderzeichen eine zusätzliche Bedeutung zu. In der folgenden Tabelle wird beschrieben, wie die folgende Gruppe von Zeichen interpretiert wird:

Zeichen | BESCHREIBUNG
----------|------------
\ | Ein umgekehrter Schrägstrich wird als Escapezeichen für die in dieser Tabelle angegebenen Zeichen verwendet. Direkt vor dem Sonderzeichen, das mit Escapezeichen versehen werden soll, muss ein umgekehrter Schrägstrich eingegeben werden.<br/>Es folgt als Beispiel die Syntax für das Escapezeichen eines Sternchens: `\*`
% | Ein Prozentzeichen wird zum Angeben der URL-Codierung verwendet (z.B. `%20`).
\* | Ein Sternchen ist ein Platzhalter, der ein oder mehrere Zeichen darstellt.
Leerzeichen | Ein Leerzeichen gibt an, dass eine Übereinstimmungsbedingung entweder mit den angegebenen Werten oder Mustern erfüllt werden kann.
'Wert' | Ein einfaches Anführungszeichen hat keine besondere Bedeutung. Jedoch wird eine Gruppe einfacher Anführungszeichen verwendet, um anzugeben, dass ein Wert als Literalwert behandelt werden soll. Es kann auf folgende Weisen verwendet werden:<br><br/>– Es ermöglicht das Erfüllen einer Übereinstimmungsbedingung, sobald der angegebene Wert mit einem beliebigen Teil des Vergleichswerts übereinstimmt.  Beispielsweise stimmt `'ma'` mit beliebigen der folgenden Zeichenfolgen überein: <br/><br/>/business/**ma**rathon/asset.htm<br/>**ma**p.gif<br/>/business/template.**ma**p<br /><br />– Es ermöglicht die Angabe eines Sonderzeichens als Literalzeichen. Sie können z.B. ein literales Leerzeichen angeben, indem Sie ein Leerzeichen in einfache Anführungszeichen setzen (d.h. `' '` oder `'sample value'`).<br/>– Es ermöglicht die Angabe eines leeren Werts. Geben Sie einen leeren Wert an, indem Sie zwei einfache Anführungszeichen eingeben (d.h. '').<br /><br/>**Wichtig:**<br/>– Wenn der angegebene Wert keinen Platzhalter enthält, wird er automatisch als Literalwert behandelt, d.h., es müssen nicht zwei einfache Anführungszeichen angegeben werden.<br/>– Wenn ein umgekehrter Schrägstrich nicht als Escapezeichen für ein anderes Zeichen in dieser Tabelle fungiert, wird er ignoriert, sofern er zwischen zwei einfachen Anführungszeichen angegeben wird.<br/>– Eine andere Möglichkeit zum Angeben eines Sonderzeichens als Literalzeichen besteht darin, es mithilfe eines umgekehrten Schrägstrichs (d.h. `\`) mit einem Escapezeichen zu versehen.

### <a name="regular-expressions"></a>Reguläre Ausdrücke

Reguläre Ausdrücke definieren ein Muster, das in einem Textwert gesucht wird. Die Notation regulärer Ausdrücke definiert spezifische Bedeutungen für eine Vielzahl von Symbolen. Die folgende Tabelle zeigt, wie Sonderzeichen von Übereinstimmungsbedingungen behandelt werden, die reguläre Ausdrücke unterstützen.

Sonderzeichen | BESCHREIBUNG
------------------|------------
\ | Ein umgekehrter Schrägstrich als Escapezeichen für das nach diesem folgende Zeichen bewirkt, dass das Zeichen als Literalwert behandelt wird, anstatt seine Bedeutung als regulärer Ausdruck zu haben. Es folgt als Beispiel die Syntax für das Escapezeichen eines Sternchens: `\*`
% | Die Bedeutung eines Prozentzeichens hängt von seiner Verwendung ab.<br/><br/> `%{HTTPVariable}`: Diese Syntax gibt eine HTTP-Variable an.<br/>`%{HTTPVariable%Pattern}`: Diese Syntax verwendet ein Prozentzeichen, um eine HTTP-Variable und ein Trennzeichen anzugeben.<br />`\%`: Wenn ein Prozentzeichen mit einem Escapezeichen versehen wird, kann es als Literalwert verwendet werden oder die URL-Codierung angeben (z. B. `\%20`).
\* | Ein Sternchen ermöglicht, dass für das vorherige Zeichen keine oder mehrere Übereinstimmungen gefunden werden.
Leerzeichen | Ein Leerzeichen wird in der Regel als Literalzeichen behandelt.
'Wert' | Einfache Anführungszeichen werden als Literalzeichen behandelt. Eine Gruppe einfacher Anführungszeichen hat keine besondere Bedeutung.

## <a name="next-steps"></a>Nächste Schritte

- [Übereinstimmungsbedingungen der Regel-Engine](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Regel-Engine – bedingte Ausdrücke](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Regel-Engine – Features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Überschreiben des HTTP-Verhaltens mithilfe der Regel-Engine](cdn-verizon-premium-rules-engine.md)
- [Übersicht über das Azure Content Delivery Network](cdn-overview.md)