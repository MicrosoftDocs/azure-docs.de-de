---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit NetDocuments | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und NetDocuments konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.openlocfilehash: b9a85c7093d0f42ac97c4a548ba6f0feebae8b89
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350720"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit NetDocuments

In diesem Tutorial erfahren Sie, wie Sie NetDocuments in Azure Active Directory (Azure AD) integrieren. Die Integration von NetDocuments in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf NetDocuments hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei NetDocuments anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein NetDocuments-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* NetDocuments unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-netdocuments-from-the-gallery"></a>Hinzufügen von NetDocuments aus dem Katalog

Zum Konfigurieren der Integration von NetDocuments in Azure AD müssen Sie NetDocuments aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **NetDocuments** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **NetDocuments** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netdocuments"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für NetDocuments

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit NetDocuments mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in NetDocuments eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit NetDocuments die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für NetDocuments](#configure-netdocuments-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines NetDocuments-Testbenutzers](#create-netdocuments-test-user)** , um eine Entsprechung von B. Simon in NetDocuments zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **NetDocuments** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`
    
    c. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `http://netdocuments.com/VAULT`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächlichen Werte für Anmelde-URL und Antwort-URL. Die Repository-ID ist ein Wert, der mit **CA-** beginnt, gefolgt von einem Code aus acht Zeichen, der Ihrem NetDocuments-Repository zugeordnet ist. Weitere Informationen können Sie dem [Supportdokument zur Verbundidentität von NetDocuments](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login) entnehmen. Sie können sich auch an das [Kundensupportteam von NetDocuments](https://support.netdocuments.com/hc/) wenden, um diese Werte zu erhalten, falls beim Konfigurieren mit den oben angegebenen Informationen Schwierigkeiten auftreten sollten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die NetDocuments-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. **nameidentifier** ist hier **user.userprincipalname** zugeordnet. Die NetDocuments-Anwendung erwartet, dass **nameidentifier** dem Eintrag **employeeid** oder einem anderen Anspruch zugeordnet wird, der als **nameidentifier** für Ihre Organisation fungiert. Bearbeiten Sie daher die Attributzuordnung, indem Sie auf das Symbol **Bearbeiten** klicken und die Attributzuordnung ändern.

    ![image](common/edit-attribute.png)

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **NetDocuments einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf NetDocuments gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **NetDocuments** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-netdocuments-sso"></a>Konfigurieren des einmaligen Anmeldens für NetDocuments

1. Melden Sie sich in einem anderen Webbrowserfenster bei der NetDocuments-Unternehmenswebsite als Administrator an.

2. Wählen Sie in der Ecke oben rechts Ihren Namen und dann **Admin** aus.

3. Wählen Sie **Security Center** aus.
   
    ![Screenshot: Option „Security Center“ unter „Legal Documents“ (Rechtliche Dokumente) ausgewählt](./media/netdocuments-tutorial/security-center.png "Security Center")

4. Wählen Sie **Advanced Authentication** (Erweiterte Authentifizierung) aus.
    
    ![Erweiterte Authentifizierungsoptionen konfigurieren](./media/netdocuments-tutorial/advance-authentication.png "Erweiterte Authentifizierungsoptionen konfigurieren")

5.  Führen Sie auf der Registerkarte **Federated ID** (Verbund-ID) die folgenden Schritte aus:   
   
    ![Verbundidentität](./media/netdocuments-tutorial/federated-id.png "Verbundidentität")
   
    a. Wählen Sie als **Servertyp für Identitätsverbund** die Option **Active Directory-Verbunddienste** aus.
    
    b.  Wählen Sie **Choose File** (Datei auswählen) aus, um die Metadatendatei hochzuladen, die Sie über das Azure-Portal heruntergeladen haben.
    
    c.  Wählen Sie **SAVE** (SPEICHERN) aus.

### <a name="create-netdocuments-test-user"></a>Erstellen eines NetDocuments-Testbenutzers

Damit sich Azure AD-Benutzer bei NetDocuments anmelden können, müssen sie in NetDocuments bereitgestellt werden. Im Fall von NetDocuments ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrer **NetDocuments**-Unternehmenswebsite als Administrator an.

2. Wählen Sie in der Ecke oben rechts Ihren Namen und dann **Admin** aus.
   
    ![Administrator](./media/netdocuments-tutorial/user-admin.png "Admin")

3. Wählen Sie **Benutzer und Gruppen**.
   
    ![Screenshot: Option „Users & Groups“ (Benutzer und Gruppen) unter „Legal Documents“ (Rechtliche Dokumente) ausgewählt](./media/netdocuments-tutorial/users-groups.png "Repository")

4. Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse eines gültigen Azure Active Directory-Kontos ein, das Sie bereitstellen möchten, und klicken Sie auf **Benutzer hinzufügen**.
   
    ![E-Mail-Adresse](./media/netdocuments-tutorial/user-mail.png "E-Mail-Adresse")
   
    > [!NOTE]
    > Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird. Sie können Azure Active Directory-Benutzerkonten auch mithilfe von anderen Tools zum Erstellen von NetDocuments-Benutzerkonten oder mithilfe der von NetDocuments bereitgestellten APIs erstellen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „NetDocuments“ klicken, sollten Sie automatisch bei der NetDocuments-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [NetDocuments mit Azure AD ausprobieren](https://aad.portal.azure.com/)