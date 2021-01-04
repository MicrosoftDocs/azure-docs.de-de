---
title: Häufig gestellte Fragen zu Azure AD Multi-Factor Authentication – Azure Active Directory
description: Antworten auf häufig gestellte Fragen im Zusammenhang mit Azure AD Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1dfe99a2ed57ecc953c69d1bad89e2b2c8816ca3
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741447"
---
# <a name="frequently-asked-questions-about-azure-ad-multi-factor-authentication"></a>Häufig gestellte Fragen zu Azure AD Multi-Factor Authentication

Dieser Artikel enthält Antworten auf häufig gestellte Fragen zu Azure AD Multi-Factor Authentication und zur Nutzung des Multi-Factor Authentication-Diensts. Er ist unterteilt in allgemeine Fragen zum Dienst sowie in Fragen zu Abrechnungsmodellen, zur Benutzeroberfläche und zur Problembehandlung.

> [!IMPORTANT]
> Ab dem 1. Juli 2019 bietet Microsoft keine MFA-Server mehr für neue Bereitstellungen an. Neue Kunden, die eine Multi-Factor Authentication für ihre Benutzer einrichten möchten, können stattdessen cloudbasierte Multi-Factor Authentication von Azure AD verwenden. Bestehende Kunden, die ihren MFA-Server vor dem 1. Juli aktiviert haben, können weiterhin die neusten Versionen und zukünftige Updates herunterladen sowie Anmeldedaten zur Aktivierung generieren.
>
> Die unten in Bezug auf den Azure Multi-Factor Authentication-Server angegebenen Informationen gelten nur für Benutzer, die bereits den MFA-Server ausführen.
>
> Die nutzungsbasierte Lizenzierung steht neuen Kunden seit dem 1. September 2018 nicht mehr zur Verfügung.
> Seit dem 1. September 2018 können keine neuen Authentifizierungsanbieter mehr erstellt werden. Vorhandene Authentifizierungsanbieter können ggf. weiterhin verwendet und aktualisiert werden. Multi-Factor Authentication ist in Azure AD Premium-Lizenzen weiterhin ein verfügbares Feature.

## <a name="general"></a>Allgemein

* [Wie werden Benutzerdaten vom Azure Multi-Factor Authentication-Server behandelt?](#how-does-azure-multi-factor-authentication-server-handle-user-data)
* [Welche SMS-Kurzcodes werden für das Senden von SMS-Nachrichten an meine Benutzer verwendet?](#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

### <a name="how-does-azure-multi-factor-authentication-server-handle-user-data"></a>Wie werden Benutzerdaten vom Azure Multi-Factor Authentication-Server behandelt?

Beim Multi-Factor Authentication-Server werden die Daten eines Benutzers nur auf den lokalen Servern gespeichert. Daten werden nicht dauerhaft in der Cloud gespeichert. Wenn der Benutzer die zweistufige Überprüfung ausführt, sendet der Multi-Factor Authentication-Server für die Authentifizierung Daten an den Azure AD Multi-Factor Authentication-Clouddienst. Zur Kommunikation zwischen Multi-Factor Authentication-Server und dem Multi-Factor Authentication-Clouddienst wird Secure Sockets Layer (SSL) oder Transport Layer Security (TLS) über den ausgehenden Port 443 verwendet.

Wenn Authentifizierungsanforderungen an den Clouddienst gesendet werden, werden Daten für Authentifizierungs- und Verwendungsberichte gesammelt. Folgende Datenfelder werden in die Protokolle der zweistufigen Überprüfung einbezogen:

* **Eindeutige ID** (entweder Benutzername oder lokale Server-ID der Multi-Factor Authentication)
* **Vor- und Nachname** (optional)
* **E-Mail-Adresse** (optional)
* **Rufnummer** (für eine Anruf- oder SMS-Authentifizierung)
* **Gerätetoken** (für die Authentifizierung einer mobilen App)
* **Authentifizierungsmodus**
* **Authentifizierungsergebnis**
* **Name des Multi-Factor Authentication-Servers**
* **IP des Multi-Factor Authentication-Servers**
* **Client-IP** (falls verfügbar)

Die optionalen Felder können im Multi-Factor Authentication-Server konfiguriert werden.

Das Authentifizierungsergebnis (Erfolg oder Ablehnung) und der Grund für etwaige Ablehnungen werden mit den Authentifizierungsdaten gespeichert. Diese Daten stehen in den Authentifizierungs- und Verwendungsberichten zur Verfügung.

Weitere Informationen finden Sie unter [Datenresidenz und Kundendaten für Azure AD Multi-Factor Authentication](concept-mfa-data-residency.md).

### <a name="what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users"></a>Welche SMS-Kurzcodes werden für das Senden von SMS-Nachrichten an meine Benutzer verwendet?

In den USA verwenden wir die folgenden SMS-Kurzcodes:

* *97671*
* *69829*
* *51789*
* *99399*

In Kanada verwenden wir die folgenden SMS-Kurzcodes:

* *759731*
* *673801*

Die Zustellung einheitlicher SMS- oder sprachbasierter Aufforderungen zur Multi-Factor Authentication an dieselbe Nummer kann nicht garantiert werden. Im Interesse unserer Benutzer können wir jederzeit Kurzcodes hinzuzufügen oder entfernen, wenn wir Routenanpassungen zur Verbesserung der Zustellbarkeit von SMS vornehmen.

Wir unterstützt keine Kurzcodes für Länder/Regionen außerhalb der USA und Kanadas.

## <a name="billing"></a>Abrechnung

Die meisten Fragen zur Abrechnung werden auf der Seite [Multi-Factor Authentication – Preise](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) und in der Dokumentation [Azure AD Multi-Factor Authentication-Versionen und -Verbrauchstarife](concept-mfa-licensing.md) beantwortet.

* [Werden meiner Organisation die Kosten der für die Authentifizierung verwendeten Telefonanrufe und SMS in Rechnung gestellt?](#is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication)
* [Werden bei Verwendung des benutzerbasierten Abrechnungsmodells alle aktivierten Benutzer abgerechnet oder nur die Benutzer, die eine zweistufige Überprüfung durchgeführt haben?](#does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification)
* [Wie funktioniert die Abrechnung für Multi-Factor Authentication?](#how-does-multi-factor-authentication-billing-work)
* [Gibt es eine kostenlose Version von Azure AD Multi-Factor Authentication?](#is-there-a-free-version-of-azure-ad-multi-factor-authentication)
* [Kann meine Organisation jederzeit zwischen den Verbrauchsmodellen „Pro Benutzer“ und „Pro Authentifizierung“ wechseln?](#can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time)
* [Kann meine Organisation jederzeit zwischen der nutzungsbasierten Abrechnung und Abonnements (lizenzbasiertes Modell) wechseln?](#can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time)
* [Muss meine Organisation Identitäten verwenden und synchronisieren, um Azure AD Multi-Factor Authentication nutzen zu können?](#does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-ad-multi-factor-authentication)

### <a name="is-my-organization-charged-for-sending-the-phone-calls-and-text-messages-that-are-used-for-authentication"></a>Werden meiner Organisation die Kosten der für die Authentifizierung verwendeten Telefonanrufe und SMS in Rechnung gestellt?

Nein. Durch individuelle Telefonanrufe oder SMS, die über Azure AD Multi-Factor Authentication an Benutzer gesendet werden, entstehen für Sie keine Kosten. Bei Verwendung eines authentifizierungsbasierten MFA-Anbieters werden die Kosten für die einzelnen Authentifizierungen, aber nicht für die verwendete Methode in Rechnung gestellt.

Bei Ihren Benutzern können Kosten für die eingehenden Telefonanrufe oder SMS anfallen. Das hängt allerdings vom jeweiligen Netzbetreiber ab.

### <a name="does-the-per-user-billing-model-charge-me-for-all-enabled-users-or-just-the-ones-that-performed-two-step-verification"></a>Werden bei Verwendung des benutzerbasierten Abrechnungsmodells alle aktivierten Benutzer abgerechnet oder nur die Benutzer, die eine zweistufige Überprüfung durchgeführt haben?

Die Abrechnung basiert auf der Anzahl von Benutzern, die zur Nutzung von Multi-Factor Authentication konfiguriert sind – unabhängig davon, ob sie im Abrechnungsmonat eine zweistufige Überprüfung initiiert haben.

### <a name="how-does-multi-factor-authentication-billing-work"></a>Wie funktioniert die Abrechnung für Multi-Factor Authentication?

Wenn Sie einen benutzer- oder authentifizierungsbasierten MFA-Anbieter erstellen, wird das Azure-Abonnement Ihrer Organisation monatlich auf der Grundlage der Nutzung abgerechnet. Dieses Abrechnungsmodell ähnelt der Azure-Abrechnung für die Nutzung von virtuellen Computern und Web-Apps.

Wenn Sie ein Abonnement für Azure AD Multi-Factor Authentication erwerben, fällt für Ihre Organisation lediglich die jährliche Lizenzgebühr für die einzelnen Benutzer an. Wenn Sie MFA-Lizenzen und Microsoft 365, Azure AD Premium oder Enterprise Mobility + Security besitzen, werden die Pakete so abgerechnet.

Weitere Informationen finden Sie unter [Beziehen von Azure AD Multi-Factor Authentication](concept-mfa-licensing.md).

### <a name="is-there-a-free-version-of-azure-ad-multi-factor-authentication"></a>Gibt es eine kostenlose Version von Azure AD Multi-Factor Authentication?

Das Feature „Sicherheitsstandards“ kann im Tarif „Azure AD Free“ aktiviert werden. Bei Verwenden von „Sicherheitsstandards“ sind alle Benutzer für die mehrstufige Authentifizierung mit der App Microsoft Authenticator aktiviert. Es gibt keine Möglichkeit, bei Verwenden von „Sicherheitsstandards“ die Überprüfung per SMS oder Telefon zu verwenden. Die App Microsoft Authenticator muss genutzt werden.

Weitere Informationen finden Sie unter [Was sind Sicherheitsstandards?](../fundamentals/concept-fundamentals-security-defaults.md).

### <a name="can-my-organization-switch-between-per-user-and-per-authentication-consumption-billing-models-at-any-time"></a>Kann meine Organisation jederzeit zwischen den Verbrauchsmodellen „Pro Benutzer“ und „Pro Authentifizierung“ wechseln?

Wenn Ihre Organisation MFA als eigenständigen Dienst mit nutzungsbasierter Abrechnung erwirbt, wählen Sie bei der Erstellung eines MFA-Anbieters ein Abrechnungsmodell aus. Das Abrechnungsmodell kann nach der Erstellung eines MFA-Anbieters nicht mehr geändert werden. 

Falls Ihr MFA-Anbieter *nicht* mit einem Azure AD-Mandanten verknüpft ist oder Sie den neuen MFA-Anbieter mit einem anderen Azure AD-Mandanten verknüpfen, werden Benutzereinstellungen und Konfigurationsoptionen nicht übernommen. Darüber hinaus müssen vorhandene Azure MFA-Server unter Verwendung von Aktivierungsanmeldeinformationen des neuen MFA-Anbieters erneut aktiviert werden. Wenn Sie die MFA-Server erneut aktivieren, um sie mit dem neuen MFA-Anbieter zu verknüpfen, hat das keinerlei Auswirkungen auf die Authentifizierung per Telefonanruf oder SMS, aber Benachrichtigungen der mobilen App funktionieren erst wieder, wenn die Benutzer die mobile App erneut aktivieren.

Weitere Informationen zu MFA-Anbietern finden Sie unter [Erste Schritte mit Azure Multi-Factor Authentication-Anbietern](concept-mfa-authprovider.md).

### <a name="can-my-organization-switch-between-consumption-based-billing-and-subscriptions-a-license-based-model-at-any-time"></a>Kann meine Organisation jederzeit zwischen der nutzungsbasierten Abrechnung und Abonnements (lizenzbasiertes Modell) wechseln?

In einigen Fällen schon.

Wenn Ihr Verzeichnis über einen *benutzerbasierten* Azure Multi-Factor Authentication-Anbieter verfügt, können Sie MFA-Lizenzen hinzufügen. Benutzer mit Lizenzen fließen nicht in die benutzer-/nutzungsbasierte Abrechnung mit ein. Für Benutzer ohne Lizenz kann MFA weiterhin über den MFA-Anbieter aktiviert werden. Wenn Sie für alle Ihre Benutzer, die für die Verwendung von Multi-Factor Authentication konfiguriert sind, Lizenzen erwerben und zuweisen, können Sie den Azure Multi-Factor Authentication-Anbieter löschen. Sollten später einmal mehr Benutzer als Lizenzen vorhanden sein, können Sie jederzeit einen weiteren benutzerbasierten MFA-Anbieter erstellen.

Falls Ihr Verzeichnis über einen *authentifizierungsbezogenen* Azure Multi-Factor Authentication-Anbieter verfügt, werden Ihnen immer die einzelnen Authentifizierungen in Rechnung gestellt, solange der MFA-Anbieter mit Ihrem Abonnement verknüpft ist. Sie können Benutzern zwar MFA-Lizenzen zuweisen, Ihnen wird jedoch weiterhin jede Anforderung einer zweistufigen Überprüfung in Rechnung gestellt. Dabei spielt es keine Rolle, ob dem anfordernden Benutzer eine MFA-Lizenz zugewiesen ist.

### <a name="does-my-organization-have-to-use-and-synchronize-identities-to-use-azure-ad-multi-factor-authentication"></a>Muss meine Organisation Identitäten verwenden und synchronisieren, um Azure AD Multi-Factor Authentication nutzen zu können?

Wenn Ihre Organisation ein nutzungsbasiertes Abrechnungsmodell verwendet, ist Azure Active Directory optional, aber nicht erforderlich. Wenn Ihr MFA-Anbieter nicht mit einem Azure AD-Mandanten verknüpft ist, können Sie den Azure Multi-Factor Authentication-Server nur lokal bereitstellen.

Azure Active Directory ist für das Lizenzmodell erforderlich, da Lizenzen dem Azure AD-Mandanten hinzugefügt werden, wenn Sie sie nach dem Kauf Benutzern im Verzeichnis zuweisen.

## <a name="manage-and-support-user-accounts"></a>Verwalten von Benutzerkonten und Bereitstellen von Support

* [Was soll ich meinen Benutzern raten, wenn sie auf ihrem Smartphone keine Antwort erhalten?](#what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone)
* [Was kann ich tun, wenn ein Benutzer nicht auf sein Konto zugreifen kann?](#what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account)
* [Was kann ich tun, wenn ein Benutzer ein Smartphone mit App-Kennwörtern verliert?](#what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords)
* [Was kann ich tun, wenn sich ein Benutzer nicht bei nicht browserbasierten Apps anmelden kann?](#what-if-a-user-cant-sign-in-to-non-browser-apps)
* [Meine Benutzer erhalten manchmal keine SMS, oder es tritt ein Timeout bei der Überprüfung auf.](#my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out)
* [Kann ich ändern, wie lange meine Benutzer für die Eingabe des Überprüfungscodes aus einer SMS Zeit haben, bevor ein Timeout auftritt?](#can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out)
* [Kann ich Hardwaretoken in Verbindung mit dem Azure Multi-Factor Authentication-Server verwenden?](#can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server)
* [Kann ich den Azure Multi-Factor Authentication-Server verwenden, um Terminaldienste zu schützen?](#can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services)
* [Ich habe im MFA-Server die Rufnummernanzeige konfiguriert, meine Benutzer erhalten von Multi-Factor Authentication aber weiterhin anonyme Anrufe.](#i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller)
* [Warum werden meine Benutzer zur Registrierung ihrer Sicherheitsinformationen aufgefordert?](#why-are-my-users-being-prompted-to-register-their-security-information)

### <a name="what-should-i-tell-my-users-to-do-if-they-dont-receive-a-response-on-their-phone"></a>Was soll ich meinen Benutzern raten, wenn sie auf ihrem Smartphone keine Antwort erhalten?

Bitten Sie Ihre Benutzer, innerhalb von 5 Minuten bis zu fünfmal zu versuchen, einen Telefonanruf oder eine SMS für die Authentifizierung zu empfangen. Microsoft verwendet mehrere Anbieter für die Übermittlung von Anrufen und SMS-Nachrichten. Wenn dies nicht funktioniert, öffnen Sie zur weiteren Problembehandlung einen Supportvorgang.

Sicherheits-Apps von Drittanbietern können auch die SMS-Nachricht oder den Telefonanruf mit dem Überprüfungscode blockieren. Wenn Sie eine Sicherheits-App eines Drittanbieters einsetzen, versuchen Sie, den Schutz zu deaktivieren, und fordern Sie dann die Zusendung eines weiteren MFA-Überprüfungscodes an.

Wenn die obigen Schritte nicht funktionieren, prüfen Sie, ob Benutzer für mehr als eine Überprüfungsmethode konfiguriert sind. Versuchen Sie, sich erneut anzumelden, aber wählen Sie auf der Anmeldeseite eine andere Überprüfungsmethode aus.

Weitere Informationen finden Sie in der [Anleitung zur Problembehandlung für Endbenutzer](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

### <a name="what-should-i-do-if-one-of-my-users-cant-get-in-to-their-account"></a>Was kann ich tun, wenn ein Benutzer nicht auf sein Konto zugreifen kann?

Sie können den Benutzer auffordern, den Registrierungsprozess erneut zu durchlaufen, um das Benutzerkonto zurückzusetzen. Weitere Informationen finden Sie unter [Verwalten von Benutzereinstellungen mit Azure AD Multi-Factor Authentication (MFA) in der Cloud](howto-mfa-userdevicesettings.md).

### <a name="what-should-i-do-if-one-of-my-users-loses-a-phone-that-is-using-app-passwords"></a>Was kann ich tun, wenn ein Benutzer ein Smartphone mit App-Kennwörtern verliert?

Löschen Sie alle App-Kennwörter des Benutzers, um nicht autorisierte Zugriffe zu verhindern. Wenn der Benutzer über ein Ersatzgerät verfügt, kann er neue Kennwörter erstellen. Weitere Informationen finden Sie unter [Verwalten von Benutzereinstellungen mit Azure AD Multi-Factor Authentication (MFA) in der Cloud](howto-mfa-userdevicesettings.md).

### <a name="what-if-a-user-cant-sign-in-to-non-browser-apps"></a>Was kann ich tun, wenn sich ein Benutzer nicht bei nicht browserbasierten Apps anmelden kann?

Falls Ihre Organisation noch Legacyclients verwendet und Sie [die Verwendung von App-Kennwörtern zugelassen haben](howto-mfa-app-passwords.md), können sich Ihre Benutzer nicht mit ihrem Benutzernamen und Kennwort bei diesen Legacyclients anmelden. Stattdessen müssen sie [App-Kennwörter einrichten](../user-help/multi-factor-authentication-end-user-app-passwords.md). Ihre Benutzer müssen ihre Anmeldeinformationen löschen, die App neu starten und sich dann mit ihrem Benutzernamen und ihrem *App-Kennwort* (anstelle des regulären Kennworts) anmelden.

Wenn Ihre Organisation keine Legacyclients verwendet, sollten Sie die Erstellung von App-Kennwörtern durch Benutzer nicht zulassen.

> [!NOTE]
> **Moderne Authentifizierung für Office 2013-Clients**
>
> App-Kennwörter werden nur für Apps benötigt, die keine moderne Authentifizierung unterstützen. Office 2013-Clients unterstützen moderne Authentifizierungsprotokolle, müssen aber konfiguriert werden. Die moderne Authentifizierung steht allen Kunden zur Verfügung, die das Office 2013-Update vom März 2015 oder höher verwenden. Weitere Informationen finden Sie in dem Blogbeitrag [Updated Office 365 modern authentication (Aktualisierte moderne Authentifizierung in Office 365)](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/).

### <a name="my-users-say-that-sometimes-they-dont-receive-the-text-message-or-the-verification-times-out"></a>Meine Benutzer erhalten manchmal keine SMS, oder es tritt ein Timeout bei der Überprüfung auf.

Die Zustellung von SMS-Nachrichten kann nicht garantiert werden, da die Zuverlässigkeit des Diensts durch unkontrollierbare Faktoren beeinträchtigt werden kann. Unter diese Faktoren fallen Bestimmungsland/-region, der Mobilfunkanbieter und die Signalstärke.

Sicherheits-Apps von Drittanbietern können auch die SMS-Nachricht oder den Telefonanruf mit dem Überprüfungscode blockieren. Wenn Sie eine Sicherheits-App eines Drittanbieters einsetzen, versuchen Sie, den Schutz zu deaktivieren, und fordern Sie dann die Zusendung eines weiteren MFA-Überprüfungscodes an.

Falls Ihre Benutzer häufig SMS-Empfangsprobleme haben, fordern Sie sie auf, die App Microsoft Authenticator oder die Telefonanrufmethode zu verwenden. Microsoft Authenticator kann Benachrichtigungen sowohl über Mobilfunk- als auch WLAN-Verbindungen empfangen. Darüber hinaus kann die mobile App Überprüfungscodes auch dann generieren, wenn das Gerät noch gar kein Signal empfangen hat. Die Microsoft Authenticator-App ist für [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [iOS](https://go.microsoft.com/fwlink/?Linkid=825073) und [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) verfügbar.

### <a name="can-i-change-the-amount-of-time-my-users-have-to-enter-the-verification-code-from-a-text-message-before-the-system-times-out"></a>Kann ich ändern, wie lange meine Benutzer für die Eingabe des Überprüfungscodes aus einer SMS Zeit haben, bevor ein Timeout auftritt?

In einigen Fällen schon.

Für unidirektionale SMS mit Azure MFA Server v7.0 oder höher können Sie die Einstellungen für das Zeitlimit per Einstellung eines Registrierungsschlüssel konfigurieren. Nachdem der MFA-Clouddienst die Textnachricht gesendet hat, wird der Überprüfungscode (oder die Einmalkennung) an den MFA-Server zurückgegeben. MFA-Server speichert den Code standardmäßig für 300 Sekunden im Arbeitsspeicher. Wenn der Benutzer seinen Code nicht innerhalb dieser 300 Sekunden eingibt, wird die Authentifizierung verweigert. Im Folgenden werden die Schritte für das Ändern der standardmäßigen Zeitlimiteinstellung beschrieben:

1. Gehe zu `HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor`.
2. Erstellen Sie einen **DWORD**-Registrierungsschlüssel namens *pfsvc_pendingSmsTimeoutSeconds*, und legen Sie fest, wie lange Azure MFA Server Einmalkennungen speichern soll (in Sekunden).

>[!TIP]
>
> Wenn Sie über mehrere MFA-Server verfügen, ist der an den Benutzer gesendete Überprüfungscode nur demjenigen Server bekannt, der die ursprüngliche Authentifizierungsanforderung verarbeitet hat. Wenn der Benutzer den Code erfasst, muss die Authentifizierungsanforderung, mit der der Code überprüft wird, an denselben Server gesendet werden. Wird der Überprüfungscode an einen anderen Server gesendet, wird die Überprüfung verweigert.

Wenn die Benutzer nicht innerhalb des Zeiteinschränkungen auf die SMS antworten, wird ihre Authentifizierung verweigert.

Bei unidirektionalen SMS mit Azure AD MFA in der Cloud (einschließlich AD FS-Adapter und Network Policy Server-Erweiterung) können Sie die Zeitlimiteinstellung nicht konfigurieren. Azure AD speichert den Überprüfungscode für 180 Sekunden.

### <a name="can-i-use-hardware-tokens-with-azure-multi-factor-authentication-server"></a>Kann ich Hardwaretoken in Verbindung mit dem Azure Multi-Factor Authentication-Server verwenden?

Wenn Sie den Azure Multi-Factor Authentication-Server verwenden, können Sie zeitbasierte Token für die offene Authentifizierung (Open Authentication, OATH) mit einmaligen Kennwörtern (Time-based One-time Password Algorithm, TOTP) von Drittanbietern importieren und sie dann für die zweistufige Überprüfung verwenden.

Sie können ActiveIdentity-Token vom Typ „OATH TOTP“ verwenden, wenn Sie den geheimen Schlüssel in eine CSV-Datei einfügen und in den Azure Multi-Factor Authentication-Server importieren. Sie können OATH-Token mit Active Directory Federation Services (AD FS), mit formularbasierter IIS-Authentifizierung (Internet Information Services) sowie mit Remote Authentication Dial-In User Service (RADIUS) verwenden, sofern das Clientsystem diese Benutzereingabe annehmen kann.

Sie können OATH TOTP-Drittanbietertoken mit folgenden Formaten importieren:  

- Portable Symmetric Key Container (PSKC)
- CSV, wenn die Datei eine Seriennummer, einen geheimen Schlüssel im Base 32-Format, und ein Zeitintervall enthält

### <a name="can-i-use-azure-multi-factor-authentication-server-to-secure-terminal-services"></a>Kann ich den Azure Multi-Factor Authentication-Server verwenden, um Terminaldienste zu schützen?

Ja, aber wenn Sie Windows Server 2012 R2 oder höher verwenden, können Sie Terminaldienste nur mithilfe von Remotedesktopgateway (RD-Gateway) schützen.

Aufgrund von Sicherheitsänderungen in Windows Server 2012 R2 hat sich die Verbindungsherstellung zwischen dem Azure Multi-Factor Authentication-Server und dem Sicherheitspaket der lokalen Sicherheitsautorität (Local Security Authority, LSA) in Windows Server 2012 und früheren Versionen geändert. Bei der Verwendung von Terminaldiensten unter Windows Server 2012 oder einer älteren Version können Sie [eine Anwendung mit der Windows-Authentifizierung sichern](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Wenn Sie Windows Server 2012 R2 verwenden, benötigen Sie ein RD-Gateway.

### <a name="i-configured-caller-id-in-mfa-server-but-my-users-still-receive-multi-factor-authentication-calls-from-an-anonymous-caller"></a>Ich habe im MFA Server die Rufnummernanzeige konfiguriert, meine Benutzer erhalten von Multi-Factor Authentication aber weiterhin anonyme Anrufe.

Wenn Multi-Factor Authentication-Anrufe über das öffentliche Telefonnetz geleitet werden, laufen sie manchmal über einen Betreiber, der die Rufnummernanzeige nicht unterstützt. Aus diesem Grund ist die Anzeige der Rufnummer nicht garantiert, obwohl das Multi-Factor Authentication-System sie immer sendet.

### <a name="why-are-my-users-being-prompted-to-register-their-security-information"></a>Warum werden meine Benutzer zur Registrierung ihrer Sicherheitsinformationen aufgefordert?

Benutzer können aus mehreren Gründen zur Registrierung ihrer Sicherheitsinformationen aufgefordert werden:

- Unter Umständen hat der Administrator in Azure AD MFA für den Benutzer aktiviert, der Benutzer hat jedoch noch keine Sicherheitsinformationen für sein Konto registriert.
- Für den Benutzer wurde die Self-Service-Kennwortzurücksetzung in Azure AD aktiviert. Mithilfe der Sicherheitsinformationen kann der Benutzer sein Kennwort zurücksetzen, falls er es einmal vergessen sollte.
- Der Benutzer hat auf eine Anwendung mit einer Richtlinie für bedingten Zugriff zugegriffen, die MFA erfordert, und hat sich bislang noch nicht für MFA registriert.
- Der Benutzer registriert ein Gerät bei Azure AD (einschließlich Azure AD Join), und Ihre Organisation schreibt MFA für die Geräteregistrierung vor, der Benutzer hat sich bislang aber noch nicht für MFA registriert.
- Der Benutzer generiert Windows Hello for Business unter Windows 10 (wofür MFA erforderlich ist) und hat sich bislang noch nicht für MFA registriert.
- Eine von der Organisation erstellte und aktivierte MFA-Registrierungsrichtlinie wurde auf den Benutzer angewendet.
- Der Benutzer hat sich zwar bereits für MFA registriert, dabei aber eine Überprüfungsmethode gewählt, die inzwischen von einem Administrator deaktiviert wurde. Daher muss der Benutzer die MFA-Registrierung erneut durchlaufen und eine neue Standardüberprüfungsmethode auswählen.

## <a name="errors"></a>Errors

* [Was sollten Benutzer tun, wenn bei der Verwendung von Benachrichtigungen über die mobile App die Fehlermeldung „Authentifizierungsanforderung gilt nicht für ein aktiviertes Konto“ angezeigt wird?](#what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications)
* [Was können Benutzer tun, wenn sie beim Anmelden bei einer nicht browserbasierten Anwendung die Fehlermeldung 0x800434D4L erhalten?](#what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application)

### <a name="what-should-users-do-if-they-see-an-authentication-request-is-not-for-an-activated-account-error-message-when-using-mobile-app-notifications"></a>Was sollten Benutzer tun, wenn bei der Verwendung von Benachrichtigungen über die mobile App die Fehlermeldung „Authentifizierungsanforderung gilt nicht für ein aktiviertes Konto“ angezeigt wird?

Bitten Sie den Benutzer, das folgende Verfahren zu befolgen, um sein Konto aus Microsoft Authenticator zu entfernen und es anschließend erneut hinzuzufügen:

1. Wechseln Sie zum [Azure-Portalprofil](https://account.activedirectory.windowsazure.com/profile/), und melden Sie sich mit einem Organisationskonto an.
2. Wählen Sie **Zusätzliche Sicherheitsüberprüfung** aus.
3. Entfernen Sie das vorhandene Konto aus der App Microsoft Authenticator.
4. Klicken Sie auf **Konfigurieren**, und folgen Sie den Anweisungen, um Microsoft Authenticator neu zu konfigurieren.

### <a name="what-should-users-do-if-they-see-a-0x800434d4l-error-message-when-signing-in-to-a-non-browser-application"></a>Was können Benutzer tun, wenn sie beim Anmelden bei einer nicht browserbasierten Anwendung die Fehlermeldung 0x800434D4L erhalten?

Der Fehler *0x800434D4L* tritt auf, wenn Sie versuchen, sich bei einer nicht browserbasierten, auf einem lokalen Computer installierten Anwendung anzumelden, die nicht mit Konten verwendet werden kann, die eine zweistufige Überprüfung erfordern.

Um diesen Fehler zu umgehen, können Sie separate Benutzerkonten für Administratoraufgaben und Nicht-Administratoraufgaben erstellen. Später können Sie die Postfächer beider Konten miteinander verknüpfen, sodass Sie sich bei Outlook lediglich über ihr Nicht-Administratorkonto anmelden. Weitere Informationen über diese Lösung und wie ein Administrator die Möglichkeit erhält, den Inhalt des Postfachs eines Benutzers zu öffnen und anzuzeigen, finden Sie in [Verwalten von Berechtigungen für Empfänger](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Nächste Schritte

Wenn Ihre Frage hier nicht beantwortet wird, sind die folgenden Supportoptionen verfügbar:

* Durchsuchen Sie die [Microsoft Support Wissensdatenbank](https://support.microsoft.com) nach Lösungen für häufige technische Probleme.
* In [Azure Active Directory Q&A](/answers/topics/azure-active-directory.html)können Sie nach technischen Fragen und Antworten aus der Community suchen oder eine eigene Frage stellen.
* Wenden Sie sich über den [Support für Azure Multi-Factor Authentication-Server](https://support.microsoft.com/oas/default.aspx?prid=14947) an einen Support-Experten. Es ist hilfreich, wenn Sie uns so viele Informationen wie möglich über Ihr Problem geben, wenn Sie sich mit uns in Verbindung setzen. Diese Informationen können die Website umfassen, auf der der Fehler aufgetreten ist, den spezifischen Fehlercode, die spezifische Sitzungs-ID, und die ID des Benutzers, der den Fehler beobachtet hat.
* Wenn Sie ein PhoneFactor-Kunde mit einem älteren Gerät sind und Fragen haben oder Hilfe beim Zurücksetzen eines Kennworts benötigen, öffnen Sie über die E-Mail-Adresse [phonefactorsupport@microsoft.com](mailto:phonefactorsupport@microsoft.com) einen Supportvorgang.