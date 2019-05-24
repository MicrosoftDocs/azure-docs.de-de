---
title: Unterstützte Benutzerkonten in Apps (Zielgruppe) – Microsoft Identity Platform
description: Konzeptionelle Dokumentation über Zielgruppen und unterstützte Kontotypen in Anwendungen
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 908ba764dfee7c164c3e6f0ff24d2bbf6a0df287
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544843"
---
# <a name="supported-account-types"></a>Unterstützte Kontotypen

In diesem Artikel wird erläutert, welche Kontotypen (manchmal als „Zielgruppen“ bezeichnet) in Anwendungen unterstützt werden.

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Unterstützte Kontentypen in Microsoft Identity Platform-Anwendungen

In der öffentlichen Microsoft Azure-Cloud können die meisten App-Typen Benutzer mit beliebiger Zielgruppe anmelden:

- Wenn Sie eine Branchenanwendung schreiben, können Sie Benutzer in Ihrer eigenen Organisation anmelden. Eine solche Anwendung wird manchmal als **einzelner Mandant** bezeichnet.
- Wenn Sie ein ISV sind, können Sie eine Anwendung schreiben, die Benutzer anmeldet:

  - In einer beliebigen Organisation. Eine solche Anwendung wird als **mehrinstanzenfähige** Webanwendung bezeichnet. Bisweilen ist zu lesen, dass sie Benutzer bei deren Geschäfts-, Schul- oder Unikonten anmeldet.
  - Bei ihrem Geschäfts-, Schul- oder Unikonto oder ihrem persönlichen Microsoft-Konto.
  - Nur bei ihrem persönlichen Microsoft-Konto.
    > [!NOTE]
    > Derzeit unterstützt Microsoft Identity Platform persönliche Microsoft-Konten nur, wenn eine Anwendung für **Geschäfts-, Schul- oder Unikonten oder persönliche Microsoft-Konten** registriert und dann im Code für die Anwendung die Anmeldung einschränkt wird. Dazu wird beim Erstellen der Anwendung eine Azure AD-Autorität angegeben, wie beispielsweise `https://login.onmicrosoftonline.com/consumers`.

- Wenn Sie eine B2C-Anwendung schreiben, können Sie Benutzer über Azure AD B2C auch mit Identitäten sozialer Netzwerke anmelden.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Bestimmte Authentifizierungsabläufe unterstützen nicht alle Kontotypen.

Einige Kontotypen können mit bestimmten Authentifizierungsabläufen nicht verwendet werden. Auf dem Desktop betrifft dies beispielsweise UWP- oder Daemon-Anwendungen:

- Daemon-Anwendungen können nur mit Azure Active Directory-Organisationen verwendet werden. Das Verwenden von Daemon-Anwendungen zum Ändern persönlicher Microsoft-Konten ist zwecklos (die Administratoreinwilligung wird nie erteilt).  
- Sie können den Ablauf der integrierten Windows-Authentifizierung nur mit Geschäfts-, Schul- oder Unikonten (in Ihrer oder einer anderen Organisation) verwenden. Tatsächlich funktioniert die integrierte Windows-Authentifizierung mit Domänenkonten und erfordert, dass die Computer in die Domäne oder in Azure AD eingebunden sind. Dieser Ablauf ist für persönliche Microsoft-Konten nicht sinnvoll.
- Die [Zuweisung eines Ressourcenbesitzerkennworts](./v2-oauth-ropc.md) (Benutzername/Kennwort) kann nicht mit persönlichen Microsoft-Konten verwendet werden. Tatsächlich erfordern persönliche Microsoft-Konten, dass der Benutzer bei jeder Anmeldesitzung dem Zugriff auf persönliche Ressourcen zustimmt. Daher ist dieses Verhalten mit nicht-interaktiven Strömen nicht kompatibel.
- Gerätecodefluss funktioniert noch nicht mit persönlichen Microsoft-Konten.

## <a name="supported-account-types-in-national-clouds"></a>Unterstützte Kontotypen in nationalen Clouds

 Apps können Benutzer auch in [nationalen Clouds](authentication-national-cloud.md) anmelden. Allerdings werden persönliche Microsoft-Konten in diesen Clouds nicht unterstützt (per Definition dieser Clouds). Deshalb sind die unterstützten Kontotypen für diese Clouds auf Ihre Organisation (einzelner Mandant) oder beliebige Organisationen (mehrinstanzenfähige Anwendungen) beschränkt.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Mandanten in Azure Active Directory](./single-and-multi-tenant-apps.md)
- Weitere Informationen zu [nationalen Clouds](./authentication-national-cloud.md)