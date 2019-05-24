---
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: f903828285b0d4fdc8fbd932fa7c85056e937481
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66148837"
---
# <a name="deprecated-container-service-frequently-asked-questions"></a>(VERALTET) Häufig gestellte Fragen zu Container Service

[!INCLUDE [ACS deprecation](container-service-deprecation.md)]

## <a name="orchestrators"></a>Orchestratoren

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>Welche Containerorchestratoren werden für Azure Container Service unterstützt? 

Unterstützt werden DC/OS (Open Source), Docker Swarm und Kubernetes. Weitere Informationen finden Sie in der [Übersicht](../articles/container-service/kubernetes/container-service-intro-kubernetes.md).
 
### <a name="do-you-support-docker-swarm-mode"></a>Wird der Docker Swarm-Modus unterstützt? 

Der Swarm-Modus wird nicht unterstützt, die Unterstützung ist aber bereits in Planung. 

### <a name="does-azure-container-service-support-windows-containers"></a>Unterstützt Azure Container Service Windows-Container?  

Derzeit werden Linux-Container für alle Orchestratoren unterstützt. Die Unterstützung für Windows-Container mit Kubernetes befindet sich in der Vorschauphase.

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>Wird ein bestimmter Orchestrator in Azure Container Service empfohlen? 
Wir empfehlen im Allgemeinen keinen bestimmten Orchestrator. Falls Sie bereits mit einem der unterstützten Orchestratoren vertraut sind, können Sie sich Ihre Erfahrung in Azure Container Service zunutze machen. Datentrends legen jedoch nahe, dass DC/OS sich in der Produktion für Big Data- und IoT-Workloads bewährt hat, Kubernetes sich für native Cloudworkloads eignet und Docker Swarm sich durch seine Integration in Docker-Tools und seine einfache Lernkurve auszeichnet.

Abhängig von Ihrem Szenario können Sie auch benutzerdefinierte Containerlösungen mit anderen Azure-Diensten erstellen und verwalten. Zu diesen Diensten zählen [Virtual Machines](../articles/virtual-machines/linux/overview.md), [Service Fabric](../articles/service-fabric/service-fabric-overview.md), [Web-Apps](../articles/app-service/overview.md) und [Batch](../articles/batch/batch-technical-overview.md).  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Was ist der Unterschied zwischen Azure Container Service und ACS Engine? 
Azure Container Service ist ein durch eine SLA abgedeckter Azure-Dienst mit Features im Azure-Portal, Azure-Befehlszeilentools und Azure-APIs. Der Dienst ermöglicht die schnelle Implementierung und Verwaltung von Clustern unter Verwendung standardmäßiger Containerorchestrierungstools mit einer vergleichsweise geringen Anzahl von Konfigurationsoptionen. 

[ACS Engine](http://github.com/Azure/acs-engine) ist ein Open-Source-Projekt, mit dem Poweruser sämtliche Ebenen der Clusterkonfiguration anpassen können. Aufgrund der Möglichkeit, sowohl die Infrastruktur- als auch die Softwarekonfiguration zu ändern, bieten wir für ACS Engine keine SLA an. Der Support wird über das Open-Source-Projekt auf GitHub und nicht über offizielle Microsoft-Kanäle bereitgestellt. 

Weitere ausführliche Informationen finden Sie in unserer [Support-Richtlinie für Container](https://support.microsoft.com/en-us/help/4035670/support-policy-for-containers).

## <a name="cluster-management"></a>Clusterverwaltung

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>Wie erstelle ich SSH-Schlüssel für meine Cluster?

Für die Authentifizierung bei den virtuellen Linux-Computern Ihres Clusters können Sie mithilfe der Standardtools Ihres Betriebssystems ein Paar aus einem öffentlichen und einem privaten SSH-RSA-Schlüssel erstellen. Eine entsprechende Anleitung finden Sie im Leitfaden für [OS X und Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) bzw. im Leitfaden für [Windows](../articles/virtual-machines/linux/ssh-from-windows.md). 

Wenn Sie einen Containerdienstcluster mithilfe von [Azure CLI-Befehlen](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) bereitstellen, können SSH-Schlüssel automatisch für Ihren Cluster generiert werden.

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>Wie erstelle ich einen Dienstprinzipal für meinen Kubernetes-Cluster?

Eine Azure Active Directory-Dienstprinzipal-ID und ein Kennwort werden auch für die Erstellung eines Kubernetes-Clusters in Azure Container Service benötigt. Weitere Informationen finden Sie im Artikel zum [Dienstprinzipal für einen Kubernetes-Cluster](../articles/container-service/kubernetes/container-service-kubernetes-service-principal.md).

Wenn Sie einen Kubernetes-Cluster mithilfe von [Azure CLI-Befehlen](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) bereitstellen, können Dienstprinzipal-Anmeldeinformationen automatisch für Ihren Cluster generiert werden.

### <a name="how-large-a-cluster-can-i-create"></a>Welche Clustergrößen kann ich erstellen?
Sie können einen Cluster mit einem, drei oder fünf Masterknoten erstellen. Außerdem können Sie bis zu 100 Agent-Knoten auswählen.

> [!IMPORTANT]
> Bei größeren Clustern und abhängig von der für die Knoten gewählten VM-Größe müssen Sie in Ihrem Abonnement unter Umständen das Kernkontingent erhöhen. In diesem Fall können Sie kostenlos [eine Anfrage an den Onlinekundensupport richten](../articles/azure-supportability/how-to-create-azure-support-request.md) und eine Erhöhung des Kontingents anfordern. Bei Verwendung eines [kostenlosen Azure-Kontos](https://azure.microsoft.com/free/)können Sie nur eine begrenzte Anzahl von Azure-Compute-Kernen nutzen.
> 

### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>Wie kann ich nach der Clustererstellung die Masteranzahl erhöhen? 
Nach der Erstellung des Clusters ist die Masteranzahl unveränderlich. Bei der Clustererstellung sollten Sie im Idealfall mehrere Master auswählen, um Hochverfügbarkeit sicherzustellen.

### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>Wie kann ich nach der Clustererstellung die Agent-Anzahl erhöhen? 
Die Agent-Anzahl im Cluster kann über das Azure-Portal oder mithilfe von Befehlszeilentools skaliert werden. Informationen hierzu finden Sie unter [Skalieren eines Azure Container Service-Clusters](../articles/container-service/kubernetes/container-service-scale.md).

### <a name="what-are-the-urls-of-my-masters-and-agents"></a>Wie lauten die URLs meiner Master und Agents? 
Die URLs von Clusterressourcen in Azure Container Service basieren auf dem von Ihnen angegebenen DNS-Namenspräfix sowie auf dem Namen der Azure-Region, die Sie für die Bereitstellung ausgewählt haben. Der vollständig qualifizierte Domänennamen (Fully Qualified Domain Name, FQDN) des Masterknotens hat beispielsweise das folgende Format:

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

Häufig verwendete URLs für Ihren Cluster finden Sie im Azure-Portal, im Azure-Ressourcen-Explorer und in anderen Azure-Tools.

### <a name="how-do-i-tell-which-orchestrator-version-is-running-in-my-cluster"></a>Woher weiß ich, welche Orchestratorversion in meinem Cluster ausgeführt wird?

* DC/OS: Siehe [Mesosphere-Dokumentation](https://docs.mesosphere.com/1.7/usage/cli/command-reference/)
* Docker Swarm: Führen Sie `docker version` aus.
* Kubernetes: Führen Sie `kubectl version` aus.

### <a name="how-do-i-upgrade-the-orchestrator-after-deployment"></a>Wie aktualisiere ich den Orchestrator nach der Bereitstellung?

Derzeit stellt Azure Container Service keine Tools zum Aktualisieren der in Ihrem Cluster bereitgestellten Orchestratorversion zur Verfügung. Wenn Container Service eine höhere Version unterstützt, können Sie einen neuen Cluster bereitstellen. Eine andere Möglichkeit besteht darin, einen Cluster direkt mithilfe orchestratorspezifischer Tools (sofern verfügbar) zu aktualisieren. Ein Beispiel finden Sie im Artikel zu [DC/OS-Upgrades](http://docs.mesosphere.com/1.12/installing/production/upgrading).
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>Wo finde ich die SSH-Verbindungszeichenfolge für meinen Cluster?

Die Verbindungszeichenfolge können Sie im Azure-Portal oder mithilfe von Azure-Befehlszeilentools ermitteln. 

1. Navigieren Sie im Portal zur Ressourcengruppe für die Clusterbereitstellung.  

2. Klicken Sie auf **Übersicht** und anschließend unter **Zusammenfassung** auf den Link für **Bereitstellungen**. 

3. Klicken Sie auf dem Blatt **Bereitstellungsverlauf** auf die Bereitstellung, deren Name sich aus **microsoft-acs** und einem Bereitstellungsdatum zusammensetzt. Beispiel: microsoft-acs-201701310000.  

4. Auf der Seite **Zusammenfassung** werden unter **Ausgaben** mehrere Clusterlinks bereitgestellt. **SSHMaster0** stellt eine SSH-Verbindungszeichenfolge für den ersten Master in Ihrem Containerdienstcluster bereit. 

Wie bereits erwähnt kann der FQDN des Masters auch mithilfe von Azure-Tools ermittelt werden. Stellen Sie eine SSH-Verbindung mit dem Master her, und verwenden Sie dabei den FQDN des Masters sowie den Benutzernamen, den Sie bei der Clustererstellung angegeben haben. Beispiel: 

```bash
ssh userName@masterFQDN –A –p 22 
```

Weitere Informationen finden Sie unter [Verbinden mit einem Azure Container Service-Cluster](../articles/container-service/kubernetes/container-service-connect.md).

### <a name="my-dns-name-resolution-isnt-working-on-windows-what-should-i-do"></a>Mein DNS-Namensauflösung funktioniert unter Windows nicht. Wie sollte ich vorgehen?

Unter Windows gibt es einige bekannte DNS-Probleme, für die momentan noch Korrekturen verteilt werden. Vergewissern Sie sich, dass Sie über die neueste ACS-Engine und die neueste Windows-Version (mit Installation von [KB4074588](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4074588) und [KB4089848](https://www.catalog.update.microsoft.com/Search.aspx?q=KB4089848)) verfügen, damit Ihre Umgebung hiervon profitieren kann. Weitere Schritte zur Problembehandlung finden Sie in der folgenden Tabelle:

| DNS-Symptom | Problemumgehung  |
|-------------|-------------|
|Beim Absturz eines instabilen Workload-Containers wird der Netzwerknamespace bereinigt. | Stellen Sie die betroffenen Dienste erneut bereit. |
| Der VIP-Dienstzugriff funktioniert nicht. | Konfigurieren Sie ein [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/), damit jederzeit ein normales (nicht privilegiertes) Pod ausgeführt wird. |
|Bei einem Ausfall des Knotens, auf dem der Container ausgeführt wird, sind DNS-Abfragen unter Umständen nicht erfolgreich und führen zu einem negativen Cacheeintrag. | Führen Sie in den betroffenen Containern Folgendes aus: <ul><li> `New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name MaxCacheTtl -Value 0 -Type DWord`</li><li>`New-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\Dnscache\Parameters' -Name MaxNegativeCacheTtl -Value 0 -Type DWord`</li><li>`Restart-Service dnscache` </li></ul><br> Sollte das Problem weiterhin bestehen, deaktivieren Sie das DNS-Caching: <ul><li>`Set-Service dnscache -StartupType disabled`</li><li>`Stop-Service dnscache`</li></ul> |

## <a name="next-steps"></a>Nächste Schritte

* [Informieren Sie sich ausführlicher](../articles/container-service/kubernetes/container-service-intro-kubernetes.md) über Azure Container Service.
* Stellen Sie einen Azure Container Service-Cluster über das [Portal](../articles/container-service/dcos-swarm/container-service-deployment.md) oder mithilfe der [Azure CLI](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) bereit.
