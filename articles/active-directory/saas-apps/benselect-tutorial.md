---
title: 'Tutorial: Azure Active Directory-Integration mit BenSelect | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und BenSelect konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: ffa17478-3ea1-4356-a289-545b5b9a4494
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 699afd4703efc5e8f63bb13fe1dd753a0c72594d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60282985"
---
# <a name="tutorial-azure-active-directory-integration-with-benselect"></a>Tutorial: Azure Active Directory-Integration mit BenSelect

In diesem Tutorial erfahren Sie, wie Sie BenSelect in Azure Active Directory (Azure AD) integrieren.

Diese Integration bietet folgende Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf BenSelect haben soll.
- Sie können es Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei BenSelect anzumelden (Single Sign-On, SSO; einmaliges Anmelden).
- Sie können Ihre Konten an einem zentralen Ort verwalten – im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit BenSelect konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein SSO-fähiges BenSelect-Abonnement

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/)eine einmonatige Testversion anfordern.

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von BenSelect über den Katalog
1. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-benselect-from-the-gallery"></a>Hinzufügen von BenSelect über den Katalog
Zum Konfigurieren der Integration von BenSelect in Azure AD müssen Sie BenSelect über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um BenSelect über den Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Active Directory][1]

1. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![ANWENDUNGEN][2]
    
1. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![ANWENDUNGEN][3]

1. Geben Sie im Suchfeld die Zeichenfolge **BenSelect**ein.

    ![Erstellen eines Azure AD-Testbenutzers](./media/benselect-tutorial/tutorial_benselect_search.png)

1. Wählen Sie im Ergebnisbereich die Option **BenSelect** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Erstellen eines Azure AD-Testbenutzers](./media/benselect-tutorial/tutorial_benselect_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurieren und Testen der einmaligen Anmeldung von Azure AD
In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei BenSelect mithilfe eines Testbenutzers namens „Britta Simon“.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in BenSelect als Gegenstück zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in BenSelect muss eine Linkbeziehung eingerichtet werden.

Weisen Sie in BenSelect den Wert für **Benutzername** in Azure AD als Wert für **Benutzername** zu, um eine Linkbeziehung herzustellen.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei BenSelect müssen die folgenden Schritte ausgeführt werden:

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
1. **[Erstellen eines Azure AD-Testbenutzers](#creating-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit der Testbenutzerin Britta Simon zu testen.
1. **[Erstellen eines BenSelect-Testbenutzers](#creating-a-benselect-test-user)** , um in BenSelect einen Gegenpart von Britta Simon zu erhalten, der mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Zuweisen des Azure AD-Testbenutzers](#assigning-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Testing Single Sign-On](#testing-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens von Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden in Ihrer BenSelect-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit BenSelect die folgenden Schritte aus:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **BenSelect** auf **Einmaliges Anmelden**.

    ![Configure single sign-on][4]

1. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Configure single sign-on](./media/benselect-tutorial/tutorial_benselect_samlbase.png)

1. Führen Sie die folgenden Schritte im Abschnitt **Domäne und URLs für BenSelect** durch:

    ![Configure single sign-on](./media/benselect-tutorial/tutorial_benselect_url.png)

    Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein: `https://www.benselect.com/enroll/login.aspx?Path=<tenant name>`

    > [!NOTE] 
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Aktualisieren Sie den Wert mit der richtigen Antwort-URL. Wenden Sie sich an das [Supportteam von BenSelect](mailto:support@selerix.com), um diesen Wert zu erhalten.
 
1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Zertifikat (Rohdaten)** , und speichern Sie die Zertifikatdatei auf Ihrem Computer.

    ![Configure single sign-on](./media/benselect-tutorial/tutorial_benselect_certificate.png) 

1. Die BenSelect-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Der folgende Screenshot zeigt ein Beispiel für diese Attributzuordnungen:

    ![Configure single sign-on](./media/benselect-tutorial/tutorial_benselect_06.png)

1. Im Abschnitt **Benutzerattribute** des Dialogfelds **Einmaliges Anmelden**:

    a. Wählen Sie in der Dropdownliste **Benutzer-ID** den Eintrag **ExtractMailPrefix** aus.

    b. Wählen Sie in der Dropdownliste **E-Mail** den Eintrag **user.userprincipalname** aus.

1. Klicken Sie auf die Schaltfläche **Save** .

    ![Configure single sign-on](./media/benselect-tutorial/tutorial_general_400.png)

1. Klicken Sie im Abschnitt **BenSelect-Konfiguration** auf **BenSelect konfigurieren**, um das Fenster **Anmeldung konfigurieren** zu öffnen. Kopieren Sie die **Abmelde-URL, die SAML-Entitäts-ID und die URL für den SAML-SSO-Dienst** aus dem Abschnitt **Kurzübersicht**.

    ![Configure single sign-on](./media/benselect-tutorial/tutorial_benselect_configure.png) 

1. Zum Konfigurieren des einmaligen Anmeldens bei **BenSelect** müssen Sie das heruntergeladene **Zertifikat (Rohdaten)** sowie **Abmelde-URL, SAML-Entitäts-ID und URL für den SAML-SSO-Dienst** an das [BenSelect-Supportteam](mailto:support@selerix.com) senden.

   >[!NOTE]
   >Weisen Sie darauf hin, dass für diese Integration der SHA256-Algorithmus erforderlich ist, um SSO für den entsprechenden Server (etwa „app2101“) festzulegen. (SHA1 wird nicht unterstützt.) 
   
> [!TIP]
> Während der Einrichtung der App können Sie im [Azure-Portal](https://portal.azure.com) nun eine Kurzfassung dieser Anweisungen lesen.  Nachdem Sie diese App aus dem Abschnitt **Active Directory > Unternehmensanwendungen** heruntergeladen haben, klicken Sie einfach auf die Registerkarte **Einmaliges Anmelden**, und rufen Sie die eingebettete Dokumentation über den Abschnitt **Konfiguration** um unteren Rand der Registerkarte auf. Weitere Informationen zur eingebetteten Dokumentation finden Sie hier: [Dokumentation zu eingebettetem Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers
Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

![Azure AD-Benutzer erstellen][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **Azure-Portals** auf das Symbol für **Azure Active Directory**.

    ![Erstellen eines Azure AD-Testbenutzers](./media/benselect-tutorial/create_aaduser_01.png) 

1. Wechseln Sie zu **Benutzer und Gruppen**, und klicken Sie auf **Alle Benutzer**, um die Liste der Benutzer anzuzeigen.
    
    ![Erstellen eines Azure AD-Testbenutzers](./media/benselect-tutorial/create_aaduser_02.png) 

1. Klicken Sie oben im Dialogfeld auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/benselect-tutorial/create_aaduser_03.png) 

1. Führen Sie auf der Dialogfeldseite **Benutzer** die folgenden Schritte aus:
 
    ![Erstellen eines Azure AD-Testbenutzers](./media/benselect-tutorial/create_aaduser_04.png) 

    a. Geben Sie in das Textfeld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie in das Textfeld **Benutzername** die **E-Mail-Adresse** von Britta Simon ein.

    c. Wählen Sie **Kennwort anzeigen** aus, und notieren Sie sich den Wert des **Kennworts**.

    d. Klicken Sie auf **Create**.
 
### <a name="creating-a-benselect-test-user"></a>Erstellen eines BenSelect-Testbenutzers

In diesem Abschnitt wird in BenSelect ein Benutzer namens Britta Simon erstellt. Lassen Sie sich beim Hinzufügen der Benutzer im BenSelect-Konto ggf. vom [Supportteam von BenSelect](mailto:support@selerix.com) unterstützen.

### <a name="assigning-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf BenSelect gewähren.

![Benutzer zuweisen][200] 

**Führen Sie die folgenden Schritte aus, um Britta Simon BenSelect zuzuweisen:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

1. Wählen Sie in der Anwendungsliste die Option **BenSelect**aus.

    ![Configure single sign-on](./media/benselect-tutorial/tutorial_benselect_app.png) 

1. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Benutzer zuweisen][202] 

1. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Benutzer zuweisen][203]

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

1. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    
### <a name="testing-single-sign-on"></a>Testen der einmaligen Anmeldung

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden (SSO) über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „BenSelect“ klicken, sollten Sie automatisch bei Ihrer BenSelect-Anwendung angemeldet werden.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/benselect-tutorial/tutorial_general_01.png
[2]: ./media/benselect-tutorial/tutorial_general_02.png
[3]: ./media/benselect-tutorial/tutorial_general_03.png
[4]: ./media/benselect-tutorial/tutorial_general_04.png

[100]: ./media/benselect-tutorial/tutorial_general_100.png

[200]: ./media/benselect-tutorial/tutorial_general_200.png
[201]: ./media/benselect-tutorial/tutorial_general_201.png
[202]: ./media/benselect-tutorial/tutorial_general_202.png
[203]: ./media/benselect-tutorial/tutorial_general_203.png

