---
title: Problembehandlung beim Azure AD-Kennwortschutz – Azure Active Directory
description: Informationen zur allgemeinen Problembehandlung für den Azure AD-Kennwortschutz
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 108ead982529d2ac6549cceffd9d2177ab6456bf
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60414765"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Problembehandlung beim Azure AD-Kennwortschutz

Nach der Bereitstellung des Azure AD-Kennwortschutzes ist möglicherweise eine Problembehandlung erforderlich. In diesem Artikel werden einige allgemeine Problembehandlungsschritte ausführlich beschrieben, damit Sie diese besser verstehen können.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>Der DC-Agent hat einen Proxy nicht im Verzeichnis gefunden.

Das wichtigste Anzeichen für dieses Problem sind Ereignisse vom Typ „30017“ im Administratorereignisprotokoll des DC-Agents.

Das Problem ist in der Regel darauf zurückzuführen, dass ein Proxy noch nicht registriert wurde. Wenn ein Proxy registriert wurde, kann es aufgrund der AD-Replikationswartezeit zu einer Verzögerung kommen, bevor ein bestimmter DC-Agent den Proxy sehen kann.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>Der DC-Agent kann nicht mit einem Proxy kommunizieren.

Das wichtigste Anzeichen für dieses Problem sind Ereignisse vom Typ „30018“ im Administratorereignisprotokoll des DC-Agents. Dies kann mehrere mögliche Ursachen haben:

1. Der DC-Agent befindet sich in einem isolierten Teil des Netzwerks, der keine Netzwerkkonnektivität mit dem bzw. den registrierten Proxy(s) zulässt. Dieses Problem ist harmlos, solange andere DC-Agents mit dem bzw. den Proxy(s) kommunizieren können, um Kennwortrichtlinien aus Azure herunterzuladen, die der isolierte Domänencontroller (DC) dann per Replikation der Richtliniendateien in der SYSVOL-Freigabe erhält.

1. Der Proxyhostcomputer blockiert den Zugriff auf den RPC-Endpunktzuordnungsendpunkt (Port 135).

   Das Installationsprogramm für den Proxy des Azure AD-Kennwortschutzes erstellt automatisch eine eingehende Windows-Firewallregel, die den Zugriff auf Port 135 zulässt. Wird diese Regel später gelöscht oder deaktiviert, können DC-Agents nicht mit dem Proxydienst kommunizieren. Wenn die integrierte Windows-Firewall deaktiviert wurde, um stattdessen ein anderes Firewallprodukt zu verwenden, müssen Sie diese andere Firewall zum Zulassen des Zugriffs auf Port 135 konfigurieren.

1. Der Proxyhostcomputer blockiert den Zugriff auf den RPC-Endpunkt (dynamisch oder statisch), an dem der Proxydienst lauscht.

   Das Installationsprogramm für den Proxy des Azure AD-Kennwortschutzes erstellt automatisch eine eingehende Windows-Firewallregel, die den Zugriff auf alle eingehenden Ports zulässt, an denen der Proxydienst des Azure AD-Kennwortschutzes lauscht. Wird diese Regel später gelöscht oder deaktiviert, können DC-Agents nicht mit dem Proxydienst kommunizieren. Wenn die integrierte Windows-Firewall deaktiviert wurde, um stattdessen ein anderes Firewallprodukt zu verwenden, müssen Sie diese andere Firewall zum Zulassen des Zugriffs auf alle eingehenden Ports konfigurieren, an denen der Proxydienst des Azure AD-Kennwortschutzes lauscht. Diese Konfiguration kann präzisiert werden, wenn der Proxydienst zum Lauschen an einem bestimmten statischen RPC-Port konfiguriert wurde (mit dem Cmdlet `Set-AzureADPasswordProtectionProxyConfiguration`).

## <a name="the-proxy-service-can-receive-calls-from-dc-agents-in-the-domain-but-is-unable-to-communicate-with-azure"></a>Der Proxydienst kann Aufrufe von DC-Agents in der Domäne empfangen, aber nicht mit Azure kommunizieren.

1. Stellen Sie sicher, dass der Proxycomputer über Konnektivität mit den Endpunkten verfügt, die in den [Bereitstellungsanforderungen](howto-password-ban-bad-on-premises-deploy.md) aufgeführt sind.

1. Stellen Sie sicher, dass die Gesamtstruktur und alle Proxyserver für denselben Azure-Mandanten registriert sind.

   Sie können dies überprüfen, indem Sie die PowerShell-Cmdlets `Get-AzureADPasswordProtectionProxy` und `Get-AzureADPasswordProtectionDCAgent` ausführen und dann die `AzureTenant`-Eigenschaft für jedes zurückgegebene Element vergleichen. Zur ordnungsgemäßen Ausführung müssen diese innerhalb einer Gesamtstruktur auf allen DC-Agents und Proxyservern übereinstimmen.

   Wenn ein Registrierungskonflikt bei einem Azure-Mandanten vorliegt, kann dieser durch Ausführen des PowerShell-Cmdlets `Register-AzureADPasswordProtectionProxy` und/oder `Register-AzureADPasswordProtectionForest` (je nach Bedarf) repariert werden, wobei sichergestellt wird, dass für alle Registrierungen die Anmeldeinformationen desselben Azure-Mandanten verwendet werden.

## <a name="the-dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files-and-other-state"></a>Der DC-Agent kann Kennwortrichtliniendateien und andere Zustandsdateien nicht verschlüsseln oder entschlüsseln.

Dieses Problem kann sich durch unterschiedlichste Symptome äußern, in der Regel hat es aber immer die gleiche Grundursache.

Der Azure AD-Kennwortschutz weist eine kritische Abhängigkeit von der Ver- und Entschlüsselungsfunktion des Microsoft-Schlüsselverteilungsdiensts (Key Distribution Service, KDS) auf, der auf Domänencontrollern unter Windows Server 2012 und höher verfügbar ist. Der Schlüsselverteilungsdienst muss auf allen Domänencontrollern in einer Domäne, auf denen Windows Server 2012 und höher ausgeführt wird, aktiviert und funktionsfähig sein.

Der Startmodus des Schlüsselverteilungsdiensts ist standardmäßig auf „Manuell“ (Start durch Auslöser) festgelegt. Bei dieser Konfiguration wird der Dienst bei Bedarf gestartet, wenn ein Client zum ersten Mal versucht, ihn zu verwenden. Der Standardstartmodus des Diensts ist für die Verwendung des Azure AD-Kennwortschutzes akzeptabel.

Wenn der Startmodus des Schlüsselverteilungsdiensts auf „Deaktiviert“ festgelegt wurde, muss die Konfiguration geändert werden, damit der Azure AD-Kennwortschutz einwandfrei funktioniert.

Für dieses Problem kann ein einfacher Test durchgeführt werden: Starten Sie den Schlüsselverteilungsdienst manuell über die MMC-Konsole der Dienstverwaltung oder mit einem anderen Tool für die Dienstverwaltung (führen Sie z. B. „net start kdssvc“ in einer Eingabeaufforderungskonsole aus). Der Schlüsselverteilungsdienst sollte erfolgreich gestartet und weiter ausgeführt werden.

Wenn der Schlüsselverteilungsdienst nicht gestartet werden kann, liegt dies in den meisten Fällen daran, dass sich das Active Directory-Domänencontrollerobjekt außerhalb der Standardorganisationseinheit der Domänencontroller befindet. Diese Konfiguration wird nicht vom Schlüsselverteilungsdienst unterstützt und ist keine Einschränkung, die der Azure AD-Kennwortschutz erfordert. Sie können das Problem beheben, indem Sie das Domänencontrollerobjekt an einen Speicherort unter der Standardorganisationseinheit der Domänencontroller verschieben.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Unsichere Kennwörter werden akzeptiert, obwohl dies nicht der Fall sein sollte.

Dieses Problem kann mehrere Ursachen haben.

1. Ihr(e) DC-Agent(s) kann bzw. können eine Richtlinie nicht herunterladen oder vorhandene Richtlinien nicht entschlüsseln. Überprüfen Sie in den obigen Abschnitten, ob eines der dort beschriebenen Probleme die Ursache ist.

1. Der Erwingungsmodus der Kennwortrichtlinie ist noch auf „Überwachung“ eingestellt. Wenn diese Konfiguration aktiv ist, konfigurieren Sie den Modus im Azure AD-Kennwortschutzportal neu, und legen Sie „Erzwingen“ fest. Siehe [Aktivieren des Kennwortschutzes](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. Die Kennwortrichtlinie wurde deaktiviert. Wenn diese Konfiguration aktiv ist, konfigurieren Sie die Richtlinie im Azure AD-Kennwortschutzportal neu, und legen Sie „Aktiviert“ fest. Siehe [Aktivieren des Kennwortschutzes](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. Sie haben die DC-Agent-Software nicht auf allen Domänencontrollern in der Domäne installiert. In diesem Fall ist es schwierig sicherzustellen, dass Windows-Remoteclients während einer Kennwortänderung einen bestimmten Domänencontroller als Ziel verwenden. Wenn Sie der Meinung sind, dass Sie einen bestimmten Domänencontroller mit der DC-Agent-Software erfolgreich als Ziel festgelegt haben, können Sie dies im DC-Agent-Administratorereignisprotokoll nochmals überprüfen: Das Ergebnis der Kennwortüberprüfung wird unabhängig von seinem Wert in mindestens einem Ereignis dokumentiert. Ist für den Benutzer, dessen Kennwort geändert wird, kein Ereignis vorhanden, wurde die Kennwortänderung wahrscheinlich von einem anderen Domänencontroller verarbeitet.

   Alternativ können Sie folgenden Test durchführen: Versuchen Sie, Kennwörter festzulegen bzw. zu ändern, während Sie direkt bei einem Domänencontroller angemeldet sind, auf dem die DC-Agent-Software installiert ist. Diese Vorgehensweise wird nicht für Active Directory-Produktionsdomänen empfohlen.

   Die inkrementelle Bereitstellung der DC-Agent-Software wird zwar unterstützt (mit den obigen Einschränkungen), Microsoft empfiehlt jedoch ausdrücklich, die DC-Agent-Software so bald wie möglich auf allen Domänencontrollern in einer Domäne zu installieren.

1. Möglicherweise funktioniert der Kennwortüberprüfungsalgorithmus wie erwartet. Siehe [Auswerten von Kennwörtern](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="directory-services-repair-mode"></a>Reparaturmodus für Verzeichnisdienste

Wenn der Domänencontroller im Reparaturmodus für Verzeichnisdienste gestartet wird, erkennt dies der DC-Agent-Dienst, und er veranlasst unabhängig von der derzeit aktiven Richtlinienkonfiguration die Deaktivierung aller Kennwortüberprüfungs- oder Erzwingungsaktivitäten.

## <a name="emergency-remediation"></a>Notfallbereinigung

Wenn der DC-Agent-Dienst Probleme verursacht, kann er sofort heruntergefahren werden. Die DC-Agent-Kennwortfilter-DLL versucht weiterhin, den nicht ausgeführten Dienst aufzurufen, und protokolliert die Warnungsereignisse (10012, 10013), aber alle eingehenden Kennwörter werden währenddessen akzeptiert. Der DC-Agent-Dienst kann dann auch bei Bedarf über den Dienststeuerungs-Manager von Windows mit dem Starttyp „Disabled“ konfiguriert werden.

Als weitere Problembehandlungsmaßnahme könnten Sie im Azure AD-Kennwortschutzportal den Aktivierungmodus auf „Nein“ festlegen. Nachdem die aktualisierte Richtlinie heruntergeladen wurde, wechseln alle DC-Agent-Dienste in einen Ruhemodus, in dem alle Kennwörter unverändert akzeptiert werden. Weitere Informationen finden Sie unter [Erzwingungsmodus](howto-password-ban-bad-on-premises-operations.md#enforce-mode).

## <a name="domain-controller-demotion"></a>Herabstufung des Domänencontrollers

Dies wird unterstützt, um einen Domänencontroller herabzustufen, der noch die DC-Agent-Software ausführt. Administratoren sollten jedoch bedenken, dass die DC-Agent-Software während der Herabstufung weiterhin die Durchsetzung der aktuellen Kennwortrichtlinie erzwingt. Das neue lokale Kennwort des Administratorkontos (angegeben als Teil des Herabstufungsvorgangs) wird wie jedes andere Kennwort überprüft. Microsoft empfiehlt, als Teil eines DC-Herabstufungsvorgangs sichere Kennwörter für lokale Administratorkonten auszuwählen. Jedoch kann die Überprüfung des neuen lokalen Kennworts des Administratorkontos durch die DC-Agent-Software bereits vorhandene Herabstufungsvorgänge stören.

Wenn die Herabstufung erfolgreich abgeschlossen, der Domänencontroller neu gestartet wurde und wieder als normaler Mitgliedsserver ausgeführt wird, kehrt die DC-Agent-Software zur Ausführung im passiven Modus zurück. Sie kann dann zu einem beliebigen Zeitpunkt deinstalliert werden.

## <a name="removal"></a>Entfernen

Wenn die Software des Azure AD-Kennwortschutzes deinstalliert und alle zugehörigen Zustandsdaten in den Domänen und der Gesamtstruktur bereinigt werden sollen, gehen Sie wie folgt vor:

> [!IMPORTANT]
> Es ist wichtig, diese Schritte der Reihe nach auszuführen. Wenn eine Instanz des Proxydiensts weiterhin ausgeführt wird, wird sie ihr „serviceConnectionPoint“-Objekt regelmäßig neu erstellen. Wenn eine Instanz des DC-Agent-Diensts weiterhin ausgeführt wird, wird sie ihr „serviceConnectionPoint“-Objekt und den „sysvol“-Status regelmäßig neu erstellen.

1. Deinstallieren Sie die Proxysoftware von allen Computern. Dieser Schritt erfordert **keinen** Neustart.
2. Deinstallieren Sie die DC-Agent-Software von allen Domänencontrollern. Dieser Schritt **erfordert** einen Neustart.
3. Entfernen Sie manuell alle Proxydienst-Verbindungspunkte in jedem Domänennamenkontext. Der Speicherort dieser Objekte kann möglicherweise mit dem folgenden Active Directory-PowerShell-Befehl erkannt werden:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Lassen Sie nicht das Sternchen („*“) am Ende des Variablenwerts „$keywords“ aus.

   Die mit dem Befehl `Get-ADObject` gefundenen Ergebnisobjekte können dann an `Remove-ADObject` weitergeleitet oder manuell gelöscht werden.

4. Entfernen Sie manuell alle DC-Agent-Verbindungspunkte in jedem Domänennamenkontext. Je nachdem, wie umfassend die Software bereitgestellt wurde, kann pro Domänencontroller in der Gesamtstruktur eines dieser Objekte vorhanden sein. Der Speicherort dieser Objekte kann möglicherweise mit dem folgenden Active Directory-PowerShell-Befehl erkannt werden:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Die mit dem Befehl `Get-ADObject` gefundenen Ergebnisobjekte können dann an `Remove-ADObject` weitergeleitet oder manuell gelöscht werden.

   Lassen Sie nicht das Sternchen („*“) am Ende des Variablenwerts „$keywords“ aus.

5. Entfernen Sie manuell den Konfigurationsstatus auf Gesamtstrukturebene. Der Konfigurationsstatus der Gesamtstruktur wird in einem Container im Active Directory-Konfigurationsnamenskontext beibehalten. Er kann wie folgt ermittelt und gelöscht werden:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Entfernen Sie manuell den gesamten auf SYSVOL bezogenen Status durch manuelles Löschen des folgenden Ordners und seines gesamten Inhalts:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Falls erforderlich, kann auch lokal auf einem bestimmten Domänencontroller auf diesen Pfad zugegriffen werden; der Standardspeicherort entspräche etwa dem folgenden Pfad:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Dieser Pfad ist anders, wenn die SYSVOL-Freigabe an einem nicht standardmäßigen Speicherort konfiguriert wurde.

## <a name="next-steps"></a>Nächste Schritte

[Häufig gestellte Fragen zum Azure AD-Kennwortschutz](howto-password-ban-bad-on-premises-faq.md)

Weitere Informationen zu der Liste global gesperrter und benutzerdefinierter gesperrter Kennwörter finden Sie im Artikel [Beseitigen falscher Kennwörter in Ihrer Organisation](concept-password-ban-bad.md)
