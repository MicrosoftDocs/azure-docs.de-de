---
title: 'Tutorial: Azure Active Directory-Integration von OpsGenie | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und OpsGenie konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 98e4d1870c57c9718e2d4293157b21ead8ea44e1
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65987491"
---
# <a name="tutorial-azure-active-directory-integration-with-opsgenie"></a>Tutorial: Azure Active Directory-Integration von OpsGenie

In diesem Tutorial erfahren Sie, wie Sie OpsGenie in Azure Active Directory (Azure AD) integrieren.
Die Integration von OpsGenie in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf OpsGenie hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei OpsGenie anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit OpsGenie konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein OpsGenie-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* OpsGenie unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-opsgenie-from-the-gallery"></a>Hinzufügen von OpsGenie aus dem Katalog

Zum Konfigurieren der Integration von OpsGenie in Azure AD müssen Sie OpsGenie aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um OpsGenie aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **OpsGenie** ein, wählen Sie im Ergebnisbereich **OpsGenie** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![OpsGenie in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit OpsGenie mithilfe eines Testbenutzers namens **B. Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in OpsGenie eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei OpsGenie müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für OpsGenie](#configure-opsgenie-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen
5. **[Erstellen eines OpsGenie-Testbenutzers](#create-opsgenie-test-user)** , um eine Entsprechung von B. Simon in OpsGenie zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit OpsGenie die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **OpsGenie** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für OpsGenie](common/sp-signonurl.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL ein: `https://app.opsgenie.com/auth/login`.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

6. Kopieren Sie im Abschnitt **OpsGenie einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-opsgenie-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für OpsGenie

1. Öffnen Sie eine weitere Browserinstanz, und melden Sie sich bei OpsGenie als Administrator an.

2. Klicken Sie auf **Einstellungen** und dann auf die Registerkarte **Einmaliges Anmelden**.
   
    ![Einmaliges Anmelden bei OpsGenie](./media/opsgenie-tutorial/tutorial_opsgenie_06.png)

3. Um SSO (Single Sign-On, einmaliges Anmelden) zu aktivieren, wählen Sie **Enabled**aus.
   
    ![OpsGenie-Einstellungen](./media/opsgenie-tutorial/tutorial_opsgenie_07.png) 

4. Klicken Sie im Abschnitt **Anbieter** auf die Registerkarte **Azure Active Directory**.
   
    ![OpsGenie-Einstellungen](./media/opsgenie-tutorial/tutorial_opsgenie_08.png) 

5. Führen Sie auf der Dialogfeldseite "Azure Active Directory" die folgenden Schritte aus:
   
    ![OpsGenie-Einstellungen](./media/opsgenie-tutorial/tutorial_opsgenie_09.png)
    
    a. Fügen Sie im Textfeld **SAML 2.0 Endpoint** (SAML 2.0-Endpunkt) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.
    
    b. Fügen Sie im Textfeld **Metadaten-URL** den Wert für die **Verbundmetadaten-URL der App** ein, den Sie aus dem Azure-Portal kopiert haben.
    
    c. Klicken Sie auf **Änderungen speichern**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens B. Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie **B. Simon** in das Feld **Name** ein.
  
    b. Geben Sie im Feld **Benutzername** **bsimon@yourcompanydomain.extension** ein.  
    Zum Beispiel, BSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf OpsGenie gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **OpsGenie** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **OpsGenie**aus.

    ![OpsGenie-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-opsgenie-test-user"></a>Erstellen eines OpsGenie-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens B. Simon in OpsGenie. 

1. Melden Sie sich in einem Webbrowserfenster bei Ihrem OpsGenie-Mandanten als Administrator an.

2. Navigieren Sie zur Liste der Benutzer, indem Sie im linken Bereich auf **Users** (Benutzer) klicken.
   
    ![OpsGenie-Einstellungen](./media/opsgenie-tutorial/tutorial_opsgenie_10.png) 

3. Klicken Sie auf **Benutzer hinzufügen**.

4. Führen Sie im Dialogfeld **Add User** die folgenden Schritte aus:
   
    ![OpsGenie-Einstellungen](./media/opsgenie-tutorial/tutorial_opsgenie_11.png)
   
    a. Geben Sie im Textfeld **Email** (E-Mail-Adresse) die E-Mail-Adresse von B. Simon aus Azure Active Directory ein.
   
    b. Geben Sie im Textfeld **Full Name** (Vollständiger Name) den Namen **B. Simon** ein.
   
    c. Klicken Sie auf **Speichern**. 

>[!NOTE]
>B: Daraufhin erhält B. Simon eine E-Mail mit Anweisungen zum Einrichten ihres Profils.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „OpsGenie“ klicken, sollten Sie automatisch bei der OpsGenie-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

