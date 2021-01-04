---
title: 'Tutorial: Sitzungs- und geräteübergreifendes Freigeben von Ankern'
description: In diesem Tutorial wird beschrieben, wie Sie Azure Spatial Anchors-Bezeichner zwischen Android/iOS-Geräten in Unity mit einem Back-End-Dienst freigeben.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 3048eb1ca7f9312a43bc1ab5885bf19d4c2e7931
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185386"
---
# <a name="tutorial-share-spatial-anchors-across-sessions-and-devices"></a>Tutorial: Sitzungs- und geräteübergreifendes Freigeben von Raumankern

Azure Spatial Anchors ist ein plattformübergreifender Entwicklerdienst, mit dem Sie Mixed Reality-Umgebungen mit Objekten erstellen können, die ihre Position im Zeitverlauf geräteübergreifend beibehalten. 

In diesem Tutorial verwenden Sie [Azure Spatial Anchors](../overview.md), um im Rahmen einer Sitzung Anker zu erstellen und diese anschließend auf dem gleichen oder auf einem anderen Gerät zu finden. Die gleichen Anker können auch von mehreren Geräten am gleichen Ort und zur gleichen Zeit gefunden werden.

![Animation, die mit einem mobilen Gerät erstellte Raumanker zeigt, die im Laufe der Tage mit einem anderen Gerät verwendet werden.](./media/persistence.gif)


In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Bereitstellen einer ASP.NET Core-Web-App in Azure, mit der Sie Anker freigeben können, und Speichern der Anker im Speicher für einen bestimmten Zeitraum
> * Konfigurieren der AzureSpatialAnchorsLocalSharedDemo-Szene im Unity-Beispiel aus den Schnellstartanleitungen, um die Web-App für die Freigabe von Ankern (Sharing Anchors-Web-App) zu nutzen
> * Bereitstellen und Ausführen der Anker auf einem oder mehreren Geräten

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

> [!NOTE]
> Sie verwenden in diesem Tutorial zwar Unity und eine ASP.NET Core-Web-App, aber dieser Ansatz dient nur als Beispiel für die geräteübergreifende Freigabe von Azure Spatial Anchors-Bezeichnern. Sie können auch andere Sprachen und Back-End-Technologien verwenden, um dieses Ziel zu erreichen.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-the-sample-project"></a>Herunterladen des Beispielprojekts

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>Bereitstellen des Diensts für die Freigabe von Ankern (Sharing Anchors)

## <a name="visual-studio"></a>[Visual Studio](#tab/VS)

Öffnen Sie Visual Studio und dann das Projekt im Ordner *Sharing\SharingServiceSample*.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/VSC)

Sie müssen eine Ressourcengruppe und einen App Service-Plan erstellen, bevor Sie den Dienst in Visual Studio Code bereitstellen.

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

Navigieren Sie zum <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a>, und melden Sie sich dann bei Ihrem Azure-Abonnement an.

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Wählen Sie neben **Ressourcengruppe** die Option **Neu** aus.

Nennen Sie die Ressourcengruppe **myResourceGroup**, und wählen Sie anschließend **OK** aus.

### <a name="create-an-app-service-plan"></a>Wie erstelle ich einen Plan?

[!INCLUDE [app-service-plan](../../../includes/app-service-plan.md)]

Wählen Sie neben **Hostingplan** die Option **Neu** aus.

Verwenden Sie im Bereich **Hostingplan konfigurieren** die folgenden Einstellungen:

| Einstellung | Vorgeschlagener Wert | BESCHREIBUNG |
|-|-|-|
|App Service-Plan| MySharingServicePlan | Name des App Service-Plans |
| Standort | USA (Westen) | Das Rechenzentrum, in dem die Web-App gehostet wird |
| Size | Kostenlos | Der [Tarif](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), der die Hostingfunktionen festlegt |

Klicken Sie auf **OK**.

Öffnen Sie Visual Studio Code und dann das Projekt im Ordner *Sharing\SharingServiceSample*. 

Wenn Sie den Freigabedienst über Visual Studio Code bereitstellen möchten, befolgen Sie die Anweisungen unter <a href="/aspnet/core/tutorials/publish-to-azure-webapp-using-vscode?view=aspnetcore-2.2#open-it-with-visual-studio-code" target="_blank">Veröffentlichen einer ASP.NET Core-App in Azure mit Visual Studio Code</a>. Beginnen Sie mit dem Abschnitt „Öffnen mit Visual Studio Code“. Erstellen Sie kein weiteres ASP.NET-Projekt wie in den obigen Schritten beschrieben, da Sie bereits über das Projekt für die Bereitstellung und Veröffentlichung verfügen: SharingServiceSample.

---

## <a name="deploy-the-sample-app"></a>Bereitstellen der Beispiel-App

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine ASP.NET Core-Web-App in Azure bereitgestellt sowie eine Unity-App konfiguriert und bereitgestellt. Sie haben Raumanker mit der App erstellt und mithilfe der ASP.NET Core-Web-App für andere Geräte freigegeben.

Sie können Ihre ASP.NET Core-Web-App so optimieren, dass sie Azure Cosmos DB zur Speicherung der freigegebenen Raumankerbezeichner nutzt. Indem Sie Unterstützung für Azure Cosmos DB hinzufügen, können Sie noch heute Ihre ASP.NET Core-Web-App einen Anker erstellen lassen. Anschließend können Sie mithilfe des Ankerbezeichners, der in Ihrer Web-App gespeichert ist, festlegen, dass die App einige Tage später den Anker erneut sucht.

> [!div class="nextstepaction"]
> [Verwenden von Azure Cosmos DB zum Speichern von Ankern](./tutorial-use-cosmos-db-to-store-anchors.md)