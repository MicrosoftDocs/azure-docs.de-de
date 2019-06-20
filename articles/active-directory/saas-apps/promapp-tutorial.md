---
title: 'Tutorial: Azure Active Directory-Integration mit Promapp | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Promapp konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: e91351d4571eaa084865c5a179ed05e6c773b952
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240411"
---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>Tutorial: Azure Active Directory-Integration mit Promapp

In diesem Tutorial erfahren Sie, wie Sie Promapp in Azure Active Directory (Azure AD) integrieren.
Diese Integration bietet die folgenden Vorteile:

* Sie können mit Azure AD steuern, wer Zugriff auf Promapp hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Promapp anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten an einem zentralen Ort verwalten: im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Promapp konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie sich für eine [einmonatige Testversion](https://azure.microsoft.com/pricing/free-trial/) registrieren.
* Ein Promapp-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Promapp unterstützt SP- und IdP-initiiertes einmaliges Anmelden.

* Promapp unterstützt die Just-in-Time-Benutzerbereitstellung.

## <a name="add-promapp-from-the-gallery"></a>Hinzufügen von Promapp aus dem Katalog

Zum Einrichten der Integration von Promapp in Azure AD müssen Sie Promapp aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Klicken Sie im linken Bereich des [Azure-Portals](https://portal.azure.com) auf **Azure Active Directory**:

    ![Wählen Sie „Azure Active Directory“.](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** > **Alle Anwendungen**:

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Um eine Anwendung hinzuzufügen, wählen Sie oben im Fenster die Option **Neue Anwendung**:

    ![Auswählen von „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Promapp** ein. Wählen Sie in den Suchergebnissen den Eintrag **Promapp** aus, und wählen Sie dann **Hinzufügen** aus.

     ![Suchergebnisse](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Promapp mithilfe eines Testbenutzers namens Britta Simon.
Damit einmaliges Anmelden funktioniert, müssen Sie eine Beziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Promapp einrichten.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Promapp müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden der Funktion zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Promapp](#configure-promapp-single-sign-on)** auf der Anwendungsseite
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD für den Benutzer zu aktivieren.
5. **[Testen des einmaligen Anmeldens](#test-single-sign-on)** , um sicherzustellen, dass die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Promapp die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für Promapp die Option **Einmaliges Anmelden** aus:

    ![„Einmaliges Anmelden“ auswählen](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren:

    ![SSO-Methode auswählen](common/select-saml-option.png)

3. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Symbol **Bearbeiten** aus, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen:

    ![Symbol „Bearbeiten“](common/edit-urls.png)

4. Führen Sie im Dialogfeld **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im IDP-initiierten Modus konfigurieren möchten.

    ![Dialogfeld „Grundlegende SAML-Konfiguration“](common/idp-intiated.png)

    1. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein:

       | |
        |--|
        | `https://go.promapp.com/TENANTNAME/`|
        | `https://au.promapp.com/TENANTNAME/`|
        | `https://us.promapp.com/TENANTNAME/`|
        | `https://eu.promapp.com/TENANTNAME/`|
        | `https://ca.promapp.com/TENANTNAME/`|
        |   |

       > [!NOTE]
       > Die Azure AD-Integration mit Promapp ist derzeit nur für die vom Dienst initiierte Authentifizierung konfiguriert. (Das bedeutet, dass das Aufrufen einer Promapp-URL den Authentifizierungsprozesses initiiert.) Das Feld **Antwort-URL** ist jedoch ein Pflichtfeld.

    1. Geben Sie im Feld **Antwort-URL** eine URL im folgenden Format ein:

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

5. Wenn Sie die Anwendung im SP-initiierten Modus konfigurieren möchten, wählen Sie **Zusätzliche URLs festlegen** aus. Geben Sie im Feld **Anmelde-URL** eine URL im folgenden Format ein:

      `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    ![SSO-Informationen zur Domäne und zu den URLs für Promapp](common/metadata-upload-additional-signon.png)

   

    > [!NOTE]
    > Diese Werte sind Platzhalter. Sie müssen die tatsächlichen Werte für Bezeichner, Antwort-URL und Anmelde-URL verwenden. Die Werte erhalten Sie vom [Promapp-Supportteam](https://www.promapp.com/about-us/contact-us/). Sie können sich auch die Muster im Azure-Portal im Dialogfeld **Grundlegende SAML-Konfiguration** ansehen.

6. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Link **Herunterladen** neben **Zertifikat (Base64)** gemäß Ihren Anforderungen aus, und speichern Sie das Zertifikat auf Ihrem Computer:

    ![Downloadlink für Zertifikat](common/certificatebase64.png)

7. Kopieren Sie im Abschnitt **Promapp einrichten** die entsprechenden URLs gemäß Ihren Anforderungen:

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    1. **Anmelde-URL**

    1. **Azure AD-Bezeichner**

    1. **Abmelde-URL**

### <a name="configure-promapp-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Promapp

1. Melden Sie sich bei Ihrer Promapp-Unternehmenswebsite als Administrator an.

2. Wählen Sie im oberen Fensterbereich **Admin** (Administrator) aus:
   
    ![Auswählen von „Admin“ (Administrator)][12]

3. Wählen Sie **Configure** (Konfigurieren) aus:
   
    ![Auswählen von „Configure“ (Konfigurieren)][13]

4. Führen Sie im Dialogfeld **Security** (Sicherheit) die folgenden Schritte aus:
   
    ![Dialogfeld „Security“ (Sicherheit)][14]
    
    1. Fügen Sie im Feld **SSO-Login URL** (SSO-Anmelde-URL) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.
    
    1. Wählen Sie in der Liste **SSO - Single Sign-on Mode** (SSO: SSO-Modus) die Option **Optional** aus. Wählen Sie **Speichern** aus.

       > [!NOTE]
       > Der optionale Modus dient nur zu Testzwecken. Wenn Sie mit der Konfiguration zufrieden sind, wählen Sie in der Liste **SSO - Single Sign-on Mode** (SSO: SSO-Modus) die Option **Required** (Erforderlich) aus, um für alle Benutzer die Authentifizierung über Azure AD zu erzwingen.

    1. Öffnen Sie im Editor das Zertifikat, das Sie im vorherigen Abschnitt heruntergeladen haben. Kopieren Sie den Inhalt des Zertifikats ohne die erste ( **-----BEGIN CERTIFICATE-----** ) und die letzte Zeile ( **-----END CERTIFICATE-----** ). Fügen Sie den Zertifikatsinhalt ins Feld **SSO - x.509 Certificate** (SSO: x.509-Zertifikat) ein, und wählen Sie dann **Save** (Speichern) aus.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer namens Britta Simon.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** aus, und wählen Sie dann **Benutzer** und anschließend **Alle Benutzer** aus:

    ![„Alle Benutzer“ auswählen](common/users.png)

2. Wählen Sie im oberen Bildschirmbereich die Option **Neuer Benutzer** aus:

    ![„Neuer Benutzer“ auswählen](common/new-user.png)

3. Führen Sie im Dialogfeld **Benutzer** die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    1. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.
  
    1. Geben Sie im Feld **Benutzername** die Zeichenfolge **BrittaSimon@\<IhreUnternehmensdomäne>.\<Erweiterung>** ein. (Beispiel: BrittaSimon@contoso.com.)

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den im Feld **Kennwort** angezeigten Wert.

    1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Promapp gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Promapp** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen den Eintrag **Promapp** aus.

    ![Liste der Anwendungen](common/all-applications.png)

3. Wählen Sie im linken Bereich die Option **Benutzer und Gruppen** aus:

    ![Benutzer und Gruppen auswählen](common/users-groups-blade.png)

4. Wählen Sie die Schaltfläche **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Auswählen von „Benutzer hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste den Eintrag **Britta Simon** aus, und klicken Sie dann am unteren Bildschirmrand auf die Schaltfläche **Auswählen**.

6. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Klicken Sie am unteren Bildschirmrand auf die Schaltfläche **Auswählen**.

7. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="just-in-time-user-provisioning"></a>Just-In-Time-Benutzerbereitstellung.

Promapp unterstützt die Just-in-Time-Benutzerbereitstellung. Dieses Feature ist standardmäßig aktiviert. Ist ein Benutzer noch nicht in Promapp vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

Jetzt müssen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich testen.

Wenn Sie im Zugriffsbereich die Kachel „Promapp“ auswählen, sollten Sie automatisch bei der Promapp-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Zugreifen auf und Verwenden von Apps im Portal „Meine Apps“](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Tutorials zur Integration von SaaS-Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png
