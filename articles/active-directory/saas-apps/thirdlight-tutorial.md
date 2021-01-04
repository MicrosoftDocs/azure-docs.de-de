---
title: 'Tutorial: Azure Active Directory-Integration mit ThirdLight | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und ThirdLight konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: f15d00f1050177c6255fb5528f03314153c3ed9a
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92514660"
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Tutorial: Azure Active Directory-Integration mit Thirdlight

In diesem Tutorial erfahren Sie, wie Sie ThirdLight in Azure Active Directory (Azure AD) integrieren. Diese Integration bietet die folgenden Vorteile:

* Sie können mit Azure AD steuern, wer Zugriff auf ThirdLight hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei ThirdLight anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten an einem zentralen Ort verwalten: im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit ThirdLight konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein ThirdLight-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* ThirdLight unterstützt SP-initiiertes einmaliges Anmelden.

## <a name="add-thirdlight-from-the-gallery"></a>Hinzufügen von ThirdLight aus dem Katalog

Zum Einrichten der Integration von ThirdLight in Azure AD müssen Sie ThirdLight aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Klicken Sie im linken Bereich des [Azure-Portals](https://portal.azure.com) auf **Azure Active Directory** :

    ![Wählen Sie „Azure Active Directory“.](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** > **Alle Anwendungen** :

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Um eine Anwendung hinzuzufügen, wählen Sie oben im Fenster die Option **Neue Anwendung** :

    ![Auswählen von „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie **ThirdLight** in das Suchfeld ein. Wählen Sie in den Suchergebnissen den Eintrag **ThirdLight** aus, und wählen Sie dann **Hinzufügen** aus.

     ![Suchergebnisse](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit ThirdLight mithilfe eines Testbenutzers namens Britta Simon.
Damit einmaliges Anmelden funktioniert, müssen Sie eine Beziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in ThirdLight einrichten.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit ThirdLight müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden der Funktion zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für ThirdLight](#configure-thirdlight-single-sign-on)** auf der Anwendungsseite
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD für den Benutzer zu aktivieren.
5. **[Erstellen eines ThirdLight-Testbenutzers](#create-a-thirdlight-test-user)** , der mit der Darstellung des Benutzers in Azure AD verknüpft ist
6. **[Testen des einmaligen Anmeldens](#test-single-sign-on)** , um sicherzustellen, dass die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit ThirdLight die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für ThirdLight die Option **Einmaliges Anmelden** aus:

    ![„Einmaliges Anmelden“ auswählen](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren:

    ![SSO-Methode auswählen](common/select-saml-option.png)

3. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Symbol **Bearbeiten** aus, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen:

    ![Symbol „Bearbeiten“](common/edit-urls.png)

4. Führen Sie im Dialogfeld **Grundlegende SAML-Konfiguration** die folgenden Schritte aus.

    ![Dialogfeld „Grundlegende SAML-Konfiguration“](common/sp-identifier.png)

    1. Geben Sie im Feld **Anmelde-URL** eine URL im folgenden Format ein:
    
          `https://<subdomain>.thirdlight.com/`

    1. Geben Sie im Feld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein:

       `https://<subdomain>.thirdlight.com/saml/sp`

       > [!NOTE]
       > Diese Werte sind Platzhalter. Sie müssen die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner verwenden. Die Werte erhalten Sie vom [ThirdLight-Supportteam](https://www.thirdlight.com/support). Sie können sich auch die Muster im Azure-Portal im Dialogfeld **Grundlegende SAML-Konfiguration** ansehen.

5. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** neben **Verbundmetadaten-XML** den Link **Herunterladen** gemäß Ihren Anforderungen aus, und speichern Sie die Datei auf Ihrem Computer.

    ![Downloadlink für Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **ThirdLight einrichten** die entsprechenden URLs gemäß Ihren Anforderungen:

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    1. **Anmelde-URL**

    1. **Azure AD-Bezeichner**

    1. **Abmelde-URL** :

### <a name="configure-thirdlight-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für ThirdLight

1. Melden Sie sich in einem neuen Webbrowserfenster auf der ThirdLight-Unternehmenswebsite als Administrator an.

1. Navigieren Sie zu **Configuration** > **System Administration** > **SAML2** (Konfiguration > Systemverwaltung > SAML2):

    ![System Administration (Systemverwaltung)](./media/thirdlight-tutorial/ic805843.png "Systemverwaltung")

1. Führen Sie im Abschnitt für die SAML2-Konfiguration die folgenden Schritte aus:
  
    ![Abschnitt für die SAML2-Konfiguration](./media/thirdlight-tutorial/ic805844.png "Abschnitt für die SAML2-Konfiguration")

    1. Wählen Sie **Einmaliges Anmelden bei SAML2 aktivieren** aus.

    1. Wählen Sie unter **Source for IdP Metadata** (Quelle für IdP-Metadaten) die Option **Load IdP Metadata from XML** (IdP-Metadaten aus XML laden) aus.

    1. Öffnen Sie die Metadatendatei, die Sie im vorherigen Abschnitt aus dem Azure-Portal heruntergeladen haben. Kopieren Sie den Inhalt der Datei, und fügen Sie ihn in das Feld **IdP Metadata XML** (IdP-Metadaten-XML) ein.

    1. Wählen Sie **Save SAML2 settings** (SAML2-Einstellungen speichern) aus.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin namens „Britta Simon“.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory** aus, und wählen Sie dann **Benutzer** und anschließend **Alle Benutzer** aus:

    ![„Alle Benutzer“ auswählen](common/users.png)

2. Wählen Sie oben im Fenster die Option **Neuer Benutzer** aus:

    ![„Neuer Benutzer“ auswählen](common/new-user.png)

3. Führen Sie im Dialogfeld **Benutzer** die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    1. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.
  
    1. Geben Sie in das Feld **Benutzername** den Namen **BrittaSimon@\<yourcompanydomain>.\<extension>** ein. (Beispiel: BrittaSimon@contoso.com.)

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den im Feld **Kennwort** angezeigten Wert.

    1. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf ThirdLight gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **ThirdLight** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen den Eintrag **ThirdLight** aus.

    ![Liste der Anwendungen](common/all-applications.png)

3. Wählen Sie im linken Bereich die Option **Benutzer und Gruppen** aus:

    ![Benutzer und Gruppen auswählen](common/users-groups-blade.png)

4. Wählen Sie die Schaltfläche **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Auswählen von „Benutzer hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Fenster auf die Schaltfläche **Auswählen** .

6. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Klicken Sie unten im Fenster auf die Schaltfläche **Auswählen** .

7. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-a-thirdlight-test-user"></a>Erstellen eines ThirdLight-Testbenutzers

Damit sich Azure AD-Benutzer bei ThirdLight anmelden können, müssen Sie sie in ThirdLight hinzufügen. Sie müssen diese manuell hinzufügen.

Zum Erstellen eines Benutzerkontos führen Sie die folgenden Schritte aus:

1. Melden Sie sich bei der ThirdLight-Unternehmenswebsite als Administrator an.

1. Klicken Sie auf die Registerkarte **Users** (Benutzer).

1. Wählen Sie **Benutzer und Gruppen** .

1. Wählen Sie **Add new User** (Neuen Benutzer hinzufügen) aus.

1. Geben Sie den Benutzernamen, einen Namen oder eine Beschreibung und die E-Mail-Adresse eines gültigen Azure AD-Kontos ein, das Sie bereitstellen möchten. Wählen Sie eine Voreinstellung oder eine Gruppe neuer Mitglieder aus.

1. Klicken Sie auf **Erstellen** .

> [!NOTE]
> Sie können Azure AD-Benutzerkonten mithilfe beliebiger Tools zum Erstellen von Benutzerkonten oder mithilfe der von ThirdLight bereitgestellten API erstellen.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

Jetzt müssen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich testen.

Wenn Sie im Zugriffsbereich die Kachel „ThirdLight“ auswählen, sollten Sie automatisch bei der ThirdLight-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Zugreifen auf und Verwenden von Apps im Portal „Meine Apps“](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Tutorials zur Integration von SaaS-Anwendungen in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)