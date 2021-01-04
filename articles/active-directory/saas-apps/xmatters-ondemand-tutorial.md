---
title: 'Tutorial: Azure Active Directory-Integration mit xMatters OnDemand | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und xMatters OnDemand konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/19/2020
ms.author: jeedes
ms.openlocfilehash: cbadf2e072cdd9bfdf64cb2b799355aada8ec4b0
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621173"
---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>Tutorial: Azure Active Directory-Integration mit xMatters OnDemand

In diesem Tutorial erfahren Sie, wie Sie xMatters OnDemand in Azure Active Directory (Azure AD) integrieren.
Die Integration von xMatters OnDemand in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf xMatters OnDemand hat.
* Sie können Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei xMatters OnDemand anzumelden (Single Sign-On, SSO; einmaliges Anmelden).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit xMatters OnDemand konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* xMatters OnDemand-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* xMatters OnDemand unterstützt **IDP**-initiiertes einmaliges Anmelden.

## <a name="adding-xmatters-ondemand-from-the-gallery"></a>Hinzufügen von xMatters OnDemand aus dem Katalog

Zum Konfigurieren der Integration von xMatters OnDemand in Azure AD müssen Sie xMatters OnDemand aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **xMatters OnDemand** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **xMatters OnDemand** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-xmatters-ondemand"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für xMatters OnDemand

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit xMatters OnDemand mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in xMatters OnDemand eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit xMatters OnDemand die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
    2. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
2. **[Konfigurieren des einmaligen Anmeldens für xMatters OnDemand](#configure-xmatters-ondemand-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines xMatters OnDemand-Testbenutzers](#create-xmatters-ondemand-test-user)**, um ein Pendant von Britta Simon in xMatters OnDemand zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
3. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **xMatters OnDemand** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Bezeichner** eine URL in einem der folgenden Formate ein:

    | Bezeichner |
    | ---------- |
    | `https://<companyname>.au1.xmatters.com.au/` |
    | `https://<companyname>.cs1.xmatters.com/` |
    | `https://<companyname>.xmatters.com/` |
    | `https://www.xmatters.com` |
    | `https://<companyname>.xmatters.com.au/` |

    b. Geben Sie im Textfeld **Antwort-URL** eine URL in einem der folgenden Formate ein:

    | Antwort-URL |
    | ---------- |
    |  `https://<companyname>.au1.xmatters.com.au` |
    | `https://<companyname>.xmatters.com/sp/<instancename>` |
    | `https://<companyname>.cs1.xmatters.com/sp/<instancename>` |
    | `https://<companyname>.au1.xmatters.com.au/<instancename>` |

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL. Wenden Sie sich an das [Kundensupportteam von xMatters OnDemand](https://www.xmatters.com/company/contact-us/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

    > [!IMPORTANT]
    > Sie müssen das Zertifikat an das [xMatters OnDemand-Supportteam](https://www.xmatters.com/company/contact-us/) weiterleiten. Das Zertifikat muss vom xMatters-Supportteam hochgeladen werden, bevor Sie die Konfiguration der einmaligen Anmeldung abschließen können.

6. Kopieren Sie im Abschnitt **xMatters OnDemand einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf xMatters OnDemand gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **xMatters OnDemand** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.


## <a name="configure-xmatters-ondemand-sso"></a>Konfigurieren des einmaligen Anmeldens für xMatters OnDemand

1. Melden Sie sich in einem anderen Webbrowserfenster bei der xMatters OnDemand-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf **Admin** (Verwaltung) und dann auf **Company Details** (Unternehmensdetails).

    ![Seite „Admin“ (Verwaltung)](./media/xmatters-ondemand-tutorial/admin.png "Admin")

3. Führen Sie auf der Seite **SAML-Konfiguration** die folgenden Schritte aus:

    ![Abschnitt für die SAML-Konfiguration ](./media/xmatters-ondemand-tutorial/saml-configuration.png "SAML-Konfiguration")

    a. Wählen Sie **SAML aktivieren**.

    b. Fügen Sie in das Textfeld **Identity Provider ID** (Identitätsanbieter-ID) den Wert von **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Textfeld **Single Sign On URL** (URL für einmaliges Anmelden) den Wert von **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Fügen Sie in das Textfeld **Logout URL Redirect** (Abmelde-URL-Umleitung) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    e. Klicken Sie auf **Choose File** (Datei auswählen), um das **Zertifikat (Base64)** hochzuladen, das Sie aus dem Azure-Portal heruntergeladen haben. 

    f. Klicken Sie oben auf der Seite "Firmendetails" auf **Änderungen speichern**.

    ![Details zum Unternehmen](./media/xmatters-ondemand-tutorial/save-button.png "Details zum Unternehmen")

### <a name="create-xmatters-ondemand-test-user"></a>Erstellen eines xMatters OnDemand-Testbenutzers

1. Melden Sie sich bei Ihrem **XMatters OnDemand**-Mandanten an.

2. Navigieren Sie zu **Benutzersymbol** > **Users** (Benutzer), und klicken Sie auf **Add Users** (Benutzer hinzufügen).

    ![Benutzer](./media/xmatters-ondemand-tutorial/add-user.png "Benutzer")

3. Füllen Sie im Abschnitt **Add Users** (Benutzer hinzufügen) die erforderlichen Felder aus, und klicken Sie auf **Add User** (Benutzer hinzufügen).

    ![Benutzer hinzufügen](./media/xmatters-ondemand-tutorial/add-user-2.png "Benutzer hinzufügen")



### <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der xMatters OnDemand-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „xMatters OnDemand“ klicken, sollten Sie automatisch bei der xMatters OnDemand-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von xMatters OnDemand können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.
