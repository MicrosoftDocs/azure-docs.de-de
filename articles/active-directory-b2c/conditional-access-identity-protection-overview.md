---
title: Identity Protection und bedingter Zugriff in Azure AD B2C
description: Hier erfahren Sie, wie Sie mit Identity Protection Risikoanmeldungen und Risikoerkennungen anzeigen. Außerdem erfahren Sie, wie sich mithilfe von bedingtem Zugriff Organisationsrichtlinien auf der Grundlage von Risikoereignissen in Ihren Azure AD B2C-Mandanten erzwingen lassen.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/01/2020
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: b17706f908613336714bb7daf08fdf3bfd5eaf64
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96928391"
---
# <a name="identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Identity Protection und bedingter Zugriff für Azure AD B2C

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Verbessern Sie die Sicherheit von Azure Active Directory B2C (Azure AD B2C) mit Azure AD Identity Protection und bedingtem Zugriff. Die Identity Protection-Features zur Erkennung von Risiken wie Risikobenutzern und Risikoanmeldungen werden automatisch erkannt und in Ihrem Azure AD B2C-Mandanten angezeigt. Sie können Richtlinien für bedingten Zugriff erstellen, die diese Risikoerkennungen nutzen, um Aktionen zu bestimmen und Organisationsrichtlinien zu erzwingen. Zusammen geben diese Funktionen Azure AD B2C-Anwendungsbesitzern mehr Kontrolle über risikobehaftete Authentifizierungen und Zugriffsrichtlinien.
  
Wenn Sie bereits mit [Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) und [bedingtem Zugriff](../active-directory/conditional-access/overview.md) in Azure AD vertraut sind, gibt es bei der Verwendung dieser Funktionen mit Azure AD B2C nur einige kleinere Unterschiede zu beachten. Diese sind im vorliegenden Artikel beschrieben.

![Bedingter Zugriff in einem B2C-Mandanten](media/conditional-access-identity-protection-overview/conditional-access-b2c.png)

> [!NOTE]
> Für die Verwendung von bedingtem Zugriff ist Azure AD B2C Premium P2 erforderlich.

## <a name="benefits-of-identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Vorteile von Identity Protection und bedingtem Zugriff für Azure AD B2C  

Die Kombination aus Richtlinien für bedingten Zugriff und Identity Protection-Risikoerkennung ermöglicht es Ihnen, auf risikobehaftete Authentifizierungen mit der passenden Richtlinienaktion zu reagieren.

- **Profitieren Sie von mehr Transparenz im Zusammenhang mit Authentifizierungsrisiken für Ihre Apps und Ihre Kundenbasis.** Anhand von Signalen aus mehreren Milliarden monatlichen Authentifizierungen für Azure AD und Microsoft-Konten können die Risikoerkennungsalgorithmen nun Authentifizierungen für Ihre lokalen Consumer oder Bürger als niedriges, mittleres oder hohes Risiko kennzeichnen.
- **Behandeln Sie Risiken automatisch, indem Sie Ihre eigene adaptive Authentifizierung konfigurieren.** Für angegebene Anwendungen können Sie festlegen, dass bestimmte Benutzer einen zweiten Authentifizierungsfaktor verwenden müssen, wie etwa bei der mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA). Alternativ können Sie den Zugriff basierend auf der erkannten Risikostufe blockieren. Genau wie bei anderen Azure AD B2C-Umgebungen können Sie die resultierende Endbenutzererfahrung an die Sprache, den Stil und die Marke Ihrer Organisation anpassen. Außerdem können Sie Abhilfealternativen anzeigen, wenn der Benutzer nicht zugreifen kann.
- **Steuern Sie den Zugriff basierend auf Standort, Gruppen und Apps.**  Bedingter Zugriff kann auch für die Steuerung in nicht risikobehafteten Situationen verwendet werden. So können Sie beispielsweise festlegen, dass Kunden, die auf eine bestimmte App zugreifen, eine mehrstufige Authentifizierung durchlaufen müssen, oder den Zugriff aus angegebenen geografischen Regionen blockieren.
- **Nutzen Sie die Integration mit Azure AD B2C-Benutzerflows und benutzerdefinierten IEF-Richtlinien (Identity Experience Framework).** Ergänzen Sie Ihre vorhandenen Umgebungen mit den Steuerelementen, die Sie für die Interaktion mit bedingtem Zugriff benötigen. Sie können auch erweiterte Szenarien für die Zugriffsgewährung implementieren, beispielsweise wissensbasierten Zugriff oder Ihren bevorzugten MFA-Anbieter.

## <a name="feature-differences-and-limitations"></a>Funktionsunterschiede und -einschränkungen

Identity Protection und bedingter Zugriff in Azure AD B2C funktionieren weitgehend wie in Azure AD, es gibt jedoch ein paar Ausnahmen:

- Security Center ist in Azure AD B2C nicht verfügbar.

- Identity Protection und bedingter Zugriff werden für ROPC-Server-zu-Server-Flows in Azure AD B2C-Mandanten nicht unterstützt.

- In Azure AD B2C-Mandanten sind Identity Protection-Risikoerkennungen nur für lokale B2C-Konten, aber nicht für soziale Identitäten wie Google oder Facebook verfügbar.

- In Azure AD B2C-Mandanten steht nur ein Teil der Identity Protection-Risikoerkennungen zur Verfügung. Weitere Informationen finden Sie unter [Einrichten von Identity Protection](conditional-access-identity-protection-setup.md#set-up-identity-protection).

- Das Gerätekompatibilitätsfeature des bedingten Zugriffs ist in Azure AD B2C-Mandanten nicht verfügbar.


## <a name="integrate-conditional-access-with-user-flows-and-custom-policies"></a>Integrieren von bedingtem Zugriff in Benutzerflows und benutzerdefinierte Richtlinien

In Azure AD B2C können Bedingungen für bedingten Zugriff über integrierte Benutzerflows ausgelöst werden. Des Weiteren können Sie bedingten Zugriff in benutzerdefinierte Richtlinien integrieren. Das Messaging der Endbenutzererfahrung kann genau wie andere Aspekte des B2C-Benutzerflows an die Sprache, Marke und Abhilfealternativen Ihrer Organisation angepasst werden. Weitere Informationen finden Sie unter [Definieren eines technischen Profils für den bedingten Zugriff in einer benutzerdefinierten Richtlinie in Azure Active Directory B2C](conditional-access-technical-profile.md).

## <a name="microsoft-graph-api"></a>Microsoft Graph-API

Richtlinien für bedingten Zugriff in Azure AD B2C können auch mit der Microsoft Graph-API verwaltet werden. Ausführliche Informationen finden Sie unter [Was ist bedingter Zugriff?](../active-directory/conditional-access/overview.md) und in der [Microsoft Graph-Referenz](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta).

## <a name="next-steps"></a>Nächste Schritte

- [Einrichten von Identity Protection und bedingtem Zugriff in Azure AD B2C](conditional-access-identity-protection-setup.md)
- [Was ist Identity Protection?](../active-directory/identity-protection/overview-identity-protection.md)
- [Was ist bedingter Zugriff?](../active-directory/conditional-access/overview.md)