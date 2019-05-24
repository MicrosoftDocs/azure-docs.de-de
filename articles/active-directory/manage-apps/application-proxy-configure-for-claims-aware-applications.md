---
title: Ansprüche unterstützende Apps – Azure AD-App-Proxy | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie lokale ASP.NET-Anwendungen veröffentlichen, die AD FS-Ansprüche für sicheren Remotezugriff durch Ihre Benutzer akzeptieren.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: c97729cf7d88ebdeefb44c83eb571bb6d7ebd0ed
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825595"
---
# <a name="working-with-claims-aware-apps-in-application-proxy"></a>Arbeiten mit Ansprüche unterstützenden Apps im Anwendungsproxy
[Ansprüche unterstützende Apps](https://msdn.microsoft.com/library/windows/desktop/bb736227.aspx) führen eine Umleitung an den Sicherheitstokendienst (Security Token Service, STS) aus. Der STS fordert vom Benutzer Anmeldeinformationen im Austausch gegen ein Token an und leitet ihn dann an die Anwendung um. Es gibt mehrere Möglichkeiten, den Anwendungsproxy zum Arbeiten mit diesen Umleitungen zu aktivieren. Verwenden Sie diesen Artikel zum Konfigurieren Ihrer Bereitstellung für Ansprüche unterstützende Anwendungen. 

## <a name="prerequisites"></a>Voraussetzungen
Stellen Sie sicher, dass der STS, zu dem die Ansprüche unterstützende App umgeleitet wird, außerhalb Ihres lokalen Netzwerks zur Verfügung steht. Sie können den STS über einen Proxy verfügbar machen, oder indem Sie externe Verbindungen zulassen. 

## <a name="publish-your-application"></a>Veröffentlichen der Anwendung

1. Veröffentlichen Sie Ihre Anwendung entsprechend den Anweisungen unter [Veröffentlichen von Anwendungen mit einem Anwendungsproxy](application-proxy-add-on-premises-application.md).
2. Navigieren Sie zu der Anwendungenseite im Portal, und wählen Sie **Einmaliges Anmelden**.
3. Wenn Sie **Azure Active Directory** als **Präauthentifizierungsmethode** gewählt haben, wählen Sie **Azure AD-SSO deaktiviert** als **Interne Authentifizierungsmethode** aus. Wenn Sie **Passthrough** als **Methode für die Präauthentifizierung** wählen, müssen Sie nichts ändern.

## <a name="configure-adfs"></a>Konfigurieren von ADFS

Sie können AD FS auf zwei Arten für Ansprüche unterstützende Apps konfigurieren. Die erste ist die Verwendung von benutzerdefinierter Domänen. Die zweite erfolgt mit dem WS-Verbund. 

### <a name="option-1-custom-domains"></a>Option 1: Benutzerdefinierte Domänen

Wenn alle internen URLs für Ihre Anwendungen vollständig qualifizierte Domänennamen (Fully Qualified Domain Names, FQDNs) sind, können Sie [benutzerdefinierte Domänen](application-proxy-configure-custom-domain.md) für Ihre Anwendungen konfigurieren. Verwenden Sie die benutzerdefinierten Domänen, um externe URLs zu erstellen, die mit den internen URLs identisch sind. Wenn Ihre externen URLs mit Ihren internen URLs übereinstimmen, funktionieren die STS-Umleitungen unabhängig davon, ob Ihre Benutzer lokal oder remote darauf zugreifen. 

### <a name="option-2-ws-federation"></a>Option 2: WS-Federation-

1. Öffnen Sie die AD FS-Verwaltung.
2. Wechseln Sie zu **Vertrauensstellungen der vertrauenden Seite**, klicken Sie mit der rechten Maustaste auf die App, die Sie mit dem Anwendungsproxy veröffentlichen, und wählen Sie **Eigenschaften** aus.  

   ![Vertrauensstellungen der vertrauenden Seite: mit der rechten Maustaste auf den App-Namen klicken – Screenshot](./media/application-proxy-configure-for-claims-aware-applications/appproxyrelyingpartytrust.png)  

3. Wählen Sie auf der Registerkarte **Endpunkte** unter **Endpunkttyp** die Option **WS-Verbund** aus.
4. Geben Sie unter **Vertrauenswürdige URL** die URL ein, die Sie im Anwendungsproxy unter **Externe URL** eingegeben haben, und klicken Sie auf **OK**.  

   ![Hinzufügen eines Endpunkts – Wert "Vertrauenswürdige URL" festlegen – Screenshot](./media/application-proxy-configure-for-claims-aware-applications/appproxyendpointtrustedurl.png)  

## <a name="next-steps"></a>Nächste Schritte
* [Aktivieren des einmaligen Anmeldens](configure-single-sign-on-portal.md) für Anwendungen, die nicht Ansprüche unterstützen
* [Aktivieren von nativen Client-Apps für die Interaktion mit Proxyanwendungen](application-proxy-configure-native-client-application.md)


