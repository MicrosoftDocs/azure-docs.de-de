---
title: 'Tutorial: Azure Active Directory-Integration mit Percolate | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Percolate konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 913ffc0670e40e749bd28382d492a16891fdc5c9
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522260"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Tutorial: Azure Active Directory-Integration mit Percolate

In diesem Tutorial erfahren Sie, wie Sie Percolate in Azure Active Directory (Azure AD) integrieren.

Diese Integration bietet die folgenden Vorteile:

* Sie können Azure AD verwenden, um zu steuern, wer Zugriff auf Percolate hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei Percolate anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten an einem zentralen Ort verwalten: im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Percolate konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Percolate-Abonnement, für das einmaliges Anmelden aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Percolate unterstützt SP- und IDP-initiiertes einmaliges Anmelden.

## <a name="add-percolate-from-the-gallery"></a>Hinzufügen von Percolate über den Katalog

Um die Integration von Percolate in Azure AD zu konfigurieren, müssen Sie Percolate über den Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Klicken Sie im linken Bereich des [Azure-Portals](https://portal.azure.com) auf **Azure Active Directory** :

    ![Wählen Sie „Azure Active Directory“.](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** > **Alle Anwendungen** :

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Um eine Anwendung hinzuzufügen, wählen Sie oben im Fenster die Option **Neue Anwendung** :

    ![Auswählen von „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie **Percolate** in das Suchfeld ein. Wählen Sie in den Suchergebnissen den Eintrag **Percolate** aus, und wählen Sie dann **Hinzufügen** aus.

     ![Suchergebnisse](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Percolate mithilfe eines Testbenutzers namens Britta Simon.
Damit einmaliges Anmelden funktioniert, müssen Sie eine Beziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Percolate einrichten.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Percolate müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden der Funktion zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Percolate](#configure-percolate-single-sign-on)** auf der Anwendungsseite.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD für den Benutzer zu aktivieren.
5. **[Erstellen eines Percolate-Testbenutzers](#create-a-percolate-test-user)** , der mit der Darstellung des Benutzers in Azure AD verknüpft ist.
6. **[Testen des einmaligen Anmeldens](#test-single-sign-on)** , um sicherzustellen, dass die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei Percolate die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Percolate** die Option **Einmaliges Anmelden** aus:

    ![„Einmaliges Anmelden“ auswählen](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren:

    ![SSO-Methode auswählen](common/select-saml-option.png)

3. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Symbol **Bearbeiten** aus, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen:

    ![Symbol „Bearbeiten“](common/edit-urls.png)

4. Im Dialogfeld **Grundlegende SAML-Konfiguration** müssen Sie keine Aktion ausführen, um die Anwendung im IDP-initiierten Modus zu konfigurieren. Die App ist bereits in Azure integriert.

    ![SSO-Informationen zur Domäne und zu den URLs für Percolate](common/preintegrated.png)

5. Wenn Sie die Anwendung im SP-initiierten Modus konfigurieren möchten, wählen Sie die Option **Zusätzliche URLs festlegen** aus, und geben Sie im Feld **Anmelde-URL** die Zeichenfolge **https://percolate.com/app/login** ein:

   ![Screenshot, auf dem „Zusätzliche URLs festlegen“ ausgewählt und das Textfeld „Anmelde-URL“ hervorgehoben ist](common/metadata-upload-additional-signon.png)
6. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** das **Kopiersymbol** aus, um die **App-Verbundmetadaten-URL** zu kopieren. Speichern Sie diese URL.

    ![Kopieren der App-Verbundmetadaten-URL](common/copy-metadataurl.png)

7. Kopieren Sie im Abschnitt **Percolate einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    1. **Anmelde-URL**

    1. **Azure AD-Bezeichner**

    1. **Abmelde-URL** :

### <a name="configure-percolate-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Percolate

1. Melden Sie sich in einem neuen Webbrowserfenster als Administrator bei Percolate an.

2. Wählen Sie auf der linken Seite der Homepage die Option **Setting** (Einstellungen) aus:
    
    ![„Einstellungen“ auswählen](./media/percolate-tutorial/configure01.png)

3. Wählen Sie im linken Bereich unter **Organization** (Organisation) die Option **SSO** aus:

    ![„SSO“ unter „Organization“ auswählen](./media/percolate-tutorial/configure02.png)

    1. Fügen Sie im Feld **Login URL** (Anmelde-URL) den Wert für **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Fügen Sie im Feld **Entity ID** (Entitäts-ID) den Wert für **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Öffnen Sie im Editor das Base64-codierte Zertifikat, das Sie aus dem Azure-Portal heruntergeladen haben. Kopieren Sie den Inhalt, und fügen Sie ihn anschließend in das Feld **x509 certificates** (X509-Zertifikate) ein.

    1. Geben Sie im Feld **Email attribute** (E-Mail-Attribut) die Zeichenfolge **emailaddress** ein.

    1. Das Feld **Identity provider metadata URL** (Metadaten-URL des Identitätsanbieters) ist optional. Falls Sie eine **App-Verbundmetadaten-URL** aus dem Azure-Portal kopiert haben, können Sie diese in das Feld einfügen.

    1. Wählen Sie in der Liste **Should AuthNRequests be signed?** (Sollen Authentifizierungsanforderungen signiert werden?) die Option **No** (Nein) aus.

    1. Wählen Sie in der Liste **Enable SSO auto-Provisioning** (Automatische Bereitstellung für SSO aktivieren) die Option **No** (Nein) aus.

    1. Wählen Sie **Speichern** aus.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin namens „Britta Simon“.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** aus, und wählen Sie dann **Benutzer** und anschließend **Alle Benutzer** aus:

    ![„Alle Benutzer“ auswählen](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus:

    ![„Neuer Benutzer“ auswählen](common/new-user.png)

3. Führen Sie im Dialogfeld **Benutzer** die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    1. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.
  
    1. Geben Sie in das Feld **Benutzername** den Namen **BrittaSimon@\<yourcompanydomain>.\<extension>** ein. (Beispiel: BrittaSimon@contoso.com.)

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den im Feld **Kennwort** angezeigten Wert.

    1. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure AD, indem Sie ihr Zugriff auf Percolate gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** , danach **Alle Anwendungen** und dann **Percolate** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen den Eintrag **Percolate** aus.

    ![Liste der Anwendungen](common/all-applications.png)

3. Wählen Sie im linken Bereich die Option **Benutzer und Gruppen** aus:

    ![Screenshot, auf dem im linken Bereich „Benutzer und Gruppen“ ausgewählt ist](common/users-groups-blade.png)

4. Wählen Sie die Schaltfläche **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Benutzer und Gruppen auswählen](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste den Eintrag **Britta Simon** aus, und klicken Sie dann am unteren Bildschirmrand auf die Schaltfläche **Auswählen** .

6. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Klicken Sie am unteren Bildschirmrand auf die Schaltfläche **Auswählen** .

7. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-a-percolate-test-user"></a>Erstellen eines Percolate-Testbenutzers

Damit sich Azure AD-Benutzer an Percolate anmelden können, müssen Sie diese in Percolate hinzufügen. Sie müssen diese manuell hinzufügen.

Zum Erstellen eines Benutzerkontos führen Sie die folgenden Schritte aus:

1. Melden Sie sich bei Percolate als Administrator an.

2. Wählen Sie im linken Bereich unter **Organization** (Organisation) die Option **Users** (Benutzer) aus. Wählen Sie **New users** (Neue Benutzer) aus:

    ![Neue Benutzer auswählen](./media/percolate-tutorial/configure03.png)

3. Führen Sie auf der Seite **Create users** (Benutzer erstellen) die folgenden Schritte aus.

    ![Seite zum Erstellen von Benutzern](./media/percolate-tutorial/configure04.png)

    1. Geben Sie im Feld **Email** (E-Mail) die E-Mail-Adresse des Benutzers ein. Beispiel: brittasimon@contoso.com.

    1. Geben Sie im Feld **Full name** (Vollständiger Name) den Namen des Benutzers ein. Beispiel: **Brittasimon**

    1. Wählen Sie **Create users** (Benutzer erstellen) aus.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

Jetzt müssen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich testen.

Wenn Sie im Zugriffsbereich auf die Kachel „Percolate“ klicken, sollten Sie automatisch bei der Percolate-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen finden Sie unter [Zugreifen auf und Verwenden von Apps im Portal „Meine Apps“](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Tutorials zur Integration von SaaS-Anwendungen in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)