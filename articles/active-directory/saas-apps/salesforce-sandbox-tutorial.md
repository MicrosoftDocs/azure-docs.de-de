---
title: 'Tutorial: Azure Active Directory-Integration mit Salesforce Sandbox | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Salesforce Sandbox konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: b2940f3eee3112fe1c6d57cc92157c573ecad109
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65904282"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Tutorial: Azure Active Directory-Integration mit Salesforce Sandbox

In diesem Tutorial erfahren Sie, wie Sie Salesforce Sandbox in Azure Active Directory (Azure AD) integrieren.

Sandboxes bieten Ihnen die Möglichkeit, mehrere Kopien Ihres Unternehmens in separaten Umgebungen für eine Vielzahl von Zwecken, wie z. B. Entwicklung, Tests und Schulungen, ohne eine Beeinträchtigung der Daten und Anwendungen in Ihrer Vertriebsproduktions-Organisation zu erstellen.
Weitere Informationen finden Sie in der [Übersicht zu Sandbox](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5).

Die Integration von Salesforce Sandbox in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Salesforce Sandbox hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Salesforce Sandbox anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in Salesforce Sandbox konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Salesforce Sandbox-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Salesforce Sandbox unterstützt **SP- und IDP**-initiiertes einmaliges Anmelden.
* Salesforce Sandbox unterstützt die **Just-in-Time**-Benutzerbereitstellung.
* Salesforce Sandbox unterstützt die [**automatisierte** Benutzerbereitstellung](salesforce-sandbox-provisioning-tutorial.md).

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Hinzufügen von Salesforce Sandbox aus dem Katalog

Zum Konfigurieren der Integration von Salesforce Sandbox in Azure AD müssen Sie Salesforce Sandbox aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

**Um Salesforce Sandbox aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie in das Suchfeld den Namen **Salesforce Sandbox** ein, wählen Sie **Salesforce Sandbox** im Ergebnisbereich aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Salesforce Sandbox in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Salesforce Sandbox mithilfe einer Testbenutzerin namens **Britta Simon**.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in Salesforce Sandbox als Pendant für einen Benutzer in Azure AD fungiert. Anders ausgedrückt: Zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Salesforce Sandbox muss eine Linkbeziehung eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Salesforce Sandbox müssen die folgenden Schritte ausgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Salesforce Sandbox](#configure-salesforce-sandbox-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Salesforce Sandbox-Testbenutzers](#create-salesforce-sandbox-test-user)** , um eine Entsprechung von Britta Simon in Salesforce Sandbox zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD bei Salesforce Sandbox zu konfigurieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Salesforce Sandbox** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine **Metadatendatei des Dienstanbieters** verfügen und die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Klicken Sie auf **Metadatendatei hochladen**.

    ![Metadatendatei hochladen](common/upload-metadata.png)

    b. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.

    ![Metadatendatei auswählen](common/browse-upload-metadata.png)

    > [!NOTE]
    > Sie können die Dienstanbieter-Metadatendatei im Salesforce Sandbox-Verwaltungsportal abrufen, was im weiteren Verlauf des Tutorials erläutert wird.

    c. Nachdem die Metadatendatei erfolgreich hochgeladen wurde, wird der Wert von **Antwort-URL** automatisch im Textfeld **Antwort-URL** aufgefüllt.

    ![image](common/both-replyurl.png)

    > [!Note]
    > Falls der Wert für die **Antwort-URL** nicht automatisch eingefügt wird, geben Sie ihn gemäß Ihren Anforderungen manuell ein.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Metadaten-XML** aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **Salesforce Sandbox einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-salesforce-sandbox-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Salesforce Sandbox

1. Öffnen Sie eine neue Registerkarte in Ihrem Browser, und melden Sie sich mit Ihrem Salesforce Sandbox-Administratorkonto an.

2. Klicken Sie in der oberen rechten Ecke der Seite unter dem **Einstellungssymbol** auf **Setup**.

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/configure1.png)

3. Scrollen Sie im linken Navigationsbereich nach unten bis zu den **EINSTELLUNGEN**, und klicken Sie auf **Identität**, um den zugehörigen Bereich zu erweitern. Klicken Sie dann auf **Einstellungen für einmaliges Anmelden**.

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. Klicken Sie auf der Seite **Einstellungen für einmaliges Anmelden** auf die Schaltfläche **Bearbeiten**.

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/configure3.png)

5. Wählen Sie **SAML aktiviert**, und klicken Sie dann auf **Speichern**.

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Klicken Sie auf **Neu aus Metadatendatei**, um Ihre SAML-Einstellungen für einmaliges Anmelden zu konfigurieren.

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Klicken Sie auf **Datei auswählen**, um die Metadaten-XML-Datei hochzuladen, die Sie aus dem Azure-Portal heruntergeladen haben, und klicken Sie dann auf **Erstellen**.

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. Auf der Seite **SAML-Einstellungen für einmaliges Anmelden** werden die Felder automatisch aufgefüllt. Klicken Sie auf „Speichern“.

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. Klicken Sie auf der Seite **Einstellungen für einmaliges Anmelden** auf die Schaltfläche **Metadaten herunterladen**, um die Dienstanbieter-Metadatendatei herunterzuladen. Verwenden Sie diese Datei im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal, um die notwendigen URLs wie oben erläutert zu konfigurieren.

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/configure4.png)

10. Wenn Sie die Anwendung im **SP**-initiierten Modus konfigurieren möchten, sind dies die Voraussetzungen:

    a. Sie sollten über eine überprüfte Domäne verfügen.

    b. Sie müssen Ihre Domäne in Salesforce Sandbox konfigurieren und aktivieren. Die entsprechenden Schritte werden später in diesem Tutorial erläutert.

    c. Klicken Sie im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus:
  
    ![SSO-Informationen zur Domäne und zu den URLs für Salesforce Sandbox](common/both-signonurl.png)

    Geben Sie im Textfeld **Anmelde-URL** den Wert im folgenden Format ein: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`.

    > [!NOTE]
    > Dieser Wert sollte aus dem Salesforce Sandbox-Portal kopiert werden, nachdem Sie die Domäne aktiviert haben.

11. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Verbundmetadaten-XML**, und speichern Sie die XML-Datei dann auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

12. Öffnen Sie eine neue Registerkarte in Ihrem Browser, und melden Sie sich mit Ihrem Salesforce Sandbox-Administratorkonto an.

13. Klicken Sie in der oberen rechten Ecke der Seite unter dem **Einstellungssymbol** auf **Setup**.

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/configure1.png)

14. Scrollen Sie im linken Navigationsbereich nach unten bis zu den **EINSTELLUNGEN**, und klicken Sie auf **Identität**, um den zugehörigen Bereich zu erweitern. Klicken Sie dann auf **Einstellungen für einmaliges Anmelden**.

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Klicken Sie auf der Seite **Einstellungen für einmaliges Anmelden** auf die Schaltfläche **Bearbeiten**.

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/configure3.png)

16. Wählen Sie **SAML aktiviert**, und klicken Sie dann auf **Speichern**.

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Klicken Sie auf **Neu aus Metadatendatei**, um Ihre SAML-Einstellungen für einmaliges Anmelden zu konfigurieren.

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Klicken Sie auf **Datei auswählen**, um die Metadaten-XML-Datei hochzuladen, und klicken Sie auf **Erstellen**.

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Auf der Seite **SAML-Einstellungen für einmaliges Anmelden** werden Felder automatisch aufgefüllt. Geben Sie den Namen der Konfiguration (z.B. *SPSSOWAAD_Test*) in das Textfeld **Name** ein, und klicken Sie auf „Speichern“.

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Führen Sie die folgenden Schritte aus, um Ihre Domäne in Salesforce Sandbox zu aktivieren.

    > [!NOTE]
    > Vor dem Aktivieren der Domäne müssen Sie die gleiche Domäne in Salesforce Sandbox erstellen. Weitere Informationen finden Sie unter [Defining Your Domain Name](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US) (Festlegen des Domänennamens). Nachdem die Domäne erstellt wurde, sollten Sie sicherstellen, dass diese richtig konfiguriert wurde.

21. Klicken Sie in Salesforce Sandbox im linken Navigationsbereich auf **Unternehmenseinstellungen**, um den zugehörigen Abschnitt zu erweitern, und klicken Sie dann auf **My Domain** (Meine Domäne).

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. Klicken Sie im Abschnitt **Authentifizierungskonfiguration** auf **Bearbeiten**.

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. Wählen Sie im Abschnitt **Authentifizierungskonfiguration** für den **Authentifizierungsdienst** den Namen der SAML-Einstellung für einmaliges Anmelden aus, den Sie während der SSO-Konfiguration in Salesforce Sandbox festgelegt haben, und klicken Sie auf **Speichern**.

    ![Configure single sign-on](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen `brittasimon@yourcompanydomain.extension` ein. Beispiel: BrittaSimon@contoso.com.

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Salesforce Sandbox gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**, **Alle Anwendungen** und dann **Salesforce Sandbox** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen **Salesforce Sandbox** aus.

    ![Der Salesforce Sandbox-Link in der Liste der Anwendungen](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-salesforce-sandbox-test-user"></a>Erstellen eines Salesforce Sandbox-Testbenutzers

In diesem Abschnitt wird ein Benutzer mit dem Namen Britta Simon in Salesforce Sandbox erstellt. Salesforce Sandbox unterstützt die Just-in-Time-Bereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Falls ein Benutzer nicht bereits in Salesforce Sandbox vorhanden ist, wird beim Versuch, auf Salesforce Sandbox zuzugreifen, ein neuer Benutzer erstellt. Außerdem unterstützt Salesforce Sandbox die automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](salesforce-sandbox-provisioning-tutorial.md).

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Salesforce Sandbox“ klicken, sollten Sie automatisch bei der Salesforce Sandbox-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

* [Konfigurieren der Benutzerbereitstellung](salesforce-sandbox-provisioning-tutorial.md)
