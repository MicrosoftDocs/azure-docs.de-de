---
title: Azure Virtual WAN – Automatisierungsrichtlinien für Partner | Microsoft-Dokumentation
description: Richten Sie eine Automatisierungsumgebung ein, um eine Verbindung mit einem lokalen VPN, SD-WAN-CPE oder einem Branchgerät für Azure Virtual WAN herzustellen und dieses zu konfigurieren.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 29fff3a6a430e3bc1a0b3a13876b55d22f7cb545
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566468"
---
# <a name="automation-guidelines-for-virtual-wan-partners"></a>Automatisierungsrichtlinien für Virtual WAN-Partner

In diesem Artikel erfahren Sie, wie Sie die Automatisierungsumgebung einrichten, um ein Zweigstellengerät (ein lokales VPN-Gerät oder SDWAN eines Kunden) für Azure Virtual WAN zu verbinden und zu konfigurieren. Wenn Sie Zweigstellengeräte anbieten, die VPN-Konnektivität über IPsec/IKEv2 oder IPsec/IKEv1 ermöglichen, ist dieser Artikel für Sie interessant.

Ein Zweigstellengerät (ein lokales VPN-Gerät des Kunden oder SDWAN CPE) verwendet typischerweise ein Controller-/Gerätedashboard, das bereitgestellt werden soll. SD-WAN-Lösungsadministratoren nutzen oftmals eine Verwaltungskonsole, um ein Gerät vorab bereitzustellen, bevor es an das Netzwerk angeschlossen wird. Dieses VPN-fähige Gerät bezieht seine Steuerungsebenenlogik von einem Controller. Der VPN-Geräte- oder SD-WAN-Controller kann Azure-APIs verwenden, um Konnektivität mit Azure Virtual WAN zu automatisieren. Für diese Art von Verbindung wird ein lokales VPN-Gerät benötigt, dem eine extern zugängliche, öffentliche IP-Adresse zugewiesen ist.

## <a name="before-you-begin-automating"></a><a name ="before"></a>Schritte vor Beginn der Konfiguration der Automatisierung

* Stellen Sie sicher, dass Ihr Gerät IPsec IKEv1/IKEv2 unterstützt. Siehe [Standardrichtlinien](#default).
* Machen Sie sich mit den [REST-APIs](#additional) vertraut, mit denen Sie Konnektivität mit Azure Virtual WAN automatisieren.
* Sehen Sie sich die Portalbenutzeroberfläche von Azure Virtual WAN an.
* Entscheiden Sie dann, welchen Teil der Konnektivitätsschritte Sie automatisieren möchten. Die folgende Mindestautomatisierung wird empfohlen:

  * Zugriffssteuerung
  * Hochladen der Informationen des Zweigstellengeräts in Azure Virtual WAN
  * Herunterladen der Azure-Konfiguration und Einrichten der Konnektivität zwischen Zweigstellengerät und Azure Virtual WAN

### <a name="additional-information"></a><a name ="additional"></a>Weitere Informationen

* [Rest-API](/rest/api/virtualwan/virtualhubs) zum Automatisieren der Erstellung virtueller Hubs
* [Rest-API](/rest/api/virtualwan/vpngateways) zum Automatisieren des Azure-VPN-Gateways für das virtuelle WAN
* [Rest-API](/rest/api/virtualwan/vpnconnections) zum Verbinden von VPNSite mit einem Azure-VPN-Hub
* [IPsec-Standardrichtlinien](#default)

## <a name="customer-experience"></a><a name ="ae"></a>Benutzerfreundlichkeit

Verstehen der erwarteten Kundenerfahrung in Verbindung mit Azure Virtual WAN

  1. Normalerweise startet ein Virtual WAN-Benutzer den Prozess, indem er eine Virtual WAN-Ressource erstellt.
  2. Der Benutzer richtet einen dienstprinzipalbasierten Ressourcengruppenzugriff für das lokale System (Ihren Zweigstellencontroller oder Ihre VPN-Gerätebereitstellungssoftware) ein, um Zweigstelleninformationen in Azure Virtual WAN zu schreiben.
  3. Der Benutzer kann zu diesem Zeitpunkt entscheiden, sich bei Ihrer Benutzeroberfläche anzumelden und die Anmeldeinformationen für den Dienstprinzipal einzurichten. Sobald dies erfolgt ist, sollte Ihr Controller in der Lage sein, Zweigstelleninformationen mit der von Ihnen bereitgestellten Automatisierung hochzuladen. Das manuelle Gegenstück hierfür auf Azure-Seite ist „Standort erstellen“.
  4. Sobald die Informationen zum Standort (Zweigstellengerät) in Azure verfügbar sind, verbindet der Benutzer den Standort mit einem Hub. Ein virtueller Hub ist ein von Microsoft verwaltetes virtuelles Netzwerk. Der Hub enthält verschiedene Dienstendpunkte zum Aktivieren der Konnektivität über Ihr lokales Netzwerk (vpnsite). Der Hub ist der Kern Ihres Netzwerks in einer Region. Es kann pro Azure-Region nur einen Hub geben, und der darin befindliche VPN-Endpunkt (VPN-Gateway) wird bei diesem Vorgang erzeugt. Das VPN Gateway ist ein skalierbares Gateway, das je nach Bandbreiten- und Verbindungsbedarf entsprechend dimensioniert ist. Sie können die Erstellung von virtuellen Hubs und VPN-Gateways über Ihr Dashboard für Zweigstellengeräte-Controller automatisieren.
  5. Sobald der virtuelle Hub dem Standort zugeordnet ist, wird eine Konfigurationsdatei erstellt, die der Benutzer manuell herunterladen kann. Hier kommt Ihre Automatisierung ins Spiel, die die Benutzererfahrung nahtlos macht. Anstatt dass der Benutzer das Zweigstellengerät manuell herunterladen und konfigurieren muss, können Sie die Automatisierung so einrichten, so nur noch wenige Mausklicks auf Ihrer Benutzeroberfläche nötig sind. Dadurch werden typische Verbindungsprobleme wie Nichtübereinstimmung gemeinsam verwendeter Schlüssel und von IPSec-Parametern, Lesbarkeit von Konfigurationsdateien usw. vermieden.
  6. Am Ende dieses Schritts in Ihrer Lösung steht dem Benutzer eine nahtlose Site-to-Site Verbindung zwischen Zweigstellengerät und virtuellem Hub zur Verfügung. Sie können auch zusätzliche Verbindungen über andere Hubs einrichten. Jede Verbindung ist ein Aktiv/Aktiv-Tunnel. Ihr Kunde kann für jede der Anbindungen an den Tunnel einen anderen Internetdienstanbieter wählen.
  7. Sie sollten Problembehandlungs- und Überwachungsfunktionen in der CPE-Verwaltungsschnittstelle bereitstellen. Typische Szenarien sind z.B. „Kunde kann aufgrund eines CPE-Problems nicht auf Azure-Ressourcen zugreifen“, „IPsec-Parameter auf der CPE-Seite anzeigen“ usw.

## <a name="automation-details"></a><a name ="understand"></a>Automationsdetails

###  <a name="access-control"></a><a name="access"></a>Zugriffssteuerung

Kunden müssen entsprechende Zugriffssteuerungen für Virtual WAN in der Gerätebenutzeroberfläche einrichten können. Dafür wird ein Azure-Dienstprinzipal empfohlen. Der auf einem Dienstprinzipal basierende Zugriff bietet dem Gerätecontroller eine entsprechende Authentifizierung, um Zweigstelleninformationen hochzuladen. Weitere Informationen finden Sie unter [Erstellen von Dienstprinzipalen](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Obwohl diese Funktionalität nicht zum Angebot von Azure Virtual WAN gehört, werden im Folgenden die typischen Schritte zur Einrichtung des Zugriffs in Azure aufgeführt, woraufhin die relevanten Details in das Dashboard zur Geräteverwaltung eingegeben werden.

* Erstellen Sie eine Azure Active Directory-Anwendung für Ihren lokalen Gerätecontroller.
* Abrufen der Anwendungs-ID und des Authentifizierungsschlüssels
* Abrufen der Mandanten-ID
* Zuweisen der Anwendung zur Rolle „Mitwirkender“

###  <a name="upload-branch-device-information"></a><a name="branch"></a>Hochladen von Informationen zum Zweigstellengerät

Sie sollten die Benutzererfahrung zum Hochladen von Zweigstelleninformationen (lokal) in Azure gestalten. Zur Erstellung der Standortinformationen in Virtual WAN können Sie [REST-APIs](/rest/api/virtualwan/vpnsites) für den VPN-Standort verwenden. Sie können alle SDWAN/VPN-Zweigstellengeräte bereitstellen oder entsprechende Geräteanpassungen auswählen.

### <a name="device-configuration-download-and-connectivity"></a><a name="device"></a>Herunterladen der Gerätekonfiguration und Einrichten von Konnektivität

Dieser Schritt umfasst das Herunterladen der Azure-Konfiguration und Einrichten von Konnektivität zwischen Zweigstellengerät und Azure Virtual WAN. In diesem Schritt würde ein Kunde, der keinen Anbieter verwendet, die Azure-Konfiguration manuell herunterladen und auf sein lokales SDWAN/VPN-Gerät anwenden. Als Anbieter sollten Sie diesen Schritt automatisieren. Weitere Informationen finden Sie im Download der [REST-APIs](/rest/api/virtualwan/vpnsitesconfiguration/download). Der Gerätecontroller kann die REST-API „GetVpnConfiguration“ zum Herunterladen der Azure-Konfiguration aufrufen.

**Konfigurationshinweise**

  * Wenn Azure-VNets an den virtuellen Hub angefügt sind, werden sie als ConnectedSubnets angezeigt.
  * Für VPN-Konnektivität wird eine routenbasierte Konfiguration verwendet. Sie unterstützt die Protokolle IKEv1 und IKEv2.

## <a name="device-configuration-file"></a><a name="devicefile"></a>Gerätekonfigurationsdatei

Die Gerätekonfigurationsdatei enthält die Einstellungen, die beim Konfigurieren Ihrer lokalen VPN-Geräte verwendet werden. Beachten Sie beim Anzeigen dieser Datei die folgenden Informationen:

* **vpnSiteConfiguration**: In diesem Abschnitt sind die Gerätedetails für die Einrichtung einer Site angegeben, für die eine Verbindung mit dem virtuellen WAN hergestellt wird. Sie enthält den Namen und die öffentliche IP-Adresse des Zweigstellengeräts.
* **vpnSiteConnections**: Dieser Abschnitt enthält die folgenden Informationen:

    * **Adressraum** des virtuellen Hub-VNET.<br>Beispiel:
 
        ```
        "AddressSpace":"10.1.0.0/24"
        ```
    * **Adressraum** der VNETs, die mit dem Hub verbunden sind.<br>Beispiel:

         ```
        "ConnectedSubnets":["10.2.0.0/16","10.3.0.0/16"]
         ```
    * **IP-Adressen** des vpngateway für den virtuellen Hub. Da für das vpngateway jede Verbindung aus zwei Tunneln mit Aktiv-Aktiv-Konfiguration besteht, sind in dieser Datei beide IP-Adressen angegeben. In diesem Beispiel werden für jede Site „Instance0“ und „Instance1“ angezeigt.<br>Beispiel:

        ``` 
        "Instance0":"104.45.18.186"
        "Instance1":"104.45.13.195"
        ```
    * **Details zur Konfiguration der vpngateway-Verbindung**, z.B. BGP, vorinstallierter Schlüssel usw. Der vorinstallierte Schlüssel (Pre-Shared Key, PSK) wird automatisch für Sie erstellt. Sie können die Verbindung für einen benutzerdefinierten PSK auf der Seite „Übersicht“ jederzeit bearbeiten.
  
**Beispiel für Gerätekonfigurationsdatei**

  ```
  { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"r403583d-9c82-4cb8-8570-1cbbcd9983b5"
      },
      "vpnSiteConfiguration":{ 
         "Name":"testsite1",
         "IPAddress":"73.239.3.208"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe",
               "ConnectedSubnets":[ 
                  "10.2.0.0/16",
                  "10.3.0.0/16"
               ]
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.186",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"bkOWe5dPPqkx0DfFE3tyuP7y3oYqAEbI",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"1f33f891-e1ab-42b8-8d8c-c024d337bcac"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite2",
         "IPAddress":"66.193.205.122"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"XzODPyAYQqFs4ai9WzrJour0qLzeg7Qg",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   },
   { 
      "configurationVersion":{ 
         "LastUpdatedTime":"2018-07-03T18:29:49.8405161Z",
         "Version":"cd1e4a23-96bd-43a9-93b5-b51c2a945c7"
      },
      "vpnSiteConfiguration":{ 
         "Name":" testsite3",
         "IPAddress":"182.71.123.228"
      },
      "vpnSiteConnections":[ 
         { 
            "hubConfiguration":{ 
               "AddressSpace":"10.1.0.0/24",
               "Region":"West Europe"
            },
            "gatewayConfiguration":{ 
               "IpAddresses":{ 
                  "Instance0":"104.45.18.187",
                  "Instance1":"104.45.13.195"
               }
            },
            "connectionConfiguration":{ 
               "IsBgpEnabled":false,
               "PSK":"YLkSdSYd4wjjEThR3aIxaXaqNdxUwSo9",
               "IPsecParameters":{ 
                  "SADataSizeInKilobytes":102400000,
                  "SALifeTimeInSeconds":3600
               }
            }
         }
      ]
   }
  ```

## <a name="connectivity-details"></a><a name="default"></a>Konnektivitätsdetails

Ihre lokale SDWAN/VPN- oder SD-WAN-Gerätekonfiguration muss folgenden Algorithmus- und Parameterangaben für die Azure-IPsec-/IKE-Richtlinie entsprechen oder selbige enthalten.

* IKE-Verschlüsselungsalgorithmus
* IKE-Integritätsalgorithmus
* DH-Gruppe
* IPsec-Verschlüsselungsalgorithmus
* IPsec-Integritätsalgorithmus
* PFS-Gruppe

### <a name="default-policies-for-ipsec-connectivity"></a><a name="default"></a>Standardrichtlinien für IPSec-Konnektivität

[!INCLUDE [IPsec Default](../../includes/virtual-wan-ipsec-include.md)]

### <a name="custom-policies-for-ipsec-connectivity"></a><a name="custom"></a>Benutzerdefinierte Richtlinien für IPsec-Konnektivität

[!INCLUDE [IPsec Custom](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Virtual WAN, finden Sie unter [Über Virtual WAN](virtual-wan-about.md) und [FAQS zu Azure Virtual WAN](virtual-wan-faq.md).

Wenn Sie weitere Informationen wünschen, senden Sie eine E-Mail an <azurevirtualwan@microsoft.com>. Fügen Sie den Namen Ihres Unternehmens in eckigen Klammern in die Betreffzeile ein.