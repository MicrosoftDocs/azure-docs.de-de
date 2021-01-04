---
title: Konfigurieren eines Verfügbarkeitsgruppenlisteners für SQL Server auf virtuellen RHEL-Computern in Azure – Virtuelle Linux-Computer | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie einen Verfügbarkeitsgruppenlistener in SQL Server auf virtuellen RHEL-Computern in Azure einrichten.
ms.service: virtual-machines-linux
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: jroth
ms.date: 03/11/2020
ms.openlocfilehash: 83fde9e957cb2011ce585603e51d331be171bc08
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586226"
---
# <a name="tutorial-configure-an-availability-group-listener-for-sql-server-on-rhel-virtual-machines-in-azure"></a>Tutorial: Konfigurieren eines Verfügbarkeitsgruppenlisteners für SQL Server auf virtuellen RHEL-Computern in Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!NOTE]
> Dieses Tutorial befindet sich in der **Public Preview-Phase**. 
>
> In diesem Tutorial wird SQL Server 2017 mit RHEL 7.6 verwendet. Hochverfügbarkeit kann aber auch mit SQL Server 2019 in RHEL 7 oder RHEL 8 konfiguriert werden. Die Befehle zum Konfigurieren von Verfügbarkeitsgruppenressourcen haben sich in RHEL 8 geändert. Informationen zu den korrekten Befehlen finden Sie im Artikel [Erstellen von Verfügbarkeitsgruppenressourcen](/sql/linux/sql-server-linux-availability-group-cluster-rhel#create-availability-group-resource) sowie in RHEL 8-Ressourcen.

In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie einen Verfügbarkeitsgruppenlistener für Ihre SQL Server-Instanzen auf virtuellen RHEL-Computern (VMs) in Azure erstellen. Sie lernen Folgendes:

> [!div class="checklist"]
> - Erstellen eines Lastenausgleichs im Azure-Portal
> - Konfigurieren des Back-End-Pools für den Lastenausgleich
> - Erstellen eines Tests für den Lastenausgleich
> - Festlegen der Lastenausgleichsregeln
> - Erstellen der Lastenausgleichsressource im Cluster
> - Erstellen des Verfügbarkeitsgruppenlisteners
> - Testen der Verbindungsherstellung mit dem Listener
> - Testen eines Failovers

## <a name="prerequisite"></a>Voraussetzung

Abschluss von [Tutorial: Konfigurieren von Verfügbarkeitsgruppen für SQL Server auf virtuellen RHEL-Computern in Azure](rhel-high-availability-stonith-tutorial.md)

## <a name="create-the-load-balancer-in-the-azure-portal"></a>Erstellen des Lastenausgleichs im Azure-Portal

In der folgenden Anleitung werden die Schritte 1 bis 4 aus dem Abschnitt [Erstellen und Konfigurieren des Load Balancers im Azure-Portal](../windows/availability-group-load-balancer-portal-configure.md#create--configure-load-balancer) des Artikels [Konfigurieren einer Load Balancer-Instanz für eine Verfügbarkeitsgruppe in Azure auf Azure SQL Server-VMs](../windows/availability-group-load-balancer-portal-configure.md) beschrieben.

### <a name="create-the-load-balancer"></a>Erstellen des Lastenausgleichs

1. Öffnen Sie im Azure-Portal die Ressourcengruppe mit den virtuellen SQL Server-Computern. 

2. Klicken Sie in der Ressourcengruppe auf **Hinzufügen**.

3. Suchen Sie nach **Load Balancer**, und wählen Sie dann in den Suchergebnissen den **Load Balancer** aus, der von **Microsoft** veröffentlicht wurde.

4. Klicken Sie auf dem Blatt **Load Balancer** auf **Erstellen**.

5. Konfigurieren Sie den Load Balancer unter **Lastenausgleich erstellen** wie folgt:

   | Einstellung | Wert |
   | --- | --- |
   | **Name** |Namenstext für den Load Balancer. Beispiel: **sqlLB**. |
   | **Typ** |**Intern** |
   | **Virtuelles Netzwerk** |Das erstellte standardmäßige virtuelle Netzwerk sollte den Namen **VM1VNET** haben. |
   | **Subnetz** |Wählen Sie das Subnetz aus, in dem sich die SQL Server-Instanzen befinden. Der Standardwert sollte **VM1Subnet** lauten.|
   | **IP-Adresszuweisung** |**Statisch** |
   | **Private IP-Adresse** |Verwenden Sie die im Cluster erstellte IP-Adresse `virtualip`. |
   | **Abonnement** |Verwenden Sie das für Ihre Ressourcengruppe verwendete Abonnement. |
   | **Ressourcengruppe** |Wählen Sie die Ressourcengruppe aus, in der sich die SQL Server-Instanzen befinden. |
   | **Location** |Wählen Sie die Azure-Region aus, in der sich die SQL Server-Instanzen befinden. |

### <a name="configure-the-back-end-pool"></a>Konfigurieren des Back-End-Pools
Im Kontext von Azure wird der Back-End-Adresspool als *Back-End-Pool* bezeichnet. Im vorliegenden Fall enthält der Back-End-Pool die Adressen der drei SQL Server-Instanzen in Ihrer Verfügbarkeitsgruppe. 

1. Klicken Sie in Ihrer Ressourcengruppe auf den zuvor erstellten Load Balancer. 

2. Klicken Sie unter **Einstellungen** auf **Back-End-Pools**.

3. Klicken Sie im Bereich **Back-End-Pools** auf **Hinzufügen**, um einen Back-End-Adresspool zu erstellen. 

4. Geben Sie im Bereich **Back-End-Pool hinzufügen** unter **Name** einen Namen für den Back-End-Pool ein.

5. Wählen Sie unter **Verknüpft mit** die Option **Virtueller Computer** aus. 

6. Wählen Sie die einzelnen virtuellen Computer in der Umgebung aus, und ordnen Sie ihnen jeweils die entsprechende IP-Adresse zu.

    :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-backend-pool.png" alt-text="Hinzufügen eines Back-End-Pools":::

7. Klicken Sie auf **Hinzufügen**. 

### <a name="create-a-probe"></a>Erstellen eines Tests

Mit dem Test wird definiert, wie Azure überprüft, welche SQL Server-Instanz gerade für den Verfügbarkeitsgruppenlistener zuständig ist. Azure testet den Dienst auf der Grundlage der IP-Adresse an einem Port, den Sie beim Erstellen des Tests definieren.

1. Klicken Sie auf dem Blatt **Einstellungen** des Load Balancers auf **Integritätstests**. 

2. Klicken Sie auf dem Blatt **Integritätstests** auf **Hinzufügen**.

3. Konfigurieren Sie den Test auf dem Blatt **Test hinzufügen** . Verwenden Sie dabei die folgenden Werte:

   | Einstellung | Wert |
   | --- | --- |
   | **Name** |Namenstext für den Test. Beispiel: **SQLAlwaysOnEndPointProbe**. |
   | **Protokoll** |**TCP** |
   | **Port** |Sie können jeden verfügbaren Port verwenden. Beispiel: *59999*. |
   | **Intervall** |*5* |
   | **Fehlerhafter Schwellenwert** |*2* |

4.  Klicken Sie auf **OK**. 

5. Melden Sie sich bei allen Ihren virtuellen Computern an, und öffnen Sie den Testport mithilfe der folgenden Befehle:

    ```bash
    sudo firewall-cmd --zone=public --add-port=59999/tcp --permanent
    sudo firewall-cmd --reload
    ```

Azure erstellt den Test und ermittelt dann mit seiner Hilfe, welche SQL Server-Instanz über den Listener für die Verfügbarkeitsgruppe verfügt.

### <a name="set-the-load-balancing-rules"></a>Festlegen der Lastenausgleichsregeln

Mit den Lastenausgleichsregeln wird konfiguriert, wie der Load Balancer Datenverkehr an die SQL Server-Instanzen weiterleitet. Für diesen Lastenausgleich wird Direct Server Return aktiviert, da immer nur eine der drei SQL Server-Instanzen für die Verfügbarkeitsgruppenlistener-Ressource zuständig ist.

1. Klicken Sie auf dem Blatt **Einstellungen** des Load Balancers auf **Load balancing rules** (Lastenausgleichsregeln). 

2. Klicken Sie auf dem Blatt **Load balancing rules** (Lastenausgleichsregeln) auf **Hinzufügen**.

3. Konfigurieren Sie die Lastenausgleichsregel auf dem Blatt **Lastenausgleichsregeln hinzufügen**. Verwenden Sie folgende Einstellungen: 

   | Einstellung | Wert |
   | --- | --- |
   | **Name** |Namenstext für die Lastenausgleichsregeln. Beispiel: **SQLAlwaysOnEndPointListener**. |
   | **Protokoll** |**TCP** |
   | **Port** |*1433* |
   | **Back-End-Port** |*1433*. Dieser Wert wird ignoriert, da diese Regel **Floating IP (Direct Server Return)** verwendet. |
   | **Test** |Verwenden Sie den Namen des Tests, den Sie für diesen Load Balancer erstellt haben. |
   | **Sitzungspersistenz** |**None** |
   | **Leerlaufzeitüberschreitung (Minuten)** |*4* |
   | **Floating IP (Direct Server Return)** |**Aktiviert** |

   :::image type="content" source="media/rhel-high-availability-listener-tutorial/add-load-balancing-rule.png" alt-text="Hinzufügen einer Lastenausgleichsregel":::

4. Klicken Sie auf **OK**. 
5. Azure konfiguriert die Lastenausgleichsregel. Damit ist der Load Balancer dafür konfiguriert, Datenverkehr an die SQL Server-Instanz weiterzuleiten, die den Listener für die Verfügbarkeitsgruppe hostet. 

Die Ressourcengruppe verfügt nun also über einen mit allen SQL Server-Computern verbundenen Lastenausgleich. Außerdem verfügt der Lastenausgleich über eine IP-Adresse für den SQL Server-AlwaysOn-Verfügbarkeitsgruppenlistener, sodass jeder Computer auf Anforderungen für die Verfügbarkeitsgruppen reagieren kann.

## <a name="create-the-load-balancer-resource-in-the-cluster"></a>Erstellen der Lastenausgleichsressource im Cluster

1. Melden Sie sich beim primären virtuellen Computer an. Wir müssen die Ressource erstellen, um den Testport für den Azure-Lastenausgleich zu aktivieren. (In unserem Beispiel wird der Port 59999 verwendet.) Führen Sie den folgenden Befehl aus:

    ```bash
    sudo pcs resource create azure_load_balancer azure-lb port=59999
    ```

1. Erstellen Sie eine Ressourcengruppe, die die virtuelle IP (`virtualip`) und die Ressource `azure_load_balancer` enthält:

    ```bash
    sudo pcs resource group add virtualip_group azure_load_balancer virtualip
    ```

### <a name="add-constraints"></a>Hinzufügen von Einschränkungen

1. Es muss eine Kollokationseinschränkung konfiguriert werden, um sicherzustellen, dass sich die IP-Adresse des Azure-Lastenausgleichs und die Verfügbarkeitsgruppenressource auf dem gleichen Knoten befinden. Führen Sie den folgenden Befehl aus:

    ```bash
    sudo pcs constraint colocation add azure_load_balancer ag_cluster-master INFINITY with-rsc-role=Master
    ```
1. Erstellen Sie eine Sortierungseinschränkung, um sicherzustellen, dass die Verfügbarkeitsgruppenressource vor der IP-Adresse des Azure-Lastenausgleichs einsatzbereit ist. Obwohl die Kollokationseinschränkung eine Sortierungseinschränkung impliziert, erzwingt sie diese.

    ```bash
    sudo pcs constraint order promote ag_cluster-master then start azure_load_balancer
    ```

1. Führen Sie den folgenden Befehl aus, um die Einschränkungen zu überprüfen:

    ```bash
    sudo pcs constraint list --full
    ```

    Die folgende Ausgabe wird angezeigt.

    ```output
    Location Constraints:
    Ordering Constraints:
      promote ag_cluster-master then start virtualip (kind:Mandatory) (id:order-ag_cluster-master-virtualip-mandatory)
      promote ag_cluster-master then start azure_load_balancer (kind:Mandatory) (id:order-ag_cluster-master-azure_load_balancer-mandatory)
    Colocation Constraints:
      virtualip with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-virtualip-ag_cluster-master-INFINITY)
      azure_load_balancer with ag_cluster-master (score:INFINITY) (with-rsc-role:Master) (id:colocation-azure_load_balancer-ag_cluster-master-INFINITY)
    Ticket Constraints:
    ```

## <a name="create-the-availability-group-listener"></a>Erstellen des Verfügbarkeitsgruppenlisteners

1. Führen Sie auf dem primären Knoten den folgenden Befehl in SQLCMD oder SSMS aus:

    - Ersetzen Sie die unten verwendete IP-Adresse durch die IP-Adresse `virtualip`.

    ```sql
    ALTER AVAILABILITY
    GROUP [ag1] ADD LISTENER 'ag1-listener' (
            WITH IP(('10.0.0.7'    ,'255.255.255.0'))
                ,PORT = 1433
            );
    GO
    ```

1. Melden Sie sich bei den einzelnen VM-Knoten an. Verwenden Sie den folgenden Befehl, um die Hostdatei zu öffnen und die Hostnamensauflösung für `ag1-listener` auf den einzelnen Computern einzurichten.

    ```
    sudo vi /etc/hosts
    ```

    Geben Sie im **vi**-Editor `i` ein, um Text einzufügen, und fügen Sie in einer leeren Zeile die IP-Adresse von `ag1-listener` hinzu. Fügen Sie nach der IP-Adresse ein Leerzeichen und anschließend `ag1-listener` hinzu.

    ```output
    <IP of ag1-listener> ag1-listener
    ```

    Drücken Sie zum Verlassen des **vi**-Editors **ESC**, und geben Sie anschließend den Befehl `:wq` ein, um die Datei zu schreiben und den Editor zu beenden. Führen Sie dies auf jedem Knoten durch.

## <a name="test-the-listener-and-a-failover"></a>Testen des Listeners und eines Failovers

### <a name="test-logging-in-to-sql-server-using-the-availability-group-listener"></a>Testen der Anmeldung bei SQL Server mit dem Verfügbarkeitsgruppenlistener

1. Verwenden Sie SQLCMD, um sich unter Verwendung des Namens des Verfügbarkeitsgruppenlisteners beim primären Knoten von SQL Server anzumelden:

    - Verwenden Sie eine zuvor erstellte Anmeldung, und ersetzen Sie `<YourPassword>` durch das korrekte Kennwort. Im folgenden Beispiel wird die zuvor mit SQL Server erstellte Anmeldung `sa` verwendet.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
    ```

1. Überprüfen Sie den Namen des Servers, mit dem Sie verbunden sind. Führen Sie in SQLCMD den folgenden Befehl aus:

    ```sql
    SELECT @@SERVERNAME
    ```

    Ihre Ausgabe sollte den aktuellen primären Knoten enthalten. Dabei sollte es sich um `VM1` handeln, sofern Sie noch nie ein Failover getestet haben.

    Geben Sie den Befehl `exit` ein, um die SQL Server-Sitzung zu beenden.

### <a name="test-a-failover"></a>Testen eines Failovers

1. Führen Sie den folgenden Befehl aus, um ein manuelles Failover des primären Replikats auf `<VM2>` oder auf ein anderes Replikat durchzuführen. Ersetzen Sie `<VM2>` durch Ihren Servernamen.

    ```bash
    sudo pcs resource move ag_cluster-master <VM2> --master
    ```

1. Wenn Sie Ihre Einschränkungen überprüfen, sehen Sie, dass aufgrund des manuellen Failovers eine weitere Einschränkung hinzugefügt wurde:

    ```bash
    sudo pcs constraint list --full
    ```

    Wie Sie sehen, wurde eine Einschränkung mit der ID `cli-prefer-ag_cluster-master` hinzugefügt.

1. Entfernen Sie die Einschränkung mit der ID `cli-prefer-ag_cluster-master` mithilfe des folgenden Befehls:

    ```bash
    sudo pcs constraint remove cli-prefer-ag_cluster-master
    ```

1. Überprüfen Sie Ihre Clusterressourcen mithilfe des Befehls `sudo pcs resource`. Die primäre Instanz sollte nun `<VM2>` sein.

    > [!NOTE]
    > Dieser Artikel enthält Verweise auf den Begriff Slave, einen Begriff, den Microsoft nicht mehr verwendet. Sobald der Begriff aus der Software entfernt wird, wird er auch aus diesem Artikel entfernt.


    ```output
    [<username>@<VM1> ~]$ sudo pcs resource
    Master/Slave Set: ag_cluster-master [ag_cluster]
        Masters: [ <VM2> ]
        Slaves: [ <VM1> <VM3> ]
    Resource Group: virtualip_group
        azure_load_balancer        (ocf::heartbeat:azure-lb):      Started <VM2>
        virtualip  (ocf::heartbeat:IPaddr2):       Started <VM2>
    ```

1. Verwenden Sie SQLCMD, um sich unter Verwendung des Listenernamens bei Ihrem primären Replikat anzumelden:

    - Verwenden Sie eine zuvor erstellte Anmeldung, und ersetzen Sie `<YourPassword>` durch das korrekte Kennwort. Im folgenden Beispiel wird die zuvor mit SQL Server erstellte Anmeldung `sa` verwendet.

    ```bash
    sqlcmd -S ag1-listener -U sa -P <YourPassword>
     ```

1. Überprüfen Sie den Server, mit dem Sie verbunden sind. Führen Sie in SQLCMD den folgenden Befehl aus:

    ```sql
    SELECT @@SERVERNAME
    ```

    Sie sollten nun mit dem virtuellen Computer verbunden sein, auf den Sie das Failover durchgeführt haben.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Lastenausgleich in Azure finden Sie hier:

> [!div class="nextstepaction"]
> [Konfigurieren einer Load Balancer-Instanz für eine Verfügbarkeitsgruppe in Azure auf Azure SQL Server-VMs](../windows/availability-group-load-balancer-portal-configure.md)
