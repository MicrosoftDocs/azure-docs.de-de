---
title: Ausführliche Betrachtung der Self-Service-Kennwortzurücksetzung – Azure Active Directory
description: Funktionsweise der Self-Service-Kennwortzurücksetzung
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47a6f475b5f1152850ec918b196883c6974f4d95
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60415612"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>So funktioniert's: Self-Service-Kennwortzurücksetzung in Azure AD

Wie funktioniert die Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR)? Was bedeutet diese Option in der Benutzeroberfläche? Im weiteren Verlauf erfahren Sie mehr über SSRP in Azure Active Directory (Azure AD).

|     |
| --- |
| Das Empfangen einer Benachrichtigung in der mobilen App und das Erhalten eines Codes in der mobilen App sind zwei Methoden für die Self-Service-Kennwortzurücksetzung in Azure AD und als öffentliche Previewfunktion von Azure Active Directory verfügbar. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="how-does-the-password-reset-portal-work"></a>Wie funktioniert das Portal für die Kennwortzurücksetzung?

Wenn ein Benutzer zum Kennwortzurücksetzungsportal navigiert, wird ein Workflow gestartet, um Folgendes zu bestimmen:

   * Wie soll die Seite lokalisiert werden?
   * Ist das Benutzerkonto gültig?
   * Zu welcher Organisation gehört der Benutzer?
   * Wo wird das Kennwort des Benutzers verwaltet?
   * Ist der Benutzer zur Verwendung des Features lizenziert?

In den folgenden Schritten wird beschrieben, welche Logik hinter der Seite zur Kennwortzurücksetzung steckt:

1. Der Benutzer klickt auf den Link **Sie können nicht auf Ihr Konto zugreifen?** oder wechselt direkt zu [https://aka.ms/sspr](https://passwordreset.microsoftonline.com).
   * Die Benutzeroberfläche wird basierend auf dem Browsergebietsschema in der entsprechenden Sprache wiedergegeben. Die Benutzeroberfläche für das Zurücksetzen des Kennworts wird in alle Sprachen lokalisiert, die Office 365 unterstützt.
   * Wenn Sie das Portal für die Kennwortzurücksetzung in einer anderen Sprache anzeigen möchten, fügen Sie am Ende der URL für die Kennwortzurücksetzung „?mkt=“ ein, wie im folgenden Beispiel für Spanisch zu sehen: [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us).
2. Der Benutzer gibt eine Benutzer-ID ein und durchläuft erfolgreich die Captchaprüfung.
3. Azure AD prüft folgendermaßen, ob der Benutzer diese Funktion verwenden darf:
   * Es wird geprüft, ob die Funktion für diesen Benutzer aktiviert ist und ob eine Azure AD-Lizenz zugewiesen ist.
     * Wenn diese Funktion für den Benutzer nicht aktiviert ist oder keine Lizenz vorliegt, wird der Benutzer aufgefordert, sich zum Zurücksetzen des Kennworts an den Administrator zu wenden.
   * Es wird überprüft, ob der Benutzer in seinem Konto die richtigen Authentifizierungsmethoden definiert hat, die der Administratorrichtlinie entsprechen.
     * Wenn die Richtlinie nur eine Methode erfordert, wird sichergestellt, dass der Benutzer für mindestens eine durch die Administratorrichtlinie aktivierte Authentifizierungsmethode geeignete Daten definiert hat.
       * Wenn die Authentifizierungsmethoden nicht konfiguriert sind, wird der Benutzer aufgefordert, sich an den Administrator zu wenden, um sein Kennwort zurückzusetzen.
     * Wenn die Richtlinie zwei Methoden erfordert, wird sichergestellt, dass der Benutzer für mindestens zwei durch die Administratorrichtlinie aktivierte Authentifizierungsmethoden geeignete Daten definiert hat.
       * Wenn die Authentifizierungsmethoden nicht konfiguriert sind, wird der Benutzer aufgefordert, sich an den Administrator zu wenden, um sein Kennwort zurückzusetzen.
     * Wenn einem Benutzer eine Azure-Administratorrolle zugewiesen wird, wird dadurch auch die sichere Zwei-Wege-Kennwortrichtlinie erzwungen. Weitere Informationen zu dieser Richtlinie finden Sie im Abschnitt [Unterschiede zu Richtlinien zum Zurücksetzen von Administratorkennwörtern](concept-sspr-policy.md#administrator-reset-policy-differences).
   * Es wird überprüft, ob das Benutzerkennwort lokal verwaltet wird (im Verbund, mit Pass-Through-Authentifizierung oder mit Kennworthashsynchronisierung).
     * Wenn das Rückschreiben von Kennwörtern konfiguriert ist und das Benutzerkennwort lokal verwaltet wird, kann der Benutzer mit der Authentifizierung fortfahren und sein Kennwort zurücksetzen.
     * Wenn das Rückschreiben von Kennwörtern nicht konfiguriert ist und das Benutzerkennwort lokal verwaltet wird, wird der Benutzer aufgefordert, sich zum Zurückzusetzen des Kennworts an den Administrator zu wenden.
4. Wenn festgestellt wird, dass der Benutzer sein Kennwort zurücksetzen darf, wird er durch den Vorgang für die Kennwortzurücksetzung geleitet.

## <a name="authentication-methods"></a>Authentifizierungsmethoden

Wenn SSPR aktiviert ist, müssen Sie mindestens eine der folgenden Optionen als Authentifizierungsmethode auswählen. Manchmal werden diese Optionen als Gates bezeichnet. Es wird dringend empfohlen, **mindestens zwei Authentifizierungsmethoden** auszuwählen, damit Ihre Benutzer ausweichen können, falls sie auf eine Methode nicht zugreifen können. Weitere Informationen zu den unten aufgeführten Methoden finden Sie im Artikel [Was sind Authentifizierungsmethoden?](concept-authentication-methods.md)

* Benachrichtigung in der mobilen App (Vorschauversion)
* Code in der mobilen App (Vorschauversion)
* E-Mail
* Mobiltelefon
* Bürotelefon
* Sicherheitsfragen

Benutzer können ihr Kennwort nur zurücksetzen, wenn für sie Daten in den Authentifizierungsmethoden vorliegen, die der Administrator aktiviert hat.

> [!IMPORTANT]
> Ab März 2019 werden die Telefonanrufoptionen für MFA- und der SSPR-Benutzer in kostenlosen bzw. Testversionen von Azure AD-Mandanten nicht mehr verfügbar sein. SMS-Nachrichten sind von dieser Änderung nicht betroffen. Für Benutzer in kostenpflichtigen Azure AD-Mandanten ist die Telefonanrufoption weiterhin verfügbar. Diese Änderung wirkt sich nur auf kostenlose bzw. Testversionen von Azure AD-Mandanten aus.

> [!WARNING]
> Konten, denen Azure-Administratorrollen zugewiesen wurden, müssen Methoden nutzen, die im Abschnitt [Unterschiede zu Richtlinien zum Zurücksetzen von Administratorkennwörtern](concept-sspr-policy.md#administrator-reset-policy-differences) definiert sind.

![Auswahl von Authentifizierungsmethoden im Azure-Portal][Authentication]

### <a name="number-of-authentication-methods-required"></a>Anzahl erforderlicher Authentifizierungsmethoden

Diese Option bestimmt die Mindestanzahl der verfügbaren Authentifizierungsmethoden oder Gates, die ein Benutzer zum Zurücksetzen oder Entsperren des Kennworts durchlaufen muss. Sie kann auf ein oder zwei festgelegt werden.

Benutzer können wählen, weitere Authentifizierungsmethoden bereitzustellen, wenn diese Authentifizierungsmethode vom Administrator aktiviert wird.

Sind für einen Benutzer nicht die mindestens erforderlichen Methoden registriert, wird eine Fehlerseite angezeigt, die ihn auffordert, einen Administrator um die Kennwortzurücksetzung zu bitten.

#### <a name="mobile-app-and-sspr-preview"></a>Mobile App und SSPR (Vorschauversion)

Wenn Sie eine mobile App wie die Microsoft Authenticator-App als Methode zur Kennwortzurücksetzung verwenden, sollten Sie folgende Einschränkungen beachten:

* Wenn Administratoren eine Methode zum Zurücksetzen von Kennwörtern erzwingen, steht als einzige Option der Prüfcode zur Verfügung.
* Wenn Administratoren zwei Methoden für die Kennwortzurücksetzung erfordern, können Benutzer zum Zurücksetzen **ENTWEDER** die Option „Benachrichtigung“ **ODER** die Option „Prüfcode“ verwenden – zusätzlich zu anderen aktivierten Methoden.

| Anzahl von erforderlichen Methoden zum Zurücksetzen | Eine | Zwei |
| :---: | :---: | :---: |
| Verfügbare Funktionen der mobilen App | Code | Code oder Benachrichtigung |

Benutzer können ihre mobile App nicht registrieren, wenn sie sich für die Self-Service-Kennwortzurücksetzung von [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) registrieren. Die Benutzer haben die Möglichkeit, ihre mobile App unter [https://aka.ms/mfasetup](https://aka.ms/mfasetup) oder in den neuen Sicherheitsinformationen in der Registrierungsvorschau unter [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) zu registrieren.

> [!WARNING]
> Sie müssen die [Zusammengeführte Registrierung für Self-Service-Kennwortzurücksetzung und Azure Multi-Factor Authentication (öffentliche Vorschauversion)](concept-registration-mfa-sspr-converged.md) aktivieren, bevor Benutzer auf die neue Oberfläche unter [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) zugreifen können.

### <a name="change-authentication-methods"></a>Ändern der Authentifizierungsmethoden

Was passiert, wenn Sie mit einer Richtlinie beginnen, bei der zum Zurücksetzen oder Entsperren der Registrierung nur eine einzelne Authentifizierungsmethode erforderlich ist, und Sie dies in zwei Authentifizierungsmethoden ändern?

| Anzahl der registrierten Methoden | Anzahl der erforderlichen Methoden | Ergebnis |
| :---: | :---: | :---: |
| Mindestens 1 | 1 | **Kann** zurücksetzen oder entsperren |
| 1 | 2 | **Kann nicht** zurücksetzen oder entsperren |
| 2 oder mehr | 2 | **Kann** zurücksetzen oder entsperren |

Wenn Sie die den Benutzern zur Verfügung stehenden Arten von Authentifizierungsmethoden ändern, können Benutzer unter Umständen SSPR nicht mehr verwenden, wenn ihnen nicht genügend Daten zur Verfügung stehen.

Beispiel:
1. Die ursprüngliche Richtlinie wird mit zwei erforderlichen Authentifizierungsmethoden konfiguriert. Sie verwendet nur die Bürotelefonnummer und die Sicherheitsfragen. 
2. Der Administrator ändert die Richtlinie so, dass anstelle von Sicherheitsfragen ein Mobiltelefon und eine alternative E-Mail-Adresse verwendet werden.
3. Benutzer, für die das Feld für Mobiltelefon oder alternative E-Mail-Adresse nicht aufgefüllt wird, können ihre Kennwörter nicht zurücksetzen.

## <a name="registration"></a>Registrierung

### <a name="require-users-to-register-when-they-sign-in"></a>Erzwingen der Registrierung für Benutzer bei der Anmeldung

Wenn diese Option aktiviert ist, muss ein Benutzer die Registrierung zur Kennwortzurücksetzung abschließen, wenn er sich mit Azure AD in Anwendungen anmelden möchte. Dieser Workflow schließt die folgenden Anwendungen ein:

* Office 365
* Azure-Portal
* Anpassung des Zugriffsbereichs
* Verbundanwendungen
* Benutzerdefinierte Anwendungen, die Azure AD verwenden

Wenn das Erzwingen der Registrierung deaktiviert ist, können Benutzer sich manuell registrieren. Sie können entweder [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) aufrufen oder im Zugriffsbereich auf der Registerkarte **Profil** auf den Link **Für das Zurücksetzen des Kennworts registrieren** klicken.

> [!NOTE]
> Benutzer können das Registrierungsportal für die Kennwortzurücksetzung durch Klicken auf **Abbrechen** oder durch Schließen des Fensters schließen. Sie werden jedoch bei jeder Anmeldung zur Registrierung aufgefordert, bis die Registrierung durchgeführt wurde.
>
> Hierdurch wird die Verbindung des Benutzers nicht unterbrochen, wenn er bereits angemeldet ist.

### <a name="set-the-number-of-days-before-users-are-asked-to-reconfirm-their-authentication-information"></a>Festlegen der Anzahl von Tagen, bevor Benutzer aufgefordert werden, ihre Authentifizierungsinformationen erneut zu bestätigen

Diese Option bestimmt den Zeitraum zwischen dem Festlegen und dem erneuten Bestätigen der Authentifizierungsinformationen und ist nur verfügbar, wenn die Option **Registrierung von Benutzern bei der Anmeldung verlangen** aktiviert ist.

Gültige Werte sind 0 bis 730 Tage, wobei „0“ bedeutet, dass Benutzer nie zum erneuten Bestätigen ihrer Authentifizierungsinformationen aufgefordert werden.

## <a name="notifications"></a>Benachrichtigungen

### <a name="notify-users-on-password-resets"></a>Benutzer über Kennwortzurücksetzungen benachrichtigen?

Ist diese Option auf **Ja** festgelegt, erhalten Benutzer, die ihr Kennwort zurücksetzen, eine E-Mail mit dem Hinweis, dass ihr Kennwort geändert wurde. Die E-Mail wird über das SSPR-Portal an die in Azure AD hinterlegte primäre und alternative E-Mail-Adresse gesendet. Niemand sonst wird über das Zurücksetzen informiert.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Sollen alle Administratoren benachrichtigt werden, wenn andere Administratoren ihr Kennwort zurücksetzen?

Ist diese Option auf **Ja** festgelegt, erhalten *alle Administratoren* eine E-Mail an ihre in Azure AD erfasste primäre E-Mail-Adresse. In dieser E-Mail werden sie darüber informiert, dass ein anderer Administrator ihr Kennwort mithilfe von SSPR geändert hat.

Beispiel: Es gibt vier Administratoren in einer Umgebung. Administrator A setzt das Kennwort mithilfe von SSPR zurück. Administrator B, C und D werden per E-Mail über die Kennwortzurücksetzung informiert.

## <a name="on-premises-integration"></a>Lokale Integration

Wenn Sie Azure AD Connect installieren, konfigurieren und aktivieren, stehen folgende zusätzliche Optionen für lokale Integrationen zur Verfügung. Wenn diese Optionen abgeblendet sind, wurde das Rückschreiben nicht ordnungsgemäß konfiguriert. Weitere Informationen finden Sie unter [Konfigurieren des Kennwortrückschreibens](howto-sspr-writeback.md).

![Überprüfen von Aktivierung und Funktionsfähigkeit des Kennwortrückschreibens][Writeback]

Auf dieser Seite erhalten Sie einen schnellen Überblick über den Status des lokalen Clients für das Rückschreiben. Basierend auf der aktuellen Konfiguration wird eine der folgenden Meldungen angezeigt:

* Ihr lokaler Client für das Rückschreiben ist einsatzbereit.
* Azure AD Connect ist online und mit Ihrem lokalen Client für das Rückschreiben verbunden. Die installierte Version von Azure AD Connect ist jedoch offenbar veraltet. Ziehen Sie ein [Upgrade von Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) in Betracht, um sicherzustellen, dass Sie über die neuesten Konnektivitätsfeatures und wichtige Fehlerbehebungen verfügen.
* Leider können wir den Status Ihres lokalen Clients für das Rückschreiben nicht überprüfen, weil die installierte Version von Azure AD Connect nicht aktuell ist. [Aktualisieren Sie Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md), um den Verbindungsstatus überprüfen zu können.
* Leider können wir keine Verbindung mit Ihrem lokalen Client für das Rückschreiben herstellen. [Führen Sie eine Problembehandlung für Azure AD Connect durch](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity), um die Verbindung wiederherzustellen.
* Leider können wir keine Verbindung mit Ihrem lokalen Client für das Rückschreiben herstellen, weil das Kennwortrückschreiben nicht ordnungsgemäß konfiguriert wurde. [Konfigurieren Sie das Kennwortrückschreiben](howto-sspr-writeback.md), um die Verbindung wiederherzustellen.
* Leider können wir keine Verbindung mit Ihrem lokalen Client für das Rückschreiben herstellen. Möglicherweise liegen auf unserer Seite vorübergehende Probleme vor. Wenn das Problem weiterhin besteht, [führen Sie eine Problembehandlung für Azure AD Connect durch](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity), um die Verbindung wiederherzustellen.

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Kennwörter in Ihr lokales Verzeichnis zurückschreiben?

Dieses Steuerelement bestimmt, ob das Rückschreiben von Kennwörtern für dieses Verzeichnis aktiviert ist. Wenn das Rückschreiben aktiviert ist, gibt es den Status des lokalen Diensts für das Rückschreiben an. Dieses Steuerelement ist nützlich, wenn Sie das Kennwortrückschreiben vorübergehend deaktivieren möchten, ohne Azure AD Connect erneut zu konfigurieren.

* Wenn die Option auf **Ja** gesetzt ist, wird das Rückschreiben aktiviert, und Verbundbenutzer und Benutzer mit Pass-Through-Authentifizierung oder mit Kennworthashsynchronisierung können ihre Kennwörter zurücksetzen.
* Wenn die Option auf **Nein** gesetzt ist, wird das Rückschreiben deaktiviert, und Verbundbenutzer und Benutzer mit Pass-Through-Authentifizierung oder mit Kennworthashsynchronisierung können ihre Kennwörter nicht zurücksetzen.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Benutzern das Entsperren von Konten ohne Zurücksetzen des Kennworts erlauben

Dieses Steuerelement legt fest, ob Benutzer, die das Kennwortzurücksetzungsportal aufrufen, die Option zum Entsperren ihrer lokalen Active Directory-Konten ohne Zurücksetzen ihres Kennworts erhalten sollen. Standardmäßig werden bei einer Kennwortzurücksetzung Konten von Azure AD entsperrt. Mit dieser Einstellung können Sie diese beiden Vorgänge trennen.

* Bei der Einstellung **Ja** erhalten Benutzer die Option zum Zurücksetzen ihres Kennworts und Entsperren ihres Kontos oder die Option zum Entsperren des Kontos, ohne dass das Kennwort zurückgesetzt werden muss.
* Bei der Einstellung **Nein** können Benutzer das Entsperren des Kontos nur in Kombination mit dem Zurücksetzen des Kennworts vornehmen.

### <a name="on-premises-active-directory-password-filters"></a>Lokale Active Directory-Kennwortfilter

Die Azure AD-Self-Service-Kennwortzurücksetzung ist äquivalent zu einer vom Administrator ausgelösten Kennwortzurücksetzung in Active Directory. Wenn Sie einen Kennwortfilter eines Drittanbieters verwenden, um benutzerdefinierte Kennwortrichtlinien durchzusetzen, und Sie die Überprüfung dieses Kennwortfilters während der Azure AD-Self-Service-Kennwortzurücksetzung als erforderlich festlegen, müssen Sie sicherstellen, dass der Kennwortfilter des Drittanbieters so konfiguriert ist, dass er im Szenario der Kennwortzurücksetzung durch den Administrator angewendet wird. Der [Azure AD-Kennwortschutz für Windows Server Active Directory](concept-password-ban-bad-on-premises.md) wird standardmäßig unterstützt.

## <a name="password-reset-for-b2b-users"></a>Kennwortzurücksetzung für B2B-Benutzer

Das Zurücksetzen und Ändern von Kennwörtern wird von allen B2B-Konfigurationen (Business-to-Business) uneingeschränkt unterstützt. Das Zurücksetzen von B2B-Benutzerkennwörtern wird in den folgenden drei Fällen unterstützt:

* **Benutzer aus einer Partnerorganisation mit einem vorhandenen Azure AD-Mandanten:** Falls die Organisation, mit der Sie eine Partnerschaft eingegangen sind, über einen Azure AD-Mandanten verfügt, *respektieren wir die in diesem Mandanten aktivierten Kennwortzurücksetzungsrichtlinien*. Damit die Kennwortzurücksetzung funktioniert, muss die Partnerorganisation nur sicherstellen, dass Azure AD SSPR aktiviert ist. Für Office 365-Kunden fallen keine zusätzlichen Gebühren an, und Azure AD SSPR kann anhand der Schritte unter [Schnelle Bereitstellung der Self-Service-Kennwortzurücksetzung in Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) aktiviert werden.
* **Benutzer, die sich per Self-Service-Registrierung registriert haben:** Wenn die Organisation, mit der Sie eine Partnerschaft eingegangen sind, das [Feature für Self-Service-Registrierung](../users-groups-roles/directory-self-service-signup.md) verwendet, um auf einen Mandanten zuzugreifen, steht eine Zurücksetzung des Kennworts unter Verwendung der E-Mail-Adresse, mit der die Registrierung erfolgt ist, zur Verfügung.
* **B2B-Benutzer:** Alle neuen B2B-Benutzer, die mithilfe der neuen [Azure AD-B2B-Funktionen](../active-directory-b2b-what-is-azure-ad-b2b.md) erstellt werden, können ihre Kennwörter unter Verwendung der E-Mail-Adresse zurücksetzen, mit der sie sich im Rahmen des Einladungsprozesses registriert haben.

Um dieses Szenario zu testen, wechseln Sie mit einem der Partnerbenutzer zu https://passwordreset.microsoftonline.com. Sofern eine alternative E-Mail-Adresse oder eine E-Mail-Adresse für die Authentifizierung definiert ist, funktioniert die Kennwortzurücksetzung wie erwartet.

> [!NOTE]
> Microsoft-Konten (etwa Hotmail.com, Outlook.com oder eine andere persönliche E-Mail-Adresse), denen der Gastzugriff auf Ihren Azure AD-Mandanten gewährt wurde, können Azure AD SSPR nicht nutzen. Für diese Konten muss das Kennwort anhand der Informationen im Artikel [Wenn Sie sich nicht bei Ihrem Microsoft-Konto anmelden können](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) zurückgesetzt werden.

## <a name="next-steps"></a>Nächste Schritte

Die folgenden Artikel führen zu weiteren Informationen zur Kennwortzurücksetzung mit Azure AD:

* [Erfolgreiches Rollout der Self-Service-Kennwortzurücksetzung](howto-sspr-deployment.md)
* [Zurücksetzen oder Ändern des Kennworts](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrieren für die Self-Service-Kennwortzurücksetzung](../user-help/active-directory-passwords-reset-register.md)
* [Lizenzanforderungen für Azure AD-Self-Service-Kennwortzurücksetzung](concept-sspr-licensing.md)
* [Bereitstellen der Kennwortzurücksetzung ohne erforderliche Endbenutzerregistrierung](howto-sspr-authenticationdata.md)
* [Authentifizierungsmethoden](concept-sspr-howitworks.md#authentication-methods)
* [Kennwortrichtlinien und -einschränkungen in Azure Active Directory](concept-sspr-policy.md)
* [Übersicht über die Kennwortrückschreibung](howto-sspr-writeback.md)
* [Berichterstellungsoptionen für die Kennwortverwaltung von Azure AD](howto-sspr-reporting.md)
* [Welche Optionen sind für SSPR verfügbar, und was bedeuten sie?](concept-sspr-howitworks.md)
* [Anscheinend ist ein Fehler aufgetreten. Wie behebe ich Probleme mit SSPR?](active-directory-passwords-troubleshoot.md)
* [Ich habe eine Frage, die nicht an einer anderen Stelle abgedeckt wurde.](active-directory-passwords-faq.md)

[Authentication]: ./media/concept-sspr-howitworks/sspr-authentication-methods.png "Verfügbare Azure AD-Authentifizierungsmethoden und erforderliche Menge"
[Writeback]: ./media/concept-sspr-howitworks/troubleshoot-writeback-running.png "Informationen zur Konfiguration und Problembehandlung des Kennwortrückschreibens für die lokale Integration"
