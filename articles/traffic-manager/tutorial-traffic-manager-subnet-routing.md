---
title: 'Tutorial: Konfigurieren des Routings für Subnetzdatenverkehr mit Azure Traffic Manager'
description: In diesem Artikel Tutorial wird beschrieben, wie Traffic Manager so konfiguriert wird, dass Datenverkehr von Benutzersubnetzen an bestimmte Endpunkte geleitet wird.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: duau
ms.openlocfilehash: 348f1d779f8ea67860726b8f8b7739921e7ad54a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003802"
---
# <a name="tutorial-direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Tutorial: Weiterleiten von Datenverkehr an bestimmte Endpunkte mit dem Traffic Manager basierend auf einem Benutzersubnetz

In diesem Artikel wird das Konfigurieren der Routingmethode für Subnetzdatenverkehr beschrieben. Mit der Datenverkehrsrouting-Methode **Subnetz** können Sie IP-Adressbereiche bestimmten Endpunkten zuordnen. Wenn eine Anforderung vom Traffic Manager empfangen wird, wird die Quell-IP-Adresse der Anforderung überprüft und der damit verbundene Endpunkt zurückgegeben.

In diesem Tutorial wird abhängig von der IP-Adresse der Benutzerabfrage der Datenverkehr unter Verwendung von Subnetzrouting entweder an eine interne Website oder an eine Produktionswebsite geleitet.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen von zwei VMs, die eine Standardwebsite auf IIS ausführen
> * Erstellen von zwei Test-VMs zum Anzeigen von Traffic Manager in Aktion
> * Konfigurieren des DNS-Namens für die virtuellen Computer, die IIS ausführen
> * Erstellen eines Traffic Manager-Profils für das Datenverkehrsrouting basierend auf dem Subnetz eines Benutzers
> * Erstellen von VM-Endpunkten für das Traffic Manager-Profil
> * Anzeigen von Traffic Manager in Aktion

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um den Traffic Manager in Aktion sehen zu können, müssen Sie in diesem Tutorial Folgendes bereitstellen:

- Zwei grundlegende Websites, die in verschiedenen Azure-Regionen ausgeführt werden: **USA, Osten** (interne Website) und **Europa, Westen** (Produktionswebsite)
- zwei Test-VMs zum Testen des Traffic Managers – ein virtueller Computer in **USA, Osten** und der zweite virtuelle Computer in **Europa, Westen**.

Mit den Test-VMs wird veranschaulicht, wie der Traffic Manager basierend auf dem Subnetz, aus dem die Benutzerabfrage stammt, Benutzerdatenverkehr an die interne Website oder die Produktionswebsite weiterleitet.

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

### <a name="create-websites"></a>Erstellen von Websites

In diesem Abschnitt erstellen Sie zwei Websiteinstanzen, die die zwei Dienstendpunkte für das Traffic Manager-Profil in zwei Azure-Regionen bereitstellen. Das Erstellen der beiden Websites umfasst die folgenden Schritte:

1. Erstellen Sie zwei VMs für die Ausführung einer Standardwebsite – eine in **USA, Osten** und die andere in **Europa, Westen**.
2. Installieren Sie den IIS-Server auf jedem virtuellen Computer und aktualisieren Sie die Standardwebsiteseite, die den Namen der VM beschreibt, mit der ein Benutzer beim Besuch der Website verbunden ist.

#### <a name="create-vms-for-running-websites"></a>Erstellen von virtuellen Computern für die Ausführung von Websites

In diesem Abschnitt erstellen Sie die beiden virtuellen Computer *myIISVMEastUS* und *myIISVMWestEurope* in den Azure-Regionen **USA, Osten** und **Europa, Westen**.

1. Wählen Sie im Azure-Portal links oben **Ressource erstellen** > **Compute** > **Windows Server 2019 Datacenter** aus.
2. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Werte ein (bzw. wählen Sie sie aus):

   - **Abonnement** > **Ressourcengruppe**: Wählen Sie **Neu erstellen** aus, und geben Sie **myResourceGroupTM1** ein.
   - **Instanzendetails** > **Name des virtuellen Computers**: Geben Sie *myIISVMEastUS* ein.
   - **Instanzendetails** > **Region**:  Wählen Sie **USA, Osten** aus.
   - **Administratorkonto** > **Benutzername**:  Geben Sie den gewünschten Benutzernamen ein.
   - **Administratorkonto** > **Kennwort**:  Geben Sie das gewünschte Kennwort ein. Das Kennwort muss mindestens zwölf Zeichen lang sein und die [definierten Anforderungen an die Komplexität](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) erfüllen.
   - **Regeln für eingehende Ports** > **Öffentliche Eingangsports**: Wählen Sie **Ausgewählte Ports zulassen** aus.
   - **Regeln für eingehende Ports** > **Eingangsports auswählen**: Wählen Sie im Dropdownfeld **RDP** und **HTTP** aus.

3. Wählen Sie die Registerkarte **Verwaltung** oder **Weiter: Datenträger** und anschließend **Weiter: Netzwerk** > **Weiter: Verwaltung** aus. Legen Sie unter **Überwachung** die Option **Startdiagnose** auf **Aus** fest.
4. Klicken Sie auf **Überprüfen + erstellen**.
5. Überprüfen Sie die Einstellungen, und klicken Sie dann auf **Erstellen**.  
6. Führen Sie die Schritte zum Erstellen eines zweiten virtuellen Computers mit dem Namen *myIISVMWestEurope* aus. Geben Sie der **Ressourcengruppe** den Namen *myResourceGroupTM2*, und verwenden Sie als **Standort** die Option *Europa, Westen*. Legen Sie bei allen anderen Einstellungen die gleichen Optionen fest wie für *myIISVMEastUS*.
7. Die Erstellung der VMs kann einige Minuten dauern. Fahren Sie mit den restlichen Schritten erst fort, nachdem beide VMs erstellt wurden.

#### <a name="install-iis-and-customize-the-default-web-page"></a>Installieren von IIS und Anpassen der Standardwebseite

In diesem Abschnitt Installieren Sie den IIS-Server auf den beiden virtuellen Computern (*myIISVMEastUS* & *myIISVMWestEurope*) und aktualisieren dann die Standardwebsiteseite. Die benutzerdefinierte Websiteseite zeigt den Namen des virtuellen Computers an, mit dem Sie eine Verbindung herstellen, wenn Sie die Website über einen Webbrowser besuchen.

1. Wählen Sie im linken Menü **Alle Ressourcen** aus, und klicken Sie anschließend in der Ressourcenliste auf *myIISVMEastUS* in der Ressourcengruppe *myResourceGroupTM1*.
2. Klicken Sie auf der Seite **Übersicht** auf **Verbinden**, und wählen Sie dann in **Herstellen einer Verbindung mit dem virtuellen Computer** die Option **RDP-Datei herunterladen** aus.
3. Öffnen Sie die heruntergeladene RDP-Datei. Wenn Sie dazu aufgefordert werden, wählen Sie **Verbinden** aus. Geben Sie den Benutzernamen und das Kennwort ein, die Sie beim Erstellen des virtuellen Computers festgelegt haben. Unter Umständen müssen Sie auf **Weitere Optionen** und anschließend auf **Anderes Konto verwenden** klicken, um die Anmeldeinformationen anzugeben, die Sie beim Erstellen des virtuellen Computers eingegeben haben.
4. Klicken Sie auf **OK**.
5. Während des Anmeldevorgangs wird unter Umständen eine Zertifikatwarnung angezeigt. Wenn eine Warnung angezeigt wird, klicken Sie auf **Ja** bzw. **Weiter**, um mit dem Herstellen der Verbindung fortzufahren.
6. Navigieren Sie auf dem Serverdesktop zu **Windows-Verwaltungsprogramme**>**Server-Manager**.
7. Starten Sie Windows PowerShell auf dem virtuellen Computer *myIISVMEastUS*, und verwenden Sie die folgenden Befehle, um den IIS-Server zu installieren und die HTM-Standarddatei zu aktualisieren.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

8. Schließen Sie die RDP-Verbindung mit dem virtuellen Computer *myIISVMEastUS*.
9. Wiederholen Sie die Schritte 1 bis 6, und stellen Sie eine RDP-Verbindung mit dem virtuellen Computer *myIISVMWestEurope* innerhalb der Ressourcengruppe *myResourceGroupTM2* her, um die IIS zu installieren und die zugehörige Standardwebseite anzupassen.
10. Starten Sie Windows PowerShell auf dem virtuellen Computer *myIISVMWestEurope*, und verwenden Sie die folgenden Befehle, um den IIS-Server zu installieren und die HTM-Standarddatei zu aktualisieren.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurieren von DNS-Namen für die virtuellen Computer, die IIS ausführen

Basierend auf DNS-Namen der Dienstendpunkte leitet Traffic Manager den Benutzerdatenverkehr weiter. In diesem Abschnitt konfigurieren Sie die DNS-Namen für die IIS-Server *myIISVMEastUS* und *myIISVMWestEurope*.

1. Klicken Sie im linken Menü auf **Alle Ressourcen**, und wählen Sie dann in der Ressourcenliste *myIISVMEastUS* in der Ressourcengruppe *myResourceGroupTM1* aus.
2. Wählen Sie auf der Seite **Übersicht** unter **DNS-Name** die Option **Konfigurieren** aus.
3. Fügen Sie auf der Seite **Konfiguration** als DNS-Namen einen eindeutigen Namen ein, und wählen Sie dann **Speichern**.
4. Wiederholen Sie die Schritte 1 bis 3 für den virtuellen Computer mit dem Namen *myIISVMWestEurope*, der sich in der Ressourcengruppe *myResourceGroupTM2* befindet.

### <a name="create-test-vms"></a>Erstellen von Test-VMs

In diesem Abschnitt erstellen Sie je einen virtuellen Computer (*myVMEastUS* und *myVMWestEurope*) in den Azure-Regionen **USA, Osten** und **Europa, Westen**. Sie testen mit diesen virtuellen Computern, wie Traffic Manager Benutzerdatenverkehr basierend auf dem Subnetz der Benutzerabfrage weiterleitet.

1. Wählen Sie im Azure-Portal links oben **Ressource erstellen** > **Compute** > **Windows Server 2019 Datacenter** aus.
2. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Werte ein (bzw. wählen Sie sie aus):

   - **Abonnement** > **Ressourcengruppe**: Wählen Sie **myResourceGroupTM1** aus.
   - **Instanzendetails** > **Name des virtuellen Computers**: Geben Sie *myVMEastUS* ein.
   - **Instanzendetails** > **Region**:  Wählen Sie **USA, Osten** aus.
   - **Administratorkonto** > **Benutzername**:  Geben Sie den gewünschten Benutzernamen ein.
   - **Administratorkonto** > **Kennwort**:  Geben Sie das gewünschte Kennwort ein. Das Kennwort muss mindestens zwölf Zeichen lang sein und die [definierten Anforderungen an die Komplexität](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) erfüllen.
   - **Regeln für eingehende Ports** > **Öffentliche Eingangsports**: Wählen Sie **Ausgewählte Ports zulassen** aus.
   - **Regeln für eingehende Ports** > **Eingangsports auswählen**: Wählen Sie im Dropdownfeld **RDP** aus.

3. Wählen Sie die Registerkarte **Verwaltung** oder **Weiter: Datenträger** und anschließend **Weiter: Netzwerk** > **Weiter: Verwaltung** aus. Legen Sie unter **Überwachung** die Option **Startdiagnose** auf **Aus** fest.
4. Klicken Sie auf **Überprüfen + erstellen**.
5. Überprüfen Sie die Einstellungen, und klicken Sie dann auf **Erstellen**.  
6. Führen Sie die Schritte zum Erstellen eines zweiten virtuellen Computers mit dem Namen *myVMWestEurope* aus. Geben Sie der **Ressourcengruppe** den Namen *myResourceGroupTM2*, und verwenden Sie als **Standort** die Option *Europa, Westen*. Legen Sie bei allen anderen Einstellungen die gleichen Optionen fest wie für *myVMEastUS*.
7. Die Erstellung der VMs kann einige Minuten dauern. Fahren Sie mit den restlichen Schritten erst fort, nachdem beide VMs erstellt wurden.

## <a name="create-a-traffic-manager-profile"></a>Erstellen eines Traffic Manager-Profils

Erstellen Sie ein Traffic Manager-Profil, mit dem Sie basierend auf der Quell-IP-Adresse der Anforderung bestimmte Endpunkte zurückgeben können.

1. Klicken Sie links oben auf dem Bildschirm auf **Ressource erstellen** > **Netzwerk** > **Traffic Manager-Profil** > **Erstellen**.
2. Geben Sie unter **Traffic Manager-Profil erstellen** die folgenden Informationen ein, oder wählen Sie sie aus, übernehmen Sie die Standardwerte für die übrigen Einstellungen, und klicken Sie auf **Erstellen**:

    | Einstellung                 | Wert                                              |
    | ---                     | ---                                                |
    | Name                   | Dieser Name muss innerhalb der Zone „trafficmanager.net“ eindeutig sein und ergibt den DNS-Namen „trafficmanager.net“, der für den Zugriff auf Ihr Traffic Manager-Profil verwendet wird.                                   |
    | Routingmethode          | Wählen Sie als Routingmethode **Subnetz** aus.                                       |
    | Subscription            | Wählen Sie Ihr Abonnement aus.                          |
    | Resource group          | Wählen Sie **Vorhanden** aus, und geben Sie *myResourceGroupTM1* ein. |
    | |                              |
    |

    ![Erstellen eines Traffic Manager-Profils](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Hinzufügen von Traffic Manager-Endpunkten

Fügen Sie die beiden virtuellen Computer mit den IIS-Servern (*myIISVMEastUS* & *myIISVMWestEurope*) hinzu, um Benutzerdatenverkehr basierend auf dem Subnetz der Benutzerabfrage weiterzuleiten.

1. Suchen Sie in der Suchleiste des Portals nach dem Namen des Traffic Manager-Profils, das Sie im vorhergehenden Abschnitt erstellt haben, und wählen Sie in den angezeigten Ergebnissen das Profil aus.
2. Klicken Sie unter **Traffic Manager-Profil** im Abschnitt **Einstellungen** auf **Endpunkte** und dann auf **Hinzufügen**.
3. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus, übernehmen Sie die Standardwerte für die übrigen Einstellungen, und klicken Sie auf **OK**:

    | Einstellung                 | Wert                                              |
    | ---                     | ---                                                |
    | type                    | Azure-Endpunkt                                   |
    | Name           | myInternalWebSiteEndpoint                                        |
    | Zielressourcentyp           | Öffentliche IP-Adresse                          |
    | Zielressource          | **Wählen Sie eine öffentliche IP-Adresse aus**, um die Liste der Ressourcen mit öffentlichen IP-Adressen im gleichen Abonnement anzuzeigen. Wählen Sie in **Ressource** die öffentliche IP-Adresse mit dem Namen *myIISVMEastUS-ip* aus. Dies ist die öffentliche IP-Adresse der IIS-Server-VM in „USA, Osten“.|
    |  Einstellungen für das Subnetzrouting    |   Fügen Sie die IP-Adresse der Test-VM *myVMEastUS* hinzu. Jede Benutzerabfrage von diesem virtuellen Computer wird an *myInternalWebSiteEndpoint* weitergeleitet.    |

4. Wiederholen Sie die Schritte 2 und 3, um einen anderen Endpunkt mit dem Namen *myProdWebsiteEndpoint* für die öffentliche IP-Adresse *myIISVMWestEurope-ip* hinzuzufügen, die dem virtuellen IIS-Servercomputer mit dem Namen *myIISVMWestEurope* zugeordnet ist. Fügen Sie unter **Einstellungen für das Subnetzrouting** die IP-Adresse der Test-VM *myVMWestEurope* hinzu. Jede Benutzerabfrage von diesem virtuellen Testcomputer wird an den Endpunkt *myProdWebsiteEndpoint* geleitet.
5. Wenn Sie das Hinzufügen beider Endpunkte abgeschlossen haben, werden diese unter **Traffic Manager-Profil** zusammen mit ihrem Überwachungsstatus als **Online** angezeigt.

## <a name="test-traffic-manager-profile"></a>Testen des Traffic Manager-Profils

In diesem Abschnitt testen Sie, wie der Traffic Manager Benutzerdatenverkehr von einem bestimmten Subnetz an einen bestimmten Endpunkt weiterleitet. Um den Traffic Manager in Aktion anzuzeigen, führen Sie die folgenden Schritte aus:

1. Bestimmen Sie den DNS-Namen des Traffic Manager-Profils.
2. Zeigen Sie den Traffic Manager folgendermaßen in Aktion an:
    - Browsen Sie von der Test-VM (*myVMEastUS*) aus, die sich in der Region **USA, Osten** befindet, in einem Webbrowser zu dem DNS-Namen des Traffic Manager-Profils.
    - Navigieren Sie auf dem virtuellen Testcomputer (*myVMWestEurope*), der sich in der Region **Europa, Westen** befindet, in einem Webbrowser zu dem DNS-Namen Ihres Traffic Manager-Profils.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Bestimmen des DNS-Namens des Traffic Manager-Profils

In diesem Tutorial verwenden Sie der Einfachheit halber den DNS-Namen des Traffic Manager-Profils, um die Websites zu besuchen.

Sie können den DNS-Namen des Traffic Manager-Profils folgendermaßen bestimmen:

1. Suchen Sie in der Suchleiste des Portals nach dem Namen des **Traffic Manager-Profils**, das Sie im vorhergehenden Abschnitt erstellt haben. Klicken Sie in den angezeigten Ergebnissen auf das Traffic Manager-Profil.
2. Klicken Sie auf **Overview**.
3. Unter **Traffic Manager-Profil** wird der DNS-Name Ihres neu erstellten Traffic Manager-Profils angezeigt. In Produktionsbereitstellungen konfigurieren Sie mithilfe eines DNS-CNAME-Eintrags einen benutzerdefinierten Domänennamen für den Verweis auf den Traffic Manager-Domänennamen.

### <a name="view-traffic-manager-in-action"></a>Anzeigen von Traffic Manager in Aktion

In diesem Abschnitt sehen Sie den Traffic Manager in Aktion.

1. Wählen Sie im linken Menü **Alle Ressourcen** aus, und klicken Sie anschließend in der Ressourcenliste auf *myVMEastUS* in der Ressourcengruppe *myResourceGroupTM1*.
2. Klicken Sie auf der Seite **Übersicht** auf **Verbinden**, und wählen Sie dann in **Herstellen einer Verbindung mit dem virtuellen Computer** die Option **RDP-Datei herunterladen** aus.
3. Öffnen Sie die heruntergeladene RDP-Datei. Wenn Sie dazu aufgefordert werden, wählen Sie **Verbinden** aus. Geben Sie den Benutzernamen und das Kennwort ein, die Sie beim Erstellen des virtuellen Computers festgelegt haben. Unter Umständen müssen Sie auf **Weitere Optionen** und anschließend auf **Anderes Konto verwenden** klicken, um die Anmeldeinformationen anzugeben, die Sie beim Erstellen des virtuellen Computers eingegeben haben.
4. Klicken Sie auf **OK**.
5. Während des Anmeldevorgangs wird unter Umständen eine Zertifikatwarnung angezeigt. Wenn eine Warnung angezeigt wird, klicken Sie auf **Ja** bzw. **Weiter**, um mit dem Herstellen der Verbindung fortzufahren.
6. Geben Sie in einem Webbrowser auf der VM *myVMEastUS* den DNS-Namen Ihres Traffic Manager-Profils an, um Ihre Website anzuzeigen. Da die IP-Adresse des virtuellen Computers *myVMEastUS* dem Endpunkt *myInternalWebsiteEndpoint* zugeordnet ist, startet der Webbrowser den Testwebsiteserver *myIISVMEastUS*.

7. Stellen Sie dann mit den Schritten 1 bis 5 eine Verbindung mit dem virtuellen Computer *myVMWestEurope* her, der sich in **Europa, Westen** befindet, und browsen Sie von diesem virtuellen Computer aus zu dem Domänennamen des Traffic Manager-Profils. Da die IP-Adresse des virtuellen Computers *myVMWestEurope* dem Endpunkt *myProductionWebsiteEndpoint* zugeordnet ist, startet der Webbrowser den Testwebsiteserver *myIISVMWestEurope*.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppen (**ResourceGroupTM1** und **ResourceGroupTM2**) nicht mehr benötigen, löschen Sie sie. Wählen Sie hierzu die Ressourcengruppe aus (**ResourceGroupTM1** oder **ResourceGroupTM2**), und wählen Sie dann **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

Hier erfahren Sie mehr zur Subnetz-Routingmethode:

> [!div class="nextstepaction"]
> [Subnetzdatenverkehrs-Routingmethode](traffic-manager-routing-methods.md#subnet)
