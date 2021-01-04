---
title: Planen einer Bereitstellung mit kennwortloser Authentifizierung mit Azure AD
description: Hier erfahren Sie, wie Sie eine kennwortlose Azure Active Directory-Authentifizierungsimplementierung planen und bereitstellen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: baselden
author: justinha
manager: daveba
ms.reviewer: baselden, librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: a786907c5c954aa45de266b6d92dd47867a8445d
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743614"
---
# <a name="plan-a-passwordless-authentication-deployment-in-azure-active-directory"></a>Planen einer Bereitstellung mit kennwortloser Authentifizierung in Azure Active Directory

> [!NOTE]
> Um eine Offlineversion dieses Bereitstellungsplans zu erstellen, verwenden Sie die Browserfunktion zum Drucken als PDF-Datei.

Die meisten Cyberangriffe beginnen mit einem kompromittierten Benutzernamen und Kennwort. Organisationen versuchen, der Bedrohung mit folgenden Konzepten zu begegnen:

- Lange Kennwörter
- Komplexe Kennwörter
- Häufige Kennwortänderungen
- Multi-Factor Authentication (MFA)

Laut [Untersuchungen von Microsoft](https://aka.ms/passwordguidance) sind diese Maßnahmen ein Ärgernis für Benutzer und führen zu höheren Supportkosten. Weitere Informationen finden Sie unter [Your Pa$$word doesn’t matter](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984) (Ihr Kennwort spielt keine Rolle).

### <a name="benefits-of-passwordless-authentication"></a>Vorteile der kennwortlosen Authentifizierung

- **Erhöhte Sicherheit:** Reduziertes Risiko von Phishing- und Kennwort-Spray-Angriffen, da Kennwörter als Angriffsfläche entfallen.
-  **Höhere Benutzerfreundlichkeit:** Stellen Sie Benutzern eine bequeme Möglichkeit bereit, von überall aus auf Daten zuzugreifen. Bieten Sie unkomplizierten mobilen Zugriff auf Anwendungen und Dienste wie Outlook, OneDrive oder Office.
-  **Verlässliche Einblicke:** Sie erhalten Einblicke in die kennwortlose Aktivität der Benutzer mit stabiler Protokollierung und Überwachung.

Beim kennwortlosen Zugriff wird das Kennwort durch etwas ersetzt, das Sie haben, plus etwas, das Sie sind oder wissen. In Windows Hello for Business wäre das beispielsweise eine biometrische Geste wie etwa ein Gesicht oder Fingerabdruck oder eine gerätespezifische PIN, die nicht über ein Netzwerk übertragen wird.

## <a name="passwordless-authentication-methods"></a>Methoden zur kennwortlosen Authentifizierung
Microsoft bietet drei Optionen für die kennwortlose Authentifizierung, die viele Szenarios abdecken. Diese Methoden können miteinander kombiniert werden:

- [Windows Hello for Business-](./concept-authentication-passwordless.md) eignet sich am besten für Benutzer von dedizierten Windows-Computern.
- Die Sicherheitsschlüsselanmeldung mit [FIDO2-Sicherheitsschlüsseln](./concept-authentication-passwordless.md) ist besonders nützlich für Benutzer, die sich bei gemeinsam genutzten Computern wie Kiosken anmelden, sowie in Situationen, in denen die Verwendung von Telefonen eingeschränkt ist, und für hochprivilegierte Identitäten.
- Die Anmeldung per Telefon mit der [Microsoft Authenticator-App](./concept-authentication-passwordless.md) ist hilfreich, um für Benutzer mit mobilen Geräten eine kennwortlose Option bereitzustellen. Die Authenticator-App verwandelt jedes iOS- oder Android-Smartphone in eine sichere, kennwortlose Anmeldeinformation und ermöglicht es Benutzern, sich bei beliebigen Plattformen oder Browsern anzumelden. Für die Anmeldung erhalten Benutzer eine Benachrichtigung auf ihrem Smartphone, gleichen eine auf dem Bildschirm angezeigte Zahl mit der Zahl auf ihrem Smartphone ab und verwenden dann zur Bestätigung ihre biometrischen Daten oder ihre PIN.

### <a name="passwordless-authentication-scenarios"></a>Szenarien der kennwortlosen Authentifizierung

Die Methoden zur kennwortlosen Authentifizierung von Microsoft ermöglichen verschiedene Szenarien. Beachten Sie die Anforderungen Ihrer Organisation, die Voraussetzungen und die Funktionen der einzelnen Authentifizierungsmethoden, um Ihre Strategie für die kennwortlose Authentifizierung auszuwählen. Jede Organisation, die Windows 10-Geräte verwendet, sollte Windows Hello for Business verwenden. Fügen Sie dann entweder die Anmeldung per Telefon (mit der Microsoft Authenticator-App) oder Sicherheitsschlüssel für weitere Szenarien hinzu.

| Szenario | Telefonauthentifizierung | Sicherheitsschlüssel | Windows Hello for Business |
| --- | --- | --- | --- |
| **Computeranmeldung**: <br> Von zugewiesenem Windows 10-Gerät | **Nein** | **Ja** <br> Mit biometrischer Erkennung, PIN | **Ja**<br>Mit biometrischer Erkennung und/oder PIN |
| **Computeranmeldung**: <br> Von gemeinsam genutztem Windows 10-Gerät | **Nein** | **Ja** <br> Mit biometrischer Erkennung, PIN  | **Nein** |
| **Web-App-Anmeldung**: <br>Von einem benutzerdedizierten Computer | **Ja** | **Ja** <br> Bereitgestelltes einmaliges Anmelden bei Apps wird durch Computeranmeldung aktiviert | **Ja**<br> Bereitgestelltes einmaliges Anmelden bei Apps wird durch Computeranmeldung aktiviert |
| **Web-App-Anmeldung**: <br> Von einem Mobilgerät oder Nicht-Windows-Gerät | **Ja** | **Nein** | **Nein** |
| **Computeranmeldung**: <br> Nicht-Windows-Computer | **Nein** | **Nein** | **Nein** |

Weitere Informationen zum Auswählen der besten Methode für Ihre Organisation finden Sie unter [Auswählen einer kennwortlosen Methode](./concept-authentication-passwordless.md#choose-a-passwordless-method).

## <a name="prerequisites"></a>Voraussetzungen

Für eine kennwortlose Bereitstellung müssen Organisationen die folgenden Voraussetzungen erfüllen:

| Voraussetzung | Authenticator-App | FIDO2-Sicherheitsschlüssel |
| --- | --- | --- |
| [Kombinierte Registrierung für Azure AD Multi-Factor Authentication und Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR)](howto-registration-mfa-sspr-combined.md) ist aktiviert. | √ | √ |
| [Benutzer können Azure AD Multi-Factor Authentication nutzen.](howto-mfa-getstarted.md) | √ | √ |
| [Benutzer haben sich für Azure AD Multi-Factor Authentication und SSPR registriert.](howto-registration-mfa-sspr-combined.md) | √ | √ |
| [Benutzer haben ihre mobilen Geräte für Azure Active Directory registriert](../devices/overview.md) | √ |   |
| Windows 10, Version 1809 oder höher, mit einem unterstützten Browser wie Microsoft Edge oder Mozilla Firefox <br> (Version 67 oder höher). <br> *Microsoft empfiehlt Version 1903 oder höher für native Unterstützung*. |   | √ |
| Kompatible FIDO2-Sicherheitsschlüssel. Stellen Sie sicher, dass Sie ein [von Microsoft getestetes und verifiziertes](./concept-authentication-passwordless.md) FIDO2-Sicherheitsgerät oder ein anderes kompatibles FIDO2-Sicherheitsgerät verwenden. |   | √ |

### <a name="prerequisites-for-windows-hello-for-business"></a>Voraussetzungen für Windows Hello for Business

Die Voraussetzungen für Windows Hello for Business sind stark davon abhängig, ob Sie die Bereitstellung in einer lokalen Konfiguration, in einer Hybridkonfiguration oder in einer reinen Cloudkonfiguration durchführen. Weitere Informationen finden Sie in der [vollständigen Liste der Voraussetzungen für Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

### <a name="azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication

Benutzer registrieren ihre kennwortlose Methode als Teil des Azure AD Multi-Factor Authentication-Registrierungsflows. Die mehrstufige Authentifizierung mit einem Benutzernamen und einem Kennwort sowie einer anderen registrierten Methode kann ersatzweise in Szenarios verwendet werden, in denen sie ihr Smartphone oder ihren Sicherheitsschlüssel nicht verwenden können.

### <a name="licensing"></a>Lizenzierung 
Es fallen keine zusätzlichen Kosten für die kennwortlose Authentifizierung an, obwohl einige Voraussetzungen möglicherweise ein Premium-Abonnement erfordern. Ausführliche Informationen zu Features und Lizenzen finden Sie unter [Azure Active Directory – Preise](https://azure.microsoft.com/pricing/details/active-directory/). 

## <a name="develop-a-plan"></a>Entwickeln eines Plans

Berücksichtigen Sie Ihre geschäftlichen Anforderungen und die Anwendungsfälle für jede Authentifizierungsmethode. Wählen Sie dann die Methode aus, die Ihren Anforderungen am besten entspricht.

### <a name="use-cases"></a>Anwendungsfälle

In der folgenden Tabelle werden die Anwendungsfälle erläutert, die während dieses Projekts implementiert werden müssen.

| Bereich | BESCHREIBUNG |
| --- | --- |
| **zugreifen** | Die kennwortlose Anmeldung ist auf einem firmeneigenen oder persönlichen Gerät innerhalb oder außerhalb des Unternehmensnetzwerks verfügbar. |
| **Überwachung** | Nutzungsdaten stehen Administratoren zur Überwachung nahezu in Echtzeit zur Verfügung. <br> Die Nutzungsdaten werden mindestens alle 29 Tage in die Unternehmenssysteme heruntergeladen, oder das SIEM-Tool (Security Information & Event Management) wird verwendet. |
| **Governance** | Der Lebenszyklus von Benutzerzuweisungen zur entsprechenden Authentifizierungsmethode und zu zugeordneten Gruppen wird definiert und überwacht. |
| **Security** | Der Zugriff auf die entsprechende Authentifizierungsmethode wird über Benutzer- und Gruppenzuweisungen gesteuert. <br> Nur autorisierte Benutzer können die kennwortlose Anmeldung verwenden. |
| **Leistung** | Die Zeitachsen für die Verteilung von Zugriffszuweisungen werden dokumentiert und überwacht. <br> Die Anmeldezeiten werden zur einfachen Nutzung gemessen. |
| **Benutzerfreundlichkeit** | Die Benutzer kennen die mobile Kompatibilität. <br> Benutzer können die Authenticator-App für die kennwortlose Anmeldung konfigurieren. |
| **Unterstützung** | Benutzer wissen, wie sie Unterstützung bei Problemen mit der kennwortlosen Anmeldung erhalten. |

### <a name="engage-the-right-stakeholders"></a>Einbeziehen der richtigen Beteiligten

Wenn Technologieprojekte nicht gelingen, ist dies in der Regel auf nicht erfüllte Erwartungen auf den Gebieten Auswirkungen, Ergebnisse und Zuständigkeiten zurückzuführen. Um diese Fallstricke zu vermeiden, stellen Sie sicher, dass Sie die [richtigen Beteiligten hinzuziehen](../fundamentals/active-directory-deployment-plans.md#include-the-right-stakeholders) und dass die Rollen der Beteiligten im Projekt gut verstanden werden.

### <a name="plan-communications"></a>Planen der Benachrichtigungen

Kommunikation ist ein kritischer Faktor für den Erfolg jedes neuen Diensts. Kommunizieren Sie proaktiv darüber, wie sich die Erfahrung der Benutzer ändern wird, wann sie sich ändert und wie sie Unterstützung erhalten, wenn sie auf Probleme stoßen.

Ihre Kommunikation mit Endbenutzern muss folgende Informationen enthalten:

- [Aktivieren der kombinierten Sicherheitsregistrierung](howto-authentication-passwordless-phone.md)
- [Herunterladen der Microsoft Authenticator-App](../user-help/user-help-auth-app-download-install.md)
- [Registrieren der Microsoft Authenticator-App](howto-authentication-passwordless-phone.md)
- [Anmelden per Telefon](../user-help/user-help-auth-app-sign-in.md)

Microsoft bietet MFA-[Kommunikationsvorlagen](https://aka.ms/mfatemplates), [Kommunikationsvorlagen](https://www.microsoft.com/download/details.aspx?id=56768) für die Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR) und eine [Endbenutzerdokumentation](../user-help/security-info-setup-signin.md), womit Sie Ihre Kommunikation entwerfen können. Sie können Benutzer auf [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) verweisen, wo sie sich durch Auswahl der **Sicherheitsinformation**-Links auf dieser Seite direkt registrieren können.

### <a name="plan-to-pilot"></a>Planen eines Pilotversuchs

Wenn Sie die kennwortlose Authentifizierung bereitstellen, sollten Sie zunächst mindestens eine Pilotgruppe aktivieren. Sie können speziell zu diesem Zweck [Gruppen erstellen](../fundamentals/active-directory-groups-create-azure-portal.md). Fügen Sie die Benutzer, die am Pilotversuch teilnehmen, den Gruppen hinzu. Aktivieren Sie dann neue kennwortlose Authentifizierungsmethoden für die ausgewählten Gruppen.

Gruppen können aus einem lokalen Verzeichnis oder Azure AD synchronisiert werden. Sobald Sie mit den Ergebnissen Ihres Pilotversuchs zufrieden sind, können Sie die kennwortlose Authentifizierung für alle Benutzer aktivieren.

Weitere Informationen finden Sie auf der Bereitstellungspläneseite unter [Bewährte Methoden für einen Pilotversuch](../fundamentals/active-directory-deployment-plans.md).

## <a name="plan-passwordless-authentication-with-the-microsoft-authenticator-app"></a>Planen der kennwortlosen Authentifizierung mit der Microsoft Authenticator-App

Die Microsoft Authenticator-App ist als kostenloser Download bei Google Play und im Apple App Store verfügbar. Erfahren Sie mehr über das [Herunterladen der Microsoft Authenticator-App](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). Lassen Sie die Benutzer die Microsoft Authenticator-App herunterladen. Befolgen Sie die Anweisungen zum Aktivieren der Anmeldung per Telefon. 

Sie wandelt jedes iOS- oder Android-Telefon in sichere kennwortlose Anmeldeinformationen um. Benutzer können sich bei jeder beliebigen Plattform oder jedem beliebigen Browser anmelden, indem sie eine Benachrichtigung auf ihrem Telefon erhalten, eine auf dem Bildschirm angezeigte Zahl mit der Zahl auf dem Telefon abgleichen und dann ihre biometrischen Daten oder ihre PIN zur Bestätigung verwenden. [Erfahren Sie, wie die Microsoft Authenticator-App funktioniert](./concept-authentication-passwordless.md#microsoft-authenticator-app).

![Anmelden mit der Authenticator-App](./media/howto-authentication-passwordless-deployment/passwordless-dp-sign-in.png)

### <a name="technical-considerations-for-the-microsoft-authenticator-app"></a>Technische Überlegungen zur Microsoft Authenticator-App

**Integration der Active Directory-Verbunddienste (AD FS)** : Wenn ein Benutzer die kennwortlosen Microsoft Authenticator-Anmeldeinformationen aktiviert, wird die Authentifizierung für diesen Benutzer standardmäßig auf das Senden einer Genehmigungsbenachrichtigung festgelegt. Benutzer in einem hybriden Mandanten werden für die Anmeldung nicht zu AD FS weitergeleitet, es sei denn, sie wählen „Stattdessen Ihr Kennwort verwenden“ aus. Bei diesem Prozess werden auch alle lokalen Richtlinien für bedingten Zugriff und Passthrough-Authentifizierungsflüsse umgangen. Wenn jedoch ein *Anmeldehinweis* (login_hint) angegeben ist, wird ein Benutzer zu AD FS weitergeleitet, und die Option zur Verwendung von kennwortlosen Anmeldeinformationen wird umgangen.

**Azure AD MFA-Server**: Für die mehrstufige Authentifizierung über den lokalen Azure MFA-Server einer Organisation aktivierte Endbenutzer können einen einzelnen Satz von Anmeldeinformationen für die kennwortlose Anmeldung per Telefon erstellen und verwenden. Wenn der Benutzer versucht, mehrere Installationen (fünf oder mehr) von Microsoft Authenticator mit den Anmeldeinformationen zu aktualisieren, kann diese Änderung zu einem Fehler führen.

**Geräteregistrierung**: Um die Authenticator-App für die kennwortlose Authentifizierung zu verwenden, muss das Gerät im Azure AD-Mandanten registriert sein und darf kein gemeinsam genutztes Gerät sein. Ein Gerät kann nur bei einem einzigen Mandanten registriert werden. Das bedeutet, dass nur ein einzelnes Geschäfts-, Schul- oder Unikonto für die Anmeldung per Telefon über die Authenticator-App unterstützt wird.

## <a name="plan-passwordless-authentication-with-fido2-security-keys"></a>Planen der kennwortlosen Authentifizierung mit FIDO2-Sicherheitsschlüsseln
Es gibt drei Arten von Bereitstellungen der kennwortlosen Anmeldung mit Sicherheitsschlüsseln:

-    Azure Active Directory-Web-Apps in einem unterstützten Browser
-    In Azure Active Directory eingebundene Windows 10-Geräte
-    Windows 10-Geräte mit Azure Active Directory-Hybrideinbindung (Vorschauversion)
     -    Sie ermöglichen den Zugriff auf cloudbasierte und lokale Ressourcen. Weitere Informationen zum Zugriff auf lokale Ressourcen finden Sie unter [Aktivieren der kennwortlosen Anmeldung mit Sicherheitsschlüsseln bei lokalen Ressourcen mit Azure Active Directory (Vorschauversion)](./howto-authentication-passwordless-security-key-on-premises.md).

Sie müssen **kompatible FIDO2-Sicherheitsschlüssel** aktivieren. Microsoft hat [Schlüsselpartnerschaften mit FIDO2-Schlüsselanbietern](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Microsoft-passwordless-partnership-leads-to-innovation-and-great/ba-p/566493) angekündigt.

**Für Azure AD-Web-Apps und Windows-Geräte mit Azure AD-Einbindung:**

-    Windows 10 Version 1809 oder höher mit einem unterstützten Browser wie Microsoft Edge oder Mozilla Firefox (ab Version 67) 
-    Windows 10 Version 1809 unterstützt die FIDO2-Anmeldung und erfordert möglicherweise die Bereitstellung von Software des FIDO2-Schlüsselanbieters. Es wird die Verwendung von mindestens Version 1903 empfohlen. 

**Geräte mit Azure Active Directory-Hybriddomäneneinbindung:** 
-    Windows 10, Version 2004 oder höher
-    Vollständig gepatchte Domänenserver unter Windows Server 2016 oder 2019.
-    Neue Version von Azure AD Connect

Eine vollständige Liste der Anforderungen finden Sie unter [Aktivieren der kennwortlosen Anmeldung mit Sicherheitsschlüsseln bei Windows 10-Geräten mit Azure Active Directory](./howto-authentication-passwordless-security-key-windows.md#requirements).


### <a name="security-key-life-cycle"></a>Lebenszyklus von Sicherheitsschlüsseln

Sicherheitsschlüssel ermöglichen den Zugriff auf Ihre Ressourcen, und Sie sollten die Verwaltung dieser physischen Geräte planen.

1. **Schlüsselverteilung:** Planen Sie, wie Schlüssel für Ihre Organisation bereitgestellt werden sollen. Möglicherweise verfügen Sie über einen zentralisierten Bereitstellungsprozess oder erlauben den Endbenutzern, mit FIDO 2.0 kompatible Schlüssel zu erwerben.
1. **Schlüsselaktivierung:** Endbenutzer müssen den Sicherheitsschlüssel selbst aktivieren. Endbenutzer registrieren ihre Sicherheitsschlüssel unter [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) und aktivieren den zweiten Faktor (PIN oder biometrisch) bei der ersten Verwendung.
1. **Deaktivieren eines Schlüssels:** Die Sicherheitsschlüsselfunktionalität befindet sich zwar in der Vorschauphase, aber ein Administrator kann keinen Schlüssel aus einem Benutzerkonto entfernen. Der Benutzer muss ihn entfernen. Wenn ein Schlüssel verloren geht oder gestohlen wird:
   1. Entfernen Sie den Benutzer aus jeder Gruppe, für die die kennwortlose Authentifizierung aktiviert ist.
   1. Vergewissern Sie sich, dass der Schlüssel als Authentifizierungsmethode entfernt wurde.
   1. Geben Sie einen neuen Schlüssel aus. **Schlüsselersetzung:** Benutzer können zwei Sicherheitsschlüssel gleichzeitig aktivieren. Wenn Sie einen Sicherheitsschlüssel ersetzen, stellen Sie sicher, dass der Benutzer den zu ersetzenden Schlüssel auch entfernt hat.

### <a name="enable-windows-10-support"></a>Aktivieren von Windows 10-Unterstützung

Um die Windows 10-Anmeldung mit FIDO2-Sicherheitsschlüsseln zu aktivieren, muss die Anmeldeinformationsanbieter-Funktionalität in Windows 10 aktiviert werden. Wählen Sie eine der folgenden Optionen aus:

- [Aktivieren des Anmeldeinformationsanbieters mit Intune](howto-authentication-passwordless-security-key-windows.md#enable-with-intune)
   - Die Intune-Bereitstellung ist die empfohlene Option.
- [Aktivieren des Anmeldeinformationsanbieters mittels eines Bereitstellungspakets](howto-authentication-passwordless-security-key-windows.md#enable-with-a-provisioning-package)
   - Wenn die Intune-Bereitstellung nicht möglich ist, müssen Administratoren auf jedem Computer ein Paket bereitstellen, um die Anmeldeinformationsanbieter-Funktionalität zu aktivieren. Die Paketinstallation kann mit einer der folgenden Optionen ausgeführt werden:
      - Gruppenrichtlinie oder Configuration Manager
      - Lokale Installation auf einem Windows 10-Computer
- [Aktivieren mit Gruppenrichtlinie](howto-authentication-passwordless-security-key-windows.md#enable-with-group-policy)
   - Diese Option wird nur für Geräte mit Azure AD-Hybrideinbindung unterstützt.

#### <a name="enable-on-premises-integration"></a>Aktivieren der lokalen Integration

Gehen Sie wie unter [Aktivieren der kennwortlosen Anmeldung mit Sicherheitsschlüsseln bei lokalen Ressourcen mit Azure Active Directory (Vorschauversion)](howto-authentication-passwordless-security-key-on-premises.md) beschrieben vor, um den Zugriff auf lokale Ressourcen zu ermöglichen.

> [!IMPORTANT]
> Diese Schritte müssen auch für alle Geräte mit Azure AD-Hybrideinbindung ausgeführt werden, um FIDO2-Sicherheitsschlüssel für die Windows 10-Anmeldung verwenden zu können.

### <a name="register-security-keys"></a>Registrieren von Sicherheitsschlüsseln

Benutzer müssen ihren Sicherheitsschlüssel auf jedem ihrer Windows 10-Computer mit Azure Active Directory-Einbindung registrieren.

Weitere Informationen finden Sie unter [Benutzerregistrierung und Verwaltung von FIDO2-Sicherheitsschlüsseln](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys).


## <a name="plan-auditing-security-and-testing"></a>Planen von Überwachung, Sicherheit und Tests
Die Planung einer Überwachung, die Ihren Organisations- und Complianceframeworks entspricht, ist ein wesentlicher Aspekt Ihrer Bereitstellung.

### <a name="auditing-passwordless"></a>Überwachen der kennwortlosen Authentifizierung

Azure AD stellt Berichte zur Verfügung, die technische und geschäftliche Einblicke bieten. Sorgen Sie dafür, dass die Besitzer Ihrer geschäftlichen und technischen Anwendungen den Besitz dieser Berichte übernehmen und sie den Anforderungen Ihrer Organisation gemäß nutzen.

Im Abschnitt **Authentifizierungsmethoden** des Azure Active Directory-Portals können Administratoren Einstellungen für kennwortlose Anmeldeinformationen aktivieren und verwalten.

Azure AD fügt den Überwachungsprotokollen in folgenden Fällen Einträge hinzu:

- Ein Administrator nimmt im Authentifizierungsmethoden-Abschnitt Änderungen vor.
- Ein Benutzer nimmt in Azure Active Directory Änderungen an seinen Anmeldeinformationen vor.

Die folgende Tabelle enthält einige Beispiele für typische Berichtsszenarien:

|   | Risikomanagement | Steigerung der Produktivität | Governance und Einhaltung |
| --- | --- | --- | --- |
| **Berichtstypen** | Authentifizierungsmethoden: Benutzer, die für die kombinierte Sicherheitsregistrierung registriert sind | Authentifizierungsmethoden: Benutzer, die für die App-Benachrichtigung registriert sind | Anmeldungen: Überprüfen, wer wie auf den Mandanten zugreift |
| **Mögliche Aktionen** | Zielbenutzer noch nicht registriert | Übernahme von Microsoft Authenticator-App oder Sicherheitsschlüsseln steuern | Zugriff widerrufen oder zusätzliche Sicherheitsrichtlinien für Administratoren erzwingen |

**Azure AD speichert die meisten Überwachungsdaten 30 Tage** und stellt sie per Azure-Verwaltungsportal oder API zur Verfügung, damit Sie sie in Ihre Analysesysteme herunterladen können. Wenn Sie einen längeren Aufbewahrungszeitraum benötigen, exportieren und nutzen Sie Protokolle in einem SIEM-Tool wie [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk oder Sumo Logic. [Erfahren Sie mehr über das Anzeigen von Zugriffs- und Verwendungsberichten](../reports-monitoring/overview-reports.md).

Um ihre Anmeldeinformationen zu registrieren und zu verwalten, können Benutzer zu [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) navigieren. Dieser Link leitet Benutzer zur Benutzeroberfläche für die Verwaltung von Endbenutzer-Anmeldeinformationen, die über die kombinierte SSPR/MFA-Registrierung aktiviert wurde. Azure AD protokolliert die Registrierung von FIDO2-Sicherheitsgeräten sowie Änderungen an den Authentifizierungsmethoden durch Benutzer.

### <a name="plan-security"></a>Planen der Sicherheit
Im Rahmen dieses Rolloutplans empfiehlt Microsoft, die kennwortlose Authentifizierung für alle privilegierten Administratorkonten zu aktivieren.

Wenn Benutzer das Konto für einen Sicherheitsschlüssel aktivieren oder deaktivieren bzw. den zweiten Faktor für den Sicherheitsschlüssel auf ihren Windows 10-Computern zurücksetzen, wird dem Sicherheitsprotokoll unter den folgenden Ereignis-IDs ein Eintrag hinzugefügt: *4670* und *5382*.

### <a name="plan-testing"></a>Planen von Tests

Vergewissern Sie sich in jeder Phase der Bereitstellung, dass alles wie erwartet funktioniert, indem Sie Szenarien und die Einführung testen.

#### <a name="testing-the-microsoft-authenticator-app"></a>Testen der Microsoft Authenticator-App

Im Anschluss finden Sie Beispieltestfälle für die kennwortlose Authentifizierung mit der Microsoft Authenticator-App:

| Szenario | Erwartete Ergebnisse |
| --- | --- |
| Benutzer kann Microsoft Authenticator-App registrieren | Benutzer kann App über „aka.ms/mysecurityinfo“ registrieren |
| Benutzer kann die Anmeldung per Telefon aktivieren | Anmeldung per Telefon ist für das Geschäftskonto konfiguriert |
| Benutzer kann auf eine App mit Anmeldung per Telefon zugreifen | Benutzer durchläuft die Anmeldung per Telefon und erreicht die Anwendung. |
| Testen des Rollbacks der Registrierung für die Anmeldung per Telefon durch Deaktivieren der kennwortlosen Microsoft Authenticator-Anmeldung im Bildschirm „Authentifizierungsmethoden“ des Azure Active Directory-Portals | Zuvor aktivierte Benutzer können die kennwortlose Anmeldung über Microsoft Authenticator nicht mehr verwenden. |
| Entfernen der Anmeldung per Telefon aus der Microsoft Authenticator-App | Geschäftskonto ist auf Microsoft Authenticator nicht mehr verfügbar |

#### <a name="testing-security-keys"></a>Testen von Sicherheitsschlüsseln

Im Folgenden finden Sie Beispieltestfälle für die kennwortlose Authentifizierung mit Sicherheitsschlüsseln.

**Kennwortlose FIDO-Anmeldung bei Windows 10-Geräten mit Azure Active Directory-Einbindung**

| Szenario | Erwartete Ergebnisse |
| --- | --- |
| Der Benutzer kann das FIDO2-Gerät registrieren (1809) | Der Benutzer kann das FIDO2-Gerät unter „Einstellungen > Konten > Anmeldeoptionen > Sicherheitsschlüssel“ registrieren |
| Der Benutzer kann das FIDO2-Gerät zurücksetzen (1809) | Der Benutzer kann das FIDO2-Gerät mithilfe der Herstellersoftware zurücksetzen |
| Der Benutzer kann sich mit dem FIDO2-Gerät (1809) anmelden | Der Benutzer kann den Sicherheitsschlüssel im Anmeldefenster auswählen und sich erfolgreich anmelden. |
| Der Benutzer kann das FIDO2-Gerät registrieren (1903) | Der Benutzer kann das FIDO2-Gerät unter „Einstellungen > Konten > Anmeldeoptionen > Sicherheitsschlüssel“ registrieren |
| Der Benutzer kann das FIDO2-Gerät zurücksetzen (1903) | Der Benutzer kann das FIDO2-Gerät unter „Einstellungen > Konten > Anmeldeoptionen > Sicherheitsschlüssel“ zurücksetzen |
| Der Benutzer kann sich mit dem FIDO2-Gerät (1903) anmelden | Der Benutzer kann den Sicherheitsschlüssel im Anmeldefenster auswählen und sich erfolgreich anmelden. |

**Kennwortlose FIDO-Anmeldung bei Azure AD-Web-Apps**

| Szenario | Erwartete Ergebnisse |
| --- | --- |
| Der Benutzer kann das FIDO2-Gerät über „aka.ms/mysecurityinfo“ mit Microsoft Edge registrieren | Registrierung sollte erfolgreich sein |
| Der Benutzer kann das FIDO2-Gerät über „aka.ms/mysecurityinfo“ mit Firefox registrieren | Registrierung sollte erfolgreich sein |
| Der Benutzer kann sich mithilfe des FIDO2-Geräts über Microsoft Edge bei OneDrive online anmelden | Anmeldung sollte erfolgreich sein |
| Der Benutzer kann sich mithilfe des FIDO2-Geräts über Firefox bei OneDrive online anmelden | Anmeldung sollte erfolgreich sein |
| Testen des Rollbacks der FIDO2-Geräteregistrierung durch Deaktivieren der FIDO2-Sicherheitsschlüssel im Fenster mit den Authentifizierungsmethoden im Azure Active Directory-Portal | Die Benutzer werden aufgefordert, sich mit ihrem Sicherheitsschlüssel anzumelden. Die Benutzer melden sich erfolgreich an, und es wird ein Fehler angezeigt: „Für Ihre Unternehmensrichtlinie müssen Sie eine andere Methode für die Anmeldung verwenden.“ Benutzer sollten dann in der Lage sein, eine andere Methode auszuwählen und sich erfolgreich anzumelden. Schließen Sie das Fenster, und melden Sie sich erneut an, um zu überprüfen, ob diese Fehlermeldung nicht angezeigt wird. |

### <a name="plan-for-rollback"></a>Planen der Zurücksetzung

Obwohl die kennwortlose Authentifizierung ein einfaches Feature mit minimalen Auswirkungen auf Endbenutzer ist, muss möglicherweise ein Rollback ausgeführt werden.

Für das Rollback muss sich der Administrator beim Azure Active Directory-Portal anmelden, die gewünschten sicheren Authentifizierungsmethoden auswählen und die Aktivierungsoption in **Nein** ändern. Dadurch wird die kennwortlose Funktion für alle Benutzer deaktiviert.

Benutzer, die bereits FIDO2-Sicherheitsgeräte registriert haben, werden bei der nächsten Anmeldung aufgefordert, das Sicherheitsgerät zu verwenden, und die folgende Fehlermeldung wird angezeigt:

![Auswählen einer anderen Anmeldeoption](./media/howto-authentication-passwordless-deployment/passwordless-choose-sign-in.png)

## <a name="deploy-and-troubleshoot-passwordless-authentication"></a>Bereitstellen und Beheben von Problemen bei der kennwortlosen Authentifizierung

Befolgen Sie die unten aufgeführten Schritte, die der Methode Ihrer Wahl entsprechen.

### <a name="required-administrative-roles"></a>Erforderliche Administratorrollen

| Azure AD-Rolle | BESCHREIBUNG |
| --- | --- |
| Globaler Administrator|Die am wenigsten privilegierte Rolle kann die kombinierte Registrierung implementieren. |
| Authentifizierungsadministrator | Die am wenigsten privilegierte Rolle, die Authentifizierungsmethoden implementieren und verwalten kann. |
| Benutzer | Die am wenigsten privilegierte Rolle zum Konfigurieren der Authenticator-App auf dem Gerät oder zum Registrieren eines Sicherheitsschlüsselgeräts für die Web- oder Windows 10-Anmeldung. |

### <a name="deploy-phone-sign-in-with-the-microsoft-authenticator-app"></a>Bereitstellen der Anmeldung per Telefon mit der Microsoft Authenticator-App

Führen Sie die Schritte im Artikel [Aktivieren der kennwortlosen Anmeldung mit der Microsoft Authenticator-App (Vorschauversion)](howto-authentication-passwordless-phone.md) aus, um die Microsoft Authenticator-App als kennwortlose Authentifizierungsmethode in Ihrer Organisation zu aktivieren.

### <a name="deploy-fido2-security-key-sign-in"></a>Bereitstellen der FIDO2-Sicherheitsschlüsselanmeldung

Führen Sie die Schritte im Artikel [Aktivieren der kennwortlosen Anmeldung mit Sicherheitsschlüsseln bei Azure AD](howto-authentication-passwordless-security-key.md) aus, um FIDO2-Sicherheitsschlüssel als kennwortlose Authentifizierungsmethoden zu aktivieren.

### <a name="troubleshoot-phone-sign-in"></a>Behandeln von Problemen bei der Anmeldung per Telefon

| Szenario | Lösung |
| --- | --- |
| Der Benutzer kann keine kombinierte Registrierung ausführen. | Sicherstellen, dass die [kombinierte Registrierung](concept-registration-mfa-sspr-combined.md) aktiviert ist. |
| Der Benutzer kann die Anmeldung per Telefon in der Authenticator-App nicht aktivieren. | Sicherstellen, dass sich der Benutzer im Gültigkeitsbereich für die Bereitstellung befindet. |
| Der Benutzer befindet sich NICHT im Gültigkeitsbereich für die kennwortlose Authentifizierung. Trotzdem wird ihm die Option für die kennwortlose Anmeldung angezeigt, er kann sie jedoch nicht abschließen. | Dieses Szenario tritt ein, wenn der Benutzer vor der Erstellung der Richtlinie die telefonische Anmeldung in der Anwendung aktiviert hat. <br> *So aktivieren Sie die Anmeldung* Fügen Sie den Benutzer dem Bereich der Benutzer hinzu, für die die kennwortlose Anmeldung aktiviert ist. <br> *So blockieren Sie die Anmeldung* Veranlassen Sie, dass der Benutzer seine Anmeldeinformationen aus der Anwendung entfernt. |

### <a name="troubleshoot-security-key-sign-in"></a>Behandeln von Problemen bei der Anmeldung mit Sicherheitsschlüssel

| Szenario | Lösung |
| --- | --- |
| Der Benutzer kann keine kombinierte Registrierung ausführen. | Sicherstellen, dass die [kombinierte Registrierung](concept-registration-mfa-sspr-combined.md) aktiviert ist. |
| Der Benutzer kann in seinen [Sicherheitseinstellungen](https://aka.ms/mysecurityinfo) keinen Sicherheitsschlüssel hinzufügen. | Sicherstellen, dass [Sicherheitsschlüssel](howto-authentication-passwordless-security-key.md) aktiviert sind. |
| Der Benutzer kann in den Windows 10-Anmeldeoptionen keinen Sicherheitsschlüssel hinzufügen. | [Sicherstellen, dass Sicherheitsschlüssel für die Windows-Anmeldung vorhanden sind](./concept-authentication-passwordless.md) |
| **Fehlermeldung**: Wir haben festgestellt, dass dieser Browser oder dieses Betriebssystem keine FIDO2-Sicherheitsschlüssel unterstützt. | Kennwortlose FIDO2-Sicherheitsgeräte können nur in unterstützten Browsern (Microsoft Edge, Firefox-Version 67) unter Windows 10, Version 1809 oder höher, registriert werden. |
| **Fehlermeldung**: Für Ihre Unternehmensrichtlinie müssen Sie eine andere Anmeldungsmethode verwenden. | Unsichere Sicherheitsschlüssel sind im Mandanten aktiviert. |
| Benutzer kann meinen Sicherheitsschlüssel unter Windows 10, Version 1809 nicht verwalten | Version 1809 erfordert, dass Sie die vom FIDO2-Schlüsselanbieter bereitgestellte Sicherheitsschlüssel-Verwaltungssoftware verwenden. Bitten Sie den Anbieter um Support. |
| Mein FIDO2-Sicherheitsschlüssel ist möglicherweise fehlerhaft. Wie kann ich ihn testen? | Navigieren Sie zu [https://webauthntest.azurewebsites.net/](https://webauthntest.azurewebsites.net/), geben Sie Anmeldeinformationen für ein Testkonto ein, stecken Sie den möglicherweise fehlerhaften Sicherheitsschlüssel ein, wählen Sie rechts oben auf dem Bildschirm die Schaltfläche **+** aus, klicken Sie auf „Erstellen“, und durchlaufen Sie den Erstellungsvorgang. Wenn in diesem Szenario ein Fehler auftritt, ist Ihr Gerät möglicherweise fehlerhaft. |

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren der kennwortlosen Anmeldung mit Sicherheitsschlüsseln bei Azure AD](howto-authentication-passwordless-security-key.md)
- [Aktivieren der kennwortlosen Anmeldung mit der Microsoft Authenticator-App (Vorschauversion)](howto-authentication-passwordless-phone.md)
- [Authentifizierungsmethoden: Nutzung und Erkenntnisse](howto-authentication-methods-usage-insights.md)