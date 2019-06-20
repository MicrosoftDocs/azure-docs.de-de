---
title: Verbinden von Azure Kubernetes Service (AKS) mit Azure Database for MySQL
description: Es wird beschrieben, wie Sie Azure Kubernetes Service mit Azure Database for MySQL verbinden.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/28/2018
ms.openlocfilehash: d9f2e26a2bc89329ca9038c666c0d960289e2670
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60790520"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Verbinden von Azure Kubernetes Service und Azure Database for MySQL

Azure Kubernetes Service (AKS) stellt einen verwalteten Kubernetes-Cluster bereit, den Sie in Azure nutzen können. Unten sind einige Optionen angegeben, die Sie berücksichtigen sollten, wenn Sie AKS und Azure Database for MySQL zusammen zum Erstellen einer Anwendung verwenden.


## <a name="accelerated-networking"></a>Beschleunigte Netzwerke
Verwenden Sie in Ihrem AKS-Cluster zugrunde liegende virtuelle Computer, für die der beschleunigte Netzwerkbetrieb aktiviert ist. Wenn der beschleunigte Netzwerkbetrieb auf einem virtuellen Computer aktiviert ist, ist die Latenz geringer, Jitter reduziert und die CPU-Auslastung des virtuellen Computers niedriger. Informieren Sie sich eingehender über die Funktionsweise des beschleunigten Netzwerkbetriebs, die unterstützten Betriebssystemversionen und die unterstützten VM-Instanzen für [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

Ab November 2018 unterstützt AKS den beschleunigten Netzwerkbetrieb auf diesen unterstützen VM-Instanzen. Der beschleunigte Netzwerkbetrieb ist für neue AKS-Cluster, in denen diese virtuellen Computer verwendet werden, standardmäßig aktiviert.

Sie können überprüfen, ob Ihr AKS-Cluster über den beschleunigten Netzwerkbetrieb verfügt:
1. Navigieren Sie zum Azure-Portal, und wählen Sie Ihren AKS-Cluster aus.
2. Wählen Sie die Registerkarte „Eigenschaften“.
3. Kopieren Sie den Namen der **Infrastrukturressourcengruppe**.
4. Suchen Sie über die Suchleiste des Portals nach der Infrastrukturressourcengruppe, und öffnen Sie sie.
5. Wählen Sie in dieser Ressourcengruppe einen virtuellen Computer aus.
6. Navigieren Sie zur Registerkarte **Netzwerk** des virtuellen Computers.
7. Überprüfen Sie, ob die Option **Beschleunigter Netzwerkbetrieb** auf „Aktiviert“ festgelegt ist.

Alternativ über die Azure-Befehlszeilenschnittstelle mithilfe der folgenden beiden Befehle:
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
Die Ausgabe ist die von AKS erstellte generierte Ressourcengruppe, die die Netzwerkschnittstelle enthält. Verwenden Sie den Namen „nodeResourceGroup“ im nächsten Befehl. **EnableAcceleratedNetworking** ist entweder „true“ oder „false“:
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="open-service-broker-for-azure"></a>Installieren von Service Broker für Azure 
Mit [Open Service Broker für Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) können Sie Azure-Dienste direkt über Kubernetes oder Cloud Foundry bereitstellen. Es handelt sich um eine Implementierung von [Open Service Broker-API](https://www.openservicebrokerapi.org/) für Azure.

Mit OSBA können Sie einen Azure Database for MySQL-Server erstellen und per nativer Kubernetes-Sprache an Ihren AKS-Cluster binden. Informieren Sie sich auf der [GitHub-Seite zu OSBA](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/mysql.md), wie Sie OSBA und Azure Database for MySQL zusammen nutzen können. 



## <a name="next-steps"></a>Nächste Schritte
- [Erstellen eines Azure Kubernetes Service-Clusters](../aks/kubernetes-walkthrough.md)
- Informieren Sie sich, wie Sie [WordPress mit OSBA und Azure Database for MySQL über ein Helm-Diagramm installieren](../aks/integrate-azure.md).
