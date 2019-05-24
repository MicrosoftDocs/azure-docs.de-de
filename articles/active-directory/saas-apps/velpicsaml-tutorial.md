---
title: 'Tutorial: Azure Active Directory-Integration mit Velpic SAML | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Velpic SAML konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc8431e2c45cc3bfdfa08dd0078220ab2d290309
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65905400"
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>Tutorial: Azure Active Directory-Integration mit Velpic SAML

In diesem Tutorial erfahren Sie, wie Sie Velpic SAML in Azure Active Directory (Azure AD) integrieren.
Die Integration von Velpic SAML in Azure AD bietet Ihnen folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Velpic SAML hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Velpic SAML anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Velpic SAML konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Velpic SAML-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Velpic SAML unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-velpic-saml-from-the-gallery"></a>Hinzufügen von Velpic SAML aus dem Katalog

Zum Konfigurieren der Integration von Velpic SAML in Azure AD müssen Sie Velpic SAML aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um Velpic SAML aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Velpic SAML** ein, wählen Sie im Ergebnisbereich **Velpic SAML** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Velpic SAML in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Velpic SAML basierend auf einem Testbenutzer mit dem Namen **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Velpic SAML eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Velpic SAML müssen Sie die folgenden Schritte durchführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Velpic SAML](#configure-velpic-saml-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Velpic SAML-Testbenutzers](#create-velpic-saml-test-user)**, um eine Entsprechung von Britta Simon in Velpic SAML zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Velpic SAML die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Velpic SAML** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Velpic SAML](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<sub-domain>.velpicsaml.net`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://auth.velpic.com/saml/v2/<entity-id>/login`.

    > [!NOTE]
    > Beachten Sie, dass die Anmelde-URL vom Velpic SAML-Team bereitgestellt wird und dass der Bezeichnerwert zur Verfügung steht, wenn Sie das SSO-Plug-In auf der Velpic SAML-Seite konfigurieren. Sie müssen diesen Wert von der Velpic SAML-Anwendungsseite kopieren und hier einfügen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **Velpic SAML einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-velpic-saml-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Velpic SAML

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Velpic SAML-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf die Registerkarte **Verwalten**, und wechseln Sie zum Abschnitt **Integration**. Hier müssen Sie auf die Schaltfläche **Plug-Ins** klicken, um ein neues Plug-In für die Anmeldung zu erstellen.

    ![Plug-In](./media/velpicsaml-tutorial/velpic_1.png)

3. Klicken Sie auf die Schaltfläche **Plug-In hinzufügen**.
    
    ![Plug-In](./media/velpicsaml-tutorial/velpic_2.png)

4. Klicken Sie auf der Seite „Plug-In hinzufügen“ auf die Kachel **SAML**.
    
    ![Plug-In](./media/velpicsaml-tutorial/velpic_3.png)

5. Geben Sie den Namen des neuen SAML-Plug-Ins ein, und klicken Sie auf die Schaltfläche **Hinzufügen**.

    ![Plug-In](./media/velpicsaml-tutorial/velpic_4.png)

6. Geben Sie die Details wie folgt ein:

    ![Plug-In](./media/velpicsaml-tutorial/velpic_5.png)

    a. Geben Sie im Textfeld **Name** den Namen des SAML-Plug-Ins ein.

    b. Fügen Sie im Textfeld **Issuer URL** (Aussteller-URL) den **Azure AD-Bezeichner** ein, den Sie im Azure-Portal im Fenster **Anmeldung konfigurieren** kopiert haben.

    c. Laden Sie in der **Konfiguration der Anbietermetadaten** die XML-Metadatendatei hoch, die Sie aus dem Azure-Portal heruntergeladen haben.

    d. Sie können auch die SAML-Just-in-Time-Bereitstellung aktivieren, indem Sie das Kontrollkästchen **Neue Benutzer automatisch erstellen** aktivieren. Wenn ein Benutzer nicht in Velpic vorhanden und dieses Flag nicht aktiviert ist, tritt bei der Anmeldung über Azure ein Fehler auf. Wenn das Flag aktiviert ist, wird der Benutzer zum Zeitpunkt der Anmeldung automatisch in Velpic bereitgestellt. 

    e. Kopieren Sie die **SSO-URL** aus dem Textfeld, und fügen Sie sie im Azure-Portal ein.
    
    f. Klicken Sie auf **Speichern**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen `brittasimon@yourcompanydomain.extension` ein. Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Velpic SAML gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Velpic SAML** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Velpic SAML** aus.

    ![Velpic SAML-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-velpic-saml-test-user"></a>Erstellen eines Velpic SAML-Testbenutzers

Dieser Schritt ist in der Regel nicht erforderlich, da die Anwendung die Just-in-Time-Benutzerbereitstellung unterstützt. Wenn die automatische Benutzerbereitstellung nicht aktiviert ist, kann die manuelle Erstellung von Benutzern wie unten beschrieben durchgeführt werden.

Melden Sie sich als Administrator bei Ihrer Velpic SAML-Unternehmenswebsite an, und führen Sie die folgenden Schritte aus:
    
1. Klicken Sie auf die Registerkarte „Verwalten“, und wechseln Sie zum Abschnitt „Benutzer“. Klicken Sie dann auf die Schaltfläche „Neu“, um Benutzer hinzuzufügen.

    ![Benutzer hinzufügen](./media/velpicsaml-tutorial/velpic_7.png)

2. Führen Sie im Dialogfeld **Neuen Benutzer erstellen** die folgenden Schritte aus.

    ![user](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. Geben Sie im Textfeld **First Name** (Vorname) den Vornamen ein, z. B. Britta.

    b. Geben Sie im Textfeld **Last Name** (Nachname) den Nachnamen ein, z. B. Simon.

    c. Geben Sie im Textfeld **Benutzername** den Benutzernamen von Britta Simon ein.

    d. Geben Sie im Textfeld **Email** (E-Mail) die E-Mail-Adresse des Kontos ein, z. B. Brittasimon@contoso.com.

    e. Die übrigen Informationen sind optional und können nach Bedarf ausgefüllt werden.
    
    f. Klicken Sie auf **SPEICHERN**.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

1. Wenn Sie im Zugriffsbereich auf die Velpic SAML-Kachel klicken, wird die Anmeldeseite der Velpic SAML-Anwendung aufgerufen. Auf der Anmeldeseite wird Ihnen die Schaltfläche **Mit Azure AD anmelden** angezeigt.

    ![Plug-In](./media/velpicsaml-tutorial/velpic_6.png)

1. Klicken Sie auf die Schaltfläche **Mit Azure AD anmelden**, um sich über das Azure AD-Konto bei Velpic anzumelden.

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

