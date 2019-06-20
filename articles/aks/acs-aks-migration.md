---
title: Migrieren von Azure Container Service (ACS) zu Azure Kubernetes Service (AKS)
description: Migrieren von Azure Container Service (ACS) zu Azure Kubernetes Service (AKS)
services: container-service
author: noelbundick
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: nobun
ms.custom: mvc
ms.openlocfilehash: dcee8da943603fb0978caf9992be76347ca197d6
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65977716"
---
# <a name="migrate-from-azure-container-service-acs-to-azure-kubernetes-service-aks"></a>Migrieren von Azure Container Service (ACS) zu Azure Kubernetes Service (AKS)

Dieser Artikel unterstützt Sie bei der erfolgreichen Planung und Durchführung einer Migration von Azure Container Service (ACS) mit Kubernetes zu Azure Kubernetes Service (AKS). Zur Unterstützung bei wichtigen Entscheidungen erläutert dieser Leitfaden die Unterschiede zwischen ACS und AKS und bietet eine Übersicht über den Migrationsprozess.

## <a name="differences-between-acs-and-aks"></a>Unterschiede zwischen ACS und AKS

ACS und AKS unterscheiden sich in einigen wichtigen Bereichen, die sich auf die Migration auswirken. Vor einer Migration müssen Sie sich mit den folgenden Unterschieden vertraut machen und diese in Ihre Planung einbeziehen:

* AKS-Knoten verwenden [verwaltete Datenträger](../virtual-machines/windows/managed-disks-overview.md).
    * Nicht verwaltete Datenträger müssen konvertiert werden, bevor sie an AKS-Knoten angefügt werden können.
    * Benutzerdefinierte `StorageClass`-Objekte für Azure-Datenträger müssen von `unmanaged` in `managed` geändert werden.
    * Alle `PersistentVolumes` müssen `kind: Managed` verwenden.
* AKS unterstützt [mehrere Knotenpools](https://docs.microsoft.com/azure/aks/use-multiple-node-pools) (derzeit Vorschauversion).
* Windows Server-basierte Knoten befinden sich derzeit in [AKS in der Vorschauphase](https://azure.microsoft.com/blog/kubernetes-on-azure/).
* AKS unterstützt eine begrenzte Anzahl von [Regionen](https://docs.microsoft.com/azure/aks/quotas-skus-regions).
* AKS ist ein verwalteter Dienst mit einer gehosteten Kubernetes-Steuerungsebene. Sie müssen Ihre Anwendungen möglicherweise ändern, wenn Sie die Konfiguration der ACS-Master zuvor geändert haben.

## <a name="differences-between-kubernetes-versions"></a>Unterschiede zwischen Kubernetes-Versionen

Wenn Sie eine Migration zu einer neueren Version von Kubernetes durchführen (z. B. von 1.7.x zu 1.9.x), können Sie sich in den folgenden Ressourcen mit einigen Änderungen an der Kubernetes-API vertraut machen:

* [Migration einer ThirdPartyResource zu CustomResourceDefinition](https://kubernetes.io/docs/tasks/access-kubernetes-api/migrate-third-party-resource/)
* [Änderungen an der Workloads-API in den Versionen 1.8 und 1.9](https://kubernetes.io/docs/reference/workloads-18-19/)

## <a name="migration-considerations"></a>Überlegungen zur Migration

### <a name="agent-pools"></a>Agentpools

Obwohl AKS die Kubernetes-Steuerungsebene verwaltet, müssen Sie dennoch die Größe und Anzahl der in Ihren neuen Cluster einzuschließenden Knoten definieren. Wenn Sie eine 1:1-Zuordnung von ACS zu AKS erzielen möchten, müssen Sie die Informationen zu Ihren vorhandenen ACS-Knoten erfassen. Verwenden Sie diese Daten, wenn Sie Ihren neuen AKS-Cluster erstellen.

Beispiel:

| NAME | Count | Größe des virtuellen Computers | Betriebssystem |
| --- | --- | --- | --- |
| agentpool0 | 3 | Standard_D8_v2 | Linux |
| agentpool1 | 1 | Standard_D2_v2 | Windows |

Da während der Migration zusätzliche VMs in Ihrem Abonnement bereitgestellt werden, müssen Sie überprüfen, ob Ihre Kontingente und Limits für diese Ressourcen ausreichen. 

Weitere Informationen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](https://docs.microsoft.com/azure/azure-subscription-service-limits). Um Ihre aktuellen Kontingente zu überprüfen, wechseln Sie im Azure-Portal zum [Blatt „Abonnements“](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), und wählen Sie Ihr Abonnement und dann **Nutzung + Kontingente** aus.

### <a name="networking"></a>Netzwerk

Für komplexe Anwendungen führen Sie die Migration typischerweise nach und nach durch, statt alle Anwendungen gleichzeitig zu migrieren. Dies bedeutet, dass die alte und neue Umgebung möglicherweise über das Netzwerk kommunizieren müssen. Anwendungen, in denen zuvor `ClusterIP`-Dienste für die Kommunikation genutzt wurden, müssen möglicherweise als Typ `LoadBalancer` verfügbar gemacht und in geeigneter Weise geschützt werden.

Um die Migration durchzuführen, müssen Clients auf die neuen Dienste zeigen, die in AKS ausgeführt werden. Es wird empfohlen, den Datenverkehr umzuleiten, indem Sie den DNS aktualisieren, damit auf den Load Balancer verwiesen wird, der vor Ihrem AKS-Cluster platziert ist.

### <a name="stateless-applications"></a>Zustandslose Anwendungen

Die Migration von zustandslosen Anwendung ist der einfachste Fall. Sie wenden Ihre YAML-Definitionen auf den neuen Cluster an und überprüfen, ob alles wie erwartet funktioniert. Anschließend leiten Sie den Datenverkehr um, damit der neue Cluster aktiviert wird.

### <a name="stateful-applications"></a>Zustandsbehaftete Anwendungen

Planen Sie die Migration von zustandsbehafteten Anwendungen sorgfältig, um Datenverlust oder eine unerwartete Downtime zu vermeiden.

#### <a name="highly-available-applications"></a>Hochverfügbare Anwendungen

Einige zustandsbehaftete Anwendungen können Sie in einer Hochverfügbarkeitskonfiguration bereitstellen. In diesen Anwendungen können Daten replikatübergreifend kopiert werden. Wenn Sie derzeit diese Art von Bereitstellung verwenden, können Sie unter Umständen ein neues Mitglied im neuen AKS-Cluster erstellen und dann die Migration mit minimaler Auswirkung auf die nachgelagerten Aufrufer durchführen. Im Allgemeinen gelten für dieses Szenario die folgenden Migrationsschritte:

1. Erstellen eines neuen sekundären Replikats in AKS
2. Warten auf Daten für die Replikation
3. Failover, um das sekundäre Replikat als neues primäres Replikat festzulegen
4. Umleiten des Datenverkehrs an den AKS-Cluster

#### <a name="migrating-persistent-volumes"></a>Migration persistenter Volumes

Wenn Sie vorhandene persistente Volumes zu AKS migrieren, führen Sie im Allgemeinen folgende Schritte aus:

1. Stilllegen von Schreibvorgängen in die Anwendung (Dieser Schritt ist optional und erfordert eine Ausfallzeit.)
2. Erstellen von Momentaufnahmen der Datenträger
3. Erstellen neuer verwalteter Datenträger aus den Momentaufnahmen
4. Erstellen von persistenten Volumes in AKS
5. Aktualisieren der Podspezifikationen zur [Verwendung vorhandener Volumes](https://docs.microsoft.com/azure/aks/azure-disk-volume) anstelle von PersistentVolumeClaims (statische Bereitstellung)
6. Bereitstellen der Anwendung in AKS
7. Überprüfen
8. Umleiten des Datenverkehrs an den AKS-Cluster

> [!IMPORTANT]
> Wenn Sie die Schreibvorgänge nicht stilllegen, müssen Sie Daten zur neuen Bereitstellung replizieren. Andernfalls fehlen die Daten, die nach dem Erstellen der Datenträgermomentaufnahmen geschrieben wurden.

Mit einigen Open-Source-Tools können Sie verwaltete Datenträger erstellen und Volumes zwischen Kubernetes-Clustern migrieren:

* [Azure CLI Disk Copy-Erweiterung:](https://github.com/noelbundick/azure-cli-disk-copy-extension) Kopieren und Konvertieren von Datenträgern zwischen Ressourcengruppen und Azure-Regionen
* [Azure Kube CLI-Erweiterung:](https://github.com/yaron2/azure-kube-cli) Auflisten von ACS Kubernetes-Volumes und Migrieren der Volumes zu einem AKS-Cluster

#### <a name="azure-files"></a>Azure Files

Im Gegensatz zu Datenträgern können Azure Files-Instanzen gleichzeitig auf mehreren Hosts bereitgestellt werden. In Azure und in Kubernetes ist es möglich, einen Pod in Ihrem AKS-Cluster zu erstellen, der weiterhin von Ihrem ACS-Cluster verwendet wird. Um Datenverlust und unerwartetes Verhalten zu vermeiden, müssen Sie sicherstellen, dass die Cluster nicht gleichzeitig in dieselben Dateien schreiben.

Wenn Ihre Anwendung mehrere Replikate hosten kann, die auf dieselbe Dateifreigabe verweisen, führen Sie die Schritte für die Migration zustandsloser Anwendungen aus, und stellen Sie Ihre YAML-Definitionen auf Ihrem neuen Cluster bereit. Andernfalls umfasst ein möglicher Ansatz für die Migration die folgenden Schritte:

1. Bereitstellen Ihrer Anwendung in AKS mit einer Replikatanzahl von 0
2. Skalieren der Anwendung in AKS auf 0 (Dieser Schritt erfordert eine Ausfallzeit.)
3. Skalieren der Anwendung in AKS auf 1
4. Überprüfen
5. Umleiten des Datenverkehrs an den AKS-Cluster

Wenn Sie mit einer leeren Freigabe beginnen und eine Kopie der Quelldaten erstellen möchten, können Sie die [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest)-Befehle zum Migrieren Ihrer Daten verwenden.

### <a name="deployment-strategy"></a>Bereitstellungsstrategie

Es empfiehlt sich die Verwendung Ihrer vorhandenen CI/CD-Pipeline, um eine als funktionierend bekannten Konfiguration in AKS bereitzustellen. Klonen Sie Ihre vorhandenen Bereitstellungsaufgaben, und stellen Sie sicher, dass `kubeconfig` auf den neuen AKS-Cluster zeigt.

Wenn dies nicht möglich ist, exportieren Sie die Ressourcendefinitionen aus ACS, und wenden Sie sie anschließend auf AKS an. Sie können `kubectl` zum Exportieren von Objekten verwenden.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

Dazu können abhängig von Ihren Bereitstellungsanforderungen verschiedene Open-Source-Tools verwendet werden:

* [Velero](https://github.com/heptio/ark) (Dieses Tool erfordert Kubernetes 1.7.)
* [Azure Kube CLI-Erweiterung](https://github.com/yaron2/azure-kube-cli)
* [ReShifter](https://github.com/mhausenblas/reshifter)

## <a name="migration-steps"></a>Schritte bei der Migration

1. [Erstellen Sie einen AKS-Cluster](https://docs.microsoft.com/azure/aks/create-cluster) über das Azure-Portal, die Azure-Befehlszeilenschnittstelle oder eine Azure Resource Manager-Vorlage.

   > [!NOTE]
   > Azure Resource Manager-Beispielvorlagen für AKS finden Sie im Repository [Azure/AKS](https://github.com/Azure/AKS/tree/master/examples/vnet) auf GitHub.

2. Nehmen Sie eventuell erforderliche Änderungen an Ihren YAML-Definitionen vor. Ersetzen Sie beispielsweise `apps/v1beta1` für `Deployments` durch `apps/v1`.

3. [Migrieren Sie Volumes](#migrating-persistent-volumes) (optional) von Ihrem ACS-Cluster zu Ihrem AKS-Cluster.

4. Verwenden Sie Ihr CI/CD-System, um Anwendungen in AKS bereitzustellen. Oder verwenden Sie kubectl, um die YAML-Definitionen anzuwenden.

5. Überprüfen Stellen Sie sicher, dass Ihre Anwendungen wie erwartet funktionieren und dass migrierte Daten an das Ziel kopiert wurden.

6. Leiten Sie den Datenverkehr um. Aktualisieren Sie DNS, um Clients an Ihre AKS-Bereitstellung zu verweisen.

7. Führen Sie eventuelle Aufgaben nach der Migration aus. Wenn Sie Volumes migriert haben und Schreibvorgänge nicht stillgelegt haben, kopieren Sie diese Daten in den neuen Cluster.
