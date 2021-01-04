---
title: Verwalten von virtuellen Netzwerken mithilfe der Azure CLI für Azure Database for MySQL – Flexible Server
description: Erstellen und Verwalten von virtuellen Netzwerken für Azure Database for MySQL – Flexible Server über die Azure CLI
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 70cb1297c4b47f22f9eb5cc6992e6fcd6c58b364
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545037"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-mysql---flexible-server-using-the-azure-cli"></a>Erstellen und Verwalten von virtuellen Netzwerken für Azure Database for MySQL – Flexible Server über die Azure CLI

> [!IMPORTANT]
> Azure Database for MySQL Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

Azure Database for MySQL Flexible Server unterstützt zwei Arten von sich gegenseitig ausschließenden Netzwerkverbindungsmethoden, mit denen eine Verbindung mit Ihrem flexiblen Server hergestellt werden kann. Die zwei Optionen sind:

- Öffentlicher Zugriff (zugelassene IP-Adressen)
- Privater Zugriff (VNET-Integration)

In diesem Artikel wird die Erstellung von MySQL-Servern mit der Option **Privater Zugriff (VNET-Integration)** über die Azure CLI behandelt. Mit *Privater Zugriff (VNET-Integration)* können Sie Ihre Flexible Server-Instanz in Ihrer eigenen [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)-Instanz bereitstellen. Azure Virtual Network-Instanzen ermöglichen eine private und sichere Netzwerkkommunikation. Bei privatem Zugriff sind die Verbindungen mit dem MySQL-Server auf Ihr virtuelles Netzwerk beschränkt. Weitere Informationen hierzu finden Sie unter [Privater Zugriff (VNET-Integration)](./concepts-networking.md#private-access-vnet-integration).

In Azure Database for MySQL Flexible Server können Sie den Server nur während der Erstellung des Servers in einem virtuellen Netzwerk und einem Subnetz bereitstellen. Nachdem die Flexible Server-Instanz in einem virtuellen Netzwerk und einem Subnetz bereitgestellt wurde, können Sie sie nicht in ein anderes virtuelles Netzwerk oder Subnetz verschieben oder auf *Öffentlicher Zugriff (zugelassene IP-Adressen)* umstellen.

## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel durchführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert.

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren** . Sie können Cloud Shell auch auf einer separaten Browserregisterkarte öffnen, indem Sie zu [https://shell.azure.com/bash](https://shell.azure.com/bash) navigieren. Wählen Sie **Kopieren** aus, um die Codeblöcke zu kopieren. Fügen Sie die Blöcke anschließend in Cloud Shell ein, und wählen Sie **Eingabe** , um sie auszuführen.

Wenn Sie es vorziehen, die CLI lokal zu installieren und zu verwenden, müssen Sie für diesen Schnellstart mindestens Version 2.0 der Azure CLI verwenden. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen sich mit dem Befehl [az login](/cli/azure/reference-index#az-login) bei Ihrem Konto anmelden. Beachten Sie die Eigenschaft **ID** , die auf die **Abonnement-ID** für Ihr Azure-Konto verweist.

```azurecli-interactive
az login
```

Wählen Sie mithilfe des Befehls [az account set](/cli/azure/account#az-account-set) das Abonnement unter Ihrem Konto aus. Notieren Sie sich aus der Ausgabe von **az login** den Wert für **ID** . Sie verwenden ihn im Befehl als Wert für das Argument **subscription** . Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem die Ressource fakturiert sein sollte. Verwenden Sie [az account list](/cli/azure/account#az-account-list), um alle Abonnements abzurufen.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-azure-database-for-mysql-flexible-server-using-cli"></a>Erstellen einer Azure Database for MySQL Flexible Server-Instanz unter Verwendung der Befehlszeilenschnittstelle
Sie können den `az mysql flexible-server`-Befehl verwenden, um die Flexible Server-Instanz mit *Privater Zugriff (VNET-Integration)* zu erstellen. Dieser Befehl verwendet „Privater Zugriff (VNET-Integration)“ als Standardverbindungsmethode. Es werden ein virtuelles Netzwerk und ein Subnetz für Sie erstellt, falls noch nicht bereitgestellt. Sie können auch das bereits vorhandene virtuelle Netzwerk und das Subnetz unter Verwendung der Subnetz-ID bereitstellen. <!-- You can provide the **vnet**,**subnet**,**vnet-address-prefix** or**subnet-address-prefix** to customize the virtual network and subnet.--> Wie in den folgenden Beispielen gezeigt, gibt es verschiedene Optionen zum Erstellen einer Flexible Server-Instanz unter Verwendung der Befehlszeilenschnittstelle.

>[!Important]
> Mit diesem Befehl wird das Subnetz an **Microsoft.DBforMySQL/flexibleServers** delegiert. Diese Delegierung bedeutet, dass dieses Subnetz nur von Azure Database for MySQL Flexible Servers genutzt werden kann. Im delegierten Subnetz können sich keine anderen Azure-Ressourcentypen befinden.
>

Die vollständige Liste von konfigurierbaren CLI-Parametern finden Sie in der [Referenzdokumentation](/cli/azure/mysql/flexible-server) zur Azure CLI. In den folgenden Befehlen können Sie beispielsweise optional die Ressourcengruppe festlegen.

- Erstellen einer Flexible Server-Instanz mithilfe des virtuellen Standardnetzwerks/-subnetzes mit dem Standard Adresspräfix
    ```azurecli-interactive
    az mysql flexible-server create
    ```
<!--- Create a flexible server using already existing virtual network and subnet
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --subnet mySubnet
    ```-->
- Erstellen einer Flexible Server-Instanz unter Verwendung eines bereits vorhandenen virtuellen Netzwerks/Subnetzes und der Subnetz-ID. Das bereitgestellte Subnetz sollte keine anderen Ressourcen enthalten, und dieses Subnetz wird an **Microsoft.DBforMySQL/flexibleServers** delegiert, falls nicht bereits delegiert.
    ```azurecli-interactive
    az mysql flexible-server create --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNetName}/subnets/{SubnetName}
    ```
    > [!Note]
    > Das virtuelle Netzwerk und das Subnetz sollten sich in derselben Region und demselben Abonnement befinden wie Ihre Flexible Server-Instanz.
<!--
- Create a flexible server using new virtual network, subnet with non-default address prefix
    ```azurecli-interactive
    az mysql flexible-server create --vnet myVnet --vnet-address-prefix 10.0.0.0/24 --subnet mySubnet --subnet-address-prefix 10.0.0.0/24
    ```-->
Die vollständige Liste von konfigurierbaren CLI-Parametern finden Sie in der [Referenzdokumentation](/cli/azure/mysql/flexible-server) zur Azure CLI.


## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [Netzwerke in Azure Database for MySQL Flexible Server](./concepts-networking.md).
- [Erstellen und Verwalten von virtuellen Netzwerken für Azure Database for MySQL Flexible Server mit dem Azure-Portal](./how-to-manage-virtual-network-portal.md).
- Erfahren Sie mehr über [Virtuelle Netzwerke für Azure Database for MySQL Flexible Server](./concepts-networking.md#private-access-vnet-integration).