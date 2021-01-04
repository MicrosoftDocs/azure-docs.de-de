---
title: Häufig gestellte Fragen zu Azure Red Hat OpenShift
description: Enthält Antworten auf häufige gestellte Fragen zu Microsoft Azure Red Hat OpenShift.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 07/31/2020
ms.openlocfilehash: 3a474228776c689dbbd6f15ddd926f29383400ce
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964710"
---
# <a name="azure-red-hat-openshift-faq"></a>Häufig gestellte Fragen zu Azure Red Hat OpenShift

Dieser Artikel beantwortet häufig gestellte Fragen zu Microsoft Azure Red Hat OpenShift.

## <a name="installation-and-upgrade"></a>Installation und Upgrade

### <a name="which-azure-regions-are-supported"></a>Welche Azure-Regionen werden unterstützt?

Eine Liste der unterstützten Regionen für Azure Red Hat OpenShift 4.x finden Sie unter [Verfügbare Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all).

Eine Liste der unterstützten Regionen für Azure Red Hat OpenShift 3.11 finden Sie unter [Verfügbare Produkte nach Region](supported-resources.md#azure-regions).

### <a name="what-virtual-machine-sizes-can-i-use"></a>Welche Größen von virtuellen Computern kann ich verwenden?

Eine Liste der unterstützten Größen virtueller Computer für Azure Red Hat OpenShift 4 finden Sie unter [Unterstützte Ressourcen für Azure Red Hat OpenShift 4](support-policies-v4.md).

Eine Liste der unterstützten Größen virtueller Computer für Azure Red Hat OpenShift 3.11 finden Sie unter [Unterstützte Ressourcen für Azure Red Hat OpenShift 3.11](supported-resources.md).

### <a name="what-is-the-maximum-number-of-pods-in-an-azure-red-hat-openshift-cluster--what-is-the-maximum-number-of-pods-per-node-in-azure-red-hat-openshift"></a>Was ist die maximale Anzahl von Pods in einem Azure Red Hat OpenShift-Cluster?  Was ist die maximale Anzahl von Pods pro Knoten in Azure Red Hat OpenShift?

Die tatsächliche Anzahl der unterstützten Pods hängt von den Arbeitsspeicher-, CPU- und Speicheranforderungen einer Anwendung ab.

Azure Red Hat OpenShift 4.x hat ein Limit von 250 Pods pro Knoten und ist auf 100 Computeknoten begrenzt. Damit wird die maximale Anzahl der in einem Cluster unterstützten Pods auf 250&times;100 = 25.000 begrenzt.

Azure Red Hat OpenShift 3.11 hat ein Limit von 50 Pods pro Knoten und ist auf 20 Computeknoten begrenzt. Damit wird die maximale Anzahl der in einem Cluster unterstützten Pods auf 50&times;20 = 1.000 begrenzt.

### <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Kann ein Cluster Computeknoten über mehrere Azure-Regionen hinweg besitzen?

Nein. Alle Knoten in einem Azure Red Hat OpenShift-Cluster müssen aus derselben Azure-Region stammen.

### <a name="can-a-cluster-be-deployed-across-multiple-availability-zones"></a>Kann ein Cluster über mehrere Verfügbarkeitszonen hinweg bereitgestellt werden?

Ja. Dies erfolgt automatisch, wenn Ihr Cluster in einer Azure-Region bereitgestellt wird, die Verfügbarkeitszonen unterstützt. Weitere Informationen finden Sie unter [Verfügbarkeitszonen](../availability-zones/az-overview.md#availability-zones).

### <a name="are-control-plane-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Werden Knoten der Steuerungsebene abstrahiert, weil sie zu Azure Kubernetes Service (AKS) gehören?

Nein. Alle Ressourcen, einschließlich der Clustermasterknoten, werden in Ihrem Kundenabonnement ausgeführt. Diese Typen von Ressourcen werden in einer „Nur-Lesen“-Ressourcengruppe platziert.

### <a name="does-the-cluster-reside-in-a-customer-subscription"></a>Befindet sich der Cluster in einem Kundenabonnement? 

Die verwaltete Azure-Anwendung befindet sich in einer gesperrten Ressourcengruppe im Kundenabonnement. Kunden können Objekte in dieser Ressourcengruppe anzeigen, aber nicht ändern.

### <a name="is-there-any-element-in-azure-red-hat-openshift-shared-with-other-customers-or-is-everything-independent"></a>Gibt es ein Element in Azure Red Hat OpenShift, das mit anderen Kunden gemeinsam genutzt wird? Oder ist alles unabhängig?

Jeder Azure Red Hat OpenShift-Cluster ist einem bestimmten Kunden zugeordnet und befindet sich im Abonnement des Kunden. 

### <a name="are-infrastructure-nodes-available"></a>Sind Infrastrukturknoten verfügbar?

Auf Azure Red Hat OpenShift 4.x-Clustern sind Infrastrukturknoten derzeit nicht verfügbar.

Auf Azure Red Hat OpenShift 3.11-Clustern sind Infrastrukturknoten standardmäßig enthalten.

## <a name="how-do-i-handle-cluster-upgrades"></a>Wie gehe ich mit Clusterupgrades um?

Informationen zu Upgrades, Wartung und unterstützten Versionen finden Sie unter [Unterstützung des Lebenszyklus für Azure Red Hat OpenShift 4](support-lifecycle.md).

### <a name="how-will-the-host-operating-system-and-openshift-software-be-updated"></a>Wie werden Hostbetriebssystem und OpenShift-Software aktualisiert?

Die Hostbetriebssysteme und die OpenShift-Software werden aktualisiert, wenn Azure Red Hat OpenShift Nebenreleaseversionen und Patches von der OpenShift-Upstreamcontainerplattform anwendet.

### <a name="whats-the-process-to-reboot-the-updated-node"></a>Wie sieht der Prozess für einen Neustart des aktualisierten Knotens aus?

Knoten werden im Rahmen eines Upgrades neu gestartet.

## <a name="cluster-operations"></a>Clustervorgänge

### <a name="can-i-use-prometheus-to-monitor-my-applications"></a>Kann ich Prometheus zum Überwachen meiner Anwendungen verwenden?

Prometheus ist vorinstalliert und für Azure Red Hat OpenShift 4.x-Cluster konfiguriert. Erfahren Sie mehr über die [Clusterüberwachung](https://docs.openshift.com/container-platform/3.11/install_config/prometheus_cluster_monitoring.html).

Für Azure Red Hat OpenShift 3.11-Cluster können Sie Prometheus in Ihrem Namespace bereitstellen und Anwendungen in Ihrem Namespace überwachen. Weitere Informationen finden Sie unter [Bereitstellen einer Prometheus-Instanz in einem Azure Red Hat OpenShift-Cluster](howto-deploy-prometheus.md).

### <a name="can-i-use-prometheus-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Kann ich Prometheus zum Überwachen von Metriken zur Clusterintegrität und -kapazität verwenden?

In Azure Red Hat OpenShift 4.x: Ja.

In Azure Red Hat OpenShift 3.11: Nein.

### <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Können Protokolle von zugrunde liegenden virtuellen Computern an ein Protokollanalysesystem eines Kunden gestreamt werden?

Protokolle von zugrundeliegenden virtuellen Computern werden vom verwalteten Dienst verarbeitet und für Kunden nicht verfügbar gemacht.

### <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-azure-red-hat-openshift-cluster"></a>Wie kann ein Kunde Zugriff auf Metriken wie CPU/Arbeitsspeicher auf Knotenebene erhalten, um Aktionen wie Skalieren, Debuggen von Problemen usw. auszuführen? Ich kann „kubectl top“ anscheinend nicht auf einem Azure Red Hat OpenShift-Cluster ausführen.

Für Azure Red Hat OpenShift 4.x-Cluster enthält die OpenShift-Webkonsole alle Metriken auf Knotenebene. Weitere Informationen finden Sie in der Red Hat-Dokumentation unter [Anzeigen von Clusterinformationen](https://docs.openshift.com/aro/4/web_console/using-dashboard-to-get-cluster-information.html).

Für Azure Red Hat OpenShift 3.11-Cluster können Kunden auf CPU-/Arbeitsspeichermetriken auf Knotenebene zugreifen, indem sie den Befehl `oc adm top nodes` oder `kubectl top nodes` zusammen mit der Clusterrolle „customer-admin“ verwenden. Kunden können auch mit dem Befehl `oc adm top pods` oder `kubectl top pods` auf die CPU-/Arbeitsspeichermetriken von `pods` zugreifen.

### <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Wie werden, wenn die Bereitstellung hochskaliert wird, Azure-Fehlerdomänen in der Pod-Platzierung zugeordnet, damit sichergestellt ist, dass nicht alle Pods für einen Dienst durch einen Fehler in einer einzelnen Fehlerdomäne lahmgelegt werden?

Es gibt standardmäßig fünf Fehlerdomänen, wenn VM-Skalierungsgruppen in Azure verwendet werden. Jede Instanz eines virtuellen Computers in einer Skalierungsgruppe wird in einer dieser Fehlerdomänen platziert. Dadurch ist sichergestellt, dass Anwendungen, die auf den Computeknoten in einem Cluster bereitgestellt werden, in separaten Fehlerdomänen platziert werden.

Weitere Informationen hierzu finden Sie unter [Auswählen der richtigen Anzahl von Fehlerdomänen für VM-Skalierungsgruppen](../virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains.md).

### <a name="is-there-a-way-to-manage-pod-placement"></a>Gibt es eine Möglichkeit, die Pod-Platzierung zu verwalten?

Wenn Kunden die Rolle „customer-admin“ zugewiesen wurde, können sie Knoten abrufen und sich Bezeichnungen ansehen. Dies bietet eine Möglichkeit, einen beliebigen virtuellen Computer in der Skalierungsgruppe als Ziel festzulegen.

Die Verwendung bestimmter Bezeichnungen muss sehr vorsichtig erfolgen:

- Der Hostname darf nicht verwendet werden. Der Hostname wird häufig mit Upgrades und Updates ausgetauscht und wird garantiert geändert.
- Wenn der Kunde bestimmte Bezeichnungen oder eine Bereitstellungsstrategie benötigt, ließe sich dies erreichen, würde aber technische Anstrengungen voraussetzen und wird derzeit nicht unterstützt.

Weitere Informationen finden Sie unter [Steuern der Pod-Platzierung](https://docs.openshift.com/aro/4/nodes/scheduling/nodes-scheduler-about.html).

### <a name="is-the-image-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Ist die Imageregistrierung extern verfügbar, damit ich Tools wie Jenkins verwenden kann?

Für 4.x-Cluster müssen Sie eine sichere Registrierung verfügbar machen und die Authentifizierung konfigurieren. Weitere Informationen finden Sie in der Red Hat-Dokumentation zu den folgenden Themen:

- [Verfügbar machen einer Registrierung](https://docs.openshift.com/aro/4/registry/securing-exposing-registry.html)
- [Zugreifen auf die Registrierung](https://docs.openshift.com/aro/4/registry/accessing-the-registry.html)

Für Cluster der Version 3.11 ist die Docker-Imageregistrierung verfügbar. Die Docker-Registrierung ist über `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` verfügbar. Sie können auch die Azure Container Registry verwenden.

## <a name="networking"></a>Netzwerk

### <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Kann ich einen Cluster in einem vorhandenen virtuellen Netzwerk bereitstellen?

In Clustern der Version 4.x können Sie einen Cluster in einem vorhandenen virtuellen Netzwerk bereitstellen.

In Clustern der Version 3.11 können Sie einen Cluster nicht in einem vorhandenen virtuellen Netzwerk bereitstellen. Sie können einen Azure Red Hat OpenShift 3.11-Cluster über Peering mit einem vorhandenen virtuellen Netzwerk verbinden.

### <a name="is-cross-namespace-networking-supported"></a>Wird namespaceübergreifender Netzwerkdatenverkehr unterstützt?

Kunden- und einzelne Projektadministratoren können namespaceübergreifenden Netzwerkdatenverkehr pro Projekt mithilfe von `NetworkPolicy`-Objekten anpassen (einschließlich seiner Untersagung).

### <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Ich versuche, per Peering eine Verbindung mit einem virtuellen Netzwerk in einem anderen Abonnement herzustellen, erhalte jedoch den Fehler „Fehler beim Abrufen des VNet-CDIRs“.

Stellen Sie in dem Abonnement mit dem virtuellen Netzwerk sicher, dass der `Microsoft.ContainerService`-Anbieter mit folgendem Befehl registriert wird: `az provider register -n Microsoft.ContainerService --wait`

### <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Können IP-Adressbereiche für die Bereitstellung im privaten VNet angegeben werden, um Konflikte mit anderen Unternehmens-VNets zu vermeiden, nachdem Peering erfolgt ist?

In Clustern der Version 4.x können Sie Ihre eigenen IP-Adressbereiche angeben.

In Clustern der Version 3.11 unterstützt Azure Red Hat OpenShift das VNet-Peering und ermöglicht es dem Kunden, ein VNet, mit dem Peering erfolgt, und einen VNet-CIDR bereitzustellen, in dem das OpenShift-Netzwerk funktioniert.

Das von Azure Red Hat OpenShift erstellte VNet wird geschützt und akzeptiert keine Konfigurationsänderungen. Das VNet, für das Peering erfolgt ist, wird vom Kunden gesteuert und befindet sich in dessen Abonnement.

### <a name="is-the-software-defined-network-module-configurable"></a>Ist das softwaredefinierte Netzwerkmodul konfigurierbar?

Das softwaredefinierte Netzwerk ist `openshift-ovs-networkpolicy` und nicht konfigurierbar.

### <a name="what-azure-load-balancer-is-used-by-azure-red-hat-openshift--is-it-standard-or-basic-and-is-it-configurable"></a>Welcher Azure Load Balancer wird von Azure Red Hat OpenShift verwendet?  Ist es die Standard- oder Basic-Variante, und ist diese konfigurierbar?

Azure Red Hat OpenShift verwendet die Standard-Variante von Azure Load Balancer, und diese ist nicht konfigurierbar.

## <a name="permissions"></a>Berechtigungen

### <a name="can-an-admin-manage-users-and-quotas"></a>Kann ein Administrator Benutzer und Kontingente verwalten?

Ja. Ein Azure Red Hat OpenShift-Administrator kann zusätzlich zum Zugriff auf alle von Benutzern erstellte Projekte Benutzer und Kontingente verwalten.

### <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Kann ich einen Cluster auf nur bestimmte Azure AD-Benutzer beschränken?

Ja. Sie können einschränken, welche Azure AD-Benutzer sich bei einem Cluster anmelden können, indem Sie die Azure AD-Anwendung konfigurieren. Detaillierte Informationen zu diesem Thema finden Sie unter [Vorgehensweise: Einschränken Ihrer App auf eine Gruppe von Benutzern](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md).

### <a name="can-i-restrict-users-from-creating-projects"></a>Kann ich das Erstellen von Projekten durch Benutzer einschränken?

Ja. Melden Sie sich bei Ihrem Cluster als Administrator an, und führen Sie den folgenden Befehl aus:

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

Weitere Informationen finden Sie in der OpenShift-Dokumentation unter dem Thema zum Deaktivieren der eigenständigen Bereitstellung für Ihre Clusterversion:

- [Deaktivieren der eigenständigen Bereitstellung in Clustern der Version 4.3](https://docs.openshift.com/aro/4/applications/projects/configuring-project-creation.html#disabling-project-self-provisioning_configuring-project-creation)
- [Deaktivieren der eigenständigen Bereitstellung in Clustern der Version 3.11](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)

### <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Welche UNIX-Rechte (in IaaS) sind für Master/Infra/App-Knoten verfügbar?

Für Cluster der Version 4.x ist der Knotenzugriff über die Clusteradministratorrolle verfügbar. Weitere Informationen finden Sie in der [Übersicht über Kubernetes RBAC](https://docs.openshift.com/container-platform/4.3/authentication/using-rbac.html).

Bei Clustern der Version 3.11 ist der Knotenzugriff unzulässig.

### <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Welche OCP-Rechte haben wir? Clusteradministrator? Projektadministrator?

Bei Clustern der Version 4.x ist die Clusteradministratorrolle verfügbar. Weitere Informationen finden Sie in der [Übersicht über Kubernetes RBAC](https://docs.openshift.com/container-platform/4.3/authentication/using-rbac.html).

Informationen zu Clustern der Version 3.11 finden Sie in der Übersicht über die [Clusterverwaltung](https://docs.openshift.com/aro/admin_guide/index.html).

### <a name="which-identity-providers-are-available"></a>Welche Identitätsanbieter sind verfügbar?

Für Cluster der Version 4.x konfigurieren Sie Ihren eigenen Identitätsanbieter. Weitere Informationen finden Sie in der Red Hat-Dokumentation zum Thema [Konfigurieren von Identitätsanbietern](https://docs.openshift.com/aro/4/authentication/identity_providers/configuring-ldap-identity-provider.html).

Für Cluster der Version 3.11 können Sie die Azure AD-Integration verwenden. 

## <a name="storage"></a>Storage

### <a name="is-data-on-my-cluster-encrypted"></a>Werden Daten in meinem Cluster verschlüsselt?

Standardmäßig werden ruhende Daten verschlüsselt. Die Azure Storage-Plattform verschlüsselt Ihre Daten automatisch vor dem dauerhaften Ablegen und entschlüsselt sie vor dem Abrufen. Weitere Informationen finden Sie unter [Azure-Speicherdienstverschlüsselung für ruhende Daten](../storage/common/storage-service-encryption.md).

### <a name="is-data-stored-in-etcd-encrypted-on-azure-red-hat-openshift"></a>Werden in etcd gespeicherte Daten unter Azure Red Hat OpenShift verschlüsselt?

Bei Azure Red Hat OpenShift 4-Clustern werden die Daten standardmäßig nicht verschlüsselt, aber Sie haben die Möglichkeit, die Verschlüsselung zu aktivieren. Weitere Informationen finden Sie im Leitfaden zum [Verschlüsseln von etcd](https://docs.openshift.com/container-platform/4.3/authentication/encrypting-etcd.html).

Bei Clustern der Version 3.11 werden die Daten nicht auf der etcd-Ebene verschlüsselt. Die Option zum Aktivieren der Verschlüsselung wird derzeit nicht unterstützt. OpenShift unterstützt diese Funktionalität, aber es sind technische Anstrengungen erforderlich, um sie geplant zu implementieren. Die Daten werden auf Datenträgerebene verschlüsselt. Weitere Informationen hierzu finden Sie unter [Encrypting Data at Datastore Layer](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html) (Verschlüsseln von Daten auf Datenspeicherebene).

### <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Können wir eine permanente Speicherlösung auswählen, z. B. OCS? 

Für Cluster der Version 4.x ist Azure Disk (Premium_LRS) als Standardspeicherklasse konfiguriert. Weitere Speicheranbieter und Einzelheiten zur Konfiguration (einschließlich Azure Files) finden Sie in der Red Hat-Dokumentation zum [permanenten Speicher](https://docs.openshift.com/aro/4/storage/understanding-persistent-storage.html).

Für Cluster der Version 3.11 werden standardmäßig zwei Speicherklassen bereitgestellt: eine für Azure Disk (Premium_LRS) und eine für Azure Files.

## <a name="does-aro-store-any-customer-data-outside-of-the-clusters-region"></a>Speichert ARO Kundendaten außerhalb der Region des Clusters?

Nein. Alle in einem ARO-Cluster erstellten Daten verbleiben innerhalb der Region des Clusters.
