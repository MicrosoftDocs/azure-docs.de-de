---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 28aab15dc67e051190e8d4e35e92240a56fe54a6
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178039"
---
Wenn auf allen Servern im Cluster Windows Server 2008 R2 oder Windows Server 2012 ausgeführt wird, müssen Sie sicherstellen, dass der Hotfix [KB2854082](https://support.microsoft.com/kb/2854082) auf allen lokalen Servern oder virtuellen Azure-Computern installiert ist, die Teil des Clusters sind. Auf allen Servern oder virtuellen Computern, die sich im Cluster befinden, aber nicht in der Verfügbarkeitsgruppe, sollte dieser Hotfix ebenfalls installiert sein.

Laden Sie in der Remotedesktopsitzung für jeden Clusterknoten [KB2854082](https://support.microsoft.com/kb/2854082) in ein lokales Verzeichnis herunter. Installieren Sie dann den Hotfix nacheinander auf allen Clusterknoten. Wenn der Clusterdienst derzeit auf dem Clusterknoten ausgeführt wird, wird der Server am Ende der Installation des Hotfixes neu gestartet.

> [!WARNING]
> Das Beenden des Clusterdiensts oder das Neustarten des Servers wirkt sich auf den Quorumzustand des Clusters und der Verfügbarkeitsgruppe aus und kann dazu führen, dass der Cluster offline geschaltet wird. Um die Hochverfügbarkeit des Clusters während der Installation zu gewährleisten, überprüfen Sie Folgendes:
> 
> * Der Cluster weist einen optimalen Quorumzustand auf. 
> * Vor der Installation des Hotfixes auf einem Knoten sind alle Clusterknoten online.
> * Bevor die Installation des Hotfixes auf einem anderen Knoten im Cluster ausgeführt wird, muss die Installation des Hotfixes auf einem Knoten vollständig abgeschlossen sein, einschließlich des kompletten Neustarts des Servers.
> 
> 

