---
title: 'Tutorial: Azure Active Directory-Integration mit OpenAthens | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und OpenAthens konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3eca6fc3ab788ee7085c0df5f6c9770858af29ba
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65891700"
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Tutorial: Azure Active Directory-Integration mit OpenAthens

In diesem Tutorial erfahren Sie, wie Sie OpenAthens in Azure Active Directory (Azure AD) integrieren.
Die Integration von OpenAthens in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf OpenAthens hat.
* Sie können es Ihren Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei OpenAthens angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit OpenAthens konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* OpenAthens-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* OpenAthens unterstützt **IDP**-initiiertes einmaliges Anmelden.

* OpenAthens unterstützt die **Just-in-Time**-Benutzerbereitstellung.

## <a name="adding-openathens-from-the-gallery"></a>Hinzufügen von OpenAthens aus dem Katalog

Zum Konfigurieren der Integration von OpenAthens in Azure AD müssen Sie OpenAthens aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um OpenAthens aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **OpenAthens** ein, wählen Sie im Ergebnisbereich **OpenAthens** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![OpenAthens in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei OpenAthens basierend auf einer Testbenutzerin mit dem Namen **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in OpenAthens eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei OpenAthens müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)**, um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für OpenAthens](#configure-openathens-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)**, um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines OpenAthens-Testbenutzers](#create-openathens-test-user)**, um eine Entsprechung von Britta Simon in OpenAthens zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)**, um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei OpenAthens die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **OpenAthens** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

5. Laden Sie im Abschnitt **Grundlegende SAML-Konfiguration** die **Dienstanbieter-Metadatendatei** hoch. Die Schritte hierfür sind weiter unten in diesem Tutorial beschrieben.

    a. Klicken Sie auf **Metadatendatei hochladen**.

    ![OpenAthens – Hochladen von Metadaten](common/upload-metadata.png)

    b. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.

    ![OpenAthens – Durchsuchen des Uploads von Metadaten](common/browse-upload-metadata.png)

    c. Nach dem erfolgreichen Upload der Metadatendatei wird der Wert **Bezeichner** automatisch in das Textfeld im Abschnitt **Grundlegende SAML-Konfiguration** eingefügt:

    ![SSO-Informationen zur Domäne und zu den URLs für OpenAthens](common/idp-identifier.png)

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

### <a name="configure-openathens-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für OpenAthens

1. Melden Sie sich in einem anderen Webbrowserfenster bei der OpenAthens-Unternehmenswebsite als Administrator an.

2. Wählen Sie in der Liste auf der Registerkarte **Management** (Verwaltung) die Option **Connections** (Verbindungen) aus. 

    ![Einmaliges Anmelden konfigurieren](./media/openathens-tutorial/tutorial_openathens_application1.png)

3. Wählen Sie **SAML 1.1/2.0** aus, und klicken Sie anschließend auf die Schaltfläche **Configure** (Konfigurieren).

    ![Einmaliges Anmelden konfigurieren](./media/openathens-tutorial/tutorial_openathens_application2.png)
    
4. Klicken Sie zum Hinzufügen der Konfiguration auf die Schaltfläche **Browse** (Durchsuchen), um die Metadaten-XML-Datei hochzuladen, die Sie aus dem Azure-Portal heruntergeladen haben, und klicken Sie anschließend auf **Add** (Hinzufügen).

    ![Einmaliges Anmelden konfigurieren](./media/openathens-tutorial/tutorial_openathens_application3.png)

5. Führen Sie auf der Registerkarte **Details** die folgenden Schritte aus.

    ![Einmaliges Anmelden konfigurieren](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. Wählen Sie für **Display name mapping** (Zuordnung des Anzeigenamens) die Option **Use Attribute** (Attribut verwenden) aus.

    b. Geben Sie im Textfeld **Display name attribute** (Anzeigenamenattribut) den Wert `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` ein.
    
    c. Wählen Sie für **Unique user mapping** (Eindeutige Benutzerzuordnung) die Option **Use Attribute** (Attribut verwenden) aus.

    d. Geben Sie im Textfeld **Unique user attribute** (Eindeutiges Benutzerattribut) den Wert `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` ein.

    e. Aktivieren Sie unter **Status** alle drei Kontrollkästchen.

    f. Wählen Sie in **Create local accounts** (Lokale Konten erstellen) die Option **automatically** (automatisch) aus.

    g. Klicken Sie auf **Save changes** (Änderungen speichern).

    h. Kopieren Sie auf der Registerkarte **</> Vertrauende Seite** die **Metadaten-URL**, und öffnen Sie die Seite im Browser, um die Datei mit der **SP-Metadaten-XML** herunterzuladen. Laden Sie diese SP-Metadatendatei im Abschnitt **Grundlegende SAML-Konfiguration** in Azure AD hoch.

    ![Einmaliges Anmelden konfigurieren](./media/openathens-tutorial/tutorial_openathens_application5.png)

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf OpenAthens gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **OpenAthens**.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in der Anwendungsliste **OpenAthens** ein, und wählen Sie den Eintrag aus.

    ![OpenAthens-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-openathens-test-user"></a>Erstellen eines OpenAthens-Testbenutzers

In diesem Abschnitt wird in OpenAthens eine Benutzerin namens „Britta Simon“ erstellt. OpenAthens unterstützt die **Just-in-Time-Benutzerbereitstellung**, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in OpenAthens vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „OpenAthens“ klicken, sollten Sie automatisch bei Ihrer OpenAthens-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

