---
title: Erstellen, Ändern oder Löschen einer Azure-Netzwerksicherheitsgruppe
titlesuffix: Azure Virtual Network
description: Erfahren Sie, wie Sie eine Netzwerksicherheitsgruppe erstellen, ändern oder löschen.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2018
ms.author: kumud
ms.openlocfilehash: f1353165954021cd949d6e46357d10514ee26b3c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65560940"
---
# <a name="create-change-or-delete-a-network-security-group"></a>Erstellen, Ändern oder Löschen einer Netzwerksicherheitsgruppe

Mit Sicherheitsregeln in Netzwerksicherheitsgruppen können Sie den Typ des ein- und ausgehenden Netzwerkdatenverkehrs von Subnetzen virtueller Netzwerke und Netzwerkschnittstellen filtern. Wenn Sie nicht mit Netzwerksicherheitsgruppen vertraut sind, können Sie unter [Netzwerksicherheit](security-overview.md) mehr darüber erfahren und mit dem Tutorial [Filtern von Netzwerkdatenverkehr mithilfe einer Netzwerksicherheitsgruppe unter Verwendung von PowerShell](tutorial-filter-network-traffic.md) Erfahrungen im Umgang mit Netzwerksicherheitsgruppen sammeln.

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Führen Sie zuerst die folgenden Aufgaben aus, ehe Sie die Schritte in den Abschnitten dieses Artikels durchführen:

- Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für ein [kostenloses Testkonto](https://azure.microsoft.com/free) registrieren.
- Öffnen Sie bei Verwendung des Portals https://portal.azure.com, und melden Sie sich mit Ihrem Azure-Konto an.
- Wenn Sie PowerShell-Befehle zum Durchführen von Aufgaben in diesem Artikel verwenden, führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/powershell) oder durch Ausführen von PowerShell auf Ihrem Computer aus. Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. Für dieses Tutorial ist das Azure PowerShell-Modul Version 1.0.0 oder höher erforderlich. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.
- Wenn Sie Befehle der Azure-Befehlszeilenschnittstelle (CLI) zum Durchführen von Aufgaben in diesem Artikel verwenden, führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/bash) oder durch Ausführen der CLI auf Ihrem Computer aus. Für dieses Tutorial ist mindestens Version 2.0.28 der Azure CLI erforderlich. Führen Sie `az --version` aus, um die installierte Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli). Wenn Sie die Azure CLI lokal ausführen, müssen Sie auch `az login` ausführen, um eine Verbindung mit Azure herzustellen.

Das Konto, bei dem Sie sich anmelden oder das Sie zum Herstellen einer Verbindung mit Azure verwenden, muss der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Rolle zugewiesen sein, der die entsprechenden, in [Berechtigungen](#permissions) aufgeführten Aktionen zugewiesen sind.

## <a name="work-with-network-security-groups"></a>Arbeiten mit Netzwerksicherheitsgruppen

Sie können eine Netzwerksicherheitsgruppe erstellen, [komplett anzeigen](#view-all-network-security-groups), [Details von ihr anzeigen](#view-details-of-a-network-security-group), sie [ändern](#change-a-network-security-group) und [löschen](#delete-a-network-security-group). Sie können eine Netzwerksicherheitsgruppe auch einer Netzwerkschnittstelle oder einem Subnetz [zuordnen bzw. davon trennen](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

### <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Die Anzahl der Netzwerksicherheitsgruppen, die Sie pro Azure-Standort und -Abonnement erstellen können, ist begrenzt. Ausführliche Informationen finden Sie im Artikel zu den [Einschränkungen für Azure-Abonnements](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Wählen Sie links oben im Portal **+ Ressource erstellen** aus.
2. Wählen Sie die Option **Netzwerk** und dann **Netzwerksicherheitsgruppe** aus.
3. Geben Sie einen **Namen** für die Netzwerksicherheitsgruppe ein, wählen Sie Ihr **Abonnement** aus, erstellen Sie eine neue **Ressourcengruppe**, oder wählen Sie eine vorhandene Ressourcengruppe aus, wählen Sie einen **Speicherort**, und wählen Sie dann die Option **Erstellen**.

**Befehle**

- Azure CLI: [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create)
- PowerShell: [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup)

### <a name="view-all-network-security-groups"></a>Anzeigen aller Netzwerksicherheitsgruppen

Geben Sie im Suchfeld oben im Portal *Netzwerksicherheitsgruppen* ein. Wenn **Netzwerksicherheitsgruppen** in den Suchergebnissen angezeigt werden, wählen Sie diese aus. Die in Ihrem Abonnement enthaltenen Netzwerksicherheitsgruppen werden aufgeführt.

**Befehle**

- Azure CLI: [az network nsg list](/cli/azure/network/nsg#az-network-nsg-list)
- PowerShell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="view-details-of-a-network-security-group"></a>Anzeigen von Details einer Netzwerksicherheitsgruppe

1. Geben Sie im Suchfeld oben im Portal *Netzwerksicherheitsgruppen* ein. Wenn **Netzwerksicherheitsgruppen** in den Suchergebnissen angezeigt werden, wählen Sie diese aus.
2. Wählen Sie in der Liste die Netzwerksicherheitsgruppe aus, für die Sie Details anzeigen möchten. Unter **EINSTELLUNGEN** sehen Sie die **Eingangssicherheitsregeln** und **Ausgangssicherheitsregeln**, die **Netzwerkschnittstellen** und **Subnetze**, denen die Netzwerksicherheitsgruppe zugeordnet ist. Sie können auch **Diagnoseprotokolle** aktivieren oder deaktivieren und **Effektive Sicherheitsregeln** anzeigen. Weitere Informationen finden Sie unter [Protokollanalysen für Netzwerksicherheitsgruppen (NSGs)](virtual-network-nsg-manage-log.md) und [Problembehandlung bei Netzwerksicherheitsgruppen über das Azure-Portal](diagnose-network-traffic-filter-problem.md).
3. Weitere Informationen zu allgemeinen aufgelisteten Azure-Einstellungen finden Sie in den folgenden Artikeln:
    *   [Aktivitätsprotokoll](../azure-monitor/platform/activity-logs-overview.md)
    *   [Zugriffssteuerung (IAM)](../role-based-access-control/overview.md)
    *   [Tags](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Sperren](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    *   [Automatisierungsskript](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Befehle**

- Azure CLI: [az network nsg show](/cli/azure/network/nsg#az-network-nsg-show)
- PowerShell: [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)

### <a name="change-a-network-security-group"></a>Ändern einer Netzwerksicherheitsgruppe

1. Geben Sie im Suchfeld oben im Portal *Netzwerksicherheitsgruppen* ein. Wenn **Netzwerksicherheitsgruppen** in den Suchergebnissen angezeigt werden, wählen Sie diese aus.
2. Wählen Sie die Netzwerksicherheitsgruppe aus, die Sie ändern möchten. Die gängigsten Änderungen sind das [Hinzufügen](#create-a-security-rule) oder [Entfernen](#delete-a-security-rule) von Sicherheitsregeln und [Zuordnen einer Netzwerksicherheitsgruppe zu einem Subnetz oder einer Netzwerkschnittstelle bzw. das Trennen davon](#associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface).

**Befehle**

- Azure CLI: [az network nsg update](/cli/azure/network/nsg#az-network-nsg-update)
- PowerShell: [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)

### <a name="associate-or-dissociate-a-network-security-group-to-or-from-a-subnet-or-network-interface"></a>Zuordnen einer Netzwerksicherheitsgruppe zu einer Netzwerkschnittstelle oder einem Subnetz bzw. deren Trennung davon

Wie Sie eine Netzwerksicherheitsgruppe einer Netzwerkschnittstelle zuordnen bzw. davon trennen, erfahren Sie unter [Zuordnen einer Netzwerksicherheitsgruppe zu einer Netzwerkschnittstelle bzw. Trennen davon](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group). Wie Sie eine Netzwerksicherheitsgruppe einem Subnetz zuordnen bzw. davon trennen, erfahren Sie unter [Ändern von Subnetzeinstellungen](virtual-network-manage-subnet.md#change-subnet-settings).

### <a name="delete-a-network-security-group"></a>Löschen einer Netzwerksicherheitsgruppe

Wenn eine Netzwerksicherheitsgruppe Subnetzen bzw. Netzwerkschnittstellen zugeordnet ist, kann sie nicht gelöscht werden. Trennen Sie eine Netzwerksicherheitsgruppe von allen Subnetzen und Netzwerkschnittstellen, bevor Sie versuchen, sie zu löschen.

1. Geben Sie im Suchfeld oben im Portal *Netzwerksicherheitsgruppen* ein. Wenn **Netzwerksicherheitsgruppen** in den Suchergebnissen angezeigt werden, wählen Sie diese aus.
2. Wählen Sie die Netzwerksicherheitsgruppe, die Sie löschen möchten, in der Liste aus.
3. Wählen Sie **Löschen** und dann **Ja**.

**Befehle**

- Azure CLI: [az network nsg delete](/cli/azure/network/nsg#az-network-nsg-delete)
- PowerShell: [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup)

## <a name="work-with-security-rules"></a>Arbeiten mit Sicherheitsregeln

Eine Netzwerksicherheitsgruppe muss keine Sicherheitsregeln enthalten, kann aber Sicherheitsregeln enthalten. Sie können eine Sicherheitsregel erstellen, [komplett anzeigen](#view-all-security-rules), [Details von ihr anzeigen](#view-details-of-a-security-rule), sie [ändern](#change-a-security-rule) und [löschen](#delete-a-security-rule).

### <a name="create-a-security-rule"></a>Erstellen einer Sicherheitsregel

Die Anzahl der Sicherheitsregeln, die Sie pro Netzwerksicherheitsgruppe pro Azure-Standort und -Abonnement erstellen können, ist begrenzt. Ausführliche Informationen finden Sie im Artikel zu den [Einschränkungen für Azure-Abonnements](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Geben Sie im Suchfeld oben im Portal *Netzwerksicherheitsgruppen* ein. Wenn **Netzwerksicherheitsgruppen** in den Suchergebnissen angezeigt werden, wählen Sie diese aus.
2. Wählen Sie in der Liste die Netzwerksicherheitsgruppe aus, der Sie eine Sicherheitsregel hinzufügen möchten.
3. Wählen Sie **Eingangssicherheitsregeln** unter **EINSTELLUNGEN** aus. Es sind mehrere vorhandene Regeln aufgelistet. Einige der Regeln haben Sie möglicherweise nicht hinzugefügt. Wenn eine Netzwerksicherheitsgruppe erstellt wird, werden mehrere Standardsicherheitsregeln darin erstellt. Weitere Informationen finden Sie unter [Standardsicherheitsregeln](security-overview.md#default-security-rules).  Sie können Standardsicherheitsregeln nicht löschen, können jedoch mit Regeln mit höherer Priorität überschreiben.
4. <a name = "security-rule-settings"></a>Wählen Sie **+ Hinzufügen**.  Wählen Sie Werte für die folgenden Einstellungen aus, oder fügen Sie sie hinzu, und wählen Sie dann **OK** aus:
    
    |Einstellung  |Wert  |Details  |
    |---------|---------|---------|
    |`Source`     | Wählen Sie für Eingangssicherheitsregeln die Optionen **Any** (Beliebig), **Anwendungssicherheitsgruppe**, **IP-Adressen** oder **Service Tag** (Diensttag). Bei der Erstellung einer Ausgangssicherheitsregel gelten die gleichen Optionen wie für **Ziel**.       | Wenn Sie **Anwendungssicherheitsgruppe** wählen, sollten Sie mindestens eine vorhandene Anwendungssicherheitsgruppe auswählen, die sich in derselben Region wie die Netzwerkschnittstelle befindet. Informieren Sie sich über das [Erstellen einer Anwendungssicherheitsgruppe](#create-an-application-security-group). Wenn Sie sowohl für **Quelle** als auch für **Ziel** die Option **Anwendungssicherheitsgruppe** wählen, müssen sich die Netzwerkschnittstellen beider Anwendungssicherheitsgruppen in demselben virtuellen Netzwerk befinden. Bei Auswahl von **IP-Adressen** müssen Sie **Quell-IP-Adressen/CIDR-Bereiche** angeben. Sie können einen einzelnen Wert oder eine durch Trennzeichen getrennte Liste von mehreren Werten angeben. Ein Beispiel für mehrere Werte ist „10.0.0.0/16 192.188.1.1“. Es gibt Grenzen für die Anzahl der Werte, die Sie angeben können. Ausführliche Informationen finden Sie im Artikel zu den [Einschränkungen für Azure-Abonnements](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Wählen Sie bei Auswahl von **Service Tag** (Diensttag) ein Diensttag aus. Ein Diensttag ist ein vordefinierter Bezeichner für eine Kategorie von IP-Adressen. Weitere Informationen zu verfügbaren Diensttags und dazu, was die einzelnen Tag darstellen, finden Sie unter [Diensttags](security-overview.md#service-tags). Wenn die von Ihnen angegebene IP-Adresse einem virtuellen Azure-Computer zugewiesen ist, sollten Sie sicherstellen, dass Sie die private IP-Adresse und nicht die öffentliche IP-Adresse angeben, die dem virtuellen Computer zugewiesen ist. Sicherheitsregeln werden verarbeitet, nachdem Azure die öffentliche IP-Adresse in eine private IP-Adresse für Eingangssicherheitsregeln übersetzt und bevor Azure eine private IP-Adresse in eine öffentliche IP-Adresse für Ausgangsregeln übersetzt hat. Weitere Informationen zu öffentlichen und privaten IP-Adressen in Azure finden Sie unter [IP-Adresstypen und Zuordnungsmethoden in Azure](virtual-network-ip-addresses-overview-arm.md).        |
    |Quellportbereiche     | Geben Sie einen einzelnen Port wie z.B. 80 an, einen Bereich von Ports wie z.B. „1024-65535“ oder eine durch Trennzeichen getrennte Liste von einzelnen Ports und/oder Portbereichen wie z.B. „80, 1024-65535“. Geben Sie ein Sternchen ein, um Datenverkehr an einem beliebigen Port zuzulassen. | Die Ports und Bereiche legen fest, an welchen Ports die Regel Datenverkehr zulässt oder verweigert. Es gibt Grenzen für die Anzahl der Ports, die Sie angeben können. Ausführliche Informationen finden Sie im Artikel zu den [Einschränkungen für Azure-Abonnements](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).  |
    |Ziel     | Wählen Sie als Sicherheitsregeln für ausgehenden Datenverkehr die Optionen **Beliebig**, **Anwendungssicherheitsgruppe**, **IP-Adressen** oder **Virtuelles Netzwerk** aus. Bei der Erstellung einer Sicherheitsregel für eingehenden Datenverkehr gelten die gleichen Optionen, die für **Quelle** genannt wurden.        | Wenn Sie **Anwendungssicherheitsgruppe** wählen, müssen Sie mindestens eine vorhandene Anwendungssicherheitsgruppe auswählen, die sich in derselben Region wie die Netzwerkschnittstelle befindet. Informieren Sie sich über das [Erstellen einer Anwendungssicherheitsgruppe](#create-an-application-security-group). Bei Auswahl von **Anwendungssicherheitsgruppe** sollten Sie mindestens eine vorhandene Anwendungssicherheitsgruppe auswählen, die sich in derselben Region wie die Netzwerkschnittstelle befindet. Geben Sie bei Auswahl von **IP-Adressen** Daten unter **Ziel-IP-Adressen/CIDR-Bereiche** an. Ähnlich wie bei **Quelle** und **Quell-IP-Adressen/CIDR-Bereiche** können Sie eine einzelne oder mehrere Adressen bzw. Bereiche angeben, und die Anzahl, die Sie angeben können, ist begrenzt. Die Auswahl von **Virtuelles Netzwerk** – eines Diensttags – bedeutet, dass Datenverkehr an allen IP-Adressen innerhalb des Adressraums des virtuellen Netzwerks zulässig ist. Wenn die von Ihnen angegebene IP-Adresse einem virtuellen Azure-Computer zugewiesen ist, sollten Sie sicherstellen, dass Sie die private IP-Adresse und nicht die öffentliche IP-Adresse angeben, die dem virtuellen Computer zugewiesen ist. Sicherheitsregeln werden verarbeitet, nachdem Azure die öffentliche IP-Adresse in eine private IP-Adresse für Eingangssicherheitsregeln übersetzt und bevor Azure eine private IP-Adresse in eine öffentliche IP-Adresse für Ausgangsregeln übersetzt hat. Weitere Informationen zu öffentlichen und privaten IP-Adressen in Azure finden Sie unter [IP-Adresstypen und Zuordnungsmethoden in Azure](virtual-network-ip-addresses-overview-arm.md).        |
    |Zielportbereiche     | Geben Sie einen einzelnen Wert oder eine durch Trennzeichen getrennte Liste von Werten an. | Ähnlich wie bei **Quellportbereiche** können Sie einen einzelnen Port und Bereich oder mehrere angeben, und die Anzahl, die Sie angeben können, ist begrenzt. |
    |Protocol     | Wählen Sie **Beliebig**, **TCP** oder **UDP**.        |         |
    |Aktion     | Wählen Sie **Zulassen** oder **Verweigern**.        |         |
    |Priorität     | Geben Sie einen Wert von 100-4096 ein, der für alle Sicherheitsregeln innerhalb der Netzwerksicherheitsgruppe eindeutig ist. |Regeln werden in der Reihenfolge ihrer Priorität verarbeitet. Je niedriger die Zahl, desto höher die Priorität. Sie sollten beim Erstellen von Regeln eine Lücke zwischen Prioritätsnummern lassen, z.B. 100, 200, 300. Lücken erleichtern das zukünftige Hinzufügen von Regeln, die Sie vielleicht zum Herauf- oder Herabsetzen im Vergleich zu vorhandenen Regeln aufstellen müssen.         |
    |NAME     | Ein eindeutiger Name für die Regel in der Netzwerksicherheitsgruppe.        |  Der Name kann bis zu 80 Zeichen umfassen. Er muss mit einem Buchstaben oder einer Zahl beginnen, mit einem Buchstaben, einer Zahl oder einem Unterstrich enden und darf nur Buchstaben, Zahlen, Unterstriche, Punkte oder Bindestriche umfassen.       |
    |BESCHREIBUNG     | Eine optionale Beschreibung.        |         |

**Befehle**

- Azure CLI: [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create)
- PowerShell: [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig)

### <a name="view-all-security-rules"></a>Anzeigen aller Sicherheitsregeln

Eine Netzwerksicherheitsgruppe muss keine Regeln enthalten, kann aber Regeln enthalten. Weitere Informationen zu den Details, die beim Anzeigen von Regeln aufgeführt werden, finden Sie unter [Netzwerksicherheit](security-overview.md).

1. Geben Sie im Suchfeld oben im Portal *Netzwerksicherheitsgruppen* ein. Wenn **Netzwerksicherheitsgruppen** in den Suchergebnissen angezeigt werden, wählen Sie diese aus.
2. Wählen Sie in der Liste die Netzwerksicherheitsgruppe aus, für die Sie Regeln anzeigen möchten.
3. Wählen Sie **Eingangssicherheitsregeln** oder **Ausgangssicherheitsregeln** unter **EINSTELLUNGEN** aus.

Die Liste enthält alle Regeln, die Sie erstellt haben, und die [Standardsicherheitsregeln](security-overview.md#default-security-rules) der Netzwerksicherheitsgruppe.

**Befehle**

- Azure CLI: [az network nsg rule list](/cli/azure/network/nsg/rule#az-network-nsg-rule-list)
- PowerShell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="view-details-of-a-security-rule"></a>Anzeigen von Details einer Sicherheitsregel

1. Geben Sie im Suchfeld oben im Portal *Netzwerksicherheitsgruppen* ein. Wenn **Netzwerksicherheitsgruppen** in den Suchergebnissen angezeigt werden, wählen Sie diese aus.
2. Wählen Sie die Netzwerksicherheitsgruppe aus, für die Sie Details einer Sicherheitsregel anzeigen möchten.
3. Wählen Sie **Eingangssicherheitsregeln** oder **Ausgangssicherheitsregeln** unter **EINSTELLUNGEN** aus.
4. Wählen Sie die Regel aus, für die Sie Details anzeigen möchten. Eine ausführliche Erläuterung aller Einstellungen finden Sie unter [Sicherheitsregeleinstellungen](#security-rule-settings).

**Befehle**

- Azure CLI: [az network nsg rule show](/cli/azure/network/nsg/rule#az-network-nsg-rule-show)
- PowerShell: [Get-AzNetworkSecurityRuleConfig](/powershell/module/az.network/get-aznetworksecurityruleconfig)

### <a name="change-a-security-rule"></a>Ändern einer Sicherheitsregel

1. Führen Sie die Schritte in [Anzeigen von Details einer Sicherheitsregel](#view-details-of-a-security-rule) aus.
2. Ändern Sie die Einstellungen nach Bedarf, und wählen Sie dann **Speichern**. Eine ausführliche Erläuterung aller Einstellungen finden Sie unter [Sicherheitsregeleinstellungen](#security-rule-settings).

**Befehle**

- Azure CLI: [az network nsg rule update](/cli/azure/network/nsg/rule#az-network-nsg-rule-update)
- PowerShell: [Set-AzNetworkSecurityRuleConfig](/powershell/module/az.network/set-aznetworksecurityruleconfig)

### <a name="delete-a-security-rule"></a>Löschen einer Sicherheitsregel

1. Führen Sie die Schritte in [Anzeigen von Details einer Sicherheitsregel](#view-details-of-a-security-rule) aus.
2. Wählen Sie **Löschen** und dann **Ja**.

**Befehle**

- Azure CLI: [az network nsg rule delete](/cli/azure/network/nsg/rule#az-network-nsg-rule-delete)
- PowerShell: [Remove-AzNetworkSecurityRuleConfig](/powershell/module/az.network/remove-aznetworksecurityruleconfig)

## <a name="work-with-application-security-groups"></a>Arbeiten mit Anwendungssicherheitsgruppen

Eine Anwendungssicherheitsgruppe muss keine Netzwerkschnittstellen enthalten, kann aber Netzwerkschnittstellen enthalten. Weitere Informationen finden Sie unter [Anwendungssicherheitsgruppen](security-overview.md#application-security-groups). Alle Netzwerkschnittstellen innerhalb einer Anwendungssicherheitsgruppe müssen sich im gleichen virtuellen Netzwerk befinden. Wie Sie eine Netzwerkschnittstelle einer Anwendungssicherheitsgruppe hinzufügen, erfahren Sie unter [Erstellen, Ändern oder Löschen von Netzwerkschnittstellen](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups).

### <a name="create-an-application-security-group"></a>Erstellen einer Anwendungssicherheitsgruppe

1. Klicken Sie im Azure-Portal links oben auf **+ Ressource erstellen**.
2. Geben Sie in das Feld **Marketplace durchsuchen** den Suchbegriff *Anwendungssicherheitsgruppe* ein. Wenn in den Suchergebnissen **Anwendungssicherheitsgruppe** angezeigt wird, wählen Sie dieses Ergebnis aus. Wählen Sie unter **Alles** erneut **Anwendungssicherheitsgruppe** aus, und wählen Sie anschließend **Erstellen**.
3. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus, und wählen Sie dann **Erstellen** aus:

    | Einstellung        | Wert                                                   |
    | ---            | ---                                                     |
    | NAME           | Der Name muss innerhalb einer Ressourcengruppe eindeutig sein.        |
    | Abonnement   | Wählen Sie Ihr Abonnement aus.                               |
    | Ressourcengruppe | Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue. |
    | Location       | Standort auswählen                                       |

**Befehle**

- Azure CLI: [az network asg create](/cli/azure/network/asg#az-network-asg-create)
- PowerShell: [New-AzApplicationSecurityGroup](/powershell/module/az.network/new-azapplicationsecuritygroup)

### <a name="view-all-application-security-groups"></a>Anzeigen aller Anwendungssicherheitsgruppen

1. Wählen Sie im Azure-Portal oben links die Option **Alle Dienste**.
2. Geben Sie im Feld **Alle Dienste > Filter** den Begriff *Anwendungssicherheitsgruppen* ein, und wählen Sie anschließend in den Suchergebnissen den Eintrag **Anwendungssicherheitsgruppen** aus.

**Befehle**

- Azure CLI: [az network asg list](/cli/azure/network/asg#az-network-asg-list)
- PowerShell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="view-details-of-a-specific-application-security-group"></a>Anzeigen von Details einer bestimmten Anwendungssicherheitsgruppe

1. Wählen Sie im Azure-Portal oben links die Option **Alle Dienste**.
2. Geben Sie im Feld **Alle Dienste > Filter** den Begriff *Anwendungssicherheitsgruppen* ein, und wählen Sie anschließend in den Suchergebnissen den Eintrag **Anwendungssicherheitsgruppen** aus.
3. Wählen Sie die Anwendungssicherheitsgruppe aus, für die Sie die Details anzeigen möchten.

**Befehle**

- Azure CLI: [az network asg show](/cli/azure/network/asg#az-network-asg-show)
- PowerShell: [Get-AzApplicationSecurityGroup](/powershell/module/az.network/get-azapplicationsecuritygroup)

### <a name="change-an-application-security-group"></a>Ändern einer Anwendungssicherheitsgruppe

1. Wählen Sie im Azure-Portal oben links die Option **Alle Dienste**.
2. Geben Sie im Feld **Alle Dienste > Filter** den Begriff *Anwendungssicherheitsgruppen* ein, und wählen Sie anschließend in den Suchergebnissen den Eintrag **Anwendungssicherheitsgruppen** aus.
3. Wählen Sie die Anwendungssicherheitsgruppe aus, für die Sie die Einstellungen ändern möchten. Sie können Tags hinzufügen oder entfernen oder der Anwendungssicherheitsgruppe Berechtigungen zuweisen oder diese entfernen.

- Azure CLI: [az network asg update](/cli/azure/network/asg#az-network-asg-update)
- PowerShell: Kein PowerShell-Cmdlet

### <a name="delete-an-application-security-group"></a>Löschen einer Anwendungssicherheitsgruppe

Sie können eine Anwendungssicherheitsgruppe nicht löschen, wenn Netzwerkschnittstellen darin enthalten sind. Entfernen Sie alle Netzwerkschnittstellen aus der Anwendungssicherheitsgruppe, indem Sie entweder die Einstellungen der Netzwerkschnittstellen ändern oder die Netzwerkschnittstellen löschen. Weitere Informationen finden Sie unter [Hinzufügen einer Netzwerkschnittstelle zu einer Anwendungssicherheitsgruppe bzw. deren Entfernen daraus](virtual-network-network-interface.md#add-to-or-remove-from-application-security-groups) oder [Löschen einer Netzwerkschnittstelle](virtual-network-network-interface.md#delete-a-network-interface).

1. Wählen Sie im Azure-Portal oben links die Option **Alle Dienste**.
2. Geben Sie im Feld **Alle Dienste > Filter** den Begriff *Anwendungssicherheitsgruppen* ein, und wählen Sie anschließend in den Suchergebnissen den Eintrag **Anwendungssicherheitsgruppen** aus.
3. Wählen Sie die Anwendungssicherheitsgruppe aus, die Sie löschen möchten.
4. Wählen Sie **Löschen** und dann **Ja**, um die Anwendungssicherheitsgruppe zu löschen.

**Befehle**

- Azure CLI: [az network asg delete](/cli/azure/network/asg#az-network-asg-delete)
- PowerShell: [Remove-AzApplicationSecurityGroup](/powershell/module/az.network/remove-azapplicationsecuritygroup)

## <a name="permissions"></a>Berechtigungen

Zum Durchführen von Aufgaben für Netzwerksicherheitsgruppen, Sicherheitsregeln und Anwendungssicherheitsgruppen muss Ihr Konto der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Rolle zugewiesen sein, der die entsprechenden, in den folgenden Tabellen aufgeführten Berechtigungen zugewiesen wurden:

### <a name="network-security-group"></a>Netzwerksicherheitsgruppe

| Aktion                                                        |   NAME                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/read                  |   Netzwerksicherheitsgruppe abrufen                                          |
| Microsoft.Network/networkSecurityGroups/write                 |   Netzwerksicherheitsgruppe erstellen oder aktualisieren                             |
| Microsoft.Network/networkSecurityGroups/delete                |   Netzwerksicherheitsgruppe löschen                                       |
| Microsoft.Network/networkSecurityGroups/join/action           |   Netzwerksicherheitsgruppe einem Subnetz oder einer Netzwerkschnittstelle zuordnen 


### <a name="network-security-group-rule"></a>Netzwerksicherheitsgruppen-Regel

| Aktion                                                        |   NAME                                                                |
|-------------------------------------------------------------- |   -------------------------------------------                         |
| Microsoft.Network/networkSecurityGroups/rules/read            |   Regel abrufen                                                            |
| Microsoft.Network/networkSecurityGroups/rules/write           |   Regel erstellen oder aktualisieren                                               |
| Microsoft.Network/networkSecurityGroups/rules/delete          |   Regel löschen                                                         |

### <a name="application-security-group"></a>Anwendungssicherheitsgruppe

| Aktion                                                                     | NAME                                                     |
| --------------------------------------------------------------             | -------------------------------------------              |
| Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action     | IP-Konfiguration mit einer Anwendungssicherheitsgruppe verknüpfen|
| Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Sicherheitsregel mit einer Anwendungssicherheitsgruppe verknüpfen    |
| Microsoft.Network/applicationSecurityGroups/read                           | Anwendungssicherheitsgruppe abrufen                        |
| Microsoft.Network/applicationSecurityGroups/write                          | Anwendungssicherheitsgruppe erstellen oder aktualisieren           |
| Microsoft.Network/applicationSecurityGroups/delete                         | Löschen einer Anwendungssicherheitsgruppe                     |

## <a name="next-steps"></a>Nächste Schritte

- Erstellen einer Netzwerk- oder Anwendungssicherheitsgruppe mithilfe von [PowerShell](powershell-samples.md)- oder [Azure CLI](cli-samples.md)-Beispielskripts oder mithilfe von Azure [Resource Manager-Vorlagen](template-samples.md)
- Erstellen und Anwenden einer [Azure-Richtlinie](policy-samples.md) für virtuelle Netzwerke
