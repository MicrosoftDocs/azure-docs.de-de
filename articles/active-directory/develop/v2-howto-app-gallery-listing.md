---
title: Veröffentlichen Ihrer App im App-Katalog von Azure Active Directory
description: Erfahren Sie, wie Sie eine Anwendung, die einmaliges Anmelden unterstützt, im Azure Active Directory-App-Katalog listen.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 12/14/2020
ms.author: kenwith
ms.reviewer: jeedes
ms.custom: aaddev
ms.openlocfilehash: f2a04a341239b62290eecac8c0e1ce70b7c927c1
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988907"
---
# <a name="publish-your-app-to-the-azure-ad-app-gallery"></a>Veröffentlichen Ihrer App im Azure AD-App-Katalog

Sie können Ihre App im Azure AD-App-Katalog veröffentlichen. Wenn Ihre App veröffentlicht wurde, wird Sie als Option für Kunden angezeigt, die Apps zu ihrem Mandanten hinzufügen. 

Das Hinzufügen Ihrer App zum Azure AD-Katalog bietet u. a. folgende Vorteile:

- Kunden finden die bestmögliche Umgebung für einmaliges Anmelden für Ihre App.
- Die Konfiguration der Anwendung ist einfach und in wenigen Schritten erledigt.
- Eine schnelle Suche findet Ihre Anwendung im Katalog.
- Azure AD-Kunden mit den Tarifen Free, Basic und Premium können diese Integration nutzen.
- Gemeinsame Kunden erhalten ausführliche Tutorials zur Konfiguration.

Außerdem gibt es viele Vorteile, wenn Ihre Kunden Azure AD als Identitätsanbieter für Ihre App verwenden. Dazu zählen:

- Bereitstellen des einmaligen Anmeldens für Ihre Benutzer. Mit dem einmaligen Anmelden reduzieren Sie die Supportkosten, da Sie durch einmaliges Anmelden die Benutzerfreundlichkeit erhöhen. Die IT-Administratoren müssen sich bei Aktivierung von 1-Klick-SSO nicht in die Konfiguration Ihrer Anwendung für die Nutzung in ihrer Organisation einarbeiten. Weitere Informationen zum einmaligen Anmelden finden Sie unter [Worum handelt es sich beim einmaligen Anmelden?](../manage-apps/what-is-single-sign-on.md).
- Ihre App kann im Microsoft 365-App-Katalog, im Microsoft 365-App-Startfeld und in Microsoft Search auf Office.com aufgefunden werden. 
- Integrierte App-Verwaltung. Weitere Informationen zur App-Verwaltung in Azure AD finden Sie unter [Worum handelt es sich bei der Anwendungsverwaltung?](../manage-apps/what-is-application-management.md)
- Ihre App kann mithilfe der [Graph-API](/graph/) auf Daten zugreifen, die die Benutzerproduktivität im Microsoft-Ökosystem erhöhen.
- Die gemeinsam mit dem Azure AD-Team erstellte anwendungsspezifische Dokumentation fördert die Akzeptanz bei den gemeinsamen Kunden.
- Bieten Sie Ihren Kunden die Möglichkeit, die Authentifizierung und Autorisierung ihrer Mitarbeiter- und Gastidentitäten umfassend zu verwalten.
- Die gesamte Verantwortung für Kontoverwaltung und Compliance wird dem Zuständigen für die betreffenden Identitäten auf Kundenseite übertragen.
- Sie räumen die Möglichkeit ein, SSO für bestimmte Identitätsanbieter, Gruppen oder Benutzer zu aktivieren bzw. zu deaktivieren, um deren Geschäftsanforderungen gerecht zu werden.
- Verbessern Sie die Marktgängigkeit und Akzeptanz Ihres Produkts. Viele große Organisationen fordern (oder wünschen) eine nahtlose SSO-Erfahrung ihrer Mitarbeiter für alle Anwendungen. Die einfache Gestaltung von SSO ist unerlässlich.
- Mindern Sie potenzielle Probleme auf Endbenutzerseite, wodurch Sie die Nutzung durch Endbenutzer steigern und Ihre Erträge steigern können.
- Kunden, die das System für die domänenübergreifende Identitätsverwaltung (System for Cross-domain Identity Management, [SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) verwenden, können die Bereitstellung für dieselbe App nutzen.
- Mit dem einmaligen Anmelden (SSO) von Azure AD und dem Verzicht auf die Eingabe separater Anmeldeinformationen erhöhen Sie Sicherheit und Benutzerfreundlichkeit, wenn sich Benutzer bei Ihren Anwendungen anmelden.

> [!TIP]
> Wenn Sie Ihre Anwendung per Kauf oder Abonnement für andere Unternehmen zur Nutzung anbieten, machen Sie die Anwendung für Kunden in deren eigenen Azure-Mandanten verfügbar. Sie erstellen also eine mehrinstanzenfähige Anwendung. Eine Übersicht über dieses Konzept finden Sie unter [Mehrinstanzenfähige Anwendungen in Azure](../../dotnet-develop-multitenant-applications.md) und [Mandanten in Azure Active Directory](single-and-multi-tenant-apps.md).

> [!IMPORTANT]
> Wenn Sie Ihre App im Azure AD-Katalog veröffentlichen möchten, müssen Sie den speziellen Geschäftsbedingungen zustimmen. Lesen Sie vor dem Start unbedingt die [Geschäftsbedingungen](https://azure.microsoft.com/support/legal/active-directory-app-gallery-terms/), und erklären Sie sich mit diesen einverstanden.

Folgende Schritte müssen Sie zum Veröffentlichen Ihrer App im Azure AD-App-Katalog ausführen:
1. Wählen Sie für Ihre App den richtigen Standard für einmaliges Anmelden aus.
2. Implementieren Sie einmaliges Anmelden in Ihrer App.
3. Erstellen Sie Ihren Azure-Mandanten, und testen Sie Ihre App.
4. Erstellen und veröffentlichen Sie die Dokumentation.
5. Übermitteln Sie Ihre App.
6. Werden Sie Mitglied im Microsoft Partner Network.

## <a name="what-is-the-azure-ad-application-gallery"></a>Was ist der Azure AD-Anwendungskatalog?

- Kunden finden so die bestmögliche Umgebung für einmaliges Anmelden.
- Die Konfiguration der Anwendung ist einfach und in wenigen Schritten erledigt.
- Eine schnelle Suche findet Ihre Anwendung im Katalog.
- Azure AD-Kunden mit den Tarifen Free, Basic und Premium können diese Integration nutzen.
- Gemeinsame Kunden erhalten ausführliche Tutorials zur Konfiguration.
- Kunden, die das System für die domänenübergreifende Identitätsverwaltung (System for Cross-domain Identity Management, [SCIM](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)) verwenden, können die Bereitstellung für dieselbe App nutzen.

## <a name="prerequisites"></a>Voraussetzungen

Für Tests benötigen Sie ein dauerhaftes Konto mit mindestens zwei registrierten Benutzern.

- Bei Verbundanwendungen (OpenID und SAML/WS-Fed) muss die Anwendung das Software-as-a-Service-Modell (SaaS-Modell) für die Aufnahme in den Azure AD-App-Katalog unterstützen. Die Unternehmenskataloganwendungen müssen mehrere Kundenkonfigurationen und nicht einen bestimmten Kunden unterstützen.
- Für OpenID Connect muss die Anwendung mehrinstanzenfähig sein, und das [Azure AD-Zustimmungsframework](../develop/consent-framework.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) muss für die Anwendung ordnungsgemäß implementiert werden. Der Benutzer kann die Anmeldeanforderung an einen gemeinsamen Endpunkt senden, damit jeder Kunde der Anwendung zustimmen kann. Sie können den Benutzerzugriff anhand der Mandanten-ID und des im Token erhaltenen UPN des Benutzers steuern.
- Für SAML 2.0/WS-Fed muss Ihre Anwendung über eine Funktion zur SAML-/WS-Fed-SSO-Integration im SP- oder IDP-Modus verfügen. Stellen Sie sicher, dass diese Funktion ordnungsgemäß funktioniert, bevor Sie die Anforderung senden.
- Stellen Sie beim Kennwort-SSO sicher, dass Ihre Anwendung die Formularauthentifizierung unterstützt, sodass Kennworttresore verwendet werden können, damit einmaliges Anmelden wie erwartet funktioniert.
- Für Tests benötigen Sie ein dauerhaftes Konto mit mindestens zwei registrierten Benutzern.

**Wie erhalte ich Azure AD für Entwickler?**

Sie können ein kostenloses Testkonto mit allen Premium-Features von Azure AD erhalten. Dieses ist 90 Tage lang kostenlos mit einer Verlängerungsmöglichkeit, solange Sie Entwicklungsaufgaben ausführen: https://docs.microsoft.com/office/developer-program/office-365-developer-program

## <a name="step-1---choose-the-right-single-sign-on-standard-for-your-app"></a>Schritt 1: Auswählen des richtigen Standards für einmaliges Anmelden für Ihre App

Um eine Anwendung im Azure AD-App-Katalog zu listen, müssen Sie mindestens eine der unterstützten Optionen für einmaliges Anmelden implementieren. Unter [Optionen für einmaliges Anmelden (SSO)](../manage-apps/sso-options.md) lernen Sie die Optionen für einmaliges Anmelden kennen und erfahren, wie Kunden diese Optionen in Azure AD konfigurieren.

Die folgende Tabelle enthält einen Vergleich der drei wichtigsten Standards: Open Authentication 2.0 (OAuth 2.0) mit OpenID Connect (OIDC), Security Assertion Markup Language (SAML) und Webdiensteverbund (WS-Fed).

| Funktion| OAuth/OIDC| SAML/WS-Fed |
| - |-|-|
| Webbasiertes einmaliges Anmelden| √| √ |
| Webbasiertes einmaliges Abmelden| √| √ |
| Mobile-basiertes einmaliges Anmelden| √| √* |
| Mobile-basiertes einmaliges Abmelden| √| √* |
| Richtlinien für den bedingten Zugriff für mobile Anwendungen| √| √* |
| Nahtlose MFA-Erfahrung für mobile Anwendungen| √| √* |
| SCIM-Bereitstellung| √| √ |
| Zugriff auf Microsoft Graph| √| X |

*Möglich, Microsoft stellt jedoch keine Beispiele und Anweisungen bereit.

### <a name="oauth-20-and-openid-connect"></a>OAuth 2.0 und OpenID Connect
OAuth 2.0 ist ein [Branchenstandard](https://oauth.net/2/)-Protokoll für die Autorisierung. OpenID Connect (OIDC) ist ein [Branchenstandard](https://openid.net/connect/) und eine Identitätsauthentifizierungsschicht, die das OAuth 2.0-Protokoll ergänzt. 

**Gründe für die Auswahl von OAuth/OIDC**
- Die inhärente Autorisierung in diesen Protokollen ermöglicht Ihrer Anwendung über die Microsoft Graph-API den Zugriff auf und die Integration mit umfassenden Benutzer- und Unternehmensdaten.
- Die Endbenutzerumgebung für Ihre Kunden wird vereinfacht, wenn Sie einmaliges Anmelden für Ihre Anwendung übernehmen. Sie können auf einfache Weise die erforderlichen Berechtigungssätze definieren, die dann automatisch dem zustimmenden Administrator bzw. Endbenutzer präsentiert werden.
- Mit diesen Protokollen können Ihre Kunden Richtlinien für bedingten Zugriff und MFA-Richtlinien (Multi-Factor Authentication) verwenden, um den Zugriff auf die Anwendungen zu steuern. 
- Microsoft bietet Bibliotheken und [Codebeispiele für mehrere Technologieplattformen](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples), um Ihre Entwicklung zu unterstützen.  

**Einige Aspekte, die zu berücksichtigen sind**
- Wenn Sie bereits SAML-basiertes einmaliges Anmelden für Ihre Anwendung implementiert haben, sollten Sie keinen neuen Standard implementieren, um Ihre App in den Katalog aufzunehmen.

### <a name="saml-20-or-ws-fed"></a>SAML 2.0 oder WS-Fed

SAML ist ein ausgereifter und weit verbreiteter [Standard für einmaliges Anmelden](https://www.oasis-open.org/standards#samlv2.0) bei Webanwendungen. Unter [Verwendung des SAML-Protokolls durch Azure AD](active-directory-saml-protocol-reference.md) erfahren Sie, wie SAML von Azure verwendet wird. 

Webdiensteverbund (WS-Fed) ist ein [Branchenstandard](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html), der im Allgemeinen für mit der .NET-Plattform entwickelte Webanwendungen verwendet wird.

**Gründe für die Auswahl von SAML**
- SAML 2.0 ist ein ausgereifter Standard, und die meisten Technologieplattformen unterstützen Open-Source-Bibliotheken für SAML 2.0. 
- Sie können für Ihre Kunden eine Verwaltungsschnittstelle zum Konfigurieren von SAML-SSO bereitstellen. Sie können das SAML-basierte einmalige Anmelden für Microsoft Azure AD sowie jeden anderen Identitätsanbieter konfigurieren, der SAML unterstützt.

**Einige Aspekte, die zu berücksichtigen sind**
- Wenn Sie die Protokolle SAML 2.0 oder WSFed für mobile Anwendungen verwenden, ist die Leistung bestimmter Richtlinien für den bedingten Zugriff beeinträchtigt, u. a. gilt dies für die mehrstufige Authentifizierung (MFA).
- Für den Zugriff auf Microsoft Graph müssen Sie die Autorisierung über OAuth 2.0 implementieren, um die erforderlichen Token zu generieren. 

### <a name="password-based"></a>Kennwortbasiert
Das kennwortbasierte einmalige Anmelden (auch als „Password Vaulting“ oder „Kennworttresor“ bezeichnet) ermöglicht es Ihnen, den Benutzerzugriff und die Kennwörter für Webanwendungen zu verwalten, die keinen Identitätsverbund unterstützen. Es ist auch in Szenarien nützlich, in denen mehrere Benutzer ein Konto gemeinsam verwenden müssen, wie z. B. bei den App-Konten für soziale Medien Ihrer Organisation.


## <a name="step-2---implement-single-sign-on-in-your-app"></a>Schritt 2: Implementieren des einmaligen Anmeldens in Ihrer App
Bei jeder App im Katalog muss eine der unterstützten Optionen für einmaliges Anmelden (SSO) implementiert sein. Weitere Informationen zu den unterstützten Optionen finden Sie unter [Optionen für einmaliges Anmelden (SSO)](../manage-apps/sso-options.md).

Informationen zu OAuth und OIDC finden Sie im [Leitfaden zu Authentifizierungsmustern](v2-app-types.md) und unter [Azure Active Directory-Codebeispiele](sample-v2-code.md).

Für SAML und WS-Fed muss Ihre Anwendung über eine Funktion zur Integration des einmaligen Anmeldens im SP- oder IDP-Modus verfügen. Stellen Sie sicher, dass diese Funktion ordnungsgemäß funktioniert, bevor Sie die Anforderung senden.

Weitere Informationen zur Authentifizierung finden Sie unter [Was ist die Authentifizierung?](../azuread-dev/v1-authentication-scenarios.md)

> [!IMPORTANT]
> Für Verbundanwendungen (OpenID und SAML/WS-Fed) muss die App das SaaS-Modell (Software as a Service) unterstützen. Anwendungen im Azure AD-Katalog müssen mehrere Kundenkonfigurationen unterstützen und sollten nicht nur für einen bestimmten Kunden ausgelegt sein.

### <a name="implement-oauth-20-and-openid-connect"></a>Implementieren von OAuth 2.0 und OpenID Connect

Für OpenID Connect muss die Anwendung mehrinstanzenfähig sein, und das [Azure AD-Zustimmungsframework](consent-framework.md) muss für die Anwendung ordnungsgemäß implementiert werden. Der Benutzer kann die Anmeldeanforderung an einen gemeinsamen Endpunkt senden, damit jeder Kunde der Anwendung zustimmen kann. Sie können den Benutzerzugriff anhand der Mandanten-ID und des im Token erhaltenen UPN des Benutzers steuern.

Spezielle Beispiele hierzu können Sie unter [Microsoft Identity Platform-Codebeispiele](sample-v2-code.md) überprüfen. 

Mobile-spezifische Beispiele finden Sie unter: 
* [Android](quickstart-v2-android.md)
* [iOS](quickstart-v2-ios.md)
* [Universelle Windows-Plattform](quickstart-v2-uwp.md)

### <a name="implement-saml-20"></a>Implementieren von SAML 2.0

Wenn Ihre App SAML 2.0 unterstützt, können Sie sie direkt in einen Azure AD-Mandanten integrieren. Weitere Informationen zur SAML-Konfiguration mit Azure AD finden Sie unter [Konfigurieren des SAML-basierten einmaligen Anmeldens](../manage-apps/configure-saml-single-sign-on.md).

Microsoft stellt keine Bibliotheken für die SAML-Implementierung bereit und empfiehlt auch keine Bibliotheken. Es stehen viele Open Source-Bibliotheken zur Verfügung.

### <a name="implement-ws-fed"></a>Implementieren von WS-Fed
Weitere Informationen zu WS-Fed in ASP.NET Core finden Sie unter [Authentifizieren von Benutzern mit WS-Fed in ASP.NET Core](/aspnet/core/security/authentication/ws-federation).

### <a name="implement-password-vaulting"></a>Implementieren des Kennworttresors

Erstellen Sie eine Webanwendung, die über eine HTML-Anmeldeseite verfügt. Stellen Sie sicher, dass Ihre Anwendung die Formularauthentifizierung unterstützt, damit Kennworttresore verwendet werden können und einmaliges Anmelden erwartungsgemäß funktioniert.

## <a name="step-3---implement-scim-user-provisioning-in-your-app"></a>Schritt 3: Implementieren der SCIM-Benutzerbereitstellung in Ihrer App
Die Unterstützung der [SCIM](https://aka.ms/scimoverview)-Bereitstellung ist ein optionaler Schritt beim Erstellen Ihrer Anwendung, wird aber dringend empfohlen. Die Unterstützung des SCIM-Standards ist einfach und ermöglicht Kunden, Benutzerkonten in Ihrer App automatisch zu erstellen und zu aktualisieren, ohne auf manuelle Prozesse wie das Hochladen von CSV-Dateien zurückgreifen zu müssen. Außerdem können Kunden das Entfernen von Benutzern und das Synchronisieren von Gruppenmitgliedschaften automatisieren. Beides ist mit einer Lösung wie SAML JIT nicht möglich. 

### <a name="learn-about-scim"></a>Informationen zu SCIM
Weitere Informationen zu den SCIM-Standards und den Vorteilen für Ihre Kunden finden Sie unter [Provisioning with SCIM – getting started](https://aka.ms/scimoverview) (Erste Schritte bei der Bereitstellung mit SCIM).

### <a name="understand-the-azure-ad-scim-implementation"></a>Verstehen der Azure AD-SCIM-Implementierung
Weitere Informationen zur Azure AD-Implementierung von SCIM finden Sie unter [Erstellen eines SCIM-Endpunkts und Konfigurieren der Benutzerbereitstellung mit Azure AD](../app-provisioning/use-scim-to-provision-users-and-groups.md).

### <a name="implement-scim"></a>Implementieren von SCIM
Azure AD stellt [Referenzcode](https://aka.ms/scimoverview) bereit, um Sie beim Erstellen eines SCIM-Endpunkts zu unterstützen. Es gibt auch viele Bibliotheken/Referenzen von Drittanbietern, die Sie auf GitHub finden können.  

## <a name="step-4---create-your-azure-tenant-and-test-your-app"></a>Schritt 4: Erstellen Ihres Azure-Mandanten und Testen der App

Sie benötigen einen Azure AD Mandanten, um Ihre App zu testen. Informationen zum Einrichten Ihrer Entwicklungsumgebung finden Sie unter [Schnellstart: Einrichten eines Mandanten](quickstart-create-new-tenant.md).

Alternativ wird zusammen mit jedem Microsoft 365-Abonnement ein Azure AD-Mandant eingerichtet. Informationen zum Einrichten einer kostenlosen Microsoft 365-Entwicklungsumgebung finden Sie unter [Teilnehmen am Microsoft 365-Entwicklerprogramm](/office/developer-program/microsoft-365-developer-program).

Sobald Sie über einen Mandanten verfügen, testen Sie einmaliges Anmelden und [Bereitstellung](../app-provisioning/use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client). 

Im Fall von **OIDC- oder OATH-Anwendungen**[registrieren Sie Ihre Anwendung](quickstart-register-app.md) als mehrinstanzenfähige Anwendung. ‎Wählen Sie unter „Unterstützte Kontotypen“ die Option „Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten“ aus.

Im Fall von **SAML- und WS-Fed-basierten Anwendungen** verwenden Sie eine generische SAML-Vorlage in Azure AD, um Anwendungen für das [SAML-basierte einmalige Anmelden zu konfigurieren](../manage-apps/configure-saml-single-sign-on.md).

Sie können bei Bedarf auch [eine Einzelmandantenanwendung in eine mehrinstanzenfähige Anwendung konvertieren](howto-convert-app-to-be-multi-tenant.md).


## <a name="step-5---create-and-publish-documentation"></a>Schritt 5: Erstellen und Veröffentlichen der Dokumentation

### <a name="documentation-on-your-site"></a>Dokumentation auf Ihrer Website

Die einfache Einführung ist ein bedeutender Faktor bei Entscheidungen über Unternehmenssoftware. Eine übersichtliche und leicht verständliche Dokumentation unterstützt Ihre Kunden bei der Einführung und reduziert die Supportkosten. In Zusammenarbeit mit Tausenden von Softwareanbietern hat Microsoft erfahren, was funktioniert.

Die Dokumentation auf Ihrer Website sollte mindestens die folgenden Punkte enthalten.

* Einführung in die Funktionalität des einmaligen Anmeldens
  * Unterstützte Protokolle
  * Version und SKU
  * Liste der unterstützten Identitätsanbieter mit Dokumentationslinks
* Lizenzierungsinformationen zu Ihrer Anwendung
* Rollenbasierte Zugriffssteuerung zum Konfiguration von einmaligem Anmelden
* Schritte der SSO-Konfiguration
  * Konfigurationselemente der Benutzeroberfläche für SAML mit vom Anbieter erwarteten Werten
  * Informationen des Dienstanbieters, die an Identitätsanbieter weitergegeben werden sollen
* Bei OIDC/OAuth
  * Liste der Berechtigungen, die für die Übereinstimmung mit geschäftlichen Begründungen erforderlich sind
* Testschritte für Pilotbenutzer
* Problembehandlungsinformationen inklusive Fehlercodes und Meldungen
* Supportmechanismen für Kunden
* Details zu Ihrem SCIM-Endpunkt, einschließlich der unterstützten Ressourcen und Attribute

### <a name="documentation-on-the-microsoft-site"></a>Dokumentation auf der Microsoft-Website

Wenn Sie Ihre Anwendung im Azure Active Directory-Anwendungskatalog auflisten, der Ihre Anwendung auch im Azure Marketplace veröffentlicht, generiert Microsoft eine Dokumentation für unsere gemeinsamen Kunden, die den schrittweisen Prozess erklärt. [Hier](../saas-apps/tutorial-list.md) sehen Sie ein Beispiel. Diese Dokumentation wird auf der Grundlage Ihrer Eingliederung in den Katalog erstellt und kann leicht aktualisiert werden, wenn Sie mit Ihrem GitHub-Konto Änderungen an Ihrer Anwendung vornehmen.


## <a name="step-6---submit-your-app"></a>Schritt 6: Übermitteln der App

Nachdem Sie mit einem Test sichergestellt haben, dass die Integration Ihrer Anwendung in Azure AD funktioniert, senden Sie Ihre Anwendungsanforderung im [Microsoft-Anwendungsnetzwerkportal](https://microsoft.sharepoint.com/teams/apponboarding/Apps).

Wenn Sie sich zum ersten Mal im Portal anzumelden versuchen, wird einer von zwei Bildschirmen angezeigt. 

Wenn Sie die Meldung „Das hat nicht funktioniert“ erhalten, müssen Sie sich an das [Azure AD-SSO-Integrationsteam](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) wenden. Geben Sie das E-Mail-Konto an, das Sie zum Senden der Anforderung verwenden möchten. Es empfiehlt sich, eine geschäftliche E-Mail-Adresse wie `name@yourbusiness.com` zu verwenden. Das Azure AD-Team fügt das Konto dann im Microsoft-Anwendungsnetzwerkportal hinzu.

Wenn die Seite „Zugriff anfordern“ angezeigt wird, geben Sie den geschäftlichen Grund an, und wählen Sie **Zugriff anfordern** aus.

Nachdem das Konto hinzugefügt wurde, können Sie sich im Microsoft Application Network-Portal anmelden und die Anforderung senden, indem Sie auf der Startseite die Kachel **Anforderung senden (ISV)** auswählen.

![Kachel „Anforderung senden (ISV)“ auf der Startseite](./media/howto-app-gallery-listing/homepage.png)

### <a name="issues-on-logging-into-portal"></a>Probleme bei der Anmeldung am Portal

Wenn dieser Fehler angezeigt wird, während Sie sich anmelden, finden Sie hier die Details zu diesem Problem sowie zum Beheben des Fehlers.

* Wenn Ihre Anmeldung blockiert wurde, wie unten gezeigt:

  ![Fehler beim Auflösen einer Anwendung im Katalog](./media/howto-app-gallery-listing/blocked.png)

**Hintergrund:**

Der Gastbenutzer befindet sich in einem Verbund mit einem Basismandanten, der ebenfalls in Azure AD enthalten ist. Der Gastbenutzer wird als hohes Risiko eingestuft. Microsoft gestattet Benutzern mit hohem Risiko keinen Zugriff auf Ressourcen. Alle Benutzer mit hohem Risiko (Mitarbeiter oder Gäste/Lieferanten) müssen Ihr Risiko für den Zugriff auf Microsoft-Ressourcen korrigieren bzw. eliminieren. Für Gastbenutzer stammt dieses Benutzerrisiko vom Basismandanten, und die Richtlinie stammt vom Ressourcenmandanten (in diesem Fall Microsoft).
 
**Sichere Lösungen:**

* Über MFA registrierte Gastbenutzer korrigieren ihre eigenen Benutzerrisiken. Dies kann durch den Gastbenutzer erfolgen, der eine gesicherte Kennwortänderung oder -zurücksetzung durchführt (https://aka.ms/sspr) in seinem Basismandanten (dazu sind MFA und SSPR im Basismandanten erforderlich)). Die gesicherte Kennwortänderung oder -zurücksetzung muss in Azure AD und nicht lokal initiiert werden.

* Gastbenutzer bitten ihre Administratoren, das Risiko zu korrigieren. In diesem Fall führt der Administrator eine Kennwortzurücksetzung aus (temporäre Kennwortgenerierung). Dies erfordert keinen Identitätsschutz. Der Administrator des Gastbenutzers kann zu https://aka.ms/RiskyUsers navigieren und auf „Kennwort zurücksetzen“ klicken.

* Gastbenutzer bitten ihre Administratoren, das Risiko zu schließen bzw. zu verwerfen. Auch dies erfordert keinen Identitätsschutz. Der Administrator kann zu https://aka.ms/RiskyUsers navigieren und dann auf „Benutzerrisiko verwerfen“ klicken. Administratoren müssen jedoch die erforderliche Sorgfalt walten lassen, um sicherzustellen, dass es sich um eine falsch positive Risikobewertung handelt, bevor sie das Benutzerrisiko schließen. Andernfalls setzen sie ihre Ressourcen und die von Microsoft aufs Spiel, indem sie eine Risikobewertung ohne Untersuchung unterdrücken.

> [!NOTE]
> Wenn Sie Probleme beim Zugriff haben, wenden Sie sich an das [Azure AD-SSO-Integrationsteam](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

### <a name="implementation-specific-options"></a>Implementieren spezieller Optionen
Wenn Sie Ihre im Katalog aufzulistende Anwendung mithilfe von OpenID Connect hinzufügen möchten, wählen Sie **OpenID Connect und OAuth 2.0** (wie hier gezeigt) aus.

![Auflisten einer OpenID Connect-Anwendung im Katalog](./media/howto-app-gallery-listing/openid.png)

Wenn Sie Ihre im Katalog aufzulistende Anwendung mithilfe von **SAML 2.0** oder **WS-Fed** hinzufügen möchten, wählen Sie **SAML 2.0/WS-Fed** (wie hier gezeigt) aus.

![Auflisten einer SAML 2.0- oder WS-Fed-Anwendung im Katalog](./media/howto-app-gallery-listing/saml.png)

Wenn Sie Ihre im Katalog aufzulistende Anwendung mithilfe von Kennwort-SSO hinzufügen möchten, wählen Sie **Kennwort-SSO (Benutzername und Kennwort)** wie hier gezeigt aus.

![Auflisten einer Kennwort-SSO-Anwendung im Katalog](./media/howto-app-gallery-listing/passwordsso.png)

Wenn Sie einen [SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md) 2.0-Endpunkt für die Benutzerbereitstellung implementieren, wählen Sie die dargestellte Option aus. Wenn Sie in der Onboardinganforderung das Schema bereitstellen, befolgen Sie [diese Anweisungen](../app-provisioning/export-import-provisioning-configuration.md), um Ihr Schema herunterzuladen. Wir verwenden das konfigurierte Schema beim Testen der nicht aus dem Katalog stammenden Anwendung zum Erstellen der Kataloganwendung. 

   ![Anfordern der Benutzerbereitstellung](./media/howto-app-gallery-listing/user-provisioning.png)

### <a name="update-or-remove-an-existing-listing"></a>Aktualisieren oder Entfernen einer vorhandenen Auflistung

Im [Microsoft Application Network-Portal](https://microsoft.sharepoint.com/teams/apponboarding/Apps) können Sie eine vorhandene Katalog-App aktualisieren oder entfernen.

![Auflisten einer SAML-Anwendung im Katalog](./media/howto-app-gallery-listing/updateorremove.png)

> [!NOTE]
> Wenn Sie Probleme mit dem Zugriff haben, lesen Sie den vorherigen Abschnitt zum Erstellen Ihres Kontos. Wenn dies nicht funktioniert, wenden Sie sich an das [Azure AD-SSO-Integrationsteam](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

### <a name="list-requests-by-customers"></a>Auflisten von Anforderungen nach Kunden

Kunden können eine Anforderung zum Auflisten einer Anwendung senden, indem sie **App-Anforderungen nach Kunden** > **Neue Anforderung übermitteln** auswählen.

![Zeigt die Kachel für die von Kunden angeforderten Apps](./media/howto-app-gallery-listing/customer-submit-request.png)

Nachstehend ist der Flow für die vom Kunden angeforderten Anwendungen dargestellt.

![Zeigt den Flow für die vom Kunden angeforderten Apps](./media/howto-app-gallery-listing/customer-request-2.png)


### <a name="timelines"></a>Zeitpläne

Das Auflisten einer SAML 2.0- oder WS-Fed-Anwendung im Katalog dauert etwa 7 bis 10 Werktage.

![Zeitskala für das Auflisten einer SAML-Anwendung im Katalog](./media/howto-app-gallery-listing/timeline.png)

Das Auflisten einer OpenID Connect-Anwendung im Katalog dauert etwa 2 bis 5 Werktage.

![Zeitskala für das Auflisten einer OpenID Connect-Anwendung im Katalog](./media/howto-app-gallery-listing/timeline2.png)

### <a name="escalations"></a>Eskalationen

Bei Eskalationen senden Sie eine E-Mail an das [Azure AD-SSO-Integrationsteam](mailto:SaaSApplicationIntegrations@service.microsoft.com). Wir melden uns so schnell wie möglich bei Ihnen.


## <a name="step-7---join-the-microsoft-partner-network"></a>Schritt 7: Beitreten zum Microsoft Partner Network
Das Microsoft Partner Network bietet sofortigen Zugriff auf exklusive Ressourcen, Programme, Tools und Verbindungen. Unter [Erreichen Sie Geschäftskunden](https://partner.microsoft.com/explore/commercial#gtm) wird erläutert, wie Sie Mitglied des Netzwerks werden und Ihren Markteinführungsplan erstellen.


## <a name="next-steps"></a>Nächste Schritte
* [Erstellen eines SCIM-Endpunkts und Konfigurieren der Benutzerbereitstellung](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [Authentifizierungsszenarien für Azure AD](authentication-flows-app-scenarios.md)
