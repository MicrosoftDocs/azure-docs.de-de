---
title: Erstellen von IP-Adressgruppen in Azure Firewall
description: IP-Adressgruppen ermöglichen Ihnen das Gruppieren und Verwalten von IP-Adressen für Azure Firewall-Regeln.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c53b35351108717c7b597e052a66e9902be5ec6c
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394525"
---
# <a name="create-ip-groups"></a>Erstellen von IP-Adressgruppen

IP-Adressgruppen ermöglichen Ihnen das Gruppieren und Verwalten von IP-Adressen für Azure Firewall-Regeln. Sie können eine einzelne IP-Adresse, mehrere IP-Adressen oder aber einen oder mehrere IP-Adressbereiche enthalten.

## <a name="create-an-ip-group---azure-portal"></a>Erstellen einer IP-Adressgruppe – Azure-Portal

So erstellen Sie eine IP-Adressgruppe über das Azure-Portal

1. Wählen Sie auf der Startseite des Azure-Portals **Ressource erstellen** aus.
1. Geben Sie im Suchfeld **IP-Adressgruppen** ein, und wählen Sie dann **IP-Adressgruppen** aus.
1. Klicken Sie auf **Erstellen**.
1. Wählen Sie Ihr Abonnement aus.
1. Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue.
1. Geben Sie einen eindeutigen Namen für Ihre IP-Adressgruppe ein, und wählen Sie eine Region aus.
1. Klicken Sie auf **Weiter: IP-Adressen**.
1. Geben Sie eine IP-Adresse, mehrere IP-Adressen oder IP-Adressbereiche ein.

   Es gibt zwei Möglichkeiten zum Eingeben von IP-Adressen:
   - Sie können sie manuell eingeben.
   - Sie können sie aus einer Datei importieren.

   Zum Importieren aus einer Datei wählen Sie **Aus Datei importieren** aus. Sie können entweder Ihre Datei in das Feld ziehen oder **Nach Dateien suchen** auswählen. Falls erforderlich, können Sie Ihre hochgeladenen IP-Adressen überprüfen und bearbeiten.

   Wenn Sie eine IP-Adresse eingeben, wird sie vom Portal im Hinblick auf Überlappung, Duplikate und Formatierungsprobleme überprüft.

1. Nach Abschluss dieses Vorgangs wählen Sie **Bewerten + erstellen** aus.
1. Klicken Sie auf **Erstellen**.

## <a name="create-an-ip-group---azure-powershell"></a>Erstellen einer IP-Adressgruppe – Azure PowerShell

In diesem Beispiel wird eine IP-Adressgruppe mit einem Adresspräfix und einer IP-Adresse mithilfe von Azure PowerShell erstellt:

```azurepowershell
$ipGroup = @{
    Name              = 'ipGroup'
    ResourceGroupName = 'ipGroupRG'
    Location          = 'West US'
    IpAddress         = @('10.0.0.0/24', '192.168.1.10') 
}

New-AzIpGroup @ipGroup
```

## <a name="create-an-ip-group---azure-cli"></a>Erstellen einer IP-Adressgruppe – Azure-Befehlszeilenschnittstelle

In diesem Beispiel wird eine IP-Adressgruppe mit einem Adresspräfix und einer IP-Adresse mithilfe der Azure-Befehlszeilenschnittstelle erstellt:

```azurecli-interactive
az network ip-group create \
    --name ipGroup \ 
    --resource-group ipGroupRG \
    --location westus \
    --ip-addresses '10.0.0.0/24' '192.168.1.10'
```

## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen zu IP-Adressgruppen](ip-groups.md)
