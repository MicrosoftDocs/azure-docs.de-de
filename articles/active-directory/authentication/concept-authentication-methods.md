---
title: Authentifizierungsmethoden – -Azure Active Directory
description: Übersicht über die verfügbaren Authentifizierungsmethoden in Azure AD für die Multi-Factor Authentication und die Self-Service-Kennwortzurücksetzung
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0bcaf356108984baf473cdef8c18c5561343cd9
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119364"
---
# <a name="what-are-authentication-methods"></a>Authentifizierungsmethoden

Wenn Sie als Administrator Authentifizierungsmethoden für Azure Multi-Factor Authentication und für die Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) auswählen, empfiehlt es sich, Benutzer zur Registrierung mehrerer Authentifizierungsmethoden zu zwingen. Wenn eine Authentifizierungsmethode für einen Benutzer nicht verfügbar ist, kann er sich mit einer anderen Methode authentifizieren.

Administratoren können in einer Richtlinie definieren, welche Authentifizierungsmethoden Benutzern von SSPR und MFA zur Verfügung stehen. Einige Authentifizierungsmethoden sind nicht in allen Funktionen verfügbar. Weitere Informationen zum Konfigurieren von Richtlinien finden Sie in den Artikeln [Erfolgreicher Rollout der Self-Service-Kennwortzurücksetzung](howto-sspr-deployment.md) und [Bereitstellen von cloudbasierter Azure Multi-Factor Authentication](howto-mfa-getstarted.md).

Administratoren wird empfohlen, Benutzern mehr als die erforderliche Mindestanzahl von Authentifizierungsmethoden zur Verfügung zu stellen, für den Fall, dass sie auf eine Methode nicht zugreifen können.

|Authentifizierungsmethode|Verwendung|
| --- | --- |
| Kennwort | MFA und SSPR |
| Sicherheitsfragen | Nur SSPR |
| E-Mail-Adresse | Nur SSPR |
| Microsoft Authenticator-App | MFA und Public Preview für SSPR |
| OATH-Hardwaretoken | Public Preview für MFA und SSPR |
| sms | MFA und SSPR |
| Anruf | MFA und SSPR |
| App-Kennwörter | MFA nur in bestimmten Fällen |

![Verwendete Authentifizierungsmethoden auf dem Anmeldebildschirm](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| OATH-Hardwaretoken für MFA und SSPR sowie das Empfangen einer Benachrichtigung in der mobilen App und das Erhalten eines Codes in der mobilen App sind Methoden für die Self-Service-Kennwortzurücksetzung in Azure AD und als öffentliche Previewfunktion von Azure Active Directory verfügbar. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="password"></a>Kennwort

Ihr Azure AD-Kennwort gilt als eine Authentifizierungsmethode. Diese Methode **kann nicht deaktiviert werden**.

## <a name="security-questions"></a>Sicherheitsfragen

Sicherheitsfragen stehen **nur in der Self-Service-Kennwortzurücksetzung in Azure AD** für Nichtadministratorkonten zur Verfügung.

Wenn Sie Sicherheitsfragen verwenden, wird empfohlen, diese in Verbindung mit einer anderen Methode zu verwenden. Sicherheitsfragen können weniger sicher als andere Methoden sein, da einige Personen unter Umständen die Antworten auf die Fragen eines anderen Benutzers kennen.

> [!NOTE]
> Sicherheitsfragen werden privat und sicher in einem Benutzerobjekt im Verzeichnis gespeichert und können nur während der Registrierung von Benutzern beantwortet werden. Es gibt keine Möglichkeit für einen Administrator, die Fragen oder Antworten eines Benutzers zu lesen oder zu ändern.
>

### <a name="predefined-questions"></a>Vordefinierte Fragen

* In welcher Stadt haben Sie Ihren ersten Partner bzw. Ihre erste Partnerin kennengelernt?
* In welcher Stadt haben sich Ihre Eltern kennengelernt?
* In welcher Stadt lebt Ihr Bruder/Ihre Schwester?
* Wie heißt die Geburtsstadt Ihres Vaters?
* In welcher Stadt haben Sie Ihre erste Arbeitsstelle angetreten?
* Wie heißt die Geburtsstadt Ihrer Mutter?
* In welcher Stadt haben Sie den Neujahrstag 2000 verbracht?
* Wie hieß Ihr Lieblingslehrer bzw. Ihre Lieblingslehrerin in der Grundschule mit Nachnamen?
* Bei welcher Universität haben Sie sich beworben, dort jedoch nicht studiert?
* An welchem Ort fand Ihre Hochzeitsfeier statt?
* Wie lautet der 2. Vorname Ihres Vaters?
* Was ist Ihr Lieblingsgericht?
* Wie lautet der Vor- und Nachname Ihrer Großmutter mütterlicherseits?
* Wie lautet der 2. Vorname Ihrer Mutter?
* In welchem Monat und Jahr ist Ihre älteste Schwester bzw. Ihr ältester Bruder geboren? (z.B. November 1985)
* Wie lautet der 2. Vorname Ihrer ältesten Schwester bzw. Ihres ältesten Bruders?
* Wie lautet der Vor- und Nachname Ihres Großvaters väterlicherseits?
* Wie lautet der 2. Vorname Ihrer jüngsten Schwester bzw. Ihres jüngsten Bruders?
* Auf welche Schule sind Sie in der sechsten Klasse gegangen?
* Wie lautet der Vor- und Nachname Ihres besten Freundes bzw. Ihrer besten Freundin in Ihrer Kindheit?
* Wie lautet der Vor- und Nachname Ihres ersten Partners bzw. Ihrer ersten Partnerin?
* Wie lautete der Nachname des Lehrers bzw. der Lehrerin Ihres Lieblingsfachs?
* Welche Marke und welches Modell hatte Ihr erstes Auto oder Motorrad?
* Wie heißt die erste Schule, die Sie besucht haben?
* In welchem Krankenhaus wurden Sie geboren?
* In welcher Straße haben Sie als Kind zuerst gewohnt?
* Wer war der Held Ihrer Kindheit?
* Wie hieß Ihr Lieblingskuscheltier?
* Wie hieß Ihr erstes Haustier?
* Welchen Spitznamen hatten Sie als Kind?
* Was war in der Schulzeit Ihre Lieblingssportart?
* Was war Ihr erster Job?
* Wie lauten die letzten vier Ziffern der Telefonnummer in Ihrer Kindheit?
* Was wollten Sie als Kind später einmal werden?
* Welches ist die berühmteste Person, die Sie kennen gelernt haben?

Alle vordefinierten Sicherheitsfragen werden in alle Office 365-Sprachen lokalisiert, basierend auf dem Gebietsschema des Browsers des Benutzers.

### <a name="custom-security-questions"></a>Benutzerdefinierte Sicherheitsfragen

Benutzerdefinierte Sicherheitsfragen werden nicht lokalisiert. Alle benutzerdefinierten Fragen werden auch bei abweichendem Gebietsschema im Browser in der Sprache angezeigt, in der sie in der administrativen Benutzeroberfläche eingegeben wurden. Wenn Sie lokalisierte Fragen benötigen, verwenden Sie die vordefinierten Fragen.

Die Länge von benutzerdefinierten Sicherheitsfragen ist auf 200 Zeichen begrenzt.

### <a name="security-question-requirements"></a>Anforderungen an Sicherheitsfragen

* Die Mindestanzahl von Zeichen für Antworten ist drei Zeichen.
* Die maximale Zeichenanzahl für Antworten ist 40 Zeichen.
* Benutzer können dieselbe Frage nicht mehrmals beantworten.
* Benutzer können nicht dieselbe Antwort für mehrere Fragen angeben.
* Bei der Definition von Fragen und Antworten kann ein beliebiger Zeichensatz verwendet werden (einschließlich Unicode).
* Die Anzahl der definierten Fragen muss größer als oder gleich der Anzahl der für die Registrierung erforderlichen Fragen sein.

## <a name="email-address"></a>E-Mail-Adresse

Die E-Mail-Adresse steht **nur in der Self-Service-Kennwortzurücksetzung in Azure AD** zur Verfügung.

Es wird empfohlen, ein E-Mail-Konto zu verwenden, für dessen Zugriff das Azure AD-Benutzerkennwort nicht erforderlich ist.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator-App

Die Microsoft Authenticator-App bietet eine zusätzliche Sicherheitsstufe für Ihr Geschäfts-, Schul-, Uni- oder Microsoft-Konto.

Die Microsoft Authenticator-App ist für [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) und [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071) verfügbar.

> [!NOTE]
> Benutzer können ihre mobile App nicht registrieren, wenn sie sich für die Self-Service-Kennwortzurücksetzung registrieren. Stattdessen haben sie die Möglichkeit, ihre mobile App unter [https://aka.ms/mfasetup](https://aka.ms/mfasetup) oder in den Sicherheitsinformationen in der Registrierungsvorschau unter [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) zu registrieren.
>

### <a name="notification-through-mobile-app"></a>Benachrichtigung über mobile App

Die Microsoft Authenticator-App kann dazu beitragen, nicht autorisierten Zugriff auf Konten zu verhindern und betrügerische Transaktionen zu stoppen, indem sie eine Benachrichtigung an Ihr Smartphone oder Tablett sendet. Überprüfen Sie die Benachrichtigung, und wählen Sie „Bestätigen“ aus, um den Zugriff zuzulassen. Wählen Sie andernfalls „Verweigern“ aus.

> [!WARNING]
> Wenn bei der Self-Service-Kennwortzurücksetzung nur eine Methode erforderlich ist, steht Benutzern nur der Prüfcode zur Verfügung, **um ein Höchstmaß an Sicherheit zu gewährleisten**.
>
> Wenn zwei Methoden erforderlich sind, können Benutzer zum Zurücksetzen **ENTWEDER** die Option „Benachrichtigung“ **ODER** die Option „Prüfcode“ verwenden – zusätzlich zu anderen aktivierten Methoden.
>

Wenn Sie das Empfangen einer Benachrichtigung sowie das Erhalten eines Prüfcodes in der mobilen App aktivieren, können Benutzer, die die Microsoft Authenticator-App mithilfe einer Benachrichtigung registrieren, sowohl die Benachrichtigung als auch den Code zum Bestätigen ihrer Identität verwenden.

> [!NOTE]
> Wenn es in Ihrer Organisation Mitarbeiter gibt, die in China arbeiten oder nach China reisen, müssen Sie beachten, dass die Methode **Benachrichtigung über mobile App** auf **Android-Geräten** in diesem Land nicht funktioniert. Daher sollten diesen Benutzern alternative Methoden zur Verfügung gestellt werden.

### <a name="verification-code-from-mobile-app"></a>Überprüfungscode von der mobilen App

Die Microsoft Authenticator-App und andere Drittanbieter-Apps können als Softwaretoken zum Generieren eines OATH-Prüfcodes verwendet werden. Nachdem Sie Benutzernamen und Kennwort eingegeben haben, geben Sie auf dem Anmeldebildschirm den in der App generierten Code ein. Der Überprüfungscode kann als zweite Authentifizierungsmethode eingegeben werden.

> [!WARNING]
> Wenn bei der Self-Service-Kennwortzurücksetzung nur eine Methode erforderlich ist, steht Benutzern nur der Prüfcode zur Verfügung, **um ein Höchstmaß an Sicherheit zu gewährleisten**.
>

Benutzer verfügen möglicherweise über eine Kombination aus bis zu fünf OATH-Hardwaretoken oder Authenticator-Anwendungen wie die Microsoft Authenticator-App, die für die jederzeitige Verwendung konfiguriert sind.

## <a name="oath-hardware-tokens-public-preview"></a>OATH-Hardwaretoken (Öffentliche Vorschau)

OATH ist ein offener Standard, der angibt, wie Einmalkennwortcodes (OTP) generiert werden. Azure AD unterstützt die Verwendung von OATH-TOTP SHA-1-Token der Varianten 30 Sekunden oder 60 Sekunden. Kunden können sich diese Token vom Hersteller ihrer Wahl beschaffen. Geheime Schlüssel sind auf 128 Zeichen beschränkt, was möglicherweise nicht mit allen Token kompatibel ist.

![Hochladen von OATH-Token in das OATH-Token-Blatt des MFA-Servers](media/concept-authentication-methods/oath-tokens-azure-ad.png)

OATH-Hardware-Token werden als Teil der öffentlichen Vorschau unterstützt. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Sobald Sie Token erworben haben, müssen Sie sie in einem Format mit kommagetrennten Werten (CSV) einschließlich UPN, Seriennummer, geheimer Schlüssel, Zeitintervall, Hersteller und Modell hochladen, wie das folgende Beispiel zeigt.

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567890abcdef1234567890abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Stellen Sie sicher, dass Sie die Kopfzeile in Ihre CSV-Datei wie oben gezeigt einschließen.

Einmal ordnungsgemäß als CSV-Datei formatiert, kann sich ein Administrator dann beim Azure-Portal anmelden und zu **Azure Active Directory**, **MFA-Server**, **OATH-Token** navigieren und die resultierende CSV-Datei hochladen.

Je nach Größe der CSV-Datei kann die Verarbeitung ein paar Minuten dauern. Klicken Sie auf die Schaltfläche **Aktualisieren**, um den aktuellen Status abzurufen. Wenn in der Datei Fehler vorhanden sind, haben Sie die Option, eine CSV-Datei herunterzuladen, in der alle zu behebenden Fehler für Sie aufgeführt sind.

Sobald alle Fehler behoben sind, kann der Administrator jeden Schlüssel aktivieren, indem er für das zu aktivierende Token auf **Aktivieren** klickt und das in dem Token angezeigte OTP eingibt.

Benutzer verfügen möglicherweise über eine Kombination aus bis zu fünf OATH-Hardwaretoken oder Authenticator-Anwendungen wie die Microsoft Authenticator-App, die für die jederzeitige Verwendung konfiguriert sind.

## <a name="mobile-phone"></a>Mobiltelefon

Mobiltelefonbenutzern stehen zwei Optionen zur Verfügung.

Wenn ein Benutzer nicht möchte, dass seine Mobiltelefonnummer im Verzeichnis sichtbar ist, sie aber dennoch für die Kennwortzurücksetzung verwenden möchte, sollten Administratoren die Daten nicht in das Verzeichnis übernehmen. Der Benutzer sollte sein Attribut **Authentifizierungstelefon** über das [Registrierungsportal für die Kennwortzurücksetzung](https://aka.ms/ssprsetup) auffüllen. Administratoren können diese Informationen im Profil des Benutzers anzeigen, sie werden jedoch nicht an anderer Stelle veröffentlicht.

Für ein ordnungsgemäßes Funktionieren müssen Telefonnummern im Format *+Landesvorwahl Telefonnummer* vorliegen (Beispiel: +1 4255551234).

> [!NOTE]
> Zwischen Landesvorwahl und Telefonnummer muss sich ein Leerzeichen befinden.
>
> Für die Kennwortzurücksetzung werden Nebenstellen nicht unterstützt. Selbst bei der Angabe im Format +1 4255551234X12345 werden Nebenstellen vor dem Anruf entfernt.

### <a name="text-message"></a>Textnachricht

Eine SMS mit einem Prüfcode wird an die Mobiltelefonnummer gesendet. Geben Sie den dort angezeigten Prüfcode ein, um den Vorgang fortzusetzen.

### <a name="phone-call"></a>Telefonanruf

Sie erhalten einen automatisierten Anruf unter der von Ihnen angegebenen Telefonnummer. Nehmen Sie den Anruf an, und drücken Sie die #-TASTE auf der Telefontastatur, um sich zu authentifizieren.

> [!IMPORTANT]
> Ab März 2019 werden die Telefonanrufoptionen für MFA- und der SSPR-Benutzer in kostenlosen bzw. Testversion von Azure AD-Mandanten nicht mehr verfügbar sein. SMS-Nachrichten sind von dieser Änderung nicht betroffen. Für Benutzer in kostenpflichtigen Azure AD-Mandanten ist die Telefonanrufoption weiterhin verfügbar. Diese Änderung wirkt sich nur auf kostenlose bzw. Testversionen von Azure AD-Mandanten aus.

## <a name="office-phone"></a>Bürotelefon

Sie erhalten einen automatisierten Anruf unter der von Ihnen angegebenen Telefonnummer. Nehmen Sie den Anruf an, und drücken Sie die #-TASTE auf der Telefontastatur, um sich zu authentifizieren.

Für ein ordnungsgemäßes Funktionieren müssen Telefonnummern im Format *+Landesvorwahl Telefonnummer* vorliegen (Beispiel: +1 4255551234).

Das Attribut „Bürotelefon“ wird von Ihrem Administrator verwaltet.

> [!IMPORTANT]
> Ab März 2019 werden die Telefonanrufoptionen für MFA- und der SSPR-Benutzer in kostenlosen bzw. Testversionen von Azure AD-Mandanten nicht mehr verfügbar sein. SMS-Nachrichten sind von dieser Änderung nicht betroffen. Für Benutzer in kostenpflichtigen Azure AD-Mandanten ist die Telefonanrufoption weiterhin verfügbar. Diese Änderung wirkt sich nur auf kostenlose bzw. Testversionen von Azure AD-Mandanten aus.

> [!NOTE]
> Zwischen Landesvorwahl und Telefonnummer muss sich ein Leerzeichen befinden.
>
> Für die Kennwortzurücksetzung werden Nebenstellen nicht unterstützt. Selbst bei der Angabe im Format +1 4255551234X12345 werden Nebenstellen vor dem Anruf entfernt.

## <a name="app-passwords"></a>App-Kennwörter

Bestimmte nicht browserbasierte Apps unterstützen nicht die Multi-Factor Authentication, wenn ein Benutzer für die Multi-Factor Authentication aktiviert wurde und versucht, nicht browserbasierte Apps zu verwenden, kann er sich nicht authentifizieren. Mit einem App-Kennwort können Benutzer den Authentifizierungsvorgang fortsetzen.

Wenn Sie die Multi-Factor Authentication über Richtlinien zum bedingten Zugriff und nicht über die MFA pro Benutzer erzwingen, können Sie keine Anwendungskennwörter erstellen. Anwendungen, die Richtlinien zum bedingten Zugriff für die Zugriffskontrolle verwenden, benötigen keine Anwendungskennwörter.

Wenn für Ihr Unternehmen ein SSO-Verbund mit Azure AD konfiguriert ist und Sie Azure MFA verwenden möchten, sollten Sie folgende Details berücksichtigen:

* App-Kennwörter werden von Azure AD überprüft, sodass der Verbund umgangen wird. Der Verbund wird nur beim Einrichten von App-Kennwörtern verwendet. Die Kennwörter der SSO-Verbundbenutzer werden in der Organisations-ID gespeichert. Wenn ein Benutzer das Unternehmen verlässt, muss diese Information mit DirSync der Organisations-ID hinzugefügt werden. Nach dem Deaktivieren oder Löschen von Konten kann die Synchronisierung bis zu drei Stunden dauern, sodass sich das Deaktivieren bzw. Löschen von App-Kennwörtern in Azure AD verzögert.
* Lokale Einstellungen für die Clientzugriffssteuerung werden vom App-Kennwort nicht berücksichtigt.
* Für App-Kennwörter ist keine lokale Funktion zur Protokollierung oder Überwachung der Authentifizierung verfügbar.
* In bestimmten erweiterten Architekturentwürfen ist bei der Verwendung der zweistufigen Überprüfung mit Clients möglicherweise eine Kombination aus Organisationsbenutzername/-kennwort sowie App-Kennwörtern erforderlich, je nachdem, wo die Authentifizierung stattfindet. Bei Clients, die sich bei einer lokalen Infrastruktur authentifizieren, verwenden Sie einen Organisationsbenutzernamen und ein Organisationskennwort. Für Clients, die bei Azure AD authentifizieren, verwenden Sie das App-Kennwort.
* Standardmäßig können keine Benutzer App-Kennwörter erstellen. Wenn Sie Benutzern erlauben müssen, App-Kennwörter zu erstellen, wählen Sie in den Einstellungen die Option **Benutzern das Erstellen von App-Kennwörtern zum Anmelden bei Anwendungen gestatten, die nicht auf Browsern basieren** aus.

## <a name="next-steps"></a>Nächste Schritte

[Aktivieren der Self-Service-Kennwortzurücksetzung für Ihre Organisation](quickstart-sspr.md)

[Aktivieren von Azure Multi-Factor Authentication für Ihre Organisation](howto-mfa-getstarted.md)

[Aktivieren der kombinierten Registrierung in Ihrem Mandanten](howto-registration-mfa-sspr-combined.md)

[Dokumentation zur Konfiguration der Methode für die Authentifizierung von Endbenutzern](https://aka.ms/securityinfoguide)
