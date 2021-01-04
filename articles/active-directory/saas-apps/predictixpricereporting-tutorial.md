---
title: 'Tutorial: Azure Active Directory-Integration mit Predictix Price Reporting | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Predictix Price Reporting konfigurieren.
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
ms.openlocfilehash: 801197a171b5be2cfe46c249398381e1b25857ff
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92515354"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-price-reporting"></a>Tutorial: Azure Active Directory-Integration mit Predictix Price Reporting

In diesem Tutorial erfahren Sie, wie Sie Predictix Price Reporting in Azure Active Directory (Azure AD) integrieren.

Diese Integration bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Predictix Price Reporting hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Predictix Price Reporting anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten an einem zentralen Ort verwalten: im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration von Predictix Price Reporting zu konfigurieren, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie nicht über eine Azure AD-Umgebung verfügen, können Sie sich für ein [einmonatiges Testabonnement](https://azure.microsoft.com/pricing/free-trial/) registrieren.
* Ein Predictix Price Reporting-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Predictix Price Reporting unterstützt SP-initiiertes einmaliges Anmelden.

## <a name="adding-predictix-price-reporting-from-the-gallery"></a>Hinzufügen von Predictix Price Reporting aus dem Katalog

Um die Integration von Predictix Price Reporting in Azure AD einzurichten, müssen Sie Ihrer Liste der verwalteten SaaS-Apps Predictix Price Reporting aus dem Katalog hinzufügen.

1. Klicken Sie im linken Bereich des [Azure-Portals](https://portal.azure.com) auf **Azure Active Directory** :

    ![Wählen Sie „Azure Active Directory“.](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen** > **Alle Anwendungen** :

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Um eine Anwendung hinzuzufügen, wählen Sie oben im Fenster die Option **Neue Anwendung** :

    ![Auswählen von „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Predictix Price Reporting** ein. Wählen Sie in den Suchergebnissen den Eintrag **Predictix Price Reporting** aus, und wählen Sie dann **Hinzufügen** aus.

     ![Suchergebnisse](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Predictix Price Reporting mithilfe einer Testbenutzerin namens „Britta Simon“.
Damit einmaliges Anmelden funktioniert, müssen Sie eine Beziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Predictix Price Reporting einrichten.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Predictix Price Reporting müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden der Funktion zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Predictix Price Reporting](#configure-predictix-price-reporting-single-sign-on)** auf der Anwendungsseite.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD für den Benutzer zu aktivieren.
5. **[Erstellen eines Predictix Price Reporting-Testbenutzers](#create-a-predictix-price-reporting-test-user)** , der mit der Darstellung des Benutzers in Azure AD verknüpft ist.
6. **[Testen des einmaligen Anmeldens](#test-single-sign-on)** , um sicherzustellen, dass die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei Predictix Price Reporting die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Predictix Price Reporting** die Option **Einmaliges Anmelden** aus:

    ![„Einmaliges Anmelden“ auswählen](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren:

    ![SSO-Methode auswählen](common/select-saml-option.png)

3. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Symbol **Bearbeiten** aus, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen:

    ![Symbol „Bearbeiten“](common/edit-urls.png)

4. Führen Sie im Dialogfeld **Grundlegende SAML-Konfiguration** die folgenden Schritte aus.

    ![Dialogfeld „Grundlegende SAML-Konfiguration“](common/sp-identifier.png)

    1. Geben Sie im Feld **Anmelde-URL** eine URL im folgenden Format ein:

       `https://<companyname-pricing>.predictix.com/sso/request`

    1. Geben Sie im Feld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein:

        ```https
        https://<companyname-pricing>.predictix.com
        https://<companyname-pricing>.dev.predictix.com
        ```

    > [!NOTE]
    > Diese Werte sind Platzhalter. Sie müssen die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner verwenden. Wenden Sie sich an das [Supportteam von Predictix Price Reporting](https://www.infor.com/company/customer-center/), um die Werte zu erhalten. Sie können sich auch die Muster im Azure-Portal im Dialogfeld **Grundlegende SAML-Konfiguration** ansehen.

5. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Link **Herunterladen** neben **Zertifikat (Base64)** gemäß Ihren Anforderungen aus, und speichern Sie das Zertifikat auf Ihrem Computer:

    ![Downloadlink für Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Predictix Price Reporting einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    1. **Anmelde-URL**

    1. **Azure AD-Bezeichner**

    1. **Abmelde-URL** :

### <a name="configure-predictix-price-reporting-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Predictix Price Reporting

Um das einmalige Anmelden auf der Seite von Predictix Price Reporting zu konfigurieren, müssen Sie das heruntergeladene Zertifikat und die aus dem Azure-Portal kopierten URLs an das [Supportteam von Predictix Price Reporting](https://www.infor.com/company/customer-center/) senden. Dieses Team sorgt dafür, dass die SAML-Verbindung für einmaliges Anmelden auf beiden Seiten ordnungsgemäß eingerichtet wird.

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure AD, indem Sie ihr Zugriff auf Predictix Price Reporting gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen** , **Alle Anwendungen** und **Predictix Price Reporting** aus.

    ![Unternehmensanwendungen](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen **Predictix Price Reporting** aus.

    ![Liste der Anwendungen](common/all-applications.png)

3. Wählen Sie im linken Bereich die Option **Benutzer und Gruppen** aus:

    ![Benutzer und Gruppen auswählen](common/users-groups-blade.png)

4. Wählen Sie die Schaltfläche **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Auswählen von „Benutzer hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste den Eintrag **Britta Simon** aus, und klicken Sie dann am unteren Bildschirmrand auf die Schaltfläche **Auswählen** .

6. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Klicken Sie am unteren Bildschirmrand auf die Schaltfläche **Auswählen** .

7. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-a-predictix-price-reporting-test-user"></a>Erstellen eines Predictix Price Reporting-Testbenutzers

Als Nächstes müssen Sie in Predictix Price Reporting einen Benutzer mit dem Namen „Britta Simon“ erstellen. Lassen Sie sich beim Hinzufügen von Benutzern vom [Supportteam von Predictix Price Reporting](https://www.infor.com/company/customer-center/) unterstützen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

Jetzt müssen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich testen.

Wenn Sie im Zugriffsbereich die Kachel „Predictix Price Reporting“ auswählen, sollten Sie automatisch bei der Predictix Price Reporting-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen finden Sie unter [Zugreifen auf und Verwenden von Apps im Portal „Meine Apps“](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Tutorials zur Integration von SaaS-Anwendungen in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)