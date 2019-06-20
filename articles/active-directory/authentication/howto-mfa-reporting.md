---
title: Zugriffs- und Nutzungsberichte für Azure MFA – Azure Active Directory
description: Beschreibt, wie Sie das Berichte-Feature für Multi-Factor Authentication verwenden.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988e8982b6f06fb1210330c5cafdb696892794fe
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235527"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Berichte in Azure Multi-Factor Authentication

Azure Multi-Factor Authentication bietet verschiedene Berichte, die Sie und Ihre Organisation im Azure-Portal verwenden können. In der folgenden Tabelle sind die verfügbaren Berichte aufgeführt:

| Bericht | Location | BESCHREIBUNG |
|:--- |:--- |:--- |
| Verlauf – gesperrte Benutzer | Azure AD > MFA-Server > Benutzer blockieren/entsperren | Zeigt die Liste der Anforderungen zum Blockieren und Entsperren von Benutzern an. |
| Nutzung und Betrugswarnungen | Azure AD > Anmeldungen | Bietet Informationen zur Gesamtnutzung, Übersichts- und Detailinformationen zu Benutzern sowie einen Verlauf von Betrugswarnungen, die im angegebenen Zeitraum gesendet wurden. |
| Nutzung für lokale Komponenten | Azure AD > MFA-Server > Aktivitätsbericht | Bietet Informationen zur Gesamtnutzung für MFA durch die NPS-Erweiterung, AD FS und den MFA-Server. |
| Verlauf – Umgangene Benutzer | Azure AD > MFA-Server > Einmalumgehung | Zeigt den Verlauf von Anforderungen zur Umgehung der Multi-Factor Authentication für einen Benutzer an. |
| Serverstatus | Azure AD > MFA-Server > Serverstatus | Zeigt den Status von Multi-Factor Authentication-Servern an, die mit Ihrem Konto verknüpft sind. |

## <a name="view-mfa-reports"></a>Anzeigen von MFA-Berichten

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie auf der linken Seite **Azure Active Directory** > **MFA-Server** aus.
3. Wählen Sie den Bericht aus, den Sie anzeigen möchten.

   ![MFA Server-Serverstatusbericht im Azure-Portal](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Azure AD-Anmeldungenbericht

Mit dem **Anmeldungenaktivitätsbericht** im [Azure-Portal](https://portal.azure.com) können Sie alle Informationen abrufen, die Sie zum Ermitteln des Zustands Ihrer Umgebung benötigen.

Der Anmeldungenbericht kann Informationen zur Nutzung von verwalteten Anwendungen und Benutzeranmeldeaktivitäten, z.B. auch Informationen zur Nutzung von MFA (Multi-Factor Authentication, mehrstufige Authentifizierung), enthalten. Die MFA-Daten liefern Ihnen Erkenntnisse zur Funktionsweise von MFA in Ihrer Organisation. Sie können dann beispielsweise folgende Fragen beantworten:

- Wurde bei der Anmeldung MFA verwendet?
- Wie hat der Benutzer den MFA-Vorgang durchgeführt?
- Warum konnte der Benutzer den MFA-Vorgang nicht durchführen?
- Wie viele Benutzer werden zur Durchführung des MFA-Vorgangs aufgefordert?
- Wie viele Benutzer können den MFA-Vorgang nicht durchführen?
- Welche MFA-Probleme treten für Benutzer häufig auf?

Diese Daten sind über das [Azure-Portal](https://portal.azure.com) und die [Berichterstellungs-API](../reports-monitoring/concept-reporting-api.md) verfügbar.

![Azure AD-Anmeldebericht im Azure-Portal](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Struktur des Anmeldungenberichts

Über die Berichte zu den Anmeldeaktivitäten für MFA erhalten Sie Zugriff auf die folgenden Informationen:

**MFA erforderlich:** Gibt an, ob MFA für die Anmeldung erforderlich ist. MFA kann aufgrund von MFA pro Benutzer, bedingtem Zugriff oder aus anderen Gründen obligatorisch sein. Mögliche Werte sind **Ja** oder **Nein**.

**MFA-Ergebnis:** Weitere Informationen dazu, ob der MFA-Vorgang erfolgreich durchgeführt wurde:

- Wenn der MFA-Vorgang erfolgreich war, enthält diese Spalte weitere Details dazu.
   - Azure Multi-Factor Authentication
      - completed in the cloud (in der Cloud durchgeführt)
      - has expired due to the policies configured on tenant (ist aufgrund der auf dem Mandanten konfigurierten Richtlinien abgelaufen)
      - registration prompted (zur Registrierung aufgefordert)
      - satisfied by claim in the token (per Anspruch im Token erfüllt)
      - satisfied by claim provided by external provider (per Anspruch vom externen Anbieter erfüllt)
      - satisfied by strong authentication (per strenger Authentifizierung erfüllt)
      - skipped as flow exercised was Windows broker logon flow (übersprungen, da es sich um einen Windows-Broker-Anmeldefluss gehandelt hat)
      - skipped due to app password (übersprungen aufgrund von App-Kennwort)
      - skipped due to location (übersprungen aufgrund von Standort)
      - skipped due to registered device (übersprungen aufgrund von registriertem Gerät)
      - skipped due to remembered device (übersprungen aufgrund von gespeichertem Gerät)
      - successfully completed (erfolgreich abgeschlossen)
   - Redirected to external provider for multi-factor authentication (umgeleitet an externen Anbieter zur mehrstufigen Authentifizierung)

- Wenn der MFA-Vorgang nicht erfolgreich war, ist in dieser Spalte der Grund angegeben.
   - Azure Multi-Factor Authentication denied; (Azure Multi-Factor Authentication verweigert;)
      - authentication in-progress (Authentifizierung in Bearbeitung)
      - duplicate authentication attempt (versuchte doppelte Authentifizierung)
      - entered incorrect code too many times (falschen Code zu häufig eingegeben)
      - invalid authentication (ungültige Authentifizierung)
      - invalid mobile app verification code (ungültiger Verifizierungscode der mobilen App)
      - misconfiguration (Fehlkonfiguration)
      - phone call went to voicemail (Voicemail bei Telefonanruf)
      - phone number has an invalid format (Telefonnummer hat ein ungültiges Format)
      - service error (Dienstfehler)
      - unable to reach the user’s phone (Telefon des Benutzers nicht erreichbar)
      - unable to send the mobile app notification to the device (Benachrichtigung über mobile App kann nicht an das Gerät gesendet werden)
      - unable to send the mobile app notification (Benachrichtigung über mobile App kann nicht gesendet werden)
      - user declined the authentication (Benutzer hat die Authentifizierung abgelehnt)
      - user did not respond to mobile app notification (Benutzer hat auf Benachrichtigung über mobile App nicht geantwortet)
      - user does not have any verification methods registered (für Benutzer sind keine Verifizierungsmethoden registriert)
      - user entered incorrect code (Benutzer hat falschen Code eingegeben)
      - user entered incorrect PIN (Benutzer hat falsche PIN eingegeben)
      - user hung up the phone call without succeeding the authentication (Benutzer hat Gespräch beendet, ohne die Authentifizierung durchzuführen)
      - user is blocked (Benutzer ist gesperrt)
      - user never entered the verification code (Benutzer hat den Verifizierungscode niemals eingegeben)
      - user not found (Benutzer wurde nicht gefunden)
      - verification code already used once (Verifizierungscode wurde bereits einmal verwendet)

**MFA-Authentifizierungsmethode:** Die Authentifizierungsmethode, die vom Benutzer zum Durchführen des MFA-Vorgangs verwendet wurde. Mögliche Werte sind:

- Textnachricht
- Benachrichtigung über eine mobile App
- Telefonanruf (Telefonnummer für Authentifizierung)
- Prüfcode in der mobilen App
- Telefonanruf (Geschäftliche Telefonnummer)
- Telefonanruf (alternative Telefonnummer für Authentifizierung)

**MFA-Authentifizierungsdetail:** Bereinigte Version der Telefonnummer, z.B. +X XXXXXXXX64.

**Bedingter Zugriff**: Finden Sie Informationen über Richtlinien für bedingten Zugriff, die den Anmeldeversuch beeinflusst haben, einschließlich:

- Richtlinienname
- Gewährungssteuerelemente
- Sitzungssteuerelemente
- Ergebnis

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>PowerShell-Berichte über für MFA registrierte Benutzer

Stellen Sie zunächst sicher, dass das [MSOnline-V1-PowerShell-Modul](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) installiert ist.

Identifizieren Sie mithilfe des folgenden PowerShell-Befehls Benutzer, die sich für MFA registriert haben.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifizieren Sie mithilfe des folgenden PowerShell-Befehls Benutzer, die sich nicht für MFA registriert haben.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="possible-results-in-activity-reports"></a>Mögliche Ergebnisse in Aktivitätsberichten

Die folgende Tabelle kann verwendet werden, um die Problembehandlung für die mehrstufige Authentifizierung durchzuführen, indem die heruntergeladene Version des Aktivitätsberichts für die mehrstufige Authentifizierung verwendet wird. Die Anzeige erfolgt nicht direkt im Azure-Portal.

| Anrufergebnis | BESCHREIBUNG | Genauere Beschreibung |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN wurde eingegeben | Der Benutzer hat eine PIN eingegeben.  Wenn die Authentifizierung erfolgreich war, wurde die richtige PIN eingegeben.  Falls die Authentifizierung verweigert wird, wurde eine falsche PIN eingegeben, oder für den Benutzer ist der Modus „Standard“ festgelegt. |
| SUCCESS_NO_PIN | Nur # wurde eingegeben | Wenn der Benutzer auf den Modus „PIN“ festgelegt ist und die Authentifizierung verweigert wird, bedeutet dies Folgendes: Der Benutzer hat seine PIN nicht eingegeben, sondern nur #.  Wenn der Benutzer auf den Modus „Standard“ festgelegt ist und die Authentifizierung erfolgreich ist, bedeutet dies, dass der Benutzer nur # eingegeben hat. Dies ist im Modus „Standard“ die richtige Vorgehensweise. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # nach Eingabe nicht gedrückt | Der Benutzer hat keine DTMF-Ziffern gesendet, weil # nicht eingegeben wurde.  Andere eingegebene Ziffern werden nur gesendet, wenn # eingegeben wird, um den Abschluss der Eingabe anzugeben. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Keine Telefoneingabe: Zeitüberschreitung | Der Anruf wurde angenommen, aber es ist keine Antwort vorhanden.  Dies deutet normalerweise darauf hin, dass der Anruf per Voicemail angenommen wurde. |
| SUCCESS_PIN_EXPIRED | PIN ist abgelaufen und wurde nicht geändert | Die PIN des Benutzers ist abgelaufen, und er wurde zum Ändern aufgefordert, aber die Änderung der PIN war nicht erfolgreich. |
| SUCCESS_USED_CACHE | Cache wurde verwendet | Die Authentifizierung war ohne Multi-Factor Authentication-Anruf erfolgreich, weil eine vorherige erfolgreiche Authentifizierung für denselben Benutzernamen innerhalb des konfigurierten Cachezeitrahmens durchgeführt wurde. |
| SUCCESS_BYPASSED_AUTH | Authentifizierung wurde umgangen | Die Authentifizierung war erfolgreich, indem für den Benutzer eine Einmalumgehung initiiert wurde.  Ausführlichere Informationen zur Umgehung finden Sie im Bericht „Verlauf – Umgangene Benutzer“. |
| SUCCESS_USED_IP_BASED_CACHE | IP-basierter Cache wurde verwendet | Die Authentifizierung war ohne Multi-Factor Authentication-Anruf erfolgreich, weil eine vorherige erfolgreiche Authentifizierung für denselben Benutzernamen, Authentifizierungstyp, Anwendungsnamen und dieselbe IP-Adresse innerhalb des konfigurierten Cachezeitrahmens durchgeführt wurde. |
| SUCCESS_USED_APP_BASED_CACHE | App-basierter Cache wurde verwendet | Die Authentifizierung war ohne Multi-Factor Authentication-Anruf erfolgreich, weil eine vorherige erfolgreiche Authentifizierung für denselben Benutzernamen, Authentifizierungstyp und Anwendungsnamen innerhalb des konfigurierten Cachezeitrahmens durchgeführt wurde. |
| SUCCESS_INVALID_INPUT | Ungültige Telefoneingabe | Die vom Telefon gesendete Antwort ist ungültig.  Dies kann daran liegen, dass die Antwort von einem Faxgerät oder Modem stammt oder der Benutzer als Teil der PIN „*“ eingegeben hat. |
| SUCCESS_USER_BLOCKED | Benutzer ist gesperrt | Die Telefonnummer des Benutzers wird blockiert.  Eine blockierte Nummer kann vom Benutzer während eines Authentifizierungsanrufs oder von einem Administrator über das Azure-Portal initiiert werden. <br> HINWEIS:   Eine gesperrte Nummer ist auch ein Nebenprodukt einer Betrugswarnung. |
| SUCCESS_SMS_AUTHENTICATED | SMS wurde authentifiziert | Der Benutzer hat auf eine bidirektionale Testnachricht richtig mit seiner Einmalkennung (One-Time Passcode, OTP) bzw. OTP + PIN geantwortet. |
| SUCCESS_SMS_SENT | SMS wurde gesendet | Das Senden der SMS mit der Einmalkennung (One-Time Passcode, OTP) war erfolgreich.  Der Benutzer gibt die Einmalkennung bzw. OTP + PIN in die Anwendung ein, um die Authentifizierung abzuschließen. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Mobile App wurde authentifiziert | Die Authentifizierung des Benutzers über die mobile App war erfolgreich. |
| SUCCESS_OATH_CODE_PENDING | OATH-Code ausstehend | Der Benutzer wurde zum Eingeben seines OATH-Codes aufgefordert, aber er hat nicht geantwortet. |
| SUCCESS_OATH_CODE_VERIFIED | OATH-Code wurde überprüft | Der Benutzer hat nach der entsprechenden Aufforderung einen gültigen OATH-Code eingegeben. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | OATH-Fallbackcode wurde verifiziert | Für den Benutzer wurde die Authentifizierung mit seinem Hauptverfahren für Multi-Factor Authentication verweigert, und dann hat er als Fallbacklösung einen gültigen OATH-Code angegeben. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Sicherheitsfragen für Fallback wurden beantwortet | Für den Benutzer wurde die Authentifizierung mit seinem Hauptverfahren für Multi-Factor Authentication verweigert, und dann hat er die Fallback-Sicherheitsfragen richtig beantwortet. |
| FAILED_PHONE_BUSY | Authentifizierung bereits in Bearbeitung | Der Multi-Factor Authentication-Vorgang verarbeitet bereits eine Authentifizierung für diesen Benutzer.  Die Ursache hierfür sind häufig RADIUS-Clients, die während desselben Anmeldungsvorgangs mehrere Authentifizierungsanforderungen senden. |
| CONFIG_ISSUE | Telefonnummer nicht erreichbar | Es wurde versucht, den Anruf zu tätigen, aber er war nicht erfolgreich oder wurde nicht angenommen.  Hierzu gehören Fälle wie Besetztzeichen, schnell aufeinander folgendes Besetztzeichen (Verbindung getrennt), Dreifach-Ton (Nummer nicht mehr gültig), Zeitüberschreitung beim Klingeln usw. |
| FAILED_INVALID_PHONENUMBER | Ungültiges Format der Telefonnummer | Die Telefonnummer hat ein ungültiges Format.  Telefonnummern müssen numerisch sein und für den Ländercode „+1“ (USA und Kanada) zehn Stellen haben. |
| FAILED_USER_HUNGUP_ON_US | Benutzer hat aufgelegt | Der Benutzer hat den Anruf angenommen und dann aufgelegt, ohne Tasten zu drücken. |
| FAILED_INVALID_EXTENSION | Ungültige Durchwahlnummer | Die Durchwahlnummer enthält ungültige Zeichen.  Es sind nur Ziffern, Kommas, „*“ und „#“ zulässig.  Das Präfix „@“ kann auch verwendet werden. |
| FAILED_FRAUD_CODE_ENTERED | Betrugscode wurde eingegeben | Der Benutzer hat während des Anrufs einen Betrugsfall gemeldet, sodass die Authentifizierung verweigert und die Telefonnummer gesperrt wurde.| 
| FAILED_SERVER_ERROR | Anruf nicht möglich | Der Multi-Factor Authentication-Dienst konnte den Anruf nicht tätigen. |
| FAILED_SMS_NOT_SENT | SMS konnte nicht gesendet werden | Die SMS konnte nicht gesendet werden.  Die Authentifizierung wurde verweigert. |
| FAILED_SMS_OTP_INCORRECT | OTP für SMS fehlerhaft | Der Benutzer hat eine falsche Einmalkennung (One-Time Passcode, OTP) aus der empfangenen SMS eingegeben.  Die Authentifizierung wurde verweigert. |
| FAILED_SMS_OTP_PIN_INCORRECT | OTP + PIN für SMS fehlerhaft | Der Benutzer hat eine falsche Einmalkennung (One-Time Passcode, OTP) bzw. eine falsche Benutzer-PIN eingegeben.  Die Authentifizierung wurde verweigert. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Maximale Anzahl von Versuchen für SMS-OTP überschritten | Der Benutzer hat die maximale Anzahl von Versuchen zur Eingabe der Einmalkennung (One-Time Passcode, OTP) überschritten. |
| FAILED_PHONE_APP_DENIED | Mobile App verweigert | Der Benutzer hat die Authentifizierung in der mobilen App verweigert, indem er die Schaltfläche „Verweigern“ betätigt hat. |
| FAILED_PHONE_APP_INVALID_PIN | Mobile App: ungültige PIN | Der Benutzer hat bei der Authentifizierung in der mobilen App eine ungültige PIN eingegeben. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | PIN für mobile App wurde nicht geändert | Der Benutzer hat in der mobilen App eine erforderliche PIN-Änderung nicht erfolgreich abgeschlossen. |
| FAILED_FRAUD_REPORTED | Betrug wurde gemeldet | Der Benutzer hat den Betrug in der mobilen App gemeldet. |
| FAILED_PHONE_APP_NO_RESPONSE | Keine Antwort von der mobilen App | Der Benutzer hat auf die Authentifizierungsanforderung der mobilen App nicht reagiert. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Mobile App: alle Geräte gesperrt | Die Geräte vom Typ „mobile App“ für diesen Benutzer reagieren nicht mehr auf Benachrichtigungen und wurden gesperrt. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Mobile App: Fehler bei Benachrichtigung | Beim Versuch, eine Benachrichtigung an die mobile App auf dem Gerät des Benutzers zu senden, ist ein Fehler aufgetreten. |
| FAILED_PHONE_APP_INVALID_RESULT | Mobile App: ungültiges Ergebnis | Die mobile App hat ein ungültiges Ergebnis zurückgegeben. |
| FAILED_OATH_CODE_INCORRECT | Falscher OATH-Code | Der Benutzer hat einen falschen OATH-Code eingegeben.  Die Authentifizierung wurde verweigert. |
| FAILED_OATH_CODE_PIN_INCORRECT | OATH-Code + PIN fehlerhaft | Der Benutzer hat einen falschen OATH-Code bzw. eine falsche Benutzer-PIN eingegeben.  Die Authentifizierung wurde verweigert. |
| FAILED_OATH_CODE_DUPLICATE | Doppelter OATH-Code | Der Benutzer hat einen OATH-Code eingegeben, der schon einmal verwendet wurde.  Die Authentifizierung wurde verweigert. |
| FAILED_OATH_CODE_OLD | OATH-Code veraltet | Der Benutzer hat einen OATH-Code eingegeben, der älter als ein bereits verwendeter OATH-Code ist.  Die Authentifizierung wurde verweigert. |
| FAILED_OATH_TOKEN_TIMEOUT | Zeitüberschreitung für OATH-Code | Der Benutzer hat zu lange gebraucht, um den OATH-Code einzugeben, und für den Multi-Factor Authentication-Vorgang ist eine Zeitüberschreitung eingetreten. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Zeitüberschreitung für Sicherheitsfragen | Der Benutzer hat zu lange gebraucht, um die Sicherheitsfragen zu beantworten, und für den Multi-Factor Authentication-Vorgang ist eine Zeitüberschreitung eingetreten. |
| FAILED_AUTH_RESULT_TIMEOUT | Zeitüberschreitung bei Authentifizierung | Der Benutzer hat zu lange gebraucht, um den Multi-Factor Authentication-Vorgang durchzuführen. |
| FAILED_AUTHENTICATION_THROTTLED | Authentifizierung wurde gedrosselt | Der Multi-Factor Authentication-Vorgang wurde vom Dienst gedrosselt. |

## <a name="next-steps"></a>Nächste Schritte

* [Für Benutzer](../user-help/multi-factor-authentication-end-user.md)
* [Bereitstellungsort](concept-mfa-whichversion.md)
