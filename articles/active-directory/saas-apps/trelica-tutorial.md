---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Trelica | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Trelica konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.openlocfilehash: a674f5f653ad420ab8f28ff73c6b86f9c18b154e
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92517751"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trelica"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Trelica

In diesem Tutorial erfahren Sie, wie Sie Trelica in Azure Active Directory (Azure AD) integrieren.

Diese Integration ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Trelica hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Trelica anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps (Software as a Service) mit Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Trelica-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Trelica unterstützt IDP-initiiertes einmaliges Anmelden.
* Trelica unterstützt die Just-In-Time-Benutzerbereitstellung.
* Nach dem Konfigurieren von Trelica können Sie die Sitzungssteuerung erzwingen. Diese Steuerung schützt in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.

## <a name="adding-trelica-from-the-gallery"></a>Hinzufügen von Trelica über den Katalog

Zum Konfigurieren der Integration von Trelica in Azure AD müssen Sie Trelica über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im Navigationsbereich ganz links den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Trelica** in das Suchfeld ein.
1. Wählen Sie in den Suchergebnissen **Trelica** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trelica"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Trelica

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Trelica mithilfe einer Testbenutzerin mit dem Namen **B. Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Trelica eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Trelica die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Trelica](#configure-trelica-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Trelica-Testbenutzers](#create-a-trelica-test-user)** , um eine Entsprechung von B. Simon in Trelica zu erhalten Diese Entsprechung ist mit der Benutzerdarstellung in Azure AD verknüpft.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Trelica** zum Abschnitt **Verwalten** . Wählen Sie **Einmaliges Anmelden** .
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Seite „Einmaliges Anmelden (SSO) mit SAML einrichten“ mit hervorgehobenem Stiftsymbol für „Grundlegende SAML-Konfiguration“](common/edit-urls.png)

1. Geben Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die folgenden Werte ein:

    1. Geben Sie im Feld **Bezeichner** die URL **https://app.trelica.com** ein.

    1. Geben Sie im Feld **Antwort-URL** eine URL im folgenden Format ein: `https://app.trelica.com/Id/Saml2/<CUSTOM_IDENTIFIER>/Acs`.

    > [!NOTE]
    > Der Wert der Antwort-URL entspricht nicht dem tatsächlichen Wert. Aktualisieren Sie diesen Wert mit der richtigen Antwort-URL (auch als ACS bezeichnet).
    > Diese finden Sie, indem Sie sich bei Trelica anmelden und zur [Konfigurationsseite des SAML-Identitätsanbieters](https://app.trelica.com/Admin/Profile/SAML) (Administrator > Konto > SAML) wechseln. Klicken Sie auf die Schaltfläche „Kopieren“ neben der **Assertion Consumer Service-URL (ACS)** , um diese in die Zwischenablage zu kopieren und sie dann im Textfeld **Antwort-URL** in Azure AD einzufügen.
    > Lesen Sie die [Trelica-Hilfedokumentation](https://docs.trelica.com/admin/saml/azure-ad), oder wenden Sie sich bei Fragen an das [Trelica-Clientsupportteam](mailto:support@trelica.com).

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Abschnitt „SAML-Signaturzertifikat“ mit hervorgehobener Kopierschaltfläche neben „Verbundmetadaten-XML“](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon:

1. Wählen Sie im Bereich ganz links im Azure-Portal **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie **B.Simon** in das Feld **Name** ein.
   1. Geben Sie im Feld **Benutzername** Folgendes ein: **B.Simon@** _Unternehmensdomäne_ **.** _Erweiterung_ . Beispiel: B.Simon@contoso.com.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.
   1. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Trelica gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Trelica** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

   ![Abschnitt „Verwalten“ mit hervorgehobener Option „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Klicken Sie auf **Benutzer hinzufügen** . Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

   ![Fenster „Benutzer und Gruppen“ mit hervorgehobener Option zum Hinzufügen eines Benutzers](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ die Option **B.Simon** aus. Wählen Sie dann unten auf dem Bildschirm die Schaltfläche **Auswählen** aus.
1. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Wählen Sie dann unten auf dem Bildschirm die Schaltfläche **Auswählen** aus.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

## <a name="configure-trelica-sso"></a>Konfigurieren des einmaligen Anmeldens für Trelica

Um auf Seiten von **Trelica** das einmalige Anmelden zu konfigurieren, wechseln Sie zur [Konfigurationsseite des SAML-Identitätsanbieters](https://app.trelica.com/Admin/Profile/SAML) (Administrator > Konto > SAML). Klicken Sie auf die Schaltfläche **Neu** . Geben Sie **Azure AD** als Namen ein, und wählen Sie **Metadaten von URL** als Metadatentyp aus. Fügen Sie in Trelica im Feld **Metadaten-URL** die **App-Verbundmetadaten-URL** ein, die Sie Azure AD entnommen haben.

Lesen Sie die [Trelica-Hilfedokumentation](https://docs.trelica.com/admin/saml/azure-ad), oder wenden Sie sich bei Fragen an das [Trelica-Clientsupportteam](mailto:support@trelica.com).

### <a name="create-a-trelica-test-user"></a>Erstellen eines Trelica-Testbenutzers

Trelica unterstützt die Just-In-Time-Benutzerbereitstellung (standardmäßig aktiviert). In diesem Abschnitt ist keine Aktion erforderlich. Ist ein Benutzer noch nicht in Trelica vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mithilfe des Portals „Meine Apps“.

Wenn Sie im Zugriffsbereich die Kachel „Trelica“ auswählen, werden Sie automatisch bei der Trelica-Instanz angemeldet, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Portal „Meine Apps“ finden Sie unter [Anmelden beim Portal „Meine Apps“ und Starten von Apps über dieses](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [Trelica mit Azure AD ausprobieren](https://aad.portal.azure.com/)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Schützen von Apps mit der App-Steuerung für bedingten Zugriff von Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)