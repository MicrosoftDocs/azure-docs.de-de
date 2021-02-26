---
title: Erstellen und Aktivieren eines Azure DDoS Protection-Plans mit einer Azure Resource Manager-Vorlage (ARM-Vorlage).
description: Es wird beschrieben, wie Sie einen Azure DDoS Protection-Plan mit einer Azure Resource Manager-Vorlage (ARM-Vorlage) erstellen und aktivieren.
services: ddos-protection
documentationcenter: na
author: mumian
ms.service: ddos-protection
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.custom: subject-armqs
ms.author: jgao
ms.date: 01/14/2021
ms.openlocfilehash: 75d6c484a0f1d6325aaa7894d8902ff78cadbd74
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092499"
---
# <a name="quickstart-create-an-azure-ddos-protection-standard-using-arm-template"></a>Schnellstart: Erstellen eines Azure DDoS Protection-Standard-Plans per ARM-Vorlage

In dieser Schnellstartanleitung wird beschrieben, wie Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) verwenden, um einen Plan als Schutz vor DDoS-Angriffen (Distributed Denial of Service) und ein virtuelles Netzwerk (VNET) erstellen und den Schutzplan dann für das VNET aktivieren. Mit einem Azure DDoS Protection-Standard-Plan wird abonnementübergreifend eine Reihe von virtuellen Netzwerken definiert, für die der DDoS-Schutz aktiviert ist. Sie können einen DDoS-Schutzplan für Ihre Organisation konfigurieren und virtuelle Netzwerke über verschiedene Abonnements hinweg mit demselben Plan verknüpfen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-create-and-enable-ddos-protection-plans).

:::code language="json" source="~/quickstart-templates/101-create-and-enable-ddos-protection-plans/azuredeploy.json":::

Die Vorlage definiert zwei Ressourcen:

- [Microsoft.Network/ddosProtectionPlans](/azure/templates/microsoft.network/ddosprotectionplans)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

In diesem Beispiel werden mit der Vorlage eine neue Ressourcengruppe, ein DDoS-Schutzplan und ein VNET erstellt.

1. Wählen Sie die Schaltfläche **In Azure bereitstellen** aus, um sich bei Azure anzumelden und die Vorlage zu öffnen.

    [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

1. Geben Sie die Werte für die Erstellung einer neuen Ressourcengruppe, eines DDoS-Schutzplans und eines VNET-Namens ein.

    :::image type="content" source="media/manage-ddos-protection-template/ddos-template.png" alt-text="DDoS-Schnellstartvorlage":::

    - **Abonnement**: Der Name des Azure-Abonnements, unter dem die Ressourcen bereitgestellt werden.
    - **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue Ressourcengruppe.
    - **Region**: Die Region, in der die Ressourcengruppe bereitgestellt wird, z. B. „USA, Osten“.
    - **Name des DDoS-Schutzplans**: Der Name des neuen DDoS-Schutzplans.
    - **Name des virtuellen Netzwerks**: Dient zum Erstellen eines Namens für das neue VNET.
    - **Standort**: Bei dieser Funktion wird die Region der Ressourcengruppe zum Bereitstellen von Ressourcen genutzt.
    - **VNET-Adresspräfix**: Verwenden Sie den Standardwert, oder geben Sie Ihre VNET-Adresse ein.
    - **Subnetzpräfix**: Verwenden Sie den Standardwert, oder geben Sie Ihr VNET-Subnetz ein.
    - **DDoS-Schutzplan aktiviert**: Die Standardeinstellung ist `true`, um den DDoS-Schutzplan zu aktivieren.

1. Klicken Sie auf **Überprüfen + erstellen**.
1. Vergewissern Sie sich, dass die Überprüfung der Vorlage erfolgreich war, und wählen Sie **Erstellen** aus, um mit der Bereitstellung zu starten.

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Wählen Sie die Schaltfläche **Kopieren** aus, um den Azure CLI- oder Azure PowerShell-Befehl zu kopieren. Mit der Schaltfläche **Jetzt testen** wird Azure Cloud Shell geöffnet, um den Befehl ausführen zu können.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli-interactive
az network ddos-protection show \
  --resource-group MyResourceGroup \
  --name MyDdosProtectionPlan
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName 'MyResourceGroup' -Name 'MyDdosProtectionPlan'
```

---

In der Ausgabe werden die neuen Ressourcen angezeigt.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```Output
{
  "etag": "W/\"abcdefgh-1111-2222-bbbb-987654321098\"",
  "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan",
  "location": "eastus",
  "name": "MyDdosProtectionPlan",
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "resourceGuid": null,
  "tags": null,
  "type": "Microsoft.Network/ddosProtectionPlans",
  "virtualNetworks": [
    {
      "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : MyDdosProtectionPlan
Id                : /subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan
Etag              : W/"abcdefgh-1111-2222-bbbb-987654321098"
ProvisioningState : Succeeded
VirtualNetworks   : [
                      {
                        "Id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet"
                      }
                    ]
```

---

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nach Abschluss des Vorgangs können Sie die Ressourcen löschen. Der Befehl löscht die Ressourcengruppe und alle darin enthaltenen Ressourcen.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli-interactive
az group delete --name MyResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'MyResourceGroup'
```

---

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Anzeigen und Konfigurieren von Telemetriedaten für Ihren DDoS-Schutzplan, finden Sie in den Tutorials.

> [!div class="nextstepaction"]
> [Anzeigen und Konfigurieren der DDoS Protection-Telemetrie](telemetry.md)
