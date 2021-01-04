---
title: 'Tutorial: Azure Active Directory-Integration in MobileIron | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und MobileIron konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: 7965edd5dc54155f03b9211d5c2b21d451018bd1
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514451"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>Tutorial: Azure Active Directory-Integration in MobileIron

In diesem Tutorial erfahren Sie, wie Sie MobileIron in Azure Active Directory (Azure AD) integrieren.
Die Integration von MobileIron in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf MobileIron hat.
* Sie können es Ihren Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei MobileIron angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit MobileIron konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* MobileIron-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* MobileIron unterstützt **SP- und IDP** -initiiertes einmaliges Anmelden.

## <a name="adding-mobileiron-from-the-gallery"></a>Hinzufügen von MobileIron aus dem Katalog

Zum Konfigurieren der Integration von MobileIron in Azure AD müssen Sie MobileIron über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um MobileIron aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory** .

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **MobileIron** ein, wählen Sie im Ergebnisbereich **MobileIron** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

     ![MobileIron in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei MobileIron anhand einer Testbenutzerin namens **Britta Simon** .
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in MobileIron eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei MobileIron müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für MobileIron](#configure-mobileiron-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines MobileIron-Testbenutzers](#create-mobileiron-test-user)** , um eine Entsprechung von Britta Simon in MobileIron zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in MobileIron die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **MobileIron** die Option **Einmaliges Anmelden** .

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten Modus** konfigurieren möchten:

    ![Screenshot der grundlegenden S A M L-Konfiguration, in der Sie I D und Antwort-U R L eingeben und „Speichern“ auswählen](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://www.mobileiron.com/<key>`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<host>.mobileiron.com/saml/SSO/alias/<key>`

    c. Klicken Sie auf **Zusätzliche URLs festlegen** , und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![Screenshot: Option „Zusätzliche URLs festlegen“ zum Eingeben einer Anmelde-URL](common/metadata-upload-additional-signon.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<host>.mobileiron.com/user/login.html`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner, die Antwort-URL und die Anmelde-URL. Sie erhalten die Werte des Schlüssels und des Hosts aus dem Verwaltungsportal von MobileIron, das später in diesem Tutorial beschrieben wird.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML** -Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

### <a name="configure-mobileiron-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für MobileIron

1. Melden Sie sich in einem anderen Webbrowserfenster bei der MobileIron-Unternehmenswebsite als Administrator an.

2. Navigieren Sie zu **Admin** > **Identity** (Identität), und wählen Sie im Feld **Info on Cloud IDP Setup** (Info zum Cloud-IDP-Setup) die Option **AAD** .

    ![Screenshot: Registerkarte „Admin“ auf der MobileIron-Website mit Auswahl von „Identity“ (Identität)](./media/mobileiron-tutorial/tutorial_mobileiron_admin.png)

3. Kopieren Sie die Werte von **Key** (Schlüssel) und **Host** , und fügen Sie sie zum Ausfüllen der URLs im Azure-Portal in den Abschnitt **Grundlegende SAML-Konfiguration** ein.

    ![Screenshot: Option zum Einrichten von SAML mit einem Schlüssel- und einem Hostwert](./media/mobileiron-tutorial/key.png)

4. Klicken Sie in **Metadatendatei aus AAD exportieren und in MobileIron-Cloudfeld importieren** auf **Datei auswählen** , um die heruntergeladenen Metadaten vom Azure-Portal hochzuladen. Klicken Sie nach dem Hochladen auf **Fertig** .

    ![Einmaliges Anmelden konfigurieren – Administratormetadatenschaltfläche](./media/mobileiron-tutorial/tutorial_mobileiron_adminmetadata.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** Folgendes ein: **brittasimon\@ihreunternehmensdomäne.erweiterung** .  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf MobileIron gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen** , **Alle Anwendungen** und **MobileIron** .

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in der Anwendungsliste **MobileIron** ein, und wählen Sie den Eintrag aus.

    ![MobileIron-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen** .

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen** .

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-mobileiron-test-user"></a>Erstellen eines MobileIron-Testbenutzers

Damit sich Azure AD-Benutzer bei MobileIron anmelden können, müssen sie in MobileIron bereitgestellt werden.  
Im Fall von MobileIron ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei der MobileIron-Unternehmenswebsite als Administrator an.

1. Wechseln Sie zu **Users** , und klicken Sie auf **Add** > **Single User** .

    ![Einmaliges Anmelden konfigurieren – Benutzerschaltfläche](./media/mobileiron-tutorial/tutorial_mobileiron_user.png)

1. Führen Sie auf der Dialogfeldseite **Single User** die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren – Benutzerhinzufüge-Schaltfläche](./media/mobileiron-tutorial/tutorial_mobileiron_useradd.png)

    a. Geben Sie im Textfeld **E-mail adress** die E-Mail-Adresse des Benutzers ein, z.B. brittasimon@contoso.com.

    b. Geben Sie im Textfeld **First Name** den Vornamen des Benutzers ein, z.B. „Britta“.

    c. Geben Sie im Textfeld **Last Name** den Nachnamen des Benutzers ein, z.B. „Simon“.

    d. Klicken Sie auf **Fertig** .

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „MobileIron“ klicken, sollten Sie automatisch bei Ihrer MobileIron-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)