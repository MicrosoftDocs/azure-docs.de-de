---
title: 'Tutorial: Azure Active Directory-Integration mit Palo Alto Networks Captive Portal | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Palo Alto Networks Captive Portal konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a62aa573d49ccdd237e692b63a6fda0dd83d52a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "65869993"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Tutorial: Azure Active Directory-Integration mit Palo Alto Networks Captive Portal

In diesem Tutorial erfahren Sie, wie Sie Palo Alto Networks Captive Portal in Azure Active Directory (Azure AD) integrieren.

Die Integration von Palo Alto Networks Captive Portal in Azure AD hat folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Palo Alto Networks Captive Portal haben soll.
* Sie können Benutzer unter Verwendung von Azure AD-Benutzerkonten automatisch bei Palo Alto Networks Captive Portal anmelden (einmaliges Anmelden).
* Sie können Ihre Konten an einem zentralen Ort verwalten – im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps (Software as a Service) in Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Palo Alto Networks Captive Portal einrichten zu können, benötigen Sie Folgendes:

* Ein Azure Active Directory-Abonnement. Falls Sie nicht über Azure AD verfügen, können Sie eine [einmonatige Testversion](https://azure.microsoft.com/pricing/free-trial/) verwenden.
* Ein SSO-fähiges Abonnement für Palo Alto Networks Captive Portal.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

Palo Alto Networks Captive Portal unterstützt folgende Szenarien:

* **IDP-initiiertes einmaliges Anmelden**
* **Just-In-Time-Benutzerbereitstellung**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>Hinzufügen von Palo Alto Networks Captive Portal aus dem Katalog

Fügen Sie im Katalog zunächst Palo Alto Networks Captive Portal Ihrer Liste mit den verwalteten SaaS-Apps hinzu:

1. Wählen Sie im linken Menü des [Azure-Portals](https://portal.azure.com) die Option **Azure Active Directory** aus.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen**.

    ![Menüoption „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Wählen Sie **Neue Anwendung** aus.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie **Palo Alto Networks Captive Portal** in das Suchfeld ein. Wählen Sie in den Suchergebnissen die Option **Palo Alto Networks - Captive Portal** und anschließend **Hinzufügen** aus.

     ![Palo Alto Networks - Captive Portal in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

Sie konfigurieren und testen das einmalige Anmelden von Azure AD bei Palo Alto Networks Captive Portal mithilfe eines Testbenutzers namens *Britta Simon*. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem gleichen Benutzer in Palo Alto Networks Captive Portal eingerichtet werden. 

Führen Sie die folgenden Aufgaben aus, um das einmalige Anmelden von Azure AD mit Palo Alto Networks Captive Portal zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD:](#configure-azure-ad-single-sign-on)** Ermöglichen Sie dem Benutzer die Verwendung dieses Features.
2. **[Konfigurieren des einmaligen Anmeldens für Palo Alto Networks Captive Portal:](#configure-palo-alto-networks-captive-portal-single-sign-on)** Konfigurieren Sie die Einstellungen für einmaliges Anmelden in der Anwendung.
3. **[Erstellen eines Azure AD-Testbenutzers:](#create-an-azure-ad-test-user)** Testen Sie das einmalige Anmelden von Azure AD mit dem Benutzer *Britta Simon*.
4. **[Zuweisen des Azure AD-Testbenutzers:](#assign-the-azure-ad-test-user)** Richten Sie Britta Simon für die Verwendung des einmaligen Anmeldens in Azure AD ein.
5. **Erstellen eines Testbenutzers für Palo Alto Networks Captive Portal:** Erstellen Sie in Palo Alto Networks Captive Portal einen entsprechenden Benutzer namens *Britta Simon*, der mit dem Azure AD-Benutzer verknüpft ist.
6. **[Testen des einmaligen Anmeldens:](#test-single-sign-on)** Vergewissern Sie sich, dass die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

Aktivieren Sie zunächst das einmalige Anmelden von Azure AD im Azure-Portal:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Palo Alto Networks – Captive Portal** die Option **Einmaliges Anmelden**.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Bereich **SSO-Methode auswählen** die Methode **SAML** aus.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Wählen Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** die Option **Bearbeiten** (Stiftsymbol) aus.

    ![Stiftsymbol der Bearbeitungsoption](common/edit-urls.png)

4. Führen Sie im Bereich **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![Bereich „Grundlegende SAML-Konfiguration“ für Palo Alto Networks Captive Portal](common/idp-intiated.png)

   1. Geben Sie unter **Bezeichner** eine URL mit dem Muster `https://<customer_firewall_host_name>/SAML20/SP` ein.

   2. Geben Sie unter **Antwort-URL** eine URL mit dem Muster `https://<customer_firewall_host_name>/SAML20/SP/ACS` ein.

      > [!NOTE]
      > Aktualisieren Sie die Platzhalterwerte in diesem Schritt mit den tatsächlichen Werten für Bezeichner und Antwort-URL. Die tatsächlichen Werte erhalten Sie vom [Clientsupportteam für Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support).

5. Wählen Sie im Abschnitt **SAML-Signaturzertifikat** neben **Verbundmetadaten-XML** die Option **Herunterladen** aus. Speichern Sie die heruntergeladene Datei auf Ihrem Computer.

    ![Downloadlink für Verbundmetadaten-XML](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Palo Alto Networks Captive Portal

Richten Sie als Nächstes das einmalige Anmelden in Palo Alto Networks Captive Portal ein:

1. Melden Sie sich in einem anderen Browserfenster als Administrator bei der Palo Alto Networks-Website an.

2. Klicken Sie auf die Registerkarte **Device** (Gerät).

    ![Geräteregisterkarte der Palo Alto Networks-Website](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Wählen Sie im Menü die Option **SAML Identity Provider** (SAML-Identitätsanbieter) und anschließend **Import** (Importieren) aus.

    ![Importschaltfläche](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Führen Sie im Dialogfeld **SAML Identity Provider Server Profile Import** (SAML-Identitätsanbieter: Importieren des Serverprofils) die folgenden Schritte aus:

    ![Konfigurieren des einmaligen Anmeldens für Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. Geben Sie einen **Profilnamen** ein (beispielsweise **AzureAD-CaptivePortal**).
    
    2. Wählen Sie neben **Identity Provider Metadata** (Metadaten des Identitätsanbieters) die Option **Browse** (Durchsuchen) aus. Wählen Sie die Datei „metadata.xml“ aus, die Sie im Azure-Portal heruntergeladen haben.
    
    3. Klicken Sie auf **OK**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Erstellen Sie als Nächstes im Azure-Portal einen Testbenutzer namens *Britta Simon*:

1. Wählen Sie im Azure-Portal **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie im Bereich **Benutzer** die folgenden Schritte aus:

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    1. Geben Sie unter **Name** den Namen **BrittaSimon** ein.
  
    2. Geben Sie unter **Benutzername** die Zeichenfolge **BrittaSimon\@\<Ihre_Unternehmensdomäne\>** ein. Beispiel: **BrittaSimon\@contoso.com**.

    3. Geben Sie unter **Kennwort** ein Kennwort ein. Es empfiehlt sich, das eingegebene Kennwort zu dokumentieren. Sie können das Kontrollkästchen **Kennwort anzeigen** aktivieren, um das Kennwort anzuzeigen.

    4. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

Gewähren Sie als Nächstes Zugriff auf Palo Alto Networks Captive Portal, damit Britta Simon das einmalige Anmelden von Azure verwenden kann:

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.

    ![Bereich „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Geben Sie in der Anwendungsliste **Palo Alto Networks - Captive Portal** ein, und wählen Sie die Anwendung aus.

    ![Link „Palo Alto Networks - Captive Portal“ in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf **Benutzer hinzufügen**. Wählen Sie im Bereich **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Bereich **Benutzer und Gruppen** in der Liste **Benutzer** die Option **Britta Simon** aus. Wählen Sie **Auswählen**.

6. Wählen Sie im Bereich **Rolle hinzufügen** die relevante Rolle für den Benutzer aus, um der SAML-Assertion einen Rollenwert hinzuzufügen. Wählen Sie **Auswählen**.

7. Wählen Sie im Bereich **Zuweisung hinzufügen** die Option **Zuweisen** aus.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Erstellen eines Testbenutzers für Palo Alto Networks Captive Portal

Erstellen Sie als Nächstes in Palo Alto Networks Captive Portal einen Benutzer namens *Britta Simon*. Palo Alto Networks Captive Portal unterstützt die Just-in-Time-Benutzerbereitstellung, und sie ist standardmäßig aktiviert. In diesem Abschnitt müssen keine Aufgaben ausgeführt werden. Falls ein Benutzer noch nicht in Palo Alto Networks Captive Portal vorhanden ist, wird nach der Authentifizierung ein neuer Benutzer erstellt.

> [!NOTE]
> Setzen Sie sich mit dem [Clientsupportteam für Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support) in Verbindung, wenn Sie einen Benutzer manuell erstellen möchten.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

Palo Alto Networks Captive Portal wird hinter der Firewall auf einem virtuellen Windows-Computer installiert. Melden Sie sich unter Verwendung des Remotedesktopprotokolls (RDP) bei dem virtuellen Computer an, um das einmalige Anmelden in Palo Alto Networks Captive Portal zu testen. Öffnen Sie in der RDP-Sitzung einen Browser, und besuchen Sie eine beliebige Website. Die SSO-URL wird geöffnet, und Sie werden zur Authentifizierung aufgefordert. Nach Abschluss der Authentifizierung können Sie auf Websites zugreifen.

## <a name="additional-resources"></a>Zusätzliche Ressourcen

Weitere Informationen finden Sie in diesen Artikeln:

- [Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Bedingter Zugriff in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

