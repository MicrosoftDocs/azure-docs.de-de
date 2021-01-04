---
title: 'Tutorial: Azure Active Directory-Integration in PolicyStat | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und PolicyStat konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 4d6274f3e1356a8ff8a997e830f0ebee43f50cff
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92893032"
---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>Tutorial: Azure Active Directory-Integration in PolicyStat

In diesem Tutorial erfahren Sie, wie Sie PolicyStat in Azure Active Directory (Azure AD) integrieren.
Die Integration von PolicyStat in Azure AD bietet Ihnen folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf PolicyStat hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei PolicyStat anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in PolicyStat konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein PolicyStat-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* PolicyStat unterstützt **SP-initiiertes** einmaliges Anmelden.

* PolicyStat unterstützt die **Just-in-Time** -Benutzerbereitstellung.

## <a name="adding-policystat-from-the-gallery"></a>Hinzufügen von PolicyStat aus dem Katalog

Zum Konfigurieren der Integration von PolicyStat in Azure AD müssen Sie PolicyStat aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

**Um PolicyStat aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung** , um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **PolicyStat** ein, wählen Sie im Ergebnisbereich **PolicyStat** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** , um die Anwendung hinzuzufügen.

     ![PolicyStat in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit PolicyStat mithilfe eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in PolicyStat eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei PolicyStat müssen die folgenden Schritte ausgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für PolicyStat](#configure-policystat-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
4. **[Erstellen eines PolicyStat-Testbenutzers](#create-policystat-test-user)** , um ein Pendant von Britta Simon in PolicyStat zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
5. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit PolicyStat die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **PolicyStat** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten** , um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für PolicyStat](common/sp-identifier.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<companyname>.policystat.com`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<companyname>.policystat.com/saml2/metadata/`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Supportteam für den PolicyStat-Client](http://www.policystat.com/support/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML** -Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

5. Die PolicyStat-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie der Konfiguration Ihrer SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol **Bearbeiten** , um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![Screenshot: Dialogfeld „Benutzerattribute“, in dem das Bearbeitungssymbol ausgewählt ist](common/edit-attribute.png)

6. Darüber hinaus erwartet die PolicyStat-Anwendung, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Führen Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die folgenden Schritte aus, um das SAML-Tokenattribut wie in der folgenden Tabelle gezeigt hinzuzufügen:

    | Name | Quellattribut |
    |------------------- | -------------------- |
    | uid | ExtractMailPrefix([mail]) |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen** , um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.
    
    ![Screenshot: Abschnitt „Benutzeransprüche“ mit den hervorgehobenen Aktionen „Neuen Anspruch hinzufügen“ und „Speichern“](common/new-save-attribute.png)

    ![Screenshot: Dialogfeld „Benutzeransprüche verwalten“, in dem die Textfelder „Name“, „Transformation“ und „Parameter“ hervorgehoben sind und die Schaltfläche „Speichern“ ausgewählt ist](./media/policystat-tutorial/attribute01.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Aktivieren Sie für „Quelle“ die Option **Transformation**.

    e. Geben Sie in der Liste **Transformation** den für diese Zeile angezeigten Attributwert ein.
    
    f. Geben Sie in der Liste **Parameter 1** den für diese Zeile angezeigten Attributwert ein.

    g. Klicken Sie auf **Speichern**.

7. Kopieren Sie im Abschnitt **PolicyStat einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-policystat-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für PolicyStat

1. Melden Sie sich in einem anderen Webbrowserfenster bei der PolicyStat-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf die Registerkarte **Administrator** und anschließend links im Navigationsbereich auf **Einmaliges Anmelden – Konfiguration**.
   
    ![Administratormenü](./media/policystat-tutorial/ic808633.png "Administratormenü")

3. Klicken Sie auf **Ihre IDP-Metadaten** , und führen Sie anschließend im Abschnitt **Ihre IDP-Metadaten** die folgenden Schritte aus:
   
    ![Screenshot: Ausgewählte Aktion „Your IDP Metadata“ (Ihre IDP-Metadaten)](./media/policystat-tutorial/ic808636.png "Einmaliges Anmelden – Konfiguration")
   
    a. Öffnen Sie die heruntergeladene Metadatendatei, kopieren Sie den Inhalt, und fügen Sie ihn in das Textfeld **Ihre Identitätsanbietermetadaten** ein.

    b. Klicken Sie auf **Änderungen speichern**.

4. Klicken Sie auf **Attribute konfigurieren** , und führen Sie anschließend im Abschnitt **Attribute konfigurieren** die folgenden Schritte aus:
   
    a. Geben Sie im Textfeld **Benutzernamen-Attribut** die Zeichenfolge **uid** ein.

    b. Geben Sie im Textfeld **Vornamen-Attribut** den Namen Ihres Vornamen-Attributanspruchs aus Azure ein **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`** .

    c. Geben Sie im Textfeld **Nachnamen-Attribut** den Namen Ihres Nachnamen-Attributanspruchs aus Azure ein **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`** .

    d. Geben Sie im Textfeld **E-Mail-Attribut** den Namen Ihres E-Mail-Attributanspruchs aus Azure ein **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** .

    e. Klicken Sie auf **Änderungen speichern**.

5. Wählen Sie im Abschnitt **Einrichtung** die Option **Integration des einmaligen Anmeldens aktivieren** aus.
   
    ![SSO-Konfiguration](./media/policystat-tutorial/ic808634.png "Einmaliges Anmelden – Konfiguration")


### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Ihrem eigenen Konto die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf PolicyStat gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **PolicyStat** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **PolicyStat** aus.

    ![PolicyStat-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen** , und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ Ihr Konto aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-policystat-test-user"></a>Erstellen eines PolicyStat-Testbenutzers

In diesem Abschnitt wird in PolicyStat ein Benutzer mit dem Namen „Britta Simon“ erstellt. PolicyStat unterstützt die Just-In-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in PolicyStat vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

>[!NOTE]
>Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von PolicyStat-Benutzerkonten oder mithilfe der von PolicyStat bereitgestellten APIs erstellen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „PolicyStat“ klicken, sollten Sie automatisch bei der PolicyStat-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)