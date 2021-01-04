---
title: Konnektivität mit einem öffentlichen Endpunkt für Azure-VMs und Standard-ILB in SAP-Szenarien mit Hochverfügbarkeit
description: Konnektivität öffentlicher Endpunkte für VMs, die Azure Load Balancer Standard in SAP-Hochverfügbarkeitsszenarien verwenden
services: virtual-machines-windows,virtual-network,storage,
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: radeltch
ms.openlocfilehash: 9c9979699b5bcb3636adc0f9b58331568ea9cad1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486301"
---
# <a name="public-endpoint-connectivity-for-virtual-machines-using-azure-standard-load-balancer-in-sap-high-availability-scenarios"></a>Konnektivität öffentlicher Endpunkte für VMs, die Azure Load Balancer Standard in SAP-Hochverfügbarkeitsszenarien verwenden

In diesem Artikel werden Konfigurationen beschrieben, die ausgehende Verbindungen mit öffentlichen Endpunkten ermöglichen. Die Konfigurationen erfolgen hauptsächlich im Rahmen der Hochverfügbarkeit mit Pacemaker für SUSE/RHEL.  

Wenn Sie Pacemaker mit dem Azure Fence-Agent in Ihrer Hochverfügbarkeitslösung verwenden, dann müssen die virtuellen Computer über eine ausgehende Verbindung mit der Azure-Verwaltungs-API verfügen. In diesem Artikel werden verschiedene Optionen vorgestellt, damit Sie die für Ihr Szenario am besten geeignete Option auswählen können.  

## <a name="overview"></a>Übersicht

Bei der Implementierung der Hochverfügbarkeit für SAP-Lösungen über das Clustering ist [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) eine der erforderlichen Komponenten. Azure bietet zwei Load Balancer-SKUs: Standard und Basic.

Die Standard-Variante von Azure Load Balancer bietet einige Vorteile gegenüber der Basic-Variante. Es funktioniert z. B. in allen Azure-Verfügbarkeitszonen, es verfügt über bessere Überwachungs- und Protokollierungsfunktionen zur einfacheren Problembehandlung und über reduzierte Wartezeiten. Das Feature „HA-Ports“ umfasst sämtliche Ports, d. h. es ist nicht mehr erforderlich, alle einzelnen Ports aufzulisten.  

Es gibt einige wichtige Unterschiede zwischen der Basic- und der Standard-SKU von Azure Load Balancer. Einer davon ist die Behandlung des ausgehenden Datenverkehrs zum öffentlichen Endpunkt. Einen vollständigen Vergleich zwischen Basic- und Standard-SKU für Load Balancer finden Sie unter [Vergleich der Load Balancer-SKUs](../../../load-balancer/load-balancer-overview.md).  
 
Wenn virtuelle Computer ohne öffentliche IP-Adressen im Back-End-Pool einer internen Azure Load Balancer Standard-Instanz (ohne öffentliche IP-Adresse) platziert werden, gibt es keine ausgehende Konnektivität mit öffentlichen Endpunkten, sofern keine zusätzliche Konfiguration erfolgt.  

Wenn einem virtuellen Computer eine öffentliche IP-Adresse zugewiesen wird oder er sich im Back-End-Pool eines Load Balancers mit öffentlicher IP-Adresse befindet, verfügt er über ausgehende Verbindungen zu öffentlichen Endpunkten.  

SAP-Systeme enthalten oft vertrauliche Geschäftsdaten. Es ist selten akzeptabel, dass virtuelle Computer, die SAP-Systeme hosten, über öffentliche IP-Adressen zugänglich sind. Gleichzeitig gibt es Szenarien, die ausgehende Verbindungen vom virtuellen Computer mit öffentlichen Endpunkten erfordern würden.  

Beispiele für Szenarien, die den Zugriff auf den öffentlichen Azure-Endpunkt erfordern:  
- Der Azure Fence-Agent benötigt Zugriff auf **management.azure.com** und **login.microsoftonline.com**.  
- [Azure Backup](https://docs.microsoft.com/azure/backup/tutorial-backup-sap-hana-db#set-up-network-connectivity)
- [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-urls)  
- Verwenden des öffentlichen Repositorys zum Patchen des Betriebssystems
- Der Datenfluss der SAP-Anwendung kann ausgehende Verbindungen mit öffentlichen Endpunkten erfordern.

Wenn Ihre SAP-Bereitstellung keine ausgehenden Verbindungen mit öffentlichen Endpunkten erfordert, müssen Sie die zusätzliche Konfiguration nicht implementieren. Es ist ausreichend, für Ihr Hochverfügbarkeitsszenario einen internen Azure Load Balancer mit Standard-SKU zu erstellen, sofern keine eingehenden Verbindungen von öffentlichen Endpunkten erforderlich sind.  

> [!Note]
> Wenn virtuelle Computer ohne öffentliche IP-Adressen im Back-End-Pool einer internen Azure Load Balancer Standard-Instanz (ohne öffentliche IP-Adresse) platziert werden, liegt keine ausgehende Internetverbindung vor, sofern nicht in einer zusätzlichen Konfiguration das Routing an öffentliche Endpunkte zugelassen wird.  
>Wenn die VMs entweder über öffentliche IP-Adressen verfügen oder sich bereits im Back-End-Pool von Azure Load Balancer mit öffentlicher IP-Adresse befinden, verfügt die VM bereits über eine ausgehende Verbindung mit öffentlichen Endpunkten.


Lesen Sie zuerst die folgenden Dokumente:

* Azure Load Balancer Standard
  * [Übersicht über Azure Load Balancer Standard](../../../load-balancer/load-balancer-overview.md): Umfassende Übersicht über Azure Load Balancer Standard, wichtige Grundsätze, Konzepte und Tutorials. 
  * [Ausgehende Verbindungen in Azure](../../../load-balancer/load-balancer-outbound-connections.md#scenarios): Szenarien zum Erreichen von ausgehenden Verbindungen in Azure.
  * [Load Balancer-Ausgangsregeln](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules): Erläutert die Konzepte der Load Balancer-Ausgangsregeln und die Erstellung von Ausgangsregeln.
* Azure Firewall
  * [Übersicht über Azure Firewall](../../../firewall/overview.md): Übersicht über Azure Firewall.
  * [Tutorial: Bereitstellen und Konfigurieren von Azure Firewall](../../../firewall/tutorial-firewall-deploy-portal.md): Anweisungen zur Konfiguration von Azure Firewall über das Azure-Portal.
* [Virtuelle Netzwerke – Benutzerdefinierte Regeln](../../../virtual-network/virtual-networks-udr-overview.md#user-defined): Konzepte und Regeln für das Azure-Routing.  
* [Diensttags für Sicherheitsgruppen](../../../virtual-network/network-security-groups-overview.md#service-tags): Informationen zum Vereinfachen Ihrer Konfiguration für Netzwerksicherheitsgruppen und Firewall mit Diensttags.

## <a name="option-1-additional-external-azure-standard-load-balancer-for-outbound-connections-to-internet"></a>Option 1: Zusätzlicher externer Azure Load Balancer Standard für ausgehende Verbindungen mit dem Internet.

Eine Möglichkeit, ausgehende Verbindungen mit öffentlichen Endpunkten zu erreichen, ohne die eingehende Verbindung zur VM vom öffentlichen Endpunkt aus zu erlauben, besteht darin, einen zweiten Load Balancer mit öffentlicher IP-Adresse zu erstellen, die VMs zum Back-End-Pool des zweiten Load Balancers hinzuzufügen und nur [ausgehende Regeln](../../../load-balancer/load-balancer-outbound-connections.md#outboundrules) zu definieren.  
Verwenden Sie [Netzwerksicherheitsgruppen](../../../virtual-network/network-security-groups-overview.md), um die öffentlichen Endpunkte zu steuern, die für ausgehende Aufrufe von der VM zugänglich sind.  
Weitere Informationen finden Sie unter Szenario 2 im Dokument [Ausgehende Verbindungen](../../../load-balancer/load-balancer-outbound-connections.md#scenarios).  
Die Konfiguration würde wie folgt aussehen:  

![Steuern der Konnektivität mit öffentlichen Endpunkten mit Netzwerksicherheitsgruppen](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-public.png)

### <a name="important-considerations"></a>Wichtige Hinweise

- Sie können einen zusätzlichen öffentlichen Load Balancer für mehrere VMs in demselben Subnetz verwenden, um ausgehende Verbindungen mit öffentlichen Endpunkten zu erreichen und die Kosten zu optimieren.  
- Verwenden Sie [Netzwerksicherheitsgruppen](../../../virtual-network/network-security-groups-overview.md), um zu steuern, welche öffentlichen Endpunkte von den VMs aus zugänglich sind. Sie können die Netzwerksicherheitsgruppe entweder dem Subnetz oder den einzelnen virtuellen Computern zuordnen. Verwenden Sie nach Möglichkeit [Diensttags](../../../virtual-network/network-security-groups-overview.md#service-tags), um die Komplexität der Sicherheitsregeln zu reduzieren.  
- Azure Load Balancer Standard mit öffentlicher IP-Adresse und Ausgangsregeln ermöglicht den direkten Zugriff auf den öffentlichen Endpunkt. Wenn unternehmensweite Sicherheitsanforderungen bestehen, dass der gesamte ausgehende Datenverkehr über eine zentrale Unternehmenslösung für Überwachung und Protokollierung abgewickelt wird, können Sie die Anforderung mit diesem Szenario möglicherweise nicht erfüllen.  

>[!TIP]
>Verwenden Sie nach Möglichkeit [Diensttags](../../../virtual-network/network-security-groups-overview.md#service-tags), um die Komplexität der Netzwerksicherheitsgruppe zu reduzieren. 

### <a name="deployment-steps"></a>Bereitstellungsschritte

1. Erstellen oder Aktualisieren eines Lastenausgleichs  
   1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf „Alle Ressourcen“ und dann auf „Hinzufügen“. Suchen Sie anschließend nach **Load Balancer**  
   1. Klicken Sie auf **Erstellen** 
   1. Load Balancer-Name **MyPublicILB**.  
   1. Wählen Sie **Öffentlich** als Typ und **Standard** als SKU aus.  
   1. Wählen Sie **Öffentliche IP-Adresse erstellen** aus, und geben Sie als Namen **MyPublicILBFrondEndIP** an.  
   1. Wählen Sie **Zonenredundant** als Verfügbarkeitszone aus.  
   1. Klicken Sie auf „Überprüfen und erstellen“, dann auf „Erstellen“.  
2. Erstellen Sie den Back-End-Pool **MyBackendPoolOfPublicILB**, und fügen Sie die virtuellen Computer hinzu.  
   1. Wählen Sie das virtuelle Netzwerk aus.  
   1. Wählen Sie die virtuellen Computer und ihre IP-Adressen aus, und fügen Sie sie dem Back-End-Pool hinzu.  
3. [Erstellen Sie Ausgangsregeln](../../../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard%3ftabs%3doption-1-create-load-balancer-standard#create-outbound-rule-configuration). Derzeit ist es nicht möglich, Ausgangsregeln über das Azure-Portal zu erstellen. Sie können Ausgangsregeln mit der [Azure-Befehlszeilenschnittstelle](../../../cloud-shell/overview.md?view=azure-cli-latest) erstellen.  

   ```azurecli
    az network lb outbound-rule create --address-pool MyBackendPoolOfPublicILB --frontend-ip-configs MyPublicILBFrondEndIP --idle-timeout 30 --lb-name MyPublicILB --name MyOutBoundRules  --outbound-ports 10000 --enable-tcp-reset true --protocol All --resource-group MyResourceGroup
   ```

4. Erstellen Sie Regeln für Netzwerksicherheitsgruppen, um den Zugriff auf bestimmte öffentliche Endpunkte einzuschränken. Wenn es eine vorhandene Netzwerksicherheitsgruppe gibt, können Sie diese anpassen. Das folgende Beispiel zeigt, wie Sie den Zugriff auf die Azure-Verwaltungs-API zulassen: 
   1. Navigieren Sie zur Netzwerksicherheitsgruppe.
   1. Klicken Sie auf „Ausgangssicherheitsregeln“.
   1. Fügen Sie eine Regel für **Gesamten ausgehenden Zugriff verweigern** zu **Internet** hinzu.
   1. Fügen Sie eine Regel zu **Zugriff** auf **AzureCloud** zulassen mit einer Priorität hinzu, die niedriger ist als die Priorität der Regel, um den gesamten Internetzugriff zu verweigern.


   Die ausgehenden Sicherheitsregeln würden wie folgt aussehen: 

   ![Ausgehende Verbindung mit zweitem Load Balancer mit öffentlicher IP-Adresse](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-network-security-groups.png)

   Weitere Informationen zu Azure-Netzwerksicherheitsgruppen finden Sie unter [Sicherheitsgruppen](../../../virtual-network/network-security-groups-overview.md). 

## <a name="option-2-azure-firewall-for-outbound-connections-to-internet"></a>Option 2: Azure Firewall für ausgehende Verbindungen mit dem Internet

Eine weitere Möglichkeit, ausgehende Verbindungen mit öffentlichen Endpunkten zu erreichen, ohne die eingehende Verbindung mit dem virtuellen Computer von öffentlichen Endpunkten aus zu ermöglichen, bietet Azure Firewall. Azure Firewall ist ein verwalteter Dienst mit integrierter Hochverfügbarkeit, der mehrere Verfügbarkeitszonen umfassen kann.  
Sie müssen auch eine [Benutzerdefinierte Route](../../../virtual-network/virtual-networks-udr-overview.md#custom-routes) bereitstellen, die mit dem Subnetz verbunden ist, in dem virtuelle Computer und der Azure Load Balancer bereitgestellt werden, und auf Azure Firewall verweisen, um den Datenverkehr über Azure Firewall zu leiten.  
Weitere Informationen zum Bereitstellen von Azure Firewall finden Sie unter [Bereitstellen und Konfigurieren von Azure Firewall](../../../firewall/tutorial-firewall-deploy-portal.md).  

Die Architektur sieht folgendermaßen aus:

![Ausgehende Verbindung mit Azure Firewall](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall.png)

### <a name="important-considerations"></a>Wichtige Hinweise

- Azure Firewall ist ein cloudnativer Dienst mit integrierter Hochverfügbarkeit, der die Zonenbereitstellung unterstützt.
- Erfordert zusätzliches Subnetz, das den Namen „AzureFirewallSubnet“ aufweisen muss. 
- Wenn große Datasets aus dem virtuellen Netzwerk, in dem sich die SAP-VMs befinden, an eine VM in einem anderen virtuellen Netzwerk oder an einen öffentlichen Endpunkt übertragen werden, ist dies möglicherweise keine kostengünstige Lösung. Ein Beispiel hierfür ist das Kopieren großer Sicherungen über virtuelle Netzwerke. Weitere Informationen finden Sie unter „Azure Firewall – Preise“.  
- Wenn die Firewalllösung im Unternehmen nicht Azure Firewall ist und die Sicherheitsanforderungen bestehen, dass sämtlicher ausgehender Datenverkehr über eine zentralisierte Unternehmenslösung geleitet wird, ist diese Lösung möglicherweise nicht praktikabel.  

>[!TIP]
>Verwenden Sie nach Möglichkeit [Diensttags](../../../virtual-network/network-security-groups-overview.md#service-tags), um die Komplexität der Azure Firewall-Regeln zu reduzieren.  

### <a name="deployment-steps"></a>Bereitstellungsschritte

1. Die Bereitstellungsschritte gehen davon aus, dass Sie bereits ein virtuelles Netzwerk und Subnetz für Ihre VMs definiert haben.  
2. Erstellen Sie das Subnetz **AzureFirewallSubnet** in demselben virtuellen Netzwerk, in dem die virtuellen Computer und der Load Balancer Standard bereitgestellt werden.  
   1. Navigieren Sie im Azure-Portal zum virtuellen Netzwerk: Klicken Sie auf „Alle Ressourcen“, „Virtuelles Netzwerk suchen“ und dann auf „Virtuelles Netzwerk“. Wählen Sie dann „Subnetze“ aus.  
   1. Klicken Sie auf „Subnetz hinzufügen“. Geben Sie **AzureFirewallSubnet** als Name ein. Geben Sie den entsprechenden Adressbereich ein. Speichern Sie.  
3. Erstellen Sie Azure Firewall.  
   1. Wählen Sie im Azure-Portal „Alle Ressourcen“ aus, klicken Sie auf „Hinzufügen“ und dann auf „Firewall“ und „Erstellen“. Wählen Sie „Ressourcengruppe“ aus (wählen Sie dieselbe Ressourcengruppe aus, in der sich das virtuelle Netzwerk befindet).  
   1. Geben Sie den Namen für die Azure Firewall-Ressource ein. Beispiel: **MyAzureFirewall**.  
   1. Wählen Sie „Region“ und mindestens zwei Verfügbarkeitszonen aus, die mit den Verfügbarkeitszonen ausgerichtet sind, in denen Ihre virtuellen Computer bereitgestellt werden.  
   1. Wählen Sie Ihr virtuelles Netzwerk aus, in dem die SAP-VMs und der Azure Load Balancer Standard bereitgestellt werden.  
   1. Öffentliche IP-Adresse: Klicken Sie auf „Erstellen“, und geben Sie einen Namen ein. Beispiel: **MyFirewallPublicIP**.  
4. Erstellen Sie eine Azure Firewall-Regel, um ausgehende Verbindungen mit bestimmten öffentlichen Endpunkten zuzulassen. Das Beispiel zeigt, wie Sie den Zugriff auf den öffentlichen Endpunkt der Azure-Verwaltungs-API zulassen.  
   1. Wählen Sie „Regeln“, „Netzwerkregelsammlung“ aus, und klicken Sie dann auf „Netzwerkregelsammlung hinzufügen“.  
   1. Name: **MyOutboundRule**, Priorität eingeben, Aktion **Zulassen** auswählen.  
   1. Dienst: Name **ToAzureAPI**.  Protokoll: Wählen Sie **Beliebig** aus. Quelladresse: Geben Sie den Bereich für Ihr Subnetz ein, in dem z. B. die virtuellen Computer und der Load Balancer Standard bereitgestellt werden: **11.97.0.0/24**. Zielports: Geben Sie <b>*</b> ein.  
   1. Speichern
   1. Da Sie sich noch bei der Azure Firewall befinden, wählen Sie „Übersicht“ aus. Notieren Sie sich die private IP-Adresse für Azure Firewall.  
5. Erstellen einer Route zu Azure Firewall  
   1. Wählen Sie im Azure-Portal „Alle Ressourcen“ aus, und klicken Sie dann auf „Hinzufügen“, „Routingtabelle“, „Erstellen“.  
   1. Geben Sie den Namen „MyRouteTable“ ein, wählen Sie „Abonnement“, „Ressourcengruppe“ und „Standort“ (entsprechend dem Standort Ihres virtuellen Netzwerks und der Firewall) aus.  
   1. Speichern  

   Die Firewallregel würde wie folgt aussehen: ![Abbildung der potenziellen Firewall](./media/high-availability-guide-standard-load-balancer/high-availability-guide-standard-load-balancer-firewall-rule.png)

6. Erstellen Sie eine benutzerdefinierte Route aus dem Subnetz Ihrer VMs zur privaten IP-Adresse von **MyAzureFirewall**.
   1. Da Sie sich bei der Routingtabelle befinden, klicken Sie auf „Routen“. Klicken Sie auf „Hinzufügen“. 
   1. Routenname: ToMyAzureFirewall, Adresspräfix: **0.0.0.0/0**. Typ des nächsten Hops: Wählen Sie „Virtuelles Gerät“ aus. Adresse des nächsten Hops: Geben Sie die private IP-Adresse der von Ihnen konfigurierten Firewall ein. **11.97.1.4**.  
   1. Speichern

## <a name="option-3-using-proxy-for-pacemaker-calls-to-azure-management-api"></a>Option 3: Verwenden von Proxy für Pacemaker-Aufrufe an die Azure-Verwaltungs-API

Sie können einen Proxy verwenden, um Pacemaker-Aufrufe für den öffentlichen Endpunkt der Azure-Verwaltungs-API zuzulassen.  

### <a name="important-considerations"></a>Wichtige Hinweise

  - Wenn bereits ein Unternehmensproxy vorhanden ist, können Sie ausgehende Aufrufe darüber an öffentliche Endpunkte weiterleiten. Ausgehende Aufrufe an öffentliche Endpunkte werden über den Unternehmenssteuerungspunkt geleitet.  
  - Stellen Sie sicher, dass die Proxykonfiguration ausgehende Verbindungen mit der Azure-Verwaltungs-API zulässt: `https://management.azure.com` und `https://login.microsoftonline.com`  
  - Stellen Sie sicher, dass eine Route von den virtuellen Computern zum Proxy vorhanden ist.  
  - Der Proxy verarbeitet nur HTTP/HTTPS-Aufrufe. Wenn es zusätzlich erforderlich ist, ausgehende Aufrufe an den öffentlichen Endpunkt über verschiedene Protokolle (wie RFC) zu tätigen, wird eine alternative Lösung benötigt.  
  - Die Proxylösung muss über Hochverfügbarkeit verfügen, um eine Instabilität im Pacemaker-Cluster zu vermeiden.  
  - Abhängig vom Standort des Proxys können zusätzliche Wartezeiten bei den Aufrufen vom Azure Fence-Agent an die Azure-Verwaltungs-API auftreten. Wenn sich Ihr Unternehmensproxy noch in der lokalen Umgebung befindet, während sich Ihr Pacemaker-Cluster in Azure befindet, messen Sie die Wartezeit und überlegen Sie, ob diese Lösung für Sie geeignet ist.  
  - Wenn nicht bereits ein Unternehmensproxy mit Hochverfügbarkeit vorhanden ist, wird diese Option nicht empfohlen, da für den Kunden zusätzliche Kosten und Komplexität entstehen würden. Wenn Sie sich dennoch für den Einsatz einer zusätzlichen Proxylösung entscheiden, um ausgehende Verbindungen von Pacemaker mit der öffentlichen API der Azure-Verwaltung zu ermöglichen, stellen Sie sicher, dass der Proxy über Hochverfügbarkeit verfügt und die Wartezeit von den virtuellen Computern zum Proxy gering ist.  

### <a name="pacemaker-configuration-with-proxy"></a>Pacemaker-Konfiguration mit Proxy 

Es gibt viele verschiedene Proxyoptionen in der Branche. Eine schrittweise Anleitung für die Proxybereitstellung liegt außerhalb des Umfangs dieses Dokuments. Im folgenden Beispiel gehen wir davon aus, dass Ihr Proxy auf **MyProxyService** antwortet und an Port **MyProxyPort** lauscht.  
Damit Pacemaker mit der Azure-Verwaltungs-API kommunizieren kann, führen Sie die folgenden Schritte für alle Clusterknoten durch:  

1. Bearbeiten Sie die Pacemaker-Konfigurationsdatei „/etc/sysconfig/pacemaker“, und fügen Sie die folgenden Zeilen (alle Clusterknoten) hinzu:

   ```console
   sudo vi /etc/sysconfig/pacemaker
   # Add the following lines
   http_proxy=http://MyProxyService:MyProxyPort
   https_proxy=http://MyProxyService:MyProxyPort
   ```

2. Starten Sie den Pacemaker-Dienst auf **allen** Clusterknoten neu.  
  - SUSE
 
     ```console
     # Place the cluster in maintenance mode
     sudo crm configure property maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo crm configure property maintenance-mode=true
     ```

  - Red Hat  

     ```console
     # Place the cluster in maintenance mode
     sudo pcs property set maintenance-mode=true
     #Restart on all nodes
     sudo systemctl restart pacemaker
     # Take the cluster out of maintenance mode
     sudo pcs property set maintenance-mode=false
     ```

## <a name="other-options"></a>Weitere Optionen

Wenn ausgehender Datenverkehr über einen URL-basierten Firewallproxy eines Drittanbieters geleitet wird, gilt Folgendes:

- Stellen Sie bei Verwendung des Azure-Fence-Agents sicher, dass die Firewallkonfiguration ausgehende Verbindungen mit der Azure-Verwaltungs-API zulässt: `https://management.azure.com` und `https://login.microsoftonline.com`.   
- Wenn Sie zum Anwenden von Updates und Patches die Updateinfrastruktur für die öffentliche Azure-Cloud von SUSE verwenden, lesen Sie [Grundlegendes zur Updateinfrastruktur für die öffentliche Azure-Cloud](https://suse.com/c/azure-public-cloud-update-infrastructure-101/).

## <a name="next-steps"></a>Nächste Schritte

* [Informationen zum Konfigurieren von Pacemaker unter SUSE in Azure](./high-availability-guide-suse-pacemaker.md)
* [Informationen zum Konfigurieren von Pacemaker unter Red Hat in Azure](./high-availability-guide-rhel-pacemaker.md)