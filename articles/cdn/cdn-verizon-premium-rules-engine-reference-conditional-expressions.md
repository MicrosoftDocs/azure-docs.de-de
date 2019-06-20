---
title: Bedingte Ausdrücke der Regel-Engine für Azure CDN Premium von Verizon | Microsoft-Dokumentation
description: Referenzdokumentation zu den Übereinstimmungsbedingungen und Features der Regel-Engine für Azure CDN Premium von Verizon.
services: cdn
author: mdgattuso
ms.service: cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: c6e49e6fbc0c541ce9a8cd903eb313d61413257c
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482012"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Bedingte Ausdrücke der Regel-Engine für Azure CDN Premium von Verizon

Dieser Artikel enthält ausführliche Beschreibungen der bedingten Ausdrücke für die [Regel-Engine](cdn-verizon-premium-rules-engine.md) von Azure Content Delivery Network (CDN).

Der erste Teil einer Regel ist der bedingte Ausdruck.

Bedingter Ausdruck | BESCHREIBUNG
-----------------------|-------------
IF | Ein IF-Ausdruck ist immer Teil der ersten Anweisung einer Regel. Wie alle anderen bedingten Ausdrücke auch, muss diese IF-Anweisung einer Übereinstimmung zugeordnet sein. Wenn keine zusätzlichen bedingten Ausdrücke definiert sind, wird mit dieser Übereinstimmung das Kriterium ermittelt, das erfüllt sein muss, bevor bestimmte Features auf eine Anforderung angewendet werden können.
AND IF | Ein AND IF-Ausdruck kann nur nach den folgenden Arten von bedingten Ausdrücken hinzugefügt werden: IF, AND IF. Hiermit wird angegeben, dass eine weitere Bedingung vorhanden ist, die für die erste IF-Anweisung erfüllt sein muss.
ELSE IF| Mit einem ELSE IF-Ausdruck wird eine alternative Bedingung angegeben, die erfüllt sein muss, bevor ein spezifischer Funktionssatz für diese ELSE IF-Anweisung ausgeführt werden kann. Durch das Vorhandensein einer ELSE IF-Anweisung wird das Ende der vorherigen Anweisung angegeben. Der einzige bedingte Ausdruck, der nach einer ELSE IF-Anweisung stehen kann, ist eine weitere ELSE IF-Anweisung. Dies bedeutet, dass eine ELSE IF-Anweisung nur verwendet werden kann, um eine einzelne zusätzliche Bedingung anzugeben, die erfüllt sein muss.

**Beispiel**: ![CDN-Übereinstimmungsbedingung](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Eine Regel kann die von einer vorherigen Regel angegebenen Aktionen überschreiben.
   > Beispiel: Mit einer Regel vom Typ „catch-all“ werden alle Anforderungen per tokenbasierter Authentifizierung geschützt. Eine weitere Regel kann direkt darunter erstellt werden, um eine Ausnahme für bestimmte Arten von Anforderungen festzulegen.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über das Azure Content Delivery Network](cdn-overview.md)
- [Regel-Engine – Referenz](cdn-verizon-premium-rules-engine-reference.md)
- [Übereinstimmungsbedingungen der Regel-Engine](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Regel-Engine – Features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Überschreiben des HTTP-Standardverhaltens mithilfe der Regel-Engine](cdn-verizon-premium-rules-engine.md)