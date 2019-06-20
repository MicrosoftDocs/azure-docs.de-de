---
title: 'Tutorial: Azure Active Directory-Integration mit RunMyProcess | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie das einmalige Anmelden zwischen Azure Active Directory und RunMyProcess konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfef1371b7ac61712c0f70efd48c0e791c4c729d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60518270"
---
# <a name="tutorial-azure-active-directory-integration-with-runmyprocess"></a>Tutorial: Azure Active Directory-Integration mit RunMyProcess

In diesem Tutorial erfahren Sie, wie Sie RunMyProcess in Azure Active Directory (Azure AD) integrieren.

Die Integration von RunMyProcess in Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf RunMyProcess hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei RunMyProcess anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit RunMyProcess konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein RunMyProcess-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie hier eine einmonatige Testversion anfordern: [Testversion](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von RunMyProcess aus dem Katalog
1. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-runmyprocess-from-the-gallery"></a>Hinzufügen von RunMyProcess aus dem Katalog
Zum Konfigurieren der Integration von RunMyProcess in Azure AD müssen Sie RunMyProcess aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um RunMyProcess aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Active Directory][1]

1. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![ANWENDUNGEN][2]
    
1. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![ANWENDUNGEN][3]

1. Geben Sie im Suchfeld als Suchbegriff **RunMyProcess** ein.

    ![Erstellen eines Azure AD-Testbenutzers](./media/runmyprocess-tutorial/tutorial_runmyprocess_search.png)

1. Wählen Sie im Ergebnisbereich **RunMyProcess** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Erstellen eines Azure AD-Testbenutzers](./media/runmyprocess-tutorial/tutorial_runmyprocess_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit RunMyProcess mithilfe einer Testbenutzerin namens Britta Simon.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in RunMyProcess als Pendant für einen Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in RunMyProcess muss eine Linkbeziehung eingerichtet werden.

Weisen Sie in RunMyProcess den Wert für **Benutzername** in Azure AD als Wert für **Benutzername** zu, um eine Linkbeziehung herzustellen.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei RunMyProcess müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configuring-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
1. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit der Testbenutzerin Britta Simon zu testen.
1. **[Erstellen eines RunMyProcess-Testbenutzers](#creating-a-runmyprocess-test-user)** , um ein Pendant von Britta Simon in RunMyProcess zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Testing Single Sign-On](#testing-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens von Azure AD

In diesem Abschnitt ermöglichen Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden in Ihrer RunMyProcess-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in RunMyProcess die folgenden Schritte aus:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **RunMyProcess** auf **Einmaliges Anmelden**.

    ![Configure single sign-on][4]

1. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Configure single sign-on](./media/runmyprocess-tutorial/tutorial_runmyprocess_samlbase.png)

1. Führen Sie auf der Seite **Domäne und URLs für RunMyProcess** die folgenden Schritte aus:

    ![Configure single sign-on](./media/runmyprocess-tutorial/tutorial_runmyprocess_url.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://live.runmyprocess.com/live/<tenant id>`.

    > [!NOTE] 
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Wenden Sie sich an das [Supportteam für den RunMyProcess-Client](mailto:support@runmyprocess.com), um den Wert zu erhalten. 

1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Zertifikat (Base64)** , und speichern Sie die Zertifikatdatei auf Ihrem Computer.

    ![Configure single sign-on](./media/runmyprocess-tutorial/tutorial_runmyprocess_certificate.png) 

1. Klicken Sie auf die Schaltfläche **Save** .

    ![Configure single sign-on](./media/runmyprocess-tutorial/tutorial_general_400.png)

1. Klicken Sie im Abschnitt **RunMyProcess-Konfiguration** auf **RunMyProcess konfigurieren**, um das Fenster **Anmeldung konfigurieren** zu öffnen. Kopieren Sie die **Abmelde-URL und die URL für den SAML-SSO-Dienst** aus dem Abschnitt **Kurzübersicht**.

    ![Configure single sign-on](./media/runmyprocess-tutorial/tutorial_runmyprocess_configure.png) 

1. Melden Sie sich in einem anderen Webbrowserfenster bei Ihrem RunMyProcess-Mandanten als Administrator an.

1. Klicken Sie links im Navigationsbereich auf **Konto**, und wählen Sie **Konfiguration**.
   
    ![Einmaliges Anmelden auf App-Seite konfigurieren](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. Navigieren Sie zum Abschnitt **Authentifizierungsmethode**, und führen Sie die folgenden Schritte aus:
   
    ![Einmaliges Anmelden auf App-Seite konfigurieren](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Wählen unter **Methode** die Option **SSO mit Samlv2** aus. 

    b. Fügen Sie in das Textfeld **SSO redirect** (SSO-Umleitung) den Wert der **SAML-Dienst-URL für einmaliges Anmelden** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Textfeld **Logout redirect** (Abmeldungsumleitung) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Geben Sie im Textfeld **Name Id Format** (Format der Namens-ID) den Wert von **Namensbezeichnerformat** als **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress** ein.

    e. Kopieren Sie den Inhalt der heruntergeladenen Zertifikatdatei, und fügen Sie ihn in das Textfeld **Zertifikat** ein. 
 
    f. Klicken Sie auf das Symbol **Speichern**.

> [!TIP]
> Während der Einrichtung der App können Sie im [Azure-Portal](https://portal.azure.com) nun eine Kurzfassung dieser Anweisungen lesen.  Nachdem Sie diese App aus dem Abschnitt **Active Directory > Unternehmensanwendungen** heruntergeladen haben, klicken Sie einfach auf die Registerkarte **Einmaliges Anmelden**, und rufen Sie die eingebettete Dokumentation über den Abschnitt **Konfiguration** um unteren Rand der Registerkarte auf. Weitere Informationen zur eingebetteten Dokumentation finden Sie hier: [Dokumentation zu eingebettetem Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

![Azure AD-Benutzer erstellen][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Portals** auf das Symbol für **Azure Active Directory**.

    ![Erstellen eines Azure AD-Testbenutzers](./media/runmyprocess-tutorial/create_aaduser_01.png) 

1. Wechseln Sie zu **Benutzer und Gruppen**, und klicken Sie auf **Alle Benutzer**, um die Liste der Benutzer anzuzeigen.
    
    ![Erstellen eines Azure AD-Testbenutzers](./media/runmyprocess-tutorial/create_aaduser_02.png) 

1. Klicken Sie oben im Dialogfeld auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/runmyprocess-tutorial/create_aaduser_03.png) 

1. Führen Sie auf der Dialogfeldseite **Benutzer** die folgenden Schritte aus:
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/runmyprocess-tutorial/create_aaduser_04.png) 

    a. Geben Sie in das Textfeld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie in das Textfeld **Benutzername** die **E-Mail-Adresse** von Britta Simon ein.

    c. Wählen Sie **Kennwort anzeigen** aus, und notieren Sie sich den Wert des **Kennworts**.

    d. Klicken Sie auf **Create**.
 
### <a name="creating-a-runmyprocess-test-user"></a>Erstellen eines RunMyProcess-Testbenutzers

Damit sich Azure AD-Benutzer bei RunMyProcess anmelden können, müssen sie in RunMyProcess bereitgestellt werden. Im Fall von RunMyProcess ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei der RunMyProcess-Unternehmenswebsite als Administrator an.

1. Klicken Sie auf **Konto**, und wählen Sie links im Navigationsbereich die Option **Benutzer**. Klicken Sie anschließend auf **Neuer Benutzer**.
   
    ![Neuer Benutzer](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "Neuer Benutzer")

1. Führen Sie im Abschnitt **Benutzereinstellungen** die folgenden Schritte aus:
   
    ![Profil](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profil") 
  
    a. Geben Sie **Name** und **E-Mail-Adresse** eines gültigen Azure AD-Benutzerkontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein. 

    b. Wählen Sie eine **IDE-Sprache**, eine **Sprache** und ein **Profil** aus. 

    c. Wählen Sie **Kontoerstellungs-E-Mail an mich senden**aus. 

    d. Klicken Sie auf **Speichern**.
   
    >[!NOTE]
    >Sie können Azure Active Directory-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von RunMyProcess-Benutzerkonten oder mithilfe der von RunMyProcess bereitgestellten APIs erstellen. 
    > 

### <a name="assigning-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf RunMyProcess gewähren.

![Benutzer zuweisen][200] 

**Um Britta Simon RunMyProcess zuzuweisen, führen Sie die folgenden Schritte aus:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

1. Wählen Sie in der Anwendungsliste den Eintrag **RunMyProcess** aus.

    ![Configure single sign-on](./media/runmyprocess-tutorial/tutorial_runmyprocess_app.png) 

1. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Benutzer zuweisen][202] 

1. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Benutzer zuweisen][203]

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

1. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    
### <a name="testing-single-sign-on"></a>Testen der einmaligen Anmeldung

Das Ziel dieses Abschnitts ist das Testen Ihrer Azure AD-Konfiguration für einmaliges Anmelden (SSO) über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „RunMyProcess“ klicken, sollten Sie automatisch bei Ihrer RunMyProcess-Anwendung angemeldet werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/runmyprocess-tutorial/tutorial_general_01.png
[2]: ./media/runmyprocess-tutorial/tutorial_general_02.png
[3]: ./media/runmyprocess-tutorial/tutorial_general_03.png
[4]: ./media/runmyprocess-tutorial/tutorial_general_04.png

[100]: ./media/runmyprocess-tutorial/tutorial_general_100.png

[200]: ./media/runmyprocess-tutorial/tutorial_general_200.png
[201]: ./media/runmyprocess-tutorial/tutorial_general_201.png
[202]: ./media/runmyprocess-tutorial/tutorial_general_202.png
[203]: ./media/runmyprocess-tutorial/tutorial_general_203.png

