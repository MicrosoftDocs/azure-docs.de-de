---
title: Problembehandlung beim lokalen Azure AD-Kennwortschutz
description: Lernen Sie die Problembehandlung beim Azure AD-Kennwortschutz in einer lokalen Active Directory Domain Services-Umgebung kennen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ca00785bfe8a99b8a3d620559c4fa492ee60c63
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741744"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>Problembehandlung: Lokaler Azure AD-Kennwortschutz

Nach der Bereitstellung des Azure AD-Kennwortschutzes ist möglicherweise eine Problembehandlung erforderlich. In diesem Artikel werden einige allgemeine Problembehandlungsschritte ausführlich beschrieben, damit Sie diese besser verstehen können.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>Der DC-Agent hat einen Proxy nicht im Verzeichnis gefunden.

Das wichtigste Anzeichen für dieses Problem sind Ereignisse vom Typ „30017“ im Administratorereignisprotokoll des DC-Agents.

Das Problem ist in der Regel darauf zurückzuführen, dass ein Proxy noch nicht registriert wurde. Wenn ein Proxy registriert wurde, kann es aufgrund der AD-Replikationswartezeit zu einer Verzögerung kommen, bevor ein bestimmter DC-Agent den Proxy sehen kann.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>Der DC-Agent kann nicht mit einem Proxy kommunizieren.

Das wichtigste Anzeichen für dieses Problem sind Ereignisse vom Typ „30018“ im Administratorereignisprotokoll des DC-Agents. Dieses Problem kann mehrere mögliche Ursachen haben:

1. Der DC-Agent befindet sich in einem isolierten Teil des Netzwerks, der keine Netzwerkkonnektivität mit dem bzw. den registrierten Proxy(s) zulässt. Dieses Problem ist harmlos, solange andere DC-Agents mit den Proxys kommunizieren können, um Kennwortrichtlinien aus Azure herunterzuladen. Nach dem Herunterladen werden diese Richtlinien dann vom isolierten Domänencontroller (DC) per Replikation der Richtliniendateien in der SYSVOL-Freigabe abgerufen.

1. Der Proxyhostcomputer blockiert den Zugriff auf den RPC-Endpunktzuordnungsendpunkt (Port 135).

   Das Installationsprogramm für den Proxy des Azure AD-Kennwortschutzes erstellt automatisch eine eingehende Windows-Firewallregel, die den Zugriff auf Port 135 zulässt. Wird diese Regel später gelöscht oder deaktiviert, können DC-Agents nicht mit dem Proxydienst kommunizieren. Wenn die integrierte Windows-Firewall deaktiviert wurde, um stattdessen ein anderes Firewallprodukt zu verwenden, müssen Sie diese andere Firewall zum Zulassen des Zugriffs auf Port 135 konfigurieren.

1. Der Proxyhostcomputer blockiert den Zugriff auf den RPC-Endpunkt (dynamisch oder statisch), an dem der Proxydienst lauscht.

   Das Installationsprogramm für den Proxy des Azure AD-Kennwortschutzes erstellt automatisch eine eingehende Windows-Firewallregel, die den Zugriff auf alle eingehenden Ports zulässt, an denen der Proxydienst des Azure AD-Kennwortschutzes lauscht. Wird diese Regel später gelöscht oder deaktiviert, können DC-Agents nicht mit dem Proxydienst kommunizieren. Wenn die integrierte Windows-Firewall deaktiviert wurde, um stattdessen ein anderes Firewallprodukt zu verwenden, müssen Sie diese andere Firewall zum Zulassen des Zugriffs auf alle eingehenden Ports konfigurieren, an denen der Proxydienst des Azure AD-Kennwortschutzes lauscht. Diese Konfiguration kann präzisiert werden, wenn der Proxydienst zum Lauschen an einem bestimmten statischen RPC-Port konfiguriert wurde (mit dem Cmdlet `Set-AzureADPasswordProtectionProxyConfiguration`).

1. Der Proxyhostcomputer ist nicht so konfiguriert, dass Anmeldungen beim Computer durch Domänencontroller zulässig sind. Dieses Verhalten wird über die Zuweisung der Benutzerberechtigung „Auf diesen Computer vom Netzwerk aus zugreifen“ gesteuert. Allen Domänencontrollern in allen Domänen in der Gesamtstruktur muss diese Berechtigung erteilt werden. Diese Einstellung wird häufig im Rahmen eines größeren Aufwands für die Netzwerkhärtung eingeschränkt.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>Der Proxydienst kann nicht mit Azure kommunizieren

1. Stellen Sie sicher, dass der Proxycomputer über Konnektivität mit den Endpunkten verfügt, die in den [Bereitstellungsanforderungen](howto-password-ban-bad-on-premises-deploy.md) aufgeführt sind.

1. Stellen Sie sicher, dass die Gesamtstruktur und alle Proxyserver für denselben Azure-Mandanten registriert sind.

   Sie können diese Anforderung überprüfen, indem Sie die PowerShell-Cmdlets `Get-AzureADPasswordProtectionProxy` und `Get-AzureADPasswordProtectionDCAgent` ausführen und dann die `AzureTenant`-Eigenschaft für jedes zurückgegebene Element vergleichen. Zur ordnungsgemäßen Ausführung muss der gemeldete Mandantenname für alle DC-Agents und Proxyserver identisch sein.

   Wenn ein Registrierungskonflikt bei einem Azure-Mandanten vorliegt, kann dieser durch Ausführen des PowerShell-Cmdlets `Register-AzureADPasswordProtectionProxy` und/oder `Register-AzureADPasswordProtectionForest` (je nach Bedarf) behoben werden, indem sichergestellt wird, dass für alle Registrierungen die Anmeldeinformationen desselben Azure-Mandanten verwendet werden.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>Der DC-Agent kann Kennwortrichtliniendateien nicht ver- oder entschlüsseln

Der Azure AD-Kennwortschutz weist eine kritische Abhängigkeit von der Ver- und Entschlüsselungsfunktion des Microsoft-Schlüsselverteilungsdiensts (Key Distribution Service, KDS) auf. Verschlüsselungs- oder Entschlüsselungsfehler können mit einer Vielzahl von Symptomen auftreten und verschiedene mögliche Ursachen haben.

1. Stellen Sie sicher, dass der Schlüsselverteilungsdienst auf allen Windows Server 2012-Domänencontrollern (und höher) in einer Domäne aktiviert und funktionsfähig ist.

   Der Startmodus des Schlüsselverteilungsdiensts ist standardmäßig auf „Manuell“ (Start durch Auslöser) festgelegt. Bei dieser Konfiguration wird der Dienst bei Bedarf gestartet, wenn ein Client zum ersten Mal versucht, ihn zu verwenden. Der Standardstartmodus des Diensts ist für die Verwendung des Azure AD-Kennwortschutzes akzeptabel.

   Wenn der Startmodus des Schlüsselverteilungsdiensts auf „Deaktiviert“ festgelegt wurde, muss die Konfiguration geändert werden, damit der Azure AD-Kennwortschutz einwandfrei funktioniert.

   Für dieses Problem kann ein einfacher Test durchgeführt werden: Starten Sie den Schlüsselverteilungsdienst manuell über die MMC-Konsole der Dienstverwaltung oder mit einem anderen Verwaltungstool (führen Sie z. B. „net start kdssvc“ in einer Eingabeaufforderungskonsole aus). Der Schlüsselverteilungsdienst sollte erfolgreich gestartet und weiter ausgeführt werden.

   Wenn der Schlüsselverteilungsdienst nicht gestartet werden kann, liegt dies in den meisten Fällen daran, dass sich das Active Directory-Domänencontrollerobjekt außerhalb der Standardorganisationseinheit der Domänencontroller befindet. Diese Konfiguration wird nicht vom Schlüsselverteilungsdienst unterstützt und ist keine Einschränkung, die der Azure AD-Kennwortschutz erfordert. Sie können das Problem beheben, indem Sie das Domänencontrollerobjekt an einen Speicherort unter der Standardorganisationseinheit der Domänencontroller verschieben.

1. Nicht kompatible, vom Schlüsselverteilungsdienst verschlüsselte Pufferformatänderungen zwischen Windows Server 2012 R2 und Windows Server 2016

   In Windows Server 2016 wurde ein Sicherheitsfix für den Schlüsselverteilungsdienst eingeführt, der das Format der vom Schlüsselverteilungsdienst verschlüsselten Puffer modifiziert; bei der Entschlüsselung dieser Puffer treten unter Windows Server 2012 und Windows Server 2012 R2 manchmal Fehler auf. Die umgekehrte Richtung ist in Ordnung. Puffer, die unter Windows Server 2012 und Windows Server 2012 R2 vom Schlüsselverteilungsdienst verschlüsselt wurden, werden unter Windows Server 2016 und höher immer erfolgreich entschlüsselt. Wenn auf den Domänencontrollern in Ihren Active Directory-Domänen eine Kombination dieser Betriebssysteme ausgeführt wird, können gelegentlich Entschlüsselungsfehler beim Azure AD-Kennwortschutz gemeldet werden. Aufgrund der Art des Sicherheitsfixes ist es nicht möglich, das zeitliche Auftreten oder die Symptome dieser Fehler genau vorherzusagen. Aus diesem Grund ist nicht vorher bestimmbar, welcher DC-Agent für den Azure AD-Kennwortschutz auf welchem Domänencontroller die Daten zu einem bestimmten Zeitpunkt verschlüsselt.

   Als Problemumgehung kann nur empfohlen werden, keine Kombination dieser inkompatiblen Betriebssysteme in Active Directory-Domänen auszuführen. Anders ausgedrückt: Sie sollten nur Windows Server 2012- und Windows Server 2012 R2-Domänencontroller oder nur Windows Server 2016-Domänencontroller (und höher) ausführen.

## <a name="dc-agent-thinks-the-forest-has-not-been-registered"></a>Der DC-Agent geht davon aus, dass die Gesamtstruktur nicht registriert wurde

Dieses Problem äußert sich in 30016-Ereignissen, die im DC-Agent/Administratorkanal in etwa wie folgt protokolliert werden:

```text
The forest has not been registered with Azure. Password policies cannot be downloaded from Azure unless this is corrected.
```

Für dieses Problem gibt es zwei mögliche Ursachen:

1. Die Gesamtstruktur wurde nicht registriert. Dann können Sie das Problem umgehen, indem Sie den Befehl „Register-AzureADPasswordProtectionForest“ wie in den [Bereitstellungsanforderungen](howto-password-ban-bad-on-premises-deploy.md) beschrieben ausführen.
1. Die Gesamtstruktur wurde registriert, aber der DC-Agent kann die zugehörigen Registrierungsdaten nicht entschlüsseln. In diesem Fall liegt dieselbe Grundursache wie für das oben unter der Überschrift [Der DC-Agent kann Kennwortrichtliniendateien nicht ver- oder entschlüsseln](howto-password-ban-bad-on-premises-troubleshoot.md#dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files) beschriebene Problem vor. Sie können diese Theorie ganz einfach überprüfen, weil dieser Fehler nur für DC-Agents angezeigt wird, die in Domänencontrollern für Windows Server 2012 oder Windows Server 2012R2 ausgeführt werden. DC-Agents, die in Domänencontrollern für Windows Server 2016 und höher ausgeführt werden, stellen keine Probleme dar. Auch in diesem Fall können Sie das Problem umgehen, indem Sie alle Domänencontroller auf Windows Server 2016 oder höher aktualisieren.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Unsichere Kennwörter werden akzeptiert, obwohl dies nicht der Fall sein sollte.

Dieses Problem kann mehrere Ursachen haben.

1. Auf Ihren DC-Agents wird eine öffentliche Vorschauversion der Software ausgeführt, die abgelaufen ist. Informationen dazu finden Sie unter [Öffentliche Vorschauversion der DC-Agent-Software ist abgelaufen](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired).

1. Ihr(e) DC-Agent(s) kann bzw. können eine Richtlinie nicht herunterladen oder vorhandene Richtlinien nicht entschlüsseln. Überprüfen Sie in den obigen Abschnitten, ob eines der dort beschriebenen Probleme die Ursache ist.

1. Der Erwingungsmodus der Kennwortrichtlinie ist noch auf „Überwachung“ eingestellt. Wenn diese Konfiguration aktiv ist, konfigurieren Sie den Modus im Azure AD-Kennwortschutzportal neu, und legen Sie „Erzwingen“ fest. Weitere Informationen finden Sie unter [Betriebsmodi](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. Die Kennwortrichtlinie wurde deaktiviert. Wenn diese Konfiguration aktiv ist, konfigurieren Sie die Richtlinie im Azure AD-Kennwortschutzportal neu, und legen Sie „Aktiviert“ fest. Weitere Informationen finden Sie unter [Betriebsmodi](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. Sie haben die DC-Agent-Software nicht auf allen Domänencontrollern in der Domäne installiert. In diesem Fall ist es schwierig sicherzustellen, dass Windows-Remoteclients während einer Kennwortänderung einen bestimmten Domänencontroller als Ziel verwenden. Wenn Sie der Meinung sind, dass Sie einen bestimmten Domänencontroller mit der DC-Agent-Software erfolgreich als Ziel festgelegt haben, können Sie dies im DC-Agent-Administratorereignisprotokoll nochmals überprüfen: Das Ergebnis der Kennwortüberprüfung wird unabhängig von seinem Wert in mindestens einem Ereignis dokumentiert. Ist für den Benutzer, dessen Kennwort geändert wird, kein Ereignis vorhanden, wurde die Kennwortänderung wahrscheinlich von einem anderen Domänencontroller verarbeitet.

   Alternativ können Sie folgenden Test durchführen: Versuchen Sie, Kennwörter festzulegen bzw. zu ändern, während Sie direkt bei einem Domänencontroller angemeldet sind, auf dem die DC-Agent-Software installiert ist. Diese Vorgehensweise wird nicht für Active Directory-Produktionsdomänen empfohlen.

   Die inkrementelle Bereitstellung der DC-Agent-Software wird zwar unterstützt (mit den obigen Einschränkungen), Microsoft empfiehlt jedoch ausdrücklich, die DC-Agent-Software so bald wie möglich auf allen Domänencontrollern in einer Domäne zu installieren.

1. Möglicherweise funktioniert der Kennwortüberprüfungsalgorithmus wie erwartet. Siehe [Auswerten von Kennwörtern](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Fehler beim Festlegen eines unsicheren DSRM-Kennworts über „Ntdsutil.exe“

Active Directory führt für ein neues DSRM-Kennwort (Directory Services Repair Mode, Reparaturmodus für Verzeichnisdienste) stets eine Überprüfung durch, um sicherzustellen, dass es die Kennwortkomplexitätsanforderungen der Domäne erfüllt. Bei dieser Überprüfung werden auch Kennwortfilter-DLLs wie z.B. der Azure AD-Kennwortschutz einbezogen. Wenn das neue DSRM-Kennwort abgelehnt wird, wird die folgende Fehlermeldung angezeigt:

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

Für die vom Azure AD-Kennwortschutz erfassten Ereignisprotokolle zur Kennwortüberprüfung für ein Active Directory-DSRM-Kennwort wird erwartet, dass die Ereignisprotokollmeldungen keinen Benutzernamen enthalten. Dieses Verhalten tritt auf, weil das DSRM-Konto ein lokales Konto ist, das nicht der eigentlichen Active Directory-Domäne angehört.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>Fehler beim Höherstufen des Domänencontrollerreplikats aufgrund eines unsicheren DSRM-Kennworts

Beim Höherstufen des Domänencontrollers wird das neue DSRM-Kennwort zur Überprüfung an einen vorhandenen Domänencontroller in der Domäne übermittelt. Wenn das neue DSRM-Kennwort abgelehnt wird, wird die folgende Fehlermeldung angezeigt:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

Ebenso wie beim Problem oben enthält das ausgegebene Ereignis der Überprüfung durch den Azure AD-Kennwortschutz für dieses Szenario leere Benutzernamen.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>Fehler beim Herabstufen des Domänencontrollers aufgrund eines unsicheren lokalen Administratorkennworts

Dies wird unterstützt, um einen Domänencontroller herabzustufen, der noch die DC-Agent-Software ausführt. Administratoren sollten jedoch bedenken, dass die DC-Agent-Software während der Herabstufung weiterhin die Durchsetzung der aktuellen Kennwortrichtlinie erzwingt. Das neue lokale Kennwort des Administratorkontos (angegeben als Teil des Herabstufungsvorgangs) wird wie jedes andere Kennwort überprüft. Microsoft empfiehlt im Rahmen der Herabstufung eines Domänencontrollers die Auswahl sicherer Kennwörter für lokale Administratorkonten.

Wenn die Herabstufung erfolgreich abgeschlossen, der Domänencontroller neu gestartet wurde und wieder als normaler Mitgliedsserver ausgeführt wird, kehrt die DC-Agent-Software zur Ausführung im passiven Modus zurück. Sie kann dann zu einem beliebigen Zeitpunkt deinstalliert werden.

## <a name="booting-into-directory-services-repair-mode"></a>Start im Reparaturmodus für Verzeichnisdienste

Wenn der Domänencontroller im Reparaturmodus für Verzeichnisdienste gestartet wird, erkennt dies die DC-Agent-Kennwortfilter-DLL und veranlasst unabhängig von der derzeit aktiven Richtlinienkonfiguration die Deaktivierung aller Kennwortüberprüfungs- oder Erzwingungsaktivitäten. Die DC-Agent-Kennwortfilter-DLL protokolliert ein Warnungsereignis vom Typ „10023“ im Administratorereignisprotokoll. Beispiel:

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>Öffentliche Vorschauversion der DC-Agent-Software ist abgelaufen

Während der öffentlichen Vorschauphase des Azure AD-Kennwortschutzes war die DC-Agent-Software hartcodiert, um die Verarbeitung von Kennwortüberprüfungsanforderungen an den folgenden Terminen zu beenden:

* Version 1.2.65.0 beendet die Verarbeitung von Kennwortüberprüfungsanforderungen am 1. September 2019.
* Version 1.2.25.0 und frühere Versionen haben die Verarbeitung von Kennwortüberprüfungsanforderungen am 1. Juli 2019 beendet.

Bei Näherrücken des Termins geben alle zeitlich begrenzten DC-Agent-Versionen zum Startzeitpunkt ein Ereignis von Typ „10021“ im DC-Agent-Administratorereignisprotokoll aus, das Folgendem ähnelt:

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

Nach Ablauf des Termins geben alle zeitlich begrenzten DC-Agent-Versionen zum Startzeitpunkt ein Ereignis von Typ „10022“ im DC-Agent-Administratorereignisprotokoll aus, das Folgendem ähnelt:

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

Da der Ablauftermin nur beim ersten Start geprüft wird, werden diese Ereignisse möglicherweise erst lange nach Ablauf der Frist angezeigt. Wenn der Ablauf der Frist erkannt wird, treten keine negativen Auswirkungen für den Domänencontroller oder die größere Umgebung auf, außer dass alle Kennwörter automatisch genehmigt werden.

> [!IMPORTANT]
> Microsoft empfiehlt, für abgelaufene DC-Agents in der öffentlichen Vorschauversion sofort ein Upgrade auf die neueste Version durchzuführen.

Eine einfache Möglichkeit, um DC-Agents in Ihrer Umgebung zu ermitteln, für die ein Upgrade durchgeführt werden muss, ist das Ausführen des Cmdlets `Get-AzureADPasswordProtectionDCAgent`. Beispiel:

```powershell
PS C:\> Get-AzureADPasswordProtectionDCAgent

ServerFQDN            : bpl1.bpl.com
SoftwareVersion       : 1.2.125.0
Domain                : bpl.com
Forest                : bpl.com
PasswordPolicyDateUTC : 8/1/2019 9:18:05 PM
HeartbeatUTC          : 8/1/2019 10:00:00 PM
AzureTenant           : bpltest.onmicrosoft.com
```

In diesem Zusammenhang enthält das Feld „SoftwareVersion“ natürlich die wichtigste Eigenschaft. Sie können auch die PowerShell-Filterung verwenden, um DC-Agents herauszufiltern, die bereits über mindestens die erforderliche Basisversion verfügen. Beispiel:

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

Die Azure AD-Kennwortschutz-Proxysoftware ist in keiner Version zeitlich begrenzt. Microsoft empfiehlt dennoch, dass sowohl für DC- als auch Proxy-Agents ein Upgrade auf die neuesten Versionen durchgeführt wird, sobald diese veröffentlicht werden. Mithilfe des Cmdlets `Get-AzureADPasswordProtectionProxy` kann nach Proxy-Agents gesucht werden, für die ein Upgrade erforderlich ist, ähnlich wie im Beispiel oben für DC-Agents.

Weitere Informationen zu bestimmten Upgradeverfahren finden Sie unter [Upgrade des DC-Agents](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) und [Upgrade des Proxydiensts](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service).

## <a name="emergency-remediation"></a>Notfallbereinigung

Wenn der DC-Agent-Dienst Probleme verursacht, kann er sofort heruntergefahren werden. Die DC-Agent-Kennwortfilter-DLL versucht weiterhin, den nicht ausgeführten Dienst aufzurufen, und protokolliert die Warnungsereignisse (10012, 10013), aber alle eingehenden Kennwörter werden währenddessen akzeptiert. Der DC-Agent-Dienst kann dann auch bei Bedarf über den Dienststeuerungs-Manager von Windows mit dem Starttyp „Disabled“ konfiguriert werden.

Als weitere Problembehandlungsmaßnahme könnten Sie im Azure AD-Kennwortschutzportal den Aktivierungmodus auf „Nein“ festlegen. Nachdem die aktualisierte Richtlinie heruntergeladen wurde, wechseln alle DC-Agent-Dienste in einen Ruhemodus, in dem alle Kennwörter unverändert akzeptiert werden. Weitere Informationen finden Sie unter [Betriebsmodi](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

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
