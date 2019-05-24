---
title: 'Tutorial: Azure Active Directory-Integration mit Flatter Files | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Flatter Files konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 48c5804df53c084715cac872aa431cf8694c6d35
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65740277"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Tutorial: Azure Active Directory-Integration mit Flatter Files

In diesem Tutorial erfahren Sie, wie Sie Flatter Files in Azure Active Directory (Azure AD) integrieren.
Die Integration von Flatter Files in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Flatter Files hat.
* Sie können es Ihren Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei Flatter Files angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Flatter Files konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Flatter Files-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Flatter Files unterstützt **IDP-initiiertes** einmaliges Anmelden.

## <a name="adding-flatter-files-from-the-gallery"></a>Hinzufügen von Flatter Files aus dem Katalog

Zum Konfigurieren der Integration von Flatter Files in Azure AD müssen Sie Flatter Files aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um Flatter Files aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Flatter Files** ein, wählen Sie im Ergebnisbereich **Flatter Files** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Flatter Files in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Flatter Files basierend auf einem Testbenutzer namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Flatter Files eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD mit Flatter Files müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Flatter Files](#configure-flatter-files-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Flatter Files-Testbenutzers](#create-flatter-files-test-user)**, um ein Pendant von Britta Simon in Flatter Files zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Flatter Files die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Flatter Files** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Im Abschnitt **Grundlegende SAML-Konfiguration** muss der Benutzer keine Schritte ausführen, weil die App bereits in Azure integriert ist.

    ![SSO-Informationen zur Domäne und zu den URLs für Flatter Files](common/preintegrated.png)

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Flatter Files einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-flatter-files-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Flatter Files

1. Melden Sie sich bei Ihrer Flatter Files-Anwendung als Administrator an.

2. Klicken Sie auf **DASHBOARD**. 
   
    ![Configure single sign-on](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

3. Klicken Sie auf **Settings** (Einstellungen), und führen Sie auf der Registerkarte **Company** (Unternehmen) die folgenden Schritte aus: 
   
    ![Configure single sign-on](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Wählen Sie **Use SAML 2.0 for Authentication**aus.
    
    b. Klicken Sie auf **Configure SAML**.

4. Führen Sie im Dialogfeld **SAML Configuration** die folgenden Schritte aus: 
   
    ![Configure single sign-on](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. Geben Sie in das Textfeld für die **Domäne** die registrierte Domäne ein.
   
   > [!NOTE]
   > Wenn Sie noch keine registrierte Domäne besitzen, wenden Sie sich unter [support@flatterfiles.com](mailto:support@flatterfiles.com). 
    
    b. Fügen Sie in das Textfeld **Identity Provider URL** (Identitätsanbieter-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.
   
    c.  Öffnen Sie das Base64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Zertifikat des Identitätsanbieters** ein.

    d. Klicken Sie auf **Aktualisieren**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** Folgendes ein: **brittasimon\@ihreunternehmensdomäne.erweiterung**.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Flatter Files gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **Flatter Files** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Flatter Files**aus.

    ![Flatter Files-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-flatter-files-test-user"></a>Erstellen eines Flatter Files-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in Flatter Files.

**Um einen Benutzer namens Britta Simon in Flatter Files zu erstellen, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrer **Flatter Files** -Unternehmenswebsite als Administrator an.

2. Klicken Sie im Navigationsbereich links auf **Settings** (Einstellungen) und anschließend auf die Registerkarte **Users** (Benutzer).
   
    ![Erstellen eines Flatter Files-Benutzers](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Klicken Sie auf **Benutzer hinzufügen**. 

4. Führen Sie im Dialogfeld **Add User** die folgenden Schritte aus:
   
    ![Erstellen eines Flatter Files-Benutzers](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. Geben Sie in das Textfeld **Vorname** den Namen **Britta** ein.
   
    b. Geben Sie in das Textfeld **Nachname** den Namen **Simon** ein. 
   
    c. Geben Sie im Textfeld **Email Address** die E-Mail-Adresse von Britta Simon aus dem Azure-Portal ein.
   
    d. Klicken Sie auf **Submit**.   


### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Flatter Files“ klicken, sollten Sie automatisch bei der Flatter Files-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

