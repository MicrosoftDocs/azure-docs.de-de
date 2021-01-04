---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Roadmunk'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Roadmunk konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: a7f4682be2f7fbf308aba32768efa932f27b7a87
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181701"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-roadmunk"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Roadmunk

In diesem Tutorial erfahren Sie, wie Sie Roadmunk in Azure Active Directory (Azure AD) integrieren. Die Integration von Roadmunk in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Roadmunk hat.
* Ermöglichen Sie Ihren Benutzern, sich mit ihrem Azure AD-Konto automatisch bei Roadmunk anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Roadmunk-Abonnement, das für einmaliges Anmelden aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

Roadmunk unterstützt einmaliges *SP (Service Provider)* - und *IDP (Identity Provider)* -initiiertes Anmelden.

## <a name="add-roadmunk-from-the-gallery"></a>Hinzufügen von Roadmunk aus dem Katalog

Um Roadmunk in Azure AD zu integrieren, fügen Sie Roadmunk aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzu:

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Bereich **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Roadmunk** im Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Roadmunk** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-roadmunk"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Roadmunk

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Roadmunk mithilfe eines Testbenutzers mit dem Namen *B. Simon*. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Roadmunk eingerichtet werden.

Nachfolgend finden Sie eine Übersicht über das Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Roadmunk:

1. [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso), damit Ihre Benutzer dieses Feature verwenden können
    1. [Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user), um das einmalige Anmelden von Azure AD mithilfe von B. Simon zu testen.
    1. [Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user), um B.Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen
1. [Konfigurieren des einmaliges Anmeldens für Roadmunk](#configure-roadmunk-sso), um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. [Erstellen eines Roadmunk-Testbenutzers](#create-roadmunk-test-user), damit Sie die Entsprechung von B. Simon in Roadmunk mit der Darstellung des Benutzers in Azure AD verknüpfen können.
1. [Testen des einmaligen Anmeldens](#test-sso), um sicherzustellen, dass die Konfiguration funktioniert.

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren:

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Roadmunk** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die Einstellungen zu bearbeiten.

   ![Screenshot mit dem Symbol „Bearbeiten“ für die grundlegende SAML-Konfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie eine SP-Metadatendatei haben und den IDP-initiierten Modus konfigurieren möchten:

    a. Wählen Sie **Metadatendatei hochladen** aus.

    ![Screenshot mit dem Link zum Hochladen der Metadatendatei](common/upload-metadata.png)

    b. Wählen Sie das Ordnersymbol aus, um die Metadatendatei auszuwählen, die Sie in Schritt 4 des Verfahrens „Konfigurieren des einmaligen Anmeldens für Roadmunk“ heruntergeladen haben. Wählen Sie dann die Option **Hochladen** aus.

    ![Screenshot: Auswählen der Metadatendatei](common/browse-upload-metadata.png)

    Nach dem erfolgreichen Hochladen der Metadatendatei werden im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für **Bezeichner** und **Antwort-URL** automatisch eingefügt.

    ![Screenshot: Abschnitt „Grundlegende SAML-Konfiguration“. Die Felder „Bezeichner“ und „Antwort-URL“ sind hervorgehoben.](common/idp-intiated.png)

    > [!Note]
    > Werden die Werte für **Bezeichner** und **Antwort-URL** nicht automatisch aufgefüllt, geben Sie diese manuell ein.

1. Wenn Sie die Anwendung im SP-initiierten Modus konfigurieren möchten, wählen Sie **Zusätzliche URLs festlegen** aus. Geben Sie im Feld **Anmelde-URL** die URL `https://login.roadmunk.com` ein.

    ![Der Screenshot zeigt, wo eine Anmelde-URL für den SP-initiierten Modus festgelegt wird](common/metadata-upload-additional-signon.png)

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**. Wählen Sie anschließend **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Screenshot: Download-Link für das SAML-Signaturzertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Roadmunk einrichten** die URL(s), die Sie benötigen.

    ![Der Screenshot zeigt, wo die URLs der Konfiguration kopiert werden.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer. Nennen Sie den Benutzer *B. Simon*.

1. Wählen Sie im Azure-Portal im linken Bereich **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Fenster die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Geben Sie z. B. `B.Simon@contoso.com` ein
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf Roadmunk gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Roadmunk** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Klicken Sie auf **Benutzer hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste **Benutzer** die Option **B. Simon** aus. Wählen Sie unten im Dialogfeld **Auswählen** aus.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie diese im Dropdownmenü **Rolle auswählen** auswählen. Wird für diese App keine Rolle eingerichtet, wird die Rolle **Standardzugriff** ausgewählt.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

## <a name="configure-roadmunk-sso"></a>Konfigurieren des einmaligen Anmeldens für Roadmunk

1. Melden Sie sich bei der Roadmunk-Website als Administrator an.

1. Wählen Sie am unteren Rand der Seite das Benutzersymbol aus, und wählen Sie dann **Kontoeinstellungen** aus.

    ![Der Screenshot zeigt, wo die Einstellungen des Benutzerkontos ausgewählt werden](./media/roadmunk-tutorial/account.png)

1. Navigieren Sie zu **Unternehmen** > **Authentifizierungseinstellungen**.

1. Führen Sie auf der Seite **Authentifizierungseinstellungen** die folgenden Schritte aus:

    ![Screenshot: Seite „Authentifizierungseinstellungen“](./media/roadmunk-tutorial/saml-sso.png)

    a. Aktivieren Sie **Einmaliges Anmelden (SSO) mit SAML**.

    b. Laden Sie im Abschnitt **Schritt 1** entweder die XML-Metadatendatei hoch, oder geben Sie die URL für die Metadaten an.

    c. Laden Sie im Abschnitt **Schritt 2** die Roadmunk-Metadatendatei herunter, und speichern Sie sie auf Ihrem Computer.

    d. Wenn Sie sich mit dem einmaligen Anmelden anmelden möchten, wählen Sie im Abschnitt **Schritt 3** die Option **Nur SAML-Anmeldung erzwingen** aus.

    e. Wählen Sie **Speichern** aus.


### <a name="create-roadmunk-test-user"></a>Erstellen eines Roadmunk-Testbenutzers

1. Melden Sie sich bei der Roadmunk-Website als Administrator an.

1. Wählen Sie am unteren Rand der Seite das Benutzersymbol aus, und wählen Sie dann **Kontoeinstellungen** aus.

    ![Der Screenshot zeigt, wie die Kontoeinstellungen für den Testbenutzer geöffnet werden](./media/roadmunk-tutorial/account.png)

1. Öffnen Sie die Registerkarte **Benutzer**, und wählen Sie dann **Benutzer einladen** aus.

    ![Screenshot: Registerkarte „Benutzer“. Die Schaltfläche „Benutzer einladen“ ist hervorgehoben. Im geöffneten Fenster sind die Felder „E-Mail-Adresse“ und „Rolle“ hervorgehoben.](./media/roadmunk-tutorial/create-user.png)

1. Geben Sie in dem angezeigten Formular die erforderlichen Informationen ein, und wählen Sie dann **Einladen** aus.


## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden (SSO) über den Zugriffsbereich.

Wenn Sie im Portal „Meine Apps“ die Kachel **Roadmunk** auswählen, sollten Sie automatisch bei dem Roadmunk-Konto angemeldet werden, für das Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen finden Sie unter [Anmelden beim Portal „Meine Apps“ und Starten von Apps über dieses](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Roadmunk können Sie die Sitzungssteuerung erzwingen. Die Sitzungssteuerung schützt in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten. Die Sitzungssteuerung basiert auf bedingtem Zugriff. 

Erfahren Sie, wie Sie die [Sitzungssteuerung mit Microsoft Cloud App Security erzwingen](/cloud-app-security/proxy-deployment-any-app).