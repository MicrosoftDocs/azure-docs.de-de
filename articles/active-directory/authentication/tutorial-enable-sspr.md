---
title: Aktivieren der Self-Service-Kennwortzurücksetzung von Azure Active Directory
description: In diesem Tutorial erfahren Sie, wie Sie die Self-Service-Kennwortzurücksetzung von Azure Active Directory für eine Gruppe von Benutzern aktivieren und den Prozess zur Kennwortzurücksetzung testen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 924ff6091e7eb6633d36f75fd6d53745139454d7
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741064"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Tutorial: Ermöglichen der Kontoentsperrung oder Kennwortzurücksetzung für Benutzer mit der Self-Service-Kennwortzurücksetzung von Azure Active Directory

Mit der Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) von Azure Active Directory (Azure AD) können Benutzer ihr Kennwort ohne Beteiligung eines Administrators oder des Helpdesks ändern oder zurücksetzen. Wenn das Konto eines Benutzers gesperrt ist oder dieser sein Kennwort vergessen hat, kann er die Schritte zum Entsperren ausführen und anschließend weiterarbeiten. Dies führt zu weniger Anrufen beim Helpdesk und Produktivitätsverlusten, wenn sich ein Benutzer nicht an seinem Gerät oder einer Anwendung anmelden kann.

> [!IMPORTANT]
> In diesem Tutorial erfahren Sie, wie ein Administrator die Self-Service-Kennwortzurücksetzung aktivieren kann. Wenn Sie bereits als Endbenutzer für die Self-Service-Kennwortzurücksetzung registriert sind und wieder zu Ihrem Konto zurückkehren müssen, gehen Sie zu https://aka.ms/sspr.
>
> Wenn Ihr IT-Team die Möglichkeit zum Zurücksetzen Ihres eigenen Kennworts nicht aktiviert hat, wenden Sie sich an den Helpdesk, um weitere Unterstützung zu erhalten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Aktivieren der Self-Service-Kennwortzurücksetzung für eine Gruppe von Azure AD-Benutzern
> * Konfigurieren von Authentifizierungsmethoden und Registrierungsoptionen
> * Testen des SSPR-Prozesses als Benutzer

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Einen funktionierenden Azure AD-Mandanten mit mindestens einer aktivierten Azure Active Directory Free-Lizenz oder -Testlizenz. Im Free-Tarif funktioniert SSPR nur für Cloudbenutzer in Azure AD.
    * Für spätere Tutorials in dieser Reihe ist eine Azure AD Premium P1-Lizenz oder -Testlizenz für das lokale Kennwortrückschreiben erforderlich.
    * Erstellen Sie ggf. [ein kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ein Konto mit Berechtigungen vom Typ *Globaler Administrator*
* Ein Benutzer ohne Administratorrechte mit einem Ihnen bekannten Kennwort, wie z. B. *testuser*. In diesem Tutorial testen Sie SSPR für Endbenutzer mit diesem Konto.
    * Wenn Sie einen Benutzer erstellen müssen, finden Sie weitere Informationen unter [Schnellstart: Hinzufügen neuer Benutzer in Azure Active Directory](../fundamentals/add-users-azure-active-directory.md) weiter.
* Eine Gruppe, in der der Benutzer ohne Administratorrechte Mitglied ist, z. B. *SSPR-Test-Group*. In diesem Tutorial aktivieren Sie SSPR für diese Gruppe.
    * Wenn Sie eine Gruppe erstellen müssen, finden Sie weitere Informationen unter [Erstellen einer Gruppe und Hinzufügen von Mitgliedern in Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md).

## <a name="enable-self-service-password-reset"></a>Aktivieren der Self-Service-Kennwortzurücksetzung

Azure AD ermöglicht die Aktivierung von SSPR für *keine*, *ausgewählte* oder *alle* Benutzer. Dank dieser differenzierten Auswahloptionen können Sie eine Teilmenge von Benutzern zum Testen des SSPR-Registrierungsprozesses und -Workflows auswählen. Wenn Sie mit dem Prozess vertraut sind und die Anforderungen einer größeren Gruppe von Benutzern mitteilen möchten, können Sie eine Benutzergruppe für die Aktivierung von SSPR auswählen. Alternativ können Sie SSPR für alle Benutzer im Azure AD-Mandanten aktivieren.

> [!NOTE]
>
> Über das Azure-Portal kann derzeit nur eine Azure AD-Gruppe für SSPR aktiviert werden. Im Rahmen einer umfassenderen Bereitstellung von SSPR werden geschachtelte Gruppen unterstützt. Stellen Sie sicher, dass den Benutzern in den von Ihnen ausgewählten Gruppen die entsprechenden Lizenzen zugewiesen sind. Derzeit gibt es keinen Überprüfungsprozess für diese Lizenzierungsanforderungen.

In diesem Tutorial konfigurieren Sie SSPR für eine Gruppe von Benutzern in einer Testgruppe. Im folgenden Beispiel wird die Gruppe *SSPR-Test-Group* verwendet. Geben Sie bei Bedarf eine eigene Azure AD-Gruppe an:

1. Melden Sie sich mit dem Konto mit den Berechtigungen vom Typ *Globaler Administrator* beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Azure Active Directory**, wählen Sie den Eintrag aus, und wählen Sie dann im Menü auf der linken Seite **Kennwortzurücksetzung** aus.
1. Wählen Sie auf der Seite **Eigenschaften** unter *Self-Service-Kennwortzurücksetzung aktiviert* die Option **Gruppe auswählen** aus.
1. Navigieren Sie zu Ihrer Azure AD-Gruppe (etwa *SSPR-Test-Group*), und wählen Sie die Gruppe und anschließend *Auswählen* aus.

    [ ![Auswählen einer Gruppe im Azure-Portal, für die die Self-Service-Kennwortzurücksetzung aktiviert werden soll](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png) ](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. Wählen Sie zum Aktivieren von SSPR für die ausgewählten Benutzer **Speichern** aus.

## <a name="select-authentication-methods-and-registration-options"></a>Auswählen von Authentifizierungsmethoden und Registrierungsoptionen

Wenn Benutzer ihr Konto entsperren oder das Kennwort zurücksetzen müssen, werden sie zur Verwendung einer zusätzlichen Bestätigungsmethode aufgefordert. Durch diesen zusätzlichen Authentifizierungsfaktor wird sichergestellt, dass nur genehmigte SSPR-Ereignisse ausgeführt werden. Basierend auf den vom Benutzer bereitgestellten Registrierungsinformationen können Sie auswählen, welche Authentifizierungsmethoden zugelassen werden sollen.

1. Rufen Sie über das Menü auf der linken Seite die Seite **Authentifizierungsmethoden** auf, und legen Sie dort die Option **Anzahl von Methoden, die zurückgesetzt werden müssen** auf *1* fest.

    Zur Verbesserung der Sicherheit können Sie die Anzahl der für SSPR erforderlichen Authentifizierungsmethoden erhöhen.

1. Legen Sie unter **Für Benutzer verfügbare Methoden** fest, welche Methoden Ihre Organisation zulassen möchte. Aktivieren Sie für dieses Tutorial die Kontrollkästchen, um die folgenden Methoden zu aktivieren:

    * *Benachrichtigung über eine mobile App*
    * *Code der mobilen App*
    * *E-Mail*
    * *Mobiltelefon*

    Zusätzliche Authentifizierungsmethoden, z. B. *Bürotelefon* oder *Sicherheitsfragen*, können je nach Ihren geschäftlichen Anforderungen aktiviert werden.

1. Wählen Sie zum Anwenden der Authentifizierungsmethoden **Speichern** aus.

Bevor Benutzer ihr Konto entsperren oder das Kennwort ändern können, müssen sie ihre Kontaktinformationen registrieren. Diese Kontaktinformationen werden für die verschiedenen Authentifizierungsmethoden verwendet, die in den vorherigen Schritten konfiguriert wurden.

Ein Administrator kann diese Kontaktinformationen manuell angeben, oder Benutzer können die Informationen selbst in einem Registrierungsportal eingeben. In diesem Tutorial konfigurieren Sie die Benutzer so, dass sie bei der nächsten Anmeldung zur Registrierung aufgefordert werden.

1. Navigieren Sie über das Menü auf der Linken Seite zur Seite **Registrierung**, und wählen Sie für **Registrierung von Benutzern bei der Anmeldung verlangen?** die Option *Ja* aus.
1. Es ist wichtig, dass die Kontaktinformationen auf dem neuesten Stand gehalten werden. Wenn die Kontaktinformationen beim Starten eines SSPR-Ereignisses veraltet sind, kann der Benutzer unter Umständen sein Konto nicht entsperren oder das Kennwort nicht zurücksetzen.

    Legen Sie **Anzahl der Tage, bevor Benutzer aufgefordert werden, ihre Authentifizierungsinformationen erneut zu bestätigen** auf *180* fest.
1. Wählen Sie zum Übernehmen der Registrierungseinstellungen **Speichern** aus.

## <a name="configure-notifications-and-customizations"></a>Konfigurieren von Benachrichtigungen und Anpassungen

Um Benutzer über die Kontoaktivität auf dem Laufenden zu halten, können Sie E-Mail-Benachrichtigungen konfigurieren, die im Falle eines SSPR-Ereignisses gesendet werden. Diese Benachrichtigungen können sowohl für reguläre Benutzerkonten als auch für Administratorkonten eingerichtet werden. Für Administratorkonten bietet diese Benachrichtigung eine zusätzliche Information, wenn das Kennwort eines privilegierten Administratorkontos per SSPR zurückgesetzt wird. Alle globalen Administratoren werden benachrichtigt, wenn SSPR für ein Administratorkonto verwendet wird.

1. Navigieren Sie über das Menü auf der linken Seite zur Seite **Benachrichtigungen**, und konfigurieren Sie dort die folgenden Optionen:

   * Legen Sie **Benachrichtigen von Benutzern über Kennwortzurücksetzungen** auf *Ja* fest.
   * Legen Sie **Benachrichtigen aller Administratoren, wenn andere Administratoren ihr Kennwort zurücksetzen** auf *Ja* fest.

1. Wählen Sie zum Übernehmen der Benachrichtigungseinstellungen **Speichern** aus.

Wenn Benutzer zusätzliche Hilfe beim SSPR-Prozess benötigen, können Sie den Link für „Wenden Sie sich an Ihren Administrator.“ anpassen. Dieser Link wird im SSPR-Registrierungsprozess verwendet, wenn ein Benutzer sein Konto entsperrt oder das Kennwort zurücksetzt. Um sicherzustellen, dass Ihre Benutzer die erforderliche Unterstützung erhalten, wird dringend empfohlen, eine benutzerdefinierte Helpdesk-E-Mail-Adresse oder -URL bereitzustellen.

1. Navigieren Sie über das Menü auf der linken Seite zur Seite **Anpassung**, und legen Sie *Benutzerdefinierter Helpdesklink* auf **Ja** fest.
1. Geben Sie im Feld **Benutzerdefinierte Helpdesk-E-Mail oder -URL** eine E-Mail-Adresse oder eine Webseiten-URL (etwa *`https://support.contoso.com/`* ) an, unter der Benutzer weitere Unterstützung von Ihrer Organisation anfordern können.
1. Wählen Sie zum Übernehmen des benutzerdefinierten Links **Speichern** aus.

## <a name="test-self-service-password-reset"></a>Testen der Self-Service-Kennwortzurücksetzung

Wenn SSPR aktiviert und konfiguriert ist, testen Sie den SSPR-Prozess mit einem Benutzer aus der Gruppe, die Sie im vorherigen Abschnitt ausgewählt haben, etwa *Test-SSPR-Group*. Im folgenden Beispiel wird das Konto *testuser* verwendet. Geben Sie ein eigenes Benutzerkonto an, das Teil der Gruppe ist, für die Sie im ersten Abschnitt dieses Tutorials SSPR aktiviert haben.

> [!NOTE]
> Wenn Sie die Self-Service-Kennwortzurücksetzung testen, verwenden Sie ein Konto ohne Administratorrechte. Standardmäßig ist für Administratoren die Self-Service-Kennwortzurücksetzung aktiviert, und sie müssen zum Zurücksetzen ihres Kennworts zwei Authentifizierungsverfahren verwenden. Weitere Informationen finden Sie unter [Unterschiede zu Richtlinien zum Zurücksetzen von Administratorkennwörtern](concept-sspr-policy.md#administrator-reset-policy-differences).

1. Wenn Sie den manuellen Registrierungsvorgang anzeigen möchten, öffnen Sie ein neues Browserfenster im InPrivate- oder Inkognitomodus, und navigieren Sie zu [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup). Benutzer sollten bei der nächsten Anmeldung zu diesem Registrierungsportal weitergeleitet werden.
1. Melden Sie sich als Testbenutzer ohne Administratorrechte an, z. B. *testuser*, und registrieren Sie Ihre Kontaktinformationen für die Authentifizierungsmethoden.
1. Wählen Sie abschließend die Schaltfläche **Sieht gut aus** aus, und schließen Sie das Browserfenster.
1. Öffnen Sie ein neues Browserfenster im InPrivate- oder Inkognitomodus, und navigieren Sie zu [https://aka.ms/sspr](https://aka.ms/sspr).
1. Geben Sie die Kontoinformationen des Testbenutzers ohne Administratorrechte, z. B. *testuser*, und die CAPTCHA-Zeichen ein, und wählen Sie anschließend **Weiter** aus.

    ![Eingeben der Benutzerkontoinformationen zum Zurücksetzen des Kennworts](media/tutorial-enable-sspr/password-reset-page.png)

1. Führen Sie die Schritte für die Verifizierung aus, um Ihr Kennwort zurückzusetzen. Wenn Sie fertig sind, erhalten Sie eine E-Mail-Benachrichtigung mit dem Hinweis, dass Ihr Kennwort zurückgesetzt wurde.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In einem anderen Tutorial dieser Reihe konfigurieren Sie das Kennwortrückschreiben. Diese Funktion schreibt Kennwortänderungen von Azure AD-SSPR zurück in eine lokale AD-Umgebung. Wenn Sie diese Tutorialreihe zum Konfigurieren des Kennwortrückschreibens fortsetzen möchten, deaktivieren Sie SSPR jetzt nicht.

Wenn Sie die im Rahmen dieses Tutorials konfigurierte SSPR-Funktionalität nicht mehr nutzen möchten, legen Sie wie folgt den Status für SSPR auf **Keine** fest:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Azure Active Directory**, wählen Sie den Eintrag aus, und wählen Sie dann im Menü auf der linken Seite **Kennwortzurücksetzung** aus.
1. Wählen Sie auf der Seite **Eigenschaften** unter *Self-Service-Kennwortzurücksetzung aktiviert* die Option **Keine** aus.
1. Wählen Sie zum Übernehmen der SSPR-Änderung **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie die Azure AD-Self-Service-Kennwortzurücksetzung für eine ausgewählte Gruppe von Benutzern aktiviert. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Aktivieren der Self-Service-Kennwortzurücksetzung für eine Gruppe von Azure AD-Benutzern
> * Konfigurieren von Authentifizierungsmethoden und Registrierungsoptionen
> * Testen des SSPR-Prozesses als Benutzer

> [!div class="nextstepaction"]
> [Aktivieren von Azure AD Multi-Factor Authentication](./tutorial-enable-azure-mfa.md)
