---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 760bb5b62e9bba9b7a83f99760f7fe5d8c399dfb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178042"
---
1. Erweitern Sie im Failovercluster-Manager **Rollen**, und markieren Sie dann Ihre Verfügbarkeitsgruppe.  

2. Klicken Sie auf der Registerkarte **Ressourcen** mit der rechten Maustaste auf den Listenernamen, und klicken Sie dann auf **Eigenschaften**.

3. Klicken Sie auf die Registerkarte **Abhängigkeiten** . Wenn mehrere Ressourcen aufgeführt sind, überprüfen Sie, ob die IP-Adressen OR-Abhängigkeiten aufweisen (keine AND-Abhängigkeiten).  

4. Klicken Sie auf **OK**.

5. Klicken Sie mit der rechten Maustaste auf den Listenernamen, und klicken Sie dann auf **Online schalten**.

6. Sobald der Listener online ist, klicken Sie auf der Registerkarte **Ressourcen** mit der rechten Maustaste auf die Verfügbarkeitsgruppe, und klicken Sie dann auf **Eigenschaften**.
   
    ![Konfigurieren der Verfügbarkeitsgruppenressource](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Erstellen Sie eine Abhängigkeit für die Ressource des Listenernamens (nicht den Namen der IP-Adressressourcen), und klicken Sie dann auf **OK**.
   
    ![Hinzufügen einer Abhängigkeit vom Listenernamen](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Starten Sie SQL Server Management Studio, und stellen Sie dann eine Verbindung mit dem primären Replikat her.

9. Navigieren Sie zu **Hochverfügbarkeit mit AlwaysOn** > **Verfügbarkeitsgruppen** >  **\<Verfügbarkeitsgruppenname\>**  > **Verfügbarkeitsgruppenlistener**.  
    Der Listenername, den Sie im Failovercluster-Manager erstellt haben, sollte angezeigt werden.

10. Klicken Sie mit der rechten Maustaste auf den Listenernamen, und klicken Sie dann auf **Eigenschaften**.

11. Geben Sie mit dem zuvor verwendeten „$EndpointPort“ im Feld **Port** die Portnummer für den Verfügbarkeitsgruppenlistener an (in diesem Tutorial war 1433 der Standardwert), und klicken Sie dann auf **OK**.

