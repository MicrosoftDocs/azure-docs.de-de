---
title: Lastenausgleichskonfiguration (nur ausgehender Datenverkehr)
titleSuffix: Azure Load Balancer
description: In diesem Artikel wird beschrieben, wie Sie einen internen Lastenausgleich mit ausgehender NAT erstellen.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2020
ms.author: allensu
ms.openlocfilehash: ee264a22de5ce094e8a4c1335ace77cbbba49270
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694992"
---
# <a name="outbound-only-load-balancer-configuration"></a>Lastenausgleichskonfiguration (nur ausgehender Datenverkehr)

Verwenden Sie eine Kombination aus internen und externen Load Balancer Standard-Instanzen, um für virtuelle Computer hinter einem internen Lastenausgleichsmodul die Konnektivität in ausgehender Richtung sicherzustellen. 

Diese Konfiguration ermöglicht ausgehende NAT (Network Address Translation, Netzwerkadressenübersetzung) für ein internes Lastenausgleichsmodul, um für Ihren Back-End-Pool ein Setup vom Typ „Nur ausgehend“ zu erhalten.


<p align="center">
  <img src="./media/egress-only/load-balancer-egress-only.png" alt="Figure depicts a egress only load balancer configuration." width="500" title="Virtual Network NAT">
</p>


<!-- 
:::image type="content" source="./media/egress-only/load-balancer-egress-only.png" alt-text="Figure depicts a egress only load balancer configuration" border="true":::
-->


*Abbildung: Lastenausgleichskonfiguration vom Typ „Nur ausgehend“*

Folgende Schritte sind erforderlich:

1. Erstellen eines virtuellen Netzwerks mit einem Bastionhost
2. Erstellen eines virtuellen Computers nur mit einer privaten IP-Adresse
3. Erstellen von internen und öffentlichen Load Balancer Standard-Instanzen
4. Hinzufügen von Back-End-Pools zu beiden Lastenausgleichsmodulen und Anordnen der VM in jedem Pool
5. Stellen Sie eine Verbindung mit Ihrer VM über den Bastionhost her, und führen Sie dann Folgendes durch:
    1. Testen der ausgehenden Konnektivität 
    2. Konfigurieren einer Ausgangsregel auf dem öffentlichen Lastenausgleichsmodul
    3. Erneutes Testen der ausgehenden Konnektivität

## <a name="create-virtual-network-and-virtual-machine"></a>Erstellen eines virtuellen Netzwerks und eines virtuellen Computers

Erstellen eines virtuellen Netzwerks mit zwei Subnetzen:

* Primäres Subnetz
* Bastionsubnetz

Erstellen eines virtuellen Computers im neuen virtuellen Netzwerk

### <a name="create-the-virtual-network"></a>Erstellen des virtuellen Netzwerks

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie links oben auf dem Bildschirm **Ressource erstellen > Netzwerk > Virtuelles Netzwerk** aus, oder suchen Sie über das Suchfeld nach **Virtuelles Netzwerk**.

2. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | **Einstellung**          | **Wert**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektdetails**  |                                                                 |
    | Subscription     | Auswählen des Azure-Abonnements                                  |
    | Ressourcengruppe   | Wählen Sie **Neu erstellen**. </br> Geben Sie **myResourceGroupLB** ein. </br> Klicken Sie auf **OK**. |
    | **Instanzendetails** |                                                                 |
    | Name             | Geben Sie **myVNet** ein.                                    |
    | Region           | Wählen Sie **USA, Osten 2** aus. |

3. Wählen Sie die Registerkarte **IP-Adressen** oder die Schaltfläche **Weiter: IP-Adressen** am unteren Seitenrand aus.

4. Geben Sie auf der Registerkarte **IP-Adressen** die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | IPv4-Adressraum | Geben Sie **10.1.0.0/16** ein. |

5. Wählen Sie unter **Subnetzname** das Wort **Standard** aus.

6. Geben Sie unter **Subnetz bearbeiten** die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | Subnetzname | Geben Sie **myBackendSubnet** ein. |
    | Subnetzadressbereich | Geben Sie **10.1.0.0/24** ein. |

7. Wählen Sie **Speichern** aus.

8. Wählen Sie die Registerkarte **Sicherheit** .

9. Wählen Sie unter **BastionHost** die Option **Aktivieren** aus. Geben Sie die folgenden Informationen ein:

    | Einstellung            | Wert                      |
    |--------------------|----------------------------|
    | Bastion-Name | Geben Sie **myBastionHost** ein. |
    | AzureBastionSubnet-Adressraum | Geben Sie **10.1.1.0/24** ein. |
    | Öffentliche IP-Adresse | Wählen Sie **Neu erstellen**. </br> Geben Sie **myBastionIP** als **Name** ein. </br> Klicken Sie auf **OK**. |


8. Wählen Sie die Registerkarte **Überprüfen + erstellen** oder die Schaltfläche **Überprüfen + erstellen** aus.

9. Klicken Sie auf **Erstellen**.

### <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

1. Navigieren Sie links oben im Portal zu **Ressource erstellen** > **Compute** > **Virtueller Computer**. 
   
2. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Werte ein, oder wählen Sie sie aus:

    | Einstellung | Wert                                          |
    |-----------------------|----------------------------------|
    | **Projektdetails** |  |
    | Subscription | Auswählen des Azure-Abonnements |
    | Ressourcengruppe | Wählen Sie **myResourceGroupLB** aus. |
    | **Instanzendetails** |  |
    | Name des virtuellen Computers | Geben Sie **myVM** ein. |
    | Region | Wählen Sie **USA, Osten 2** aus. |
    | Verfügbarkeitsoptionen | Wählen Sie **Keine Infrastrukturredundanz erforderlich** aus. |
    | Image | Wählen Sie **Windows Server 2019 Datacenter** aus. |
    | Azure Spot-Instanz | Wählen Sie **Nein** aus. |
    | Size | Wählen Sie eine VM-Größe aus, oder übernehmen Sie die Standardeinstellung. |
    | **Administratorkonto** |  |
    | Username | Geben Sie einen Benutzernamen ein. |
    | Kennwort | Geben Sie ein Kennwort ein. |
    | Kennwort bestätigen | Geben Sie das Kennwort erneut ein. |
    | **Regeln für eingehende Ports** |  |
    | Öffentliche Eingangsports | Wählen Sie **Ausgewählte Ports zulassen** aus. |
    | Eingangsports auswählen | Wählen Sie **RDP (3389)** aus. |

3. Wählen Sie die Registerkarte **Netzwerk** aus, oder wählen Sie **Weiter: Datenträger** und anschließend **Weiter: Netzwerk** aus.
  
4. Wählen Sie auf der Registerkarte „Netzwerk“ die folgenden Werte aus, oder geben Sie sie ein:

    | Einstellung | Wert |
    |-----|------------|
    | **Netzwerkschnittstelle** |  |
    | Virtuelles Netzwerk | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Öffentliche IP-Adresse | Wählen Sie **Keine** aus. |
    | NIC-Netzwerksicherheitsgruppe | Wählen Sie **Keine** aus.|
    | Diese VM hinter einer vorhandenen Lastenausgleichslösung platzieren? | Wählen Sie **Nein** aus. |
   
5. Wählen Sie die Registerkarte **Verwaltung** oder **Weiter** > **Verwaltung**.

6. Wählen Sie auf der Registerkarte **Verwaltung** die folgenden Werte aus, oder geben Sie sie ein:
    
    | Einstellung | Wert |
    |-|-|
    | **Überwachung** |  |
    | Startdiagnose | Wählen Sie **Aus** aus. |
   
7. Klicken Sie auf **Überprüfen + erstellen**. 
  
8. Überprüfen Sie die Einstellungen, und wählen Sie dann die Option **Erstellen**.

## <a name="create-load-balancers-and-test-connectivity"></a>Erstellen von Lastenausgleichsmodulen und Testen der Konnektivität

Verwenden Sie das Azure-Portal, um Folgendes zu erstellen:

* Interner Lastenausgleich
* Öffentlicher Load Balancer
 
Fügen Sie Ihre erstellte VM jeweils dem Back-End-Pool hinzu.  Anschließend richten Sie eine Konfiguration ein, bei der nur die ausgehende Konnektivität von Ihrer VM zulässig ist und vorher und nachher Tests durchgeführt werden.

### <a name="create-internal-load-balancer"></a>Erstellen eines internen Lastenausgleichs

1. Wählens Sie links oben auf dem Bildschirm die Optionen **Ressource erstellen** > **Netzwerk** > **Lastenausgleich** aus.

2. Geben Sie auf der Seite **Lastenausgleich erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, bzw. wählen Sie sie aus: 

    | Einstellung                 | Wert                                              |
    | ---                     | ---                                                |
    | Subscription               | Wählen Sie Ihr Abonnement aus.    |    
    | Resource group         | Wählen Sie die im vorherigen Schritt erstellte Ressourcengruppe **myResourceGroupLB** aus.|
    | Name                   | Geben Sie **myInternalLoadBalancer** ein.                                   |
    | Region         | Wählen Sie **USA, Osten 2** aus.                                        |
    | type          | Wählen Sie **Intern** aus.                                        |
    | SKU           | Wählen Sie **Standard** aus. |
    | Virtuelles Netzwerk | Wählen Sie das im vorherigen Schritt erstellte virtuelle Netzwerk **myVNet** aus. |
    | Subnet  | Wählen Sie das im vorherigen Schritt erstellte Subnetz **myBackendSubnet** aus. |
    | IP-Adresszuweisung | Wählen Sie **Dynamisch** aus. |

3. Übernehmen Sie bei den anderen Einstellungen die Standardwerte, und wählen Sie **Überprüfen + erstellen** aus.

4. Wählen Sie auf der Registerkarte **Bewerten + erstellen** die Option **Erstellen** aus.   

### <a name="create-public-load-balancer"></a>Erstellen eines öffentlichen Lastenausgleichsmoduls

1. Wählens Sie links oben auf dem Bildschirm die Optionen **Ressource erstellen** > **Netzwerk** > **Lastenausgleich** aus.

2. Geben Sie auf der Seite **Lastenausgleich erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, bzw. wählen Sie sie aus: 

    | Einstellung                 | Wert                                              |
    | ---                     | ---                                                |
    | Subscription               | Wählen Sie Ihr Abonnement aus.    |    
    | Resource group         | Wählen Sie **Neu erstellen** aus, und geben Sie **myResourceGroupLB** in das Textfeld ein.|
    | Name                   | Geben Sie **myPublicLoadBalancer** ein.                                   |
    | Region         | Wählen Sie **USA, Osten 2** aus.                                        |
    | type          | Wählen Sie **Öffentlich** aus.                                        |
    | SKU           | Wählen Sie **Standard** aus. |
    | Öffentliche IP-Adresse | Wählen Sie **Neu erstellen**. |
    | Name der öffentlichen IP-Adresse | Geben Sie im Textfeld den Text **myFrontendIP** ein.|
    | Verfügbarkeitszone | Wählen Sie **Zonenredundant** aus. |
    | Öffentliche IPv6-Adresse hinzufügen | Wählen Sie **Nein** aus. |

3. Übernehmen Sie bei den anderen Einstellungen die Standardwerte, und wählen Sie **Überprüfen + erstellen** aus.

4. Wählen Sie auf der Registerkarte **Bewerten + erstellen** die Option **Erstellen** aus.   
   
### <a name="create-internal-backend-address-pool"></a>Erstellen eines internen Back-End-Adresspools

Erstellen Sie den Back-End-Adresspool **myInternalBackendPool**:

1. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myInternalLoadBalancer** aus.

2. Wählen Sie unter **Einstellungen** die Option **Back-End-Pools** und dann **Hinzufügen** aus.

3. Geben Sie auf der Seite **Back-End-Pool hinzufügen** die Zeichenfolge **myInternalBackendPool** als Namen für Ihren Back-End-Pool ein.
 
4. Wählen Sie in **Virtuelles Netzwerk** die Option **myVNet** aus.

5. Wählen Sie unter **Virtuelle Computer** die Option **Hinzufügen** und dann **myVM** aus.
 
6. Wählen Sie **Hinzufügen** aus.

### <a name="create-public-backend-address-pool"></a>Erstellen eines öffentlichen Back-End-Adresspools

Erstellen Sie den Back-End-Adresspool **myPublicBackendPool**:

1. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myPublicLoadBalancer** aus.

2. Wählen Sie unter **Einstellungen** die Option **Back-End-Pools** und dann **Hinzufügen** aus.

3. Geben Sie auf der Seite **Back-End-Pool hinzufügen** die Zeichenfolge **myPublicBackendPool** als Namen für Ihren Back-End-Pool ein.

4. Wählen Sie in **Virtuelles Netzwerk** die Option **myVNet** aus.
 
5. Wählen Sie unter **Virtuelle Computer** die Option **Hinzufügen** und dann **myVM** aus.
 
6. Wählen Sie **Hinzufügen** aus.

### <a name="test-connectivity-before-outbound-rule"></a>Testen der Konnektivität vor der Ausgangsregel

1. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myVM** (in der Ressourcengruppe **myResourceGroupLB**) aus.

2. Wählen Sie auf der Seite **Übersicht** die Option **Verbinden** und dann **Bastion** aus.

4. Geben Sie den Benutzernamen mit dem zugehörigen Kennwort ein, den Sie bei der VM-Erstellung verwendet haben.

5. Wählen Sie **Verbinden**.

6. Öffnen Sie Internet Explorer.

7. Geben Sie in der Adressleiste **https://whatsmyip.org** ein.

8. Bei der Verbindungsherstellung sollte ein Fehler auftreten. Standardmäßig ist für eine öffentliche Load Balancer Standard-Instanz [kein ausgehender Datenverkehr ohne definierte Ausgangsregel zulässig](load-balancer-overview.md#securebydefault).
 
### <a name="create-a-public-load-balancer-outbound-rule"></a>Erstellen einer öffentlichen Ausgangsregel für den Lastenausgleich

1. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myPublicLoadBalancer** aus.

2. Wählen Sie unter **Einstellungen** die Option **Ausgangsregeln** und dann **Hinzufügen** aus.

3. Verwenden Sie diese Werte zur Konfiguration der Ausgangsregeln:

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myOutboundRule** ein. |
    | Front-End-IP-Adresse | Wählen Sie **LoadBalancerFrontEnd** aus.|
    | Leerlaufzeitüberschreitung (Minuten) | Bewegen Sie den Schieberegler auf **15 Minuten**.|
    | TCP-Zurücksetzung | Wählen Sie **Aktiviert**.|
    | Back-End-Pool | Wählen Sie **myPublicBackendPool** aus.| |
    | Portzuordnung -> Portzuordnung | Wählen Sie **Standardanzahl ausgehender Ports verwenden** aus. |

4. Wählen Sie **Hinzufügen**.

### <a name="test-connectivity-after-outbound-rule"></a>Testen der Konnektivität nach der Ausgangsregel

1. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myVM** (in der Ressourcengruppe **myResourceGroupLB**) aus.

2. Wählen Sie auf der Seite **Übersicht** die Option **Verbinden** und dann **Bastion** aus.

4. Geben Sie den Benutzernamen mit dem zugehörigen Kennwort ein, den Sie bei der VM-Erstellung verwendet haben.

5. Wählen Sie **Verbinden**.

6. Öffnen Sie Internet Explorer.

7. Geben Sie in der Adressleiste **https://whatsmyip.org** ein.

8. Bei der Verbindungsherstellung sollte kein Fehler auftreten.

9. Die angezeigte IP-Adresse sollte die Front-End-IP-Adresse von **myPublicLoadBalancer** sein.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, die Lastenausgleichsmodule, die VM und alle zugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. 

Wählen Sie hierzu die Ressourcengruppe **myResourceGroupLB** und dann die Option **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Konfiguration vom Typ „Nur ausgehend“ mit einer Kombination aus öffentlichen und internen Lastenausgleichsmodulen erstellt.  

Mit dieser Konfiguration können Sie einen Lastenausgleich für eingehenden internen Datenverkehr für Ihren Back-End-Pool durchführen, während gleichzeitig alle öffentlichen eingehenden Verbindungen verhindert werden.

- Informieren Sie sich über [Azure Load Balancer](load-balancer-overview.md).
- Informieren Sie sich über [Ausgehende Verbindungen in Azure](load-balancer-outbound-connections.md).
- Load Balancer: [Häufig gestellte Fragen](load-balancer-faqs.md).
- Informieren Sie sich über [Azure Bastion](../bastion/bastion-overview.md).