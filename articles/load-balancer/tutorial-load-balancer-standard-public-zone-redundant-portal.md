---
title: 'Tutorial: Durchführen eines Lastenausgleichs über Verfügbarkeitszonen hinweg für VMs: Azure-Portal'
titleSuffix: Azure Load Balancer
description: In diesem Tutorial wird gezeigt, wie Sie einen Lastenausgleich im Tarif „Standard“ mit einem zonenredundanten Front-End erstellen, um mit dem Azure-Portal einen Lastenausgleich für VMs über alle Verfügbarkeitszonen hinweg durchzuführen.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines across availability zones in a region, so that the customers can still access the web service if a datacenter is unavailable.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 4e07285eca0fd10b73b386fcf139cdad5b94ddc2
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696403"
---
# <a name="tutorial-load-balance-vms-across-availability-zones-with-a-standard-load-balancer-using-the-azure-portal"></a>Tutorial: Verfügbarkeitszonenübergreifender Lastenausgleich für VMs mit einer Load Balancer Standard-Instanz im Azure-Portal

Lastenausgleich bietet ein höheres Maß an Verfügbarkeit durch Verteilung der eingehenden Anforderungen auf mehrere virtuelle Computer. In diesem Tutorial werden die einzelnen Schritte zum Erstellen einer öffentlichen Load Balancer Standard-Instanz beschrieben, mit dem ein Lastenausgleich für VMs über Verfügbarkeitszonen hinweg durchgeführt wird. So schützen Sie Ihre Apps und Daten vor einem unwahrscheinlichen Fehler oder Ausfall eines gesamten Datencenters. Bei Zonenredundanz können eine oder mehrere Verfügbarkeitszonen ausfallen, aber der Datenpfad bleibt dennoch so lange verfügbar, wie eine Zone in der Region fehlerfrei bleibt. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Einrichten eines Load Balancers im Tarif „Standard“
> * Erstellen von Netzwerksicherheitsgruppen zum Definieren von Regeln für eingehenden Datenverkehr
> * Erstellen von zonenredundanten VMs übergreifend für mehrere Zonen und Anfügen an einen Load Balancer
> * Erstellen eines Integritätstests für den Load Balancer
> * Erstellen von Load Balancer-Regeln
> * Erstellen einer einfachen IIS-Website
> * Anzeigen eines Load Balancers im Betrieb

Informationen zur Verwendung von Verfügbarkeitszonen mit Load Balancer Standard finden Sie unter [Load Balancer Standard und Verfügbarkeitszonen](load-balancer-standard-availability-zones.md).

Sie können dieses Tutorial auch mit der [Azure-Befehlszeilenschnittstelle](./quickstart-load-balancer-standard-public-cli.md) durcharbeiten.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen. 

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="create-a-standard-load-balancer"></a>Einrichten eines Load Balancers im Tarif „Standard“

Ein Load Balancer im Standard-Tarif unterstützt nur eine öffentliche Standard-IP-Adresse. Wenn Sie beim Erstellen des Load Balancers eine neue öffentliche IP-Adresse erstellen, erfolgt automatisch eine Konfiguration mit der SKU „Standard“, und darüber hinaus ist der Load Balancer auch automatisch zonenredundant.

1. Klicken Sie links oben auf dem Bildschirm auf **Ressource erstellen** > **Netzwerk** > **Load Balancer**.
2. Geben Sie auf der Seite **Lastenausgleich erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, oder wählen Sie sie aus, übernehmen Sie die Standardwerte für die übrigen Einstellungen, und klicken Sie auf **Überprüfen + erstellen**:

    | Einstellung                 | Wert                                              |
    | ---                     | ---                                                |
    | Subscription               | Wählen Sie Ihr Abonnement aus.    |    
    | Resource group         | Wählen Sie **Neu erstellen**, und geben Sie *MyResourceGroupLBAZ* in das Textfeld ein.|
    | Name                   | *myLoadBalancer*                                   |
    | Region         | Wählen Sie **Europa, Westen** aus.                                        |
    | type          | Wählen Sie **Öffentlich** aus.                                        |
    | SKU           | Wählen Sie **Standard** aus.                          |
    | Öffentliche IP-Adresse | Wählen Sie **Neu erstellen**. |
    | Name der öffentlichen IP-Adresse              | Geben Sie *myPublicIP* in das Textfeld ein.   |
    |Verfügbarkeitszone| Wählen Sie **Zonenredundant** aus.    |
   

## <a name="create-backend-servers"></a>Erstellen von Back-End-Servern

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk und virtuelle Computer in unterschiedlichen Zonen für die Region und installieren anschließend IIS auf den virtuellen Computern, um den zonenredundanten Lastenausgleich zu testen. Fällt eine Zone aus, tritt infolgedessen ein Fehler beim Integritätstest für die VM in derselben Zone auf, und der Datenverkehr wird weiterhin von VMs in den anderen Zonen verarbeitet.

## <a name="virtual-network-and-parameters"></a>Virtuelles Netzwerk und Parameter

In den Schritten dieses Abschnitts müssen die folgenden Parameter wie folgt ersetzt werden:

| Parameter                   | Wert                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLBAZ (Wählen Sie die vorhandene Ressourcengruppe aus.) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | Europa, Westen      |
| **\<IPv4-address-space>**   | 10.0.0.0/16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.0.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

Erstellen Sie eine Netzwerksicherheitsgruppe, um eingehende Verbindungen für Ihr virtuelles Netzwerk zu definieren.

1. Klicken Sie auf der oberen linken Seite des Bildschirms auf **Ressource erstellen**, geben Sie in das Suchfeld *Netzwerksicherheitsgruppe* ein, und klicken Sie auf der Netzwerksicherheitsgruppe-Seite auf **Erstellen**.
2. Geben Sie auf der Seite „Netzwerksicherheitsgruppe erstellen“ die folgenden Werte ein:
    - *myNetworkSecurityGroup*: Für den Namen der Netzwerksicherheitsgruppe.
    - *myResourceGroupLBAZ*: Für den Namen der vorhandenen Ressourcengruppe.
   
![Screenshot, der den Bereich „Netzwerksicherheitsgruppe erstellen“ zeigt.](./media/load-balancer-standard-public-availability-zones-portal/create-nsg.png)

### <a name="create-network-security-group-rules"></a>Erstellen von Netzwerksicherheitsgruppen-Regeln

In diesem Abschnitt erstellen Sie Netzwerksicherheitsgruppen-Regeln, um eingehende HTTP- und RDP-Verbindungen mit dem Azure-Portal zuzulassen.

1. Klicken Sie im Azure-Portal im linken Menü auf **Alle Ressourcen**. Suchen Sie nach **myNetworkSecurityGroup**, und klicken Sie darauf (in der Ressourcengruppe **myResourceGroupLBAZ**).
2. Klicken Sie unter **Einstellungen** auf **Eingangssicherheitsregeln** und anschließend auf **Hinzufügen**.
3. Geben Sie für die Eingangssicherheitsregel *myHTTPRule* folgende Werte ein, um eine eingehende HTTP-Verbindung über den Port 80 zuzulassen:
    - *Service Tag* für **Quelle**
    - *Internet* für **Quelldiensttag**
    - *80* für **Zielportbereiche**
    - *TCP* für **Protokoll**
    - *Zulassen* für **Aktion**
    - *100* für **Priorität**
    - *myHTTPRule* als Name der Lastenausgleichsregel
    - *Allow HTTP* als Beschreibung der Lastenausgleichsregel
4. Klicken Sie auf **OK**.
 
   ![Screenshot des Bereichs „Eingangssicherheitsregel hinzufügen“.](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)
5. Wiederholen Sie die Schritte 2 bis 4, um eine weitere Regel namens *myRDPRule* zu erstellen und eine eingehende RDP-Verbindung über den Port 3389 zu ermöglichen. Verwenden Sie dabei die folgenden Werte:
    - *Service Tag* für **Quelle**
    - *Internet* für **Quelldiensttag**
    - *3389* für **Zielportbereiche**
    - *TCP* für **Protokoll**
    - *Zulassen* für **Aktion**
    - *200* für **Priorität**
    - *myRDPRule* als Name
    - *Allow RDP* als Beschreibung

### <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Erstellen Sie virtuelle Computer in unterschiedlichen Zonen (Zone 1, Zone 2 und Zone 3) für die Region, die als Back-End-Server für den Lastenausgleich dienen können.

1. Klicken Sie links oben auf dem Bildschirm auf **Ressource erstellen** > **Compute** > **Windows Server 2016 Datacenter**, und geben Sie folgende Werte für den virtuellen Computer ein:
    - *myVM1*: Name des virtuellen Computers        
    - *azureuser*: Name des Administratorbenutzers    
    - *myResourceGroupLBAZ*: Wählen Sie für **Ressourcengruppe** die Option **Vorhandene verwenden** und anschließend *myResourceGroupLBAZ* aus.
2. Klicken Sie auf **OK**.
3. Wählen Sie als Größe des virtuellen Computers **DS1_V2** aus, und klicken Sie auf **Auswählen**.
4. Geben Sie für die VM-Einstellungen folgende Werte ein:
    - *Zone 1*: Für die Zone, in der die VM platziert werden soll.
    -  *myVnet*: Vergewissern Sie sich, dass als virtuelles Netzwerk diese Option ausgewählt ist.
    - *myBackendSubnet*: Vergewissern Sie sich, dass als Subnetz diese Option ausgewählt ist.
    - *myNetworkSecurityGroup*: Für den Namen der Netzwerksicherheitsgruppe (Firewall).
5. Klicken Sie auf **Deaktiviert**, um die Startdiagnose zu deaktivieren.
6. Klicken Sie auf **OK**, überprüfen Sie die Einstellungen auf der Seite „Zusammenfassung“, und klicken Sie dann auf **Erstellen**.
7. Erstellen Sie anhand der Schritte 1–6 in Zone 2 eine zweite VM namens *VM2* und in Zone 3 eine dritte VM mit *myVnet* als virtuelles Netzwerk, *myBackendSubnet* als Subnetz und **myNetworkSecurityGroup* als Netzwerksicherheitsgruppe.

### <a name="install-iis-on-vms"></a>Installieren von IIS auf VMs

1. Klicken Sie im linken Menü auf **Alle Ressourcen** und anschließend in der Ressourcenliste auf **myVM1** (in der Ressourcengruppe *myResourceGroupLBAZ*).
2. Klicken Sie auf der Seite **Übersicht** auf **Verbinden**, um eine RDP-Verbindung mit dem virtuellen Computer herzustellen.
3. Melden Sie mit dem Benutzernamen *azureuser* bei dem virtuellen Computer.
4. Navigieren Sie auf dem Serverdesktop zu **Windows-Verwaltungsprogramme**>**Windows PowerShell**.
5. Führen Sie im PowerShell-Fenster die folgenden Befehle aus, um den IIS-Server zu installieren, die Standarddatei „iisstart.htm“ zu entfernen und dann eine neue Datei „iisstart.htm“ hinzuzufügen, die den Namen des virtuellen Computers enthält:
   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
6. Schließen Sie die RDP-Sitzung mit *myVM1*.
7. Wiederholen Sie die Schritte 1 bis 6, um IIS und die aktualisierte Datei „iisstart.htm“ auf *myVM2* und *myVM3* zu installieren.

## <a name="create-load-balancer-resources"></a>Erstellen von Load Balancer-Ressourcen

In diesem Abschnitt konfigurieren Sie Lastenausgleichseinstellungen für einen Back-End-Adresspool und einen Integritätstest. Außerdem geben Sie Lastenausgleichs- und NAT-Regeln an.


### <a name="create-a-backend-address-pool"></a>Erstellen eines Back-End-Adresspools

Zum Verteilen von Datenverkehr auf die virtuellen Computer enthält ein Back-End-Adresspool die IP-Adressen der virtuellen NICs, die mit dem Load Balancer verbunden sind. Erstellen Sie den Back-End-Adresspool *myBackendPool* mit *VM1*, *VM2* und *VM3*.

1. Klicken Sie im linken Menü auf **Alle Ressourcen** und dann in der Ressourcenliste auf **myLoadBalancer**.
2. Klicken Sie unter **Einstellungen** auf **Back-End-Pools** und anschließend auf **Hinzufügen**.
3. Gehen Sie auf der Seite **Back-End-Pool hinzufügen** wie folgt vor:
    - Geben Sie unter „Name“ die Zeichenfolge *myBackEndPool* als Name für Ihren Back-End-Pool ein.
    - Klicken Sie für **Virtuelles Netzwerk** im Dropdownmenü auf **myVNet**
    - Klicken Sie für **Virtueller Computer** im Dropdownmenü auf **myVM1**.
    - Klicken Sie für **IP-Adresse** im Dropdownmenü auf die IP-Adresse von myVM1.
4. Klicken Sie auf **Neue Back-End-Ressource hinzufügen**, um jeden virtuellen Computer  (*myVM2* und *myVM3*) zum Back-End-Pool der Load Balancer-Instanz hinzuzufügen.
5. Klicken Sie auf **Hinzufügen**.

    ![Hinzufügen zum Back-End-Adresspool](./media/load-balancer-standard-public-availability-zones-portal/add-backend-pool.png)

3. Vergewissern Sie sich, dass in der Back-End-Pool-Einstellung für Ihren Load Balancer alle drei VMs (**myVM1**, **myVM2** und **myVM3**) angezeigt werden.

### <a name="create-a-health-probe"></a>Erstellen eines Integritätstests

Damit der Load Balancer den Status Ihrer App überwachen kann, verwenden Sie einen Integritätstest. Abhängig von der Reaktion auf Integritätsüberprüfungen werden der Load Balancer-Rotation durch den Integritätstest dynamisch virtuelle Computer hinzugefügt oder daraus entfernt. Erstellen Sie zur Überwachung der Integrität der virtuellen Computer einen Integritätstest namens *myHealthProbe*.

1. Klicken Sie im linken Menü auf **Alle Ressourcen** und dann in der Ressourcenliste auf **myLoadBalancer**.
2. Klicken Sie unter **Einstellungen** auf **Integritätstests** und anschließend auf **Hinzufügen**.
3. Verwenden Sie folgende Werte, um den Integritätstest zu erstellen:
    - *myHealthProbe*: Name des Integritätstests
    - **HTTP**: Protokolltyp
    - *80*: Portnummer
    - *15*: Wert für **Intervall** (Sekunden zwischen Testversuchen)
    - *2*: Wert für **Fehlerschwellenwert** oder Anzahl aufeinander folgender Testfehler, die auftreten müssen, damit ein virtueller Computer als fehlerhaft eingestuft wird
4. Klicken Sie auf **OK**.

   ![Hinzufügen eines Tests](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Erstellen einer Load Balancer-Regel

Mithilfe einer Load Balancer-Regel wird definiert, wie Datenverkehr auf die virtuellen Computer verteilt werden soll. Sie definieren die Front-End-IP-Konfiguration für den eingehenden Datenverkehr und den Back-End-IP-Pool zum Empfangen des Datenverkehrs zusammen mit dem erforderlichen Quell- und Zielport. Erstellen Sie eine Lastenausgleichsregel namens *myLoadBalancerRuleWeb*, die an Port 80 des Front-Ends *FrontendLoadBalancer* lauscht und den Netzwerkdatenverkehr nach erfolgtem Lastenausgleich an den Back-End-Adresspool *myBackEndPool* sendet, wobei ebenfalls der Port 80 verwendet wird. 

1. Klicken Sie im linken Menü auf **Alle Ressourcen** und dann in der Ressourcenliste auf **myLoadBalancer**.
2. Klicken Sie unter **Einstellungen** auf **Lastenausgleichsregeln** und anschließend auf **Hinzufügen**.
3. Konfigurieren Sie die Lastenausgleichsregel mit folgenden Werten:
    - *myHTTPRule*: Name der Lastenausgleichsregel
    - **TCP**: Protokolltyp
    - *80*: Portnummer
    - *80*: Back-End-Port
    - *myBackendPool*: Name des Back-End-Pools
    - *myHealthProbe*: Name des Integritätstests
4. Klicken Sie auf **OK**.
    
    
    ![Hinzufügen einer Lastenausgleichsregel](./media/load-balancer-standard-public-availability-zones-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>Testen des Lastenausgleichs
1. Ermitteln Sie auf dem Bildschirm **Übersicht** die öffentliche IP-Adresse für den Load Balancer. Klicken Sie auf **Alle Ressourcen** und anschließend auf **myPublicIP**.

2. Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein. Die Standardseite des IIS-Webservers wird im Browser angezeigt.

      ![IIS-Webserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Sie können eine erzwungene Aktualisierung Ihres Webbrowsers durchführen, um zu verfolgen, wie der Lastenausgleich den Datenverkehr auf die in der Zone angeordneten VMs verteilt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, den Lastenausgleich und alle dazugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. Wählen Sie dazu die Ressourcengruppe aus, die den Lastenausgleich enthält, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über den Lastenausgleich für virtuelle Computer innerhalb einer bestimmten Verfügbarkeitszone:
> [!div class="nextstepaction"]
> [Tutorial: Durchführen eines Lastenausgleichs für virtuelle Computer innerhalb einer Verfügbarkeitszone mit Load Balancer Standard im Azure-Portal](tutorial-load-balancer-standard-public-zonal-portal.md)