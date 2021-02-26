---
title: 'Tutorial: Azure Active Directory-Integration mit Ceridian Dayforce HCM | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Ceridian Dayforce HCM konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 4468340dbeeeb67b736d9fd2d227d9c7b2ecbeb6
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99427717"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Tutorial: Azure Active Directory-Integration mit Ceridian Dayforce HCM

In diesem Tutorial erfahren Sie, wie Sie Ceridian Dayforce HCM in Azure Active Directory (Azure AD) integrieren. Die Integration von Ceridian Dayforce HCM in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Ceridian Dayforce HCM hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Ceridian Dayforce HCM anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Ceridian Dayforce HCM-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Ceridian Dayforce HCM unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="add-ceridian-dayforce-hcm-from-the-gallery"></a>Hinzufügen von Ceridian Dayforce HCM aus dem Katalog

Zum Konfigurieren der Integration von Ceridian Dayforce HCM in Azure AD müssen Sie Ceridian Dayforce HCM aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Ceridian Dayforce HCM** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Ceridian Dayforce HCM** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-ceridian-dayforce-hcm"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Ceridian Dayforce HCM

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Ceridian Dayforce HCM mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Ceridian Dayforce HCM eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Ceridian Dayforce HCM die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Ceridian Dayforce HCM](#configure-ceridian-dayforce-hcm-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Ceridian Dayforce HCM-Testbenutzers](#create-ceridian-dayforce-hcm-test-user)** , um eine Entsprechung von B. Simon in Ceridian Dayforce HCM zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD 

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Ceridian Dayforce HCM** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Ceridian Dayforce HCM](common/sp-identifier-reply.png)

    a. Geben Sie im Textfeld **Anmelde-URL** die URL ein, die Benutzer zum Anmelden bei Ihrer Ceridian Dayforce HCM-Anwendung verwenden.

    | Environment | URL |
    | :-- | :-- |
    | Für die Bereitstellung | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | Zu Testzwecken | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. Geben Sie im Textfeld **Bezeichner** die URL nach folgendem Muster ein: .

    | Environment | URL |
    | :-- | :-- |
    | Für die Bereitstellung | `https://ncpingfederate.dayforcehcm.com/sp` |
    | Zu Testzwecken | `https://fs-test.dayforcehcm.com/sp` |

    c. Geben Sie im Textfeld **Antwort-URL** die URL ein, die von Azure AD zum Senden der Antwort verwendet wird.

    | Environment | URL |
    | :-- | :-- |
    | Für die Bereitstellung | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | Zu Testzwecken | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL, den tatsächlichen Bezeichner und die tatsächliche Antwort-URL. Wenden Sie sich an das [Ceridian Dayforce HCM Client support team (Ceridian Dayforce HCM Client-Supportteam)](https://www.ceridian.com/support), um diese Werte abzurufen. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Die Ceridian Dayforce HCM-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![Screenshot der Benutzerattribute mit ausgewähltem Symbol „Bearbeiten“](common/edit-attribute.png)

6. Konfigurieren Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** das SAML-Tokenattribut wie in der obigen Abbildung gezeigt, und führen Sie die folgenden Schritte aus:

    | Name | Quellattribut|
    | ---------| --------- |
    | name  | user.extensionattribute2 |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![Screenshot: Benutzeransprüche mit Option zum Hinzufügen eines neuen Anspruchs](common/new-save-attribute.png)

    ![Screenshot des Dialogfelds „Benutzeransprüche verwalten“, in dem Sie die hier beschriebenen Werte eingeben können](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Wählen Sie in der Liste **Quellattribut** das Benutzerattribut aus, das Sie für Ihre Implementierung verwenden möchten. Wenn Sie z.B. den Wert „EmployeeID“ als eindeutige Benutzer-ID verwenden möchten und den Attributwert in „ExtensionAttribute2“ gespeichert haben, wählen Sie „user.extensionattribute2“ aus.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

7. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Metadaten-XML** aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

8. Kopieren Sie im Abschnitt **Ceridian Dayforce HCM einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Ceridian Dayforce HCM gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Ceridian Dayforce HCM** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="configure-ceridian-dayforce-hcm-sso"></a>Konfigurieren des einmaligen Anmeldens für Ceridian Dayforce HCM

Zum Konfigurieren des einmaligen Anmeldens aufseiten von **Ceridian Dayforce HCM** müssen Sie die heruntergeladene **Metadaten-XML** und die kopierten URLs aus dem Azure-Portal an das [Supportteam von Ceridian Dayforce HCM](https://www.ceridian.com/support) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-ceridian-dayforce-hcm-test-user"></a>Erstellen einen Ceridian Dayforce HCM-Testbenutzers

In diesem Abschnitt erstellen Sie in Ceridian Dayforce HCM eine Benutzerin namens Britta Simon. Wenden Sie sich an das [Ceridian Dayforce HCM-Supportteam](https://www.ceridian.com/support), um die Benutzer auf der Ceridian Dayforce HCM-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Ceridian Dayforce HCM weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Navigieren Sie direkt zur Anmelde-URL für Ceridian Dayforce HCM, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Ceridian Dayforce HCM“ klicken, werden Sie zur Anmelde-URL für Ceridian Dayforce HCM umgeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Ceridian Dayforce HCM können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Hier](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app) erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Cloud App Security erzwingen.
