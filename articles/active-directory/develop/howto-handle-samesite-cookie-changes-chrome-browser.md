---
title: Behandeln der Änderungen bei SameSite-Cookies im Chrome-Browser | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie Änderungen bei SameSite-Cookies im Chrome-Browser behandeln.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 30c4f054259aa7c3f2a9fdfaeeadd64f26dd9bea
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444910"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>Verarbeiten von SameSite-Cookieänderungen im Chrome-Browser

## <a name="what-is-samesite"></a>Was ist SameSite?

`SameSite` ist eine Eigenschaft, die in HTTP-Cookies festgelegt werden kann, um CSRF-Angriffe (Cross Site Request Forgery) in Webanwendungen zu verhindern:

- Wenn `SameSite` auf **Lax** festgelegt ist, wird das Cookie in Anforderungen innerhalb derselben Website und in GET-Anforderungen von anderen Websites gesendet. Es wird nicht in domänenübergreifenden GET-Anforderungen gesendet.
- Der Wert **Strict** stellt sicher, dass das Cookie nur in Anforderungen innerhalb derselben Website gesendet wird.

Standardmäßig ist der Wert `SameSite` in Browsern NICHT festgelegt. Daher gibt es keine Einschränkungen für Cookies, die in Anforderungen gesendet werden. Eine Anwendung muss den CSRF-Schutz durch Festlegen von **Lax** oder **Strict** gemäß Ihren Anforderungen aktivieren.

## <a name="samesite-changes-and-impact-on-authentication"></a>Änderungen bei SameSite und Auswirkungen auf die Authentifizierung

Aktuelle [Updates der SameSite-Standards](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) schlagen den Schutz von Apps vor, indem sie das Standardverhalten von `SameSite` festlegen, wenn kein Wert auf „Lax“ eingestellt ist. Diese Risikominimierung bedeutet, dass Cookies mit Ausnahme bei GET-Anforderungen von anderen Websites bei HTTP-Anforderungen eingeschränkt werden. Außerdem wird der Wert **None** eingeführt, um Einschränkungen für gesendete Cookies zu entfernen. Diese Updates werden in Kürze in einer kommenden Version des Chrome-Browsers veröffentlicht.

Wenn sich Web-Apps bei Microsoft Identity Platform mit dem Antwortmodus „form_post“ authentifizieren, antwortet der Anmeldeserver der Anwendung mit HTTP POST, um Token oder Authentifizierungscode zu senden. Da es sich bei dieser Anforderung um eine domänenübergreifende Anforderung handelt (von `login.microsoftonline.com` zu Ihrer Domäne, z. B. `https://contoso.com/auth`) unterliegen jetzt Cookies, die in Ihrer App festgelegt wurden, den neuen Regeln in Chrome. Bei den Cookies, die in websiteübergreifenden Szenarien verwendet werden müssen, handelt es sich um Cookies mit den Werten *Status* und *Nonce* , die auch in der Anmeldeanforderung gesendet werden. Es gibt auch noch andere Cookies, die von Azure AD gelöscht werden, um die Sitzung zu speichern.

Wenn Sie Ihre Web-Apps nicht aktualisieren, führt dieses neue Verhalten zu Authentifizierungsfehlern.

## <a name="mitigation-and-samples"></a>Risikominimierung und Beispiele

Um Authentifizierungsfehler zu beheben, können Web-Apps, die sich bei der Microsoft Identity-Plattform authentifizieren, für Cookies, die bei der Ausführung domänenübergreifender Szenarien im Chrome-Browser verwendet werden, die `SameSite`-Eigenschaft auf `None` festlegen.
Andere Browser ([hier](https://www.chromium.org/updates/same-site/incompatible-clients) finden Sie eine komplette Liste) folgen dem bisherigen Verhalten von `SameSite` und beziehen die Cookies nicht ein, wenn `SameSite=None` festgelegt ist.
Aus diesem Grund müssen Web-Apps zur Unterstützung der Authentifizierung auf mehreren Browsern nur für Chrome den Wert `SameSite` auf `None` festlegen und den Wert für andere Browser leer lassen.

Diese Vorgehensweise wird in den folgenden Codebeispielen veranschaulicht.

# <a name="net"></a>[.NET](#tab/dotnet)

Die folgende Tabelle zeigt die Pull Requests, mit denen die SameSite-Änderungen in unseren ASP.NET- und ASP.NET Core-Beispielen umgangen wurden.

| Beispiel | Pull Request |
| ------ | ------------ |
|  [Inkrementelles Tutorial zur ASP.NET Core-Web-App](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [Same site cookie fix #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET MVC-Web-App-Beispiel](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [Same site cookie fix #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [active-directory-dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [Same site cookie fix #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

Ausführliche Informationen zur Behandlung von SameSite-Cookies in ASP.NET und ASP.NET Core finden Sie auch unter:

- [Arbeiten mit SameSite-Cookies in ASP.NET Core](/aspnet/core/security/samesite).
- [ASP.NET-Blog zum SameSite-Thema](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| Beispiel |
| ------ |
|  [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| Beispiel | Pull Request |
| ------ | ------------ |
|  [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [Same site cookie fix #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [Same site cookie fix #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über SameSite und das Web-App-Szenario:

- [Häufig gestellte Fragen in Google Chrome zu SameSite](https://www.chromium.org/updates/same-site/faq)

- [Chromium-Seite „SameSite“](https://www.chromium.org/updates/same-site)

- [Szenario: Web-App, die Benutzer anmeldet](scenario-web-app-sign-user-overview.md)