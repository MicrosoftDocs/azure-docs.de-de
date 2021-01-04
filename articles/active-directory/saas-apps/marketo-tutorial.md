---
title: 'Tutorial: Azure Active Directory-Integration mit Marketo | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Marketo konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2020
ms.author: jeedes
ms.openlocfilehash: bd62855aef59439ed476a13770f79ce6d3cf68f8
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96180794"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Tutorial: Azure Active Directory-Integration mit Marketo

In diesem Tutorial erfahren Sie, wie Sie Marketo in Azure Active Directory (Azure AD) integrieren.
Die Integration von Marketo in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Marketo haben soll.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei Marketo anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Marketo konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* SSO-fähiges Marketo-Abonnement

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Marketo unterstützt **IDP**-initiiertes einmaliges Anmelden.

## <a name="adding-marketo-from-the-gallery"></a>Hinzufügen von Marketo aus dem Katalog

Zum Konfigurieren der Integration von Marketo in Azure AD müssen Sie Marketo aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** im Suchfeld den Begriff **Marketo** ein.
1. Wählen Sie im Ergebnisbereich den Eintrag **Marketo** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurieren und Testen des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Marketo mithilfe eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Marketo eingerichtet werden.

Führen Sie zum Konfigurieren des einmaligen Anmeldens in Azure AD mit Marketo die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer Britta Simon zu testen
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon das einmalige Anmelden von Azure AD zu ermöglichen
2. **[Konfigurieren des einmaligen Anmeldens von Marketo](#configure-marketo-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines Marketo-Testbenutzers](#create-marketo-test-user)** , um in Marketo eine Entsprechung von Britta Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
3. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Marketo** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://saml.marketo.com/sp`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    c. Geben Sie im Textfeld **Relayzustand** eine URL nach folgendem Muster ein: `https://<munchkinid>.marketo.com/`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit den tatsächlichen Werten für „Bezeichner“, „Antwort-URL“ und „Relayzustand“. Wenden Sie sich an das [Clientsupportteam von Marketo](https://investors.marketo.com/contactus.cfm), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Marketo einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Marketo gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Marketo** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-marketo-sso"></a>Konfigurieren des einmaligen Anmeldens von Marketo

1. Um die Munchkin-ID Ihrer Anwendung zu erhalten, melden Sie sich mit Ihren Administratoranmeldeinformationen bei Marketo an und führen Sie folgende Aktionen aus:
   
    a. Melden Sie sich mit Ihren Administratoranmeldeinformationen bei der Marketo-App an.
   
    b. Klicken Sie auf die Schaltfläche **Admin** im oberen Navigationsbereich.
   
    ![Konfigurieren von einmaligem Anmelden 1](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navigieren Sie zum Menü „Integration“, und klicken Sie auf den **Munchkin-Link**.
   
    ![Konfigurieren von einmaligem Anmelden 2](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Kopieren Sie die auf dem Bildschirm angezeigte Munchkin-ID, und geben Sie Ihre Antwort-URL im Azure AD-Konfigurations-Assistenten ein.
   
    ![Konfigurieren von einmaligem Anmelden 3](./media/marketo-tutorial/tutorial_marketo_12.png) 

2. Führen Sie die unten beschriebenen Schritte aus, um SSO in der Anwendung zu konfigurieren:
   
    a. Melden Sie sich mit Ihren Administratoranmeldeinformationen bei der Marketo-App an.
   
    b. Klicken Sie auf die Schaltfläche **Admin** im oberen Navigationsbereich.
   
    ![Konfigurieren von einmaligem Anmelden 4](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navigieren Sie zum Menü „Integration“, und klicken Sie auf **Einmaliges Anmelden**.
   
    ![Konfigurieren von einmaligem Anmelden 5](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. Klicken zum Aktivieren der SAML-Einstellungen auf die Schaltfläche **Bearbeiten**.
   
    ![Einmaliges Anmelden konfigurieren6](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Aktivieren** Sie die Einstellungen für einmaliges Anmelden.
   
    f. Fügen Sie den **Azure AD-Bezeichner** in das Textfeld für die **Aussteller-ID** ein.
   
    g. Geben Sie die URL als **in das Textfeld** Entitäts-ID`http://saml.marketo.com/sp` ein.
   
    h. Wählen Sie den Speicherort der Benutzer-ID als **Namensbezeichnerelement** aus.
   
    ![Konfigurieren von einmaligem Anmelden 7](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Wenn Ihre Benutzer-ID kein UPN-Wert ist, ändern Sie den Wert in der Registerkarte „Attribut“.
   
    i. Laden Sie das Zertifikat hoch, das Sie aus dem Azure AD-Konfigurations-Assistenten heruntergeladen haben. **Speichern** Sie die Einstellungen.
   
    j. Bearbeiten Sie die Einstellungen für Seitenumleitungen.
   
    k. Fügen Sie die **Anmelde-URL** in das Textfeld für die **Anmelde-URL** ein.
   
    l. Fügen Sie die **Abmelde-URL** in das Textfeld für die **Abmelde-URL** ein.
   
    m. Kopieren Sie in der **Fehler-URL** die **URL Ihrer Marketo-Instanz**, und klicken Sie auf die Schaltfläche **Speichern**, um die Einstellungen zu speichern.
   
    ![Konfigurieren von einmaligem Anmelden 8](./media/marketo-tutorial/tutorial_marketo_10.png)

3. Um SSO für Benutzer zu aktivieren, führen Sie die folgenden Aktionen aus:
   
    a. Melden Sie sich mit Ihren Administratoranmeldeinformationen bei der Marketo-App an.
   
    b. Klicken Sie auf die Schaltfläche **Admin** im oberen Navigationsbereich.
   
    ![Konfigurieren von einmaligem Anmelden 9](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Navigieren Sie zum Menü **Sicherheit**, und klicken Sie auf **Anmeldeeinstellungen**.
   
    ![Konfigurieren von einmaligem Anmelden 10](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Aktivieren Sie die Option **SSO erfordern**, und **speichern** Sie die Einstellungen.
   
    ![Konfigurieren von einmaligem Anmelden 11](./media/marketo-tutorial/tutorial_marketo_14.png)


### <a name="create-marketo-test-user"></a>Erstellen eines Marketo-Testbenutzers

In diesem Abschnitt erstellen Sie in Marketo einen Benutzer namens Britta Simon. Befolgen Sie diese Schritte, um einen Benutzer in der Marketo-Plattform zu erstellen.

1. Melden Sie sich mit Ihren Administratoranmeldeinformationen bei der Marketo-App an.

2. Klicken Sie auf die Schaltfläche **Admin** im oberen Navigationsbereich.
   
    ![Testbenutzer 1](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Navigieren Sie zum Menü **Sicherheit**, und klicken Sie auf **Benutzer und Rollen**
   
    ![Testbenutzer 2](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Klicken Sie auf der Registerkarte „Benutzer“ auf den Link **Neuen Benutzer einladen**.
   
    ![Testbenutzer 3](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. Geben Sie die folgenden Informationen im Assistenten zum Einladen neuer Benutzer ein.
   
    a. Geben Sie die **E-Mail** -Adresse des Benutzers in das Textfeld ein.
   
    ![Testbenutzer 4](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Geben Sie den **Vornamen** in das Textfeld ein.
   
    c. Geben Sie den **Nachnamen** in das Textfeld ein.
   
    d. Klicken Sie auf **Weiter**.

6. Wählen Sie auf der Registerkarte **Berechtigungen** die Option **userRoles** aus, und klicken Sie auf **Weiter**.
   
    ![Testbenutzer 5](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Klicken Sie auf die Schaltfläche **Senden**, um die Benutzereinladung zu senden.
   
    ![Testbenutzer 6](./media/marketo-tutorial/tutorial_marketo_18.png)

8. Der Benutzer erhält die E-Mail-Benachrichtigung und muss auf den Link klicken sowie das Kennwort ändern, um das Konto zu aktivieren. 

### <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

1. Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Marketo-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

1. Sie können den Microsoft-Zugriffsbereich verwenden. Wenn Sie im Zugriffsbereich auf die Kachel „Marketo“ klicken, sollten Sie automatisch bei der Marketo-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Marketo können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.