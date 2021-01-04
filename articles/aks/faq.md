---
title: Häufig gestellte Fragen zu Azure Kubernetes Service (AKS)
description: Finden Sie Antworten auf einige der häufig gestellten Fragen zu Azure Kubernetes Service (AKS).
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 94cbaf417413b3e11071fb8c7237cbb3ac7b9a37
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96780347"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Häufig gestellte Fragen zu Azure Kubernetes Service (AKS)

Dieser Artikel behandelt häufig gestellte Fragen zu Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-currently-provide-aks"></a>In welchen Azure-Regionen wird AKS derzeit zur Verfügung gestellt?

Eine vollständige Liste der verfügbaren Regionen finden Sie unter [AKS-Regionen und Verfügbarkeit][aks-regions].

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>Kann ich einen AKS-Cluster regionsübergreifend verteilen?

Nein. AKS-Cluster sind regionale Ressourcen und können sich nicht über mehrere Regionen erstrecken. Eine Anleitung zum Erstellen einer Architektur, die mehrere Regionen umfasst, finden Sie unter [Bewährte Methoden für Geschäftskontinuität und Notfallwiederherstellung][bcdr-bestpractices].

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>Kann ich einen AKS-Cluster über Verfügbarkeitszonen hinweg verteilen?

Ja. Sie können einen AKS-Cluster über eine oder mehrere [Verfügbarkeitszonen][availability-zones] hinweg in [Regionen bereitstellen, die diese unterstützen][az-regions].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Kann ich einschränken, wer Zugriff auf den Kubernetes-API-Server hat?

Ja. Es gibt zwei Optionen zum Einschränken des Zugriffs auf den API-Server:

- Verwenden Sie [vom API-Server autorisierte IP-Bereiche][api-server-authorized-ip-ranges], wenn Sie einen öffentlichen Endpunkt für den API-Server verwalten, aber den Zugriff auf eine Reihe von vertrauenswürdigen IP-Bereichen beschränken möchten.
- Verwenden Sie [einen privaten Cluster][private-clusters], wenn Sie den API-Server darauf beschränken möchten, dass er *nur* von Ihrem virtuellen Netzwerk aus zugänglich ist.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Kann ich unterschiedliche VM-Größen in einem einzigen Cluster verwenden?

Ja. Sie können virtuelle Computer unterschiedlicher Größen in Ihrem AKS-Cluster verwenden, indem Sie [mehrere Knotenpools][multi-node-pools] erstellen.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Werden Sicherheitsupdates auf AKS-Agent-Knoten angewendet?

Azure wendet automatisch Sicherheitspatches auf die Linux-Knoten in Ihrem Cluster an, und zwar gemäß einem nächtlichen Zeitplan. Sie sind jedoch dafür verantwortlich, dass diese Linux-Knoten bei Bedarf neu gestartet werden. Sie haben mehrere Möglichkeiten für Neustarts von Knoten:

- Manuell über das Azure-Portal oder die Azure-CLI.
- Durch ein Upgrade des AKS-Clusters. Durch Clusterupgrades [werden Knoten automatisch abgesperrt und ausgeglichen][cordon-drain]. Anschließend werden neue Knoten mit dem neuesten Ubuntu-Image und einer neuen Patchversion oder einer Kubernetes-Nebenversion online geschaltet. Weitere Informationen finden Sie unter [Aktualisieren eines AKS-Clusters][aks-upgrade].
- Durch Verwenden eines [Knotenimageupgrades](node-image-upgrade.md).

### <a name="windows-server-nodes"></a>Windows Server-Knoten

Für Windows Server-Knoten werden die neuesten Updates von Windows Update nicht automatisch ausgeführt und angewendet. Sie sollten in regelmäßigen Abständen ein Upgrade für den Cluster und die Windows Server-Knotenpools in Ihrem AKS-Cluster durchführen, passend zum Windows Update-Freigabezyklus und Ihrem eigenen Validierungsprozess. Während dieses Upgrades werden Knoten erstellt, die das neueste Windows Server-Image und die neuesten Windows Server-Patches ausführen und die älteren Knoten entfernen. Weitere Informationen zu diesem Prozess finden Sie unter [Durchführen eines Upgrades für einen Knotenpool in AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Warum werden zwei Ressourcengruppen mit AKS erstellt?

AKS baut auf einer Reihe von Azure-Infrastrukturressourcen auf, einschließlich VM-Skalierungsgruppen, virtueller Netzwerke und verwalteter Datenträger. Dies ermöglicht es Ihnen, viele der Kernfunktionen der Azure-Plattform in der von AKS bereitgestellten Managed Kubernetes-Umgebung zu nutzen. Beispielsweise können die meisten Typen von virtuellen Azure-Computern direkt mit AKS verwendet werden, und Azure Reservations können zum automatischen Empfang von Rabatten für diese Ressourcen verwendet werden.

Um diese Architektur zu ermöglichen, umfass jede AKS-Bereitstellung zwei Ressourcengruppen:

1. Die erste Ressourcengruppe wird von Ihnen erstellt. Diese Gruppe enthält nur die Kubernetes-Dienstressource. Der AKS-Ressourcenanbieter erstellt während der Bereitstellung automatisch die zweite Ressourcengruppe. *MC_myResourceGroup_myAKSCluster_eastus* ist ein Beispiel für die zweite Ressourcengruppe. Informationen dazu, wie Sie den Namen dieser zweiten Ressourcengruppe angeben, finden Sie im nächsten Abschnitt.
1. Die zweite Ressourcengruppe, als *Knotenressourcengruppe* bezeichnet, enthält alle Infrastrukturressourcen für den Cluster. Diese Ressourcen umfassen die virtuellen Computer des Kubernetes-Knotens, virtuelle Netzwerke und Speicher. Standardmäßig lautet der Name der Knotenressourcengruppe z. B. *MC_myResourceGroup_myAKSCluster_eastus*. AKS löscht automatisch die Knotenressource, wenn der Cluster gelöscht wird. Sie sollte daher nur für Ressourcen verwendet werden, die den gleichen Lebenszyklus wie der Cluster haben.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Kann ich einen eigenen Namen für die AKS-Knotenressourcengruppe angeben?

Ja. In AKS wird der Knotenressourcengruppe standardmäßig der Name *MC_resourcegroupname_clustername_location* zugewiesen, Sie können jedoch auch einen eigenen Namen angeben.

Installieren Sie die [aks-preview][aks-preview-cli]-Erweiterungsversion *0.3.2* oder höher der Azure CLI, wenn Sie einen eigenen Ressourcengruppennamen angeben möchten. Verwenden Sie bei der Erstellung eines AKS-Clusters mit dem Befehl [az aks create][az-aks-create] den Parameter `--node-resource-group`, und geben Sie einen Namen für die Ressourcengruppe an. Wenn Sie [eine Azure Resource Manager-Vorlage verwenden][aks-rm-template], um einen AKS-Cluster bereitzustellen, können Sie die *nodeResourceGroup*-Eigenschaft verwenden, um den Namen der Ressourcengruppe zu definieren.

* Die sekundäre Ressourcengruppe wird automatisch vom Azure-Ressourcenanbieter in Ihrem eigenen Abonnement erstellt.
* Sie können nur einen benutzerdefinierten Namen für die Ressourcengruppe angeben, wenn Sie den Cluster erstellen.

Denken Sie bei der Arbeit mit der Knotenressourcengruppe daran, dass Folgendes nicht möglich ist:

* Angeben einer vorhandenen Ressourcengruppe als Knotenressourcengruppe
* Angeben eines anderen Abonnements für die Knotenressourcengruppe
* Ändern des Namens der Knotenressourcengruppe, nachdem der Cluster erstellt wurde
* Angeben von Namen für die verwalteten Ressourcen innerhalb der Knotenressourcengruppe
* Ändern oder Löschen von Azure erstellter Tags für verwaltete Ressourcen innerhalb der Knotenressourcengruppe (Weitere Informationen finden Sie im nächsten Abschnitt.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Kann ich Tags und andere Eigenschaften der AKS-Ressourcen in der Knotenressourcengruppe ändern?

Wenn Sie die in Azure erstellten Tags und andere Ressourceneigenschaften in der Knotenressourcengruppe ändern oder löschen, kann dies zu unerwarteten Ergebnissen wie Skalierungs- und Aktualisierungsfehlern führen. In AKS können Sie benutzerdefinierte Tags, die von Endbenutzern erstellt wurden, erstellen und ändern, und Sie können diese Tags hinzufügen, wenn Sie [einen Knotenpool erstellen](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool). Sie können benutzerdefinierte Tags erstellen oder ändern, um beispielsweise eine Geschäftseinheit oder eine Kostenstelle zuzuweisen. Dies können Sie auch durch Erstellen von Azure-Richtlinien erreichen, die auf die verwaltete Ressourcengruppe ausgerichtet sind.

**Von Azure erstellte Tags** für Ressourcen unter der Knotenressourcengruppe im AKS-Cluster dürfen dagegen nicht geändert werden, da dies zu einer Verletzung des Servicelevelziels (Service-Level Objective, SLO) führt. Weitere Informationen finden Sie unter [Bietet AKS eine Vereinbarung zum Servicelevel?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Welche Kubernetes-Zugangssteuerungen werden von AKS unterstützt? Können Zulassungscontroller hinzugefügt oder entfernt werden?

AKS unterstützt die folgenden [Zugangssteuerungen][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *PodNodeSelector*
- *PodTolerationRestriction*
- *ExtendedResourceToleration*

Derzeit können Sie die Liste der Zugriffssteuerungen in AKS nicht ändern.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>Kann ich in AKS Zugangscontrollerwebhooks verwenden?

Ja, Sie können Zugangscontrollerwebhooks in AKS verwenden? Es wird empfohlen, interne AKS-Namespaces auszuschließen, die mit der Bezeichnung **control-plane** gekennzeichnet sind. Dies ist beispielsweise durch das Hinzufügen der folgenden Webhookkonfigurationen möglich:

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

AKS dient als eine Art Firewall für den ausgehenden Datenverkehr des API-Servers, sodass der Zugriff auf Ihre Zugangscontrollerwebhooks vom Cluster aus möglich sein muss.

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>Können Zugangscontrollerwebhooks Auswirkungen auf „kube-system“ und interne AKS-Namespaces haben?

Der AKS-Namespace verfügt über einen **Admissions Enforcer**, der die internen „kube-system“- und AKS-Namespaces automatisch ausschließt, um die Stabilität des Systems zu schützen und zu verhindern, dass benutzerdefinierte Zugangscontroller „kube-system“ und interne AKS-Namespaces beeinträchtigen. Dieser Dienst stellt sicher, dass die benutzerdefinierten Zugangscontroller keine Auswirkungen auf die in „kube-system“ ausgeführten Dienste haben.

Bei einem kritischen Anwendungsfall hinsichtlich der Bereitstellung in „kube-system“ (nicht empfohlen), die vom benutzerdefinierten Zugangscontrollerwebhook abgedeckt sein muss, müssen Sie ggf. die folgende Bezeichnung oder Anmerkung hinzufügen, sodass der Admissions Enforcer diese ignoriert.

Bezeichnung: ```"admissions.enforcer/disabled": "true"``` oder Anmerkung: ```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>Ist Azure Key Vault in AKS integriert?

AKS ist derzeit nicht nativ mit Azure Key Vault integriert. Das Projekt [Azure Key Vault-Anbieter für CSI Secrets Store][csi-driver] ermöglicht jedoch die direkte Integration von Kubernetes-Pods in Key Vault-Geheimnisse.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Kann ich Windows Server-Container unter AKS ausführen?

Ja, Windows Server-Container sind in AKS verfügbar. Erstellen Sie einen Knotenpool, der Windows Server als Gastbetriebssystem ausführt, um Windows Server-Container in AKS auszuführen. Windows Server-Container können nur Windows Server 2019 verwenden. Informationen zu den ersten Schritten finden Sie unter [Erstellen eines AKS-Clusters mit einem Windows Server-Knotenpool][aks-windows-cli].

Die Windows Server-Unterstützung für Knotenpools umfasst einige Einschränkungen, die Teil des Upstreamprojekts „Windows Server in Kubernetes“ sind. Weitere Informationen zu diesen Einschränkungen finden Sie unter [Aktuelle Einschränkungen für Windows Server-Knotenpools und Anwendungsworkloads in Azure Kubernetes Service (AKS)][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>Bietet AKS eine Vereinbarung zum Servicelevel?

AKS bietet SLA-Garantien als optionales Add-On-Feature mit [Betriebszeit-SLA][uptime-sla].

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Kann ich Rabatte für Azure-Reservierungen auf meine AKS-Agent-Knoten anwenden?

AKS-Agent-Knoten werden als standardmäßige Azure-VMs abgerechnet. Wenn Sie also [Azure-Reservierungen][reservation-discounts] für die von Ihnen in AKS verwendete VM-Größe erworben haben, werden diese Rabatte automatisch angewendet.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Kann ich meinen Cluster zwischen Azure-Mandanten verschieben/migrieren?

Das Verschieben Ihres AKS-Clusters zwischen Mandanten wird derzeit nicht unterstützt.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Kann ich meinen Cluster zwischen Abonnements verschieben/migrieren?

Das Verschieben von Clustern zwischen Abonnements wird derzeit nicht unterstützt.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Kann ich meine AKS-Cluster aus dem aktuellen Azure-Abonnement in ein anderes verschieben? 

Das Verschieben Ihres AKS-Clusters und der zugehörigen Ressourcen zwischen Azure-Abonnements wird nicht unterstützt.

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>Kann ich meine AKS-Cluster- oder AKS-Infrastrukturressourcen in andere Ressourcengruppen verschieben oder sie umbenennen?

Das Verschieben oder Umbenennen Ihres AKS-Clusters und der zugehörigen Ressourcen wird nicht unterstützt.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Warum dauert das Löschen meines Clusters so lange? 

Die meisten Cluster werden auf Benutzeranforderung gelöscht. In einigen Fällen kann der Vorgang zusätzliche Zeit in Anspruch nehmen oder fehlschlagen, insbesondere bei Kunden, die ihre eigene Ressourcengruppe verwenden, oder beim Löschen von RG-übergreifenden Aufgaben. Wenn Sie ein Problem mit Löschvorgängen haben, vergewissern Sie sich, dass keine Sperren für die Ressourcengruppen vorliegen, dass alle Verknüpfungen mit Ressourcen außerhalb der Ressourcengruppe aufgehoben wurden usw.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Wenn Pods/Bereitstellungen den Zustand „NodeLost“ oder „Unbekannt“ aufweisen, kann ich meinen Cluster trotzdem aktualisieren?

Dies ist möglich, aber AKS empfiehlt dies nicht. Upgrades sollten ausgeführt werden, wenn der Zustand des Clusters bekannt und fehlerfrei ist.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Kann ich ein Upgrade ausführen, wenn ein Cluster mit einem oder mehreren Knoten einen fehlerhaften Zustand aufweisen oder heruntergefahren wurden?

Nein. Löschen/Entfernen Sie alle Knoten, die den Status „Fehler“ aufweisen oder anderweitig aus dem Cluster entfernt wurden, bevor Sie das Upgrade ausführen.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Ich habe eine Clusterlöschung ausgeführt, aber der Fehler `[Errno 11001] getaddrinfo failed` wird angezeigt. 

Dies wird in der Regel dadurch verursacht, dass eine oder mehrere Netzwerksicherheitsgruppen (NSGs) noch von Benutzern verwendet werden und dem Cluster zugeordnet sind.  Entfernen Sie diese, und versuchen Sie erneut, den Löschvorgang auszuführen.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Ich habe ein Upgrade ausgeführt, aber jetzt befinden sich die Pods in Absturzschleifen, und Bereitschaftstests schlagen fehl.

Vergewissern Sie sich, dass der Dienstprinzipal nicht abgelaufen ist.  Siehe: [AKS-Dienstprinzipal](./kubernetes-service-principal.md) und [Aktualisieren der AKS-Anmeldeinformationen](./update-credentials.md).

## <a name="my-cluster-was-working-but-suddenly-cant-provision-loadbalancers-mount-pvcs-etc"></a>Mein Cluster hat funktioniert, kann aber plötzlich keine LoadBalancer bereitstellen, keine PVCs einbinden usw. 

Vergewissern Sie sich, dass der Dienstprinzipal nicht abgelaufen ist.  Siehe: [AKS-Dienstprinzipal](./kubernetes-service-principal.md) und [Aktualisieren der AKS-Anmeldeinformationen](./update-credentials.md).

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>Kann ich meinen AKS-Cluster auf 0 (null) skalieren?
Sie können einen AKS-Cluster, der gerade ausgeführt wird, [vollständig beenden](start-stop-cluster.md) und so die entsprechenden Computekosten einsparen. Außerdem können Sie [alle oder bestimmte `User`-Knotenpools auch auf 0 skalieren oder automatisch skalieren](scale-cluster.md#scale-user-node-pools-to-0) und nur die erforderliche Clusterkonfiguration beibehalten.
[Systemknotenpools](use-system-pools.md) können nicht direkt auf Null skaliert werden.

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Kann ich die VM-Skalierungsgruppen-APIs für eine manuelle Skalierung verwenden?

Nein, Skalierungsvorgänge mithilfe der VM-Skalierungsgruppen-APIs werden nicht unterstützt. Verwenden Sie die AKS-APIs (`az aks scale`).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-zero-nodes"></a>Kann ich VM-Skalierungsgruppen verwenden, um manuell auf null Knoten zu skalieren?

Nein, Skalierungsvorgänge mithilfe der VM-Skalierungsgruppen-APIs werden nicht unterstützt. Sie können die AKS-API verwenden, um eine Skalierung auf null Nicht-System-Knotenpools durchzuführen, oder den [Cluster stattdessen beenden](start-stop-cluster.md).

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Kann ich alle meine VMs beenden oder deren Zuordnung aufheben?

AKS verfügt zwar über Resilienzmechanismen, um eine solche Konfiguration zu überstehen und eine Wiederherstellung vorzunehmen. Dies ist aber keine unterstützte Konfiguration. [Beenden Sie den Cluster stattdessen](start-stop-cluster.md).

## <a name="can-i-use-custom-vm-extensions"></a>Kann ich benutzerdefinierte VM-Erweiterungen verwenden?

Der Log Analytics-Agent wird unterstützt, da es sich hierbei um eine von Microsoft verwaltete Erweiterung handelt. Andernfalls trifft dies nicht zu, AKS ist ein verwalteter Dienst. Die Bearbeitung der IaaS-Ressourcen wird nicht unterstützt. Nutzen Sie die Kubernetes-APIs und -Mechanismen zum Installieren benutzerdefinierter Komponenten. Verwenden Sie beispielsweise DaemonSets, um erforderliche Komponenten zu installieren.

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>Speichert AKS Kundendaten außerhalb der Region des Clusters?

Die Funktion zum Aktivieren des Speicherns von Kundendaten in einer einzelnen Region ist derzeit nur in der Region „Asien, Südosten“ (Singapur) des geografischen Raums „Asien-Pazifik“ verfügbar. Bei allen anderen Regionen werden Kundendaten unter „Geografien“ gespeichert.

## <a name="are-aks-images-required-to-run-as-root"></a>Sind AKS-Images für eine Ausführung als root erforderlich?

Mit Ausnahme der folgenden beiden Images müssen AKS-Images nicht als root ausgeführt werden:

- *mcr.microsoft.com/oss/kubernetes/coredns*
- *mcr.microsoft.com/azuremonitor/containerinsights/ciprod*

## <a name="what-is-azure-cni-transparent-mode-vs-bridge-mode"></a>Was ist der transparente Azure CNI-Modus im Vergleich zum Bridge-Modus?

Ab v1.2.0 verfügt Azure CNI über einen transparenten Modus als Standard für Linux-CNI-Bereitstellungen mit einem Mandanten. Der transparente Modus ersetzt den Bridge-Modus. In diesem Abschnitt werden die Unterschiede der beiden Modi und die Vorteile bzw. Einschränkungen für die Verwendung des transparenten Modus in Azure CNI erörtert.

### <a name="bridge-mode"></a>Bridge-Modus

Wie der Name vermuten lässt, erstellt der Bridge-Modus von Azure CNI „Just-in-Time“ eine L2-Bridge mit dem Namen „azure0“. Alle hostseitigen Pod-`veth`-Paarschnittstellen werden mit dieser Bridge verbunden. Die Kommunikation zwischen Pods in der VM und der verbleibende Datenverkehr durchlaufen diese Bridge. Die fragliche Bridge ist ein virtuelles Gerät der Ebene 2, das selbst keine Daten empfangen oder übertragen kann, es sei denn, Sie binden mindestens ein reales Gerät an dieses Gerät. Aus diesem Grund muss eth0 der Linux-VM in ein untergeordnetes Element für die „azure0“-Bridge konvertiert werden. Dadurch wird eine komplexe Netzwerktopologie innerhalb der Linux-VM erstellt, und als Symptom musste CNI andere Netzwerkfunktionen wie DNS-Serverupdates usw. übernehmen.

:::image type="content" source="media/faq/bridge-mode.png" alt-text="Topologie des Bridge-Modus":::

Im Folgenden finden Sie ein Beispiel dafür, wie die IP-Routeneinrichtung im Bridge-Modus aussieht. Unabhängig davon, wie viele Pods der Knoten aufweist, gibt es immer nur zwei Routen. Die erste besagt, dass der gesamte Datenverkehr mit Ausnahme des lokalen Datenverkehrs in azure0 über die Schnittstelle mit der IP „src 10.240.0.4“ (die primäre IP-Adresse des Knotens) an das Standardgateway des Subnetzes geleitet wird, und die zweite besagt, dass „10.20.x.x“ Pod-zu-Kernel ist, und der Kernel entscheidet.

```bash
default via 10.240.0.1 dev azure0 proto dhcp src 10.240.0.4 metric 100
10.240.0.0/12 dev azure0 proto kernel scope link src 10.240.0.4
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
root@k8s-agentpool1-20465682-1:/#
```

### <a name="transparent-mode"></a>Transparenter Modus
Der transparente Modus verwendet einen geradlinigen Ansatz zum Einrichten von Linux-Netzwerken. In diesem Modus ändert Azure CNI keine Eigenschaften der eth0-Schnittstelle in der Linux-VM. Dank dieses minimalen Ansatzes zum Ändern der Linux-Netzwerkeigenschaften können komplexe Eckfallprobleme reduziert werden, die für Cluster im Bridge-Modus auftreten können. Im transparenten Modus erstellt Azure CNI hostseitige Pod-`veth`-Paarschnittstellen, die dem Hostnetzwerk hinzugefügt werden. Die Kommunikation zwischen Pods im Cluster erfolgt über IP-Routen, die CNI hinzufügt. Im Wesentlichen erfolgt die Kommunikation zwischen Pods über Ebene 3, und Poddatenverkehr wird durch L3-Routingregeln weitergeleitet.

:::image type="content" source="media/faq/transparent-mode.png" alt-text="Topologie des transparenten Modus":::

Im Folgenden finden Sie ein Beispiel für eine IP-Routeneinrichtung des transparenten Modus. An die Schnittstelle jedes Pods wird eine statische Route angefügt, sodass der Datenverkehr mit der Ziel-IP als Pod direkt an die hostseitige `veth`-Paarschnittstelle des Pods gesendet wird.

```bash
10.240.0.216 dev azv79d05038592 proto static
10.240.0.218 dev azv8184320e2bf proto static
10.240.0.219 dev azvc0339d223b9 proto static
10.240.0.222 dev azv722a6b28449 proto static
10.240.0.223 dev azve7f326f1507 proto static
10.240.0.224 dev azvb3bfccdd75a proto static
168.63.129.16 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
169.254.169.254 via 10.240.0.1 dev eth0 proto dhcp src 10.240.0.4 metric 100
172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 linkdown
```

### <a name="benefits-of-transparent-mode"></a>Vorteile des transparenten Modus

- Dieser Modus bietet eine Entschärfung für eine parallele `conntrack`-DNS-Racebedingung und die Vermeidung von Problemen durch DNS-Latenz von 5 Sekunden, ohne dass DNS des lokalen Knotens eingerichtet werden muss (Sie können aus Leistungsgründen DNS des lokalen Knotens ggf. trotzdem verwenden).
- Er eliminiert die anfängliche 5-sekündige DNS-Latenz, die der CNI-Bridge-Modus heute aufgrund der Bridge-Einrichtung verursacht, die „Just-in-Time“ erfolgt.
- Einer der Eckfälle im Bridge-Modus ist, dass Azure CNI die benutzerdefinierten DNS-Server-Listen, die Benutzer dem VNET oder der NIC hinzufügen, nicht ständig aktualisieren kann. Dies führt dazu, dass CNI nur die erste Instanz der DNS-Serverliste auswählt. Dieses Problem wird im transparenten Modus gelöst, weil CNI keine eth0-Eigenschaften ändert. Weitere Informationen finden Sie [hier](https://github.com/Azure/azure-container-networking/issues/713).
- Dieser Modus bietet eine bessere Verarbeitung von UDP-Datenverkehr und Entschärfung für UDP-Überflutung bei einem Timeout von ARP. Wenn die Bridge im Bridge-Modus eine MAC-Adresse des Ziel-Pods bei der Kommunikation zwischen Pods in der VM nicht kennt, führt dies zu einem Paketsturm an alle Ports. Dieses Problem ist im transparenten Modus gelöst, da sich keine L2-Geräte im Pfad befinden. Weitere Informationen finden Sie [hier](https://github.com/Azure/azure-container-networking/issues/704).
- Der transparente Modus ist in Bezug auf Durchsatz und Latenz bei der Kommunikation zwischen Pods in der VM im Vergleich zum Bridge-Modus leistungsfähiger.


<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: ./windows-faq.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/
