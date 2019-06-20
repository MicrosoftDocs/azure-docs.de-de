---
author: vhorne
ms.service: dns
ms.topic: include
ms.date: 11/25/2018
ms.author: victorh
ms.openlocfilehash: 9cc650cea17acb8d89933c819c4ca60e2c459d1c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178722"
---
SPF-Einträge (Sender Policy Framework) werden verwendet, um anzugeben, welche E-Mail-Server E-Mails im Namen eines Domänennamens senden können. Es ist wichtig, SPF-Einträge korrekt zu konfigurieren, um zu verhindern, dass Empfänger Ihre E-Mail als „Junk“ markieren.

Mit den DNS RFCs wurde ursprünglich der neue Eintragstyp SPF eingeführt, um dieses Szenario zu unterstützen. Außerdem wurde die Verwendung des Eintragstyps TXT zum Angeben von SPF-Einträgen zugelassen, um ältere Namenserver zu unterstützen. Diese Mehrdeutigkeit verursachte Verwirrung, die durch [RFC 7208](http://tools.ietf.org/html/rfc7208#section-3.1) behoben wurde. Darin ist angegeben, dass SPF-Datensätze mit dem Eintragstyp TXT erstellt werden müssen. Außerdem ist der Eintragstyp SPF als veraltet angegeben.

**SPF-Einträge werden von Azure DNS unterstützt und müssen mit dem Eintragstyp TXT erstellt werden.** Der veraltete Eintragstyp SPF wird nicht unterstützt. Wenn Sie [eine DNS-Zonendatei importieren](../articles/dns/dns-import-export.md), werden alle SPF-Einträge, für die der Eintragstyp SPF verwendet wird, in den Eintragstyp TXT konvertiert.
