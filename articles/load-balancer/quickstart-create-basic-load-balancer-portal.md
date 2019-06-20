---
title: 'Schnellstart: Erstellen eines öffentlichen Load Balancers im Tarif „Basic“ über das Portal'
titlesuffix: Azure Load Balancer
description: In dieser Schnellstartanleitung wird gezeigt, wie Sie über das Azure-Portal einen öffentlichen Load Balancer erstellen.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Basic Load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: kumud
ms.custom: seodec18
ms.openlocfilehash: db781899a3fe0d13d030943ed3ab4ebd3d105ad1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64727572"
---
# <a name="quickstart-create-a-basic-load-balancer-by-using-the-azure-portal"></a>Schnellstart: Erstellen eines Load Balancers im Tarif „Basic“ über das Azure-Portal

Durch die Verteilung der eingehenden Anforderungen auf virtuelle Computer (VMs) ermöglicht ein Lastenausgleich ein höheres Maß an Verfügbarkeit und Skalierbarkeit. Sie können das Azure-Portal verwenden, um einen Load Balancer zu erstellen und den Datenverkehr auf VMs zu verteilen. In dieser Schnellstartanleitung wird veranschaulicht, wie Sie einen Load Balancer, Back-End-Server und Netzwerkressourcen im Tarif „Basic“ erstellen und konfigurieren.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen. 

Melden Sie sich zur Durchführung der Aufgaben dieser Schnellstartanleitung am [Azure-Portal](https://portal.azure.com) an.

## <a name="create-a-basic-load-balancer"></a>Erstellen eines Load Balancers im Tarif „Basic“

Erstellen Sie zunächst einen öffentlichen Load Balancer im Tarif „Basic“ über das Portal. Der von Ihnen erstellte Name und die öffentliche IP-Adresse werden automatisch als Front-End des Load Balancers konfiguriert.

1. Klicken Sie links oben auf dem Bildschirm auf **Ressource erstellen** > **Netzwerk** > **Load Balancer**.
2. Geben Sie auf der Seite **Lastenausgleich erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, oder wählen Sie sie aus, übernehmen Sie die Standardwerte für die übrigen Einstellungen, und klicken Sie auf **Überprüfen + erstellen**:

    | Einstellung                 | Wert                                              |
    | ---                     | ---                                                |
    | Abonnement               | Wählen Sie Ihr Abonnement aus.    |    
    | Ressourcengruppe         | Wählen Sie **Neu erstellen**, und geben Sie *MyResourceGroupLB* in das Textfeld ein.|
    | NAME                   | *myLoadBalancer*                                   |
    | Region         | Wählen Sie **Europa, Westen** aus.                                        |
    | Type          | Wählen Sie **Öffentlich** aus.                                        |
    | SKU           | Wählen Sie **Basic** aus.                          |
    | Öffentliche IP-Adresse | Wählen Sie **Neu erstellen**. |
    | Name der öffentlichen IP-Adresse              | *MyPublicIP*   |
    | Zuweisung| statischen|

3. Klicken Sie auf der Registerkarte **Überprüfen + erstellen** auf **Erstellen**.   


## <a name="create-back-end-servers"></a>Erstellen von Back-End-Servern

Erstellen Sie als Nächstes ein virtuelles Netzwerk sowie zwei virtuelle Computer für den Back-End-Pool Ihres Load Balancers im Tarif „Basic“. 

### <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

1. Wählen Sie oben links im Portal **Ressource erstellen** > **Netzwerk** > **Virtuelles Netzwerk**.
   
1. Geben Sie im Bereich **Virtuelles Netzwerk erstellen** diese Werte ein (bzw. wählen Sie sie aus):
   
   - **Name**: Geben Sie *MyVnet* ein.
   - **Ressourcengruppe**: Öffnen Sie die Dropdownliste **Vorhandene auswählen**, und wählen Sie **MyResourceGroupLB** aus. 
   - **Subnetz** > **Name**: Geben Sie *MyBackendSubnet* ein.
   
1. Klicken Sie auf **Erstellen**.

   ![Erstellen eines virtuellen Netzwerks](./media/load-balancer-get-started-internet-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

1. Wählen Sie oben links im Portal die Option **Ressource erstellen** > **Compute** > **Windows Server 2016 Datacenter**. 
   
1. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Werte ein (bzw. wählen Sie sie aus):
   - **Abonnement** > **Ressourcengruppe**: Öffnen Sie die Dropdownliste, und wählen Sie **MyResourceGroupLB** aus.
   - **Instanzendetails** > **Name des virtuellen Computers**: Geben Sie *MyVM1* ein.
   - **Instanzendetails** > **Verfügbarkeitsoptionen**: 
     1. Öffnen Sie die Dropdownliste, und wählen Sie die Option **Verfügbarkeitsgruppe** aus. 
     2. Wählen Sie **Neu erstellen**, geben Sie *MyAvailabilitySet* ein, und wählen Sie **OK**.
  
1. Wählen Sie die Registerkarte **Netzwerk** aus, oder wählen Sie **Weiter: Datenträger** und anschließend **Weiter: Netzwerk** aus. 
   
   Stellen Sie sicher, dass Folgendes ausgewählt ist:
   - **Virtuelles Netzwerk**: **MyVnet**
   - **Subnetz**: **MyBackendSubnet**
   - **Öffentliche IP**: **MyVM1-ip**
   
   Wählen Sie zum Erstellen einer neuen Netzwerksicherheitsgruppe (NSG) – einer Art Firewall – unter **Netzwerksicherheitsgruppe** die Option **Erweitert**. 
   1. Wählen Sie im Feld **Netzwerksicherheitsgruppe konfigurieren** die Option **Neu erstellen**. 
   1. Geben Sie *MyNetworkSecurityGroup* ein, und wählen Sie **OK**. 
   
1. Wählen Sie die Registerkarte **Verwaltung** oder **Weiter** > **Verwaltung**. Legen Sie unter **Überwachung** die Option **Startdiagnose** auf **Aus** fest.
   
1. Klicken Sie auf **Überprüfen + erstellen**.
   
1. Überprüfen Sie die Einstellungen, und wählen Sie dann die Option **Erstellen**. 

1. Führen Sie die Schritte zum Erstellen einer zweiten VM mit dem Namen *MyVM2* mit der **öffentlichen IP-Adresse** *MyVM2-ip* aus, und verwenden Sie für alle anderen Einstellungen die Werte wie für MyVM1. 

### <a name="create-nsg-rules-for-the-vms"></a>Erstellen von NSG-Regeln für die virtuellen Computer

In diesem Abschnitt erstellen Sie Netzwerksicherheitsgruppen-Regeln für die VMs, um Internetverbindungen (HTTP) und Remotedesktopverbindungen (RDP) in eingehender Richtung zuzulassen.

1. Wählen Sie im linken Menü die Option **Alle Ressourcen**. Wählen Sie in der Ressourcenliste in der Ressourcengruppe **MyResourceGroupLB** die Option **MyNetworkSecurityGroup**.
   
1. Wählen Sie unter **Einstellungen** die Option **Eingangssicherheitsregeln** und dann **Hinzufügen**.
   
1. Geben Sie im Dialogfeld **Eingangssicherheitsregel hinzufügen** für die HTTP-Regel folgende Werte ein (bzw. wählen Sie sie aus):
   
   - **Quelle**: Wählen Sie **Diensttag** aus.  
   - **Quelldiensttag**: Wählen Sie **Internet** aus. 
   - **Zielportbereiche**: Geben Sie *80* ein.
   - **Protokoll**: Wählen Sie **TCP** aus. 
   - **Aktion**: Wählen Sie **Zulassen** aus.  
   - **Priorität**: Geben Sie *100* ein. 
   - **Name**: Geben Sie *MyHTTPRule* ein. 
   - **Beschreibung:** Geben Sie *Allow HTTP* ein. 
   
1. Wählen Sie **Hinzufügen**. 
   
   ![Erstellen einer NSG-Regel](./media/load-balancer-get-started-internet-portal/8-load-balancer-nsg-rules.png)
   
1. Wiederholen Sie die Schritte für die RDP-Eingangsregel mit den folgenden abweichenden Werten:
   - **Zielportbereiche**: Geben Sie *3389* ein.
   - **Priorität**: Geben Sie *200* ein. 
   - **Name**: Geben Sie *MyRDPRule* ein. 
   - **Beschreibung:** Geben Sie *Allow RDP* ein. 

## <a name="create-resources-for-the-load-balancer"></a>Erstellen von Ressourcen für den Load Balancer

In diesem Abschnitt konfigurieren Sie Load Balancer-Einstellungen für einen Back-End-Adresspool, einen Integritätstest und eine Lastenausgleichsregel.

### <a name="create-a-backend-address-pool"></a>Erstellen eines Back-End-Adresspools

Zum Verteilen von Datenverkehr auf die VMs nutzt der Load Balancer einen Back-End-Adresspool. Der Back-End-Adresspool enthält die IP-Adressen der virtuellen Netzwerkschnittstellen (NICs), die mit dem Load Balancer verbunden sind. 

**Erstellen Sie wie folgt einen Back-End-Adresspool, der VM1 und VM2 enthält:**

1. Wählen Sie im Menü auf der linken Seite die Option **Alle Ressourcen** und dann in der Ressourcenliste die Option **MyLoadBalancer**.
   
1. Wählen Sie unter **Einstellungen** die Option **Back-End-Pools** und dann **Hinzufügen**.
   
1. Geben Sie auf der Seite **Back-End-Pool hinzufügen** die folgenden Werte ein (bzw. wählen Sie sie aus):
   
   - **Name**: Geben Sie *MyBackEndPool* ein.
   - **Verknüpft mit**: Öffnen Sie die Dropdownliste, und wählen Sie die Option **Verfügbarkeitsgruppe** aus.
   - **Verfügbarkeitsgruppe**: Wählen Sie **MyAvailabilitySet** aus.
   
1. Wählen Sie **Zielnetzwerk-IP-Konfiguration hinzufügen**. 
   1. Fügen Sie alle virtuellen Computer (**MyVM1** und **MyVM2**), die Sie erstellt haben, dem Back-End-Pool hinzu.
   2. Öffnen Sie nach dem Hinzufügen eines Computers jeweils die Dropdownliste, und wählen Sie die **Netzwerk-IP-Konfiguration** aus. 
   
1. Klicken Sie auf **OK**.
   
   ![Hinzufügen des Back-End-Adresspools](./media/load-balancer-get-started-internet-portal/3-load-balancer-backend-02.png)
   
1. Erweitern Sie auf der Seite **Back-End-Pools** den Eintrag **MyBackendPool**, und stellen Sie sicher, dass sowohl **VM1** als auch **VM2** aufgeführt ist.

### <a name="create-a-health-probe"></a>Erstellen eines Integritätstests

Damit der Load Balancer den VM-Status überwachen kann, verwenden Sie einen Integritätstest. Abhängig von der Reaktion auf Integritätsüberprüfungen werden der Load Balancer-Rotation durch den Integritätstest dynamisch virtuelle Computer hinzugefügt oder daraus entfernt. 

**Erstellen Sie zur Überwachung der Integrität der virtuellen Computer wie folgt einen Integritätstest:**

1. Wählen Sie im Menü auf der linken Seite die Option **Alle Ressourcen** und dann in der Ressourcenliste die Option **MyLoadBalancer**.
   
1. Wählen Sie unter **Einstellungen** die Option **Integritätstests** und dann **Hinzufügen**.
   
1. Geben Sie auf der Seite **Integritätstest hinzufügen** die folgenden Werte ein (bzw. wählen Sie sie aus):
   
   - **Name**: Geben Sie *MyHealthProbe* ein.
   - **Protokoll**: Öffnen Sie die Dropdownliste, und wählen Sie **HTTP** aus. 
   - **Port**: Geben Sie *80* ein. 
   - **Pfad**: Übernehmen Sie */* als Standard-URI. Sie können diesen Wert durch einen beliebigen anderen URI ersetzen. 
   - **Intervall**: Geben Sie *15* ein. Das Intervall ist die Anzahl von Sekunden zwischen Testversuchen.
   - **Fehlerschwellenwert**: Geben Sie *2* ein. Dieser Wert gibt die Anzahl aufeinander folgender Testfehler an, die auftreten müssen, damit ein virtueller Computer als fehlerhaft eingestuft wird.
   
1. Klicken Sie auf **OK**.
   
   ![Hinzufügen eines Tests](./media/load-balancer-get-started-internet-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>Erstellen einer Load Balancer-Regel

Mit einer Lastenausgleichsregel wird definiert, wie Datenverkehr auf die virtuellen Computer verteilt wird. Die Regel definiert die Front-End-IP-Konfiguration für eingehenden Datenverkehr und den Back-End-IP-Pool zum Empfangen des Datenverkehrs sowie die erforderlichen Quell- und Zielports. 

Mit der Lastenausgleichsregel **MyLoadBalancerRule** wird über Port 80 des Front-Ends **LoadBalancerFrontEnd** gelauscht. Die Regel sendet Netzwerkdatenverkehr an den Back-End-Adresspool **MyBackEndPool** (ebenfalls unter Port 80). 

**Erstellen Sie die Lastenausgleichsregel wie folgt:**


1. Wählen Sie im Menü auf der linken Seite die Option **Alle Ressourcen** und dann in der Ressourcenliste die Option **MyLoadBalancer**.
   
1. Wählen Sie unter **Einstellungen** die Option **Lastenausgleichsregeln** und dann **Hinzufügen**.
   
1. Geben Sie auf der Seite **Lastenausgleichsregel hinzufügen** die folgenden Werte ein (bzw. wählen Sie sie aus):
   
   - **Name**: Geben Sie *MyLoadBalancerRule* ein.
   - **Front-End-IP-Adresse**: Geben Sie *LoadBalancerFrontend* ein.
   - **Protokoll**: Wählen Sie **TCP** aus.
   - **Port**: Geben Sie *80* ein.
   - **Back-End-Port**: Geben Sie *80* ein.
   - **Back-End-Pool**: Wählen Sie **MyBackendPool** aus.
   - **Integritätstest**: Wählen Sie **MyHealthProbe** aus. 
   
1. Klicken Sie auf **OK**.
   
   ![Hinzufügen einer Lastenausgleichsregel](./media/load-balancer-get-started-internet-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>Testen des Lastenausgleichs

Sie verwenden die öffentliche IP-Adresse, um den Lastenausgleich auf den virtuellen Computern zu testen. 

Ermitteln Sie im Portal auf der Seite **Übersicht** für **MyLoadBalancer** unter **Öffentliche IP-Adresse** die öffentliche IP-Adresse. Zeigen Sie mit der Maus auf die Adresse, und wählen Sie das Symbol **Kopieren**, um sie zu kopieren. 

### <a name="install-iis-on-the-vms"></a>Installieren von IIS auf den virtuellen Computern

Installieren Sie Internetinformationsdienste (IIS) auf den virtuellen Computern, um das Testen des Load Balancers zu unterstützen.

**Stellen Sie die Remotedesktopverbindung (RDP) mit der VM wie folgt her:**

1. Wählen Sie im Portal im Menü auf der linken Seite die Option **Alle Ressourcen**. Wählen Sie in der Ressourcenliste in der Ressourcengruppe **MyResourceGroupLB** die Option **MyVM1**.
   
1. Wählen Sie auf der Seite **Übersicht** die Option **Verbinden** und dann **RDP-Datei herunterladen**. 
   
1. Öffnen Sie die heruntergeladene RDP-Datei, und wählen Sie **Verbinden**.
   
1. Wählen Sie auf dem Bildschirm „Windows-Sicherheit“ die Option **Weitere Optionen** und dann **Anderes Konto verwenden**. 
   
   Geben Sie Benutzername und Kennwort ein, und wählen Sie **OK** aus.
   
1. Wählen Sie für alle Eingabeaufforderungen zu Zertifikaten die Antwort **Ja**. 
   
   Der VM-Desktop wird in einem neuen Fenster geöffnet. 
   
**So installieren Sie IIS:**

1. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myVM1** (in der Ressourcengruppe *myResourceGroupSLB*) aus.
2. Wählen Sie auf der Seite **Übersicht** die Option **Verbinden**, um eine RDP-Verbindung mit dem virtuellen Computer herzustellen.
5. Melden Sie sich mit den Anmeldeinformationen, die Sie während der Erstellung dieses virtuellen Computers bereitgestellt haben, beim virtuellen Computer an. Dadurch wird eine Remotedesktopsitzung mit dem virtuellen Computer *myVM1* gestartet.
6. Navigieren Sie auf dem Serverdesktop zu **Windows-Verwaltungsprogramme**>**Windows PowerShell**.
7. Führen Sie im PowerShell-Fenster die folgenden Befehle aus, um den IIS-Server zu installieren, die Standarddatei „iisstart.htm“ zu entfernen und dann eine neue Datei „iisstart.htm“ hinzuzufügen, die den Namen des virtuellen Computers enthält:

   ```azurepowershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
    remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. Schließen Sie die RDP-Sitzung mit *myVM1*.
7. Wiederholen Sie die Schritte 1 bis 6, um IIS und die aktualisierte Datei „iisstart.htm“ auf *myVM2* zu installieren.
   
1. Wiederholen Sie die Schritte für den virtuellen Computer **MyVM2**, aber legen Sie den Zielserver dieses Mal auf **MyVM2** fest.

### <a name="test-the-load-balancer"></a>Testen des Lastenausgleichs

Öffnen Sie einen Browser, und fügen Sie die öffentliche IP-Adresse Ihres Lastenausgleichs in die Adressleiste ein. Im Browser sollte die Standardseite des IIS-Webservers angezeigt werden.

![IIS-Webserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Sie können eine erzwungene Aktualisierung Ihres Webbrowsers durchführen, um zu verfolgen, wie der Load Balancer den Datenverkehr auf die beiden virtuellen Computer verteilt, auf denen Ihre App ausgeführt wird.
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Öffnen Sie die Ressourcengruppe **MyResourceGroupLB**, und wählen Sie die Option **Ressourcengruppe löschen**, um den Load Balancer und alle zugehörigen Ressourcen zu löschen, sofern Sie sie nicht mehr benötigen.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Load Balancer im Tarif „Basic“ erstellt. Sie haben eine Ressourcengruppe, Netzwerkressourcen, Back-End-Server, einen Integritätstest und Regeln für den Load Balancer erstellt und konfiguriert. Sie haben IIS auf den VMs installiert und verwendet, um den Load Balancer zu testen. 

Weitere Informationen zu Azure Load Balancer finden Sie in den Tutorials.

> [!div class="nextstepaction"]
> [Azure Load Balancer-Tutorials](tutorial-load-balancer-basic-internal-portal.md)
