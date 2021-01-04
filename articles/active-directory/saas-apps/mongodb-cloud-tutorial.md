---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit MongoDB Cloud | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und MongoDB Cloud konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: af1c0702929e7cd700c8d19ab24e40f9c6f43a21
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602171"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit MongoDB Cloud

In diesem Tutorial erfahren Sie, wie Sie MongoDB Cloud in Azure Active Directory (Azure AD) integrieren. Die Integration von MongoDB Cloud in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf MongoDB Cloud, MongoDB Atlas, MongoDB Community, MongoDB University und den MongoDB Support hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei MongoDB Cloud anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps (Software as a Service) mit Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Eine MongoDB Cloud-Organisation, für die einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist. Sie können sich für einen [kostenlosen Cluster](https://www.mongodb.com/cloud) registrieren.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* MongoDB Cloud unterstützt **SP**- und **IDP**-initiiertes einmaliges Anmelden.
* MongoDB Cloud unterstützt die **Just-In-Time**-Bereitstellung von Benutzern.
* Nach dem Konfigurieren von MongoDB Cloud können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. Weitere Informationen finden Sie im Artikel zum [Erzwingen der Sitzungssteuerung mit Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mongodb-cloud-from-the-gallery"></a>Hinzufügen von MongoDB Cloud aus dem Katalog

Zum Konfigurieren der Integration von MongoDB Cloud in Azure AD müssen Sie MongoDB Cloud aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Bereich **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **MongoDB Cloud** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **MongoDB Cloud** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-single-sign-on-for-mongodb-cloud"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für MongoDB Cloud

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit MongoDB Cloud mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in MongoDB Cloud eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit MongoDB Cloud die folgenden Schritte aus:

1. [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso), um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
    1. [Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user), um das einmalige Anmelden von Azure AD mit B.Simon zu testen
    1. [Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user), um B.Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen
1. [Konfigurieren des einmaligen Anmeldens für MongoDB Cloud](#configure-mongodb-cloud-sso), um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. [Erstellen eines MongoDB Cloud-Testbenutzers](#create-a-mongodb-cloud-test-user), um eine Entsprechung von B. Simon in MongoDB Cloud zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. [Testen des einmaligen Anmeldens](#test-sso), um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **MongoDB Cloud** zum Abschnitt **Verwalten**. Wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die Einstellungen zu bearbeiten.

   ![Screenshot: Seite „Einmaliges Anmelden (SSO) mit SAML einrichten“ mit hervorgehobenem Stiftsymbol](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://www.okta.com/saml2/service-provider/<Customer_Unique>`.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://auth.mongodb.com/sso/saml2/<Customer_Unique>`.

1. Wählen Sie **Zusätzliche URLs festlegen** aus, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten** Modus konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://cloud.mongodb.com/sso/<Customer_Unique>`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Diese Werte erhalten Sie vom [Supportteam für den MongoDB Cloud-Client](https://support.mongodb.com/). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Ihre MongoDB Cloud-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![Screenshot der Standardattribute](common/default-attributes.png)

1. Von der MongoDB Cloud-Anwendung wird erwartet, dass in der SAML-Antwort zusätzlich zu den oben genannten Attributen noch einige weitere Attribute zurückgegeben werden. Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überarbeiten.
    
    | Name | Quellattribut|
    | ---------------| --------- |
    | email | user.userprincipalname |
    | firstName | user.givenname |
    | lastName | user.surname |

1. Suchen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** nach **Verbundmetadaten-XML**. Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Screenshot: Abschnitt „SAML-Signaturzertifikat“ mit hervorgehobenem Downloadlink](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **MongoDB Cloud einrichten** die entsprechenden URLs basierend auf Ihren Anforderungen.

    ![Screenshot: Abschnitt „MongoDB Cloud einrichten“ mit hervorgehobenen URLs](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer namens B.Simon.

1. Wählen Sie im Azure-Portal im linken Bereich **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich das Kennwort.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf MongoDB Cloud gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **MongoDB Cloud** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

   ![Screenshot: Abschnitt „Verwalten“ mit hervorgehobener Option „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Klicken Sie auf **Benutzer hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Screenshot: Seite „Benutzer und Gruppen“ mit hervorgehobener Option zum Hinzufügen eines Benutzers](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste der Benutzer die Option **B.Simon** aus. Wählen Sie anschließend am unteren Bildschirmrand **Auswählen** aus.
1. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Wählen Sie anschließend am unteren Bildschirmrand **Auswählen** aus.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

## <a name="configure-mongodb-cloud-sso"></a>Konfigurieren des einmaligen Anmeldens für MongoDB Cloud

Zum Konfigurieren des einmaligen Anmeldens aufseiten von MongoDB Cloud benötigen Sie die entsprechenden URLs aus dem Azure-Portal. Darüber hinaus müssen Sie die Verbundanwendung für Ihre MongoDB Cloud-Organisation konfigurieren. Befolgen Sie die Anweisungen in der [Dokumentation zu MongoDB Cloud](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/). Sollte ein Problem auftreten, wenden Sie sich an das [MongoDB Cloud-Supportteam](https://support.mongodb.com/).

### <a name="create-a-mongodb-cloud-test-user"></a>Erstellen eines MongoDB Cloud-Testbenutzers

MongoDB Cloud unterstützt die Just-In-Time-Benutzerbereitstellung (standardmäßig aktiviert). Es ist keine weitere Aktion Ihrerseits erforderlich. Ist ein Benutzer noch nicht in MongoDB Cloud vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich die Kachel „MongoDB Cloud“ auswählen, werden Sie automatisch bei der MongoDB Cloud-Instanz angemeldet, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen finden Sie unter [Anmelden beim Portal „Meine Apps“ und Starten von Apps über dieses](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Tutorials zur Integration von SaaS-Anwendungen in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [Registrieren für MongoDB Atlas in Azure](https://azuremarketplace.microsoft.com/marketplace/apps/mongodb.mdb_atlas_oct2020?tab=Overview)

- [MongoDB Cloud mit Azure AD ausprobieren](https://aad.portal.azure.com/)

- [Was ist Sitzungssteuerung in Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Schützen von Apps mit der App-Steuerung für bedingten Zugriff von Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)