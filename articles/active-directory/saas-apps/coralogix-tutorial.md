---
title: 'Tutorial: Azure Active Directory-Integration mit Coralogix | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Coralogix konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: ba79bfc1-992e-4924-b76a-8eb0dfb97724
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/2/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0095a825f5582dc795f5bebdcf08be07a92946e
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65900400"
---
# <a name="tutorial-azure-active-directory-integration-with-coralogix"></a>Tutorial: Azure Active Directory-Integration mit Coralogix

In diesem Tutorial erfahren Sie, wie Sie Coralogix in Azure Active Directory (Azure AD) integrieren.
Die Integration von Coralogix in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Coralogix hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Coralogix anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten an einem zentralen Ort verwalten: im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Coralogix konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement Falls Sie über keine Azure AD-Umgebung verfügen, können Sie [eine einmonatige Testversion](https://azure.microsoft.com/pricing/free-trial/) erhalten.
- Ein Coralogix-Abonnement, für das einmaliges Anmelden aktiviert ist. 

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Coralogix unterstützt SP-initiiertes einmaliges Anmelden.

## <a name="add-coralogix-from-the-gallery"></a>Hinzufügen von Coralogix aus dem Katalog

Zum Konfigurieren der Integration von Coralogix in Azure AD müssen Sie zuerst Coralogix über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

Um Coralogix aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:

1. Klicken Sie im linken Bereich des [Azure-Portals](https://portal.azure.com) auf das Symbol **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Wählen Sie oben im Dialogfeld die Schaltfläche **Neue Anwendung** aus, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie in das Suchfeld **Coralogix** ein. Wählen Sie im Ergebnisbereich **Coralogix** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Coralogix in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Coralogix basierend auf einer Testbenutzerin namens Britta Simon.
Damit einmaliges Anmelden funktioniert, muss eine Verknüpfung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Coralogix eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Coralogix müssen die folgenden Schritte ausgeführt werden:

1. [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on), um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. [Konfigurieren des einmaligen Anmeldens für Coralogix](#configure-coralogix-single-sign-on), um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
3. [Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user), um das einmalige Anmelden mit Azure AD mit der Testbenutzerin Britta Simon zu testen.
4. [Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user), um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. [Erstellen eines Coralogix-Testbenutzers](#create-a-coralogix-test-user), um ein Pendant von Britta Simon in Coralogix zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
6. [Testen des einmaligen Anmeldens](#test-single-sign-on), um sicherzustellen, dass die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei Coralogix die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Coralogix** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Symbol **Bearbeiten** aus, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Dialogfeld **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Coralogix](common/sp-identifier.png)

    a. Geben Sie in das Feld **Anmelde-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.coralogix.com`

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL ein, wie z.B.:
    
    `https://api.coralogix.com/saml/metadata.xml`

    oder

    `https://aws-client-prod.coralogix.com/saml/metadata.xml` 

    > [!NOTE]
    > Der Wert der Anmelde-URL entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Wenden Sie sich an das [Supportteam von Coralogix](mailto:info@coralogix.com), um den Wert zu erhalten. Sie können sich auch im Azure-Portal die Muster im Abschnitt **Grundlegende SAML-Konfiguration** ansehen.

5. Die Coralogix-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute über den Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![image](common/edit-attribute.png)

6. Bearbeiten Sie die Ansprüche im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** durch Klicken auf das Symbol **Bearbeiten**. Sie können die Ansprüche auch hinzufügen, indem Sie auf **Neuen Anspruch hinzufügen** klicken, um das SAML-Tokenattribut wie in der vorherigen Abbildung gezeigt zu konfigurieren. Führen Sie dann die folgenden Schritte aus:
    
    a. Klicken Sie auf das Symbol **Bearbeiten**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![Abbildung](./media/coralogix-tutorial/tutorial_usermail.png) ![Abbildung](./media/coralogix-tutorial/tutorial_usermailedit.png)

    b. Wählen Sie in der Liste **Namensbezeichnerformat auswählen** die Option **E-Mail-Adresse** aus.

    c. Wählen Sie in der Liste **Quellattribut** den Eintrag **user.mail** aus.

    d. Wählen Sie **Speichern** aus.

7. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um die Ihren Anforderungen entsprechenden **Verbundmetadaten-XML** aus den verfügbaren Optionen herunterzuladen. Speichern Sie das Zertifikat anschließend auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

8. Kopieren Sie im Abschnitt **Coralogix einrichten** die entsprechenden URLs.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-coralogix-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Coralogix

Zum Konfigurieren des einmaligen Anmeldens aufseiten von **Coralogix** müssen Sie die heruntergeladene **Verbundmetadaten-XML** und die aus dem Azure-Portal kopierten URLs an das [Supportteam von Coralogix](mailto:info@coralogix.com) senden. Es sorgt dafür, dass die SAML-Verbindung für einmaliges Anmelden auf beiden Seiten ordnungsgemäß eingerichtet wird.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie im Dialogfeld **Benutzer** die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** die Zeichenfolge „brittasimon@yourcompanydomain.extension“ ein. In diesem Fall können Sie beispielsweise „brittasimon@contoso.com“ eingeben.

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie den Wert im Feld **Kennwort**.

    d. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Coralogix gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**, danach **Alle Anwendungen** und dann **Coralogix** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Coralogix**aus.

    ![Coralogix-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus. Klicken Sie unten auf der Seite auf die Schaltfläche **Auswählen**.

6. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Klicken Sie unten auf der Seite auf die Schaltfläche **Auswählen**.

7. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Schaltfläche **Zuweisen**.

### <a name="create-a-coralogix-test-user"></a>Erstellen eines Coralogix-Testbenutzers

In diesem Abschnitt erstellen Sie in Coralogix einen Benutzer namens Britta Simon. Lassen Sie sich beim Hinzufügen der Benutzer auf der Coralogix-Plattform vom  [Coralogix-Supportteam](mailto:info@coralogix.com) unterstützen. Sie müssen Benutzer erstellen und aktivieren, bevor Sie einmaliges Anmelden verwenden.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Portals „Meine Apps“.

Wenn Sie im Portal „Meine Apps“ auf die Kachel „Coralogix“ klicken, sollten Sie automatisch bei Coralogix angemeldet werden. Weitere Informationen zum Portal „Meine Apps“ finden Sie unter [Was ist das Portal „Meine Apps“?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

