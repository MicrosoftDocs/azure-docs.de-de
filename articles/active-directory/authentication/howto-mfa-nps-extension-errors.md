---
title: Problembehandlung der NPS-Erweiterung für Azure AD MFA – Azure Active Directory
description: Hilfe zum Auflösen von Problemen mit der NPS-Erweiterung für Azure AD Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: fa3a2366f007ff1481e7c84f049e606586392037
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742951"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-ad-multi-factor-authentication"></a>Auflösen von Fehlermeldungen in der NPS-Erweiterung für Azure AD Multi-Factor Authentication

Wenn Fehler in der NPS-Erweiterung für Azure AD Multi-Factor Authentication auftreten, können Sie mit diesem Artikel schneller eine Lösung erreichen. NPS-Erweiterungsprotokolle befinden sich auf dem Server mit der NPS-Erweiterung in der Ereignisanzeige unter **Benutzerdefinierte Ansichten** > **Serverrollen** > **Netzwerkrichtlinien- und Zugriffsdienste**.

## <a name="troubleshooting-steps-for-common-errors"></a>Schritte zur Problembehandlung bei häufigen Fehlern

| Fehlercode | Schritte zur Problembehandlung |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Kontaktieren Sie den Support](#contact-microsoft-support), und geben Sie die Liste der Schritte zum Erfassen von Protokollen an. Stellen Sie so viele Informationen wie möglich über das bereit, was vor dem Fehler passiert ist, einschließlich der Mandanten-ID und des Benutzerprinzipalnamens (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Möglicherweise gibt es ein Problem damit, wie das Clientzertifikat installiert oder Ihrem Mandanten zugeordnet wurde. Befolgen Sie die Anweisungen unter [Behandeln von Problemen mit der MFA NPS-Erweiterung](howto-mfa-nps-extension.md#troubleshooting), um Clientzertifizierungsprobleme zu untersuchen. |
| **ESTS_TOKEN_ERROR** | Befolgen Sie die Anweisungen unter [Behandeln von Problemen mit der MFA NPS-Erweiterung](howto-mfa-nps-extension.md#troubleshooting), um Clientzertifizierungs- und ADAL-Tokenprobleme zu untersuchen. |
| **HTTPS_COMMUNICATION_ERROR** | Der NPS-Server kann keine Antworten von Azure AD MFA empfangen. Stellen Sie sicher, dass Ihre Firewalls bidirektional für Datenverkehr an und von https://adnotifications.windowsazure.com geöffnet sind. |
| **HTTP_CONNECT_ERROR** | Stellen Sie auf dem Server, auf dem die NPS-Erweiterung ausgeführt wird, sicher, dass Sie `https://adnotifications.windowsazure.com` und `https://login.microsoftonline.com/` erreichen können. Wenn diese Websites nicht geladen werden, beheben Sie Konnektivitätsprobleme auf diesem Server. |
| **NPS-Erweiterung für Azure AD MFA:** <br> Die NPS-Erweiterung für Azure AD MFA führt nur sekundäre Authentifizierungsanforderungen für Radius im Status „AccessAccept“ aus. Anforderungen für einen Benutzer mit dem Antwortstatus „AccessReject“ werden ignoriert. | Dieser Fehler entsteht in der Regel durch einen Fehler in AD oder bedeutet, dass der NPS-Server keine Antworten von Azure AD empfangen kann. Stellen Sie sicher, dass Ihre Firewalls bidirektional für Datenverkehr über die Ports 80 und 443 an und von `https://adnotifications.windowsazure.com` und `https://login.microsoftonline.com` geöffnet sind. Überprüfen Sie unbedingt auch auf der Registerkarte „Einwählen“ der Netzwerkzugriffsberechtigungen, ob die Einstellung auf „Zugriff über NPS-Netzwerkrichtlinien steuern“ festgelegt ist. Dieser Fehler kann ausgelöst werden, wenn dem Benutzer keine Lizenz zugewiesen wird. |
| **REGISTRY_CONFIG_ERROR** | Ein Schlüssel fehlt in der Registrierung für die Anwendung. Die Ursache kann sein, dass ein [PowerShell-Skript](howto-mfa-nps-extension.md#install-the-nps-extension) nach der Installation nicht ausgeführt wurde. Die Fehlermeldung sollte den fehlenden Schlüssel enthalten. Stellen Sie sicher, dass der Schlüssel unter „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa“ vorhanden ist. |
| **REQUEST_FORMAT_ERROR** <br> In der RADIUS-Anforderung fehlt das obligatorische userName\Identifier-Attribut für RADIUS. Stellen Sie sicher, dass NPS RADIUS-Anforderungen empfängt. | Dieser Fehler entsteht normalerweise durch ein Installationsproblem. Die NPS-Erweiterung muss auf NPS-Servern installiert sein, die RADIUS-Anforderungen empfangen können. NPS-Server, die als Abhängigkeiten für Dienste wie RDG und RRAS installiert sind, empfangen keine RADIUS-Anforderungen. Die NPS-Erweiterung funktioniert nicht, wenn sie über solche Installationen installiert wird, und es kommt zu Fehlern, da sie die Details aus der Authentifizierungsanforderung nicht lesen kann. |
| **REQUEST_MISSING_CODE** | Stellen Sie sicher, dass das Protokoll für die Kennwortverschlüsselung zwischen den NPS- und NAS-Servern die sekundäre Authentifizierungsmethode unterstützt, die Sie verwenden. **PAP** unterstützt alle Authentifizierungsmethoden von Azure AD MFA in der Cloud: Telefonanruf, unidirektionale Textnachricht, Benachrichtigung über eine mobile App und Überprüfungscode in der mobilen App. **CHAPV2** und **EAP** unterstützt Telefonanruf und Benachrichtigung über eine mobile App. |
| **USERNAME_CANONICALIZATION_ERROR** | Stellen Sie sicher, dass der Benutzer in Ihrer lokalen Active Directory-Instanz vorhanden ist und dass der NPS-Dienst über Berechtigungen zum Zugriff auf das Verzeichnis verfügt. Wenden Sie sich bei Verwendung von gesamtstrukturübergreifenden Vertrauensstellungen [an den Support](#contact-microsoft-support), um weitere Hilfe zu erhalten. |

### <a name="alternate-login-id-errors"></a>Alternative Anmelde-ID-Fehler

| Fehlercode | Fehlermeldung | Schritte zur Problembehandlung |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Fehler: userObjectSid lookup failed (Fehler bei der userObjectSid-Suche) | Stellen Sie sicher, dass der Benutzer in Ihrer lokalen Active Directory-Instanz vorhanden ist. Wenden Sie sich bei Verwendung von gesamtstrukturübergreifenden Vertrauensstellungen [an den Support](#contact-microsoft-support), um weitere Hilfe zu erhalten. |
| **ALTERNATE_LOGIN_ID_ERROR** | Error: Alternate LoginId lookup failed (Fehler bei der Suche nach alternativer Anmelde-ID) | Stellen Sie sicher, dass LDAP_ALTERNATE_LOGINID_ATTRIBUTE auf ein [gültiges Active Directory-Attribut](/windows/win32/adschema/attributes-all) festgelegt ist. <br><br> Wenn LDAP_FORCE_GLOBAL_CATALOG auf „TRUE“ festgelegt, oder LDAP_LOOKUP_FORESTS mit einem nicht leeren Wert konfiguriert wurde, stellen Sie sicher, dass Sie einen globalen Katalog konfiguriert haben, und dass das AlternateLoginId-Attribut hinzugefügt wurde. <br><br> Wenn LDAP_LOOKUP_FORESTS mit einem nicht leeren Wert konfiguriert wurde, stellen Sie sicher, dass der Wert richtig ist. Wenn mehr als ein Name der Gesamtstruktur vorhanden ist, müssen die Namen durch Semikolons und nicht durch Leerzeichen getrennt werden. <br><br> Wenn diese Schritte das Problem nicht beheben, [wenden Sie sich an den Support](#contact-microsoft-support), um mehr Unterstützung zu erhalten. |
| **ALTERNATE_LOGIN_ID_ERROR** | Error: Alternate LoginId value is empty (Der AlternateLoginId-Wert ist leer) | Stellen Sie sicher, dass das AlternateLoginId-Attribut für den Benutzer konfiguriert ist. |

## <a name="errors-your-users-may-encounter"></a>Fehler, die bei Ihren Benutzern auftreten können

| Fehlercode | Fehlermeldung | Schritte zur Problembehandlung |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | Der Mandant des Aufrufers verfügt nicht über Zugriffsberechtigungen zur Authentifizierung des Benutzers. | Überprüfen Sie, ob die Mandantendomäne und die Domäne des Benutzerprinzipalnamens (UPN) identisch sind. Stellen Sie beispielsweise sicher, dass user@contoso.com versucht, sich beim Contoso-Mandanten zu authentifizieren. Der UPN stellt einen gültigen Benutzer für den Mandanten in Azure dar. |
| **AuthenticationMethodNotConfigured** | Die angegebene Authentifizierungsmethode wurde für den Benutzer nicht konfiguriert. | Fordern Sie den Benutzer auf, die Überprüfungsmethoden gemäß der Anleitung in [Verwalten der Einstellungen für die zweistufige Überprüfung](../user-help/multi-factor-authentication-end-user-manage-settings.md) hinzuzufügen oder zu überprüfen. |
| **AuthenticationMethodNotSupported** | Die angegebene Authentifizierungsmethode wird nicht unterstützt. | Erfassen Sie alle Protokolle, die diesen Fehler enthalten, und [wenden Sie sich an den Support](#contact-microsoft-support). Wenn Sie sich an den Support wenden, geben Sie den Benutzernamen und die sekundäre Überprüfungsmethode an, die den Fehler ausgelöst hat. |
| **BecAccessDenied** | Der MSODS BEC-Aufruf hat „Zugriff verweigert“ zurückgegeben, wahrscheinlich ist der Benutzername im Mandanten nicht definiert. | Der Benutzer ist in der lokalen Active Directory-Instanz vorhanden, wird aber nicht mit AD Connect in Azure AD synchronisiert. Oder der Benutzer fehlt für den Mandanten. Fügen Sie den Benutzer zu Azure AD hinzu, und fordern Sie ihn auf, die Überprüfungsmethoden gemäß der Anleitung in [Verwalten der Einstellungen für die zweistufige Überprüfung](../user-help/multi-factor-authentication-end-user-manage-settings.md) hinzuzufügen oder zu überprüfen. |
| **InvalidFormat** oder **StrongAuthenticationServiceInvalidParameter** | Die Telefonnummer hat ein nicht erkennbares Format. | Bitten Sie die Benutzer, ihre Telefonnummern für die Überprüfung zu korrigieren. |
| **InvalidSession** | Die angegebene Sitzung ist ungültig oder möglicherweise abgelaufen | Der Abschluss der Sitzung hat mehr als drei Minuten in Anspruch genommen. Stellen Sie sicher, dass der Benutzer innerhalb von drei Minuten nach Initiierung der Authentifizierungsanforderung den Überprüfungscode eingibt oder auf die App-Benachrichtigung antwortet. Wenn das Problem dadurch nicht behoben wird, stellen Sie sicher, dass keine Netzwerklatenzen zwischen Client, NAS-Server, NPS-Server und Azure AD MFA-Endpunkt vorhanden sind.  |
| **NoDefaultAuthenticationMethodIsConfigured** | Für den Benutzer wurde keine Standardauthentifizierungsmethode konfiguriert. | Fordern Sie den Benutzer auf, die Überprüfungsmethoden gemäß der Anleitung in [Verwalten der Einstellungen für die zweistufige Überprüfung](../user-help/multi-factor-authentication-end-user-manage-settings.md) hinzuzufügen oder zu überprüfen. Stellen Sie sicher, dass der Benutzer eine Standardauthentifizierungsmethode ausgewählt und diese Methode für sein Konto konfiguriert hat. |
| **OathCodePinIncorrect** | Falscher Code und falsche Pin eingegeben. | Dieser Fehler sollte in der NPS-Erweiterung nicht auftreten. Wenn bei Ihrem Benutzer der Fehler auftritt, [wenden Sie sich an den Support](#contact-microsoft-support), um Hilfe bei der Problembehandlung zu erhalten. |
| **ProofDataNotFound** | Nachweisdaten wurden für die angegebene Authentifizierungsmethode nicht konfiguriert. | Fordern Sie den Benutzer auf, eine andere Überprüfungsmethode auszuprobieren oder eine neue Überprüfungsmethode gemäß der Anleitung in [Verwalten der Einstellungen für die zweistufige Überprüfung](../user-help/multi-factor-authentication-end-user-manage-settings.md) hinzuzufügen. Wenn dieser Fehler weiterhin angezeigt wird, nachdem Sie sichergestellt haben, dass die Überprüfungsmethode ordnungsgemäß eingerichtet ist, [wenden Sie sich an den Support](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Falscher Code und falsche Pin eingegeben. (OneWaySMS) | Dieser Fehler sollte in der NPS-Erweiterung nicht auftreten. Wenn bei Ihrem Benutzer der Fehler auftritt, [wenden Sie sich an den Support](#contact-microsoft-support), um Hilfe bei der Problembehandlung zu erhalten. |
| **TenantIsBlocked** | Der Mandant wird blockiert. | [Wenden Sie sich an den Support](#contact-microsoft-support), und geben Sie die *Mandanten-ID* von der Azure AD-Eigenschaftenseite im Azure-Portal an. |
| **UserNotFound** | Der angegebene Benutzer wurde nicht gefunden. | Der Mandant wird in Azure AD nicht mehr als aktiv angezeigt. Überprüfen Sie, ob Ihr Abonnement aktiv ist und Sie über die erforderlichen Erstanbieter-Apps verfügen. Stellen Sie außerdem sicher, dass der Mandant im Zertifikatantragsteller den erwarteten Wert aufweist und dass das Zertifikat noch gültig und unter dem Dienstprinzipal registriert ist. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Meldungen, die den Benutzern angezeigt werden können, aber keine Fehler sind

Manchmal können Ihren Benutzern Meldungen von Multi-Factor Authentication angezeigt werden, da ihre Authentifizierungsanforderung fehlgeschlagen ist. Dies sind keine Fehler als Folge der Konfiguration, sondern absichtliche Warnungen, die erläutern, warum eine Authentifizierungsanforderung verweigert wurde.

| Fehlercode | Fehlermeldung | Empfohlene Schritte |
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Falscher Code wurde eingegeben/der OATH-Code ist falsch | Der Benutzer hat den falschen Code eingegeben. Fordern Sie ihn auf, es erneut zu versuchen, indem er einen neuen Code anfordert oder sich erneut anmeldet. |
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Maximal zulässige Anzahl von Codewiederholungen erreicht | Der Benutzer hat die Überprüfung zu oft nicht bestanden. Abhängig von Ihren Einstellungen muss die Sperre jetzt möglicherweise durch einen Administrator aufgehoben werden.  |
| **SMSAuthFailedWrongCodeEntered** | Falscher Code eingegeben/OTP der Textmeldung ist falsch | Der Benutzer hat den falschen Code eingegeben. Fordern Sie ihn auf, es erneut zu versuchen, indem er einen neuen Code anfordert oder sich erneut anmeldet. |

## <a name="errors-that-require-support"></a>Fehler, für die Support erforderlich ist

Wenn einer dieser Fehler auftritt, sollten Sie sich [an den Support wenden](#contact-microsoft-support), um Hilfe bei der Diagnose zu erhalten. Es gibt keine Standardschritte, um diese Fehler zu beheben. Wenn Sie sich an den Support wenden, sollten Sie möglichst viele Informationen zu den Schritten, die zu einem Fehler geführt haben, und Informationen zum Mandanten angeben.

| Fehlercode | Fehlermeldung |
| ---------- | ------------- |
| **InvalidParameter** | Die Anforderung darf nicht null sein. |
| **InvalidParameter** | ObjectId darf nicht null oder leer sein für ReplicationScope:{0} |
| **InvalidParameter** | Die Länge von CompanyName \{0}\ ist länger als die maximal zulässige Länge {1} |
| **InvalidParameter** | UserPrincipalName darf nicht null oder leer sein. |
| **InvalidParameter** | Die angegebene TenantId hat nicht das richtige Format. |
| **InvalidParameter** | SessionId darf nicht null oder leer sein. |
| **InvalidParameter** | ProofData aus der Anforderung oder MSODS konnte nicht aufgelöst werden. ProofData ist unbekannt. |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Nächste Schritte

### <a name="troubleshoot-user-accounts"></a>Problembehandlung bei Benutzerkonten

Wenn Ihre Benutzer [Probleme mit der zweistufigen Überprüfung haben](../user-help/multi-factor-authentication-end-user-troubleshoot.md), unterstützen Sie sie beim Diagnostizieren der Probleme.

### <a name="health-check-script"></a>Skript zur Integritätsprüfung

Das [Skript zur Integritätsprüfung der Azure AD MFA NPS-Erweiterung](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/) führt eine grundlegende Integritätsprüfung bei der Problembehandlung der NPS-Erweiterung durch. Führen Sie das Skript aus, und wählen Sie Option 3 aus.

### <a name="contact-microsoft-support"></a>Microsoft-Support kontaktieren

Wenn Sie zusätzliche Hilfe benötigen, wenden Sie sich über [Azure Multi-Factor Authentication-Server – Support](https://support.microsoft.com/oas/default.aspx?prid=14947) an einen Supportspezialisten. Es ist hilfreich, wenn Sie uns so viele Informationen wie möglich über Ihr Problem geben, wenn Sie sich mit uns in Verbindung setzen. Diese Informationen können die Seite umfassen, auf der der Fehler aufgetreten ist, den spezifischen Fehlercode, die spezifische Sitzungs-ID, die ID des Benutzers, der den Fehler beobachtet hat, und Debugprotokolle.

Um Debugprotokolle zur Unterstützung der Diagnose zu erfassen, führen Sie die folgenden Schritte auf dem NPS-Server aus:

1. Öffnen Sie den Registrierungs-Editor, und wechseln Sie zu „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa“. Legen Sie **VERBOSE_LOG** auf **TRUE** fest.
2. Öffnen Sie eine Administratoreingabeaufforderung, und führen Sie diese Befehle aus:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. Reproduzieren Sie das Problem.

4. Beenden Sie die Ablaufverfolgung mit diesen Befehlen:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. Öffnen Sie den Registrierungs-Editor, und wechseln Sie zu „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa“. Legen Sie **VERBOSE_LOG** auf **FALSE** fest.
6. Zippen Sie den Inhalt des Ordners „C:\NPS“, und fügen Sie die ZIP-Datei an die Supportanfrage an.