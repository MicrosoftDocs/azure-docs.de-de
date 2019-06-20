---
title: Hybridverbindung mit einer Anwendung mit zwei Ebenen | Microsoft Docs
description: Erfahren Sie, wie Sie virtuelle Geräte und benutzerdefinierte Routen bereitstellen, um eine Anwendungsumgebung mit mehreren Ebenen in Azure zu erstellen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: carmonm
editor: tysonn
ms.assetid: 1f509bec-bdd1-470d-8aa4-3cf2bb7f6134
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2016
ms.author: kumud
ms.openlocfilehash: 1bdc485dfb352144e8a8d0fb75965cbb78288e2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64575591"
---
# <a name="virtual-appliance-scenario"></a>Szenario für virtuelle Geräte
Als gängiges Szenario müssen größere Azure-Kunden eine Anwendung mit zwei Ebenen bereitstellen, die über das Internet verfügbar ist und gleichzeitig den Zugriff auf die Back-End-Ebene über ein lokales Rechenzentrum ermöglicht. In diesem Dokument wird schrittweise ein Szenario mit benutzerdefinierten Routen (User Defined Routes, UDR), einem VPN-Gateway und virtuellen Netzwerkgeräten zum Bereitstellen einer Umgebung mit zwei Ebenen erläutert, die folgende Anforderungen erfüllt:

* Die Webanwendung darf nur über das öffentliche Internet zugänglich sein.
* Der Webserver, auf dem die Anwendung gehostet wird, muss auf einen Back-End-Anwendungsserver zugreifen können.
* Der gesamte Datenverkehr aus dem Internet an die Webanwendung muss über ein virtuelles Firewallgerät geleitet werden. Dieses virtuelle Gerät wird ausschließlich für den Internetdatenverkehr verwendet.
* Der gesamte Datenverkehr an den Anwendungsserver muss über ein virtuelles Firewallgerät geleitet werden. Dieses virtuelle Gerät wird für den Zugriff auf den Back-End-Server und den Zugriff auf den vom lokalen Netzwerk über ein VPN-Gateway eingehenden Datenverkehr eingesetzt.
* Administratoren müssen die virtuellen Firewallgeräte über ihre lokalen Computer verwalten können, indem sie ein drittes ausschließlich zu Verwaltungszwecken genutztes virtuelles Firewallgerät nutzen.

Dabei handelt es sich um ein Umkreisnetzwerk-Standardszenario (auch als DMZ bezeichnet) mit einer DMZ und einem geschützten Netzwerk. Dieses Szenario kann in Azure unter Verwendung von Netzwerksicherheitsgruppen (NSGs) oder virtuellen Firewallgeräten oder einer Kombination aus beiden geschaffen werden. In der folgenden Tabelle sind einige Vor- und Nachteile im Hinblick auf Netzwerksicherheitsgruppen und virtuelle Firewallgeräte aufgeführt.

|  | Vorteile | Nachteile |
| --- | --- | --- |
| NSG |Keine Kosten. <br/>In Azure RBAC integriert. <br/>Regeln können in Azure Resource Manager-Vorlagen erstellt werden. |Komplexität kann in größeren Umgebungen variieren. |
| Firewall |Vollständige Kontrolle über die Datenebene. <br/>Zentrale Verwaltung über Firewallkonsole. |Kosten der Firewallgeräte. <br/>Nicht in Azure RBAC integriert. |

In der unten beschriebenen Lösung wird ein Szenario mit einem Umkreisnetzwerk (DMZ) bzw. ein geschütztes Netzwerk mithilfe virtueller Firewallgeräte implementiert.

## <a name="considerations"></a>Überlegungen
Die oben erläuterte Umgebung können Sie in Azure mithilfe verschiedener derzeit verfügbarer Features bereitstellen.

* **Virtuelles Netzwerk (VNET)** . Ein Azure-VNET ähnelt einem lokalen Netzwerk und kann in ein oder mehrere Subnetze segmentiert werden, um die Isolation des Datenverkehrs und eine Trennung von Zuständigkeiten zu gewährleisten.
* **Virtuelles Gerät**. Mehrere Partner bieten im Azure Marketplace virtuelle Geräte an, die für die drei oben beschriebenen Firewalls verwendet werden können. 
* **Benutzerdefinierte Routen (UDR)** . Routingtabellen können benutzerdefinierte Routen enthalten, über die im Azure-Netzwerk die Weiterleitung der Pakete in einem VNET gesteuert wird. Diese Routingtabellen können auf Subnetze angewendet werden. Eines der neuesten Features in Azure bietet die Möglichkeit, eine Routingtabelle auf das GatewaySubnet anzuwenden, sodass der gesamte Datenverkehr, der über eine Hybridverbindung im Azure-VNET eingeht, an ein virtuelles Gerät weitergeleitet werden kann.
* **IP-Weiterleitung**. Mit der Azure-Netzwerk-Engine werden Pakete standardmäßig nur an virtuelle Netzwerkkarten (NICs) weitergeleitet, wenn die Ziel-IP-Adresse der Pakete der IP-Adresse der NICs entspricht. Wenn also mit einer benutzerdefinierten Route definiert wird, dass ein Paket an ein bestimmtes virtuelles Gerät gesendet werden muss, wird dieses Paket in der Azure-Netzwerk-Engine ignoriert. Um sicherzustellen, dass das Paket an einen virtuellen Computer (in diesem Fall ein virtuelles Gerät) übermittelt wird, das nicht das eigentliche Ziel für das Paket ist, müssen Sie die IP-Weiterleitung für das virtuelle Gerät aktivieren.
* **Netzwerksicherheitsgruppen (NSGs)** . Im folgenden Beispiel werden zwar keine Netzwerksicherheitsgruppen verwendet, dennoch könnten Sie Netzwerksicherheitsgruppen auf die Subnetze und/oder Netzwerkkarten anwenden, um den eingehenden und ausgehenden Datenverkehr dieser Subnetze und Netzwerkkarten weiter zu filtern.

![IPv6-Konnektivität](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

Dieses Beispiel umfasst ein Abonnement, das Folgendes enthält:

* 2 Ressourcengruppen, die im Diagramm nicht abgebildet sind. 
  * **ONPREMRG**. Enthält alle Ressourcen, die für die Simulation eines lokalen Netzwerks erforderlich sind.
  * **AZURERG**. Enthält alle Ressourcen, die für die virtuelle Azure-Netzwerkumgebung erforderlich sind. 
* Ein VNET mit dem Namen **onpremvnet** zum Imitieren eines lokalen Rechenzentrums, das wie folgt segmentiert ist.
  * **onpremsn1**. Subnetz mit einem virtuellen Computer, auf dem Ubuntu ausgeführt wird, um einen lokalen Server zu imitieren.
  * **onpremsn2**. Subnetz mit einem virtuellen Computer, auf dem Ubuntu ausgeführt wird, um einen von einem Administrator verwendeten lokalen Computer zu imitieren.
* Es gibt ein virtuelles Firewallgerät mit dem Namen **OPFW** in **onpremvnet**, über das ein Tunnel zu **azurevnet** verwaltet wird.
* Ein VNET mit dem Namen **azurevnet** , das wie folgt segmentiert ist.
  * **azsn1**. Subnetz, das ausschließlich für die externe Firewall verwendet wird. Der gesamte Internetdatenverkehr geht über dieses Subnetz ein. Dieses Subnetz enthält nur eine Netzwerkkarte, die mit der externen Firewall verknüpft ist.
  * **azsn2**. Front-End-Subnetz, in dem ein als Webserver ausgeführter virtueller Computer gehostet wird, auf den über das Internet zugegriffen wird.
  * **azsn3**. Back-End-Subnetz, in dem ein als Back-End-Anwendungsserver ausgeführter virtueller Computer gehostet wird, auf den über den Front-End-Webserver zugegriffen wird.
  * **azsn4**. Verwaltungssubnetz, das ausschließlich für die Bereitstellung des Verwaltungszugriffs auf alle virtuellen Firewallgeräte verwendet wird. Dieses Subnetz enthält jeweils nur eine Netzwerkkarte für die in der Lösung verwendeten virtuellen Firewallgeräte.
  * **GatewaySubnet**. Subnetz für Azure-Hybridverbindungen, das für ExpressRoute und VPN Gateway erforderlich ist, um die Konnektivität zwischen Azure-VNETs und anderen Netzwerken zu gewährleisten. 
* Es gibt drei virtuelle Firewallgeräte im Netzwerk **azurevnet** . 
  * **AZF1**. Externe Firewall, die durch Verwendung einer öffentlichen IP-Adressressource in Azure für das öffentliche Internet verfügbar ist. Dazu benötigen Sie eine Vorlage vom Marketplace oder direkt vom Geräteanbieter, über die ein virtuelles Gerät mit drei Netzwerkkarten bereitgestellt wird.
  * **AZF2**. Interne Firewall für die Steuerung des Datenverkehrs zwischen **azsn2** und **azsn3**. Auch hier handelt es sich um ein virtuelles Gerät mit drei Netzwerkkarten.
  * **AZF3**. Verwaltungsfirewall, auf die Administratoren über das lokale Rechenzentrum zugreifen können und die mit einem Verwaltungssubnetz verbunden ist, über das alle Firewallgeräte verwaltet werden. Vorlagen für virtuelle Geräte mit zwei Netzwerkkarten finden Sie im Marketplace, oder Sie können sie direkt vom Geräteanbieter anfordern.

## <a name="user-defined-routing-udr"></a>Benutzerdefiniertes Routing
Jedes Subnetz in Azure kann mit einer benutzerdefinierten Routingtabelle verknüpft werden, in der definiert wird, wie der im jeweiligen Subnetz initiierte Datenverkehr weitergeleitet wird. Wenn keine benutzerdefinierten Routen definiert sind, werden Standardrouten verwendet, um die Weiterleitung des Datenverkehrs zwischen den Subnetzen zu ermöglichen. Weiterführende Informationen zu benutzerdefinierten Routen finden Sie unter [Was sind benutzerdefinierte Routen und IP-Weiterleitung?](virtual-networks-udr-overview.md).

Um sicherzustellen, dass die Kommunikation basierend auf der letzten oben angeführten Anforderung über das richtige Firewallgerät erfolgt, müssen Sie in **azurevnet**die folgende Routingtabelle mit benutzerdefinierten Routen erstellen.

### <a name="azgwudr"></a>azgwudr
In diesem Szenario wird nur der Datenverkehr von lokalen Quellen zu Azure zum Verwalten der Firewalls durch Verbinden mit **AZF3** verwendet. Dieser Datenverkehr muss über die interne Firewall **AZF2** geleitet werden. Daher ist nur eine Route im **GatewaySubnet** erforderlich (siehe unten).

| Ziel | Nächster Hop | Erklärung |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Ermöglicht, dass lokaler Datenverkehr zur Verwaltungsfirewall **AZF3** |

### <a name="azsn2udr"></a>azsn2udr
| Ziel | Nächster Hop | Erklärung |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Ermöglicht den Datenverkehr zum Back-End-Subnetz, in dem der Anwendungsserver gehostet wird, über **AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Ermöglicht die Weiterleitung des gesamten anderen Datenverkehrs über **AZF1** |

### <a name="azsn3udr"></a>azsn3udr
| Ziel | Nächster Hop | Erklärung |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Ermöglicht, dass der Datenverkehr an **azsn2** vom Anwendungsserver zum Webserver über **AZF2** geleitet wird |

Sie müssen zudem Routingtabellen für die Subnetze in **onpremvnet** erstellen, um das lokale Rechenzentrum zu imitieren.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Ziel | Nächster Hop | Erklärung |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Ermöglicht den Datenverkehr zu **onpremsn2** über **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Ziel | Nächster Hop | Erklärung |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Ermöglicht den Datenverkehr zum Back-End-Subnetz in Azure über **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Ermöglicht den Datenverkehr zu **onpremsn1** über **OPFW** |

## <a name="ip-forwarding"></a>SSL-Weiterleitung
Benutzerdefinierte Routen und die IP-Weiterleitung sind Features, die Sie kombiniert nutzen können, um über virtuelle Geräte den Datenverkehrsfluss in einem Azure-VNET zu steuern.  Ein virtuelles Gerät ist letztlich nur ein virtueller Computer, der eine Anwendung zur Verarbeitung des Netzwerkverkehrs ausführt, z. B. eine Firewall oder ein NAT-Gerät.

Dieser virtuelle Computer muss eingehenden Datenverkehr empfangen können, der nicht an ihn selbst adressiert ist. Damit ein virtueller Computer an andere Ziele gerichteten Datenverkehr empfangen kann, müssen Sie für den virtuellen Computer die IP-Weiterleitung aktivieren. Hierbei handelt es sich um eine Azure-Einstellung, keine Einstellung im Gastbetriebssystem. Auf dem virtuellen Gerät muss dennoch eine Anwendung zum Verarbeiten und zur entsprechenden Weiterleitung des eingehenden Datenverkehrs ausgeführt werden.

Weitere Informationen zur IP-Weiterleitung finden Sie unter [Was sind benutzerdefinierte Routen und IP-Weiterleitung?](virtual-networks-udr-overview.md).

Als Beispiel soll die folgende Konfiguration in einem Azure-VNET dienen:

* Das Subnetz **onpremsn1** enthält einen virtuellen Computer mit dem Namen **onpremvm1**.
* Das Subnetz **onpremsn2** enthält einen virtuellen Computer mit dem Namen **onpremvm2**.
* Ein virtuelles Gerät mit dem Namen **OPFW** ist mit **onpremsn1** und **onpremsn2** verbunden.
* Eine mit **onpremsn1** verbundene benutzerdefinierte Route gibt an, dass der gesamte Datenverkehr für **onpremsn2** an **OPFW** geleitet werden muss.

Wenn nun **onpremvm1** versucht, eine Verbindung mit **onpremvm2** herzustellen, wird die benutzerdefinierte Route verwendet, und der Datenverkehr wird an **OPFW** als nächsten Hop gesendet. Bedenken Sie dabei, dass das eigentliche Paketziel nicht geändert wird, als Ziel ist weiterhin **onpremvm2** angegeben. 

Wenn die IP-Weiterleitung für **OPFW**nicht aktiviert ist, werden die Pakete in der virtuellen Azure-Netzwerklogik ausgelassen, da Pakete nur an einen virtuellen Computer gesendet werden können, wenn die IP-Adresse des virtuellen Computers als Ziel für das Paket festgelegt ist.

Mit der IP-Weiterleitung werden die Pakete an OPFW weitergeleitet, ohne dass die ursprüngliche Zieladresse geändert wird. **OPFW** muss die Pakete verarbeiten. Zudem muss festgelegt werden, wie mit den Paketen weiter verfahren wird.

Damit das Szenario oben erfolgreich implementiert wird, müssen Sie die IP-Weiterleitung auf den Netzwerkkarten für **OPFW**, **AZF1**, **AZF2** und **AZF3** aktivieren, die für das Routing verwendet werden (also alle Netzwerkkarten mit Ausnahme derjenigen, die mit dem Verwaltungssubnetz verbunden sind). 

## <a name="firewall-rules"></a>Firewallregeln
Wie oben beschrieben, wird mit der IP-Weiterleitung nur sichergestellt, dass Pakete an die virtuellen Geräte gesendet werden. Auf den virtuellen Geräten muss dennoch festgelegt werden, wie mit diesen Paketen weiter verfahren wird. Im Szenario oben müssen Sie die folgenden Regeln in den virtuellen Geräten erstellen:

### <a name="opfw"></a>OPFW
OPFW stellt ein lokales Gerät dar, für das folgende Regeln definiert sind:

* **Route**: Der gesamte Datenverkehr an 10.0.0.0/16 (**azurevnet**) muss über den Tunnel **ONPREMAZURE** geleitet werden.
* **Richtlinie:** Zulassen des gesamten bidirektionalen Datenverkehrs zwischen **port2** und **ONPREMAZURE**.

### <a name="azf1"></a>AZF1
AZF1 stellt ein virtuelles Azure-Gerät dar, für das folgende Regeln definiert sind:

* **Richtlinie:** Zulassen des gesamten bidirektionalen Datenverkehrs zwischen **port1** und **port2**.

### <a name="azf2"></a>AZF2
AZF2 stellt ein virtuelles Azure-Gerät dar, für das folgende Regeln definiert sind:

* **Route**: Der gesamte Datenverkehr an 10.0.0.0/16 (**onpremvnet**) muss über **port1** an die IP-Adresse des Azure-Gateways (z.B. 10.0.0.1) geleitet werden.
* **Richtlinie:** Zulassen des gesamten bidirektionalen Datenverkehrs zwischen **port1** und **port2**.

## <a name="network-security-groups-nsgs"></a>Netzwerksicherheitsgruppen
In diesem Szenario werden keine Netzwerksicherheitsgruppen verwendet. Sie können jedoch Netzwerksicherheitsgruppen auf jedes Subnetz anwenden, um den ein- und ausgehenden Datenverkehr zu beschränken. So können Sie beispielsweise die folgende NSG-Regel auf das externe FW-Subnetz anwenden.

**Eingehend**

* Zulassen des gesamten TCP-Datenverkehrs aus dem Internet an Port 80 auf allen virtuellen Computern im Subnetz.
* Verweigern des gesamten anderen Datenverkehrs aus dem Internet.

**Ausgehend**

* Verweigern des gesamten Datenverkehrs in das Internet.

## <a name="high-level-steps"></a>Schritte auf oberer Ebene:
Führen Sie zum Bereitstellen dieses Szenarios die oben beschriebenen Schritte aus.

1. Melden Sie sich bei Ihrem Azure-Abonnement an.
2. Wenn Sie ein VNET zum Imitieren des lokalen Netzwerks bereitstellen möchten, geben Sie die Ressourcen an, die zu **ONPREMRG**gehören.
3. Geben Sie die Ressourcen an, die zu **AZURERG**gehören.
4. Geben Sie den Tunnel zwischen **onpremvnet** und **azurevnet** an.
5. Nachdem Sie alle Ressourcen angegeben haben, melden Sie sich bei **onpremvm2** an, und pingen Sie 10.0.3.101, um die Verbindung zwischen **onpremsn2** und **azsn3** zu testen.

