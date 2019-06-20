---
title: 'Tutorial: Azure Active Directory-Integration mit InsideView | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und InsideView konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: 0fdabd237fa128326673d84e889387d03f184b00
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236575"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Tutorial: Azure Active Directory-Integration mit InsideView

In diesem Tutorial erfahren Sie, wie Sie InsideView in Azure Active Directory (Azure AD) integrieren.
Diese Integration bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf InsideView hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei InsideView anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten an einem zentralen Ort verwalten: im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit InsideView konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein InsideView-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* InsideView unterstützt IdP-initiiertes einmaliges Anmelden.

## <a name="add-insideview-from-the-gallery"></a>Hinzufügen von InsideView aus dem Katalog

Zum Einrichten der Integration von InsideView in Azure AD müssen Sie InsideView aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Klicken Sie im linken Bereich des [Azure-Portals](https://portal.azure.com) auf **Azure Active Directory**:

    ![Wählen Sie „Azure Active Directory“.](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** > **Alle Anwendungen**:

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Um eine Anwendung hinzuzufügen, wählen Sie oben im Fenster die Option **Neue Anwendung**:

    ![Auswählen von „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **InsideView** ein. Wählen Sie in den Suchergebnissen den Eintrag **InsideView** aus, und wählen Sie dann **Hinzufügen** aus.

    ![Suchergebnisse](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit InsideView mithilfe eines Testbenutzers namens Britta Simon.
Damit einmaliges Anmelden funktioniert, müssen Sie eine Beziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in InsideView einrichten.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit InsideView müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden der Funktion zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für InsideView](#configure-insideview-single-sign-on)** auf der Anwendungsseite
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD für den Benutzer zu aktivieren.
5. **[Erstellen eines InsideView-Testbenutzers](#create-an-insideview-test-user)** , der mit der Darstellung des Benutzers in Azure AD verknüpft ist
6. **[Testen des einmaligen Anmeldens](#test-single-sign-on)** , um sicherzustellen, dass die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit InsideView die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für InsideView die Option **Einmaliges Anmelden** aus.

    ![„Einmaliges Anmelden“ auswählen](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren:

    ![SSO-Methode auswählen](common/select-saml-option.png)

3. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Symbol **Bearbeiten** aus, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen:

    ![Symbol „Bearbeiten“](common/edit-urls.png)

4. Führen Sie im Dialogfeld **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![Dialogfeld „Grundlegende SAML-Konfiguration“](common/idp-reply.png)

    Geben Sie im Feld **Antwort-URL** eine URL im folgenden Format ein:

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Dieser Wert ist ein Platzhalter. Sie müssen die tatsächliche Antwort-URL verwenden. Den Wert erhalten Sie vom [InsideView-Supportteam ](mailto:support@insideview.com). Sie können sich auch die Muster im Azure-Portal im Dialogfeld **Grundlegende SAML-Konfiguration** ansehen.

5. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Link **Herunterladen** neben **Zertifikat (Rohdaten)** gemäß Ihren Anforderungen aus, und speichern Sie das Zertifikat auf Ihrem Computer:

    ![Downloadlink für Zertifikat](common/certificateraw.png)

6. Kopieren Sie im Abschnitt **InsideView einrichten** die entsprechenden URLs gemäß Ihren Anforderungen:

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    1. **Anmelde-URL**

    1. **Azure AD-Bezeichner**

    1. **Abmelde-URL**

### <a name="configure-insideview-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für InsideView

1. Melden Sie sich in einem neuen Webbrowserfenster auf der InsideView-Unternehmenswebsite als Administrator an.

1. Wählen Sie oben im Fenster **Admin** (Administrator) > **SingleSignOn Settings** (SSO-Einstellungen) > **Add SAML** (SAML hinzufügen) aus.
   
   ![SAML-Einstellungen für einmaliges Anmelden](./media/insideview-tutorial/ic794135.png "SAML-Einstellungen für einmaliges Anmelden")

1. Führen Sie im Abschnitt **Add a New SAML** (Neue SAML hinzufügen) die folgenden Schritte aus:

    ![Abschnitt „Add a New SAML“ (Neue SAML hinzufügen)](./media/insideview-tutorial/ic794136.png "Abschnitt „Add a New SAML“ (Neue SAML hinzufügen)")

    1. Geben Sie im Textfeld **STS Name** (STS-Name) einen Namen für die Konfiguration ein.

    1. Fügen Sie im Feld **SamlP/WS-Fed Unsolicited EndPoint** (Unangeforderter SamlP/WS-Fed-Endpunkt) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    1. Öffnen Sie das Rohdatenzertifikat, das Sie aus dem Azure-Portal heruntergeladen haben. Kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn ins Feld **STS Certificate** (STS-Zertifikat) ein.

    1. Geben Sie im Feld **Crm User Id Mapping** (CRM-Benutzer-ID-Zuordnung) die Zeichenfolge **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** ein.

    1. Geben Sie im Feld **Crm Email Mapping** (CRM-E-Mail-Adresszuordnung) die Zeichenfolge **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** ein.

    1. Geben Sie im Feld **Crm First Name Mapping** (CRM-Vornamenzuordnung) die Zeichenfolge **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname** ein.

    1. Geben Sie im Feld **Crm lastName Mapping** (CRM-Nachnamenzuordnung) die Zeichenfolge **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname** ein.  

    1. Wählen Sie **Speichern** aus.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer namens Britta Simon.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** aus, und wählen Sie dann **Benutzer** und anschließend **Alle Benutzer** aus:

    ![„Alle Benutzer“ auswählen](common/users.png)

2. Wählen Sie oben im Fenster die Option **Neuer Benutzer** aus:

    ![„Neuer Benutzer“ auswählen](common/new-user.png)

3. Führen Sie im Dialogfeld **Benutzer** die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    1. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.
  
    1. Geben Sie im Feld **Benutzername** die Zeichenfolge **BrittaSimon@\<IhreUnternehmensdomäne>.\<Erweiterung>** ein. (Beispiel: BrittaSimon@contoso.com.)

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den im Feld **Kennwort** angezeigten Wert.

    1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf InsideView gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **InsideView** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen den Eintrag **InsideView** aus.

    ![Liste der Anwendungen](common/all-applications.png)

3. Wählen Sie im linken Bereich die Option **Benutzer und Gruppen** aus:

    ![Benutzer und Gruppen auswählen](common/users-groups-blade.png)

4. Wählen Sie die Schaltfläche **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Auswählen von „Benutzer hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Fenster auf die Schaltfläche **Auswählen**.

6. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Klicken Sie unten im Fenster auf die Schaltfläche **Auswählen**.

7. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-an-insideview-test-user"></a>Erstellen eines InsideView-Testbenutzers

Damit sich Azure AD-Benutzer bei InsideView anmelden können, müssen Sie sie in InsideView hinzufügen. Sie müssen diese manuell hinzufügen.

Wenden Sie sich an das [InsideView-Supportteam](mailto:support@insideview.com), wenn Sie Benutzer oder Kontakte in InsideView erstellen möchten.

> [!NOTE]
> Sie können Azure AD-Benutzerkonten mithilfe beliebiger Tools zum Erstellen von Benutzerkonten oder mithilfe der von InsideView bereitgestellten API erstellen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

Jetzt müssen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich testen.

Wenn Sie im Zugriffsbereich die Kachel „InsideView“ auswählen, sollten Sie automatisch bei der InsideView-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Zugreifen auf und Verwenden von Apps im Portal „Meine Apps“](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Tutorials zur Integration von SaaS-Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
