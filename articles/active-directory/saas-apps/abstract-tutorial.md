---
title: 'Tutorial: Azure Active Directory-Integration mit Abstract | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Abstract konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.openlocfilehash: e1c3236c4c1957b4d0daee8d30c71f03fb8674dd
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587824"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>Tutorial: Integrieren von Abstract in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Abstract in Azure Active Directory (Azure AD) integrieren. Die Integration von Abstract in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Abstract hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Abstract anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Abstract-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Abstract unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

## <a name="adding-abstract-from-the-gallery"></a>Hinzufügen von Abstract aus dem Katalog

Zum Konfigurieren der Integration von Abstract in Azure AD müssen Sie Abstract aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Abstract** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Abstract** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Abstract mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Abstract eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Abstract müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Abstract](#configure-abstract-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Abstract-Testbenutzers](#create-abstract-test-user)** , um ein Pendant von Britta Simon in Abstract zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Abstract** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Im Abschnitt **Grundlegende SAML-Konfiguration** ist die Anwendung im **IDP-initiierten** Modus vorkonfiguriert, und die erforderlichen URLs sind bereits mit Azure vorausgefüllt. Der Benutzer muss die Konfiguration speichern, indem er auf die Schaltfläche **Speichern** klickt.

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL ein: `https://app.abstract.com/signin`.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>Konfigurieren des einmaligen Anmeldens für Abstract

Rufen Sie unbedingt die Werte für `App Federation Metadata Url` und `Azure AD Identifier` aus dem Azure-Portal ab, da Sie sie zum Konfigurieren des einmaligen Anmeldens für Abstract benötigen.

Diese Informationen finden Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten**:

* Der Wert für `App Federation Metadata Url` befindet sich im Abschnitt **SAML-Signaturzertifikat**.
* `Azure AD Identifier` befindet sich im Abschnitt **Abstract einrichten**.


Sie können nun einmaliges Anmelden für Abstract konfigurieren:

>[!Note]
>Sie müssen sich mit dem Administratorkonto einer Organisation authentifizieren, um auf die SSO-Einstellungen für Abstract zuzugreifen.

1. Öffnen Sie die [Abstract-Web-App](https://app.abstract.com/).
2. Navigieren Sie in der linken Seitenleiste zur Seite **Permissions** (Berechtigungen).
3. Geben Sie im Abschnitt **Configure SSO** (SSO konfigurieren) die Werte für **Metadata URL** (Metadaten-URL) und **Entity ID** (Entitäts-ID) ein.
4. Geben Sie ggf. manuelle Ausnahmen ein. Für die im Abschnitt für manuelle Ausnahmen aufgeführten E-Mail-Adressen wird SSO umgangen, und Benutzer können sich mit E-Mail und Kennwort anmelden. 
5. Klicken Sie auf **Änderungen speichern**.

>[!Note] 
>In der Liste für manuelle Ausnahmen müssen primäre E-Mail-Adressen verwendet werden. Bei der Aktivierung von SSO tritt ein Fehler auf, wenn es sich bei der aufgeführten E-Mail-Adresse um die sekundäre E-Mail-Adresse eines Benutzers handelt. In diesem Fall wird eine Fehlermeldung mit der primären E-Mail-Adresse für das Konto angezeigt, bei dem der Fehler auftritt. Fügen Sie die primäre E-Mail-Adresse zur Liste manueller Ausnahmen hinzu, nachdem Sie sich vergewissert haben, dass Sie den Benutzer kennen.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Abstract gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Abstract** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-abstract-test-user"></a>Erstellen eines Abstract-Testbenutzers

So testen Sie einmaliges Anmelden für Abstract:

1. Öffnen Sie die [Abstract-Web-App](https://app.abstract.com/).
2. Navigieren Sie in der linken Seitenleiste zur Seite **Permissions** (Berechtigungen).
3. Klicken Sie auf **Test with my Account** (Mit meinem Konto testen). Tritt beim Test ein Fehler auf, [wenden Sie sich an unser Supportteam](https://help.abstract.com/hc/).

>[!Note]
>Sie müssen sich mit dem Administratorkonto einer Organisation authentifizieren, um auf die SSO-Einstellungen für Abstract zuzugreifen.
Dieses Organisationsadministratorkonto muss im Azure-Portal Abstract zugewiesen werden.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Abstract“ klicken, sollten Sie automatisch bei der Abstract-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)