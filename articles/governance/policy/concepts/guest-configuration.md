---
title: Informationen zum Überwachen der Inhalte virtueller Computer
description: Hier erfahren Sie, wie Azure Policy mithilfe des Gastkonfigurations-Agents Einstellungen in VMs überprüft.
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: a956d5e8dcfa82f85020928e1427a08ac8fe7a69
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906249"
---
# <a name="understand-azure-policys-guest-configuration"></a>Informationen zu Guest Configuration von Azure Policy

Mit Azure Policy können Einstellungen innerhalb eines Computers überwacht werden. Dies gilt für in Azure ausgeführte Computer sowie für [über Arc verbundene Computer](../../../azure-arc/servers/overview.md). Für die Überprüfung verwenden Sie die Erweiterung und den Client Guest Configuration. Die Erweiterung überprüft über den Client u. a. die folgenden Einstellungen:

- Die Konfiguration des Betriebssystems
- Die Konfiguration oder das Vorhandensein der Anwendung
- Umgebungseinstellungen

Derzeit wird für die meisten Richtliniendefinitionen der Azure Policy-Gastkonfiguration nur eine Überprüfung der Einstellungen auf dem Computer durchgeführt. Es werden keine Konfigurationen angewendet. Eine Ausnahme ist hierbei eine integrierte Richtlinie, die [weiter unten beschrieben ist](#applying-configurations-using-guest-configuration).

## <a name="enable-guest-configuration"></a>Aktivieren der Gastkonfiguration

Überprüfen Sie die folgenden Details, um den Status von Computern in Ihrer Umgebung zu überwachen, einschließlich Computern in Azure und über Arc verbundener Computer.

## <a name="resource-provider"></a>Ressourcenanbieter

Bevor Sie Guest Configuration verwenden können, müssen Sie den Ressourcenanbieter registrieren. Der Ressourcenanbieter wird automatisch registriert, wenn die Zuweisung einer Guest Configuration-Richtlinie über das Portal erfolgt. Hierfür können Sie über das [Portal](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), die [Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell) oder [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) manuell registrieren.

## <a name="deploy-requirements-for-azure-virtual-machines"></a>Bereitstellen von Anforderungen für virtuelle Azure-Computer

Zum Überwachen von Einstellungen innerhalb eines Computers ist eine [VM-Erweiterung](../../../virtual-machines/extensions/overview.md) aktiviert, und der Computer muss über eine systemseitig verwaltete Identität verfügen. Mit der Erweiterung werden anwendbare Richtlinienzuweisungen sowie die entsprechende Konfigurationsdefinition heruntergeladen. Die Identität wird verwendet, um den Computer zu authentifizieren, wenn Lese- und Schreibvorgänge im Gastkonfigurationsdienst durchgeführt werden. Die Erweiterung ist für über Arc verbundene Computer nicht erforderlich, da sie im Agent für über Arc verbundene Computer enthalten ist.

> [!IMPORTANT]
> Die Gastkonfigurationserweiterung und eine verwaltete Identität sind zum Überwachen von virtuellen Azure-Computern erforderlich. Weisen Sie die folgenden Richtlinieninitiative zu, um die Erweiterung im gewünschten Umfang bereitzustellen:
> 
> `Deploy prerequisites to enable Guest Configuration policies on virtual machines`

### <a name="limits-set-on-the-extension"></a>Für die Erweiterung festgelegte Grenzwerte

Um die Auswirkungen der Erweiterung auf die auf dem Computer ausgeführten Anwendungen zu beschränken, darf die Gastkonfiguration höchstens 5 % der CPU auslasten. Diese Einschränkung gilt sowohl für integrierte als auch für angepasste Definitionen. Dies gilt auch für den Gastkonfigurationsdienst im Agent für über Arc verbundene Computer.

### <a name="validation-tools"></a>Überprüfungstools

Auf dem Computer verwendet der Gastkonfigurationsclient lokale Tools zum Ausführen der Überprüfung.

In der folgenden Tabelle sind die lokalen Tools aufgeführt, die unter den jeweiligen unterstützten Betriebssystemen verwendet werden. Bei integrierten Inhalten werden diese Tools in der Gastkonfiguration automatisch geladen.

|Betriebssystem|Überprüfungstool|Notizen|
|-|-|-|
|Windows|[PowerShell Desired State Configuration](/powershell/scripting/dsc/overview/overview) v2| Wird mittels Sideloading in einen Ordner quergeladen, der nur von Azure Policy verwendet wird. Verursacht keinen Konflikt mit Windows PowerShell DSC. PowerShell Core wird nicht zum Systempfad hinzugefügt.|
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Installiert Chef InSpec (Version 2.2.61) am Standardspeicherort und wird dem Systempfad hinzugefügt. Abhängigkeiten für das InSpec-Paket einschließlich Ruby und Python werden ebenfalls installiert. |

### <a name="validation-frequency"></a>Validierungshäufigkeit

Der Guest Configuration-Client prüft alle 5 Minuten, ob neuer Inhalt vorliegt. Nachdem eine Gastzuweisung empfangen wurde, werden die Einstellungen für diese Konfiguration alle 15 Minuten erneut überprüft. Die Ergebnisse werden an den Guest Configuration-Ressourcenanbieter gesendet, wenn die Überwachung abgeschlossen ist. Wenn eine [Auswertungsauslöser](../how-to/get-compliance-data.md#evaluation-triggers)-Richtlinie auftritt, wird der Zustand des Computers an den Guest Configuration-Ressourcenanbieter geschrieben. Dieses Update bewirkt, dass Azure Policy die Azure Resource Manager-Eigenschaften auswertet. Eine bedarfsgesteuerte Auswertung durch Azure Policy ruft den aktuellen Wert beim Guest Configuration-Ressourcenanbieter ab. Es wird jedoch keine neue Überwachung der Konfiguration auf dem Computer ausgelöst.

## <a name="supported-client-types"></a>Unterstützte Clienttypen

Richtliniendefinitionen der Gastkonfiguration enthalten die neuen Versionen. Ältere Versionen von Betriebssystemen, die im Azure Marketplace verfügbar sind, werden ausgeschlossen, wenn der Guest Configuration-Agent nicht kompatibel ist. In der folgenden Tabelle sind die in Azure-Images unterstützten Betriebssysteme aufgeführt:

|Herausgeber|Name|Versionen|
|-|-|-|
|Canonical|Ubuntu Server|14.04–18.04|
|Credativ|Debian|8 und höher|
|Microsoft|Windows Server|2012 und höher|
|Microsoft|Windows-Client|Windows 10|
|OpenLogic|CentOS|7.3 und höher|
|Red Hat|Red Hat Enterprise Linux|7.4 bis 7.8|
|Suse|SLES|12 SP3-SP5|

Benutzerdefinierte Images von virtuellen Computern werden von Richtliniendefinitionen der Gastkonfiguration unterstützt, sofern es sich um eines der Betriebssysteme in der obigen Tabelle handelt.

## <a name="network-requirements"></a>Netzwerkanforderungen

Virtuelle Computer in Azure können entweder ihren lokalen Netzwerkadapter oder einen privaten Link verwenden, um mit dem Gastkonfigurationsdienst zu kommunizieren.

Azure Arc-Computer stellen mithilfe der lokalen Netzwerkinfrastruktur eine Verbindung her, um Azure-Dienste zu erreichen und den Compliancestatus zu melden.

### <a name="communicate-over-virtual-networks-in-azure"></a>Kommunizieren über virtuelle Netzwerke in Azure

Virtuelle Computer, die virtuelle Netzwerke für die Kommunikation verwenden, benötigen ausgehenden Zugriff auf Azure-Rechenzentren an Port `443`. Wenn Sie ein privates virtuelles Netzwerk in Azure verwenden und keinen ausgehenden Datenverkehr zulassen, müssen Ausnahmen über Netzwerksicherheitsgruppen-Regeln konfiguriert werden. Der Diensttag „GuestAndHybridManagement“ kann verwendet werden, um auf den Gastkonfigurationsdienst zu verweisen.

### <a name="communicate-over-private-link-in-azure"></a>Kommunizieren über einen privaten Link in Azure

Für die Kommunikation mit dem Gastkonfigurationsdienst können virtuelle Computer einen [privaten Link](../../../private-link/private-link-overview.md) verwendet. Wenden Sie ein Tag mit dem Namen `EnablePrivateNeworkGC` und dem Wert `TRUE` an, um dieses Feature zu aktivieren. Das Tag kann vor oder nach der Anwendung von Richtliniendefinitionen der Gastkonfiguration auf den Computer angewendet werden.

Der Datenverkehr wird mithilfe der [virtuellen öffentlichen IP-Adresse](../../../virtual-network/what-is-ip-address-168-63-129-16.md) von Azure weitergeleitet, um einen sicheren, authentifizierten Kanal mit Azure-Plattformressourcen einzurichten.

### <a name="azure-arc-connected-machines"></a>Vernetzte Azure Arc-Computer

Knoten, die sich außerhalb von Azure befinden und über Azure Arc verbunden sind, benötigen eine Verbindung mit dem Gastkonfigurationsdienst. Details zu den Netzwerk- und Proxyanforderungen finden Sie in der [Azure Arc-Dokumentation](../../../azure-arc/servers/overview.md).

Für die Kommunikation mit dem Gastkonfigurations-Ressourcenanbieter in Azure benötigen Computer ausgehenden Zugriff auf Azure-Rechenzentren über Port **443**. Wenn ein Netzwerk in Azure keinen ausgehenden Datenverkehr zulässt, müssen Ausnahmen über [Netzwerksicherheitsgruppen](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)-Regeln konfiguriert werden. Der [Diensttag](../../../virtual-network/service-tags-overview.md) „GuestAndHybridManagement“ kann verwendet werden, um auf den Gastkonfigurationsdienst zu verweisen.

Lassen Sie für über Arc verbundene Server in privaten Rechenzentren Datenverkehr über die folgenden Muster zu:

- Port: Für ausgehenden Zugriff auf das Internet ist nur TCP 443 erforderlich
- Globale URL: `*.guestconfiguration.azure.com`

## <a name="managed-identity-requirements"></a>Anforderungen für verwaltete Identitäten

Durch Richtliniendefinitionen in der Initiative [Voraussetzungen zum Aktivieren der Gastkonfigurationsrichtlinien auf VMs bereitstellen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12794019-7a00-42cf-95c2-882eed337cc8) wird eine systemseitig zugewiesene verwaltete Identität aktiviert, falls noch nicht vorhanden. Die Initiative enthält zwei Richtliniendefinitionen, durch die die Identitätserstellung verwaltet wird. Die IF-Bedingungen in den Richtliniendefinitionen gewährleisten das korrekte Verhalten basierend auf dem aktuellen Zustand der Computerressource in Azure.

Wenn der Computer derzeit keine verwalteten Identitäten aufweist, lautet die effektive Richtlinie: [\[Vorschau\]: Systemseitig zugewiesene verwaltete Identität hinzufügen, um Gastkonfigurationszuweisungen auf VMs ohne Identität zu aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e)

Wenn der Computer derzeit über eine benutzerseitig zugewiesene Systemidentität verfügt, lautet die effektive Richtlinie: [\[Vorschau\]: Systemseitig zugewiesene verwaltete Identität hinzufügen, um Gastkonfigurationszuweisungen auf VMs mit einer benutzerseitig zugewiesenen Identität zu aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6)

## <a name="guest-configuration-definition-requirements"></a>Anforderungen an die Guest Configuration-Definition

Die Richtliniendefinitionen der Gastkonfiguration verwenden den Effekt **AuditIfNotExists**. Wenn die Definition zugewiesen ist, verarbeitet ein Back-End-Dienst automatisch den Lebenszyklus aller Anforderungen im Azure-Ressourcenanbieter `Microsoft.GuestConfiguration`.

Die **AuditIfNotExists**-Richtliniendefinitionen geben erst dann Konformitätsergebnisse zurück, wenn alle Anforderungen auf dem Computer erfüllt sind. Eine Beschreibung der Anforderungen finden Sie im Abschnitt [Bereitstellen von Anforderungen für virtuelle Azure-Computer](#deploy-requirements-for-azure-virtual-machines).

> [!IMPORTANT]
> In einem früheren Release der Gastkonfiguration war eine Initiative erforderlich, um die Definitionen **DeployIfNoteExists** und **AuditIfNotExists** zu kombinieren. **DeployIfNotExists**-Definitionen sind nicht mehr erforderlich. Die Definitionen und Initiaitiven sind als `[Deprecated]` gekennzeichnet, aber vorhandene Zuweisungen funktionieren weiterhin. Informationen finden Sie in folgendem Blogbeitrag: [Wichtige Änderung für Überwachungsrichtlinien für Gastkonfigurationen](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)

Azure Policy verwendet die Eigenschaft **complianceStatus** des Gastkonfigurations-Ressourcenanbieters, um die Konformität im Knoten **Konformität** zu melden. Weitere Informationen finden Sie unter [Abrufen von Konformitätsdaten](../how-to/get-compliance-data.md).

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Überwachen von Betriebssystemeinstellungen anhand von Branchenrichtlinien

Eine Initiative in Azure Policy bietet die Möglichkeit, Betriebssystemeinstellungen anhand einer „Baseline“ zu überwachen. Die Definition _\[Vorschau\]: Überwachen von Windows-VMs, die nicht den Einstellungen für Azure-Sicherheitsbaselines entsprechen_ enthält einen Satz von Regeln, die der Active Directory-Gruppenrichtlinie basieren.

Die meisten Einstellungen sind als Parameter verfügbar. Mithilfe von Parametern können Sie anpassen, was überwacht werden soll.
Passen Sie die Richtlinie an Ihre Anforderungen an, oder ordnen Sie die Richtlinie Drittanbieterinformationen (z. B. branchenspezifischen Standards) zu.

Einige Parameter unterstützen einen ganzzahligen Wertebereich. Beispielsweise könnte mit der Einstellung „Maximales Kennwortalter“ die effektive „Gruppenrichtlinie“-Einstellung überwacht werden. Der Bereich „1,70“ würde sicherstellen, dass Benutzer ihre Kennwörter mindestens alle 70 Tage, aber nicht früher als nach einem Tag ändern müssen.

Wenn Sie die Richtlinie mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) zuweisen, verwenden Sie eine Parameterdatei, um Ausnahmen zu verwalten. Checken Sie die Dateien in ein Versionskontrollsystem ein, z. B. Git. Kommentare zu Dateiänderungen geben Aufschluss darüber, warum eine Zuweisung eine Ausnahme vom erwarteten Wert darstellt.

#### <a name="applying-configurations-using-guest-configuration"></a>Anwenden von Konfigurationen mithilfe der Gastkonfiguration

Nur mit der Definition _Konfigurieren der Zeitzone auf Windows-Computern_ werden durch Konfigurieren der Zeitzone Änderungen am Computer vorgenommen. Benutzerdefinierte Richtliniendefinitionen zum Konfigurieren von Einstellungen auf Computern werden nicht unterstützt.

Beim Zuweisen von Definitionen, die mit _Konfigurieren_ beginnen, müssen Sie auch die Definition _Bereitstellen von Voraussetzungen, um die Gastkonfigurationsrichtlinie auf Windows-VMs zu aktivieren_ zuweisen. Sie können diese Definitionen auf Wunsch in einer Initiative kombinieren.

> [!NOTE]
> Die integrierte Zeitzonenrichtlinie ist die einzige Definition, die das Konfigurieren von Einstellungen auf Computern unterstützt. Benutzerdefinierte Richtliniendefinitionen zum Konfigurieren von Einstellungen auf Computern werden nicht unterstützt.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Zuweisen von Richtlinien zu Computern außerhalb von Azure

Die für die Gastkonfiguration verfügbaren Überwachungsrichtliniendefinitionen umfassen den Ressourcentyp **Microsoft.HybridCompute/machines**. Alle Computer, die in [Azure Arc für Server](../../../azure-arc/servers/overview.md) integriert sind und sich im Geltungsbereich der Richtlinienzuweisung befinden, werden automatisch eingeschlossen.

### <a name="multiple-assignments"></a>Mehrere Zuweisungen

Aufgrund der Richtliniendefinitionen für die Gastkonfiguration kann die gleiche Gastzuweisung derzeit lediglich einmal pro Computer zugewiesen werden, auch wenn bei der Richtlinienzuweisung andere Parameter verwendet werden.

## <a name="client-log-files"></a>Protokolldateien des Clients

Die Guest Configuration-Erweiterung schreibt Protokolldateien an die folgenden Speicherorte:

Windows: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Dabei bezieht sich `<version>` auf die aktuelle Versionsnummer.

### <a name="collecting-logs-remotely"></a>Remotesammeln von Protokollen

Im ersten Schritt bei der Problembehandlung von Konfigurationen oder Modulen durch die Gastkonfiguration sollte das Cmdlet `Test-GuestConfigurationPackage` anhand der Schritte unter [Erstellen eines benutzerdefinierten Richtlinienpakets für Gastkonfigurationen](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows) ausgeführt werden.
Wenn dies nicht erfolgreich ist, kann das Sammeln von Clientprotokollen helfen, Probleme zu diagnostizieren.

#### <a name="windows"></a>Windows

Erfassen Sie Informationen aus Protokolldateien mithilfe des Azure-VM-Befehls [Ausführen](../../../virtual-machines/windows/run-command.md), wobei das folgende PowerShell-Beispiel hilfreich sein kann.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Erfassen Sie Informationen aus Protokolldateien mithilfe des Azure VM-Befehls [Ausführen](../../../virtual-machines/linux/run-command.md), wobei das folgende Bash-Beispiel hilfreich sein kann.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Beispiele für Guest Configuration

Beispiele für integrierte Gastkonfigurationsrichtlinien finden Sie unter:

- [Integrierte Richtliniendefinitionen: Gastkonfiguration](../samples/built-in-policies.md#guest-configuration)
- [Integrierte Initiativen: Gastkonfiguration](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy-Beispiele: GitHub-Repository](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie die Details der einzelnen Einstellungen in der [Kompatibilitätsansicht der Gastkonfiguration](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration) anzeigen.
- Sehen Sie sich die Beispiele unter [Azure Policy-Beispiele](../samples/index.md) an.
- Lesen Sie die Informationen unter [Struktur von Azure Policy-Definitionen](./definition-structure.md).
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](./effects.md).
- Informieren Sie sich über das [programmgesteuerte Erstellen von Richtlinien](../how-to/programmatically-create.md).
- Informieren Sie sich über das [Abrufen von Konformitätsdaten](../how-to/get-compliance-data.md).
- Erfahren Sie, wie Sie [nicht konforme Ressourcen korrigieren](../how-to/remediate-resources.md) können.
- Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../../management-groups/overview.md).
