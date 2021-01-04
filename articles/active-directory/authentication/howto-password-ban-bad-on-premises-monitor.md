---
title: Überwachen des lokalen Azure AD-Kennwortschutzes
description: Erfahren Sie, wie Sie in einer lokalen Active Directory Domain Services-Umgebung die Protokolle für den Azure AD-Kennwortschutz überwachen und überprüfen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: edc246a414401c4c1c0248787eda0381fcd63037
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741761"
---
# <a name="monitor-and-review-logs-for-on-premises-azure-ad-password-protection-environments"></a>Überwachen und Überprüfen der Protokolle für lokale Umgebungen mit Azure AD-Kennwortschutz

Nach der Bereitstellung des Azure AD-Kennwortschutzes sind Überwachung und Berichterstellung zentrale Aufgaben. Dieser Artikel enthält detaillierte Informationen, damit Sie verschiedene Überwachungstechniken verstehen, einschließlich der Speicherorte, an denen die einzelnen Dienste Informationen protokollieren, und der Methoden zum Berichten über die Verwendung des Azure AD-Kennwortschutzes.

Überwachung und Berichterstellung erfolgen entweder durch Ereignisprotokollmeldungen oder durch Ausführen von PowerShell-Cmdlets. Ereignisprotokollmeldungen werden sowohl vom DC-Agent als auch von Proxydiensten protokolliert. Alle nachfolgend beschriebenen PowerShell-Cmdlets sind nur auf dem Proxyserver verfügbar (siehe PowerShell-Modul „AzureADPasswordProtection“). Die DC-Agent-Software installiert kein PowerShell-Modul.

## <a name="dc-agent-event-logging"></a>DC-Agent-Ereignisprotokollierung

Auf jedem Domänencontroller schreibt die DC-Agent-Dienstsoftware die Ergebnisse der einzelnen Kennwortüberprüfungsvorgänge (und andere Status) in ein lokales Ereignisprotokoll:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Das DC-Agent-Administratorprotokoll ist die Hauptquelle für Informationen zum Verhalten der Software.

Beachten Sie, dass das Ablaufverfolgungsprotokoll standardmäßig deaktiviert ist.

Durch die verschiedenen DC-Agent-Komponenten protokollierte Ereignisse fallen in die folgenden Bereiche:

|Komponente |Ereignis-ID-Bereich|
| --- | --- |
|DC-Agent-Kennwortfilter-DLL| 10000-19999|
|DC-Agent-Diensthostprozess| 20000-29999|
|Validierungslogik für DC-Agent-Dienstrichtlinie| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>DC-Agent-Administratorereignisprotokoll

### <a name="password-validation-outcome-events"></a>Ergebnisereignisse der Kennwortvalidierung

Auf jedem Domänencontroller schreibt die DC-Agent-Dienstsoftware die Ergebnisse der einzelnen Kennwortüberprüfungsvorgänge in das DC-Agent-Administratorereignisprotokoll.

Für eine erfolgreiche Kennwortvalidierung wird im Allgemeinen ein Ereignis aus der DC-Agent-Kennwortfilter-DLL protokolliert. Für eine nicht erfolgreiche Kennwortvalidierung werden im Allgemeinen zwei Ereignisse protokolliert, eines aus dem DC-Agent-Dienst und eines aus der DC-Agent-Kennwortfilter-DLL.

Diskrete Ereignisse zum Erfassen dieser Situationen werden basierend auf folgenden Faktoren protokolliert:

* Ob ein bestimmtes Kennwort festgelegt oder geändert wird.
* Ob die Validierung eines angegebenen Kennworts erfolgreich oder nicht erfolgreich ist.
* Ob die Validierung aufgrund der globalen Microsoft-Richtlinie, der Organisationsrichtlinie oder einer Kombination aus beiden nicht erfolgreich ist.
* Ob der ausschließliche Überwachungsmodus für die aktuelle Kennwortrichtlinie derzeit aktiviert oder deaktiviert ist.

Die wichtigsten auf die Kennwortüberprüfung bezogenen Ereignisse sind wie folgt:

| Ereignis |Kennwortänderung |Kennwortfestlegung|
| --- | :---: | :---: |
|Pass |10014 |10015|
|Fehler (aufgrund der Kundenkennwortrichtlinie)| 10016, 30002| 10017, 30003|
|Fehler (aufgrund der Microsoft-Kennwortrichtlinie)| 10016, 30004| 10017, 30005|
|Fehler (aufgrund einer Kombination aus der Microsoft- und der Kundenkennwortrichtlinie)| 10016, 30026| 10017, 30027|
|Fehler (aufgrund des Benutzernamens)| 10016, 30021| 10017, 30022|
|Im ausschließlichen Überwachungsmodus bestanden (würde Kundenkennwortrichtlinie nicht bestehen)| 10024, 30008| 10025, 30007|
|Im ausschließlichen Überwachungsmodus bestanden (würde Kennwortrichtlinie von Microsoft nicht bestehen)| 10024, 30010| 10025, 30009|
|Im Nur-Überprüfungsmodus bestanden (würde Kombination aus Microsoft- und Kundenkennwortrichtlinie nicht bestehen)| 10024, 30028| 10025, 30029|
|Im ausschließlichen Überwachungsmodus bestanden (Benutzername hätte zu einem Fehler geführt)| 10016, 30024| 10017, 30023|

Wenn in der obigen Tabelle auf „kombinierte Richtlinien“ verwiesen wird, bezieht sich das auf Situationen, in denen das Kennwort eines Benutzers mindestens ein Token enthält, das sowohl in der Microsoft-Kennwortsperrliste als auch in der Kunden-Kennwortsperrliste auftritt.

Die Fälle in der obigen Tabelle, in denen auf den Benutzernamen verwiesen wird, beziehen sich auf Situationen, in denen das Kennwort eines Benutzers entweder den Kontonamen des Benutzers und/oder einen der Anzeigenamen des Benutzers enthielt. In diesen Szenarien wird das Kennwort des Benutzers jeweils abgelehnt, wenn die Richtlinie auf „Erzwingen“ festgelegt ist, oder zugelassen, wenn sich die Richtlinie im Überwachungsmodus befindet.

Wenn ein Paar von Ereignissen gemeinsam protokolliert wird, sind beide Ereignisse explizit über dieselbe CorrelationId zugeordnet.

### <a name="password-validation-summary-reporting-via-powershell"></a>Zusammenfassende Berichterstellung für die Kennwortvalidierung mithilfe von PowerShell

Mit dem Cmdlet `Get-AzureADPasswordProtectionSummaryReport` kann eine zusammenfassende Darstellung der Kennwortvalidierungsaktivität erstellt werden. Eine Beispielausgabe dieses Cmdlets lautet folgendermaßen:

```powershell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

Der Umfang der Berichterstellung des Cmdlets kann mit einem der –Forest-, –Domain- oder –DomainController-Parameter beeinflusst werden. Keine Angabe eines Parameters bedeutet –Forest.

Die Cmdlet `Get-AzureADPasswordProtectionSummaryReport` funktioniert durch Abfragen des DC-Agent-Administratorereignisprotokolls und anschließendes Zählen der Gesamtanzahl von Ereignissen, die den einzelnen angezeigten Ergebniskategorien entsprechen. Die folgende Tabelle enthält die Zuordnungen zwischen den einzelnen Ergebnissen und der zugehörigen Ereignis-ID:

|Get-AzureADPasswordProtectionSummaryReport-Eigenschaft |Zugehörige Ereignis-ID|
| :---: | :---: |
|PasswordChangesValidated |10014|
|PasswordSetsValidated |10015|
|PasswordChangesRejected |10016|
|PasswordSetsRejected |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

Beachten Sie, dass das Cmdlet `Get-AzureADPasswordProtectionSummaryReport` in PowerShell-Skriptform angeboten wird und bei Bedarf direkt am folgenden Speicherort referenziert werden kann:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> Dieses Cmdlet funktioniert, indem es eine PowerShell-Sitzung mit jedem Domänencontroller öffnet. Damit dies gelingt, muss die Unterstützung für PowerShell-Remotesitzungen auf jedem Domänencontroller aktiviert sein, und der Client muss über ausreichende Berechtigungen verfügen. Weitere Informationen zu Anforderungen für PowerShell-Remotesitzungen führen Sie in einem PowerShell-Fenster „Get-Help about_Remote_Troubleshooting“ aus.

> [!NOTE]
> Bei diesem Cmdlet wird jeweils das Admin-Ereignisprotokoll der einzelnen DC-Agent-Dienste remote abgefragt. Wenn die Ereignisprotokolle eine große Anzahl von Ereignissen enthalten, kann es lange dauern, bis das Cmdlet abgeschlossen ist. Darüber hinaus können umfangreiche Netzwerkabfragen großer Datenmengen die Leistung des Domänencontrollers beeinträchtigen. Daher sollte dieses Cmdlet in Produktionsumgebungen sorgfältig verwendet werden.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Beispiel einer Ereignisprotokollmeldung für Ereignis-ID 10014 – Kennwort erfolgreich geändert

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Beispiel einer Ereignisprotokollmeldung für Ereignis-ID 10017 und 30003 – Kennwort nicht erfolgreich festgelegt

10017:

```text
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```text
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Beispiel einer Ereignisprotokollmeldung für Ereignis-ID 30001 – Kennwort akzeptiert, da keine Richtlinie verfügbar

```text
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Beispiel einer Ereignisprotokollmeldung für Ereignis-ID 30006 – neue Richtlinie wird erzwungen

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>Beispiel einer Ereignisprotokollmeldung für Ereignis-ID 30019 – Azure AD-Kennwortschutz ist deaktiviert

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>DC-Agent-Betriebsprotokoll

Der DC-Agent-Dienst protokolliert auch betriebsbedingte Ereignisse im folgenden Protokoll:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>DC-Agent-Ablaufprotokoll

Der DC-Agent-Dienst kann auch ausführliche Ablaufverfolgungsereignisse auf Debugebene im folgenden Protokoll protokollieren:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Das Ablaufprotokoll ist standardmäßig deaktiviert.

> [!WARNING]
> Wenn es aktiviert ist, empfängt es eine große Anzahl von Ereignissen. Das kann sich auf die Leistung des Domänencontrollers auswirken. Aus diesem Grund sollte dieses verbesserte Protokoll nur aktiviert werden, wenn ein Problem genauere Untersuchung erfordert, und dann nur für einen minimalen Zeitraum.

## <a name="dc-agent-text-logging"></a>DC-Agent-Textprotokoll

Der DC-Agent-Dienst kann zum Schreiben in ein Textprotokoll konfiguriert werden, indem der folgende Registrierungswert festgelegt wird:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

Das Textprotokoll ist standardmäßig deaktiviert. Damit Änderungen an diesem Wert wirksam werden, ist ein Neustart des DC-Agent-Diensts erforderlich. Wenn der Dienst aktiviert ist, schreibt er in eine Protokolldatei unter folgendem Pfad:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> Das Textprotokoll empfängt die gleichen Einträge auf Debugebene, die im Ablaufprotokoll protokolliert werden können, hat jedoch i.d.R. ein zur Überprüfung und Analyse besser geeignetes Format.

> [!WARNING]
> Wenn aktiviert, empfängt dieses Protokoll eine große Anzahl von Ereignissen und kann sich auf die Leistung des Domänencontrollers auswirken. Aus diesem Grund sollte dieses verbesserte Protokoll nur aktiviert werden, wenn ein Problem genauere Untersuchung erfordert, und dann nur für einen minimalen Zeitraum.

## <a name="dc-agent-performance-monitoring"></a>DC-Agent-Leistungsüberwachung

Die DC-Agent-Dienstsoftware installiert ein Leistungsindikatorobjekt mit dem Namen **Azure AD-Kennwortschutz**. Die folgenden Leistungsindikatoren sind derzeit verfügbar:

|Name des Leistungsindikators | BESCHREIBUNG|
| --- | --- |
|Verarbeitete Kennwörter |Dieser Leistungsindikator gibt die Gesamtanzahl der verarbeiteten Kennwörter (akzeptiert oder abgelehnt) seit dem letzten Neustart an.|
|Akzeptierte Kennwörter |Dieser Leistungsindikator gibt die Gesamtanzahl der Kennwörter an, die seit dem letzten Neustart akzeptiert wurden.|
|Abgelehnte Kennwörter |Dieser Leistungsindikator gibt die Gesamtanzahl der Kennwörter an, die seit dem letzten Neustart abgelehnt wurden.|
|Laufende Kennwortfilteranforderungen |Dieser Leistungsindikator zeigt die Anzahl der laufenden Kennwortfilteranforderungen an.|
|Spitzenwert der Kennwortfilteranforderungen |Dieser Leistungsindikator zeigt den Spitzenwert gleichzeitiger Kennwortfilteranforderungen seit dem letzten Neustart an.|
|Kennwortfilteranforderungen-Fehler |Dieser Leistungsindikator gibt die Gesamtzahl der Kennwortfilteranforderungen an, die seit dem letzten Neustart aufgrund eines Fehlers nicht erfolgreich waren. Fehler können auftreten, wenn der DC-Agent-Dienst des Azure AD-Kennwortschutzes nicht ausgeführt wird.|
|Kennwortfilteranforderungen/s |Dieser Leistungsindikator zeigt die Rate an, mit der Kennwörter verarbeitet werden.|
|Verarbeitungszeit der Kennwortfilteranforderung |Dieser Leistungsindikator zeigt die zum Verarbeiten einer Kennwortfilteranforderung durchschnittlich erforderliche Zeit an.|
|Spitzenwert der Verarbeitungszeit der Kennwortfilteranforderung |Dieser Leistungsindikator zeigt den Spitzenwert der Verarbeitungszeit der Kennwortfilteranforderung seit dem letzten Neustart an.|
|Aufgrund des Überwachungsmodus akzeptierte Kennwörter |Dieser Leistungsindikator zeigt die Gesamtzahl der Kennwörter (seit dem letzten Neustart) an, die normalerweise abgelehnt worden wären, jedoch akzeptiert wurden, da die Kennwortrichtlinie für den Überwachungsmodus konfiguriert wurde.|

## <a name="dc-agent-discovery"></a>DC-Agent-Ermittlung

Mit dem `Get-AzureADPasswordProtectionDCAgent`-Cmdlet können grundlegende Informationen über die verschiedenen DC-Agents angezeigt werden, die in einer Domäne oder Gesamtstruktur ausgeführt werden. Diese Informationen werden aus den serviceConnectionPoint-Objekten abgerufen, die von den ausgeführten DC-Agent-Diensten registriert werden.

Eine Beispielausgabe dieses Cmdlets lautet folgendermaßen:

```powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

Die verschiedenen Eigenschaften werden von jedem DC-Agent-Dienst ungefähr stündlich aktualisiert. Die Daten unterliegen nach wie vor der Replikationswartezeit von Active Directory.

Der Bereich der Cmdlet-Abfrage kann durch die Verwendung des Parameters „–Forest“ oder „–Domain“ beeinflusst werden.

Wenn der HeartbeatUTC-Wert veraltet, kann dies darauf hinweisen, dass der DC-Agent des Azure AD-Kennwortschutzes auf diesem Domänencontroller nicht ausgeführt wird oder deinstalliert wurde oder dass der Computer herabgestuft wurde und kein Domänencontroller mehr ist.

Wenn der PasswordPolicyDateUTC-Wert veraltet, kann dies darauf hinweisen, dass der DC-Agent des Azure AD-Kennwortschutzes auf diesem Computer nicht ordnungsgemäß funktioniert.

## <a name="dc-agent-newer-version-available"></a>Neuere DC-Agent-Version verfügbar

Der DC-Agent-Dienst protokolliert im Betriebsprotokoll ein Warnungsereignis vom Typ 30034, wenn eine neuere Version der DC-Agent-Software verfügbar ist. Beispiel:

```text
An update for Azure AD Password Protection DC Agent is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
```

Die Version der neueren Software ist im obigen Ereignis nicht angegeben. Zu dieser Information gelangen Sie über den Link in der Ereignismeldung.

> [!NOTE]
> In der obigen Ereignismeldung ist zwar von „autoupgrade“ (automatisches Upgrade) die Rede, dieses Feature wird jedoch aktuell von der DC-Agent-Software nicht unterstützt.

## <a name="proxy-service-event-logging"></a>Proxydienst-Ereignisprotokollierung

Der Proxydienst sendet einen Mindestsatz von Ereignissen an die folgenden Ereignisprotokolle:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Beachten Sie, dass das Ablaufverfolgungsprotokoll standardmäßig deaktiviert ist.

> [!WARNING]
> Wenn es aktiviert ist, empfängt es eine große Anzahl von Ereignissen. Das kann sich auf die Leistung des Proxyhosts auswirken. Aus diesem Grund sollte dieses Protokoll nur aktiviert werden, wenn ein Problem genauere Untersuchung erfordert, und dann nur für einen minimalen Zeitraum.

Ereignisse werden durch die verschiedenen Proxykomponenten mit den folgenden Bereichen protokolliert:

|Komponente |Ereignis-ID-Bereich|
| --- | --- |
|Proxydienst-Hostprozess| 10000-19999|
|Proxydienst-Kerngeschäftslogik| 20000-29999|
|PowerShell-Cmdlets| 30000-39999|

## <a name="proxy-service-text-logging"></a>Proxydienst-Textprotokoll

Der Proxydienst kann zum Schreiben in ein Textprotokoll konfiguriert werden, indem der folgende Registrierungswert festgelegt wird:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters!EnableTextLogging = 1 (REG_DWORD-Wert)

Das Textprotokoll ist standardmäßig deaktiviert. Damit Änderungen an diesem Wert wirksam werden, ist ein Neustart des Proxydiensts erforderlich. Wenn der Proxydienst aktiviert ist, schreibt er in eine Protokolldatei unter folgendem Pfad:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> Das Textprotokoll empfängt die gleichen Einträge auf Debugebene, die im Ablaufprotokoll protokolliert werden können, hat jedoch i.d.R. ein zur Überprüfung und Analyse besser geeignetes Format.

> [!WARNING]
> Wenn dieses Protokoll aktiviert ist, empfängt es eine große Anzahl von Ereignissen und kann sich auf die Leistung des Computers auswirken. Aus diesem Grund sollte dieses verbesserte Protokoll nur aktiviert werden, wenn ein Problem genauere Untersuchung erfordert, und dann nur für einen minimalen Zeitraum.

## <a name="powershell-cmdlet-logging"></a>PowerShell-Cmdlet-Protokollierung

PowerShell-Cmdlets, die eine Statusänderung verursachen (z.B. Register-AzureADPasswordProtectionProxy), protokollieren normalerweise ein Ergebnisereignis im Betriebsprotokoll.

Darüber hinaus schreiben die meisten PowerShell-Cmdlets des Azure AD-Kennwortschutzes in ein Textprotokoll unter folgendem Pfad:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Wenn ein Cmdlet-Fehler auftritt und sich die Ursache und/oder Lösung nicht direkt ermitteln lässt, können Sie auch diese Textprotokolle anzeigen.

## <a name="proxy-discovery"></a>Proxyermittlung

Mit dem Cmdlet `Get-AzureADPasswordProtectionProxy` können grundlegende Informationen zu den verschiedenen Proxydiensten des Azure AD-Kennwortschutzes angezeigt werden, die in einer Domäne oder Gesamtstruktur ausgeführt werden. Diese Informationen werden aus den serviceConnectionPoint-Objekten abgerufen, die von den ausgeführten Proxydiensten registriert werden.

Eine Beispielausgabe dieses Cmdlets lautet folgendermaßen:

```powershell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

Die verschiedenen Eigenschaften werden von den einzelnen Proxydiensten ungefähr stündlich aktualisiert. Die Daten unterliegen nach wie vor der Replikationswartezeit von Active Directory.

Der Bereich der Cmdlet-Abfrage kann durch die Verwendung des Parameters „–Forest“ oder „–Domain“ beeinflusst werden.

Wenn der HeartbeatUTC-Wert veraltet, kann dies darauf hinweisen, dass der Proxy des Azure AD-Kennwortschutzes auf diesem Computer nicht ausgeführt wird oder deinstalliert wurde.

## <a name="proxy-agent-newer-version-available"></a>Neuere Proxy-Agent-Version verfügbar

Der Proxydienst protokolliert im Betriebsprotokoll ein Warnungsereignis vom Typ 20002, wenn eine neuere Version der Proxysoftware verfügbar ist. Beispiel:

```text
An update for Azure AD Password Protection Proxy is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
.
```

Die Version der neueren Software ist im obigen Ereignis nicht angegeben. Zu dieser Information gelangen Sie über den Link in der Ereignismeldung.

Dieses Ereignis wird auch dann ausgegeben, wenn der Proxy-Agent mit aktiviertem automatischem Upgrade konfiguriert ist.

## <a name="next-steps"></a>Nächste Schritte

[Problembehandlung für den Azure AD-Kennwortschutz](howto-password-ban-bad-on-premises-troubleshoot.md)

Weitere Informationen zu der Liste global gesperrter und benutzerdefinierter gesperrter Kennwörter finden Sie im Artikel [Beseitigen falscher Kennwörter in Ihrer Organisation](concept-password-ban-bad.md)
