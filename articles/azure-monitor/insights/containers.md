---
title: Containerüberwachungslösung in Azure Monitor | Microsoft-Dokumentation
description: Mit der Containerüberwachungslösung in Azure Monitor können Sie Ihre Docker- und Windows-Containerhosts an einem zentralen Ort anzeigen und verwalten.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/06/2020
ms.openlocfilehash: a02ea022bedd92e9deaa0730cc1be051a9d20c88
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145683"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Containerüberwachungslösung in Azure Monitor

![Container-Symbol](./media/containers/containers-symbol.png)

In diesem Artikel wird das Einrichten und Verwenden der Containerüberwachungslösung in Azure Monitor beschrieben, die Sie beim Anzeigen und Verwalten Ihrer Docker- und Windows-Containerhosts an einem zentralen Ort unterstützt. Docker ist ein Softwarevirtualisierungssystem zum Erstellen von Containern, durch die das Bereitstellen von Software in einer IT-Infrastruktur automatisiert werden kann.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Die Lösung zeigt, welche Container, welches Containerimage und wo Container ausgeführt werden. Sie können ausführliche Überwachungsinformationen anzeigen, die auch die mit Containern verwendeten Befehle enthalten. Außerdem können Sie die Probleme mit Containern beheben, indem Sie zentralisierte Protokolle anzeigen und durchsuchen, ohne eine Remoteanzeige der Docker- und Windows-Hosts zu benötigen. Sie können Container suchen, die Störungen verursachen und übermäßig viele Ressourcen auf einem Host verbrauchen. Darüber hinaus können Sie an einem zentralen Ort Informationen zur Leistung und Auslastung von CPU, Arbeitsspeicher, Speicher und Netzwerk zu Containern anzeigen. Auf Windows-Computern können Sie die Protokolle von Windows Server-, Hyper-V- und Docker-Containern zentralisieren und vergleichen. Die Lösung unterstützt die folgenden Containerorchestratoren:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Wenn Sie Container in [Azure Service Fabric](../../service-fabric/service-fabric-overview.md) bereitgestellt haben, empfiehlt es sich, sowohl die [Service Fabric-Lösung](../../service-fabric/service-fabric-diagnostics-oms-setup.md) als auch diese Lösung zu aktivieren, um die Überwachung von Clusterereignissen einzubeziehen. Lesen Sie vor dem Aktivieren der Service Fabric-Lösung den Artikel [Verwenden der Service Fabric-Lösung](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) durch, um mehr über diese Lösung und deren Verwendung zu erfahren.

Wenn Sie die Leistung Ihrer Workloads überwachen möchten, die in in Azure Kubernetes Service (AKS) gehosteten Kubernetes-Umgebungen bereitgestellt werden, finden Sie die entsprechenden Informationen unter [Überwachen von Azure Kubernetes Service](./container-insights-overview.md). Die Überwachung dieser Plattform wird von der Containerüberwachungslösung nicht unterstützt.  

In der folgenden Abbildung sind die Beziehungen zwischen verschiedenen Containerhosts und Agents mit Azure Monitor dargestellt:

![Containerdiagramm](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Systemanforderungen und unterstützte Plattformen

Prüfen Sie zunächst anhand der folgenden Informationen, ob die Voraussetzungen erfüllt sind.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Unterstützung der Containerüberwachungslösung für Docker Orchestrator und das Betriebssystem

Die folgende Tabelle zeigt die Docker-Orchestrierung und die Unterstützung der Betriebssystemüberwachung von Containerbestand, Leistung und Protokollen mit Azure Monitor:   

|Docker-Orchestrierung | ACS | Linux | Windows | Container<br>Inventory | Image<br>Inventory | Node<br>Inventory | Container<br>Leistung | Container<br>Ereignis | Ereignis<br>Log | Container<br>Log |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Dienst<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat Open<br>Shift | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(eigenständig) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Linux-Server<br>(eigenständig) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |

### <a name="docker-versions-supported-on-linux"></a>Unter Linux unterstützte Docker-Versionen

- Docker 1.11 bis 1.13
- Docker CE und EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>Als Containerhosts unterstützte x64-Linux-Distributionen

- Ubuntu 14.04 LTS und 16.04 LTS
- CoreOS (Stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7.2 und 7.3
- SLES 12
- RHEL 7.2 und 7.3
- Red Hat OpenShift Container Platform (OCP) 3.4 und 3.5
- ACS Mesosphere DC/OS 1.7.3 bis 1.8.8
- ACS Kubernetes 1.4.5 bis 1.6
    - Kubernetes-Ereignisse, Kubernetes-Inventar und Containerprozesse werden nur mit Version 1.4.1-45 und höher des Log Analytics-Agents für Linux unterstützt.
- ACS Docker Swarm

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Unterstützte Windows-Betriebssysteme

- Windows Server 2016
- Windows 10 Anniversary Edition (Professional oder Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Unter Windows unterstützte Docker-Versionen

- Docker 1.12 und 1.13
- Docker 17.03.0 und höher

## <a name="installing-and-configuring-the-solution"></a>Installieren und Konfigurieren der Lösung

Verwenden Sie die folgenden Informationen zum Installieren und Konfigurieren der Lösung.

1. Fügen Sie die Containerüberwachungslösung über den [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) oder mithilfe des unter [Überwachungslösungen in Azure Monitor](./solutions.md) beschriebenen Prozesses Ihrem Log Analytics-Arbeitsbereich hinzu.

2. Installieren und verwenden Sie Docker mit einem Log Analytics-Agent. Basierend auf Ihrem Betriebssystem und Docker-Orchestrator können Sie die folgenden Methoden verwenden, um den Agent zu konfigurieren.
   - Für eigenständige Hosts:
     - Unter unterstützten Linux-Betriebssystemen installieren Sie zunächst Docker und führen es aus. Anschließend installieren und konfigurieren Sie den [Log Analytics-Agent für Linux](../learn/quick-collect-linux-computer.md).  
     - Unter CoreOS kann der Log Analytics-Agent für Linux nicht ausgeführt werden. Stattdessen führen Sie eine Containerversion des Log Analytics-Agents für Linux aus. Informieren Sie sich über Linux-Containerhosts mit CoreOS oder Azure Government-Linux-Containerhosts mit CoreOS, wenn Sie in Azure Government Cloud mit Containern arbeiten.
     - Installieren Sie unter Windows Server 2016 und Windows 10 die Docker-Engine und den Client, und stellen Sie dann eine Verbindung mit einem Agent her, um Informationen zu sammeln und sie an Azure Monitor zu senden. Wenn Sie über eine Windows-Umgebung verfügen, lesen Sie [Installieren und Konfigurieren von Windows-Containerhosts](#install-and-configure-windows-container-hosts).
   - Für Docker-Orchestrierung mit mehreren Hosts:
     - Wenn Sie über eine Red Hat OpenShift-Umgebung verfügen, helfen Ihnen die Informationen unter „Konfigurieren eines Log Analytics-Agents für Red Hat OpenShift“ weiter.
     - Bei einem Kubernetes-Cluster mit Azure Container Service:
       - Siehe [Konfigurieren eines Log Analytics-Linux-Agents für Kubernetes](#configure-a-log-analytics-linux-agent-for-kubernetes)
       - Lesen Sie [Konfigurieren eines Log Analytics-Windows-Agents für Kubernetes](#configure-a-log-analytics-windows-agent-for-kubernetes).
       - Lesen Sie „Verwenden von Helm zum Bereitstellen des Log Analytics-Agents unter Linux Kubernetes“.
     - Wenn Sie über einen DC/OS-Cluster in Azure Container Service verfügen, finden Sie unter [Überwachen eines Azure Container Service-DC/OS-Clusters mit Log Analytics](/previous-versions/azure/container-service/dcos-swarm/container-service-monitoring-oms) weitere Informationen.
     - Wenn Sie über eine Umgebung im Docker Swarm-Modus verfügen, finden Sie weitere Informationen unter „Konfigurieren eines Log Analytics-Agents für Docker Swarm“.
     - Wenn Sie über einen Service Fabric-Cluster verfügen, finden Sie weitere Informationen unter [Überwachen von Containern mit Azure Monitor-Protokollen](../../service-fabric/service-fabric-diagnostics-oms-containers.md).

Weitere Informationen zum Installieren und Konfigurieren von Docker-Engines auf Windows-Computern finden Sie im Artikel [Docker-Engine unter Windows](/virtualization/windowscontainers/manage-docker/configure-docker-daemon).

> [!IMPORTANT]
> Docker muss ausgeführt werden, **bevor** Sie den [Log Analytics-Agent für Linux](../learn/quick-collect-linux-computer.md) auf Ihren Containerhosts installieren. Wenn Sie den Agent bereits vor der Installation von Docker installiert haben, müssen Sie den Log Analytics-Agent für Linux erneut installieren. Weitere Informationen zu Docker finden Sie auf der [Docker-Website](https://www.docker.com).

### <a name="install-and-configure-linux-container-hosts"></a>Installieren und Konfigurieren von Linux-Containerhosts

Verwenden Sie nach dem Installieren von Docker die folgenden Einstellungen für den Containerhost, um den Agent für die Verwendung mit Docker zu konfigurieren. Zunächst benötigen Sie Ihre ID und den Schlüssel für den Log Analytics-Arbeitsbereich. Diese Daten finden Sie im Azure-Portal. Klicken Sie in Ihrem Arbeitsbereich auf **Schnellstart** > **Computer** , um Ihre **Arbeitsbereichs-ID** und Ihren **Primärschlüssel** anzuzeigen.  Kopieren Sie beide Angaben, und fügen Sie sie in den von Ihnen bevorzugten Editor ein.

**Für alle Linux-Containerhosts mit Ausnahme von CoreOS:**

- Weitere Informationen und Anweisungen zum Installieren des Log Analytics-Agents für Linux finden Sie in der [Übersicht zum Log Analytics-Agent für Linux](../platform/log-analytics-agent.md).

**Für alle Linux-Containerhosts mit CoreOS:**

Starten Sie den Container, den Sie überwachen möchten. Verwenden Sie das folgende Beispiel, und passen Sie es an:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Für alle Azure Government-Linux-Containerhosts mit CoreOS:**

Starten Sie den Container, den Sie überwachen möchten. Verwenden Sie das folgende Beispiel, und passen Sie es an:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Wechseln von der Verwendung eines installierten Linux-Agents zu einem in einem Container enthaltenen**

Wenn Sie zuvor den direkt installierten Agent verwendet haben und stattdessen einen Agent verwenden möchten, der in einem Container ausgeführt wird, müssen Sie zunächst den Log Analytics-Agent für Linux entfernen. Informationen zum erfolgreichen Deinstallieren des Agents finden Sie unter [Deinstallieren des Log Analytics-Agents für Linux](../learn/quick-collect-linux-computer.md).  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Konfigurieren eines Log Analytics-Agents für Docker Swarm

Sie können den Log Analytics-Agent als globalen Dienst in Docker Swarm ausführen. Erstellen Sie anhand der folgenden Informationen einen Log Analytics-Agent-Dienst. Sie müssen Ihre Log Analytics-Arbeitsbereichs-ID und den Primärschlüssel angeben.

- Führen Sie auf dem Masterknoten Folgendes aus.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Sichern von Geheimnissen für Docker Swarm

Verwenden Sie für Docker Swarm die folgenden Informationen, um die Geheimnisinformationen zu erstellen, sobald das Geheimnis für die Arbeitsbereichs-ID und den Primärschlüssel erstellt ist.

1. Führen Sie auf dem Masterknoten Folgendes aus.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Vergewissern Sie sich, dass die Geheimnisse ordnungsgemäß erstellt wurden.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Führen Sie den folgenden Befehl aus, um die Geheimnisse im Log Analytics-Agent im Container einzubinden.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Konfigurieren eines Log Analytics-Agents für Red Hat OpenShift

Es gibt drei Möglichkeiten, wie Sie in Red Hat OpenShift den Log Analytics-Agent hinzufügen können, um mit dem Sammeln von Daten für die Containerüberwachung zu beginnen.

* [Installieren des Log Analytics-Agents für Linux](../learn/quick-collect-linux-computer.md) direkt auf jedem OpenShift-Knoten  
* [Aktivieren der Log Analytics-VM-Erweiterung](../learn/quick-collect-azurevm.md) auf jedem OpenShift-Knoten in Azure  
* Installieren des Log Analytics-Agents als OpenShift-Daemongruppe  

In diesem Abschnitt werden die Schritte beschrieben, die zum Installieren des Log Analytics-Agents als OpenShift-Daemongruppe erforderlich sind.  

1. Melden Sie sich am OpenShift-Masterknoten an, und kopieren Sie die YAML-Datei [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) aus GitHub auf Ihren Masterknoten. Nehmen Sie die Änderung mit Ihrer Log Analytics-Arbeitsbereich-ID und dem Primärschlüssel vor.
2. Führen Sie die folgenden Befehle aus, um ein Projekt für Azure Monitor zu erstellen und das Benutzerkonto festzulegen:

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Führen Sie Folgendes aus, um das DaemonSet bereitzustellen:

    `oc create -f ocp-omsagent.yaml`

4. Geben Sie Folgendes ein, um zu überprüfen, ob alles richtig konfiguriert ist und funktioniert:

    `oc describe daemonset omsagent`  

    Die Ausgabe sollte in etwa wie folgt aussehen:

    ```
    [ocpadmin@khm-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

Führen Sie die folgenden Schritte aus, wenn Sie Geheimnisse nutzen möchten, um beim Verwenden der YAML-Datei der Log Analytics-Agent-Daemongruppe Ihre Log Analytics-Arbeitsbereichs-ID und den Primärschlüssel zu schützen.

1. Melden Sie sich am OpenShift-Masterknoten an, und kopieren Sie die YAML-Datei [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) und das Skript [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) zum Generieren von Geheimnissen aus GitHub.  Mit diesem Skript wird die YAML-Geheimnisdatei für die Log Analytics-Arbeitsbereichs-ID und den Primärschlüssel generiert, um Ihre Geheimnisinformationen zu schützen.  
2. Führen Sie die folgenden Befehle aus, um ein Projekt für Azure Monitor zu erstellen und das Benutzerkonto festzulegen: Im Skript werden Sie zum Generieren von Geheimnissen zum Angeben Ihrer Log Analytics-Arbeitsbereich-ID `<WSID>` und Ihres Primärschlüssels `<KEY>` aufgefordert. Wenn der Vorgang abgeschlossen ist, wird die Datei „ocp-secret.yaml“ erstellt.  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Stellen Sie die Geheimnisdatei bereit, indem Sie Folgendes ausführen:

    `oc create -f ocp-secret.yaml`

4. Überprüfen Sie die Bereitstellung, indem Sie Folgendes ausführen:

    `oc describe secret omsagent-secret`  

    Die Ausgabe sollte in etwa wie folgt aussehen:  

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe secret omsagent-secret  
    Name:           omsagent-secret  
    Namespace:      omslogging  
    Labels:         <none>  
    Annotations:    <none>  

    Type:   Opaque  

    Data  
    ====  
    KEY:    89 bytes  
    WSID:   37 bytes  
    ```

5. Stellen Sie die YAML-Datei für die Log Analytics-Agent-Daemongruppe bereit, indem Sie Folgendes ausführen:

    `oc create -f ocp-ds-omsagent.yaml`  

6. Überprüfen Sie die Bereitstellung, indem Sie Folgendes ausführen:

    `oc describe ds oms`

    Die Ausgabe sollte in etwa wie folgt aussehen:

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Konfigurieren eines Log Analytics-Linux-Agents für Kubernetes

Für Kubernetes verwenden Sie ein Skript, um die YAML-Datei mit Geheimnissen für Ihre Arbeitsbereichs-ID und den Primärschlüssel zum Installieren des Log Analytics-Agents für Linux zu generieren. Auf der GitHub-Seite [Log Analytics Docker Kubernetes](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) finden Sie Dateien, die Sie mit Ihren oder ohne Ihre Geheimnisinformationen verwenden können.

- Die standardmäßige Daemongruppe des Log Analytics-Agents für Linux enthält keine Geheimnisinformationen („omsagent.yaml“).
- Die YAML-Datei für die Daemongruppe des Log Analytics-Agents für Linux verwendet Geheimnisinformationen („omsagent-ds-secrets.yaml“) mit Skripts zum Generieren von Geheimnissen, um die YAML-Geheimnisdatei („omsagentsecret.yaml“) zu generieren.

Sie können OMS-Agent-DaemonSets mit oder ohne Geheimnisse erstellen.

**Standardmäßige OMSagent-DaemonSet-YAML-Datei ohne Geheimnisse**

- Ersetzen Sie für die standardmäßige YAML-Datei der Log Analytics-Agent-Daemongruppe `<WSID>` und `<KEY>` durch Ihre Werte für WSID und KEY. Kopieren Sie die Datei in Ihren Masterknoten, und führen Sie Folgendes aus:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Standardmäßige OMSagent-DaemonSet-YAML-Datei mit Geheimnissen**

1. Um die Log Analytics-Agent-Daemongruppe mit Geheimnisinformationen zu verwenden, erstellen Sie zuerst die Geheimnisse.
    1. Kopieren Sie das Skript und die Vorlagendatei für Geheimnisse, und vergewissern Sie sich, dass sie sich im selben Verzeichnis befinden.
        - Skript zum Generieren von Geheimnissen – „secret-gen.sh“
        - Vorlage für Geheimnisse - „secret-template.yaml“
    2. Führen Sie das Skript wie im folgenden Beispiel aus. Im Skript werden Sie zur Eingabe der Log Analytics-Arbeitsbereichs-ID und des Primärschlüssels aufgefordert. Nach der Eingabe wird eine YAML-Geheimnisdatei erstellt, die Sie dann ausführen können.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Erstellen Sie den Pod für Geheimnisse, indem Sie Folgendes ausführen:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Führen Sie zur Überprüfung Folgendes aus:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        Die Ausgabe sollte ähnlich wie die Folgende aussehen:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        Die Ausgabe sollte ähnlich wie die Folgende aussehen:

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Erstellen Ihres OMS-Agent-DaemonSet durch Ausführen von ```sudo kubectl create -f omsagent-ds-secrets.yaml```

2. Vergewissern Sie sich, dass die Log Analytics-Agent-Daemongruppe ausgeführt wird, ähnlich wie folgt:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

Verwenden Sie für Kubernetes ein Skript, um die YAML-Datei mit Geheimnissen für die Arbeitsbereichs-ID und den Primärschlüssel für den Log Analytics-Agent für Linux zu erstellen. Verwenden Sie die folgenden Beispielinformationen mit der [OMSagent-YAML-Datei](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml), um Ihre Geheimnisinformationen zu sichern.

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Konfigurieren eines Log Analytics-Windows-Agents für Kubernetes

Für Windows Kubernetes verwenden Sie ein Skript, um die YAML-Datei mit Geheimnissen für Ihre Arbeitsbereichs-ID und den Primärschlüssel zum Installieren des Log Analytics-Agents zu generieren. Auf der GitHub-Seite [Log Analytics Docker Kubernetes](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) finden Sie Dateien, die Sie mit Ihren Geheimnisinformationen verwenden können.  Sie müssen den Log Analytics-Agent für die Master- und Agent-Knoten separat installieren.  

1. Um die Log Analytics-Agent-Daemongruppe mit Geheimnisinformationen auf dem Masterknoten zu verwenden, melden Sie sich an und erstellen zuerst die Geheimnisse.
    1. Kopieren Sie das Skript und die Vorlagendatei für Geheimnisse, und vergewissern Sie sich, dass sie sich im selben Verzeichnis befinden.
        - Skript zum Generieren von Geheimnissen – „secret-gen.sh“
        - Vorlage für Geheimnisse - „secret-template.yaml“

    2. Führen Sie das Skript wie im folgenden Beispiel aus. Im Skript werden Sie zur Eingabe der Log Analytics-Arbeitsbereichs-ID und des Primärschlüssels aufgefordert. Nach der Eingabe wird eine YAML-Geheimnisdatei erstellt, die Sie dann ausführen können.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Erstellen Ihres OMS-Agent-DaemonSet durch Ausführen von ```kubectl create -f omsagentsecret.yaml```
    4. Führen Sie zur Überprüfung Folgendes aus:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        Die Ausgabe sollte ähnlich wie die Folgende aussehen:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Erstellen Ihres OMS-Agent-DaemonSet durch Ausführen von ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Vergewissern Sie sich, dass die Log Analytics-Agent-Daemongruppe ausgeführt wird, ähnlich wie folgt:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Zum Installieren des Agents auf den Workerknoten, die Windows ausführen, führen Sie die Schritte im Abschnitt [Installieren und Konfigurieren von Windows-Containerhosts](#install-and-configure-windows-container-hosts) aus.

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Verwenden von Helm zum Bereitstellen des Log Analytics-Agents unter Linux Kubernetes

Führen Sie die folgenden Schritte aus, um Helm zum Bereitstellen des Log Analytics-Agents für Ihre Linux Kubernetes-Umgebung zu verwenden.

1. Erstellen Ihres OMS-Agent-DaemonSet durch Ausführen von ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. Die Ergebnisse sehen etwa wie folgt aus:

    ```
    NAME:   omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED

    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     3s

    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         3s
    ```

3. Sie können den Status von omsagent überprüfen, indem Sie den folgenden Befehl ausführen: ```helm status "omsagent"```. Die Ausgabe gleicht dem folgenden Beispiel:

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
   
    Weitere Informationen finden Sie unter [Containerlösung Helm-Diagramm](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Installieren und Konfigurieren von Windows-Containerhosts

Verwenden Sie die Informationen in diesem Abschnitt zum Installieren und Konfigurieren von Windows-Containerhosts.

#### <a name="preparation-before-installing-windows-agents"></a>Vorbereitung vor der Installation von Windows-Agents

Vor der Installation von Agents auf Windows-Computern müssen Sie den Docker-Dienst konfigurieren. Die Konfiguration ermöglicht dem Windows-Agent oder der Azure Monitor-Erweiterung für virtuelle Computer, den Docker-TCP-Socket zu verwenden, sodass die Agents remote auf den Docker-Daemon zugreifen und Daten für die Überwachung erfassen können.

##### <a name="to-configure-the-docker-service"></a>So konfigurieren Sie den Docker-Dienst  

Führen Sie die folgenden PowerShell-Befehle aus, um TCP-Pipe und Named Pipe für Windows Server zu aktivieren:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

Weitere Informationen zur Docker-Daemon-Konfiguration mit Windows-Containern finden Sie unter [Docker-Engine unter Windows](/virtualization/windowscontainers/manage-docker/configure-docker-daemon).

#### <a name="install-windows-agents"></a>Installieren des Windows-Agents

Um die Überwachung von Windows- und Hyper-V-Containern zu aktivieren, installieren Sie den Microsoft Monitoring Agent (MMA) auf Windows-Computern, bei denen es sich um Containerhosts handelt. Informationen zu Windows-Computern in Ihrer lokalen Umgebung finden Sie unter [Verbinden von Windows-Computern mit dem Log Analytics-Dienst in Azure](../platform/agent-windows.md). Verbinden Sie virtuelle Computer in Azure unter Verwendung der [VM-Erweiterung](../learn/quick-collect-azurevm.md) mit Azure Monitor.

Sie können Windows-Container überwachen, die unter Service Fabric ausgeführt werden. Allerdings werden derzeit nur [unter Azure ausgeführte virtuelle Computer](../learn/quick-collect-azurevm.md) und [Windows-Computer, die in Ihrer lokalen Umgebung ausgeführt werden](../platform/agent-windows.md), für Service Fabric unterstützt.

Sie können überprüfen, ob die Containerüberwachungslösung für Windows korrekt festgelegt ist. Um zu überprüfen, ob das Management Pack ordnungsgemäß heruntergeladen wurde, suchen Sie nach *ContainerManagement.xxx*. Die Dateien sollten sich im Verzeichnis „C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs“ befinden.

## <a name="solution-components"></a>Lösungskomponenten

Navigieren Sie im Azure-Portal zum *Lösungskatalog* , und fügen Sie die **Containerüberwachungslösung** hinzu. Wenn Sie Windows-Agents verwenden, wird auf jedem Computer mit einem Agent beim Hinzufügen dieser Lösung das folgende Management Pack installiert. Für das Management Pack ist keine Konfiguration oder Wartung erforderlich.

- *ContainerManagement.xxx* wird unter „C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs“ installiert.

## <a name="container-data-collection-details"></a>Details zur Datensammlung in Containern

Die Containerüberwachungslösung sammelt verschiedene Leistungsmetriken und Protokolldaten von Containerhosts und Containern mit Agents, die Sie aktivieren.

Alle drei Minuten werden von den folgenden Agent-Typen Daten gesammelt.

- [Log Analytics-Agent für Linux](../learn/quick-collect-linux-computer.md)
- [Windows-Agent](../platform/agent-windows.md)
- [Log Analytics-VM-Erweiterung](../learn/quick-collect-azurevm.md)

### <a name="container-records"></a>Containerdatensätze

Die folgende Tabelle enthält Beispiele für von der Containerüberwachungslösung gesammelte Datentypen, die in Protokollsuchergebnissen angezeigt werden.

| Datentyp | Datentyp in der Protokollsuche | Felder |
| --- | --- | --- |
| Leistung von Hosts und Containern | `Perf` | Computer, ObjectName, CounterName &#40;%Processor Time, Disk Reads MB, Disk Writes MB, Memory Usage MB, Network Receive Bytes, Network Send Bytes, Processor Usage sec, Network&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Containerinhalt | `ContainerInventory` | TimeGenerated, Computer, Containername, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Containerimageinhalt | `ContainerImageInventory` | TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, Running, Paused, Stopped, Failed, SourceSystem, ImageID, TotalContainer |
| Containerprotokoll | `ContainerLog` | TimeGenerated, Computer, Image-ID, Containername, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Containerdienstprotokoll | `ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |
| Containerknotenbestand | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Kubernetes-Bestand | `KubePodInventory_CL` | TimeGenerated, Computer, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Containerprozess | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Kubernetes-Ereignisse | `KubeEvents_CL` | TimeGenerated, Computer, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, Message |

Bezeichnungen, die an *PodLabel* -Datentypen angefügt werden, sind Ihre eigenen benutzerdefinierten Bezeichnungen. Die angefügten PodLabel-Bezeichnungen in der Tabelle stellen Beispiele dar. Deshalb unterscheiden sich `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s` im Dataset Ihrer Umgebung und ähneln allgemein `PodLabel_yourlabel_s`.

## <a name="monitor-containers"></a>Überwachen von Containern
Nachdem Sie die Lösung im Azure-Portal aktiviert haben, werden auf der Kachel **Container** zusammenfassende Informationen zu den Containerhosts und den auf den Hosts ausgeführten Containern angezeigt.

![Kachel „Container“](./media/containers/containers-title.png)

Die Kachel zeigt eine Übersicht über die Anzahl der Container in der Umgebung an. Außerdem erfahren Sie, ob Container fehlerhaft sind, ausgeführt werden oder beendet wurden.

### <a name="using-the-containers-dashboard"></a>Verwenden des Containerdashboards

Klicken Sie auf die Kachel **Container**. Dort sind die Ansichten wie folgt angeordnet:

- **Containerereignisse** – Zeigt den Containerstatus und die Computer mit fehlerhaften Containern an.
- **Containerprotokolle** – Zeigt ein Diagramm von Containerprotokolldateien, die mit der Zeit generiert wurden, und eine Liste von Computern mit der höchsten Anzahl von Protokolldateien an.
- **Kubernetes-Ereignisse** – Zeigt ein Diagramm von Kubernetes-Ereignissen, die mit der Zeit generiert wurden, und eine Liste der Gründe an, warum die Ereignisse von Pods generiert wurden. *Dieses Dataset wird nur in Linux-Umgebungen verwendet.*
- **Kubernetes-Namespacebestand** – Zeigt die Anzahl der Namespaces und Pods und ihre Hierarchie an. *Dieses Dataset wird nur in Linux-Umgebungen verwendet.*
- **Containerknotenbestand** – Zeigt die Anzahl der Orchestrierungstypen an, die auf Containerknoten/-hosts verwendet werden. Die Computerknoten/-hosts werden auch in der Anzahl von Containern aufgeführt. *Dieses Dataset wird nur in Linux-Umgebungen verwendet.*
- **Containerimagebestand** – Zeigt die Gesamtanzahl der verwendeten Containerimages und die Anzahl der Imagetypen an. Die Anzahl der Images wird auch im Imagetag aufgeführt.
- **Containerstatus** – Zeigt die Gesamtanzahl der Containerknoten/Hostcomputer an, auf denen Container ausgeführt werden. Computer werden auch in der Anzahl der ausgeführten Hosts aufgelistet.
- **Containerprozess** – Zeigt ein Liniendiagramm von Containerprozessen an, die über einen bestimmten Zeitraum ausgeführt werden. Container werden auch durch Ausführen des Befehls bzw. Prozesses in Containern aufgeführt. *Dieses Dataset wird nur in Linux-Umgebungen verwendet.*
- **Container-CPU-Leistung** – Zeigt ein Liniendiagramm der durchschnittlichen CPU-Auslastung im Zeitverlauf für Computerknoten/-hosts an. Zudem werden die Computerknoten/-hosts basierend auf der durchschnittlichen CPU-Auslastung aufgeführt.
- **Containerspeicherleistung** – Zeigt ein Liniendiagramm der Speicherauslastung im Zeitverlauf an. Zudem wird die Computerspeicherauslastung basierend auf dem Instanznamen aufgeführt.
- **Computerleistung** – Zeigt Liniendiagramme zum Prozentsatz der CPU-Leistung im Zeitverlauf, den Prozentsatz der Speicherauslastung im Zeitverlauf und die Menge des freien Speicherplatzes auf dem Datenträger in MB im Zeitverlauf an. Sie können den Mauszeiger über eine beliebige Zeile in einem Diagramm bewegen, um weitere Details anzuzeigen.

Jeder Bereich des Dashboards ist eine visuelle Darstellung einer Suche, die über die gesammelten Daten ausgeführt wird.

![Screenshot eines Dashboards zum Anzeigen der gesammelten Daten ](./media/containers/containers-dash01.png)

![Containerdashboard](./media/containers/containers-dash02.png)

Klicken Sie im Bereich **Containerstatus** auf den oberen Bereich, wie unten dargestellt.

![Containerstatus](./media/containers/containers-status.png)

Log Analytics wird geöffnet und zeigt Informationen zum Zustand Ihrer Container an.

![Log Analytics für Container](./media/containers/containers-log-search.png)

Hier können Sie die Suchabfrage bearbeiten, um die für Sie interessanten spezifischen Informationen zu suchen. Weitere Informationen zu Protokollabfragen finden Sie unter [Analysieren von Protokolldaten in Azure Monitor](../log-query/log-query-overview.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Beheben von Problemen durch das Suchen fehlerhafter Container

Log Analytics kennzeichnet einen Container als **Fehlerhaft** , wenn dieser mit einem anderen Exitcode als null beendet wurde. Im Bereich **Container mit Fehlern** wird eine Übersicht über die Fehler und Ausfälle in der Umgebung angezeigt.

### <a name="to-find-failed-containers"></a>So suchen Sie nach Containern mit Fehlern

1. Klicken Sie auf den Bereich **Containerstatus**.  
   ![Containerstatus](./media/containers/containers-status.png)
2. Log Analytics wird geöffnet und zeigt den Zustand Ihrer Container an:  
   ![Containerzustand](./media/containers/containers-log-search.png)
3. Erweitern Sie die Zeile „Fehler“, und klicken Sie auf „+“, um die Kriterien der Abfrage hinzufügen. Kommentieren Sie anschließend die Zeile „Summarize“ in der Abfrage aus.
   ![Screenshot der Zeile, die auskommentiert werden sollte](./media/containers/containers-state-failed-select.png)  
1. Führen Sie die Abfrage aus, und erweitern Sie anschließend eine Zeile in den Ergebnissen, um die Image-ID anzuzeigen.  
   ![Screenshot der Anzeige der Image-ID](./media/containers/containers-state-failed.png)  
1. Geben Sie Folgendes in die Protokollabfrage ein: `ContainerImageInventory | where ImageID == <ImageID>`, um Details zum Image wie Imagegröße sowie Anzahl der beendeten und fehlerhaften Images anzuzeigen.  
   ![Container mit Fehlern](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Abfragen von Containerdaten in Protokollen

Wenn Sie Probleme mit einen bestimmten Fehler beheben möchten, kann es hilfreich sein, herauszufinden, wo dieser in Ihrer Umgebung auftritt. Mit den folgenden Protokolltypen können Sie Abfragen erstellen, die die gewünschten Informationen zurückgeben.

- **ContainerImageInventory:** Verwenden Sie diesen Typ, wenn Sie die Informationen organisiert nach Image suchen und Imageinformationen wie Image-IDs oder -Größen anzeigen möchten.
- **ContainerInventory:** Verwenden Sie diesen Typ, wenn Sie Informationen zum Containerspeicherort und -namen sowie zu den darin ausgeführten Images benötigen.
- **ContainerLog:** Verwenden Sie diesen Typ, wenn Sie bestimmte Informationen und Einträge im Fehlerprotokoll suchen möchten.
- **ContainerNodeInventory_CL** Verwenden Sie diesen Typ, wenn Sie Informationen zum Host bzw. Knoten, auf dem sich Container befinden, benötigen. Dieser gibt Informationen zu Docker-Version, Orchestrierungstyp, Speicher und Netzwerk an.
- **ContainerProcess_CL** Verwenden Sie diesen Typ, um schnell den Prozess, der im Container ausgeführt wird, anzuzeigen.
- **ContainerServiceLog:** Verwenden Sie diesen Typ, wenn Sie Audit-Trail-Informationen zum Docker-Daemon suchen, z.B. zu den Befehlen „start“, „stop“, „delete“ oder „pull“.
- **KubeEvents_CL** Verwenden Sie diesen Typ, um die Kubernetes-Ereignisse anzuzeigen.
- **KubePodInventory_CL** Verwenden Sie diesen Typ, wenn Sie Informationen zur Clusterhierarchie einsehen möchten.

### <a name="to-query-logs-for-container-data"></a>So fragen Sie Containerdaten in Protokollen ab

* Wählen Sie ein Image aus, bei dem kürzlich ein Fehler aufgetreten ist, und suchen Sie die zugehörigen Fehlerprotokolle. Suchen Sie zunächst mit **ContainerInventory** nach dem Namen eines Containers, in dem das Image ausgeführt wird. Suchen Sie beispielsweise nach `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`.  
    ![Durchsuchen von Ubuntu-Containern](./media/containers/search-ubuntu.png)

  Erweitern Sie in den Ergebnissen eine beliebige Zeile, um Details für den entsprechenden Container anzuzeigen.

## <a name="example-log-queries"></a>Beispiele für Protokollabfragen

Es ist oft hilfreich, die Erstellung von Abfragen ausgehend von einem oder zwei Beispielen zu beginnen und diese dann an die eigene Umgebung anzupassen. Als Ausgangspunkt können Sie mit dem Bereich **BEISPIELABFRAGEN** ganz rechts auf der Lösungsseite experimentieren, um Sie bei der Erstellung erweiterter Abfragen zu unterstützen.

![Containerabfragen](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>Speichern von Protokollabfragen

Das Speichern von Abfragen ist ein Standardfeature in Azure Monitor. Durch die Speicherung können Sie schnell auf die Abfragen zurückgreifen, die Sie als besonders hilfreich empfinden.

Nachdem Sie eine Abfrage erstellt haben, die für Sie nützlich ist, speichern Sie sie, indem Sie oben auf der Seite „Protokollsuche“ auf **Favoriten** klicken. Sie können später auf der Seite **Mein Dashboard** auf einfache Weise darauf zugreifen.

## <a name="next-steps"></a>Nächste Schritte

[Fragen Sie Protokolle ab](../log-query/log-query-overview.md), um detaillierte Containerdatensätze anzuzeigen.

