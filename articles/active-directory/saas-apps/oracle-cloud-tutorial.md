---
title: 'Tutorial: Azure Active Directory-Integration mit Oracle Cloud Infrastructure Console | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Oracle Cloud Infrastructure Console konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2020
ms.author: jeedes
ms.openlocfilehash: 914858471432979e5516a1f075ed4fcbb8061a5d
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181848"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>Tutorial: Integrieren von Oracle Cloud Infrastructure Console in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Oracle Cloud Infrastructure Console in Azure Active Directory (Azure AD) integrieren. Die Integration von Oracle Cloud Infrastructure Console in Azure AD bietet folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Oracle Cloud Infrastructure Console haben soll.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei Oracle Cloud Infrastructure Console anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* SSO-fähiges Oracle Cloud Infrastructure Console-Abonnement

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Oracle Cloud Infrastructure Console unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>Hinzufügen von Oracle Cloud Infrastructure Console über den Katalog

Um die Integration von Oracle Cloud Infrastructure Console in Azure AD zu konfigurieren, müssen Sie Oracle Cloud Infrastructure Console über den Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Oracle Cloud Infrastructure Console** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich die Option **Oracle Cloud Infrastructure Console** aus, und fügen Sie die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurieren und Testen des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Oracle Cloud Infrastructure Console mithilfe eines Testbenutzers namens **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Oracle Cloud Infrastructure Console eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Oracle Cloud Infrastructure Console zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Konfigurieren von Oracle Cloud Infrastructure Console](#configure-oracle-cloud-infrastructure-console)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Oracle Cloud Infrastructure Console-Testbenutzers](#create-oracle-cloud-infrastructure-console-test-user)** , um in Oracle Cloud Infrastructure Console eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Oracle Cloud Infrastructure Console** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie auf der Seite **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

   > [!NOTE]
   > Die Dienstanbieter-Metadatendatei erhalten Sie in diesem Tutorial im Abschnitt **Konfigurieren von Oracle Cloud Infrastructure Console**.
    
   1. Klicken Sie auf **Metadatendatei hochladen**.

   1. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.

   1. Nach dem erfolgreichen Hochladen der Metadatendatei werden die Werte unter **Bezeichner** und **Antwort-URL** im Textfeld des Abschnitts **Grundlegende SAML-Konfiguration** automatisch eingefügt.
    
      > [!NOTE]
      > Sollten die Werte **Bezeichner** und **Antwort-URL** nicht automatisch aufgefüllt werden, geben Sie die erforderlichen Werte manuell ein.

      Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://console.<REGIONNAME>.oraclecloud.com/`

      > [!NOTE]
      > Dieser Wert entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Wenden Sie sich an das [Clientsupportteam von Oracle Cloud Infrastructure Console](https://www.oracle.com/support/advanced-customer-support/products/cloud.html), um diesen Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

   ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Ihre Oracle Cloud Infrastructure Console-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol **Bearbeiten**, um das Dialogfeld „Benutzerattribute“ zu öffnen.

   ![image1](common/edit-attribute.png)

1. Darüber hinaus wird von der Oracle Cloud Infrastructure Console-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Führen Sie im Dialogfeld **Gruppenansprüche (Vorschau)** im Abschnitt **Benutzerattribute und Ansprüche** die folgenden Schritte aus:

   1. Klicken Sie **auf** den Stift **neben Name Bezeichnerwert**.

   1. Wählen Sie unter **Namensbezeichnerformat auswählen** die Option **Beständig** aus.
 
   1. Klicken Sie auf **Speichern**.

      ![Bild2](./media/oracle-cloud-tutorial/config07.png)
    
      ![image3](./media/oracle-cloud-tutorial/config11.png)

   1. Klicken Sie auf den **Stift** neben **Im Anspruch zurückgegebene Gruppen**.

   1. Wählen Sie in der Optionsfeldliste die Option **Sicherheitsgruppen** aus.

   1. Wählen Sie **Quellattribut** von **Gruppen-ID** aus.

   1. Aktivieren Sie **Name des Gruppenanspruchs anpassen**.

   1. Geben Sie in das Textfeld **Name** den Namen **groupName** ein.

   1. Geben Sie in das Textfeld **Namespace (optional)** den Namespace `https://auth.oraclecloud.com/saml/claims` ein.

   1. Klicken Sie auf **Speichern**.

      ![Bild4](./media/oracle-cloud-tutorial/config08.png)

1. Kopieren Sie im Abschnitt **Oracle Cloud Infrastructure Console einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

   ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B. Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B. Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Oracle Cloud Infrastructure Console gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **Oracle Cloud Infrastructure Console** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann am unteren Bildschirmrand auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-oracle-cloud-infrastructure-console"></a>Konfigurieren von Oracle Cloud Infrastructure Console

1. Melden Sie sich in einem anderen Browserfenster bei Oracle Cloud Infrastructure Console als Administrator an.

1. Klicken Sie links auf das Menü. Klicken Sie anschließend auf **Identity** (Identität), und navigieren Sie zu **Federation** (Verbund).

   ![Konfiguration 1](./media/oracle-cloud-tutorial/config01.png)

1. Speichern Sie die **Dienstanbieter-Metadatendatei**, indem Sie auf den Link **Download this document** (Dieses Dokument herunterladen) klicken, und laden Sie sie in den Abschnitt **Grundlegende SAML-Konfiguration** des Azure-Portals hoch. Klicken Sie anschließend auf **Add Identity Provider** (Identitätsanbieter hinzufügen).

   ![Konfiguration 2](./media/oracle-cloud-tutorial/config02.png)

1. Gehen Sie im Popupfenster **Add Identity Provider** (Identitätsanbieter hinzufügen) wie folgt vor:

   ![Konfiguration 3](./media/oracle-cloud-tutorial/config03.png)

   1. Geben Sie im Textfeld **NAME** Ihren Namen ein.

   1. Geben Sie im Textfeld **DESCRIPTION** (BESCHREIBUNG) Ihre Beschreibung ein.

   1. Wählen Sie unter **TYPE** (TYP) die Option **MICROSOFT ACTIVE DIRECTORY FEDERATION SERVICE (ADFS) OR SAML 2.0 COMPLIANT IDENTITY PROVIDER** (MICROSOFT ACTIVE DIRECTORY FEDERATION SERVICES (AD FS) ODER MIT SAML 2.0 KOMPATIBLER IDENTITÄTSANBIETER) aus.

   1. Klicken Sie zum Hochladen der Verbundmetadaten-XML-Datei, die Sie aus dem Azure-Portal heruntergeladen haben, auf **Browse** (Durchsuchen).

   1. Klicken Sie auf **Continue** (Weiter), und gehen Sie im Abschnitt **Edit Identity Provider** (Identitätsanbieter bearbeiten) wie folgt vor:

      ![Konfiguration 4](./media/oracle-cloud-tutorial/configure-09.png)

   1. Für **IDENTITY PROVIDER GROUP** (IDENTITÄTSANBIETERGRUPPE) sollte die ID des Azure AD-Gruppenobjekts ausgewählt werden. Geben Sie als GRUPPEN-ID die GUID der Gruppe aus Azure Active Directory an. Die Gruppe muss der entsprechenden Gruppe im Feld **OCI GROUP** (OCI-GRUPPE) zugeordnet werden.

   1. Bei Bedarf können gemäß Ihrem Setup in Azure-Portal und den Anforderungen Ihrer Organisation mehrere Gruppen zugeordnet werden. Klicken Sie auf **+ Add mapping** (+ Zuordnung hinzufügen), um weitere Gruppen hinzuzufügen.

   1. Klicken Sie auf **Submit**(Senden).
   
### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Erstellen eines Oracle Cloud Infrastructure Console-Testbenutzers

 Oracle Cloud Infrastructure Console unterstützt die Just-In-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Bei einem Zugriffsversuch wird kein neuer Benutzer erstellt, und es muss auch kein Benutzer erstellt werden.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

Wenn Sie im Zugriffsbereich die Kachel „Oracle Cloud Infrastructure Console“ auswählen, werden Sie zur Anmeldeseite für Oracle Cloud Infrastructure Console weitergeleitet. Wählen Sie im Dropdownmenü die Option **IDENTITY PROVIDER** (IDENTITÄTSANBIETER) aus, und klicken Sie wie weiter unten gezeigt auf **Continue** (Weiter), um sich anzumelden. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

![Konfiguration](./media/oracle-cloud-tutorial/config10.png)

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Oracle Cloud Infrastructure Console können Sie Sitzungssteuerungen erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützen. Sitzungssteuerungen basieren auf bedingtem Zugriff. [Hier](/cloud-app-security/proxy-deployment-aad) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.