---
title: 'Tutorial: Azure Active Directory-Integration mit TigerText Secure Messenger | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und TigerText Secure Messenger konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 840b1fe55556cfd853e0928164891d6b21b17cc2
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956865"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Tutorial: Azure Active Directory-Integration mit TigerText Secure Messenger

In diesem Tutorial erfahren Sie, wie Sie TigerText Secure Messenger in Azure Active Directory (Azure AD) integrieren.

Die Integration von TigerText Secure Messenger in Azure AD bietet folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf TigerText Secure Messenger haben soll.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei TigerText Secure Messenger anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten an einem zentralen Ort verwalten: im Azure-Portal.

Details zur Integration von SaaS-Apps (Software-as-a-Service) in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Voraussetzungen

Um die TigerText Secure Messenger-Integration in Azure AD konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Ein TigerText Secure Messenger-Abonnement, das für das einmalige Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung und integrieren TigerText Secure Messenger in Azure AD.

TigerText Secure Messenger unterstützt SP-initiiertes einmaliges Anmelden.

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>Hinzufügen von TigerText Secure Messenger aus dem Azure Marketplace

Zum Konfigurieren der Integration von TigerText Secure Messenger in Azure AD müssen Sie TigerText Secure Messenger aus dem Azure Marketplace der Liste mit den verwalteten SaaS-Apps hinzufügen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com?azure-portal=true) an.
1. Wählen Sie im linken Bereich **Azure Active Directory** aus.

    ![Die Option „Azure Active Directory“](common/select-azuread.png)

1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie oben im Fenster **+Neue Anwendung** aus, um eine neue Anwendung hinzuzufügen.

    ![Die Option „Neue Anwendung“](common/add-new-app.png)

1. Geben Sie im Suchfeld **TigerText Secure Messenger** ein. Wählen Sie in den Suchergebnissen **TigerText Secure Messenger** aus, und wählen Sie dann **Hinzufügen** aus, um die Anwendung hinzuzufügen.

    ![TigerText Secure Messenger in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei TigerText Secure Messenger mithilfe eines Testbenutzers namens **Britta Simon**. Damit einmaliges Anmelden funktioniert, muss eine Verknüpfung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in TigerText Secure Messenger eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei TigerText Secure Messenger müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für TigerText Secure Messenger](#configure-tigertext-secure-messenger-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit der Testbenutzerin Britta Simon zu testen.
1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Erstellen eines TigerText Secure Messenger-Testbenutzers](#create-a-tigertext-secure-messenger-test-user)** , damit ein Benutzer namens Britta Simon in TigerText Secure Messenger verfügbar ist, der mit dem Azure AD-Benutzer namens Britta Simon verknüpft ist.
1. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD bei TigerText Secure Messenger zu konfigurieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **TigerText Secure Messenger** die Option **Einmaliges Anmelden** aus.

    ![Option „Einmaliges Anmelden konfigurieren“](common/select-sso.png)

1. Wählen Sie im Bereich **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

1. Wählen Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** die Option **Bearbeiten** (Stiftsymbol), um den Bereich **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Bereich **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und den URLs für TigerText Secure Messenger](common/sp-identifier.png)

    1. Geben Sie im Feld **Anmelde-URL** eine URL ein:

       `https://home.tigertext.com`

    1. Geben Sie im Feld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > Der **Bezeichner (Entitäts-ID)** -Wert ist nicht korrekt. Aktualisieren Sie den Wert mit dem tatsächlichen Bezeichner. Wenden Sie sich an das [Supportteam für TigerText Secure Messenger](mailto:prosupport@tigertext.com), um den Wert zu erhalten. Sie können sich auch die Muster im Bereich **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Klicken Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um die **Verbundmetadaten-XML** aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadoption für Verbundmetadaten-XML](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **TigerText Secure Messenger einrichten** die URL(s), die Sie benötigen:

   * **Anmelde-URL**
   * **Azure AD-Bezeichner**
   * **Abmelde-URL**

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für TigerText Secure Messenger

Zum Konfigurieren des einmaligen Anmeldens aufseiten von TigerText Secure Messenger müssen Sie die heruntergeladene Verbundmetadaten-XML und die kopierten URLs aus dem Azure-Portal an das [Supportteam von TigerText Secure Messenger](mailto:prosupport@tigertext.com) senden. Das TigerText Secure Messenger-Team sorgt dafür, dass die SAML-Verbindung für einmaliges Anmelden auf beiden Seiten ordnungsgemäß eingerichtet wird.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin mit dem Namen Britta Simon.

1. Wählen Sie im linken Bereich des Azure-Portals **Azure Active Directory**   > **Benutzer** > **Alle Benutzer** aus.

    ![Die Optionen „Benutzer“ und „Alle Benutzer“](common/users.png)

1. Wählen Sie oben im Bildschirm die Option **+ Neuer Benutzer** aus.

    ![Option „Neuer Benutzer“](common/new-user.png)

1. Führen Sie im Bereich **Benutzer** folgende Schritte aus:

    ![Der Bereich „Benutzer“](common/user-properties.png)

    1. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.
  
    1. Geben Sie im Feld **Benutzername** den **brittasimon\@\<ihreunternehmensdomäne>.\<erweiterung>** ein. Beispiel: **BrittaSimon\@contoso.com**.

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.

    1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf TigerText Secure Messenger gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **TigerText Secure Messenger** aus.

    ![Bereich für Unternehmensanwendungen](common/enterprise-applications.png)

1. Wählen Sie in der Anwendungsliste den Eintrag **TigerText Secure Messenger** aus.

    ![TigerText Secure Messenger in der Anwendungsliste](common/all-applications.png)

1. Wählen Sie im linken Bereich unter **VERWALTEN** die Option **Benutzer und Gruppen** aus.

    ![Option „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie die Schaltfläche **+ Benutzer hinzufügen** aus, und wählen Sie anschließend im Bereich **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Bereich **Benutzer und Gruppen** in der Liste **Benutzer** den Eintrag **Britta Simon** und anschließend unten im Bereich **Auswählen** aus.

1. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Bereich **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Wählen Sie unten im Bereich die Option **Auswählen**.

1. Wählen Sie im Bereich **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-a-tigertext-secure-messenger-test-user"></a>Erstellen eines TigerText Secure Messenger-Testbenutzers

In diesem Abschnitt wird in TigerText Secure Messenger ein Benutzer namens Britta Simon erstellt. Arbeiten Sie mit dem [Supportteam von TigerText Secure Messenger](mailto:prosupport@tigertext.com) zusammen, um Britta Simon als Benutzer in TigerText Secure Messenger hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Portals „Meine Apps“.

Bei Auswahl von **TigerText Secure Messenger** im Portal „Meine Apps“ sollten Sie automatisch bei dem TigerText Secure Messenger-Abonnement angemeldet werden, für das Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Portal „Meine Apps“ finden Sie unter [Zugreifen auf und Verwenden von Apps im Portal „Meine Apps“](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
