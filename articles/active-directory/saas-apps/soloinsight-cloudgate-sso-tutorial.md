---
title: 'Tutorial: Azure Active Directory-Integration mit Soloinsight-CloudGate SSO | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Soloinsight-CloudGate SSO konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.openlocfilehash: 193d779a2e9246fe36af4828251a15d593e80ac7
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514774"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Tutorial: Integrieren von Soloinsight-CloudGate SSO in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Soloinsight-CloudGate SSO in Azure Active Directory (Azure AD) integrieren. Die Integration von Soloinsight-CloudGate SSO in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Soloinsight-CloudGate SSO hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Soloinsight-CloudGate SSO anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie kein Abonnement besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige kostenlose Testversion erhalten.
* Soloinsight-CloudGate SSO-Abonnement, für das einmaliges Anmelden (SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. Soloinsight-CloudGate SSO unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Hinzufügen von Soloinsight-CloudGate SSO aus dem Katalog

Zum Konfigurieren der Integration von Soloinsight-CloudGate SSO in Azure AD müssen Sie Soloinsight-CloudGate SSO aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Soloinsight-CloudGate SSO** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Soloinsight-CloudGate SSO** aus, und fügen Sie die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Soloinsight-CloudGate SSO mithilfe eines Testbenutzers mit dem Namen **Britta Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Soloinsight-CloudGate SSO eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Soloinsight-CloudGate SSO müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
2. **[Konfigurieren des einmaligen Anmeldens für Soloinsight-CloudGate SSO](#configure-soloinsight-cloudgate-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit der Testbenutzerin Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Soloinsight-CloudGate SSO-Testbenutzers](#create-soloinsight-cloudgate-sso-test-user)** , um eine Entsprechung von Britta Simon in Soloinsight-CloudGate SSO zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Soloinsight-CloudGate SSO** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie auf der Seite **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    1. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.sigateway.com/login`.

    1. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.sigateway.com/process/sso`.

   > [!NOTE]
   > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit der tatsächlichen Anmelde-URL und dem Bezeichner. Dies ist später in diesem Tutorial im Abschnitt **Konfigurieren des einmaligen Anmeldens für Soloinsight-CloudGate SSO** beschrieben.

1. Suchen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Eintrag **Zertifikat (Base64)** . Klicken Sie auf **Herunterladen** , um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

   ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Soloinsight-CloudGate SSO einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

   ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Konfigurieren von Soloinsight-CloudGate SSO

1. Wenn Sie die Konfiguration in Soloinsight-CloudGate SSO automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Soloinsight-CloudGate SSO einrichten** , um zur Anwendung Soloinsight-CloudGate SSO weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Soloinsight-CloudGate SSO anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 8.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Soloinsight-CloudGate SSO manuell einrichten möchten, öffnen Sie ein neues Webbrowserfenster, melden Sie sich bei der Soloinsight-CloudGate SSO-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

4. Melden Sie sich mit Ihren Anmeldeinformationen beim CloudGate-Webportal an, um die Werte abzurufen, die bei der grundlegenden SAML-Konfiguration im Azure-Portal eingefügt werden sollen. Greifen Sie anschließend auf die SSO-Einstellungen zu, die sich unter dem Pfad **Startseite > Verwaltung > Systemeinstellungen > Allgemein** befinden.

    ![CloudGate SSO-Einstellungen](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **SAML-Consumer-URL**

    * Kopieren Sie die Links in den Feldern **Saml Consumer URL** (SAML-Consumer-URL) und **Redirect URL** (Umleitungs-URL), und fügen Sie sie im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in die Felder **Bezeichner (Entitäts-ID)** und **Antwort-URL** ein.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **SAML-Signaturzertifikat**

    * Navigieren Sie zur Quelle der Zertifikatdatei (Base64), die aus den Listen unter „SAML-Signaturzertifikat“ im Azure-Portal heruntergeladen wurden, und klicken Sie mit der rechten Maustaste darauf. Wählen Sie in der Liste die Option **Edit with Notepad++** (Mit Notepad++ bearbeiten). 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Kopieren Sie den Inhalt der Notepad++-Zertifikatdatei (Base64).

        ![Zertifikatkopie](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Fügen Sie den Inhalt in den SSO-Einstellungen im CloudGate-Webportal in das Feld **Zertifikat** ein, und klicken Sie auf die Schaltfläche „Speichern“.

        ![Zertifikatportal](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Default Group** (Standardgruppe)

    * Wählen Sie im CloudGate-Webportal in der Dropdownliste von **Default Group** (Standardgruppe) die Option **Business Admin** (Unternehmensadministrator).

        ![Standardgruppe](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **AD-Bezeichner und Anmelde-URL**

    * Die kopierte **Anmelde-URL** aus der Konfiguration unter **Soloinsight-CloudGate SSO einrichten** im Azure-Portal muss in den Abschnitt mit den SSO-Einstellungen des CloudGate-Webportals eingegeben werden.

    * Fügen Sie den Link **Anmelde-URL** aus dem Azure-Portal in das Feld **AD Login URL** (AD-Anmelde-URL) im CloudGate-Webportal ein.

    * Fügen Sie den Link **Azure AD-Bezeichner** aus dem Azure-Portal in das Feld **AD Identifier** (AD-Bezeichner) im CloudGate-Webportal ein.

        ![AD-Anmeldung](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin mit dem Namen Britta Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `Britta Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `BrittaSimon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert aus dem Feld **Kennwort** .
   1. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon das einmalige Anmelden von Azure, indem Sie ihr Zugriff auf Soloinsight-CloudGate SSO gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **Soloinsight-CloudGate SSO** .
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste den Eintrag **Britta Simon** aus, und klicken Sie anschließend im unteren Bildschirmbereich auf die Schaltfläche **Auswählen** .
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen** .
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Erstellen eines Soloinsight-CloudGate SSO-Testbenutzers

Wählen Sie zum Erstellen eines Testbenutzers im Hauptmenü Ihres CloudGate-Webportals die Option **Mitarbeiter** , und füllen Sie das Formular „Add New employee“ (Neuen Mitarbeiter hinzufügen) aus. Die Autoritätsebene, die dem Testbenutzer zugewiesen werden soll, lautet **Business Admin** (Unternehmensadministrator). Klicken Sie auf **Erstellen** , nachdem alle erforderlichen Felder ausgefüllt wurden.

![Mitarbeitertest](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

Wenn Sie im Zugriffsbereich die Kachel „Soloinsight-CloudGate SSO“ auswählen, sollten Sie automatisch bei der Soloinsight-CloudGate SSO-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)