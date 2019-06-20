---
title: Optionen für die Zustandsspeicherung in Azure Service Fabric Mesh | Microsoft-Dokumentation
description: Weitere Informationen zur zuverlässigen Zustandsspeicherung in Service Fabric Mesh-Anwendungen, die in Azure Service Fabric Mesh ausgeführt werden
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/27/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: ef51040d1bad74ee74d5901d1f5acbe875c02a07
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60810537"
---
# <a name="state-management-with-service-fabric"></a>Zustandsverwaltung mit Service Fabric

Service Fabric unterstützt viele verschiedene Optionen für die Zustandsspeicherung. Eine grundlegende Übersicht über die Zustandsverwaltungsmuster und Service Fabric finden Sie unter [Service Fabric-Konzepte: Zustand](/azure/service-fabric/service-fabric-concepts-state). Alle diese Konzepte gelten unabhängig davon, ob Ihre Dienste innerhalb oder außerhalb von Service Fabric Mesh ausgeführt werden. 

Mit Service Fabric Mesh können Sie ganz einfach eine neue Anwendung bereitstellen und mit einem bestehenden Datenspeicher verbinden, der in Azure gehostet wird. Neben der Verwendung einer beliebigen Remotedatenbank gibt es weitere Möglichkeiten, Daten zu speichern, je nachdem, ob der Dienst lokal oder remote gespeichert werden soll. 

## <a name="volumes"></a>Volumes

Container verwenden häufig temporäre Datenträger. Temporäre Datenträger sind jedoch flüchtig, sodass Sie einen neuen temporären Datenträger erhalten und die Informationen verlieren, wenn ein Container abstürzt. Außerdem ist es schwierig, Informationen auf temporären Datenträgern mit anderen Containern gemeinsam zu verwenden. Volumes sind Verzeichnisse, die in Ihre Containerinstanzen eingebunden werden, und mit denen Sie Zustände erhalten können. Volumes bieten Ihnen einen universellen Dateispeicher und ermöglichen das Lesen/Schreiben von Dateien über normale Datenträger-E/A-Datei-APIs. Die Volume-Ressource beschreibt, wie ein Verzeichnis bereitgestellt wird und welcher Hintergrundspeicher verwendet werden soll. Sie können Azure File Storage oder den Service Fabric Volume-Datenträger zum Speichern von Daten auswählen.

![Volumes][image3]

### <a name="service-fabric-reliable-volume"></a>Service Fabric Reliable Volume

Service Fabric Reliable Volume ist ein Docker-Volumetreiber, der verwendet wird, um ein lokales Volume für einen Container bereitzustellen. Lese- und Schreibvorgänge sind lokale Vorgänge und sehr schnell. Daten werden auf sekundäre Knoten repliziert und werden damit hochverfügbar. Failover ist ebenfalls schnell. Wenn ein Container abstürzt, wird ein Failover auf einen Knoten ausgeführt, der bereits über eine Kopie Ihrer Daten verfügt. Ein Beispiel finden Sie unter [Bereitstellen einer App mit Service Fabric Reliable Volume](service-fabric-mesh-howto-deploy-app-sfreliable-disk-volume.md).

### <a name="azure-files-volume"></a>Azure Files Volume

Azure Files Volume ist ein Docker-Volumetreiber, der verwendet wird, um eine Azure Files-Freigabe in einen Container einzubinden. Azure Files Storage verwendet Netzwerkspeicher, sodass Lese- und Schreibvorgänge über das Netzwerk erfolgen. Im Vergleich zu Service Fabric Reliable Volume ist die Speicherung von Azure Files weniger leistungsstark, bietet aber eine kostengünstigere und absolut zuverlässige Datenoption. Ein Beispiel finden Sie unter [Bereitstellen einer App mit Azure Files Volume](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Anwendungsmodell finden Sie unter [Service Fabric-Ressourcen](service-fabric-mesh-service-fabric-resources.md).

[image3]: ./media/service-fabric-mesh-storing-state/volumes.png
