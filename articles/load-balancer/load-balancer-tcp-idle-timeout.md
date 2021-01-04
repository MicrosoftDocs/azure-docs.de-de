---
title: Konfigurieren der TCP-Zurücksetzung und des Leerlauftimeouts für Load Balancer
titleSuffix: Azure Load Balancer
description: In diesem Artikel erfahren Sie, wie die TCP-Zurücksetzung und das Leerlauftimeout für Azure Load Balancer konfiguriert werden.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2020
ms.author: allensu
ms.openlocfilehash: 8a6be588544883b77c3ff115c9dba5e6ecd5fbd7
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747203"
---
# <a name="configure-tcp-reset-and-idle-timeout-for-azure-load-balancer"></a>Konfigurieren der TCP-Zurücksetzung und des Leerlauftimeouts für Azure Load Balancer

Azure Load Balancer weist den folgenden Leerlauftimeoutbereich auf:

* 4 Minuten bis 100 Minuten für Ausgangsregeln
* 4 Minuten bis 30 Minuten für Load Balancer-Regeln und NAT-Regeln für eingehenden Datenverkehr

Standardmäßig ist der Wert auf 4 Minuten festgelegt. Wenn ein Inaktivitätszeitraum den Timeoutwert überschreitet, gibt es keine Garantie dafür, dass die TCP- oder HTTP-Sitzung zwischen dem Client und Ihrem Dienst aufrechterhalten wird. 

In den folgenden Abschnitten wird beschrieben, wie Sie die Einstellungen für Leerlauftimeout und TCP-Zurücksetzung für Load Balancer-Ressourcen ändern.

## <a name="set-tcp-reset-and-idle-timeout"></a>Festlegen der TCP-Zurücksetzung und des Leerlauftimeouts
---
# <a name="portal"></a>[**Portal**](#tab/tcp-reset-idle-portal)

Um das Leerlauftimeout und die TCP-Zurücksetzung für eine Load Balancer-Ressource festzulegen, bearbeiten Sie die Load Balancer-Regel. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie im linken Menü die Option **Ressourcengruppen** aus.

3. Wählen Sie die Ressourcengruppe für Ihre Load Balancer-Ressource aus. In diesem Beispiel heißt die Ressourcengruppe **myResourceGroup**.

4. Wählen Sie Ihren Load Balancer aus. In diesem Beispiel heißt die Ressourcengruppe **myLoadBalancer**.

5. Wählen Sie unter **Einstellungen** die Option **Lastenausgleichsregeln** aus.

     :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules.png" alt-text="Bearbeiten von Lastenausgleichsregeln" border="true":::

6. Wählen Sie Ihre Lastenausgleichsregel aus. In diesem Beispiel heißt die Lastenausgleichsregel **myLBrule**.

7. Verschieben Sie in der Lastenausgleichsregel den Schieberegler **Leerlauftimeout (Minuten)** zum gewünschten Timeoutwert.  

8. Wählen Sie unter **TCP-Zurücksetzung** die Option **Aktiviert** aus.

   :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules-tcp-reset.png" alt-text="Festlegen von Leerlauftimeout und TCP-Zurücksetzung" border="true":::

9. Wählen Sie **Speichern** aus.

# <a name="powershell"></a>[**PowerShell**](#tab/tcp-reset-idle-powershell)

Zum Festlegen von Leerlauftimeout und TCP-Zurücksetzung geben Sie mithilfe von [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer) Werte für die folgenden Parameter für Lastenausgleichsregeln an:

* **IdleTimeoutInMinutes**
* **EnableTcpReset**

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 5.4.1 des Azure PowerShell-Moduls verwenden. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

Ersetzen Sie die folgenden Beispiele durch die Werte aus Ihren Ressourcen:

* **myResourceGroup**
* **myLoadBalancer**

```azurepowershell
$lb = Get-AzLoadBalancer -Name "myLoadBalancer" -ResourceGroup "myResourceGroup"
$lb.LoadBalancingRules[0].IdleTimeoutInMinutes = '15'
$lb.LoadBalancingRules[0].EnableTcpReset = 'true'
Set-AzLoadBalancer -LoadBalancer $lb
```

# <a name="azure-cli"></a>[**Azure CLI**](#tab/tcp-reset-idle-cli)

Zum Festlegen von Leerlauftimeout und TCP-Zurücksetzung verwenden Sie die folgenden Parameter für [az network lb rule update](/cli/azure/network/lb/rule?az_network_lb_rule_update):

* **--idle-timeout**
* **--enable-tcp-reset**

Überprüfen Sie Ihre Umgebung, bevor Sie beginnen:

* Melden Sie sich beim Azure-Portal an, und führen Sie `az login` aus, um sich zu vergewissern, dass Ihr Abonnement aktiv ist.
* Überprüfen Sie Ihre Version der Azure-Befehlszeilenschnittstelle, indem Sie in einem Terminal oder Befehlsfenster `az --version`ausführen. Die neueste Version finden Sie unter [Versionshinweise für die Azure CLI](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Sollten Sie nicht über die neueste Version verfügen, aktualisieren Sie Ihre Installation wie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli) beschrieben.

Ersetzen Sie die folgenden Beispiele durch die Werte aus Ihren Ressourcen:

* **myResourceGroup**
* **myLoadBalancer**
* **myLBrule**


```azurecli
az network lb rule update \
    --resource-group myResourceGroup \
    --name myLBrule \
    --lb-name myLoadBalancer \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
---
## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Leerlauftimeout und TCP-Zurücksetzung finden Sie unter [TCP-Zurücksetzung und Leerlauftimeout für Load Balancer](load-balancer-tcp-reset.md).

Weitere Informationen zum Konfigurieren des Load Balancer-Verteilungsmodus finden Sie unter [Konfigurieren des Verteilungsmodus für Azure Load Balancer](load-balancer-distribution-mode.md).
