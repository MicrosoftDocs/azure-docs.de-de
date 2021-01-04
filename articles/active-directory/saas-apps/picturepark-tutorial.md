---
title: 'Tutorial: Azure Active Directory-Integration in Picturepark | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Picturepark konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 8a00cf11edfea2e732a18a392d465525b38ea45f
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92520845"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Tutorial: Azure Active Directory-Integration in Picturepark

In diesem Tutorial erfahren Sie, wie Sie Picturepark in Azure Active Directory (Azure AD) integrieren.
Die Integration von Picturepark in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Picturepark hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Picturepark anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in Picturepark konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie über keine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Picturepark-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Picturepark unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-picturepark-from-the-gallery"></a>Hinzufügen von Picturepark aus dem Katalog

Zum Konfigurieren der Integration von Picturepark in Azure AD müssen Sie Picturepark aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um Picturepark aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory** .

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Picturepark** ein, wählen Sie im Ergebnisbereich **Picturepark** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

     ![Picturepark in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Picturepark mithilfe eines Testbenutzers namens **Britta Simon** .
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Picturepark eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD mit Picturepark müssen die folgenden Schritte ausgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Picturepark](#configure-picturepark-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Picturepark-Testbenutzers](#create-picturepark-test-user)** , um ein Pendant von Britta Simon in Picturepark zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Picturepark die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Picturepark** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Picturepark](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<companyname>.picturepark.com`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein:

    ```http
        https://<companyname>.current-picturepark.com
        https://<companyname>.picturepark.com
        https://<companyname>.next-picturepark.com
    ```

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Supportteam für den Picturepark-Client](https://picturepark.com/company/picturepark-customer-support), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche **Bearbeiten** , um das Dialogfeld **SAML-Signaturzertifikat** zu öffnen.

    ![Bearbeiten des SAML-Signaturzertifikats](common/edit-certificate.png)

6. Kopieren Sie im Abschnitt **SAML-Signaturzertifikat** den **Fingerabdruck** , und speichern Sie ihn auf Ihrem Computer.

    ![Kopieren des Fingerabdruckwerts](common/copy-thumbprint.png)

7. Kopieren Sie im Abschnitt **Picturepark einrichten** die entsprechenden URLs gemäß Ihren Anforderungen. Verwenden Sie für **Anmelde-URL** einen Wert im folgenden Format: `https://login.microsoftonline.com/_my_directory_id_/wsfed`.

    > [!Note]
    > _my_directory_id_ ist die Mandanten-ID des Azure AD-Abonnements.

    ![Kopieren der Konfiguration-URLs](./media/picturepark-tutorial/configurls.png)

    a. Azure AD-Bezeichner

    b. Abmelde-URL

### <a name="configure-picturepark-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Picturepark

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Picturepark-Unternehmenswebsite als Administrator an.

2. Klicken Sie oben auf der Symbolleiste auf **Verwaltungstools** , und klicken Sie dann auf **Verwaltungskonsole** .
   
    ![Management Console (Verwaltungskonsole)](./media/picturepark-tutorial/ic795062.png "Verwaltungskonsole")

3. Klicken Sie auf **Authentifizierung** und dann auf **Identitätsanbieter** .
   
    ![Authentifizierung](./media/picturepark-tutorial/ic795063.png "Authentication")

4. Führen Sie im Abschnitt **Identitätsanbieterkonfiguration** die folgenden Schritte aus:
   
    ![Identity provider configuration (Identitätsanbieterkonfiguration)](./media/picturepark-tutorial/ic795064.png "Identitätsanbieterkonfiguration")
   
    a. Klicken Sie auf **Hinzufügen** .
  
    b. Geben Sie einen Namen für die Konfiguration ein.
   
    c. Wählen Sie **Als Standard festlegen** .
   
    d. Fügen Sie in das Textfeld **Issuer URI** (Aussteller-URI) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.
   
    e. Fügen Sie im Textfeld **Trusted Issuer Thumb Print** (Fingerabdruck eines vertrauenswürdigen Anbieters) den Wert von **Fingerabdruck** ein, den Sie aus dem Abschnitt **SAML-Signaturzertifikat** kopiert haben. 

5. Klicken Sie auf **JoinDefaultUsersGroup** .

6. Um das Attribut **E-Mail-Adresse** im Textfeld **Anspruch** festzulegen, geben Sie `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` ein, und klicken Sie auf **Speichern** .

      ![Konfiguration](./media/picturepark-tutorial/ic795065.png "Konfiguration")

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** , **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** den Namen `brittasimon@yourcompanydomain.extension` ein. Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Picturepark gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Picturepark** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Picturepark** aus.

    ![Picturepark-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen** .

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen** .

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

### <a name="create-picturepark-test-user"></a>Erstellen eines Picturepark-Testbenutzers

Damit sich Azure AD-Benutzer bei Picturepark anmelden können, müssen sie in Picturepark bereitgestellt werden. Im Fall von Picturepark ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrem **Picturepark** -Mandanten an.

1. Klicken Sie oben in der Symbolleiste auf **Verwaltungstools** , und klicken Sie dann auf **Benutzer** .
   
    ![Benutzer](./media/picturepark-tutorial/ic795067.png "Benutzer")

1. Klicken Sie auf der Registerkarte **Benutzerübersicht** auf **Neu** .
   
    ![Benutzerverwaltung](./media/picturepark-tutorial/ic795068.png "Benutzerverwaltung")

1. Führen Sie im Dialogfeld **Benutzer erstellen** die folgenden Schritte für einen gültigen Azure Active Directory-Benutzer aus, den Sie bereitstellen möchten:
   
    ![Benutzer erstellen](./media/picturepark-tutorial/ic795069.png "Benutzer erstellen")
   
    a. Geben Sie im Textfeld **Email address** (E-Mail-Adresse) die **E-Mail-Adresse** des Benutzers ein: `BrittaSimon@contoso.com`.  
   
    b. Geben Sie in den Textfeldern **Kennwort** und **Kennwort bestätigen** das **Kennwort** von Britta Simon ein. 
   
    c. Geben Sie im Textfeld **Vorname** den **Vornamen** des Benutzers ein, z.B. **Britta** . 
   
    d. Geben Sie im Textfeld **Nachname** den **Nachnamen** des Benutzers ein, z.B. **Simon** .
   
    e. Geben Sie im Textfeld **Unternehmen** den **Unternehmensnamen** des Benutzers ein. 
   
    f. Wählen Sie **Land/Region** des Benutzers im Textfeld **Country** (Land) aus.
  
    g. Geben Sie im Textfeld **PLZ** die **Postleitzahl** des Orts ein.
   
    h. Geben Sie im Textfeld **Ort** den **Ortsnamen** des Benutzers ein.

    i. Wählen Sie eine **Sprache** aus.
   
    j. Klicken Sie auf **Erstellen** .

>[!NOTE]
>Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Picturepark-Benutzerkonten oder mithilfe der von Picturepark bereitgestellten APIs erstellen.
>

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Picturepark“ klicken, sollten Sie automatisch bei der Picturepark-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)