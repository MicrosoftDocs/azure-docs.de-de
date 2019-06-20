---
title: Erweiterte Anforderungsbegrenzung mit Azure API Management
description: Erfahren Sie, wie Sie mit Azure API Management flexibel Richtlinien zur Kontingents- und Ratenbegrenzung erstellen und anwenden.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: 22c3987121e2ab3479274c89c359c679f5f1135e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61087129"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Erweiterte Anforderungsbegrenzung mit Azure API Management
Die Fähigkeit, eingehende Anforderungen zu begrenzen oder zu drosseln, ist für Azure API Management von großer Bedeutung. Mit API Management lässt sich entweder die Rate der Anforderungen oder die Gesamtzahl der Anforderungen bzw. die Gesamtmenge der übertragenen Daten steuern. Dies ermöglicht es den API-Anbietern, ihre APIs vor Missbrauch zu schützen und mit verschiedenen API-Produkttarifen Mehrwert zu schaffen.

## <a name="product-based-throttling"></a>Produktbasierte Drosselung
Bisher konnte die Rate nur auf der Basis eines bestimmten Produktabonnements gedrosselt werden, das im Azure-Portal definiert wurde. Dies ist nützlich, wenn der API-Anbieter Beschränkungen für Entwickler einführen möchte, die sich für die Nutzung der API registriert haben. Es hilft aber beispielsweise nicht bei der Drosselung einzelner Endbenutzer der API. Daher ist es möglich, dass ein einzelner Benutzer der Anwendung eines Entwicklers das gesamte Kontingent in Anspruch nimmt und dadurch andere Kunden des Entwicklers an der Nutzung der Anwendung hindert. Außerdem können verschiedene Kunden gemeinsam ein so hohes Anforderungsvolumen generieren, dass der Zugriff für gelegentliche Benutzer beschränkt wird.

## <a name="custom-key-based-throttling"></a>Benutzerdefinierte Drosselung auf der Basis von Schlüsseln
Die neuen Richtlinien [rate-limit-by-key](/azure/api-management/api-management-access-restriction-policies#LimitCallRateByKey) und [quota-by-key](/azure/api-management/api-management-access-restriction-policies#SetUsageQuotaByKey) bieten mehr Flexibilität bei der Steuerung des Datenverkehrs. Mit diesen neuen Richtlinien können Sie in selbst definierten Ausdrücken Schlüssel angeben, die zum Nachverfolgen der Datenverkehrsnutzung verwendet werden. Wie das funktioniert, lässt sich am einfachsten anhand eines Beispiels veranschaulichen. 

## <a name="ip-address-throttling"></a>Drosselung nach IP-Adresse
Die folgenden Richtlinien beschränken eine einzelne Client-IP-Adresse auf nur 10 Aufrufe pro Minute bei einer Gesamtanzahl von 1.000.000 Aufrufen und 10.000 Kilobyte Bandbreite pro Monat. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Wenn alle Clients im Internet eine eindeutige IP-Adresse verwenden würden, wäre dies vielleicht eine effektive Möglichkeit zur Nutzungsbegrenzung nach Benutzer. Allerdings ist die Wahrscheinlichkeit hoch, dass mehrere Benutzer eine einzige öffentliche IP-Adresse gemeinsam nutzen, weil sie über ein NAT-Gerät auf das Internet zugreifen. Für APIs, die einen nicht authentifizierten Zugriff zulassen, ist `IpAddress` dennoch unter Umständen die beste Option.

## <a name="user-identity-throttling"></a>Drosselung nach Benutzeridentität
Wenn ein Endbenutzer authentifiziert ist, kann auf Basis der eindeutigen Identitätsinformationen dieses Benutzers ein Drosselungsschlüssel generiert werden.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

In diesem Beispiel wird veranschaulicht, wie der „Authorization“-Header extrahiert, in ein `JWT`-Objekt konvertiert und der Antragsteller des Tokens verwendet wird, um den Benutzer zu identifizieren. Diesen verwenden wir als Schlüssel zum Begrenzen der Rate. Wenn die Benutzeridentität in `JWT` als einer der anderen Ansprüche gespeichert ist, kann stattdessen dieser Wert verwendet werden.

## <a name="combined-policies"></a>Kombinierte Richtlinien
Obwohl die neuen Drosselungsrichtlinien mehr Steuerungsmöglichkeiten bieten als die bisher vorhandenen, lohnt es sich weiterhin, beide Fähigkeiten zu kombinieren. Die Drosselung nach Produktabonnementschlüssel ([Aufrufrate nach Abonnement einschränken](/azure/api-management/api-management-access-restriction-policies#LimitCallRate) und [Nutzungskontingent nach Abonnement festlegen](/azure/api-management/api-management-access-restriction-policies#SetUsageQuota)) gibt Ihnen die Möglichkeit, nach Nutzungsstufen gestaffelte Gebühren für eine API festzulegen und sie so zu monetarisieren. Die feiner differenzierten Steuerungsmöglichkeiten einer Drosselung nach Benutzer können ergänzend genutzt werden, um zu verhindern, dass das Verhalten eines Benutzers die Erfahrung eines anderen beeinträchtigt. 

## <a name="client-driven-throttling"></a>Clientgesteuerte Drosselung
Wenn der Drosselungsschlüssel mithilfe eines [Richtlinienausdrucks](/azure/api-management/api-management-policy-expressions) definiert wird, legt der API-Anbieter Art und Umfang der Drosselung fest. Ein Entwickler möchte jedoch möglicherweise selbst steuern, wie er die Raten für seine Kunden begrenzt. Der API-Anbieter kann dies einrichten. Dazu führt er einen benutzerdefinierten Header ein, der es der Clientanwendung des Entwicklers ermöglicht, den Schlüssel an die API zu kommunizieren.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Auf diese Weise kann die Clientanwendung des Entwicklers festlegen, wie der Schlüssel zur Ratenbegrenzung erstellt werden soll. Die Cliententwickler können eigene Ratenstufen erstellen, indem sie den Benutzern Gruppen von Schlüsseln zuweisen und eine Rotation der Schlüsselnutzung einrichten.

## <a name="summary"></a>Zusammenfassung
Azure API Management ermöglicht Drosselungen bzw. Begrenzungen nach Rate und Kontingent, um einen API-Dienst zu schützen und Mehrwert daraus zu generieren. Die neuen Drosselungsrichtlinien ermöglichen durch ihre benutzerdefinierten Umfangsregeln eine feinere Steuerung der Drosselung, sodass Ihre Kunden noch bessere Anwendungen erstellen können. Die Beispiele in diesem Artikel veranschaulichen die Verwendung dieser neuen Richtlinien. Sie zeigen, wie Client-IP-Adressen, Benutzeridentitäten und vom Client generierte Werte zum Erstellen von Schlüsseln zur Ratenbegrenzung genutzt werden können. Es gibt jedoch noch viele andere Teile einer Nachricht, die für diesen Zweck verwendet werden können, z. B. Benutzer-Agent, URL-Pfad-Fragmente und Nachrichtengröße.

## <a name="next-steps"></a>Nächste Schritte
Bitte geben Sie uns im Disqus-Thread Feedback zu diesem Thema. Wir würden gern von Ihnen hören, welche anderen möglichen Schlüsselwerte sich in Ihren Szenarien anbieten oder bewährt haben.

