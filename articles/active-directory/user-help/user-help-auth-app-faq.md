---
title: Fragen und Antworten zur Microsoft Authenticator-App – Azure AD
description: Häufig gestellte Fragen und Antworten über die Microsoft Authenticator-App und die Zweistufige Überprüfung
services: active-directory
author: curtand
manager: daveba
ms.assetid: f04d5bce-e99e-4f75-82d1-ef6369be3402
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/28/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: 84dde96276414e98cbde05d6e046dce03227f60d
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547858"
---
# <a name="frequently-asked-questions-faq-about-the-microsoft-authenticator-app"></a>Häufig gestellte Fragen zur Microsoft Authenticator-App

In diesem Artikel werden häufig gestellte Fragen zur Microsoft Authenticator-App beantwortet. Wenn Sie keine Antwort auf Ihre Frage finden, wechseln Sie zum [Forum zur Microsoft Authenticator-App](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp).

Die Microsoft Authenticator-App ersetzt die Azure Authenticator-App und ist die empfohlene App für Azure AD Multi-Factor Authentication. Die Microsoft Authenticator-App ist für [Android](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fplay.google.com%2Fstore%2Fapps%2Fdetails%3Fid%3Dcom.azure.authenticator) und [iOS](https://app.adjust.com/e3rxkc_7lfdtm?fallback=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fmicrosoft-authenticator%2Fid983156458) verfügbar.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="registering-a-device"></a>Registrieren eines Geräts

**F:** Bedeutet die Registrierung eines Geräts die Zustimmung für den Zugriff auf das Gerät durch das Unternehmen oder den Dienst?

**A:** Durch das Registrieren eines Geräts erhält Ihr Gerät Zugriff auf die Dienste Ihrer Organisation, ohne dass Sie Ihrer Organisation erlauben, auf Ihr Gerät zuzugreifen.

### <a name="error-adding-account"></a>Fehler beim Hinzufügen des Kontos

**F:** Wenn ich versuche, mein Konto hinzuzufügen, erhalte ich etwa folgende Fehlermeldung: „Das Konto, das Sie hinzufügen möchten, ist zu diesem Zeitpunkt ungültig. Wenden Sie sich an Ihren Administrator, um dieses Problem zu beheben (Eindeutigkeitsüberprüfung).“ Wie sollte ich vorgehen?

**A:** Wenden Sie sich an Ihren Administrator, und informieren Sie ihn darüber, dass Sie aufgrund eines Eindeutigkeitsüberprüfungsproblems am Hinzufügen Ihres Kontos zu einem Authentifikator gehindert wurden. Sie müssen Ihren Anmeldebenutzernamen angeben, damit Ihr Administrator Sie in Ihrer Organisation suchen kann.

### <a name="legacy-apns-support-deprecated"></a>Unterstützung für ältere APNs als veraltet markiert

**F:** Die ältere binäre Schnittstelle für den Apple Push Notification-Dienst wird im November 2020 als veraltet gekennzeichnet. Wie kann ich für die Anmeldung weiterhin Microsoft Authenticator/PhoneFactor verwenden?

**A:** [Apple hat angekündigt](https://developer.apple.com/news/?id=11042019a), Pushbenachrichtigungen als veraltet zu kennzeichnen, die die binäre Schnittstelle für iOS-Geräte verwenden, etwa die für PhoneFactor. Um weiterhin Pushbenachrichtigungen zu erhalten, sollten Benutzer ihre Authenticator-App auf die neueste Version aktualisieren. In der Zwischenzeit können Sie das Problem umgehen, indem Sie manuell in der Authenticator-App nach Benachrichtigungen suchen.

### <a name="app-lock-feature"></a>App-Sperre

**F:** Was ist die App-Sperre, und wie kann sie meine Sicherheit erhöhen?

**A:** Mit der App-Sperre können Sie die Sicherheit Ihrer Einmalprüfcodes, App-Informationen und App-Einstellungen erhöhen. Wenn die App-Sperre aktiviert ist, werden Sie bei jedem Öffnen von Authenticator aufgefordert, sich mit Ihrer Geräte-PIN oder Ihren biometrischen Daten zu authentifizieren. Mithilfe der App-Sperre können Sie auch sicherstellen, dass Sie die einzige Person sind, die Benachrichtigungen genehmigen kann, indem Sie bei jeder Genehmigung einer Anmeldebenachrichtigung zur Eingabe Ihrer PIN oder Ihrer biometrischen Daten aufgefordert werden. Sie können die App-Sperre auf der Seite mit den Authenticator-Einstellungen aktivieren oder deaktivieren. Die App-Sperre wird standardmäßig aktiviert, wenn Sie auf Ihrem Gerät eine PIN oder biometrische Daten einrichten.<br><br>Leider bietet auch die App-Sperre keine endgültige Garantie, dass Fremde sich keinen Zugriff auf Authenticator verschaffen können. Dies liegt daran, dass die Geräteregistrierung auch außerhalb von Authenticator erfolgen kann, z. B. in den Android-Kontoeinstellungen oder in der Unternehmensportal-App.

### <a name="windows-mobile-retired"></a>Windows Mobile eingestellt

**F:** Ich habe ein Windows Mobile-Gerät, und Microsoft Authenticator ist unter Windows Mobile veraltet. Kann ich die App weiterhin für die Authentifizierung benutzen?

**A:** Alle Authentifizierungsflows, für die unter Windows Mobile Microsoft Authenticator erforderlich ist, werden nach dem 15. Juli 2020 außer Betrieb genommen. Es wird dringend empfohlen, zu einer alternativen Authentifizierungsmethode zu wechseln, damit Sie nicht den Zugriff auf Ihre Konten verlieren.<br>Alternative Optionen für Unternehmensbenutzer sind beispielsweise:<br><ul><li>Einrichten von Microsoft Authenticator für [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) oder [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).</li><li>[Einrichten von SMS](multi-factor-authentication-setup-phone-number.md) für den Empfang von Prüfcodes.</li><li>Einrichten einer Telefonnummer für den Empfang von [Anrufen zum Identitätsnachweis](multi-factor-authentication-setup-office-phone.md).</li></ul><br>Alternative Optionen für Benutzer mit privaten Microsoft-Konten sind beispielsweise:<br><ul><li>Einrichten von Microsoft Authenticator für [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) oder [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458).</li><li>Die Einrichtung einer alternativen Anmeldemethode (SMS oder E-Mail), indem Sie Ihre Sicherheitsinformationen auf der [Seite mit den Sicherheitseinstellungen für Ihr Microsoft-Konto](https://account.microsoft.com/security/) aktualisieren</li></ul>

### <a name="android-screenshots"></a>Android-Screenshots

**F:** Kann ich in Android-Authenticator Screenshots von meinem einmaligen Kennwortcode (One-Time Password Code, OTP-Code) aufnehmen?

**A:** Ab Release 6.2003.1704 der Android-Version der Authenticator-App werden alle OTP-Codes automatisch verborgen, wenn ein Screenshot von Authenticator aufgenommen wird. Wenn Sie Ihre OTP-Codes auf einem Screenshot festhalten oder zulassen möchten, dass andere Apps den Bildschirm der Authenticator-App aufnehmen können, können Sie dies konfigurieren. Aktivieren Sie einfach die Einstellung **Bildschirmerfassung** in Authenticator, und starten Sie die App neu.

### <a name="delete-stored-data"></a>Löschen gespeicherter Daten

**F:** Was speichert Authenticator in meinem Namen, und wie kann ich diese Daten löschen?

**A:** Die Authenticator-App erfasst drei Arten von Informationen:

- Kontoinformationen, die Sie beim Hinzufügen Ihres Kontos angeben. Diese Daten können durch Entfernen Ihres Kontos entfernt werden.
- Diagnoseprotokolldaten, die sich nur in der App befinden, bis Sie die Option **Feedback senden** im oberen Menü der App auswählen, um die Protokolle an Microsoft zu senden. Diese Protokolle können personenbezogene Daten wie E-Mail-Adressen, Serveradressen oder IP-Adressen sowie Gerätedaten enthalten. Dazu zählen z. B. der Gerätename und die Betriebssystemversion. Personenbezogene Daten werden nur in soweit erfasst, wie es für die Behebung von App-Problemen erforderlich ist. Sie können diese Protokolldateien in der App jederzeit durchsuchen, um die erfassten Informationen anzuzeigen. Wenn Sie Ihre Protokolldateien senden, werden die Techniker der Authenticator-App diese ausschließlich zur Behebung von durch Kunden gemeldeten Problemen verwenden.
- Nicht personenbezogene Verwendungsdaten, z. B. „Ablauf zum Hinzufügen des Kontos gestartet/Konto erfolgreich hinzugefügt“ oder „Benachrichtigung genehmigt“. Diese Daten sind ein wesentlicher Faktor bei unseren Technikentscheidungen. Anhand Ihrer Nutzungsdaten erfahren wir, wie wir unsere Apps auf für unsere Kunden relevante Weise verbessern können. Sie werden über diese Datensammlung informiert, wenn Sie die App zum ersten Mal benutzen. In diesem Zusammenhang werden Sie darauf hingewiesen, dass die Datensammlung auf der App-Seite  **Einstellungen** deaktiviert werden kann. Sie können die Einstellung jederzeit aktivieren oder deaktivieren.

### <a name="codes-in-the-app"></a>Codes in der App

**F:** Wozu dienen die Codes in der App?

**A:** Wenn Sie Authenticator öffnen, werden Ihre hinzugefügten Konten als Kacheln angezeigt. Für Ihre Geschäfts-, Schul- oder Unikonten und Ihre privaten Microsoft-Konten werden in der Vollbildansicht des Kontos sechs- oder achtstellige Zahlen angezeigt (der Zugriff erfolgt durch Tippen auf die Kontokachel). Für andere Konten wird eine sechs bis achtstellige Zahl auf der App-Seite **Accounts** angezeigt.<br>Sie verwenden diese Codes als einmaliges Kennwort, um zu bestätigen, dass Sie derjenige sind, der Sie vorgeben, zu sein. Nachdem Sie sich mit Ihrem Benutzernamen und Kennwort angemeldet haben, geben Sie den Überprüfungscode ein, der dem Konto zugeordnet ist. Wenn Sie z. B. Katy sind und sich bei Ihrem Contoso-Konto anmelden, würden Sie auf die Kontokachel 895823 tippen und den entsprechenden Prüfcode verwenden. Für das Outlook-Konto führen Sie dieselben Schritte aus.<br>Tippen Sie auf die Kontokachel „Contoso“.<br>![Kontokacheln in der Authenticator-App](media/user-help-auth-app-faq/katy-signin.png)<br>Nachdem Sie auf die Kontokachel „Contoso“ getippt haben, wird der Prüfcode im Vollbildmodus angezeigt.<br>![Prüfcode in der Kontokachel in Authenticator](media/user-help-auth-app-faq/verification-code.png)

### <a name="countdown-timer"></a>Countdown

**F:** Warum wird die Zahl neben dem Code rückwärts gezählt?

**A:** Möglicherweise wird ein rückwärts zählender 30-Sekunden-Timer neben dem aktiven Überprüfungscode angezeigt. Dieser Timer bewirkt, dass Sie sich niemals zweimal mit demselben Code anmelden. Im Gegensatz zu einem Kennwort möchten wir nicht, dass Sie sich diese Zahl merken. Nur eine Person mit Zugriff auf Ihr Telefon soll Ihren Code kennen.

### <a name="grayed-account-tile"></a>Ausgegraute Kontokachel

**F:** Warum ist meine Kontokachel abgeblendet?

**A:** Einige Organisationen setzen Authenticator für das einmalige Anmelden und zum Schutz von Organisationsressourcen voraus. In diesem Fall wird das Konto nicht für die zweistufige Überprüfung verwendet und als abgeblendet oder inaktiv angezeigt. Diese Art von Konto wird häufig als „Broker“-Konto bezeichnet.

### <a name="device-registration"></a>Geräteregistrierung

**F:** Was ist die Geräteregistrierung?

**A:** Ihre Organisation kann Sie dazu auffordern, Ihr Gerät zu registrieren, um den Zugriff auf gesicherte Ressourcen wie Dateien und Apps nachverfolgen zu können. Sie kann außerdem den bedingten Zugriff aktivieren, um das Risiko des unberechtigten Zugriffs auf diese Ressourcen zu reduzieren. Sie können die Registrierung Ihres Geräts unter **Einstellungen** aufheben, aber dann verlieren Sie möglicherweise den Zugriff auf E-Mails in Outlook, Dateien in OneDrive und die Möglichkeit, die Anmeldung per Telefon zu verwenden.

### <a name="verification-codes-when-connected"></a>Prüfcodes bei Verbindung

**F:** Muss ich mit dem Internet oder meinem Netzwerk verbunden sein, um die Codes für die Überprüfung abzurufen und zu verwenden?

**A:** Die Codes setzen nicht voraus, dass Sie mit dem Internet oder sonstigen Daten verbunden sind, daher benötigen Sie keinen Telefondienst zur Anmeldung. Darüber hinaus wird Ihr Akku nicht belastet, da die Ausführung der App endet, sobald Sie sie schließen.

### <a name="no-notifications-when-app-is-closed"></a>Keine Benachrichtigungen bei geschlossener App

**F:** Warum erhalte ich nur dann Benachrichtigungen, wenn die App geöffnet ist? Wenn die App geschlossen ist, erhalte ich keine Benachrichtigungen.

**A:** Wenn Sie auch mit aktiviertem Rufton Benachrichtigungen, aber keine Warnung erhalten, sollten Sie Ihre App-Einstellungen überprüfen. Stellen Sie sicher, dass Sound oder Vibrieren bei Benachrichtigungen für die App aktiviert ist. Wenn Sie überhaupt keine Benachrichtigungen erhalten, überprüfen Sie Folgendes:<ul><li>Ist Ihr Telefon im Nicht-stören-Modus oder stummgeschaltet? Diese Modi können verhindern, dass Apps Benachrichtigungen senden.</li><li>Erhalten Sie Benachrichtigungen von anderen Apps? Wenn dies nicht der Fall ist, liegt möglicherweise ein Problem mit den Netzwerkverbindungen auf Ihrem Mobiltelefon oder mit dem Benachrichtigungskanal von Android oder Apple vor. Sie können versuchen, Ihre Netzwerkverbindungen über Ihre Telefoneinstellungen aufzulösen. Unter Umständen müssen Sie sich an Ihren Internetanbieter wenden, damit dieser mit dem Benachrichtigungskanal von Android oder Apple hilft.</li><li>Können Sie nur für bestimmte Konten in der App Benachrichtigungen erhalten? Falls Ja, entfernen Sie das problematische Konto aus Ihrer App, fügen Sie es erneut hinzu, sodass Benachrichtigungen möglich sind, und prüfen Sie, ob das Problem damit behoben ist.</li></ul>Wenn Sie alle diese Schritte versucht haben und immer noch Probleme auftreten, sollten Sie Ihre Protokolldateien zur Diagnose einsenden. Öffnen Sie die App, wechseln Sie zum oberen Menü der App, und wählen Sie  **Feedback senden** aus. Wechseln Sie anschließend zum [Internetforum zur Microsoft Authenticator-App](https://social.technet.microsoft.com/Forums/en-US/home?forum=MicrosoftAuthenticatorApp), und informieren Sie Microsoft über Ihr Problem und darüber, wie Sie bisher versucht haben, es zu lösen.

### <a name="switch-to-push-notifications"></a>Umstellen auf Pushbenachrichtigungen

**F:** Ich verwende die Überprüfungscodes in der App, aber wie wechsle ich zu Pushbenachrichtigungen?

**A:** Sie können Benachrichtigungen für Ihr Geschäfts-, Schul- oder Unikonto (falls dies von Ihrem Administrator gestattet ist) oder für Ihr privates Microsoft-Konto einrichten. Für Konten von Drittanbietern wie Google oder Facebook ist es nicht möglich, Benachrichtigungen zu aktivieren.<br>Wenn Sie in Ihrem privaten Konto Benachrichtigungen empfangen möchten, müssen Sie Ihr Gerät mit dem Konto neu registrieren. Wechseln Sie zu **Konto hinzufügen**, wählen Sie **Persönliches Microsoft-Konto** aus, und melden Sie sich dann mit Ihrem Benutzernamen und Kennwort an.<br>Ihre Organisation entscheidet, ob One-Click-Benachrichtigungen für Ihr Geschäfts-, Schul- oder Unikonto zugelassen sind.

### <a name="notifications-for-other-accounts"></a>Benachrichtigungen für andere Konten

**F:** Funktionieren Benachrichtigungen auch für Nicht-Microsoft-Konten?

**A:** Nein, Benachrichtigungen funktionieren nur bei Microsoft-Konten und Azure Active Directory-Konten. Wenn Ihre Organisation oder Bildungseinrichtung Azure AD-Konten verwendet, kann sie dieses Feature deaktivieren.

### <a name="backup-and-recovery"></a>Sicherung und Wiederherstellung

**F:** Ich habe ein neues Gerät oder habe mein Gerät auf der Grundlage einer Sicherung wiederhergestellt. Wie richte ich meine Konten in Authenticator wieder ein?

**A:** Wenn Sie die Option **Cloudsicherung** aktiviert haben, können Sie mithilfe der alten Sicherung Ihre Kontoanmeldeinformationen auf dem neuen iOS- oder Android-Gerät wiederherstellen. Weitere Informationen finden Sie im Artikel [Sichern und Wiederherstellen von Kontoanmeldeinformationen mithilfe der Microsoft Authenticator-App](user-help-auth-app-backup-recovery.md).

### <a name="lost-device"></a>Verlorenes Gerät

**F:** Ich habe mein Gerät verloren oder verwende nun ein neues Gerät. Wie stelle ich sicher, dass Benachrichtigungen nicht mehr an mein altes Gerät gesendet werden?

**A:** Wenn Sie Authenticator zu Ihrem neuen Gerät hinzufügen, wird die App nicht automatisch auf dem alten Gerät entfernt. Es reicht auch nicht aus, die App auf dem alten Gerät zu löschen. Sie müssen die App auf dem alten Gerät löschen UND Microsoft oder Ihrer Organisation mitteilen, dass die Registrierung des alten Geräts aufgehoben werden soll.<ul><li>**Entfernen der App auf einem Gerät mit einem persönlichen Microsoft-Konto:** Navigieren Sie auf der Seite [Kontosicherheit](https://account.microsoft.com/security)  zum Bereich für die zweistufige Überprüfung, und deaktivieren Sie die Überprüfung für Ihr altes Gerät.</li><li>**Entfernen der App auf einem Gerät mit einem Geschäfts-, Schul- oder Unikonto von Microsoft:** Navigieren Sie auf der Seite [MyApps](https://myapps.microsoft.com/) oder im benutzerdefinierten Portal Ihrer Organisation zum Bereich für die zweistufige Überprüfung, und deaktivieren Sie die Überprüfung für Ihr altes Gerät.</li></ul>

### <a name="remove-account-from-app"></a>Entfernen eines Kontos aus der App

**F:** Wie entferne ich ein Konto aus der App?

**A:** Tippen Sie auf die Kachel des Kontos, das Sie aus der App entfernen möchten, um die Vollbildansicht des Kontos zu laden. Tippen Sie auf **Konto entfernen**, um das Konto aus der App zu entfernen.<br>Wenn Sie ein Gerät besitzen, das bei Ihrer Organisation registriert ist, müssen Sie möglicherweise einen weiteren Schritt ausführen, um Ihr Konto zu entfernen. Auf diesen Geräten wird Authenticator automatisch als Geräteadministrator registriert. Wenn Sie die App vollständig deinstallieren möchten, müssen Sie die Registrierung der App zuerst in den App-Einstellungen aufheben.

### <a name="too-many-permissions"></a>Zu viele Berechtigungen

**F:** Warum fordert die App so viele Berechtigungen an?

**A:** Hier ist die vollständige Liste der Berechtigungen, die möglicherweise erforderlich sind, sowie Hinweise zu deren Verwendung in der App. Die jeweils angezeigten Berechtigungen hängen vom Typ Ihres Telefons ab.<ul><li>**Speicherort:** Manchmal möchte Ihre Organisation Ihren Standort kennen, bevor sie Ihnen Zugriff auf bestimmte Ressourcen gewährt. Diese Berechtigung wird von der App nur angefordert, wenn Ihre Organisation über eine Richtlinie verfügt, die einen Standort erfordert.</li><li>**Verwenden biometrischer Hardware:** Bei einigen Geschäfts-, Schul- und Unikonten wird eine zusätzliche PIN benötigt, wenn Sie Ihre Identität bestätigen. Die App benötigt Ihre Zustimmung zur Verwendung von biometrischer Erkennung oder Gesichtserkennung anstelle der Eingabe einer PIN.</li><li>**Kamera.** Wir verwenden Ihre Kamera, um QR-Codes zu scannen, wenn Sie ein Geschäfts-, Schul- oder Unikonto oder ein nicht von Microsoft stammendes Konto hinzufügen.</li><li>**Kontakte und Telefon.** Die App benötigt diese Berechtigung, um auf Ihrem Telefon nach Geschäfts-, Schul- oder Unikonten von Microsoft suchen und diese für Sie der App hinzufügen zu können.</li><li>**SMS.** So wird sichergestellt, dass Ihre Telefonnummer mit der bei uns registrierten Nummer übereinstimmt, wenn Sie sich zum ersten Mal mit Ihrem privaten Konto anmelden. Wir senden Ihnen eine SMS mit einem sechs- bis achtstelligen Prüfcode an Ihr Telefon, auf dem Sie die App installiert haben. Sie müssen den Code nicht aufrufen und eingeben, denn Authenticator erkennt ihn automatisch in der SMS.</li><li>**Andere Apps in den Hintergrund rücken.** Die Benachrichtigung an Sie, mit der Ihre Identität überprüft wird, wird auch in allen anderen ausgeführten Apps angezeigt.</li><li>**Daten aus dem Internet empfangen.** Diese Berechtigung ist für das Senden von Benachrichtigungen erforderlich.</li><li>**Standby-Modus verhindern.** Wenn Sie Ihr Gerät bei Ihrer Organisation registrieren, kann Ihre Organisation diese Richtlinie auf Ihrem Telefon ändern.</li><li>**Vibrationsfunktion steuern.** Sie können auswählen, ob Ihr Telefon vibrieren soll, wenn Sie eine Benachrichtigung erhalten, dass Sie Ihre Identität bestätigen müssen.</li><li>**Verwendung der Fingerprint-Hardware.** Bei einigen Geschäfts-, Schul- und Unikonten wird eine zusätzliche PIN benötigt, wenn Sie Ihre Identität bestätigen. Sie können Ihren Fingerabdruck verwenden, um den Prozess zu vereinfachen, anstatt die PIN einzugeben.</li><li> **Anzeigen von Netzwerkverbindungen.** Wenn Sie ein Microsoft-Konto hinzufügen, benötigt die App eine Netzwerk-/Internetverbindung.</li><li>**Lesen Ihres Speichers**. Diese Berechtigung ist nur notwendig, wenn Sie ein technisches Problem über die App-Einstellungen melden. Einige Informationen aus Ihrem Speicherkonto werden gesammelt, um das Problem zu diagnostizieren.</li><li>**Vollständiger Netzwerkzugriff.** Diese Berechtigung ist erforderlich, um Benachrichtigungen senden zu können, mit denen Sie Ihre Identität bestätigen.</li><li>**Bei Gerätestart automatisch starten.** Wenn Sie Ihr Telefon neu starten, stellt diese Berechtigung sicher, dass Sie weiterhin Benachrichtigungen erhalten, um Ihre Identität zu bestätigen.</li></ul>

### <a name="approve-requests-without-unlocking"></a>Genehmigen von Anforderungen ohne Entsperren

**F:** Warum ist es möglich, mit Authenticator eine Anforderung zu genehmigen, ohne das Gerät zu entsperren?

**A:** Sie müssen Ihr Gerät nicht entsperren, um Überprüfungsanforderungen zu genehmigen. Das Einzige, was Sie nachweisen können müssen, ist, dass Sie jederzeit über Ihr Mobiltelefon erreichbar sind. Bei der zweistufigen Überprüfung werden zwei Dinge abgefragt: etwas, das Sie wissen (das Kennwort), und etwas, das Sie haben: Ihr Telefon (als Nachweis für die mehrstufige Authentifizierung registriert und mit eingerichtetem Authenticator). Somit sind die Kriterien des zweiten Authentifizierungsschritts erfüllt, wenn Sie über das Smartphone verfügen und die Anforderung bestätigen.

### <a name="activity-notifications"></a>Benachrichtigungen zu Aktivitäten

**F:** Warum erhalte ich Benachrichtigungen über meine Kontoaktivität?

**A:** Wann immer Änderungen an Ihren privaten Microsoft-Konten vorgenommen werden, werden als zusätzliche Sicherheitsmaßnahme umgehend Aktivitätsbenachrichtigungen an Authenticator gesendet. Früher haben Sie diese Benachrichtigungen nur per E-Mail und SMS empfangen. Weitere Informationen zu diesen Aktivitätsbenachrichtigungen finden Sie unter [Was passiert bei einer ungewöhnlichen Anmeldung bei Ihrem Konto?](https://support.microsoft.com/help/13967/microsoft-account-unusual-sign-in). Wenn Sie Ihre Benachrichtigungen auf andere Weise erhalten möchten, melden Sie sich bei der Seite [Wie können wir Ihnen nicht kritische Kontobenachrichtigungen zukommen lassen?](https://account.live.com/SecurityNotifications/Update) Ihres Kontos an.

### <a name="one-time-passcodes"></a>Einmalkennungen

**F:** Meine Einmalkennungen funktionieren nicht.   Wie sollte ich vorgehen?

**A:** Stellen Sie sicher, dass die Datums- und Uhrzeitangaben auf Ihrem Gerät richtig sind und automatisch synchronisiert werden. Wenn Datum und Uhrzeit falsch oder nicht synchronisiert sind, funktioniert der Code nicht.

### <a name="windows-10-mobile"></a>Windows 10 Mobile

**F:** Das Betriebssystem Windows 10 Mobile wurde im Dezember 2019 eingestellt. Wird auch die Microsoft Authenticator-App unter Windows Mobile-Betriebssystemen eingestellt?

**A:** Die Unterstützung für Authenticator wird nach dem 28. Februar 2020 für alle Windows Mobile-Betriebssysteme eingestellt. Nach dem zuvor genannten Datum sind die Benutzer nicht mehr berechtigt, neue Updates für die App zu erhalten. Nach dem 28. Februar 2020 stellen Microsoft-Dienste, die derzeit Authentifizierungen mithilfe der Microsoft Authenticator-App unter allen Windows Mobile-Betriebssystemen unterstützen, ihre Unterstützung ein. Wir empfehlen unseren Benutzern dringend, vor diesem Datum eine Umstellung auf einen alternativen Authentifizierungsmechanismus vorzunehmen, damit sie sich bei Microsoft-Diensten authentifizieren können.

### <a name="default-mail-app"></a>Standard-E-Mail-App

**F:** Wenn ich mich mit der Standard-Mail-App von iOS bei meinem Geschäfts-, Schul-oder Unikonto anmelde, erhalte ich von Authenticator eine Aufforderung, meine Daten für die Sicherheitsüberprüfung einzugeben. Wenn ich diese Informationen eingegeben habe und zur E-Mail-App zurückkehre, erhalte ich eine Fehlermeldung. Was kann ich tun?

**A:** Dies liegt sehr wahrscheinlich daran, dass Sie für Ihre Anmeldung und Ihre E-Mail-App zwei verschiedene Apps nutzen, sodass der anfängliche Anmeldevorgang im Hintergrund nicht mehr funktioniert und fehlschlägt. Um dieses Problem zu beheben, empfehlen wir, das Symbol **Safari** unten rechts auf dem Bildschirm auszuwählen, wenn Sie sich bei Ihrer E-Mail-App anmelden. Durch den Wechsel zu Safari erfolgt der gesamte Anmeldevorgang in einer einzigen App, sodass Sie sich erfolgreich bei der App anmelden können.

### <a name="apple-watch-watchos-7"></a>Apple Watch mit watchOS 7

**F:** Warum treten Probleme bei meiner Apple Watch unter watchOS 7 auf?

**A:** Es liegt ein Problem beim Genehmigen von Benachrichtigungen unter watchOS 7 vor, und wir arbeiten mit Apple zusammen, um dies zu beheben. In der Zwischenzeit sollten alle Benachrichtigungen, die die Microsoft Authenticator-App unter watchOS erfordern, stattdessen auf Ihrem Smartphone genehmigt werden.

### <a name="apple-watch-doesnt-show-accounts"></a>Keine Konten auf Apple Watch

**F:** Warum werden nicht alle meine Konten angezeigt, wenn ich Authenticator auf meiner Apple Watch öffne?

**A:** Authenticator unterstützt nur die Verwendung von privaten bzw. Geschäfts-, Schul- oder Unikonten von Microsoft mit Pushbenachrichtigungen auf der Apple Watch-Begleit-App. Für Ihre anderen Konten, z. B. von Google oder Facebook, müssen Sie die Authenticator-App auf Ihrem Telefon öffnen, um Ihre Prüfcodes anzuzeigen.

### <a name="apple-watch-notifications"></a>Apple Watch-Benachrichtigungen

**F:** Warum kann ich Benachrichtigungen auf meiner Apple Watch nicht genehmigen oder verweigern?

**A:** Stellen Sie zunächst sicher, dass Sie Authenticator auf Ihrem iPhone auf Version 6.0.0 oder höher aktualisiert haben. Danach öffnen Sie die Microsoft Authenticator-Begleit-App auf Ihrer Apple Watch und suchen nach Konten, unter denen sich eine **Set Up**-Schaltfläche befindet. Schließen Sie den Einrichtungsprozess ab, um Benachrichtigungen für diese Konten zu genehmigen.

### <a name="apple-watch-communication-error"></a>Apple Watch-Kommunikationsfehler

**F:** Ich erhalte einen Kommunikationsfehler zwischen Apple Watch und meinem Telefon. Wie kann ich dieses Problem beheben?

**A:** Dieser Fehler tritt auf, wenn der Bildschirm Ihrer Uhr in den Standbymodus wechselt, bevor die Kommunikation mit Ihrem Smartphone beendet ist.<br><b>Wenn der Fehler während des Setups passiert:</b><br>Versuchen Sie, Setup erneut auszuführen, und stellen Sie dabei sicher, dass Sie Ihre Apple Watch aktiviert halten, bis der Prozess abgeschlossen ist. Öffnen Sie gleichzeitig die App auf Ihrem Telefon, und reagieren Sie auf alle angezeigten Aufforderungen.<br>Wenn Ihr Telefon und die Apple Watch immer noch nicht kommunizieren, können Sie Folgendes versuchen:<ol><li>Erzwingen Sie das Beenden der Microsoft Authenticator-Telefon-App, und öffnen Sie sie erneut auf Ihrem iPhone.</li><li>Erzwingen Sie das Beenden der Begleit-App auf Ihrer Apple Watch.<ol><li> Öffnen Sie die Microsoft Authenticator-Begleit-App auf Ihrer Apple Watch.</li><li>Halten Sie die Seitentaste gedrückt, bis der Bildschirm **Shutdown (Herunterfahren)** angezeigt wird.</li><li>Lassen Sie die Seitentaste los, und halten Sie die Digital Crown gedrückt, um das Beenden der aktiven App zu erzwingen.</li></ol></li><li>Deaktivieren Sie Bluetooth und WLAN sowohl für Ihr Telefon als auch für die Watch, und aktivieren Sie sie dann erneut.</li><li>Starten Sie Ihr iPhone und Ihre Apple Watch neu.</li></ol><b>Wenn der Fehler auftritt, während Sie versuchen, eine Benachrichtigung zu akzeptieren:</b><br>Halten Sie beim nächsten Versuch, eine Mitteilung auf Ihrer Apple Watch zu akzeptieren, den Bildschirm aktiviert, bis die Anforderung abgeschlossen ist und Sie den Sound hören, der anzeigt, dass der Vorgang erfolgreich abgeschlossen wurde.

### <a name="apple-watch-companion-app-not-syncing"></a>Keine Synchronisierung der Apple Watch-Begleit-App

**F:** Warum wird die Microsoft Authenticator-Begleit-App für Apple Watch auf meiner Watch nicht synchronisiert oder angezeigt?

**A:** Wenn die App auf Ihrer Apple Watch nicht angezeigt wird, versuchen Sie Folgendes: <ol><li>Stellen Sie sicher, dass auf Ihrer Apple Watch watchOS 4.0 oder höher ausgeführt wird.</li><li>Synchronisieren Sie Ihre Apple Watch erneut.</li></ol>

### <a name="apple-watch-companion-app-crashed"></a>Absturz der Apple Watch-Begleit-App

**F:** Meine Apple Watch-Begleit-App ist abgestürzt. Kann ich Ihnen meine Absturzprotokolle zur Untersuchung senden?

**A:** Sie müssen zuerst sicherstellen, dass Sie sich entschieden haben, Ihre Analysen für uns freizugeben. Als TestFlight-Benutzer sind Sie bereits registriert. Andernfalls wechseln Sie zu **Einstellungen > Datenschutz > Analyse**, und wählen Sie sowohl die Option **iPhone- und Watch-Analyse freigeben** als auch **Für App-Entwickler freigeben** aus.<br>Nachdem Sie sich angemeldet haben, können Sie versuchen, den Absturz zu reproduzieren, damit Ihre Absturzprotokolle automatisch zur Untersuchung an uns gesendet werden. Wenn Sie den Absturz jedoch nicht reproduzieren können, können Sie die Protokolldateien manuell kopieren und an uns senden.<ol><li>Öffnen die Watch-App auf Ihrem Telefon, wechseln Sie zu **Einstellungen > Allgemein**, und klicken Sie dann auf **Watch-Analyse kopieren**.</li><li>Suchen Sie den entsprechenden Absturz unter **Einstellungen > Datenschutz > Analyse > Analysedaten**, und kopieren Sie dann den gesamten Text manuell.</li><li>Öffnen Sie Authenticator auf Ihrem Telefon, und fügen Sie den kopierten Text in das Feld  **Beschreiben Sie das Problem** unter **Sie haben Probleme?** auf der Seite  **Feedback senden** ein. </li></ol>

## <a name="autofill-with-authenticator"></a>AutoAusfüllen bei Authenticator

**F:** Was ist die AutoAusfüllen-Funktion bei Authenticator?

**A:** Die Authenticator-App speichert Kennwörter für Apps und Websites, die Sie auf Ihrem Smartphone besuchen, nun sicher und füllt sie automatisch aus. Sie können AutoAusfüllen verwenden, um Ihre Kennwörter auf Ihren iOS- und Android-Geräten zu synchronisieren und automatisch auszufüllen. Nachdem Sie die Authenticator-App auf Ihrem Smartphone als Anbieter für AutoAusfüllen eingerichtet haben, bietet sie die Speicherung Ihrer Kennwörter an, wenn Sie diese auf der Anmeldeseite einer Website oder App eingeben. Die Kennwörter werden in [Ihrem persönlichen Microsoft-Konto](https://account.microsoft.com/account) gespeichert und sind auch verfügbar, wenn Sie sich mit Ihrem persönlichen Microsoft-Konto bei Microsoft Edge anmelden.

**F:** Welche Informationen kann die Funktion AutoAusfüllen von Authenticator für mich ausfüllen?

**A:** Authenticator kann automatisch Benutzernamen und Kennwörter auf Websites und in Apps ausfüllen, die Sie auf Ihrem Smartphone bereits besucht haben.

**F:** Wie deaktiviere ich das automatische Ausfüllen von Kennwörtern in Authenticator auf meinem Smartphone?

**A:** Folgen Sie diesen Schritten:

1. Öffnen Sie die Authenticator-App.
1. Wählen Sie in Authenticator auf der Registerkarte **Kennwörter** die Option **Bei Microsoft anmelden** aus, und melden Sie sich mit [Ihrem Microsoft-Konto](https://account.microsoft.com/account) an. Derzeit unterstützt diese Funktion nur Microsoft-Konten und auch noch keine Geschäfts-, Schul- oder Unikonten.

**F:** Wie lege ich Authenticator als Standardanbieter für AutoAusfüllen auf meinem Smartphone fest?

**A:** Folgen Sie diesen Schritten:

1. Öffnen Sie die Authenticator-App.
1. Wählen Sie in der App auf der Registerkarte **Kennwörter** die Option **Bei Microsoft anmelden** aus, und melden Sie sich mit [Ihrem Microsoft-Konto](https://account.microsoft.com/account) an.
1. Führen Sie eines der folgenden Verfahren aus:

   - Wählen Sie bei iOS unter **Einstellungen** im Abschnitt „AutoAusfüllen-Einstellungen“ die Option **Aktivieren von AutoAusfüllen** aus, um zu erfahren, wie Sie Authenticator als Standardanbieter für AutoAusfüllen festlegen.
   - Wählen Sie bei Android unter **Einstellungen** im Abschnitt „AutoAusfüllen-Einstellungen“ die Option **Als AutoAusfüllen-Anbieter festlegen** aus.

**F:** Wie gehe ich vor, wenn **AutoAusfüllen** nicht in den Einstellungen verfügbar ist?

**A:** Wenn AutoAusfüllen in Authenticator nicht verfügbar ist, ist die Funktion möglicherweise für Ihre Organisation oder Ihren Kontotyp nicht zulässig. Sie können diese Funktion auf einem Gerät verwenden, auf dem Ihr Geschäfts-, Schul- oder Unikonto nicht hinzugefügt wurde. Weitere Informationen zum Zulassen von AutoAusfüllen für Ihre Organisation finden Sie unter [AutoAusfüllen für IT-Administratoren](#autofill-for-it-admins).

**F:** Wie beende ich die Synchronisierung von Kennwörtern?

**A:** Um die Synchronisierung von Kennwörtern in der Authenticator-App zu deaktivieren, öffnen Sie **Einstellungen** > **AutoAusfüllen-Einstellungen** > **Konto synchronisieren**. Auf dem nächsten Bildschirm können Sie **Synchronisierung beenden und AutoAusfüllen-Daten entfernen** auswählen. Damit werden die Kennwörter und alle anderen AutoAusfüllen-Daten vom Gerät entfernt. Das Entfernen der AutoAusfüllen-Daten hat keine Auswirkungen auf die mehrstufige Authentifizierung.

**F:** Wie werden meine Kennwörter durch die Authenticator-App geschützt?

**A:** Die Authenticator-App bietet bereits einen hohen Grad an Sicherheit für die mehrstufige Authentifizierung und die Kontoverwaltung, und dieselben hohen Sicherheitsstandards werden auch auf die Verwaltung Ihrer Kennwörter angewandt.

- **Die Authenticator-App erfordert eine strenge Authentifizierung:** Für die Anmeldung bei Authenticator ist ein zweiter Faktor erforderlich. Dies bedeutet, dass Ihre Kennwörter in der Authenticator-App auch dann geschützt sind, wenn jemand das Kennwort Ihres Microsoft-Kontos kennt.
- **AutoAusfüllen-Daten werden durch Biometriedaten und einen Passcode geschützt:** Bevor Sie das Kennwort für eine App oder Website automatisch ausfüllen können, erfordert Authenticator eine biometrische Kennung oder einen Gerätepasscode. Dies trägt dazu bei, dass selbst dann, wenn eine andere Person Zugriff auf Ihr Gerät hat, Ihr Kennwort nicht ausgefüllt oder angezeigt werden kann, da die biometrischen Daten oder die Geräte-PIN fehlen. Außerdem können Benutzer die Seite „Kennwörter“ nur öffnen, wenn sie die biometrischen Daten oder die PIN bereitstellen. Dies gilt selbst dann, wenn sie die App-Sperre in den App-Einstellungen deaktivieren.
- **Verschlüsselte Kennwörter auf dem Gerät:** Die Kennwörter werden auf dem Gerät verschlüsselt, und die Ver-/Entschlüsselungsschlüssel werden niemals gespeichert, sondern immer dynamisch generiert. Kennwörter werden nur entschlüsselt, wenn der Benutzer dies möchte, also während des automatischen Ausfüllens oder wenn der Benutzer das Kennwort anzeigen möchte. Für beide Aktionen sind jedoch die biometrischen Daten oder die PIN erforderlich.
- **Cloud- und Netzwerksicherheit:** Ihre Kennwörter in der Cloud werden nur ver- und entschlüsselt, wenn sie auf Ihrem Gerät eintreffen. Die Kennwörter werden über eine per SSL geschützte HTTPS-Verbindung synchronisiert, sodass vertrauliche Daten bei der Synchronisierung nicht von Angreifern abgehört werden können. Außerdem wird die Integrität der über das Netzwerk synchronisierten Daten mithilfe kryptografischer Hashfunktionen (insbesondere durch hashbasierte Nachrichtenauthentifizierungscodes) überprüft.

## <a name="autofill-for-it-admins"></a>AutoAusfüllen für IT-Administratoren

**F:** Können meine Mitarbeiter oder Schüler/Studierende AutoAusfüllen für Kennwörter in der Authenticator-App ebenfalls nutzen?

**A:** Ja, AutoAusfüllen funktioniert jetzt selbst dann für die meisten Unternehmensbenutzer, wenn ein Geschäfts-, Schul- oder Unikonto zur Authenticator-App hinzugefügt wird. Sie können ein Formular ausfüllen, um AutoAusfüllen für Ihre Organisation zu konfigurieren (zulassen oder verweigern), und das Formular [an das Authenticator-Team senden](https://aka.ms/ConfigureAutofillInAuthenticator).

**F:** Werden die Kennwörter für Geschäfts-, Schul- oder Unikonten meiner Benutzer automatisch synchronisiert?

**A:** Nein. Für das automatische Auffüllen von Kennwörtern werden keine Kennwörter der Geschäfts-, Schul- oder Unikonten von Benutzern synchronisiert. Wenn Benutzer eine Website oder eine App besuchen, bietet Authenticator die Speicherung des Kennworts für diese Website oder App an. Das Kennwort wird nur gespeichert, wenn der Benutzer sich dazu entscheidet.
  
**F:** Kann ich nur bestimmte Benutzer meiner Organisation in die Positivliste für AutoAusfüllen aufnehmen?

**A:** Nein. Unternehmen können derzeit AutoAusfüllen für Kennwörter nur für alle oder keine Ihrer Mitarbeiter aktivieren.

**F:** Was geschieht, wenn meine Mitarbeiter oder Schüler/Studierenden mehrere Geschäfts-, Schul- oder Unikonten haben? Einer meiner Mitarbeiter hat z. B. Konten mehrerer Unternehmen oder Bildungseinrichtungen in Microsoft Authenticator.

**A:** Alle Unternehmen oder Bildungseinrichtungen, die in der Authenticator-App hinzugefügt werden, müssen auch in die Positivliste für das automatische Ausfüllen von Kennwörtern in Authenticator aufgenommen werden, damit der App-Besitzer sie verwenden kann. Eine Ausnahme von dieser Einschränkung bildet der Fall, dass Mitarbeiter oder Schüler/Studierende ihre Geschäfts-, Schul- oder Unikonten in der cloudbasierten Microsoft Multi-Factor Authentication als [externe Konten oder Konten eines Drittanbieters](user-help-auth-app-add-non-ms-account.md) hinzufügen.

## <a name="next-steps"></a>Nächste Schritte

- Wenn Sie Probleme beim Abrufen des Prüfcodes für Ihr persönliches Microsoft-Konto haben, lesen Sie im Artikel **Sicherheitsinformation und Prüfcodes zum Microsoft-Konto** den Abschnitt [Problembehandlung beim Prüfcode](https://support.microsoft.com/help/12428/microsoft-account-security-info-verification-codes).

- Weitere Informationen zur zweistufigen Überprüfung finden Sie unter [Einrichten meines Kontos für die Überprüfung in zwei Schritten](multi-factor-authentication-end-user-first-time.md).

- Weitere Informationen zu Sicherheitsinformationen finden Sie unter [Übersicht über die Sicherheitsinformationen (Vorschau)](./security-info-setup-signin.md).

- Falls Ihre Frage hier nicht beantwortet wurde, lassen Sie es uns wissen. Posten Sie Ihre Frage im [Forum der Microsoft Authenticator-App](https://social.technet.microsoft.com/Forums/en-us/home?forum=MicrosoftAuthenticatorApp), um Hilfe aus der Community zu erhalten, oder hinterlassen Sie einen Kommentar auf dieser Seite.
