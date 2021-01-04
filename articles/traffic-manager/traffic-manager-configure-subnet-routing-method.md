---
title: Konfigurieren des Routings für Subnetzdatenverkehr – Azure Traffic Manager
description: In diesem Artikel wird erläutert, wie der Traffic Manager so konfiguriert wird, dass Datenverkehr von bestimmten Subnetzen weitergeleitet wird.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: duau
ms.openlocfilehash: b1901ddce2eb9c8ff5ec9ac90a56379e74c11aa6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994892"
---
# <a name="direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Weiterleiten von Datenverkehr an bestimmte Endpunkte mit dem Traffic Manager basierend auf einem Benutzersubnetz

In diesem Artikel wird das Konfigurieren der Routingmethode für Subnetzdatenverkehr beschrieben. Mit der Datenverkehrsrouting-Methode **Subnetz** können Sie IP-Adressbereiche bestimmten Endpunkten zuordnen. Wenn eine Anforderung vom Traffic Manager empfangen wird, wird die Quell-IP-Adresse der Anforderung überprüft und der damit verbundene Endpunkt zurückgegeben.

In dem Szenario, das im vorliegenden Artikel beschrieben wird, wird abhängig von der IP-Adresse der Benutzerabfrage der Datenverkehr unter Verwendung von Subnetzrouting entweder an eine interne Website oder an eine Produktionswebsite weitergeleitet.

Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen
Um den Traffic Manager in Aktion sehen zu können, müssen Sie in diesem Tutorial Folgendes bereitstellen:
- Zwei grundlegende Websites, die in verschiedenen Azure-Regionen ausgeführt werden: **USA, Osten** (interne Website) und **Europa, Westen** (Produktionswebsite)
- zwei Test-VMs zum Testen des Traffic Managers – ein virtueller Computer in **USA, Osten** und der zweite virtuelle Computer in **Europa, Westen**.

Mit den Test-VMs wird veranschaulicht, wie der Traffic Manager basierend auf dem Subnetz, aus dem die Benutzerabfrage stammt, Benutzerdatenverkehr an die interne Website oder die Produktionswebsite weiterleitet.

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

### <a name="create-websites"></a>Erstellen von Websites

In diesem Abschnitt erstellen Sie zwei Websiteinstanzen, die die zwei Dienstendpunkte für das Traffic Manager-Profil in zwei Azure-Regionen bereitstellen. Das Erstellen der beiden Websites umfasst die folgenden Schritte:
1. Erstellen Sie zwei VMs für die Ausführung einer Standardwebsite – eine in **USA, Osten** und die andere in **Europa, Westen**.
2. Installieren Sie den IIS-Server auf jedem virtuellen Computer und aktualisieren Sie die Standardwebsiteseite, die den Namen der VM beschreibt, mit der ein Benutzer beim Besuch der Website verbunden ist.

#### <a name="create-vms-for-running-websites"></a>Erstellen von virtuellen Computern für die Ausführung von Websites
In diesem Abschnitt erstellen Sie die beiden VMs *myEndpointVMEastUS* und *myEndpointVMWEurope* in den Azure-Regionen **USA, Osten** und **Europa, Westen**.

1. Wählen Sie im Azure-Portal links oben **Ressource erstellen** > **Compute** > **Windows Server 2016 VM** aus.
2. Geben Sie die folgenden Informationen für **Grundlagen** ein, oder wählen Sie sie aus, übernehmen Sie die Standardwerte für die übrigen Einstellungen, und klicken Sie auf **Erstellen**:

    |Einstellung|value|
    |---|---|
    |Name|myIISVMEastUS|
    |Benutzername| Geben Sie den gewünschten Benutzernamen ein.|
    |Kennwort| Geben Sie das gewünschte Kennwort ein. Das Kennwort muss mindestens zwölf Zeichen lang sein und die [definierten Anforderungen an die Komplexität](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) erfüllen.|
    |Resource group| Klicken Sie auf **Neu**, und geben Sie *myResourceGroupTM1* ein.|
    |Location| Wählen Sie **USA, Osten** aus.|
    |||

4. Wählen Sie unter **Größe auswählen** eine VM-Größe aus.
5. Wählen Sie die folgenden Werte unter **Einstellungen** aus, und wählen Sie anschließend **OK**:
    
    |Einstellung|value|
    |---|---|
    |Virtuelles Netzwerk| Wählen Sie **Virtuelles Netzwerk** in **Virtuelles Netzwerk erstellen** aus, und geben Sie *myVNet1* für **Name** und *mySubnet* für das Subnetz ein.|
    |Netzwerksicherheitsgruppen (NSG)|Wählen Sie **Standard** aus, und wählen Sie in der Dropdownliste **Öffentliche Eingangsports hinzufügen** die Optionen **HTTP** und **RDP** aus. |
    |Startdiagnose|Wählen Sie **Deaktiviert** aus.|
    |||

6. Klicken Sie auf der Seite **Zusammenfassung** unter **Erstellen** auf **Erstellen**, um die Bereitstellung der VM zu starten.

7. Führen Sie die Schritte 1 bis 6 mit den folgenden Änderungen erneut aus:

    |Einstellung|value|
    |---|---|
    |Resource group | Klicken Sie auf **Neu**, und geben Sie *myResourceGroupTM2* ein.|
    |Location|Europa, Westen|
    |VM-Name | myIISVMWEurope|
    |Virtuelles Netzwerk | Wählen Sie **Virtuelles Netzwerk** in **Virtuelles Netzwerk erstellen** aus, und geben Sie *myVNet2* für **Name** und *mySubnet* für das Subnetz ein.|
    |||

8. Die Erstellung der VMs kann einige Minuten dauern. Fahren Sie mit den restlichen Schritten erst fort, nachdem beide VMs erstellt wurden.

   ![Erstellen einer VM](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Installieren von IIS und Anpassen der Standardwebseite

In diesem Abschnitt Installieren Sie den IIS-Server auf den beiden virtuellen Computern – *myIISVMEastUS*  & *myIISVMWEurope* – und aktualisieren dann die Standardwebsiteseite. Die benutzerdefinierte Websiteseite zeigt den Namen des virtuellen Computers an, mit dem Sie eine Verbindung herstellen, wenn Sie die Website über einen Webbrowser besuchen.

1. Wählen Sie im linken Menü **Alle Ressourcen** aus, und klicken Sie anschließend in der Ressourcenliste auf *myIISVMEastUS* in der Ressourcengruppe *myResourceGroupTM1*.
2. Klicken Sie auf der Seite **Übersicht** auf **Verbinden**, und wählen Sie dann in **Herstellen einer Verbindung mit dem virtuellen Computer** die Option **RDP-Datei herunterladen** aus.
3. Öffnen Sie die heruntergeladene RDP-Datei. Wenn Sie dazu aufgefordert werden, wählen Sie **Verbinden** aus. Geben Sie den Benutzernamen und das Kennwort ein, die Sie beim Erstellen des virtuellen Computers festgelegt haben. Unter Umständen müssen Sie auf **Weitere Optionen** und anschließend auf **Anderes Konto verwenden** klicken, um die Anmeldeinformationen anzugeben, die Sie beim Erstellen des virtuellen Computers eingegeben haben.
4. Klicken Sie auf **OK**.
5. Während des Anmeldevorgangs wird unter Umständen eine Zertifikatwarnung angezeigt. Wenn eine Warnung angezeigt wird, klicken Sie auf **Ja** bzw. **Weiter**, um mit dem Herstellen der Verbindung fortzufahren.
6. Navigieren Sie auf dem Serverdesktop zu **Windows-Verwaltungsprogramme**>**Server-Manager**.
7. Starten Sie Windows PowerShell auf *myIISVMEastUS*, und verwenden Sie die folgenden Befehle, um den IIS-Server zu installieren und die HTM-Standarddatei zu aktualisieren.
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my test website server - " + $env:computername)
    ```
8. Schließen Sie die RDP-Verbindung mit *myIISVMEastUS*.
9. Wiederholen Sie die Schritte 1 bis 6 mit dem Herstellen einer RDP-Verbindung mit der VM *myIISVMWEurope* innerhalb der Ressourcengruppe *myResourceGroupTM2*, um die IIS zu installieren und die zugehörige Standardwebseite anzupassen.
10. Starten Sie Windows PowerShell auf *myIISVMWEurope*, und verwenden Sie die folgenden Befehle, um den IIS-Server zu installieren und die HTM-Standarddatei zu aktualisieren.
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my production website server - " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurieren von DNS-Namen für die virtuellen Computer, die IIS ausführen

Basierend auf DNS-Namen der Dienstendpunkte leitet Traffic Manager den Benutzerdatenverkehr weiter. In diesem Abschnitt konfigurieren Sie die DNS-Namen für die IIS-Server: *myIISVMEastUS* und *myIISVMWEurope*.

1. Klicken Sie im linken Menü auf **Alle Ressourcen**, und wählen Sie dann in der Ressourcenliste *myIISVMEastUS* in der Ressourcengruppe *myResourceGroupTM1* aus.
2. Wählen Sie auf der Seite **Übersicht** unter **DNS-Name** die Option **Konfigurieren** aus.
3. Fügen Sie auf der Seite **Konfiguration** als DNS-Namen einen eindeutigen Namen ein, und wählen Sie dann **Speichern**.
4. Wiederholen Sie die Schritte 1 bis 3 für den virtuellen Computer mit dem Namen *myIISVMWEurope*, der sich in der Ressourcengruppe *myResourceGroupTM1* befindet.

### <a name="create-test-vms"></a>Erstellen von Test-VMs

In diesem Abschnitt erstellen Sie je einen virtuellen Computer (*mVMEastUS* und *myVMWestEurope*) in den Azure-Regionen **USA, Osten** und **Europa, Westen**. Mit diesen virtuellen Computern testen Sie, wie Traffic Manager Datenverkehr zum nächsten IIS-Server weiterleitet, wenn Sie zu der Website browsen.

1. Wählen Sie im Azure-Portal links oben **Ressource erstellen** > **Compute** > **Windows Server 2016 VM** aus.
2. Geben Sie die folgenden Informationen für **Grundlagen** ein, oder wählen Sie sie aus, übernehmen Sie die Standardwerte für die übrigen Einstellungen, und klicken Sie auf **Erstellen**:

    |Einstellung|value|
    |---|---|
    |Name|myVMEastUS|
    |Benutzername| Geben Sie den gewünschten Benutzernamen ein.|
    |Kennwort| Geben Sie das gewünschte Kennwort ein. Das Kennwort muss mindestens zwölf Zeichen lang sein und die [definierten Anforderungen an die Komplexität](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) erfüllen.|
    |Resource group| Wählen Sie **Vorhandene** und dann *myResourceGroupTM1* aus.|
    |||

4. Wählen Sie unter **Größe auswählen** eine VM-Größe aus.
5. Wählen Sie die folgenden Werte unter **Einstellungen** aus, und wählen Sie anschließend **OK**:

    |Einstellung|value|
    |---|---|
    |Virtuelles Netzwerk| Wählen Sie **Virtuelles Netzwerk** unter **Virtuelles Netzwerk erstellen** aus, und geben Sie *myVNet3* für **Name** und *mySubnet3* für das Subnetz ein.|
    |Netzwerksicherheitsgruppen (NSG)|Wählen Sie **Standard** aus, und wählen Sie in der Dropdownliste **Öffentliche Eingangsports hinzufügen** die Optionen **HTTP** und **RDP** aus. |
    |Startdiagnose|Wählen Sie **Deaktiviert** aus.|
    |||

6. Klicken Sie auf der Seite **Zusammenfassung** unter **Erstellen** auf **Erstellen**, um die Bereitstellung der VM zu starten.

7. Führen Sie die Schritte 1 bis 5 mit den folgenden Änderungen erneut aus:

    |Einstellung|value|
    |---|---|
    |VM-Name | *myVMWEurope*|
    |Resource group | Wählen Sie **Vorhandene** und dann *myResourceGroupTM2* aus.|
    |Virtuelles Netzwerk | Wählen Sie **Virtuelles Netzwerk** unter **Virtuelles Netzwerk erstellen** aus, und geben Sie *myVNet4* für **Name** und *mySubnet4* für das Subnetz ein.|
    |||

8. Die Erstellung der VMs kann einige Minuten dauern. Fahren Sie mit den restlichen Schritten erst fort, nachdem beide VMs erstellt wurden.

## <a name="create-a-traffic-manager-profile"></a>Erstellen eines Traffic Manager-Profils
Erstellen Sie ein Traffic Manager-Profil, mit dem Sie basierend auf der Quell-IP-Adresse der Anforderung bestimmte Endpunkte zurückgeben können.

1. Klicken Sie links oben auf dem Bildschirm auf **Ressource erstellen** > **Netzwerk** > **Traffic Manager-Profil** > **Erstellen**.
2. Geben Sie unter **Traffic Manager-Profil erstellen** die folgenden Informationen ein, oder wählen Sie sie aus, übernehmen Sie die Standardwerte für die übrigen Einstellungen, und klicken Sie auf **Erstellen**:

    | Einstellung                 | value                                              |
    | ---                     | ---                                                |
    | Name                   | Dieser Name muss innerhalb der Zone „trafficmanager.net“ eindeutig sein und ergibt den DNS-Namen „trafficmanager.net“, der für den Zugriff auf Ihr Traffic Manager-Profil verwendet wird.                                   |
    | Routingmethode          | Wählen Sie als Routingmethode **Subnetz** aus.                                       |
    | Subscription            | Wählen Sie Ihr Abonnement aus.                          |
    | Resource group          | Wählen Sie **Vorhanden** aus, und geben Sie *myResourceGroupTM1* ein. |
    | |                              |
    |

    ![Erstellen eines Traffic Manager-Profils](./media/traffic-manager-subnet-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Hinzufügen von Traffic Manager-Endpunkten

Fügen Sie die beiden VMs mit den IIS-Servern (*myIISVMEastUS* & *myIISVMWEurope*) hinzu, um Benutzerdatenverkehr basierend auf dem Subnetz der Benutzerabfrage weiterzuleiten.

1. Suchen Sie in der Suchleiste des Portals nach dem Namen des Traffic Manager-Profils, das Sie im vorhergehenden Abschnitt erstellt haben, und wählen Sie in den angezeigten Ergebnissen das Profil aus.
2. Klicken Sie unter **Traffic Manager-Profil** im Abschnitt **Einstellungen** auf **Endpunkte** und dann auf **Hinzufügen**.
3. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus, übernehmen Sie die Standardwerte für die übrigen Einstellungen, und klicken Sie auf **OK**:

    | Einstellung                 | value                                              |
    | ---                     | ---                                                |
    | type                    | Azure-Endpunkt                                   |
    | Name           | myTestWebSiteEndpoint                                        |
    | Zielressourcentyp           | Öffentliche IP-Adresse                          |
    | Zielressource          | **Wählen Sie eine öffentliche IP-Adresse aus**, um die Liste der Ressourcen mit öffentlichen IP-Adressen im gleichen Abonnement anzuzeigen. Wählen Sie in **Ressource** die öffentliche IP-Adresse mit dem Namen *myIISVMEastUS-ip* aus. Dies ist die öffentliche IP-Adresse der IIS-Server-VM in „USA, Osten“.|
    |  Einstellungen für das Subnetzrouting    |   Fügen Sie die IP-Adresse der Test-VM *myVMEastUS* hinzu. Jede Benutzerabfrage von dieser VM wird an *myTestWebSiteEndpoint* weitergeleitet.    |

4. Wiederholen Sie die Schritte 2 und 3, um einen anderen Endpunkt mit dem Namen *myProductionEndpoint* für die öffentliche IP-Adresse *myIISVMWEurope-ip* hinzuzufügen, die der IIS-Server-VM mit dem Namen *myIISVMWEurope* zugeordnet ist. Fügen Sie unter **Einstellungen für das Subnetzrouting** die IP-Adresse der Test-VM *myVMWestEurope* hinzu. Jede Benutzerabfrage von dieser Test-VM wird an den Endpunkt *myProductionWebsiteEndpoint* weitergeleitet.
5. Wenn Sie das Hinzufügen beider Endpunkte abgeschlossen haben, werden diese unter **Traffic Manager-Profil** zusammen mit ihrem Überwachungsstatus als **Online** angezeigt.

    ![Hinzufügen eines Traffic Manager-Endpunkts](./media/traffic-manager-subnet-routing-method/customize-endpoint-with-subnet-routing-eastus.png)

## <a name="test-traffic-manager-profile"></a>Testen des Traffic Manager-Profils
In diesem Abschnitt testen Sie, wie der Traffic Manager Benutzerdatenverkehr von einem bestimmten Subnetz an einen bestimmten Endpunkt weiterleitet. Um den Traffic Manager in Aktion anzuzeigen, führen Sie die folgenden Schritte aus:
1. Bestimmen Sie den DNS-Namen des Traffic Manager-Profils.
2. Zeigen Sie den Traffic Manager folgendermaßen in Aktion an:
    - Browsen Sie von der Test-VM (*myVMEastUS*) aus, die sich in der Region **USA, Osten** befindet, in einem Webbrowser zu dem DNS-Namen des Traffic Manager-Profils.
    - Browsen Sie von der Test-VM (*myVMWestEurope*) aus, die sich in der Region **Europa, Westen** befindet, in einem Webbrowser zu dem DNS-Namen des Traffic Manager-Profils.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Bestimmen des DNS-Namens des Traffic Manager-Profils
In diesem Tutorial verwenden Sie der Einfachheit halber den DNS-Namen des Traffic Manager-Profils, um die Websites zu besuchen.

Sie können den DNS-Namen des Traffic Manager-Profils folgendermaßen bestimmen:

1. Suchen Sie in der Suchleiste des Portals nach dem Namen des **Traffic Manager-Profils**, das Sie im vorhergehenden Abschnitt erstellt haben. Klicken Sie in den angezeigten Ergebnissen auf das Traffic Manager-Profil.
1. Klicken Sie auf **Overview**.
2. Unter **Traffic Manager-Profil** wird der DNS-Name Ihres neu erstellten Traffic Manager-Profils angezeigt. In Produktionsbereitstellungen konfigurieren Sie mithilfe eines DNS-CNAME-Eintrags einen benutzerdefinierten Domänennamen für den Verweis auf den Traffic Manager-Domänennamen.

   ![Traffic Manager-DNS-Name](./media/traffic-manager-subnet-routing-method/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Anzeigen von Traffic Manager in Aktion
In diesem Abschnitt sehen Sie den Traffic Manager in Aktion.

1. Wählen Sie im linken Menü **Alle Ressourcen** aus, und klicken Sie anschließend in der Ressourcenliste auf *myVMEastUS* in der Ressourcengruppe *myResourceGroupTM1*.
2. Klicken Sie auf der Seite **Übersicht** auf **Verbinden**, und wählen Sie dann in **Herstellen einer Verbindung mit dem virtuellen Computer** die Option **RDP-Datei herunterladen** aus.
3. Öffnen Sie die heruntergeladene RDP-Datei. Wenn Sie dazu aufgefordert werden, wählen Sie **Verbinden** aus. Geben Sie den Benutzernamen und das Kennwort ein, die Sie beim Erstellen des virtuellen Computers festgelegt haben. Unter Umständen müssen Sie auf **Weitere Optionen** und anschließend auf **Anderes Konto verwenden** klicken, um die Anmeldeinformationen anzugeben, die Sie beim Erstellen des virtuellen Computers eingegeben haben.
4. Klicken Sie auf **OK**.
5. Während des Anmeldevorgangs wird unter Umständen eine Zertifikatwarnung angezeigt. Wenn eine Warnung angezeigt wird, klicken Sie auf **Ja** bzw. **Weiter**, um mit dem Herstellen der Verbindung fortzufahren.
1. Geben Sie in einem Webbrowser auf der VM *myVMEastUS* den DNS-Namen Ihres Traffic Manager-Profils an, um Ihre Website anzuzeigen. Da die IP-Adresse des virtuellen Computers *myVMEastUS* dem Endpunkt *myIISVMEastUS* zugeordnet ist, startet der Webbrowser den Testwebsiteserver *myIISVMEastUS*.

   ![Testen des Traffic Manager-Profils](./media/traffic-manager-subnet-routing-method/test-traffic-manager.png)

2. Stellen Sie dann mit den Schritten 1 bis 5 eine Verbindung mit dem virtuellen Computer *myVMWestEurope* her, der sich in **Europa, Westen** befindet, und browsen Sie von diesem virtuellen Computer aus zu dem Domänennamen des Traffic Manager-Profils. Da die IP-Adresse dem virtuellen Computer *myVMWestEurope* dem Endpunkt *myIISVMEastUS* zugeordnet ist, startet der Webbrowser den Testwebsiteserver *myIISVMWEurope*.

## <a name="delete-the-traffic-manager-profile"></a>Löschen des Traffic Manager-Profils
Wenn Sie die Ressourcengruppen (**ResourceGroupTM1** und **ResourceGroupTM2**) nicht mehr benötigen, löschen Sie sie. Wählen Sie hierzu die Ressourcengruppe aus (**ResourceGroupTM1** oder **ResourceGroupTM2**), und wählen Sie dann **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur [gewichteten Routingmethode für Datenverkehr](traffic-manager-configure-weighted-routing-method.md).
- Informationen zur [prioritätsbasierten Routingmethode](traffic-manager-configure-priority-routing-method.md).
- Informationen zur [geografischen Routingmethode](traffic-manager-configure-geographic-routing-method.md).
