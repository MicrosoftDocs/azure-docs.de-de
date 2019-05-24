---
title: 'Tutorial: Azure Active Directory-Integration mit Jitbit Helpdesk | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Jitbit Helpdesk konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 851b28d10bdf0b2df67e1c0782a683e790b711bc
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65898005"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Tutorial: Azure Active Directory-Integration mit Jitbit Helpdesk

In diesem Tutorial erfahren Sie, wie Sie Jitbit Helpdesk in Azure Active Directory (Azure AD) integrieren.
Die Integration von Jitbit Helpdesk in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Jitbit Helpdesk hat.
* Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Jitbit Helpdesk anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Jitbit Helpdesk konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Jitbit Helpdesk-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Jitbit Helpdesk unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Hinzufügen von Jitbit Helpdesk aus dem Katalog

Zum Konfigurieren der Integration von Jitbit Helpdesk in Azure AD müssen Sie Jitbit Helpdesk aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um Jitbit Helpdesk über den Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie in das Suchfeld **Jitbit Helpdesk** ein, wählen Sie im Ergebnisbereich **Jitbit Helpdesk** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Jitbit Helpdesk in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Jitbit Helpdesk mithilfe eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Jitbit Helpdesk eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD mit Jitbit Helpdesk müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Jitbit Helpdesk](#configure-jitbit-helpdesk-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Jitbit Helpdesk-Testbenutzers](#create-jitbit-helpdesk-test-user)**, um eine Entsprechung von Britta Simon in Jitbit Helpdesk zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Jitbit Helpdesk die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Jitbit Helpdesk** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Jitbit Helpdesk](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Wenden Sie sich an das [Kundensupportteam von Jitbit Helpdesk](https://www.jitbit.com/support/), um diesen Wert zu erhalten.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** wie folgt eine URL ein: `https://www.jitbit.com/web-helpdesk/`.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Jitbit Helpdesk einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-jitbit-helpdesk-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Jitbit Helpdesk

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Jitbit Helpdesk-Unternehmenswebsite als Administrator an.

1. Klicken Sie oben auf der Symbolleiste auf **Verwaltung**.

    ![Verwaltung](./media/jitbit-helpdesk-tutorial/ic777681.png "Verwaltung")

1. Klicken Sie auf **Allgemeine Einstellungen**.

    ![Benutzer, Unternehmen und Berechtigungen](./media/jitbit-helpdesk-tutorial/ic777680.png "Benutzer, Unternehmen und Berechtigungen")

1. Führen Sie im Konfigurationsabschnitt **Authentifizierungseinstellungen** die folgenden Schritte aus:

    ![Authentifizierungseinstellungen](./media/jitbit-helpdesk-tutorial/ic777683.png "Authentifizierungseinstellungen")

    a. Wählen für die Anmeldung per SSO die Option**Einmaliges Anmelden mit SAML 2.0 aktivieren** mit **OneLogin** aus.

    b. Fügen Sie in das Textfeld **EndPoint URL** (Endpunkt-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Öffnen Sie das **Base64**-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **X.509-Zertifikat** ein.

    d. Klicken Sie auf **Änderungen speichern**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** **brittasimon@yourcompanydomain.extension** ein.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Jitbit Helpdesk gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Jitbit Helpdesk** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Jitbit Helpdesk** aus.

    ![Jitbit Helpdesk-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-jitbit-helpdesk-test-user"></a>Erstellen eines Jitbit Helpdesk-Testbenutzers

Damit sich Azure AD-Benutzer bei Jitbit Helpdesk anmelden können, müssen sie in Jitbit Helpdesk bereitgestellt werden. Im Fall von Jitbit Helpdesk ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrem **Jitbit Helpdesk**-Mandanten an.

1. Klicken Sie oben im Menü auf **Verwaltung**.

    ![Verwaltung](./media/jitbit-helpdesk-tutorial/ic777681.png "Verwaltung")

1. Klicken Sie auf **Benutzer, Unternehmen und Berechtigungen**.

    ![Benutzer, Unternehmen und Berechtigungen](./media/jitbit-helpdesk-tutorial/ic777682.png "Benutzer, Unternehmen und Berechtigungen")

1. Klicken Sie auf **Benutzer hinzufügen**.

    ![Benutzer hinzufügen](./media/jitbit-helpdesk-tutorial/ic777685.png "Benutzer hinzufügen")

1. Geben Sie im Abschnitt „Erstellen“ wie folgt die Daten des Azure AD-Kontos ein, das Sie bereitstellen möchten:

    ![Erstellen](./media/jitbit-helpdesk-tutorial/ic777686.png "Erstellen")

   a. Geben Sie im Textfeld **Username** (Benutzername) den Namen des Benutzers ein, z. B. **BrittaSimon**.

   b. Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse des Benutzers wie folgt ein: **BrittaSimon@contoso.com**.

   c. Geben Sie im Textfeld **Vorname** den Vornamen des Benutzers wie folgt ein: **Britta**.

   d. Geben Sie im Textfeld **Nachname** den Nachnamen des Benutzers wie folgt ein: **Simon**.

   e. Klicken Sie auf **Create**.

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Jitbit Helpdesk-Benutzerkonten oder mithilfe der von Jitbit Helpdesk bereitgestellten APIs erstellen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Jitbit Helpdesk“ klicken, sollten Sie automatisch bei der Jitbit Helpdesk-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
