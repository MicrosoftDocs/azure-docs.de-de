---
title: Konfigurieren von Business Continuity & Disaster Recovery (BCDR) im Azure Stack Edge-VPN (virtuelles privates Netzwerk)
description: Hier wird beschrieben, wie BCDR im Azure Stack Edge-VPN konfiguriert wird.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/17/2020
ms.author: alkohli
ms.openlocfilehash: 4d735c623a6dffe24108d06d00caa7fba987c7df
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465447"
---
# <a name="configure-business-continuity-and-disaster-recovery-for-azure-stack-edge-vpn"></a>Konfigurieren von Business Continuity & Disaster Recovery für Azure Stack Edge-VPNs

[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]

In diesem Artikel wird beschrieben, wie Sie Business Continuity & Disaster Recovery (BCDR) für ein VPN (virtuelles privates Netzwerk) auf einem Azure Stack Edge-Gerät konfigurieren.

Die Informationen in diesem Artikel beziehen sich auf Geräte vom Typ „Azure Stack Edge Pro R“ und „Azure Stack Edge Mini R“.

## <a name="configure-failover-to-a-paired-region"></a>Failoverkonfiguration für eine gekoppelte Region

Von Ihrem Azure Stack Edge-Gerät werden auch andere Azure-Dienste wie z. B. Azure Storage verwendet. Sie können BCDR für jeden in Frage kommenden Azure-Dienst konfigurieren, der vom Azure Stack Edge-Gerät verwendet wird. Wenn ein von Azure Stack Edge verwendeter Azure-Dienst ein Failover zu seiner gekoppelten Region ausführt, stellt das Azure Stack Edge-Gerät nun eine Verbindung mit den neuen IP-Adressen her, sodass die Kommunikation nicht doppelt verschlüsselt wird. 

Das Azure Stack Edge-Gerät verwendet Split Tunneling. Das heißt, dass alle in der Heimregion (die Region, die dem Azure Stack Edge-Gerät zugewiesen ist) konfigurierten Daten und Dienste über den VPN-Tunnel geleitet werden. Wenn für Azure-Dienste ein Failover in eine gekoppelte Region stattfindet, die sich außerhalb der Heimregion befindet, werden die Daten nicht mehr über das VPN übermittelt und daher auch nicht doppelt verschlüsselt. 

Dieses Szenario wirkt sich in der Regel nur auf eine kleine Anzahl von Azure-Diensten aus. Um dieses Problem zu beheben, müssen die folgenden Änderungen an der Konfiguration des Azure Stack Edge-VPNs vorgenommen werden:

1. Fügen Sie die IP-Adressbereiche, die beim Failover für den Azure-Dienst verwendet werden sollen, zu den inklusiven Routen im Azure Stack Edge-VPN hinzu. Die Dienste werden dann ab diesem Zeitpunkt über das VPN geleitet.

    Um die inklusiven Routen hinzuzufügen, müssen Sie die JSON-Datei herunterladen, in der die spezifischen Routen für den Dienst enthalten sind. Stellen Sie sicher, dass Sie diese Datei mit den neuen Routen aktualisieren.
2. Fügen Sie die entsprechenden IP-Adressbereiche des Azure-Diensts in der Azure-Routingtabelle hinzu.
3. Fügen Sie die Routen zur Firewall hinzu.

> [!NOTE]
>
> 1. Wie Sie ein Failover für ein Azure VPN Gateway und ein Azure Virtual Network (VNet) durchführen, wird im Abschnitt zur [Notfallwiederherstellung einer Azure-Region durch ein Failover](#recover-from-a-failed-azure-region) beschrieben.
> 2. Die in der Azure-Routingtabelle hinzugefügten IP-Adressbereiche können den Grenzwert von 400 überschreiten. In diesem Fall müssen Sie die Schritte im Abschnitt zum [Verschieben von Geräten zwischen Azure-Regionen](#move-from-an-azure-region-to-another) befolgen.

## <a name="recover-from-a-failed-azure-region"></a>Failoverwiederherstellung einer Azure-Region

Wenn für die gesamte Azure-Region aufgrund eines Katastrophenfalls (z. B. ein Erdbeben) ein Failover ausgeführt wird, werden alle Azure-Dienste in dieser Region beim Failover berücksichtigt, auch der Azure Stack Edge-Dienst. Da mehrere Dienste betroffen sind, kann die Anzahl der inklusiven Routen leicht auf einige Hundert ansteigen. In Azure gilt eine Beschränkung auf 400 Routen. 

Wenn für die Region ein Failover ausgeführt wird, werden auch das virtuelle Netzwerk (VNet) und das VPN-Gateway (Virtual Network Gateway) per Failover auf die neue Region umgestellt. Um dieses Problem zu beheben, müssen die folgenden Änderungen an der Konfiguration des Azure Stack Edge-VPNs vorgenommen werden:

1. Verschieben Sie das VNet in die Zielregion. Weitere Informationen finden Sie unter [Verschieben eines virtuellen Azure-Netzwerks in eine andere Region im Azure-Portal](../virtual-network/move-across-regions-vnet-portal.md).
2. Stellen Sie ein neues Azure VPN Gateway in der Zielregion bereit, in die Sie das VNet verschoben haben. Weitere Informationen finden Sie unter [Erstellen eines Gateways für das virtuelle Netzwerk](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw).
3. Aktualisieren Sie die Konfiguration des Azure Stack Edge-VPNs, um das oben angegebene VPN-Gateway in der VPN-Verbindung zu verwenden. Wählen Sie dann die Zielregion aus, um Routen hinzuzufügen, die das VPN-Gateway nutzen.
4. Aktualisieren Sie die Azure-Routingtabelle für eingehende Routen, wenn sich der Clientadresspool ebenfalls ändert. 

## <a name="move-from-an-azure-region-to-another"></a>Verschieben zwischen zwei Azure-Regionen

Sie können Ihr Azure Stack Edge-Gerät von einem Ort an einen anderen Ort verschieben. Um eine Region zu verwenden, die dem Bereitstellungsort Ihres Geräts am nächsten liegt, müssen Sie für das Gerät eine neue Heimregion konfigurieren. Nehmen Sie die folgenden Änderungen vor:

1. Sie können die Konfiguration des Azure Stack Edge-VPNs aktualisieren, um das VPN-Gateway einer neuen Region zu verwenden. Anschließend wählen Sie die neue Region aus, um Routen hinzuzufügen, die das VPN-Gateway verwenden.

## <a name="next-steps"></a>Nächste Schritte

[Sichern eines Azure Stack Edge-Geräts](azure-stack-edge-gpu-prepare-device-failure.md).