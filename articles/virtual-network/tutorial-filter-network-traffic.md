---
title: 'Filtern von Netzwerkdatenverkehr: Tutorial – Azure-Portal'
titlesuffix: Azure Virtual Network
description: In diesem Tutorial erfahren Sie, wie Sie an ein Subnetz gerichteten Netzwerkdatenverkehr mithilfe einer Netzwerksicherheitsgruppe unter Verwendung des Azure-Portals filtern.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.service: virtual-network
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/13/2018
ms.author: kumud
ms.openlocfilehash: 4097d4fc46aac88cd44d21a4cdcf0d7d5093feea
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242737"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Tutorial: Filtern von Netzwerkdatenverkehr mithilfe einer Netzwerksicherheitsgruppe über das Azure-Portal

Sie können eingehenden und ausgehenden Netzwerkdatenverkehr im Subnetz eines virtuellen Netzwerks mithilfe einer Netzwerksicherheitsgruppe filtern. Netzwerksicherheitsgruppen enthalten Sicherheitsregeln, die Netzwerkdatenverkehr nach IP-Adresse, Port und Protokoll filtern. Sicherheitsregeln werden auf Ressourcen angewendet, die in einem Subnetz bereitgestellt sind. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen von Netzwerksicherheitsgruppe und Sicherheitsregeln
> * Erstellen eines virtuellen Netzwerks und Zuweisen einer Netzwerksicherheitsgruppe zu einem Subnetz
> * Bereitstellen von virtuellen Computern in einem Subnetz
> * Testen von Datenverkehrsfiltern

Dieser Artikel kann auch mit der [Azure-Befehlszeilenschnittstelle](tutorial-filter-network-traffic-cli.md) oder mit [PowerShell](tutorial-filter-network-traffic-powershell.md) durchgearbeitet werden.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

1. Klicken Sie im Azure-Portal links oben auf **+ Ressource erstellen**.
2. Wählen Sie **Netzwerk** und anschließend **Virtuelles Netzwerk** aus.
3. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus, übernehmen Sie die Standardwerte für die übrigen Einstellungen, und klicken Sie auf **Erstellen**:

    | Einstellung                 | Wert                                              |
    | ---                     | ---                                                |
    | NAME                    | myVirtualNetwork                                   |
    | Adressraum           | 10.0.0.0/16                                        |
    | Abonnement            | Wählen Sie Ihr Abonnement aus.                          |
    | Ressourcengruppe          | Klicken Sie auf **Neu erstellen**, und geben Sie *myResourceGroup* ein. |
    | Location                | Wählen Sie **USA, Osten** aus.                                |
    | Subnetzname            | mySubnet                                           |
    | Subnetzadressbereich  | 10.0.0.0/24                                        |

## <a name="create-application-security-groups"></a>Erstellen von Anwendungssicherheitsgruppen

Mithilfe einer Anwendungssicherheitsgruppe können Sie Server mit ähnlichen Funktionen gruppieren, wie etwa Webserver.

1. Klicken Sie im Azure-Portal links oben auf **+ Ressource erstellen**.
2. Geben Sie in das Feld **Marketplace durchsuchen** den Suchbegriff *Anwendungssicherheitsgruppe* ein. Wenn in den Suchergebnissen **Anwendungssicherheitsgruppe** angezeigt wird, wählen Sie dieses Ergebnis aus. Wählen Sie unter **Alles** erneut **Anwendungssicherheitsgruppe** aus, und wählen Sie anschließend **Erstellen**.
3. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus, und wählen Sie dann **Erstellen** aus:

    | Einstellung        | Wert                                                         |
    | ---            | ---                                                           |
    | NAME           | myAsgWebServers                                               |
    | Abonnement   | Wählen Sie Ihr Abonnement aus.                                     |
    | Ressourcengruppe | Wählen Sie **Vorhandene verwenden** und dann **myResourceGroup** aus. |
    | Location       | USA (Ost)                                                       |

4. Führen Sie Schritt 3 erneut aus, und geben Sie dabei die folgenden Werte an:

    | Einstellung        | Wert                                                         |
    | ---            | ---                                                           |
    | NAME           | myAsgMgmtServers                                              |
    | Abonnement   | Wählen Sie Ihr Abonnement aus.                                     |
    | Ressourcengruppe | Wählen Sie **Vorhandene verwenden** und dann **myResourceGroup** aus. |
    | Location       | USA (Ost)                                                       |

## <a name="create-a-network-security-group"></a>Erstellen einer Netzwerksicherheitsgruppe

1. Klicken Sie im Azure-Portal links oben auf **+ Ressource erstellen**.
2. Wählen Sie die Option **Netzwerk** und dann **Netzwerksicherheitsgruppe** aus.
3. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus, und wählen Sie dann **Erstellen** aus:

    |Einstellung|Wert|
    |---|---|
    |NAME|myNsg|
    |Abonnement| Wählen Sie Ihr Abonnement aus.|
    |Ressourcengruppe | Wählen Sie **Vorhandene verwenden** und dann *myResourceGroup*.|
    |Location|USA (Ost)|

## <a name="associate-network-security-group-to-subnet"></a>Zuordnen einer Netzwerksicherheitsgruppe zu einem Subnetz

1. Beginnen Sie oben im Portal im Feld *Ressourcen, Dienste und Dokumente durchsuchen* mit der Eingabe von *myNsg*. Wenn **myNsg** in den Suchergebnissen angezeigt wird, wählen Sie diesen Eintrag aus.
2. Klicken Sie unter **EINSTELLUNGEN** auf **Subnetze** und anschließend auf **+ Zuordnen**, wie in der folgenden Abbildung gezeigt:

    ![Zuordnen der Netzwerksicherheitsgruppe zum Subnetz](./media/tutorial-filter-network-traffic/associate-nsg-subnet.png)

3. Wählen Sie unter **Subnetz zuordnen** die Option **Virtuelles Netzwerk** und anschließend **myVirtualNetwork** aus. Wählen Sie **Subnetz**, **mySubnet** und dann **OK** aus.

## <a name="create-security-rules"></a>Erstellen von Sicherheitsregeln

1. Wählen Sie unter **EINSTELLUNGEN** die Option **Eingangssicherheitsregeln** und anschließend **+ Hinzufügen** aus, wie in der folgenden Abbildung gezeigt:

    ![Hinzufügen einer Eingangssicherheitsregel](./media/tutorial-filter-network-traffic/add-inbound-rule.png)

2. Erstellen Sie eine Sicherheitsregel, die für die Anwendungssicherheitsgruppe **myAsgWebServers** die Ports 80 und 443 zulässt. Geben Sie unter **Eingangssicherheitsregel hinzufügen** die folgenden Werte ein oder wählen Sie die Werte aus, übernehmen Sie die restlichen Standardeinstellungen, und wählen Sie dann **Hinzufügen** aus:

    | Einstellung                 | Wert                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Ziel             | Wählen Sie zunächst **Anwendungssicherheitsgruppe** und anschließend für **Anwendungssicherheitsgruppe** den Namen **myAsgWebServers** aus.  |
    | Zielportbereiche | Geben Sie 80,443 ein.                                                                                                    |
    | Protocol                | Wählen Sie TCP aus.                                                                                                      |
    | NAME                    | Allow-Web-All                                                                                                   |

3. Führen Sie Schritt 2 erneut aus, und verwenden Sie dabei die folgenden Werte:

    | Einstellung                 | Wert                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Ziel             | Wählen Sie zunächst **Anwendungssicherheitsgruppe** und anschließend für **Anwendungssicherheitsgruppe** den Namen **myAsgMgmtServers** aus. |
    | Zielportbereiche | Geben Sie 3389 ein.                                                                                                      |
    | Protocol                | Wählen Sie TCP aus.                                                                                                      |
    | Priorität                | Geben Sie 110 ein.                                                                                                       |
    | NAME                    | Allow-RDP-All                                                                                                   |

    In diesem Tutorial wird RDP (Port 3389) für den virtuellen Computer, der der Anwendungssicherheitsgruppe *myAsgMgmtServers* zugewiesen ist, im Internet verfügbar gemacht. In Produktionsumgebungen empfiehlt es sich, eine VPN-basierte oder private Netzwerkverbindung mit den Azure-Ressourcen herzustellen, die Sie verwalten möchten, anstatt den Port 3389 für das Internet verfügbar zu machen.

Nachdem Sie die Schritte 1 bis 3 durchgeführt haben, überprüfen Sie die Regeln, die Sie erstellt haben. Ihre Liste sollte wie die Liste in der folgenden Abbildung aussehen:

![Sicherheitsregeln](./media/tutorial-filter-network-traffic/security-rules.png)

## <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

Erstellen Sie zwei virtuelle Computer im virtuellen Netzwerk.

### <a name="create-the-first-vm"></a>Erstellen des ersten virtuellen Computers

1. Klicken Sie im Azure-Portal links oben auf **+ Ressource erstellen**.
2. Wählen Sie **Compute** und dann **Windows Server 2016 Datacenter**.
3. Geben Sie die folgenden Informationen ein, oder wählen Sie sie aus, übernehmen Sie die Standardwerte für die übrigen Einstellungen, und klicken Sie auf **OK**:

    |Einstellung|Wert|
    |---|---|
    |NAME|myVmWeb|
    |Benutzername| Geben Sie den gewünschten Benutzernamen ein.|
    |Kennwort| Geben Sie das gewünschte Kennwort ein. Das Kennwort muss mindestens zwölf Zeichen lang sein und die [definierten Anforderungen an die Komplexität](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm) erfüllen.|
    |Abonnement| Wählen Sie Ihr Abonnement aus.|
    |Ressourcengruppe| Wählen Sie **Vorhandene verwenden** und dann **myResourceGroup** aus.|
    |Location| Wählen Sie **USA, Osten** aus.|

4. Wählen Sie eine Größe für den virtuellen Computer aus, und klicken Sie dann auf **Auswählen**.
5. Wählen Sie unter **Einstellungen** die folgenden Werte aus, übernehmen Sie die restlichen Standardeinstellungen, und wählen Sie dann **OK** aus:

    |Einstellung|Wert|
    |---|---|
    |Virtuelles Netzwerk |Wählen Sie **myVirtualNetwork** aus.|
    |Netzwerksicherheitsgruppen (NSG) | Wählen Sie **Advanced** (Erweitert).|
    |Netzwerksicherheitsgruppe (Firewall)| Wählen Sie **(new) myVmWeb-nsg** aus. Wählen Sie anschließend unter **Netzwerksicherheitsgruppe auswählen** die Option **Keine** aus. |

6. Wählen Sie auf der Seite **Zusammenfassung** unter **Erstellen** die Option **Erstellen**, um die Bereitstellung des virtuellen Computers zu starten.

### <a name="create-the-second-vm"></a>Erstellen des zweiten virtuellen Computers

Führen Sie die Schritte 1 bis 6 erneut aus, geben Sie jedoch in Schritt 3 dem virtuellen Computer den Namen *myVmMgmt*. Die Bereitstellung des virtuellen Computers dauert einige Minuten. Führen Sie den nächsten Schritt erst durch, nachdem der virtuelle Computer bereitgestellt wurde.

## <a name="associate-network-interfaces-to-an-asg"></a>Zuordnen von Netzwerkschnittstellen zu einer Anwendungssicherheitsgruppe

Beim Erstellen der virtuellen Computer im Portal wird für jeden virtuellen Computer eine Netzwerkschnittstelle erstellt und mit dem jeweiligen virtuellen Computer verbunden. Fügen Sie die Netzwerkschnittstelle für die einzelnen virtuellen Computer einer der Anwendungssicherheitsgruppen hinzu, die Sie zuvor erstellt haben:

1. Beginnen Sie oben im Portal im Feld *Ressourcen, Dienste und Dokumente durchsuchen* mit der Eingabe von *myVmWeb*. Wenn die VM **myVmWeb** in den Suchergebnissen angezeigt wird, wählen Sie sie aus.
2. Wählen Sie unter **EINSTELLUNGEN** die Option **Netzwerk** aus.  Wählen Sie zunächst **Configure the application security groups** (Anwendungssicherheitsgruppen konfigurieren), anschließend **myAsgWebServers** für **Anwendungssicherheitsgruppen** und schließlich **Speichern**, wie in der folgenden Abbildung gezeigt:

    ![Zuordnen von Anwendungssicherheitsgruppen](./media/tutorial-filter-network-traffic/associate-to-asg.png)

3. Führen Sie die Schritte 1 und 2 erneut durch. Suchen Sie dabei nach dem virtuellen Computer **myVmMgmt**, und wählen Sie die Anwendungssicherheitsgruppe **myAsgMgmtServers** aus.

## <a name="test-traffic-filters"></a>Testen von Datenverkehrsfiltern

1. Stellen Sie eine Verbindung mit dem virtuellen Computer *myVmMgmt* her. Geben Sie oben im Portal im Suchfeld *myVmMgmt* ein. Wenn **myVmMgmt** in den Suchergebnissen angezeigt wird, wählen Sie diese Angabe aus. Wählen Sie die Schaltfläche **Verbinden** aus.
2. Wählen Sie **RDP-Datei herunterladen** aus.
3. Öffnen Sie die heruntergeladene RDP-Datei, und wählen Sie **Verbinden** aus. Geben Sie den Benutzernamen und das Kennwort ein, die Sie beim Erstellen des virtuellen Computers festgelegt haben. Unter Umständen müssen Sie auf **Weitere Optionen** und anschließend auf **Anderes Konto verwenden** klicken, um die Anmeldeinformationen anzugeben, die Sie beim Erstellen des virtuellen Computers eingegeben haben.
4. Klicken Sie auf **OK**.
5. Während des Anmeldevorgangs wird unter Umständen eine Zertifikatwarnung angezeigt. Wenn eine Warnung angezeigt wird, klicken Sie auf **Ja** bzw. **Weiter**, um mit dem Herstellen der Verbindung fortzufahren.

    Die Verbindung wird erfolgreich hergestellt, da am Port 3389 eingehender Datenverkehr aus dem Internet für die Anwendungssicherheitsgruppe *myAsgMgmtServers* zugelassen wird, in der sich die an den virtuellen Computer *myVmMgmt* angefügte Netzwerkschnittstelle befindet.

6. Stellen Sie vom virtuellen Computer *myVmMgmt* aus eine Verbindung mit dem virtuellen Computer *myVmWeb* her. Geben Sie dabei in einer PowerShell-Sitzung den folgenden Befehl ein:

    ``` 
    mstsc /v:myVmWeb
    ```

    Sie können vom virtuellen Computer myVmMgmt aus eine Verbindung mit dem virtuellen Computer myVmWeb herstellen, da virtuelle Computer im selben virtuellen Netzwerk standardmäßig über jeden beliebigen Port miteinander kommunizieren können. Über das Internet kann jedoch keine Remotedesktopverbindung mit dem virtuellen Computer *myVmWeb* hergestellt werden, da die Sicherheitsregel für *myAsgWebServers* am Port 3389 keinen eingehenden Datenverkehr aus dem Internet zulässt. Eingehender Datenverkehr aus dem Internet wird standardmäßig für alle Ressourcen abgelehnt.

7. Geben Sie den folgenden Befehl über eine PowerShell-Sitzung auf dem virtuellen Computer *myVmWeb* ein, um Microsoft IIS auf dem virtuellen Computer *myVmWeb* zu installieren:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. Trennen Sie nach Abschluss der IIS-Installation die Verbindung mit dem virtuellen Computer *myVmWeb*. Dadurch befinden Sie sich in der Remotedesktopverbindung des virtuellen Computers *myVmMgmt*.
9. Trennen Sie die Verbindung mit dem virtuellen Computer *myVmMgmt*.
10. Beginnen Sie oben im Azure-Portal im Feld *Ressourcen, Dienste und Dokumente durchsuchen* mit der Eingabe von *myVmWeb* über Ihren Computer. Wenn **myVmWeb** in den Suchergebnissen angezeigt wird, wählen Sie diese Angabe aus. Notieren Sie die **öffentliche IP-Adresse** Ihres virtuellen Computers. Die in der folgenden Abbildung dargestellte Adresse lautet 137.135.84.74. Ihre Adresse lautet jedoch anders:

    ![Öffentliche IP-Adresse](./media/tutorial-filter-network-traffic/public-ip-address.png)
  
11. Navigieren Sie auf Ihrem Computer in einem Internetbrowser zu `http://<public-ip-address-from-previous-step>`, um sich zu vergewissern, dass Sie vom Internet aus auf den Webserver *myVmWeb* zugreifen können. Die Willkommenseite von IIS wird angezeigt, da am Port 80 eingehender Datenverkehr aus dem Internet für die Anwendungssicherheitsgruppe *myAsgWebServers* zugelassen wird, in der sich die an den virtuellen Computer *myVmWeb* angefügte Netzwerkschnittstelle befindet.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe mit allen ihren Ressourcen, wenn Sie sie nicht mehr benötigen:

1. Geben Sie im oben im Portal im Feld *Suche* die Zeichenfolge **myResourceGroup** ein. Wenn **myResourceGroup** in den Suchergebnissen angezeigt wird, wählen Sie diese Angabe aus.
2. Wählen Sie die Option **Ressourcengruppe löschen**.
3. Geben Sie für **Geben Sie den Ressourcengruppennamen ein:** den Namen *myResourceGroup* ein, und klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Netzwerksicherheitsgruppe erstellt und dem Subnetz eines virtuellen Netzwerks zugeordnet. Weitere Informationen zu Netzwerksicherheitsgruppen finden Sie unter [Netzwerksicherheitsgruppen – Übersicht](security-overview.md) sowie unter [Verwalten einer Netzwerksicherheitsgruppe](manage-network-security-group.md).

Azure leitet standardmäßig Datenverkehr zwischen Subnetzen weiter. Sie können Datenverkehr zwischen Subnetzen aber beispielsweise auch über einen virtuellen Computer weiterleiten, der als Firewall fungiert. Im nächsten Tutorial erfahren Sie, wie Sie eine Routingtabelle erstellen.

> [!div class="nextstepaction"]
> [Weiterleiten von Netzwerkdatenverkehr mithilfe des Azure-Portals](./tutorial-create-route-table-portal.md)
