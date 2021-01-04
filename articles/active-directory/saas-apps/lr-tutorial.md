---
title: 'Tutorial: Azure Active Directory-Integration mit LoginRadius | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie das einmalige Anmelden zwischen Azure Active Directory und LoginRadius konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: 832c08123904b9fb889231faa86c1308704a2581
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97606418"
---
# <a name="tutorial-azure-active-directory-integration-with-loginradius"></a>Tutorial: Azure Active Directory-Integration mit LoginRadius

In diesem Tutorial erfahren Sie, wie Sie LoginRadius in Azure Active Directory (Azure AD) integrieren.

Die Integration von LoginRadius in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf LoginRadius hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei LoginRadius anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit LoginRadius konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein LoginRadius-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* LoginRadius unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-loginradius-from-the-gallery"></a>Hinzufügen von LoginRadius aus dem Katalog

Zum Konfigurieren der Integration von LoginRadius in Azure AD müssen Sie LoginRadius aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Hinzufügen von LoginRadius aus dem Katalog:**

1. Wählen Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich das Symbol **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Wählen Sie zum Hinzufügen einer neuen Anwendung die Schaltfläche **Neue Anwendung** aus.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **LoginRadius** ein, wählen Sie im Ergebnisbereich **LoginRadius** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![LoginRadius in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei LoginRadius mithilfe eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in LoginRadius eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei LoginRadius müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für LoginRadius](#configure-loginradius-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines LoginRadius-Testbenutzers](#create-loginradius-test-user)** , um eine Entsprechung von Britta Simon in LoginRadius zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei LoginRadius die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **LoginRadius** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Bereich **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Symbol **Bearbeiten** aus, um den Bereich **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Im Abschnitt **Grundlegende SAML-Konfiguration**:

   ![SSO-Informationen zur Domäne und zu den URLs für LoginRadius](common/sp-identifier.png)

   1. Geben Sie in das Textfeld **Anmelde-URL** die URL `https://secure.loginradius.com/login` ein.

   1. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** die URL `https://lr.hub.loginradius.com/` ein.

   1. Geben Sie in das Textfeld **Antwort-URL (Assertionsverbraucherdienst-URL)** die ACS-URL `https://lr.hub.loginradius.com/saml/serviceprovider/AdfsACS.aspx` von LoginRadius ein. 

5. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Option **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **LoginRadius einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

   ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

   - Anmelde-URL

   - Azure AD-Bezeichner

   - Abmelde-URL

## <a name="configure-loginradius-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für LoginRadius

In diesem Abschnitt aktivieren Sie das einmalige Anmelden für die LoginRadius-Verwaltungskonsole.

1. Melden Sie sich bei Ihrem Konto der LoginRadius-[Verwaltungskonsole](https://adminconsole.loginradius.com/login) an.

2. Wechseln Sie in der [LoginRadius-Verwaltungskonsole](https://secure.loginradius.com/account/team) zum Abschnitt **Teamverwaltung**.

3. Wählen Sie die Registerkarte **Einmaliges Anmelden** und dann **Azure AD** aus:

   ![Screenshot, der das Menü für einmaliges Anmelden in der LoginRadius-Teamverwaltungskonsole zeigt](./media/loginradius-tutorial/azure-ad.png)
4. Führen Sie auf der Azure AD-Setupseite die folgenden Schritte aus:

   ![Screenshot, der die Azure Active Directory-Konfiguration in der LoginRadius-Teamverwaltungskonsole zeigt](./media/loginradius-tutorial/single-sign-on.png)

    1. Geben Sie als **Speicherort des ID-Anbieters** den ANMELDUNGSENDPUNKT ein, den Sie von Ihrem Azure AD-Konto erhalten.

    1. Geben Sie als **Abmelde-URL des ID-Anbieters** den ABMELDUNGSENDPUNKT ein, den Sie von Ihrem Azure AD-Konto erhalten.
 
    1. Geben Sie als **Zertifikat des ID-Anbieters** das Azure AD-Zertifikat ein, das Sie von Ihrem Azure AD-Konto erhalten. Geben Sie den Zertifikatwert mit Kopf- und Fußzeile ein. Beispiel: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`

    1. Geben Sie als **Zertifikat des Dienstanbieters** und **Zertifikatschlüssel des Dienstanbieters** Ihr Zertifikat und Ihren Schlüssel ein. 

       Sie können ein selbstsigniertes Zertifikat erstellen, indem Sie die folgenden Befehle über die Befehlszeile (Linux/Mac) ausführen:

       - Befehl zum Abrufen des Zertifikatschlüssels für SP: `openssl genrsa -out lr.hub.loginradius.com.key 2048`

       - Befehl zum Abrufen des Zertifikats für SP: `openssl req -new -x509 -key lr.hub.loginradius.com.key -out lr.hub.loginradius.com.cert -days 3650 -subj /CN=lr.hub.loginradius.com`

       > [!NOTE]
       > Achten Sie darauf, die Zertifikat- und Zertifikatschlüsselwerte mit der Kopf- und Fußzeile einzugeben:
       > - Beispielformat für Zertifikatwert: `-----BEGIN CERTIFICATE-----<certifciate value>-----END CERTIFICATE-----`
       > - Beispielformat für Zertifikatschlüsselwert: `-----BEGIN RSA PRIVATE KEY-----<certifciate key value>-----END RSA PRIVATE KEY-----`

5. Wählen Sie im Abschnitt **Datenzuordnung** die Felder (SP-Felder) aus, und geben Sie die entsprechenden Azure AD-Felder (IdP-Felder) ein.

    Im folgenden sind einige Feldnamen für Azure AD aufgeführt.

    | Felder    | Profilschlüssel                                                          |
    | --------- | -------------------------------------------------------------------- |
    | Email     | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
    | FirstName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`    |
    | LastName  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`      |

    > [!NOTE]
    > Die Feldzuordnung **Email** ist erforderlich. Die Feldzuordnungen **FirstName-** und **LastName** sind optional.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

In diesem Abschnitt wird über das Azure-Portal eine Testbenutzerin namens Britta Simon erstellt.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den **Benutzereigenschaften** die folgenden Schritte aus:

   ![Dialogfeld „Benutzer“](common/user-properties.png)

   1. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge `brittasimon@yourcompanydomain.extension` ein. Beispiel: BrittaSimon@contoso.com.

   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.

   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf LoginRadius gewähren.

1. Wählen Sie im Azure-Portal die Option **Unternehmensanwendungen** aus, und wählen Sie dann **Alle Anwendungen** und **LoginRadius** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **LoginRadius** aus.

    ![LoginRadius-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Wählen Sie die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Bereich **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Bereich **Benutzer und Gruppen** in der Liste **Benutzer** den Eintrag **Britta Simon** aus, und wählen Sie dann unten auf dem Bildschirm die Schaltfläche **Auswählen**.

6. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Bereich **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Wählen Sie dann unten auf dem Bildschirm die Schaltfläche **Auswählen** aus.

7. Wählen Sie im Bereich **Zuweisung hinzufügen** die Schaltfläche **Zuweisen** aus.

### <a name="create-loginradius-test-user"></a>Erstellen eines LoginRadius-Testbenutzers

1. Melden Sie sich bei Ihrem Konto der LoginRadius-[Verwaltungskonsole](https://adminconsole.loginradius.com/login) an.

2. Wechseln Sie in der LoginRadius-Verwaltungskonsole zum Abschnitt „Teamverwaltung“.

   ![Screenshot, der die LoginRadius-Verwaltungskonsole zeigt](./media/loginradius-tutorial/team-management.png)
3. Wählen Sie im seitlichen Menü die Option **Teammitglied hinzufügen** aus, um das Formular zu öffnen. 

4. Auf dem Formular **Teammitglied hinzufügen** erstellen Sie in Ihrer LoginRadius-Site eine Benutzerin namens Britta Simon, indem Sie die Details der Benutzerin angeben und die Berechtigungen zuweisen, die sie haben soll. Weitere Informationen zu den Berechtigungen, die auf Rollen basieren, finden Sie im Abschnitt zu [Rollenzugriffsberechtigungen](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) im Dokument zur [Verwaltung von Teammitgliedern](https://www.loginradius.com/docs/api/v2/admin-console/team-management/manage-team-members#roleaccesspermissions0) von LoginRadius. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

1. Rufen Sie in einem Browser https://accounts.loginradius.com/auth.aspx auf, und wählen Sie **Fed SSO log in** (SSO-Verbundanmeldung) aus.
2. Geben Sie den Namen Ihrer LoginRadius-App ein, und wählen Sie dann **Anmelden** aus.
3. Daraufhin sollte ein Popupelement geöffnet werden, in dem Sie aufgefordert werden, sich bei Ihrem Azure AD-Konto anzumelden.
4. Nach der Authentifizierung wird das Popupelement geschlossen, und Sie werden bei der LoginRadius-Verwaltungskonsole angemeldet.

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)
