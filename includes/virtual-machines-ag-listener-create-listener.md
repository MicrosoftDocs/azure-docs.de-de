---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 394b242ab46da7821f77e8d008836753f4e358e2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66165360"
---
In diesem Schritt erstellen Sie manuell den Verfügbarkeitsgruppenlistener im Failovercluster-Manager und in SQL Server Management Studio.

1. Öffnen Sie den Failovercluster-Manager über den Knoten, der das primäre Replikat hostet.

2. Wählen Sie den Knoten **Netzwerke** aus, und notieren Sie dann den Namen des Clusternetzwerks. Dieser Name wird in der Variablen $ClusterNetworkName im PowerShell-Skript verwendet.

3. Erweitern Sie den Clusternamen, und klicken Sie dann auf **Rollen**.

4. Klicken Sie im Bereich **Rollen** mit der rechten Maustaste auf den Verfügbarkeitsgruppennamen, und wählen Sie dann **Ressource hinzufügen** > **Clientzugriffspunkt** aus.
   
    ![Hinzufügen eines Clientzugriffspunkts für die Verfügbarkeitsgruppe](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. Erstellen Sie im Feld **Name** einen Namen für diesen neuen Listener, klicken Sie zweimal auf **Weiter** und anschließend auf **Fertig stellen**.  
    Schalten Sie den Listener oder die Ressource jetzt noch nicht online.

6. Klicken Sie auf die Registerkarte **Ressourcen**, und erweitern Sie dann den Clientzugriffspunkt, den Sie gerade erstellt haben. 
    Die IP-Adressressource für jedes der Clusternetzwerke im Cluster wird angezeigt. Wenn dies eine reine Azure-Lösung ist, wird nur eine IP-Adressressource angezeigt.

7. Führen Sie einen der folgenden Schritte aus:
   
   * So konfigurieren Sie eine Hybridlösung
     
        a. Klicken Sie mit der rechten Maustaste auf die IP-Adressressource, die Ihrem lokalen Subnetz entspricht, und wählen Sie dann **Eigenschaften** aus. Notieren Sie den Namen der IP-Adresse und des Netzwerks.
   
        b. Wählen Sie **Statische IP-Adresse** aus, weisen Sie eine nicht verwendete IP-Adresse zu, und klicken Sie dann auf **OK**.
 
   * So konfigurieren Sie eine reine Azure-Lösung

        a. Klicken Sie mit der rechten Maustaste auf die IP-Adressressource, die Ihrem Azure-Subnetz entspricht, und wählen Sie dann **Eigenschaften** aus.
       
       > [!NOTE]
       > Kann der Listener später durch eine in Konflikt stehende IP-Adresse, die von DHCP ausgewählt wird, nicht online geschaltet werden, können Sie in diesem Eigenschaftenfenster eine gültige statische IP-Adresse konfigurieren.
       > 
       > 

       b. Ändern Sie im gleichen Eigenschaftenfenster **IP-Adresse** den Wert für **IP-Adressname**.  
        Dieser Name wird in der Variablen $IPResourceName des PowerShell-Skripts verwendet. Wenn die Lösung mehrere virtuelle Azure-Netzwerke umfasst, wiederholen Sie diesen Schritt für jede IP-Ressource.

