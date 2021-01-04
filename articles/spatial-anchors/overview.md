---
title: Azure Spatial Anchors-Übersicht
description: Es wird beschrieben, wie Azure Spatial Anchors Sie beim Entwickeln von plattformübergreifenden Mixed Reality-Umgebungen unterstützt.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: a422371bacddf049365562fce9af7e61f35089a1
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95487383"
---
# <a name="azure-spatial-anchors-overview"></a>Azure Spatial Anchors-Übersicht

Willkommen bei Azure Spatial Anchors. Mit Azure Spatial Anchors erhalten Entwickler wichtige Funktionen zum Entwickeln von Mixed Reality-Anwendungen mit räumlichem Bezug. Diese Anwendungen können für Microsoft HoloLens, iOS-basierte Geräte mit ARKit-Unterstützung und Android-basierte Geräte mit ARCore-Unterstützung geeignet sein. Mit Azure Spatial Anchors können Entwickler Mixed Reality-Plattformen verwenden, um Räume wahrzunehmen, präzise Points of Interest festzulegen und diese Points of Interest von unterstützten Geräten abzurufen.
Diese präzisen Points of Interest werden als Spatial Anchors bezeichnet.

![Plattformübergreifend](./media/cross-platform.png)

## <a name="examples"></a>Beispiele

Einige Beispiele für Anwendungsfälle, die mit Spatial Anchors ermöglicht werden, sind:

- [Umgebungen für mehrere Benutzer](tutorials/tutorial-share-anchors-across-devices.md): Azure Spatial Anchors erleichtert es Personen, die sich an demselben Ort befinden, an Mixed Reality-Anwendungen für mehrere Benutzer teilzunehmen. Beispielsweise können zwei Personen eine Partie Mixed Reality-Schach beginnen, indem sie ein virtuelles Schachbrett auf einem Tisch anordnen. Indem sie ihre Geräte dann in Richtung des Tischs halten, können sie das virtuelle Schachbrett zusammen anzeigen und damit interagieren.

- [Wegeleitsysteme](concepts/anchor-relationships-way-finding.md): Entwickler können auch Spatial Anchors-Instanzen miteinander verbinden, um dafür Beziehungen herzustellen. Eine App kann beispielsweise eine Benutzeroberfläche mit zwei oder mehr Points of Interest enthalten, mit denen ein Benutzer interagieren muss, um eine Aufgabe zu erfüllen. Diese Points of Interest können auf verbundene Weise erstellt werden. Später beim Durchführen der mehrteiligen Aufgabe durch den Benutzer kann die App nach Ankern fragen, die sich in der Nähe des aktuellen Ankers befinden, um den Benutzer zum nächsten Schritt der Aufgabe zu leiten.

- [Verwenden von virtuellen Inhalten in der realen Welt](how-tos/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor): Eine App kann über eine Funktion verfügen, mit der ein Benutzer einen virtuellen Kalender an der Wand eines Konferenzraums anordnen kann, den andere Personen mit einer Smartphone-App oder einem HoloLens-Gerät anzeigen können. In einer Industrieumgebung kann ein Benutzer beispielsweise Kontextinformationen zu einem Computer erhalten, indem er mit einer unterstützten Gerätekamera darauf zeigt.

Azure Spatial Anchors besteht aus einem verwalteten Dienst und Client-SDKs für unterstützte Geräteplattformen. Die folgenden Abschnitte enthalten Informationen zu den ersten Schritten beim Erstellen von Apps mit Azure Spatial Anchors.

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie Ihre erste App mit Azure Spatial Anchors.

> [!div class="nextstepaction"]
> [Unity (HoloLens)](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](quickstarts/get-started-xamarin-ios.md)
