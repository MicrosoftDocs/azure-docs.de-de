---
title: Lastenausgleich bei Konfigurationen mit mehreren IP-Adressen – Azure-Portal
titlesuffix: Azure Load Balancer
description: Lastenausgleich in Konfigurationen mit primären und sekundären IP-Adressen
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: se0dec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 0cf5aa45e1e8a28dfcdadac0ea32658e5993d06c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60591713"
---
# <a name="load-balancing-on-multiple-ip-configurations-by-using-the-azure-portal"></a>Lastenausgleich bei Konfigurationen mit mehreren IP-Adressen mithilfe des Azure-Portals

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [PowerShell](load-balancer-multiple-ip-powershell.md)
> * [BEFEHLSZEILENSCHNITTSTELLE (CLI)](load-balancer-multiple-ip-cli.md)


In diesem Artikel wird veranschaulicht, wie Sie Azure Load Balancer mit mehreren IP-Adressen auf einer sekundären Netzwerkschnittstellenkarte (Network Interface Card, NIC) verwenden. Dieses Szenario ist im folgenden Diagramm dargestellt:

![Load Balancer-Szenarios](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

In diesem Szenario verwenden wir die folgende Konfiguration:

- Zwei virtuelle Computer (VMs), auf denen Windows ausgeführt wird.
- Jede VM verfügt über eine primäre und eine sekundäre NIC.
- Jede sekundäre NIC verfügt über zwei IP-Konfigurationen.
- Jeder virtuelle Computer hostet zwei Websites: „contoso.com“ und „fabrikam.com“.
- Jede Website ist an eine IP-Konfiguration auf der sekundären NIC gebunden.
- Azure Load Balancer wird verwendet, um zwei Front-End-IP-Adressen verfügbar zu machen (eine für jede Website). Die Front-End-Adressen werden verwendet, um Datenverkehr auf die jeweilige IP-Konfiguration für jede Website zu verteilen.
- Die gleiche Portnummer wird für beide Front-End-IP-Adressen und Back-End-Pool-IP-Adressen verwendet.

## <a name="prerequisites"></a>Voraussetzungen

Für unser Szenariobeispiel wird vorausgesetzt, dass Sie über die Ressourcengruppe **contosofabrikam** verfügen, die wie folgt konfiguriert ist:

- Die Ressourcengruppe enthält ein virtuelles Netzwerk mit dem Namen **myVNet**.
- Das Netzwerk **myVNet** enthält die beiden VMs **VM1** und **VM2**.
- VM1 und VM2 sind in derselben Verfügbarkeitsgruppe **myAvailset** enthalten. 
- VM1 und VM2 verfügen jeweils über eine primäre NIC mit dem Namen **VM1NIC1** bzw. **VM2NIC1**. 
- VM1 und VM2 verfügen jeweils über eine sekundäre NIC mit dem Namen **VM1NIC2** bzw. **VM2NIC2**.

Weitere Informationen zum Erstellen von virtuellen Computern mit mehreren NICs finden Sie unter [Erstellen und Verwalten eines virtuellen Windows-Computers mit mehreren Netzwerkkarten](../virtual-machines/windows/multiple-nics.md).

## <a name="perform-load-balancing-on-multiple-ip-configurations"></a>Durchführen des Lastenausgleichs bei Konfigurationen mit mehreren IP-Adressen

Führen Sie die unten angegebenen Schritte aus, um das in diesem Artikel beschriebene Szenario umzusetzen.

### <a name="step-1-configure-the-secondary-nics"></a>Schritt 1: Konfigurieren der sekundären NICs

Fügen Sie für jeden virtuellen Computer in Ihrem virtuellen Netzwerk die IP-Konfiguration für die sekundäre NIC hinzu:  

1. Navigieren Sie zum Azure-Portal: https://portal.azure.com. Melden Sie sich mit Ihrem Azure-Konto an.

2. Wählen Sie oben links auf dem Bildschirm das Symbol **Ressourcengruppe**. Wählen Sie anschließend die Ressourcengruppe aus, auf der sich Ihre VMs befinden (z.B. **contosofabrikam**). Im Bereich **Ressourcengruppen** werden alle Ressourcen und NICs für die VMs angezeigt.

3. Fügen Sie für die sekundäre NIC jeder VM die IP-Konfiguration hinzu:

    1. Wählen Sie die sekundäre NIC aus, die Sie konfigurieren möchten.
    
    2. Wählen Sie die **IP-Konfigurationen** aus. Wählen Sie oben im nächsten Bereich die Option **Hinzufügen**.

    3. Fügen Sie der NIC unter **IP-Konfiguration hinzufügen** eine zweite IP-Konfiguration hinzu: 

        1. Geben Sie einen Namen für die sekundäre IP-Konfiguration ein. (Nennen Sie die IP-Konfiguration für VM1 und VM2 **VM1NIC2-ipconfig2** bzw. **VM2NIC2-ipconfig2**.)

        2. Wählen Sie für **Private IP-Adresse** unter **Allocation** (Zuteilung) die Option **Statisch**.

        3. Klicken Sie auf **OK**.

Nachdem die zweite IP-Konfiguration für die sekundäre NIC abgeschlossen ist, wird sie in den Einstellungen unter **IP-Konfigurationen** für die betreffende NIC angezeigt.

### <a name="step-2-create-the-load-balancer"></a>Schritt 2: Erstellen des Lastenausgleichs

Erstellen Sie Ihren Lastenausgleich für die Konfiguration:

1. Navigieren Sie zum Azure-Portal: https://portal.azure.com. Melden Sie sich mit Ihrem Azure-Konto an.

2. Wählen Sie oben links auf dem Bildschirm **Ressource erstellen** > **Netzwerk** > **Load Balancer** aus. Wählen Sie als Nächstes die Option **Erstellen**.

3. Geben Sie unter **Lastenausgleich erstellen** einen Namen für Ihren Lastenausgleich ein. In diesem Szenario verwenden wir den Namen **mylb**.

4. Erstellen Sie unter **Öffentliche IP-Adresse** eine neue öffentliche IP-Adresse mit dem Namen **PublicIP1**.

5. Wählen Sie unter **Ressourcengruppe** die vorhandene Ressourcengruppe für Ihre VMs aus (z.B. **contosofabrikam**). Wählen Sie den Standort für die Bereitstellung Ihres Lastenausgleichs aus, und wählen Sie dann **OK**.

Die Bereitstellung des Lastenausgleichs beginnt. Es kann einige Minuten dauern, bis die Bereitstellung abgeschlossen ist. Nach Abschluss der Bereitstellung wird der Lastenausgleich als Ressource in Ihrer Ressourcengruppe angezeigt.

### <a name="step-3-configure-the-front-end-ip-pool"></a>Schritt 3: Konfigurieren des Front-End-IP-Pools

Konfigurieren Sie für jede Website (contoso.com und fabrikam.com) den Front-End-IP-Pool für Ihren Lastenausgleich:

1. Wählen Sie im Portal die Option **Weitere Dienste**. Geben Sie im Filterfeld den Text **Öffentliche IP-Adresse** ein, und wählen Sie die Option **Öffentliche IP-Adressen**. Wählen Sie oben im nächsten Bereich die Option **Hinzufügen**.

2. Konfigurieren Sie zwei öffentliche IP-Adressen (**PublicIP1** und **PublicIP2**) für beide Websites (contoso.com und fabrikam.com):

   1. Geben Sie einen Namen für Ihre Front-End-IP-Adresse ein.

   2. Wählen Sie unter **Ressourcengruppe** die vorhandene Ressourcengruppe für Ihre VMs aus (z.B. **contosofabrikam**).

   3. Wählen Sie als **Speicherort** den gleichen Speicherort wie für die virtuellen Computer aus.

   4. Klicken Sie auf **OK**.

      Nachdem die öffentlichen IP-Adressen erstellt wurden, werden sie unter **Öffentliche IP** angezeigt.

3. <a name="step3-3"></a>Wählen Sie im Portal die Option **Weitere Dienste**. Geben Sie im Filterfeld **Load Balancer** ein, und wählen Sie **Load Balancer**. 

4. Wählen Sie den Lastenausgleich (**mylb**) aus, dem Sie den Front-End-IP-Pool hinzufügen möchten.

5. Klicken Sie unter **Einstellungen** auf **Front-End-IP-Konfiguration**. Wählen Sie oben im nächsten Bereich die Option **Hinzufügen**.

6. Geben Sie einen Namen für Ihre Front-End-IP-Adresse ein (z.B. **contosofe** oder **fabrikamfe**).

7. <a name="step3-7"></a>Wählen Sie die Option **IP-Adresse**. Wählen Sie unter **Öffentliche IP-Adresse wählen** die IP-Adressen für Ihr Front-End aus (**PublicIP1** oder **PublicIP2**).

8. Erstellen Sie die zweite Front-End-IP-Adresse, indem Sie <a href="#step3-3">Schritt 3</a> bis <a href="#step3-7">Schritt 7</a> dieses Abschnitts wiederholen.

Nachdem der Front-End-Pool konfiguriert wurde, werden die IP-Adressen in den Einstellungen für Ihren Lastenausgleich unter **Front-End-IP-Konfiguration** angezeigt. 
    
### <a name="step-4-configure-the-back-end-pool"></a>Schritt 4: Konfigurieren des Back-End-Pools

Konfigurieren Sie für jede Website (contoso.com und fabrikam.com) den Back-End-Adresspool für Ihren Lastenausgleich:
        
1. Wählen Sie im Portal die Option **Weitere Dienste**. Geben Sie im Filterfeld **Load Balancer** ein, und wählen Sie **Load Balancer**.

2. Wählen Sie den Lastenausgleich aus (**mylb**), dem Sie den Back-End-Pool hinzufügen möchten.

3. Wählen Sie unter **Einstellungen** die Option **Back-End-Pools** aus. Geben Sie einen Namen für den Back-End-Pool ein (z.B. **contosopool** oder **fabrikampool**). Wählen Sie oben im nächsten Bereich die Option **Hinzufügen**. 

4. Wählen Sie als **Verknüpft mit** **Verfügbarkeitsgruppe** aus.

5. Wählen Sie unter **Verfügbarkeitsgruppe** die Option **meineVerfügbarkeitsgruppe** aus.

6. Fügen Sie die Zielnetzwerk-IP-Konfigurationen für beide VMs hinzu: 

    ![Konfigurieren der Back-End-Pools für den Lastenausgleich](./media/load-balancer-multiple-ip/lb-backendpool.PNG)
    
    1. Wählen Sie für **Virtueller Zielcomputer** den virtuellen Computer aus, den Sie dem Back-End-Pool hinzufügen möchten (z.B. **VM1** oder **VM2**).

    2. Wählen Sie unter **Netzwerk-IP-Konfiguration** die IP-Konfiguration der sekundären NIC für die VM aus, die Sie im vorherigen Schritt ausgewählt haben (z.B. **VM1NIC2-ipconfig2** oder **VM2NIC2-ipconfig2**).

7. Klicken Sie auf **OK**.

Nachdem der Back-End-Pool konfiguriert wurde, werden die Adressen unter den **Back-End-Pool**-Einstellungen für Ihren Lastenausgleich angezeigt.

### <a name="step-5-configure-the-health-probe"></a>Schritt 5: Konfigurieren des Integritätstests

Konfigurieren Sie einen Integritätstest für Ihren Lastenausgleich:

1. Wählen Sie im Portal die Option **Weitere Dienste**. Geben Sie im Filterfeld **Load Balancer** ein, und wählen Sie **Load Balancer**.

2. Wählen Sie den Lastenausgleich (**mylb**) aus, dem Sie den Integritätstest hinzufügen möchten.

3. Wählen Sie unter **Einstellungen** die Option **Integritätstest** aus. Wählen Sie oben im nächsten Bereich die Option **Hinzufügen**. 

4. Geben Sie einen Namen für den Integritätstest ein (z.B. **HTTP**). Klicken Sie auf **OK**.

### <a name="step-6-configure-load-balancing-rules"></a>Schritt 6: Konfigurieren von Lastenausgleichsregeln

Konfigurieren Sie für jede Website (contoso.com und fabrikam.com) die Lastenausgleichsregeln:
    
1. <a name="step6-1"></a>Wählen Sie unter **Einstellungen** die Option **Lastenausgleichsregeln**. Wählen Sie oben im nächsten Bereich die Option **Hinzufügen**. 

2. Geben Sie unter **Name** einen Namen für die Lastenausgleichsregel ein (z.B. **HTTPc** für contoso.com oder **HTTPf** für fabrikam.com).

3. Wählen Sie unter **Front-End-IP-Adresse** die Front-End-IP-Adresse aus, die Sie zuvor erstellt haben (z.B. **contosofe** oder **fabrikamfe**).

4. Behalten Sie für **Port** und **Back-End-Port** den Standardwert **80** bei.

5. Wählen Sie unter **Floating IP (Direct Server Return)** die Option **Deaktiviert** aus.

6. <a name="step6-6"></a>Wählen Sie **OK**.

7. Erstellen Sie die zweite Lastenausgleichsregel, indem Sie <a href="#step6-1">Schritt 1</a> bis <a href="#step6-6">Schritt 6</a> dieses Abschnitts wiederholen.

Nachdem die Regeln konfiguriert wurden, werden sie unter den Einstellungen für **Lastenausgleichsregeln** Ihres Lastenausgleichs angezeigt.

### <a name="step-7-configure-dns-records"></a>Schritt 7: Konfigurieren von DNS-Einträgen

Im letzten Schritt konfigurieren Sie Ihre DNS-Ressourceneinträge so, dass sie auf die entsprechenden Front-End-IP-Adressen für Ihren Lastenausgleich zeigen. Sie können Ihre Domänen in Azure DNS hosten. Weitere Informationen zur Verwendung von Azure DNS mit Lastenausgleich finden Sie unter [Verwenden von Azure DNS mit anderen Azure-Diensten](../dns/dns-for-azure-services.md).

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie in [Verwenden von Lastenausgleichsdiensten in Azure](../traffic-manager/traffic-manager-load-balancing-azure.md), wie Sie Lastenausgleichsdienste in Azure kombinieren.
- Erfahren Sie in [Azure Monitor-Protokolle für den Azure Load Balancer](../load-balancer/load-balancer-monitor-log.md), wie Sie verschiedene Protokolltypen verwenden, um den Load Balancer zu verwalten und eventuelle Fehler zu beheben.
