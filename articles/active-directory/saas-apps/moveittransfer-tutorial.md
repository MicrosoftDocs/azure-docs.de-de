---
title: 'Tutorial: Azure Active Directory-Integration mit MOVEit Transfer – Azure AD-Integration | Microsoft-Dokumentation'
description: Informationen zur Konfiguration des einmaligen Anmeldens zwischen Azure Active Directory und MOVEit Transfer – Azure AD-Integration
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: d7020299bbd52f5e7ba22809847815cb04048cb6
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65905085"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Tutorial: Azure Active Directory-Integration mit MOVEit Transfer – Azure AD-Integration

In diesem Tutorial erfahren Sie, wie Sie MOVEit Transfer – Azure AD-Integration mit Azure Active Directory (Azure AD) integrieren.
Die Integration von MOVEit Transfer – Azure AD-Integration in Azure AD bietet folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf MOVEit Transfer – Azure AD-Integration haben soll.
* Sie können es Ihren Benutzern ermöglichen, dass sie mit ihrem Azure AD-Konto automatisch bei MOVEit Transfer – Azure AD-Integration angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit MOVEit Transfer – Azure AD-Integration konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* MOVEit Transfer – Azure AD-Integration-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* MOVEit Transfer – Azure AD-Integration unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Hinzufügen von MOVEit Transfer – Azure AD-Integration aus dem Katalog

Zum Konfigurieren der Integration von MOVEit Transfer – Azure AD-Integration in Azure AD müssen Sie MOVEit Transfer – Azure AD-Integration über den Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um MOVEit Transfer – Azure AD-Integration über den Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfenster **MOVEit Transfer – Azure AD-Integration** ein, wählen Sie **MOVEit Transfer – Azure AD-Integration** vom Ergebnisbereich aus, und klicken Sie anschließend auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![MOVEit Transfer – Azure AD-Integration in der Liste der Ergebnisse](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei MOVEit Transfer – Azure AD-Integration basierend auf einer Testbenutzerin mit dem Namen **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in MOVEit Transfer – Azure AD-Integration eine Linkbeziehung eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit MOVEit Transfer – Azure AD-Integration müssen die folgenden Schritte ausgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für MOVEit Transfer – Azure AD-Integration](#configure-moveit-transfer---azure-ad-integration-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Testbenutzers für MOVEit Transfer – Azure AD-Integration](#create-moveit-transfer---azure-ad-integration-test-user)**, um in MOVEit Transfer – Azure AD-Integration eine Entsprechung von Britta Simon zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit MOVEit Transfer – Azure AD-Integration zu konfigurieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **MOVEit Transfer – Azure AD-Integration** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine **Dienstanbieter-Metadatendatei** verfügen:

    a. Klicken Sie auf **Metadatendatei hochladen**.

    ![Metadatendatei hochladen](common/upload-metadata.png)

    b. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.

    ![Metadatendatei auswählen](common/browse-upload-metadata.png)

    c. Nach dem erfolgreichen Upload der Metadatendatei werden die Werte unter **Bezeichner** und **Antwort-URL** im Abschnitt **Grundlegende SAML-Konfiguration** automatisch eingefügt:

    ![SSO-Informationen zur Domäne und zu den URLs für MOVEit Transfer – Azure AD-Integration](common/sp-identifier-reply.png)

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://contoso.com`

    > [!NOTE]
    > Der Wert der **Anmelde-URL** entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Wenden Sie sich an das [Kundensupportteam von MOVEit Transfer – Azure AD-Integration](https://community.ipswitch.com/s/support), um den Wert zu erhalten. Sie können die **Dienstanbieter-Metadatendatei** über die **Metadaten-URL des Dienstanbieters** herunterladen. Dies ist weiter unten im Abschnitt **Konfigurieren des einmaligen Anmeldens für MOVEit Transfer – Azure AD-Integration** beschrieben. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **MOVEit Transfer – Azure AD-Integration einrichten** je nach Bedarf die entsprechenden URLs.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für MOVEit Transfer – Azure AD-Integration

1. Melden Sie sich bei Ihrem MOVEit Transfer-Mandanten als Administrator an.

2. Klicken Sie im linken Navigationsbereich auf **Einstellungen**.

    ![Abschnitt „Einstellungen“ auf App-Seite](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. Klicken Sie auf den Link **Einmaliges Anmelden** (unter **Sicherheitsrichtlinien > Benutzerauthentifizierung**).

    ![App-seitige Sicherheitsrichtlinien](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. Klicken Sie auf den Metadaten-URL-Link, um das Metadatendokument herunterzuladen.

    ![Metadaten-URL des Dienstanbieters](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * Stellen Sie sicher, dass der Wert von **entityID** mit dem Wert von **Bezeichner** im Abschnitt **Grundlegende SAML-Konfiguration** übereinstimmt.
   * Stellen Sie sicher, dass die **AssertionConsumerService**-Speicherort-URL mit der **ANTWORT-URL** im Abschnitt **Grundlegende SAML-Konfiguration** übereinstimmt.
    
     ![Einmaliges Anmelden auf App-Seite konfigurieren](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Klicken Sie auf die Schaltfläche **Add Identity Provider** (Identitätsanbieter hinzufügen), um einen neuen Verbundidentitätsanbieter hinzuzufügen.

    ![Identitätsanbieter hinzufügen](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. Klicken Sie auf **Durchsuchen...**, um die Metadatendatei auszuwählen, die Sie aus dem Azure-Portal heruntergeladen haben, und klicken Sie anschließend auf **Add Identity Provider** (Identitätsanbieter hinzufügen), um die heruntergeladene Datei hochzuladen.

    ![SAML-Identitätsanbieter](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. Wählen Sie auf der Seite **Edit Federated Identity Provider Settings...** (Einstellungen für Verbundidentitätsanbieter bearbeiten...) unter **Aktiviert** die Option **Ja** aus, und klicken Sie anschließend auf **Speichern**.

    ![Einstellungen für den Verbundidentitätsanbieter](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. Führen Sie auf der Seite **Edit Federated Identity Provider User Settings** (Benutzereinstellungen für Verbundidentitätsanbieter) die folgenden Aktionen aus:
    
    ![Bearbeiten der Einstellungen für den Verbundidentitätsanbieter](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Wählen Sie unter **Anmeldename** die Option **SAML NameID** (SAML-Namens-ID) aus.
    
    b. Wählen Sie als **vollständiger Name** **Andere** aus, und fügen Sie im Textfeld **Attributname** den Wert `http://schemas.microsoft.com/identity/claims/displayname` ein.
    
    c. Wählen Sie als **E-Mail** **Andere** aus, und fügen Sie im Textfeld **Attributname** den Wert `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` ein.
    
    d. Wählen Sie unter **Auto-create account on signon** (Konto bei der Anmeldung automatisch erstellen) die Option **Ja** aus.
    
    e. Klicken Sie auf die Schaltfläche **Save** .

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf MOVEit Transfer – Azure AD-Integration gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **MOVEit Transfer – Azure AD-Integration**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste die Option **MOVEit Transfer – Azure AD-Integration** aus.

    ![Die Verknüpfung für MOVEit Transfer – Azure AD-Integration in der Liste der Anwendungen](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Erstellen eines Testbenutzers für MOVEit Transfer – Azure AD-Integration

In diesem Abschnitt wird in MOVEit Transfer – Azure AD-Integration ein Benutzer namens Britta Simon erstellt. MOVEit Transfer – Azure AD-Integration unterstützt die von Ihnen aktivierte Just-in-Time-Bereitstellung. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist noch kein Benutzer vorhanden, wird beim Aufrufen von MOVEit Transfer – Azure AD-Integration ein neuer Benutzer erstellt.

>[!NOTE]
>Setzen Sie sich mit dem [Supportteam von MOVEit Transfer– Azure AD-Integrations-Client](https://community.ipswitch.com/s/support) in Verbindung, wenn Sie einen Benutzer manuell erstellen müssen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „MOVEit Transfer – Azure AD-Integration“ klicken, sollten Sie automatisch an der MOVEit Transfer – Azure AD-Integration-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

