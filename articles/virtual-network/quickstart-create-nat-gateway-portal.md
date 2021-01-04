---
title: 'Tutorial: Erstellen eines NAT-Gateways: Azure-Portal'
titlesuffix: Azure Virtual Network NAT
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie ein NAT-Gateway mithilfe des Azure-Portals erstellen.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 5010dc08b695a0376ace5dde935c63caf0c39633
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012073"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>Tutorial: Erstellen eines NAT-Gateways mithilfe des Azure-Portals

In diesem Tutorial erfahren Sie, wie Sie den Azure Virtual Network NAT-Dienst verwenden. Sie erstellen ein NAT-Gateway, um ausgehende Konnektivität für einen virtuellen Computer in Azure bereitzustellen. 

Falls Sie es vorziehen, können Sie diese Schritte anstatt mit dem Portal auch per [Azure CLI](quickstart-create-nat-gateway-cli.md), [Azure PowerShell](quickstart-create-nat-gateway-powershell.md) oder Bereitstellung einer [ARM-Vorlage](quickstart-create-nat-gateway-powershell.md) ausführen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

## <a name="virtual-network-and-parameters"></a>Virtuelles Netzwerk und Parameter

Sie müssen zunächst die Ressourcengruppe und das virtuelle Netzwerk erstellen, damit Sie einen virtuellen Computer bereitstellen und das NAT-Gateway verwenden können.

In den Schritten dieses Abschnitts müssen die folgenden Parameter wie folgt ersetzt werden:

| Parameter                   | Wert                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | USA (Ost) 2      |
| **\<IPv4-address-space>**   | 192.168.0.0\16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 192.168.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-vm-to-use-the-nat-gateway"></a>Erstellen eines virtuellen Computers zur Verwendung des NAT-Gateways

Sie erstellen nun einen virtuellen Computer zur Verwendung des NAT-Diensts. Dieser virtuelle Computer verfügt über eine öffentliche IP-Adresse, die als öffentliche IP auf Instanzebene verwendet wird, um den Zugriff auf den virtuellen Computer zu ermöglichen. Der NAT-Dienst erkennt die Flussrichtung und ersetzt in Ihrem Subnetz das Standardziel im Internet. Die öffentliche IP-Adresse des virtuellen Computers wird nicht für ausgehende Verbindungen verwendet.

1. Wählen Sie links oben im Portal **Ressource erstellen** > **Compute** > **Ubuntu Server 18.04 LTS** aus, oder suchen Sie über die Marketplace-Suche nach **Ubuntu Server 18.04 LTS**.

2. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Werte ein (bzw. wählen Sie sie aus):
   - **Abonnement** > **Ressourcengruppe**: Wählen Sie **myResourceGroupNAT** aus.
   - **Instanzendetails** > **Name des virtuellen Computers**: Geben Sie **myVM** ein.
   - **Instanzendetails** > **Region**: Wählen Sie **USA, Osten 2** aus.
   - **Administratorkonto** > **Authentifizierungstyp**: Wählen Sie **Kennwort** aus.
   - **Administratorkonto**: Geben Sie Informationen für **Benutzername**, **Kennwort** und **Kennwort bestätigen** ein.
   - **Regeln für eingehende Ports** > **Öffentliche Eingangsports**: Wählen Sie **Ausgewählte Ports zulassen** aus.
   - **Regeln für eingehende Ports** > **Eingangsports auswählen**: Wählen Sie **SSH (22)** aus.
   - Wählen Sie die Registerkarte **Netzwerk** aus, oder wählen Sie **Weiter: Datenträger** und anschließend **Weiter: Netzwerk** aus.

3. Stellen Sie auf der Registerkarte **Netzwerk** sicher, dass Folgendes ausgewählt ist:
   - **Virtuelles Netzwerk:** **myVnet**
   - **Subnetz**: **mySubnet**
   - **Öffentliche IP-Adresse**: Wählen Sie **Neu erstellen** aus.  Geben Sie im Fenster **Öffentliche IP-Adresse erstellen** im Feld **Name** den Namen **myPublicIPVM** ein, und wählen Sie unter **SKU** die Option **Standard** aus.  Klicken Sie auf **OK**.
   - **NIC-Netzwerksicherheitsgruppe:** Wählen Sie **Basic** aus.
   - **Öffentliche Eingangsports**: Wählen Sie **Ausgewählte Ports zulassen** aus.
   - **Eingangsports auswählen**: Vergewissern Sie sich, dass **SSH** ausgewählt ist.

4. Legen Sie auf der Registerkarte **Verwaltung** unter **Überwachung** die Option **Startdiagnose** auf **Aus** fest.

5. Klicken Sie auf **Überprüfen + erstellen**. 

6. Überprüfen Sie die Einstellungen, und klicken Sie auf **Erstellen**.

## <a name="create-the-nat-gateway"></a>Erstellen des NAT-Gateways

Sie können mehrere öffentliche IP-Adressressourcen und/oder Präfixes für öffentliche IP-Adressen verwenden. Sie fügen eine öffentliche IP-Ressource, ein Präfix für öffentliche IP-Adressen und eine NAT-Gatewayressource hinzu.

In diesem Abschnitt erfahren Sie, wie Sie mit der NAT-Gatewayressource die folgenden Komponenten des NAT-Diensts erstellen und konfigurieren:
  - Öffentlicher IP-Adresspool und Präfix für öffentliche IP-Adressen für ausgehende Datenflüsse, die von der NAT-Gatewayressource übersetzt werden
  - Ändern des Leerlauftimeouts von vier Minuten (Standardwert) in zehn Minuten

### <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse

1. Wählen Sie links oben im Portal **Ressource erstellen** > **Netzwerk** > **Öffentliche IP-Adresse** aus, oder suchen Sie über die Marketplace-Suche nach **Öffentliche IP-Adresse**.

2. Geben Sie unter **Öffentliche IP-Adresse erstellen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung | Wert |
    | ------- | ----- |
    | IP-Version | Wählen Sie **IPv4** aus.
    | SKU | Wählen Sie **Standard** aus.
    | Name | Geben Sie **myPublicIP** ein. |
    | Subscription | Wählen Sie Ihr Abonnement aus.|
    | Resource group | Wählen Sie **myResourceGroupNAT** aus. |
    | Standort | Wählen Sie **USA, Osten 2** aus.|

3. Übernehmen Sie die übrigen Standardeinstellungen, und wählen Sie **Erstellen** aus.

### <a name="create-a-public-ip-prefix"></a>Erstellen des Präfixes für öffentliche IP-Adressen

1. Wählen Sie links oben im Portal **Ressource erstellen** > **Netzwerk** > **Präfix für öffentliche IP-Adresse** aus, oder suchen Sie über die Marketplace-Suche nach **Präfix für öffentliche IP-Adresse**. 

2. Geben Sie auf der Registerkarte **Grundlagen** unter **Präfix für öffentliche IP-Adresse erstellen** die folgenden Werte ein, oder wählen Sie sie aus:
   - **Abonnement** > **Ressourcengruppe**: Wählen Sie **myResourceGroupNAT**> aus.
   - **Instanzendetails** > **Name**: Geben Sie **myPublicIPprefix** ein.
   - **Instanzendetails** > **Region**: Wählen Sie **USA, Osten 2** aus.
   - **Instanzendetails** > **Präfixgröße**: Wählen Sie **/31 (2 addresses)** (/31 (2 Adressen)) aus.

3. Übernehmen Sie die übrigen Standardeinstellungen, und wählen Sie **Überprüfen + erstellen** aus.

4. Überprüfen Sie die Einstellungen, und wählen Sie dann die Option **Erstellen**.
   

### <a name="create-a-nat-gateway-resource"></a>Erstellen einer NAT-Gatewayressource

1. Wählen Sie links oben im Portal **Ressource erstellen** > **Netzwerk** > **NAT-Gateway** aus, oder suchen Sie über die Marketplace-Suche nach **NAT-Gateway**.

2. Geben Sie auf der Registerkarte **Grundlagen** unter **Gateway für die Netzwerkadressübersetzung (NAT) erstellen** die folgenden Werte ein, oder wählen Sie sie aus:
   - **Abonnement** > **Ressourcengruppe**: Wählen Sie **myResourceGroupNAT** aus.
   - **Instanzendetails** > **Name des NAT-Gateways**: Geben Sie **myNATgateway** ein.
   - **Instanzendetails** > **Region**: Wählen Sie **USA, Osten 2** aus.
   - **Instanzendetails** > **Leerlauftimeout (Minuten)** : Geben Sie **10** ein.
   - Wählen Sie die Registerkarte **Öffentliche IP-Adresse** oder **Weiter: Öffentliche IP-Adresse** aus.

3. Geben Sie auf der Registerkarte **Öffentliche IP-Adresse** die folgenden Werte ein, oder wählen Sie sie aus:
   - **Öffentliche IP-Adressen**: Wählen Sie **myPublicIP** aus.
   - **Präfixe für öffentliche IP-Adressen**: Wählen Sie **myPublicIPprefix** aus.
   - Wählen Sie die Registerkarte **Subnetz** oder **Weiter: Subnetz** aus.

4. Geben Sie auf der Registerkarte **Subnetz** die folgenden Werte ein, oder wählen Sie sie aus:
   - **Virtuelles Netzwerk**: Wählen Sie **myResourceGroupNAT** > **myVnet** aus.
   - **Subnetzname**: Aktivieren Sie das Kästchen neben **mySubnet**.

5. Klicken Sie auf **Überprüfen + erstellen**.

6. Überprüfen Sie die Einstellungen, und wählen Sie dann die Option **Erstellen**.

## <a name="discover-the-ip-address-of-the-vm"></a>Ermitteln der IP-Adresse des virtuellen Computers

1. Wählen Sie im Portal auf der linken Seite **Ressourcengruppen** aus.
2. Wählen Sie **myResourceGroupNAT** aus.
3. Wählen Sie **myVM** aus.
4. Kopieren Sie unter **Übersicht** den Wert unter **Öffentliche IP-Adresse**, und fügen Sie ihn in einen Editor ein, damit Sie ihn für den Zugriff auf den virtuellen Computer verwenden können.

>[!IMPORTANT]
>Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in einen Editor ein, damit Sie sie für den Zugriff auf den virtuellen Computer verwenden können.

## <a name="sign-in-to-vm"></a>Anmelden beim virtuellen Computer

Öffnen Sie [Azure Cloud Shell](https://shell.azure.com) im Browser. Verwenden Sie die im vorherigen Schritt abgerufene IP-Adresse, um eine SSH-Verbindung mit dem virtuellen Computer herzustellen.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Sie können den NAT-Dienst nun verwenden.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, das NAT-Gateway und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. Wählen Sie die Ressourcengruppe **myResourceGroupNAT** aus, die das NAT-Gateway enthält, und wählen Sie anschließend **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie ein NAT-Gateway und einen virtuellen Computer für seine Verwendung erstellt. 

Sehen Sie sich die Metriken in Azure Monitor an, um Informationen zum Betrieb Ihres NAT-Diensts anzuzeigen. Diagnostizieren Sie Probleme wie die Ressourcenüberlastung von verfügbaren SNAT-Ports.  Die Ressourcenüberlastung von SNAT-Ports wird durch Hinzufügen zusätzlicher Ressourcen für öffentliche IP-Adressen und/oder zusätzlicher Präfixressourcen für öffentliche IP-Adressen behoben.


- Erfahren Sie mehr über [Azure Virtual Network NAT](./nat-overview.md).
- Informieren Sie sich über [NAT-Gatewayressourcen](./nat-gateway-resource.md).
- Schnellstart zur Bereitstellung einer [NAT-Gatewayressource mithilfe der Azure CLI](./quickstart-create-nat-gateway-cli.md)
- Schnellstart zur Bereitstellung einer [NAT-Gatewayressource mithilfe von Azure PowerShell](./quickstart-create-nat-gateway-powershell.md)
- Schnellstart zur Bereitstellung einer [NAT-Gatewayressource mithilfe des Azure-Portals](./quickstart-create-nat-gateway-portal.md)
> [!div class="nextstepaction"]

