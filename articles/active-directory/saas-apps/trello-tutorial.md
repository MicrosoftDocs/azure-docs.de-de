---
title: 'Tutorial: Azure Active Directory-Integration mit Trello | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Trello konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 453827b42d12333fd4f27761e7f73484fb749532
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65865794"
---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>Tutorial: Azure Active Directory-Integration mit Trello

In diesem Tutorial erfahren Sie, wie Sie Trello in Azure Active Directory (Azure AD) integrieren.
Die Integration von Trello in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Trello hat.
* Sie können Ihren Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei Trello angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten an einem zentralen Ort verwalten: im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Trello konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über keine Azure AD-Umgebung verfügen, können Sie [eine einmonatige Testversion](https://azure.microsoft.com/pricing/free-trial/) erhalten.
* Ein Trello-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Trello unterstützt SP- und IDP-initiiertes einmaliges Anmelden

* Trello unterstützt die Just-in-Time-Benutzerbereitstellung

## <a name="add-trello-from-the-gallery"></a>Hinzufügen von Trello aus dem Katalog

Zum Konfigurieren der Integration von Trello in Azure AD müssen Sie zuerst Trello aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

Um Trello aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:

1. Klicken Sie im linken Bereich des [Azure-Portals](https://portal.azure.com) auf das Symbol **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Wählen Sie **Unternehmensanwendungen** und dann **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Wählen Sie oben im Dialogfeld die Schaltfläche **Neue Anwendung** aus, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie in das Suchfeld **Trello** ein, und wählen Sie dann im Ergebnisbereich **Trello** aus.

5. Wählen Sie die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Trello in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Trello mithilfe einer Testbenutzerin namens **Britta Simon**.

Damit einmaliges Anmelden funktioniert, muss eine Verknüpfung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Trello eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Trello müssen Sie die folgenden Bausteine ausführen:

1. [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on), um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. [Konfigurieren des einmaligen Anmeldens für Trello](#configure-trello-single-sign-on), um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. [Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user), um das einmalige Anmelden mit Azure AD mit der Testbenutzerin Britta Simon zu testen.
4. [Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user), um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. [Erstellen eines Trello-Testbenutzers](#create-a-trello-test-user), um ein Pendant von Britta Simon in Trello zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
6. [Testen des einmaligen Anmeldens](#test-single-sign-on), um sicherzustellen, dass die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

> [!NOTE]
> Sie sollten über ein **\<Unternehmens\>**-Schlüsselwort aus Trello verfügen. Wenn Sie nicht über das Schlüsselwort verfügen, wenden Sie sich an das [Trello-Supportteam](mailto:support@trello.com), um das Schlüsselwort für Ihr Unternehmen zu erhalten.

Führen Sie zum Konfigurieren des einmaligen Anmeldens über Azure AD bei Trello die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Trello** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im IDP-initiierten Modus konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für Trello](common/idp-intiated.png)

    a. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: `https://trello.com/auth/saml/metadata`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Muster ein: `https://trello.com/auth/saml/consume/<enterprise>`

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie die folgenden Schritte aus, wenn Sie die Anwendung im SP-initiierten Modus konfigurieren möchten:

    ![SSO-Informationen zur Domäne und zu den URLs für Trello](common/metadata-upload-additional-signon.png)

    Geben Sie im Feld **URL für Anmeldung** eine URL mit diesem Muster ein: `https://trello.com/auth/saml/login/<enterprise>`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Wenden Sie sich an das [Clientsupportteam von Trello](mailto:support@trello.com), um diese Werte zu erhalten. Sie können sich auch im Azure-Portal die Muster im Abschnitt **Grundlegende SAML-Konfiguration** ansehen.

6. Die Trello-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute über den Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![Dialogfeld „Benutzerattribute“](common/edit-attribute.png)

7. Konfigurieren Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** das SAML-Tokenattribut, wie in der obigen Abbildung gezeigt. Führen Sie dann die folgenden Schritte aus:

    | NAME |  Quellattribut|
    | --- | --- |
    | User.Email | user.mail |
    | User.FirstName | user.givenname |
    | User.LastName | user.surname |

    a. Wählen Sie **Neuen Anspruch hinzufügen** aus, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![Dialogfeld „Benutzeransprüche“](common/new-save-attribute.png)

    ![Benutzeransprüche verwalten](common/new-attribute-details.png)

    b. Geben Sie im Feld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie **Namespace** leer.

    d. Wählen Sie für **Quelle** die Option **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **OK**.

    g. Wählen Sie **Speichern** aus.

8. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen. Speichern Sie es anschließend auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

9. Kopieren Sie im Abschnitt **Trello einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-trello-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Trello

Zum Konfigurieren des einmaligen Anmeldens aufseiten von Trello müssen Sie das heruntergeladene **Zertifikat (Base64)** und die aus dem Azure-Portal kopierten URLs an das [Supportteam von Trello](mailto:support@trello.com) senden. Es sorgt dafür, dass die SAML-Verbindung für einmaliges Anmelden auf beiden Seiten ordnungsgemäß eingerichtet wird.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie im Dialogfeld **Benutzer** die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** die Zeichenfolge „brittasimon@yourcompanydomain.extension“ ein. In diesem Fall können Sie beispielsweise „BrittaSimon@contoso.com“ eingeben.

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie den Wert im Feld **Kennwort**.

    d. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Trello gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**, danach **Alle Anwendungen** und dann **Trello** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Trello**aus.

    ![Trello-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus. Klicken Sie unten auf der Seite auf die Schaltfläche **Auswählen**.

6. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus. Klicken Sie unten auf der Seite auf die Schaltfläche **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-a-trello-test-user"></a>Erstellen eines Trello-Testbenutzers

In diesem Abschnitt erstellen Sie in Trello einen Benutzer namens Britta Simon. Trello unterstützt die Just-in-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in Trello vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

> [!NOTE]
> Setzen Sie sich mit dem  [Supportteam von Trello](mailto:support@trello.com) in Verbindung, wenn Sie einen Benutzer manuell erstellen müssen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Portals „Meine Apps“.

Wenn Sie im Portal „Meine Apps“ auf die Kachel „Trello“ klicken, sollten Sie automatisch bei Trello angemeldet werden. Weitere Informationen zum Portal „Meine Apps“ finden Sie unter [Was ist das Portal „Meine Apps“?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

