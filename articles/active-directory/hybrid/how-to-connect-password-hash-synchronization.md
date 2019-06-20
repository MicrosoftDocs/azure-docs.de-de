---
title: Implementieren der Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung | Microsoft Docs
description: Enthält Informationen zur Funktionsweise der Kennworthashsynchronisierung sowie zu ihrer Einrichtung.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 05f16c3e-9d23-45dc-afca-3d0fa9dbf501
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/02/2019
ms.subservice: hybrid
ms.author: billmath
search.appverid:
- MET150
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66ff1cb57bb3317adc7ab6208ebf5029361b7c54
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235152"
---
# <a name="implement-password-hash-synchronization-with-azure-ad-connect-sync"></a>Implementieren der Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung
In diesem Artikel finden Sie alle Informationen, die Sie benötigen, um Benutzerkennwörter aus einer lokalen Active Directory-Instanz mit einer cloudbasierten Azure Active Directory-Instanz (Azure AD) zu synchronisieren.

## <a name="how-password-hash-synchronization-works"></a>So funktioniert die Kennworthashsynchronisierung
Der Active Directory-Domänendienst speichert Kennwörter in Form einer Hashwertdarstellung des tatsächlichen Benutzerkennworts. Ein Hashwert ist das Ergebnis einer unidirektionalen mathematischen Funktion (des „*Hashalgorithmus*“). Es ist nicht möglich, das Ergebnis einer unidirektionalen Funktion in die Nur-Text-Version eines Kennworts umzukehren. Sie können einen Kennworthash nicht zur Anmeldung in Ihrem lokalen Netzwerk verwenden.

Um Ihr Kennwort zu synchronisieren, extrahiert die Azure AD Connect-Synchronisierung den Kennworthash aus der lokalen Active Directory-Instanz. Vor der Synchronisierung mit dem Azure Active Directory-Authentifizierungsdienst wird der Kennworthash einer zusätzlichen Sicherheitsverarbeitung unterzogen. Kennwörter werden pro Benutzer und in chronologischer Reihenfolge synchronisiert.

Der tatsächliche Datenfluss des Kennworthashsynchronisierungs-Vorgangs ähnelt der Synchronisierung von Benutzerdaten. Kennwörter werden jedoch häufiger synchronisiert als vom Standardzeitfenster für die Verzeichnissynchronisierung für andere Attribute vorgesehen. Der Prozess der Kennworthashsynchronisierung wird alle zwei Minuten ausgeführt. Sie können die Häufigkeit der Ausführung nicht ändern. Wenn Sie ein Kennwort synchronisieren, wird das vorhandene Cloudkennwort überschrieben.

Bei der ersten Aktivierung der Kennworthashsynchronisierung wird eine anfängliche Synchronisierung der Kennwörter aller im Bereich befindlichen Benutzer durchgeführt. Es ist nicht möglich, explizit eine Teilmenge der Benutzerkennwörter zu definieren, die Sie synchronisieren möchten.

Wenn Sie ein lokales Kennwort ändern, wird das aktualisierte Kennwort synchronisiert. Dies dauert meist nur wenige Minuten.
Das Feature der Kennworthashsynchronisierung versucht automatisch, fehlerhafte Synchronisierungsversuche erneut auszuführen. Wenn beim Versuch, ein Kennwort zu synchronisieren, ein Fehler auftritt, wird der Fehler in der Ereignisanzeige protokolliert.

Die Synchronisierung eines Kennworts hat keinen Einfluss auf den derzeit angemeldeten Benutzer.
Wenn eine synchronisierte Kennwortänderung durchgeführt wird, während Sie an einem Clouddienst angemeldet sind, wirkt sich dies nicht direkt auf Ihre aktuelle Clouddienstsitzung aus. Wenn aber für den Clouddienst eine erneute Authentifizierung erforderlich ist, müssen Sie Ihr neues Kennwort angeben.

Ein Benutzer muss seine Unternehmensanmeldeinformationen ein zweites Mal eingeben, um sich bei Azure AD zu authentifizieren, und zwar unabhängig davon, ob er bei seinem Unternehmensnetzwerk angemeldet ist. Dieses Verhalten kann aber auf ein Mindestmaß beschränkt werden, indem der Benutzer bei der Anmeldung das Kontrollkästchen „Angemeldet bleiben“ aktiviert. Dadurch wird ein Sitzungscookie festgelegt, das die Authentifizierung 180 Tage lang umgeht. Die Einstellung „Angemeldet bleiben“ kann vom Azure AD-Administrator aktiviert oder deaktiviert werden. Darüber hinaus können Sie die Kennworteingabeaufforderungen reduzieren, indem Sie [nahtloses einmaliges Anmelden](how-to-connect-sso.md) aktivieren. Dabei werden Benutzer automatisch angemeldet, wenn sie mit ihren Unternehmensgeräten mit dem Unternehmensnetzwerk verbunden sind.

> [!NOTE]
> Die Kennwortsynchronisierung wird nur für Objekttyp-Benutzer in Active Directory unterstützt. Sie wird vom iNetOrgPerson-Objektyp nicht unterstützt.

### <a name="detailed-description-of-how-password-hash-synchronization-works"></a>Ausführliche Beschreibung der Funktionsweise der Kennworthashsynchronisierung
Im folgenden Abschnitt wird ausführlich beschrieben, wie die Kennworthashsynchronisierung zwischen Active Directory und Azure AD funktioniert.

![Detaillierter Kennwortfluss](./media/how-to-connect-password-hash-synchronization/arch3b.png)


1. Alle zwei Minuten fordert der Kennworthashsynchronisierungs-Agent auf dem AD Connect-Server gespeicherte Kennworthashes (unicodePwd-Attribut) von einem DC an.  Diese Anforderung erfolgt über das [MS-DRSR](https://msdn.microsoft.com/library/cc228086.aspx)-Standardreplikationsprotokoll, das zum Synchronisieren von Daten zwischen DCs verwendet wird. Das Dienstkonto muss die AD-Berechtigungen „Verzeichnisänderungen replizieren“ und „Verzeichnisänderungen replizieren: Alle“ haben (die bei der Installation standardmäßig erteilt werden), um die Kennworthashes abzurufen.
2. Vor dem Senden verschlüsselt der Domänencontroller den MD4-Kennworthash mithilfe eines Schlüssels, bei dem es sich um einen [MD5](https://www.rfc-editor.org/rfc/rfc1321.txt)-Hash des RPC-Sitzungsschlüssel und einen Salt-Wert handelt. Anschließend wird das Ergebnis über RPC an den Kennworthashsynchronisierungs-Agent gesendet. Der Domänencontroller übergibt auch mithilfe des Replikationsprotokolls des Domänencontrollers den Salt-Wert an den Synchronisierungs-Agent, damit der Agent den Umschlag entschlüsseln kann.
3.  Sobald der Kennworthashsynchronisierungs-Agent über den verschlüsselten Umschlag verfügt, verwendet er [MD5CryptoServiceProvider](https://msdn.microsoft.com/library/System.Security.Cryptography.MD5CryptoServiceProvider.aspx) und den Salt-Wert, um einen Schlüssel zum Rückentschlüsseln der empfangenen Daten in ihr ursprüngliches MD4-Format zu generieren. Der Kennworthashsynchronisierungs-Agent verfügt niemals über Zugriff auf das unverschlüsselte Kennwort. Die Nutzung von MD5 durch den Kennworthashsynchronisierungs-Agent dient ausschließlich zur Kompatibilität des Replikationsprotokolls mit dem Domänencontroller und erfolgt nur lokal zwischen dem Domänencontroller und dem Kennworthashsynchronisierungs-Agent.
4.  Der Kennworthashsynchronisierungs-Agent erweitert den binären 16-Byte-Kennworthash auf 64 Bytes, indem zunächst der Hash in eine hexadezimale 32-Byte-Zeichenfolge umgewandelt wird, die anschließend mithilfe der UTF-16-Codierung wieder in das Binärformat konvertiert wird.
5.  Der Kennworthashsynchronisierungs-Agent fügt der 64-Byte-Binärdatei einen benutzerspezifischen Salt-Wert mit einer Länge von zehn Bytes hinzu, um den ursprünglichen Hash noch besser zu schützen.
6.  Anschließend kombiniert der Kennworthashsynchronisierungs-Agent den MD4-Hash mit dem benutzerspezifischen Salt-Wert und gibt das Ergebnis in die Funktion [PBKDF2](https://www.ietf.org/rfc/rfc2898.txt) ein. Hierzu werden 1.000 Iterationen des mit [HMAC-SHA256](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) verschlüsselten Hashalgorithmus verwendet. 
7.  Der Kennworthashsynchronisierungs-Agent verwendet den resultierenden 32-Byte-Hash, verkettet sowohl den benutzerspezifischen Salt-Wert als auch die Anzahl von SHA256-Iterationen damit (für die Verwendung durch Azure AD) und überträgt die Zeichenfolge anschließend von Azure AD Connect per SSL an Azure AD.</br> 
8.  Wenn ein Benutzer sich bei Azure AD anzumelden versucht und sein Kennwort eingibt, durchläuft das Kennwort denselben aus MD4+Salt+PBKDF2+HMAC-SHA256 bestehenden Prozess. Wenn der resultierende Hash dem in Azure AD gespeicherten Hash entspricht, hat der Benutzer das richtige Kennwort eingegeben, woraufhin er authentifiziert wird. 

>[!Note] 
>Der ursprüngliche MD4-Hash wird nicht an Azure AD übertragen. Stattdessen wird der SHA256-Hash des ursprünglichen MD4-Hashs übertragen. Daher kann der Hash, wenn er in Azure AD gespeichert ist, nicht für einen lokalen Pass-the-Hash-Angriff verwendet werden.

### <a name="how-password-hash-synchronization-works-with-azure-active-directory-domain-services"></a>So funktioniert die Kennworthashsynchronisierung mit Azure Active Directory Domain Services
Sie können die Kennworthashsynchronisierung auch dazu verwenden, Ihre lokalen Kennwörter mit [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md) zu synchronisieren. In diesem Szenario authentifiziert die Azure Active Directory Domain Services-Instanz Ihre Benutzer in der Cloud mit allen Methoden, die auch in Ihrer lokalen Active Directory-Instanz zur Verfügung stehen. Dieses Szenario ähnelt der Verwendung des Active Directory-Migrationsprogramms in einer lokalen Umgebung.

### <a name="security-considerations"></a>Sicherheitshinweise
Beim Synchronisieren von Kennwörtern wird die Nur-Text-Version Ihres Kennworts gegenüber dem Kennworthashsynchronisierungs-Feature, Azure AD oder einem der zugehörigen Dienste nicht offengelegt.

Die Authentifizierung von Benutzern erfolgt im Abgleich mit Azure AD und nicht mit der organisationseigenen Active Directory-Instanz. Die in Azure AD gespeicherten SHA256-Kennwortdaten – ein Hash des ursprünglichen MD4-Hashs – sind sicherer als die in Active Directory gespeicherten Daten. Da dieser SHA256-Hash darüber hinaus nicht entschlüsselt werden kann, lässt er sich nicht in die Active Directory-Umgebung der Organisation zurückübertragen, um in einem Pass-the-Hash-Angriff als gültiges Benutzerkennwort vorgelegt zu werden.

### <a name="password-policy-considerations"></a>Überlegungen zur Kennwortrichtlinie
Es gibt zwei Arten von Kennwortrichtlinien, die von der Aktivierung der Kennworthashsynchronisierung betroffen sind:

* Kennwortkomplexitätsrichtlinie
* Kennwortablaufrichtlinie

#### <a name="password-complexity-policy"></a>Kennwortkomplexitätsrichtlinie  
Wenn die Kennworthashsynchronisierung aktiviert wird, setzen die Kennwortkomplexitätsrichtlinien in Ihrer lokalen Active Directory-Instanz die Komplexitätsrichtlinien in der Cloud für synchronisierte Benutzer außer Kraft. Sie können alle gültigen Kennwörter Ihrer lokalen Active Directory-Instanz für den Zugriff auf Azure AD-Dienste verwenden.

> [!NOTE]
> Kennwörter für Benutzer, die direkt in der Cloud erstellt werden, unterliegen auch weiterhin in der Cloud definierten Kennwortrichtlinien.

#### <a name="password-expiration-policy"></a>Kennwortablaufrichtlinie  
Wenn sich ein Benutzer im Bereich der Kennworthashsynchronisierung befindet, wird das Cloudkontokennwort auf *Läuft nie ab* festgelegt.

Sie können sich mit einem synchronisierten Kennwort, das in der lokalen Umgebung abgelaufen ist, weiterhin bei Ihren Clouddiensten anmelden. Ihr Cloudkennwort wird aktualisiert, wenn Sie das Kennwort in der lokalen Umgebung das nächste Mal ändern.

#### <a name="account-expiration"></a>Kontoablauf
Wenn Ihre Organisation im Rahmen der Verwaltung von Benutzerkonten das accountExpires-Attribut verwendet, wird dieses Attribut nicht mit Azure AD synchronisiert. Deshalb bleibt ein abgelaufenes Active Directory-Konto in einer für die Kennworthashsynchronisierung konfigurierten Umgebung in Azure AD weiter aktiv. Wenn das Konto abgelaufen ist, wird eine Workflowaktion empfohlen, die ein PowerShell-Skript zum Deaktivieren des Azure AD-Kontos des Benutzers auslöst (verwenden Sie das Cmdlet [Set-AzureADUser](https://docs.microsoft.com/powershell/module/azuread/set-azureaduser?view=azureadps-2.0)). Umgekehrt sollte die Azure AD-Instanz aktiviert sein, wenn das Konto aktiviert ist.

### <a name="overwrite-synchronized-passwords"></a>Überschreiben synchronisierter Kennwörter
Ein Administrator kann Ihr Kennwort mithilfe von Windows PowerShell manuell zurücksetzen.

In diesem Fall überschreibt das neue Kennwort Ihr synchronisiertes Kennwort, und alle in der Cloud definierten Kennwortrichtlinien gelten für das neue Kennwort.

Wenn Sie das lokale Kennwort erneut ändern, wird das neue Kennwort mit der Cloud synchronisiert, und das manuell aktualisierte Kennwort wird überschrieben.

Die Synchronisierung eines Kennworts hat keinen Einfluss auf den angemeldeten Azure-Benutzer. Wenn eine synchronisierte Kennwortänderung durchgeführt wird, während Sie bei einem Clouddienst angemeldet sind, wirkt sich dies nicht direkt auf Ihre aktuelle Clouddienstsitzung aus. Durch die Einstellung „Angemeldet bleiben“ wird die Dauer dieser Differenz verlängert. Wenn für den Clouddienst eine erneute Authentifizierung erforderlich ist, müssen Sie Ihr neues Kennwort angeben.

### <a name="additional-advantages"></a>Zusätzliche Vorteile

- Im Allgemeinen ist die Kennworthashsynchronisierung einfacher zu implementieren als ein Verbunddienst. Sie erfordert keine zusätzliche Server und beseitigt die Abhängigkeit von einem hoch verfügbaren Verbunddienst zum Authentifizieren von Benutzern.
- Die Kennworthashsynchronisierung kann auch zusätzlich zum Verbund aktiviert werden. Sie kann als Fallback dienen, wenn der Verbunddienst ausfällt.

## <a name="enable-password-hash-synchronization"></a>Aktivieren der Kennworthashsynchronisierung

>[!IMPORTANT]
>Wenn Sie von AD FS (oder anderen Verbundtechnologien) zur Kennworthashsynchronisierung migrieren, wird dringend empfohlen, dem ausführlichen [Leitfaden zur Bereitstellung](https://aka.ms/adfstophsdpdownload) zu folgen.

Wenn Sie Azure AD Connect mit den **Expresseinstellungen** installieren, wird die Kennworthashsynchronisierung automatisch aktiviert. Weitere Informationen finden Sie unter [Erste Schritte mit Azure AD Connect mit Expresseinstellungen](how-to-connect-install-express.md).

Wenn Sie beim Installieren von Azure AD Connect benutzerdefinierte Einstellungen verwenden, steht die Kennworthashsynchronisierung auf der Seite „Benutzeranmeldung“ zur Verfügung. Weitere Informationen finden Sie unter [Benutzerdefinierte Installation von Azure AD Connect](how-to-connect-install-custom.md).

![Aktivieren der Kennworthashsynchronisierung](./media/how-to-connect-password-hash-synchronization/usersignin2.png)

### <a name="password-hash-synchronization-and-fips"></a>Kennworthashsynchronisierung und FIPS
Wenn Ihr Server wegen FIPS (Federal Information Processing Standard) gesperrt wurde, ist MD5 deaktiviert.

**Führen Sie zum Aktivieren von MD5 für die Kennworthashsynchronisierung die folgenden Schritte aus:**

1. Wechseln Sie zu „%programfiles%\Azure AD Sync\Bin“.
2. Öffnen Sie „miiserver.exe.config“.
3. Wechseln Sie zum Knoten „configuration/runtime“ (am Ende der Datei).
4. Fügen Sie den folgenden Knoten hinzu: `<enforceFIPSPolicy enabled="false"/>`
5. Speichern Sie die Änderungen.

Der folgende Codeausschnitt zeigt, wie dies aussehen sollte:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Informationen zu Sicherheitsfragen und FIPS finden Sie im Blogbeitrag [AAD Password Sync, Encryption and FIPS compliance](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/) (AAD-Kennwortsynchronisierung, Verschlüsselung und FIPS-Konformität).

## <a name="troubleshoot-password-hash-synchronization"></a>Problembehandlung bei der Kennworthashsynchronisierung
Wenn bei der Kennworthashsynchronisierung Probleme auftreten, finden Sie weitere Informationen unter [Problembehandlung bei der Kennworthashsynchronisierung](tshoot-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Nächste Schritte
* [Azure AD Connect-Synchronisierung: Grundlagen und Anpassung der Synchronisierung](how-to-connect-sync-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md)
* [Sehen Sie sich einen ausführlichen Bereitstellungsplan für die Migration von AD FS zur Kennworthashsynchronisierung an.](https://aka.ms/authenticationDeploymentPlan)
