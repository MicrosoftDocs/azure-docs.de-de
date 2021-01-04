---
title: 'Schnellstart: Erstellen eines öffentlichen Lastenausgleichs: Azure-Portal'
titleSuffix: Azure Load Balancer
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie einen Lastenausgleich über das Azure-Portal erstellen.
services: load-balancer
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a load balancer so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2020
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 5f19b55a955b8e3e1500cf14fa221b46808ac857
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94698290"
---
# <a name="quickstart-create-a-public-load-balancer-to-load-balance-vms-using-the-azure-portal"></a>Schnellstart: Erstellen eines öffentlichen Lastenausgleichs für den Lastenausgleich virtueller Computer über das Azure-Portal

Beginnen Sie mit der Verwendung von Azure Load Balancer, indem Sie einen öffentlichen Lastenausgleich und drei virtuelle Computer über das Azure-Portal erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

---

# <a name="standard-sku"></a>[**Standard-SKU**](#tab/option-1-create-load-balancer-standard)

>[!NOTE]
>Ein Lastenausgleich mit Standard-SKU wird für Produktionsworkloads empfohlen.  Weitere Informationen zu SKUs finden Sie unter **[Azure Load Balancer-SKUs](skus.md)** .

In diesem Abschnitt wird ein Lastenausgleich für virtuelle Computer erstellt. 

Wenn Sie einen öffentlichen Lastenausgleich erstellen, muss für den Lastenausgleich auch eine neue, als Front-End konfigurierte öffentliche IP-Adresse erstellt werden. Dieses Front-End hat standardmäßig den Namen **LoadBalancerFrontend**.

1. Wählens Sie links oben auf dem Bildschirm die Optionen **Ressource erstellen** > **Netzwerk** > **Lastenausgleich** aus.

2. Geben Sie auf der Seite **Lastenausgleich erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, bzw. wählen Sie sie aus: 

    | Einstellung                 | Wert                                              |
    | ---                     | ---                                                |
    | Subscription               | Wählen Sie Ihr Abonnement aus.    |    
    | Resource group         | Wählen Sie die Option **Neu erstellen** aus, und geben Sie im Textfeld **CreatePubLBQS-rg** ein.|
    | Name                   | Geben Sie **myLoadBalancer** ein.                                   |
    | Region         | Wählen Sie **Europa, Westen** aus.                                        |
    | type          | Wählen Sie **Öffentlich** aus.                                        |
    | SKU           | Wählen Sie **Standard** aus. |
    | Öffentliche IP-Adresse | Wählen Sie **Neu erstellen**. Wenn Sie über eine vorhandene öffentliche IP-Adresse verfügen, die Sie verwenden möchten, wählen Sie **Vorhandene verwenden** aus. |
    | Name der öffentlichen IP-Adresse | Geben Sie **myPublicIP** in das Textfeld ein.|
    | Verfügbarkeitszone | Wählen Sie **Zonenredundant** aus, um einen resilienten Lastenausgleich zu erstellen. Wählen Sie zum Erstellen eines zonalen Lastenausgleichs eine bestimmte Zone aus 1, 2 oder 3 aus. |
    | Öffentliche IPv6-Adresse hinzufügen | Wählen Sie **Nein** aus. </br> Weitere Informationen zu IPv6-Adressen und zum Lastenausgleich finden Sie unter [Was ist IPv6 für Azure Virtual Network?](../virtual-network/ipv6-overview.md).  |

3. Übernehmen Sie bei den anderen Einstellungen die Standardwerte, und wählen Sie **Überprüfen + erstellen** aus.

4. Wählen Sie auf der Registerkarte **Bewerten + erstellen** die Option **Erstellen** aus.   
    
    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png" alt-text="Erstellen einer Load Balancer Standard-Instanz" border="true":::
 
## <a name="create-load-balancer-resources"></a>Erstellen von Load Balancer-Ressourcen

In diesem Abschnitt wird Folgendes konfiguriert:

* Lastenausgleichseinstellungen für einen Back-End-Adresspool
* Ein Integritätstest
* Eine Lastenausgleichsregel

### <a name="create-a-backend-pool"></a>Erstellen eines Back-End-Pools

Der Back-End-Adresspool enthält die IP-Adressen der virtuellen NICs, die mit dem Lastenausgleich verbunden sind. 

Erstellen Sie den Back-End-Adresspool **myBackendPool**, um virtuelle Computer für den Lastenausgleich von Internetdatenverkehr einzubeziehen.

1. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myLoadBalancer** aus.

2. Wählen Sie unter **Einstellungen** die Option **Back-End-Pools** und dann **Hinzufügen** aus.

3. Geben Sie auf der Seite **Back-End-Pool hinzufügen** die Zeichenfolge **myBackendPool** als Name für Ihren Back-End-Pool ein, und wählen Sie anschließend **Hinzufügen** aus.

### <a name="create-a-health-probe"></a>Erstellen eines Integritätstests

Der Status Ihrer App wird vom Lastenausgleich mithilfe eines Integritätstests überwacht. 

Abhängig von der Reaktion auf Integritätsüberprüfungen werden der Load Balancer-Instanz durch den Integritätstest virtuelle Computer hinzugefügt oder daraus entfernt. 

Erstellen Sie zur Überwachung der Integrität der virtuellen Computer einen Integritätstest mit dem Namen **myHealthProbe**.

1. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myLoadBalancer** aus.

2. Wählen Sie unter **Einstellungen** die Option **Integritätstests** und dann **Hinzufügen** aus.
    
    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myHealthProbe** ein. |
    | Protocol | Wählen Sie **HTTP** aus. |
    | Port | Geben Sie **80** ein.|
    | Intervall | Geben Sie für das **Intervall** den Wert **15** (Sekunden zwischen Testversuchen) ein. |
    | Fehlerhafter Schwellenwert | Wählen Sie **2** als Wert für den **Fehlerschwellenwert** bzw. als Anzahl aufeinander folgender Testfehler aus, die auftreten müssen, damit ein virtueller Computer als fehlerhaft eingestuft wird.|
    | | |

3. Übernehmen Sie die übrigen Standardeinstellungen, und wählen Sie **OK** aus.

### <a name="create-a-load-balancer-rule"></a>Erstellen einer Load Balancer-Regel

Mithilfe einer Load Balancer-Regel wird definiert, wie Datenverkehr auf die virtuellen Computer verteilt werden soll. Sie definieren die Front-End-IP-Konfiguration für den eingehenden Datenverkehr und den Back-End-IP-Pool für den Empfang des Datenverkehrs. Quell- und Zielport werden in der Regel definiert. 

In diesem Abschnitt wird eine Lastenausgleichsregel mit folgenden Merkmalen erstellt:

* Sie heißt **myHTTPRule**.
* Sie befindet sich im Front-End **LoadBalancerFrontEnd**.
* Sie lauscht a **Port 80**.
* Sie leitet vom Lastenausgleich verteilten Datenverkehr an den **Port 80** des Back-Ends **myBackendPool** weiter.

1. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myLoadBalancer** aus.

2. Wählen Sie unter **Einstellungen** die Option **Lastenausgleichsregeln** und dann **Hinzufügen** aus.

3. Konfigurieren Sie die Lastenausgleichsregel mit folgenden Werten:
    
    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myHTTPRule** ein. |
    | IP-Version | Wählen Sie **IPv4** aus. |
    | Front-End-IP-Adresse | Wählen Sie **LoadBalancerFrontEnd** aus. |
    | Protocol | Wählen Sie **TCP** aus. |
    | Port | Geben Sie **80** ein.|
    | Back-End-Port | Geben Sie **80** ein. |
    | Back-End-Pool | Wählen Sie **myBackendPool** aus.|
    | Integritätstest | Wählen Sie **myHealthProbe** aus. |
    | Leerlaufzeitüberschreitung (Minuten) | Bewegen Sie den Schieberegler auf **15 Minuten**. |
    | TCP-Zurücksetzung | Wählen Sie **Aktiviert**. |
    | Übersetzung der Quellnetzwerkadresse (SNAT) für ausgehenden Datenverkehr | Wählen Sie **(Empfohlen) Verwenden Sie Ausgangsregeln, um Back-End-Poolmitgliedern Zugriff auf das Internet zu gewähren** aus. |

4. Übernehmen Sie die übrigen Standardeinstellungen, und wählen Sie dann **OK** aus.

## <a name="create-backend-servers"></a>Erstellen von Back-End-Servern

In diesem Abschnitt führen Sie folgende Schritte aus:

* Erstellen eines virtuellen Netzwerks
* Erstellen von drei virtuellen Computern für den Back-End-Pool des Lastenausgleichs
* Installieren von IIS auf den virtuellen Computern, um den Lastenausgleich zu testen

## <a name="create-the-virtual-network"></a>Erstellen des virtuellen Netzwerks

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk und ein Subnetz.

1. Wählen Sie links oben auf dem Bildschirm **Ressource erstellen > Netzwerk > Virtuelles Netzwerk** aus, oder suchen Sie über das Suchfeld nach **Virtuelles Netzwerk**.

2. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | **Einstellung**          | **Wert**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektdetails**  |                                                                 |
    | Subscription     | Auswählen des Azure-Abonnements                                  |
    | Ressourcengruppe   | Wählen Sie **CreatePubLBQS-rg** aus. |
    | **Instanzendetails** |                                                                 |
    | Name             | Geben Sie **myVNet** ein.                                    |
    | Region           | Wählen Sie **Europa, Westen** aus. |

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

### <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

In diesem Abschnitt werden drei virtuelle Computer (**myVM1**, **myVM2** und **myVM3**) in drei verschiedenen Zonen (**Zone 1**, **Zone 2** und **Zone 3**) erstellt. 

Diese virtuellen Computer werden dem Back-End-Pool des zuvor erstellten Lastenausgleichs hinzugefügt.

1. Navigieren Sie links oben im Portal zu **Ressource erstellen** > **Compute** > **Virtueller Computer**. 
   
2. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Werte ein, oder wählen Sie sie aus:

    | Einstellung | Wert                                          |
    |-----------------------|----------------------------------|
    | **Projektdetails** |  |
    | Subscription | Auswählen des Azure-Abonnements |
    | Ressourcengruppe | Wählen Sie **CreatePubLBQS-rg** aus. |
    | **Instanzendetails** |  |
    | Name des virtuellen Computers | Geben Sie **myVM1** ein. |
    | Region | Wählen Sie **Europa, Westen** aus. |
    | Verfügbarkeitsoptionen | Wählen Sie **Verfügbarkeitszonen** aus. |
    | Verfügbarkeitszone | Wählen Sie **1** aus. |
    | Image | Wählen Sie **Windows Server 2019 Datacenter** aus. |
    | Azure Spot-Instanz | Wählen Sie **Nein** aus. |
    | Size | Wählen Sie eine VM-Größe aus, oder übernehmen Sie die Standardeinstellung. |
    | **Administratorkonto** |  |
    | Username | Geben Sie einen Benutzernamen ein. |
    | Kennwort | Geben Sie ein Kennwort ein. |
    | Kennwort bestätigen | Geben Sie das Kennwort erneut ein. |
    | **Regeln für eingehende Ports** |  |
    | Öffentliche Eingangsports | Wählen Sie **Keine** aus. |

3. Wählen Sie die Registerkarte **Netzwerk** aus, oder wählen Sie **Weiter: Datenträger** und anschließend **Weiter: Netzwerk** aus.
  
4. Wählen Sie auf der Registerkarte „Netzwerk“ die folgenden Werte aus, oder geben Sie sie ein:

    | Einstellung | Wert |
    |-|-|
    | **Netzwerkschnittstelle** |  |
    | Virtuelles Netzwerk | **myVNet** |
    | Subnet | **myBackendSubnet** |
    | Öffentliche IP-Adresse | Wählen Sie **Keine** aus. |
    | NIC-Netzwerksicherheitsgruppe | Wählen Sie **Erweitert** aus.|
    | Konfigurieren von Netzwerksicherheitsgruppen | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Netzwerksicherheitsgruppe erstellen** als **Name** die Zeichenfolge **myNSG** ein. </br> Wählen Sie unter **Regeln für eingehenden Datenverkehr** die Option **+ Eingangsregel hinzufügen** aus. </br> Geben Sie unter **Zielportbereiche** den Wert **80** ein. </br> Geben Sie unter **Priorität** den Wert **100** ein. </br> Geben Sie unter **Name** die Zeichenfolge **myHTTPRule** ein. </br> Wählen Sie **Hinzufügen** aus. </br> Klicken Sie auf **OK**. |
    | **Lastenausgleich**  |
    | Diese VM hinter einer vorhandenen Lastenausgleichslösung platzieren? | Klicken Sie auf **Ja**. |
    | **Lastenausgleichseinstellungen** |
    | Optionen für den Lastenausgleich | Wählen Sie **Azure-Lastenausgleich** aus. |
    | Wählen Sie einen Lastenausgleich aus. | Wählen Sie **myLoadBalancer** aus.  |
    | Wählen Sie einen Back-End-Pool aus. | Wählen Sie **myBackendPool** aus. |

5. Wählen Sie die Registerkarte **Verwaltung** oder **Weiter** > **Verwaltung**.

6. Wählen Sie auf der Registerkarte **Verwaltung** die folgenden Werte aus, oder geben Sie sie ein:
    
    | Einstellung | Wert |
    |-|-|
    | **Überwachung** |  |
    | Startdiagnose | Wählen Sie **Aus** aus. |
   
7. Klicken Sie auf **Überprüfen + erstellen**. 
  
8. Überprüfen Sie die Einstellungen, und wählen Sie dann die Option **Erstellen**.

9. Führen Sie die Schritte 1 bis 8 aus, um zwei weitere virtuelle Computer mit folgenden Werten zu erstellen. Verwenden Sie für alle übrigen Einstellungen die Werte von **myVM1**:

    | Einstellung | VM 2| VM 3|
    | ------- | ----- |---|
    | Name |  **myVM2** |**myVM3**|
    | Verfügbarkeitszone | **2** |**3**|
    | Netzwerksicherheitsgruppe | Wählen Sie die vorhandene Netzwerksicherheitsgruppe (**myNSG**) aus.| Wählen Sie die vorhandene Netzwerksicherheitsgruppe (**myNSG**) aus.|

## <a name="create-outbound-rule-configuration"></a>Erstellen der Konfiguration für Ausgangsregeln
Ausgangsregeln für den Lastenausgleich konfigurieren das ausgehende SNAT für virtuelle Computer im Back-End-Pool. 

Weitere Informationen zu ausgehenden Verbindungen finden Sie unter [Ausgehende Verbindungen in Azure](load-balancer-outbound-connections.md).

### <a name="create-outbound-rule"></a>Erstellen einer Ausgangsregel

1. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myLoadBalancer** aus.

2. Wählen Sie unter **Einstellungen** die Option **Ausgangsregeln** und dann **Hinzufügen** aus.

3. Verwenden Sie diese Werte zur Konfiguration der Ausgangsregeln:

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myOutboundRule** ein. |
    | Front-End-IP-Adresse | Wählen Sie **Neu erstellen**. </br> Geben Sie in **Name** den Wert **LoadBalancerFrontEndOutbound** ein. </br> Wählen Sie **IP-Adresse** oder **IP-Präfix** aus. </br> Wählen Sie unter **Öffentliche IP-Adresse** oder **Öffentliches IP-Präfix** die Option **Neu erstellen** aus. </br> Geben Sie als Name **myPublicIPOutbound** or **myPublicIPPrefixOutbound** ein. </br> Wählen Sie **Hinzufügen**.|
    | Leerlaufzeitüberschreitung (Minuten) | Bewegen Sie den Schieberegler auf **15 Minuten**.|
    | TCP-Zurücksetzung | Wählen Sie **Aktiviert**.|
    | Back-End-Pool | Wählen Sie **Neu erstellen**. </br> Geben Sie in **Name** den Wert **myBackendPoolOutbound** ein. </br> Wählen Sie **Hinzufügen**. |
    | Portzuordnung -> Portzuordnung | Wählen Sie **Anzahl ausgehender Ports manuell auswählen** aus. |
    | Ausgehende Ports -> Auswählen nach | Wählen Sie **Ports pro Instanz** aus. |
    | Ausgehende Ports -> Ports pro Instanz | Geben Sie **10000** ein. |

4. Wählen Sie **Hinzufügen**.

### <a name="add-virtual-machines-to-outbound-pool"></a>Hinzufügen virtueller Computer zum ausgehenden Pool

1. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myLoadBalancer** aus.

2. Wählen Sie unter **Einstellungen** die Option **Back-End-Pools**.

3. Wählen Sie **myBackendPoolOutbound** aus.

4. Wählen Sie in **Virtuelles Netzwerk** die Option **myVNet** aus.

5. Wählen Sie in **Vrtuelle Computer** die Option **+ Hinzufügen** aus.

6. Aktivieren Sie die Kontrollkästchen neben **myVM1**, **myVM2** und **myVM3**. 

7. Wählen Sie **Hinzufügen**.

8. Wählen Sie **Speichern** aus.

# <a name="basic-sku"></a>[**Basic-SKU**](#tab/option-1-create-load-balancer-basic)

>[!NOTE]
>Ein Lastenausgleich mit Standard-SKU wird für Produktionsworkloads empfohlen.  Weitere Informationen zu SKUs finden Sie unter **[Azure Load Balancer-SKUs](skus.md)** .

In diesem Abschnitt wird ein Lastenausgleich für virtuelle Computer erstellt. 

Wenn Sie einen öffentlichen Lastenausgleich erstellen, muss für den Lastenausgleich auch eine neue, als Front-End konfigurierte öffentliche IP-Adresse erstellt werden. Dieses Front-End hat standardmäßig den Namen **LoadBalancerFrontend**.

1. Wählens Sie links oben auf dem Bildschirm die Optionen **Ressource erstellen** > **Netzwerk** > **Lastenausgleich** aus.

2. Geben Sie auf der Seite **Lastenausgleich erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, bzw. wählen Sie sie aus: 

    | Einstellung                 | Wert                                              |
    | ---                     | ---                                                |
    | Subscription               | Wählen Sie Ihr Abonnement aus.    |    
    | Resource group         | Wählen Sie die Option **Neu erstellen** aus, und geben Sie im Textfeld **CreatePubLBQS-rg** ein.|
    | Name                   | Geben Sie **myLoadBalancer** ein.                                   |
    | Region         | Wählen Sie **Europa, Westen** aus.                                        |
    | type          | Wählen Sie **Öffentlich** aus.                                        |
    | SKU           | Wählen Sie **Basic** aus. |
    | Öffentliche IP-Adresse | Wählen Sie **Neu erstellen**. Wenn Sie über eine vorhandene öffentliche IP-Adresse verfügen, die Sie verwenden möchten, wählen Sie **Vorhandene verwenden** aus. |
    | Name der öffentlichen IP-Adresse | Geben Sie **myPublicIP** in das Textfeld ein.|
    | Zuweisung | Wählen Sie **Dynamisch** aus. |
    | Öffentliche IPv6-Adresse hinzufügen | Wählen Sie **Nein** aus. </br> Weitere Informationen zu IPv6-Adressen und zum Lastenausgleich finden Sie unter [Was ist IPv6 für Azure Virtual Network?](../virtual-network/ipv6-overview.md).  |

3. Übernehmen Sie bei den anderen Einstellungen die Standardwerte, und wählen Sie **Überprüfen + erstellen** aus.

4. Wählen Sie auf der Registerkarte **Bewerten + erstellen** die Option **Erstellen** aus.   

    :::image type="content" source="./media/quickstart-load-balancer-standard-public-portal/create-basic-load-balancer.png" alt-text="Erstellen eines Lastenausgleichs im Basic-Tarif" border="true":::

## <a name="create-load-balancer-resources"></a>Erstellen von Load Balancer-Ressourcen

In diesem Abschnitt wird Folgendes konfiguriert:

* Erstellen Sie ein virtuelles Netzwerk.
* Lastenausgleichseinstellungen für einen Back-End-Adresspool
* Ein Integritätstest
* Eine Lastenausgleichsregel

## <a name="create-the-virtual-network"></a>Erstellen des virtuellen Netzwerks

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk und ein Subnetz.

1. Wählen Sie links oben auf dem Bildschirm **Ressource erstellen > Netzwerk > Virtuelles Netzwerk** aus, oder suchen Sie über das Suchfeld nach **Virtuelles Netzwerk**.

2. Geben Sie unter **Virtuelles Netzwerk erstellen** auf der Registerkarte **Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | **Einstellung**          | **Wert**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projektdetails**  |                                                                 |
    | Subscription     | Auswählen des Azure-Abonnements                                  |
    | Ressourcengruppe   | Wählen Sie **CreatePubLBQS-rg** aus. |
    | **Instanzendetails** |                                                                 |
    | Name             | Geben Sie **myVNet** ein.                                    |
    | Region           | Wählen Sie **Europa, Westen** aus. |

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
### <a name="create-a-backend-pool"></a>Erstellen eines Back-End-Pools

Der Back-End-Adresspool enthält die IP-Adressen der virtuellen NICs, die mit dem Lastenausgleich verbunden sind. 

Erstellen Sie den Back-End-Adresspool **myBackendPool**, um virtuelle Computer für den Lastenausgleich von Internetdatenverkehr einzubeziehen.

1. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myLoadBalancer** aus.

2. Wählen Sie unter **Einstellungen** die Option **Back-End-Pools** und dann **Hinzufügen** aus.

3. Geben Sie auf der Seite **Back-End-Pool hinzufügen** die folgenden Werte ein, oder wählen Sie sie aus:
    
    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myBackendPool** ein. |
    | Virtuelles Netzwerk | Wählen Sie **myVNet** aus. |
    | Zugeordnet zu | Wählen Sie **Virtuelle Computer** aus. |

### <a name="create-a-health-probe"></a>Erstellen eines Integritätstests

Der Status Ihrer App wird vom Lastenausgleich mithilfe eines Integritätstests überwacht. 

Abhängig von der Reaktion auf Integritätsüberprüfungen werden der Load Balancer-Instanz durch den Integritätstest virtuelle Computer hinzugefügt oder daraus entfernt. 

Erstellen Sie zur Überwachung der Integrität der virtuellen Computer einen Integritätstest mit dem Namen **myHealthProbe**.

1. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myLoadBalancer** aus.

2. Wählen Sie unter **Einstellungen** die Option **Integritätstests** und dann **Hinzufügen** aus.
    
    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myHealthProbe** ein. |
    | Protocol | Wählen Sie **HTTP** aus. |
    | Port | Geben Sie **80** ein.|
    | `Path` | Eingeben von **/** |
    | Intervall | Geben Sie für das **Intervall** den Wert **15** (Sekunden zwischen Testversuchen) ein. |
    | Fehlerhafter Schwellenwert | Wählen Sie **2** als Wert für den **Fehlerschwellenwert** bzw. als Anzahl aufeinander folgender Testfehler aus, die auftreten müssen, damit ein virtueller Computer als fehlerhaft eingestuft wird.|

3. Klicken Sie auf **OK**.

### <a name="create-a-load-balancer-rule"></a>Erstellen einer Load Balancer-Regel

Mithilfe einer Load Balancer-Regel wird definiert, wie Datenverkehr auf die virtuellen Computer verteilt werden soll. Sie definieren die Front-End-IP-Konfiguration für den eingehenden Datenverkehr und den Back-End-IP-Pool für den Empfang des Datenverkehrs. Quell- und Zielport werden in der Regel definiert. 

In diesem Abschnitt wird eine Lastenausgleichsregel mit folgenden Merkmalen erstellt:

* Sie heißt **myHTTPRule**.
* Sie befindet sich im Front-End **LoadBalancerFrontEnd**.
* Sie lauscht a **Port 80**.
* Sie leitet vom Lastenausgleich verteilten Datenverkehr an den **Port 80** des Back-Ends **myBackendPool** weiter.

1. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myLoadBalancer** aus.

2. Wählen Sie unter **Einstellungen** die Option **Lastenausgleichsregeln** und dann **Hinzufügen** aus.

3. Konfigurieren Sie die Lastenausgleichsregel mit folgenden Werten:
    
    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie **myHTTPRule** ein. |
    | IP-Version | Wählen Sie **IPv4** aus. |
    | Front-End-IP-Adresse | Wählen Sie **LoadBalancerFrontEnd** aus. |
    | Protocol | Wählen Sie **TCP** aus. |
    | Port | Geben Sie **80** ein.|
    | Back-End-Port | Geben Sie **80** ein. |
    | Back-End-Pool | Wählen Sie **myBackendPool** aus.|
    | Integritätstest | Wählen Sie **myHealthProbe** aus. |
    | Leerlaufzeitüberschreitung (Minuten) | Bewegen Sie den Schieberegler auf **15 Minuten**. |
 
4. Übernehmen Sie die übrigen Standardeinstellungen, und wählen Sie dann **OK** aus.

## <a name="create-backend-servers"></a>Erstellen von Back-End-Servern

In diesem Abschnitt führen Sie folgende Schritte aus:

* Erstellen von drei virtuellen Computern für den Back-End-Pool des Lastenausgleichs
* Erstellen einer Verfügbarkeitsgruppe für die virtuellen Computer
* Installieren von IIS auf den virtuellen Computern, um den Lastenausgleich zu testen

### <a name="create-virtual-machines"></a>Erstellen von virtuellen Computern

In diesem Abschnitt werden drei virtuelle Computer (**myVM1**, **myVM2** und **myVM3**) mit einer öffentlichen IP-Adresse vom Typ „Basic“ erstellt.  

Die drei virtuellen Computer werden einer Verfügbarkeitsgruppe namens **myAvailabilitySet** hinzugefügt.

Diese virtuellen Computer werden dem Back-End-Pool des zuvor erstellten Lastenausgleichs hinzugefügt.

1. Navigieren Sie links oben im Portal zu **Ressource erstellen** > **Compute** > **Virtueller Computer**. 
   
2. Geben Sie unter **Virtuellen Computer erstellen** auf der Registerkarte **Grundlagen** die folgenden Werte ein, oder wählen Sie sie aus:

    | Einstellung | Wert                                          |
    |-----------------------|----------------------------------|
    | **Projektdetails** |  |
    | Subscription | Auswählen des Azure-Abonnements |
    | Ressourcengruppe | Wählen Sie **CreatePubLBQS-rg** aus. |
    | **Instanzendetails** |  |
    | Name des virtuellen Computers | Geben Sie **myVM1** ein. |
    | Region | Wählen Sie **Europa, Westen** aus. |
    | Verfügbarkeitsoptionen | Wählen Sie **Verfügbarkeitsgruppe** aus. |
    | Verfügbarkeitsgruppe | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Name** die Zeichenfolge **myAvailabilitySet** ein. </br> Klicken Sie auf **OK**. |
    | Image | **Windows Server 2019 Datacenter** |
    | Azure Spot-Instanz | Wählen Sie **Nein** aus. |
    | Size | Wählen Sie eine VM-Größe aus, oder übernehmen Sie die Standardeinstellung. |
    | **Administratorkonto** |  |
    | Username | Geben Sie einen Benutzernamen ein. |
    | Kennwort | Geben Sie ein Kennwort ein. |
    | Kennwort bestätigen | Geben Sie das Kennwort erneut ein. |

3. Wählen Sie die Registerkarte **Netzwerk** aus, oder wählen Sie **Weiter: Datenträger** und anschließend **Weiter: Netzwerk** aus.
  
4. Wählen Sie auf der Registerkarte „Netzwerk“ die folgenden Werte aus, oder geben Sie sie ein:

    | Einstellung | Wert |
    |-|-|
    | **Netzwerkschnittstelle** |  |
    | Virtuelles Netzwerk | Wählen Sie **myVNet** aus. |
    | Subnet | Wählen Sie **myBackendSubnet** aus. |
    | Öffentliche IP-Adresse | Wählen Sie **Keine** aus. |
    | NIC-Netzwerksicherheitsgruppe | Wählen Sie **Erweitert** aus.|
    | Konfigurieren von Netzwerksicherheitsgruppen | Wählen Sie **Neu erstellen**. </br> Geben Sie unter **Netzwerksicherheitsgruppe erstellen** als **Name** die Zeichenfolge **myNSG** ein. </br> Wählen Sie unter **Regeln für eingehenden Datenverkehr** die Option **+ Eingangsregel hinzufügen** aus. </br> Geben Sie unter **Zielportbereiche** den Wert **80** ein. </br> Geben Sie unter **Priorität** den Wert **100** ein. </br> Geben Sie unter **Name** die Zeichenfolge **myHTTPRule** ein. </br> Wählen Sie **Hinzufügen** aus. </br> Klicken Sie auf **OK**. |
    | **Lastenausgleich**  |
    | Diese VM hinter einer vorhandenen Lastenausgleichslösung platzieren? | Wählen Sie **Nein** aus. |
 
5. Wählen Sie die Registerkarte **Verwaltung** oder **Weiter** > **Verwaltung**.

6. Wählen Sie auf der Registerkarte **Verwaltung** die folgenden Werte aus, oder geben Sie sie ein:
    
    | Einstellung | Wert |
    |---|---|
    | **Überwachung** | |
    | Startdiagnose | Wählen Sie **Aus** aus. |

7. Klicken Sie auf **Überprüfen + erstellen**. 
  
8. Überprüfen Sie die Einstellungen, und wählen Sie dann die Option **Erstellen**.

9. Führen Sie die Schritte 1 bis 8 aus, um zwei weitere virtuelle Computer mit folgenden Werten zu erstellen. Verwenden Sie für alle übrigen Einstellungen die Werte von **myVM1**:

    | Einstellung | VM 2| VM 3|
    | ------- | ----- |---|
    | Name |  **myVM2** |**myVM3**|
    | Verfügbarkeitsgruppe| Wählen Sie **myAvailabilitySet** aus. | Wählen Sie **myAvailabilitySet** aus.|
    | Netzwerksicherheitsgruppe | Wählen Sie die vorhandene Netzwerksicherheitsgruppe (**myNSG**) aus.| Wählen Sie die vorhandene Netzwerksicherheitsgruppe (**myNSG**) aus.|

### <a name="add-virtual-machines-to-the-backend-pool"></a>Hinzufügen virtueller Computer zum Back-End-Pool

Die in den vorherigen Schritten erstellten virtuellen Computer müssen dem Back-End-Pool von **myLoadBalancer** hinzugefügt werden.

1. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myLoadBalancer** aus.

2. Wählen Sie unter **Einstellungen** die Option **Back-End-Pools** und anschließend **myBackendPool** aus.

3. Wählen Sie unter **Verknüpft mit** die Option **Virtuelle Computer** aus.

4. Wählen Sie im Abschnitt **Virtuelle Computer** die Option **+ Hinzufügen** aus.

5. Aktivieren Sie die Kontrollkästchen neben **myVM1**, **myVM2** und **myVM3**.

6. Wählen Sie **Hinzufügen**.

7. Wählen Sie **Speichern** aus.

---

## <a name="install-iis"></a>Installieren von IIS

1. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend in der Ressourcenliste den Eintrag **myVM1** (in der Ressourcengruppe **CreateStdLBQS-rg**) aus.

2. Wählen Sie auf der Seite **Übersicht** die Option **Verbinden** und dann **Bastion** aus.

4. Geben Sie den Benutzernamen und das Kennwort ein, die Sie bei der VM-Erstellung verwendet haben.

5. Wählen Sie **Verbinden**.

6. Navigieren Sie auf dem Serverdesktop zu **Windows-Verwaltungsprogramme** > **Windows PowerShell**.

7. Führen Sie im PowerShell-Fenster die im Anschluss angegebenen Befehle aus. Diese bewirken Folgendes:

    * Installieren des IIS-Servers
    * Entfernen der Standarddatei „iisstart.htm“
    * Hinzufügen einer neuen Datei namens „iisstart.htm“ zum Anzeigen des Namens des virtuellen Computers

   ```powershell
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. Schließen Sie die Bastion-Sitzung mit **myVM1**.

9. Wiederholen Sie die Schritte 1 bis 6, um IIS und die aktualisierte Datei „iisstart.htm“ auf **myVM2** und **myVM3** zu installieren.

## <a name="test-the-load-balancer"></a>Testen des Lastenausgleichs

1. Ermitteln Sie auf dem Bildschirm **Übersicht** die öffentliche IP-Adresse für den Load Balancer. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend **myPublicIP** aus.

2. Kopieren Sie die öffentliche IP-Adresse, und fügen Sie sie in die Adressleiste des Browsers ein. Die Standardseite des IIS-Webservers wird im Browser angezeigt.

   ![IIS-Webserver](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Um zu sehen, wie Datenverkehr durch den Lastenausgleich auf alle drei virtuellen Computer verteilt wird, können Sie die Standardseite des IIS-Webservers der einzelnen virtuellen Computer anpassen und dann über den Clientcomputer eine Aktualisierung Ihres Webbrowsers erzwingen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, den Lastenausgleich und alle zugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. Wählen Sie hierzu die Ressourcengruppe **CreatePubLBQS-rg** aus, die die Ressourcen enthält, und wählen Sie anschließend **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung führen Sie die folgenden Schritte aus:

* Erstellen einer Azure Load Balancer-Instanz (Standard oder Basic)
* Anfügen von drei virtuellen Computern an den Lastenausgleich
* Konfigurieren der Lastenausgleichsregel für den Datenverkehr sowie eines Integritätstests und anschließendes Testen des Lastenausgleichs 

Fahren Sie mit dem folgenden Artikel fort, um sich weiter über Azure Load Balancer zu informieren:
> [!div class="nextstepaction"]
> [Was versteht man unter Azure Load Balancer?](load-balancer-overview.md)