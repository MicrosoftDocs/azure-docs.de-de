---
title: 'Tutorial: Azure Active Directory-Integration mit AirWatch | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und AirWatch konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: e4f9d05a4b76e440f986a9003c20a48b22c516a8
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259816"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Tutorial: Integrieren von AirWatch in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie AirWatch in Azure Active Directory (Azure AD) integrieren. Die Integration von AirWatch in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf AirWatch hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei AirWatch anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:
 
* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein AirWatch-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. 

* AirWatch unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="add-airwatch-from-the-gallery"></a>Hinzufügen von AirWatch aus dem Katalog

Zum Konfigurieren der Integration von AirWatch in Azure AD müssen Sie AirWatch aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **AirWatch** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **AirWatch** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-airwatch"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für AirWatch

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit AirWatch mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in AirWatch eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit AirWatch die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für AirWatch](#configure-airwatch-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines AirWatch-Testbenutzers](#create-airwatch-test-user)** , um eine Entsprechung von B. Simon in AirWatch zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **AirWatch** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie auf der Seite **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    1. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`.

    1. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** folgenden Wert ein: `AirWatch`.

    > [!NOTE]
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Wenden Sie sich an das [Supportteam von AirWatch](https://www.air-watch.com/company/contact-us/), um diesen Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die AirWatch-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![image](common/edit-attribute.png)

1. Bearbeiten Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die Ansprüche mithilfe des Symbols zum **Bearbeiten**, oder fügen Sie die Ansprüche über **Neuen Anspruch hinzufügen** hinzu, um das SAML-Tokenattribut wie in der obigen Abbildung gezeigt zu konfigurieren. Führen Sie dann die folgenden Schritte aus:

    | Name |  Quellattribut|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um die Metadaten-XML herunterzuladen und auf Ihrem Computer zu speichern.

   ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **AirWatch einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

   ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf AirWatch gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **AirWatch** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="configure-airwatch-sso"></a>Konfigurieren des einmaligen Anmeldens für AirWatch

1. Melden Sie sich in einem anderen Webbrowserfenster bei der AirWatch-Unternehmenswebsite als Administrator an.

1. Auf der Einstellungsseite: Wählen Sie **Settings > Enterprise Integration > Directory Services** (Einstellungen > Unternehmensintegration > Verzeichnisdienste) aus.

   ![Einstellungen](./media/airwatch-tutorial/services.png "Einstellungen")

1. Klicken Sie auf die Registerkarte **Benutzer**, geben Sie im Textfeld **Basis-DN** Ihren Domänennamen ein, und klicken Sie dann auf **Speichern**.

   ![Screenshot, auf dem das Textfeld „Basis-DN“ hervorgehoben ist](./media/airwatch-tutorial/user.png "Benutzer")

1. Klicken Sie auf die Registerkarte **Server** .

   ![Server](./media/airwatch-tutorial/directory.png "Server")

1. Führen Sie im Abschnitt für **LDAP** die folgenden Schritte aus:

    ![Screenshot der Änderungen, die Sie im Abschnitt „LDAP“ vornehmen müssen](./media/airwatch-tutorial/ldap.png "LDAP")   

    a. Wählen Sie unter **Verzeichnistyp** die Option **Keiner** aus.

    b. Aktivieren Sie **Use SAML For Authentication**.

1. Klicken Sie im Abschnitt **SAML 2.0** auf **Upload** (Hochladen), um das heruntergeladene Zertifikat hochzuladen.

    ![Upload](./media/airwatch-tutorial/uploads.png "Upload")

1. Führen Sie im Abschnitt **Request** die folgenden Schritte aus:

    ![Abschnitt für Anforderungen](./media/airwatch-tutorial/request.png "Anforderung")  

    a. Wählen Sie als **Anforderungsbindungstyp** die Option **POST** aus.

    b. Kopieren Sie im Azure-Portal auf der Dialogfeldseite **Einmaliges Anmelden konfigurieren für AirWatch** den Wert für **Anmelde-URL**, und fügen Sie ihn in das Textfeld **Identity Provider Single Sign On URL** (SSO-URL des Identitätsanbieters) ein.

    c. Wählen Sie als **NameID-Format** die Option **E-Mail-Adresse** aus.

    d. Wählen Sie unter **Authentication Request Security** (Sicherheit der Authentifizierungsanforderung) die Option **None** (Keine) aus.

    e. Klicken Sie auf **Speichern**.

1. Klicken Sie erneut auf die Registerkarte **User** .

    ![Benutzer](./media/airwatch-tutorial/users.png "Benutzer")

1. Führen Sie im Abschnitt **Attribute** die folgenden Schritte aus:

    ![Attribut](./media/airwatch-tutorial/attributes.png "attribute")

    a. Geben Sie im Textfeld **Objektbezeichner** Folgendes ein: `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. Geben Sie im Textfeld **Benutzername** Folgendes ein: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. Geben Sie im Textfeld **Anzeigename** Folgendes ein: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. Geben Sie im Textfeld **Vorname** Folgendes ein: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. Geben Sie im Textfeld **Nachname** Folgendes ein: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. Geben Sie in das Textfeld **E-Mail** die Adresse `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` ein.

    g. Klicken Sie auf **Speichern**.

### <a name="create-airwatch-test-user"></a>Erstellen eines AirWatch-Testbenutzers

Damit sich Azure AD-Benutzer bei AirWatch anmelden können, müssen sie in AirWatch bereitgestellt werden. Im Fall von AirWatch ist die Bereitstellung eine manuelle Aufgabe.

**Um die Benutzerbereitstellung zu konfigurieren, führen Sie die folgenden Schritte durch:**

1. Melden Sie sich bei der **AirWatch**-Unternehmenswebsite als Administrator an.

2. Klicken Sie im Navigationsbereich links auf **Konten**, und klicken Sie dann auf **Benutzer**.
  
   ![Benutzer](./media/airwatch-tutorial/accounts.png "Benutzer")

3. Klicken Sie im Menü **Users** (Benutzer) auf **List View** (Listenansicht), und klicken Sie dann auf **Add > Add User** (Hinzufügen > Benutzer hinzufügen).
  
   ![Screenshot, auf dem die Schaltflächen „Hinzufügen“ und „Benutzer hinzufügen“ hervorgehoben sind](./media/airwatch-tutorial/add.png "Benutzer hinzufügen")

4. Führen Sie im Dialogfeld **Add / Edit User** die folgenden Schritte aus:

   ![Benutzer hinzufügen](./media/airwatch-tutorial/save.png "Benutzer hinzufügen")

   a. Geben Sie in die Textfelder **Benutzername**, **Kennwort**, **Kennwort bestätigen**, **Vorname**, **Nachname** und **E-Mail-Adresse** die Informationen eines gültigen Azure Active Directory-Kontos ein, das Sie bereitstellen möchten.

   b. Klicken Sie auf **Speichern**.

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mit anderen Tools zum Erstellen von AirWatch-Benutzerkonten oder mit den APIs von AirWatch bereitstellen.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für AirWatch weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die AirWatch-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“auf die Kachel „AirWatch“ klicken, werden Sie zur Anmelde-URL für AirWatch weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von AirWatch können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.
