---
title: 'Tutorial: Azure Active Directory-Integration mit Secret Server (On-Premises) | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden für Azure Active Directory und Secret Server (On-Premises) konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9167a5ed72e6fec2ca03cc97d1d41dd6cd4aaba6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104564"
---
# <a name="tutorial-azure-active-directory-integration-with-secret-server-on-premises"></a>Tutorial: Azure Active Directory-Integration mit Secret Server (On-Premises)

In diesem Tutorial erfahren Sie, wie Sie Secret Server (On-Premises) in Azure Active Directory (Azure AD) integrieren.

Die Integration von Secret Server (On-Premises) in Azure AD bietet die folgenden Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf Secret Server (On-Premises) hat.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Secret Server (On-Premises) anzumelden (Single Sign-On, SSO; einmaliges Anmelden).
- Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Secret Server (On-Premises) konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein Secret Server (On-Premises)-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie eine [einmonatige Testversion anfordern](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von Secret Server (On-Premises) aus dem Katalog
1. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Hinzufügen von Secret Server (On-Premises) aus dem Katalog
Zum Konfigurieren der Integration von Secret Server (On-Premises) in Azure AD müssen Sie Secret Server (On-Premises) aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

**Um Secret Server (On-Premises) aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Schaltfläche „Azure Active Directory“][1]

1. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“][2]
    
1. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“][3]

1. Geben Sie im Suchfeld **Secret Server (On-Premises)** ein, wählen Sie im Ergebnisbereich **Secret Server (On-Premises)** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Secret Server (On-Premises) in der Ergebnisliste](./media/secretserver-on-premises-tutorial/tutorial_secretserver_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Secret Server (On-Premises) basierend auf einer Testbenutzerin namens Britta Simon.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Secret Server (On-Premises) als Gegenstück zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Secret Server (On-Premises) muss eine Linkbeziehung eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Secret Server (On-Premises) müssen die folgenden Schritte ausgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
1. **[Erstellen eines Secret Server (On-Premises)-Testbenutzers](#create-a-secret-server-on-premises-test-user)** , um eine Entsprechung von Britta Simon in Secret Server (On-Premises) zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden in Ihrer Secret Server (On-Premises)-Anwendung.

**Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in Secret Server (On-Premises) die folgenden Schritte aus:**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Secret Server (On-Premises)** auf **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden][4]

1. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.

    ![Dialogfeld „Einmaliges Anmelden“](./media/secretserver-on-premises-tutorial/tutorial_secretserver_samlbase.png)

1. Führen Sie im Abschnitt **Domäne und URLs für Secret Server (On-Premises)** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für Secret Server (On-Premises)](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url.png)

    a. Geben Sie im Textfeld **Bezeichner** den vom Benutzer ausgewählten Wert als Beispiel ein: `https://secretserveronpremises.azure`.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein: `https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > Die oben gezeigte Entitäts-ID ist nur ein Beispiel. Sie können einen beliebigen eindeutigen Wert für Ihre Secret Server-Instanz in Azure AD auswählen. Sie müssen diese Entitäts-ID zur Konfiguration an das [Supportteam für den Secret Server (On-Premises)-Client](https://thycotic.force.com/support/s/) senden. Weitere Einzelheiten hierzu finden Sie in [diesem Artikel](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

1. Aktivieren Sie **Erweiterte URL-Einstellungen anzeigen**, und führen Sie die folgenden Schritte aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für Secret Server (On-Premises)](./media/secretserver-on-premises-tutorial/tutorial_secretserver_url1.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<SecretServerURL>/login.aspx`.
     
    > [!NOTE] 
    > Hierbei handelt es sich um Beispielwerte. Die Werte müssen durch die tatsächliche Antwort-URL und die tatsächliche Anmelde-URL ersetzt werden. Wenden Sie sich an das [Supportteam für den Secret Server (On-Premises)-Client](https://thycotic.force.com/support/s/), um diese Werte zu erhalten.

1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Zertifikat (Base64)** , und speichern Sie die Zertifikatdatei auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](./media/secretserver-on-premises-tutorial/tutorial_secretserver_certificate.png)

1. Aktivieren Sie **Erweiterte Einstellungen für die Zertifikatsignatur**, und wählen Sie unter **Signaturoption** die Option **SAML-Antwort und-Assertion signieren** aus.

    ![Signaturoptionen](./media/secretserver-on-premises-tutorial/signing.png)

1. Klicken Sie auf die Schaltfläche **Save** .

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/secretserver-on-premises-tutorial/tutorial_general_400.png)
    
1. Klicken Sie im Abschnitt **Secret Server (On-Premises)-Konfiguration** auf **Secret Server (On-Premises) konfigurieren**, um das Fenster **Anmeldung konfigurieren** zu öffnen. Kopieren Sie die **Abmelde-URL, die SAML-Entitäts-ID und die URL für den SAML-SSO-Dienst** aus dem Abschnitt **Kurzübersicht**.

    ![Secret Server (On-Premises)-Konfiguration](./media/secretserver-on-premises-tutorial/tutorial_secretserver_configure.png)

1. Zum Konfigurieren des einmaligen Anmeldens bei **Secret Server (On-Premises)** müssen Sie das heruntergeladene **Zertifikat (Base64), Abmelde-URL und SAML-Dienst-URL für einmaliges Anmelden** sowie die **SAML-Entitäts-ID** an das [Secret Server (On-Premises)-Supportteam](https://thycotic.force.com/support/s/) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

   ![Erstellen eines Azure AD-Testbenutzers][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Bereich des Azure-Portals auf die Schaltfläche **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](./media/secretserver-on-premises-tutorial/create_aaduser_01.png)

1. Navigieren Sie zu **Benutzer und Gruppen**, und klicken Sie dann auf **Alle Benutzer**, um die Liste mit den Benutzern anzuzeigen.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](./media/secretserver-on-premises-tutorial/create_aaduser_02.png)

1. Klicken Sie oben im Dialogfeld **Alle Benutzer** auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.

    ![Schaltfläche „Hinzufügen“](./media/secretserver-on-premises-tutorial/create_aaduser_03.png)

1. Führen Sie im Dialogfeld **Neuer Benutzer** die folgenden Schritte aus:

    ![Dialogfeld „Benutzer“](./media/secretserver-on-premises-tutorial/create_aaduser_04.png)

    a. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie im Feld **Benutzername** die E-Mail-Adresse des Benutzers Britta Simon ein.

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.

    d. Klicken Sie auf **Create**.
 
### <a name="create-a-secret-server-on-premises-test-user"></a>Erstellen eines Secret Server (On-Premises)-Testbenutzers

In diesem Abschnitt erstellen Sie in Secret Server (On-Premises) einen Benutzer namens Britta Simon. Lassen Sie sich beim Hinzufügen der Benutzer auf der Secret Server (On-Premises)-Plattform ggf. vom [Secret Server (On-Premises)-Supportteam](https://thycotic.force.com/support/s/) unterstützen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Secret Server (On-Premises) gewähren.

![Zuweisen der Benutzerrolle][200]

**Führen Sie die folgenden Schritte aus, um Britta Simon Secret Server (On-Premises) zuzuweisen:**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201]

1. Wählen Sie in der Anwendungsliste **Secret Server (On-Premises)** aus.

    ![Secret Server (On-Premises)-Link in der Anwendungsliste](./media/secretserver-on-premises-tutorial/tutorial_secretserver_app.png)

1. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Link „Benutzer und Gruppen“][202]

1. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“][203]

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

1. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Secret Server (On-Premises)“ klicken, sollten Sie automatisch bei Ihrer Secret Server (On-Premises)-Anwendung angemeldet werden.
Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/secretserver-on-premises-tutorial/tutorial_general_01.png
[2]: ./media/secretserver-on-premises-tutorial/tutorial_general_02.png
[3]: ./media/secretserver-on-premises-tutorial/tutorial_general_03.png
[4]: ./media/secretserver-on-premises-tutorial/tutorial_general_04.png

[100]: ./media/secretserver-on-premises-tutorial/tutorial_general_100.png

[200]: ./media/secretserver-on-premises-tutorial/tutorial_general_200.png
[201]: ./media/secretserver-on-premises-tutorial/tutorial_general_201.png
[202]: ./media/secretserver-on-premises-tutorial/tutorial_general_202.png
[203]: ./media/secretserver-on-premises-tutorial/tutorial_general_203.png

