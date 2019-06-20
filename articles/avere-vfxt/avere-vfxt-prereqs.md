---
title: Voraussetzungen für Avere vFXT – Azure
description: Voraussetzungen für Avere vFXT für Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 352833b12c00abbefcf7016d27dfb580ee25e450
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60409242"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Vorbereiten der Avere vFXT-Erstellung

In diesem Artikel werden die Voraussetzungen für die Erstellung eines Avere vFXT-Clusters erläutert.

## <a name="create-a-new-subscription"></a>Erstellen eines neuen Abonnements

Beginnen Sie mit der Erstellung eines neuen Azure-Abonnements. Verwenden Sie ein separates Abonnement für jedes Avere vFXT-Projekt, damit Sie alle Projektressourcen und -kosten leicht nachverfolgen können, andere Projekte vor einer möglichen Ressourcenbegrenzung während der Bereitstellung schützen und die Bereinigung vereinfachen können.  

So erstellen Sie ein neues Azure-Abonnement im Azure-Portal

* Navigieren Sie zum Blatt [Abonnements](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
* Klicken Sie ganz oben auf die Schaltfläche **+ Hinzufügen**.
* Melden Sie sich an, wenn Sie dazu aufgefordert werden.
* Wählen Sie ein Angebot aus und führen Sie die Schritte durch, um ein neues Abonnement zu erstellen.

## <a name="configure-subscription-owner-permissions"></a>Konfigurieren der Berechtigungen des Abonnementbesitzers

Ein Benutzer mit Besitzerberechtigungen für das Abonnement sollte den vFXT-Cluster erstellen. Zum Akzeptieren der Nutzungsbedingungen der Software und zum Ausführen anderer Aktionen sind Berechtigungen des Abonnementbesitzers erforderlich. 

Es gibt einige Problemumgehungsszenarien, in denen es möglich ist, dass Nicht-Besitzer einen Avere vFXT für Azure-Cluster erstellen. Diese Szenarien beinhalten die Einschränkung von Ressourcen und die Zuordnung zusätzlicher Rollen zum Ersteller. In beiden Fällen muss ein Abonnementbesitzer auch die [Nutzungsbedingungen für die Avere vFXT-Software](#accept-software-terms) vorab akzeptieren. 

| Szenario | Einschränkungen | Erforderliche Zugriffsrollen für die Erstellung des Avere vFXT-Clusters | 
|----------|--------|-------|
| Ressourcengruppenadministrator | Das virtuelle Netzwerk, der Clustercontroller und die Clusterknoten müssen in der Ressourcengruppe erstellt werden. | Rolle [Benutzerzugriffsadministrator](../role-based-access-control/built-in-roles.md#user-access-administrator) und [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor), beide für die Zielressourcengruppe festgelegt | 
| Externes VNET | Der Clustercontroller und die Clusterknoten werden in der Ressourcengruppe erstellt, jedoch wird ein vorhandenes virtuelles Netzwerk in einer anderen Ressourcengruppe verwendet. | (1) Rolle [Benutzerzugriffsadministrator](../role-based-access-control/built-in-roles.md#user-access-administrator) und [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor), festgelegt auf die vFXT-Ressourcengruppe; und (2) Rolle [Mitwirkender von virtuellen Computern](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [Benutzerzugriffsadministrator](../role-based-access-control/built-in-roles.md#user-access-administrator) und [Avere-Mitwirkender](../role-based-access-control/built-in-roles.md#avere-contributor), festgelegt auf die VNET-Ressourcengruppe |
 
Alternativ können Sie wie in [diesem Artikel](avere-vfxt-non-owner.md) beschrieben vorab eine benutzerdefinierte RBAC-Rolle (role-based access control, rollenbasierte Zugriffssteuerung) erstellen und dem Benutzer Berechtigungen zuweisen. Diese Methode gewährt diesen Benutzern jedoch erhebliche Berechtigungen. 

## <a name="quota-for-the-vfxt-cluster"></a>Kontingent für den vFXT-Cluster

Sie müssen über ein ausreichendes Kontingent für die folgenden Azure-Komponenten verfügen. Fordern Sie bei Bedarf eine [Kontingenterhöhung](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) an.

> [!NOTE]
> Die hier aufgeführten virtuellen Computer und SSD-Komponenten gelten für den vFXT-Cluster selbst. Sie benötigen zusätzliche Kontingente für die virtuellen Computer und SSDs, die Sie für Ihre Serverfarm verwenden möchten.  Stellen Sie sicher, dass das Kontingent für die Region aktiviert ist, in der Sie den Workflow ausführen möchten.

|Azure-Komponente|Kontingent|
|----------|-----------|
|Virtuelle Computer|Mindestens drei E32s_v3|
|SSD Premium-Speicher|200 GB Speicherplatz für das Betriebssystem plus 1 bis 4 TB Cachespeicherplatz pro Knoten |
|Speicherkonto (optional) |V2|
|Back-End-Datenspeicher (optional) |Ein neuer LRS-Blobcontainer |

## <a name="accept-software-terms"></a>Nutzungsbedingungen der Software akzeptieren

> [!NOTE] 
> Dieser Schritt ist nicht erforderlich, wenn ein Abonnementbesitzer den Avere vFXT-Cluster erstellt.

Bei der Clustererstellung müssen Sie die Nutzungsbedingungen für die Avere vFXT-Software akzeptieren. Wenn Sie kein Abonnementbesitzer sind, lassen Sie einen Abonnementbesitzer die Bedingungen vorab akzeptieren. Dieser Schritt muss nur einmal pro Abonnement durchgeführt werden.

So akzeptieren Sie die Nutzungsbedingungen der Software im Voraus 

1. Öffnen Sie eine Cloud-Shell im Azure-Portal oder Navigieren Sie zu <https://shell.azure.com>. Melden Sie sich mit Ihrer Abonnement-ID an.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. Führen Sie diesen Befehl aus, um Nutzungsbedingungen zu akzeptieren und den programmgesteuerten Zugriff auf das Softwareimage von Avere vFXT für Azure zu ermöglichen: 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Erstellen eines Speicherdienstendpunkts in Ihrem virtuellen Netzwerk (falls erforderlich)

Ein [Dienstendpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) sorgt dafür, dass der Azure Blob-Datenverkehr lokal bleibt, anstatt ihn außerhalb des virtuellen Netzwerks weiterzuleiten. Er wird für alle Avere vFXT for Azure-Cluster empfohlen, bei denen Azure Blob Storage für den Back-End-Datenspeicher verwendet wird. 

Wenn Sie ein vorhandenes VNET angeben und einen neuen Azure-Blobcontainer für den Back-End-Speicher als Teil der Clustererstellung erstellen, müssen Sie über einen Dienstendpunkt im VNET für Microsoft Storage verfügen. Dieser Endpunkt muss vor dem Erstellen des Clusters vorhanden sein, andernfalls treten bei der Erstellung Fehler auf. 

Ein Speicherdienstendpunkt wird für alle Avere vFXT for Azure-Cluster empfohlen, bei denen Azure Blob Storage verwendet wird, auch wenn der Speicher später hinzugefügt wird. 

> [!TIP] 
> * Überspringen Sie diesen Schritt, wenn Sie ein neues virtuelles Netzwerk als Teil der Clustererstellung erstellen. 
> * Dieser Schritt ist optional, wenn Sie bei der Clustererstellung keinen Blobspeicher erstellen. In diesem Fall können Sie den Dienstendpunkt später erstellen, wenn Sie Azure Blob Storage verwenden möchten.

Erstellen Sie den Speicherdienstendpunkt über das Azure-Portal. 

1. Klicken Sie im Portal links auf **Virtuelle Netzwerke**.
1. Wählen Sie das VNET für den Cluster aus. 
1. Klicken Sie links auf **Dienstendpunkte**.
1. Klicken Sie oben auf **Hinzufügen**.
1. Übernehmen Sie den Dienst ``Microsoft.Storage`` unverändert, und wählen Sie das Subnetz des Clusters aus.
1. Klicken Sie unten auf **Hinzufügen**.

   ![Screenshot für das Azure-Portal mit Anmerkungen für die Schritte zum Erstellen des Dienstendpunkts](media/avere-vfxt-service-endpoint.png)


## <a name="next-step-create-the-vfxt-cluster"></a>Nächster Schritt: Erstellen des vFXT-Clusters

Nach Abschluss dieser Voraussetzungen können Sie in die Erstellung des Clusters einsteigen. Anweisungen finden Sie unter [Bereitstellen des vFXT-Clusters](avere-vfxt-deploy.md).