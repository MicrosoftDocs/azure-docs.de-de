---
title: 'Konzepte: Netzwerkdiagramm für Azure Red Hat OpenShift 4'
description: Netzwerkdiagramm und Übersicht für Azure Red Hat OpenShift-Netzwerke
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: azure-redhat-openshift
ms.date: 11/23/2020
ms.openlocfilehash: b785a6b73f595072f5d723bad8c119ddc4dc0f11
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100636327"
---
# <a name="network-concepts-for-azure-red-hat-openshift-aro"></a>Netzwerkkonzepte für Azure Red Hat OpenShift (ARO)

Dieser Leitfaden enthält eine Übersicht über Netzwerke in Azure Red Hat OpenShift für OpenShift 4-Cluster sowie ein Diagramm und eine Liste wichtiger Endpunkte. Weitere Informationen zu wichtigen OpenShift-Netzwerkkonzepten finden Sie in der [Netzwerkdokumentation für Azure Red Hat OpenShift 4](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html).

![Azure Red Hat OpenShift 4: Netzwerkdiagramm](./media/concepts-networking/aro4-networking-diagram.png)

Wenn Sie Azure Red Hat OpenShift 4 bereitstellen, befindet sich Ihr gesamter Cluster in einem virtuellen Netzwerk. Innerhalb dieses virtuellen Netzwerks befinden sich Ihre Master- und Workerknoten jeweils in einem eigenen Subnetz. Von den Subnetzen wird jeweils ein interner und ein öffentlicher Lastenausgleich verwendet.

## <a name="networking-components"></a>Netzwerkkomponenten

Die folgende Liste enthält wichtige Netzwerkkomponenten in einem Azure Red Hat OpenShift-Cluster:

* **aro-pls**
    * Hierbei handelt es sich um einen Azure Private Link-Endpunkt, der von Websitezuverlässigkeits-Technikern (Site Reliability Engineers, SREs) von Microsoft und Red Hat genutzt wird, um den Cluster zu verwalten.
* **aro-internal-lb**
    * Dieser Endpunkt gleicht den Datenverkehr für den API-Server aus. Für diesen Lastenausgleich sind die Masterknoten im Back-End-Pool.
* **aro-public-lb**
    * Wenn die API öffentlich ist, wird Datenverkehr durch diesen Endpunkt an den API-Server weitergeleitet und ausgeglichen. Von diesem Endpunkt wird eine öffentliche ausgehende IP-Adresse zugewiesen, damit Masterknoten auf Azure Resource Manager zugreifen und Informationen zur Clusterintegrität zurückgeben können.
* **aro-internal**
    * Dieser Endpunkt gleicht internen Dienstdatenverkehr aus. Für diesen Lastenausgleich sind die Workerknoten im Back-End-Pool.
    * Dieser Lastenausgleich wird nicht standardmäßig erstellt. Dieser Lastenausgleich wird erstellt, wenn Sie einen Dienst vom Typ „LoadBalancer“ mit den korrekten Anmerkungen erstellen. Beispiel: service.beta.kubernetes.io/azure-load-balancer-internal: "true".
* **aro-internal-lb**
    * Dieser Endpunkt wird für öffentlichen Datenverkehr verwendet. Wenn Sie eine Anwendung und eine Route erstellen, ist dies der Pfad für eingehenden Datenverkehr.
    * Dieser Lastenausgleich deckt auch ausgehende Internetkonnektivität beliebiger Pods ab, die in den Workerknoten ausgeführt werden (über Azure Load Balancer-Ausgangsregeln).
        * Ausgangsregeln können aktuell nicht konfiguriert werden. Durch sie werden jedem Knoten 1.024 TCP-Ports zugewiesen.
        * Da „DisableOutboundSnat“ in den LB-Regeln nicht konfiguriert ist, können Pods als IP-Adresse für ausgehenden Datenverkehr eine beliebige öffentliche IP-Adresse erhalten, die in dieser ALB-Instanz konfiguriert ist.
        * Aufgrund der beiden vorherigen Punkte müssen ARO öffentliche Dienste vom Typ „LoadBalancer“ hinzugefügt werden, wenn Sie kurzlebige SNAT-Ports hinzufügen möchten.
* **aro-outbound-pip**
    * Dieser Endpunkt fungiert als öffentliche IP-Adresse (Public IP, PIP) für die Workerknoten.
    * Dieser Endpunkt ermöglicht es Diensten, eine bestimmte IP-Adresse aus einem Azure Red Hat OpenShift-Cluster einer Zulassungsliste hinzuzufügen.
* **aro-nsg**
    * Wenn Sie einen Dienst verfügbar machen, wird von der API eine Regel in dieser Netzwerksicherheitsgruppe erstellt, damit Datenverkehr weitergeleitet werden und die Steuerungsebene sowie die Knoten erreichen kann.
    * Durch diese Netzwerksicherheitsgruppe wird standardmäßig der gesamte ausgehende Datenverkehr zugelassen. Ausgehender Datenverkehr kann aktuell nur auf die Azure Red Hat OpenShift-Steuerungsebene beschränkt werden.
* **aro-controlplane-nsg**
  * Durch diesen Endpunkt wird eingehender Datenverkehr nur über den Port 6443 für die Masterknoten zugelassen.
* **Azure Container Registry**
    * Hierbei handelt es sich um eine von Microsoft intern bereitgestellte und verwendete Containerregistrierung. Diese Registrierung ist schreibgeschützt und nicht für die Verwendung durch Azure Red Hat OpenShift-Benutzer vorgesehen.
        * Durch diese Registrierung werden Hostplattformimages und Clusterkomponenten bereitgestellt. Beispiele wären etwa Container für die Überwachung oder Protokollierung.
        * Verbindungen mit dieser Registrierung werden über den Dienstendpunkt hergestellt (interne Konnektivität zwischen Azure-Diensten).
        * Diese interne Registrierung steht außerhalb des Clusters standardmäßig nicht zur Verfügung.
* **Private Link**
    * Ermöglicht Netzwerkkonnektivität für Websitezuverlässigkeits-Techniker von Microsoft und Red Hat zwischen der Verwaltungsebene und einem Cluster, um die Verwaltung Ihres Clusters zu erleichtern.

## <a name="networking-policies"></a>Netzwerkrichtlinien

* **Eingehender Datenverkehr:** Die Netzwerkrichtlinie für eingehenden Datenverkehr wird im Rahmen von [OpenShift SDN](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/about-openshift-sdn.html) unterstützt. Diese Netzwerkrichtlinie ist standardmäßig aktiviert, und die Erzwingung wird von Benutzern durchgeführt. Die Netzwerkrichtlinie für eingehenden Datenverkehr ist zwar mit der V1-Netzwerkrichtlinie (V1 NetworkPolicy) kompatibel, die Typen „Egress“ (Ausgehend) und „IPBlock“ (IP-Block) werden jedoch noch nicht unterstützt.

* **Ausgehender Datenverkehr:** Die Netzwerkrichtlinien für ausgehenden Datenverkehr werden mithilfe der [Firewall für ausgehenden Datenverkehr](https://docs.openshift.com/container-platform/4.5/networking/openshift_sdn/configuring-egress-firewall.html) in OpenShift unterstützt. Pro Namespace/Projekt gibt es jeweils nur eine einzelne Richtlinie für ausgehenden Datenverkehr. Richtlinien für ausgehenden Datenverkehr werden im Standardnamespace nicht unterstützt und nacheinander von der ersten bis zur letzten Richtlinie ausgewertet.

## <a name="networking-basics-in-openshift"></a>Netzwerkgrundlagen in OpenShift

OpenShift Software Defined Networking [(SDN)](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/about-openshift-sdn.html) wird verwendet, um ein Overlaynetzwerk mithilfe von Open vSwitch ([OVS](https://www.openvswitch.org/)) zu konfigurieren. Hierbei handelt es sich um eine OpenFlow-Implementierung, die auf der CNI-Spezifikation (Container Network Interface, Containernetzwerkschnittstelle) basiert. Von SDN werden verschiedene Plug-Ins unterstützt. In Azure Red Hat OpenShift 4 wird das Plug-In „Network Policy“ (Netzwerkrichtlinie) verwendet. Da die gesamte Netzwerkkommunikation durch SDN verwaltet wird, sind für die Kommunikation zwischen Pods keine zusätzlichen Routen in Ihren virtuellen Netzwerken erforderlich.

## <a name="networking--for-azure-red-hat-openshift"></a>Netzwerk für Azure Red Hat OpenShift

Bei den folgenden Punkten handelt es sich um spezifische Netzwerkfeatures für Azure Red Hat OpenShift:  
* Benutzer können Ihren ARO-Cluster in einem vorhandenen virtuellen Netzwerk erstellen oder im Rahmen der ARO-Clustererstellung ein virtuelles Netzwerk erstellen.
* Pod- und Dienstnetzwerk-CIDRs sind konfigurierbar.
* Knoten und Master befinden sich in unterschiedlichen Subnetzen.
* Die VNET-Subnetze von Knoten und Mastern müssen eine Mindestgröße von /27 haben.
* Standardpod-CIDR ist 10.128.0.0/14.
* Standarddienst-CIDR ist 172.30.0.0/16.
* Pod- und Dienstnetzwerk-CIDRs dürfen sich nicht mit anderen Adressbereichen in Ihrem Netzwerk überschneiden und dürfen nicht innerhalb des IP-Adressbereichs des virtuellen Netzwerks Ihres Clusters liegen.
* Pod-CIDR muss eine Mindestgröße von /18 haben. (Beim Podnetzwerk handelt es sich um nicht routingfähige IP-Adressen, und es wird ausschließlich innerhalb von OpenShift SDN verwendet.)
* Jedem Knoten wird ein Subnetz vom Typ „/23“ (512 IP-Adressen) für die zugehörigen Pods zugewiesen. Dieser Wert kann nicht geändert werden.
* Ein Pod kann nicht an mehrere Netzwerke angefügt werden.
* Es kann keine statische IP-Adresse für ausgehenden Datenverkehr konfiguriert werden. (Hierbei handelt es sich um ein OpenShift-Feature. Weitere Informationen finden Sie unter [Konfigurieren von IP-Adressen für ausgehenden Datenverkehr](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/assigning-egress-ips.html).)

## <a name="network-settings"></a>Netzwerkeinstellungen

Für Azure Red Hat OpenShift 4-Cluster stehen folgende Netzwerkeinstellungen zur Verfügung:

* **API Visibility** (API-Sichtbarkeit): Legen Sie die API-Sichtbarkeit beim Ausführen des Befehls [az aro create](tutorial-create-cluster.md#create-the-cluster) fest.
    * „Public“ (Öffentlich): Auf den API-Server kann von externen Netzwerken zugegriffen werden.
    * „Private“ (Privat): Vom API-Server wurde eine private IP-Adresse aus dem Mastersubnetz zugewiesen, und der Zugriff ist nur über verbundene Netzwerke (mittels Peering verbundene virtuelle Netzwerke, andere Subnetze im Cluster) möglich. Für den Kunden wird eine private DNS-Zone erstellt.
* **Ingress Visibility** (Sichtbarkeit für eingehenden Datenverkehr): Legen Sie die API-Sichtbarkeit beim Ausführen des Befehls [az aro create](tutorial-create-cluster.md#create-the-cluster) fest.
    * „Public“ (Öffentlich): Für Routen wird standardmäßig eine öffentliche Load Balancer Standard-Instanz verwendet. (Kann geändert werden.)
    * „Private“ (Privat): Für Routen wird standardmäßig der interne Lastenausgleich verwendet. (Kann geändert werden.)

## <a name="network-security-groups"></a>Netzwerksicherheitsgruppen
Netzwerksicherheitsgruppen werden in der (für Benutzer gesperrten) Ressourcengruppe des Knotens erstellt. Die Netzwerksicherheitsgruppen werden direkt den Subnetzen zugewiesen und nicht den NICs des Knotens. Die Netzwerksicherheitsgruppen sind unveränderlich, und Benutzer sind nicht berechtigt, sie zu ändern.

Mit einem öffentlich sichtbaren API-Server können keine Netzwerksicherheitsgruppen erstellt und den NICs zugewiesen werden.

## <a name="domain-forwarding"></a>Domänenweiterleitung
Von Azure Red Hat OpenShift wird CoreDNS verwendet. Die Domänenweiterleitung kann konfiguriert werden. In Ihren virtuellen Netzwerken kann kein eigenes DNS verwendet werden. Weitere Informationen finden Sie in der Dokumentation zur [Verwendung der DNS-Weiterleitung](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html#nw-dns-forward_dns-operator).

## <a name="whats-new-in-openshift-45"></a>Neues in OpenShift 4.5

Mit der Unterstützung von OpenShift 4.5 hat Azure Red Hat OpenShift einige bedeutende Architekturänderungen eingeführt. Diese Änderungen gelten nur für neu erstellte Cluster mit OpenShift 4.5. Bei bereits vorhandenen Clustern, für die ein Upgrade auf OpenShift 4.5 durchgeführt wurde, wird die Netzwerkarchitektur durch den Upgradeprozess nicht geändert. Benutzer müssen ihre Cluster neu erstellen, um die neue Architektur verwenden zu können.

![Azure Red Hat OpenShift 4.5: Netzwerkdiagramm](./media/concepts-networking/aro-4-5-networking-diagram.png)

Wie Sie in der obigen Abbildung sehen, gibt es ein paar Änderungen:
* Bislang wurden von ARO zwei öffentliche Lastenausgleichsmodule verwendet: eins für den API-Server und eins für den Workerknotenpool. Dies wurde im Zuge des Architekturupdates unter einem einzelnen Lastenausgleichsmodul zusammengefasst. 
* Die dedizierten IP-Adressressourcen für ausgehenden Datenverkehr wurden entfernt, um die Komplexität zu verringern.
* Von der ARO-Steuerungsebene wird nun die gleiche Netzwerksicherheitsgruppe verwendet wie von den ARO-Workerknoten.

Weitere Informationen zu OpenShift 4.5 finden Sie in den [Versionshinweisen zu OpenShift 4.5](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu ausgehendem Datenverkehr und zur Unterstützung von Azure Red Hat OpenShift in diesem Zusammenhang finden Sie unter [Unterstützungsrichtlinien in Azure Red Hat OpenShift](support-policies-v4.md).
