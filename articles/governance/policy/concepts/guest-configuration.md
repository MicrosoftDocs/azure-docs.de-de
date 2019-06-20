---
title: Grundlegendes zur Vorgehensweise beim Überwachen des Inhalts eines virtuellen Computers
description: Hier erfahren Sie, wie Azure Policy mithilfe von Guest Configuration Einstellungen in einem virtuellen Azure-Computer überprüft.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: c98229a28f31ff715f252dc3915ca690e99245ff
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979508"
---
# <a name="understand-azure-policys-guest-configuration"></a>Informationen zu Guest Configuration von Azure Policy

Mit Azure Policy können Sie nicht nur Azure-Ressourcen überprüfen und [korrigieren](../how-to/remediate-resources.md), sondern auch Einstellungen auf einem virtuellen Computer. Für die Überprüfung verwenden Sie die Erweiterung und den Client Guest Configuration. Mit der Erweiterung werden über den Client Einstellungen wie die Konfiguration des Betriebssystems, die Konfiguration oder das Vorhandensein einer Anwendung, Umgebungseinstellungen und vieles mehr überprüft.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="extension-and-client"></a>Erweiterung und Client

Zur Überprüfung von Einstellungen in einem virtuellen Computer wird eine [VM-Erweiterung](../../../virtual-machines/extensions/overview.md) aktiviert. Mit der Erweiterung werden anwendbare Richtlinienzuweisungen sowie die entsprechende Konfigurationsdefinition heruntergeladen.

### <a name="register-guest-configuration-resource-provider"></a>Registrieren des Guest Configuration-Ressourcenanbieters

Bevor Sie Guest Configuration verwenden können, müssen Sie den Ressourcenanbieter registrieren. Hierfür können Sie über das Portal oder mit PowerShell registrieren. Der Ressourcenanbieter wird automatisch registriert, wenn die Zuweisung einer Guest Configuration-Richtlinie über das Portal erfolgt.

#### <a name="registration---portal"></a>Registrierung – Portal

Gehen Sie wie folgt vor, um den Ressourcenanbieter für Guest Configuration über das Azure-Portal zu registrieren:

1. Rufen Sie das Azure-Portal auf, und klicken Sie auf **Alle Dienste**. Suchen Sie nach **Abonnements**, und wählen Sie diese Option aus.

1. Suchen Sie nach dem Abonnement, das Sie für Guest Configuration aktivieren möchten, und klicken Sie darauf.

1. Klicken Sie im linken Menü der Seite **Abonnement** auf **Ressourcenanbieter**.

1. Suchen Sie durch Filtern oder Blättern nach **Microsoft.GuestConfiguration**, und klicken Sie dann in derselben Zeile auf **Registrieren**.

#### <a name="registration---powershell"></a>Registrierung – PowerShell

Führen Sie zum Registrieren des Ressourcenanbieters für Guest Configuration über PowerShell den folgenden Befehl aus:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

### <a name="validation-tools"></a>Überprüfungstools

Im virtuellen Computer verwendet der Guest Configuration-Client lokale Tools zum Ausführen der Überprüfung.

In der folgenden Tabelle sind die lokalen Tools aufgeführt, die unter den jeweiligen unterstützten Betriebssystemen verwendet werden:

|Betriebssystem|Überprüfungstool|Notizen|
|-|-|-|
|Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ruby und Python werden durch die Erweiterung Guest Configuration installiert. |

### <a name="validation-frequency"></a>Validierungshäufigkeit

Der Guest Configuration-Client prüft alle 5 Minuten, ob neuer Inhalt vorliegt. Sobald eine Gastzuweisung empfangen wird, werden die Einstellungen in einem 15-Minuten-Intervall überprüft. Die Ergebnisse werden an den Guest Configuration-Ressourcenanbieter gesendet, sobald die Überwachung abgeschlossen ist. Wenn eine [Auswertungsauslöser](../how-to/get-compliance-data.md#evaluation-triggers)-Richtlinie auftritt, wird der Zustand des Computers an den Guest Configuration-Ressourcenanbieter geschrieben. Dies bewirkt, dass Azure Policy die Azure Resource Manager-Eigenschaften auswertet. Eine bedarfsgesteuerte Auswertung durch Azure Policy ruft den aktuellen Wert beim Guest Configuration-Ressourcenanbieter ab. Es wird jedoch keine neue Überwachung der Konfiguration innerhalb des virtuellen Computers ausgelöst.

### <a name="supported-client-types"></a>Unterstützte Clienttypen

In der folgenden Tabelle sind die in Azure-Images unterstützten Betriebssysteme aufgeführt:

|Herausgeber|NAME|Versionen|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Windows-Client|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|SUSE|SLES|12 SP3|

> [!IMPORTANT]
> Per Guest Configuration können Knoten überwacht werden, auf denen ein unterstütztes Betriebssystem ausgeführt wird. Wenn Sie virtuelle Computer überwachen möchten, für die ein benutzerdefiniertes Image verwendet wird, müssen Sie die **DeployIfNotExists**-Definition duplizieren und den **If**-Abschnitt so ändern, dass er Ihre Imageeigenschaften enthält.

### <a name="unsupported-client-types"></a>Nicht unterstützte Clienttypen

Windows Server Nano Server wird in keiner Version unterstützt.

### <a name="guest-configuration-extension-network-requirements"></a>Netzwerkanforderungen für die Gastkonfigurationserweiterung

Für die Kommunikation mit dem Guest Configuration-Ressourcenanbieter in Azure benötigen virtuelle Computer Zugriff auf Azure-Rechenzentren in ausgehender Richtung über Port **443**. Wenn Sie ein privates virtuelles Netzwerk in Azure verwenden und keinen ausgehenden Datenverkehr zulassen, müssen Ausnahmen über [Netzwerksicherheitsgruppen](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)-Regeln konfiguriert werden. Derzeit ist für Azure Policy Guest Configuration kein Diensttag vorhanden.

Für Listen mit IP-Adressen können Sie [IP-Bereiche des Microsoft Azure-Rechenzentrums](https://www.microsoft.com/download/details.aspx?id=41653) herunterladen. Diese Datei mit den jeweils aktuellen bereitgestellten Bereichen und allen anstehenden Änderungen an den IP-Adressbereichen wird wöchentlich veröffentlicht. Sie müssen nur Zugriff in ausgehender Richtung auf die IP-Adressen in den Regionen zulassen, in denen Ihre VMs bereitgestellt werden.

> [!NOTE]
> Die XML-Datei mit den IP-Adressen der Azure-Rechenzentren enthält die IP-Adressbereiche, die in den Microsoft Azure-Rechenzentren verwendet werden. Die Datei enthält die Bereiche für Compute, SQL und Storage. Eine aktualisierte Datei wird wöchentlich veröffentlicht. Die Datei enthält die derzeit bereitgestellten Bereichen und alle anstehenden Änderungen an den IP-Adressbereichen. In der Datei enthaltene neue Bereiche werden frühestens nach einer Woche in den Rechenzentren verwendet. Sie sollten die neue XML-Datei jede Woche herunterladen. Führen Sie damit dann eine Aktualisierung an Ihrem Standort durch, um in Azure ausgeführte Dienste ordnungsgemäß zu identifizieren. Benutzer von Azure ExpressRoute sollten beachten, dass diese Datei zum Aktualisieren der BGP-Ankündigung (Border Gateway Protocol) von Azure-Bereichen jeweils in der ersten Woche des Monats verwendet wird.

## <a name="guest-configuration-definition-requirements"></a>Anforderungen an die Guest Configuration-Definition

Für jede mit Guest Configuration ausgeführte Überprüfung werden zwei Richtliniendefinitionen benötigt: **DeployIfNotExists** und **Audit**. Die Definition **DeployIfNotExists** dient zum Vorbereiten des virtuellen Computers mit dem Guest Configuration-Agent und anderen Komponenten zur Unterstützung der [Überprüfungstools](#validation-tools).

Mit der Richtliniendefinition **DeployIfNotExists** werden die folgenden Elemente überprüft und korrigiert:

- Stellen Sie sicher, dass dem virtuellen Computer eine auszuwertende Konfiguration zugewiesen wurde. Wenn derzeit keine Zuweisung vorhanden ist, gehen Sie wie folgt vor, um die Zuweisung abzurufen und den virtuellen Computer vorzubereiten:
  - Authentifizieren Sie sich beim virtuellen Computer mithilfe einer [verwalteten Identität](../../../active-directory/managed-identities-azure-resources/overview.md).
  - Installieren Sie die neueste Version der Erweiterung **Microsoft.GuestConfiguration**.
  - Installieren Sie [Überprüfungstools](#validation-tools) und ggf. Abhängigkeiten.

Wenn die **DeployIfNotExists**-Zuweisung nicht konform ist, kann ein [Wartungstask](../how-to/remediate-resources.md#create-a-remediation-task) verwendet werden.

Sobald die **DeployIfNotExists**-Zuweisung konform ist, wird die Richtliniendefinition **Audit** verwendet, um mithilfe der lokalen Überprüfungstools zu ermitteln, ob die Konfigurationszuweisung konform ist.
Das Überprüfungstool stellt die Ergebnisse dem Guest Configuration-Client zur Verfügung. Der Client leitet die Ergebnisse an die Guest-Erweiterung weiter, die sie über den Guest Configuration-Ressourcenanbieter bereitstellt.

Azure Policy verwendet die Eigenschaft **complianceStatus** des Guest Configuration-Ressourcenanbieters, um die Konformität im Knoten **Konformität** zu melden. Weitere Informationen finden Sie unter [Abrufen von Konformitätsdaten](../how-to/getting-compliance-data.md).

> [!NOTE]
> Für jede Guest Configuration-Definition müssen die beiden Richtliniendefinitionen **DeployIfNotExists** und **Audit** vorhanden sein.

Alle integrierten Richtlinien für Guest Configuration sind in einer Initiative zum Gruppieren der Definitionen zur Verwendung in Zuweisungen enthalten. Der integrierte Initiative mit dem Namen *[Vorschau]: Kennwortsicherheitseinstellungen auf virtuellen Linux- und Windows-Computern überwachen* enthält 18 Richtlinien. Es gibt sechs **DeployIfNotExists**- und **Audit**-Paare für Windows und drei für Linux. Dabei stellt die Logik innerhalb der Definition nur sicher, dass das Zielbetriebssystem anhand der [Richtlinienregel](definition-structure.md#policy-rule)definition ausgewertet wird.

## <a name="client-log-files"></a>Protokolldateien des Clients

Die Guest Configuration-Erweiterung schreibt Protokolldateien an die folgenden Speicherorte:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Dabei bezieht sich `<version>` auf die aktuelle Versionsnummer.

## <a name="guest-configuration-samples"></a>Beispiele für Guest Configuration

Beispiele für Guest Configuration von Policy finden Sie unter:

- [Beispielindex: Guest Configuration](../samples/index.md#guest-configuration)
- [Azure Policy-Beispiele: GitHub-Repository](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration).

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die Beispiele unter [Azure Policy-Beispiele](../samples/index.md) an.
- Lesen Sie die Informationen unter [Struktur von Azure Policy-Definitionen](definition-structure.md).
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](effects.md).
- Informieren Sie sich über das [programmgesteuerte Erstellen von Richtlinien](../how-to/programmatically-create.md).
- Informieren Sie sich über das [Abrufen von Konformitätsdaten](../how-to/getting-compliance-data.md).
- Erfahren Sie, wie Sie [nicht konforme Ressourcen korrigieren](../how-to/remediate-resources.md) können.
- Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../../management-groups/index.md).