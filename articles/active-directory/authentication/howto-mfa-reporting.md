---
title: Einzelheiten zu Anmeldeereignissen für Azure AD Multi-Factor Authentication – Azure Active Directory
description: Erfahren Sie, wie Sie Anmeldeaktivitäten für Azure AD Multi-Factor Authentication-Ereignisse und Statusmeldungen anzeigen.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/15/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5f78b70599d6d0ae8825accf4cc55cdc1c01d9ce
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861237"
---
# <a name="use-the-sign-ins-report-to-review-azure-ad-multi-factor-authentication-events"></a>Verwenden des Anmeldeberichts zum Überprüfen von Azure AD Multi-Factor Authentication-Ereignissen

Zum Überprüfen von Azure AD Multi-Factor Authentication-Ereignissen und zum besseren Verständnis können Sie den Azure Active Directory-Anmeldebericht (Azure AD) verwenden. Dieser Bericht zeigt Authentifizierungsdetails für Ereignisse, bei denen ein Benutzer zur mehrstufigen Authentifizierung aufgefordert wird. Er zeigt auch, ob Richtlinien für bedingten Zugriff verwendet wurden. Ausführliche Informationen zum Anmeldebericht finden Sie unter [Berichte zu Anmeldeaktivitäten in Azure AD](../reports-monitoring/concept-sign-ins.md).

In diesem Artikel erfahren Sie, wie Sie den Azure AD-Anmeldebericht im Azure-Portal und dann das PowerShell-Modul „MSOnline V1“ anzeigen.

## <a name="view-the-azure-ad-sign-ins-report"></a>Anzeigen des Azure AD-Anmeldeberichts

Der Anmeldebericht enthält Informationen zur Nutzung von verwalteten Anwendungen und zu Anmeldeaktivitäten von Benutzern, darunter auch Informationen zur Nutzung von Azure Multi-Factor Authentication (MFA). Die MFA-Daten liefern Ihnen Erkenntnisse zur Funktionsweise von MFA in Ihrer Organisation. Hier finden Sie Antworten auf folgende Fragen:

- Wurde bei der Anmeldung MFA verwendet?
- Wie hat der Benutzer den MFA-Vorgang durchgeführt?
- Warum konnte der Benutzer den MFA-Vorgang nicht durchführen?
- Wie viele Benutzer werden zur Durchführung des MFA-Vorgangs aufgefordert?
- Wie viele Benutzer können den MFA-Vorgang nicht durchführen?
- Welche MFA-Probleme treten für Benutzer häufig auf?

Führen Sie die folgenden Schritte aus, um den Bericht zu den Anmeldeaktivitäten im [Azure-Portal](https://portal.azure.com) anzuzeigen. Mithilfe der [Berichterstellungs-API](../reports-monitoring/concept-reporting-api.md) können Sie auch Daten abfragen.

1. Melden Sie sich mit dem Konto mit den Berechtigungen vom Typ *Globaler Administrator* beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Azure Active Directory**, wählen Sie den Eintrag aus, und wählen Sie anschließend im Menü auf der linken Seite die Option **Benutzer** aus.
1. Wählen Sie im Menü auf der linken Seite unter **Aktivität** die Option *Anmeldungen* aus.
1. Angezeigt wird eine Liste mit Anmeldeereignissen und Statusangaben. Sie können ein Ereignis auswählen, um weitere Details anzuzeigen.

    Als Ereignisdetails werden auf der Registerkarte *Authentifizierungsdetails* oder *Bedingter Zugriff* der Statuscode bzw. die Richtlinie angezeigt, durch die die Aufforderung zur mehrstufigen Authentifizierung ausgelöst wurde.

    [![Screenshot mit einem Beispiel für einen Azure Active Directory-Anmeldebericht im Azure-Portal](media/howto-mfa-reporting/sign-in-report-cropped.png)](media/howto-mfa-reporting/sign-in-report.png#lightbox)

Sofern verfügbar, wird die Art der Authentifizierung angezeigt, z. B. Textnachricht, Benachrichtigung der Microsoft Authenticator-App oder Telefonanruf.

Im Fenster *Authentifizierungsdetails* werden die folgenden Einzelheiten für ein Anmeldeereignis angezeigt, aus denen hervorgeht, ob die MFA-Anforderung erfolgreich war oder abgelehnt wurde:

* Wenn der MFA-Vorgang erfolgreich war, enthält diese Spalte weitere Details dazu.
   * completed in the cloud (in der Cloud durchgeführt)
   * has expired due to the policies configured on tenant (ist aufgrund der auf dem Mandanten konfigurierten Richtlinien abgelaufen)
   * registration prompted (zur Registrierung aufgefordert)
   * satisfied by claim in the token (per Anspruch im Token erfüllt)
   * satisfied by claim provided by external provider (per Anspruch vom externen Anbieter erfüllt)
   * satisfied by strong authentication (per strenger Authentifizierung erfüllt)
   * skipped as flow exercised was Windows broker logon flow (übersprungen, da es sich um einen Windows-Broker-Anmeldefluss gehandelt hat)
   * skipped due to app password (übersprungen aufgrund von App-Kennwort)
   * skipped due to location (übersprungen aufgrund von Standort)
   * skipped due to registered device (übersprungen aufgrund von registriertem Gerät)
   * skipped due to remembered device (übersprungen aufgrund von gespeichertem Gerät)
   * successfully completed (erfolgreich abgeschlossen)

* Wenn der MFA-Vorgang nicht erfolgreich war, ist in dieser Spalte der Grund angegeben.
   * authentication in-progress (Authentifizierung in Bearbeitung)
   * duplicate authentication attempt (versuchte doppelte Authentifizierung)
   * entered incorrect code too many times (falschen Code zu häufig eingegeben)
   * invalid authentication (ungültige Authentifizierung)
   * invalid mobile app verification code (ungültiger Verifizierungscode der mobilen App)
   * misconfiguration (Fehlkonfiguration)
   * phone call went to voicemail (Voicemail bei Telefonanruf)
   * phone number has an invalid format (Telefonnummer hat ein ungültiges Format)
   * service error (Dienstfehler)
   * unable to reach the user's phone (Telefon des Benutzers nicht erreichbar)
   * unable to send the mobile app notification to the device (Benachrichtigung über mobile App kann nicht an das Gerät gesendet werden)
   * unable to send the mobile app notification (Benachrichtigung über mobile App kann nicht gesendet werden)
   * user declined the authentication (Benutzer hat die Authentifizierung abgelehnt)
   * user did not respond to mobile app notification (Benutzer hat auf Benachrichtigung über mobile App nicht geantwortet)
   * user does not have any verification methods registered (für Benutzer sind keine Verifizierungsmethoden registriert)
   * user entered incorrect code (Benutzer hat falschen Code eingegeben)
   * user entered incorrect PIN (Benutzer hat falsche PIN eingegeben)
   * user hung up the phone call without succeeding the authentication (Benutzer hat Gespräch beendet, ohne die Authentifizierung durchzuführen)
   * user is blocked (Benutzer ist gesperrt)
   * user never entered the verification code (Benutzer hat den Verifizierungscode niemals eingegeben)
   * user not found (Benutzer wurde nicht gefunden)
   * verification code already used once (Verifizierungscode wurde bereits einmal verwendet)

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>PowerShell-Berichte über für MFA registrierte Benutzer

Stellen Sie zunächst sicher, dass das [MSOnline-V1-PowerShell-Modul](/powershell/azure/active-directory/overview) installiert ist.

Identifizieren Sie mithilfe des folgenden PowerShell-Befehls Benutzer, die sich für MFA registriert haben. Mit dieser Reihe von Befehlen werden deaktivierte Benutzer ausgeschlossen, da sich diese Konten nicht bei Azure AD authentifizieren können:

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Identifizieren Sie mithilfe des folgenden PowerShell-Befehls Benutzer, die sich nicht für MFA registriert haben. Mit dieser Reihe von Befehlen werden deaktivierte Benutzer ausgeschlossen, da sich diese Konten nicht bei Azure AD authentifizieren können:

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0 -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Identifizieren Sie die registrierten Benutzer und Ausgabemethoden:

```powershell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="downloaded-activity-reports-result-codes"></a>Ergebniscodes im heruntergeladenen Aktivitätsbericht

Mithilfe der Version des Aktivitätsberichts, die Sie in den vorherigen Schritten im Portal oder mit PowerShell-Befehlen heruntergeladen haben, und der folgenden Tabelle können Sie Probleme bei Ereignissen besser beheben. Diese Ergebniscodes werden nicht direkt im Azure-Portal angezeigt.

| Anrufergebnis | BESCHREIBUNG | Genauere Beschreibung |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN wurde eingegeben | Der Benutzer hat eine PIN eingegeben.   Wenn die Authentifizierung erfolgreich war, wurde die richtige PIN eingegeben.   Falls die Authentifizierung verweigert wird, wurde eine falsche PIN eingegeben, oder für den Benutzer ist der Modus „Standard“ festgelegt. |
| SUCCESS_NO_PIN | Nur # wurde eingegeben | Wenn der Benutzer auf den Modus „PIN“ festgelegt ist und die Authentifizierung verweigert wird, bedeutet dies Folgendes: Der Benutzer hat seine PIN nicht eingegeben, sondern nur #.  Wenn der Benutzer auf den Modus „Standard“ festgelegt ist und die Authentifizierung erfolgreich ist, bedeutet dies, dass der Benutzer nur # eingegeben hat. Dies ist im Modus „Standard“ die richtige Vorgehensweise. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # nach Eingabe nicht gedrückt | Der Benutzer hat keine DTMF-Ziffern gesendet, weil # nicht eingegeben wurde.   Andere eingegebene Ziffern werden nur gesendet, wenn # eingegeben wird, um den Abschluss der Eingabe anzugeben. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Keine Telefoneingabe: Zeitüberschreitung | Der Anruf wurde angenommen, aber es ist keine Antwort vorhanden.   Dies deutet normalerweise darauf hin, dass der Anruf per Voicemail angenommen wurde. |
| SUCCESS_PIN_EXPIRED | PIN ist abgelaufen und wurde nicht geändert | Die PIN des Benutzers ist abgelaufen, und er wurde zum Ändern aufgefordert, aber die Änderung der PIN war nicht erfolgreich. |
| SUCCESS_USED_CACHE | Cache wurde verwendet | Die Authentifizierung war ohne Multi-Factor Authentication-Anruf erfolgreich, weil eine vorherige erfolgreiche Authentifizierung für denselben Benutzernamen innerhalb des konfigurierten Cachezeitrahmens durchgeführt wurde. |
| SUCCESS_BYPASSED_AUTH | Authentifizierung wurde umgangen | Die Authentifizierung war erfolgreich, indem für den Benutzer eine Einmalumgehung initiiert wurde.  Einzelheiten zur Umgehung enthält der Bericht „Verlauf – Benutzer mit umgangener Authentifizierung“. |
| SUCCESS_USED_IP_BASED_CACHE | IP-basierter Cache wurde verwendet | Die Authentifizierung war ohne Multi-Factor Authentication-Anruf erfolgreich, weil eine vorherige erfolgreiche Authentifizierung für denselben Benutzernamen, Authentifizierungstyp, Anwendungsnamen und dieselbe IP-Adresse innerhalb des konfigurierten Cachezeitrahmens durchgeführt wurde. |
| SUCCESS_USED_APP_BASED_CACHE | App-basierter Cache wurde verwendet | Die Authentifizierung war ohne Multi-Factor Authentication-Anruf erfolgreich, weil eine vorherige erfolgreiche Authentifizierung für denselben Benutzernamen, Authentifizierungstyp und Anwendungsnamen innerhalb des konfigurierten Cachezeitrahmens durchgeführt wurde. |
| SUCCESS_INVALID_INPUT | Ungültige Telefoneingabe | Die vom Telefon gesendete Antwort ist ungültig.   Dies kann daran liegen, dass die Antwort von einem Faxgerät oder Modem stammt oder der Benutzer als Teil der PIN „*“ eingegeben hat. |
| SUCCESS_USER_BLOCKED | Benutzer ist gesperrt | Die Telefonnummer des Benutzers wird blockiert.   Eine blockierte Nummer kann vom Benutzer während eines Authentifizierungsanrufs oder von einem Administrator über das Azure-Portal initiiert werden. <br> HINWEIS:   Eine gesperrte Nummer ist auch ein Nebenprodukt einer Betrugswarnung. |
| SUCCESS_SMS_AUTHENTICATED | SMS wurde authentifiziert | Der Benutzer hat auf eine bidirektionale Testnachricht richtig mit seiner Einmalkennung (One-Time Passcode, OTP) bzw. OTP + PIN geantwortet. |
| SUCCESS_SMS_SENT | SMS wurde gesendet | Das Senden der SMS mit der Einmalkennung (One-Time Passcode, OTP) war erfolgreich.   Der Benutzer gibt die Einmalkennung bzw. OTP + PIN in die Anwendung ein, um die Authentifizierung abzuschließen. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Mobile App wurde authentifiziert | Die Authentifizierung des Benutzers über die mobile App war erfolgreich. |
| SUCCESS_OATH_CODE_PENDING | OATH-Code ausstehend | Der Benutzer wurde zum Eingeben seines OATH-Codes aufgefordert, aber er hat nicht geantwortet. |
| SUCCESS_OATH_CODE_VERIFIED | OATH-Code wurde überprüft | Der Benutzer hat nach der entsprechenden Aufforderung einen gültigen OATH-Code eingegeben. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | OATH-Fallbackcode wurde verifiziert | Für den Benutzer wurde die Authentifizierung mit seinem Hauptverfahren für Multi-Factor Authentication verweigert, und dann hat er als Fallbacklösung einen gültigen OATH-Code angegeben. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Sicherheitsfragen für Fallback wurden beantwortet | Für den Benutzer wurde die Authentifizierung mit seinem Hauptverfahren für Multi-Factor Authentication verweigert, und dann hat er die Fallback-Sicherheitsfragen richtig beantwortet. |
| FAILED_PHONE_BUSY | Authentifizierung bereits in Bearbeitung | Der Multi-Factor Authentication-Vorgang verarbeitet bereits eine Authentifizierung für diesen Benutzer.   Die Ursache hierfür sind häufig RADIUS-Clients, die während desselben Anmeldungsvorgangs mehrere Authentifizierungsanforderungen senden. |
| CONFIG_ISSUE | Telefonnummer nicht erreichbar | Es wurde versucht, den Anruf zu tätigen, aber er war nicht erfolgreich oder wurde nicht angenommen.   Hierzu gehören Fälle wie Besetztzeichen, schnell aufeinander folgendes Besetztzeichen (Verbindung getrennt), Dreifach-Ton (Nummer nicht mehr gültig), Zeitüberschreitung beim Klingeln usw. |
| FAILED_INVALID_PHONENUMBER | Ungültiges Format der Telefonnummer | Die Telefonnummer hat ein ungültiges Format.   Telefonnummern müssen numerisch sein und für den Ländercode „+1“ (USA und Kanada) zehn Stellen haben. |
| FAILED_USER_HUNGUP_ON_US | Benutzer hat aufgelegt | Der Benutzer hat den Anruf angenommen und dann aufgelegt, ohne Tasten zu drücken. |
| FAILED_INVALID_EXTENSION | Ungültige Durchwahlnummer | Die Durchwahlnummer enthält ungültige Zeichen.   Es sind nur Ziffern, Kommas, „*“ und „#“ zulässig.   Das Präfix „@“ kann auch verwendet werden. |
| FAILED_FRAUD_CODE_ENTERED | Betrugscode wurde eingegeben | Der Benutzer hat während des Anrufs einen Betrugsfall gemeldet, sodass die Authentifizierung verweigert und die Telefonnummer gesperrt wurde.| 
| FAILED_SERVER_ERROR | Anruf nicht möglich | Der Multi-Factor Authentication-Dienst konnte den Anruf nicht tätigen. |
| FAILED_SMS_NOT_SENT | SMS konnte nicht gesendet werden | Die SMS konnte nicht gesendet werden.   Die Authentifizierung wurde verweigert. |
| FAILED_SMS_OTP_INCORRECT | OTP für SMS fehlerhaft | Der Benutzer hat eine falsche Einmalkennung (One-Time Passcode, OTP) aus der empfangenen SMS eingegeben.   Die Authentifizierung wurde verweigert. |
| FAILED_SMS_OTP_PIN_INCORRECT | OTP + PIN für SMS fehlerhaft | Der Benutzer hat eine falsche Einmalkennung (One-Time Passcode, OTP) bzw. eine falsche Benutzer-PIN eingegeben.   Die Authentifizierung wurde verweigert. |
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

## <a name="additional-mfa-reports"></a>Weitere MFA-Berichte

Für Ereignisse der mehrstufigen Authentifizierung (und für den MFA-Server) sind die folgenden weiteren Informationen und Berichte verfügbar:

| Bericht | Standort | BESCHREIBUNG |
|:--- |:--- |:--- |
| Verlauf – gesperrte Benutzer | Azure AD > Sicherheit > MFA > Benutzer sperren/entsperren | Zeigt die Liste der Anforderungen zum Blockieren und Entsperren von Benutzern an. |
| Nutzung für lokale Komponenten | Azure AD > Sicherheit > MFA > Aktivitätsbericht | Enthält Informationen zur allgemeinen Nutzung für MFA-Server durch die NPS-Erweiterung, AD FS und den MFA-Server. |
| Verlauf – Umgangene Benutzer | Azure AD > Sicherheit > MFA > Einmalige Umgehung | Stellt den Verlauf der MFA-Serveranforderungen zum Umgehen der MFA für einen Benutzer bereit. |
| Serverstatus | Azure AD > Sicherheit > MFA > Serverstatus | Zeigt den Status der MFA-Server an, die mit Ihrem Konto verknüpft sind. |

## <a name="next-steps"></a>Nächste Schritte

Dieser Artikel enthält eine Übersicht über den Bericht zu Anmeldeaktivitäten. Detailliertere Informationen zum Inhalt dieses Berichts und zu den Daten finden Sie unter [Berichte zu Anmeldeaktivitäten in Azure AD](../reports-monitoring/concept-sign-ins.md).
