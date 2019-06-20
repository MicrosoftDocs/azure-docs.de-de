---
title: Einmaliges Anmelden – nicht im Katalog enthaltene Anwendungen – Microsoft Identity Platform | Microsoft-Dokumentation
description: Konfigurieren des einmaligen Anmeldens bei nicht im Katalog enthaltenen Anwendungen auf der Microsoft Identity Platform (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: e34e6257b4800387470cdc1b7d624bf3ebd1d3e6
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989256"
---
# <a name="configure-single-sign-on-to-non-gallery-applications-in-microsoft-identity-platform"></a>Konfigurieren des einmaligen Anmeldens bei nicht im Katalog enthaltenen Anwendungen auf der Microsoft Identity Platform

Dieser Artikel beschreibt ein Feature, mit dem Administratoren das einmalige Anmelden (Single Sign-On, SSO) bei Anwendungen konfigurieren können, die im App-Katalog der Microsoft Identity Platform nicht vorhanden sind, *ohne dafür Code schreiben zu müssen*.

Wenn Sie stattdessen nach einer Anleitung für Entwickler zum Integrieren von benutzerdefinierten Apps in Azure AD mithilfe von Code suchen, finden Sie diese unter [Authentifizierungsszenarien für Azure AD](../develop/authentication-scenarios.md).

Der Anwendungskatalog der Microsoft Identity Platform enthält eine Liste der Anwendungen, die bekanntermaßen eine Form des einmaligen Anmeldens mit der Microsoft Identity Platform unterstützen, wie [in diesem Artikel](what-is-single-sign-on.md)beschrieben. Wenn Sie (als IT-Spezialist oder Systemintegrator in Ihrer Organisation) die Anwendung gefunden haben, mit der Sie eine Verbindung herstellen möchten, können Sie mithilfe der Schritt-für-Schritt-Anleitungen im Azure-Portal das einmalige Anmelden aktivieren.

Die folgenden Funktionen sind gemäß Ihrer Lizenzvereinbarung ebenfalls verfügbar. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/active-directory/).

- Self-Service-Integration von Anwendungen, die ein modernes Protokoll wie [OpenID Connect/OAuth](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols) verwenden, um Benutzer zu authentifizieren und Token für [Microsoft Graph](https://graph.microsoft.com) abzurufen
- Self-Service-Integration von Anwendungen, die [Security Assertion Markup Language (SAML) 2.0](https://wikipedia.org/wiki/SAML_2.0)-Identitätsanbieter unterstützen (vom Dienstanbieter (SP) oder vom Identitätsanbieter (IdP) initiiert)
- Self-Service-Integration für Webanwendungen, die über eine HTML-basierte Anmeldeseite mit [kennwortbasierter einmaliger Anmeldung (Single Sign-On, SSO)](what-is-single-sign-on.md#password-based-sso)
- Self-Service-Verbindung für Anwendungen, die das [SCIM-Protokoll (System for Cross-Domain Identity Management) verwenden](use-scim-to-provision-users-and-groups.md)
- Möglichkeit zum Hinzufügen von Links zu Anwendungen im [Office 365-App-Startfeld](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) oder im [Azure AD-Zugriffsbereich](what-is-single-sign-on.md#linked-sign-on)

Diese Funktionen können die Self-Service-Integration einer von Ihnen verwendeten SaaS-Anwendung (Software-as-a-Service) umfassen, selbst wenn diese Anwendung noch nicht in den Azure AD-Anwendungskatalog aufgenommen wurde. Eine weitere Funktion ist die Self-Service-Integration der Webanwendung eines Drittanbieters, die Ihre Organisation lokal oder in der Cloud auf von Ihnen gesteuerten Servern bereitgestellt hat.

Diese Funktionen – auch als *App-Integrationsvorlagen* bezeichnet – bieten standardbasierte Verbindungspunkte für Anwendungen, die eine SAML-, SCIM- oder formularbasierte Authentifizierung unterstützen. Diese Funktionen bieten flexible Optionen und Einstellungen für die Kompatibilität mit einer umfassenden Reihe von Anwendungen.

## <a name="adding-an-unlisted-application"></a>Hinzufügen einer nicht aufgeführten Anwendung

Die Microsoft Identity Platform stellt zwei Mechanismen zum Registrieren von Anwendungen bereit.

Eine Anwendung, die ein modernes Protokoll wie [OpenID Connect/OAuth](../develop/active-directory-v2-protocols.md) zum Authentifizieren der Benutzer verwendet, wird über das [Anwendungsregistrierungsportal](../develop/quickstart-register-app.md) registriert.

Zum Registrieren von Anwendungen, die andere Arten von [unterstützten Authentifizierungsmechanismen](what-is-single-sign-on.md) verwenden – wie z.B. das [SAML](../develop/single-sign-on-saml-protocol.md)-Protokoll –, verwenden Sie das Blatt **Unternehmensanwendungen**, um diese Anwendungen mit der Microsoft Identity Platform zu verknüpfen.

Um eine nicht gelistete Anwendung mithilfe einer Vorlage für die App-Integration zu verknüpfen, führen Sie folgende Schritte aus:

1. Melden Sie sich mit Ihrem Administratorkonto für die Microsoft Identity Platform beim [Azure Active Directory-Portal](https://aad.portal.azure.com/) an.
2. Wählen Sie **Unternehmensanwendungen** > **Neue Anwendung** aus.
3. (Optional, aber empfohlen) Geben Sie im Suchfeld **Aus Katalog hinzufügen** den Anzeigename der Anwendung ein. Wenn die Anwendung in den Suchergebnissen angezeigt wird, wählen Sie sie aus und überspringen den Rest dieses Verfahrens.
4. Wählen Sie **Nicht-Kataloganwendung** aus. Die Seite **Eigene Anwendung hinzufügen** wird angezeigt.

   ![Anwendung hinzufügen](./media/configure-single-sign-on-non-gallery-applications/add-your-own-application.png)
5. Geben Sie den Anzeigenamen für Ihre neue Anwendung ein.
6. Wählen Sie **Hinzufügen**.

Das Hinzufügen einer Anwendung mit dieser Methode gestaltet sich ähnlich benutzerfreundlich wie bei vorab integrierten Anwendungen. Wählen Sie zunächst in der Randleiste der Anwendung die Option **Einmaliges Anmelden** aus. Die nächste Seite (**SSO-Methode auswählen**) zeigt die Optionen für die Konfiguration des einmaligen Anmeldens an:

- **SAML**
- **Kennwortbasiert**
- **Verknüpft**

![SSO-Methode auswählen](./media/configure-single-sign-on-non-gallery-applications/select-a-single-sign-on-method.png)

Weitere Informationen zu diesen Optionen finden Sie in den folgenden Abschnitten dieses Artikels.

## <a name="saml-based-single-sign-on"></a>SAML-basiertes einmaliges Anmelden

Wählen Sie die Option **SAML** aus, um die SAML-basierte Authentifizierung für die Anwendung zu konfigurieren. (Diese Option setzt voraus, dass die Anwendung SAML 2.0 unterstützt.) Die Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** wird angezeigt.

![Einmaliges Anmelden (SSO) mit SAML einrichten](./media/configure-single-sign-on-non-gallery-applications/set-up-single-sign-on-with-saml.png)

Diese Seite enthält fünf Rubriken:

| Nummer der Rubrik | Name der Rubrik | Hier finden Sie eine Übersicht über die Rubrik: |
| --- | --- | --- |
| 1 | **Grundlegende SAML-Konfiguration** | [Eingabe der SAML-Basiskonfiguration](#enter-basic-saml-configuration) |
| 2 | **Benutzerattribute und Ansprüche** | [Überprüfen oder Anpassen der im SAML-Token ausgestellten Ansprüche](#review-or-customize-the-claims-issued-in-the-saml-token) |
| 3 | **SAML-Signaturzertifikat** | [Überprüfen von Zertifikatablaufdaten, Status und E-Mail-Benachrichtigung](#review-certificate-expiration-data-status-and-email-notification) |
| 4 | **\<Anwendungsname> einrichten** | [Einrichten der Zielanwendung](#set-up-target-application) |
| 5 | **Einmaliges Anmelden mit \<Anwendungsname> testen** | [Testen der SAML-Anwendung](#test-the-saml-application) |

Informieren Sie sich jetzt darüber, wie die SAML-Funktionen der Anwendung genutzt werden können, bevor Sie fortfahren. Erfassen Sie die Informationen für die folgenden Abschnitte, um das einmalige Anmelden zwischen der Anwendung und Azure AD zu konfigurieren.

### <a name="enter-basic-saml-configuration"></a>Eingabe der SAML-Basiskonfiguration

Um Azure AD einzurichten, wechseln Sie zur Rubrik **Grundlegende SAML-Konfiguration**, und wählen Sie das Symbol **Bearbeiten** aus (ein Bleistiftsymbol). Sie können die Werte manuell eingeben oder eine Metadatendatei hochladen, um die Werte der Felder zu extrahieren.

![Grundlegende SAML-Konfiguration](./media/configure-single-sign-on-non-gallery-applications/basic-saml-configuration.png)

Die folgenden beiden Felder sind erforderlich:

- **Bezeichner**: Dieser Wert muss die Anwendung, für die das einmalige Anmelden konfiguriert wird, eindeutig identifizieren. Sie finden diesen Wert als **Issuer**-Element (Aussteller) in der **AuthnRequest** (SAML-Anforderung), die von der Anwendung gesendet wird. Dieser Wert ist auch als **Entitäts-ID** in SAML-Metadaten enthalten, die von der Anwendung bereitgestellt werden. Informationen zu den Werten für **Entity ID** (Entitäts-ID) bzw. **Audience** (Zielgruppe) finden Sie in der SAML-Dokumentation der Anwendung.

  Der folgende Code zeigt, wie der **Identifier** (Bezeichner) bzw. **Issuer** (Aussteller) in der SAML-Anforderung aussieht, die von der Anwendung an Azure AD gesendet wird:

  ```xml
  <samlp:AuthnRequest
  xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
  ID="id6c1c178c166d486687be4aaf5e482730"
  Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
  xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
  </samlp:AuthnRequest>
  ```

- **Antwort-URL**: Unter der Antwort-URL erwartet die Anwendung den Empfang des SAML-Tokens. Diese URL wird auch als URL für den Assertionsverbraucherdienst (Assertion Consumer Service, ACS) bezeichnet. In der SAML-Dokumentation der Anwendung finden Sie Details dazu, wie die Antwort-URL bzw. ACS-URL des SAML-Tokens lautet.

  Zur Konfiguration mehrerer replyURLs-Elemente können Sie das folgende PowerShell-Skript verwenden.

  ```powershell
  $sp = Get-AzureADServicePrincipal -SearchString "<Exact app name>"
  $app = Get-AzureADApplication -SearchString "<Exact app name>"
  $urllist = New-Object "System.Collections.Generic.List[String]"
  $urllist.Add("<reply URL 1>")
  $urllist.Add("<reply URL 2>")
  $urllist.Add("<reply URL 3>")
  Set-AzureADApplication -ObjectId $app.ObjectId -ReplyUrls $urllist
  Set-AzureADServicePrincipal -ObjectId $sp.ObjectId -ReplyUrls $urllist
  ```

Die folgenden drei Felder sind optional:

- **Anmelde-URL** (nur SP-initiiert). Dieser Wert gibt an, wo sich Benutzer bei dieser Anwendung anmelden. Sofern die Anwendung für das SP-initiierte einmalige Anmelden konfiguriert ist, geschieht Folgendes, wenn ein Benutzer zu dieser URL navigiert: Der Dienstanbieter führt die erforderliche Umleitung an Azure AD durch, um den Benutzer zu authentifizieren und anzumelden. Wenn Sie dieses Feld ausfüllen, verwendet Azure AD diese URL, um die Anwendung aus Office 365 und dem Azure AD-Zugriffsbereich zu starten. Wenn Sie dieses Feld leer lassen, führt Azure AD stattdessen eine IdP-initiierte Anmeldung durch, während die Anwendung aus Office 365, dem Azure AD-Zugriffsbereich oder über die Azure AD-SSO-URL (die Sie von der Seite **Dashboard** kopieren können) gestartet wird.

- **Relayzustand**: Sie können einen Relayzustand in SAML angeben, um die Anwendung anzuweisen, wohin Benutzer nach der Authentifizierung umgeleitet werden sollen. Bei diesem Wert handelt es sich in der Regel um eine URL oder einen URL-Pfad, über die bzw. den Benutzer zu einer bestimmten Stelle in der Anwendung gelangen.

- **Abmelde-URL**: Dieser Wert wird verwendet, um die SAML-Abmeldeantwort an die Anwendung zurückzusenden.

Weitere Informationen finden Sie unter [SAML-Protokoll für einmaliges Anmelden](../develop/single-sign-on-saml-protocol.md).

### <a name="review-or-customize-the-claims-issued-in-the-saml-token"></a>Überprüfen oder Anpassen der im SAML-Token ausgestellten Ansprüche

Wenn sich ein Benutzer bei der Anwendung authentifiziert, stellt Azure AD der Anwendung ein SAML-Token aus, das Informationen (so genannte „Ansprüche“) über den Benutzer enthält, die ihn eindeutig identifizieren. Zu diesen Informationen gehören standardmäßig der Benutzername, die E-Mail-Adresse, der Vorname und der Nachname des Benutzers.

So können Sie die im SAML-Token an die Anwendung gesendeten Ansprüche anzeigen oder bearbeiten:

- Wechseln Sie zur Rubrik **Benutzerattribute und Ansprüche**, und klicken Sie auf das Symbol **Bearbeiten**. Die Seite **Benutzerattribute und Ansprüche** wird angezeigt.

![Benutzerattribute und Ansprüche](./media/configure-single-sign-on-non-gallery-applications/user-attributes-and-claims.png)

Sie müssen die im SAML-Token ausgestellten Ansprüche aus zwei Gründen möglicherweise bearbeiten:

- Die Anwendung wurde erfordert einen anderen Satz an Anspruchs-URIs oder Anspruchswerten.
- Für Ihre Anwendung muss der Anspruch **Namensbezeichner** einen anderen Wert als den Benutzernamen (auch als Benutzerprinzipalname bezeichnet) aufweisen, der auf der Microsoft Identity Platform gespeichert ist.

Weitere Informationen finden Sie unter [Gewusst wie: Anpassen von Ansprüchen im SAML-Token für Unternehmensanwendungen](../develop/active-directory-saml-claims-customization.md).

### <a name="review-certificate-expiration-data-status-and-email-notification"></a>Überprüfen von Zertifikatablaufdaten, Status und E-Mail-Benachrichtigung

Wenn Sie eine Katalog- oder eine Nicht-Kataloganwendung erstellen, erstellt Azure AD ein anwendungsspezifisches Zertifikat, das drei Jahre nach dem Erstellungsdatum abläuft. Sie benötigen dieses Zertifikat, um die Vertrauensstellung zwischen Azure AD und der Anwendung einzurichten. Einzelheiten zum Zertifikatsformat finden Sie in der SAML-Dokumentation der Anwendung.

In Azure AD können Sie das aktive Zertifikat im Base64- oder Raw-Format direkt von der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** herunterladen. Alternativ dazu können Sie das aktive Zertifikat erhalten, indem Sie die XML-Datei mit den Anwendungsmetadaten herunterladen oder die App-Verbundmetadaten-URL verwenden.

Um Ihre Zertifikate (aktiv oder inaktiv) anzuzeigen, zu erstellen oder herunterzuladen, wechseln Sie zur Rubrik **SAML-Signaturzertifikat** und wählen das Symbol **Bearbeiten** aus. Die Seite **SAML-Signaturzertifikat** wird angezeigt.

![SAML-Signaturzertifikat](./media/configure-single-sign-on-non-gallery-applications/saml-signing-certificate.png)

Stellen Sie sicher, dass das Zertifikat über Folgendes verfügt:

- *Das gewünschte Ablaufdatum*. Sie können ein Ablaufdatum konfigurieren, das bis zu drei Jahre in der Zukunft liegt.
- *Aktiver Status des gewünschten Zertifikats*. Wenn der Status **Inaktiv** lautet, ändern Sie diesen zu **Aktiv**. Um den Status zu ändern, klicken Sie mit der rechten Maustaste auf die Zeile des gewünschten Zertifikats, und wählen Sie **Zertifikat als aktiv festlegen** aus.
- *Die richtige Anmeldeoption und der entsprechende Algorithmus*.
- *Die richtige(n) E-Mail-Adresse(n) für Benachrichtigungen*. Wenn sich das aktive Zertifikat dem Ablaufdatum nähert, sendet Azure AD eine Benachrichtigung an die in diesem Feld konfigurierte E-Mail-Adresse.  

Weitere Informationen finden Sie unter [Verwalten von Zertifikaten für die einmalige Verbundanmeldung in Azure Active Directory](manage-certificates-for-federated-single-sign-on.md) und [Erweiterte Optionen für die Zertifikatsignatur im SAML-Token für Katalog-Apps in Azure Active Directory](certificate-signing-options.md).

### <a name="set-up-target-application"></a>Einrichten der Zielanwendung

Um die Anwendung für das einmalige Anmelden zu konfigurieren, suchen Sie zunächst die Dokumentation der Anwendung. Wechseln Sie zur Rubrik **\<Anwendungsname> einrichten**, und wählen Sie **Schrittanleitung anzeigen** aus. Die Dokumentation wird auf der Seite **Anmeldung konfigurieren** angezeigt. Auf dieser Seite finden Sie Informationen dazu, wie Sie die Werte für **Anmelde-URL**, **Azure AD-Bezeichner** und **Abmelde-URL** in der Rubrik **\<Anwendungsname> einrichten** ausfüllen.

Die erforderlichen Werte variieren je nach Anwendung. Einzelheiten finden Sie in der SAML-Dokumentation der Anwendung. Die Werte für **Anmelde-URL** und **Abmelde-URL** werden in den gleichen Endpunkt aufgelöst – hierbei handelt es sich um den Endpunkt, in dem die SAML-Anforderungen für Ihre Azure AD-Instanz verarbeitet werden. **Azure AD-Bezeichner** ist der Wert für den **Issuer** (Aussteller) in dem SAML-Token, das für die Anwendung ausgestellt wird.

### <a name="assign-users-and-groups-to-your-saml-application"></a>Zuweisen von Benutzern und Gruppen zu Ihrer SAML-Anwendung

Nachdem Sie Ihre Anwendung zur Verwendung von Azure AD als SAML-basierten Identitätsanbieter konfiguriert haben, ist sie schon fast zum Testen bereit. Als Sicherheitsmaßnahme stellt Azure AD nur dann ein Token aus, das dem Benutzer die Anmeldung bei der Anwendung erlaubt, wenn Azure AD dem Benutzer Zugriff gewährt hat. Benutzer können Zugriff direkt oder über eine Gruppenmitgliedschaft erhalten.

So weisen Sie Ihrer Anwendung einen neuen Benutzer oder eine neue Gruppe zu:

1. Wählen Sie auf der Randleiste der Anwendung die Option **Benutzer und Gruppen** aus. Die Seite **\<Anwendungsname> – Benutzer und Gruppen** wird geöffnet und zeigt die aktuelle Liste der zugewiesenen Benutzer und Gruppen an.
2. Wählen Sie **Benutzer hinzufügen** aus. Die Seite **Zuweisungen hinzufügen** wird angezeigt.
3. Wählen Sie **Benutzer und Gruppen (\<Anzahl> ausgewählt)** aus. Die Seite **Benutzer und Gruppen** wird geöffnet und zeigt eine Liste der verfügbaren Benutzer und Gruppen an.
4. Geben Sie einen Namen ein, oder scrollen Sie, um den Benutzer oder die Gruppe in der Liste zu finden, den bzw. die Sie zuweisen möchten.
5. Wählen Sie jeden Benutzer und jede Gruppe aus, den/die Sie hinzufügen möchten, und klicken Sie dann auf die Schaltfläche **Auswählen**. Die Seite **Benutzer und Gruppen** wird geschlossen.
6. Wählen Sie auf der Seite **Zuweisungen hinzufügen** die Option **Zuweisen** aus. Die Seite **<application name> – Benutzer und Gruppen** wird geöffnet und zeigt die zusätzlichen Benutzer in der Liste an.

   ![Benutzer und Gruppen einer Anwendung](./media/configure-single-sign-on-non-gallery-applications/application-users-and-groups.png)

In dieser Liste können Sie folgende Aktionen ausführen:

- Entfernen eines Benutzers
- Bearbeiten der Rollen von Benutzern
- Aktualisieren von Anmeldeinformationen (Benutzername und Kennwort), sodass der Benutzer sich über seinen Zugriffsbereich bei der Anwendung authentifizieren kann

Sie können mehrere Benutzer oder Gruppen gleichzeitig bearbeiten oder entfernen.

Indem Sie einen Benutzer zuweisen, kann Azure AD diesem Benutzer ein Token ausstellen. Zudem wird dann im Zugriffsbereich des Benutzers eine Kachel für diese Anwendung angezeigt. Wenn der Benutzer Office 365 verwendet, wird die Anwendungskachel auch im Office 365-App-Startfeld angezeigt.

> [!NOTE]
> Sie können ein Kachellogo für die Anwendung hochladen. Verwenden Sie hierzu die Schaltfläche **Logo hochladen** auf der Registerkarte **Konfigurieren** für die Anwendung.

### <a name="test-the-saml-application"></a>Testen der SAML-Anwendung

Vor dem Testen der SAML-Anwendung müssen Sie die Anwendung bereits bei Azure AD eingerichtet und der Anwendung Benutzer oder Gruppen zugewiesen haben. Um die SAML-Anwendung zu testen, wählen Sie **Einmaliges Anmelden** aus. Damit werden Sie zur Seite **SAML-basiertes einmaliges Anmelden** zurückgeleitet. (Wenn eine andere SSO-Methode in Kraft war, wählen Sie **Modi für einmaliges Anmelden ändern** > **SAML** aus.) Wählen Sie in der Rubrik **Einmaliges Anmelden mit \<Anwendungsname> testen** die Option **Testen** aus. Weitere Informationen finden Sie unter [Debuggen des SAML-basierten einmaligen Anmeldens bei Anwendungen in Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md).

## <a name="password-single-sign-on"></a>Einmaliges Anmelden per Kennwort

Wählen Sie diese Option aus, um das [kennwortbasierte einmalige Anmelden](what-is-single-sign-on.md) für eine Webanwendung zu konfigurieren, die über eine HTML-Anmeldeseite verfügt. Das kennwortbasierte einmalige Anmelden (auch als „Password Vaulting“ oder „Kennworttresor“ bezeichnet) ermöglicht es Ihnen, den Benutzerzugriff und die Kennwörter für Webanwendungen zu verwalten, die keinen Identitätsverbund unterstützen. Das Feature ist auch für Szenarien nützlich, in denen mehrere Benutzer ein Konto gemeinsam verwenden müssen, wie z.B. bei den App-Konten für die sozialen Medien Ihrer Organisation.

Nach der Auswahl von **Kennwortbasiert** werden Sie aufgefordert, die URL für die webbasierte Anmeldeseite der Anwendung einzugeben.

![Kennwortbasiertes einmaliges Anmelden](./media/configure-single-sign-on-non-gallery-applications/password-based-sso.png)

Führen Sie die folgenden Schritte aus:

1. Geben Sie die URL ein. Bei dieser Zeichenfolge muss es sich um die Seite handeln, die das Eingabefeld für den Benutzernamen enthält.
2. Wählen Sie **Speichern** aus. Azure AD versucht, die Anmeldeseite im Hinblick auf die Eingabe eines Benutzernamens und eines Kennworts zu analysieren.
3. Wenn Azure AD die Seite nicht analysieren kann, wählen Sie **\<Anwendungsname>-Einstellungen für einmaliges Anmelden über ein Kennwort konfigurieren** aus, um die Seite **Anmeldung konfigurieren** anzuzeigen. (Wenn der Versuch erfolgreich war, können Sie den Rest dieses Verfahrens ignorieren.)
4. Wählen Sie **Anmeldefelder manuell erkennen** aus. Es werden weitere Anweisungen angezeigt, die die manuelle Erkennung von Anmeldefeldern beschreiben.

   ![Manuelle Konfiguration des kennwortbasierten einmaligen Anmeldens](./media/configure-single-sign-on-non-gallery-applications/password-configure-sign-on.png)
5. Wählen Sie **Anmeldefelder erfassen** aus. Auf einer neuen Registerkarte wird eine Seite mit dem Erfassungsstatus geöffnet, auf der die Meldung **Die Metadatenerfassung wird aktuell ausgeführt.** angezeigt wird.
6. Wenn auf einer neuen Registerkarte das Feld **Erweiterung für Zugriffsbereich erforderlich** angezeigt wird, klicken Sie auf **Jetzt installieren**, um die Browsererweiterung **Erweiterung zur sicheren Anmeldung bei "Meine Apps"** zu installieren. (Die Browsererweiterung erfordert Microsoft Edge, Chrome oder Firefox.) Installieren, starten und aktivieren Sie die Erweiterung, und aktualisieren Sie die Seite mit dem Erfassungsstatus.

   Die Browsererweiterung wird in einer anderen Registerkarte geöffnet, die die eingegebene URL anzeigt.
7. Führen Sie auf der Registerkarte mit der eingegebenen URL den Anmeldevorgang durch. Füllen Sie die Felder für Benutzername und Kennwort aus, und versuchen Sie, sich anzumelden. (Sie müssen nicht das richtige Kennwort angeben.)

   Sie werden aufgefordert, die erfassten Anmeldefelder zu speichern.
8. Klicken Sie auf **OK**. Die Registerkarte wird geschlossen, die Browsererweiterung aktualisiert die Seite mit dem Erfassungsstatus mit der Meldung **Die Metadaten für die Anwendung wurden aktualisiert**. Dann wird auch diese Registerkarte geschlossen.
9. Wählen Sie auf der Azure AD-Seite **Anmeldung konfigurieren** die Option **OK, ich konnte mich erfolgreich bei der App anmelden** aus.
10. Klicken Sie auf **OK**.

Nach dem Erfassen der Anmeldeseite können Sie Benutzer und Gruppen zuweisen sowie Richtlinien für Anmeldeinformationen einrichten, genauso wie bei normalen [Anwendungen für das einmalige Anmelden mit Kennwort](what-is-single-sign-on.md).

> [!NOTE]
> Sie können ein Kachellogo für die Anwendung hochladen. Verwenden Sie hierzu die Schaltfläche **Logo hochladen** auf der Registerkarte **Konfigurieren** für die Anwendung.

## <a name="existing-single-sign-on"></a>Vorhandenes einmaliges Anmelden

Wählen Sie diese Option aus, um im Azure AD-Zugriffsbereich oder Office 365-Portal Ihrer Organisation einen Link zu einer Anwendung hinzufügen. Mit dieser Methode können Sie Links zu benutzerdefinierten Webanwendungen hinzufügen, die derzeit für die Authentifizierung Active Directory-Verbunddienste (oder andere Verbunddienste) anstelle von Azure AD verwenden. Oder Sie können Deep-Links auf spezifische SharePoint-Seiten oder andere Webseiten hinzufügen, die in den Zugriffsbereichen der betreffenden Benutzer angezeigt werden sollen.

Nachdem Sie auf **Verknüpft** geklickt haben, werden Sie aufgefordert, die URL der Anwendung einzugeben, zu der ein Link erstellt werden soll. Geben Sie die URL ein, und klicken Sie auf **Speichern**. Sie können der Anwendung Benutzer und Gruppen zuweisen. Dadurch wird die Anwendung im [Office 365-App-Startfeld](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) oder im [Azure AD-Zugriffsbereich](end-user-experiences.md) für die betreffenden Benutzer angezeigt.

> [!NOTE]
> Sie können ein Kachellogo für die Anwendung hochladen. Verwenden Sie hierzu die Schaltfläche **Logo hochladen** auf der Registerkarte **Konfigurieren** für die Anwendung.

## <a name="related-articles"></a>Verwandte Artikel

- [Gewusst wie: Anpassen von Ansprüchen im SAML-Token für Unternehmensanwendungen](../develop/active-directory-saml-claims-customization.md)
- [Debuggen des SAML-basierten einmaligen Anmeldens bei Anwendungen in Azure Active Directory](../develop/howto-v1-debug-saml-sso-issues.md)
- [Microsoft Identity Platform (vormals Azure Active Directory für Entwickler)](../develop/index.yml)
