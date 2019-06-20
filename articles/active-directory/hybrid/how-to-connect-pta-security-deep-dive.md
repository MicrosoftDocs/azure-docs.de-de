---
title: Azure Active Directory-Passthrough-Authentifizierung – ausführliche Informationen zur Sicherheit | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie die Azure Active Directory-Passthrough-Authentifizierung (Azure AD) Ihre lokalen Konten schützt.
services: active-directory
keywords: Passthrough-Authentifizierung mit Azure AD Connect, Active Directory installieren, erforderliche Komponenten für Azure AD, SSO, einmaliges Anmelden
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f5e2443a285e065426e3dba0312ef6420097ef1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60348064"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory-Passthrough-Authentifizierung – ausführliche Informationen zur Sicherheit

In diesem Artikel wird die Funktionsweise der Passthrough-Authentifizierung von Azure Active Directory (Azure AD) ausführlich beschrieben. Der Schwerpunkt liegt auf den Sicherheitsaspekten des Features. Dieser Artikel ist für Sicherheits- und IT-Administratoren, Chief Compliance und Security Officers und andere IT-Profis interessant, die in kleineren bis mittelgroßen Unternehmen oder in großen Konzernen für die Bereiche IT-Sicherheit und Compliance verantwortlich sind.

Die folgenden Themen werden behandelt:
- Ausführliche technische Informationen darüber, wie die Authentifizierungs-Agents installiert und registriert werden
- Ausführliche technische Informationen über die Verschlüsselung von Kennwörtern bei der Benutzeranmeldung
- Die Sicherheit der Kanäle zwischen lokalen Authentifizierungs-Agents und Azure AD
- Ausführliche technische Informationen darüber, wie die Authentifizierungs-Agents während des Betriebs geschützt bleiben
- Andere sicherheitsbezogene Themen

## <a name="key-security-capabilities"></a>Wichtige Sicherheitsfunktionen

Dies sind die wichtigsten Sicherheitsaspekte des Features:
- Es basiert auf einer sicheren mehrinstanzenfähigen Architektur, die eine Isolation von Anmeldeanforderungen zwischen Mandanten ermöglicht.
- Lokale Kennwörter werden niemals in irgendeiner Form in der Cloud gespeichert.
- Lokale Authentifizierungs-Agents, die auf Anforderungen zur Kennwortvalidierung lauschen und darauf antworten, stellen aus Ihrem Netzwerk nur ausgehende Verbindungen her. Es ist nicht erforderlich, diese Authentifizierungs-Agents in einem Umkreisnetzwerk (DMZ) zu installieren. Eine bewährte Methode ist die Behandlung aller Server, auf denen Authentifizierungs-Agents ausgeführt werden, als Ebene-0-Systeme (siehe [Referenz](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)).
- Nur Standardports (80 und 443) werden für die ausgehende Kommunikation von den Authentifizierungs-Agents mit Azure AD verwendet. Sie müssen keine eingehenden Ports in Ihrer Firewall öffnen. 
  - Port 443 wird für die gesamte authentifizierte ausgehende Kommunikation verwendet.
  - Port 80 wird nur zum Herunterladen der Zertifikatsperrlisten (Certificate Revocation Lists, CRLs) verwendet, um sicherzustellen, dass keines der von diesem Feature verwendeten Zertifikate widerrufen wurde.
  - Die vollständige Liste der Netzwerkanforderungen finden Sie unter [Azure Active Directory-Passthrough-Authentifizierung: Schnellstart](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites).
- Kennwörter, die Benutzer während einer Anmeldung angeben, werden in der Cloud verschlüsselt, bevor sie von den lokalen Authentifizierungs-Agents zur Validierung für Active Directory akzeptiert werden.
- Der HTTPS-Kanal zwischen Azure AD und dem lokalen Authentifizierungs-Agent wird durch Verwenden von gegenseitiger Authentifizierung geschützt.
- Ihre Benutzerkonten werden durch die nahtlose Kompatibilität mit [Azure AD-Richtlinien für bedingten Zugriff](../active-directory-conditional-access-azure-portal.md), einschließlich der Multi-Factor Authentication (MFA), durch das [Blockieren der Legacyauthentifizierung](../conditional-access/conditions.md) und durch das [Herausfiltern von Brute-Force-Kennwortangriffen](../authentication/howto-password-smart-lockout.md) geschützt.

## <a name="components-involved"></a>Beteiligte Komponenten

Allgemeine Informationen zur Betriebs-, Dienst- und Datensicherheit von Azure AD finden Sie im [Trust Center](https://azure.microsoft.com/support/trust-center/). Die folgenden Komponenten sind beteiligt, wenn Sie Passthrough-Authentifizierung für Benutzeranmeldungen verwenden:
- **Azure AD STS**: Ein zustandsloser Sicherheitstokendienst (Security Token Service, STS) zum Verarbeiten von Anmeldeanforderungen und Ausstellen von Sicherheitstoken für Browser, Clients oder Dienste von Benutzern nach Bedarf.
- **Azure Service Bus**: Ermöglicht die cloudfähige Kommunikation mit Messagingfunktionen für Unternehmen und die Weiterleitung der Kommunikation, um Sie beim Verbinden Ihrer lokalen Lösungen mit der Cloud zu unterstützen.
- **Azure AD Connect-Authentifizierungs-Agent**: Eine lokale Komponente, die Kennwortüberprüfungsanforderungen überwacht und darauf reagiert.
- **Azure SQL-Datenbank**: Enthält Informationen zu den Authentifizierungs-Agents Ihrer Mandanten, einschließlich der zugehörigen Metadaten und Verschlüsselungsschlüssel.
- **Active Directory**: Lokale Active Directory-Instanz, in der Ihre Benutzerkonten und die zugehörigen Kennwörter gespeichert sind.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Installation und Registrierung der Authentifizierungs-Agents

Authentifizierung-Agents werden mit Azure AD installiert und registriert, wenn Sie eine der folgenden Aktionen ausführen:
   - [Aktivieren von Passthrough-Authentifizierung über Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [Hinzufügen weiterer Authentifizierung-Agents, um die Hochverfügbarkeit von Anmeldeanforderungen sicherzustellen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
Es sind drei Phasen erforderlich, um einen Authentifizierungs-Agent in Betrieb zu nehmen:

1. Installation des Authentifizierungs-Agents
2. Registrierung des Authentifizierungs-Agents
3. Initialisierung des Authentifizierungs-Agents

In den folgenden Abschnitten werden diese Phasen ausführlich erläutert.

### <a name="authentication-agent-installation"></a>Installation des Authentifizierungs-Agents

Nur globale Administratoren können einen Authentifizierungs-Agent auf einem lokalen Server installieren (durch Verwenden von Azure AD Connect oder eigenständig). Bei der Installation werden der Liste in **Systemsteuerung** > **Programme** > **Programme und Features** diesen beiden neuen Einträge hinzugefügt:
- Die eigentliche Authentifizierungs-Agent-Anwendung. Diese Anwendung wird mit [NetworkService](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx)-Berechtigungen ausgeführt.
- Die Updater-Anwendung, die zur automatischen Aktualisierung des Authentifizierungs-Agents verwendet wird. Diese Anwendung wird mit [LocalSystem](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx)-Berechtigungen ausgeführt.

### <a name="authentication-agent-registration"></a>Registrierung des Authentifizierungs-Agents

Nachdem Sie den Authentifizierungs-Agent installiert haben, muss er unter Azure AD registriert werden. Azure AD weist jedem Authentifizierungs-Agent ein eindeutiges Zertifikat zur digitalen Identität zu, das für die sichere Kommunikation mit Azure AD verwendet werden kann.

Beim Registrierungsverfahren wird der Authentifizierungs-Agent auch an Ihren Mandanten gebunden. Dadurch ist sichergestellt, dass Azure AD weiß, dass dieser spezielle Authentifizierungs-Agent der einzige autorisierte Agent ist, der die Anforderungen zur Kennwortvalidierung für Ihren Mandanten verarbeiten darf. Dieses Verfahren wird für jeden neuen Authentifizierungs-Agent wiederholt, den Sie registrieren.

Die Authentifizierungs-Agents führen die folgenden Schritte aus, um sich bei Azure AD zu registrieren:

![Agent-Registrierung](./media/how-to-connect-pta-security-deep-dive/pta1.png)

1. Azure AD fordert zunächst, dass sich ein globaler Administrator mit seinen Anmeldeinformationen bei Azure AD anmeldet. Während der Anmeldung ruft der Authentifizierungs-Agent ein Zugriffstoken ab, das er im Namen des globalen Administrators verwenden kann.
2. Anschließend generiert der Authentifizierungs-Agent ein Schlüsselpaar: einen öffentlichen Schlüssel und einen privaten Schlüssel.
    - Das Schlüsselpaar wird über die standardmäßige RSA-2048-Bit-Verschlüsselung generiert.
    - Der private Schlüssel verbleibt auf dem lokalen Server, auf dem sich der Authentifizierungs-Agent befindet.
3. Der Authentifizierungs-Agent sendet per HTTPS eine „registration“-Anforderung an Azure AD, die die folgenden Komponenten enthält:
    - Das in Schritt 1 abgerufene Zugriffstoken.
    - Den in Schritt 2 generierten öffentlichen Schlüssel.
    - Eine Zertifikatsignieranforderung (Certificate Signing Request/CSR oder Zertifikatanforderung). Diese Anforderung beantragt ein Zertifikat zur digitalen Identität mit Azure AD als dessen Zertifizierungsstelle.
4. Azure AD überprüft das Zugriffstoken in der Registrierungsanforderung und stellt sicher, dass die Anforderung von einem globalen Administrator stammt.
5. Azure AD signiert dann ein Zertifikat zur digitalen Identität und sendet dieses Zertifikat an den Authentifizierungs-Agent.
    - Die Stammzertifizierungsstelle (Stamm-CA) in Azure AD wird verwendet, um das Zertifikat zu signieren. 

      > [!NOTE]
      > Diese Zertifizierungsstelle ist _nicht_ im Store mit den vertrauenswürdigen Stammzertifizierungsstellen von Windows enthalten.
    - Die Zertifizierungsstelle wird nur von der Passthrough-Authentifizierung verwendet. Die Zertifizierungsstelle wird verwendet, um CSRs während der Registrierung eines Authentifizierungs-Agents zu signieren.
    -  Keiner der anderen Azure AD-Dienste verwendet diese Zertifizierungsstelle.
    - Der Antragsteller des Zertifikats (Distinguished Name, DN) ist auf Ihre Mandanten-ID festgelegt. Dieser DN ist eine GUID, mit der Ihr Mandant eindeutig identifiziert wird. Dieser DN beschränkt die Verwendung des Zertifikats auf Ihren Mandanten.
6. Azure AD speichert den öffentlichen Schlüssel des Authentifizierungs-Agents in einer Azure SQL-Datenbank, auf die nur Azure AD Zugriff hat.
7. Das Zertifikat (in Schritt 5 ausgestellt) wird auf dem lokalen Server im Windows-Zertifikatspeicher gespeichert (Speicherort: [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE)). Es wird sowohl vom Authentifizierungs-Agent als auch von den Updater-Anwendungen verwendet.

### <a name="authentication-agent-initialization"></a>Initialisierung des Authentifizierungs-Agents

Wenn der Authentifizierungs-Agent gestartet wird – entweder erstmalig nach der Registrierung oder nach einem Serverneustart –, muss es ihm ermöglicht werden, mit dem Azure AD-Dienst sicher kommunizieren und Anforderungen zur Kennwortvalidierung akzeptieren zu können.

![Agent-Initialisierung](./media/how-to-connect-pta-security-deep-dive/pta2.png)

Ein Authentifizierungs-Agent wird wie folgt initialisiert:

1. Der Authentifizierungs-Agent sendet eine ausgehende Bootstrap-Anforderung an Azure AD. 
    - Diese Anforderung erfolgt über Port 443 und über einen gegenseitig authentifizierten HTTPS-Kanal. In der Anforderung wird das Zertifikat verwendet, das während der Registrierung des Authentifizierungs-Agents ausgegeben wurde.
2. Azure AD reagiert auf die Anforderung, indem ein Zugriffsschlüssel für eine Azure Service Bus-Warteschlange bereitgestellt wird, der für Ihren Mandanten eindeutig und durch Ihre Mandanten-ID identifiziert ist.
3. Der Authentifizierungs-Agent stellt eine dauerhafte ausgehende HTTPS-Verbindung (über Port 443) mit der Warteschlange her. 
    - Der Authentifizierungs-Agent kann jetzt Anforderungen zur Kennwortvalidierung abrufen und verarbeiten.

Wenn Sie unter Ihrem Mandanten mehrere Authentifizierungs-Agents registriert haben, wird anhand des Initialisierungsverfahrens sichergestellt, dass dafür jeweils eine Verbindung mit derselben Service Bus-Warteschlange hergestellt wird. 

## <a name="process-sign-in-requests"></a>Verarbeiten von Anmeldeanforderungen 

Im folgenden Diagramm ist dargestellt, wie Anforderungen zur Benutzeranmeldung bei der Passthrough-Authentifizierung verarbeitet werden.

![Anmeldung verarbeiten](./media/how-to-connect-pta-security-deep-dive/pta3.png)

Bei der Passthrough-Authentifizierung wird eine Anforderung zur Benutzeranmeldung wie folgt verarbeitet: 

1. Der Benutzer versucht, auf eine Anwendung zuzugreifen (z.B. [Outlook Web-App](https://outlook.office365.com/owa)).
2. Wenn der Benutzer nicht bereits angemeldet ist, leitet die Anwendung den Browser auf die Anmeldeseite von Azure AD um.
3. Der Azure AD-Sicherheitstokendienst (STS) reagiert mit der Seite **Benutzeranmeldung**.
4. Der Benutzer gibt auf der **Benutzeranmeldeseite** seinen Benutzernamen ein und wählt anschließend die Schaltfläche **Weiter** aus.
5. Der Benutzer gibt auf der **Benutzeranmeldeseite** sein Kennwort ein und wählt anschließend die Schaltfläche **Anmelden** aus.
6. Der Benutzername und das Kennwort werden in einer HTTPS POST-Anforderung an den Azure AD STS gesendet.
7. Der Azure AD STS ruft öffentliche Schlüssel für alle Authentifizierungs-Agents, die unter Ihrem Mandanten registriert sind, aus der Azure SQL-Datenbank ab und verwendet diese zum Verschlüsseln des Kennworts.
    - Der Dienst erstellt „N“ verschlüsselte Kennwortwerte für „N“ Authentifizierungs-Agents, die unter Ihrem Mandanten registriert sind.
8. Der Azure AD STS reiht die Anforderung zur Kennwortvalidierung, die aus den Benutzername- und den verschlüsselten Kennwortwerten besteht, in die Service Bus-Warteschlange ein, die zu Ihrem Mandanten gehört.
9. Da die initialisierten Authentifizierungs-Agents eine dauerhafte Verbindung mit der Service Bus-Warteschlange haben, ruft einer der verfügbaren Authentifizierungs-Agents die Anforderung zur Kennwortvalidierung ab.
10. Der Authentifizierungs-Agent ermittelt durch Verwenden eines Bezeichners den verschlüsselten Kennwortwert, der zu seinem öffentlichen Schlüssel gehört, und entschlüsselt den Wert, indem er seinen privaten Schlüssel verwendet.
11. Der Authentifizierungs-Agent versucht, den Benutzernamen und das Kennwort für die lokale Active Directory-Instanz zu validieren, indem er die [Win32 LogonUser-API](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) verwendet, wobei der Parameter **dwLogonType** auf **LOGON32_LOGON_NETWORK** festgelegt ist. 
    - Diese API ist dieselbe API, die von Active Directory-Verbunddienste (AD FS) verwendet wird, um Benutzer in einem Szenario mit Verbundanmeldung anzumelden.
    - Diese API setzt auf den Standardauflösungsvorgang in Windows Server auf, um den Domänencontroller zu suchen.
12. Der Authentifizierungs-Agent empfängt das Ergebnis von Active Directory, also z.B. Erfolg, Benutzername oder Kennwort fehlerhaft oder Kennwort abgelaufen.

   > [!NOTE]
   > Wenn beim Authentifizierungs-Agent während der Anmeldung ein Fehler auftritt, wird die gesamte Anforderung gelöscht. Es erfolgt keine Weitergabe der Anmeldeanforderungen von einem Authentifizierungs-Agent an einen anderen Authentifizierungs-Agent auf lokaler Ebene. Diese Agents kommunizieren nur mit der Cloud und nicht miteinander.
13. Der Authentifizierungs-Agent leitet das Ergebnis über einen ausgehenden gegenseitig authentifizierten HTTPS-Kanal (Port 443) zurück an den Azure AD STS. Bei der gegenseitigen Authentifizierung wird das Zertifikat verwendet, das zuvor während der Registrierung für den Authentifizierungs-Agent ausgestellt wurde.
14. Der Azure AD STS überprüft, ob das Ergebnis mit der jeweiligen Anmeldeanforderung auf Ihrem Mandanten korreliert.
15. Der Azure AD STS fährt gemäß Konfiguration mit dem Anmeldeverfahren fort. Wenn die Kennwortvalidierung erfolgreich war, kann der Benutzer beispielsweise zum Einrichten von Multi-Factor Authentication aufgefordert oder zurück an die Anwendung geleitet werden.

## <a name="operational-security-of-the-authentication-agents"></a>Betriebssicherheit der Authentifizierungs-Agents

Um sicherzustellen, dass die Passthrough-Authentifizierung im Betrieb sicher bleibt, erneuert Azure AD die Zertifikate der Authentifizierungs-Agents in regelmäßigen Abständen. Die Erneuerungen werden von Azure AD ausgelöst. Die Erneuerungen werden nicht von den Authentifizierungs-Agents selbst gesteuert.

![Betriebssicherheit](./media/how-to-connect-pta-security-deep-dive/pta4.png)

So erneuern Sie die Vertrauensstellung eines Authentifizierungs-Agents mit Azure AD:

1. Der Authentifizierungs-Agent pingt Azure AD in regelmäßigen Abständen nach einigen Stunden, um zu überprüfen, ob es an der Zeit ist, sein Zertifikat zu erneuern. Das Zertifikat wird 30 Tage vor dessen Ablauf erneuert.
    - Diese Überprüfung wird über einen HTTPS-Kanal mit gegenseitiger Authentifizierung und mit dem Zertifikat ausgeführt, das während der Registrierung ausgestellt wurde.
2. Wenn der Dienst angibt, dass es Zeit für eine Erneuerung ist, generiert der Authentifizierungs-Agent ein neues Schlüsselpaar: einen öffentlichen Schlüssel und einen privaten Schlüssel.
    - Diese Schlüssel werden über die standardmäßige RSA-2048-Bit-Verschlüsselung generiert.
    - Der private Schlüssel verlässt den lokalen Server niemals.
3. Der Authentifizierungs-Agent sendet per HTTPS dann eine „certificate renewal“-Anforderung an Azure AD, die die folgenden Komponenten enthält:
    - Das vorhandene Zertifikat, das aus dem Speicherort CERT_SYSTEM_STORE_LOCAL_MACHINE im Windows-Zertifikatspeicher abgerufen wurde. An diesem Verfahren ist kein globaler Administrator beteiligt, sodass kein Zugriffstoken für den globalen Administrator erforderlich ist.
    - Den in Schritt 2 generierten öffentlichen Schlüssel.
    - Eine Zertifikatsignieranforderung (Certificate Signing Request/CSR oder Zertifikatanforderung). Diese Anforderung beantragt ein neues Zertifikat zur digitalen Identität mit Azure AD als dessen Zertifizierungsstelle.
4. Azure AD überprüft das vorhandene Zertifikat in der Anforderung für die Zertifikaterneuerung. Anschließend wird überprüft, ob die Anforderung von einem Authentifizierungs-Agent stammt, der unter Ihrem Mandanten registriert ist.
5. Wenn das vorhandene Zertifikat noch gültig ist, signiert Azure AD ein neues Zertifikat zur digitalen Identität und sendet das neue Zertifikat zurück an den Authentifizierungs-Agent. 
6. Falls das vorhandene Zertifikat abgelaufen ist, löscht Azure AD den Authentifizierungs-Agent aus der Liste mit den registrierten Authentifizierungs-Agents Ihres Mandanten. Anschließend muss ein globaler Administrator einen neuen Authentifizierungs-Agent manuell installieren und registrieren.
    - Verwenden Sie die Stamm-CA (Stammzertifizierungsstelle) von Azure AD, um das Zertifikat zu signieren.
    - Legen Sie den Antragsteller des Zertifikats (Distinguished Name oder DN) auf Ihre Mandanten-ID fest. Diese ist eine GUID, mit der Ihr Mandant eindeutig identifiziert wird. Der DN bewirkt, dass das Zertifikat nur für Ihren Mandanten gilt.
6. Azure AD speichert den neuen öffentlichen Schlüssel des Authentifizierungs-Agents in einer Azure SQL-Datenbank, auf die nur Azure AD selbst Zugriff hat. Außerdem macht Azure AD den alten öffentlichen Schlüssel, der dem Authentifizierungs-Agent zugeordnet ist, ungültig.
7. Anschließend wird das neue Zertifikat (in Schritt 5 ausgestellt) auf dem Server im Windows-Zertifikatspeicher gespeichert (Speicherort: [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER)).
    - Da das Verfahren zur Erneuerung der Vertrauensstellung nicht interaktiv durchgeführt wird (ohne Vorhandensein des globalen Administrators), hat der Authentifizierungs-Agent keinen Zugriff mehr, um das vorhandene Zertifikat im Speicherort CERT_SYSTEM_STORE_LOCAL_MACHINE zu aktualisieren. 
    
   > [!NOTE]
   > Bei dieser Vorgehensweise wird das Zertifikat nicht aus dem Speicherort CERT_SYSTEM_STORE_LOCAL_MACHINE entfernt.
8. Das neue Zertifikat wird von jetzt an für die Authentifizierung verwendet. Bei jeder nachfolgenden Erneuerung des Zertifikats wird das Zertifikat im Speicherort CERT_SYSTEM_STORE_LOCAL_MACHINE ersetzt.

## <a name="auto-update-of-the-authentication-agents"></a>Automatische Aktualisierung der Authentifizierungs-Agents

Mit der Updater-Anwendung wird der Authentifizierungs-Agent automatisch aktualisiert, wenn eine neue Version (mit Fehlerbehebungen oder Leistungsverbesserungen) veröffentlicht wird. Die Updater-Anwendung verarbeitet aber keine Anforderungen zur Kennwortvalidierung für Ihren Mandanten.

Azure AD hostet die neue Version der Software als signiertes **Windows Installer-Paket (MSI)** . Das MSI-Paket wird signiert, indem [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) mit SHA256 als Digestalgorithmus verwendet wird. 

![Automatische Aktualisierung](./media/how-to-connect-pta-security-deep-dive/pta5.png)

So wird ein Authentifizierungs-Agent automatisch aktualisiert:

1. Die Updater-Anwendung pingt Azure AD einmal pro Stunde, um zu überprüfen, ob eine neue Version des Authentifizierungs-Agents verfügbar ist. 
    - Diese Überprüfung wird über einen HTTPS-Kanal mit gegenseitiger Authentifizierung und mithilfe des Zertifikats ausgeführt, das während der Registrierung ausgestellt wurde. Der Authentifizierungs-Agent und der Updater nutzen das auf dem Server gespeicherte Zertifikat gemeinsam.
2. Wenn eine neue Version verfügbar ist, gibt Azure AD das signierte MSI-Paket zurück an den Updater.
3. Der Updater überprüft, ob das MSI-Paket von Microsoft signiert ist.
4. Der Updater führt das MSI-Paket aus. Dieser Vorgang umfasst die folgenden Schritte:

   > [!NOTE]
   > Der Updater wird mit den Berechtigungen von [Lokales System](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx) ausgeführt.

    - Der Authentifizierungs-Agent-Dienst wird beendet.
    - Die neue Version des Authentifizierungs-Agent wird auf dem Server installiert.
    - Der Authentifizierungs-Agent-Dienst wird neu gestartet.

>[!NOTE]
>Wenn Sie unter Ihrem Mandanten mehrere Authentifizierungs-Agents registriert haben, erneuert Azure AD die dazugehörigen Zertifikate nicht gleichzeitig und führt auch keine Aktualisierung dafür durch. Stattdessen führt Azure AD jeweils einzelne Vorgänge aus, um die Hochverfügbarkeit von Anmeldeanforderungen sicherzustellen.
>


## <a name="next-steps"></a>Nächste Schritte
- [Aktuelle Einschränkungen](how-to-connect-pta-current-limitations.md): Informieren Sie sich darüber, welche Szenarien unterstützt werden.
- [Schnellstart](how-to-connect-pta-quick-start.md): Aktivieren und Ausführen der Passthrough-Authentifizierung von Azure AD.
- [Migrieren von AD FS zur Passthrough-Authentifizierung](https://aka.ms/adfstoptadpdownload): Ein detaillierter Leitfaden zur Migration von AD FS (oder anderen Verbundtechnologien) zur Passthrough-Authentifizierung
- [Smart Lockout](../authentication/howto-password-smart-lockout.md): Konfigurieren Sie die Smart Lockout-Funktion für Ihren Mandanten, um Benutzerkonten zu schützen.
- [Funktionsweise](how-to-connect-pta-how-it-works.md): Lernen Sie die grundlegende Funktionsweise der Passthrough-Authentifizierung von Azure AD kennen.
- [Häufig gestellte Fragen](how-to-connect-pta-faq.md): Finden Sie Antworten auf häufig gestellte Fragen.
- [Problembehandlung](tshoot-connect-pass-through-authentication.md): Hier finden Sie Informationen zum Beheben von allgemeinen Problemen, die bei der Passthrough-Authentifizierung auftreten können.
- [Nahtloses einmaliges Anmelden mit Azure AD:](how-to-connect-sso.md) Erfahren Sie mehr über diese Ergänzungsfunktion.
