---
title: Häufig gestellte Fragen zu Azure Red Hat OpenShift | Microsoft-Dokumentation
description: Enthält Antworten auf häufige gestellte Fragen zu Microsoft Azure Red Hat OpenShift.
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: 2001b849e9c43d552889475ca237c52b141f3f04
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306268"
---
# <a name="azure-red-hat-openshift-faq"></a>Häufig gestellte Fragen zu Azure Red Hat OpenShift

Dieser Artikel beantwortet häufig gestellte Fragen zu Microsoft Azure Red Hat OpenShift.

## <a name="how-do-i-get-started"></a>Wie fange ich an?

Bevor Sie Azure Red Hat OpenShift verwenden können, müssen Sie mindestens 4 reservierte Azure Red Hat OpenShift-Anwendungsknoten gekauft haben.

Wenn Sie Azure-Kunde sind, [erwerben Sie reservierte Azure Red Hat OpenShift-Instanzen](https://aka.ms/openshift/buy) über das Azure-Portal. Nach dem Kauf wird Ihr Abonnement innerhalb von 24 Stunden aktiviert. Anschließend können Sie Cluster bereitstellen.

Wenn Sie kein Azure-Kunde sind, [wenden Sie sich an das Vertriebsteam](https://aka.ms/openshift/contact-sales), und füllen Sie das Formular am Ende der Seite aus, um den Prozess zu starten.

Weitere Informationen finden Sie auf der [Azure Red Hat OpenShift-Preisseite](https://aka.ms/openshift/pricing).

## <a name="which-azure-regions-are-supported"></a>Welche Azure-Regionen werden unterstützt?

Eine Liste der weltweiten Regionen, in denen Azure Red Hat OpenShift unterstützt wird, finden Sie unter [Unterstützte Ressourcen](supported-resources.md#azure-regions).

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Kann ich einen Cluster in einem vorhandenen virtuellen Netzwerk bereitstellen?

Nein. Aber Sie können einen Azure Red Hat OpenShift-Cluster über Peering mit einem vorhandenen VNET verbinden. Weitere Informationen finden Sie unter [Verbinden des virtuellen Netzwerks eines Clusters mit einem bestehenden virtuellen Netzwerk ](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network).

## <a name="what-cluster-operations-are-available"></a>Welche Clustervorgänge sind verfügbar?

Sie können nur die Anzahl von Computeknoten zentral hoch- oder herunterskalieren. Keine weiteren Änderungen sind an der `Microsoft.ContainerService/openShiftManagedClusters`-Ressource nach der Erstellung zulässig. Die maximale Anzahl von Computeknoten ist auf 20 beschränkt.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Welche Größen von virtuellen Computern kann ich verwenden?

Eine Liste der VM-Größen, die Sie mit einem Azure Red Hat OpenShift-Cluster verwenden können, finden Sie unter [VM-Größen für Azure Red Hat OpenShift](supported-resources.md#virtual-machine-sizes).

## <a name="is-data-on-my-cluster-encrypted"></a>Werden Daten in meinem Cluster verschlüsselt?

Standardmäßig erfolgt die Verschlüsselung ruhender Daten. Die Azure Storage-Plattform verschlüsselt Ihre Daten automatisch vor dem dauerhaften Ablegen und entschlüsselt sie vor dem Abrufen. Details finden Sie unter [Azure-Speicherdienstverschlüsselung für ruhende Daten](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

## <a name="can-i-use-prometheusgrafana-to-monitor-containers-and-manage-capacity"></a>Kann ich Prometheus/Grafana zum Überwachen von Containern und Verwalten der Kapazität verwenden?

Nein, zurzeit nicht.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Ist die Docker-Registrierung extern verfügbar, damit ich Tools wie Jenkins verwenden kann?

Die Docker-Registrierung steht unter `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` zur Verfügung. Es wird aber keine Garantie für starke Dauerhaftigkeit des Speichers übernommen. Sie können auch die [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) verwenden.

## <a name="is-cross-namespace-networking-supported"></a>Wird namespaceübergreifender Netzwerkdatenverkehr unterstützt?

Kunden- und einzelne Projektadministratoren können namespaceübergreifenden Netzwerkdatenverkehr pro Projekt mithilfe von `NetworkPolicy`-Objekten anpassen (einschließlich seiner Untersagung).

## <a name="can-an-admin-manage-users-and-quotas"></a>Kann ein Administrator Benutzer und Kontingente verwalten?

Ja. Ein Azure Red Hat OpenShift-Administrator kann zusätzlich zum Zugriff auf alle von Benutzern erstellte Projekte Benutzer und Kontingente verwalten.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Kann ich einen Cluster auf nur bestimmte Azure AD-Benutzer beschränken?

Ja. Sie können einschränken, welche Azure AD-Benutzer sich bei einem Cluster anmelden können, indem Sie die Azure AD-Anwendung konfigurieren. Detailinformationen finden Sie unter [Gewusst wie: Einschränken Ihrer App auf eine Gruppe von Benutzern](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users).

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Kann ein Cluster Computeknoten über mehrere Azure-Regionen hinweg besitzen?

Nein. Alle Knoten in einem Azure Red Hat OpenShift-Cluster müssen aus derselben Azure-Region stammen.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Werden Master- und Infrastrukturknoten abstrahiert, weil sie zu Azure Kubernetes Service (AKS) gehören?

Nein. Alle Ressourcen, einschließlich des Clustermasters, werden in Ihrem Kundenabonnement ausgeführt. Diese Typen von Ressourcen werden in einer „Nur-Lesen“-Ressourcengruppe platziert.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>Wird Open Service Broker für Azure (OSBA) unterstützt?

Ja. Sie können OSBA mit Azure Red Hat OpenShift verwenden. Weitere Informationen finden Sie unter [Open Service Broker für Azure](https://github.com/Azure/open-service-broker-azure#openshift-project-template).
