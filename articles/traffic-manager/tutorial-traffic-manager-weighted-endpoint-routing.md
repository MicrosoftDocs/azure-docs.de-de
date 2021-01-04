---
title: 'Tutorial: Weiterleiten von Datenverkehr an gewichtete Endpunkte – Azure Traffic Manager'
description: In diesem Tutorialartikel erfahren Sie, wie Sie Datenverkehr mithilfe von Traffic Manager an gewichtete Endpunkte weiterleiten.
services: traffic-manager
author: duongau
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: duau
ms.openlocfilehash: 55c316a370b9e44e906e48b4716201384567c9c2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003785"
---
# <a name="tutorial-control-traffic-routing-with-weighted-endpoints-by-using-traffic-manager"></a>Tutorial: Steuern des Routings von Datenverkehr mit gewichteten Endpunkten unter Verwendung von Traffic Manager

In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure Traffic Manager und der gewichteten Routingmethode das Routing von Benutzerdatenverkehr zwischen Endpunkten steuern. Bei dieser Methode werden jedem Endpunkt im Rahmen der Traffic Manager-Profilkonfiguration Gewichtungen zugewiesen. Benutzerdatenverkehr wird dann basierend auf der Gewichtung weitergeleitet, die dem jeweiligen Endpunkt zugewiesen ist. Die Gewichtung wird als ganze Zahl zwischen 1 und 1.000 angegeben. Je höher der Gewichtungswert, der einem Endpunkt zugewiesen ist, desto höher seine Priorität.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen von zwei virtuellen Computern, die eine Standardwebsite in IIS ausführen
> * Erstellen von zwei virtuellen Testcomputern, um Traffic Manager in Aktion zu sehen
> * Konfigurieren des DNS-Namens für die virtuellen Computer, die IIS ausführen
> * Erstellen eines Traffic Manager-Profils
> * Hinzufügen von VM-Endpunkten zum Traffic Manager-Profil
> * Sehen Sie sich Traffic Manager in Aktion an.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um Traffic Manager in Aktion zu sehen, muss im Rahmen dieses Tutorials Folgendes bereitgestellt werden:

- Zwei Instanzen von Standardwebsites, die in verschiedenen Azure-Regionen („USA, Osten“ und „Europa, Westen“) ausgeführt werden.
- Zwei virtuelle Testcomputer zum Testen von Traffic Manager: ein virtueller Computer in „USA, Osten“ und einer in „Europa, Westen“. Die virtuellen Testcomputer veranschaulichen, wie Traffic Manager Benutzerdatenverkehr an eine Website weiterleitet, deren Endpunkt eine höhere Gewichtung zugewiesen ist.

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

### <a name="create-websites"></a>Erstellen von Websites

In diesem Abschnitt erstellen Sie zwei Websiteinstanzen, die die zwei Dienstendpunkte für das Traffic Manager-Profil in zwei Azure-Regionen bereitstellen. Führen Sie die folgenden Schritte aus, um die beiden Websites zu erstellen:

1. Erstellen Sie zwei virtuelle Computer für die Ausführung einer Standardwebsite: einen in „USA, Osten“ und einen in „Europa, Westen“.
2. Installieren Sie auf jedem virtuellen Computer einen IIS-Server. Aktualisieren Sie die Standardwebseite, die den Namen des virtuellen Computers beschreibt, mit dem ein Benutzer beim Besuch der Website verbunden wird.

#### <a name="create-vms-for-running-websites"></a>Erstellen von virtuellen Computern für die Ausführung von Websites

In diesem Abschnitt erstellen Sie die beiden virtuellen Computer *myIISVMEastUS* und *myIISVMWestEurope* in den Azure-Regionen „USA, Osten“ und „Europa, Westen“.

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

![Erstellen einer VM](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-webpage"></a>Installieren von IIS und Anpassen der Standardwebseite

In diesem Abschnitt installieren Sie den IIS-Server auf den beiden virtuellen Computern („myIISVMEastUS“ und „myIISVMWestEurope“) und aktualisieren dann die Standardwebseite. Die benutzerdefinierte Webseite zeigt den Namen des virtuellen Computers an, mit dem Sie eine Verbindung herstellen, wenn Sie die Website über einen Webbrowser besuchen.

1. Wählen Sie im linken Menü die Option **Alle Ressourcen**. Wählen Sie in der Ressourcenliste **myIISVMEastUS** in der Ressourcengruppe **myResourceGroupTM1** aus.
2. Wählen Sie auf der Seite **Übersicht** die Option **Verbinden** aus. Wählen Sie unter **Connect to virtual machine** (Mit virtuellem Computer verbinden) die Option **RDP-Datei herunterladen** aus.
3. Öffnen Sie die heruntergeladene RDP-Datei. Wählen Sie **Verbinden** aus, wenn eine entsprechende Aufforderung angezeigt wird. Geben Sie den Benutzernamen und das Kennwort an, die Sie beim Erstellen des virtuellen Computers angegeben haben. Unter Umständen müssen Sie **Weitere Optionen** > **Anderes Konto verwenden** auswählen, um die Anmeldeinformationen anzugeben, die Sie beim Erstellen des virtuellen Computers eingegeben haben.
4. Klicken Sie auf **OK**.
5. Während des Anmeldevorgangs wird unter Umständen eine Zertifikatwarnung angezeigt. Sollte eine Warnung angezeigt werden, wählen Sie **Ja** bzw. **Weiter** aus, um mit der Verbindungsherstellung fortzufahren.
6. Navigieren Sie auf dem Serverdesktop zu **Windows-Verwaltungsprogramme** > **Server-Manager**.
7. Öffnen Sie Windows PowerShell auf dem ersten virtuellen Computer. Verwenden Sie die folgenden Befehle, um den IIS-Server zu installieren und die HTM-Standarddatei zu aktualisieren.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default .htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom .htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

    ![Installieren von IIS und Anpassen der Webseite](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)

8. Schließen Sie die RDP-Verbindung mit **myIISVMEastUS**.
9. Wiederholen Sie die Schritte 1 bis 8. Erstellen Sie eine RDP-Verbindung mit dem virtuellen Computer **myIISVMWestEurope** innerhalb der Ressourcengruppe **myResourceGroupTM2**, um IIS zu installieren und die dazugehörige Standardwebseite anzupassen.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Konfigurieren von DNS-Namen für die virtuellen Computer, die IIS ausführen

Traffic Manager leitet Benutzerdatenverkehr basierend auf dem DNS-Namen der Dienstendpunkte weiter. In diesem Abschnitt konfigurieren Sie die DNS-Namen für die IIS-Server „myIISVMEastUS“ und „myIISVMWestEurope“.

1. Wählen Sie im linken Menü die Option **Alle Ressourcen**. Wählen Sie in der Ressourcenliste **myIISVMEastUS** in der Ressourcengruppe **myResourceGroupTM1** aus.
2. Wählen Sie auf der Seite **Übersicht** unter **DNS-Name** die Option **Konfigurieren** aus.
3. Fügen Sie auf der Seite **Konfiguration** einen eindeutigen Namen als DNS-Name hinzu. Klicken Sie dann auf **Speichern**.
4. Wiederholen Sie die Schritte 1 bis 3 für den virtuellen Computer **myIISVMWestEurope** in der Ressourcengruppe **myResourceGroupTM2**.

### <a name="create-a-test-vm"></a>Erstellen eines virtuellen Testcomputers

In diesem Abschnitt erstellen Sie je einen virtuellen Computer (*myVMEastUS* und *myVMWestEurope*) in den Azure-Regionen **USA, Osten** und **Europa, Westen**. Mit diesen virtuellen Computern testen Sie, wie Traffic Manager Datenverkehr an den Websiteendpunkt mit dem höheren Gewichtungswert weiterleitet.

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

Erstellen Sie ein Traffic Manager-Profil auf der Grundlage der Routingmethode **Gewichtet**.

1. Wählen Sie links oben auf dem Bildschirm **Ressource erstellen** > **Netzwerk** > **Traffic Manager-Profil** > **Erstellen** aus.
2. Geben Sie unter **Traffic Manager-Profil erstellen** die folgenden Informationen ein, bzw. wählen Sie sie aus. Behalten Sie bei den anderen Einstellungen die Standardwerte bei, und wählen Sie anschließend **Erstellen** aus.

    | Einstellung                 | Wert                                              |
    | ---                     | ---                                                |
    | Name                   | Geben Sie einen Namen ein, der innerhalb der Zone „trafficmanager.net“ eindeutig ist. Dadurch ergibt sich der DNS-Name „trafficmanager.net“, der für den Zugriff auf Ihr Traffic Manager-Profil verwendet wird.                                   |
    | Routingmethode          | Wählen Sie die Routingmethode **Gewichtet** aus.                                       |
    | Subscription            | Wählen Sie Ihr Abonnement aus.                          |
    | Resource group          | Wählen Sie **Vorhandene verwenden** und dann **myResourceGroupTM1** aus. |
    |        |   |

    ![Erstellen eines Traffic Manager-Profils](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Hinzufügen von Traffic Manager-Endpunkten

Fügen Sie die beiden virtuellen Computer hinzu, auf denen die IIS-Server („myIISVMEastUS“ und „myIISVMWestEurope“) ausgeführt werden, um Benutzerdatenverkehr an sie weiterzuleiten.

1. Suchen Sie über die Suchleiste des Portals nach dem Namen des Traffic Manager-Profils, das Sie im vorhergehenden Abschnitt erstellt haben. Wählen Sie das Profil in der Ergebnisanzeige aus.
2. Wählen Sie im **Traffic Manager-Profil** im Abschnitt **Einstellungen** die Option **Endpunkte** > **Hinzufügen** aus.
3. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus. Behalten Sie bei den anderen Einstellungen die Standardwerte bei, und wählen Sie anschließend **OK** aus.

    | Einstellung                 | Wert                                              |
    | ---                     | ---                                                |
    | type                    | Geben Sie den Azure-Endpunkt ein.                                   |
    | Name           | Geben Sie **myEastUSEndpoint** ein.                                        |
    | Zielressourcentyp           | Wählen Sie **Öffentliche IP-Adresse**.                          |
    | Zielressource          | Wählen Sie eine öffentliche IP-Adresse aus, um die Liste der Ressourcen mit öffentlichen IP-Adressen im gleichen Abonnement anzuzeigen. Wählen Sie in **Ressource** die öffentliche IP-Adresse mit dem Namen **myIISVMEastUS-ip** aus. Dies ist die öffentliche IP-Adresse der IIS-Server-VM in „USA, Osten“.|
    |  Weight      | Geben Sie **100** ein.        |
    |        |           |

4. Wiederholen Sie die Schritte 2 und 3, um einen anderen Endpunkt mit dem Namen **myWestEuropeEndpoint** für die öffentliche IP-Adresse **myIISVMWestEurope-ip** hinzuzufügen. Diese Adresse ist dem virtuellen Computer „myIISVMWestEurope“ mit dem IIS-Server zugeordnet. Geben Sie unter **Gewichtung** den Wert **25** ein.
5. Nach dem Hinzufügen werden beide Endpunkte im Traffic Manager-Profil zusammen mit ihrem Überwachungsstatus als **Online** angezeigt.

## <a name="test-the-traffic-manager-profile"></a>Testen des Traffic Manager-Profils

Führen Sie die folgenden Schritte aus, um den Traffic Manager in Aktion zu sehen:

1. Bestimmen Sie den DNS-Namen des Traffic Manager-Profils.
2. Sehen Sie sich Traffic Manager in Aktion an.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Bestimmen des DNS-Namens des Traffic Manager-Profils

In diesem Tutorial verwenden Sie der Einfachheit halber den DNS-Namen des Traffic Manager-Profils, um die Websites zu besuchen.

Sie können den DNS-Namen des Traffic Manager-Profils folgendermaßen bestimmen:

1. Suchen Sie über die Suchleiste des Portals nach dem Namen des Traffic Manager-Profils, das Sie im vorhergehenden Abschnitt erstellt haben. Wählen Sie in den angezeigten Ergebnissen das Traffic Manager-Profil aus.
2. Wählen Sie **Übersicht**.
3. Im Traffic Manager-Profil wird der dazugehörige DNS-Name angezeigt. In Produktionsbereitstellungen konfigurieren Sie mithilfe eines DNS-CNAME-Eintrags einen benutzerdefinierten Domänennamen für den Verweis auf den Traffic Manager-Domänennamen.

   ![Traffic Manager-DNS-Name](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Anzeigen von Traffic Manager in Aktion

In diesem Abschnitt sehen Sie Traffic Manager in Aktion.

1. Wählen Sie im linken Menü die Option **Alle Ressourcen**. Wählen Sie in der Ressourcenliste **myVMEastUS** in der Ressourcengruppe **myResourceGroupTM1** aus.
2. Wählen Sie auf der Seite **Übersicht** die Option **Verbinden** aus. Wählen Sie unter **Connect to virtual machine** (Mit virtuellem Computer verbinden) die Option **RDP-Datei herunterladen** aus.
3. Öffnen Sie die heruntergeladene RDP-Datei. Wählen Sie **Verbinden** aus, wenn eine entsprechende Aufforderung angezeigt wird. Geben Sie den Benutzernamen und das Kennwort ein, die Sie beim Erstellen des virtuellen Computers festgelegt haben. Unter Umständen müssen Sie **Weitere Optionen** > **Anderes Konto verwenden** auswählen, um die Anmeldeinformationen anzugeben, die Sie beim Erstellen des virtuellen Computers eingegeben haben.
4. Klicken Sie auf **OK**.
5. Während des Anmeldevorgangs wird unter Umständen eine Zertifikatwarnung angezeigt. Sollte eine Warnung angezeigt werden, wählen Sie **Ja** bzw. **Weiter** aus, um mit der Verbindungsherstellung fortzufahren.
6. Geben Sie auf dem virtuellen Computer „myVMEastUS“ in einem Webbrowser den DNS-Namen Ihres Traffic Manager-Profils an, um Ihre Website anzuzeigen. Sie werden zu der Website weitergeleitet, die auf dem IIS-Server „myIISVMEastUS“ gehostet wird, da ihm eine höhere Gewichtung (**100**) zugewiesen ist. Dem IIS-Server „myIISVMWestEurope“ ist eine niedrigere Endpunktgewichtung (**25**) zugewiesen.

   ![Testen des Traffic Manager-Profils](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

7. Wiederholen Sie die Schritte 1 bis 6 auf dem virtuellen Computer „myVMWestEurope“, um die gewichtete Websiteantwort anzuzeigen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Tutorial erstellten Ressourcengruppen nicht mehr benötigen, können Sie sie löschen. Wählen Sie hierzu die Ressourcengruppe aus (**ResourceGroupTM1** oder **ResourceGroupTM2**), und wählen Sie dann **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

Hier erfahren Sie mehr zu Routingmethoden:

> [!div class="nextstepaction"]
> [Datenverkehrsrouting-Methode](traffic-manager-routing-methods.md)
