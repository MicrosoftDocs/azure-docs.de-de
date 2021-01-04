---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SuccessFactors | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SuccessFactors konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: 6570845c6a37a09315b038287ee35a3b48f1cdd0
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521967"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>Tutorial: Integration des einmaligen Anmeldens von Azure Active Directory mit SuccessFactors

In diesem Tutorial erfahren Sie, wie Sie SuccessFactors in Azure Active Directory (Azure AD) integrieren. Die Integration von SuccessFactors in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf SuccessFactors hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei SuccessFactors anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* SuccessFactors-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SuccessFactors unterstützt **SP-initiiertes** einmaliges Anmelden.
* Nach dem Konfigurieren von SuccessFactors können Sie Sitzungssteuerungen erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützen. Sitzungssteuerungen basieren auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-aad) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.

## <a name="adding-successfactors-from-the-gallery"></a>Hinzufügen von SuccessFactors über den Katalog

Zum Konfigurieren der Integration von SuccessFactors in Azure AD müssen Sie SuccessFactors über den Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **SuccessFactors** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **SuccessFactors** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für SuccessFactors

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit SuccessFactors mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SuccessFactors eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit SuccessFactors müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für SuccessFactors](#configure-successfactors-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines SuccessFactors-Testbenutzers](#create-successfactors-test-user)** , um in SuccessFactors eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
3. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SuccessFactors** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein:

    - `https://<companyname>.successfactors.com/<companyname>`
    - `https://<companyname>.sapsf.com/<companyname>`
    - `https://<companyname>.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.eu`

    b. Geben Sie im Textfeld **Identifier** (Bezeichner) eine URL nach folgendem Muster ein:

    - `https://www.successfactors.com/<companyname>`
    - `https://www.successfactors.com`
    - `https://<companyname>.successfactors.eu`
    - `https://www.successfactors.eu/<companyname>`
    - `https://<companyname>.sapsf.com`
    - `https://hcm4preview.sapsf.com/<companyname>`
    - `https://<companyname>.sapsf.eu`
    - `https://www.successfactors.cn`
    - `https://www.successfactors.cn/<companyname>`

    c. Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein:

    - `https://<companyname>.successfactors.com/<companyname>`
    - `https://<companyname>.successfactors.com`
    - `https://<companyname>.sapsf.com/<companyname>`
    - `https://<companyname>.sapsf.com`
    - `https://<companyname>.successfactors.eu/<companyname>`
    - `https://<companyname>.successfactors.eu`
    - `https://<companyname>.sapsf.eu`
    - `https://<companyname>.sapsf.eu/<companyname>`
    - `https://<companyname>.sapsf.cn`
    - `https://<companyname>.sapsf.cn/<companyname>`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL, den tatsächlichen Bezeichner und die tatsächliche Antwort-URL. Die entsprechenden Werte erfahren Sie vom [SuccessFactors-Supportteam](https://www.sap.com/support.html).

4. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **SuccessFactors einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
    1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
    1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert aus dem Feld **Kennwort**.
    1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SuccessFactors gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **SuccessFactors** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-successfactors-sso"></a>Konfigurieren des einmaligen Anmeldens für SuccessFactors

1. Melden Sie sich in einem anderen Webbrowserfenster als Administrator beim **SuccessFactors-Verwaltungsportal** an.

2. Rufen Sie **Anwendungssicherheit** auf, und navigieren **Sie zum Feature für einmaliges Anmelden**.

3. Geben Sie unter **Reset Token** (Token zurücksetzen) einen beliebigen Wert an, und klicken Sie auf **Save Token** (Token speichern), um SAML-SSO zu aktivieren.

    ![Screenshot: Registerkarte „Application Security“ (Anwendungssicherheit), auf der „Single Sign On Features“ (Funktionen für einmaliges Anmelden) hervorgehoben ist und Sie ein Token eingeben können][11]

    > [!NOTE]
    > Dieser Wert wird als Ein-/Ausschalter verwendet. Sobald ein beliebiger Wert gespeichert wird, wird SAML-SSO aktiviert. Wird kein Wert gespeichert, ist SAML-SSO deaktiviert.

4. Sehen Sie sich den folgenden Screenshot an, und führen Sie die folgenden Aktionen aus:

    ![Screenshot: Bereich für SAML-basiertes einmaliges Anmelden, in dem Sie die beschriebenen Werte eingeben können][12]
  
    a. Wählen Sie das Optionsfeld **SAML v2 SSO** aus.
  
    b. Legen Sie den **Namen der SAML-Assertionspartei** fest (beispielsweise auf SAML-Aussteller und Unternehmensname).

    c. Fügen Sie im Textfeld **Issuer URL** (Aussteller-URL) den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Wählen Sie unter **Require Mandatory Signature** (Pflichtsignatur erforderlich) **Assertion** aus.

    e. Wählen Sie für **Enable SAML Flag** (SAML-Flag aktivieren) die Option **Aktiviert** aus.

    f. Wählen Sie für **Login Request Signature(SF Generated/SP/RP)** (Anmeldeanforderungssignatur (SF-generiert/SP/RP)) die Option **Nein** aus.

    g. Wählen Sie für **SAML Profile** (SAML-Profil) die Option **Browser/Post Profile** (Browser/Post-Profil) aus.

    h. Wählen Sie für **Enforce Certificate Valid Period** (Gültigkeitszeitraum des Zertifikats erzwingen) die Option **Nein** aus.

    i. Kopieren Sie den Inhalt der heruntergeladenen Zertifikatdatei aus dem Azure-Portal, und fügen Sie ihn in das Textfeld **SAML Verifying Certificate** (SAML-Verifizierungszertifikat) ein.

    > [!NOTE] 
    > Der Inhalt des Zertifikats muss über Tags für Beginn und Ende des Zertifikats verfügen.

5. Navigieren Sie zu „SAML V2“, und führen Sie die folgenden Schritte aus:

    ![Screenshot: Bereich für SP-initiierte Abmeldung für SAML v2, in dem Sie die beschriebenen Werte eingeben können][13]

    a. Wählen Sie für **Support SP-initiated Global Logout** (SP-initiiertes globales Abmelden unterstützen) die Option **Ja** aus.

    b. Fügen Sie in das Textfeld **Global Logout Service URL (LogoutRequest destination)** (URL des globalen Abmeldediensts (LogoutRequest-Ziel)) den Wert für die **Sign-Out URL** (Abmelde-URL) ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Wählen Sie für **Require sp must encrypt all NameID elements** (SP muss alle NameID-Elemente verschlüsseln) die Option **Nein** aus.

    d. Wählen Sie für **NameID Format** (NameID-Format) die Option **unspecified** (keine Angabe) aus.

    e. Wählen Sie für **Enable sp initiated login (AuthnRequest)** (SP-initiierte Anmeldung aktivieren (AuthnRequest)) die Option **Ja** aus.

    f. Fügen Sie den Wert der **Anmelde-URL** , den Sie aus dem Azure-Portal kopiert haben, in das Textfeld **Send request as Company-Wide issuer** (Anforderung als unternehmensweiter Aussteller senden) ein.

6. Führen Sie die folgenden Schritte aus, wenn bei den Anmeldebenutzernamen die Groß-/Kleinschreibung nicht beachtet werden soll.

    ![Einmaliges Anmelden konfigurieren][29]

    a. Rufen Sie **Unternehmenseinstellungen** (im unteren Bereich der Seite) auf.

    b. Aktivieren Sie das Kontrollkästchen **Enable Non-Case-Sensitive Username** (Groß-/Kleinschreibung bei Benutzernamen nicht beachten).

    c. Klicken Sie auf **Speichern**.

    > [!NOTE]
    > Wenn Sie diese Funktion aktivieren, überprüft das System, ob ein doppelter SAML-Anmeldename erstellt wird. Dies wäre etwa der Fall, wenn der Kunde die Benutzernamen „Benutzer1“ und „benutzer1“ verwendet. Ohne Beachtung der Groß-/Kleinschreibung werden diese zu Duplikaten. In diesem Fall gibt das System eine Fehlermeldung aus, und das Feature wird nicht aktiviert. Der Kunde muss für einen der Benutzernamen eine abweichende Schreibweise verwenden.

### <a name="create-successfactors-test-user"></a>Erstellen eines SuccessFactors-Testbenutzers

Damit sich Azure AD-Benutzer bei SuccessFactors anmelden können, müssen sie in SuccessFactors bereitgestellt werden. Im Fall von SuccessFactors ist die Bereitstellung eine manuelle Aufgabe.

Wenn Sie in SuccessFactors erstellte Benutzer abrufen möchten, wenden Sie sich an das [SuccessFactors-Supportteam](https://www.sap.com/support.html).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „SuccessFactors“ klicken, sollten Sie automatisch bei Ihrer SuccessFactors-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [SuccessFactors mit Azure AD ausprobieren](https://aad.portal.azure.com)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Schützen von Apps mit der App-Steuerung für bedingten Zugriff von Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png