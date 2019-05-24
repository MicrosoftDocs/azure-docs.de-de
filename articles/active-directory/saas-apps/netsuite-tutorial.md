---
title: 'Tutorial: Azure Active Directory-Integration mit NetSuite | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und NetSuite konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab329574ef425e8133ac746c185050efcc8bc15a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65871198"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Tutorial: Azure Active Directory-Integration mit NetSuite

In diesem Tutorial erfahren Sie, wie Sie NetSuite in Azure Active Directory (Azure AD) integrieren.
Die Integration von NetSuite in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf NetSuite hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei NetSuite anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren der Azure AD-Integration mit NetSuite benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* NetSuite-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* NetSuite unterstützt **IDP-initiiertes** einmaliges Anmelden.
* NetSuite unterstützt die **Just-in-Time**-Benutzerbereitstellung.
* NetSuite unterstützt die [automatisierte Benutzerbereitstellung](NetSuite-provisioning-tutorial.md).

## <a name="adding-netsuite-from-the-gallery"></a>Hinzufügen von NetSuite aus dem Katalog

Zum Konfigurieren der NetSuite-Integration in Azure AD müssen Sie NetSuite aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um NetSuite aus dem Katalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **NetSuite** ein, wählen Sie im Ergebnisbereich **NetSuite** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![NetSuite in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden mit Azure AD bei NetSuite mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in NetSuite eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens mit Azure AD bei NetSuite die folgenden Bausteine aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für NetSuite](#configure-netsuite-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines NetSuite-Testbenutzers](#create-netsuite-test-user)**, um ein Pendant von Britta Simon in NetSuite zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens mit Azure AD bei NetSuite die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **NetSuite** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für NetSuite](common/idp-reply.png)

    Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein:

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`

    > [!NOTE]
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Aktualisieren Sie den Wert mit der richtigen Antwort-URL. Wenden Sie sich an das [Kundensupportteam von NetSuite](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml), um den Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Die NetSuite-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![image](common/edit-attribute.png)

6. Bearbeiten Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die Ansprüche mithilfe des Symbols zum **Bearbeiten**, oder fügen Sie die Ansprüche über **Neuen Anspruch hinzufügen** hinzu, um das SAML-Tokenattribut wie in der obigen Abbildung gezeigt zu konfigurieren. Führen Sie dann die folgenden Schritte aus:
    
    | NAME | Quellattribut | 
    | ---------------| --------------- |
    | account  | `account id` |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

    >[!NOTE]
    >Bei dem Wert des Kontoattributs handelt es sich nicht um einen echten Wert. Sie werden diesen Wert aktualisieren. Die zugehörige Beschreibung folgt später in diesem Tutorial.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Metadaten-XML** aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **NetSuite einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-netsuite-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für NetSuite

1. Öffnen Sie eine neue Registerkarte in Ihrem Browser, und melden Sie sich bei Ihrer NetSuite-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf der Symbolleiste am oberen Rand der Seite auf **Setup**, navigieren Sie zu **Unternehmen**, und klicken Sie auf **Features aktivieren**.

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Klicken Sie auf der Symbolleiste in der Mitte der Seite auf **SuiteCloud**.

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-suitecloud.png)

4. Klicken Sie im Abschnitt **Authentifizierung verwalten** auf **Einmaliges Anmelden für SAML**, um die Option „Einmaliges Anmelden für SAML“ in NetSuite zu aktivieren.

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Klicken Sie auf der Symbolleiste am oberen Rand der Seite auf **Setup**.

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-setup.png)

6. Klicken Sie in der Liste **SETUPTASKS** auf **Integration**.

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-integration.png)

7. Klicken Sie im Abschnitt **Authentifizierung verwalten** auf **Einmaliges Anmelden für SAML**.

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-saml.png)

8. Führen Sie auf der Seite **SAML-Setup** unter **NetSuite-Konfiguration** die folgenden Schritte aus:

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Wählen Sie **Primäre Authentifizierungsmethode** aus.

    b. Wählen Sie im Feld mit der Bezeichnung **SAMLV2-IDENTITÄTSANBIETERMETADATEN** die Option **IDP-METADATENDATEI HOCHLADEN** aus. Klicken Sie dann zum Hochladen der Metadatendatei, die Sie aus dem Azure-Portal heruntergeladen haben, auf **Durchsuchen** .

    c. Klicken Sie auf **Submit**.

9. Klicken Sie in NetSuite auf **Setup**, navigieren Sie zu **Unternehmen**, und klicken Sie im oberen Navigationsmenü auf **Unternehmensinformationen**.

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-com.png)

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-account-id.png)

    b. Kopieren Sie auf der Seite **Unternehmensinformationen** die **Konto-ID** in der rechten Spalte.

    c. Fügen Sie die **Konto-ID**, die Sie im NetSuite-Konto kopiert haben, in das Feld **Attributwert** in Azure AD ein. 

10. Bevor Benutzer die einmalige Anmeldung in NetSuite ausführen können, müssen ihnen zunächst die entsprechenden Berechtigungen in NetSuite zugewiesen werden. Befolgen Sie die folgenden Anweisungen, um diese Berechtigungen zuzuweisen.

    a. Klicken Sie im oberen Navigationsmenü auf **Setup**.

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-setup.png)

    b. Wählen Sie im linken Navigationsmenü **Benutzer/Rollen** aus, und klicken Sie auf **Rollen verwalten**.

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Klicken Sie auf **Neue Rolle**.

    d. Geben Sie einen **Namen** für die neue Rolle ein.

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-new-role.png)

    e. Klicken Sie auf **Speichern**.

    f. Klicken Sie im oberen Menü auf **Berechtigungen**. Klicken Sie dann auf **Setup**.

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-sso.png)

    g. Wählen Sie **Einmaliges Anmelden für SAML** aus, und klicken Sie dann auf **Hinzufügen**.

    h. Klicken Sie auf **Speichern**.

    i. Klicken Sie im oberen Navigationsmenü auf **Setup** und dann auf **Setup-Manager**.

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-setup.png)

    j. Wählen Sie im linken Navigationsmenü **Benutzer/Rollen** aus, und klicken Sie auf **Benutzer verwalten**.

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Wählen Sie einen Testbenutzer. Klicken Sie auf **Bearbeiten**, und navigieren Sie anschließend zur Registerkarte **Zugriff**.

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-edit-user.png)

    l. Weisen Sie im Dialogfeld „Rollen“ die entsprechende Rolle zu, die Sie erstellt haben.

    ![Configure single sign-on](./media/NetSuite-tutorial/ns-add-role.png)

    m. Klicken Sie auf **Speichern**.

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens mit Azure, indem Sie ihr Zugriff auf NetSuite gewähren.

1. Wählen Sie im Azure-Portal die Option **Unternehmensanwendungen** aus, und wählen Sie dann **Alle Anwendungen** und **NetSuite** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in der Anwendungsliste **NetSuite** ein, und wählen Sie den Eintrag aus.

    ![NetSuite-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-netsuite-test-user"></a>Erstellen eines NetSuite-Testbenutzers

In diesem Abschnitt wird in NetSuite eine Benutzerin namens Britta Simon erstellt. NetSuite unterstützt die Just-in-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in NetSuite vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „NetSuite“ klicken, sollten Sie automatisch bei Ihrer NetSuite-Anwendung angemeldet werden. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurieren der Benutzerbereitstellung](NetSuite-provisioning-tutorial.md)

