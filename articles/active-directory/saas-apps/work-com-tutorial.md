---
title: 'Tutorial: Azure Active Directory-Integration mit Work.com | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Work.com konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: a77b6472ea91a6ba4b6e31d1b7739aae236a1783
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636152"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Tutorial: Azure Active Directory-Integration mit Work.com

In diesem Tutorial erfahren Sie, wie Sie Work.com in Azure Active Directory (Azure AD) integrieren.
Die Integration von Work.com in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Work.com hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Work.com anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Work.com konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Work.com-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Work.com unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-workcom-from-the-gallery"></a>Hinzufügen von Work.com aus dem Katalog

Zum Konfigurieren der Integration von Work.com in Azure AD müssen Sie Work.com aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Work.com aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Work.com** ein, wählen Sie im Ergebnisbereich **Work.com** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

    ![Work.com in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Work.com basierend auf einem Testbenutzer mit dem Namen **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Work.com eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Work.com müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Work.com](#configure-workcom-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Work.com-Testbenutzers](#create-workcom-test-user)** , um eine Entsprechung von Britta Simon in Work.com zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

>[!NOTE]
>Um einmaliges Anmelden zu konfigurieren, müssen Sie einen benutzerdefinierten Domänennamen von Work.com einrichten. Sie müssen mindestens einen Domänennamen definieren, testen und dann in Ihrer gesamten Organisation bereitstellen.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Work.com die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Work.com** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Work.com](common/sp-signonurl.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `http://<companyname>.my.salesforce.com`

    > [!NOTE]
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Den Wert erhalten Sie vom [Supportteam für den Work.com-Client](https://help.salesforce.com/articleView?id=000159855&type=3). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Work.com einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-workcom-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Work.com

1. Melden Sie sich bei Ihrem Work.com-Mandanten als Administrator an.

2. Wechseln Sie zu **Setup**.
   
    ![Screenshot der ausgewählten Option „Setup“ im Benutzermenü](./media/work-com-tutorial/ic794108.png "Einrichten")

3. Klicken Sie im linken Navigationsbereich im Abschnitt **Verwalten** auf **Domänenverwaltung** , um den entsprechenden Abschnitt zu erweitern, und klicken Sie anschließend auf **Meine Domäne** , um die Seite **Meine Domäne** zu öffnen. 
   
    ![Screenshot der ausgewählten Option „Meine Domäne“ unter „Domänenverwaltung“ im Bereich „Verwalten“](./media/work-com-tutorial/ic767825.png "Meine Domäne")

4. Um zu überprüfen, ob Ihre Domäne richtig eingerichtet wurde, stellen Sie sicher, dass sich die Domäne in **Schritt 4 – bereitgestellt für Benutzer** befindet, und überprüfen Sie die **Domäneneinstellungen**.
   
    ![Domäne für Benutzer bereitgestellt](./media/work-com-tutorial/ic784377.png "Domäne für Benutzer bereitgestellt")

5. Melden Sie sich bei Ihrem Work.com-Mandanten an.

6. Wechseln Sie zu **Setup**.
    
    ![Screenshot der ausgewählten Option „Setup“ im Benutzermenü](./media/work-com-tutorial/ic794108.png "Einrichten")

7. Erweitern Sie das Menü **Sicherheitskontrollen** , und klicken Sie anschließend auf **Single Sign-On Settings** (Einstellungen für einmaliges Anmelden).
    
    ![SSO-Einstellungen](./media/work-com-tutorial/ic794113.png "Einstellungen für einmaliges Anmelden")

8. Führen Sie auf der Dialogfeldseite **Einstellungen für einmaliges Anmelden** die folgenden Schritte aus:
    
    ![SAML aktiviert](./media/work-com-tutorial/ic781026.png "SAML aktiviert")
    
    a. Wählen Sie **SAML aktiviert** aus.
    
    b. Klicken Sie auf **Neu**.

9. Führen Sie im Abschnitt **Einstellungen für einmalige Anmelden für SAML** die folgenden Schritte aus:
    
    ![SAML-Einstellung für einmaliges Anmelden](./media/work-com-tutorial/ic794114.png "SAML-Einstellung für einmaliges Anmelden")
    
    a. Geben Sie im Textfeld **Name** einen Namen für die Konfiguration ein.  
       
    > [!NOTE]
    > Bei Eingabe eines Werts für **Name** wird automatisch das Textfeld **API-Name** gefüllt.
    
    b. Fügen Sie in das Textfeld **Issuer** (Aussteller) den Wert für **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.
    
    c. Klicken Sie auf **Browse** (Durchsuchen), um das aus dem Azure-Portal heruntergeladene Zertifikat hochzuladen.
    
    d. Geben Sie `https://salesforce-work.com` in das Textfeld **Entity Id** (Entitäts-ID) ein.
    
    e. Wählen Sie für **SAML Identity Type** (SAML-Identitätstyp) die Option **Assertion contains the Federation ID from the User object** (Assertion enthält die Verbund-ID aus dem Benutzerobjekt) aus.
    
    f. Wählen Sie für **SAML Identity Location** (Speicherort der SAML-Identität) die Option **Identity is in the NameIdentifier element of the Subject statement** (Identität ist im NameIdentifier-Element der Subject-Anweisung enthalten) aus.
    
    g. Fügen Sie in das Textfeld **Identity Provider Login URL** (Anmelde-URL des Identitätsanbieters) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    h. Fügen Sie in das Textfeld **Identity Provider Logout URL** (Abmelde-URL des Identitätsanbieters) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.
    
    i. Wählen Sie für **Service Provider Initiated Request Binding** (Vom Dienstanbieter initiierte Anforderungsbindung) die Option **HTTP Post** aus.
    
    j. Klicken Sie auf **Speichern**.

10. Klicken Sie im linken Navigationsbereich des klassischen Work.com-Portals auf **Domänenverwaltung** , um den entsprechenden Abschnitt zu erweitern, und klicken Sie anschließend auf **Meine Domäne** , um die Seite **Meine Domäne** zu öffnen. 
    
    ![Screenshot der ausgewählten Option „Meine Domäne“ unter „Domänenverwaltung“](./media/work-com-tutorial/ic794115.png "Meine Domäne")

11. Klicken Sie auf der Seite **Meine Domäne** im Abschnitt **Anmeldeseitenbranding** auf **Bearbeiten**.
    
    ![Screenshot des Abschnitts „Anmeldeseitenbranding“ zum Auswählen der Bearbeitungsoption](./media/work-com-tutorial/ic767826.png "Anmeldeseitenbranding")

12. Im Abschnitt **Authentifizierungsdienst** der Seite **Anmeldeseitenbranding** wird der Name Ihrer **SAML-SSO-Einstellungen** angezeigt. Wählen Sie den Namen aus, und klicken Sie auf **Speichern**.
    
    ![Screenshot der Seite „Anmeldeseitenbranding“ zum Auswählen des Einstellungsnamens (hier PPE)](./media/work-com-tutorial/ic784366.png "Anmeldeseitenbranding")

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen `brittasimon@yourcompanydomain.extension` ein. Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Work.com gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Work.com** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Work.com** aus.

    ![Work.com-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-workcom-test-user"></a>Erstellen eines Work.com-Testbenutzers

Damit sich Azure Active Directory-Benutzer anmelden können, müssen sie in Work.com bereitgestellt werden. Im Fall von Work.com ist die Bereitstellung eine manuelle Aufgabe.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>So konfigurieren Sie die Benutzerbereitstellung

1. Melden Sie sich bei der Work.com-Unternehmenswebsite als Administrator an.

2. Wechseln Sie zu **Setup**.
   
    ![Screenshot der ausgewählten Option „Setup“ im Benutzermenü](./media/work-com-tutorial/ic794108.png "Einrichten")

3. Navigieren Sie zu **Benutzer verwalten \> Benutzer**.
   
    ![Verwalten von Benutzern](./media/work-com-tutorial/IC784369.png "Benutzer verwalten")

4. Klicken Sie auf **Neuer Benutzer**.
   
    ![Alle Benutzer](./media/work-com-tutorial/IC794117.png "Alle Benutzer")

5. Führen Sie im Abschnitt „User Edit“ (Benutzer bearbeiten) die folgenden Schritte aus, und fügen Sie die Attribute eines gültigen Azure AD-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein:
   
    ![Benutzer bearbeiten](./media/work-com-tutorial/ic794118.png "Benutzer bearbeiten")
   
    a. Geben Sie im Textfeld **First Name** den **Vornamen** des Benutzers ein, z.B. **Britta**.
    
    b. Geben Sie im Textfeld **Last Name** den **Nachnamen** des Benutzers ein, z.B. **Simon**.
    
    c. Geben Sie im Textfeld **Alias** den **Namen** des Benutzers ein, z.B. **BrittaS**.
    
    d. Geben Sie im Textfeld **Email** (E-Mail) die **E-Mail-Adresse** des Benutzers ein, z. B. Brittasimon@contoso.com.
    
    e. Geben Sie im Textfeld **Benutzername** den Benutzernamen des Benutzers ein, z.B. Brittasimon@contoso.com.
    
    f. Geben Sie im Textfeld **Nick Name** den **Spitznamen** der Benutzerin **Simon** ein.
    
    g. Wählen Sie **Rolle** , **Benutzerlizenz** und **Profil** aus.
    
    h. Klicken Sie auf **Speichern**.  
      
    > [!NOTE]
    > Der Besitzer des Azure AD-Kontos erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird.
    > 

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Work.com“ klicken, sollten Sie automatisch bei der Work.com-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)