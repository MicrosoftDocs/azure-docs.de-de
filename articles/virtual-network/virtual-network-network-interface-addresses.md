---
title: Konfigurieren von IP-Adressen für Azure-Netzwerkschnittstellen | Microsoft-Dokumentation
description: Erfahren Sie etwas über das Hinzufügen, Ändern und Entfernen öffentlicher und privater IP-Adressen für Netzwerkschnittstellen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: kumud
ms.openlocfilehash: 4582f7be8e48e493a1adcb8ffc6c3a8bfe43a58e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65506373"
---
# <a name="add-change-or-remove-ip-addresses-for-an-azure-network-interface"></a>Hinzufügen, Ändern oder Entfernen von IP-Adressen für Azure-Netzwerkschnittstellen

Erfahren Sie etwas über das Hinzufügen, Ändern und Entfernen öffentlicher und privater IP-Adressen für Netzwerkschnittstellen. Die einer Netzwerkschnittstelle zugewiesenen privaten IP-Adressen ermöglichen dem virtuellen Computer die Kommunikation mit anderen Ressourcen im virtuellen Azure-Netzwerk und verbundenen Netzwerken. Eine private IP-Adresse ermöglicht ebenfalls die ausgehende Kommunikation mit dem Internet über eine nicht vorhersagbare IP-Adresse. Das Zuweisen einer [öffentlichen IP-Adresse](virtual-network-public-ip-address.md) zu einer Netzwerkschnittstelle ermöglicht eingehende Kommunikation an einen virtuellen Computer aus dem Internet. Die Adresse ermöglicht außerdem die ausgehende Kommunikation vom virtuellen Computer mit dem Internet über eine vorhersagbare IP-Adresse. Weitere Informationen finden Sie unter [Grundlegendes zu ausgehenden Verbindungen in Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Informationen zum Erstellen, Ändern oder Löschen von Netzwerkschnittstellen finden Sie im Artikel [Verwalten von Netzwerkschnittstellen](virtual-network-network-interface.md). Wenn Sie auf einem virtuellen Computer Netzwerkschnittstellen hinzufügen oder entfernen möchten, lesen Sie den Artikel [Hinzufügen oder Entfernen von Netzwerkschnittstellen](virtual-network-network-interface-vm.md).

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Führen Sie zuerst die folgenden Aufgaben aus, ehe Sie die Schritte in den Abschnitten dieses Artikels durchführen:

- Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für ein [kostenloses Testkonto](https://azure.microsoft.com/free) registrieren.
- Öffnen Sie bei Verwendung des Portals https://portal.azure.com, und melden Sie sich mit Ihrem Azure-Konto an.
- Wenn Sie PowerShell-Befehle zum Durchführen von Aufgaben in diesem Artikel verwenden, führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/powershell) oder durch Ausführen von PowerShell auf Ihrem Computer aus. Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. Für dieses Tutorial ist das Azure PowerShell-Modul Version 1.0.0 oder höher erforderlich. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.
- Wenn Sie Befehle der Azure-Befehlszeilenschnittstelle (CLI) zum Durchführen von Aufgaben in diesem Artikel verwenden, führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/bash) oder durch Ausführen der CLI auf Ihrem Computer aus. Für dieses Tutorial ist die Azure CLI-Version 2.0.31 oder höher erforderlich. Führen Sie `az --version` aus, um die installierte Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli). Wenn Sie die Azure CLI lokal ausführen, müssen Sie auch `az login` ausführen, um eine Verbindung mit Azure herzustellen.

Das Konto, bei dem Sie sich anmelden oder das Sie zum Herstellen einer Verbindung mit Azure verwenden, muss der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten Rolle](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zugewiesen sein, der die entsprechenden unter [Berechtigungen für Netzwerkschnittstellen](virtual-network-network-interface.md#permissions) aufgeführten Aktionen zugewiesen wurden.

## <a name="add-ip-addresses"></a>Hinzufügen von IP-Adressen

Einer Netzwerkschnittstelle können beliebig viele [private](#private) und [öffentliche](#public) [IPv4](#ipv4)-Adressen hinzugefügt werden (im Rahmen der im Artikel [Einschränkungen bei Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) aufgeführten Grenzwerte). Sie können einer vorhandenen Netzwerkschnittstelle nicht über das Portal eine IPv6-Adresse hinzufügen. (Sie können im Portal jedoch einer Netzwerkschnittstelle bei ihrer Erstellung eine private IPv6-Adresse hinzufügen.) Sie können PowerShell oder die Befehlszeilenschnittstelle verwenden, um einer [sekundären IP-Konfiguration](#secondary) eine private IPv6-Adresse für eine vorhandene Netzwerkschnittstelle, die nicht einem virtuellen Computer zugeordnet ist, hinzuzufügen (solange noch keine sekundären IP-Konfigurationen vorhanden sind). Sie können nicht jedes Tool verwenden, um einer Netzwerkschnittstelle eine öffentliche IPv6-Adresse hinzuzufügen. Ausführliche Informationen zur Verwendung von IPv6-Adressen finden Sie unter [IPv6](#ipv6).

1. Geben Sie im oberen Bereich des Azure-Portals im Feld mit dem Text *Ressourcen suchen* die Zeichenfolge *Netzwerkschnittstellen* ein. Wenn **Netzwerkschnittstellen** in den Suchergebnissen angezeigt wird, wählen Sie dies aus.
2. Wählen Sie die Netzwerkschnittstelle aus, der Sie eine IPv4-Adresse aus der Liste hinzufügen möchten.
3. Wählen Sie unter **EINSTELLUNGEN** die Option **IP-Konfigurationen**.
4. Wählen Sie unter **IP-Konfigurationen** die Option **+ Hinzufügen**.
5. Geben Sie Folgendes an, und wählen Sie anschließend **OK**:

   |Einstellung|Erforderlich?|Details|
   |---|---|---|
   |NAME|Ja|Muss für die Netzwerkschnittstelle eindeutig sein|
   |Type|Ja|Da Sie die IP-Konfiguration einer vorhandenen Netzwerkschnittstelle hinzufügen und jede Netzwerkschnittstelle über eine [primäre](#primary) IP-Konfiguration verfügen muss, steht nur die Option **Sekundär** zur Verfügung.|
   |Zuweisungsmethode für private IP-Adressen|Ja|[**Dynamisch**](#dynamic): Azure weist die nächste verfügbare Adresse für den Subnetzadressbereich zu, in dem die Netzwerkschnittstelle bereitgestellt wird. [**Statisch**](#static): Sie weisen eine nicht verwendete Adresse für den Subnetzadressbereich zu, in dem die Netzwerkschnittstelle bereitgestellt wird.|
   |Öffentliche IP-Adresse|Nein|**Deaktiviert:** Der IP-Konfiguration ist derzeit keine öffentliche IP-Adressressource zugeordnet. **Aktiviert:** Wählen Sie eine vorhandene öffentliche IPv4-Adresse aus, oder erstellen Sie eine neue. Eine Anleitung zur Erstellung einer öffentlichen IP-Adresse finden Sie im Artikel [Public IP addresses](virtual-network-public-ip-address.md#create-a-public-ip-address) (Öffentliche IP-Adressen).|
6. Fügen Sie dem Betriebssystem des virtuellen Computers manuell sekundäre private IP-Adressen hinzu. Gehen Sie dabei wie im Artikel [Zuweisen von mehreren IP-Adressen zu Betriebssystemen virtueller Computer](virtual-network-multiple-ip-addresses-portal.md#os-config) beschrieben vor. Spezielle Überlegungen vor dem manuellen Hinzufügen von IP-Adressen zum Betriebssystem virtueller Computer finden Sie unter [private](#private) IP-Adressen. Fügen Sie dem Betriebssystem des virtuellen Computers keine öffentlichen IP-Adressen hinzu.

**Befehle**

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az network nic ip-config create](/cli/azure/network/nic/ip-config)|
|PowerShell|[Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/add-aznetworkinterfaceipconfig)|

## <a name="change-ip-address-settings"></a>Ändern von IP-Adresseinstellungen

Sie müssen ggf. die Zuweisungsmethode einer IPv4-Adresse, die statische IPv4-Adresse oder die einer Netzwerkschnittstelle zugewiesene öffentliche IP-Adresse ändern. Wenn Sie die private IPv4-Adresse einer sekundären IP-Konfiguration ändern, die einer sekundären Netzwerkschnittstelle eines virtuellen Computers zugeordnet ist (siehe [Primäre und sekundäre Netzwerkschnittstellen](virtual-network-network-interface-vm.md)), ändern Sie den Status des virtuellen Computers in „Beendet (Zuordnung aufgehoben)“, bevor Sie die folgenden Schritte ausführen:

1. Geben Sie im oberen Bereich des Azure-Portals im Feld mit dem Text *Ressourcen suchen* die Zeichenfolge *Netzwerkschnittstellen* ein. Wenn **Netzwerkschnittstellen** in den Suchergebnissen angezeigt wird, wählen Sie dies aus.
2. Wählen Sie in der Liste die Netzwerkschnittstelle aus, die Sie anzeigen oder deren IP-Adresseinstellungen Sie ändern möchten.
3. Wählen Sie unter **EINSTELLUNGEN** die Option **IP-Konfigurationen**.
4. Wählen Sie in der Liste die IP-Konfiguration aus, die Sie ändern möchten.
5. Ändern Sie die Einstellungen wie gewünscht, indem Sie die Informationen zu den Einstellungen in Schritt 5 unter [Hinzufügen einer IP-Konfiguration](#add-ip-addresses) verwenden.
6. Wählen Sie **Speichern** aus.

>[!NOTE]
>Falls die primäre Netzwerkschnittstelle über mehrere IP-Konfigurationen verfügt und Sie die private IP-Adresse der primären IP-Konfiguration ändern, müssen Sie unter Windows die primäre und alle sekundären IP-Adressen manuell wieder der Netzwerkschnittstelle zuweisen. (Unter Linux ist dies nicht erforderlich.) Wie Sie IP-Adressen im Rahmen eines Betriebssystems manuell einer Netzwerkschnittstelle zuweisen, erfahren Sie unter [Zuweisen von mehreren IP-Adressen zu virtuellen Computern](virtual-network-multiple-ip-addresses-portal.md#os-config). Spezielle zu berücksichtigende Punkte vor dem manuellen Hinzufügen von IP-Adressen zum Betriebssystem virtueller Computer finden Sie im Abschnitt zu [privaten](#private) IP-Adressen. Fügen Sie dem Betriebssystem des virtuellen Computers keine öffentlichen IP-Adressen hinzu.

**Befehle**

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az network nic ip-config update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="remove-ip-addresses"></a>Entfernen von IP-Adressen

Sie können [private](#private) und [öffentliche](#public) IP-Adressen von einer Netzwerkschnittstelle entfernen. Doch einer Netzwerkschnittstelle muss stets mindestens eine private IPv4-Adresse zugewiesen sein.

1. Geben Sie im oberen Bereich des Azure-Portals im Feld mit dem Text *Ressourcen suchen* die Zeichenfolge *Netzwerkschnittstellen* ein. Wenn **Netzwerkschnittstellen** in den Suchergebnissen angezeigt wird, wählen Sie dies aus.
2. Wählen Sie in der Liste die Netzwerkschnittstelle aus, die Sie entfernen möchten.
3. Wählen Sie unter **EINSTELLUNGEN** die Option **IP-Konfigurationen**.
4. Wählen Sie mit der rechten Maustaste eine [sekundäre](#secondary) IP-Konfiguration (die [primäre](#primary) Konfiguration können Sie nicht löschen) aus, die Sie löschen möchten. Wählen Sie **Löschen** und dann **Ja**, um den Löschvorgang zu bestätigen. Falls der Konfiguration eine öffentliche IP-Adressressource zugeordnet war, wird die Zuweisung der Ressource zur IP-Konfiguration aufgehoben, die Ressource wird jedoch nicht gelöscht.

**Befehle**

|Tool|Get-Help|
|---|---|
|Befehlszeilenschnittstelle (CLI)|[az network nic ip-config delete](/cli/azure/network/nic/ip-config)|
|PowerShell|[Remove-AzNetworkInterfaceIpConfig](/powershell/module/az.network/remove-aznetworkinterfaceipconfig)|

## <a name="ip-configurations"></a>IP-Konfigurationen

[Private](#private) und (optional) [öffentliche](#public) IP-Adressen werden einer oder mehreren IP-Konfigurationen zugewiesen, die wiederum einer Netzwerkschnittstelle zugewiesen sind. Es gibt zwei Arten von IP-Konfigurationen:

### <a name="primary"></a>Primär

Jede Netzwerkschnittstelle ist einer primären IP-Konfiguration zugewiesen. Einer primären IP-Konfiguration:

- Ist eine [private](#private) [IPv4](#ipv4)-Adresse zugewiesen. Sie können eine private [IPv6](#ipv6)-Adresse nicht einer primären IP-Konfiguration zuweisen.
- Kann auch eine [öffentliche](#public) IPv4-Adresse zugewiesen werden. Sie können eine öffentliche IPv6-Adresse nicht einer primären oder sekundären IP-Konfiguration zuweisen. Sie können jedoch einer Azure Load Balancer-Instanz eine öffentliche IPv6-Adresse zuweisen, die dann einen Lastenausgleich für den Datenverkehr zur privaten IPv6-Adresse eines virtuellen Computers durchführt. Weitere Informationen finden Sie unter [Details und Einschränkungen für IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations).

### <a name="secondary"></a>Sekundär

Zusätzlich zu einer primären IP-Konfiguration können einer Netzwerkschnittstelle null oder mehr sekundäre IP-Konfigurationen zugewiesen werden. Einer sekundären IP-Konfiguration:

- Muss eine private IPv4- oder IPv6-Adresse zugewiesen werden. Wenn es sich um eine IPv6-Adresse handelt, kann die Netzwerkschnittstelle nur über eine sekundäre IP-Konfiguration verfügen. Wenn es sich um eine IPv4-Adresse handelt, können der Netzwerkschnittstelle mehrere sekundäre IP-Konfiguration zugewiesen werden. Weitere Informationen zur Anzahl der öffentlichen und privaten IPv4-Adressen, die einer Netzwerkschnittstelle zugewiesen werden können, finden Sie im Artikel [Einschränkungen bei Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Kann auch eine öffentliche IPv4-Adresse zugewiesen werden, wenn die private IP-Adresse eine IPv4-Adresse ist. Wenn die private IP-Adresse eine IPv6-Adresse ist, können Sie der IP-Konfiguration keine öffentliche IPv4- oder IPv6-Adresse zuweisen. In Szenarien wie den folgenden kann es hilfreich sein, einer Netzwerkschnittstelle mehrere IP-Adressen zuzuweisen:
  - Hosten mehrerer Websites oder Dienste mit unterschiedlichen IP-Adressen und SSL-Zertifikaten auf einem einzelnen Server
  - Verwenden eines virtuellen Computers als virtuelles Netzwerkgerät (etwa als Firewall oder Lastenausgleich)
  - Fähigkeit zum Hinzufügen einer privaten IPv4-Adresse für eine der Netzwerkschnittstellen zu einem Azure Load Balancer-Back-End-Pool. Bisher konnte nur die primäre IPv4-Adresse für die primäre Netzwerkschnittstelle einem Back-End-Pool hinzugefügt werden. Weitere Informationen dazu, wie bei mehreren IPv4-Konfigurationen ein Lastenausgleich durchgeführt werden kann, finden Sie im Artikel [Lastenausgleich bei mehreren IP-Konfigurationen](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
  - Die Möglichkeit eines Lastenausgleichs für eine IPv6-Adresse, die einer Netzwerkschnittstelle zugewiesen ist. Weitere Informationen zum Lastenausgleich bei einer privaten IPv6-Adresse finden Sie im Artikel [Lastenausgleich bei IPv6-Adressen](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="address-types"></a>Adresstypen

Sie können einer [IP-Konfiguration](#ip-configurations) die folgenden Typen von IP-Adressen zuweisen:

### <a name="private"></a>Private

Die privaten [IPv4](#ipv4)-Adressen ermöglichen einem virtuellen Computer die Kommunikation mit anderen Ressourcen im virtuellen Netzwerk und verbundenen Netzwerken. Die eingehende Kommunikation mit einem virtuellen Computer ist nicht möglich, und der virtuelle Computer kann auch nicht ausgehend mit einer privaten [IPv6](#ipv6)-Adresse kommunizieren. Dazu gibt es jedoch eine Ausnahme. Ein virtueller Computer kann mit Azure Load Balancer über eine IPv6-Adresse kommunizieren. Weitere Informationen finden Sie unter [Details und Einschränkungen für IPv6](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#details-and-limitations).

Standardmäßig weisen die Azure-DHCP-Server der Netzwerkschnittstelle die private IPv4-Adresse für die [primäre IP-Konfiguration](#primary) der Azure-Netzwerkschnittstelle innerhalb des Betriebssystems des virtuellen Computers zu. Sofern dies nicht unbedingt erforderlich ist, sollten Sie die IP-Adresse einer Netzwerkschnittstelle nie manuell im Betriebssystem des virtuellen Computers festlegen.

> [!WARNING]
> Wenn sich die als primäre IP-Adresse einer Netzwerkschnittstelle im Betriebssystem des virtuellen Computers festgelegte IPv4-Adresse je von der privaten IPv4-Adresse unterscheiden sollte, die der primären IP-Konfiguration der primären Netzwerkschnittstelle eines virtuellen Computers in Azure zugewiesen ist, verlieren Sie die Konnektivität mit dem virtuellen Computer.

Es gibt Situationen, in denen die IP-Adresse einer Netzwerkschnittstelle im Betriebssystem des virtuellen Computers manuell festgelegt werden muss. Dies gilt beispielsweise für das manuelle Festlegen der primären und sekundären IP-Adressen eines Windows-Betriebssystems beim Hinzufügen mehrerer IP-Adressen zu einem virtuellen Azure-Computer. Bei einem virtuellen Linux-Computer müssen Sie möglicherweise nur die sekundären IP-Adressen manuell festlegen. Weitere Informationen finden Sie unter [Hinzufügen von IP-Adressen zu einem VM-Betriebssystem](virtual-network-multiple-ip-addresses-portal.md#os-config). Wenn Sie die einer IP-Konfiguration zugewiesene Adresse einmal ändern müssen, beachten Sie folgende Empfehlungen:

1. Vergewissern Sie sich, dass der virtuelle Computer eine Adresse von den Azure DHCP-Servern empfängt. Sobald dies erfolgt ist, ändern Sie die Zuweisung der IP-Adresse im Betriebssystem wieder zurück zu DHCP, und führen Sie einen Neustart des virtuellen Computers aus.
2. Beenden Sie den virtuellen Computer (und heben die Zuordnung auf).
3. Ändern Sie die IP-Adresse für die IP-Konfiguration in Azure.
4. Starten Sie den virtuellen Computer.
5. [Konfigurieren Sie manuell](virtual-network-multiple-ip-addresses-portal.md#os-config) die sekundären IP-Adressen innerhalb des Betriebssystems (und auch die primäre IP-Adresse in Windows), damit diese mit Ihren Einstellungen in Azure übereinstimmen.

Wenn Sie die angegebenen Schritte befolgen, bleibt die zugewiesene private IP-Adresse der Netzwerkschnittstelle in Azure und im Betriebssystem des virtuellen Computers unverändert. Zum Nachverfolgen der virtuelle Computer innerhalb Ihres Abonnements, für die Sie manuell IP-Adressen im Betriebssystem festgelegt haben, sollten Sie den virtuellen Computern ein Azure-[Tag](../azure-resource-manager/resource-group-using-tags.md) hinzufügen. Sie können beispielsweise „IP-Adresszuweisung: statisch“ verwenden. Auf diese Weise können Sie problemlos die virtuellen Computer in Ihrem Abonnement finden, für die Sie manuell die IP-Adresse innerhalb des Betriebssystems festgelegt haben.

Zusätzlich zum Aktivieren der Kommunikation eines virtuellen Computers mit anderen Ressourcen innerhalb desselben Netzwerks oder in verbundenen virtuellen Netzwerken ermöglicht eine private IP-Adresse einem virtuellen Computer auch die ausgehende Kommunikation mit dem Internet. Für ausgehende Verbindungen erfolgt durch Azure eine Übersetzung der Quellnetzwerkadresse in eine nicht vorhersagbare öffentliche IP-Adresse. Weitere Informationen zu ausgehenden Internetverbindungen in Azure finden Sie im Artikel [Grundlegendes zu ausgehenden Verbindungen in Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Aus dem Internet ist keine eingehende Kommunikation mit dem privaten IP-Adressbereich eines virtuellen Computers möglich. Wenn Ihre ausgehenden Verbindungen eine vorhersagbare öffentliche IP-Adresse erfordern, ordnen Sie einer Netzwerkschnittstelle eine öffentliche IP-Adressressource zu.

### <a name="public"></a>Öffentlich

Öffentliche IP-Adressen, die über eine öffentliche IP-Adressressource zugewiesen werden, ermöglichen aus dem Internet eingehende Verbindungen mit einem virtuellen Computer. Ausgehende Verbindungen mit dem Internet verwenden eine vorhersagbare IP-Adresse. Weitere Informationen finden Sie unter [Grundlegendes zu ausgehenden Verbindungen in Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Sie können einer IP-Konfiguration eine öffentliche IP-Adresse zuweisen, was jedoch nicht erforderlich ist. Wenn Sie einem virtuellen Computer keine öffentliche IP-Adresse durch Zuordnen einer öffentlichen IP-Adressressource zuweisen, kann der virtuelle Computer trotzdem ausgehend mit dem Internet kommunizieren. In diesem Fall ist die private IP-Adresse die Quellnetzwerkadresse, die von Azure in eine nicht vorhersagbare öffentliche IP-Adresse übersetzt wird. Weitere Informationen zu öffentlichen IP-Adressressourcen finden Sie unter [Öffentliche IP-Adressressource](virtual-network-public-ip-address.md).

Die Anzahl öffentlicher und privater IP-Adressen, die Sie einer Netzwerkschnittstelle zuweisen können, ist begrenzt. Ausführliche Informationen finden Sie im Artikel [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

> [!NOTE]
> Azure übersetzt die private IP-Adresse eines virtuellen Computers in eine öffentliche IP-Adresse. Daher weiß das Betriebssystem eines virtuellen Computers nichts über die ihm zugewiesene öffentliche IP-Adresse. Aus diesem Grund besteht auch keine Notwendigkeit, innerhalb des Betriebssystems eine öffentliche IP-Adresse manuell zuzuweisen.

## <a name="assignment-methods"></a>Zuweisungsmethoden

Öffentliche und private IP-Adressen können mithilfe einer der folgenden Zuweisungsmethoden zugewiesen werden:

### <a name="dynamic"></a>Dynamisch

Standardmäßig werden dynamische private IPv4- und (optionale) IPv6-Adressen zugewiesen.

- **Nur öffentlich**: Azure weist die Adresse aus einem für jede Azure-Region eindeutigen Bereich zu. Informationen dazu, welche Bereiche den jeweiligen Regionen zugewiesen sind, finden Sie unter [IP-Bereiche für Microsoft Azure-Rechenzentren](https://www.microsoft.com/download/details.aspx?id=41653). Die Adresse kann sich ändern, wenn ein virtueller Computer angehalten (freigegeben) und dann erneut gestartet wird. Sie können eine öffentliche IPv6-Adresse mit keiner der beiden Zuweisungsmethoden einer IP-Konfiguration zuweisen.
- **Nur privat**: Azure reserviert die ersten vier Adressen in jedem Subnetzadressbereich und weist die Adressen nicht zu. Azure weist die nächste verfügbare Adresse einer Ressource aus dem Subnetzadressbereich zu. Wenn der Adressbereich des Subnetzes beispielsweise 10.0.0.0/16 lautet und die Adressen 10.0.0.0.4 bis 10.0.0.14 bereits zugewiesen sind (.0 bis .3 sind reserviert), weist Azure der Ressource die Adresse 10.0.0.15 zu. „Dynamisch“ ist das Standardverfahren für die Zuteilung. Nach der Zuweisung werden dynamische IP-Adressen nur freigegeben, wenn eine Netzwerkschnittstelle gelöscht wird oder einem anderen Subnetz desselben virtuellen Netzwerks zugewiesen wird oder wenn die Zuordnungsmethode in „Statisch“ geändert und eine andere IP-Adresse angegeben wird. Standardmäßig wird in Azure die zuvor dynamisch zugewiesene Adresse als statische Adresse zugewiesen, wenn Sie die Zuteilungsmethode von „Dynamisch“ in „Statisch“ ändern. Sie können mit der dynamischen Zuweisungsmethode nur eine private IPv6-Adresse zuweisen.

### <a name="static"></a>statischen

Sie können einer IP-Konfiguration (optional) eine öffentliche oder private statische IPv4-Adresse zuweisen. Sie können einer IP-Konfiguration jedoch keine öffentliche oder private statische IPv6-Adresse zuweisen. Weitere Informationen dazu, wie Azure öffentliche statische IPv4-Adressen zuweist, finden Sie unter [Öffentliche IP-Adressen](virtual-network-public-ip-address.md).

- **Nur öffentlich**: Azure weist die Adresse aus einem für jede Azure-Region eindeutigen Bereich zu. Sie können die Liste von Bereichen (Präfixen) für die [öffentliche Azure-Cloud](https://www.microsoft.com/download/details.aspx?id=56519), die [Azure US Government-Cloud](https://www.microsoft.com/download/details.aspx?id=57063) sowie für die Azure-Cloud in [China](https://www.microsoft.com/download/details.aspx?id=57062) und [Deutschland](https://www.microsoft.com/download/details.aspx?id=57064) herunterladen. Die Adresse ändern sich erst dann, wenn die öffentliche IP-Adressressource, der sie zugewiesen ist, gelöscht wird oder die Zuweisungsmethode in „Dynamisch“ geändert wird. Wenn die öffentliche IP-Adressressource einer IP-Konfiguration zugeordnet ist, muss vor dem Ändern der Zuordnungsmethode die Zuordnung zur IP-Konfiguration aufgehoben werden.
- **Nur privat**: Sie wählen eine Adresse aus dem Adressbereich des Subnetzes aus und weisen sie zu. Die von Ihnen zugewiesene Adresse kann eine beliebige Adresse innerhalb des Subnetzadressbereichs sein, bei der es sich nicht um eine der ersten vier Adressen im Subnetzadressbereich handelt und die derzeit keiner anderen Ressource im Subnetz zugewiesen ist. Statische Adressen werden nur freigegeben, wenn eine Netzwerkschnittstelle gelöscht wird. Wenn Sie die Zuweisungsmethode in „statisch“ ändern, weist Azure die zuvor zugewiesene dynamische IP-Adresse dynamisch als statische Adresse zu, auch wenn die Adresse nicht die nächste verfügbare Adresse im Adressbereich des Subnetzes ist. Die Adresse ändert sich auch, wenn die Netzwerkschnittstelle einem anderen Subnetz desselben virtuellen Netzwerks zugewiesen wird. Wenn Sie die Netzwerkschnittstelle einem anderen Subnetz zuweisen möchten, müssen Sie zuerst die Zuteilungsmethode von „Statisch“ in „Dynamisch“ ändern. Nachdem Sie die Netzwerkschnittstelle einem anderen Subnetz zugewiesen haben, können Sie die Zuteilungsmethode wieder in „Statisch“ ändern und eine IP-Adresse aus dem Adressbereich des neuen Subnetzes zuweisen.

## <a name="ip-address-versions"></a>IP-Adressversionen

Sie können beim Zuweisen von Adressen die folgenden Versionen angeben:

### <a name="ipv4"></a>IPv4

Jede Netzwerkschnittstelle benötigt eine [primäre](#primary) IP-Konfiguration mit einer zugewiesenen [privaten](#private) [IPv4](#ipv4)-Adresse. Sie können eine oder mehrere [sekundäre](#secondary) IP-Konfigurationen hinzufügen, die jeweils über eine private IPv4- und (optional) eine [öffentliche](#public) IPv4-Adresse verfügen.

### <a name="ipv6"></a>IPv6

Sie können der sekundären IP-Konfiguration einer Netzwerkschnittstelle null oder eine private [IPv6](#ipv6)-Adresse zuweisen. Die Netzwerkschnittstelle kann nicht über sekundäre IP-Konfigurationen verfügen. Sie können über das Portal keine IP-Konfiguration mit einer IPv6-Adresse hinzufügen. Verwenden Sie PowerShell oder die Befehlszeilenschnittstelle, um einer vorhandenen Netzwerkschnittstelle eine IP-Konfiguration mit einer privaten IPv6-Adresse hinzuzufügen. Die Netzwerkschnittstelle kann nicht an vorhandene virtuelle Computer angefügt werden.

> [!NOTE]
> Sie können über das Portal zwar eine Netzwerkschnittstelle mit einer IPv6-Adresse erstellen, es ist jedoch nicht möglich, einem neuen oder vorhandenen virtuellen Computer über das Portal eine vorhandene Netzwerkschnittstelle hinzuzufügen. Verwenden Sie PowerShell oder die Azure-Befehlszeilenschnittstelle, um eine Netzwerkschnittstelle mit einer privaten IPv6-Adresse zu erstellen, und fügen Sie die Netzwerkschnittstelle dann beim Erstellen eines virtuellen Computers an. Eine Netzwerkschnittstelle mit einer zugewiesenen privaten IPv6-Adresse kann nicht einem vorhandenen virtuellen Computer angefügt werden. Sie können einer IP-Konfiguration für eine beliebige Netzwerkschnittstelle eines virtuellen Computers keine private IPv6-Adresse hinzufügen (egal ob mit Portal, CLI oder PowerShell).

Sie können eine öffentliche IPv6-Adresse nicht einer primären oder sekundären IP-Konfiguration zuweisen.

## <a name="skus"></a>SKUs

Es wird eine öffentliche IP-Adresse mit der Basic- oder Standard-SKU erstellt. Weitere Informationen zu den Unterschieden zwischen SKUs finden Sie unter [Erstellen, Ändern oder Löschen einer öffentlichen IP-Adresse](virtual-network-public-ip-address.md).

> [!NOTE]
> Wenn Sie der Netzwerkschnittstelle eines virtuellen Computers eine öffentliche IP-Adresse für eine Standard-SKU zuweisen, müssen Sie den geplanten Datenverkehr explizit mit einer [Netzwerksicherheitsgruppe](security-overview.md#network-security-groups) zulassen. Die Kommunikation mit der Ressource schlägt fehl, bis Sie eine Netzwerksicherheitsgruppe erstellen und zuordnen und den gewünschten Datenverkehr explizit zulassen.

## <a name="next-steps"></a>Nächste Schritte
Um einen virtuellen Computer mit unterschiedlichen IP-Konfigurationen zu erstellen, lesen Sie die folgenden Artikel:

|Aufgabe|Tool|
|---|---|
|Erstellen eines virtuellen Computers mit mehreren Netzwerkschnittstellen|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Erstellen eines virtuellen Computers mit einer NIC und mehreren IPv4-Adressen|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Erstellen eines virtuellen Computers mit einer NIC und einer IPv6-Adresse (mit Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [Azure Resource Manager-Vorlage](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
