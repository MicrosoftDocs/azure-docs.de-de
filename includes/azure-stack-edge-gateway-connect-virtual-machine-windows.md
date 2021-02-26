---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: e781e3c29a6a22648c6d0596f9fe0a3e425427d7
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99500163"
---
Stellen Sie über das Remotedesktopprotokoll (RDP) mithilfe der IP-Adresse, die Sie bei der Erstellung der VM übergeben haben, eine Verbindung mit Ihrer Windows-VM her.

1. Starten Sie RDP auf Ihrem Client. Navigieren Sie zu **Start**, und geben Sie **mstsc** ein.
1. Geben Sie die IP-Adresse des virtuellen Computers und die verwendeten Anmeldeinformationen in die Vorlagenparameterdatei des virtuellen Computers ein.

    ![Screenshot des Herstellens einer RDP-Verbindung mit einer Windows-VM](media/azure-stack-edge-gateway-connect-vm-windows/connect-vm-rdp-1.png)
1. Möglicherweise müssen Sie die Verbindung mit einem nicht vertrauenswürdigen Computer genehmigen. 
1. Sie sind jetzt bei Ihrer VM angemeldet, die auf der Appliance ausgeführt wird. 