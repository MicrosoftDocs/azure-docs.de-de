---
title: 'Tutorial: Azure Active Directory-Integration in Sectigo Certificate Manager | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Sectigo Certificate Manager konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.openlocfilehash: c589db84b6221aa23868b3b49aea84f33623619f
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92673867"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Tutorial: Azure Active Directory-Integration in Sectigo Certificate Manager

In diesem Tutorial erfahren Sie, wie Sie Sectigo Certificate Manager (auch als SCM bezeichnet) in Azure Active Directory (Azure AD) integrieren.

Die Integration von Sectigo Certificate Manager in Azure AD bietet Ihnen die folgenden Vorteile:

* Sie können Azure AD verwenden, um zu steuern, wer Zugriff auf Sectigo Certificate Manager hat.
* Benutzer können sich mit ihren Azure AD-Konten automatisch bei Sectigo Certificate Manager anmelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten an einem zentralen Ort, im Azure-Portal, verwalten.

Weitere Informationen zur Integration von SaaS-Apps (Software-as-a-Service) in Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Sectigo Certificate Manager konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie kein Azure AD-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Sectigo Certificate Manager-Konto

> [!NOTE]
> Sectigo führt mehrere Instanzen von Sectigo Certificate Manager aus. Die Hauptinstanz von Sectigo Certificate Manager ist **https:\//cert-manager.com** . Diese URL wird in diesem Tutorial verwendet.  Wenn sich Ihr Konto in einer anderen Instanz befindet, müssen Sie die URLs entsprechend anpassen.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung und integrieren Sectigo Certificate Manager in Azure AD.

Sectigo Certificate Manager unterstützt die folgenden Features:

* **SP-initiiertes einmaliges Anmelden**
* **IDP-initiiertes einmaliges Anmelden**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Hinzufügen von Sectigo Certificate Manager im Azure-Portal

Um Sectigo Certificate Manager in Azure AD zu integrieren, müssen Sie Sectigo Certificate Manager Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im linken Menü **Azure Active Directory** aus.

    ![Die Option „Azure Active Directory“](common/select-azuread.png)

1. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen** .

    ![Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie zum Hinzufügen einer Anwendung **Neue Anwendung** aus.

    ![Die Option „Neue Anwendung“](common/add-new-app.png)

1. Geben Sie **Sectigo Certificate Manager** in das Suchfeld ein. Wählen Sie in den Suchergebnissen **Sectigo Certificate Manager** aus, und klicken Sie dann auf **Hinzufügen** .

    ![Sectigo Certificate Manager in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Sectigo Certificate Manager mithilfe eines Testbenutzers namens **Britta Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Sectigo Certificate Manager eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Sectigo Certificate Manager müssen Sie die folgenden Schritte ausführen:

| Aufgabe | BESCHREIBUNG |
| --- | --- |
| **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** | Ermöglicht Ihren Benutzern die Verwendung dieses Features. |
| **[Konfigurieren des einmaligen Anmeldens für Sectigo Certificate Manager](#configure-sectigo-certificate-manager-single-sign-on)** | Konfiguriert die Einstellungen für einmaliges Anmelden in der Anwendung. |
| **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** | Testet das einmalige Anmelden von Azure AD für einen Benutzer namens Britta Simon. |
| **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** | Ermöglicht Britta Simon die Verwendung des einmaligen Anmeldens von Azure AD. |
| **[Erstellen eines Sectigo Certificate Manager-Testbenutzers](#create-a-sectigo-certificate-manager-test-user)** | Erstellt eine Entsprechung von Britta Simon in Sectigo Certificate Manager, die mit ihrer Darstellung in Azure AD verknüpft ist. |
| **[Testen des einmaligen Anmeldens](#test-single-sign-on)** | Überprüft, ob die Konfiguration funktioniert. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren Sie das einmalige Anmelden von Azure AD bei Sectigo Certificate Manager im Azure-Portal.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) im Anwendungsintegrationsbereich für **Sectigo Certificate Manager** die Option **Einmaliges Anmelden** aus.

    ![Option „Einmaliges Anmelden konfigurieren“](common/select-sso.png)

1. Wählen Sie im Bereich **SSO-Methode auswählen** den Modus **SAML** oder **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

1. Wählen Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** die Option **Bearbeiten** (Stiftsymbol) aus, um den Bereich **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    1. Geben Sie im Feld **Bezeichner (Entitäts-ID)** für die Hauptinstanz von Sectigo Certificate Manager **https:\//cert-manager.com/shibboleth** ein.

    1. Geben Sie im Feld **Antwort-URL** für die Hauptinstanz von Sectigo Certificate Manager **https:\//cert-manager.com/Shibboleth.sso/SAML2/POST** ein.
        
    > [!NOTE]
    > Im Allgemeinen ist die **Anmelde-URL** für den *SP-initiierten Modus* zwar erforderlich, sie wird jedoch nicht für die Anmeldung über Sectigo Certificate Manager benötigt.        

1. Führen Sie im Abschnitt *Grundlegende SAML-Konfiguration* zum Konfigurieren des **IDP-initiierten Modus** und zum Ermöglichen von **Tests** die folgenden Schritte aus:

    1. Wählen Sie **Zusätzliche URLs festlegen** aus.

    1. Geben Sie in das Feld **Relayzustand** die kundenspezifische URL für Sectigo Certificate Manager ein. Geben Sie für die Hauptinstanz von Sectigo Certificate Manager **https:\//cert-manager.com/customer/\<customerURI\>/idp** ein.

    ![SSO-Informationen zur Domäne und zu den URLs für Sectigo Certificate Manager](common/idp-relay.png)

1. Führen Sie im Abschnitt **Benutzerattribute und Ansprüche** die folgenden Schritte aus:

    1. Löschen Sie alle unter **Zusätzliche Ansprüche** angegebenen Ansprüche.
    
    1. Wählen Sie **Neuen Anspruch hinzufügen** aus, und fügen Sie die folgenden vier Ansprüche hinzu:
    
        | Name | Namespace | `Source` | Quellattribut | BESCHREIBUNG |
        | --- | --- | --- | --- | --- |
        | eduPersonPrincipalName | empty | attribute | user.userprincipalname | Muss dem Feld **IdP Person ID** (IdP-Personen-ID) in Sectigo Certificate Manager für Administratoren entsprechen. |
        | mail | empty | attribute | user.mail | Erforderlich |
        | givenName | empty | attribute | user.givenname | Optional |
        | sn | empty | attribute | user.surname | Optional |

       ![Sectigo Certificate Manager: Hinzufügen von vier neuen Ansprüchen](media/sectigo-certificate-manager-tutorial/additional-claims.png)

1. Wählen Sie im Abschnitt **SAML-Signaturzertifikat** neben **Verbundmetadaten-XML** die Option **Herunterladen** aus. Speichern Sie die heruntergeladene XML-Datei auf Ihrem Computer.

    ![Downloadoption für Verbundmetadaten-XML](common/metadataxml.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Sectigo Certificate Manager

Zum Konfigurieren des einmaligen Anmeldens aufseiten von Sectigo Certificate Manager senden Sie die heruntergeladene Verbundmetadaten-XML-Datei an das [Supportteam von Sectigo Certificate Manager](https://sectigo.com/support). Anhand der von Ihnen gesendeten Informationen stellt das Supportteam von Sectigo Certificate Manager sicher, dass die SAML-Verbindung für einmaliges Anmelden auf beiden Seiten ordnungsgemäß eingerichtet ist.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin mit dem Namen Britta Simon.

1. Wählen Sie im Azure-Portal **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.

    ![Optionen „Benutzer“ und „Alle Benutzer“](common/users.png)

1. Wählen Sie **Neuer Benutzer** aus.

    ![Option „Neuer Benutzer“](common/new-user.png)

1. Führen Sie im Bereich **Benutzer** die folgenden Schritte aus:

    1. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.
  
    1. Geben Sie in das Feld **Benutzername** den Namen **brittasimon\@\<your-company-domain>.\<extension\>** ein. Beispiel: **brittasimon\@contoso.com**

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** . Notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.

    1. Klicken Sie auf **Erstellen** .

    ![Der Bereich „Benutzer“](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt gewähren Sie Britta Simon Zugriff auf Sectigo Certificate Manager, damit sie das einmalige Anmelden von Azure verwenden kann.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Sectigo Certificate Manager** aus.

    ![Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie in der Anwendungsliste **Sectigo Certificate Manager** aus.

    ![Sectigo Certificate Manager in der Anwendungsliste](common/all-applications.png)

1. Wählen Sie im Menü die Option **Benutzer und Gruppen** aus.

    ![Option „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Klicken Sie auf **Benutzer hinzufügen** . Wählen Sie im Bereich **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Bereich **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus. Wählen Sie **Auswählen** .

1. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Bereich **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Wählen Sie **Auswählen** .

1. Wählen Sie im Bereich **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Erstellen eines Sectigo Certificate Manager-Testbenutzers

In diesem Abschnitt erstellen Sie in Sectigo Certificate Manager einen Benutzer mit dem Namen Britta Simon. Wenden Sie sich an das [Supportteam von Sectigo Certificate Manager](https://sectigo.com/support), um den Benutzer der Sectigo Certificate Manager-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für das einmalige Anmelden.

#### <a name="test-from-sectigo-certificate-manager-sp-initiated-single-sign-on"></a>Testen über Sectigo Certificate Manager (SP-initiiertes einmaliges Anmelden)

Navigieren Sie zur kundenspezifischen URL (für die Hauptinstanz von Sectigo Certificate Manager: https:\//cert-manager.com/customer/\<customerURI\>/), und wählen Sie die Schaltfläche unter **Or Sign In With** (Oder anmelden mit) aus.  Bei ordnungsgemäßer Konfiguration werden Sie automatisch bei Sectigo Certificate Manager angemeldet.

#### <a name="test-from-azure-single-sign-on-configuration-idp-initiated-single-sign-on"></a>Testen über die Konfiguration des einmaligen Anmeldens von Azure (IDP-initiiertes einmaliges Anmelden)

Wählen Sie im Anwendungsintegrationsbereich für **Sectigo Certificate Manager** die Option **Einmaliges Anmelden** und dann **Testen** aus.  Bei ordnungsgemäßer Konfiguration werden Sie automatisch bei Sectigo Certificate Manager angemeldet.

#### <a name="test-by-using-the-my-apps-portal-idp-initiated-single-sign-on"></a>Testen über das Portal „Meine Apps“ (IDP-initiiertes einmaliges Anmelden)

Wählen Sie im Portal „Meine Apps“ die Option **Sectigo Certificate Manager** aus.  Bei ordnungsgemäßer Konfiguration werden Sie automatisch bei Sectigo Certificate Manager angemeldet. Weitere Informationen zum Portal „Meine Apps“ finden Sie unter [Zugreifen auf und Verwenden von Apps im Portal „Meine Apps“](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)
- [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md)
- [Was ist bedingter Zugriff?](../conditional-access/overview.md)