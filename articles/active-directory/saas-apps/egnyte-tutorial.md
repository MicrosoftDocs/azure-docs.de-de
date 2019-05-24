---
title: 'Tutorial: Azure Active Directory-Integration mit Egnyte | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Egnyte konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 2/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ff1718211fa298e4daee694795a0b2438033fe2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65861948"
---
# <a name="tutorial-azure-active-directory-integration-with-egnyte"></a>Tutorial: Azure Active Directory-Integration mit Egnyte

In diesem Tutorial erfahren Sie, wie Sie Egnyte in Azure Active Directory (Azure AD) integrieren.
Die Integration von Egnyte in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Egnyte haben soll.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Egnyte anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren der Azure AD-Integration mit Egnyte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Egnyte-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Egnyte unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-egnyte-from-the-gallery"></a>Hinzufügen von Egnyte aus dem Katalog

Zum Konfigurieren der Integration von Egnyte in Azure AD müssen Sie Egnyte aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um Egnyte aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie **Egnyte** in das Suchfeld ein, wählen Sie im Ergebnisbereich **Egnyte** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Egnyte in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Egnyte mithilfe eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Egnyte eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Egnyte müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Egnyte](#configure-egnyte-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Egnyte-Testbenutzers](#create-egnyte-test-user)**, um ein Pendant von Britta Simon in Egnyte zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Egnyte die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Egnyte** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Egnyte](common/sp-signonurl.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<companyname>.egnyte.com`

    > [!NOTE]
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Den Wert erhalten Sie vom [Supportteam für den Egnyte-Client](https://www.egnyte.com/corp/contact_egnyte.html). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

5. Kopieren Sie im Abschnitt **Egnyte einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-egnyte-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Egnyte

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Egnyte-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf **Einstellungen**.
   
    ![Einstellungen](./media/egnyte-tutorial/ic787819.png "Einstellungen")

3. Klicken Sie im Menü auf **Einstellungen**.

    ![Einstellungen](./media/egnyte-tutorial/ic787820.png "Einstellungen")

4. Klicken Sie auf die Registerkarte **Konfiguration** und anschließend auf **Sicherheit**.

    ![Sicherheit](./media/egnyte-tutorial/ic787821.png "Sicherheit")

5. Führen Sie im Abschnitt **SSO-Authentifizierung** die folgenden Schritte aus:

    ![SSO-Authentifizierung](./media/egnyte-tutorial/ic787822.png "SSO-Authentifizierung")   
    
    a. Wählen Sie für **SSO-Authentifizierung** die Option **SAML 2.0** aus.
   
    b. Wählen Sie für **Identitätsanbieter** die Option **AzureAD** aus.
   
    c. Fügen Sie die **Anmelde-URL**, die Sie aus dem Azure-Portal kopiert haben, in das Textfeld **Anmelde-URL des Identitätsanbieters** ein.
   
    d. Fügen Sie den **Azure AD-Bezeichner**, den Sie aus dem Azure-Portal kopiert haben, in das Textfeld **Identity provider entity ID** (Entitäts-ID des Identitätsanbieters) ein.
      
    e. Öffnen Sie das Base64-codierte Zertifikat im Editor, das Sie aus dem Azure-Portal heruntergeladen haben, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Identitätsanbieterzertifikat** ein.
   
    f. Wählen Sie für **Standardbenutzerzuordnung** die Option **E-Mail-Adresse** aus.
   
    g. Wählen Sie für **Domänenspezifischen Ausstellerwert verwenden** die Einstellung **Deaktiviert** aus.
   
    h. Klicken Sie auf **Speichern**.

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Egnyte gewähren.

1. Wählen Sie im Azure-Portal die Option **Unternehmensanwendungen** aus, und wählen Sie dann **Alle Anwendungen** und **Egnyte** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Egnyte** aus.

    ![Egnyte-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-egnyte-test-user"></a>Erstellen eines Egnyte-Testbenutzers

Damit sich Azure AD-Benutzer bei Egnyte anmelden können, müssen sie in Egnyte bereitgestellt werden. Im Fall von Egnyte ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen von Benutzerkonten die folgenden Schritte aus:**

1. Melden Sie sich bei der **Egnyte** -Unternehmenswebsite als Administrator an.

2. Navigieren Sie zu **Einstellungen \> Benutzer und Gruppen**.

3. Klicken Sie auf **Neuen Benutzer hinzufügen**, und wählen Sie dann den Benutzertyp aus, den Sie hinzufügen möchten.
   
    ![Benutzer](./media/egnyte-tutorial/ic787824.png "Benutzer")

4. Führen Sie im Abschnitt **New Power User** (Neuer Hauptbenutzer) die folgenden Schritte aus:
    
    ![Neuer Standardbenutzer](./media/egnyte-tutorial/ic787825.png "Neuer Standardbenutzer")   

    a. Geben Sie im Textfeld **E-Mail** die E-Mail-Adresse des Benutzers ein, z. B. **brittasimon\@contoso.com**.

    b. Geben Sie in das Textfeld **Benutzername** den Namen des Benutzers (**Brittasimon**) ein.

    c. Wählen Sie unter **Authentifizierungstyp** die Option **Einmaliges Anmelden** aus.
   
    d. Klicken Sie auf **Speichern**.
    
    >[!NOTE]
    >Der Besitzer des Azure Active Directory-Kontos erhält eine Benachrichtigungs-E-Mail.
    >

>[!NOTE]
>Sie können AAD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Egnyte-Benutzerkonten oder mithilfe der von Egnyte bereitgestellten APIs erstellen.
>

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Egnyte“ klicken, sollten Sie automatisch bei der Egnyte-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

