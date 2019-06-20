---
title: 'Tutorial: Azure Active Directory-Integration mit SafeConnect | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SafeConnect konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: f9aaac2e-cdba-4f01-a57f-2c5c26287085
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbb94c28d56b0492fe3488d7c9e2e1257a8b9388
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60624356"
---
# <a name="tutorial-azure-active-directory-integration-with-safeconnect"></a>Tutorial: Azure Active Directory-Integration mit SafeConnect

In diesem Tutorial erfahren Sie, wie Sie SafeConnect in Azure Active Directory (Azure AD) integrieren.

Das Integrieren von SafeConnect in Azure AD bietet Ihnen die folgenden Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf SafeConnect hat.
- Sie können Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei SafeConnect anzumelden (einmaliges Anmelden).
- Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit SafeConnect zu konfigurieren, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Ein SafeConnect-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Um die Schritte in diesem Tutorial zu testen, wird empfohlen, keine Produktionsumgebung zu verwenden.

Um die Schritte in diesem Tutorial zu testen, sollten Sie folgende Empfehlungen beachten:

- Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
- Wenn Sie keine Azure AD-Testumgebung haben, können Sie eine [einmonatige Testversion anfordern](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Beschreibung des Szenarios
In diesem Tutorial testen Sie das einmalige Anmelden für Azure AD in einer Testumgebung. Das in diesem Tutorial beschriebene Szenario besteht aus zwei Hauptbestandteilen:

1. Hinzufügen von SafeConnect aus dem Katalog
2. Konfigurieren und Testen der einmaligen Anmeldung von Azure AD

## <a name="adding-safeconnect-from-the-gallery"></a>Hinzufügen von SafeConnect aus dem Katalog
Um die Integration von SafeConnect in Azure AD zu konfigurieren, müssen Sie SafeConnect aus dem Katalog Ihrer Liste von verwalteten SaaS-Anwendungen hinzufügen.

**So fügen Sie SafeConnect aus dem Katalog hinzu**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**. 

    ![Schaltfläche „Azure Active Directory“][1]

2. Navigieren Sie zu **Unternehmensanwendungen**. Wechseln Sie dann zu **Alle Anwendungen**.

    ![Blatt „Unternehmensanwendungen“][2]
    
3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“][3]

4. Geben Sie im Suchfeld **SafeConnect** ein, wählen Sie im Ergebnisbereich **SafeConnect** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![SafeConnect in der Ergebnisliste](./media/safeconnect-tutorial/tutorial_safeconnect_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit SafeConnect mithilfe der Testbenutzerin Britta Simon.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in SafeConnect als Pendant zu einem Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und einem entsprechenden Benutzer in SafeConnect muss eine Verknüpfung eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei SafeConnect müssen Sie die folgenden Aufgaben ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
3. **[Erstellen eines SafeConnect-Testbenutzers](#create-a-safeconnect-test-user)** , um ein Pendant zu Britta Simon in SafeConnect zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal und konfigurieren das einmalige Anmelden in Ihrer SafeConnect-Anwendung.

**So konfigurieren Sie das einmalige Anmelden von Azure AD mit SafeConnect**

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **SafeConnect** auf **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden][4]

2. Wählen Sie im Dialogfeld **Einmaliges Anmelden** als **Modus** die Option **SAML-basierte Anmeldung** aus, um einmaliges Anmelden zu aktivieren.
 
    ![Dialogfeld „Einmaliges Anmelden“](./media/safeconnect-tutorial/tutorial_safeconnect_samlbase.png)

3. Führen Sie im Abschnitt **Domäne und URLs für SafeConnect** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für SafeConnect](./media/safeconnect-tutorial/tutorial_safeconnect_url.png)

     Geben Sie im Textfeld **Anmelde-URL** eine URL wie die Folgende ein: `https://portal.myweblogon.com:8443/saml/login`.

4. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Metadaten-XML**, und speichern Sie die Metadatendatei dann auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](./media/safeconnect-tutorial/tutorial_safeconnect_certificate.png) 

5. Klicken Sie auf die Schaltfläche **Save** .

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/safeconnect-tutorial/tutorial_general_400.png)

6. Um das einmalige Anmelden bei **SafeConnect** zu konfigurieren, senden Sie die heruntergeladene **Metadaten-XML**-Datei an das [SafeConnect-Supportteam](mailto:support@impulse.com). Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

   ![Erstellen eines Azure AD-Testbenutzers][100]

**Um einen Testbenutzer in Azure AD zu erstellen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Bereich des Azure-Portals auf die Schaltfläche **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](./media/safeconnect-tutorial/create_aaduser_01.png)

2. Navigieren Sie zu **Benutzer und Gruppen**, und klicken Sie dann auf **Alle Benutzer**, um die Liste mit den Benutzern anzuzeigen.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](./media/safeconnect-tutorial/create_aaduser_02.png)

3. Klicken Sie oben im Dialogfeld **Alle Benutzer** auf **Hinzufügen**, um das Dialogfeld **Benutzer** zu öffnen.

    ![Schaltfläche „Hinzufügen“](./media/safeconnect-tutorial/create_aaduser_03.png)

4. Führen Sie im Dialogfeld **Neuer Benutzer** die folgenden Schritte aus:

    ![Dialogfeld „Benutzer“](./media/safeconnect-tutorial/create_aaduser_04.png)

    a. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.

    b. Geben Sie im Feld **Benutzername** die E-Mail-Adresse des Benutzers Britta Simon ein.

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.

    d. Klicken Sie auf **Create**.
 
### <a name="create-a-safeconnect-test-user"></a>Erstellen eines SafeConnect-Testbenutzers

In diesem Abschnitt erstellen Sie in SafeConnect eine Benutzerin namens Britta Simon. Arbeiten Sie mit dem  [SafeConnect-Supportteam](mailto:support@impulse.com) zusammen, um der SafeConnect-Plattform Benutzer hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können. 

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie der Benutzerin Britta Simon das einmalige Anmelden von Azure, indem Sie ihr Zugriff auf SafeConnect gewähren.

![Zuweisen der Benutzerrolle][200] 

**So weisen Sie Britta Simon SafeConnect zu**

1. Öffnen Sie im Azure-Portal die Anwendungsansicht, navigieren Sie zur Verzeichnisansicht, wechseln Sie dann zu **Unternehmensanwendungen**, und klicken Sie auf **Alle Anwendungen**.

    ![Benutzer zuweisen][201] 

2. Wählen Sie in der Anwendungsliste **SafeConnect**aus.

    ![SafeConnect-Link in der Anwendungsliste](./media/safeconnect-tutorial/tutorial_safeconnect_app.png)  

3. Klicken Sie im Menü auf der linken Seite auf **Benutzer und Gruppen**.

    ![Link „Benutzer und Gruppen“][202]

4. Klicken Sie auf die Schaltfläche **Hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“][203]

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus.

6. Klicken Sie im Dialogfeld **Benutzer und Gruppen** auf die Schaltfläche **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf **Zuweisen**.
    
### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „SafeConnect“ klicken, sollten Sie automatisch bei Ihrer SafeConnect-Anwendung angemeldet werden.
Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/safeconnect-tutorial/tutorial_general_01.png
[2]: ./media/safeconnect-tutorial/tutorial_general_02.png
[3]: ./media/safeconnect-tutorial/tutorial_general_03.png
[4]: ./media/safeconnect-tutorial/tutorial_general_04.png

[100]: ./media/safeconnect-tutorial/tutorial_general_100.png

[200]: ./media/safeconnect-tutorial/tutorial_general_200.png
[201]: ./media/safeconnect-tutorial/tutorial_general_201.png
[202]: ./media/safeconnect-tutorial/tutorial_general_202.png
[203]: ./media/safeconnect-tutorial/tutorial_general_203.png

