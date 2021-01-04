---
title: Konfigurieren von Azure Files-Netzwerkendpunkten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Netzwerkendpunkte für Azure Files konfigurieren.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 079d7aa9b654a318c7269a41605c3e146b08f127
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621330"
---
# <a name="configuring-azure-files-network-endpoints"></a>Konfigurieren von Azure Files-Netzwerkendpunkten

Azure Files verfügt über zwei Arten von Endpunkten für den Zugriff auf Azure-Dateifreigaben: 
- Öffentliche Endpunkte mit einer öffentlichen IP-Adresse, auf die von jedem Ort der Welt aus zugegriffen werden kann.
- Private Endpunkte, die in einem virtuellen Netzwerk vorhanden sind und eine private IP-Adresse im Adressraum des virtuellen Netzwerks aufweisen.

Öffentliche und private Endpunkte befinden sich unter dem Azure-Speicherkonto. Ein Speicherkonto ist ein Verwaltungskonstrukt, das einen gemeinsam genutzten Pool mit Speicherplatz darstellt, in dem Sie mehrere Dateifreigaben sowie weitere Speicherressourcen wie Blobcontainer oder Warteschlangen bereitstellen können.

In diesem Artikel geht es um die Konfiguration der Endpunkte eines Speicherkontos für den direkten Zugriff auf die Azure-Dateifreigabe. Die meisten Details in diesem Dokument gelten auch in Bezug darauf, wie die Azure-Dateisynchronisierung mit öffentlichen und privaten Endpunkten für das Speicherkonto interagiert. Weitere Informationen zu den Netzwerkaspekten für eine Bereitstellung der Azure-Dateisynchronisierung finden Sie aber unter [Konfigurieren der Proxy- und Firewalleinstellungen der Dateisynchronisierung](storage-sync-files-firewall-and-proxy.md).

Wir empfehlen Ihnen, vor dem Lesen dieses Leitfadens den Artikel [Azure Files – Überlegungen zum Netzwerkbetrieb](storage-files-networking-overview.md) zu lesen.

## <a name="prerequisites"></a>Voraussetzungen

- In diesem Artikel wird davon ausgegangen, dass Sie bereits ein Azure-Abonnement erstellt haben. Wenn Sie noch kein Abonnement haben, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
- In diesem Artikel wird davon ausgegangen, dass Sie bereits eine Azure-Dateifreigabe in einem Speicherkonto erstellt haben, mit dem aus der lokalen Umgebung eine Verbindung hergestellt werden soll. Informationen zum Erstellen einer Azure-Dateifreigabe finden Sie unter [Erstellen einer Azure-Dateifreigabe](storage-how-to-create-file-share.md).
- Falls Sie Azure PowerShell verwenden möchten, [installieren Sie die neueste Version](/powershell/azure/install-az-ps).
- Falls Sie die Azure CLI verwenden möchten, [installieren Sie die neueste Version](/cli/azure/install-azure-cli).

## <a name="endpoint-configurations"></a>Endpunktkonfigurationen

Sie können Ihre Endpunkte so konfigurieren, dass der Netzwerkzugriff auf Ihr Speicherkonto beschränkt wird. Der Speicherkontozugriff kann auf zwei Arten auf ein virtuelles Netzwerk beschränkt werden:

- [Erstellen eines oder mehrerer privater Endpunkte für das Speicherkonto](#create-a-private-endpoint) und Beschränken des gesamten Zugriffs auf den öffentlichen Endpunkt. Dadurch wird sichergestellt, dass nur von Datenverkehr aus den gewünschten virtuellen Netzwerken auf die Azure-Dateifreigaben im Speicherkonto zugegriffen werden kann.
- [Beschränken des öffentlichen Endpunkts auf ein einzelnes virtuelles Netzwerk (oder auf mehrere)](#restrict-public-endpoint-access). Hierzu werden sogenannte *Dienstendpunkte* des virtuellen Netzwerks verwendet. Wenn Sie den Datenverkehr für ein Speicherkonto über einen Dienstendpunkt beschränken, erfolgt der Zugriff auf das Speicherkonto weiterhin über die öffentliche IP-Adresse, aber der Zugriff ist nur von den Standorten aus möglich, die Sie in Ihrer Konfiguration angeben.

### <a name="create-a-private-endpoint"></a>Erstellen eines privaten Endpunkts

Die Erstellung eines privaten Endpunkts für Ihr Speicherkonto führt dazu, dass die folgenden Azure-Ressourcen bereitgestellt werden:

- **Ein privater Endpunkt**: Eine Azure-Ressource, die den privaten Endpunkt des Speicherkontos darstellt. Sie können sich dies wie eine Ressource vorstellen, über die ein Speicherkonto und eine Netzwerkschnittstelle miteinander verbunden werden.
- **Eine Netzwerkschnittstelle (NIC)** : Die Netzwerkschnittstelle, über die eine private IP-Adresse im angegebenen virtuellen Netzwerk bzw. Subnetz verwaltet wird. Dies ist genau die gleiche Ressource, die bei der Bereitstellung eines virtuellen Computers bereitgestellt wird. Sie wird aber nicht einer VM zugewiesen, sondern befindet sich im Besitz des privaten Endpunkts.
- **Eine private DNS-Zone**: Falls Sie für dieses virtuelle Netzwerk noch keinen privaten Endpunkt bereitgestellt haben, wird für Ihr virtuelles Netzwerk eine neue private DNS-Zone bereitgestellt. Für das Speicherkonto in dieser DNS-Zone wird auch ein DNS-A-Eintrag erstellt. Wenn Sie in diesem virtuellen Netzwerk bereits einen privaten Endpunkt bereitgestellt haben, wird der vorhandenen DNS-Zone ein neuer A-Eintrag für das Speicherkonto hinzugefügt. Die Bereitstellung einer DNS-Zone ist zwar optional, aber sehr zu empfehlen. Diese Vorgehensweise ist zwingend erforderlich, wenn Sie Ihre Azure-Dateifreigaben mit einem AD-Dienstprinzipal oder per FileREST-API bereitstellen.

> [!Note]  
> In diesem Artikel wird das Speicherkonto-DNS-Suffix für die öffentlichen Azure-Regionen (`core.windows.net`) verwendet. Dieser Kommentar gilt auch für Azure Sovereign Clouds wie etwa die Azure US Government-Cloud und die Azure China-Cloud. Verwenden Sie einfach die entsprechenden Suffixe für Ihre Umgebung. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-private-portal](../../../includes/storage-files-networking-endpoints-private-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-private-powershell](../../../includes/storage-files-networking-endpoints-private-powershell.md)]

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-private-cli](../../../includes/storage-files-networking-endpoints-private-cli.md)]
---

## <a name="verify-connectivity"></a>Überprüfen der Konnektivität

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wenn Sie in Ihrem virtuellen Netzwerk über einen virtuellen Computer verfügen oder die DNS-Weiterleitung wie unter [Konfigurieren der DNS-Weiterleitung für Azure Files](storage-files-networking-dns.md) beschrieben konfiguriert haben, können Sie testen, ob Ihr privater Endpunkt richtig eingerichtet wurde. Führen Sie hierzu die folgenden Befehle über PowerShell, die Befehlszeile oder das Terminal (für Windows, Linux oder macOS) aus. Sie müssen `<storage-account-name>` durch den entsprechenden Speicherkontonamen ersetzen:

```
nslookup <storage-account-name>.file.core.windows.net
```

Falls alles richtig funktioniert hat, sollte die folgende Ausgabe (Beispiel für Windows) angezeigt werden, wobei `192.168.0.5` die private IP-Adresse des privaten Endpunkts in Ihrem virtuellen Netzwerk ist:

```Output
Server:  UnKnown
Address:  10.2.4.4

Non-authoritative answer:
Name:    storageaccount.privatelink.file.core.windows.net
Address:  192.168.0.5
Aliases:  storageaccount.file.core.windows.net
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Falls Sie in Ihrem virtuellen Netzwerk über einen virtuellen Computer verfügen oder die DNS-Weiterleitung wie unter [Konfigurieren der DNS-Weiterleitung für Azure Files](storage-files-networking-dns.md) beschrieben konfiguriert haben, können Sie mit den folgenden Befehlen testen, ob Ihr privater Endpunkt richtig eingerichtet wurde:

```PowerShell
$storageAccountHostName = [System.Uri]::new($storageAccount.PrimaryEndpoints.file) | `
    Select-Object -ExpandProperty Host

Resolve-DnsName -Name $storageAccountHostName
```

Falls alles gut funktioniert hat, sollte die folgende Ausgabe angezeigt werden, wobei `192.168.0.5` die private IP-Adresse des privaten Endpunkts in Ihrem virtuellen Netzwerk ist:

```Output
Name                             Type   TTL   Section    NameHost
----                             ----   ---   -------    --------
storageaccount.file.core.windows CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
.net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 600
Section    : Answer
IP4Address : 192.168.0.5
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Falls Sie in Ihrem virtuellen Netzwerk über einen virtuellen Computer verfügen oder die DNS-Weiterleitung wie unter [Konfigurieren der DNS-Weiterleitung für Azure Files](storage-files-networking-dns.md) beschrieben konfiguriert haben, können Sie mit den folgenden Befehlen testen, ob Ihr privater Endpunkt richtig eingerichtet wurde:

```bash
httpEndpoint=$(az storage account show \
        --resource-group $storageAccountResourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | \
    tr -d '"')

hostName=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint) | tr -d "/")
nslookup $hostName
```

Falls alles gut funktioniert hat, sollte die folgende Ausgabe angezeigt werden, wobei `192.168.0.5` die private IP-Adresse des privaten Endpunkts in Ihrem virtuellen Netzwerk ist. Sie sollten weiterhin „storageaccount.file.core.windows.net“ anstelle des `privatelink`-Pfads verwenden, um Ihre Dateifreigabe einzubinden.

```Output
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
storageaccount.file.core.windows.net      canonical name = storageaccount.privatelink.file.core.windows.net.
Name:   storageaccount.privatelink.file.core.windows.net
Address: 192.168.0.5
```
---

## <a name="restrict-public-endpoint-access"></a>Einschränken des Zugriffs auf den öffentlichen Endpunkt

Um den Zugriff auf den öffentlichen Endpunkt einzuschränken, müssen Sie zuerst den allgemeinen Zugriff auf den öffentlichen Endpunkt deaktivieren. Das Deaktivieren des Zugriffs auf den öffentlichen Endpunkt wirkt sich nicht auf private Endpunkte aus. Nachdem der öffentliche Endpunkt deaktiviert wurde, können Sie bestimmte Netzwerke oder IP-Adressen auswählen, die weiterhin darauf zugreifen dürfen. Im Allgemeinen beschränken die meisten Firewallrichtlinien für Speicherkonten den Netzwerkzugriff auf ein virtuelles Netzwerk (oder auf mehrere).

#### <a name="disable-access-to-the-public-endpoint"></a>Deaktivieren des Zugriffs auf den öffentlichen Endpunkt

Wenn der gesamte Zugriff auf den öffentlichen Endpunkt deaktiviert wird, kann über die entsprechenden privaten Endpunkte weiterhin auf das Speicherkonto zugegriffen werden. Andernfalls werden gültige Anforderungen, die an den öffentlichen Endpunkt des Speicherkontos gesendet werden, abgelehnt, es sei denn, sie stammen von [einer bestimmten zugelassenen Quelle](#restrict-access-to-the-public-endpoint-to-specific-virtual-networks). 

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-disable-portal](../../../includes/storage-files-networking-endpoints-public-disable-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-disable-powershell](../../../includes/storage-files-networking-endpoints-public-disable-powershell.md)]

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-disable-cli](../../../includes/storage-files-networking-endpoints-public-disable-cli.md)]

---

#### <a name="restrict-access-to-the-public-endpoint-to-specific-virtual-networks"></a>Einschränken des Zugriffs auf den öffentlichen Endpunkt auf bestimmte virtuelle Netzwerke

Wenn Sie das Speicherkonto auf bestimmte virtuelle Netzwerke einschränken, lassen Sie Anforderungen an den öffentlichen Endpunkt aus den angegebenen virtuellen Netzwerken zu. Hierzu werden sogenannte *Dienstendpunkte* des virtuellen Netzwerks verwendet. Die Nutzung ist mit oder ohne private Endpunkte möglich.

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-portal](../../../includes/storage-files-networking-endpoints-public-restrict-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-powershell](../../../includes/storage-files-networking-endpoints-public-restrict-powershell.md)]

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
[!INCLUDE [storage-files-networking-endpoints-public-restrict-cli](../../../includes/storage-files-networking-endpoints-public-restrict-cli.md)]

---

## <a name="see-also"></a>Weitere Informationen

- [Azure Files – Überlegungen zum Netzwerkbetrieb](storage-files-networking-overview.md)
- [Konfigurieren der DNS-Weiterleitung für Azure Files](storage-files-networking-dns.md)
- [Konfigurieren eines Site-to-Site-VPN zur Verwendung mit Azure Files](storage-files-configure-s2s-vpn.md)