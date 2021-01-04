---
title: Einschränken des ausgehenden Datenverkehrs in Azure Kubernetes Service (AKS)
description: Erfahren Sie, welche Ports und Adressen zur Steuerung des ausgehenden Datenverkehrs in Azure Kubernetes Service (AKS) erforderlich sind.
services: container-service
ms.topic: article
ms.author: jpalma
ms.date: 11/09/2020
author: palma21
ms.openlocfilehash: a1d045e66771026d2b4cf7ad44fd6943d2d407f4
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701601"
---
# <a name="control-egress-traffic-for-cluster-nodes-in-azure-kubernetes-service-aks"></a>Steuern des ausgehenden Datenverkehrs für Clusterknoten in Azure Kubernetes Service (AKS)

In diesem Artikel werden die erforderlichen Details zum Schutz des ausgehenden Datenverkehrs von Azure Kubernetes Service (AKS) behandelt. Er enthält die Clusteranforderungen für eine grundlegende AKS-Bereitstellung sowie zusätzliche Anforderungen für optionale Add-Ons und Features. [Am Ende des Artikels wird anhand eines Beispiels gezeigt, wie diese Anforderungen mit Azure Firewall konfiguriert werden.](#restrict-egress-traffic-using-azure-firewall) Diese Informationen sind jedoch auf jede Einschränkungsmethode oder Appliance für ausgehenden Datenverkehr anwendbar.

## <a name="background"></a>Hintergrund

AKS-Cluster werden in einem virtuellen Netzwerk bereitgestellt. Dabei kann es sich um ein verwaltetes (von AKS erstelltes) Netzwerk oder um ein benutzerdefiniertes (durch den Benutzer vorkonfiguriertes) Netzwerk handeln. In beiden Fällen sind für den Cluster **ausgehende** Abhängigkeiten von Diensten außerhalb des virtuellen Netzwerks vorhanden. (Eingehende Abhängigkeiten sind für den Dienst nicht vorhanden.)

Zu Verwaltungs- und Betriebszwecken müssen Knoten in einem AKS-Cluster auf bestimmte Ports und vollqualifizierte Domänennamen (FQDNs) zugreifen. Diese Endpunkte sind erforderlich, damit die Knoten mit dem API-Server kommunizieren oder Kernkomponenten des Kubernetes-Clusters sowie Sicherheitsupdates für Knoten heruntergeladen und installiert werden können. So müssen von dem Cluster beispielsweise Containerimages des Basissystems aus der Microsoft-Containerregistrierung (MCR) abgerufen werden.

Die AKS-Abhängigkeiten für ausgehenden Datenverkehr werden fast ausschließlich mit FQDNs definiert, hinter denen sich keine statischen Adressen befinden. Das Fehlen statischer Adressen bedeutet, dass keine Netzwerksicherheitsgruppen verwendet werden können, um den ausgehenden Datenverkehr eines AKS-Clusters zu sperren. 

Standardmäßig haben AKS-Cluster uneingeschränkten ausgehenden Internetzugriff. Diese Ebene des Netzwerkzugriffs ermöglicht, dass ausgeführte Knoten und Dienste nach Bedarf auf externe Ressourcen zugreifen können. Wenn Sie den ausgehenden Datenverkehr einschränken möchten, muss eine begrenzte Anzahl von Ports und Adressen zugänglich sein, um fehlerfreie Clusterwartungsaufgaben verwalten zu können. Die einfachste Lösung zum Schutz ausgehender Adressen besteht in der Verwendung eines Firewallgeräts, das den ausgehenden Datenverkehr auf der Grundlage von Domänennamen kontrolliert. Von Azure Firewall kann beispielsweise ausgehender HTTP- und HTTPS-Datenverkehr auf der Grundlage des FQDN des Ziels eingeschränkt werden. Darüber hinaus können Sie Ihre bevorzugten Firewall- und Sicherheitsregeln konfigurieren, um diese erforderlichen Ports und Adressen zuzulassen.

> [!IMPORTANT]
> In diesem Dokument wird lediglich erläutert, wie der ausgehende Datenverkehr aus dem AKS-Subnetz gesperrt wird. Für AKS gelten standardmäßig keine Anforderungen für eingehenden Datenverkehr.  Das Blockieren von **internem Datenverkehr im Subnetz** mithilfe von Netzwerksicherheitsgruppen (NSGs) und Firewalls wird nicht unterstützt. Zum Steuern und Blockieren des Datenverkehrs innerhalb des Clusters müssen [**_Netzwerkrichtlinien_* _][network-policy] verwendet werden.

## <a name="required-outbound-network-rules-and-fqdns-for-aks-clusters"></a>Erforderliche Netzwerkregeln für ausgehenden Datenverkehr und FQDNs für AKS-Cluster

Die folgenden Netzwerk- und FQDN-/Anwendungsregeln sind für einen AKS-Cluster erforderlich und können verwendet werden, um eine Azure Firewall-fremde Lösung zu konfigurieren.

_ IP-Adressabhängigkeiten gelten für Nicht-HTTP/S-Datenverkehr (TCP- und UDP-Datenverkehr).
* FQDN-HTTP/HTTPS-Endpunkte können in Ihrem Firewallgerät bereitgestellt werden.
* HTTP/HTTPS-Platzhalterendpunkte sind Abhängigkeiten, die sich je nach AKS-Cluster unterscheiden können (basierend auf einer Reihe von Qualifizierern).
* Von AKS wird ein Zugangscontroller verwendet, um den FQDN als Umgebungsvariable in alle Bereitstellungen unter „kube-system“ und „gatekeeper-system“ einzufügen. Dadurch wird sichergestellt, dass bei der gesamten Systemkommunikation zwischen Knoten und API-Server nicht die IP-Adresse, sondern der FQDN des API-Servers verwendet wird. 
* Wenn Sie über eine App oder Lösung verfügen, die mit dem API-Server kommunizieren muss, müssen Sie eine **zusätzliche** Netzwerkregel hinzufügen, um die *TCP-Kommunikation mit dem Port 443 der IP-Adresse Ihres API-Servers* zuzulassen.
* In seltenen Fällen kann sich die IP-Adresse Ihres API-Servers aufgrund eines Wartungsvorgang ändern. Geplante Wartungsvorgänge, bei denen sich die IP-Adresse des API-Servers ändern kann, werden immer vorab kommuniziert.


### <a name="azure-global-required-network-rules"></a>Für Azure Global benötigte Netzwerkregeln

Folgende Netzwerkregeln und IP-Adressabhängigkeiten werden benötigt:

| Zielendpunkt                                                             | Protokoll | Port    | Zweck  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Oder* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:1194`** <br/> *Oder* <br/> [Regionale CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:1194`** <br/> *Oder* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1\.194      | Getunnelte sichere Kommunikation zwischen den Knoten und der Steuerungsebene Dies ist für [private Cluster](private-clusters.md) nicht erforderlich.|
| **`*:9000`** <br/> *Oder* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:9000`** <br/> *Oder* <br/> [Regionale CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:9000`** <br/> *Oder* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Getunnelte sichere Kommunikation zwischen den Knoten und der Steuerungsebene Dies ist für [private Cluster](private-clusters.md) nicht erforderlich. |
| **`*:123`** oder **`ntp.ubuntu.com:123`** (bei Verwendung von Azure Firewall-Netzwerkregeln)  | UDP      | 123     | Erforderlich für die NTP-Zeitsynchronisierung (Network Time Protocol) auf Linux-Knoten                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Bei Verwendung benutzerdefinierter DNS-Server müssen die Clusterknoten auf diese Server zugreifen können. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Erforderlich bei Verwendung von Pods/Bereitstellungen, die auf den API-Server zugreifen. Von diesen Pods/Bereitstellungen wird die API-IP-Adresse verwendet. Dies ist für [private Cluster](private-clusters.md) nicht erforderlich.  |

### <a name="azure-global-required-fqdn--application-rules"></a>Für Azure Global benötigte FQDNs/Anwendungsregeln 

Die folgenden vollqualifizierten Domänennamen und Anwendungsregeln sind erforderlich:

| Ziel-FQDN                 | Port            | Zweck      |
|----------------------------------|-----------------|----------|
| **`*.hcp.<location>.azmk8s.io`** | **`HTTPS:443`** | Erforderlich für die Kommunikation zwischen Knoten und API-Server. Ersetzen Sie *\<location\>* durch die Region, in der Ihr AKS-Cluster bereitgestellt wird. |
| **`mcr.microsoft.com`**          | **`HTTPS:443`** | Erforderlich für den Zugriff auf Images in Microsoft Container Registry (MCR). Diese Registrierung enthält Images/Diagramme von Erstanbietern (beispielsweise coreDNS usw.). Diese Images sind für die korrekte Erstellung und Funktionsweise des Clusters erforderlich (unter anderem für Skalierungs- und Upgradevorgänge).  |
| **`*.data.mcr.microsoft.com`**   | **`HTTPS:443`** | Erforderlich für den auf Azure Content Delivery Network (CDN) basierenden MCR-Speicher. |
| **`management.azure.com`**       | **`HTTPS:443`** | Erforderlich für Kubernetes-Vorgänge für die Azure-API. |
| **`login.microsoftonline.com`**  | **`HTTPS:443`** | Erforderlich für die Azure Active Directory-Authentifizierung. |
| **`packages.microsoft.com`**     | **`HTTPS:443`** | Diese Adresse ist das Microsoft-Paketrepository, das für zwischengespeicherte *apt-get*-Vorgänge verwendet wird.  Beispielpakete sind Moby, PowerShell und Azure CLI. |
| **`acs-mirror.azureedge.net`**   | **`HTTPS:443`** | Hierbei handelt es sich um die Adresse für das Repository, das zum Herunterladen und Installieren erforderlicher Binärdateien wie kubenet und Azure CNI benötigt wird. |

### <a name="azure-china-21vianet-required-network-rules"></a>Für Azure China 21Vianet benötigte Netzwerkregeln

Folgende Netzwerkregeln und IP-Adressabhängigkeiten werden benötigt:

| Zielendpunkt                                                             | Protokoll | Port    | Zweck  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Oder* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.Region:1194`** <br/> *Oder* <br/> [Regionale CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:1194`** <br/> *Oder* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1.194      | Getunnelte sichere Kommunikation zwischen den Knoten und der Steuerungsebene |
| **`*:9000`** <br/> *Oder* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:9000`** <br/> *Oder* <br/> [Regionale CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:9000`** <br/> *Oder* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Getunnelte sichere Kommunikation zwischen den Knoten und der Steuerungsebene |
| **`*:22`** <br/> *Oder* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:22`** <br/> *Oder* <br/> [Regionale CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:22`** <br/> *Oder* <br/> **`APIServerPublicIP:22`** `(only known after cluster creation)`  | TCP           | 22      | Getunnelte sichere Kommunikation zwischen den Knoten und der Steuerungsebene |
| **`*:123`** oder **`ntp.ubuntu.com:123`** (bei Verwendung von Azure Firewall-Netzwerkregeln)  | UDP      | 123     | Erforderlich für die NTP-Zeitsynchronisierung (Network Time Protocol) auf Linux-Knoten                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Bei Verwendung benutzerdefinierter DNS-Server müssen die Clusterknoten auf diese Server zugreifen können. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Erforderlich bei Verwendung von Pods/Bereitstellungen, die auf den API-Server zugreifen. Von diesen Pods/Bereitstellungen wird die API-IP-Adresse verwendet.  |

### <a name="azure-china-21vianet-required-fqdn--application-rules"></a>Für Azure China 21Vianet benötigte FQDNs/Anwendungsregeln

Die folgenden vollqualifizierten Domänennamen und Anwendungsregeln sind erforderlich:

| Ziel-FQDN                               | Port            | Zweck      |
|------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Erforderlich für die Kommunikation zwischen Knoten und API-Server. Ersetzen Sie *\<location\>* durch die Region, in der Ihr AKS-Cluster bereitgestellt wird. |
| **`*.tun.<location>.cx.prod.service.azk8s.cn`**| **`HTTPS:443`** | Erforderlich für die Kommunikation zwischen Knoten und API-Server. Ersetzen Sie *\<location\>* durch die Region, in der Ihr AKS-Cluster bereitgestellt wird. |
| **`mcr.microsoft.com`**                        | **`HTTPS:443`** | Erforderlich für den Zugriff auf Images in Microsoft Container Registry (MCR). Diese Registrierung enthält Images/Diagramme von Erstanbietern (beispielsweise coreDNS usw.). Diese Images sind für die korrekte Erstellung und Funktionsweise des Clusters erforderlich (unter anderem für Skalierungs- und Upgradevorgänge). |
| **`.data.mcr.microsoft.com`**                  | **`HTTPS:443`** | Erforderlich für den auf Azure Content Delivery Network (CDN) basierenden MCR-Speicher. |
| **`management.chinacloudapi.cn`**              | **`HTTPS:443`** | Erforderlich für Kubernetes-Vorgänge für die Azure-API. |
| **`login.chinacloudapi.cn`**                   | **`HTTPS:443`** | Erforderlich für die Azure Active Directory-Authentifizierung. |
| **`packages.microsoft.com`**                   | **`HTTPS:443`** | Diese Adresse ist das Microsoft-Paketrepository, das für zwischengespeicherte *apt-get*-Vorgänge verwendet wird.  Beispielpakete sind Moby, PowerShell und Azure CLI. |
| **`*.azk8s.cn`**                               | **`HTTPS:443`** | Hierbei handelt es sich um die Adresse für das Repository, das zum Herunterladen und Installieren erforderlicher Binärdateien wie kubenet und Azure CNI benötigt wird. |

### <a name="azure-us-government-required-network-rules"></a>Für Azure US Government benötigte Netzwerkregeln

Folgende Netzwerkregeln und IP-Adressabhängigkeiten werden benötigt:

| Zielendpunkt                                                             | Protokoll | Port    | Zweck  |
|----------------------------------------------------------------------------------|----------|---------|------|
| **`*:1194`** <br/> *Oder* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:1194`** <br/> *Oder* <br/> [Regionale CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:1194`** <br/> *Oder* <br/> **`APIServerPublicIP:1194`** `(only known after cluster creation)`  | UDP           | 1.194      | Getunnelte sichere Kommunikation zwischen den Knoten und der Steuerungsebene |
| **`*:9000`** <br/> *Oder* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureCloud.<Region>:9000`** <br/> *Oder* <br/> [Regionale CIDRs](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) -  **`RegionCIDRs:9000`** <br/> *Oder* <br/> **`APIServerPublicIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Getunnelte sichere Kommunikation zwischen den Knoten und der Steuerungsebene |
| **`*:123`** oder **`ntp.ubuntu.com:123`** (bei Verwendung von Azure Firewall-Netzwerkregeln)  | UDP      | 123     | Erforderlich für die NTP-Zeitsynchronisierung (Network Time Protocol) auf Linux-Knoten                 |
| **`CustomDNSIP:53`** `(if using custom DNS servers)`                             | UDP      | 53      | Bei Verwendung benutzerdefinierter DNS-Server müssen die Clusterknoten auf diese Server zugreifen können. |
| **`APIServerPublicIP:443`** `(if running pods/deployments that access the API Server)` | TCP      | 443     | Erforderlich bei Verwendung von Pods/Bereitstellungen, die auf den API-Server zugreifen. Von diesen Pods/Bereitstellungen wird die API-IP-Adresse verwendet.  |

### <a name="azure-us-government-required-fqdn--application-rules"></a>Für Azure US Government benötigte FQDNs/Anwendungsregeln 

Die folgenden vollqualifizierten Domänennamen und Anwendungsregeln sind erforderlich:

| Ziel-FQDN                                        | Port            | Zweck      |
|---------------------------------------------------------|-----------------|----------|
| **`*.hcp.<location>.cx.aks.containerservice.azure.us`** | **`HTTPS:443`** | Erforderlich für die Kommunikation zwischen Knoten und API-Server. Ersetzen Sie *\<location\>* durch die Region, in der Ihr AKS-Cluster bereitgestellt wird.|
| **`mcr.microsoft.com`**                                 | **`HTTPS:443`** | Erforderlich für den Zugriff auf Images in Microsoft Container Registry (MCR). Diese Registrierung enthält Images/Diagramme von Erstanbietern (beispielsweise coreDNS usw.). Diese Images sind für die korrekte Erstellung und Funktionsweise des Clusters erforderlich (unter anderem für Skalierungs- und Upgradevorgänge). |
| **`*.data.mcr.microsoft.com`**                          | **`HTTPS:443`** | Erforderlich für den auf Azure Content Delivery Network (CDN) basierenden MCR-Speicher. |
| **`management.usgovcloudapi.net`**                      | **`HTTPS:443`** | Erforderlich für Kubernetes-Vorgänge für die Azure-API. |
| **`login.microsoftonline.us`**                          | **`HTTPS:443`** | Erforderlich für die Azure Active Directory-Authentifizierung. |
| **`packages.microsoft.com`**                            | **`HTTPS:443`** | Diese Adresse ist das Microsoft-Paketrepository, das für zwischengespeicherte *apt-get*-Vorgänge verwendet wird.  Beispielpakete sind Moby, PowerShell und Azure CLI. |
| **`acs-mirror.azureedge.net`**                          | **`HTTPS:443`** | Diese Adresse ist für das Repository, das zum Installieren erforderlicher Binärdateien wie kubenet und Azure CNI benötigt wird. |

## <a name="optional-recommended-fqdn--application-rules-for-aks-clusters"></a>Optional empfohlene FQDNs/Anwendungsregeln für AKS-Cluster

Die folgenden FQDNs und Anwendungsregeln sind optional, werden jedoch für AKS-Cluster empfohlen:

| Ziel-FQDN                                                               | Port          | Zweck      |
|--------------------------------------------------------------------------------|---------------|----------|
| **`security.ubuntu.com`, `azure.archive.ubuntu.com`, `changelogs.ubuntu.com`** | **`HTTP:80`** | Über diese Adresse können die Linux-Clusterknoten die erforderlichen Sicherheitspatches und Updates herunterladen. |

Wenn Sie diese FQDNs blockieren/nicht zulassen, erhalten die Knoten nur Betriebssystemupdates, wenn Sie ein [Upgrade für Knotenimages](node-image-upgrade.md) oder ein [Clusterupgrade](upgrade-cluster.md) durchführen.

## <a name="gpu-enabled-aks-clusters"></a>GPU-fähige AKS-Cluster

### <a name="required-fqdn--application-rules"></a>Benötigte FQDNs/Anwendungsregeln

Die folgenden vollqualifizierten Domänennamen und Anwendungsregeln sind für AKS-Cluster mit aktivierter GPU erforderlich:

| Ziel-FQDN                        | Port      | Zweck      |
|-----------------------------------------|-----------|----------|
| **`nvidia.github.io`**                  | **`HTTPS:443`** | Diese Adresse wird für die ordnungsgemäße Treiberinstallation und -ausführung für GPU-basierte Knoten verwendet. |
| **`us.download.nvidia.com`**            | **`HTTPS:443`** | Diese Adresse wird für die ordnungsgemäße Treiberinstallation und -ausführung für GPU-basierte Knoten verwendet. |
| **`apt.dockerproject.org`**             | **`HTTPS:443`** | Diese Adresse wird für die ordnungsgemäße Treiberinstallation und -ausführung für GPU-basierte Knoten verwendet. |

## <a name="windows-server-based-node-pools"></a>Windows Server-basierte Knotenpools 

### <a name="required-fqdn--application-rules"></a>Benötigte FQDNs/Anwendungsregeln

Die folgenden vollqualifizierten Domänennamen und Anwendungsregeln sind für die Verwendung Windows Server-basierter Knotenpools erforderlich:

| Ziel-FQDN                                                           | Port      | Zweck      |
|----------------------------------------------------------------------------|-----------|----------|
| **`onegetcdn.azureedge.net, go.microsoft.com`**                            | **`HTTPS:443`** | Zum Installieren Windows-bezogener Binärdateien |
| **`*.mp.microsoft.com, www.msftconnecttest.com, ctldl.windowsupdate.com`** | **`HTTP:80`**   | Zum Installieren Windows-bezogener Binärdateien |

## <a name="aks-addons-and-integrations"></a>AKS-Add-Ons und -Integrationen

### <a name="azure-monitor-for-containers"></a>Azure Monitor für Container

Der Zugriff auf Azure Monitor für Container kann auf zwei Arten ermöglicht werden: Sie können das Azure Monitor-Diensttag ([ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags)) zulassen **oder** Zugriff auf die erforderlichen FQDNs/Anwendungsregeln gewähren.

#### <a name="required-network-rules"></a>Benötigte Netzwerkregeln

Die folgenden vollqualifizierten Domänennamen und Anwendungsregeln sind erforderlich:

| Zielendpunkt                                                             | Protokoll | Port    | Zweck  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureMonitor:443`**  | TCP           | 443      | Dieser Endpunkt dient zum Senden von Metrikdaten und Protokollen an Azure Monitor und Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Benötigte FQDNs/Anwendungsregeln

Die folgenden vollqualifizierten Domänennamen und Anwendungsregeln sind für AKS-Cluster mit aktiviertem Azure Monitor für Container erforderlich:

| FQDN                                    | Port      | Zweck      |
|-----------------------------------------|-----------|----------|
| dc.services.visualstudio.com | **`HTTPS:443`**    | Dieser Endpunkt wird für Metriken sowie zur Überwachung von Telemetriedaten mithilfe von Azure Monitor verwendet. |
| *.ods.opinsights.azure.com    | **`HTTPS:443`**    | Dieser Endpunkt wird von Azure Monitor zur Erfassung von Log Analytics-Daten verwendet. |
| *.oms.opinsights.azure.com | **`HTTPS:443`** | Dieser Endpunkt wird von „omsagent“ verwendet. „omsagent“ dient zur Authentifizierung des Log Analytics-Diensts. |
| *.monitoring.azure.com | **`HTTPS:443`** | Dieser Endpunkt wird zum Senden von Metrikdaten an Azure Monitor verwendet. |

### <a name="azure-dev-spaces"></a>Azure Dev Spaces

Aktualisieren Sie Ihre Firewall- oder Sicherheitskonfiguration, um Netzwerkdatenverkehr zu und von allen unten genannten FQDNs und [Azure Dev Spaces-Infrastrukturdiensten][dev-spaces-service-tags] zuzulassen.

#### <a name="required-network-rules"></a>Benötigte Netzwerkregeln

| Zielendpunkt                                                             | Protokoll | Port    | Zweck  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) -  **`AzureDevSpaces`**  | TCP           | 443      | Dieser Endpunkt dient zum Senden von Metrikdaten und Protokollen an Azure Monitor und Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Benötigte FQDNs/Anwendungsregeln 

Die folgenden vollqualifizierten Domänennamen und Anwendungsregeln sind für AKS-Cluster mit aktiviertem Azure Dev Spaces-Dienst erforderlich.

| FQDN                                    | Port      | Zweck      |
|-----------------------------------------|-----------|----------|
| `cloudflare.docker.com` | **`HTTPS:443`** | Diese Adresse wird verwendet, um Linux Alpine und andere Azure Dev Spaces-Images zu pullen. |
| `gcr.io` | **`HTTPS:443`** | Diese Adresse wird zum Abrufen von Helm/Tiller-Images verwendet. |
| `storage.googleapis.com` | **`HTTPS:443`** | Diese Adresse wird zum Abrufen von Helm/Tiller-Images verwendet. |


### <a name="azure-policy"></a>Azure Policy

#### <a name="required-fqdn--application-rules"></a>Benötigte FQDNs/Anwendungsregeln 

Die folgenden vollqualifizierten Domänennamen und Anwendungsregeln sind für AKS-Cluster mit aktiviertem Azure Policy erforderlich.

| FQDN                                          | Port      | Zweck      |
|-----------------------------------------------|-----------|----------|
| **`data.policy.core.windows.net`** | **`HTTPS:443`** | Diese Adresse wird verwendet, um die Kubernetes-Richtlinien abzurufen und den Konformitätsstatus des Clusters an den Richtliniendienst zu melden. |
| **`store.policy.core.windows.net`** | **`HTTPS:443`** | Diese Adresse wird zum Abrufen der Gatekeeper-Artefakte integrierter Richtlinien verwendet. |
| **`gov-prod-policy-data.trafficmanager.net`** | **`HTTPS:443`** | Diese Adresse wird für den ordnungsgemäßen Betrieb von Azure Policy verwendet.  |
| **`raw.githubusercontent.com`**               | **`HTTPS:443`** | Diese Adresse wird verwendet, um die integrierten Richtlinien aus GitHub abzurufen und so den ordnungsgemäßen Betrieb von Azure Policy sicherzustellen. |
| **`dc.services.visualstudio.com`**            | **`HTTPS:443`** | Das Azure Policy-Add-On, das Telemetriedaten an den Application Insights-Endpunkt sendet. |

## <a name="restrict-egress-traffic-using-azure-firewall"></a>Einschränken von ausgehendem Datenverkehr mithilfe von Azure Firewall

Azure Firewall bietet ein FQDN-Tag für Azure Kubernetes Service (`AzureKubernetesService`), um diese Konfiguration zu vereinfachen. 

> [!NOTE]
> Das FQDN-Tag enthält alle oben aufgeführten FQDNs und wird automatisch auf dem neuesten Stand gehalten.
>
> Sie sollten über mindestens 20 Front-End-IPs für die Azure Firewall-Instanz für Produktionsszenarios verfügen, um das Auftreten von Problemen mit der SNAT-Portauslastung zu vermeiden.

Im Anschluss finden Sie eine Beispielarchitektur der Bereitstellung:

![Gesperrte Topologie](media/limit-egress-traffic/aks-azure-firewall-egress.png)

* Öffentlicher eingehender Datenverkehr muss Firewallfilter durchlaufen.
  * AKS-Agent-Knoten werden in einem dedizierten Subnetz isoliert.
  * [Azure Firewall](../firewall/overview.md) wird in einem eigenen Subnetz bereitgestellt.
  * Durch eine DNAT-Regel wird die öffentliche Firewall-IP-Adresse in die Front-End-IP-Adresse des Lastenausgleichs übersetzt.
* Ausgehende Anforderungen werden von Agent-Knoten über eine [benutzerdefinierte Route](egress-outboundtype.md) an die interne IP-Adresse der Azure Firewall-Instanz geleitet.
  * Anforderungen von AKS-Agent-Knoten folgen einer benutzerdefinierten Route, die in dem Subnetz platziert wurde, in dem der AKS-Cluster bereitgestellt ist.
  * Azure Firewall leitet ausgehenden Datenverkehr über ein Front-End mit öffentlicher IP-Adresse aus dem virtuellen Netzwerk.
  * Der Zugriff auf das öffentliche Internet oder andere Azure-Dienste erfolgt sowohl für eingehenden als auch für ausgehenden Datenverkehr über die IP-Adresse des Firewall-Front-Ends.
  * Optional wird der Zugriff auf die AKS-Steuerungsebene durch [vom API-Server autorisierte IP-Adressbereiche](./api-server-authorized-ip-ranges.md) geschützt. Dies schließt die öffentliche Front-End-IP-Adresse für die Firewall mit ein.
* Interner Datenverkehr
  * Optional können Sie anstelle eines [öffentlichen Lastenausgleichs](load-balancer-standard.md) oder zusätzlich zu einem öffentlichen Lastenausgleich einen [internen Lastenausgleich](internal-lb.md) für internen Datenverkehr verwenden und diesen ebenfalls in einem eigenen Subnetz isolieren.


In den folgenden Schritten wird das FQDN-Tag `AzureKubernetesService` von Azure Firewall verwendet, um den ausgehenden Datenverkehr des AKS-Clusters einzuschränken und ein Beispiel für die Konfiguration von öffentlichem eingehendem Datenverkehr über die Firewall bereitzustellen.


### <a name="set-configuration-via-environment-variables"></a>Festlegen der Konfiguration mit Umgebungsvariablen

Definieren Sie eine Reihe von Umgebungsvariablen, die bei der Erstellung von Ressourcen verwendet werden können.

```bash
PREFIX="aks-egress"
RG="${PREFIX}-rg"
LOC="eastus"
PLUGIN=azure
AKSNAME="${PREFIX}"
VNET_NAME="${PREFIX}-vnet"
AKSSUBNET_NAME="aks-subnet"
# DO NOT CHANGE FWSUBNET_NAME - This is currently a requirement for Azure Firewall.
FWSUBNET_NAME="AzureFirewallSubnet"
FWNAME="${PREFIX}-fw"
FWPUBLICIP_NAME="${PREFIX}-fwpublicip"
FWIPCONFIG_NAME="${PREFIX}-fwconfig"
FWROUTE_TABLE_NAME="${PREFIX}-fwrt"
FWROUTE_NAME="${PREFIX}-fwrn"
FWROUTE_NAME_INTERNET="${PREFIX}-fwinternet"
```

### <a name="create-a-virtual-network-with-multiple-subnets"></a>Erstellen eines virtuellen Netzwerks mit mehreren Subnetzen

Stellen Sie ein virtuelles Netzwerk mit zwei separaten Subnetzen bereit: eins für den Cluster und eins für die Firewall. Optional kann auch noch ein Subnetz für internen eingehenden Dienstdatenverkehr erstellt werden.

![Leere Netzwerktopologie](media/limit-egress-traffic/empty-network.png)

Erstellen Sie eine Ressourcengruppe für alle Ressourcen.

```azurecli
# Create Resource Group

az group create --name $RG --location $LOC
```

Erstellen Sie ein virtuelles Netzwerk mit zwei Subnetzen zum Hosten des AKS-Clusters und der Azure Firewall-Instanz. Cluster und Firewall verfügen jeweils über ein eigenes Subnetz. Beginnen wir mit dem AKS-Netzwerk.

```
# Dedicated virtual network with AKS subnet

az network vnet create \
    --resource-group $RG \
    --name $VNET_NAME \
    --location $LOC \
    --address-prefixes 10.42.0.0/16 \
    --subnet-name $AKSSUBNET_NAME \
    --subnet-prefix 10.42.1.0/24

# Dedicated subnet for Azure Firewall (Firewall name cannot be changed)

az network vnet subnet create \
    --resource-group $RG \
    --vnet-name $VNET_NAME \
    --name $FWSUBNET_NAME \
    --address-prefix 10.42.2.0/24
```

### <a name="create-and-set-up-an-azure-firewall-with-a-udr"></a>Erstellen und Einrichten einer Azure Firewall-Instanz mit einer benutzerdefinierten Route

Für Azure Firewall müssen Eingangs- und Ausgangsregeln konfiguriert werden. Die Firewall dient hauptsächlich dazu, Organisationen das Konfigurieren von differenzierten Regeln für ein- und ausgehenden Datenverkehr in den und aus dem AKS-Cluster zu ermöglichen.

![Firewall und benutzerdefinierte Routen](media/limit-egress-traffic/firewall-udr.png)


> [!IMPORTANT]
> Wenn durch Ihren Cluster oder Ihre Anwendung eine große Anzahl von ausgehenden Verbindungen erstellt wird, die jeweils das gleiche Ziel oder eine kleine Teilmenge von Zielen haben, sind unter Umständen mehr Front-End-IP-Adressen für die Firewall erforderlich, um eine Überschreitung der Portkapazität pro Front-End-IP-Adresse zu vermeiden.
> Weitere Informationen zum Erstellen einer Azure Firewall-Instanz mit mehreren IP-Adressen finden Sie [**hier**](../firewall/quick-create-multiple-ip-template.md).

Erstellen Sie eine öffentliche IP-Adressressource mit einer Standard-SKU, die als Front-End-Adresse für Azure Firewall verwendet wird.

```azurecli
az network public-ip create -g $RG -n $FWPUBLICIP_NAME -l $LOC --sku "Standard"
```

Registrieren Sie die Azure CLI Preview-Erweiterung, um eine Azure Firewall-Instanz zu erstellen.
```azurecli
# Install Azure Firewall preview CLI extension

az extension add --name azure-firewall

# Deploy Azure Firewall

az network firewall create -g $RG -n $FWNAME -l $LOC --enable-dns-proxy true
```
Die erstellte IP-Adresse kann jetzt dem Firewall-Front-End zugewiesen werden.

> [!NOTE]
> Das Einrichten der öffentlichen IP-Adresse für die Azure Firewall-Instanz kann einige Minuten dauern.
> Um die FQDN-Nutzung in Netzwerkregeln zu ermöglichen, muss der DNS-Proxy aktiviert werden. Bei aktiviertem DNS-Proxy lauscht die Firewall am Port 53 und leitet DNS-Anforderungen an den oben angegebenen DNS-Server weiter. Dadurch kann der FQDN von der Firewall automatisch übersetzt werden.

```azurecli
# Configure Firewall IP Config

az network firewall ip-config create -g $RG -f $FWNAME -n $FWIPCONFIG_NAME --public-ip-address $FWPUBLICIP_NAME --vnet-name $VNET_NAME
```

Wenn der oben stehende Befehl erfolgreich ausgeführt wurde, speichern Sie die IP-Adresse des Firewall-Front-Ends zur späteren Konfiguration.

```bash
# Capture Firewall IP Address for Later Use

FWPUBLIC_IP=$(az network public-ip show -g $RG -n $FWPUBLICIP_NAME --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RG -n $FWNAME --query "ipConfigurations[0].privateIpAddress" -o tsv)
```

> [!NOTE]
> Wenn Sie den sicheren Zugriff auf den AKS-API-Server mit [autorisierten IP-Adressbereichen](./api-server-authorized-ip-ranges.md) verwenden, müssen Sie die öffentliche IP-Adresse der Firewall zum autorisierten IP-Adressbereich hinzufügen.

### <a name="create-a-udr-with-a-hop-to-azure-firewall"></a>Erstellen einer benutzerdefinierten Route mit einem Hop zu Azure Firewall

Azure führt für Datenverkehr automatisch das Routing zwischen Azure-Subnetzen, virtuellen Netzwerken und lokalen Netzwerken durch. Wenn Sie das Standardrouting von Azure ändern möchten, erstellen Sie eine Routingtabelle.

Erstellen Sie eine leere Routingtabelle, die einem bestimmten Subnetz zugeordnet werden kann. Die Routingtabelle definiert die oben erstellte Azure Firewall-Instanz als nächsten Hop. Jedem Subnetz können null oder mehr Routentabellen zugeordnet sein.

```azurecli
# Create UDR and add a route for Azure Firewall

az network route-table create -g $RG -l $LOC --name $FWROUTE_TABLE_NAME
az network route-table route create -g $RG --name $FWROUTE_NAME --route-table-name $FWROUTE_TABLE_NAME --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP --subscription $SUBID
az network route-table route create -g $RG --name $FWROUTE_NAME_INTERNET --route-table-name $FWROUTE_TABLE_NAME --address-prefix $FWPUBLIC_IP/32 --next-hop-type Internet
```

Informationen zum Außerkraftsetzen der Standardsystemrouten von Azure sowie zum Hinzufügen zusätzlicher Routen zur Routingtabelle eines Subnetzes finden Sie in der [Dokumentation zu Routingtabellen für virtuelle Netzwerke](../virtual-network/virtual-networks-udr-overview.md#user-defined).

### <a name="adding-firewall-rules"></a>Hinzufügen von Firewallregeln

Im Anschluss finden Sie drei Netzwerkregeln, mit denen Sie Ihre Firewall konfigurieren können. Die Regeln müssen allerdings ggf. an Ihre Bereitstellung angepasst werden. Mit der ersten Regel wird der Zugriff auf den Port 9000 über TCP zugelassen. Mit der zweiten Regel wird der Zugriff auf die Ports 1194 und 123 über UDP zugelassen. (Bei Bereitstellungen für Azure China 21ViaNet sind möglicherweise [weitere](#azure-china-21vianet-required-network-rules) erforderlich.) Durch beide Regeln wird jeweils nur Datenverkehr zugelassen, der für das klassenlose domänenübergreifende Routing der verwendeten Azure-Region (in diesem Fall „USA, Osten“) bestimmt ist. Abschließend wird eine dritte Netzwerkregel hinzugefügt, um den Port 123 für den FQDN `ntp.ubuntu.com` über UDP zu öffnen. (Das Hinzufügen eines FQDN als Netzwerkregel ist eines der spezifischen Features von Azure Firewall und muss bei Verwendung eigener Optionen angepasst werden.)

Nach dem Festlegen der Netzwerkregeln wird außerdem eine Anwendungsregel mit `AzureKubernetesService` hinzugefügt, um alle benötigten FQDNs abzudecken, auf die über den TCP-Port 443 und den Port 80 zugegriffen werden kann.

```
# Add FW Network Rules

az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apiudp' --protocols 'UDP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 1194 --action allow --priority 100
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'apitcp' --protocols 'TCP' --source-addresses '*' --destination-addresses "AzureCloud.$LOC" --destination-ports 9000
az network firewall network-rule create -g $RG -f $FWNAME --collection-name 'aksfwnr' -n 'time' --protocols 'UDP' --source-addresses '*' --destination-fqdns 'ntp.ubuntu.com' --destination-ports 123

# Add FW Application Rules

az network firewall application-rule create -g $RG -f $FWNAME --collection-name 'aksfwar' -n 'fqdn' --source-addresses '*' --protocols 'http=80' 'https=443' --fqdn-tags "AzureKubernetesService" --action allow --priority 100
```

Weitere Informationen zum Azure Firewall-Dienst finden Sie in der [Azure Firewall-Dokumentation](../firewall/overview.md).

### <a name="associate-the-route-table-to-aks"></a>Zuordnen der Routingtabelle zu AKS

Um den Cluster der Firewall zuzuordnen, muss das dedizierte Subnetz für das Subnetz des Clusters auf die oben erstellte Routingtabelle verweisen. Zur Zuordnung geben Sie einen Befehl zum Aktualisieren der Routingtabelle des Clustersubnetzes an das virtuelle Netzwerk aus, in dem sich sowohl der Cluster als auch die Firewall befinden.

```azurecli
# Associate route table with next hop to Firewall to the AKS subnet

az network vnet subnet update -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --route-table $FWROUTE_TABLE_NAME
```

### <a name="deploy-aks-with-outbound-type-of-udr-to-the-existing-network"></a>Bereitstellen von AKS mit dem ausgehenden Typ „UDR“ im vorhandenen Netzwerk

Jetzt kann ein AKS-Cluster im vorhandenen virtuellen Netzwerk bereitgestellt werden. Darüber hinaus wird der [ausgehende Typ `userDefinedRouting`](egress-outboundtype.md) verwendet. Durch dieses Feature wird sichergestellt, dass ausgehender Datenverkehr die Firewall durchlaufen muss und keine anderen Ausgangspfade vorhanden sind. (Standardmäßig kann der ausgehende Typ „Lastenausgleich“ verwendet werden.)

![aks-deploy](media/limit-egress-traffic/aks-udr-fw.png)

### <a name="create-a-service-principal-with-access-to-provision-inside-the-existing-virtual-network"></a>Erstellen eines Dienstprinzipals mit Bereitstellungszugriff im vorhandenen virtuellen Netzwerk

AKS verwendet einen Dienstprinzipal zum Erstellen von Clusterressourcen. Mit dem zur Erstellungszeit übergebenen Dienstprinzipal werden zugrunde liegende AKS-Ressourcen wie Speicherressourcen, IP-Adressen und Lastenausgleichsressourcen erstellt, die von AKS verwendet werden. (Alternativ kann auch eine [verwaltete Identität](use-managed-identity.md) verwendet werden.) Ohne Erteilung der entsprechenden Berechtigungen kann der AKS-Cluster nicht bereitgestellt werden.

```azurecli
# Create SP and Assign Permission to Virtual Network

az ad sp create-for-rbac -n "${PREFIX}sp" --skip-assignment
```

Ersetzen Sie im folgenden Code `APPID` und `PASSWORD` durch die App-ID und das Kennwort für den Dienstprinzipal, die bzw. das von der vorherigen Befehlsausgabe automatisch generiert wurde. Wir verweisen auf die VNET-Ressourcen-ID, um dem Dienstprinzipal die erforderlichen Berechtigungen zu erteilen, damit von AKS Ressourcen in diesem VNET bereitgestellt werden können.

```azurecli
APPID="<SERVICE_PRINCIPAL_APPID_GOES_HERE>"
PASSWORD="<SERVICEPRINCIPAL_PASSWORD_GOES_HERE>"
VNETID=$(az network vnet show -g $RG --name $VNET_NAME --query id -o tsv)

# Assign SP Permission to VNET

az role assignment create --assignee $APPID --scope $VNETID --role "Network Contributor"
```

Ausführliche Informationen zu den erforderlichen Berechtigungen finden Sie [hier](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).

> [!NOTE]
> Bei Verwendung des kubenet-Netzwerk-Plug-Ins müssen dem AKS-Dienstprinzipal oder der verwalteten Identität Berechtigung für die vorab erstellte Routingtabelle erteilt werden, da kubenet eine Routingtabelle benötigt, um erforderliche Routingregeln hinzuzufügen. 
> ```azurecli-interactive
> RTID=$(az network route-table show -g $RG -n $FWROUTE_TABLE_NAME --query id -o tsv)
> az role assignment create --assignee $APPID --scope $RTID --role "Network Contributor"
> ```

### <a name="deploy-aks"></a>Bereitstellen von AKS

Abschließend kann der AKS-Cluster in dem vorhandenen Subnetz bereitgestellt werden, das speziell für den Cluster reserviert wurde. Das Zielsubnetz, in dem die Bereitstellung erfolgen soll, wird mit der Umgebungsvariable `$SUBNETID` definiert. In den vorherigen Schritten wurde die `$SUBNETID`-Variable nicht definiert. Zum Festlegen des Werts für die Subnetz-ID können Sie den folgenden Befehl verwenden:

```azurecli
SUBNETID=$(az network vnet subnet show -g $RG --vnet-name $VNET_NAME --name $AKSSUBNET_NAME --query id -o tsv)
```

Sie definieren den ausgehenden Typ für die Verwendung der benutzerdefinierten Route, die bereits im Subnetz vorhanden ist. Diese Konfiguration ermöglicht es AKS, die Einrichtung und die IP-Adressbereitstellung für den Lastenausgleich zu überspringen.

> [!IMPORTANT]
> Weitere Informationen zum ausgehenden Typ „UDR“ sowie zu den damit verbundenen Einschränkungen finden Sie unter [**Einschränkungen**](egress-outboundtype.md#limitations).


> [!TIP]
> Der Clusterbereitstellung können weitere Features hinzugefügt werden – beispielsweise [**private Cluster**](private-clusters.md). 
>
> Das AKS-Feature für [**vom API-Server autorisierte IP-Adressbereiche**](api-server-authorized-ip-ranges.md) kann hinzugefügt werden, um den Zugriff des API-Servers auf den öffentlichen Endpunkt der Firewall zu beschränken. Das Feature für autorisierte IP-Adressbereiche ist im Diagramm als optional gekennzeichnet. Wenn Sie das Feature für autorisierte IP-Adressbereiche verwenden, um den API-Serverzugriff einzuschränken, müssen Ihre Entwicklertools eine Jumpbox für das virtuelle Netzwerk der Firewall verwenden, oder Sie müssen alle Entwicklerendpunkte zum autorisierten IP-Adressbereich hinzufügen.

```azurecli
az aks create -g $RG -n $AKSNAME -l $LOC \
  --node-count 3 --generate-ssh-keys \
  --network-plugin $PLUGIN \
  --outbound-type userDefinedRouting \
  --service-cidr 10.41.0.0/16 \
  --dns-service-ip 10.41.0.10 \
  --docker-bridge-address 172.17.0.1/16 \
  --vnet-subnet-id $SUBNETID \
  --service-principal $APPID \
  --client-secret $PASSWORD \
  --api-server-authorized-ip-ranges $FWPUBLIC_IP
```

### <a name="enable-developer-access-to-the-api-server"></a>Ermöglichen des Entwicklerzugriffs auf den API-Server

Falls Sie im vorherigen Schritt autorisierte IP-Adressbereiche für den Cluster verwendet haben, müssen Sie die IP-Adressen Ihrer Entwicklertools der AKS-Clusterliste mit den genehmigten IP-Adressbereichen hinzufügen, um von dort aus auf den API-Server zugreifen zu können. Eine andere Möglichkeit besteht darin, in einem separaten Subnetz innerhalb des virtuellen Netzwerks von Azure Firewall eine Jumpbox mit den erforderlichen Tools zu konfigurieren.

Mit dem folgenden Befehl fügen Sie dem Bereich der genehmigten IP-Adressen eine weitere Adresse hinzu.

```bash
# Retrieve your IP address
CURRENT_IP=$(dig @resolver1.opendns.com ANY myip.opendns.com +short)

# Add to AKS approved list
az aks update -g $RG -n $AKSNAME --api-server-authorized-ip-ranges $CURRENT_IP/32

```

 Mit dem Befehl [az aks get-credentials][az-aks-get-credentials] können Sie `kubectl` für die Verbindungsherstellung mit Ihrem neu erstellten Kubernetes-Cluster konfigurieren. 

 ```azurecli
 az aks get-credentials -g $RG -n $AKSNAME
 ```

### <a name="deploy-a-public-service"></a>Bereitstellen eines öffentlichen Diensts
Nun können Sie damit beginnen, Dienste verfügbar zu machen und Anwendungen in diesem Cluster bereitzustellen. In diesem Beispiel wird ein öffentlicher Dienst verfügbar gemacht. Sie können aber auch einen internen Dienst über den [internen Lastenausgleich](internal-lb.md) verfügbar machen.

![DNAT für öffentlichen Dienst](media/limit-egress-traffic/aks-create-svc.png)

Stellen Sie die Azure-Abstimmungs-App bereit, indem Sie den folgenden YAML-Code in eine Datei namens `example.yaml` kopieren.

```yaml
# voting-storage-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-storage
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-storage
  template:
    metadata:
      labels:
        app: voting-storage
    spec:
      containers:
      - name: voting-storage
        image: mcr.microsoft.com/aks/samples/voting/storage:2.0
        args: ["--ignore-db-dir=lost+found"]
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_ROOT_PASSWORD
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
      volumes:
      - name: mysql-persistent-storage
        persistentVolumeClaim:
          claimName: mysql-pv-claim
---
# voting-storage-secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: voting-storage-secret
type: Opaque
data:
  MYSQL_USER: ZGJ1c2Vy
  MYSQL_PASSWORD: UGFzc3dvcmQxMg==
  MYSQL_DATABASE: YXp1cmV2b3Rl
  MYSQL_ROOT_PASSWORD: UGFzc3dvcmQxMg==
---
# voting-storage-pv-claim.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pv-claim
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
---
# voting-storage-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-storage
  labels: 
    app: voting-storage
spec:
  ports:
  - port: 3306
    name: mysql
  selector:
    app: voting-storage
---
# voting-app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-app
  template:
    metadata:
      labels:
        app: voting-app
    spec:
      containers:
      - name: voting-app
        image: mcr.microsoft.com/aks/samples/voting/app:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
        - name: ANALYTICS_HOST
          value: "voting-analytics"
---
# voting-app-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-app
  labels: 
    app: voting-app
spec:
  type: LoadBalancer
  ports:
  - port: 80
    targetPort: 8080
    name: http
  selector:
    app: voting-app
---
# voting-analytics-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: voting-analytics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: voting-analytics
      version: "2.0"
  template:
    metadata:
      labels:
        app: voting-analytics
        version: "2.0"
    spec:
      containers:
      - name: voting-analytics
        image: mcr.microsoft.com/aks/samples/voting/analytics:2.0
        imagePullPolicy: Always
        ports:
        - containerPort: 8080
          name: http
        env:
        - name: MYSQL_HOST
          value: "voting-storage"
        - name: MYSQL_USER
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_USER
        - name: MYSQL_PASSWORD
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_PASSWORD
        - name: MYSQL_DATABASE
          valueFrom:
            secretKeyRef:
              name: voting-storage-secret
              key: MYSQL_DATABASE
---
# voting-analytics-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: voting-analytics
  labels: 
    app: voting-analytics
spec:
  ports:
  - port: 8080
    name: http
  selector:
    app: voting-analytics
```

Stellen Sie den Dienst bereit, indem Sie Folgendes ausführen:

```bash
kubectl apply -f example.yaml
```

### <a name="add-a-dnat-rule-to-azure-firewall"></a>Hinzufügen einer DNAT-Regel zu Azure Firewall

> [!IMPORTANT]
> Wenn Sie Azure Firewall verwenden, um ausgehenden Datenverkehr einzuschränken, und eine benutzerdefinierte Route (UDR) erstellen, um den gesamten ausgehenden Datenverkehr zu erzwingen, stellen Sie sicher, dass Sie in der Firewall eine entsprechende DNAT-Regel erstellen, um ausgehenden Datenverkehr zuzulassen. Durch die Verwendung von Azure Firewall mit einer UDR wird das Setup für eingehenden Datenverkehr aufgrund von asymmetrischem Routing unterbrochen. (Das Problem tritt auf, wenn das AKS-Subnetz über eine Standardroute verfügt, die zur privaten IP-Adresse der Firewall wechselt, Sie aber einen öffentlichen Load Balancer verwenden – Eingangs- oder Kubernetes-Dienst vom Typ LoadBalancer). In diesem Fall wird der eingehende Load Balancer-Datenverkehr über die öffentliche IP-Adresse empfangen, während der Rückpfad über die private IP-Adresse der Firewall verläuft. Die Firewall löscht das zurückgegebene Paket, weil sie zustandsbehaftet ist und nichts über eine vorhandene Sitzung weiß. Informationen zur Integration von Azure Firewall in ihren Eingangs- oder Service Dienst-Load Balancer finden Sie unter [Integrieren von Azure Firewall mit Azure Load Balancer Standard](../firewall/integrate-lb.md).


Um die eingehende Konnektivität zu konfigurieren, muss eine DNAT-Regel für die Azure Firewall-Instanz geschrieben werden. Zum Testen der Konnektivität mit dem Cluster wird eine Regel definiert, durch die die öffentliche IP-Adresse des Firewall-Front-Ends an die interne IP-Adresse weitergeleitet wird, die durch den internen Dienst verfügbar gemacht wird.

Die Zieladresse kann angepasst werden, da es sich um den Port in der Firewall handelt, auf den zugegriffen werden soll. Die übersetzte Adresse muss die IP-Adresse des internen Lastenausgleichs sein. Der übersetzte Port muss der verfügbar gemachte Port für Ihren Kubernetes-Dienst sein.

Sie müssen die interne IP-Adresse angeben, die der vom Kubernetes-Dienst erstellten Lastenausgleichsressource zugewiesen wurde. Rufen Sie die Adresse ab, indem Sie Folgendes ausführen:

```bash
kubectl get services
```

Die erforderliche IP-Adresse wird in der Spalte „EXTERNAL-IP“ aufgeführt, ähnlich wie im folgenden Beispiel.

```bash
NAME               TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
kubernetes         ClusterIP      10.41.0.1       <none>        443/TCP        10h
voting-analytics   ClusterIP      10.41.88.129    <none>        8080/TCP       9m
voting-app         LoadBalancer   10.41.185.82    20.39.18.6    80:32718/TCP   9m
voting-storage     ClusterIP      10.41.221.201   <none>        3306/TCP       9m
```

Führen Sie den folgenden Befehl aus, um die IP-Adresse des Diensts abzurufen:
```bash
SERVICE_IP=$(k get svc voting-app -o jsonpath='{.status.loadBalancer.ingress[*].ip}')
```

Führen Sie den folgenden Befehl aus, um die NAT-Regel hinzuzufügen:
```azurecli
az network firewall nat-rule create --collection-name exampleset --destination-addresses $FWPUBLIC_IP --destination-ports 80 --firewall-name $FWNAME --name inboundrule --protocols Any --resource-group $RG --source-addresses '*' --translated-port 80 --action Dnat --priority 100 --translated-address $SERVICE_IP
```

### <a name="validate-connectivity"></a>Überprüfen der Konnektivität

Navigieren Sie in einem Browser zur IP-Adresse des Azure Firewall-Front-Ends, um die Konnektivität zu überprüfen.

Die AKS-Abstimmungs-App sollte angezeigt werden. In diesem Beispiel lautete die öffentliche IP-Adresse der Firewall `52.253.228.132`.


![Screenshot, der die A K S-Abstimmungs-App mit Schaltflächen für Katzen, Hunde und das Zurücksetzen sowie Summen zeigt.](media/limit-egress-traffic/aks-vote.png)


### <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Um die Azure-Ressourcen zu bereinigen, löschen Sie die AKS-Ressourcengruppe.

```azurecli
az group delete -g $RG
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, welche Ports und Adressen zugelassen werden müssen, wenn Sie den ausgehenden Datenverkehr für den Cluster einschränken möchten. Außerdem haben Sie gelernt, wie Sie Ihren ausgehenden Datenverkehr mithilfe von Azure Firewall schützen können. 

Die obigen Schritte lassen sich bei Bedarf generalisieren, um den Datenverkehr an Ihre bevorzugte Lösung für ausgehenden Datenverkehr weiterzuleiten, wie in der [Dokumentation für den ausgehenden Typ `userDefinedRoute`](egress-outboundtype.md) beschrieben.

Informationen zur Einschränkung der Kommunikation zwischen Pods sowie zu Ost-West-Datenverkehrseinschränkungen innerhalb des Clusters finden Sie unter [Sicherer Datenverkehr zwischen Pods durch Netzwerkrichtlinien in Azure Kubernetes Service (AKS)][network-policy].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[dev-spaces-service-tags]: ../dev-spaces/configure-networking.md#virtual-network-or-subnet-configurations
