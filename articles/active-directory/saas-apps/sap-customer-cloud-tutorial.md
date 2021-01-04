---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SAP Cloud for Customer | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAP Cloud for Customer konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: jeedes
ms.openlocfilehash: 9b244d5e6aa9dd732aa670fd645b6bbc266c9535
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897278"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SAP Cloud for Customer

In diesem Tutorial erfahren Sie, wie Sie SAP Cloud for Customer in Azure Active Directory (Azure AD) integrieren. Die Integration von SAP Cloud for Customer in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf SAP Cloud for Customer hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei SAP Cloud for Customer anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein SAP Cloud for Customer-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SAP Cloud for Customer unterstützt **SP** -initiiertes einmaliges Anmelden.

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Hinzufügen von SAP Cloud for Customer aus dem Katalog

Zum Konfigurieren der Integration von SAP Cloud for Customer in Azure AD müssen Sie SAP Cloud for Customer aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **SAP Cloud for Customer** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **SAP Cloud for Customer** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-for-customer"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für SAP Cloud for Customer

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit SAP Cloud for Customer mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAP Cloud for Customer eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit SAP Cloud for Customer die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für SAP Cloud for Customer](#configure-sap-cloud-for-customer-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines SAP Cloud for Customer-Testbenutzers](#create-sap-cloud-for-customer-test-user)** , um in SAP Cloud for Customer ein Pendant von B. Simon zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SAP Cloud for Customer** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<server name>.crm.ondemand.com`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<server name>.crm.ondemand.com`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Supportteam für den SAP Cloud for Customer-Client](https://www.sap.com/about/agreements.sap-cloud-services-customers.html), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Ihre SAP Cloud for Customer-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol **Bearbeiten** , um das Dialogfeld „Benutzerattribute“ zu öffnen.

    ![Screenshot: Dialogfeld „Benutzerattribute“, in dem das Symbol „Bearbeiten“ ausgewählt ist](common/edit-attribute.png)

1. Führen Sie im Dialogfeld **Benutzerattribute und Ansprüche** im Abschnitt **Benutzerattribute** die folgenden Schritte aus:

    a. Klicken Sie auf das Symbol **Bearbeiten** , um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![Screenshot: Dialogfeld „Benutzerattribute und Ansprüche“, in dem das Symbol „Bearbeiten“ ausgewählt ist](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Wählen Sie als **Quelle** die Option **Transformation** aus.

    c. Wählen Sie in der Liste **Transformation** die Option **ExtractMailPrefix()** aus.

    d. Wählen Sie in der Liste **Parameter 1** das Benutzerattribut aus, das Sie für Ihre Implementierung verwenden möchten.
    Wenn Sie z.B. den Wert „EmployeeID“ als eindeutige Benutzer-ID verwenden möchten und den Attributwert in „ExtensionAttribute2“ gespeichert haben, wählen Sie „user.extensionattribute2“ aus.

    e. Klicken Sie auf **Speichern**.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML** , und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **SAP Cloud for Customer einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon das einmalige Anmelden von Azure, indem Sie ihr Zugriff auf SAP Cloud for Customer gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **SAP Cloud for Customer** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-sap-cloud-for-customer-sso"></a>Konfigurieren des einmaligen Anmeldens für SAP Cloud for Customer

1. Melden Sie sich in einem neuen Webbrowserfenster bei der SAP Cloud for Customer-Unternehmenswebsite als Administrator an.

2. Klicken Sie im linken Bereich des Menüs auf **Identity Providers** > **Corporate Identity Providers** > **Add** (Identitätsanbieter > Unternehmensidentitätsanbieter > Hinzufügen), und fügen Sie im Popupelement den Namen des Identitätsanbieters hinzu, etwa **Azure AD**. Klicken Sie anschließend auf **Save** (Speichern) und auf **SAML 2.0 Configuration** (SAML 2.0-Konfiguration).

    ![Screenshot: Seite „Identity Providers“ (Identitätsanbieter), auf der das Textfeld „Add Identity Provider“ (Identitätsanbieter hinzufügen) hervorgehoben und die Schaltfläche „Save“ (Speichern) ausgewählt ist](./media/sap-customer-cloud-tutorial/configure01.png)

3. Führen Sie im Abschnitt **SAML 2.0 Configuration** (SAML 2.0-Konfiguration) die folgenden Schritte aus:

    ![Screenshot: Fenster „SAML 2.0 Configuration“ (SAML 2.0-Konfiguration) mit ausgewählter Schaltfläche „Browse“ (Durchsuchen)](./media/sap-customer-cloud-tutorial/configure02.png)

    a. Klicken Sie zum Hochladen der Verbundmetadaten-XML-Datei, die Sie aus dem Azure-Portal heruntergeladen haben, auf **Browse** (Durchsuchen).

    b. Wenn die XML-Datei erfolgreich hochgeladen wurde, werden die folgenden Werte automatisch aufgefüllt. Klicken Sie dann auf **Save** (Speichern).

### <a name="create-sap-cloud-for-customer-test-user"></a>Erstellen eines SAP Cloud for Customer-Testbenutzers

Damit sich Azure AD-Benutzer bei SAP Cloud for Customer anmelden können, müssen sie in SAP Cloud for Customer bereitgestellt werden. In SAP Cloud for Customer wird die Bereitstellung manuell ausgeführt.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei SAP Cloud for Customer als Sicherheitsadministrator an.

2. Klicken Sie im linken Bereich des Menüs auf **Users & Authorizations** > **User Management** > **Add User** (Benutzer und Autorisierungen > Benutzerverwaltung > Benutzer hinzufügen).

    ![Screenshot: Seite „User Management“ (Benutzerverwaltung) mit ausgewählter Schaltfläche „Add user“ (Benutzer hinzufügen)](./media/sap-customer-cloud-tutorial/configure03.png)

3. Führen Sie im Abschnitt **Add New User** (Neuen Benutzer hinzufügen) die folgenden Schritte aus:

    ![SAP-Konfiguration](./media/sap-customer-cloud-tutorial/configure04.png)

    a. Geben Sie im Textfeld **First Name** (Vorname) den Namen des Benutzers ein, z. B. **B**.

    b. Geben Sie im Textfeld **Last Name** (Nachname) den Nachnamen des Benutzers ein, z. B. **Simon**.

    c. Geben Sie im Textfeld **E-Mail** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z. B. `B.Simon@contoso.com`.

    d. Geben Sie im Textfeld **Login Name** (Anmeldename) den Namen des Benutzers ein, z. B. **B.Simon**.

    e. Wählen Sie für **User Type** (Benutzertyp) einen Wert gemäß Ihren Anforderungen aus.

    f. Wählen Sie unter **Account Activation** (Kontoaktivierung) eine Option basierend auf Ihren Anforderungen aus.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „SAP Cloud for Customer“ klicken, sollten Sie automatisch bei der SAP Cloud for Customer-Anwendung angemeldet werden, für die Sie SSO eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [SAP Cloud for Customer mit Azure AD ausprobieren](https://aad.portal.azure.com/)