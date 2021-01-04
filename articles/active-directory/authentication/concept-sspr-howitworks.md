---
title: Ausführliche Betrachtung der Self-Service-Kennwortzurücksetzung – Azure Active Directory
description: Funktionsweise der Self-Service-Kennwortzurücksetzung
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/07/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5bcc6503af5f5f685ee589ed7671b4715834fef
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779361"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>So funktioniert's: Self-Service-Kennwortzurücksetzung in Azure AD

Mit der Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) von Azure Active Directory (Azure AD) können Benutzer ihr Kennwort ohne Beteiligung eines Administrators oder des Helpdesks ändern oder zurücksetzen. Wenn das Konto eines Benutzers gesperrt ist oder dieser sein Kennwort vergessen hat, kann er die Schritte zum Entsperren ausführen und anschließend weiterarbeiten. Dies führt zu weniger Anrufen beim Helpdesk und Produktivitätsverlusten, wenn sich ein Benutzer nicht an seinem Gerät oder einer Anwendung anmelden kann.

> [!IMPORTANT]
> In diesem Konzeptartikel erfahren Administratoren, wie die Self-Service-Kennwortzurücksetzung funktioniert. Wenn Sie bereits als Endbenutzer für die Self-Service-Kennwortzurücksetzung registriert sind und den Zugriff auf Ihr Konto verloren haben, navigieren Sie zu [https://aka.ms/sspr](https://aka.ms/sspr).
>
> Wenn Ihr IT-Team die Möglichkeit zum Zurücksetzen Ihres eigenen Kennworts nicht aktiviert hat, wenden Sie sich an den Helpdesk, um weitere Unterstützung zu erhalten.

## <a name="how-does-the-password-reset-process-work"></a>Wie funktioniert der Vorgang zur Kennwortzurücksetzung?

Ein Benutzer kann sein Kennwort über das [SSPR-Portal](https://aka.ms/sspr) zurücksetzen oder ändern. Dazu müssen jedoch zunächst die gewünschten Authentifizierungsmethoden registriert worden sein. Wenn ein Benutzer auf das SSPR-Portal zugreift, berücksichtigt die Azure-Plattform Folgendes:

* Wie soll die Seite lokalisiert werden?
* Ist das Benutzerkonto gültig?
* Zu welcher Organisation gehört der Benutzer?
* Wo wird das Kennwort des Benutzers verwaltet?
* Ist der Benutzer zur Verwendung des Features lizenziert?

Wenn ein Benutzer in einer Anwendung oder auf einer Seite auf den Link **Zugriff auf Ihr Konto nicht möglich** klickt oder direkt zu [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) navigiert, wird die im SSPR-Portal angezeigte Sprache nach folgenden Kriterien ausgewählt:

* Standardmäßig wird das Gebietsschema des Browsers verwendet, um die Self-Service-Kennwortzurücksetzung in der richtigen Sprache anzuzeigen. Die Benutzeroberfläche für die Kennwortzurücksetzung wurde in alle Sprachen lokalisiert, die [Microsoft 365 unterstützt](https://support.microsoft.com/office/what-languages-is-office-available-in-26d30382-9fba-45dd-bf55-02ab03e2a7ec).
* Wenn Sie das SSPR-Portal in einer bestimmten Sprache verlinken möchten, müssen Sie `?mkt=` zusammen mit dem gewünschten Gebietsschema an die URL für die Kennwortzurücksetzung anfügen.
    * Wenn Sie beispielsweise das spanische Gebietsschema *es-us* angeben möchten, verwenden Sie `?mkt=es-us` - [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us).

Wenn das SSPR-Portal in der gewünschten Sprache angezeigt wird, wird der Benutzer aufgefordert, eine Benutzer-ID einzugeben und eine CAPTCHA-Prüfung abzulegen. Azure AD überprüft anschließend wie folgt, ob der Benutzer das SSPR-Feature verwenden kann:

* Es wird geprüft, ob das Feature für den Benutzer aktiviert und ob es einer Azure AD-Lizenz zugewiesen ist.
  * Wenn das Feature nicht für den Benutzer aktiviert oder keiner Lizenz zugewiesen ist, wird der Benutzer aufgefordert, sich zum Zurücksetzen des Kennworts an den Administrator zu wenden.
* Es wird überprüft, ob der Benutzer in seinem Konto die richtigen Authentifizierungsmethoden definiert hat, die der Administratorrichtlinie entsprechen.
  * Wenn die Richtlinie nur eine Methode erfordert, wird sichergestellt, dass der Benutzer für mindestens eine durch die Administratorrichtlinie festgelegte Authentifizierungsmethode geeignete Daten definiert hat.
    * Wenn die Authentifizierungsmethoden nicht konfiguriert sind, wird der Benutzer aufgefordert, sich an den Administrator zu wenden, um sein Kennwort zurückzusetzen.
  * Wenn die Richtlinie zwei Methoden erfordert, wird sichergestellt, dass der Benutzer für mindestens zwei durch die Administratorrichtlinie festgelegte Authentifizierungsmethoden geeignete Daten definiert hat.
    * Wenn die Authentifizierungsmethoden nicht konfiguriert sind, wird der Benutzer aufgefordert, sich an den Administrator zu wenden, um sein Kennwort zurückzusetzen.
  * Wenn einem Benutzer eine Azure-Administratorrolle zugewiesen wird, wird dadurch auch die sichere Zwei-Wege-Kennwortrichtlinie erzwungen. Weitere Informationen finden Sie unter [Unterschiede zu Richtlinien zum Zurücksetzen von Administratorkennwörtern](concept-sspr-policy.md#administrator-reset-policy-differences).
* Es wird überprüft, ob das Benutzerkennwort lokal verwaltet wird, d. h., ob der Azure AD-Mandant die Verbundauthentifizierung, die Pass-Through-Authentifizierung oder die Kennworthashsynchronisierung verwendet:
  * Wenn das Kennwortrückschreiben für den SSPR konfiguriert ist und das Benutzerkennwort lokal verwaltet wird, kann der Benutzer mit der Authentifizierung fortfahren und sein Kennwort zurücksetzen.
  * Wenn das Kennwortrückschreiben nicht für den SSPR aktiviert ist und das Benutzerkennwort lokal verwaltet wird, wird der Benutzer aufgefordert, sich zum Zurückzusetzen des Kennworts an den Administrator zu wenden.

Wenn alle vorherigen Überprüfungen erfolgreich abgeschlossen wurden, wird der Benutzer durch die Kennwortzurücksetzung oder -änderung geführt.

> [!NOTE]
> SSPR sendet im Rahmen des Vorgangs zur Kennwortzurücksetzung möglicherweise E-Mail-Benachrichtigungen an Benutzer. Diese E-Mails werden mithilfe des SMTP-Relaydiensts gesendet, der in mehreren Regionen in einem Aktiv/Aktiv-Modus betrieben wird.
>
> SMTP-Relaydienste empfangen und verarbeiten den E-Mail-Text, speichern ihn aber nicht. Der Text der SSPR-E-Mail, der möglicherweise vom Kunden bereitgestellte Informationen enthält, wird in den SMTP-Relaydienstprotokollen nicht gespeichert. Die Protokolle enthalten nur Protokollmetadaten.

Nutzen Sie das folgende Tutorial, um sich mit SSPR vertraut zu machen:

> [!div class="nextstepaction"]
> [Tutorial: Aktivieren der Self-Service-Kennwortzurücksetzung (SSPR)](tutorial-enable-sspr.md)


## <a name="require-users-to-register-when-they-sign-in"></a>Erzwingen der Registrierung für Benutzer bei der Anmeldung

Sie können diese Option aktivieren, damit Benutzer die Registrierung für den SSPR abschließen müssen, wenn sie sich mit Azure AD in einer Anwendung anmelden. Dieser Workflow schließt die folgenden Anwendungen ein:

* Microsoft 365
* Azure-Portal
* Anpassung des Zugriffsbereichs
* Verbundanwendungen
* Benutzerdefinierte Anwendungen, die Azure AD verwenden

Wenn die Registrierung nicht erzwungen wird, werden Benutzer nicht während der Anmeldung dazu aufgefordert. Sie können sich jedoch manuell registrieren. Dazu können Benutzer entweder [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) aufrufen oder im Zugriffsbereich auf der Registerkarte **Profil** auf den Link **Für das Zurücksetzen des Kennworts registrieren** klicken.

![Registrierungsoptionen für den SSPR im Azure-Portal][Registration]

> [!NOTE]
> Benutzer können das Registrierungsportal für den SSPR durch Klicken auf **Abbrechen** oder durch Schließen des Fensters schließen. Sie werden jedoch bei jeder Anmeldung zur Registrierung aufgefordert, bis die Registrierung durchgeführt wurde.
>
> Durch diese Aufforderung zur Registrierung für den SSPR wird die Verbindung eines Benutzers nicht unterbrochen, wenn er bereits angemeldet ist.

## <a name="reconfirm-authentication-information"></a>Erneute Bestätigung der Authentifizierungsinformationen

Sie können erzwingen, dass Benutzer ihre registrierten Informationen nach einem bestimmten Zeitraum bestätigen müssen, um sicherzustellen, dass die Authentifizierungsmethoden im Falle einer Kennwortzurücksetzung oder -änderung korrekt sind. Diese Option ist nur verfügbar, wenn Sie die Option **Registrierung von Benutzern bei der Anmeldung verlangen?** aktivieren.

Gültige Werte für die Aufforderung zur Bestätigung der registrierten Authentifizierungsmethoden reichen von *0* bis *730* Tagen. Wenn Sie den Wert auf *0* festlegen, werden Benutzer nie aufgefordert, ihre Authentifizierungsinformationen zu bestätigen.

## <a name="authentication-methods"></a>Authentifizierungsmethoden

Wenn der SSPR für einen Benutzer konfiguriert ist, muss der Benutzer mindestens eine Authentifizierungsmethode registrieren. Es wird jedoch dringend empfohlen, mindestens zwei Authentifizierungsmethoden auszuwählen, damit Ihre Benutzer ausweichen können, falls auf eine der Methoden kein Zugriff besteht. Weitere Informationen finden Sie unter [Authentifizierungsmethoden](concept-authentication-methods.md).

Die folgenden Authentifizierungsmethoden sind für den SSPR verfügbar:

* Benachrichtigung über eine mobile App
* Code der mobilen App:
* Email
* Mobiltelefon
* Bürotelefon
* Sicherheitsfragen

Benutzer können ihr Kennwort nur zurücksetzen, wenn sie eine Authentifizierungsmethode registriert haben, die vom Administrator freigegeben wurde.

> [!WARNING]
> Konten, denen eine Azure-*Administratorrolle* zugewiesen wurde, müssen Methoden nutzen, die im Abschnitt [Unterschiede zu Richtlinien zum Zurücksetzen von Administratorkennwörtern](concept-sspr-policy.md#administrator-reset-policy-differences) definiert sind.

![Auswahl von Authentifizierungsmethoden im Azure-Portal][Authentication]

### <a name="number-of-authentication-methods-required"></a>Anzahl erforderlicher Authentifizierungsmethoden

Sie können die Anzahl der verfügbaren Authentifizierungsmethoden konfigurieren, die ein Benutzer bereitstellen muss, um sein Kennwort zurückzusetzen oder freizuschalten. Dieser Wert kann auf *1* oder *2* festgelegt werden.

Benutzer können (und sollten) mehrere Authentifizierungsmethoden registrieren. Es wird dringend empfohlen, mindestens zwei Authentifizierungsmethoden auszuwählen, damit Ihre Benutzer ausweichen können, falls auf eine der Methoden kein Zugriff besteht.

Versucht ein Benutzer, den SSPR zu nutzen, ohne die erforderliche Mindestanzahl von Methoden registriert zu haben, wird eine Fehlerseite angezeigt, die ihn auffordert, einen Administrator um die Kennwortzurücksetzung zu bitten. Gehen Sie beim Heraufsetzen der Anzahl der erforderlichen Methoden von eins auf zwei sehr vorsichtig vor, wenn einige Ihrer bestehenden Benutzer für den SSPR registriert sind und das Feature nicht verwenden können. Weitere Informationen finden Sie im Abschnitt zum [Ändern von Authentifizierungsmethoden](#change-authentication-methods).

#### <a name="mobile-app-and-sspr"></a>Mobile App und SSPR

Wenn Sie für die Kennwortzurücksetzung eine mobile App wie die Microsoft Authenticator-App verwenden, sollten Sie Folgendes beachten:

* Wenn Administratoren eine Methode zum Zurücksetzen von Kennwörtern erzwingen, steht als einzige Option der Prüfcode zur Verfügung.
* Wenn Administratoren zwei Methoden für die Kennwortzurücksetzung voraussetzen, können Benutzer zusätzlich zu anderen aktivierten Methoden Benachrichtigungen **ODER** Prüfcodes verwenden.

| Anzahl von erforderlichen Methoden zum Zurücksetzen | Eine | Zwei |
| :---: | :---: | :---: |
| Verfügbare Funktionen der mobilen App | Code | Code oder Benachrichtigung |

Benutzer können ihre mobile App nicht registrieren, wenn sie sich unter [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) für die Self-Service-Kennwortzurücksetzung registrieren. Die Benutzer haben die Möglichkeit, ihre mobile App unter [https://aka.ms/mfasetup](https://aka.ms/mfasetup) oder bei der kombinierten Registrierung von Sicherheitsinformationen unter [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) zu registrieren.

> [!IMPORTANT]
> Die Authenticator-App kann nicht als einzige Authentifizierungsmethode ausgewählt werden, wenn nur eine einzelne Methode erforderlich ist. Ebenso können nicht die Authenticator-App und nur eine zusätzliche Methode ausgewählt werden, wenn zwei Methoden festgelegt werden müssen.
>
> Wenn Sie SSPR-Richtlinien konfigurieren, die die Authenticator-App als Methode vorsehen, sollten bei einer oder zwei erforderlichen Methoden jeweils eine bzw. zwei zusätzliche Methoden konfiguriert werden.
>
> Dies ist erforderlich, da es bei der aktuellen SSPR-Registrierung nicht möglich ist, die Authenticator-App zu registrieren. Die Option zum Registrieren der Authenticator-App ist in der neuen [kombinierten Registrierung](./concept-registration-mfa-sspr-combined.md) enthalten.
>
> Das Zulassen von Richtlinien, die nur die Authenticator-App (bei einer erforderlichen Methode) oder die Authenticator-App und nur eine zusätzliche Methode (bei zwei erforderlichen Methoden) verwenden, kann dazu führen, dass die SSPR-Registrierung für Benutzer nicht verfügbar ist, solange die neue kombinierte Registrierung nicht für sie konfiguriert wurde.

### <a name="change-authentication-methods"></a>Ändern der Authentifizierungsmethoden

Was passiert, wenn Sie mit einer Richtlinie beginnen, bei der zum Zurücksetzen oder Entsperren der Registrierung nur eine einzelne Authentifizierungsmethode erforderlich ist, und Sie dies in zwei Authentifizierungsmethoden ändern?

| Anzahl der registrierten Methoden | Anzahl der erforderlichen Methoden | Ergebnis |
| :---: | :---: | :---: |
| Mindestens 1 | 1 | **Kann** zurücksetzen oder entsperren |
| 1 | 2 | **Kann nicht** zurücksetzen oder entsperren |
| 2 oder mehr | 2 | **Kann** zurücksetzen oder entsperren |

Eine Änderung der verfügbaren Authentifizierungsmethoden kann ebenso Probleme für Ihre Benutzer verursachen. Wenn Sie die den Benutzern zur Verfügung stehenden Arten von Authentifizierungsmethoden ändern, können Benutzer unter Umständen SSPR nicht mehr verwenden, wenn ihnen nicht genügend Daten zur Verfügung stehen.

Betrachten Sie das folgende Beispielszenario:

1. Die ursprüngliche Richtlinie wird mit zwei erforderlichen Authentifizierungsmethoden konfiguriert. Sie verwendet nur die Bürotelefonnummer und die Sicherheitsfragen.
1. Der Administrator ändert die Richtlinie so, dass anstelle von Sicherheitsfragen ein Mobiltelefon und eine alternative E-Mail-Adresse verwendet werden.
1. Benutzer, für die das Feld zum Mobiltelefon oder der alternativen E-Mail-Adresse nicht aufgefüllt wurde, können ihre Kennwörter jetzt nicht mehr zurücksetzen.

## <a name="notifications"></a>Benachrichtigungen

Mit dem SSPR können Sie sowohl für Benutzer als auch für Identitätsadministratoren Benachrichtigungen konfigurieren, um das Augenmerk auf Kennwortereignisse zu stärken.

### <a name="notify-users-on-password-resets"></a>Benutzer über Kennwortzurücksetzungen benachrichtigen?

Ist diese Option auf **Ja** festgelegt, erhalten Benutzer, die ihr Kennwort zurücksetzen, eine E-Mail mit dem Hinweis, dass ihr Kennwort geändert wurde. Die E-Mail wird über das SSPR-Portal an die in Azure AD hinterlegte primäre und alternative E-Mail-Adresse gesendet. Niemand sonst wird über das Zurücksetzen informiert.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Sollen alle Administratoren benachrichtigt werden, wenn andere Administratoren ihr Kennwort zurücksetzen?

Ist diese Option auf **Ja** festgelegt, erhalten alle anderen Azure-Administratoren eine E-Mail an ihre in Azure AD hinterlegte primäre E-Mail-Adresse. In dieser E-Mail werden sie darüber informiert, dass ein anderer Administrator ihr Kennwort mithilfe von SSPR geändert hat.

Betrachten Sie das folgende Beispielszenario:

* Es gibt vier Administratoren in einer Umgebung.
* Administrator *A* setzt das Kennwort mithilfe des SSPR zurück.
* Die Administratoren *B*, *C* und *D* werden per E-Mail über die Kennwortzurücksetzung informiert.

## <a name="on-premises-integration"></a>Lokale Integration

In einer Hybridumgebung können Sie Azure AD Connect so konfigurieren, dass Kennwortänderungsereignisse aus Azure AD zurück in ein lokales Verzeichnis geschrieben werden.

![Überprüfen von Aktivierung und Funktionsfähigkeit des Kennwortrückschreibens][Writeback]

Azure AD überprüft Ihre aktuelle Hybridverbindung und zeigt eine der folgenden Nachrichten im Azure-Portal an:

* Ihr lokaler Client für das Rückschreiben ist einsatzbereit.
* Azure AD Connect ist online und mit Ihrem lokalen Client für das Rückschreiben verbunden. Die installierte Version von Azure AD Connect ist jedoch offenbar veraltet. Ziehen Sie ein [Upgrade von Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) in Betracht, um sicherzustellen, dass Sie über die neuesten Konnektivitätsfeatures und wichtige Fehlerbehebungen verfügen.
* Unfortunately, we can't check your on-premises writeback client status because the installed version of Azure AD Connect is out-of-date. (Der Status Ihres lokalen Rückschreibeclients kann nicht überprüft werden, weil die installierte Version von Azure AD Connect veraltet ist.) [Aktualisieren Sie Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md), um den Verbindungsstatus überprüfen zu können.
* Leider können wir keine Verbindung mit Ihrem lokalen Client für das Rückschreiben herstellen. [Führen Sie eine Problembehandlung für Azure AD Connect durch](./troubleshoot-sspr-writeback.md), um die Verbindung wiederherzustellen.
* Leider können wir keine Verbindung mit Ihrem lokalen Client für das Rückschreiben herstellen, weil das Kennwortrückschreiben nicht ordnungsgemäß konfiguriert wurde. [Konfigurieren Sie das Kennwortrückschreiben](./tutorial-enable-sspr-writeback.md), um die Verbindung wiederherzustellen.
* Leider können wir keine Verbindung mit Ihrem lokalen Client für das Rückschreiben herstellen. Möglicherweise liegen auf unserer Seite vorübergehende Probleme vor. Wenn das Problem weiterhin besteht, [führen Sie eine Problembehandlung für Azure AD Connect durch](./troubleshoot-sspr-writeback.md), um die Verbindung wiederherzustellen.

Nutzen Sie das folgende Tutorial, um sich mit dem SSPR-Rückschreiben vertraut zu machen:

> [!div class="nextstepaction"]
> [Tutorial: Aktivieren des Rückschreibens von Self-Service-Kennwortzurücksetzungen (SSPR)](./tutorial-enable-sspr-writeback.md)

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Kennwörter in Ihr lokales Verzeichnis zurückschreiben?

Das Kennwortrückschreiben kann im Azure-Portal aktiviert werden. Sie können dieses Feature auch vorübergehend deaktivieren, ohne Azure AD Connect neu zu konfigurieren.

* Ist die Option auf **Ja** festgelegt, ist das Rückschreiben aktiviert. Verbundbenutzer, Benutzer mit Pass-Through-Authentifizierung oder Benutzer mit Kennworthashsynchronisierung können ihre Kennwörter zurücksetzen.
* Ist die Option auf **Nein** festgelegt, ist das Rückschreiben deaktiviert. Verbundbenutzer, Benutzer mit Pass-Through-Authentifizierung oder Benutzer mit Kennworthashsynchronisierung können ihre Kennwörter nicht zurücksetzen.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Benutzern das Entsperren von Konten ohne Zurücksetzen des Kennworts erlauben

Standardmäßig werden bei einer Kennwortzurücksetzung Konten von Azure AD entsperrt. Zur Steigerung de Flexibilität können Sie auch zulassen, dass Benutzer ihre lokalen Konten entsperren können, ohne das Kennwort zurückzusetzen. Mit dieser Einstellung können Sie die folgenden beiden Vorgänge trennen.

* Bei der Einstellung **Ja** erhalten Benutzer die Option, das Kennwort zurückzusetzen und ihre Konto zu entsperren oder das Konto zu entsperren, ohne dass das Kennwort zurückgesetzt werden muss.
* Bei der Einstellung **Nein** können Benutzer das Entsperren des Kontos nur in Kombination mit dem Zurücksetzen des Kennworts vornehmen.

### <a name="on-premises-active-directory-password-filters"></a>Lokale Active Directory-Kennwortfilter

Der SSPR ist äquivalent zu einer vom Administrator ausgelösten Kennwortzurücksetzung in Active Directory. Wenn Sie einen Kennwortfilter eines Drittanbieters verwenden, um benutzerdefinierte Kennwortrichtlinien durchzusetzen, und die Überprüfung dieses Kennwortfilters während des Azure AD-SSPR erzwingen, müssen Sie sicherstellen, dass dieser Kennwortfilter so konfiguriert ist, dass er auch bei einer Kennwortzurücksetzung durch den Administrator angewendet wird. Der [Azure AD-Kennwortschutz für Active Directory Domain Services](concept-password-ban-bad-on-premises.md) wird standardmäßig unterstützt.

## <a name="password-reset-for-b2b-users"></a>Kennwortzurücksetzung für B2B-Benutzer

Das Zurücksetzen und Ändern von Kennwörtern wird von allen B2B-Konfigurationen (Business-to-Business) uneingeschränkt unterstützt. Das Zurücksetzen von B2B-Benutzerkennwörtern wird in den folgenden drei Fällen unterstützt:

* **Benutzer aus einer Partnerorganisation mit einem vorhandenen Azure AD-Mandanten:** Falls die Organisation, mit der Sie eine Partnerschaft eingegangen sind, über einen bestehenden Azure AD-Mandanten verfügt, werden alle in diesem Mandanten aktivierten Richtlinien für die Kennwortzurücksetzung berücksichtigt. Damit die Kennwortzurücksetzung funktioniert, muss die Partnerorganisation nur sicherstellen, dass Azure AD SSPR aktiviert ist. Es fallen keine zusätzlichen Gebühren für Microsoft 365-Kunden an.
* **Benutzer, die sich per Self-Service-Registrierung registriert haben:** Wenn die Organisation, mit der Sie eine Partnerschaft eingegangen sind, die [Self-Service-Registrierung](../enterprise-users/directory-self-service-signup.md) für den Zugriff auf einen Mandanten verwendet, kann das Kennwort mithilfe der registrierten E-Mail-Adresse zurückgesetzt werden.
* **B2B-Benutzer:** Alle neuen B2B-Benutzer, die mithilfe der neuen [Azure AD-B2B-Funktionen](../external-identities/what-is-b2b.md) erstellt werden, können ihre Kennwörter unter Verwendung der E-Mail-Adresse zurücksetzen, mit der sie sich im Rahmen des Einladungsprozesses registriert haben.

Um dieses Szenario zu testen, wechseln Sie mit einem der Partnerbenutzer zu https://passwordreset.microsoftonline.com. Sofern eine alternative E-Mail-Adresse oder eine E-Mail-Adresse für die Authentifizierung definiert ist, funktioniert die Kennwortzurücksetzung wie erwartet.

> [!NOTE]
> Microsoft-Konten (etwa Hotmail.com, Outlook.com oder eine andere persönliche E-Mail-Adresse), denen der Gastzugriff auf Ihren Azure AD-Mandanten gewährt wurde, können den Azure AD-SSPR nicht nutzen. Für diese Konten muss das Kennwort anhand der Informationen im Artikel [Wenn Sie sich nicht bei Ihrem Microsoft-Konto anmelden können](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant) zurückgesetzt werden.

## <a name="next-steps"></a>Nächste Schritte

Nutzen Sie das folgende Tutorial, um sich mit SSPR vertraut zu machen:

> [!div class="nextstepaction"]
> [Tutorial: Aktivieren der Self-Service-Kennwortzurücksetzung (SSPR)](tutorial-enable-sspr.md)

Die folgenden Artikel führen zu weiteren Informationen zur Kennwortzurücksetzung mit Azure AD:

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Verfügbare Azure AD-Authentifizierungsmethoden und erforderliche Menge"
[Registration]: ./media/concept-sspr-howitworks/configure-registration-options.png "Konfigurieren der SSPR-Registrierungsoptionen im Azure-Portal"
[Writeback]: ./media/concept-sspr-howitworks/on-premises-integration.png "Lokale Integration für den SSPR im Azure-Portal"