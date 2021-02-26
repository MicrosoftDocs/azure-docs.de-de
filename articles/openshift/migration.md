---
title: Migrieren von Azure Red Hat OpenShift 3.11 zu Azure Red Hat OpenShift 4
description: Migrieren von Azure Red Hat OpenShift 3.11 zu Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 08/13/2020
keywords: Migration, ARO, OpenShift, Red Hat
ms.openlocfilehash: f9bfc924581d5dbe33c7c2683a0f6083cb2abc23
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071034"
---
# <a name="migrate-from-azure-red-hat-openshift-311-to-azure-red-hat-openshift-4"></a>Migrieren von Azure Red Hat OpenShift 3.11 zu Azure Red Hat OpenShift 4

Azure Red Hat OpenShift auf OpenShift 4 bietet Kubernetes 1.16 unter Red Hat Core OS, private Cluster, Unterstützung für virtuelle Netzwerke nach dem Bring-Your-Own-Prinzip und eine vollständige Clusteradministratorrolle. Außerdem sind jetzt viele neue Funktionen verfügbar, z. B. Unterstützung für Operator Framework, OperatorHub und OpenShift Service Mesh.

Um einen erfolgreichen Übergang von Azure Red Hat OpenShift 3.11 zu Azure Red Hat OpenShift 4 zu gewährleisten, sollten Sie sich über die [Unterschiede bei der Speicherung, Protokollierung, Sicherheit, Überwachung und beim Netzwerk informieren](https://docs.openshift.com/container-platform/4.4/migration/migrating_3_4/planning-migration-3-to-4.html).

In diesem Artikel wird veranschaulicht, wie Sie von einem Azure Red Hat OpenShift 3.11-Cluster zu einem Azure Red Hat 4-Cluster migrieren.

> [!NOTE]
> Red Hat OpenShift-Migrationstools wie das Control Plane Migration Assistance Tool und das Cluster Application Migration Tool (CAM) können nicht für Azure Red Hat OpenShift 3.11-Cluster verwendet werden.

## <a name="before-you-begin"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie über einen Azure Red Hat OpenShift 3.11-Cluster verfügen.

## <a name="create-a-target-azure-red-hat-openshift-4-cluster"></a>Erstellen eines Azure Red Hat OpenShift 4-Zielclusters

[Erstellen Sie zunächst den Azure Red Hat OpenShift 4-Cluster](tutorial-create-cluster.md), den Sie als Zielcluster verwenden möchten. Hier wird die Standardkonfiguration verwendet. Wenn Sie sich für andere Einstellungen interessieren, finden Sie weitere Informationen unter [Tutorial: Erstellen eines Azure Red Hat OpenShift 4-Clusters](tutorial-create-cluster.md).

Erstellen Sie ein virtuelles Netzwerk mit zwei leeren Subnetzen für die Master- und Workerknoten.

```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
```

Verwenden Sie dann den folgenden Befehl zum Erstellen des Clusters.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

## <a name="configure-the-target-openshift-4-cluster"></a>Konfigurieren des OpenShift 4-Zielclusters

### <a name="authentication"></a>Authentifizierung

Damit Benutzer mit Azure Red Hat OpenShift interagieren können, müssen sie sich zunächst beim Cluster authentifizieren. Auf der Authentifizierungsebene wird der Benutzer, der den Anforderungen zugeordnet ist, bei der Azure Red Hat OpenShift-API identifiziert. Auf der Autorisierungsebene wird dann mithilfe von Informationen zum anfordernden Benutzer bestimmt, ob die Anforderung zulässig ist.

Beim Erstellen eines Azure Red Hat OpenShift 4-Clusters wird ein temporärer Administrator erstellt. [Stellen Sie eine Verbindung mit Ihrem Cluster her](tutorial-connect-cluster.md), fügen Sie Benutzer und Gruppen hinzu, und [konfigurieren Sie für beide die geeigneten Berechtigungen](https://docs.openshift.com/container-platform/4.6/authentication/understanding-authentication.html).

### <a name="networking"></a>Netzwerk

Azure Red Hat OpenShift 4 nutzt verschiedene Operatoren, um das Netzwerk in Ihrem Cluster einzurichten: [Cluster Network Operator](https://docs.openshift.com/container-platform/4.6/networking/cluster-network-operator.html#nw-cluster-network-operator_cluster-network-operator), [DNS Operator](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html) und [Ingress Operator](https://docs.openshift.com/container-platform/4.6/networking/ingress-operator.html). Weitere Informationen zum Einrichten von Netzwerken in einem Azure Red Hat OpenShift 4-Cluster finden Sie im [Netzwerkdiagramm](concepts-networking.md) und unter [Understanding Networking](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html) (Grundlegendes zu Netzwerken).

### <a name="storage"></a>Storage
Azure Red Hat OpenShift 4 unterstützt die folgenden PersistentVolume-Plug-Ins:

- AWS Elastic Block Store (EBS)
- Azure-Datenträger
- Azure File
- GCE Persistent Disk
- HostPath
- iSCSI
- Lokales Volume
- NFS
- Red Hat OpenShift Container Storage

Informationen zum Konfigurieren dieser Speichertypen finden Sie im Thema zum [Konfigurieren von persistentem Speicher](https://access.redhat.com/documentation/azure_red_hat_openshift/4/html/storage/configuring-persistent-storage).

### <a name="registry"></a>Registrierung

Mit Azure Red Hat OpenShift 4 können Images aus Ihrem Quellcode erstellt und bereitgestellt und deren Lebenszyklus verwaltet werden. Um dies zu ermöglichen, bietet Azure Red Hat OpenShift 4 eine [interne, integrierte Containerimageregistrierung](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html), die in Ihrer Azure Red Hat OpenShift-Umgebung für die lokale Verwaltung von Images bereitgestellt werden kann.

Wenn Sie externe Registrierungen wie [Azure Container Registry](../container-registry/index.yml), [Red Hat Quay-Registrierungen](ttps://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-quay-overview_registry-options) oder eine [authentifizierungsfähige Red Hat-Registrierung](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-authentication-enabled-registry-overview_registry-options) verwenden, befolgen Sie die Schritte zum Bereitstellen von Anmeldeinformationen für den Cluster, damit der Cluster auf die Repositorys zugreifen kann.

### <a name="monitoring"></a>Überwachung

Azure Red Hat OpenShift umfasst einen vorkonfigurierten, vorinstallierten Überwachungsstapel, der sich selbst aktualisiert und auf dem Open-Source-Projekt Prometheus und dessen umfassenderen Ökosystem basiert. Er ermöglicht die Überwachung von Clusterkomponenten und umfasst eine Reihe von Benachrichtigungen, mit denen der Clusteradministrator unverzüglich über auftretende Probleme informiert werden kann, und eine Reihe von Grafana-Dashboards. Der Clusterüberwachungsstapel wird nur für die Überwachung von Azure Red Hat OpenShift-Clustern unterstützt. Weitere Informationen finden Sie im Thema zur [Clusterüberwachung für Azure Red Hat OpenShift](https://docs.openshift.com/container-platform/4.6/monitoring/understanding-the-monitoring-stack.html).

Wenn Sie [Azure Monitor für Container für Azure Red Hat OpenShift 3.11](../azure-monitor/insights/container-insights-azure-redhat-setup.md) verwendet haben, können Sie Azure Monitor für Container auch für [Azure Red Hat OpenShift 4-Cluster](../azure-monitor/insights/container-insights-azure-redhat4-setup.md) aktivieren und weiterhin denselben Log Analytics-Arbeitsbereich verwenden.

## <a name="move-your-dns-or-load-balancer-configuration-to-the-new-cluster"></a>Verlagern der DNS- oder Load Balancer-Konfiguration in den neuen Cluster

Wenn Sie Azure Traffic Manager verwenden, fügen Sie Endpunkte hinzu, um auf ihren Zielcluster zu verweisen, und priorisieren Sie diese Endpunkte.

## <a name="deploy-application-to-your-target-cluster"></a>Bereitstellen der Anwendung in Ihrem Zielcluster

Nachdem Sie den Zielcluster ordnungsgemäß für Ihre Workload konfiguriert haben, [stellen Sie eine Verbindung mit dem Cluster her](tutorial-connect-cluster.md) und erstellen die erforderlichen Anwendungen, Komponenten oder Dienste für Ihre Projekte. Mit Azure Red Hat OpenShift können Sie diese unter Verwendung von Git, Containerimages, dem Red Hat Developer Catalog, einer Dockerfile, einer YAML-/JSON-Definition oder durch die Auswahl eines Datenbankdiensts aus dem Katalog erstellen.

## <a name="delete-your-source-cluster"></a>Löschen des Quellclusters
Nachdem Sie sich vergewissert haben, dass Ihr Azure Red Hat OpenShift 4-Cluster ordnungsgemäß eingerichtet ist, löschen Sie den Azure Red Hat OpenShift 3.11-Cluster.

```
az openshift delete --name $CLUSTER_NAME
                    --resource-group $RESOURCE_GROUP
                    [--no-wait]
                    [--subscription]
                    [--yes]
```
## <a name="next-steps"></a>Nächste Schritte
Sehen Sie sich die [Dokumentation zu Red Hat OpenShift](https://docs.openshift.com/container-platform/4.6/welcome/index.html) an.