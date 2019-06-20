---
title: Erstellen, Ändern und Löschen von Azure-Netzwerkschnittstellen
titlesuffix: Azure Virtual Network
description: Erfahren Sie, was Netzwerkschnittstellen sind und wie Sie sie erstellen, ihre Einstellungen ändern und sie löschen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: kumud
ms.openlocfilehash: f25840c21ec64ca8d8e9e17eb39637cff7524c76
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66755244"
---
# <a name="create-change-or-delete-a-network-interface"></a>Erstellen, Ändern oder Löschen von Netzwerkschnittstellen

Erfahren Sie, wie Sie Netzwerkschnittstellenkarten erstellen, ihre Einstellungen ändern und sie löschen. Eine Netzwerkschnittstelle ermöglicht einem virtuellen Azure-Computer die Kommunikation mit Internet, Azure und lokalen Ressourcen. Wenn Sie im Azure-Portal eine VM erstellen, generiert das Portal für Sie eine Netzwerkschnittstelle mit Standardeinstellungen. Sie können stattdessen Netzwerkschnittstellen mit benutzerdefinierten Einstellungen erstellen und einem virtuellen Computer bei der Erstellung Netzwerkschnittstellen hinzufügen. Sie können auch die Standardeinstellungen einer vorhandenen Netzwerkschnittstelle ändern. In diesem Artikel wird erläutert, wie Sie Netzwerkschnittstellen mit benutzerdefinierten Einstellungen erstellen, vorhandene Netzwerkschnittstelleneinstellungen ändern, z.B. die Netzwerkfilterzuweisung (Netzwerksicherheitsgruppen), Subnetzzuweisung, DNS-Servereinstellungen und IP-Weiterleitung, und Netzwerkschnittstellen löschen.

Weitere Informationen zum Hinzufügen, Ändern oder Entfernen von IP-Adressen für eine Netzwerkschnittstelle finden Sie unter [Hinzufügen, Ändern oder Entfernen von IP-Adressen für Azure-Netzwerkschnittstellen](virtual-network-network-interface-addresses.md). Wenn Sie auf einem virtuellen Computer Netzwerkschnittstellen hinzufügen oder entfernen möchten, lesen Sie [Hinzufügen oder Entfernen von Netzwerkschnittstellen](virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Führen Sie zuerst die folgenden Aufgaben aus, ehe Sie die Schritte in den Abschnitten dieses Artikels durchführen:

- Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für ein [kostenloses Testkonto](https://azure.microsoft.com/free) registrieren.
- Öffnen Sie bei Verwendung des Portals https://portal.azure.com, und melden Sie sich mit Ihrem Azure-Konto an.
- Wenn Sie PowerShell-Befehle zum Durchführen von Aufgaben in diesem Artikel verwenden, führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/powershell) oder durch Ausführen von PowerShell auf Ihrem Computer aus. Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. Für dieses Tutorial ist das Azure PowerShell-Modul Version 1.0.0 oder höher erforderlich. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.
- Wenn Sie Befehle der Azure-Befehlszeilenschnittstelle (CLI) zum Durchführen von Aufgaben in diesem Artikel verwenden, führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/bash) oder durch Ausführen der CLI auf Ihrem Computer aus. Für dieses Tutorial ist mindestens Version 2.0.28 der Azure CLI erforderlich. Führen Sie `az --version` aus, um die installierte Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli). Wenn Sie die Azure CLI lokal ausführen, müssen Sie auch `az login` ausführen, um eine Verbindung mit Azure herzustellen.

Das Konto, bei dem Sie sich anmelden oder das Sie zum Herstellen einer Verbindung mit Azure verwenden, muss der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten Rolle](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zugewiesen sein, der die entsprechenden, unter [Berechtigungen](#permissions) aufgeführten Aktionen zugewiesen wurden.

## <a name="create-a-network-interface"></a>Erstellen einer Netzwerkschnittstelle

Wenn Sie im Azure-Portal eine VM erstellen, generiert das Portal für Sie eine Netzwerkschnittstelle mit Standardeinstellungen. Wenn Sie lieber alle Netzwerkschnittstelleneinstellungen angeben möchten, können Sie eine Netzwerkschnittstelle mit benutzerdefinierten Einstellungen erstellen und beim Erstellen eines virtuellen Computers an diesen anfügen (mithilfe von PowerShell oder der Azure-Befehlszeilenschnittstelle). Sie können auch eine Netzwerkschnittstelle erstellen und an einen vorhandenen virtuellen Computer anfügen (mithilfe von PowerShell oder der Azure-Befehlszeilenschnittstelle). Weitere Informationen zum Erstellen eines virtuellen Computers mit einer vorhandenen Netzwerkschnittstelle oder zum Hinzufügen und Entfernen von Netzwerkschnittstellen bei vorhandenen virtuellen Computern finden Sie unter [Hinzufügen oder Entfernen von Netzwerkschnittstellen](virtual-network-network-interface-vm.md). Vor dem Erstellen einer Netzwerkschnittstelle müssen Sie über ein [virtuelles Netzwerk](manage-virtual-network.md) am selben Standort und im selben Abonnement verfügen, in dem Sie eine Netzwerkschnittstelle erstellen.

1. Geben Sie im oberen Bereich des Azure-Portals im Feld mit dem Text *Ressourcen suchen* die Zeichenfolge *Netzwerkschnittstellen* ein. Wenn **Netzwerkschnittstellen** in den Suchergebnissen angezeigt wird, wählen Sie dies aus.
2. Wählen Sie **+ Hinzufügen** unter **Netzwerkschnittstellen** aus.
3. Geben Sie Werte für folgende Einstellungen ein, oder wählen Sie sie aus, und wählen Sie dann **Erstellen**:

    |Einstellung|Erforderlich?|Details|
    |---|---|---|
    |NAME|Ja|Der Name muss innerhalb der ausgewählten Ressourcengruppe eindeutig sein. Im Lauf der Zeit verfügen Sie wahrscheinlich über mehrere Netzwerkschnittstellen in Ihrem Azure-Abonnement. Vorschläge zum Festlegen einer Benennungskonvention zum Vereinfachen der Verwaltung mehrerer Netzwerkschnittstellen finden Sie unter [Benennungskonventionen](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#naming-rules-and-restrictions). Nach dem Erstellen kann der Name der Netzwerkschnittstelle nicht mehr geändert werden.|
    |Virtuelles Netzwerk|Ja|Wählen Sie das virtuelle Netzwerk für die Netzwerkschnittstelle aus. Sie können eine Netzwerkschnittstelle nur einem virtuellen Netzwerk zuweisen, das dasselbe Abonnement und denselben Standort wie die Netzwerkschnittstelle hat. Nachdem eine Netzwerkschnittstelle erstellt wurde, können Sie das virtuelle Netzwerk, dem sie zugewiesen ist, nicht mehr ändern. Der virtuelle Computer, dem Sie die Netzwerkschnittstelle hinzufügen, muss auch denselben Standort und dasselbe Abonnement wie die Netzwerkschnittstelle aufweisen.|
    |Subnetz|Ja|Wählen Sie ein Subnetz innerhalb des ausgewählten virtuellen Netzwerks aus. Das Subnetz, dem die Netzwerkschnittstelle zugewiesen ist, kann nach der Erstellung geändert werden.|
    |Zuweisung der privaten IP-Adresse|Ja| Mit dieser Einstellung wählen Sie die Zuweisungsmethode für die IPv4-Adresse aus. Die folgenden Zuweisungsmethoden stehen zur Wahl: **Dynamisch:** Bei Wahl dieser Option weist Azure automatisch die nächste verfügbare Adresse aus dem Adressraum des Subnetzes zu, das Sie ausgewählt haben. **Statisch:** Bei Wahl dieser Option müssen Sie eine verfügbare IP-Adresse innerhalb des Adressraums des ausgewählten Subnetzes manuell zuweisen. Statische und dynamische Adressen bleiben unverändert, bis Sie sie ändern oder die Netzwerkschnittstelle gelöscht wird. Die Zuweisungsmethode kann nach Erstellung der Netzwerkschnittstelle geändert werden. Der DHCP-Server von Azure weist diese Adresse der Netzwerkschnittstelle innerhalb des Betriebssystems des virtuellen Computers zu.|
    |Netzwerksicherheitsgruppe|Nein| Belassen Sie diese Einstellung auf **Keine** festgelegt, wählen Sie eine vorhandene [Netzwerksicherheitsgruppe](security-overview.md) aus, oder [erstellen Sie eine Netzwerksicherheitsgruppe](tutorial-filter-network-traffic.md). Mithilfe von Netzwerksicherheitsgruppen können Sie den ein- und ausgehenden Netzwerkdatenverkehr für eine Netzwerkschnittstelle steuern. Auf eine Netzwerkschnittstelle kann maximal eine Netzwerksicherheitsgruppe angewendet werden. Auch auf das Subnetz, dem die Netzwerkschnittstelle zugewiesen ist, kann maximal eine Netzwerksicherheitsgruppe angewendet werden. Wenn eine Netzwerksicherheitsgruppe auf eine Netzwerkschnittstelle und auf das Subnetz, dem die Netzwerkschnittstelle zugewiesen ist, angewendet wird, kommt es in einigen Fällen zu unerwarteten Ergebnissen. Informationen zum Beheben von Problemen mit Netzwerksicherheitsgruppen, die auf Netzwerkschnittstellen und Subnetze angewendet werden, finden Sie unter [Beheben von Problemen bei Netzwerksicherheitsgruppen](diagnose-network-traffic-filter-problem.md).|
    |Abonnement|Ja|Wählen Sie eines Ihrer Azure-[Abonnements](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) aus. Der virtuelle Computer, an den Sie eine Netzwerkschnittstelle anfügen, und das virtuelle Netzwerk, mit dem Sie eine Verbindung herstellen, müssen zu demselben Abonnement gehören.|
    |Private IP-Adresse (IPv6)|Nein| Wenn Sie dieses Kontrollkästchen aktivieren, wird der Netzwerkschnittstelle zusätzlich zu der IPv4-Adresse der Netzwerkschnittstelle eine IPv6-Adresse zugewiesen. Im IPv6-Abschnitt dieses Artikels finden Sie wichtige Informationen zur Verwendung von IPv6 bei Netzwerkschnittstellen. Eine Auswahl der Zuweisungsmethode für die IPv6-Adresse ist nicht möglich. Wenn Sie eine IPv6-Adresse zuweisen, erfolgt die Zuordnung mit der dynamischen Methode.
    |IPv6-Name (wird nur angezeigt, wenn das Kontrollkästchen **Private IP-Adresse (IPv6)** aktiviert ist) |Ja, wenn das Kontrollkästchen **Private IP-Adresse (IPv6)** aktiviert ist.| Dieser Name wird einer sekundären IP-Konfiguration für die Netzwerkschnittstelle zugewiesen. Weitere Informationen zu IP-Konfigurationen finden Sie unter [Anzeigen der Einstellungen von Netzwerkschnittstellen](#view-network-interface-settings).|
    |Ressourcengruppe|Ja|Wählen Sie eine vorhandene [Ressourcengruppe](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) aus, oder erstellen Sie eine. Eine Netzwerkschnittstelle kann sich in der gleichen (oder in einer anderen) Ressourcengruppe befinden wie der virtuelle Computer, an den Sie sie anfügen, oder wie das virtuelle Netzwerk, mit dem Sie eine Verbindung herstellen.|
    |Location|Ja|Der virtuelle Computer, an den Sie eine Netzwerkschnittstelle anfügen, und das virtuelle Netzwerk, mit dem Sie eine Verbindung herstellen, müssen sich an demselben [Standort](https://azure.microsoft.com/regions) befinden. Dieser wird auch als Region bezeichnet.|

Das Portal stellt keine Option bereit, mit der Sie der Netzwerkschnittstelle beim Erstellen eine öffentliche IP-Adresse zuweisen können. Sie können allerdings im Portal eine öffentliche IP-Adresse erstellen und diese einer Netzwerkschnittstelle zuweisen, wenn Sie im Portal einen virtuellen Computer erstellen. Informationen zum Hinzufügen einer öffentlichen IP-Adresse zur Netzwerkschnittstelle nach deren Erstellung finden Sie unter [Verwalten von IP-Adressen](virtual-network-network-interface-addresses.md). Wenn Sie eine Netzwerkschnittstelle mit einer öffentlichen IP-Adresse erstellen möchten, müssen Sie zum Erstellen der Netzwerkschnittstelle die Befehlszeilenschnittstelle oder PowerShell verwenden.

Im Portal steht beim Erstellen von Netzwerkschnittstellen keine Option zum Zuweisen der Netzwerkschnittstelle zu Anwendungssicherheitsgruppen zur Verfügung, wohl aber auf der Azure-Befehlszeilenschnittstelle und in PowerShell. Vorhandene Netzwerkschnittstellen können jedoch mithilfe des Portals zu Anwendungssicherheitsgruppen zugewiesen werden, sofern die Netzwerkschnittstelle einem virtuellen Computer zugeordnet ist. Informationen zum Zuweisen einer Netzwerkschnittstelle zu einer Anwendungssicherheitsgruppe finden Sie unter [Hinzufügen einer Netzwerkschnittstelle zu einer Anwendungssicherheitsgruppe bzw. deren Entfernen daraus](#add-to-or-remove-from-application-security-groups).

>[!Note]
> Azure weist der Netzwerkschnittstelle erst dann eine MAC-Adresse zu, wenn die Netzwerkschnittstelle an einen virtuellen Computer angefügt und der virtuelle Computer erstmalig gestartet wurde. Die MAC-Adresse, die Azure der Netzwerkschnittstelle zuweist, kann nicht angegeben werden. Die MAC-Adresse bleibt der Netzwerkschnittstelle zugewiesen, bis die Netzwerkschnittstelle gelöscht oder die private IP-Adresse, die der primären IP-Konfiguration der primären Netzwerkschnittstelle zugewiesen ist, geändert wird. Weitere Informationen zu IP-Adressen und IP-Konfigurationen finden Sie unter [Verwalten von IP-Adressen](virtual-network-network-interface-addresses.md).

**Befehle**

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az network nic create](/cli/azure/network/nic)|
|PowerShell|[New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)|

## <a name="view-network-interface-settings"></a>Anzeigen der Einstellungen von Netzwerkschnittstellen

Sie können die meisten Einstellungen für eine Netzwerkschnittstelle anzeigen und ändern, nachdem diese erstellt wurde. Das Portal zeigt weder das DNS-Suffix noch die Mitgliedschaft in einer Anwendungssicherheitsgruppe für die Netzwerkschnittstelle an. Sie können mithilfe der PowerShell- oder Azure CLI-[Befehle](#view-settings-commands) das DNS-Suffix und die Mitgliedschaft in einer Anwendungssicherheitsgruppe anzeigen.

1. Geben Sie im oberen Bereich des Azure-Portals im Feld mit dem Text *Ressourcen suchen* die Zeichenfolge *Netzwerkschnittstellen* ein. Wenn **Netzwerkschnittstellen** in den Suchergebnissen angezeigt wird, wählen Sie dies aus.
2. Wählen Sie in der Liste die Netzwerkschnittstelle aus, die Sie anzeigen oder deren Einstellungen Sie ändern möchten.
3. Die folgenden Elemente werden für die Netzwerkschnittstelle aufgelistet, die Sie ausgewählt haben:
   - **Übersicht:** enthält Informationen über die Netzwerkschnittstelle, z.B. die zugewiesenen IP-Adressen, das virtuelle Netzwerk/Subnetz, dem die Netzwerkschnittstelle zugewiesen ist, und den virtuellen Computer, an den die Netzwerkschnittstelle angefügt ist (sofern zutreffend). Die folgende Abbildung zeigt die Übersichtseinstellungen für eine Netzwerkschnittstelle mit dem Namen **mywebserver256**: ![Übersicht über die Netzwerkschnittstelle](./media/virtual-network-network-interface/nic-overview.png)

     Sie können eine Netzwerkschnittstelle in eine andere Ressourcengruppe oder ein anderes Abonnement verschieben, indem Sie (**Ändern**) neben dem Namen der **Ressourcengruppe** oder des **Abonnements** auswählen. Wenn Sie die Netzwerkschnittstelle verschieben, müssen Sie alle Ressourcen für die Netzwerkschnittstelle ebenfalls verschieben. Wenn die Netzwerkschnittstelle an einen virtuellen Computer angefügt ist, müssen Sie z.B. auch den virtuellen Computer und andere mit dem virtuellen Computer verknüpfte Ressourcen verschieben. Informationen zum Verschieben von Netzwerkschnittstellen finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-portal). Hier erfahren Sie, welche Voraussetzungen erfüllt sein müssen und wie Sie Ressourcen über das Azure-Portal, mithilfe von PowerShell oder unter Verwendung der Azure CLI verschieben.
   - **IP-Konfigurationen:** Hier werden die öffentlichen und privaten IPv4- und IPv6-Adressen aufgeführt, die IP-Konfigurationen zugewiesen sind. Wenn einer IP-Konfiguration eine IPv6-Adresse zugewiesen ist, wird die Adresse nicht angezeigt. Weitere Informationen zu IP-Konfigurationen und zum Hinzufügen und Entfernen von IP-Adressen finden Sie unter [Konfigurieren von IP-Adressen für eine Azure-Netzwerkschnittstelle](virtual-network-network-interface-addresses.md). Die IP-Weiterleitung und Subnetzzuweisung werden auch in diesem Abschnitt konfiguriert. Weitere Informationen zu diesen Einstellungen finden Sie unter [Aktivieren oder Deaktivieren der IP-Weiterleitung](#enable-or-disable-ip-forwarding) und [Ändern der Subnetzzuweisung](#change-subnet-assignment).
   - **DNS-Server:** Sie können angeben, welcher DNS-Server einer Netzwerkschnittstelle durch die Azure-DHCP-Server zugewiesen wird. Die Netzwerkschnittstelle kann die Einstellung vom virtuellen Netzwerk, dem die Netzwerkschnittstelle zugewiesen ist, erben. Sie können aber auch benutzerdefinierte Einstellungen festlegen, die die entsprechenden Einstellungen für das virtuelle Netzwerk, dem sie zugewiesen ist, überschreiben. Um zu ändern, was angezeigt wird, lesen Sie [Ändern von DNS-Servern](#change-dns-servers).
   - **Netzwerksicherheitsgruppe (NSG)** : zeigt an, welche Netzwerksicherheitsgruppe (NSG) der Netzwerkschnittstelle zugeordnet ist (falls vorhanden). Eine NSG enthält ein- und ausgehende Regeln zum Filtern von Netzwerkdatenverkehr für die Netzwerkschnittstelle. Falls der Netzwerkschnittstelle eine NSG zugeordnet ist, wird der Name der zugeordneten NSG angezeigt. Wie Sie ändern, was angezeigt wird, erfahren Sie unter [Zuordnen oder Trennen einer Netzwerksicherheitsgruppe](#associate-or-dissociate-a-network-security-group).
   - **Eigenschaften:** zeigt wichtige Einstellungen für die Netzwerkschnittstelle an, z.B. die MAC-Adresse (leer, wenn die Netzwerkschnittstelle an keine VM angefügt ist) und das Abonnement, dem sie angehört.
   - **Effektive Sicherheitsregeln:**  Sicherheitsregeln werden aufgelistet, wenn die Netzwerkschnittstelle an einen aktiven virtuellen Computer angefügt und der Netzwerkschnittstelle oder dem Subnetz, mit dem sie verbunden ist, eine NSG zugeordnet ist. Weitere Informationen darüber, was angezeigt wird, finden Sie unter [Anzeigen effektiver Sicherheitsregeln](#view-effective-security-rules). Weitere Informationen zu NSGs finden Sie unter [Filtern des Netzwerkdatenverkehrs mit Netzwerksicherheitsgruppen](security-overview.md).
   - **Effektive Routen:** Routen werden aufgelistet, wenn die Netzwerkschnittstelle an einen aktiven virtuellen Computer angefügt ist. Bei den Routen handelt es sich um eine Kombination aus den Azure-Standardrouten, benutzerdefinierten Routen und BGP-Routen, die ggf. für das Subnetz vorhanden sind, dem die Netzwerkschnittstelle zugewiesen ist. Weitere Informationen darüber, was angezeigt wird, finden Sie unter [Anzeigen effektiver Routen](#view-effective-routes). Weitere Informationen zu Azure-Standardrouten und benutzerdefinierten Routen finden Sie unter [Routing von Datenverkehr für virtuelle Netzwerke](virtual-networks-udr-overview.md).
   - **Allgemeine Azure Resource Manager-Einstellungen:**  Weitere Informationen zu allgemeinen Azure Resource Manager-Einstellungen finden Sie unter [Aktivitätsprotokolle](../azure-monitor/platform/activity-logs-overview.md), [Zugriffssteuerung (IAM, Identity and Access Management)](../role-based-access-control/overview.md), [Tags](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Sperren von Ressourcen, um unerwartete Änderungen zu verhindern](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json) und [Exportieren der Vorlage aus der Ressourcengruppe](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).

<a name="view-settings-commands"></a>**Befehle**

Wenn eine IPv6-Adresse einer Netzwerkschnittstelle zugewiesen ist, gibt die PowerShell-Ausgabe zurück, dass die Adresse zugewiesen ist, aber nicht die zugewiesene Adresse selbst. Auf ähnliche Weise gibt die Befehlszeilenschnittstelle zurück, dass die Adresse zugewiesen ist, als Ausgabe für die Adresse jedoch lediglich *NULL*.

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az network nic list](/cli/azure/network/nic): Anzeigen von im Abonnement enthaltenen Netzwerkschnittstellen; [az network nic show](/cli/azure/network/nic): Anzeigen der Einstellungen für eine Netzwerkschnittstelle|
|PowerShell|[Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface): Anzeigen von im Abonnement enthaltenen Netzwerkschnittstellen oder der Einstellungen für eine Netzwerkschnittstelle|

## <a name="change-dns-servers"></a>Ändern von DNS-Servern

Der DHCP-Server von Azure weist den DNS-Server der Netzwerkschnittstelle innerhalb des Betriebssystems des virtuellen Computers zu. Der zugewiesene DNS-Server entspricht der geltenden DNS-Servereinstellung für eine Netzwerkschnittstelle. Weitere Informationen zu Namensauflösungseinstellungen für eine Netzwerkschnittstelle finden Sie im Artikel [Namensauflösung für virtuelle Computer](virtual-networks-name-resolution-for-vms-and-role-instances.md). Die Netzwerkschnittstelle kann die Einstellungen des virtuellen Netzwerks übernehmen oder eigene eindeutige Einstellungen verwenden, die die Einstellung für das virtuelle Netzwerk überschreiben.

1. Geben Sie im oberen Bereich des Azure-Portals im Feld mit dem Text *Ressourcen suchen* die Zeichenfolge *Netzwerkschnittstellen* ein. Wenn **Netzwerkschnittstellen** in den Suchergebnissen angezeigt wird, wählen Sie dies aus.
2. Wählen Sie in der Liste die Netzwerkschnittstelle aus, für die Sie einen DNS-Server ändern möchten.
3. Wählen Sie unter **EINSTELLUNGEN** die Option **DNS-Server** aus.
4. Wählen Sie eins von beiden:
   - **Vom virtuellen Netzwerk erben**: Wählen Sie diese Option aus, wenn die DNS-Servereinstellung des virtuellen Netzwerks übernommen werden soll, dem die Netzwerkschnittstelle zugewiesen ist. Auf Ebene des virtuellen Netzwerks ist entweder ein benutzerdefinierter DNS-Server oder der von Azure bereitgestellte DNS-Server definiert. Der von Azure bereitgestellte DNS-Server kann Hostnamen für Ressourcen auflösen, die demselben virtuellen Netzwerk zugewiesen sind. Zum Auflösen von Ressourcen, die anderen virtuellen Netzwerken zugewiesen sind, müssen vollqualifizierte Domänennamen (FQDNs) verwendet werden.
   - **Benutzerdefiniert**: Sie können einen eigenen DNS-Server für eine die virtuellen Netzwerke übergreifende Namensauflösung konfigurieren. Geben Sie die IP-Adresse des Servers ein, den Sie als DNS-Server verwenden möchten. Die von Ihnen angegebene DNS-Serveradresse wird nur dieser Netzwerkschnittstelle zugewiesen. Sie überschreibt alle DNS-Einstellungen für das virtuelle Netzwerk, dem die Netzwerkschnittstelle zugewiesen ist.
     >[!Note]
     >Wenn der virtuelle Computer eine Netzwerkkarte verwendet, die Teil einer Verfügbarkeitsgruppe ist, werden alle DNS-Server geerbt, die für jeden virtuellen Computer von allen NICs, die Teil der Verfügbarkeitsgruppe sind, angegeben sind.
5. Wählen Sie **Speichern** aus.

**Befehle**

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>Aktivieren oder Deaktivieren der IP-Weiterleitung

Die IP-Weiterleitung ermöglicht virtuellen Computern mit einer zugewiesenen Netzwerkschnittstelle Folgendes:
- Empfangen von Netzwerkdatenverkehr, der nicht für eine der IP-Adressen bestimmt ist, die einer der IP-Konfigurationen für die Netzwerkschnittstelle zugewiesen ist
- Senden von Netzwerkdatenverkehr mit einer IP-Quelladresse, die nicht der Adresse entspricht, die den IP-Konfigurationen einer Netzwerkschnittstelle zugewiesen ist

Die Einstellung muss für jede Netzwerkschnittstelle aktiviert werden, die an den virtuellen Computer angefügt ist, der Datenverkehr empfängt und weiterleiten muss. Ein virtueller Computer kann Datenverkehr unabhängig davon weiterleiten, ob er über mehrere Netzwerkschnittstellen verfügt oder nur eine einzelne Netzwerkschnittstelle angefügt ist. Bei der IP-Weiterleitung handelt es sich zwar um eine Azure-Einstellung, auf dem virtuellen Computer muss aber auch eine Anwendung ausgeführt werden, die den Datenverkehr weiterleiten kann – beispielsweise eine Firewall, ein WAN-Optimierer oder ein Lastenausgleich. Wenn ein virtueller Computer Netzwerkanwendungen ausführt, wird er häufig als virtuelles Netzwerkgerät (Network Virtual Appliance) bezeichnet. Eine Liste mit für die Bereitstellung vorbereiteten virtuellen Netzwerkgeräten finden Sie im [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Die IP-Weiterleitung wird in der Regel mit benutzerdefinierten Routen verwendet. Weitere Informationen zu benutzerdefinierten Routen finden Sie in [Benutzerdefinierte Routen](virtual-networks-udr-overview.md).

1. Geben Sie im oberen Bereich des Azure-Portals im Feld mit dem Text *Ressourcen suchen* die Zeichenfolge *Netzwerkschnittstellen* ein. Wenn **Netzwerkschnittstellen** in den Suchergebnissen angezeigt wird, wählen Sie dies aus.
2. Wählen Sie die Netzwerkschnittstelle aus, für die Sie die IP-Weiterleitung aktivieren oder deaktivieren möchten.
3. Wählen Sie **IP-Konfigurationen** im Abschnitt **EINSTELLUNGEN** aus.
4. Wählen Sie **Aktiviert** oder **Deaktiviert** (Standardeinstellung) aus, um die Einstellung zu ändern.
5. Wählen Sie **Speichern** aus.

**Befehle**

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="change-subnet-assignment"></a>Ändern der Subnetzzuweisung

Sie können das Subnetz, aber nicht das virtuelle Netzwerk ändern, dem eine Netzwerkschnittstelle zugewiesen ist.

1. Geben Sie im oberen Bereich des Azure-Portals im Feld mit dem Text *Ressourcen suchen* die Zeichenfolge *Netzwerkschnittstellen* ein. Wenn **Netzwerkschnittstellen** in den Suchergebnissen angezeigt wird, wählen Sie dies aus.
2. Wählen Sie die Netzwerkschnittstelle aus, für die Sie die Subnetzzuweisung ändern möchten.
3. Wählen Sie **IP-Konfigurationen** unter **EINSTELLUNGEN** aus. Falls neben privaten IP-Adressen für aufgeführte IP-Konfigurationen **(Statisch)** angegeben ist, müssen Sie die IP-Adresszuweisungsmethode in „Dynamisch“ ändern, indem Sie die folgenden Schritte ausführen. Alle privaten IP-Adressen müssen mit der dynamischen Zuweisungsmethode zugewiesen werden, um die Subnetzzuweisung für die Netzwerkschnittstelle zu ändern. Falls die Adressen mithilfe der dynamischen Methode zugewiesen wurden, fahren Sie mit Schritt 5 fort. Falls IPv4-Adressen mit der statischen Zuweisungsmethode zugewiesen wurden, führen Sie die folgenden Schritte aus, um die Zuweisungsmethode in „Dynamisch“ zu ändern:
   - Wählen Sie in der Liste mit den IP-Konfigurationen die IP-Konfiguration aus, deren IPv4-Adresszuweisungsmethode Sie ändern möchten.
   - Wählen Sie **Dynamisch** für die Methode der **Zuweisung** der privaten IP-Adresse aus. Sie können IPv6-Adressen nicht mit der statischen Zuweisungsmethode zuweisen.
   - Wählen Sie **Speichern** aus.
4. Wählen Sie in der Dropdownliste **Subnetz** das Subnetz aus, zu dem Sie die Netzwerkschnittstelle verschieben möchten.
5. Wählen Sie **Speichern** aus. Neue dynamische Adressen werden aus dem Subnetzadressbereich des neuen Subnetzes zugewiesen. Nach dem Zuweisen der Netzwerkschnittstelle zu einem neuen Subnetz können Sie nach Wunsch eine statische IPv4-Adresse aus dem Adressbereich des neuen Subnetzes zuweisen. Weitere Informationen zum Hinzufügen, Ändern und Entfernen von IP-Adressen für eine Netzwerkschnittstelle finden Sie unter [Verwalten von IP-Adressen](virtual-network-network-interface-addresses.md).

**Befehle**

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az network nic ip-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Hinzufügen einer Netzwerkschnittstelle zu einer Anwendungssicherheitsgruppe bzw. deren Entfernen daraus

Sie können eine Netzwerkschnittstelle im Portal nur dann einer Anwendungssicherheitsgruppe zuweisen oder sie aus dieser entfernen, wenn die Netzwerkschnittstelle einem virtuellen Computer zugeordnet ist. Mithilfe von PowerShell oder der Azure-Befehlszeilenschnittstelle können Sie eine Netzwerkschnittstelle einer Anwendungssicherheitsgruppe hinzufügen oder sie aus dieser entfernen, unabhängig davon, ob die Netzwerkschnittstelle einem virtuellen Computer zugeordnet ist oder nicht. Weitere Informationen über [Anwendungssicherheitsgruppen](security-overview.md#application-security-groups) und das [Erstellen einer Anwendungssicherheitsgruppe](manage-network-security-group.md).

1. Beginnen Sie im Feld *Search resources, services, and docs* (Ressourcen, Dienste und Dokumente durchsuchen) oben im Portal mit der Eingabe des Namens eines virtuellen Computers, der über eine Netzwerkschnittstelle verfügt, die Sie einer Anwendungssicherheitsgruppe hinzufügen oder aus ihr entfernen möchten. Wenn der Name Ihrer VM in den Suchergebnissen angezeigt wird, wählen Sie ihn aus.
2. Wählen Sie unter **EINSTELLUNGEN** die Option **Netzwerk** aus.  Wählen Sie **Configure the application security groups** (Anwendungssicherheitsgruppen konfigurieren) aus, wählen Sie die Anwendungssicherheitsgruppen aus, denen Sie die Netzwerkschnittstelle hinzufügen möchten, bzw. heben Sie die Auswahl der Anwendungssicherheitsgruppen auf, aus denen Sie die Netzwerkschnittstelle entfernen möchten, und wählen Sie dann **Speichern** aus. Nur Netzwerkschnittstellen innerhalb des gleichen virtuellen Netzwerks können der gleichen Anwendungssicherheitsgruppe hinzugefügt werden. Die Anwendungssicherheitsgruppe muss am gleichen Ort wie die Netzwerkschnittstelle vorhanden sein.

**Befehle**

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az network nic update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Zuordnen oder Trennen einer Netzwerksicherheitsgruppe

1. Geben Sie im Suchfeld oben im Portal den Suchbegriff *Netzwerkschnittstellen* ein. Wenn **Netzwerkschnittstellen** in den Suchergebnissen angezeigt wird, wählen Sie dies aus.
2. Wählen Sie in der Liste die Netzwerkschnittstelle aus, der Sie eine Netzwerksicherheitsgruppe zuordnen möchten, oder die Sie von einer Netzwerksicherheitsgruppe trennen möchten.
3. Wählen Sie die Option **Netzwerksicherheitsgruppe** unter **EINSTELLUNGEN**.
4. Wählen Sie **Bearbeiten** aus.
5. Wählen Sie **Netzwerksicherheitsgruppe** und dann die Netzwerksicherheitsgruppe aus, die Sie der Netzwerkschnittstelle zuordnen möchten, oder wählen Sie **Keine** aus, um eine Netzwerksicherheitsgruppe zu trennen.
6. Wählen Sie **Speichern** aus.

**Befehle**

- Azure CLI: [az network nic update](/cli/azure/network/nic#az-network-nic-update)
- PowerShell: [Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)

## <a name="delete-a-network-interface"></a>Löschen einer Netzwerkschnittstelle

Sie können eine Netzwerkschnittstelle löschen, solange diese nicht an einen virtuellen Computer angefügt ist. Wenn eine Netzwerkschnittstelle einem virtuellen Computer angefügt ist, müssen Sie den virtuellen Computer zunächst in den Status „Beendet (Zuordnung aufgehoben)“ versetzen und dann die Netzwerkschnittstelle vom virtuellen Computer trennen. Die Schritte zum Trennen einer Netzwerkschnittstelle von einem virtuellen Computer finden Sie unter [Trennen einer Netzwerkschnittstelle von einem virtuellen Computer](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm). Sie können eine Netzwerkschnittstelle jedoch nicht von einer VM trennen, wenn sie als einzige Netzwerkschnittstelle der VM angefügt ist. Einer VM muss mindestens eine Netzwerkschnittstelle angefügt sein. Beim Löschen eines virtuellen Computers werden alle angefügten Netzwerkschnittstellen getrennt, die Netzwerkschnittstellen werden aber nicht gelöscht.

1. Geben Sie im oberen Bereich des Azure-Portals im Feld mit dem Text *Ressourcen suchen* die Zeichenfolge *Netzwerkschnittstellen* ein. Wenn **Netzwerkschnittstellen** in den Suchergebnissen angezeigt wird, wählen Sie dies aus.
2. Wählen Sie **...** auf der rechten Seite der Netzwerkschnittstelle aus, die Sie aus der Liste der Netzwerkschnittstellen löschen möchten.
3. Klicken Sie auf **Löschen**.
4. Wählen Sie **Ja** aus, um das Löschen der Netzwerkschnittstelle zu bestätigen.

Wenn Sie eine Netzwerkschnittstelle löschen, werden alle ihr zugewiesenen MAC- und IP-Adressen freigegeben.

**Befehle**

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az network nic delete](/cli/azure/network/nic)|
|PowerShell|[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Lösen von Konnektivitätsproblemen

Wenn die ein- oder ausgehende Kommunikation bei einem virtuellen Computer nicht möglich ist, können Sicherheitsregeln der Netzwerksicherheitsgruppe oder für eine Netzwerkschnittstelle gültige Routen die Ursache des Problems sein. Folgende Optionen zum Beheben des Problems stehen Ihnen zur Verfügung:

### <a name="view-effective-security-rules"></a>Anzeigen effektiver Sicherheitsregeln

Die effektiven Sicherheitsregeln für jede Netzwerkschnittstelle, die einem virtuellen Computer angefügt sind, stellen eine Kombination der Regeln dar, die Sie in einer Netzwerksicherheitsgruppe erstellt haben, und der [Standardsicherheitsregeln](security-overview.md#default-security-rules). Wenn Sie die effektiven Sicherheitsregeln für eine Netzwerkschnittstelle kennen, können Sie leichter feststellen, warum Sie nicht mit einem virtuellen Computer kommunizieren können. Sie können die effektiven Regeln für alle Netzwerkschnittstellen anzeigen, die einem ausgeführten virtuellen Computer angefügt sind.

1. Geben Sie im Suchfeld oben im Portal den Namen eines virtuellen Computers ein, für den Sie effektive Sicherheitsregeln anzeigen möchten. Wenn Sie den Namen eines virtuellen Computers kennen, geben Sie im Suchfeld *Virtuelle Computer* ein. Wählen Sie **Virtuelle Computer** aus, wenn der Begriff in den Suchergebnissen angezeigt wird, und wählen Sie in der Liste einen virtuellen Computer aus.
2. Wählen Sie unter **EINSTELLUNGEN** die Option **Netzwerk**.
3. Wählen Sie den Namen einer Netzwerkschnittstelle aus.
4. Wählen Sie unter **SUPPORT + PROBLEMBEHANDLUNG** die Option **Effektive Sicherheitsregeln** aus.
5. Überprüfen Sie die Liste der effektiven Sicherheitsregeln, um zu bestimmen, ob die richtigen Regeln für die erforderliche ein- und ausgehende Kommunikation vorhanden sind. Weitere Informationen über den Inhalt der Liste finden Sie unter [Netzwerksicherheit](security-overview.md).

Die IP-Flussüberprüfungsfunktion von Azure Network Watcher kann Ihnen ebenfalls helfen, zu bestimmen, ob Sicherheitsregeln die Kommunikation zwischen einem virtuellen Computer und einem Endpunkt verhindern. Weitere Informationen finden Sie unter [Diagnose eines VM-Netzwerkdatenverkehr-Filterproblems – Azure-Portal](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Befehle**

- Azure CLI: [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- PowerShell: [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

### <a name="view-effective-routes"></a>Anzeigen effektiver Routen

Die effektiven Routen für die Netzwerkschnittstellen, die einem virtuellen Computer angefügt sind, sind eine Kombination der Standardrouten, der von Ihnen erstellten Routen und der Routen, die von lokalen Netzwerken per BGP über das Gateway eines virtuellen Azure-Netzwerks verteilt werden. Wenn Sie die effektiven Routen für eine Netzwerkschnittstelle kennen, können Sie leichter feststellen, warum Sie nicht mit einem virtuellen Computer kommunizieren können. Sie können die effektiven Routen für alle Netzwerkschnittstellen anzeigen, die an einen ausgeführten virtuellen Computer angefügt sind.

1. Geben Sie im Suchfeld oben im Portal den Namen eines virtuellen Computers ein, für den Sie effektive Sicherheitsregeln anzeigen möchten. Wenn Sie den Namen eines virtuellen Computers kennen, geben Sie im Suchfeld *Virtuelle Computer* ein. Wählen Sie **Virtuelle Computer** aus, wenn der Begriff in den Suchergebnissen angezeigt wird, und wählen Sie in der Liste einen virtuellen Computer aus.
2. Wählen Sie unter **EINSTELLUNGEN** die Option **Netzwerk**.
3. Wählen Sie den Namen einer Netzwerkschnittstelle aus.
4. Wählen Sie unter **SUPPORT + PROBLEMBEHANDLUNG** die Option **Effektive Routen**.
5. Überprüfen Sie die Liste der effektiven Routen, um zu bestimmen, ob die richtigen Routen für die erforderliche ein- und ausgehende Kommunikation vorhanden sind. Weitere Informationen zu den in der Liste angezeigten Inhalten finden Sie unter [Routing von Datenverkehr für virtuelle Netzwerke](virtual-networks-udr-overview.md).

Die „Nächster Hop“-Funktion von Azure Network Watcher kann Ihnen ebenfalls helfen, zu bestimmen, ob Routen die Kommunikation zwischen einem virtuellen Computer und einem Endpunkt verhindern. Weitere Informationen finden Sie unter [Diagnose eines VM-Netzwerkroutingproblems – Azure-Portal](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Befehle**

- Azure CLI: [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="permissions"></a>Berechtigungen

Zum Durchführen von Aufgaben für Netzwerkschnittstellen muss Ihr Konto der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Rolle zugewiesen sein, die über die entsprechenden Berechtigungen in der folgenden Tabelle verfügt:

| Aktion                                                                     | NAME                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft.Network/networkInterfaces/read                                   | Netzwerkschnittstelle abrufen                                     |
| Microsoft.Network/networkInterfaces/write                                  | Netzwerkschnittstelle erstellen oder aktualisieren                        |
| Microsoft.Network/networkInterfaces/join/action                            | Netzwerkschnittstelle an einen virtuellen Computer anfügen           |
| Microsoft.Network/networkInterfaces/delete                                 | Netzwerkschnittstelle löschen                                  |
| Microsoft.Network/networkInterfaces/joinViaPrivateIp/action                | Ressource mit einer Netzwerkschnittstelle verknüpfen per...     |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action             | Netzwerkschnittstelle für effektive Routingtabelle abrufen               |
| Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action  | Der Netzwerkschnittstelle zugeordnete Sicherheitsgruppen abrufen           |
| Microsoft.Network/networkInterfaces/loadBalancers/read                     | Load Balancer der Netzwerkschnittstelle abrufen                      |
| Microsoft.Network/networkInterfaces/serviceAssociations/read               | Dienstzuordnung abrufen                                   |
| Microsoft.Network/networkInterfaces/serviceAssociations/write              | Dienstzuordnung erstellen oder aktualisieren                    |
| Microsoft.Network/networkInterfaces/serviceAssociations/delete             | Dienstzuordnung löschen                                |
| Microsoft.Network/networkInterfaces/serviceAssociations/validate/action    | Dienstzuordnung überprüfen                              |
| Microsoft.Network/networkInterfaces/ipconfigurations/read                  | IP-Konfiguration der Netzwerkschnittstelle abrufen                    |

## <a name="next-steps"></a>Nächste Schritte

- Erstellen eines virtuellen Computers mit mehreren Netzwerkschnittstellenkarten per [Azure CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) oder [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Erstellen einer einzelnen NIC-VM mit mehreren IPv4-Adressen per [Azure CLI](virtual-network-multiple-ip-addresses-cli.md) oder [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
- Erstellen einer einzelnen NIC-VM mit einer privaten IPv6-Adresse (hinter einem Azure Load Balancer) per [Azure CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) oder [Azure Resource Manager-Vorlage](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Erstellen einer Netzwerkschnittstelle per [PowerShell](powershell-samples.md)- oder [Azure CLI](cli-samples.md)-Beispielskript oder per [Azure Resource Manager-Vorlagen](template-samples.md)
- Erstellen und Anwenden einer [Azure-Richtlinie](policy-samples.md) für virtuelle Netzwerke
