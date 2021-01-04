---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Pulse Secure PCS | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Pulse Secure PCS konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: jeedes
ms.openlocfilehash: d38ff5c8f33bda5b12f6267e7a8cdf477db6c7d7
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92511442"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-pcs"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Pulse Secure PCS

In diesem Tutorial erfahren Sie, wie Sie Pulse Secure PCS in Azure Active Directory (Azure AD) integrieren. Die Integration von Pulse Secure PCS in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Pulse Secure PCS hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Pulse Secure PCS anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Pulse Secure PCS-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Pulse Secure PCS unterstützt **SP** -initiiertes einmaliges Anmelden.

## <a name="adding-pulse-secure-pcs-from-the-gallery"></a>Hinzufügen von Pulse Secure PCS aus dem Katalog

Zum Konfigurieren der Integration von Pulse Secure PCS in Azure AD müssen Sie Pulse Secure PCS aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** im Suchfeld den Begriff **Pulse Secure PCS** ein.
1. Wählen Sie im Ergebnisbereich **Pulse Secure PCS** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-pcs"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Pulse Secure PCS

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Pulse Secure PCS mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Pulse Secure PCS eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Pulse Secure PCS die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Pulse Secure PCS](#configure-pulse-secure-pcs-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Pulse Secure PCS-Testbenutzers](#create-pulse-secure-pcs-test-user)** , um eine Entsprechung von B. Simon in Pulse Secure PCS zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Pulse Secure PCS** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<FQDN of PCS>/dana-na/auth/saml-consumer.cgi`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<FQDN of PCS>/dana-na/auth/saml-endpoint.cgi?p=sp1`.

    c. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://[FQDN of PCS]/dana-na/auth/saml-consumer.cgi`


    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächlichen Werte für die Anmelde-URL, die Antwort-URL und den Bezeichner. Diese Werte erhalten Sie vom [Supportteam für den Pulse Secure PCS-Client](mailto:support@pulsesecure.net). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Pulse Secure PCS einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Pulse Secure PCS gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Pulse Secure PCS** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-pulse-secure-pcs-sso"></a>Konfigurieren des einmaligen Anmeldens für Pulse Secure PCS

In diesem Abschnitt werden die SAML-Konfigurationen erläutert, die zum Konfigurieren von PCS als SAML-Dienstanbieter erforderlich sind. Die anderen grundlegenden Konfigurationen (etwa das Erstellen von Bereichen und Rollen) werden nicht behandelt.

**Zu den Pulse Connect Secure-Konfigurationen gehört Folgendes:**

* Konfigurieren von Azure AD als SAML-Metadatenanbieter
* Konfigurieren des SAML-Authentifizierungsservers
* Zuweisen zu den jeweiligen Bereichen und Rollen

#### <a name="configuring-azure-ad-as-saml-metadata-provider"></a>Konfigurieren von Azure AD als SAML-Metadatenanbieter

Führen Sie auf der folgenden Seite die folgenden Schritte aus:

![Pulse Connect Secure-Konfiguration](./media/pulse-secure-pcs-tutorial/saml-configuration.png)

1. Melden Sie sich bei der Verwaltungskonsole von Pulse Connect Secure an.
1. Navigieren Sie zu **System > Konfiguration > SAML**.
1. Klicken Sie auf **New Metadata Provider** (Neuer Metadatenanbieter).
1. Geben Sie im Textfeld **Name** einen gültigen Namen an.
1. Laden Sie die aus dem Azure-Portal heruntergeladene XML-Metadatendatei in die **Azure AD-Metadatendatei** hoch.
1. Wählen Sie **Accept Unsigned Metadata** (Nicht signierte Metadaten akzeptieren) aus.
1. Wählen Sie für Rollen **Identity Provider** (Identitätsanbieter) aus.
1. Klicken Sie auf **Save changes** (Änderungen speichern).

#### <a name="steps-to-create-a-saml-auth-server"></a>Schritte zum Erstellen eines SAML-Authentifizierungsservers:

1. Navigieren Sie zu **Authentication > Auth Servers** (Authentifizierung > Authentifizierungsserver).
1. Wählen Sie **New: SAML Server** (Neu: SAML-Server) aus, und klicken Sie auf **New Server** (Neuer Server).

    ![Pulse Connect Secure-Authentifizierungsserver](./media/pulse-secure-pcs-tutorial/new-saml-server.png)

1. Führen Sie auf der Seite „Settings“ (Einstellungen) die folgenden Schritte aus:

    ![Einstellungen des Pulse Connect Secure-Authentifizierungsservers](./media/pulse-secure-pcs-tutorial/server-settings.png)

    a. Geben Sie im Textfeld **Server Name** (Servername) einen Namen an.

    b. Wählen Sie die **SAML-Version 2.0** und unter **Configuration Mode** (Konfigurationsmodus) die Option **Metadata** (Metadaten) aus.

    c. Kopieren Sie den Wert für **Connect Secure Entity Id** (Connect Secure-Entitäts-ID), und fügen Sie ihn im Azure-Portal im Dialogfeld **Grundlegende SAML-Konfiguration** in das Feld **Bezeichner-URL** ein.

    d. Wählen Sie in der Dropdownliste **Identity Provider Entity Id** (Entitäts-ID des Identitätsanbieters) den Wert der Azure AD-Entitäts-ID aus.

    e. Wählen Sie in der Dropdownliste **Identity Provider Single Sign-On Service URL** (Dienst-URL für einmaliges Anmelden des Identitätsanbieters) den Wert der Azure AD-Anmelde-URL aus.

    f. **Single Logout** (Einmaliges Abmelden) ist eine optionale Einstellung. Ist diese Option aktiviert, wird nach der Abmeldung eine neue Authentifizierung angefordert. Wenn diese Option nicht aktiviert ist und Sie den Browser nicht geschlossen haben, können Sie die Verbindung ohne Authentifizierung erneut herstellen.

    g. Legen Sie für die **angeforderte Authentifizierungskontextklasse** die Option **Password** (Kennwort) und als **Vergleichsmethode** die Option **exact** (genau) fest.

    h. Legen Sie für **Metadata Validity** (Metadatengültigkeit) eine Anzahl von Tagen fest.
    
    i. Klicken Sie auf **Save Changes** (Änderungen speichern).

### <a name="create-pulse-secure-pcs-test-user"></a>Erstellen eines Pulse Secure PCS-Testbenutzers

In diesem Abschnitt erstellen Sie in Pulse Secure PCS einen Benutzer namens Britta Simon. Wenden Sie sich an das [Supportteam von Pulse Secure PCS](mailto:support@pulsesecure.net), um die Benutzer auf der Pulse Secure PCS-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

1. Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Pulse Secure PCS weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

2. Rufen Sie direkt die Pulse Secure PCS-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

3. Sie können den Microsoft-Zugriffsbereich verwenden. Wenn Sie im Zugriffsbereich auf die Kachel „Pulse Secure PCS“ klicken, werden Sie zur Anmelde-URL für Pulse Secure PCS umgeleitet. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Pulse Secure PCS können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.