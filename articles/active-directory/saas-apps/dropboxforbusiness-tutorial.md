---
title: 'Tutorial: Azure Active Directory-Integration mit Dropbox für Unternehmen | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Dropbox für Unternehmen konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 63502412-758b-4b46-a580-0e8e130791a1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2109c3f67a666d0c379cc188c07968c8b31d6a1
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65989666"
---
# <a name="tutorial-integrate-dropbox-for-business-with-azure-active-directory"></a>Tutorial: Integrieren von Dropbox für Unternehmen in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Dropbox für Unternehmen in Azure Active Directory (Azure AD) integrieren. Bei der Integration von Dropbox für Unternehmen in Azure AD haben Sie folgende Möglichkeiten:

* Steuern Sie in Azure AD, wer Zugriff auf Dropbox für Unternehmen hat.
* Ermöglichen Sie Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Dropbox für Unternehmen anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie kein Abonnement besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige kostenlose Testversion erhalten.
* Abonnement von Dropbox für Unternehmen, für das einmaliges Anmelden (SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

* In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. Dropbox für Unternehmen unterstützt **SP**-initiiertes SSO.

* Dropbox für Unternehmen unterstützt die **Just-in-Time**-Benutzerbereitstellung.

## <a name="adding-dropbox-for-business-from-the-gallery"></a>Hinzufügen von Dropbox für Unternehmen aus dem Katalog

Zum Konfigurieren der Integration von Dropbox für Unternehmen in Azure AD müssen Sie Dropbox für Unternehmen aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Dropbox für Unternehmen** in das Suchfeld ein.
1. Wählen Sie **Dropbox für Unternehmen** im Ergebnisbereich aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Dropbox für Unternehmen mithilfe eines Testbenutzers mit dem Namen **Britta Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Dropbox für Unternehmen eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Dropbox für Unternehmen müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Dropbox für Unternehmen](#configure-dropbox-for-business-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Dropbox für Unternehmen-Testbenutzers](#create-dropbox-for-business-test-user)** , um in Dropbox für Unternehmen eine Entsprechung von Britta Simon zu erhalten, die mit ihrer Benutzerdarstellung in Azure AD verknüpft ist.
6. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Dropbox für Unternehmen** den Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie auf der Seite **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://www.dropbox.com/sso/<id>`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** einen Wert ein: `Dropbox`.

    > [!NOTE]
    > Der hier angegebene Wert für die Anmelde-URL ist kein echter Wert. Er muss mit der tatsächlichen Anmelde-URL aktualisiert werden. Dies wird weiter unten in diesem Tutorial beschrieben.

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Dropbox für Unternehmen einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-dropbox-for-business-sso"></a>Konfigurieren des einmaligen Anmeldens für Dropbox für Unternehmen

1. Wenn Sie die Konfiguration in Dropbox für Unternehmen automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Dropbox für Unternehmen einrichten**, um zur Anwendung Dropbox für Unternehmen weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Dropbox für Unternehmen anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 8.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Dropbox für Unternehmen manuell einrichten möchten, öffnen Sie ein neues Webbrowserfenster, navigieren Sie zu Ihrem Dropbox für Unternehmen-Mandanten, und melden Sie sich bei diesem Mandanten an. Führen Sie die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren](./media/dropboxforbusiness-tutorial/ic769509.png "Einmaliges Anmelden konfigurieren")

4. Klicken Sie auf das **Benutzersymbol**, und wählen Sie die Registerkarte **Einstellungen** aus.

    ![Einmaliges Anmelden konfigurieren](./media/dropboxforbusiness-tutorial/configure1.png "Einmaliges Anmelden konfigurieren")

5. Klicken Sie im Navigationsbereich auf der linken Seite auf **Verwaltungskonsole**.

    ![Einmaliges Anmelden konfigurieren](./media/dropboxforbusiness-tutorial/configure2.png "Einmaliges Anmelden konfigurieren")

6. Klicken Sie in der **Verwaltungskonsole** im linken Navigationsbereich auf **Einstellungen**.

    ![Einmaliges Anmelden konfigurieren](./media/dropboxforbusiness-tutorial/configure3.png "Einmaliges Anmelden konfigurieren")

7. Wählen Sie im Abschnitt **Authentifizierung** die Option **Einmaliges Anmelden** aus.

    ![Einmaliges Anmelden konfigurieren](./media/dropboxforbusiness-tutorial/configure4.png "Einmaliges Anmelden konfigurieren")

8. Führen Sie im Abschnitt **Einmaliges Anmelden** die folgenden Schritte aus:  

    ![Einmaliges Anmelden konfigurieren](./media/dropboxforbusiness-tutorial/configure5.png "Einmaliges Anmelden konfigurieren")

    a. Wählen Sie in der Dropdownliste für **Einmaliges Anmelden** die Option **Erforderlich** aus.

    b. Klicken Sie auf **Anmelde-URL hinzufügen**, und fügen Sie den Wert der **Anmelde-URL**, den Sie aus dem Azure-Portal kopiert haben, in das Textfeld **Anmelde-URL des Identitätsanbieters** ein. Anschließend klicken Sie auf **Fertig**.

    ![Einmaliges Anmelden konfigurieren](./media/dropboxforbusiness-tutorial/configure6.png "Einmaliges Anmelden konfigurieren")

    c. Klicken Sie auf **Zertifikat hochladen**, und navigieren Sie dann zu Ihrer **Base64-codierten Zertifikatsdatei**, die Sie aus dem Azure-Portal heruntergeladen haben.

    d. Klicken Sie auf **Link kopieren**, und fügen Sie den kopierten Wert in das Textfeld **Anmelde-URL** im Abschnitt **Domäne und URLs für Dropbox für Unternehmen** im Azure-Portal ein.

    e. Klicken Sie auf **Speichern**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen Britta Simon.

1. Wählen Sie im linken Bereich des Azure-Portals die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `Britta Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `BrittaSimon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Dropbox für Unternehmen gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Dropbox für Unternehmen** aus.
1. Suchen Sie auf der Übersichtsseite der App den Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste den Eintrag **Britta Simon** aus, und klicken Sie anschließend im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-dropbox-for-business-test-user"></a>Erstellen eines Dropbox für Unternehmen-Testbenutzers

In diesem Abschnitt wird ein Benutzer mit dem Namen Britta Simon in Dropbox für Unternehmen erstellt. Dropbox für Unternehmen unterstützt die Just-in-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in Dropbox für Unternehmen vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

>[!Note]
>Wenn Sie einen Benutzer manuell erstellen müssen, wenden Sie sich an das [Kundensupportteam von Dropbox für Unternehmen](https://www.dropbox.com/business/contact).

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

Wenn Sie im Zugriffsbereich auf die Kachel „Dropbox für Unternehmen“ klicken, sollten Sie automatisch bei der Dropbox für Unternehmen-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)