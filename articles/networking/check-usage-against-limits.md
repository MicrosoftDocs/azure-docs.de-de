---
title: Vergleichen der Azure-Ressourcennutzung mit Grenzwerten | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die Azure-Ressourcennutzung mit den Grenzwerten eines Azure-Abonnements vergleichen.
services: networking
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/05/2018
ms.author: jdial
ms.openlocfilehash: 54050c4c20b6ebb35f198775448f51ee8cdc533b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60425407"
---
# <a name="check-resource-usage-against-limits"></a>Vergleichen der Ressourcennutzung mit Grenzwerten

In diesem Artikel erfahren Sie, wie Sie die Anzahl von Ressourcen für jeden im Abonnement bereitgestellten Netzwerkressourcentyp und die [Abonnementgrenzen](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) anzeigen. Die Möglichkeit, die Ressourcennutzung mit Grenzwerten zu vergleichen, hilft beim Nachverfolgung der aktuellen und Planen der zukünftigen Nutzung. Zum Nachverfolgen der Nutzung können Sie das [Azure-Portal](#azure-portal), [PowerShell](#powershell) oder die [Azure CLI](#azure-cli) verwenden.

## <a name="azure-portal"></a>Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus.
3. Geben Sie im Feld **Filter** den Text *Abonnements* ein. Wenn **Abonnements** in den Suchergebnissen angezeigt wird, wählen Sie diesen Eintrag aus.
4. Wählen Sie den Namen des Abonnements aus, für das Sie die Nutzungsinformationen anzeigen möchten.
5. Wählen Sie unter **EINSTELLUNGEN** die Option **Nutzung + Kontingente** aus.
6. Sie können die folgenden Optionen auswählen:
   - **Ressourcentypen**: Sie können alle Ressourcentypen auswählen oder nur diejenigen, die Sie anzeigen möchten.
   - **Anbieter**: Sie können alle Ressourcenanbieter oder **Compute**, **Netzwerk** oder **Speicher** auswählen.
   - **Standorte**: Sie können alle Azure-Standorte oder nur bestimmte Standorte auswählen.
   - Sie können alle Ressourcen oder nur diejenigen Ressourcen anzeigen, von denen mindestens eine bereitgestellt ist.

     Das Beispiel in der folgenden Abbildung zeigt alle Netzwerkressourcen mit mindestens einer in der Region „USA, Osten“ bereitgestellten Ressource:

       ![Anzeigen von Verbrauchsdaten](./media/check-usage-against-limits/view-usage.png)

     Sie können die Spalten sortieren, indem Sie auf die Spaltenüberschrift klicken. Die gezeigten Grenzwerte sind die Grenzwerte für Ihr Abonnement. Wenn Sie den Standardgrenzwert erhöhen müssen, wählen Sie **Erhöhung anfordern** aus, geben Sie die erforderlichen Informationen in der Anforderung an, und übermitteln Sie diese. Für alle Ressourcen gilt ein maximaler Grenzwert, der unter Azure-[Grenzwerte](../azure-subscription-service-limits.md?toc=%2fazure%2fnetworking%2ftoc.json#networking-limits) aufgeführt ist. Wenn Ihr aktueller Grenzwert bereits der Maximalwert ist, kann der Grenzwert nicht erhöht werden.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Sie können die nachfolgenden Befehle in [Azure Cloud Shell](https://shell.azure.com/powershell) oder über PowerShell auf Ihrem Computer ausführen. Azure Cloud Shell ist eine kostenlose interaktive Shell. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. Wenn Sie PowerShell auf Ihrem Computer ausführen, müssen Sie das Azure PowerShell-Modul Version 1.0.0 oder höher ausführen. Führen Sie `Get-Module -ListAvailable Az` auf Ihrem Computer aus, um nach der installierten Version zu suchen. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Login-AzAccount` ausführen, um sich bei Azure anzumelden.

Zeigen Sie mit [Get-AzNetworkUsage](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkusage) die Nutzung Ihrer Ressourcen im Vergleich mit den Grenzwerten an. Das folgende Beispiel ruft die Nutzung für diejenigen Ressourcen ab, von denen mindestens eine in „USA, Osten“ bereitgestellt ist:

```azurepowershell-interactive
Get-AzNetworkUsage `
  -Location eastus `
  | Where-Object {$_.CurrentValue -gt 0} `
  | Format-Table ResourceType, CurrentValue, Limit
```

Die Ausgabe ist wie im folgenden Beispiel formatiert:

```powershell
ResourceType            CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
```

## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Wenn Sie Befehle der Azure-Befehlszeilenschnittstelle (CLI) zum Durchführen von Aufgaben in diesem Artikel verwenden, führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/bash) oder durch Ausführen der CLI auf Ihrem Computer aus. Für diesen Artikel ist die Azure CLI-Version 2.0.32 oder höher erforderlich. Führen Sie `az --version` aus, um die installierte Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli). Wenn Sie die Azure CLI lokal ausführen, müssen Sie auch `az login` ausführen, um sich bei Azure anzumelden.

Zeigen Sie mit [az network list-usages](/cli/azure/network?view=azure-cli-latest#az-network-list-usages) die Nutzung Ihrer Ressourcen im Vergleich mit den Grenzwerten an. Das folgende Beispiel ruft die Nutzung der Ressourcen in „USA, Osten“ ab:

```azurecli-interactive
az network list-usages \
  --location eastus \
  --out table
```

Die Ausgabe ist wie im folgenden Beispiel formatiert:

```azurecli
Name                    CurrentValue Limit
------------            ------------ -----
Virtual Networks                   1    50
Network Security Groups            2   100
Public IP Addresses                1    60
Network Interfaces                 1 24000
Network Watchers                   1     1
Load Balancers                     0   100
Application Gateways               0    50
```
