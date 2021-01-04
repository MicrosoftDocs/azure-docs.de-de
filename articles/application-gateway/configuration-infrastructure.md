---
title: Konfigurieren der Azure Application Gateway-Infrastruktur
description: In diesem Artikel wird das Konfigurieren der Azure Application Gateway-Infrastruktur beschrieben.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: f214b0b0751f44ea1357f569fd814a7621af61ab
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397619"
---
# <a name="application-gateway-infrastructure-configuration"></a>Konfigurieren der Application Gateway-Infrastruktur

Die Application Gateway-Infrastruktur umfasst das virtuelle Netzwerk, Subnetze, Netzwerksicherheitsgruppen und benutzerdefinierte Routen.

## <a name="virtual-network-and-dedicated-subnet"></a>Virtuelles Netzwerk und dediziertes Subnetz

Ein Application Gateway ist eine dedizierte Bereitstellung in Ihrem virtuellen Netzwerk. Innerhalb Ihres virtuellen Netzwerks ist ein dediziertes Subnetz für das Application Gateway erforderlich. Sie können in einem Subnetz mehrere Instanzen einer bestimmten Application Gateway-Bereitstellung betreiben. Außerdem können Sie weitere Application Gateway-Instanzen im Subnetz bereitstellen. Aber Sie können keine andere Ressource im Application Gateway-Subnetz bereitstellen. Sie können ein Standard_v2- und ein standardmäßiges Azure Application Gateway nicht parallel in demselben Subnetz betreiben.

> [!NOTE]
> [Richtlinien für VNET-Dienstendpunkte](../virtual-network/virtual-network-service-endpoint-policies-overview.md) werden derzeit in einem Application Gateway-Subnetz nicht unterstützt.

### <a name="size-of-the-subnet"></a>Subnetzgröße

Application Gateway nutzt eine private IP-Adresse pro Instanz sowie eine weitere private IP-Adresse, wenn eine private Front-End-IP konfiguriert ist.

Azure reserviert außerdem fünf IP-Adressen in jedem Subnetz für die interne Verwendung: die ersten vier und die letzte IP-Adresse. Stellen Sie sich beispielsweise 15 Application Gateway-Instanzen ohne private Front-End-IP vor. Sie benötigen mindestens 20 IP-Adressen für dieses Subnetz: fünf zur internen Verwendung und 15 für die Application Gateway-Instanzen.

Stellen Sie sich ein Subnetz mit 27 Application Gateway-Instanzen und einer privaten Front-End-IP vor. In diesem Fall benötigen Sie 33 IP-Adressen: 27 für die Application Gateway-Instanzen, eine für das private Front-End und fünf für die interne Verwendung.

Die Application Gateway-SKU (Standard oder WAF) kann bis zu 32 Instanzen unterstützen (32 Instanz-IP-Adressen + 1 private Front-End-IP + 5 für Azure reserviert). Daher wird eine Subnetzmindestgröße von /26 empfohlen.

Die Application Gateway-SKU (Standard_v2 oder WAF_v2) kann bis zu 125 Instanzen unterstützen (125 Instanz-IP-Adressen + 1 private Front-End-IP + 5 für Azure reserviert). Daher wird eine Subnetzmindestgröße von /24 empfohlen.

## <a name="network-security-groups"></a>Netzwerksicherheitsgruppen

Netzwerksicherheitsgruppen (NSG) werden im Application Gateway-Subnetz unterstützt. Es gelten jedoch einige Einschränkungen:

- Sie müssen eingehenden Internetdatenverkehr über die TCP-Ports 65503–65534 (für die Application Gateway v1-SKU) und über die TCP-Ports 65200–65535 (für die v2-SKU) mit dem Zielsubnetz **Beliebig** und der Quelle **GatewayManager** -Diensttag zulassen. Dieser Portbereich ist für die Kommunikation mit der Azure-Infrastruktur erforderlich. Diese Ports werden von Azure-Zertifikaten geschützt (gesperrt). Externe Entitäten, einschließlich der Kunden dieses Gateways, können nicht auf diesen Endpunkten kommunizieren.

- Die ausgehende Internetverbindung kann nicht blockiert sein. Standardausgangsregeln in der NSG ermöglichen Internetkonnektivität. Wir empfehlen Folgendes:

  - Entfernen Sie die Standardausgangsregeln nicht.
  - Erstellen Sie keine anderen Ausgangsregeln, die ausgehende Konnektivität verweigern.

- Datenverkehr vom **AzureLoadBalancer** -Tag mit dem Zielsubnetz als **Beliebig** muss zulässig sein.

### <a name="allow-access-to-a-few-source-ips"></a>Erlauben des Zugriffs auf einige Quell-IP-Adressen

Verwenden Sie für dieses Szenario Netzwerksicherheitsgruppen im Application Gateway-Subnetz. Legen Sie die folgenden Einschränkungen für das Subnetz in dieser Priorität fest:

1. Erlauben Sie eingehenden Datenverkehr aus einer Quell-IP oder einem IP-Adressbereich mit dem Ziel des gesamten Application Gateway-Subnetzadressbereichs und dem Zielport als eingehenden Zugriffsport, z. B. Port 80 für den HTTP-Zugriff.
2. Erlauben Sie eingehende Anforderungen von der Quelle **GatewayManager** -Diensttag und dem Ziel **Beliebig** sowie den Zielports 65503-65534 für die Application Gateway v1-SKU und den Ports 65200-65535 für v2-SKU für die [Kommunikation des Back-End-Integritätsstatus](./application-gateway-diagnostics.md). Dieser Portbereich ist für die Kommunikation mit der Azure-Infrastruktur erforderlich. Diese Ports werden von Azure-Zertifikaten geschützt (gesperrt). Ohne entsprechende Zertifikate können externe Entitäten keine Änderungen an diesen Endpunkten vornehmen.
3. Lassen Sie eingehende Azure Load Balancer-Tests ( *AzureLoadBalancer-Tag* ) und eingehenden virtuellen Netzwerkdatenverkehr ( *VirtualNetwork* -Tag) für die [Netzwerksicherheitsgruppe](../virtual-network/network-security-groups-overview.md) zu.
4. Blockieren Sie den gesamten übrigen eingehenden Datenverkehr mit einer Alle-verweigern-Regel.
5. Zulassen von ausgehendem Datenverkehr an das Internet für alle Ziele.

## <a name="supported-user-defined-routes"></a>Unterstützte benutzerdefinierte Routen 

> [!IMPORTANT]
> Das Verwenden benutzerdefinierter Routen im Application Gateway-Subnetz kann dazu führen, dass der Integritätsstatus in der [Ansicht der Back-End-Integrität](./application-gateway-diagnostics.md#back-end-health) als **Unbekannt** angezeigt wird. Außerdem können bei der Generierung von Application Gateway-Protokollen und -Metriken Fehler auftreten. Sie sollten keine benutzerdefinierten Routen im Application Gateway-Subnetz verwenden, damit Sie die Back-End-Integrität, Protokolle und Metriken anzeigen können.

- **v1**

   Für die v1 SKU werden benutzerdefinierte Routen (User-Defined Routes, UDRs) im Application Gateway-Subnetz unterstützt, solange sie die End-to-End-Anforderung/Antwort-Kommunikation nicht ändern. Beispielsweise können Sie eine benutzerdefinierte Route im Application Gateway-Subnetz einrichten, um auf eine Firewallappliance für die Paketüberprüfung zu verweisen. Sie müssen jedoch sicherstellen, dass das Paket nach der Überprüfung das vorgesehene Ziel erreichen kann. Ein Unterlassen kann zu einem falschen Integritätstest oder Datenverkehrsrouting-Verhalten führen. Dies schließt gelernte Routen oder standardmäßige 0.0.0.0/0-Routen ein, die durch Azure ExpressRoute oder VPN-Gateways im virtuellen Netzwerk verteilt werden. Alle Szenarien, in denen 0.0.0.0/0 lokal umgeleitet werden muss (erzwungenes Tunneln), werden für v1 nicht unterstützt.

- **v2**

   Für die v2-SKU gibt es unterstützte und nicht unterstützte Szenarios:

   **Unterstützte v2-Szenarios**
   > [!WARNING]
   > Eine fehlerhafte Konfiguration der Routingtabelle kann zu asymmetrischer Routenplanung in Application Gateway v2 führen. Stellen Sie sicher, dass der gesamte Datenverkehr der Verwaltungs-/Steuerungsebene direkt an das Internet und nicht über ein virtuelles Gerät gesendet wird. Dies kann sich auch auf die Protokollierung und Metriken auswirken.


  **Szenario 1** : Benutzerdefinierte Routen zum Deaktivieren der BGP-Routenverteilung (Border Gateway Protocol) an das Application Gateway-Subnetz

   Manchmal wird die Standardgatewayroute (0.0.0.0/0) per ExpressRoute oder VPN-Gateways angekündigt, die dem virtuellen Application Gateway-Netzwerk zugeordnet sind. Dies unterbricht den Datenverkehr der Verwaltungsebene, da er einen direkten Pfad zum Internet erfordert. In solchen Szenarios kann eine benutzerdefinierte Route zum Deaktivieren der BGP-Routenverteilung verwendet werden. 

   Führen Sie die folgenden Schritte aus, um die BGP-Routenverteilung zu deaktivieren:

   1. Erstellen Sie eine Routingtabelleressource in Azure.
   2. Deaktivieren Sie den Parameter **Routenverteilung des Gateways für virtuelle Netzwerke**. 
   3. Ordnen Sie die Routingtabelle dem entsprechenden Subnetz zu. 

   Das Aktivieren der benutzerdefinierten Route für dieses Szenario sollte keine vorhandenen Setups unterbrechen.

  **Szenario 2** : Benutzerdefinierte Route zum Weiterleiten von Datenverkehr an 0.0.0.0/0 an das Internet

   Sie können eine benutzerdefinierte Route erstellen, um Datenverkehr an 0.0.0.0/0 direkt an das Internet zu übermitteln. 

  **Szenario 3:** Benutzerdefinierte Route für Azure Kubernetes Service mit kubenet

  Wenn Sie kubenet mit Azure Kubernetes Service (AKS) und dem Application Gateway-Eingangscontroller (AGIC, Application Gateway Ingress Controller) verwenden, benötigen Sie eine Routingtabelle, die die Weiterleitung des von Application Gateway an die Pods gesendeten Datenverkehrs an den richtigen Knoten erlaubt. Dies ist nicht erforderlich, wenn Sie Azure CNI verwenden. 

  Führen Sie die folgenden Schritte aus, um die Routingtabelle einzurichten, damit kubenet funktioniert:

  1. Wechseln Sie zu der Ressourcengruppe, die von AKS erstellt wurde (der Name der Ressourcengruppe sollte mit „MC_“ beginnen).
  2. Suchen Sie von AKS in dieser Ressourcengruppe erstellte Routentabelle. Die Routingtabelle sollte mit den folgenden Informationen aufgefüllt sein:
     - Das Adresspräfix sollte dem IP-Adressbereich der Pods entsprechen, die Sie in AKS erreichen möchten. 
     - Der Typ des nächsten Hops sollte „Virtuelles Gerät“ sein. 
     - Die Adresse des nächsten Hops sollte der IP-Adresse des Knotens entsprechen, der die Pods hostet.
  3. Ordnen Sie diese Routingtabelle dem Subnetz des Application Gateways zu. 
    
  **Nicht unterstützte v2-Szenarios**

  **Szenario 1** : Benutzerdefinierte Route für virtuelle Geräte

  Szenarios, in denen Datenverkehr an 0.0.0.0/0 über ein virtuelles Gerät, ein virtuelles Hub-/Spoke-Netzwerk oder lokal (Tunnelerzwingung) umgeleitet werden muss, werden in V2 nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie mehr über die Konfiguration der Front-End-IP-Adresse.](configuration-front-end-ip.md)