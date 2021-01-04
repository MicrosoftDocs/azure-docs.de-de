---
title: 'Tutorial: Filtern von eingehendem Internetdatenverkehr mit Azure Firewall-DNAT im Portal'
description: In diesem Tutorial erfahren Sie, wie Sie Azure Firewall-DNAT über das Azure-Portal bereitstellen und konfigurieren.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 71eda40abd38b4885b8e88085e338667b608902f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655135"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Tutorial: Filtern von eingehendem Internetdatenverkehr mit Azure Firewall-DNAT im Azure-Portal

Sie können die Ziel-Netzwerkadressübersetzung (Destination Network Address Translation, DNAT) von Azure Firewall so konfigurieren, dass eingehender Internetdatenverkehr für Ihre Subnetze übersetzt und gefiltert wird. Wenn Sie DNAT konfigurieren, ist die Aktion für die NAT-Regelsammlung auf **Dnat** festgelegt. Jede Regel in der NAT-Regelsammlung kann dann verwendet werden, um die öffentliche IP-Adresse und den Port Ihrer Firewall in eine private IP-Adresse und den zugehörigen Port zu übersetzen. Mit DNAT-Regeln wird implizit eine entsprechende Netzwerkregel hinzugefügt, um den übersetzten Datenverkehr zuzulassen. Sie können dieses Verhalten außer Kraft setzen, indem Sie explizit eine Netzwerkregelsammlung mit Ablehnungsregeln hinzufügen, die für den übersetzten Datenverkehr geeignet sind. Weitere Informationen zur Logik für die Azure Firewall-Regelverarbeitung finden Sie unter [Logik für die Azure Firewall-Regelverarbeitung](rule-processing.md).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten einer Netzwerkumgebung zu Testzwecken
> * Bereitstellen einer Firewall
> * Erstellen einer Standardroute
> * Konfigurieren einer DNAT-Regel
> * Testen der Firewall

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.



## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.
2. Klicken Sie auf der Startseite des Azure-Portals auf **Ressourcengruppen** und dann auf **Hinzufügen**.
3. Geben Sie unter **Ressourcengruppenname** die Zeichenfolge **RG-DNAT-Test** ein.
4. Wählen Sie unter **Abonnement** Ihr Abonnement aus.
5. Wählen Sie unter **Ressourcengruppenstandort** einen Standort aus. Alle weiteren Ressourcen, die Sie erstellen, müssen sich am gleichen Standort befinden.
6. Klicken Sie auf **Erstellen**.

## <a name="set-up-the-network-environment"></a>Einrichten der Netzwerkumgebung

In diesem Tutorial erstellen Sie zwei mittels Peering verknüpfte VNETs:

- **VN-Hub**:In diesem VNET befindet sich die Firewall.
- **VN-Spoke**: In diesem VNET befindet sich der Workloadserver.

Erstellen Sie zuerst die VNETs, und führen Sie anschließend das Peering dafür durch.

### <a name="create-the-hub-vnet"></a>Erstellen des Hub-VNET

1. Wählen Sie auf der Startseite des Azure-Portals **Alle Dienste** aus.
2. Klicken Sie unter **Netzwerk** auf **Virtuelle Netzwerke**.
3. Wählen Sie **Hinzufügen**.
4. Geben Sie unter **Name** den Namen **VN-Hub** ein.
5. Geben Sie unter **Adressraum** die Zeichenfolge **10.0.0.0/16** ein.
6. Wählen Sie unter **Abonnement** Ihr Abonnement aus.
7. Klicken Sie unter **Ressourcengruppe** auf **Vorhandene verwenden**, und wählen Sie anschließend **RG-DNAT-Test** aus.
8. Wählen Sie unter **Standort** den gleichen Standort aus wie zuvor.
9. Geben Sie unter **Subnetz** als **Name** die Zeichenfolge **AzureFirewallSubnet** ein.

     Die Firewall befindet sich diesem Subnetz, und der Subnetzname **muss** „AzureFirewallSubnet“ lauten.
     > [!NOTE]
     > Die Größe des Subnetzes „AzureFirewallSubnet“ beträgt /26. Weitere Informationen zur Subnetzgröße finden Sie unter [Azure Firewall – Häufig gestellte Fragen](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

10. Geben Sie unter **Adressbereich** die Zeichenfolge **10.0.1.0/26** ein.
11. Verwenden Sie für die anderen Einstellungen die Standardwerte, und klicken Sie dann auf **Erstellen**.

### <a name="create-a-spoke-vnet"></a>Erstellen eines Spoke-VNET

1. Wählen Sie auf der Startseite des Azure-Portals **Alle Dienste** aus.
2. Klicken Sie unter **Netzwerk** auf **Virtuelle Netzwerke**.
3. Wählen Sie **Hinzufügen**.
4. Geben Sie unter **Name** den Namen **VN-Spoke** ein.
5. Geben Sie unter **Adressraum** die Zeichenfolge **192.168.0.0/16** ein.
6. Wählen Sie unter **Abonnement** Ihr Abonnement aus.
7. Klicken Sie unter **Ressourcengruppe** auf **Vorhandene verwenden**, und wählen Sie anschließend **RG-DNAT-Test** aus.
8. Wählen Sie unter **Standort** den gleichen Standort aus wie zuvor.
9. Geben Sie unter **Subnetz** für **Name** den Namen **SN-Workload** ein.

    Der Server befindet sich in diesem Subnetz.
10. Geben Sie unter **Adressbereich** die Zeichenfolge **192.168.1.0/24** ein.
11. Verwenden Sie für die anderen Einstellungen die Standardwerte, und klicken Sie dann auf **Erstellen**.

### <a name="peer-the-vnets"></a>Verknüpfen der VNETs per Peering

Führen Sie nun das Peering für die beiden VNETs durch.

1. Klicken Sie auf das virtuelle Netzwerk **VN-Hub**.
2. Klicken Sie unter **Einstellungen** auf **Peerings**.
3. Wählen Sie **Hinzufügen**.
4. Geben Sie **Peer-HubSpoke** als **Name für das Peering von „VN-Hub“ zu „VN-Spoke“** ein.
5. Wählen Sie **VN-Spoke** für das virtuelle Netzwerk aus.
6. Geben Sie **Peer-SpokeHub** als **Name für das Peering von „VN-Spoke“ zu „VN-Hub“** ein.
7. Klicken Sie unter **Weitergeleiteten Datenverkehr von „VN-Spoke“ nach „VN-Hub“ zulassen** auf **Aktiviert**.
8. Klicken Sie auf **OK**.

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie einen virtuellen Workloadcomputer, und ordnen Sie ihn im Subnetz **SN-Workload** an.

1. Wählen Sie im Menü des Azure-Portals die Option **Ressource erstellen** aus.
2. Wählen Sie unter **Beliebt** die Option **Windows Server 2016 Datacenter** aus.

**Grundlagen**

1. Wählen Sie unter **Abonnement** Ihr Abonnement aus.
1. Klicken Sie unter **Ressourcengruppe** auf **Vorhandene verwenden**, und wählen Sie anschließend **RG-DNAT-Test** aus.
1. Geben Sie unter **Name des virtuellen Computers** **Srv-Workload** ein.
1. Wählen Sie unter **Region** denselben Standort aus wie zuvor.
1. Geben Sie einen Benutzernamen und ein Kennwort ein.
1. Klicken Sie auf **Weiter: Datenträger**.

**Datenträger**
1. Klicken Sie auf **Weiter: Netzwerk** aus.

**Netzwerk**

1. Klicken Sie unter **Virtuelles Netzwerk** auf **VN-Spoke**.
2. Wählen Sie unter **Subnetz** die Option **SN-Workload**.
3. Klicken Sie unter **Öffentliche IP-Adresse** auf **Keine**.
4. Klicken Sie unter **Öffentliche Eingangsports** auf **Keine**. 
2. Lassen Sie die restlichen Standardeinstellungen unverändert, und wählen Sie **Weiter: Verwaltung** aus.

**Verwaltung**

1. Wählen Sie unter **Startdiagnose** die Option **Aus** aus.
1. Klicken Sie auf **Überprüfen + erstellen**.

**Überprüfen + erstellen**

Überprüfen Sie die Zusammenfassung, und klicken Sie auf **Erstellen**. Dies nimmt einige Minuten in Anspruch.

Nachdem die Bereitstellung abgeschlossen ist, können Sie sich die private IP-Adresse für den virtuellen Computer notieren. Sie wird später beim Konfigurieren der Firewall benötigt. Klicken Sie auf den Namen des virtuellen Computers und dann unter **Einstellungen** auf **Netzwerk**, um nach der privaten IP-Adresse zu suchen.

## <a name="deploy-the-firewall"></a>Bereitstellen der Firewall

1. Wählen Sie auf der Startseite des Portals **Ressource erstellen** aus.
2. Klicken Sie auf **Netzwerk** und nach **Empfohlen** auf **Alle anzeigen**.
3. Klicken Sie auf **Firewall** und anschließend auf **Erstellen**. 
4. Konfigurieren Sie die Firewall auf der Seite **Firewall erstellen** anhand der folgenden Tabelle:

   |Einstellung  |Wert  |
   |---------|---------|
   |Name     |FW-DNAT-test|
   |Subscription     |\<your subscription\>|
   |Resource group     |**Vorhandene verwenden**: RG-DNAT-Test |
   |Standort     |Wählen Sie den gleichen Standort aus wie zuvor.|
   |Virtuelles Netzwerk auswählen     |**Vorhandene verwenden**: VN-Hub|
   |Öffentliche IP-Adresse     |**Neu erstellen**. Die öffentliche IP-Adresse muss vom Standard-SKU-Typ sein.|

5. Klicken Sie auf **Überprüfen + erstellen**.
6. Überprüfen Sie die Zusammenfassung, und wählen Sie dann **Erstellen** aus, um die Firewall zu erstellen.

   Die Bereitstellung dauert einige Minuten.
7. Navigieren Sie nach Abschluss der Bereitstellung zur Ressourcengruppe **RG-DNAT-Test**, und klicken Sie auf die Firewall **FW-DNAT-test**.
8. Notieren Sie sich die private IP-Adresse. Diese wird später für die Erstellung der Standardroute benötigt.

## <a name="create-a-default-route"></a>Erstellen einer Standardroute

Konfigurieren Sie die ausgehende Standardroute für das Subnetz **SN-Workload** so, dass sie die Firewall durchläuft.

1. Wählen Sie auf der Startseite des Azure-Portals **Alle Dienste** aus.
2. Wählen Sie unter **Netzwerk** die Option **Routingtabellen** aus.
3. Wählen Sie **Hinzufügen**.
4. Geben Sie unter **Name** den Namen **RT-FWroute** ein.
5. Wählen Sie unter **Abonnement** Ihr Abonnement aus.
6. Wählen Sie unter **Ressourcengruppe** die Option **Vorhandene verwenden**, und wählen Sie **RG-DNAT-Test** aus.
7. Wählen Sie unter **Standort** den gleichen Standort aus wie zuvor.
8. Klicken Sie auf **Erstellen**.
9. Klicken Sie auf **Aktualisieren** und anschließend auf die Routingtabelle **RT-FWroute**.
10. Klicken Sie auf **Subnetze** und dann auf **Zuordnen**.
11. Klicken Sie auf **Virtuelles Netzwerk** und anschließend auf **VN-Spoke**.
12. Wählen Sie unter **Subnetz** die Option **SN-Workload**.
13. Klicken Sie auf **OK**.
14. Klicken Sie auf **Routen** und dann auf **Hinzufügen**.
15. Geben Sie unter **Routenname** die Zeichenfolge **FW-DG** ein.
16. Geben Sie unter **Adresspräfix** die Zeichenfolge **0.0.0.0/0** ein.
17. Wählen Sie unter **Typ des nächsten Hops** die Option **Virtuelles Gerät** aus.

    Azure Firewall ist eigentlich ein verwalteter Dienst, in dieser Situation kann aber „Virtuelles Gerät“ verwendet werden.
18. Geben Sie unter **Adresse des nächsten Hops** die private IP-Adresse für die Firewall ein, die Sie sich zuvor notiert haben.
19. Klicken Sie auf **OK**.

## <a name="configure-a-nat-rule"></a>Konfigurieren einer NAT-Regel

1. Öffnen Sie **RG-DNAT-Test**, und klicken Sie auf die Firewall **FW-DNAT-test**. 
2. Klicken Sie auf der Seite **FW-DNAT-test** unter **Einstellungen** auf **Regeln**. 
3. Klicken Sie auf **NAT-Regelsammlung hinzufügen**. 
4. Geben Sie unter **Name** den Namen **RC-DNAT-01** ein. 
5. Geben Sie für **Priorität** den Wert **200** ein. 
6. Geben Sie unter **Regeln** für **Name** die Zeichenfolge **RL-01** ein.
7. Wählen Sie für **Protokoll** die Option **TCP** aus.
8. Geben Sie unter **Quelladressen** das Zeichen „*“ ein. 
9. Geben Sie unter **Zieladressen** die öffentliche IP-Adresse der Firewall ein. 
10. Geben Sie unter **Zielports** den Wert **3389** ein. 
11. Geben Sie für **Übersetzte Adresse** die private IP-Adresse für den virtuellen Computer „Srv-Workload“ ein. 
12. Geben Sie für **Übersetzter Port** den Wert **3389** ein. 
13. Wählen Sie **Hinzufügen**. 

## <a name="test-the-firewall"></a>Testen der Firewall

1. Verbinden Sie einen Remotedesktop mit der öffentlichen IP-Adresse der Firewall. Sie sollten mit dem virtuellen Computer **Srv-Workload** verbunden werden.
2. Schließen Sie den Remotedesktop.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können die Firewallressourcen für das nächste Tutorial behalten oder die Ressourcengruppe **RG-DNAT-Test** löschen, wenn Sie sie nicht mehr benötigen. Dadurch werden alle firewallbezogenen Ressourcen gelöscht.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Einrichten einer Netzwerkumgebung zu Testzwecken
> * Bereitstellen einer Firewall
> * Erstellen einer Standardroute
> * Konfigurieren einer DNAT-Regel
> * Testen der Firewall

Als Nächstes können Sie die Azure Firewall-Protokolle überwachen.

> [!div class="nextstepaction"]
> [Tutorial: Überwachen von Azure Firewall-Protokollen](./firewall-diagnostics.md)