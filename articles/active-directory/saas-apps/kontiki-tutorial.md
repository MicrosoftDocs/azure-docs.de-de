---
title: 'Tutorial: Azure Active Directory-Integration mit Kontiki | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Kontiki konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8d5e5413-da4c-40d8-b1d0-f03ecfef030b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bee7454942b9214eeb1253339446df370e20fe01
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785836"
---
# <a name="tutorial-azure-active-directory-integration-with-kontiki"></a>Tutorial: Azure Active Directory-Integration mit Kontiki

In diesem Tutorial erfahren Sie, wie Sie Kontiki in Azure Active Directory (Azure AD) integrieren.

Die Integration von Kontiki in Azure AD bietet die folgenden Vorteile:

* Sie können Azure AD verwenden, um zu steuern, wer Zugriff auf Kontiki hat.
* Benutzer können sich mit ihren Azure AD-Konten automatisch bei Kontiki anmelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten an einem zentralen Ort, im Azure-Portal, verwalten.

Weitere Informationen zur Integration von SaaS-Apps (Software as a Service) in Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Kontiki konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie kein Azure AD-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Ein Kontiki-Abonnement, für das einmaliges Anmelden aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung und integrieren Kontiki in Azure AD.

Kontiki unterstützt die folgenden Features:

* **SP-initiiertes einmaliges Anmelden**
* **Just-In-Time-Benutzerbereitstellung**

## <a name="add-kontiki-in-the-azure-portal"></a>Hinzufügen von Kontiki im Azure-Portal

Damit Sie Kontiki in Azure AD integrieren können, müssen Sie Kontiki zur Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im linken Menü die Option **Azure Active Directory** aus.

    ![Die Option „Azure Active Directory“](common/select-azuread.png)

1. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen**.

    ![Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie zum Hinzufügen einer Anwendung **Neue Anwendung** aus.

    ![Die Option „Neue Anwendung“](common/add-new-app.png)

1. Geben Sie im Suchfeld **Kontiki** ein. Wählen Sie in den Suchergebnissen **Kontiki** und dann **Hinzufügen** aus.

    ![Kontiki in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Kontiki mithilfe eines Testbenutzers namens **Britta Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Kontiki eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Kontiki müssen Sie die folgenden Schritte ausführen:

| Aufgabe | BESCHREIBUNG |
| --- | --- |
| **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** | Ermöglicht Ihren Benutzern die Verwendung dieses Features. |
| **[Konfigurieren des einmaligen Anmeldens für Kontiki](#configure-kontiki-single-sign-on)** | Konfiguriert die Einstellungen für einmaliges Anmelden in der Anwendung. |
| **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** | Testet das einmalige Anmelden von Azure AD für einen Benutzer namens Britta Simon. |
| **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** | Ermöglicht Britta Simon die Verwendung des einmaligen Anmeldens von Azure AD. |
| **[Erstellen eines Kontiki-Testbenutzers](#create-a-kontiki-test-user)** | Erstellt eine Entsprechung von Britta Simon in Kontiki, die mit ihrer Darstellung in Azure AD verknüpft ist. |
| **[Testen des einmaligen Anmeldens](#test-single-sign-on)** | Überprüft, ob die Konfiguration funktioniert. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren Sie das einmalige Anmelden von Azure AD bei Kontiki im Azure-Portal.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) im Anwendungsintegrationsbereich für **Kontiki** die Option **Einmaliges Anmelden** aus.

    ![Option „Einmaliges Anmelden konfigurieren“](common/select-sso.png)

1. Wählen Sie im Bereich **SSO-Methode auswählen** den Modus **SAML** oder **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

1. Wählen Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** die Option **Bearbeiten** (Stiftsymbol) aus, um den Bereich **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Bereich **Grundlegende SAML-Konfiguration** im Textfeld **Anmelde-URL** eine URL mit folgendem Muster ein: `https://<companyname>.mc.eval.kontiki.com`

    ![Informationen zur Domäne und zu den URLs für Kontiki](common/sp-signonurl.png)

    > [!NOTE]
    > Wenden Sie sich an das [Kundensupportteam von Kontiki](https://customersupport.kontiki.com/enterprise/contactsupport.html), um den richtigen Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Wählen Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Option **Herunterladen** neben **Verbundmetadaten-XML** aus. Wählen Sie eine Option zum Herunterladen entsprechend Ihren Anforderungen aus. Speichern Sie das Zertifikat auf Ihrem Computer.

    ![Downloadoption für Verbundmetadaten-XML](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Kontiki einrichten** die folgenden URLs entsprechend Ihren Anforderungen:

    * Anmelde-URL
    * Azure AD-Bezeichner
    * Abmelde-URL

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="configure-kontiki-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Kontiki

Zum Konfigurieren des einmaligen Anmeldens aufseiten von Kontiki senden Sie die heruntergeladene XML-Datei mit den Verbundmetadaten und die kopierten URLs aus dem Azure-Portal an das [Supportteam von Kontiki](https://customersupport.kontiki.com/enterprise/contactsupport.html). Anhand der von Ihnen gesendeten Informationen stellt das Supportteam von Kontiki sicher, dass die SAML-Verbindung für einmaliges Anmelden auf beiden Seiten ordnungsgemäß eingerichtet ist.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin mit dem Namen Britta Simon.

1. Wählen Sie im Azure-Portal **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.

    ![Die Optionen „Benutzer“ und „Alle Benutzer“](common/users.png)

1. Wählen Sie **Neuer Benutzer** aus.

    ![Die Option „Neuer Benutzer“](common/new-user.png)

1. Führen Sie im Bereich **Benutzer** die folgenden Schritte aus:

    1. Geben Sie in das Feld **Name** den Namen **BrittaSimon** ein.
  
    1. Geben Sie im Feld **Benutzername** die Zeichenfolge **brittasimon\@\<IhreUnternehmensdomäne>.\<Erweiterung** ein. Beispiel: **brittasimon\@contoso.com**

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**. Notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.

    1. Klicken Sie auf **Erstellen**.

    ![Der Bereich „Benutzer“](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt gewähren Sie Britta Simon Zugriff auf Kontiki, damit sie das einmalige Anmelden von Azure verwenden kann.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Kontiki** aus.

    ![Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

1. Wählen Sie in der Anwendungsliste **Kontiki** aus.

    ![Kontiki in der Anwendungsliste](common/all-applications.png)

1. Wählen Sie im Menü die Option **Benutzer und Gruppen** aus.

    ![Option „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Klicken Sie auf **Benutzer hinzufügen**. Wählen Sie im Bereich **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Bereich **Benutzer und Gruppen** in der Benutzerliste **Britta Simon** aus. Wählen Sie **Auswählen**.

1. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Bereich **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Wählen Sie **Auswählen**.

1. Wählen Sie im Bereich **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-a-kontiki-test-user"></a>Erstellen eines Kontiki-Testbenutzers

Für das Konfigurieren der Benutzerbereitstellung in Kontiki steht kein Aktionselement zur Verfügung. Wenn sich ein zugewiesener Benutzer über das Portal „Meine Apps“ bei Kontiki anmelden möchte, überprüft Kontiki, ob der Benutzer vorhanden ist. Wenn kein Benutzerkonto gefunden wird, erstellt Kontiki automatisch das Benutzerkonto.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Portals „Meine Apps“.

Nachdem Sie einmaliges Anmelden eingerichtet haben, werden Sie bei Auswahl von **Kontiki** im Portal „Meine Apps“ automatisch bei Kontiki angemeldet. Weitere Informationen zum Portal „Meine Apps“ finden Sie unter [Zugreifen auf und Verwenden von Apps im Portal „Meine Apps“](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in diesen Artikeln:

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
