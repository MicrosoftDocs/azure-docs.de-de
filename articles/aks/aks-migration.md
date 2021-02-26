---
title: Migrieren zu Azure Kubernetes Service (AKS)
description: Migrieren zu Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 02/25/2020
ms.custom: mvc
ms.openlocfilehash: 5881d03603002cc8d5bef1680083f6b4145bc77f
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526686"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Migrieren zu Azure Kubernetes Service (AKS)

Dieser Artikel unterstützt Sie beim Planen und Ausführen einer erfolgreichen Migration zu Azure Kubernetes Service (AKS). Damit Sie wichtige Entscheidungen leichter treffen können, finden Sie in diesem Handbuch Details zur aktuellen empfohlenen Konfiguration für AKS. Dieser Artikel behandelt nicht jedes Szenario, und gegebenenfalls enthält der Artikel Links zu detaillierteren Informationen zur Planung einer erfolgreichen Migration.

Dieses Dokument unterstützt die folgenden Szenarien:

* Migrieren eines von [Verfügbarkeitsgruppen](../virtual-machines/windows/tutorial-availability-sets.md) unterstützten AKS-Clusters zu [Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md)
* Migrieren eines AKS-Clusters zur Verwendung eines [Lastenausgleichs mit einer Standard-SKU](./load-balancer-standard.md)
* Migrieren von [Azure Container Service (ACS) (Einstellung 31. Januar 2020)](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) zu AKS
* Migrieren von [AKS-Engine](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) zu AKS
* Migrieren von nicht auf Azure basierenden Kubernetes-Clustern zu AKS
* Verschieben von vorhandenen Ressourcen in eine andere Region

Stellen Sie bei der Migration sicher, dass sich Ihre Kubernetes-Zielversion im unterstützten Fenster für AKS befindet. Wird eine ältere Version verwendet, die möglicherweise nicht innerhalb des unterstützten Bereichs liegt, ist eine Aktualisierung der Versionen erforderlich, um von AKS unterstützt zu werden. Weitere Informationen finden Sie unter [von AKS unterstützte Kubernetes-Versionen](./supported-kubernetes-versions.md).

Wenn Sie zu einer neueren Version von Kubernetes migrieren, überprüfen Sie die [Richtlinie zur Unterstützung der Kubernetes-Version und Versionsabweichung](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions).

Abhängig von Ihrem Szenario können verschiedene Open-Source-Tools bei der Migration verwendet werden:

* [Velero](https://velero.io/) (erfordert Kubernetes 1.7+)
* [Azure Kube CLI-Erweiterung](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

In diesem Artikel werden die Details zur Migration für folgende Szenarien zusammengefasst:

> [!div class="checklist"]
> * AKS mit Load Balancer Standard und Virtual Machine Scale Sets
> * Vorhandene angefügte Azure-Dienste
> * Sicherstellen gültiger Kontingente
> * Hohe Verfügbarkeit und Geschäftskontinuität
> * Überlegungen zu zustandslosen Anwendungen
> * Überlegungen zu zustandsbehafteten Anwendungen
> * Bereitstellung Ihrer Clusterkonfiguration

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS mit Load Balancer Standard und Virtual Machine Scale Sets

AKS ist ein verwalteter Dienst, der einzigartige Funktionen mit geringerem Verwaltungsaufwand bietet. Da es sich um einen verwalteten Dienst handelt, müssen Sie aus einer Reihe von [Regionen](./quotas-skus-regions.md) auswählen, die von AKS unterstützt werden. Die Umstellung Ihres vorhandenen Clusters zu AKS macht möglicherweise Änderungen an Ihren vorhandenen Anwendungen erforderlich, damit diese auf der verwalteten AKS-Steuerungsebene fehlerfrei bleiben.

Wir empfehlen die Verwendung von AKS-Clustern, die durch [Virtual Machine Scale Sets](../virtual-machine-scale-sets/index.yml) und den [Azure Load Balancer Standard](./load-balancer-standard.md) unterstützt werden, um sicherzustellen, dass Sie Features wie [mehrfache Knotenpools](./use-multiple-node-pools.md), [Verfügbarkeitszonen](../availability-zones/az-overview.md), [autorisierte IP-Bereiche](./api-server-authorized-ip-ranges.md), [automatische Clusterskalierung](./cluster-autoscaler.md), [Azure Policy für AKS](../governance/policy/concepts/policy-for-kubernetes.md) und andere neue Features erhalten, sobald sie veröffentlicht werden.

Von [Verfügbarkeitsgruppen für virtuelle Computer](../virtual-machines/availability.md#availability-sets) unterstützten AKS-Clustern werden viele dieser Features nicht unterstützt.

Im folgenden Beispiel wird ein AKS-Cluster mit einem einzigen Knotenpool erstellt, der von einer VM-Skalierungsgruppe unterstützt wird. Es wird eine Load Balancer Standard-Instanz verwendet. Außerdem wird die automatische Clusterskalierung im Knotenpool für den Cluster aktiviert und die Mindestanzahl von Knoten auf *1* sowie die maximale Knotenanzahl auf *3* festgelegt:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## <a name="existing-attached-azure-services"></a>Vorhandene angefügte Azure-Dienste

Beim Migrieren von Clustern haben Sie möglicherweise externe Azure-Dienste angefügt. Diese erfordern keine Neuerstellung von Ressourcen, aber die Verbindungen zwischen vorherigen und neuen Clustern müssen aktualisiert werden, um die Funktionalität zu gewährleisten.

* Azure Container Registry
* Log Analytics
* Application Insights
* Traffic Manager
* Speicherkonto
* Externe Datenbanken

## <a name="ensure-valid-quotas"></a>Sicherstellen gültiger Kontingente

Da während der Migration zusätzliche VMs in Ihrem Abonnement bereitgestellt werden, müssen Sie überprüfen, ob Ihre Kontingente und Limits für diese Ressourcen ausreichen. Möglicherweise müssen Sie eine Erhöhung des [vCPU-Kontingents](../azure-portal/supportability/per-vm-quota-requests.md) anfordern.

Möglicherweise müssen Sie eine Erhöhung der [Netzwerkkontingente](../azure-portal/supportability/networking-quota-requests.md) anfordern, um sicherzustellen, dass Sie die IPs nicht erschöpfen. Weitere Informationen finden Sie unter [Netzwerke und IP-Adressbereiche für AKS](./configure-kubenet.md).

Weitere Informationen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md). Um Ihre aktuellen Kontingente zu überprüfen, wechseln Sie im Azure-Portal zum [Blatt „Abonnements“](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), und wählen Sie Ihr Abonnement und dann **Nutzung + Kontingente** aus.

## <a name="high-availability-and-business-continuity"></a>Hohe Verfügbarkeit und Geschäftskontinuität

Wenn Ihre Anwendung keine Ausfallzeiten bewältigen kann, müssen Sie bewährte Methoden für Migrationsszenarios mit hoher Verfügbarkeit befolgen.  Bewährte Methoden für komplexe Geschäftskontinuitätsplanung, Notfallwiederherstellung und Maximierung der Betriebszeit gehen über den Rahmen dieses Dokuments hinaus.  Weitere Informationen finden Sie unter [Best Practices für Geschäftskontinuität und Notfallwiederherstellung in Azure Kubernetes Service (AKS)](./operator-best-practices-multi-region.md).

Für komplexe Anwendungen führen Sie die Migration typischerweise nach und nach durch, statt alle Anwendungen gleichzeitig zu migrieren. Dies bedeutet, dass die alte und neue Umgebung möglicherweise über das Netzwerk kommunizieren müssen. Anwendungen, in denen zuvor `ClusterIP`-Dienste für die Kommunikation genutzt wurden, müssen möglicherweise als Typ `LoadBalancer` verfügbar gemacht und in geeigneter Weise geschützt werden.

Um die Migration durchzuführen, müssen Clients auf die neuen Dienste zeigen, die in AKS ausgeführt werden. Es wird empfohlen, den Datenverkehr umzuleiten, indem Sie den DNS aktualisieren, damit auf den Load Balancer verwiesen wird, der vor Ihrem AKS-Cluster platziert ist.

[Azure Traffic Manager](../traffic-manager/index.yml) kann Kundendatenverkehr an den gewünschten Kubernetes-Cluster und die gewünschte Anwendungsinstanz weiterleiten.  Traffic Manager ist ein DNS-basierter Lastenausgleichsdienst, der Datenverkehr auf mehrere Regionen verteilen kann.  Um optimale Leistungs- und Redundanzwerte zu erzielen, leiten Sie den gesamten Anwendungsdatenverkehr über Traffic Manager an Ihren AKS-Cluster.  In einer Bereitstellung mit mehreren Clustern sollten Kunden eine Verbindung mit dem DNS-Namen der Traffic Manager-Instanz herstellen, die auf die Dienste in den einzelnen AKS-Clustern verweist. Definieren Sie diese Dienste mithilfe von Traffic Manager-Endpunkten. Jeder Endpunkt stellt die *IP-Adresse des Lastenausgleichsdiensts* dar. Leiten Sie mit dieser Konfiguration Netzwerkdatenverkehr vom Traffic Manager-Endpunkt in einer Region zum Endpunkt in einer anderen Region.

![AKS mit Traffic Manager](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Azure Front Door Service](../frontdoor/front-door-overview.md) ist eine weitere Option für das Routing von Datenverkehr für AKS-Cluster.  Über Azure Front Door Service können Sie das globale Routing für Ihren Webdatenverkehr definieren, verwalten und überwachen, indem Sie es für beste Leistung und sofortiges globales Failover bei Hochverfügbarkeit optimieren. 

### <a name="considerations-for-stateless-applications"></a>Überlegungen zu zustandslosen Anwendungen

Die Migration von zustandslosen Anwendung ist der einfachste Fall. Wenden Sie Ihre Ressourcendefinitionen (YAML oder Helm) auf den neuen Cluster an, und überprüfen Sie, ob alles wie erwartet funktioniert. Anschließend leiten Sie den Datenverkehr um, damit der neue Cluster aktiviert wird.

### <a name="considerations-for-stateful-applications"></a>Überlegungen zu zustandsbehafteten Anwendungen

Planen Sie die Migration von zustandsbehafteten Anwendungen sorgfältig, um Datenverlust oder eine unerwartete Downtime zu vermeiden.

Wenn Sie Azure Files verwenden, können Sie die Dateifreigabe als Volume in den neuen Cluster einbinden:
* [Einbinden statischer Azure Files-Instanzen als Volume](./azure-files-volume.md#mount-the-file-share-as-a-volume)

Wenn Sie Azure Managed Disks verwenden, können Sie den Datenträger nur einbinden, wenn er nicht an einen virtuellen Computer angefügt ist:
* [Einbinden statischer Azure Disk-Instanzen als Volume](./azure-disk-volume.md#mount-disk-as-volume)

Wenn keiner dieser Ansätze funktioniert, können Sie Sicherungs- und Wiederherstellungsoptionen verwenden:
* [Velero on Azure (Velero in Azure)](https://github.com/vmware-tanzu/velero-plugin-for-microsoft-azure/blob/master/README.md)

#### <a name="azure-files"></a>Azure Files

Im Gegensatz zu Datenträgern können Azure Files-Instanzen gleichzeitig auf mehreren Hosts bereitgestellt werden. In Azure und in Kubernetes ist es möglich, einen Pod in Ihrem AKS-Cluster zu erstellen, der weiterhin von Ihrem ACS-Cluster verwendet wird. Um Datenverlust und unerwartetes Verhalten zu vermeiden, müssen Sie sicherstellen, dass die Cluster nicht gleichzeitig in dieselben Dateien schreiben.

Wenn Ihre Anwendung mehrere Replikate hosten kann, die auf dieselbe Dateifreigabe verweisen, führen Sie die Schritte für die Migration zustandsloser Anwendungen aus, und stellen Sie Ihre YAML-Definitionen auf Ihrem neuen Cluster bereit. Andernfalls umfasst ein möglicher Ansatz für die Migration die folgenden Schritte:

* Überprüfen Sie, ob Ihre Anwendung ordnungsgemäß funktioniert.
* Verweisen Sie Ihren Livedatenverkehr auf den neuen AKS-Cluster.
* Trennen Sie den alten Cluster.

Wenn Sie mit einer leeren Freigabe beginnen und eine Kopie der Quelldaten erstellen möchten, können Sie die [`az storage file copy`](/cli/azure/storage/file/copy)-Befehle zum Migrieren Ihrer Daten verwenden.


#### <a name="migrating-persistent-volumes"></a>Migration persistenter Volumes

Wenn Sie vorhandene persistente Volumes zu AKS migrieren, führen Sie im Allgemeinen folgende Schritte aus:

* Stilllegen von Schreibvorgängen in die Anwendung (Dieser Schritt ist optional und erfordert eine Ausfallzeit.)
* Erstellen von Momentaufnahmen der Datenträger
* Erstellen neuer verwalteter Datenträger aus den Momentaufnahmen
* Erstellen von persistenten Volumes in AKS
* Aktualisieren der Podspezifikationen zur [Verwendung vorhandener Volumes](./azure-disk-volume.md) anstelle von PersistentVolumeClaims (statische Bereitstellung)
* Stellen Sie Ihre Anwendung in AKS bereit.
* Überprüfen Sie, ob Ihre Anwendung ordnungsgemäß funktioniert.
* Verweisen Sie Ihren Livedatenverkehr auf den neuen AKS-Cluster.

> [!IMPORTANT]
> Wenn Sie die Schreibvorgänge nicht stilllegen, müssen Sie Daten zur neuen Bereitstellung replizieren. Andernfalls fehlen die Daten, die nach dem Erstellen der Datenträgermomentaufnahmen geschrieben wurden.

Mit einigen Open-Source-Tools können Sie verwaltete Datenträger erstellen und Volumes zwischen Kubernetes-Clustern migrieren:

* [Azure CLI Disk Copy-Erweiterung:](https://github.com/noelbundick/azure-cli-disk-copy-extension) Kopieren und Konvertieren von Datenträgern zwischen Ressourcengruppen und Azure-Regionen
* [Azure Kube CLI-Erweiterung:](https://github.com/yaron2/azure-kube-cli) Auflisten von ACS Kubernetes-Volumes und Migrieren der Volumes zu einem AKS-Cluster


### <a name="deployment-of-your-cluster-configuration"></a>Bereitstellung Ihrer Clusterkonfiguration

Es empfiehlt sich, die Verwendung Ihrer vorhandenen CI/CD-Pipeline (Continuous Integration und Continuous Deliver), um eine als funktionierend bekannten Konfiguration in AKS bereitzustellen. Sie können Azure Pipelines verwenden, um [Ihre Anwendungen für AKS zu erstellen und bereitzustellen](/azure/devops/pipelines/ecosystems/kubernetes/aks-template). Klonen Sie Ihre vorhandenen Bereitstellungsaufgaben, und stellen Sie sicher, dass `kubeconfig` auf den neuen AKS-Cluster zeigt.

Wenn dies nicht möglich ist, exportieren Sie die Ressourcendefinitionen aus Ihrem vorhandenen Kubernetes-Cluster, und wenden Sie sie anschließend auf AKS an. Sie können `kubectl` zum Exportieren von Objekten verwenden.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

### <a name="moving-existing-resources-to-another-region"></a>Verschieben von vorhandenen Ressourcen in eine andere Region

Möglicherweise möchten Sie Ihren AKS-Cluster in eine [andere von AKS unterstützte Region verschieben][region-availability]. Es wird empfohlen, einen neuen Cluster in der anderen Region zu erstellen und dann Ihre Ressourcen und Anwendungen im neuen Cluster bereitzustellen. Wenn außerdem Dienste wie [Azure Dev Spaces][azure-dev-spaces] auf Ihrem AKS-Cluster ausgeführt werden, müssen Sie auch diese Dienste in Ihrem Cluster in der neuen Region installieren und konfigurieren.


In diesem Artikel wurden die Details zur Migration für folgende Szenarien zusammengefasst:

> [!div class="checklist"]
> * AKS mit Load Balancer Standard und Virtual Machine Scale Sets
> * Vorhandene angefügte Azure-Dienste
> * Sicherstellen gültiger Kontingente
> * Hohe Verfügbarkeit und Geschäftskontinuität
> * Überlegungen zu zustandslosen Anwendungen
> * Überlegungen zu zustandsbehafteten Anwendungen
> * Bereitstellung Ihrer Clusterkonfiguration


[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[azure-dev-spaces]: ../dev-spaces/index.yml
