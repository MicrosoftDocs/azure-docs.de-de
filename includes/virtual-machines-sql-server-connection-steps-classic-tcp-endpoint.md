---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: d4e8d99cd7c67136f359772664eb017c6207e6e4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178034"
---
### <a name="create-a-tcp-endpoint-for-the-virtual-machine"></a>Erstellen eines TCP-Endpunkts für den virtuellen Computer
Für den Zugriff auf SQL Server über das Internet benötigt der virtuelle Computer einen Endpunkt, um nach eingehender TCP-Kommunikation zu lauschen. Dieser Azure-Konfigurationsschritt leitet den eingehenden Datenverkehr des TCP-Ports zu einem TCP-Port, auf den der virtuelle Computer zugreifen kann.

> [!NOTE]
> Wenn die Verbindung innerhalb desselben Clouddiensts oder innerhalb desselben virtuellen Netzwerks erfolgt, müssen Sie keinen Endpunkt mit öffentlichem Zugriff erstellen. In diesem Fall können Sie mit dem nächsten Schritt fortfahren. Weitere Informationen finden Sie unter [Verbindungsszenarien](../articles/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect.md#connection-scenarios).
> 
> 

1. Wählen Sie im Azure-Portal **Virtuelle Computer (klassisch)** aus.
2. Wählen Sie dann Ihren virtuellen Computer mit SQL Server aus.
3. Wählen Sie **Endpunkte** aus, und klicken Sie dann oben auf dem Blatt „Endpunkte“ auf die Schaltfläche **Hinzufügen**.
   
    ![Schritte im Portal für die Endpunkterstellung](./media/virtual-machines-sql-server-connection-steps/portal-endpoint-creation.png)
4. Geben Sie auf dem Blatt **Endpunkt hinzufügen** einen **Namen** wie „SQLEndpoint“ ein.
5. Wählen Sie **TCP** als **Protokoll** aus.
6. Geben Sie für **Öffentlicher Port** eine Portnummer wie **57500** an.
7. Geben Sie für **Privater Port** den SQL Server-Lauschport an. Standardmäßig ist dies **1433**.
8. Klicken Sie auf **OK** , um den Endpunkt zu erstellen.

