---
title: 'Tutorial: Azure Active Directory-Integration mit Pega Systems | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Pega Systems konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 4fb117b7f7b9a0c7a6a67e2714380a01cd53a4e0
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515640"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Tutorial: Azure Active Directory-Integration mit Pega Systems

In diesem Tutorial erfahren Sie, wie Sie Pega Systems in Azure Active Directory (Azure AD) integrieren.

Diese Integration bietet die folgenden Vorteile:

* Sie können Azure AD verwenden, um zu steuern, wer Zugriff auf Pega Systems hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Pega Systems anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten an einem zentralen Ort verwalten: im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Pega Systems konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie sich für eine [einmonatige Testversion](https://azure.microsoft.com/pricing/free-trial/) registrieren.
* Ein Pega Systems-Abonnement, für das einmaliges Anmelden aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Pega Systems unterstützt SP- und IDP-initiiertes einmaliges Anmelden.

## <a name="add-pega-systems-from-the-gallery"></a>Hinzufügen von Pega Systems aus dem Katalog

Zum Einrichten der Integration von Pega Systems in Azure AD müssen Sie Pega Systems aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Klicken Sie im linken Bereich des [Azure-Portals](https://portal.azure.com) auf **Azure Active Directory** :

    ![Wählen Sie „Azure Active Directory“.](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** > **Alle Anwendungen** .

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Um eine Anwendung hinzuzufügen, wählen Sie oben im Fenster die Option **Neue Anwendung** :

    ![Auswählen von „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Pega Systems** ein. Wählen Sie in den Suchergebnissen den Eintrag **Pega Systems** aus, und wählen Sie dann **Hinzufügen** aus.

     ![Suchergebnisse](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Pega Systems mithilfe eines Testbenutzers namens Britta Simon.
Damit einmaliges Anmelden funktioniert, müssen Sie eine Beziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Pega Systems einrichten.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Pega Systems müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden der Funktion zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Pega Systems](#configure-pega-systems-single-sign-on)** auf der Anwendungsseite.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD für den Benutzer zu aktivieren.
5. **[Erstellen eines Pega Systems-Testbenutzers](#create-a-pega-systems-test-user)** , der mit der Darstellung des Benutzers in Azure AD verknüpft ist.
6. **[Testen des einmaligen Anmeldens](#test-single-sign-on)** , um sicherzustellen, dass die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei Pega Systems die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Pega Systems** die Option **Einmaliges Anmelden** aus:

    ![„Einmaliges Anmelden“ auswählen](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren:

    ![SSO-Methode auswählen](common/select-saml-option.png)

3. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Symbol **Bearbeiten** aus, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen:

    ![Symbol „Bearbeiten“](common/edit-urls.png)

4. Führen Sie im Dialogfeld **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im IDP-initiierten Modus konfigurieren möchten.

    ![Dialogfeld „Grundlegende SAML-Konfiguration“](common/idp-intiated.png)

    1. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. Geben Sie im Feld **Antwort-URL** eine URL im folgenden Format ein:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Wenn Sie die Anwendung im SP-initiierten Modus konfigurieren möchten, klicken Sie auf **Zusätzliche URLs festlegen** , und führen Sie die folgenden Schritte aus.

    ![SSO-Informationen zur Domäne und zu den URLs für Pega Systems](common/both-advanced-urls.png)

    1. Geben Sie im Feld **Anmelde-URL** den Wert der Anmelde-URL ein.

    1. Geben Sie im Feld **Relayzustand** eine URL im folgenden Format ein: `https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Die hier angegebenen Werte sind Platzhalter. Sie müssen die tatsächlichen Werte für Bezeichner, Antwort-URL, Anmelde-URL und Relayzustand-URL verwenden. Sie können die Werte für den Bezeichner und die Antwort-URL aus einer Pega-Anwendung abrufen, wie es weiter unten in diesem Tutorial beschrieben ist. Wenden Sie sich an das [Supportteam von Pega Systems](https://www.pega.com/contact-us), um den Wert für den Relayzustand zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

6. Für die Pega Systems-Anwendung müssen die SAML-Assertionen in einem bestimmten Format vorliegen. Um sie im richtigen Format zu erhalten, müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Standardattribute. Wählen Sie das Symbol **Bearbeiten** aus, um das Dialogfeld **Benutzerattribute** zu öffnen:

    ![Benutzerattribute](common/edit-attribute.png)

7. Zusätzlich zu den im vorherigen Screenshot gezeigten Attributen erfordert die Pega Systems-Anwendung einige weitere Attribute, die in der SAML-Antwort zurückgegeben werden müssen. Führen Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die folgenden Schritte aus, um diese SAML-Tokenattribute hinzuzufügen:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Diese Werte sind spezifisch für Ihre Organisation. Geben Sie die entsprechenden Werte an.

    1. Wählen Sie **Neuen Anspruch hinzufügen** aus, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen:

    ![„Neuen Anspruch hinzufügen“ auswählen](common/new-save-attribute.png)

    ![Dialogfeld „Benutzeransprüche verwalten“](common/new-attribute-details.png)

    1. Geben Sie im Feld **Name** den für die Zeile angezeigten Attributnamen ein.

    1. Lassen Sie das Feld **Namespace** leer.

    1. Wählen Sie für **Quelle** die Option **Attribut** aus.

    1. Wählen Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert aus.

    1. Klicken Sie auf **OK** .

    1. Wählen Sie **Speichern** aus.

8. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Link **Herunterladen** neben **Verbundmetadaten-XML** gemäß Ihren Anforderungen aus, und speichern Sie das Zertifikat auf Ihrem Computer:

    ![Downloadlink für Zertifikat](common/metadataxml.png)

9. Kopieren Sie im Abschnitt **Pega Systems einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    1. **Anmelde-URL**

    1. **Azure AD-Bezeichner**

    1. **Abmelde-URL** :

### <a name="configure-pega-systems-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Pega Systems

1. Zum Konfigurieren des einmaligen Anmeldens auf **Pega Systems** -Seite melden Sie sich in einem anderen Browserfenster mit einem Administratorkonto beim Pega-Portal an.

2. Wählen Sie **Create** (Erstellen) > **SysAdmin** (Systemadministrator) > **Authentication Service** (Authentifizierungsdienst) aus:

    ![Authentifizierungsdienst auswählen](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Führen Sie im Bildschirm **Create Authentication Service** (Authentifizierungsdienst erstellen) die folgenden Schritte aus.

    ![Bildschirm zum Erstellen des Authentifizierungsdiensts](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. Wählen Sie in der Liste **Type** (Typ) den Eintrag **SAML 2.0** aus.

    1. Geben Sie in das Feld **Name** den gewünschten Namen ein (z.B. **Azure AD SSO** ).

    1. Geben Sie in das Feld **Short description** (Kurzbeschreibung) eine Beschreibung ein.  

    1. Wählen Sie **Create and open** (Erstellen und öffnen) aus.
    
4. Wählen Sie im Abschnitt **Identity Provider (IdP) information** (Informationen zum Identitätsanbieter) die Option **Import IdP metadata** (IDP-Metadaten importieren) aus, und navigieren Sie zu der Metadatendatei, die Sie aus dem Azure-Portal heruntergeladen haben. Klicken Sie auf **Submit** (Senden), um die Metadaten zu laden:

    ![Abschnitt mit Informationen zum Identitätsanbieter](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    Durch den Importvorgang werden die IDP-Daten wie hier gezeigt eingetragen:

    ![Importierte IDP-Daten](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Führen Sie im Abschnitt **Service Provider (SP) settings** (Einstellungen für den Dienstanbieter) die folgenden Schritte aus.

    ![Einstellungen für den Dienstanbieter](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Kopieren Sie den Wert im Feld **Entity Identification** (Entitätsidentifizierung), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Feld **Bezeichner** ein.

    1. Kopieren Sie den Wert im Feld **Assertion Consumer Service (ACS) location** (ACS-Speicherort), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Feld **Antwort-URL** ein.

    1. Aktivieren Sie **Disable request signing** .

7. Wählen Sie **Speichern** aus.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin namens „Britta Simon“.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** aus, und wählen Sie dann **Benutzer** und anschließend **Alle Benutzer** aus:

    ![„Alle Benutzer“ auswählen](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus:

    ![„Neuer Benutzer“ auswählen](common/new-user.png)

3. Führen Sie im Dialogfeld **Benutzer** die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie in das Feld **Benutzername** den Namen **brittasimon@\<yourcompanydomain>.\<extension>** ein. (Beispiel: BrittaSimon@contoso.com.)

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den im Feld **Kennwort** angezeigten Wert.

    d. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Pega Systems gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** , danach **Alle Anwendungen** und dann **Pega Systems** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen den Eintrag **Pega Systems** aus.

    ![Liste der Anwendungen](common/all-applications.png)

3. Wählen Sie im linken Bereich die Option **Benutzer und Gruppen** aus:

    ![Benutzer und Gruppen auswählen](common/users-groups-blade.png)

4. Wählen Sie die Schaltfläche **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Auswählen von „Benutzer hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste den Eintrag **Britta Simon** aus, und klicken Sie dann am unteren Bildschirmrand auf die Schaltfläche **Auswählen** .

6. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Klicken Sie am unteren Bildschirmrand auf die Schaltfläche **Auswählen** .

7. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-a-pega-systems-test-user"></a>Erstellen eines Pega Systems-Testbenutzers

Als Nächstes müssen Sie in Pega Systems einen Benutzer mit dem Namen Britta Simon erstellen. Wenden Sie sich an das [Supportteam von Pega Systems](https://www.pega.com/contact-us), um Benutzer zu erstellen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

Jetzt müssen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich testen.

Wenn Sie im Zugriffsbereich auf die Kachel „Pega Systems“ klicken, sollten Sie automatisch bei der Pega Systems-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen finden Sie unter [Zugreifen auf und Verwenden von Apps im Portal „Meine Apps“](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Tutorials zur Integration von SaaS-Anwendungen in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)