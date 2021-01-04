---
title: Beheben von Netzwerkproblemen mit der Registrierung
description: Enthält eine Beschreibung der Symptome, Ursachen und Lösungen häufiger Probleme, die beim Zugreifen auf eine Azure-Containerregistrierung in einem virtuellen Netzwerk oder hinter einer Firewall auftreten.
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 95b32b839d1b3b804a2035b797e1146a09d5236a
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351792"
---
# <a name="troubleshoot-network-issues-with-registry"></a>Beheben von Netzwerkproblemen mit der Registrierung

Dieser Artikel enthält hilfreiche Informationen zum Beheben von Problemen, die beim Zugreifen auf eine Azure-Containerregistrierung in einem virtuellen Netzwerk oder hinter einer Firewall auftreten können. 

## <a name="symptoms"></a>Symptome

Beispiele für Symptome sind:

* Images können nicht gepusht oder gepullt werden, und Sie erhalten den Fehler `dial tcp: lookup myregistry.azurecr.io`.
* Images können nicht gepusht oder gepullt werden, und Sie erhalten den Azure CLI-Fehler `Could not connect to the registry login server`.
* Images können nicht aus der Registrierung in Azure Kubernetes Service oder einen anderen Azure-Dienst gepullt werden.
* Auf eine Registrierung hinter einem HTTPS-Proxy kann nicht zugegriffen werden, und Sie erhalten den Fehler `Error response from daemon: login attempt failed with status: 403 Forbidden`.
* Die Einstellungen für das virtuelle Netzwerk können nicht konfiguriert werden, und die Fehlermeldung `Failed to save firewall and virtual network settings for container registry` wird angezeigt.
* Das Zugreifen auf oder das Anzeigen von Registrierungseinstellungen im Azure-Portal oder das Verwalten der Registrierung mithilfe der Azure CLI ist nicht möglich.
* Einstellungen für das virtuelle Netzwerk oder Regeln für den öffentlichen Zugriff können nicht hinzugefügt oder geändert werden.
* Mit ACR Tasks können keine Images gepusht oder gepullt werden.
* Mit Azure Security Center können keine Images in der Registrierung gescannt werden, oder die Scanergebnisse werden in Azure Security Center nicht angezeigt.

## <a name="causes"></a>Ursachen

* Eine Clientfirewall oder ein Proxy verhindert den Zugriff ([Lösung](#configure-client-firewall-access)).
* Regeln für den Zugriff auf das öffentliche Netzwerk in der Registrierung verhindern den Zugriff ([Lösung](#configure-public-access-to-registry)).
* Die Konfiguration des virtuellen Netzwerks verhindert den Zugriff ([Lösung](#configure-vnet-access)).
* Sie versuchen, Azure Security Center oder bestimmte andere Azure-Dienste in eine Registrierung zu integrieren, die über einen privaten Endpunkt, Dienstendpunkt oder Zugriffsregeln für öffentliche IP-Adressen verfügt ([Lösung](#configure-service-access)).

## <a name="further-diagnosis"></a>Weitere Diagnose 

Führen Sie den Befehl [az acr check-health](/cli/azure/acr#az-acr-check-health) aus, um weitere Informationen zur Integrität der Registrierungsumgebung abzurufen und optional Zugriff auf eine Zielregistrierung zu erhalten. Diagnostizieren Sie beispielsweise bestimmte Probleme mit der Netzwerkkonnektivität oder -konfiguration. 

Befehlsbeispiele finden Sie unter [Überprüfen der Integrität einer Azure-Containerregistrierung](container-registry-check-health.md). Wenn Fehler gemeldet werden, sollten Sie die [Fehlerreferenz](container-registry-health-error-reference.md) und die folgenden Abschnitte auf empfohlene Lösungen überprüfen.

> [!NOTE]
> Einige Symptome in Bezug auf die Netzwerkkonnektivität können auch auftreten, wenn es Probleme mit der Authentifizierung oder Autorisierung der Registrierung gibt. Weitere Informationen finden Sie unter [Beheben von Problemen mit der Registrierungsanmeldung](container-registry-troubleshoot-login.md).

## <a name="potential-solutions"></a>Mögliche Lösungen

### <a name="configure-client-firewall-access"></a>Konfigurieren des Zugriffs über die Clientfirewall

Sie müssen die Firewallregeln für den Zugriff auf die öffentlichen REST- und Datenendpunkte der Registrierung konfigurieren, um den Zugriff auf eine Registrierung von einem Ort hinter einer Clientfirewall oder einem Proxyserver zu ermöglichen. Wenn [dedizierte Datenendpunkte](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) aktiviert sind, benötigen Sie Regeln für den Zugriff auf die folgenden Komponenten:

* REST-Endpunkt: `<registryname>.azurecr.io`
* Datenendpunkt(e): `<registry-name>.<region>.data.azurecr.io`

Konfigurieren Sie für eine Registrierung mit Georeplikation den Zugriff auf den Datenendpunkt für jedes regionale Replikat.

Stellen Sie hinter einem HTTPS-Proxy sicher, dass sowohl Ihr Docker-Client als auch Ihr Docker-Daemon für das Proxyverhalten konfiguriert sind.

Registrierungsressourcenprotokolle in der Tabelle „ContainerRegistryLoginEvents“ können als Hilfe beim Diagnostizieren einer blockierten Verbindungsherstellung dienen.

Verwandte Links:

* [Konfigurieren von Regeln für den Zugriff auf eine Azure-Containerregistrierung von einem Standort hinter einer Firewall](container-registry-firewall-access-rules.md)
* [HTTP/HTTPS-Proxykonfiguration](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Georeplikation in Azure Container Registry](container-registry-geo-replication.md)
* [Azure Container Registry-Protokolle für die Diagnoseauswertung und -überwachung](container-registry-diagnostics-audit-logs.md)

### <a name="configure-public-access-to-registry"></a>Konfigurieren des öffentlichen Zugriffs auf die Registrierung

Wenn Sie über das Internet auf eine Registrierung zugreifen, sollten Sie sich vergewissern, dass für die Registrierung der Zugriff auf das öffentliche Netzwerk über Ihren Client zulässig ist. Standardmäßig ist für eine Azure Container Registry-Instanz der Zugriff auf die öffentlichen Registrierungsendpunkte aus allen Netzwerken zugelassen. Eine Registrierung kann den Zugriff auf ausgewählte Netzwerke oder IP-Adressen beschränken. 

Falls die Registrierung für ein virtuelles Netzwerk mit einem Dienstendpunkt konfiguriert ist, wird mit dem Deaktivieren des öffentlichen Netzwerkzugriffs auch der Zugriff über den Dienstendpunkt deaktiviert. Wenn Ihre Registrierung für ein virtuelles Netzwerk mit Private Link konfiguriert ist, gelten IP-Netzwerkregeln nicht für die privaten Endpunkte der Registrierung. 

Verwandte Links:

* [Konfigurieren von Netzwerkregeln für öffentliche IP-Adressen](container-registry-access-selected-networks.md)
* [Herstellen einer privaten Verbindung mit einer Azure-Containerregistrierung über Azure Private Link](container-registry-private-link.md)
* [Beschränken des Zugriffs auf eine Containerregistrierung mithilfe eines Dienstendpunkts in einem virtuellen Azure-Netzwerk](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>Konfigurieren des VNET-Zugriffs

Vergewissern Sie sich, dass für das virtuelle Netzwerk entweder ein privater Endpunkt für Private Link oder ein Dienstendpunkt (Vorschau) konfiguriert ist. Ein Azure Bastion-Endpunkt wird derzeit nicht unterstützt.

Überprüfen Sie die NSG-Regeln und die Diensttags, mit denen der Datenverkehr von anderen Ressourcen im Netzwerk zur Registrierung eingeschränkt wird. 

Wenn ein Dienstendpunkt für die Registrierung konfiguriert ist, sollten Sie sich vergewissern, dass der Registrierung eine Netzwerkregel hinzugefügt wird, die den Zugriff über dieses Netzwerksubnetz zulässt. Der Dienstendpunkt unterstützt nur den Zugriff über virtuelle Computer und AKS-Cluster im Netzwerk.

Wenn Sie den Registrierungszugriff mithilfe eines virtuellen Netzwerks in einem anderen Azure-Abonnement einschränken möchten, stellen Sie sicher, dass Sie den Ressourcenanbieter `Microsoft.ContainerRegistry` in diesem Abonnement registrieren. [Registrieren Sie den Ressourcenanbieter](../azure-resource-manager/management/resource-providers-and-types.md) für Azure Container Registry über das Azure-Portal, die Azure CLI oder andere Azure-Tools.

Wenn im Netzwerk Azure Firewall oder eine ähnliche Lösung konfiguriert ist, sollten Sie überprüfen, ob der ausgehende Datenverkehr von anderen Ressourcen, z. B. einem AKS-Cluster, möglich ist, damit die Registrierungsendpunkte erreicht werden können.

Wenn ein privater Endpunkt konfiguriert ist, vergewissern Sie sich, dass das DNS den öffentlichen vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) der Registrierung (z. B. *myregistry.azurecr.io*) in die private IP-Adresse der Registrierung auflöst. Verwenden Sie für das DNS-Lookup ein Netzwerkhilfsprogramm, z. B. `dig` oder `nslookup`.

Verwandte Links:

* [Herstellen einer privaten Verbindung mit einer Azure-Containerregistrierung über Azure Private Link](container-registry-private-link.md)
* [Beschränken des Zugriffs auf eine Containerregistrierung mithilfe eines Dienstendpunkts in einem virtuellen Azure-Netzwerk](container-registry-vnet.md)
* [Erforderliche Netzwerkregeln für ausgehenden Datenverkehr und FQDNs für AKS-Cluster](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes: Debuggen der DNS-Auflösung](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [Diensttags für virtuelle Netzwerke](../virtual-network/service-tags-overview.md)

### <a name="configure-service-access"></a>Konfigurieren des Dienstzugriffs

Azure Security Center kann derzeit keine [Überprüfung von Images auf Sicherheitsrisiken](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json) in einer Registrierung durchführen, die den Zugriff auf private Endpunkte, ausgewählte Subnetze oder IP-Adressen beschränkt. Außerdem können Ressourcen der folgenden Dienste nicht auf Containerregistrierungen mit Netzwerkbeschränkungen zugreifen:

* Azure DevOps Services 
* Azure Container Instances
* Azure Container Registry Tasks

Wenn Zugriff oder eine Integration dieser Azure-Dienste mit Ihrer Containerregistrierung erforderlich ist, entfernen Sie die Netzwerkbeschränkung. Entfernen Sie z. B. die privaten Endpunkte der Registrierung, oder entfernen oder ändern Sie die Regeln der Registrierung für öffentlichen Zugriff.

Verwandte Links:

* [Azure Container Registry-Imageprüfung durch Security Center](../security-center/defender-for-container-registries-introduction.md)
* [Feedback](https://feedback.azure.com/forums/347535-azure-security-center/suggestions/41091577-enable-vulnerability-scanning-for-images-that-are)
* [Konfigurieren von Netzwerkregeln für öffentliche IP-Adressen](container-registry-access-selected-networks.md)
* [Herstellen einer privaten Verbindung mit einer Azure-Containerregistrierung über Azure Private Link](container-registry-private-link.md)


## <a name="advanced-troubleshooting"></a>Erweiterte Problembehandlung

Wenn die [Sammlung mit Ressourcenprotokollen](container-registry-diagnostics-audit-logs.md) in der Registrierung aktiviert ist, sollten Sie das Protokoll „ContainterRegistryLoginEvents“ überprüfen. In diesem Protokoll werden Authentifizierungsereignisse und der zugehörige Status gespeichert, z. B. eingehende Identität und IP-Adresse. Fragen Sie das Protokoll nach [Authentifizierungsfehlern für die Registrierung](container-registry-diagnostics-audit-logs.md#registry-authentication-failures) ab. 

Verwandte Links:

* [Protokolle für die Diagnoseauswertung und -überwachung](container-registry-diagnostics-audit-logs.md)
* [Häufig gestellte Fragen (FAQ) zur Containerregistrierung](container-registry-faq.md)
* [Azure-Sicherheitsbaseline für Azure Container Registry](security-baseline.md)
* [Bewährte Methoden für Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die folgenden Optionen an, falls Sie Ihr Problem hier nicht lösen können.

* Weitere Themen zur Problembehandlung für die Registrierung sind:
  * [Beheben von Problemen mit der Registrierungsanmeldung](container-registry-troubleshoot-login.md) 
  * [Beheben von Problemen mit der Registrierungsleistung](container-registry-troubleshoot-performance.md)
* [Optionen für Support durch die Community](https://azure.microsoft.com/support/community/)
* [Microsoft Q&A (Fragen und Antworten)](/answers/products/)
* [Öffnen eines Supporttickets](https://azure.microsoft.com/support/create-ticket/)