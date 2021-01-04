---
title: 'Schnellstart: Erstellen eines Private Link-Diensts über das Azure-Portal'
titlesuffix: Azure Private Link
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie über das Azure-Portal einen Private Link-Dienst erstellen.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: 5b7bc8be89068f0d3cf6722c36ae7fd5cc560736
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96012117"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Schnellstart: Erstellen eines Private Link-Diensts über das Azure-Portal

Bei einem Azure Private Link-Dienst handelt es sich um Ihren eigenen Dienst, der von Private Link verwaltet wird. Sie können Private Link Zugriff auf den Dienst oder die Ressource erteilen, der bzw. die hinter Azure Load Balancer Standard betrieben wird. Consumer Ihres Diensts können von ihren eigenen virtuellen Netzwerken aus privat auf ihn zugreifen. In dieser Schnellstartanleitung erfahren Sie, wie Sie über das Azure-Portal einen Private Link-Dienst erstellen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

## <a name="create-an-internal-load-balancer"></a>Erstellen eines internen Load Balancers

Erstellen Sie zunächst ein virtuelles Netzwerk. Erstellen Sie anschließend einen internen Lastenausgleich für die Verwendung mit dem Private Link-Dienst.

## <a name="virtual-network-and-parameters"></a>Virtuelles Netzwerk und Parameter

In diesem Abschnitt erstellen Sie ein virtuelles Netzwerk. Darüber hinaus erstellen Sie das Subnetz zum Hosten des Lastenausgleichs, der auf den Private Link-Dienst zugreift.

In den Schritten dieses Abschnitts müssen die folgenden Parameter wie folgt ersetzt werden:

| Parameter                   | Wert                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | USA (Ost) 2      |
| **\<IPv4-address-space>**   | 10.3.0.0/16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-standard-load-balancer"></a>Schnellstart: Erstellen einer Load Balancer Standard-Instanz mit Azure PowerShell

Erstellen Sie über das Portal einen internen Lastenausgleich im Tarif „Standard“. Der von Ihnen angegebene Name und die IP-Adresse werden automatisch als Front-End des Lastenausgleichs konfiguriert.

1. Wählen Sie oben links im Portal **Ressource erstellen** > **Netzwerk** > **Load Balancer**.

1. Geben Sie auf der Seite **Lastenausgleich erstellen** auf der Registerkarte **Grundlagen** die folgenden Informationen ein, bzw. wählen Sie sie aus:

    | Einstellung                 | Wert                                              |
    | ---                     | ---                                                |
    | **Abonnement**               | Wählen Sie Ihr Abonnement aus.    |
    | **Ressourcengruppe**         | Wählen Sie im Feld die Option **MyResourceGroupLB** aus.|
    | **Name**                   | Geben Sie **myLoadBalancer** ein.                                   |
    | **Region**         | Wählen Sie **USA, Osten 2** aus.                                        |
    | **Type**          | Wählen Sie **Intern** aus.                                        |
    | **SKU**           | Wählen Sie **Standard** aus.                          |
    | **Virtuelles Netzwerk**           | Wählen Sie **myVNet** aus.                          |
    | **IP-Adresszuweisung**              | Wählen Sie **Statisch** aus.   |
    | **Private IP-Adresse**|Geben Sie eine Adresse ein, die im Adressraum Ihres virtuellen Netzwerks und Subnetzes enthalten ist. Ein Beispiel wäre 10.3.0.7.  |

1. Übernehmen Sie die Standardwerte für die anderen Einstellungen, und wählen Sie dann **Überprüfen und erstellen** aus.

1. Wählen Sie auf der Registerkarte **Überprüfen + erstellen** die Option **Erstellen** aus.

### <a name="create-standard-load-balancer-resources"></a>Erstellen von Lastenausgleichsressourcen vom Typ „Standard“

In diesem Abschnitt konfigurieren Sie die Load Balancer-Einstellungen für einen Back-End-Adresspool und einen Integritätstest. Außerdem geben Sie Lastenausgleichsregeln an.

#### <a name="create-a-back-end-pool"></a>Erstellen eines Back-End-Pools

Der Back-End-Adresspool enthält die IP-Adressen der virtuellen Netzwerkschnittstellenkarten, die mit dem Lastenausgleich verbunden sind. Mit diesem Pool können Sie Datenverkehr auf Ihre Ressourcen verteilen. Erstellen Sie den Back-End-Adresspool **myBackendPool** für Ressourcen, die einen Lastausgleich für Datenverkehr vornehmen.

1. Wählen Sie im Menü ganz links **Alle Dienste** aus.
1. Wählen Sie die Option **Alle Ressourcen** und dann in der Ressourcenliste die Option **myLoadBalancer** aus.
1. Wählen Sie unter **Einstellungen** die Option **Back-End-Pools** und dann **Hinzufügen**.
1. Geben Sie auf der Seite **Back-End-Pool hinzufügen** die Zeichenfolge **myBackendPool** als Namen für Ihren Back-End-Pool ein, und wählen Sie anschließend **Hinzufügen** aus.

#### <a name="create-a-health-probe"></a>Erstellen eines Integritätstests

Mit einem Integritätstest kann der Lastenausgleich den Ressourcenstatus überwachen. Abhängig von der Reaktion der Ressourcen auf Integritätsüberprüfungen werden der Lastenausgleichsrotation durch den Integritätstest dynamisch Ressourcen hinzugefügt oder daraus entfernt.

So erstellen Sie einen Integritätstest zur Überwachung der Ressourcenintegrität:

1. Wählen Sie im Menü ganz links die Option **Alle Ressourcen** und dann in der Ressourcenliste die Option **myLoadBalancer** aus.

1. Wählen Sie unter **Einstellungen** die Option **Integritätstests** und dann **Hinzufügen**.

1. Geben Sie auf der Seite **Integritätstest hinzufügen** die folgenden Werte ein, bzw. wählen Sie sie aus:

   - **Name**: Geben Sie **myHealthProbe** ein.
   - **Protokoll:** Wählen Sie **TCP** aus.
   - **Port:** Geben Sie **80** ein.
   - **Intervall**: Geben Sie **15** ein. Dieses Intervall ist die Anzahl von Sekunden zwischen Testversuchen.
   - **Fehlerschwellenwert**: Geben Sie **2** ein. Dieser Wert gibt die Anzahl aufeinanderfolgender Testfehler an, die auftreten müssen, damit ein virtueller Computer als fehlerhaft eingestuft wird.

1. Klicken Sie auf **OK**.

#### <a name="create-a-load-balancer-rule"></a>Erstellen einer Load Balancer-Regel

Eine Lastenausgleichsregel definiert, wie Datenverkehr an Ressourcen verteilt wird. Die Regel definiert Folgendes:

- Die Front-End-IP-Konfiguration für eingehenden Datenverkehr
- Den Back-End-IP-Pool zum Empfangen des Datenverkehrs
- Die erforderlichen Quell- und Zielports

Mit der Lastenausgleichsregel **myLoadBalancerRule** wird an Port 80 des Front-Ends **LoadBalancerFrontEnd** gelauscht. Die Regel sendet ebenfalls über Port 80 Netzwerkdatenverkehr an den Back-End-Adresspool **myBackendPool**.

So erstellen Sie eine Lastenausgleichsregel:

1. Wählen Sie im Menü ganz links die Option **Alle Ressourcen** und dann in der Ressourcenliste die Option **myLoadBalancer** aus.

1. Wählen Sie unter **Einstellungen** die Option **Lastenausgleichsregeln** und dann **Hinzufügen** aus.

1. Geben Sie auf der Seite **Lastenausgleichsregel hinzufügen** ggf. die folgenden Werte ein, bzw. wählen Sie sie aus:

   - **Name**: Geben Sie **myLoadBalancerRule** ein.
   - **Front-End-IP-Adresse**: Geben Sie **LoadBalancerFrontEnd** ein.
   - **Protokoll:** Wählen Sie **TCP** aus.
   - **Port:** Geben Sie **80** ein.
   - **Back-End-Port**: Geben Sie **80** ein.
   - **Back-End-Pool**: Wählen Sie **myBackendPool** aus.
   - **Integritätstest**: Wählen Sie **myHealthProbe** aus. 

1. Klicken Sie auf **OK**.

## <a name="create-a-private-link-service"></a>Erstellen eines Private Link-Diensts

In diesem Abschnitt wird ein Private Link-Dienst hinter einer Load Balancer Standard-Instanz erstellt.

1. Wählen Sie oben links auf der Seite im Azure-Portal die Option **Ressource erstellen** > **Netzwerk** > **Private Link-Center (Vorschau)** aus. Sie können auch das Suchfeld des Portals verwenden, um nach Private Link zu suchen.

1. Wählen Sie unter **Private Link-Center – Übersicht** > **Machen Sie Ihren eigenen Dienst verfügbar, damit andere Benutzer eine Verbindung herstellen können.** die Option **Starten** aus.

1. Geben Sie unter **Private Link-Dienst erstellen (Vorschau) – Grundlegende Einstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung           | Wert                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | Projektdetails:  |                                                                              |
    | **Abonnement**      | Wählen Sie Ihr Abonnement aus.                                                     |
    | **Ressourcengruppe**    | Wählen Sie **myResourceGroupLB** aus.                                                    |
    | Instanzdetails: |                                                                              |
    | **Name**              | Geben Sie **myPrivateLinkService** ein. |
    | **Region**            | Wählen Sie **USA, Osten 2** aus.                                                        |

1. Klicken Sie auf **Weiter: Ausgangseinstellungen** aus.

1. Geben Sie unter **Private Link-Dienst erstellen (Vorschau) – Ausgangseinstellungen** die folgenden Informationen ein, oder wählen Sie sie aus:

    | Einstellung                           | Wert                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | **Load Balancer**                     | Wählen Sie **myLoadBalancer** aus.                                                           |
    | **Front-End-IP-Adresse des Lastenausgleichs** | Wählen Sie die Front-End-IP-Adresse von **myLoadBalancer** aus.                                |
    | **Virtuelles Quellnetzwerk für NAT**        | Wählen Sie **myVNet** aus.                                                                   |
    | **NAT-Quellsubnetz**                 | Wählen Sie **myBackendSubnet** aus.                                                          |
    | **TCP-Proxy V2 aktivieren**               | Wählen Sie abhängig davon, ob Ihre Anwendung einen TCP-Proxy V2-Header erwartet, entweder **JA** oder **NEIN** aus. |
    | **Einstellungen für private IP-Adressen**       | Konfigurieren Sie die Zuordnungsmethode und die IP-Adresse für die einzelnen NAT-IP-Adressen.                  |

1. Klicken Sie auf **Weiter: Zugriffssicherheit** aus.

1. Wählen Sie unter **Private Link-Dienst erstellen (Vorschau) – Zugriffssicherheit** die Option **Sichtbarkeit** und dann **Nur rollenbasierte Zugriffssteuerung** aus.
  
1. Wählen Sie entweder **Weiter: Tags** > **Überprüfen + erstellen** aus, oder wählen Sie im oberen Seitenbereich die Registerkarte **Überprüfen + erstellen** aus.

1. Überprüfen Sie Ihre Angaben, und wählen Sie **Erstellen** aus.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den Private Link-Dienst nicht mehr benötigen, löschen Sie die Ressourcengruppe, um die im Rahmen dieser Schnellstartanleitung verwendeten Ressourcen zu bereinigen.

1. Geben Sie oben im Portal im Suchfeld die Zeichenfolge **myResourceGroupLB** ein, und wählen Sie in den Suchergebnissen den Eintrag **myResourceGroupLB** aus.
1. Wählen Sie die Option **Ressourcengruppe löschen**.
1. Geben Sie unter **RESSOURCENGRUPPENNAMEN EINGEBEN** den Namen **myResourceGroup** ein.
1. Klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine interne Azure Load Balancer-Instanz und einen Private Link-Dienst erstellt. Sie können sich auch darüber informieren, wie Sie [mit dem Azure-Portal einen privaten Endpunkt erstellen](./create-private-endpoint-portal.md).