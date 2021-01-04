---
title: Voraussetzungen für Avere vFXT – Azure
description: Informieren Sie sich über Aufgaben, die Sie vor dem Erstellen eines Clusters in Avere vFXT for Azure ausführen müssen, einschließlich der Verwendung von Abonnements, Kontingenten und Speicherdienstendpunkten.
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: d87f57873a4bb84b20df3da3880017d9ef2484a5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008411"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Vorbereiten der Avere vFXT-Erstellung

In diesem Artikel werden die Voraussetzungen für die Erstellung eines Avere vFXT-Clusters erläutert.

## <a name="create-a-new-subscription"></a>Erstellen eines neuen Abonnements

Beginnen Sie mit der Erstellung eines neuen Azure-Abonnements. Verwenden Sie für jedes Avere vFXT-Projekt ein separates Abonnement, um die Kostenverfolgung und-Bereinigung zu vereinfachen und um sicherzustellen, dass andere Projekte nicht bei der Bereitstellung des Clusterworkflows durch Kontingente oder Ressourcendrosselung beeinträchtigt werden.

So erstellen Sie ein neues Azure-Abonnement im Azure-Portal

1. Navigieren Sie zum Blatt [Abonnements](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Klicken Sie ganz oben auf die Schaltfläche **+ Hinzufügen**.
1. Melden Sie sich an, wenn Sie dazu aufgefordert werden.
1. Wählen Sie ein Angebot aus und führen Sie die Schritte durch, um ein neues Abonnement zu erstellen.

## <a name="configure-subscription-owner-permissions"></a>Konfigurieren der Berechtigungen des Abonnementbesitzers

Ein Benutzer mit Besitzerberechtigungen für das Abonnement sollte den vFXT-Cluster erstellen. Bei der Clustererstellung muss ein Besitzer die Softwarevertragsbedingungen akzeptieren und Änderungen an Netzwerk- und Speicherressourcen autorisieren.

Es gibt einige Umgehungsmöglichkeiten, über die ein Nichtbesitzer einen Avere vFXT for Azure-Cluster erstellen kann. Diese Szenarien beinhalten die Einschränkung von Ressourcen und die Zuweisung zusätzlicher Azure-Rollen zum Ersteller. In allen diesen Fällen muss ein Abonnementbesitzer auch vorab die [Nutzungsbedingungen für die Avere vFXT-Software](#accept-software-terms) akzeptieren.

| Szenario | Beschränkungen | Erforderliche Zugriffsrollen für die Erstellung des Avere vFXT-Clusters |
|----------|--------|-------|
| Erstellung des vFXT-Clusters durch einen Ressourcengruppenadministrator | Das virtuelle Netzwerk, der Clustercontroller und die Clusterknoten müssen in der Ressourcengruppe erstellt werden. | Die Rollen [Benutzerzugriffsadministrator](../role-based-access-control/built-in-roles.md#user-access-administrator) und [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor), beide für die Zielressourcengruppe festgelegt |
| Verwenden eines vorhandenen, externen virtuellen Netzwerks | Der Clustercontroller und die Clusterknoten werden in der vFXT-Ressourcengruppe erstellt, jedoch wird ein vorhandenes virtuelles Netzwerk in einer anderen Ressourcengruppe verwendet. | (1) Rolle [Benutzerzugriffsadministrator](../role-based-access-control/built-in-roles.md#user-access-administrator) und [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor), festgelegt auf die vFXT-Ressourcengruppe; und (2) Rolle [Mitwirkender von virtuellen Computern](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [Benutzerzugriffsadministrator](../role-based-access-control/built-in-roles.md#user-access-administrator) und [Avere-Mitwirkender](../role-based-access-control/built-in-roles.md#avere-contributor), festgelegt auf die Ressourcengruppe des virtuellen Netzwerks. |
| Benutzerdefinierte Rolle für Clusterersteller | Keine Einschränkungen bei der Ressourcenplatzierung. Diese Methode verleiht Nichtbesitzern umfassende Berechtigungen. | Der Abonnementbesitzer erstellt eine benutzerdefinierte Azure-Rolle, wie in [diesem Artikel](avere-vfxt-non-owner.md) erläutert. |

## <a name="quota-for-the-vfxt-cluster"></a>Kontingent für den vFXT-Cluster

Stellen Sie sicher, dass Sie über ein ausreichendes Kontingent für die folgenden Azure-Komponenten verfügen. Fordern Sie bei Bedarf eine [Kontingenterhöhung](../azure-portal/supportability/resource-manager-core-quotas-request.md) an.

> [!NOTE]
> Die hier aufgeführten virtuellen Computer und SSD-Komponenten gelten für den vFXT-Cluster selbst. Beachten Sie, dass Sie auch ein Kontingent für die VMs und SSDs in Ihrer Computefarm benötigen.
>
> Stellen Sie sicher, dass das Kontingent für die Region aktiviert ist, in der Sie den Workflow ausführen möchten.

|Azure-Komponente|Kontingent|
|----------|-----------|
|Virtuelle Computer|Mindestens 3 E32s_v3 (einer pro Clusterknoten) |
|SSD Premium-Speicher|200 GB Speicherplatz für das Betriebssystem plus 1 bis 4 TB Cachespeicherplatz pro Knoten |
|Speicherkonto (optional) |V2|
|Back-End-Datenspeicher (optional) |Ein neuer LRS-Blobcontainer |
<!-- this table also appears in the overview - update it there if updating here -->

## <a name="accept-software-terms"></a>Nutzungsbedingungen der Software akzeptieren

> [!TIP]
> Überspringen Sie diesen Schritt, wenn ein Abonnementbesitzer den Avere vFXT-Cluster erstellt.

Bei der Clustererstellung müssen Sie die Nutzungsbedingungen für die Avere vFXT-Software akzeptieren. Wenn Sie kein Abonnementbesitzer sind, lassen Sie einen Abonnementbesitzer die Bedingungen vorab akzeptieren.

Dieser Schritt muss nur einmal pro Abonnement durchgeführt werden.

So akzeptieren Sie die Nutzungsbedingungen der Software im Voraus

1. Öffnen Sie eine Cloud-Shell im Azure-Portal oder Navigieren Sie zu <https://shell.azure.com>. Melden Sie sich mit Ihrer Abonnement-ID an.

   ```azurecli
    az login
    az account set --subscription <subscription ID>
   ```

1. Führen Sie diesen Befehl aus, um Nutzungsbedingungen zu akzeptieren und den programmgesteuerten Zugriff auf das Softwareimage von Avere vFXT für Azure zu ermöglichen:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Erstellen eines Speicherdienstendpunkts in Ihrem virtuellen Netzwerk (falls erforderlich)

Ein [Dienstendpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) sorgt dafür, dass der Azure Blob-Datenverkehr lokal bleibt, anstatt ihn außerhalb des virtuellen Netzwerks weiterzuleiten. Er wird für alle Avere vFXT for Azure-Cluster empfohlen, bei denen Azure Blob Storage für den Back-End-Datenspeicher verwendet wird.

Wenn Sie beim Erstellen des Clusters ein neues virtuelles Netzwerk erstellen, wird automatisch ein Endpunkt erstellt. Wenn Sie ein vorhandenes virtuelles Netzwerk bereitstellen, muss es über einen Microsoft Storage-Dienstendpunkt verfügen, wenn Sie während der Clustererstellung einen neuen Blob Storage-Container erstellen möchten.<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* Überspringen Sie diesen Schritt, wenn Sie ein neues virtuelles Netzwerk als Teil der Clustererstellung erstellen.
>* Der Endpunkt ist optional, wenn Sie bei der Clustererstellung keinen Blobspeicher erstellen. In diesem Fall können Sie den Dienstendpunkt später erstellen, wenn Sie Azure Blob Storage verwenden möchten.

Erstellen Sie den Speicherdienstendpunkt über das Azure-Portal.

1. Öffnen Sie im Portal Ihre Liste der virtuellen Netzwerke.
1. Wählen Sie das virtuelle Netzwerk für Ihren Cluster aus.
1. Klicken Sie im linken Menü auf **Dienstendpunkte**.
1. Klicken Sie oben auf **Hinzufügen**.
1. Wählen Sie den Dienst ``Microsoft.Storage`` aus.
1. Wählen Sie das Subnetz des Clusters aus.
1. Klicken Sie unten auf **Hinzufügen**.

   ![Screenshot für das Azure-Portal mit Anmerkungen für die Schritte zum Erstellen des Dienstendpunkts](media/avere-vfxt-service-endpoint.png)

## <a name="next-steps"></a>Nächste Schritte

Nach Abschluss dieser Voraussetzungen können Sie den Cluster erstellen. Anweisungen finden Sie unter [Bereitstellen des vFXT-Clusters](avere-vfxt-deploy.md).