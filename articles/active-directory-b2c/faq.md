---
title: Häufig gestellte Fragen zu Azure Active Directory B2C
description: Antworten auf häufig gestellte Fragen zu Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 30b7788419695db7ee5d4b81714bf55248a41b4a
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108721"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: Häufig gestellte Fragen (FAQ)

Auf dieser Seite werden häufig gestellte Fragen zu Azure Active Directory B2C (Azure AD B2C) beantwortet. Die Seite wird bei Bedarf aktualisiert.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Warum kann ich nicht auf die Azure AD B2C-Erweiterung im Azure-Portal zugreifen?

Es gibt zwei häufige Gründe, warum Sie nicht auf die Azure AD-Erweiterung zugreifen können. In Azure AD B2C muss Ihnen im Verzeichnis die Benutzerrolle „Globaler Administrator“ zugewiesen sein. Wenden Sie sich an den Administrator, wenn Sie denken, dass Sie Zugriff haben sollten. Wenn Sie über globale Administratorrechte verfügen, stellen Sie sicher, dass Sie sich in einem Azure AD B2C-Verzeichnis und nicht in einem Azure Active Directory-Verzeichnis befinden. Hier finden Sie Anweisungen zum [Erstellen eines Azure AD B2C-Mandanten](tutorial-create-tenant.md).

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Kann ich Azure AD B2C-Features in meinem vorhandenen mitarbeiterbasierten Azure AD Directory-Mandanten nutzen?

Azure AD und Azure AD B2C sind separate Produktangebote, die nicht zusammen unter demselben Mandanten verwendet werden können. Ein Azure AD-Mandant repräsentiert eine Organisation. Ein Azure AD B2C-Mandant repräsentiert eine Sammlung mit Identitäten, die mit Anwendungen der vertrauenden Seite verwendet werden sollen. Durch Hinzufügen eines **neuen OpenID Connect-Anbieters** unter **Azure AD B2C > Identitätsanbieter** oder mit benutzerdefinierten Richtlinien kann Azure AD B2C einen Verbund mit Azure AD herstellen, um die Authentifizierung von Mitarbeitern in einer Organisation zu ermöglichen.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-microsoft-365"></a>Kann ich Azure AD B2C zum Bereitstellen der Anmeldung für soziale Netzwerke (Facebook und Google+) in Microsoft 365 verwenden?

Azure AD B2C kann zum Authentifizieren von Benutzern bei Microsoft 365 nicht verwendet werden. Azure AD ist die Microsoft-Lösung zum Verwalten des Mitarbeiterzugriffs auf SaaS-Apps. Sie verfügt über Features, die für diesen Zweck konzipiert wurden, z.B. Lizenzierung und bedingter Zugriff. Azure AD B2C stellt eine Plattform für die Identitäts- und Zugriffsverwaltung bereit, die zum Erstellen von Webanwendungen und mobilen Anwendungen verwendet werden kann. Wenn Azure AD B2C für den Verbund mit einem Azure AD-Mandanten konfiguriert ist, verwaltet der Azure AD-Mandant den Mitarbeiterzugriff auf Anwendungen, die auf Azure AD B2C basieren.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Was sind lokale Konten in Azure AD B2C? Wie unterscheiden sie sich von Geschäfts-, Schul- oder Unikonten in Azure AD?

Bei einem Azure AD-Mandanten melden sich Benutzer, die zum Mandanten gehören, mit einer E-Mail-Adresse der Form `<xyz>@<tenant domain>` an. `<tenant domain>` (Mandantendomäne) ist eine der überprüften Domänen des Mandanten oder die Anfangsdomäne `<...>.onmicrosoft.com`. Dieser Kontotyp ist ein Geschäfts-, Schul- oder Unikonto.

Bei einem Azure AD B2C-Mandanten kann sich der Benutzer in den meisten Apps mit einer beliebigen E-Mail-Adresse anmelden (also beispielsweise mit joe@comcast.net, bob@gmail.com, sarah@contoso.com oder jim@live.com). Diese Art von Konto ist ein lokales Konto. Wir unterstützen auch beliebige Benutzernamen als lokale Konten (z.B. joe, bob, sarah oder jim). Sie können einen dieser beiden lokalen Kontotypen verwenden, wenn Sie im Azure-Portal Identitätsanbieter für Azure AD B2C konfigurieren. Wählen Sie in Ihrem Azure AD B2C-Mandanten **Identitätsanbieter**, **Lokale Konten** und dann **Benutzername** aus.

Benutzerkonten für Anwendungen können über einen Registrierungsbenutzerflow, einen Benutzerflow für Registrierung oder Anmeldung oder die Microsoft Graph-API sowie im Azure-Portal erstellt werden.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Welche Identitätsanbieter aus sozialen Netzwerken werden derzeit unterstützt? Welche sollen in Zukunft unterstützt werden?

Wir unterstützen derzeit verschiedene Identitätsanbieter sozialer Netzwerke wie Amazon, Facebook, GitHub (Vorschauversion), Google, LinkedIn, Microsoft-Konto (MSA), QQ (Vorschauversion), Twitter, WeChat (Vorschauversion) und Weibo (Vorschauversion). Die Unterstützung für andere beliebte soziale Netzwerke als Identitätsanbieter wird je nach Kundennachfrage jeweils geprüft.

Azure AD B2C unterstützt auch [benutzerdefinierte Richtlinien](custom-policy-overview.md). Dadurch können Sie eigene Richtlinien mit einem beliebigen Identitätsanbieter erstellen, der [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) oder SAML unterstützt. Machen Sie sich durch das [Starter Pack für benutzerdefinierte Richtlinien](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack) mit benutzerdefinierten Richtlinien vertraut.

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Kann ich Bereiche konfigurieren, um von verschiedenen Identitätsanbietern aus sozialen Netzwerken mehr Informationen zu Endkunden zu erhalten?

Nein. Die Standardbereiche für unsere unterstützten sozialen Identitätsanbieter sind:

* Facebook: email
* Google+: email
* Microsoft-Konto: openid email profile
* Amazon: profile
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Muss meine Anwendung unter Azure ausgeführt werden, damit sie mit Azure AD B2C funktioniert?

Nein, Sie können Ihre Anwendung überall hosten (in der Cloud oder lokal). Für die Interaktion mit Azure AD B2C muss nur die Fähigkeit zum Senden und Empfangen von HTTP-Anforderungen über öffentlich zugängliche Endpunkte möglich sein.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Ich habe mehrere Azure AD B2C-Mandanten. Wie kann ich sie im Azure-Portal verwalten?

Vor dem Öffnen von „Azure AD B2C“ im Menü auf der linken Seite im Azure-Portal müssen Sie zum Verzeichnis wechseln, das Sie verwalten möchten. Wechseln Sie Verzeichnisse, indem Sie rechts oben im Azure-Portal auf Ihre Identität klicken und dann ein Verzeichnis in der Dropdownliste auswählen, die angezeigt wird.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Wie kann ich Bestätigungs-E-Mails anpassen (Inhalt und das Feld „Von:“), die von Azure AD B2C gesendet werden?

Sie können das [Feature für Unternehmensbranding](../active-directory/fundamentals/customize-branding.md) verwenden, um den Inhalt von Bestätigungs-E-Mails anzupassen. Insbesondere diese beiden Elemente einer E-Mail können angepasst werden:

* **Bannerlogo**: Wird unten rechts angezeigt.
* **Hintergrundfarbe**: Wird oben angezeigt.

    ![Screenshot einer angepassten Bestätigungs-E-Mail](./media/faq/company-branded-verification-email.png)

Die E-Mail-Signatur enthält den Namen des Azure AD B2C-Mandanten, den Sie bei der ursprünglichen Erstellung des Azure AD B2C-Mandanten angegeben haben. Sie können den Namen mit folgenden Schritten ändern:

1. Melden Sie sich als globaler Administrator beim [Azure-Portal](https://portal.azure.com/) an.
1. Öffnen Sie das Blatt **Azure Active Directory**.
1. Klicken Sie auf die Registerkarte **Eigenschaften**.
1. Ändern Sie das Feld **Name**.
1. Klicken Sie oben auf der Seite auf **Speichern**.

Zurzeit besteht keine Möglichkeit, das Feld „Von:“ der E-Mail zu ändern.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Wie kann ich meine vorhandenen Benutzernamen, Kennwörter und Profile von meiner Datenbank zu Azure AD B2C migrieren?

Sie können Ihr Migrationstool mithilfe der Microsoft Graph-API schreiben. Weitere Informationen finden Sie im [Handbuch für die Benutzermigration](user-migration.md).

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Welcher Kennwortbenutzerflow wird für lokale Konten in Azure AD B2C verwendet?

Der Azure AD B2C-Kennwortbenutzerflow für lokale Konten basiert auf der Richtlinie für Azure AD. Die Azure AD B2C-Benutzerflows zur Registrierung, zur Registrierung oder Anmeldung und zur Kennwortrücksetzung verwenden sichere Kennwörter, die nicht ablaufen. Weitere Informationen finden Sie unter [Kennwortrichtlinien und -einschränkungen in Azure Active Directory](../active-directory/authentication/concept-sspr-policy.md).

Informationen zu Kontosperren und Kennwörtern finden Sie unter [Verwalten von Bedrohungen für Ressourcen und Daten in Azure Active Directory B2C](threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Kann ich Azure AD Connect zum Migrieren von Endkundenidentitäten, die in meinem lokalen Active Directory gespeichert sind, zu Azure AD B2C verwenden?

Nein. Azure AD Connect ist nicht für die Verwendung mit Azure AD B2C ausgelegt. Sie können erwägen, die [Microsoft Graph-API](manage-user-accounts-graph-api.md) für die Benutzermigration zu verwenden. Weitere Informationen finden Sie im [Handbuch für die Benutzermigration](user-migration.md).

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Kann meine App Azure AD B2C-Seiten in einem iFrame öffnen?

Nein. Aus Sicherheitsgründen können Azure AD B2C-Seiten nicht in einem iFrame geöffnet werden. Unser Dienst kommuniziert mit dem Browser, um die Verwendung von iFrames zu unterbinden. In allgemeinen Sicherheitskreisen und in der OAUTH2-Spezifikation wird davon abgeraten, iFrames für Identitätszwecke zu verwenden, weil dies mit einem Clickjacking-Risiko verbunden ist.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Funktioniert Azure AD B2C in Verbindung mit CRM-Systemen wie Microsoft Dynamics?

Die Integration in das Microsoft Dynamics 365-Portal ist möglich. Siehe [Konfigurieren des Dynamics 365-Portals für die Authentifizierung mit Azure AD B2C](/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Funktioniert Azure AD B2C in Verbindung mit SharePoint On-Premises 2016 oder früher?

Azure AD B2C ist nicht für das SharePoint-Freigabeszenario für externe Partner vorgesehen. Informationen hierzu finden Sie unter [Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/).

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Sollte ich Azure AD B2C oder B2B zum Verwalten externer Identitäten verwenden?

Unter [Vergleich von B2B Collaboration und B2C in Azure AD](../active-directory/external-identities/compare-with-b2c.md) erfahren Sie mehr über das Anwenden der entsprechenden Features in Ihren Szenarien mit externen Identitäten.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Welche Funktionen für Berichterstellung und Überwachung bietet Azure AD B2C? Sind die Funktionen mit denen in Azure AD Premium identisch?

Nein. Azure AD B2C unterstützt nicht den gleichen Berichtssatz wie Azure AD Premium. Es gibt aber viele Gemeinsamkeiten:

* **Anmeldeberichte** enthalten einen Eintrag für jede Anmeldung mit reduzierten Details.
* **Überwachungsberichte** enthalten die Administratoraktivität sowie die Anwendungsaktivität.
* **Verwendungsberichte** enthalten die Anzahl von Benutzern, die Anzahl von Anmeldungen und den MFA-Umfang.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Kann ich die Benutzeroberfläche von Seiten lokalisieren, die von Azure AD B2C bereitgestellt werden? Welche Sprachen werden unterstützt?

Ja, siehe [Sprachanpassung in Azure Active Directory B2C](language-customization.md). Wir bieten Übersetzungen für 36 Sprachen an, und Sie können alle Zeichenfolgen gemäß Ihren Anforderungen außer Kraft setzen und anpassen.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>Kann ich auf meinen von Azure AD B2C bereitgestellten Seiten für die Registrierung und Anmeldung meine eigenen URLs verwenden? Kann ich beispielsweise die URL von „contoso.b2clogin.com“ in „login.contoso.com“ ändern?

Derzeit ist dies nicht möglich. Dieses Feature ist aber geplant. Sie erreichen dieses Ziel nicht, indem Sie Ihre Domäne auf der Registerkarte **Domänen** im Azure-Portal überprüfen. Mit „b2clogin.com“ bieten wir jedoch eine [neutrale Domäne auf oberster Ebene](b2clogin.md) an, sodass die externe Darstellung ohne Erwähnung von Microsoft implementiert werden kann.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Wie lösche ich meinen Azure AD B2C-Mandanten?

Führen Sie die folgenden Schritte aus, um Ihren Azure AD B2C-Mandanten zu löschen.

Sie können die neue einheitliche Benutzeroberfläche für **App-Registrierungen** oder die alte Benutzeroberfläche für **Anwendungen (Legacy)** verwenden. [Weitere Informationen zur neuen Oberfläche](./app-registrations-training-guide.md)

#### <a name="app-registrations"></a>[App-Registrierungen](#tab/app-reg-ga/)

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com/) als *Abonnementadministrator* an. Verwenden Sie dasselbe Geschäfts-, Schul- oder Unikonto bzw. dasselbe Microsoft-Konto, mit dem Sie sich bei Azure registriert haben.
1. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Löschen Sie alle **Benutzerabläufe (Richtlinien)** in Ihrem Azure AD B2C-Mandanten.
1. Klicken Sie auf **App-Registrierungen** und dann auf die Registerkarte **Alle Anwendungen**.
1. Löschen Sie alle Anwendungen, die Sie registriert haben.
1. Löschen Sie **b2c-extensions-app**.
1. Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
1. Wählen Sie nacheinander die einzelnen Benutzer aus (mit Ausnahme des *Abonnementadministrator*-Benutzers, als der Sie aktuell angemeldet sind). Wählen Sie unten auf der Seite **Löschen** aus, und klicken Sie auf **Ja**, wenn Sie zur Bestätigung aufgefordert werden.
1. Wählen Sie im linken Menü **Azure Active Directory** aus.
1. Wählen Sie unter **Verwalten** die Option **Benutzereinstellungen** aus.
1. Wählen Sie unter **Verwalten** die Option **Eigenschaften** aus.
1. Wählen Sie unter **Zugriffsverwaltung für Azure-Ressourcen** die Option **Ja** und dann **Speichern** aus.
1. Melden Sie sich vom Azure-Portal ab und dann wieder an, um Ihren Zugriff zu aktualisieren.
1. Wählen Sie im linken Menü **Azure Active Directory** aus.
1. Wählen Sie auf der Seite **Übersicht** die Option **Mandant löschen** aus. Folgen Sie den Anweisungen auf dem Bildschirm, um den Prozess abzuschließen.

#### <a name="applications-legacy"></a>[Anwendungen (Legacy)](#tab/applications-legacy/)

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com/) als *Abonnementadministrator* an. Verwenden Sie dasselbe Geschäfts-, Schul- oder Unikonto bzw. dasselbe Microsoft-Konto, mit dem Sie sich bei Azure registriert haben.
1. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Löschen Sie alle **Benutzerabläufe (Richtlinien)** in Ihrem Azure AD B2C-Mandanten.
1. Löschen Sie alle **Anwendungen (Legacy)** , die Sie in Ihrem Azure AD B2C-Mandanten registriert haben.
1. Wählen Sie im linken Menü **Azure Active Directory** aus.
1. Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
1. Wählen Sie nacheinander die einzelnen Benutzer aus (mit Ausnahme des *Abonnementadministrator*-Benutzers, als der Sie aktuell angemeldet sind). Wählen Sie unten auf der Seite **Löschen** aus, und klicken Sie auf **Ja**, wenn Sie zur Bestätigung aufgefordert werden.
1. Wählen Sie unter **Verwalten** die Option **App-Registrierungen** aus.
1. Wählen Sie **Alle Anwendungen anzeigen** aus.
1. Wählen Sie die Anwendung **b2c-extensions-app** und anschließend **Löschen** aus, und klicken Sie dann auf **Ja**, wenn Sie zur Bestätigung aufgefordert werden.
1. Wählen Sie unter **Verwalten** die Option **Benutzereinstellungen** aus.
1. Wählen Sie unter **LinkedIn-Kontoverbindungen** die Option **Nein** und dann **Speichern** aus (sofern vorhanden).
1. Wählen Sie unter **Verwalten** die Option **Eigenschaften** aus.
1. Wählen Sie unter **Zugriffsverwaltung für Azure-Ressourcen** die Option **Ja** und dann **Speichern** aus.
1. Melden Sie sich vom Azure-Portal ab und dann wieder an, um Ihren Zugriff zu aktualisieren.
1. Wählen Sie im linken Menü **Azure Active Directory** aus.
1. Wählen Sie auf der Seite **Übersicht** die Option **Verzeichnis löschen** aus. Folgen Sie den Anweisungen auf dem Bildschirm, um den Prozess abzuschließen.

* * *

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Kann ich Azure AD B2C als Teil der Enterprise Mobility Suite erwerben?

Nein. Azure AD B2C ist ein Azure-Dienst mit nutzungsbasierter Bezahlung und nicht Teil der Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Wie melde ich Probleme mit Azure AD B2C?

Siehe [Senden von Supportanfragen für Azure Active Directory B2C](support-options.md).