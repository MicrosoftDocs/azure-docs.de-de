---
title: Installieren der Browsererweiterung für den Anwendungszugriffsbereich – Azure | Microsoft-Dokumentation
description: Korrigieren Sie allgemeine Fehler, die beim Installieren der Browsererweiterung für den Zugriffsbereich auftreten können.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: mimart
ms.reviewer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 598a69593046354e83fe35b7b2ecfb0745e8b637
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388330"
---
# <a name="install-the-access-panel-browser-extension"></a>Installieren der Browsererweiterung für den Zugriffsbereich

Der Zugriffsbereich ist ein webbasiertes Portal. Wenn Sie über ein Geschäfts-, Schul- oder Unikonto in Azure Active Directory (Azure AD) verfügen, können Sie über den Zugriffsbereich cloudbasierte Anwendungen anzeigen und starten, für die Ihnen ein Azure AD-Administrator Zugriff gewährt hat. 

Bei Verwendung von Azure AD-Editionen können Sie über den Zugriffsbereich auch Self-Service-basierte Gruppen und die App-Verwaltungsfunktionen nutzen. 

Der Zugriffsbereich ist vom Azure-Portal getrennt. Er kann auch ohne Azure-Abonnement verwendet werden.

## <a name="web-browser-requirements"></a>Webbrowseranforderungen

Die Mindestanforderung für den Zugriffsbereich ist ein Browser mit JavaScript-Unterstützung und CSS-Aktivierung. Zur Verwendung von kennwortbasiertem SSO bei Anwendungen im Zugriffsbereich muss in Ihrem Browser die Erweiterung für den Zugriffsbereich installiert sein. Die Erweiterung wird automatisch heruntergeladen, wenn Sie eine Anwendung auswählen, die für kennwortbasiertes SSO konfiguriert ist.

Für kennwortbasiertes SSO können folgende Browser verwendet werden:

- **Microsoft Edge:** Windows 10 Anniversary Edition oder höher 
- **Chrome:** Unter Windows 7 oder höher und MacOS X oder höher
- **Firefox 26.0 oder höher:** Unter Windows XP SP2 oder höher und Mac OS X 10.6 oder höher

## <a name="install-the-access-panel-browser-extension"></a>Installieren der Browsererweiterung für den Zugriffsbereich

Gehen Sie zum Installieren der Browsererweiterung für den Zugriffsbereich wie folgt vor:

1.  Öffnen Sie den [Zugriffsbereich](https://myapps.microsoft.com) in einem der unterstützten Browser, und melden Sie sich als Benutzer bei Ihrem Azure AD-Konto an.

2.  Wählen Sie eine Anwendung mit kennwortbasiertem SSO aus.

3.  Klicken Sie auf **Jetzt installieren**, wenn Sie dazu aufgefordert werden.  
    Sie werden zum Downloadlink für den gewählten Browser weitergeleitet. 
    
4.  Wählen Sie **Hinzufügen**.

5.  **Aktivieren** Sie die Erweiterung (bzw. **lassen Sie sie zu**), wenn Sie dazu aufgefordert werden.

6.  Starten Sie Ihren Browser nach Abschluss der Installation neu.

7.  Melden Sie sich beim Zugriffsbereich an, und überprüfen Sie, ob Sie Ihre Anwendungen mit kennwortbasiertem SSO starten können.

Sie können die Erweiterung für Chrome und Microsoft Edge auch direkt von den folgenden Websites herunterladen:

- [Chrome-Erweiterung](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)
- [Microsoft Edge-Erweiterung](https://www.microsoft.com/store/apps/9pc9sckkzk84)
- [Firefox-Erweiterung](https://addons.mozilla.org/en-US/firefox/addon/access-panel-extension/)

## <a name="use-the-my-apps-secure-sign-in-extension"></a>Verwenden der Erweiterung zur sicheren Anmeldung bei „Meine Apps“
* Falls sich Ihre Meine Apps-URL von `https://myapps.microsoft.com` unterscheidet, konfigurieren Ihre Standard-URL wie folgt:
   1. Klicken Sie mit der rechten Maustaste auf das Symbol für die Erweiterung, *ohne* bei der Erweiterung angemeldet zu sein.
   2. Klicken Sie im Menü auf **Meine Apps-URL**.
   3. Wählen Sie Ihre Standard-URL aus.
   4. Wählen Sie das Symbol für die Erweiterung aus.
   5. Klicken Sie auf **Melden Sie sich an, um loszulegen**, um sich bei der Erweiterung anzumelden.

* Gehen Sie wie folgt vor, um sich im Browser direkt bei einer App anzumelden:
   1. Melden Sie sich nach der Erweiterungsinstallation bei der Erweiterung an, indem Sie auf **Melden Sie sich an, um loszulegen** klicken.
   2. Melden Sie sich mit der Anmelde-URL bei der App an.  
       Bei der Anmelde-URL handelt es sich üblicherweise um die URL, unter der das Anmeldeformular einer App angezeigt wird.
      Die Erweiterung sollte ihren Status ändern und Sie darüber informieren, dass ein Kennwort verfügbar ist.
   3. Klicken Sie auf das Symbol für die Erweiterung, um sich anzumelden.

* Um eine App über die Erweiterung zu starten, gehen Sie wie folgt vor:
   1. Melden Sie sich nach der Installation bei der Erweiterung an, indem Sie **Melden Sie sich an, um loszulegen** auswählen.
   2. Klicken Sie auf das Symbol für die Erweiterung, um das dazugehörige Menü zu öffnen.
   3. Suchen Sie nach einer App, die im Portal „Meine Apps“ zur Verfügung steht.
   4. Wählen Sie die App in den Suchergebnissen aus.  
       In der Verknüpfungsliste **Zuletzt verwendet** werden die drei zuletzt verwendeten Apps angezeigt.
       
* Um interne Unternehmens-URLs per Remotezugriff zu verwenden, gehen Sie wie folgt vor:
    1. [Konfigurieren Sie den Anwendungsproxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) für Ihren Mandanten.
    2. [Veröffentlichen Sie die Anwendung](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) und die URL über den Anwendungsproxy.
    3. Installieren Sie die Erweiterung, und melden Sie sich bei ihr an, indem Sie „Melden Sie sich an, um loszulegen“ auswählen.
    4. Nun können Sie selbst per Remotezugriff zu der internen Unternehmens-URL browsen.

> [!NOTE]
> Die obigen Optionen sind nur für Microsoft Edge, Chrome und Firefox verfügbar.

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Einrichten einer Gruppenrichtlinie für Internet Explorer

Sie können eine Gruppenrichtlinie einrichten, um die Zugriffsbereichserweiterung für Internet Explorer per Remotezugriff auf den Computern Ihrer Benutzer zu installieren.

Vergewissern Sie sich vor dem Einrichten einer Gruppenrichtlinie, dass Folgendes erfüllt ist:

-   Sie haben [Active Directory-Domänendienste](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)eingerichtet und die Computer Ihrer Benutzer Ihrer Domäne hinzugefügt.

-   Zum Bearbeiten des Gruppenrichtlinienobjekts (Group Policy Object, GPO) benötigen Sie die Berechtigung *Einstellungen bearbeiten*. Standardmäßig verfügen Mitglieder der folgenden Sicherheitsgruppen über diese Berechtigung: Domänenadministratoren, Organisationsadministratoren und Richtlinien-Ersteller-Besitzer.

Eine ausführliche Anleitung zum Konfigurieren der Gruppenrichtlinie sowie zum Bereitstellen für Benutzer finden Sie unter [Bereitstellen der Zugriffsbereichserweiterung für Internet Explorer mit der Gruppenrichtlinie](deploy-access-panel-browser-extension.md).

## <a name="troubleshoot-the-access-panel-extension-in-internet-explorer"></a>Behandeln von Problemen mit der Zugriffsbereichserweiterung in Internet Explorer

Der Artikel [Problembehandlung in der Zugriffsbereichserweiterung für Internet Explorer](manage-access-panel-browser-extension.md) bietet Zugriff auf ein Diagnosetool und enthält Informationen zum Konfigurieren der Erweiterung für Internet Explorer.

> [!NOTE]
> Internet Explorer wird nur eingeschränkt unterstützt, und es werden keine neuen Softwareupdates dafür bereitgestellt. Microsoft Edge ist der empfohlene Browser.

## <a name="if-the-preceding-steps-do-not-resolve-the-issue"></a>Falls sich das Problem mit den oben genannten Schritten nicht beheben lässt:

Erstellen Sie ein Supportticket mit den folgenden Informationen, sofern verfügbar:

-   Fehlerkorrelations-ID
-   UPN (E-Mail-Adresse des Benutzers)
-   Mandanten-ID
-   Browsertyp
-   Zeitzone und Zeitpunkt/Zeitraum des Fehlers
-   Fiddler-Ablaufverfolgungen

## <a name="next-steps"></a>Nächste Schritte
[Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](what-is-single-sign-on.md)
