---
title: 'Azure AD Connect: Pass-Through-Authentifizierung – häufig gestellte Fragen | Microsoft-Dokumentation'
description: In diesem Artikel erhalten Sie Antworten auf häufig gestellte Fragen zur Passthrough-Authentifizierung von Azure Active Directory.
services: active-directory
keywords: Passthrough-Authentifizierung mit Azure AD Connect, Active Directory installieren, erforderliche Komponenten für Azure AD, SSO, einmaliges Anmelden
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75f797c40a276323cea9983c5340d2d854160c83
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368476"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory-Passthrough-Authentifizierung: Häufig gestellte Fragen

In diesem Artikel werden häufig gestellte Fragen zur Passthrough-Authentifizierung von Azure Active Directory (Azure AD) behandelt. Der Inhalt wird bei Bedarf aktualisiert.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Welche der Methoden für die Anmeldung bei Azure AD (Passthrough-Authentifizierung, Kennworthashsynchronisierung oder Active Directory-Verbunddienste (AD FS)) sollte ich auswählen?

Einen Vergleich der verschiedenen Azure AD-Anmeldemethoden und Informationen, wie Sie die richtige Anmeldemethode für Ihre Organisation wählen, finden Sie in [diesem Leitfaden](./choose-ad-authn.md).

## <a name="is-pass-through-authentication-a-free-feature"></a>Ist die Passthrough-Authentifizierung ein kostenloses Feature?

Die Passthrough-Authentifizierung ist ein kostenloses Feature. Sie benötigen für die Verwendung keine kostenpflichtigen Editionen von Azure AD.

## <a name="does-conditional-access-work-with-pass-through-authentication"></a>Funktioniert der [bedingte Zugriff](../conditional-access/overview.md) mit der Passthrough-Authentifizierung?

Ja. Alle Funktionen mit bedingtem Zugriff, einschließlich Azure AD Multi-Factor Authentication, funktionieren mit der Passthrough-Authentifizierung.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Unterstützt die Passthrough-Authentifizierung eine alternative Anmelde-ID (Alternate ID) anstelle von „UserPrincipalName“ als Benutzernamen?
Ja. Die Anmeldung mit einem UPN-fremden Wert (etwa mit einer alternativen E-Mail-Adresse) wird sowohl für die Passthrough-Authentifizierung (PTA) als auch für die Kennworthashsynchronisierung (Password Hash Sync, PHS) unterstützt. Weitere Informationen zur alternativen Anmelde-ID finden Sie [hier](../authentication/howto-authentication-use-email-signin.md).

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>Kann die Kennworthashsynchronisierung als Fallback für die Passthrough-Authentifizierung verwendet werden?

Nein. Bei der Passthrough-Authentifizierung wird _kein_ automatisches Failover auf die Kennworthashsynchronisierung ausgeführt. Um Benutzeranmeldefehler zu vermeiden, sollten Sie die Passthrough-Authentifizierung für [Hochverfügbarkeit](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) konfigurieren.

## <a name="what-happens-when-i-switch-from-password-hash-synchronization-to-pass-through-authentication"></a>Was geschieht, wenn ich von der Kennworthashsynchronisierung auf die Passthrough-Authentifizierung umstelle?

Wenn Sie Azure AD Connect verwenden, um die Anmeldemethode von Kennworthashsynchronisierung in Passthrough-Authentifizierung zu ändern, wird die Passthrough-Authentifizierung die primäre Anmeldemethode für Ihre Benutzer in verwalteten Domänen. Beachten Sie, dass die Kennworthashes aller Benutzer, die zuvor per Kennworthashsynchronisierung synchronisiert wurden, in Azure AD gespeichert bleiben.

## <a name="can-i-install-an-azure-ad-application-proxy-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Kann ich einen [Azure AD-Anwendungsproxy](../manage-apps/application-proxy.md)-Connector und einen Passthrough-Authentifizierungs-Agent auf demselben Server installieren?

Ja. Die umbenannten Versionen des Passthrough-Authentifizierungs-Agents (Versionen 1.5.193.0 oder höher) unterstützen diese Konfiguration.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>Welche Versionen von Azure AD Connect und dem Passthrough-Authentifizierungs-Agent werden benötigt?

Damit dieses Feature funktioniert, benötigen Sie Version 1.1.750.0 oder höher für Azure AD Connect und Version 1.5.193.0 oder höher für den Passthrough-Authentifizierungs-Agent. Installieren Sie die gesamte Software auf Servern mit Windows Server 2012 R2 oder höher.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Was geschieht, wenn das Kennwort des Benutzers abgelaufen ist und er versucht, sich mit der Passthrough-Authentifizierung anzumelden?

Wenn Sie das [Kennwortrückschreiben](../authentication/concept-sspr-writeback.md) für einen bestimmten Benutzer konfiguriert haben und der Benutzer sich mit der Passthrough-Authentifizierung anmeldet, kann er sein Kennwort ändern oder zurücksetzen. Die Kennwörter werden erwartungsgemäß in das lokale Active Directory zurückgeschrieben.

Wenn Sie das Kennwortrückschreiben für einen bestimmten Benutzer nicht konfiguriert haben oder der Benutzer keine gültige Azure AD hat, kann der Benutzer sein Kennwort in der Cloud nicht aktualisieren. Das Kennwort lässt sich nicht aktualisieren, auch wenn es bereits abgelaufen ist. Dem Benutzer wird stattdessen diese Meldung angezeigt: „Eine Kennwortänderung auf dieser Website wird von Ihrer Organisation nicht gestattet. Ändern Sie Ihr Kennwort anhand der von Ihrer Organisation empfohlenen Methode, oder bitten Sie Ihren Administrator um Hilfe. Der Benutzer oder der Administrator muss das Kennwort im lokalen Active Directory zurücksetzen.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Wie schützt die Passthrough-Authentifizierung vor Brute-Force-Kennwortangriffen?

[Lesen Sie die Informationen zu Smart Lockout](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Was kommunizieren Passthrough-Authentifizierungs-Agents über die Ports 80 und 443?

- Die Authentifizierungs-Agents stellen HTTPS-Anforderungen für alle Funktionsvorgänge über Port 443 aus.
- Die Authentifizierungs-Agents stellen HTTP-Anforderungen über Port 80, um die TLS/SSL-Zertifikatsperrlisten (CRLs) herunterzuladen.

     >[!NOTE]
     >In kürzlich veröffentlichten Updates wurde die Zahl der Ports, die für diese Funktion erforderlich sind, gesenkt. Wenn Sie eine ältere Version von Azure AD Connect oder des Authentifizierungs-Agents verwenden, halten Sie auch folgende Ports offen: 5671, 8080, 9090, 9091, 9350, 9352 und 10100–10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Können die Passthrough-Authentifizierungs-Agents über einen ausgehenden Webproxyserver kommunizieren?

Ja. Wenn in Ihrer lokalen Umgebung WPAD (Web Proxy Auto-Discovery) aktiviert ist, versuchen die Authentifizierungs-Agents automatisch, einen Webproxyserver im Netzwerk zu finden und zu verwenden.

Wenn in Ihrer Umgebung kein WPAD verwendet wird, können Sie Proxyinformationen (wie unten gezeigt) hinzufügen, um einem Passthrough-Authentifizierungs-Agent die Kommunikation mit Azure AD zu ermöglichen:
- Konfigurieren Sie Proxyinformationen im Internet Explorer, bevor Sie den Passthrough-Authentifizierungs-Agent auf dem Server installieren. Dadurch können Sie die Installation des Authentifizierungs-Agents abschließen. Er wird im Admin-Portal jedoch nach wie vor als **Inaktiv** angezeigt.
- Wechseln Sie auf dem Server zu „C:\Programme\Microsoft Azure AD Connect Authentication Agent“.
- Bearbeiten Sie die Konfigurationsdatei „AzureADConnectAuthenticationAgentService“, und fügen Sie die folgenden Zeilen hinzu (ersetzen Sie „http\://contosoproxy.com:8080“ durch Ihre tatsächliche Proxyadresse):

```
   <system.net>
      <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy
            usesystemdefault="true"
            proxyaddress="http://contosoproxy.com:8080"
            bypassonlocal="true"
         />
     </defaultProxy>
   </system.net>
```

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Kann ich zwei oder mehr Passthrough-Authentifizierungs-Agents auf dem gleichen Server installieren?

Nein, Sie können nur einen Passthrough-Authentifizierungs-Agent auf einem einzelnen Server installieren. Wenn Sie die Passthrough-Authentifizierung für Hochverfügbarkeit konfigurieren möchten, [folgen Sie den hier aufgeführten Anweisungen](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="do-i-have-to-manually-renew-certificates-used-by-pass-through-authentication-agents"></a>Muss ich Zertifikate, die von Passthrough-Authentifizierungs-Agents verwendet werden, manuell verlängern?

Die Kommunikation zwischen den einzelnen Passthrough-Authentifizierungs-Agents und Azure AD wird per zertifikatbasierter Authentifizierung geschützt. Diese [Zertifikate werden alle paar Monate automatisch von Azure AD verlängert](how-to-connect-pta-security-deep-dive.md#operational-security-of-the-authentication-agents). Sie müssen diese Zertifikate nicht manuell verlängern. Abgelaufene Zertifikate können Sie bei Bedarf entfernen.

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Gewusst wie: Entfernen eines Passthrough-Authentifizierungs-Agents

Solange ein Passthrough-Authentifizierungs-Agent ausgeführt wird, bleibt er aktiv und verarbeitet fortlaufend Anmeldeanforderungen von Benutzern. Wenn Sie einen Authentifizierungs-Agent deinstallieren möchten, wechseln Sie zu **Systemsteuerung > Programme > Programme und Funktionen**, und deinstallieren Sie die beiden Programme **Microsoft Azure AD Connect-Authentifizierungs-Agent** und **Microsoft Azure AD Connect Agent Updater**.

Nach Abschluss des vorherigen Schritts sehen Sie auf dem Blatt „Passthrough-Authentifizierung“ im [Azure Active Directory-Admin Center](https://aad.portal.azure.com) den Authentifizierungs-Agent als **Inaktiv**. Dies entspricht dem _erwarteten_ Verhalten. Der Authentifizierungs-Agent wird nach 10 Tagen automatisch aus der Liste entfernt.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Ich verwende bereits AD FS für die Anmeldung bei Azure AD. Wie kann ich zur Passthrough-Authentifizierung wechseln?

Wenn Sie von AD FS (oder andere Verbundtechnologien nutzen) zur Passthrough-Authentifizierung migrieren, wird dringend empfohlen, dem ausführlichen [Leitfaden zur Bereitstellung](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) zu folgen.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Kann ich die Passthrough-Authentifizierung in einer Active Directory-Umgebung mit mehreren Gesamtstrukturen verwenden?

Ja. Umgebungen mit mehreren Gesamtstrukturen (bidirektional) werden unterstützt, wenn Gesamtstruktur-Vertrauensstellungen zwischen Ihren Active Directory-Gesamtstrukturen bestehen und das Namensuffixrouting ordnungsgemäß konfiguriert ist.

## <a name="does-pass-through-authentication-provide-load-balancing-across-multiple-authentication-agents"></a>Bietet die Passthrough-Authentifizierung einen Lastenausgleich zwischen mehreren Authentifizierungs-Agents?

Nein, die Installation von mehreren Passthrough-Authentifizierungs-Agents garantiert nur [Hochverfügbarkeit](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). Dadurch wird kein deterministischer Lastenausgleich zwischen den Authentifizierungs-Agents bereitgestellt. Jeder Authentifizierungs-Agent kann eine bestimmte Benutzeranmeldungsanforderung verarbeiten.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Wie viele Passthrough-Authentifizierungs-Agents muss ich installieren?

Mit der Installation von mehreren Passthrough-Authentifizierungs-Agents wird für [Hochverfügbarkeit](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) gesorgt. Es wird aber kein deterministischer Lastenausgleich zwischen den Authentifizierungs-Agents bereitgestellt.

Berücksichtigen Sie die Spitzenlast und durchschnittliche Last in Bezug auf die Anmeldeanforderungen, die Sie für Ihren Mandanten erwarten. Als Richtwert gilt, dass ein einzelner Authentifizierungs-Agent auf einem Standardserver mit einer CPU mit vier Kernen und 16 GB RAM pro Sekunde 300 bis 400 Authentifizierungen verarbeiten kann.

Um den Netzwerkverkehr abzuschätzen, verwenden Sie die folgende Anleitung zur Skalierung:
- Jede Anforderung hat eine Nutzlastgröße von (0,5 K + 1 K * anz_agents) Bytes, d. h. Daten von Azure AD zum Authentifizierungs-Agent. Hier gibt „anz_agents“ die Anzahl der für Ihren Mandanten registrierten Authentifizierung-Agents an.
- Jede Antwort hat eine Nutzlastgröße von 1 K Bytes, d. h. Daten vom Authentifizierungs-Agent zu Azure AD.

Für die meisten Kunden reichen zwei oder drei Authentifizierungs-Agents insgesamt aus, um Hochverfügbarkeit und genügend Kapazität zu erzielen. Es wird empfohlen, die Authentifizierungs-Agents in der Nähe Ihres Domänencontrollers zu installieren, um die Anmeldungslatenz zu verbessern.

>[!NOTE]
>In einem System können maximal 40 Authentifizierungs-Agents pro Mandant installiert werden.

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>Warum benötige ich ein rein cloudbasiertes globales Administratorkonto, um die Passthrough-Authentifizierung zu aktivieren?

Es wird empfohlen, die Passthrough-Authentifizierung mit einem rein cloudbasierten globalen Administratorkonto zu aktivieren oder zu deaktivieren. Erfahren Sie, wie Sie ein [rein cloudbasiertes Konto für den globalen Administrator hinzufügen](../fundamentals/add-users-azure-active-directory.md). Auf diese Weise wird sichergestellt, dass Sie sich nicht aus Ihrem Mandanten aussperren.

## <a name="how-can-i-disable-pass-through-authentication"></a>Wie kann ich die Passthrough-Authentifizierung deaktivieren?

Führen Sie den Azure AD Connect-Assistenten erneut aus, und ändern Sie die Anmeldemethode für Benutzer von der Passthrough-Authentifizierung in eine andere Methode. Diese Änderung deaktiviert die Passthrough-Authentifizierung für den Mandanten und deinstalliert den Authentifizierungs-Agent vom Server. Auf anderen Servern müssen Sie die Authentifizierungs-Agents manuell deinstallieren.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Was geschieht, wenn ich einen Passthrough-Authentifizierungs-Agent deinstalliere?

Wenn Sie einen Passthrough-Authentifizierungs-Agents auf einem Server deinstallieren, werden vom Server keine Anmeldeanforderungen mehr angenommen. Um eine Unterbrechung der Anmeldefunktion für Benutzer zu vermeiden, stellen Sie sicher, dass ein anderer Authentifizierungs-Agent ausgeführt wird, bevor Sie einen Passthrough-Authentifizierungs-Agent deinstallieren.

## <a name="i-have-an-older-tenant-that-was-originally-setup-using-ad-fs--we-recently-migrated-to-pta-but-now-are-not-seeing-our-upn-changes-synchronizing-to-azure-ad--why-are-our-upn-changes-not-being-synchronized"></a>Ich habe einen älteren Mandanten, der ursprünglich mit AD FS eingerichtet wurde.  Wir sind kürzlich zu PTA migriert, doch jetzt sehen wir nicht, dass unsere UPN-Änderungen mit Azure AD synchronisiert werden.  Warum werden unsere UPN-Änderungen nicht synchronisiert?

A: Lokale UPN-Änderungen können nicht synchronisiert werden, wenn:

- Ihr Azure AD-Mandant vor dem 15. Juni 2015 erstellt wurde
- Sie ursprünglich mit Ihrem Azure AD-Mandanten über AD FS zur Authentifizierung verbunden waren
- Sie eine Umstellung vorgenommen haben, sodass verwaltete Benutzer PTA als Authentifizierung verwenden

Das liegt daran, dass Mandanten, die vor dem 15. Juni 2015 erstellt wurden, UPN-Änderungen standardmäßig blockiert haben.  Wenn Sie UPN-Änderungen freigeben möchten, führen Sie das folgende PowerShell-Cmdlet aus:  

`Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $True`

Mandanten, die nach dem 15. Juni 2015 erstellt wurden, synchronisieren UPN-Änderungen standardmäßig.   



## <a name="next-steps"></a>Nächste Schritte
- [Aktuelle Einschränkungen](how-to-connect-pta-current-limitations.md): Informieren Sie sich darüber, welche Szenarien unterstützt werden.
- [Schnellstart](how-to-connect-pta-quick-start.md): Aktivieren und Ausführen der Passthrough-Authentifizierung von Azure AD.
- [Migrieren von AD FS zur Passthrough-Authentifizierung](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true): Ein detaillierter Leitfaden zur Migration von AD FS (oder anderen Verbundtechnologien) zur Passthrough-Authentifizierung
- [Smart Lockout](../authentication/howto-password-smart-lockout.md): Erfahren Sie, wie Sie die Smart Lockout-Funktion für Ihren Mandanten zum Schutz von Benutzerkonten konfigurieren.
- [Technische Einzelheiten](how-to-connect-pta-how-it-works.md): Hier finden Sie Informationen zur Funktionsweise der Passthrough-Authentifizierung.
- [Problembehandlung](tshoot-connect-pass-through-authentication.md): Hier finden Sie Informationen zum Beheben von allgemeinen Problemen, die bei der Passthrough-Authentifizierung auftreten können.
- [Ausführliche Informationen zur Sicherheit](how-to-connect-pta-security-deep-dive.md): Hier erhalten Sie ausführliche technische Informationen zur Pass-Through-Authentifizierung.
- [Nahtloses einmaliges Anmelden mit Azure AD:](how-to-connect-sso.md) Erfahren Sie mehr über diese Ergänzungsfunktion.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Fordern Sie neue Features über das Azure Active Directory-Forum an.
