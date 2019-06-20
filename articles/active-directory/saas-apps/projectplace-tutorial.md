---
title: 'Tutorial: Azure Active Directory-Integration mit Projectplace | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Projectplace konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: aae6de49a3df3f1e648b99aa9936d6af85fc020f
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497288"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>Tutorial: Integrieren von Projectplace in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Projectplace in Azure Active Directory (Azure AD) integrieren. Die Integration von Projectplace in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Projectplace hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Projectplace anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.
* Benutzer können in Projectplace automatisch bereitgestellt werden.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Projectplace-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. Projectplace unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden sowie die **Just-In-Time**-Benutzerbereitstellung.

## <a name="adding-projectplace-from-the-gallery"></a>Hinzufügen von Projectplace aus dem Katalog

Zum Konfigurieren der Integration von Projectplace in Azure AD müssen Sie Projectplace aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Projectplace** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Projectplace** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Projectplace mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Projectplace eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Projectplace müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
2. **[Konfigurieren von Projectplace](#configure-projectplace)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Projectplace-Testbenutzers](#create-projectplace-test-user)** , um ein Pendant von B. Simon in Projectplace zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Projectplace** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten: Im Abschnitt **SAML-Basiskonfiguration** ist die Anwendung vorkonfiguriert, und die notwendigen URLs sind bereits mit Azure vorausgefüllt. Der Benutzer muss die Konfiguration speichern, indem er auf die Schaltfläche **Speichern** klickt.

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL ein: `https://service.projectplace.com`.

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf das **Kopiersymbol**, um die **App-Verbundmetadaten-URL** gemäß Ihren Anforderungen zu kopieren, und speichern Sie sie im Editor.

   ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

1. Kopieren Sie im Abschnitt **Projectplace einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

   ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Konfigurieren von Projectplace

Zum Konfigurieren des einmaligen Anmeldens aufseiten von **Projectplace** müssen Sie die kopierte **App-Verbundmetadaten-URL** aus dem Azure-Portal an das [Supportteam von Projectplace](https://success.planview.com/Projectplace/Support) senden. Dieses Team sorgt dafür, dass die SAML-Verbindung für einmaliges Anmelden auf beiden Seiten ordnungsgemäß eingerichtet wird.

>[!NOTE]
>Einmaliges Anmelden muss vom [Supportteam von Projectplace](https://success.planview.com/Projectplace/Support) konfiguriert werden. Sie erhalten eine Benachrichtigung, sobald die Konfiguration abgeschlossen ist. 

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B. Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `BrittaSimon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Create**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Projectplace gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Projectplace**aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann am unteren Bildschirmrand auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-projectplace-test-user"></a>Erstellen eines Projectplace-Testbenutzers

>[!NOTE]
>Sie können diesen Schritt überspringen, wenn Sie die Bereitstellung in Projectplace aktiviert haben. Sie können das [Projectplace-Supportteam](https://success.planview.com/Projectplace/Support) bitten, die Bereitstellung zu aktivieren. Anschließend werden während der ersten Anmeldung Benutzer in Projectplace erstellt.

Damit sich Azure AD-Benutzer an Projectplace anmelden können, müssen Sie diese in Projectplace hinzufügen. Sie müssen diese manuell hinzufügen.

**Führen Sie zum Erstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei der **Projectplace** -Unternehmenswebsite als Administrator an.

2. Wechseln Sie zu **Personen**, und wählen Sie dann **Mitglieder** aus:
   
    ![Zu „Personen“ wechseln und dann „Mitglieder“ auswählen](./media/projectplace-tutorial/ic790228.png "Personen")

3. Wählen Sie **Mitglied hinzufügen** aus:
   
    ![„Mitglied hinzufügen“ auswählen](./media/projectplace-tutorial/ic790232.png "Mitglieder hinzufügen")

4. Führen Sie im Abschnitt **Mitglied hinzufügen** die folgenden Schritte aus.
   
    ![Abschnitt „Mitglied hinzufügen“](./media/projectplace-tutorial/ic790233.png "Neue Mitglieder")
   
    1. Geben Sie im Feld **Neue Mitglieder** die E-Mail-Adresse eines gültigen Azure AD-Kontos ein, das Sie hinzufügen möchten.
   
    1. Wählen Sie **Senden** aus.

   An den Besitzer des Azure AD-Kontos wird eine E-Mail mit einem Link zur Bestätigung des Kontos gesendet, bevor es aktiv wird.

>[!NOTE]
>Sie können auch beliebige andere Tools für die Erstellung von Benutzerkonten oder die von Projectplace bereitgestellte API verwenden, um Azure AD-Benutzerkonten hinzuzufügen.


### <a name="test-sso"></a>Testen des einmaligen Anmeldens

Wenn Sie im Zugriffsbereich die Kachel „Projectplace“ auswählen, sollten Sie automatisch bei der Projectplace-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Was ist der bedingte Zugriff in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)