---
title: Azure Front Door – Unterstützung für Platzhalterdomänen
description: In diesem Artikel erfahren Sie, wie Azure Front Door die Zuordnung und Verwaltung von Platzhalterdomänen in der Liste benutzerdefinierter Domänen unterstützt.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: 18504f1ed4200889b20c9608c9c0ad2c13c9aaa5
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425761"
---
# <a name="wildcard-domains"></a>Platzhalterdomänen

Neben Apex-Domänen und Unterdomänen können Sie Ihren Front-End-Hosts oder benutzerdefinierten Domänen für Ihr Azure Front Door-Profil auch eine Platzhalterdomäne zuordnen. Platzhalterdomänen in der Azure Front Door-Konfiguration vereinfachen das Routingverhalten von Datenverkehr für mehrere Unterdomänen für eine API, Anwendung oder Website mit derselben Routingregel. Sie müssen die Konfiguration nicht ändern, um die einzelnen Unterdomänen separat hinzuzufügen und/oder anzugeben. Beispielsweise können Sie das Routing für `customer1.contoso.com`, `customer2.contoso.com` und `customerN.contoso.com` definieren, indem Sie dieselbe Routingregel definieren und die Platzhalterdomäne `*.contoso.com` hinzufügen.

Nachfolgend sind eine Reihe wichtiger Szenarien aufgeführt, die durch die Unterstützung von Platzhalterdomänen verbessert werden:

- Es ist nicht erforderlich, ein Onboarding der einzelnen Unterdomänen in Ihrem Azure Front Door-Profil durchzuführen und anschließend HTTPS zu aktivieren, um ein Zertifikat für jede Unterdomäne zu binden.
- Wenn eine Anwendung eine neue Unterdomäne hinzufügt, müssen Sie die Azure Front Door-Produktionskonfiguration nicht mehr ändern. Zuvor war es erforderlich, die Unterdomäne hinzuzufügen, ein Zertifikat daran zu binden, eine WAF-Richtlinie (Web Application Firewall) anzufügen und dann die Domäne zu verschiedenen Routingregeln hinzuzufügen.

> [!NOTE]
> Das Hinzufügen von Platzhalterdomänen über Azure DNS wird derzeit nur über die API, PowerShell und die Azure CLI unterstützt. Das Hinzufügen und Verwalten von Platzhalterdomänen über das Azure-Portal wird nicht unterstützt.

## <a name="adding-wildcard-domains"></a>Hinzufügen von Platzhalterdomänen

Sie können Platzhalterdomänen im Abschnitt für Front-End-Hosts oder Domänen hinzufügen. Ähnlich wie bei Unterdomänen überprüft Azure Front Door, ob eine Zuordnung eines CNAME-Eintrags für Ihre Platzhalterdomäne vorhanden ist. Bei dieser DNS-Zuordnung kann es sich um eine direkte Zuordnung eines CNAME-Eintrags handeln, wie z. B. die Zuordnung von `*.contoso.com` zu `contoso.azurefd.net`. Alternativ können Sie eine temporäre afdverify-Zuordnung verwenden. Beispiel: Bei der Zuordnung von `afdverify.contoso.com` zu `afdverify.contoso.azurefd.net` wird die Zuordnung des CNAME-Eintrags für den Platzhalter überprüft.

> [!NOTE]
> Azure DNS unterstützt Platzhalterdatensätze.

Sie können beliebig viele Unterdomänen der Platzhalterdomäne (Unterdomänen mit einer einzelnen Ebene) in Front-End-Hosts hinzufügen (bis zum Limit der Front-End-Hosts). Diese Funktionalität kann für folgende Szenarien erforderlich sein:

- Definieren einer anderen Route für eine Unterdomäne als für die übrigen Domänen (aus der Platzhalterdomäne).

- Festlegen einer anderen WAF-Richtlinie für eine bestimmte Unterdomäne. `*.contoso.com` ermöglicht z. B. das Hinzufügen von `foo.contoso.com` ohne erneuten Nachweis des Domänenbesitzes. `foo.bar.contoso.com` ist jedoch nicht zulässig, da es sich nicht um eine Unterdomäne mit einzelner Ebene von `*.contoso.com` handelt. Damit Sie `foo.bar.contoso.com` ohne eine zusätzliche Überprüfung des Domänenbesitzes hinzufügen können, muss `*.bar.contosonews.com` hinzugefügt werden.

Platzhalterdomänen und zugehörige Unterdomänen können mit bestimmten Einschränkungen hinzugefügt werden:

- Wenn eine Platzhalterdomäne zu einem Azure Front Door-Profil hinzugefügt wird:
  - Die Platzhalterdomäne kann zu keinem anderen Azure Front Door-Profil hinzugefügt werden.
  - Unterdomänen der ersten Ebene der Platzhalterdomäne können keinem anderen Azure Front Door-Profil oder Azure Content Delivery Network-Profil hinzugefügt werden.
- Wenn eine Unterdomäne einer Platzhalterdomäne bereits zu einem Azure Front Door- oder Azure Content Delivery Network-Profil hinzugefügt wurde, kann die Platzhalterdomäne für kein anderes Azure Front Door-Profil verwendet werden.
- Wenn zwei Profile (Azure Front Door oder Azure Content Delivery Network) über verschiedene Unterdomänen einer Stammdomäne verfügen, können Platzhalterdomänen zu keinem der Profile hinzugefügt werden.

## <a name="certificate-binding"></a>Zertifikatbindung

Um HTTPS-Datenverkehr für Ihre Platzhalterdomäne zu akzeptieren, müssen Sie HTTPS für die Platzhalterdomäne aktivieren. Die Zertifikatbindung für eine Platzhalterdomäne erfordert ein Platzhalterzertifikat. Der Antragstellername des Zertifikats sollte also ebenfalls die Platzhalterdomäne aufweisen.

> [!NOTE]
> Derzeit ist nur die Verwendung Ihrer eigenen benutzerdefinierten SSL-Zertifikatoption zum Aktivieren von HTTPS für Platzhalterdomänen verfügbar. Von Azure Front Door verwaltete Zertifikate können nicht für Platzhalterdomänen verwendet werden.

Sie können das Platzhalterzertifikat aus Azure Key Vault oder aus den über Azure Front Door verwalteten Zertifikaten für Unterdomänen verwenden.

Wenn eine Unterdomäne für eine Platzhalterdomäne hinzugefügt wird, der bereits ein Zertifikat zugeordnet ist, können Sie HTTPS für die Unterdomäne nicht deaktivieren. Die Unterdomäne verwendet die Zertifikatbindung für die Platzhalterdomäne, sofern sie nicht durch ein anderes Key Vault-Zertifikat oder ein über Azure Front Door verwaltetes Zertifikat außer Kraft gesetzt wird.

## <a name="waf-policies"></a>WAF-Richtlinien

WAF-Richtlinien können Platzhalterdomänen zugeordnet werden, wie es bei anderen Domänen der Fall ist. Eine andere WAF-Richtlinie kann auf eine Unterdomäne einer Platzhalterdomäne angewendet werden. Für die Unterdomänen müssen Sie die zu verwendende WAF-Richtlinie auch dann angeben, wenn es sich um dieselbe Richtlinie wie bei der Platzhalterdomäne handelt. Unterdomänen erben *nicht* automatisch die WAF-Richtlinie der Platzhalterdomäne.

Wenn Sie nicht möchten, dass eine WAF-Richtlinie für eine Unterdomäne ausgeführt wird, können Sie eine leere WAF-Richtlinie ohne verwaltete oder benutzerdefinierte Regelsätze erstellen.

## <a name="routing-rules"></a>Routingregeln

Beim Konfigurieren einer Routingregel können Sie eine Platzhalterdomäne als Front-End-Host auswählen. Sie können für Platzhalterdomänen auch ein anderes Routingverhalten als für Unterdomänen festlegen. Wie unter [Abgleich von Anforderungen mit Routingregeln durch Front Door](front-door-route-matching.md) beschrieben, wird der genaueste Treffer für die Domäne bei unterschiedlichen Routingregeln zur Laufzeit ausgewählt.

> [!IMPORTANT]
> Sie müssen über übereinstimmende Pfadmuster für Ihre Routingregeln verfügen. Anderenfalls treten bei Ihren Clients Fehler auf. Beispiel: Sie verfügen über die beiden Routingregeln Route 1 (`*.foo.com/*` ist Back-End-Pool A zugeordnet) und Route 2 (`/bar.foo.com/somePath/*` ist Back-End-Pool B zugeordnet). Sie empfangen eine Anforderung für `bar.foo.com/anotherPath/*`. Azure Front Door wählt basierend auf einer genaueren Domänenübereinstimmung Route 2 aus, findet jedoch keine übereinstimmenden Pfadmuster für die Routen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [ein Azure Front Door-Profil erstellen](quickstart-create-front-door.md).
- Erfahren Sie mehr über das [Hinzufügen einer benutzerdefinierten Domäne in Azure Front Door](front-door-custom-domain.md).
- Erfahren Sie mehr über das [Aktivieren von HTTPS für eine benutzerdefinierte Domäne](front-door-custom-domain-https.md).
