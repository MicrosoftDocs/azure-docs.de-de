---
title: 'Tutorial: Azure Active Directory-Integration mit TurboRater | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und TurboRater konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 3/8/2019
ms.author: jeedes
ms.openlocfilehash: 7483e7a9300088d07e85d238ad897522d59c5464
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92519383"
---
# <a name="tutorial-azure-active-directory-integration-with-turborater"></a>Tutorial: Azure Active Directory-Integration mit TurboRater

In diesem Tutorial erfahren Sie, wie Sie TurboRater in Azure Active Directory (Azure AD) integrieren.

Die Integration von TurboRater in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf TurboRater hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei TurboRater anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten an einem zentralen Ort verwalten: im Azure-Portal.

Details zur Integration von SaaS-Apps (Software-as-a-Service) in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit TurboRater konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Ein TurboRater-Abonnement, für das einmaliges Anmelden aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

TurboRater unterstützt IDP-initiiertes einmaliges Anmelden (SSO).

## <a name="add-turborater-from-the-azure-marketplace"></a>Hinzufügen von TurboRater aus dem Azure Marketplace

Zum Konfigurieren der Integration von TurboRater in Azure AD müssen Sie TurboRater aus dem Azure Marketplace zur Liste mit den verwalteten SaaS-Apps hinzufügen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com?azure-portal=true) an.
1. Wählen Sie im linken Bereich **Azure Active Directory** aus.

    ![Die Option „Azure Active Directory“](common/select-azuread.png)

1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.

    ![Die Option „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie oben im Fenster **+Neue Anwendung** aus, um eine neue Anwendung hinzuzufügen.

    ![Die Option „Neue Anwendung“](common/add-new-app.png)

1. Geben Sie **TurboRater** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **TurboRater** aus, und wählen Sie dann **Hinzufügen** aus, um die Anwendung hinzuzufügen.

    ![TurboRater in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei TurboRater mithilfe eines Testbenutzers namens **B Simon** . Damit einmaliges Anmelden funktioniert, muss eine Verknüpfung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in TurboRater eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei TurboRater müssen die folgenden Schritte ausgeführt werden:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieser Funktion zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für TurboRater](#configure-turborater-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Erstellen eines TurboRater-Testbenutzers](#create-a-turborater-test-user)** , damit in TurboRater ein Benutzer namens B. Simon vorhanden ist, der mit dem Azure AD-Benutzer namens B. Simon verknüpft ist.
1. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD bei TurboRater die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **TurboRater** die Option **Einmaliges Anmelden** aus.

    ![Option „Einmaliges Anmelden konfigurieren“](common/select-sso.png)

1. Wählen Sie im Bereich **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die Option **Bearbeiten** (Stiftsymbol), um den Bereich **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Bereich **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für TurboRater](common/idp-intiated.png)

    1. Geben Sie im Feld **Bezeichner (Entitäts-ID)** eine URL ein:

       `https://www.itcdataservices.com`

    1. Geben Sie im Feld **Antwort-URL (Assertionsverbraucherdienst-URL)** eine URL in folgendem Format ein:

       | Environment | URL |
       | ---------------| --------------- |
       | Test  | `https://ratingqa.itcdataservices.com/webservices/imp/saml/login` |
       | Live  | `https://www.itcratingservices.com/webservices/imp/saml/login` |

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL. Diese Werte erhalten Sie vom [TurboRater-Supportteam](https://www.getitc.com/support). Sie können sich auch die Muster im Bereich **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Klicken Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen** , um die **Verbundmetadaten-XML** aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadoption für Verbundmetadaten-XML](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **TurboRater einrichten** die URL(s), die Sie benötigen:

   * **Anmelde-URL**
   * **Azure AD-Bezeichner**
   * **Abmelde-URL**

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="configure-turborater-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für TurboRater

Zum Konfigurieren des einmaligen Anmeldens aufseiten von TurboRater müssen Sie die heruntergeladene Verbundmetadaten-XML und die kopierten URLs aus dem Azure-Portal an das [Supportteam von TurboRater](https://www.getitc.com/support) senden. Das TurboRater-Team sorgt dafür, dass die SAML-Verbindung für einmaliges Anmelden auf beiden Seiten ordnungsgemäß eingerichtet wird.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin mit dem Namen Britta Simon.

1. Wählen Sie im linken Bereich des Azure-Portals **Azure Active Directory**   > **Benutzer** > **Alle Benutzer** aus.

    ![Die Optionen „Benutzer“ und „Alle Benutzer“](common/users.png)

1. Wählen Sie oben im Bildschirm die Option **+ Neuer Benutzer** aus.

    ![Option „Neuer Benutzer“](common/new-user.png)

1. Führen Sie im Bereich **Benutzer** folgende Schritte aus:

    ![Der Bereich „Benutzer“](common/user-properties.png)

    1. Geben Sie in das Feld **Name** den Namen **BSimon** ein.
  
    1. Geben Sie in das Feld **Benutzername** den Namen **BSimon\@\<yourcompanydomain>.\<extension>** ein. Beispiel: **BSimon\@contoso.com**

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert aus dem Feld **Kennwort** .

    1. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihm Zugriff auf TurboRater gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **TurboRater** aus.

    ![Bereich für Unternehmensanwendungen](common/enterprise-applications.png)

1. Wählen Sie in der Anwendungsliste **TurboRater** aus.

    ![TurboRater in der Anwendungsliste](common/all-applications.png)

1. Wählen Sie im linken Bereich unter **VERWALTEN** die Option **Benutzer und Gruppen** aus.

    ![Option „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie die Schaltfläche **+ Benutzer hinzufügen** aus, und wählen Sie anschließend im Bereich **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Bereich **Benutzer und Gruppen** in der Liste **Benutzer** den Eintrag **B. Simon** und anschließend unten im Bereich **Auswählen** aus.

1. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Bereich **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Wählen Sie unten im Bereich die Option **Auswählen** .

1. Wählen Sie im Bereich **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-a-turborater-test-user"></a>Erstellen eines TurboRater-Testbenutzers

In diesem Abschnitt erstellen Sie in TurboRater einen Benutzer namens B. Simon. Wenden Sie sich an das [TurboRater-Supportteam](https://www.getitc.com/support), um B. Simon als Benutzer in TurboRater hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Portals „Meine Apps“.

Bei Auswahl von **TurboRater** im Portal „Meine Apps“ sollten Sie automatisch bei dem TurboRater-Abonnement angemeldet werden, für das Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Portal „Meine Apps“ finden Sie unter [Zugreifen auf und Verwenden von Apps im Portal „Meine Apps“](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Liste der Tutorials zur Integration von SaaS-Anwendungen in Azure Active Directory](./tutorial-list.md)

* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

* [Was ist bedingter Zugriff?](../conditional-access/overview.md)