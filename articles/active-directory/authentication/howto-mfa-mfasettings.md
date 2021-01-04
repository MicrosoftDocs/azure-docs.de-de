---
title: Konfigurieren von Azure AD Multi-Factor Authentication – Azure Active Directory
description: Hier erfahren Sie, wie Sie Einstellungen für Azure AD Multi-Factor Authentication im Azure-Portal konfigurieren.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: 209125a5cc8d86c1af07966d52681aa91ea7dc19
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97026998"
---
# <a name="configure-azure-ad-multi-factor-authentication-settings"></a>Konfigurieren von Azure AD Multi-Factor Authentication-Einstellungen

Zum Anpassen der Endbenutzerumgebung für Azure AD Multi-Factor Authentication können Sie Optionen für Einstellungen wie die Schwellenwerte für die Kontosperrung oder Betrugswarnungen und -benachrichtigungen konfigurieren. Einige Einstellungen befinden sich direkt im Azure-Portal für Azure Active Directory (Azure AD), während sich einige andere Einstellungen in einem separaten Portal für Azure AD Multi-Factor Authentication befinden.

Die folgenden Azure AD Multi-Factor Authentication-Einstellungen sind im Azure-Portal verfügbar:

| Funktion | BESCHREIBUNG |
| ------- | ----------- |
| [Kontosperrung](#account-lockout) | Hiermit wird bei Konten die Verwendung von Azure AD Multi-Factor Authentication vorübergehend gesperrt, wenn zu viele aufeinanderfolgende Authentifizierungsversuche abgelehnt wurden. Dieses Feature wird nur auf Benutzer angewendet, die zur Authentifizierung eine PIN eingeben. (MFA-Server) |
| [Benutzer sperren/zulassen](#block-and-unblock-users) | Hiermit werden bestimmte Benutzer daran gehindert, Azure AD Multi-Factor Authentication-Anforderungen zu empfangen. Authentifizierungsversuche für gesperrte Benutzer werden automatisch abgelehnt. Benutzer bleiben ab dem Zeitpunkt der Sperrung 90 Tage lang gesperrt, es sei denn, sie werden manuell entsperrt. |
| [Betrugswarnung](#fraud-alert) | Hiermit können Einstellungen konfiguriert werden, die es Benutzern ermöglichen, betrügerische Identitätsprüfungsanforderungen zu melden. |
| [Benachrichtigungen](#notifications) | Aktivieren Sie Benachrichtigungen für Ereignisse vom MFA-Server. |
| [OATH-Token](concept-authentication-oath-tokens.md) | Wird in cloudbasierten Azure AD Multi-Factor Authentication-Umgebungen (Azure AD MFA) verwendet, um OATH-Token für Benutzer zu verwalten. |
| [Einstellungen für Telefonanruf](#phone-call-settings) | Konfigurieren Sie Einstellungen für Telefonanrufe und Ansagen für Cloud- und lokale Umgebungen. |
| Anbieter | Hier werden alle vorhandenen Authentifizierungsanbieter angezeigt, die Sie möglicherweise mit Ihrem Konto verknüpft haben. Neue Authentifizierungsanbieter können ab 1. September 2018 nicht mehr erstellt werden. |

![Azure-Portal – Einstellungen für Azure AD Multi-Factor Authentication](./media/howto-mfa-mfasettings/multi-factor-authentication-settings-portal.png)

## <a name="account-lockout"></a>Kontosperrung

Mithilfe der Kontosperreinstellungen können Sie festlegen, wie viele fehlgeschlagene Versuche zugelassen werden, bevor das Konto vorübergehend gesperrt wird, um wiederholte MFA-Versuche im Rahmen eines Angriffs zu verhindern. Die Kontosperreinstellungen werden nur angewendet, wenn ein PIN-Code für die MFA-Eingabeaufforderung eingegeben wird.

Die folgenden Einstellungen sind verfügbar:

* Anzahl von MFA-Verweigerungen zum Auslösen einer Kontosperrung
* Zeitraum in Minuten, bis der Zähler für die Kontosperrung zurückgesetzt wird
* Zeitraum in Minuten, bis das Konto automatisch entsperrt wird

Nehmen Sie die Einstellungen vor, um die Kontosperreinstellungen zu konfigurieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Administrator an.
1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **MFA** > **Kontosperrung**.
1. Geben Sie die erforderlichen Werte für Ihre Umgebung ein, und klicken Sie dann auf **Speichern**.

    ![Screenshot: Kontosperreinstellungen im Azure-Portal](./media/howto-mfa-mfasettings/account-lockout-settings.png)

## <a name="block-and-unblock-users"></a>Benutzer sperren/zulassen

Wenn das Gerät eines Benutzers verloren geht oder gestohlen wird, können Sie Azure AD MFA-Versuche für das zugeordnete Konto blockieren. Sämtliche Azure AD MFA-Versuche für gesperrte Benutzer werden automatisch abgelehnt. Benutzer bleiben ab dem Zeitpunkt der Sperrung 90 Tage lang gesperrt.

### <a name="block-a-user"></a>Sperren eines Benutzers

Führen Sie die folgenden Schritte aus, um einen Benutzer zu sperren:

1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **MFA** > **Benutzer sperren/zulassen**.
1. Wählen Sie **Hinzufügen** aus, um einen Benutzer zu sperren.
1. Wählen Sie die **Replikationsgruppe** aus, und wählen Sie dann die Option *Azure Standard* aus.

    Geben Sie den Benutzernamen des zu sperrenden Benutzers im Format `username\@domain.com` ein, und geben Sie dann einen Kommentar im Feld *Grund* ein.
1. Wenn Sie fertig sind, klicken Sie auf **OK**, um den Benutzer zu sperren.

### <a name="unblock-a-user"></a>Zulassen eines Benutzers

Führen Sie zum Entsperren eines Benutzers die folgenden Schritte aus:

1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **MFA** > **Benutzer sperren/zulassen**.
1. Klicken Sie in der Spalte *Aktion* neben dem gewünschten Benutzer auf **Entsperren**.
1. Geben Sie im Feld *Grund für Entsperren* einen Kommentar ein.
1. Wenn Sie fertig sind, klicken Sie auf **OK**, um den Benutzer zu entsperren.

## <a name="fraud-alert"></a>Betrugswarnung

Mithilfe des Features für Betrugswarnungen können Benutzer betrügerische Versuche für den Zugriff auf ihre Ressourcen melden. Wenn eine unbekannte und verdächtige MFA-Eingabeaufforderung eingeht, können Benutzer den betrügerischen Versuch mit der Microsoft Authenticator-App oder über ihr Smartphone melden.

Die folgenden Konfigurationsoptionen stehen für Warnungen zur Verfügung:

* **Benutzer, die Betrugsversuche melden, automatisch blockieren:** Wenn ein Benutzer einen Betrug meldet, werden Azure AD MFA-Versuche für das Benutzerkonto 90 Tage lang oder so lange blockiert, bis ein Administrator die Sperre für das Konto aufhebt. Ein Administrator kann anhand des Anmeldeberichts Anmeldungen überprüfen und entsprechende Maßnahmen ergreifen, um weiterem Betrug vorzubeugen. Ein Administrator kann dann die Sperre für das Konto des Benutzers [aufheben](#unblock-a-user).
* **Code zum Melden von Betrugsversuchen während der Begrüßung**: Wenn Benutzer einen Telefonanruf zur Ausführung der mehrstufigen Authentifizierung empfangen, drücken sie normalerweise die **#** -Taste, um ihre Anmeldung zu bestätigen. Wenn sie einen Betrug melden möchten, geben sie vor dem Drücken der **#** -Taste einen Code ein. Dieser Code ist standardmäßig **0**, Sie können ihn jedoch anpassen.

   > [!NOTE]
   > In der Standardansage von Microsoft wird der Benutzer aufgefordert, zum Senden einer Betrugswarnung die Zeichenfolge **0#** einzugeben. Wenn Sie einen anderen Code als **0** verwenden möchten, sollten Sie eine benutzerdefinierte Ansage mit den passenden Anweisungen für Ihre Benutzer aufnehmen und hochladen.

Führen Sie die folgenden Schritte aus, um Betrugswarnungen zu aktivieren und zu konfigurieren:

1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **MFA** > **Betrugswarnung**.
1. Legen Sie die Einstellung *Benutzern die Ausgabe von Betrugswarnungen erlauben* auf **Ein** fest.
1. Konfigurieren Sie die Einstellungen *Benutzer, die Betrugsversuche melden, automatisch blockieren* oder *Code zum Melden von Betrugsversuchen während der Begrüßung* nach Bedarf.
1. Wählen Sie **Speichern** aus, wenn Sie so weit sind.

### <a name="view-fraud-reports"></a>Anzeigen von Betrugsberichten

Klicken Sie auf **Azure Active Directory** > **Anmeldungen** > **Details zur Authentifizierung**. Der Betrugsbericht ist nun Bestandteil des Azure AD-Standardanmeldungsberichts und wird unter **Ergebnisdetails** angezeigt, da die mehrstufige Authentifizierung verweigert und Betrugscode eingegeben wurde.
 
## <a name="notifications"></a>Benachrichtigungen

E-Mail-Benachrichtigungen können dafür konfiguriert werden, wenn Benutzer Betrugswarnungen melden. Diese Benachrichtigungen werden in der Regel an Identitätsadministratoren gesendet, da die Anmeldeinformationen für das Benutzerkonto wahrscheinlich kompromittiert sind. Im folgenden Beispiel wird gezeigt, wie eine E-Mail-Benachrichtigung für Betrugswarnungen aussieht:

![Beispiel für eine Benachrichtigungs-E-Mail für eine Betrugswarnung](./media/howto-mfa-mfasettings/multi-factor-authentication-fraud-alert-email.png)

Nehmen Sie die folgenden Einstellungen vor, um Benachrichtigungen für Betrugswarnungen zu konfigurieren:

1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Multi-Factor Authentication** > **Benachrichtigungen**.
1. Geben Sie die E-Mail-Adresse ein, die zum nächsten Feld hinzugefügt werden soll.
1. Zum Entfernen einer vorhandenen E-Mail-Adresse klicken Sie auf die Option **...** neben der E-Mail-Adresse, und wählen Sie dann **Löschen** aus.
1. Wählen Sie **Speichern** aus, wenn Sie so weit sind.

## <a name="oath-tokens"></a>OATH-Token

Azure AD unterstützt die Verwendung von OATH TOTP SHA-1-Token, die den Code alle 30 oder 60 Sekunden aktualisieren. Kunden können diese Token beim Anbieter ihrer Wahl erwerben.

OATH TOTP-Hardwaretoken sind in der Regel mit einem geheimen, im Token vorprogrammierten Schlüssel oder Anfangswert versehen. Diese Schlüssel müssen entsprechend der Beschreibung in den folgenden Schritten in Azure AD eingegeben werden. Geheime Schlüssel sind auf 128 Zeichen beschränkt, was möglicherweise nicht mit allen Token kompatibel ist. Der geheime Schlüssel darf nur die Zeichen *a-z* oder *A-Z* und die Ziffern *1-7* enthalten und muss in *Base32* codiert sein.

Programmierbare OATH TOTP-Hardwaretoken, die mit einem neuen Ausgangswert versehen werden können, können ebenfalls im Softwaretoken-Setupflow mit Azure AD eingerichtet werden.

OATH-Hardwaretoken werden in der öffentlichen Vorschau unterstützt. Weitere Informationen zu Vorschauversionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![Hochladen von OATH-Token auf das Blatt „MFA > OATH-Token“](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Sobald Sie Token erworben haben, müssen Sie diese in einem Format mit kommagetrennten Werten (CSV) einschließlich UPN, Seriennummer, geheimem Schlüssel, Zeitintervall, Hersteller und Modell hochladen, wie im folgenden Beispiel gezeigt:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Stellen Sie sicher, dass Sie die Kopfzeile in Ihre CSV-Datei einschließen.

Nach der ordnungsgemäßen Formatierung als CSV-Datei kann sich ein Administrator im Azure-Portal anmelden, zu **Azure Active Directory > Sicherheit > MFA > OATH-Token** navigieren und die resultierende CSV-Datei hochladen.

Je nach Größe der CSV-Datei kann die Verarbeitung ein paar Minuten dauern. Wählen Sie die Schaltfläche **Aktualisieren** aus, um den aktuellen Status abzurufen. Wenn die Datei Fehler enthält, können Sie eine CSV-Datei herunterladen, in der alle zu behebenden Fehler aufgeführt sind. Die Feldnamen in der heruntergeladenen CSV-Datei unterscheiden sich von der hochgeladenen Version.

Sobald alle Fehler behoben sind, kann der Administrator die einzelnen Schlüssel aktivieren, indem er für das Token **Aktivieren** auswählt und das in dem Token angezeigte Einmalkennwort eingibt.

Benutzer verfügen möglicherweise über eine Kombination aus bis zu fünf OATH-Hardwaretoken oder Authentifizierungsanwendungen wie die Microsoft Authenticator-App, die für die jederzeitige Verwendung konfiguriert sind.

## <a name="phone-call-settings"></a>Einstellungen für Telefonanruf

Wenn Benutzer Anrufe für MFA-Eingabeaufforderungen empfangen, können Sie die Anrufer-ID oder die Begrüßung konfigurieren.

Wenn Sie in den USA nicht die MFA-Anrufer-ID konfiguriert haben, stammen Anrufe von Microsoft von folgenden Telefonnummern. Wenn Sie Spamfilter verwenden, stellen Sie sicher, dass diese Nummern nicht gefiltert werden:

* *+1 (866) 539 4191*
* *+1 (855) 330 8653*
* *+1 (877) 668 6536*

> [!NOTE]
> Wenn Anrufe im Rahmen von Azure AD Multi-Factor Authentication über das öffentliche Telefonnetz erfolgen, werden sie manchmal über einen Netzbetreiber geroutet, der Anrufer-IDs nicht unterstützt. Aus diesem Grund gibt es keine Garantie für die Anrufer-ID, auch wenn Azure AD Multi-Factor Authentication sie immer sendet. Dies gilt sowohl für Telefonanrufe als auch für SMS-Nachrichten, die von Azure AD Multi-Factor Authentication bereitgestellt werden. Wenn Sie überprüfen müssen, ob eine Textnachricht von Azure AD Multi-Factor Authentication stammt, lesen Sie [Welche SMS-Kurzcodes werden für das Senden von Nachrichten verwendet?](multi-factor-authentication-faq.md#what-sms-short-codes-are-used-for-sending-sms-messages-to-my-users)

Führen Sie die folgenden Schritte aus, um Ihre eigene Anrufer-ID-Nummer zu konfigurieren:

1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **MFA** > **Einstellungen für Telefonanruf**.
1. Legen Sie für die **MFA-Anrufer-ID** die Nummer fest, die Ihren Benutzern auf dem Telefon angezeigt werden soll. Es sind nur Nummern aus den USA zulässig.
1. Wählen Sie **Speichern** aus, wenn Sie so weit sind.

### <a name="custom-voice-messages"></a>Benutzerdefinierte Sprachnachrichten

Mit dem Feature für benutzerdefinierte Sprachnachrichten können Sie Ihre eigenen Aufzeichnungen oder Begrüßungen für Azure AD MFA verwenden. Diese Nachrichten können zusätzlich zu den Standardaufzeichnungen von Microsoft verwendet werden oder diese ersetzen.

Bevor Sie beginnen, sollten Sie die folgenden Einschränkungen beachten:

* Die unterstützten Dateiformate sind *WAV* und *MP3*.
* Die maximale Dateigröße beträgt 1 MB.
* Authentifizierungsnachrichten sollten kürzer als 20 Sekunden sein. Bei Nachrichten mit einer Länge von mehr als 20 Sekunden ist die Überprüfung unter Umständen nicht erfolgreich. Der Benutzer reagiert möglicherweise erst dann, wenn die Nachricht zu Ende ist, sodass bei der Überprüfung ein Timeout auftritt.

### <a name="custom-message-language-behavior"></a>Benutzerdefiniertes Nachrichtensprachenverhalten

Wenn für den Benutzer eine benutzerdefinierte Sprachnachricht wiedergegeben wird, hängt die Sprache der Nachricht von den folgenden Faktoren ab:

* Sprache des aktuellen Benutzers.
  * Vom Browser des Benutzers erkannte Sprache.
  * Andere Authentifizierungsszenarios verhalten sich möglicherweise anders.
* Die Sprache jeglicher verfügbarer benutzerdefinierter Nachrichten.
  * Diese Sprache wird vom Administrator ausgewählt, wenn eine benutzerdefinierte Nachricht hinzugefügt wird.

Wenn beispielsweise nur eine benutzerdefinierte Nachricht in der Sprache Deutsch vorhanden ist:

* Ein Benutzer, der sich in deutscher Sprache authentifiziert, hört dann die benutzerdefinierte deutsche Nachricht.
* Ein Benutzer, der sich in Englisch authentifiziert, hört dann die englische Standardnachricht.

### <a name="custom-voice-message-defaults"></a>Benutzerdefinierte Standardsprachnachrichten

Die folgenden Beispielskripts können zum Erstellen Ihrer eigenen benutzerdefinierten Nachrichten verwendet werden. Die folgenden Standardphrasen werden verwendet, wenn Sie keine eigenen benutzerdefinierten Nachrichten konfigurieren:

| Nachrichtenname | Skript |
| --- | --- |
| Authentifizierung erfolgreich | Ihre Anmeldung wurde erfolgreich überprüft. Auf Wiedersehen. |
| Grußformel bei Durchwahl | Vielen Dank, dass Sie das Anmeldungsüberprüfungssystem von Microsoft verwenden. Drücken Sie #, um Ihre Anmeldung abzuschließen. |
| Betrugsmeldung bestätigen | Eine Betrugswarnung wurde ausgegeben. Wenden Sie sich an den IT-Helpdesk Ihres Unternehmens, um die Sperrung Ihres Kontos aufzuheben. |
| Betrug – Grußformel (Standardmodus) | Vielen Dank, dass Sie das Anmeldungsüberprüfungssystem von Microsoft verwenden. Drücken Sie das Nummernzeichen, um die Überprüfung abzuschließen. Wenn Sie diese Überprüfung nicht initiiert haben, versucht möglicherweise jemand, auf Ihr Konto zuzugreifen. Drücken Sie nacheinander die Tasten 0 und # um eine Betrugswarnung abzusenden. So benachrichtigen Sie das IT-Team Ihres Unternehmens und blockieren weitere Überprüfungsversuche. |
| Betrug wurde gemeldet. Eine Betrugswarnung wurde ausgegeben. | Wenden Sie sich an den IT-Helpdesk Ihres Unternehmens, um die Sperrung Ihres Kontos aufzuheben. |
| Aktivierung | Vielen Dank, dass Sie das Anmeldungsüberprüfungssystem von Microsoft verwenden. Drücken Sie das Nummernzeichen, um die Überprüfung abzuschließen. |
| Authentifizierung abgelehnt. Erneut versuchen. | Überprüfung abgelehnt. |
| Erneut versuchen (Standardmodus) | Vielen Dank, dass Sie das Anmeldungsüberprüfungssystem von Microsoft verwenden. Drücken Sie das Nummernzeichen, um die Überprüfung abzuschließen. |
| Grußformel (Standardmodus) | Vielen Dank, dass Sie das Anmeldungsüberprüfungssystem von Microsoft verwenden. Drücken Sie das Nummernzeichen, um die Überprüfung abzuschließen. |
| Grußformel (PIN-Modus) | Vielen Dank, dass Sie das Anmeldungsüberprüfungssystem von Microsoft verwenden. Geben Sie Ihre PIN ein, und drücken Sie danach das Nummernzeichen, um die Überprüfung abzuschließen. |
| Betrug – Grußformel (PIN-Modus) | Vielen Dank, dass Sie das Anmeldungsüberprüfungssystem von Microsoft verwenden.  Geben Sie Ihre PIN ein, und drücken Sie danach das Nummernzeichen, um die Überprüfung abzuschließen. Wenn Sie diese Überprüfung nicht initiiert haben, versucht möglicherweise jemand, auf Ihr Konto zuzugreifen. Drücken Sie nacheinander die Tasten 0 und # um eine Betrugswarnung abzusenden. So benachrichtigen Sie das IT-Team Ihres Unternehmens und blockieren weitere Überprüfungsversuche. |
| Erneut versuchen (PIN-Modus) | Vielen Dank, dass Sie das Anmeldungsüberprüfungssystem von Microsoft verwenden. Geben Sie Ihre PIN ein, und drücken Sie danach das Nummernzeichen, um die Überprüfung abzuschließen. |
| Grußformel nach Ansage der Durchwahlziffern | Wenn Sie bereits bei dieser Durchwahl sind, drücken Sie zum Fortfahren das Nummernzeichen. |
| Authentifizierung abgelehnt | Sie können zurzeit leider nicht angemeldet werden. Versuchen Sie es später noch mal. |
| Grußformel bei der Aktivierung (Standardmodus) | Vielen Dank, dass Sie das Anmeldungsüberprüfungssystem von Microsoft verwenden. Drücken Sie das Nummernzeichen, um die Überprüfung abzuschließen. |
| Aktivierung erneut versuchen (Standardmodus) | Vielen Dank, dass Sie das Anmeldungsüberprüfungssystem von Microsoft verwenden. Drücken Sie das Nummernzeichen, um die Überprüfung abzuschließen. |
| Grußformel bei der Aktivierung (PIN-Modus) | Vielen Dank, dass Sie das Anmeldungsüberprüfungssystem von Microsoft verwenden. Geben Sie Ihre PIN ein, und drücken Sie danach das Nummernzeichen, um die Überprüfung abzuschließen. |
| Grußformel vor Ansage der Durchwahlziffern | Vielen Dank, dass Sie das Anmeldungsüberprüfungssystem von Microsoft verwenden. Übertragen Sie diesen Anruf an Durchwahl ... |

### <a name="set-up-a-custom-message"></a>Einrichten einer benutzerdefinierten Nachricht

Führen Sie die folgenden Schritte aus, um Ihre eigenen benutzerdefinierten Nachrichten zu verwenden:

1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **MFA** > **Einstellungen für Telefonanruf**.
1. Wählen Sie **Begrüßung hinzufügen**.
1. Wählen Sie den **Typ** der Begrüßung aus, z. B. *Grußformel (Standardmodus)* oder *Authentifizierung erfolgreich*.
1. Wählen Sie die **Sprache** basierend auf dem vorherigen Abschnitt [Benutzerdefiniertes Nachrichtensprachenverhalten](#custom-message-language-behavior) aus.
1. Wählen Sie eine *MP3*- oder *WAV*-Audiodatei zum Hochladen aus.
1. Wenn Sie fertig sind, klicken Sie auf **Hinzufügen** und dann auf **Speichern**.

## <a name="mfa-service-settings"></a>MFA-Diensteinstellungen

Einstellungen für App-Kennwörter, vertrauenswürdige IPs, Überprüfungsoptionen und das Speichern von Multi-Factor Authentication für Azure AD Multi-Factor Authentication finden Sie in den Diensteinstellungen. Dabei handelt es sich um ein Legacyportal, das nicht Teil des herkömmlichen Azure AD-Portals ist.

Auf die Diensteinstellungen kann über das Azure-Portal zugegriffen werden, indem Sie zu **Azure Active Directory** > **Sicherheit** > **MFA** > **Erste Schritte** > **Konfigurieren** > **Zusätzliche Cloud-basierte MFA-Einstellungen** navigieren. Ein neues Fenster oder eine neue Registerkarte wird mit zusätzlichen *Diensteinstellungen* geöffnet.

## <a name="trusted-ips"></a>Vertrauenswürdige IP-Adressen

Das Feature _Vertrauenswürdige IPs_ von Azure AD MFA umgeht MFA-Eingabeaufforderungen für Benutzer, die sich über einen definierten IP-Adressbereich anmelden. Sie können vertrauenswürdige IP-Adressbereiche für Ihre lokalen Umgebungen festlegen, damit Benutzer, die sich an diesen Standorten befinden, keine Azure AD MFA-Eingabeaufforderung erhalten.

> [!NOTE]
> Private IP-Adressbereiche können nur als vertrauenswürdige IPs festgelegt werden, wenn Sie MFA-Server verwenden. Für cloudbasierte Azure AD MFA können Sie nur öffentliche IP-Adressbereiche verwenden.
>
> IPv6-Bereiche werden nur in der Schnittstelle [Benannter Standort (Vorschau)](../conditional-access/location-condition.md#preview-features) unterstützt.

Wenn Ihre Organisation die NPS-Erweiterung bereitstellt, um MFA für lokale Anwendungen bereitzustellen, beachten Sie, dass als Quell-IP-Adresse immer die des NPS-Servers angezeigt wird, über den der Authentifizierungsversuch verläuft.

| Azure AD-Mandantentyp | Optionen des Features „Vertrauenswürdige IPs“ |
|:--- |:--- |
| Verwaltet |**Bestimmter Bereich von IP-Adressen**: Administratoren geben einen Bereich von IP-Adressen an, die die mehrstufige Authentifizierung für Benutzer, die sich über das Intranet des Unternehmens anmelden, umgehen können. Es können maximal 50 vertrauenswürdige IP-Adressbereiche konfiguriert werden.|
| Im Verbund |**Alle Verbundbenutzer**: Alle Verbundbenutzer, die sich von der Organisation aus anmelden, können die mehrstufige Authentifizierung umgehen. Die Benutzer umgehen die Überprüfung mithilfe eines durch Active Directory-Verbunddienste (AD FS) ausgestellten Anspruchs.<br/>**Bestimmter Bereich von IP-Adressen**: Administratoren geben einen Bereich von IP-Adressen an, die die mehrstufige Authentifizierung für Benutzer, die sich über das Intranet des Unternehmens anmelden, umgehen können. |

Die Umgehung über vertrauenswürdige IPs funktioniert nur innerhalb des Intranets eines Unternehmens. Wenn Sie die Option **Alle Verbundbenutzer** ausgewählt haben und sich ein Benutzer von außerhalb des Unternehmensintranets anmeldet, muss sich der Benutzer mit der mehrstufige Authentifizierung authentifizieren. Dies ist auch der Fall, wenn der Benutzer einen AD FS-Anspruch vorweisen kann.

### <a name="end-user-experience-inside-of-corpnet"></a>Endbenutzererfahrung innerhalb des Unternehmensnetzwerks

Wenn das Feature „Vertrauenswürdige IPs“ deaktiviert ist, ist die mehrstufige Authentifizierung für Browserabläufe erforderlich. Für ältere Rich Client-Anwendungen werden App-Kennwörter benötigt.

Wenn vertrauenswürdige IPs verwendet werden, ist die mehrstufige Authentifizierung nicht für Browserabläufe erforderlich. App-Kennwörter sind für ältere Rich Client-Anwendungen nicht erforderlich, sofern der Benutzer nicht bereits ein App-Kennwort erstellt hat. Sobald ein App-Kennwort verwendet wird, ist es erforderlich.

### <a name="end-user-experience-outside-corpnet"></a>Endbenutzererfahrung außerhalb des Unternehmensnetzwerks

Unabhängig davon, ob vertrauenswürdige IP-Adressen definiert sind, ist die mehrstufige Authentifizierung für Browserabläufe erforderlich. Für ältere Rich Client-Anwendungen werden App-Kennwörter benötigt.

### <a name="enable-named-locations-by-using-conditional-access"></a>Aktivieren benannter Orte mit bedingtem Zugriff

Sie können die Regeln für bedingten Zugriff verwenden, um benannte Standorte zu definieren, indem Sie die folgenden Schritte ausführen:

1. Klicken Sie im Azure-Portal auf **Azure Active Directory**, und navigieren Sie dann zu **Sicherheit** > **Bedingter Zugriff** > **Benannte Standorte**.
1. Wählen Sie **Neuer Ort** aus.
1. Geben Sie einen Namen für den Standort ein.
1. Wählen Sie **Als vertrauenswürdigen Standort markieren** aus.
1. Geben Sie den IP-Adressbereich in der CIDR-Notation für Ihre Umgebung ein, z. B. *40.77.182.32/27*.
1. Klicken Sie auf **Erstellen**.

### <a name="enable-the-trusted-ips-feature-by-using-conditional-access"></a>Aktivieren des Features „Vertrauenswürdige IPs“ mit bedingtem Zugriff

Führen Sie die folgenden Schritte aus, um vertrauenswürdige IP-Adressen mit Richtlinien für bedingten Zugriff zu ermöglichen:

1. Klicken Sie im Azure-Portal auf **Azure Active Directory**, und navigieren Sie dann zu **Sicherheit** >  **Bedingter Zugriff** > **Benannte Standorte**.
1. Wählen Sie **Durch MFA bestätigte IPs konfigurieren** aus.
1. Wählen Sie auf der Seite **Diensteinstellungen** unter **Vertrauenswürdige IPs** eine der folgenden beiden Optionen aus:

   * **Für Anforderungen von Partnerbenutzern, die aus meinem Intranet stammen**: Aktivieren Sie das Kontrollkästchen, um diese Option auszuwählen. Alle Verbundbenutzer, die sich vom Unternehmensnetzwerk aus anmelden, umgehen die mehrstufige Authentifizierung mithilfe eines von AD FS ausgestellten Anspruchs. Stellen Sie sicher, dass AD FS über eine Regel zum Hinzufügen des Intranetanspruchs zum entsprechenden Datenverkehr verfügt. Wenn die Regel nicht vorhanden ist, erstellen Sie die folgende Regel in AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Für Anforderungen aus einem bestimmten Bereich öffentlicher IPs**: Geben Sie zum Auswählen dieser Option die IP-Adressen in der CIDR-Notation im Textfeld ein.
      * Verwenden Sie für IP-Adressen im Bereich xxx.xxx.xxx.1 bis xxx.xxx.xxx.254 die Notation **xxx.xxx.xxx.0/24**.
      * Verwenden Sie für eine einzelne IP-Adresse die Notation **xxx.xxx.xxx.xxx/32**.
      * Sie können bis zu 50 IP-Adressbereiche eingeben. Benutzer, die sich über diese IP-Adressen anmelden, umgehen die mehrstufige Authentifizierung.

1. Wählen Sie **Speichern** aus.

### <a name="enable-the-trusted-ips-feature-by-using-service-settings"></a>Aktivieren des Features „Vertrauenswürdige IPs“ mit Diensteinstellungen

Wenn Sie keine Richtlinien für bedingten Zugriff verwenden möchten, um vertrauenswürdige IP-Adressen zu ermöglichen, können Sie die *Diensteinstellungen* für Azure AD MFA mit den folgenden Schritten konfigurieren:

1. Klicken Sie im Azure-Portal auf **Azure Active Directory**, und wählen Sie dann **Benutzer** aus.
1. Wählen Sie **Multi-Factor Authentication** aus.
1. Klicken Sie unter „Multi-Factor Authentication“ auf **Diensteinstellungen**.
1. Wählen Sie auf der Seite **Diensteinstellungen** unter **Vertrauenswürdige IPs** eine (oder beide) der folgenden zwei Optionen aus:

   * **Für Anforderungen von Partnerbenutzern in meinem Intranet**: Aktivieren Sie das Kontrollkästchen, um diese Option auszuwählen. Alle Verbundbenutzer, die sich vom Unternehmensnetzwerk aus anmelden, umgehen die mehrstufige Authentifizierung mithilfe eines von AD FS ausgestellten Anspruchs. Stellen Sie sicher, dass AD FS über eine Regel zum Hinzufügen des Intranetanspruchs zum entsprechenden Datenverkehr verfügt. Wenn die Regel nicht vorhanden ist, erstellen Sie die folgende Regel in AD FS:

      `c:[Type== "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork"] => issue(claim = c);`

   * **Für Anforderungen aus einem angegebenen Bereich von IP-Adresssubnetzen**: Geben Sie zum Auswählen dieser Option die IP-Adressen in der CIDR-Notation im Textfeld ein.
      * Verwenden Sie für IP-Adressen im Bereich xxx.xxx.xxx.1 bis xxx.xxx.xxx.254 die Notation **xxx.xxx.xxx.0/24**.
      * Verwenden Sie für eine einzelne IP-Adresse die Notation **xxx.xxx.xxx.xxx/32**.
      * Sie können bis zu 50 IP-Adressbereiche eingeben. Benutzer, die sich über diese IP-Adressen anmelden, umgehen die mehrstufige Authentifizierung.

1. Wählen Sie **Speichern** aus.

## <a name="verification-methods"></a>Überprüfungsmethoden

Sie können im Diensteinstellungsportal auswählen, welche Überprüfungsmethoden für Ihre Benutzer verfügbar sind. Wenn Ihre Benutzer ihre Konten für Azure AD Multi-Factor Authentication registrieren, wählen sie ihre bevorzugte Überprüfungsmethode aus den von Ihnen aktivierten Optionen aus. Anleitungen zum Benutzerregistrierungsprozess finden Sie unter [Einrichten meines Kontos für die mehrstufige Authentifizierung](../user-help/multi-factor-authentication-end-user-first-time.md).

Die folgenden Überprüfungsmethoden stehen zur Auswahl:

| Methode | Beschreibung |
|:--- |:--- |
| Auf Telefon anrufen |Startet einen automatisierten Sprachanruf. Der Benutzer nimmt den Anruf an und drückt die #-Taste auf der Telefontastatur, um sich zu authentifizieren. Die Telefonnummer wird nicht mit dem lokalen Active Directory synchronisiert. |
| Textnachricht an Telefon |Sendet eine Textnachricht mit einem Überprüfungscode. Der Benutzer wird über die Anmeldeoberfläche zur Eingabe des Prüfcodes aufgefordert. Dieser Vorgang wird als „unidirektionale SMS“ bezeichnet. Bei einer bidirektionalen SMS muss der Benutzer einen bestimmten Code per SMS zurücksenden. Die Funktion für bidirektionale SMS ist veraltet und wird ab dem 14. November 2018 nicht mehr unterstützt. Administratoren sollten eine andere Methode für Benutzer aktivieren, die zuvor bidirektionale SMS verwendet haben.|
| Benachrichtigung über mobile App |Sendet eine Pushbenachrichtigung an Ihr Telefon oder registriertes Gerät. Der Benutzer zeigt die Benachrichtigung an und wählt **Überprüfen**, um die Überprüfung abzuschließen. Die Microsoft Authenticator-App ist für [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072) und [iOS](https://go.microsoft.com/fwlink/?Linkid=825073) verfügbar. |
| Prüfcode aus mobiler App oder Hardwaretoken |Die Microsoft Authenticator-App generiert alle 30 Sekunden einen neuen OATH-Überprüfungscode. Der Benutzer gibt den Überprüfungscode auf der Anmeldeoberfläche ein. Die Microsoft Authenticator-App ist für [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6), [Android](https://go.microsoft.com/fwlink/?Linkid=825072) und [iOS](https://go.microsoft.com/fwlink/?Linkid=825073) verfügbar. |

Weitere Informationen finden Sie unter [Welche Authentifizierungs- und Prüfmethoden stehen in Azure Active Directory zur Verfügung?](concept-authentication-methods.md)

### <a name="enable-and-disable-verification-methods"></a>Aktivieren und Deaktivieren von Überprüfungsmethoden

Führen Sie die folgenden Schritte aus, um Überprüfungsmethoden zu aktivieren oder zu deaktivieren:

1. Klicken Sie im Azure-Portal auf **Azure Active Directory**, und wählen Sie dann **Benutzer** aus.
1. Wählen Sie **Multi-Factor Authentication** aus.
1. Klicken Sie unter „Multi-Factor Authentication“ auf **Diensteinstellungen**.
1. Aktivieren oder deaktivieren Sie auf der Seite **Diensteinstellungen** unter **Überprüfungsoptionen** die Methoden, die den Benutzern zur Verfügung gestellt werden.
1. Klicken Sie auf **Speichern**.

## <a name="remember-multi-factor-authentication"></a>Speichern der Multi-Factor Authentication

Mit dem Feature _Multi-Factor Authentication speichern_ können Benutzer nachfolgende Überprüfungen für eine angegebene Anzahl von Tagen umgehen, nachdem sie sich mithilfe von Multi-Factor Authentication erfolgreich bei einem Gerät angemeldet haben. Wählen Sie eine Dauer von mindestens 90 Tagen aus, um die Benutzerfreundlichkeit zu verbessern und die Häufigkeit zu minimieren, mit der ein Benutzer die MFA auf demselben Gerät ausführen muss.

> [!IMPORTANT]
> Wenn ein Konto oder Gerät gefährdet ist, kann das Speichern von Multi-Factor Authentication für vertrauenswürdige Geräte die Sicherheit beeinträchtigen. Wenn ein Unternehmenskonto kompromittiert oder ein vertrauenswürdiges Gerät verloren geht oder gestohlen wird, sollten Sie [MFA-Sitzungen widerrufen](howto-mfa-userdevicesettings.md).
>
> Durch die Wiederherstellungsaktion wird der vertrauenswürdige Status aller Geräte widerrufen, und der Benutzer muss wieder die mehrstufige Authentifizierung ausführen. Sie können Ihre Benutzer auch anweisen, Multi-Factor Authentication auf ihren eigenen Geräten wie unter [Verwalten der Einstellungen für die mehrstufige Authentifizierung](../user-help/multi-factor-authentication-end-user-manage-settings.md#turn-on-two-factor-verification-prompts-on-a-trusted-device) beschrieben wiederherzustellen.

### <a name="how-the-feature-works"></a>Funktionsweise des Features

Durch das Feature „Multi-Factor Authentication speichern“ wird ein permanentes Cookie im Browser festgelegt, wenn ein Benutzer die Option **Die nächsten X Tage nicht erneut fragen** auswählt. Der Benutzer wird von diesem Browser bis zum Ablauf des Cookies nicht erneut zur Multi-Factor Authentication aufgefordert. Wenn der Benutzer einen anderen Browser auf dem gleichen Gerät öffnet oder seine Cookies löscht, wird er wieder zur Überprüfung aufgefordert.

Die Option **Die nächsten X Tage nicht erneut fragen** wird in Nicht-Browseranwendungen nicht angezeigt, unabhängig davon, ob diese die moderne Authentifizierung unterstützen. Diese Apps verwenden _Aktualisierungstoken_, die jede Stunde neue Zugriffstoken bereitstellen. Bei der Überprüfung eines Aktualisierungstokens überprüft Azure AD, ob die letzte mehrstufige Authentifizierung innerhalb der angegebenen Anzahl von Tagen durchgeführt wurde.

Mit dem Feature reduziert sich die Anzahl der Authentifizierungen für Web-Apps, die normalerweise jedes Mal dazu auffordern. Das Feature erhöht die Anzahl der Authentifizierungen für Clients mit moderner Authentifizierung, die normalerweise alle 90 Tage zur Authentifizierung auffordern, wenn eine kürzere Dauer konfiguriert ist. Die Anzahl der Authentifizierungen kann sich auch erhöhen, wenn mit bedingten Zugriffsrichtlinien kombiniert wird.

> [!IMPORTANT]
> Das Feature **Multi-Factor Authentication speichern** ist nicht kompatibel mit dem AD FS-Feature **Angemeldet bleiben**, bei dem Benutzer die mehrstufige Authentifizierung für AD FS über Azure Multi-Factor Authentication Server oder eine Lösung zur mehrstufigen Authentifizierung von Drittanbietern ausführen.
>
> Wenn Ihre Benutzer in AD FS **Angemeldet bleiben** auswählen und ihr Gerät außerdem als für Multi-Factor Authentication vertrauenswürdig markieren, werden sie nach Ablauf der Anzahl von Tagen, die unter **Multi-Factor Authentication speichern** angegeben wurde, nicht automatisch überprüft. Azure AD fordert eine neue mehrstufige Authentifizierung an, aber AD FS gibt ein Token mit dem ursprünglichen Multi-Factor Authentication-Anspruch und -Datum zurück, statt die mehrstufige Authentifizierung erneut durchzuführen. **Durch diese Reaktion entsteht eine Schleife bei der Überprüfung zwischen Azure AD und AD FS.**
>
> Das Feature **Multi-Factor Authentication speichern** ist nicht mit B2B-Benutzern kompatibel und wird B2B-Benutzer nicht angezeigt, wenn sie sich bei den eingeladenen Mandanten anmelden.
>

### <a name="enable-remember-multi-factor-authentication"></a>Aktivieren der Speicherung von Multi-Factor Authentication

Führen Sie die folgenden Schritte aus, um die Option zum Speichern des MFA-Status und zum Umgehen der Eingabeaufforderung zu aktivieren und zu konfigurieren:

1. Klicken Sie im Azure-Portal auf **Azure Active Directory**, und wählen Sie dann **Benutzer** aus.
1. Wählen Sie **Multi-Factor Authentication** aus.
1. Klicken Sie unter „Multi-Factor Authentication“ auf **Diensteinstellungen**.
1. Wählen Sie auf der Seite **Diensteinstellungen** unter **Multi-Factor Authentication speichern** die Option **Benutzern das Speichern der mehrstufigen Authentifizierung auf vertrauenswürdigen Geräten ermöglichen** aus.
1. Legen Sie fest, für wie viele Tage vertrauenswürdige Geräte die mehrstufige Authentifizierung umgehen können. Für eine optimale Benutzerleistung verlängern Sie die Dauer auf mindestens *90* Tage.
1. Wählen Sie **Speichern** aus.

### <a name="mark-a-device-as-trusted"></a>Markieren eines Geräts als vertrauenswürdig

Nachdem Sie das Feature „Multi-Factor Authentication speichern“ aktiviert haben, können Benutzer bei der Anmeldung ein Gerät als vertrauenswürdig markieren, indem sie die Option **Nicht erneut nachfragen** auswählen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über die verfügbaren Methoden in Azure AD Multi-Factor Authentication finden Sie unter [Welche Authentifizierungs- und Prüfmethoden stehen in Azure Active Directory zur Verfügung?](concept-authentication-methods.md).
