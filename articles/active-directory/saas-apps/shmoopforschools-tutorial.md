---
title: 'Tutorial: Azure Active Directory-Integration mit Shmoop For Schools | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Shmoop For Schools konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.openlocfilehash: 7838e4f2ced5f47a0fb52b6e0f07d30edd770dca
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92522074"
---
# <a name="tutorial-integrate-shmoop-for-schools-with-azure-active-directory"></a>Tutorial: Integrieren von Shmoop For Schools mit Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Shmoop For Schools in Azure Active Directory (Azure AD) integrieren. Die Integration von Shmoop For Schools in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Shmoop For Schools hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Shmoop For Schools anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Shmoop For Schools-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Shmoop For Schools unterstützt **SP** -initiiertes einmaliges Anmelden.
* Shmoop For Schools unterstützt die **Just-in-Time** -Benutzerbereitstellung.

## <a name="adding-shmoop-for-schools-from-the-gallery"></a>Hinzufügen von Shmoop For Schools über den Katalog

Zum Konfigurieren der Integration von Shmoop For Schools in Azure AD müssen Sie Shmoop For Schools aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen** , und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Shmoop For Schools** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Shmoop For Schools** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-shmoop-for-schools"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Shmoop For Schools

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Shmoop For Schools mithilfe eines Testbenutzers mit dem Namen **B. Simon** . Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Shmoop For Schools eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Shmoop For Schools zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Shmoop For Schools](#configure-shmoop-for-schools-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines Shmoop For Schools-Testbenutzers](#create-shmoop-for-schools-test-user)** , um eine Entsprechung von B. Simon in Shmoop For Schools zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
3. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Shmoop For Schools** zum Abschnitt **Verwalten** , und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** , um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://schools.shmoop.com/<uniqueid>`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Die Werte erhalten Sie vom [Supportteam für den Shmoop For Schools-Client](mailto:support@shmoop.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die Shmoop For Schools-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

    > [!NOTE]
    > Shmoop For Schools unterstützt zwei Rollen für Benutzer: **Lehrer** und **Kursteilnehmer** . Richten Sie diese Rollen in Azure AD ein, damit Benutzern die passenden Rollen zugewiesen werden können. Informationen zum Konfigurieren von Rollen in Azure AD finden Sie [hier](../develop/active-directory-enterprise-app-role-management.md).

1. Darüber hinaus wird von der Shmoop For Schools-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden, die nachstehend aufgeführt sind. Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

    | Name |  Quellattribut|
    | --------- | --------------- |
    | Rolle (role)      | user.assignedroles |

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
    1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
    1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** , und notieren Sie sich den Wert aus dem Feld **Kennwort** .
    1. Klicken Sie auf **Erstellen** .

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Shmoop For Schools gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen**  > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Shmoop For Schools** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten** , und wählen Sie **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen** .
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen** .
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** .

## <a name="configure-shmoop-for-schools-sso"></a>Konfigurieren des einmaligen Anmeldens für Shmoop For Schools

Zum Konfigurieren des einmaligen Anmeldens bei **Shmoop For Schools** müssen Sie die **App-Verbundmetadaten-URL** an das [Shmoop For Schools-Supportteam](mailto:support@shmoop.com) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-shmoop-for-schools-test-user"></a>Erstellen eines Shmoop For Schools-Testbenutzers

In diesem Abschnitt wird ein Benutzer mit dem Namen B. Simon in Shmoop For Schools erstellt. Shmoop For Schools unterstützt die (standardmäßig aktivierte) Just-In-Time-Benutzerbereitstellung. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in Shmoop For Schools vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

> [!NOTE]
> Setzen Sie sich mit dem [Shmoop For Schools-Supportteam](mailto:support@shmoop.com) in Verbindung, wenn Sie einen Benutzer manuell erstellen müssen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Shmoop For Schools“ klicken, sollten Sie automatisch bei der Shmoop For Schools-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [Testen von Shmoop For Schools mit Azure AD](https://aad.portal.azure.com/)