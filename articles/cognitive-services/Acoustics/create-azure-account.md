---
title: Einrichten eines Azure Batch-Kontos für das Projekt Akustik
titlesuffix: Azure Cognitive Services
description: In dieser Vorgehensweise wird beschrieben, wie ein Azure Batch-Konto für die Verwendung mit Projekt Akustik Unity- und Unreal-Engineintegrationen eingerichtet wird.
services: cognitive-services
author: ashtat
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: 7a7f5f6738b4bc96b6248deb062c7b3f63048148
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61335698"
---
# <a name="project-acoustics-azure-batch-account-setup"></a>Einrichten eines Azure Batch-Kontos für das Projekt Akustik
In dieser Vorgehensweise wird beschrieben, wie ein Azure Batch-Konto für die Verwendung mit Projekt Akustik Unity- und Unreal-Engineintegrationen eingerichtet wird.

## <a name="get-an-azure-subscription"></a>Erhalten eines Azure-Abonnements
Zum Einrichten von Batch- und Storage-Konten ist ein [Azure-Abonnement](https://azure.microsoft.com/free/) erforderlich. Wenn Sie sich zum ersten Mal anmelden, stellt Azure einige zeitlich begrenzte kostenlose Ressourcen und ein Guthaben in Höhe von 200 USD bereit.

## <a name="create-azure-batch-and-storage-accounts"></a>Erstellen von Azure Batch- und Azure Storage-Konten
Befolgen Sie als Nächstes [diese Anweisungen](https://docs.microsoft.com/azure/batch/batch-account-create-portal), um Ihre Azure Batch-Konten und die damit verbundenen Azure Storage-Konten einzurichten.

Wählen Sie für Batch- und Storage-Konten Standardoptionen aus:
  
  ![Screenshot der Optionen für neue Konten von Azure Batch mit Standardeinstellungen](media/new-batch-account-create.png)

  ![Screenshot der Optionen für neue Konten von Azure Storage mit Standardeinstellungen](media/batch-storage-account-create.png)

Das Bereitstellen der Konten durch Azure dauert einige Minuten. Halten Sie nach einer Benachrichtigung zum Abschluss in der oberen rechten Ecke des Portals Ausschau.
  
  ![Screenshot der Benachrichtigung über bereitgestellte Azure-Konten](media/batch-accounts-deploy-notification.png)

Ihre Konten sollten nun in Ihrem Dashboard angezeigt werden.
  
  ![Screenshot des Dashboards des Azure-Portals mit einem Batch- und Storage-Konto](media/azure-portal-dashboard.png)

## <a name="set-up-acoustics-bake-ui-with-azure-credentials"></a>Einrichten der Benutzeroberfläche für den Acoustics-Bake-Vorgang mit Azure-Anmeldeinformationen
Klicken Sie im Dashboard auf den Link für das Batch-Konto und anschließend auf der Seite „Batch-Konto“ auf den Link **Keys** (Schlüssel), um auf Ihre Anmeldeinformationen zuzugreifen.
  
  ![Screenshot eines Azure Batch-Kontos mit hervorgehobenem Link zur Seite „Schlüssel“](media/batch-access-keys.png)

  ![Screenshot der Schlüsselseite des Azure Batch-Kontos mit Zugriffsschlüsseln](media/batch-keys-info.png)

Klicken Sie auf der Seite auf den Link **Storage-Konto**, um auf die Anmeldeinformationen Ihres Azure Storage-Kontos zuzugreifen.
  
  ![Screenshot der Schlüsselseite des Azure Storage-Kontos mit Zugriffsschlüsseln](media/storage-keys-info.png)

Geben Sie diese Anmeldeinformationen in das [Unity Bake-Plug-In](unity-baking.md) oder das [Unreal Bake-Plug-In](unreal-baking.md) ein.

## <a name="node-types-and-region-support"></a>Knotentypen und Unterstützung für Regionen
Für Projekt Akustik sind für Compute optimierte Azure VM-Knoten der Fsv2- und H-Serie erforderlich, die möglicherweise nicht in allen Azure-Regionen unterstützt werden. Überprüfen Sie [diese Tabelle](https://azure.microsoft.com/global-infrastructure/services), um sicherzustellen, dass Sie den richtigen Ort für Ihr Batch-Konto auswählen.
![Screenshot der Azure Virtual Machines nach Region](media/azure-regions.png) 

## <a name="upgrading-your-quota"></a>Upgraden des Kontingents
Azure Batch-Konten werden beim Erstellen des Kontos mit einem Limit von 20 Compute-Kernen bereitgestellt. Sie sollten dieses Limit ggf. erhöhen. Dadurch werden die Zeiten für den Bake-Vorgang verkürzt, da Sie die Akustik-Workload auf mehreren Knoten (bis zur Anzahl von Testpunkten in Ihrer Szene) gleichzeitig verarbeiten können. Sie können eine Kontingenterhöhung anfordern, indem Sie auf Ihrer Azure Batch-Portalseite auf den Link **Kontingent** und anschließend auf **Kontingenterhöhung anfordern** klicken:

![Screenshot der Azure-Seite „Kontingent“](media/azure-quotas.png)

## <a name="next-steps"></a>Nächste Schritte
* Integrieren des Projekt Akustik-Plug-Ins in Ihr [Unity](unity-integration.md)- oder [Unreal](unreal-integration.md)-Projekt

