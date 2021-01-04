---
title: 'Tutorial: Azure Active Directory-Integration mit SensoScientific Wireless Temperature Monitoring System | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SensoScientific Wireless Temperature Monitoring System konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: 27512ac694f20544f1fdd5b79b27d7bf2cabdd66
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675500"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Tutorial: Azure Active Directory-Integration mit SensoScientific Wireless Temperature Monitoring System

In diesem Tutorial erfahren Sie, wie Sie SensoScientific Wireless Temperature Monitoring System in Azure Active Directory (Azure AD) integrieren.
Die Integration von SensoScientific Wireless Temperature Monitoring System in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf SensoScientific Wireless Temperature Monitoring System hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei SensoScientific Wireless Temperature Monitoring System anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit SensoScientific Wireless Temperature Monitoring System konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein SensoScientific Wireless Temperature Monitoring System-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SensoScientific Wireless Temperature Monitoring System unterstützt **IDP** -initiiertes einmaliges Anmelden.

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Hinzufügen von SensoScientific Wireless Temperature Monitoring System aus dem Katalog

Zum Konfigurieren der Integration von SensoScientific Wireless Temperature Monitoring System in Azure AD müssen Sie SensoScientific Wireless Temperature Monitoring System aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um SensoScientific Wireless Temperature Monitoring System aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory** .

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **SensoScientific Wireless Temperature Monitoring System** ein, wählen Sie im Ergebnisbereich **SensoScientific Wireless Temperature Monitoring System** aus, und klicken Sie auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

    ![SensoScientific Wireless Temperature Monitoring System in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei SensoScientific Wireless Temperature Monitoring System mithilfe einer Testbenutzerin namens **Britta Simon** .
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SensoScientific Wireless Temperature Monitoring System eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei SensoScientific Wireless Temperature Monitoring System müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für SensoScientific Wireless Temperature Monitoring System](#configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines SensoScientific Wireless Temperature Monitoring System-Testbenutzers](#create-sensoscientific-wireless-temperature-monitoring-system-test-user)** , um eine Entsprechung von Britta Simon in SensoScientific Wireless Temperature Monitoring System zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei SensoScientific Wireless Temperature Monitoring System die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **SensoScientific Wireless Temperature Monitoring System** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Im Abschnitt **Grundlegende SAML-Konfiguration** muss der Benutzer keine Schritte ausführen, weil die App bereits in Azure integriert ist.

    ![SSO-Informationen zur Domäne und zu den URLs für SensoScientific Wireless Temperature Monitoring System](common/preintegrated.png)

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **SensoScientific Wireless Temperature Monitoring System einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für SensoScientific Wireless Temperature Monitoring System

1. Melden Sie sich bei der SensoScientific Wireless Temperature Monitoring System-Anwendung als Administrator an.

1. Klicken Sie im Navigationsmenü oben auf **Configuration** (Konfiguration), und wechseln Sie unter **Single Sign On** (Einmaliges Anmelden) zu **Configure** (Konfigurieren), um die Seite „Single Sign On Settings“ (Einstellungen für einmaliges Anmelden) zu öffnen. Führen Sie folgende Schritte aus:

    ![Einmaliges Anmelden konfigurieren](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png)

    a. Wählen Sie als **Issuer Name** die Option „Azure AD“ aus.

    b. Fügen Sie im Textfeld **Issuer URL** (Aussteller-URL) den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie im Textfeld **Single Sign-On Service URL** (URL des Diensts für einmaliges Anmelden) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    d. Fügen Sie im Textfeld **Single Sign-Out Service URL** (URL des Diensts für einmaliges Abmelden) die **Abmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    e. Navigieren Sie zu dem Zertifikat, das Sie aus dem Azure-Portal heruntergeladen haben, und laden Sie es hier hoch.

    f. Klicken Sie auf **Speichern** .

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen `brittasimon@yourcompanydomain.extension` ein. Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SensoScientific Wireless Temperature Monitoring System gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen** , **Alle Anwendungen** und **SensoScientific Wireless Temperature Monitoring System** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **SensoScientific Wireless Temperature Monitoring System** aus.

    ![SensoScientific Wireless Temperature Monitoring System-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen** .

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen** .

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Erstellen eines SensoScientific Wireless Temperature Monitoring System-Testbenutzers

Damit sich Azure AD-Benutzer bei SensoScientific Wireless Temperature Monitoring System anmelden können, müssen sie in SensoScientific Wireless Temperature Monitoring System bereitgestellt werden. Arbeiten Sie mit dem [Supportteam von SensoScientific Wireless Temperature Monitoring System](https://www.sensoscientific.com/contact-us/) zusammen, um die Benutzer zur SensoScientific Wireless Temperature Monitoring System-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „SensoScientific Wireless Temperature Monitoring System“ klicken, sollten Sie automatisch bei der SensoScientific Wireless Temperature Monitoring System-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)