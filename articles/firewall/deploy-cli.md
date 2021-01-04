---
title: Bereitstellen und Konfigurieren von Azure Firewall mithilfe von Azure CLI
description: In diesem Artikel erfahren Sie, wie Sie Azure Firewall unter Verwendung von Azure CLI bereitstellen und konfigurieren.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 08/29/2019
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 6993bd10caf2f7e489de8074e311f75710b83d82
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659436"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-cli"></a>Bereitstellen und Konfigurieren von Azure Firewall mithilfe von Azure CLI

Die Steuerung des ausgehenden Netzwerkzugriffs ist ein wichtiger Teil eines umfassenden Netzwerksicherheitsplans. Vielleicht möchten Sie beispielsweise den Zugriff auf Websites einschränken. Mitunter kann es auch empfehlenswert sein, die verfügbaren Ports einzuschränken.

Eine Möglichkeit zur Steuerung des ausgehenden Netzwerkzugriffs aus einem Subnetz ist Azure Firewall. Mit Azure Firewall können Sie Folgendes konfigurieren:

* Anwendungsregeln, die vollqualifizierte Domänennamen (Fully Qualified Domain Names, FQDNs) definieren, auf die von einem Subnetz aus zugegriffen werden kann. Der FQDN kann auch [SQL-Instanzen enthalten](sql-fqdn-filtering.md).
* Netzwerkregeln, die die Quelladresse, das Protokoll, den Zielport und die Zieladresse definieren.

Die konfigurierten Firewallregeln werden auf den Netzwerkdatenverkehr angewendet, wenn Sie Ihren Netzwerkdatenverkehr an die Firewall als Subnetz-Standardgateway weiterleiten.

In diesem Artikel erstellen Sie der Einfachheit halber ein einzelnes vereinfachtes VNET mit drei Subnetzen. Für Produktionsbereitstellungen wird ein [Hub-Spoke-Modell](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) empfohlen. Die Firewall befindet sich dabei in einem eigenen VNET. Die Workloadserver befinden sich in per Peering verknüpften VNETs in derselben Region mit einem oder mehreren Subnetzen.

* **AzureFirewallSubnet:** Das Subnetz mit der Firewall.
* **Workload-SN:** Das Subnetz mit dem Workloadserver. Der Netzwerkdatenverkehr dieses Subnetzes durchläuft die Firewall.
* **Jump-SN:** Das Subnetz mit dem Sprungserver. Der Sprungserver besitzt eine öffentliche IP-Adresse, mit der Sie eine Remotedesktopverbindung herstellen können. Von dort aus können Sie dann über einen weiteren Remotedesktop eine Verbindung mit dem Workloadserver herstellen.

![Netzwerkinfrastruktur des Tutorials](media/tutorial-firewall-rules-portal/Tutorial_network.png)

In diesem Artikel werden folgende Vorgehensweisen behandelt:

* Einrichten einer Netzwerkumgebung zu Testzwecken
* Bereitstellen einer Firewall
* Erstellen einer Standardroute
* Konfigurieren einer Anwendungsregel zum Zulassen des Zugriffs auf www.google.com
* Konfigurieren einer Netzwerkregel, um den Zugriff auf externe DNS-Server zuzulassen
* Testen der Firewall

Dieses Verfahren kann auch mit dem [Azure-Portal](tutorial-firewall-deploy-portal.md) oder mit [Azure PowerShell](deploy-ps.md) durchgearbeitet werden.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Für diesen Artikel ist mindestens Version 2.0.4 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="set-up-the-network"></a>Einrichten des Netzwerks

Erstellen Sie zunächst eine Ressourcengruppe für die Ressourcen, die zum Bereitstellen der Firewall benötigt werden. Erstellen Sie dann ein VNet, Subnetze und Testserver.

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Die Ressourcengruppe enthält alle Ressourcen für die Bereitstellung.

```azurecli-interactive
az group create --name Test-FW-RG --location eastus
```

### <a name="create-a-vnet"></a>Erstellen eines VNET

Dieses virtuelle Netzwerk umfasst drei Subnetze.

> [!NOTE]
> Die Größe des Subnetzes „AzureFirewallSubnet“ beträgt /26. Weitere Informationen zur Subnetzgröße finden Sie unter [Azure Firewall – Häufig gestellte Fragen](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

```azurecli-interactive
az network vnet create \
  --name Test-FW-VN \
  --resource-group Test-FW-RG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name AzureFirewallSubnet \
  --subnet-prefix 10.0.1.0/26
az network vnet subnet create \
  --name Workload-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.2.0/24
az network vnet subnet create \
  --name Jump-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.3.0/24
```

### <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Erstellen Sie nun die virtuellen Sprung- und Workloadcomputer, und platzieren Sie sie in den entsprechenden Subnetzen.
Geben Sie bei entsprechender Aufforderung das Kennwort für den virtuellen Computer ein.

Erstellen Sie den virtuellen Srv-Jump-Computer.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Jump \
    --location eastus \
    --image win2016datacenter \
    --vnet-name Test-FW-VN \
    --subnet Jump-SN \
    --admin-username azureadmin
az vm open-port --port 3389 --resource-group Test-FW-RG --name Srv-Jump
```



Erstellen Sie zum Testen eine Netzwerkkarte für Srv-Work mit spezifischen DNS-Server-IP-Adressen und keiner öffentlichen IP-Adresse.

```azurecli-interactive
az network nic create \
    -g Test-FW-RG \
    -n Srv-Work-NIC \
   --vnet-name Test-FW-VN \
   --subnet Workload-SN \
   --public-ip-address "" \
   --dns-servers 209.244.0.3 209.244.0.4
```

Erstellen Sie jetzt den virtuellen Workloadcomputer.
Geben Sie bei entsprechender Aufforderung das Kennwort für den virtuellen Computer ein.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Work \
    --location eastus \
    --image win2016datacenter \
    --nics Srv-Work-NIC \
    --admin-username azureadmin
```

## <a name="deploy-the-firewall"></a>Bereitstellen der Firewall

Stellen Sie nun die Firewall im virtuellen Netzwerk bereit.

```azurecli-interactive
az network firewall create \
    --name Test-FW01 \
    --resource-group Test-FW-RG \
    --location eastus
az network public-ip create \
    --name fw-pip \
    --resource-group Test-FW-RG \
    --location eastus \
    --allocation-method static \
    --sku standard
az network firewall ip-config create \
    --firewall-name Test-FW01 \
    --name FW-config \
    --public-ip-address fw-pip \
    --resource-group Test-FW-RG \
    --vnet-name Test-FW-VN
az network firewall update \
    --name Test-FW01 \
    --resource-group Test-FW-RG 
az network public-ip show \
    --name fw-pip \
    --resource-group Test-FW-RG
fwprivaddr="$(az network firewall ip-config list -g Test-FW-RG -f Test-FW01 --query "[?name=='FW-config'].privateIpAddress" --output tsv)"
```

Notieren Sie sich die private IP-Adresse. Diese wird später für die Erstellung der Standardroute benötigt.

## <a name="create-a-default-route"></a>Erstellen einer Standardroute

Erstellen einer Tabelle mit deaktivierter BGP-Routenverteilung

```azurecli-interactive
az network route-table create \
    --name Firewall-rt-table \
    --resource-group Test-FW-RG \
    --location eastus \
    --disable-bgp-route-propagation true
```

Erstellen Sie die Route.

```azurecli-interactive
az network route-table route create \
  --resource-group Test-FW-RG \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $fwprivaddr
```

Ordnen Sie die Routingtabelle dem Subnetz zu.

```azurecli-interactive
az network vnet subnet update \
    -n Workload-SN \
    -g Test-FW-RG \
    --vnet-name Test-FW-VN \
    --address-prefixes 10.0.2.0/24 \
    --route-table Firewall-rt-table
```

## <a name="configure-an-application-rule"></a>Konfigurieren einer Anwendungsregel

Die Anwendungsregel lässt ausgehenden Zugriff auf www.google.com zu.

```azurecli-interactive
az network firewall application-rule create \
   --collection-name App-Coll01 \
   --firewall-name Test-FW01 \
   --name Allow-Google \
   --protocols Http=80 Https=443 \
   --resource-group Test-FW-RG \
   --target-fqdns www.google.com \
   --source-addresses 10.0.2.0/24 \
   --priority 200 \
   --action Allow
```

Azure Firewall enthält eine integrierte Regelsammlung für Infrastruktur-FQDNs, die standardmäßig zulässig sind. Diese FQDNs sind plattformspezifisch und können nicht für andere Zwecke verwendet werden. Weitere Informationen finden Sie unter [Infrastruktur-FQDNs](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurieren einer Netzwerkregel

Die Netzwerkregel lässt ausgehenden Zugriff auf zwei IP-Adressen am Port 53 (DNS) zu.

```azurecli-interactive
az network firewall network-rule create \
   --collection-name Net-Coll01 \
   --destination-addresses 209.244.0.3 209.244.0.4 \
   --destination-ports 53 \
   --firewall-name Test-FW01 \
   --name Allow-DNS \
   --protocols UDP \
   --resource-group Test-FW-RG \
   --priority 200 \
   --source-addresses 10.0.2.0/24 \
   --action Allow
```

## <a name="test-the-firewall"></a>Testen der Firewall

Testen Sie nun die Firewall, um sicherzustellen, dass sie wie erwartet funktioniert.

1. Notieren Sie die private IP-Adresse des virtuellen Computers **Srv-Work**:

   ```azurecli-interactive
   az vm list-ip-addresses \
   -g Test-FW-RG \
   -n Srv-Work
   ```

1. Verbinden Sie einen Remotedesktop mit der VM **Srv-Jump**, und melden Sie sich an. Öffnen Sie auf der VM eine Remotedesktopverbindung mit der privaten IP-Adresse von **Srv-Work**, und melden Sie sich an.

3. Öffnen Sie auf **SRV-Work** ein PowerShell-Fenster, und führen Sie die folgenden Befehle aus:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Für beide Befehle sollten Antworten zurückgegeben werden, was zeigt, dass Ihre DNS-Abfragen die Firewall durchqueren.

1. Führen Sie die folgenden Befehle aus:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   Die `www.google.com`-Anforderungen sollten erfolgreich sein, und die `www.microsoft.com`-Anforderungen sollten fehlschlagen. Dies zeigt, dass Ihre Firewall-Regeln wie erwartet funktionieren.

Damit haben Sie sich vergewissert, dass die Firewallregeln funktionieren:

* Sie können DNS-Namen mithilfe des konfigurierten externen DNS-Servers auflösen.
* Sie können zum einzigen zulässigen FQDN navigieren, aber nicht zu anderen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können die Firewallressourcen für das nächste Tutorial behalten oder die Ressourcengruppe **Test-FW-RG** löschen, wenn Sie sie nicht mehr benötigen. Dadurch werden alle firewallbezogenen Ressourcen gelöscht:

```azurecli-interactive
az group delete \
  -n Test-FW-RG
```

## <a name="next-steps"></a>Nächste Schritte

* [Tutorial: Überwachen von Azure Firewall-Protokollen](./firewall-diagnostics.md)