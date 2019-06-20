---
title: 'Tutorial: Sitzungs- und geräteübergreifendes Freigeben von Azure Spatial Anchors mit einem Azure Cosmos DB-Back-End | Microsoft-Dokumentation'
description: In diesem Tutorial wird beschrieben, wie Sie Azure Spatial Anchors-Bezeichner zwischen Android/iOS-Geräten in Unity mit einem Back-End-Dienst und Azure Cosmos DB freigeben.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 19c2298c9bda03acba28496a97c89c2a53e3c44e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65964903"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Tutorial: Sitzungs- und geräteübergreifendes Freigeben von Azure Spatial Anchors mit einem Azure Cosmos DB-Back-End

In diesem Tutorial erfahren Sie, wie Sie [Azure Spatial Anchors](../overview.md) verwenden, um im Rahmen einer Sitzung Anker zu erstellen und diese anschließend in einer anderen Sitzung auf dem gleichen oder auf einem anderen Gerät zu finden. Die zweite Sitzung könnte z.B. an einem anderen Tag stattfinden. Die gleichen Anker können auch von mehreren Geräten am gleichen Ort und zur selben Zeit gefunden werden.

![GIF zur Veranschaulichung der Objektpersistenz](./media/persistence.gif)

[Azure Spatial Anchors](../overview.md) ist ein plattformübergreifender Entwicklerdienst, mit dem Sie Mixed Reality-Umgebungen mit Objekten erstellen können, die ihre Position im Zeitverlauf geräteübergreifend beibehalten. Nach Abschluss des Vorgangs verfügen Sie über eine App, die auf zwei oder mehr Geräten bereitgestellt werden kann. Für Spatial Anchors, die von einer Instanz erstellt werden, werden die Bezeichner über Azure Cosmos DB mit anderen Ankern gemeinsam verwendet.

Sie lernen Folgendes:

> [!div class="checklist"]
> * Bereitstellen einer ASP.NET Core-Web-App in Azure, die zum Freigeben von Ankern verwendet werden kann (Speicherung in Azure Cosmos DB).
> * Konfigurieren der AzureSpatialAnchorsLocalSharedDemo-Szene im Unity-Beispiel aus den Azure-Schnellstartanleitungen, um die Sharing Anchors-Web-App zu nutzen.
> * Bereitstellen und Ausführen einer App auf einem oder mehreren Geräten.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Beachten Sie Folgendes: Sie verwenden in diesem Tutorial zwar Unity und Azure Cosmos DB, aber dies dient nur als Beispiel für die geräteübergreifende Freigabe von Spatial Anchors-Bezeichnern. Sie können auch andere Sprachen und Back-End-Technologien verwenden, um dieses Ziel zu erreichen. Darüber hinaus setzt die in diesem Tutorial verwendete ASP.NET Core-Web-App .NET Core 2.2 SDK voraus. Es lässt sich problemlos auf Web-Apps für Windows ausführen, aber derzeit nicht auf Web-Apps für Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>Erstellen eines Datenbankkontos

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Kopieren Sie den `Connection String`, da Sie ihn benötigen.

## <a name="open-the-sample-project-in-unity"></a>Öffnen des Beispielprojekts in Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>Bereitstellen des Diensts für die Freigabe von Ankern (Sharing Anchors)

Öffnen Sie Visual Studio und dann das Projekt im Ordner `Sharing\SharingServiceSample`.

### <a name="configure-the-service-to-use-your-azure-cosmos-db-database"></a>Konfigurieren des Diensts zur Verwendung Ihrer Azure Cosmos DB-Datenbankinstanz

Öffnen Sie `SharingService\Startup.cs` im **Projektmappen-Explorer**.

Suchen Sie oben in der Datei nach `#define INMEMORY_DEMO`, und kommentieren Sie diese Zeile aus. Speichern Sie die Datei .

Öffnen Sie `SharingService\appsettings.json` im **Projektmappen-Explorer**.

Suchen Sie nach der `StorageConnectionString`-Eigenschaft, und legen Sie den Wert auf den `Connection String`-Text fest, den Sie im Schritt [Erstellen eines Datenbankkontos](#create-a-database-account) kopiert haben. Speichern Sie die Datei .

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Azure Cosmos DB verwendet, um Ankerbezeichner geräteübergreifend freizugeben. Weitere Informationen zur Verwendung von Azure Spatial Anchors in einer neuen Unity HoloLens-App finden Sie im nächsten Tutorial.

> [!div class="nextstepaction"]
> [Starten einer neuen Android-App](./tutorial-new-unity-hololens-app.md)