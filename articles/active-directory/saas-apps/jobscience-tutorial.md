---
title: 'Tutorial: Azure Active Directory-Integration mit Jobscience | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Jobscience konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 5a104dcd6ccf500c115359a1b72c67b85359a802
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002186"
---
# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>Tutorial: Azure Active Directory-Integration mit Jobscience

In diesem Tutorial erfahren Sie, wie Sie Jobscience in Azure Active Directory (Azure AD) integrieren.

Die Integration von Jobscience in Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf Jobscience hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Jobscience anzumelden (Single Sign-On, SSO; einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in Jobscience konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein Jobscience-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie über keine Azure AD-Testumgebung verfügen, können Sie hier eine einmonatige Testversion anfordern: [Kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von Jobscience aus dem Katalog
1. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-jobscience-from-the-gallery"></a>Hinzufügen von Jobscience aus dem Katalog
Zum Konfigurieren der Integration von Jobscience in Azure AD müssen Sie Jobscience über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um Jobscience über den Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Active Directory][1]

1. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![Der Screenshot zeigt das Azure-Portal mit „Unternehmensanwendungen“, ausgewählt unter „Verwalten“, mit ausgewähltem „Alle Anwendungen“.][2]
    
1. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Der Screenshot zeigt die ausgewählte Schaltfläche „Neue Anwendung“.][3]

1. Geben Sie im Suchfeld den Namen **Jobscience** ein.

    ![Der Screenshot zeigt „Aus Katalog hinzufügen“ mit eingegebenem „Jobscience“.](./media/jobscience-tutorial/tutorial_jobscience_search.png)

1. Wählen Sie im Ergebnisbereich **Jobscience** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Der Screenshot zeigt die Ergebnisse, in denen „Jobscience“ enthalten ist.](./media/jobscience-tutorial/tutorial_jobscience_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Jobscience mithilfe einer Testbenutzerin namens Britta Simon.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Jobscience als Pendant zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Jobscience muss eine Linkbeziehung eingerichtet werden.

Weisen Sie in Jobscience den Wert für **Benutzername** in Azure AD als Wert für **Benutzername** zu, um eine Linkbeziehung herzustellen.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Jobscience müssen die folgenden Schritte ausgeführt werden:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
1. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit der Testbenutzerin Britta Simon zu testen.
1. **[Erstellen eines Jobscience-Testbenutzers](#creating-a-jobscience-test-user)** , um ein Pendant von Britta Simon in Jobscience zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Testen der einmaligen Anmeldung](#testing-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens von Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden in Ihrer Jobscience-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Jobscience die folgenden Schritte aus:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Jobscience** auf **Einmaliges Anmelden**.

    ![Der Screenshot zeigt „Einmaliges Anmelden“, das im Azure-Portal unter „Verwalten“ ausgewählt ist.][4]

1. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Der Screenshot zeigt den ausgewählten Modus „SAML-basierte Anmeldung“.](./media/jobscience-tutorial/tutorial_jobscience_samlbase.png)

1. Führen Sie auf der Seite **Domäne und URLs für Jobscience** die folgenden Schritte aus:

    ![Der Screenshot zeigt die Anmelde-URL.](./media/jobscience-tutorial/tutorial_jobscience_url.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `http://<company name>.my.salesforce.com`.
    
    > [!NOTE] 
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Diesen Wert erhalten Sie vom [Supportteam für den Jobscience-Client](http://www.jobscience.com/support) oder aus dem von Ihnen erstellten SSO-Profil. Die Vorgehensweise zur Erstellung wird später im Tutorial erläutert. 
 
1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Zertifikat (Base64)** , und speichern Sie die Zertifikatdatei auf Ihrem Computer.

    ![Der Screenshot zeigt den Bereich „SAML-Signaturzertifikat“, in dem Sie ein Zertifikat herunterladen können.](./media/jobscience-tutorial/tutorial_jobscience_certificate.png) 

1. Klicken Sie auf die Schaltfläche **Save** .

    ![Der Screenshot zeigt die Schaltfläche „Speichern“.](./media/jobscience-tutorial/tutorial_general_400.png)

1. Klicken Sie im Abschnitt **Jobscience-Konfiguration** auf **Jobscience konfigurieren**, um das Fenster **Anmeldung konfigurieren** zu öffnen. Kopieren Sie die **Abmelde-URL, die SAML-Entitäts-ID und die URL für den SAML-SSO-Dienst** aus dem Abschnitt **Kurzübersicht**.

    ![Der Screenshot zeigt das Konfigurationsfenster „Jobscience“.](./media/jobscience-tutorial/tutorial_jobscience_configure.png) 

1. Melden Sie sich bei der Jobscience-Unternehmenswebsite als Administrator an.

1. Wechseln Sie zu **Setup**.
   
   ![Der Screenshot zeigt das Element „Setup“ für Ihr Unternehmen.](./media/jobscience-tutorial/IC784358.png "Einrichten")

1. Klicken Sie im linken Navigationsbereich im Abschnitt **Verwalten** auf **Domänenverwaltung**, um den entsprechenden Abschnitt zu erweitern, und klicken Sie anschließend auf **Meine Domäne**, um die Seite **Meine Domäne** zu öffnen. 
   
   ![Meine Domäne](./media/jobscience-tutorial/ic767825.png "Meine Domäne")

1. Um zu überprüfen, ob Ihre Domäne richtig eingerichtet wurde, stellen Sie sicher, dass sich die Domäne in **Schritt 4 – bereitgestellt für Benutzer** befindet, und überprüfen Sie die **Domäneneinstellungen**.

    ![Domäne für Benutzer bereitgestellt](./media/jobscience-tutorial/ic784377.png "Domäne für Benutzer bereitgestellt")

1. Klicken Sie auf der Jobscience-Unternehmenswebsite auf **Sicherheitskontrollen** und anschließend auf **Einstellungen für einmaliges Anmelden**.
    
    ![Der Screenshot zeigt „Einstellungen für einmaliges Anmelden“, das in „Sicherheitskontrollen“ ausgewählt wurde.](./media/jobscience-tutorial/ic784364.png "Einstellungen für einmaliges Anmelden")

1. Führen Sie im Abschnitt **Einstellungen für einmaliges Anmelden** die folgenden Schritte aus:
    
    ![SSO-Einstellungen](./media/jobscience-tutorial/ic781026.png "Einstellungen für einmaliges Anmelden")
    
    a. Wählen Sie **SAML aktiviert** aus.

    b. Klicken Sie auf **Neu**.

1. Führen Sie im Dialogfeld **Einstellungen für einmaliges Anmelden mit SAML – Bearbeiten** die folgenden Schritte aus:
    
    ![SAML-Einstellung für einmaliges Anmelden](./media/jobscience-tutorial/ic784365.png "SAML-Einstellung für einmaliges Anmelden")
    
    a. Geben Sie im Textfeld **Name** einen Namen für die Konfiguration ein.

    b. Fügen Sie in das Textfeld **Aussteller** den Wert der **SAML-Entitäts-ID** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Geben Sie `https://salesforce-jobscience.com` in das Textfeld **Benutzer-ID** ein.

    d. Klicken Sie auf **Durchsuchen** , um Ihr Azure AD-Zertifikat hochzuladen.

    e. Wählen Sie für **SAML Identity Type** (SAML-Identitätstyp) die Option **Assertion contains the Federation ID from the User object** (Assertion enthält die Verbund-ID aus dem Benutzerobjekt) aus.

    f. Wählen Sie für **SAML Identity Location** (Speicherort der SAML-Identität) die Option **Identity is in the NameIdentifier element of the Subject statement** (Identität ist im NameIdentifier-Element der Subject-Anweisung enthalten) aus.

    g. Fügen Sie in das Textfeld für die **Anmelde-URL des Identitätsanbieters** den Wert der **SAML-Dienst-URL für einmaliges Anmelden** ein, den Sie aus dem Azure-Portal kopiert haben.

    h. Fügen Sie in das Textfeld für die **Abmelde-URL des Identitätsanbieters** den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    i. Klicken Sie auf **Speichern**.

1. Klicken Sie im linken Navigationsbereich im Abschnitt **Verwalten** auf **Domänenverwaltung**, um den entsprechenden Abschnitt zu erweitern, und klicken Sie anschließend auf **Meine Domäne**, um die Seite **Meine Domäne** zu öffnen. 
    
    ![Meine Domäne](./media/jobscience-tutorial/ic767825.png "Meine Domäne")

1. Klicken Sie auf der Seite **Meine Domäne** im Abschnitt **Anmeldeseitenbranding** auf **Bearbeiten**.
    
    ![Der Screenshot zeigt den Abschnitt „Anmeldeseitenbranding“ mit der Schaltfläche „Bearbeiten“.](./media/jobscience-tutorial/ic767826.png "Anmeldeseitenbranding")

1. Im Abschnitt **Authentifizierungsdienst** der Seite **Anmeldeseitenbranding** wird der Name Ihrer **SAML-SSO-Einstellungen** angezeigt. Wählen Sie den Namen aus, und klicken Sie auf **Speichern**.
    
    ![Der Screenshot zeigt den Abschnitt „Anmeldeseitenbranding“ mit ausgewähltem „PPE“ und „Speichern“.](./media/jobscience-tutorial/ic784366.png "Anmeldeseitenbranding")

1. Klicken Sie zum Abrufen der Anmelde-URL für vom Dienstanbieter initiiertes einmaliges Anmelden im Menüabschnitt **Sicherheitskontrollen** auf **Einstellungen für einmaliges Anmelden**.

    ![Der Screenshot zeigt „Sicherheitskontrollen verwalten“ mit ausgewähltem „Einstellungen für einmaliges Anmelden“.](./media/jobscience-tutorial/ic784368.png "Einstellungen für einmaliges Anmelden")
    
    Klicken Sie auf das SSO-Profil, das Sie im obigen Schritt erstellt haben. Auf dieser Seite wird die SSO-URL für Ihr Unternehmen angezeigt (z. B. `https://companyname.my.salesforce.com?so=companyid`).    

> [!TIP]
> Während der Einrichtung der App können Sie im [Azure-Portal](https://portal.azure.com) nun eine Kurzfassung dieser Anweisungen lesen.  Nachdem Sie diese App aus dem Abschnitt **Active Directory > Unternehmensanwendungen** heruntergeladen haben, klicken Sie einfach auf die Registerkarte **Einmaliges Anmelden**, und rufen Sie die eingebettete Dokumentation über den Abschnitt **Konfiguration** um unteren Rand der Registerkarte auf. Weitere Informationen zur eingebetteten Dokumentation finden Sie hier: [Dokumentation zu eingebettetem Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

![Azure AD-Benutzer erstellen][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Portals** auf das Symbol für **Azure Active Directory**.

    ![Der Screenshot zeigt das Symbol „Azure AD“ im Azure-Portal.](./media/jobscience-tutorial/create_aaduser_01.png) 

1. Wechseln Sie zu **Benutzer und Gruppen**, und klicken Sie auf **Alle Benutzer**, um die Liste der Benutzer anzuzeigen.
    
    ![Der Screenshot zeigt „Benutzer und Gruppen“, das im Menü „Verwalten“ ausgewählt wurde. „Alle Benutzer“ ist ausgewählt.](./media/jobscience-tutorial/create_aaduser_02.png) 

1. Klicken Sie oben im Dialogfeld auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.
 
    ![Der Screenshot zeigt die Schaltfläche „Hinzufügen“ zum Öffnen des Dialogfelds „Benutzer“.](./media/jobscience-tutorial/create_aaduser_03.png) 

1. Führen Sie auf der Dialogfeldseite **Benutzer** die folgenden Schritte aus:
 
    ![Der Screenshot zeigt das Dialogfeld „Benutzer“. Dort können Sie in diesem Schritt die Werte eingeben.](./media/jobscience-tutorial/create_aaduser_04.png) 

    a. Geben Sie in das Textfeld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie in das Textfeld **Benutzername** die **E-Mail-Adresse** von Britta Simon ein.

    c. Wählen Sie **Kennwort anzeigen** aus, und notieren Sie sich den Wert des **Kennworts**.

    d. Klicken Sie auf **Erstellen**.
 
### <a name="creating-a-jobscience-test-user"></a>Erstellen eines Jobscience-Testbenutzers

Damit sich Azure AD-Benutzer bei Jobscience anmelden können, müssen sie in Jobscience bereitgestellt werden. Im Fall von Jobscience ist die Bereitstellung eine manuelle Aufgabe.

>[!NOTE]
>Sie können Azure Active Directory-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Jobscience-Benutzerkonten oder mithilfe der von Jobscience bereitgestellten APIs erstellen.
>  
        
**Um die Benutzerbereitstellung zu konfigurieren, führen Sie die folgenden Schritte durch:**

1. Melden Sie sich bei der **Jobscience** -Unternehmenswebsite als Administrator an.

1. Wechseln Sie zu „Setup“.
   
   ![Der Screenshot zeigt das Element „Setup“.](./media/jobscience-tutorial/ic784358.png "Einrichten")
1. Navigieren Sie zu **Benutzer verwalten \> Benutzer**.
   
   ![Benutzer](./media/jobscience-tutorial/ic784369.png "Benutzer")
1. Klicken Sie auf **Neuer Benutzer**.
   
   ![Alle Benutzer](./media/jobscience-tutorial/ic784370.png "Alle Benutzer")
1. Führen Sie im Dialogfeld **Benutzer bearbeiten** die folgenden Schritte aus:
   
   ![Benutzer bearbeiten](./media/jobscience-tutorial/ic784371.png "Benutzer bearbeiten")
   
   a. Geben Sie im Textfeld **Vorname** den Vornamen des Benutzers ein, z.B. Britta.
   
   b. Geben Sie im Textfeld **Nachname** den Nachnamen des Benutzers ein, z.B. Simon.
   
   c. Geben Sie im Textfeld **Alias** einen Alias des Benutzers ein, z.B. brittas.

   d. Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse des Benutzers, z.B. Brittasimon@contoso.com, ein.

   e. Geben Sie im Textfeld **Benutzername** den Benutzernamen des Benutzers ein, z.B. Brittasimon@contoso.com.

   f. Geben Sie im Textfeld **Nick Name** (Spitzname) den Spitznamen des Benutzers ein, z.B. Simon.

   g. Klicken Sie auf **Speichern**.

    
> [!NOTE]
> Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail und folgt einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

### <a name="assigning-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Jobscience gewähren.

![Der Screenshot zeigt den Anzeigenamen eines Kontos.][200] 

**Um Britta Simon Jobscience zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Der Screenshot zeigt „Unternehmensanwendungen“ im Menü „Azure-Portal“, mit ausgewähltem „Alle Anwendungen“.][201] 

1. Wählen Sie in der Anwendungsliste die Option **Jobscience** aus.

    ![Der Screenshot zeigt die ausgewählte Option „Jobscience“.](./media/jobscience-tutorial/tutorial_jobscience_app.png) 

1. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Der Screenshot zeigt „Benutzer und Gruppen“, ausgewählt im Menü „Azure-Portal“.][202] 

1. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Der Screenshot zeigt die Schaltfläche „Hinzufügen“, über die Zuweisungen hinzugefügt werden.][203]

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

1. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    
### <a name="testing-single-sign-on"></a>Testen der einmaligen Anmeldung

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Jobscience“ klicken, sollten Sie automatisch bei Ihrer Jobscience-Anwendung angemeldet werden.
Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/jobscience-tutorial/tutorial_general_01.png
[2]: ./media/jobscience-tutorial/tutorial_general_02.png
[3]: ./media/jobscience-tutorial/tutorial_general_03.png
[4]: ./media/jobscience-tutorial/tutorial_general_04.png

[100]: ./media/jobscience-tutorial/tutorial_general_100.png

[200]: ./media/jobscience-tutorial/tutorial_general_200.png
[201]: ./media/jobscience-tutorial/tutorial_general_201.png
[202]: ./media/jobscience-tutorial/tutorial_general_202.png
[203]: ./media/jobscience-tutorial/tutorial_general_203.png