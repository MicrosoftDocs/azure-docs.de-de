---
title: Erstellen eines Datencontrollers
description: Erstellen Sie einen Azure Arc-Datencontroller in einem typischen Kubernetes-Cluster mit mehreren Knoten, den Sie bereits bereitgestellt haben.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: dd20a76ebc833320050c87862ad5022e99cd453a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384961"
---
# <a name="create-the-azure-arc-data-controller"></a>Erstellen des Azure Arc-Datencontrollers

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-creating-the-azure-arc-data-controller"></a>Übersicht über die Erstellung des Azure Arc-Datencontrollers

Azure Arc-fähige Datendienste können in vielen verschiedenen Typen von Kubernetes-Clustern und Managed Kubernetes-Diensten unter Verwendung unterschiedlicher Ansätze erstellt werden.

Derzeit werden die folgenden Kubernetes-Dienste und -Distributionen unterstützt:

- Azure Kubernetes Service (AKS)
- Azure Kubernetes Service Engine (AKS-Engine) in Azure Stack
- Azure Kubernetes Service in Azure Stack HCI
- Azure RedHat OpenShift (ARO)
- OpenShift Container Platform (OCP)
- AWS Elastic Kubernetes Service (EKS)
- Google Cloud Kubernetes Engine (GKE)
- Open-Source-Upstreamversion von Kubernetes – in der Regel mit kubeadm bereitgestellt

> [!IMPORTANT]
> * Die unterstützte Mindestversion von Kubernetes ist v1.17. Weitere Informationen finden Sie unter [Bekannte Probleme](./release-notes.md#known-issues). 
> * Die unterstützte Mindestversion von OCP ist 4.3.
> * Unter [Konnektivitätsanforderungen](connectivity.md) erfahren Sie, welche Konnektivität zwischen Ihrer Umgebung und Azure erforderlich ist.
> * Informationen zum Konfigurieren Ihres beständigen Speichers finden Sie in der [Anleitung zur Speicherkonfiguration](storage-configuration.md).
> * Wenn Sie Azure Kubernetes Service verwenden, muss die Größe der Workerknoten-VM Ihres Clusters mindestens **Standard_D8s_v3** betragen, und der Cluster muss **Premium-Datenträger** nutzen. Der Cluster sollte sich nicht über mehrere Verfügbarkeitszonen hinweg ausdehnen. 
> * Wenn Sie eine andere Kubernetes-Distribution oder einen anderen Kubernetes-Dienst verwenden, sollten Sie sicherstellen, dass der Knoten eine Mindestgröße von 8 GB RAM und 4 Kerne umfasst und dass auf allen Kubernetes-Knoten eine Gesamtkapazität von 32 GB RAM zur Verfügung steht. Beispielsweise können Sie über einen Knoten mit 32 GB RAM und 4 Kernen oder über zwei Knoten mit jeweils 16 GB RAM und vier Kernen verfügen.

> [!NOTE]
> Bei Verwendung von Red Hat OpenShift Container Platform in Azure empfiehlt es sich, die neueste verfügbare Version zu verwenden.

Abhängig von der gewählten Option sind bestimmte Tools _erforderlich_. Es wird jedoch empfohlen, [alle Clienttools zu installieren](./install-client-tools.md), bevor Sie mit der Erstellung des Azure Arc-Datencontrollers beginnen.

Unabhängig von der gewählten Option müssen Sie während der Erstellung die folgenden Informationen angeben:

- **Data controller name** (Name des Datencontrollers): Ein beschreibender Name für Ihren Datencontroller, z. B. „Produktionsdatencontroller“, „Datencontroller Seattle“.
- **Data controller username** (Benutzername für Datencontroller): Ein Benutzername für den Datencontrolleradministrator.
- **Data controller password** (Kennwort für Datencontroller): Ein Kennwort für den Datencontrolleradministrator.
- **Name of your Kubernetes namespace** (Name des Kubernetes-Namespace): Der Name des Kubernetes-Namespace, in dem Sie den Datencontroller erstellen möchten.
- **Connectivity mode** (Konnektivitätsmodus): Der Konnektivitätsmodus bestimmt den Grad der Konnektivität zwischen Ihrer Azure Arc-fähigen Datendienstumgebung und Azure. Von der Vorschauversion werden aktuell nur Modi mit indirekter oder direkter Verbindung unterstützt.  Weitere Informationen finden Sie unter [Konnektivitätsmodi und -anforderungen](./connectivity.md). 
- **Azure subscription ID** (Azure-Abonnement-ID): Die Azure-Abonnement-GUID für den Ort, an dem die Datencontrollerressource in Azure erstellt werden soll.
- **Azure resource group name** (Azure-Ressourcengruppenname): Der Name der Ressourcengruppe, in der die Datencontrollerressource in Azure erstellt werden soll.
- **Azure Location** (Azure-Standort): Der Azure-Standort, an dem die Metadaten der Datencontrollerressource in Azure gespeichert werden. Eine Liste der verfügbaren Regionen finden Sie unter [Globale Azure-Infrastruktur/Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc).

## <a name="next-steps"></a>Nächste Schritte

Es gibt mehrere Optionen zum Erstellen des Azure Arc-Datencontrollers:

> **Möchten Sie es selbst ausprobieren?**  
> Mit dem [Azure Arc-Schnelleinstieg](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) für Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) oder auf einer Azure-VM können Sie schnell die ersten Schritte unternehmen.
> 
- [Erstellen eines Datencontrollers mit [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](create-data-controller-using-azdata.md)
- [Erstellen eines Datencontrollers mit Azure Data Studio](create-data-controller-azure-data-studio.md)
- [Erstellen eines Datencontrollers über das Azure-Portal mithilfe eines Jupyter Notebooks in Azure Data Studio](create-data-controller-resource-in-azure-portal.md)
- [Erstellen eines Datencontrollers mit Kubernetes-Tools wie kubectl oder oc](create-data-controller-using-kubernetes-native-tools.md)
- [Erstellen eines Datencontrollers mit dem Azure Arc-Schnelleinstieg für eine beschleunigte Testbereitstellung](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)
