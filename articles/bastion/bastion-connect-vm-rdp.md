---
title: Herstellen einer Verbindung mit einer Windows-VM mit Azure Bastion
description: In diesem Artikel erfahren Sie, wie Sie eine Verbindung mit einem virtuellen Azure-Computer mit Azure Bastion herstellen.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: 708bd1f61da2f3973333f8e68cabdceee0717bee
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92521532"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Herstellen einer Verbindung mit einem virtuellen Windows-Computer mit Azure Bastion

Mit Azure Bastion können Sie eine sichere und problemlose Verbindung mit Ihren virtuellen Computern über SSL direkt im Azure-Portal herstellen. Wenn Sie Azure Bastion verwenden, benötigen Ihre virtuellen Computer keinen Client, keinen Agent und auch keine zusätzliche Software. In diesem Artikel erfahren Sie, wie Sie eine Verbindung mit Ihren Windows-VMs herstellen können. Informationen zur Verbindung mit einer Linux-VM finden Sie unter [Herstellen einer Verbindung mit einem virtuellen Linux-Computer](bastion-connect-vm-ssh.md).

Azure Bastion bietet sichere Verbindungen mit allen virtuellen Computern in dem virtuellen Netzwerk, in dem der Dienst bereitgestellt wird. Durch die Verwendung von Azure Bastion wird verhindert, dass Ihre virtuellen Computer RDP- und SSH-Ports öffentlich verfügbar machen. Gleichzeitig wir weiterhin der sichere Zugriff per RDP/SSH ermöglicht. Weitere Informationen finden Sie unter [Was ist Azure Bastion?](bastion-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich zunächst, dass die folgenden Kriterien erfüllt sind:

* Ein VNET mit bereits installiertem Bastion-Host

   Stellen Sie sicher, dass Sie einen Azure Bastion-Host für das virtuelle Netzwerk eingerichtet haben, in dem sich die VM befindet. Sobald der Bastion-Dienst provisioniert und in Ihrem virtuellen Netzwerk bereitgestellt wurde, können Sie ihn verwenden, um sich mit jeder VM im virtuellen Netzwerk zu verbinden. Informationen zum Einrichten eines Azure Bastion-Hosts finden Sie unter [Erstellen eines Bastion-Hosts](tutorial-create-host-portal.md#createhost).
* Ein virtueller Windows-Computer im virtuellen Netzwerk
* Folgende erforderliche Rollen:
  * Rolle „Leser“ für den virtuellen Computer
  * Rolle „Leser“ auf der Netzwerkschnittstellenkarte mit privater IP-Adresse des virtuellen Computers
  * Rolle „Leser“ für die Azure Bastion-Ressource
* Ports: Zum Herstellen einer Verbindung mit dem virtuellen Windows-Computer müssen die folgenden Ports auf Ihrer Windows-VM geöffnet sein:
  * Eingehende Ports: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Verbinden

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die [häufig gestellten Fragen zu Bastion](bastion-faq.md).