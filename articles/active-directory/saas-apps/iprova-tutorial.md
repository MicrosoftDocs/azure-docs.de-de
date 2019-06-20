---
title: 'Tutorial: Azure Active Directory-Integration mit iProva | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und iProva konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1eaeef9b-4479-4a9f-b1b2-bc13b857c75c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa5b66b3cf2ec038107293da7a821fcb75273fc9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64718342"
---
# <a name="tutorial-azure-active-directory-integration-with-iprova"></a>Tutorial: Azure Active Directory-Integration mit iProva

In diesem Tutorial erfahren Sie, wie Sie iProva in Azure Active Directory (Azure AD) integrieren.
Die Integration von iProva in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf iProva hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei iProva anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit iProva konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Abonnement für iProva, das für das einmalige Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* iProva unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-iprova-from-the-gallery"></a>Hinzufügen von iProva aus dem Katalog

Zum Konfigurieren der Integration von iProva in Azure AD müssen Sie iProva aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um iProva aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **iProva** ein, wählen Sie im Ergebnisbereich **iProva** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![iProva in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden mit Azure AD bei iProva mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in iProva eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei iProva müssen Sie die folgenden Bausteine ausführen:

1. **[Abrufen von Konfigurationsinformationen aus iProva](#retrieve-configuration-information-from-iprova)** als Vorbereitung für die nächsten Schritte.
2. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
3. **[Konfigurieren des einmaligen Anmeldens für iProva](#configure-iprova-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
4. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
5. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
6. **[Erstellen eines iProva-Testbenutzers](#create-iprova-test-user)** , um ein Pendant von Britta Simon in iProva zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
7. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="retrieve-configuration-information-from-iprova"></a>Abrufen von Konfigurationsinformationen von iProva

In diesem Abschnitt rufen Sie Informationen aus iProva ab, um das einmalige Anmelden von Azure AD zu konfigurieren.

1. Öffnen Sie einen Webbrowser, und verwenden Sie das folgende URL-Muster, um zur **SAML2-Informationsseite** in iProva zu gelangen:

    | | |
    |-|-|
    | `https://SUBDOMAIN.iprova.nl/saml2info`|
    | `https://SUBDOMAIN.iprova.be/saml2info`|
    | | |

    ![Anzeigen der iProva SAML2-Informationsseite](media/iprova-tutorial/iprova-saml2-info.png)

2. Lassen Sie die Registerkarte Browser geöffnet, während Sie mit den nächsten Schritten in einer anderen Registerkarte des Browsers fortfahren.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD in iProva die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **iProva** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für iProva](common/sp-identifier-reply.png)

    a. Füllen Sie das Feld **Bezeichner** mit dem Wert aus, der hinter der Bezeichnung **EntityID** auf der **iProva SAML2-Informationsseite** angezeigt wird. Diese Seite ist auf Ihrer anderen Browserregisterkarte weiterhin geöffnet.

    b. Füllen Sie das Feld **Antwort-URL** mit dem Wert aus, der hinter der Bezeichnung **Antwort-URL** auf der **iProva SAML2-Informationsseite** angezeigt wird. Diese Seite ist auf Ihrer anderen Browserregisterkarte weiterhin geöffnet.

    c. Füllen Sie das Feld **Anmelde-URL** mit dem Wert aus, der hinter der Bezeichnung **Anmelde-URL** auf der **iProva SAML2-Informationsseite** angezeigt wird. Diese Seite ist auf Ihrer anderen Browserregisterkarte weiterhin geöffnet.

5. Die iProva-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![image](common/edit-attribute.png)

6. Bearbeiten Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die Ansprüche mithilfe des Symbols zum **Bearbeiten**, oder fügen Sie die Ansprüche über **Neuen Anspruch hinzufügen** hinzu, um das SAML-Tokenattribut wie in der obigen Abbildung gezeigt zu konfigurieren. Führen Sie dann die folgenden Schritte aus:

    | NAME | Quellattribut| Namespace  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Geben Sie im Textfeld **Namespace** den für die betreffende Zeile angezeigten Namespacewert ein.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

7. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

### <a name="configure-iprova-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für iProva

1. Melden Sie sich mit dem **Administratorkonto** bei iProva an.

2. Öffnen Sie das Menü **Gehe zu**.

3. Wählen Sie **Anwendungsverwaltung** aus.

4. Wählen Sie im Bereich **Systemeinstellungen** die Option **Allgemein** aus.

5. Wählen Sie **Bearbeiten** aus.

6. Scrollen Sie zu **Zugriffssteuerung**.

    ![Einstellungen für die iProva-Zugriffssteuerung](media/iprova-tutorial/iprova-accesscontrol.png)

7. Suchen Sie die Einstellung **Benutzer werden automatisch mit ihren Netzwerkkonten angemeldet**, und ändern Sie sie in **Ja, Authentifizierung über SAML**. Daraufhin werden zusätzliche Optionen angezeigt.

8. Wählen Sie **Einrichten** aus.

9. Klicken Sie auf **Weiter**.

10. Sie werden von iProva gefragt, ob Sie Verbunddaten von einer URL herunterladen oder aus einer Datei hochladen möchten. Wählen Sie die Option **Von URL** aus.

    ![Herunterladen von Azure AD-Metadaten](media/iprova-tutorial/iprova-download-metadata.png)

11. Fügen Sie die Metadaten-URL ein, die Sie im letzten Schritt des Abschnitts „Konfigurieren des einmaliges Anmeldens bei Azure AD“ gespeichert haben.

12. Wählen Sie die pfeilförmige Schaltfläche aus, um die Metadaten aus Azure AD herunterzuladen.

13. Nach Abschluss des Downloadvorgangs wird die Bestätigungsmeldung **Gültige Verbunddatendatei heruntergeladen** angezeigt.

14. Klicken Sie auf **Weiter**.

15. Überspringen Sie vorerst die Option **Anmeldung testen**, und wählen Sie **Weiter** aus.

16. Wählen Sie in der Dropdownliste **Verwendung beanspruchen** die Option **windowsaccountname** aus.

17. Wählen Sie **Fertig stellen** aus.

18. Sie kehren jetzt zum Bildschirm **Allgemeine Einstellungen bearbeiten** zurück. Scrollen Sie zum unteren Seitenrand, und wählen Sie **OK** aus, um die Konfiguration zu speichern.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen `brittasimon@yourcompanydomain.extension` ein. Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie den Zugriff auf iProva gewähren.

1. Wählen Sie im Azure-Portal die Option **Unternehmensanwendungen** aus, und wählen Sie dann **Alle Anwendungen** und **iProva** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **iProva** aus.

    ![iProva-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-iprova-test-user"></a>Erstellen eines iProva-Testbenutzers

1. Melden Sie sich mit dem **Administratorkonto** bei iProva an.

2. Öffnen Sie das Menü **Gehe zu**.

3. Wählen Sie **Anwendungsverwaltung** aus.

4. Wählen Sie im Bereich **Benutzer und Benutzergruppen** die Option **Benutzer** aus.

5. Wählen Sie **Hinzufügen**.

6. Geben Sie in das Textfeld **Username** (Benutzername) den Namen des Benutzers ein (`BrittaSimon@contoso.com`).

7. Geben Sie in das Feld **Full name** (Vollständiger Name) einen vollständigen Benutzernamen wie **BrittaSimon** ein.

8. Wählen Sie die Option **Kein Kennwort (einmaliges Anmelden verwenden)** aus.

9. Geben Sie im Feld **E-mail address** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z. B. `BrittaSimon@contoso.com`.

10. Scrollen Sie zum Seitenende, und wählen Sie **Fertig stellen** aus.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „iProva“ klicken, sollten Sie automatisch bei Ihrer iProva-Anwendung angemeldet werden. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)