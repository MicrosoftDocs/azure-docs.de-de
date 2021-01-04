---
ms.openlocfilehash: c2b1e1d87600e83733eb8bdbf5bc6e2e63376cf4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993093"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Lokalisieren eines cloudbasierten Raumankers

Die Möglichkeit, einen zuvor gespeicherten cloudbasierten Raumanker zu lokalisieren, ist einer der Hauptgründe für die Nutzung von Azure Spatial Anchors. Ein cloudbasierter Raumanker kann auf verschiedene Arten lokalisiert werden. Sie können jeweils eine Strategie pro Watcher verwenden.
- Lokalisieren von Ankern anhand des Bezeichners
- Lokalisieren von Ankern, die mit einem zuvor lokalisierten Anker verbunden sind. Informationen zu Ankerbeziehungen finden Sie [hier](../articles/spatial-anchors/concepts/anchor-relationships-way-finding.md).
- Lokalisieren eines Ankers unter Verwendung der [ungefähren Standortbestimmung](../articles/spatial-anchors/concepts/coarse-reloc.md)

> [!NOTE]
> Bei jeder Lokalisierung eines Ankers wird von Azure Spatial Anchors versucht, die erfassten Umgebungsdaten zu verwenden, um die visuellen Informationen des Ankers zu erweitern. Falls Sie beim Lokalisieren eines Ankers Probleme haben, kann es hilfreich sein, einen Anker zu erstellen und diesen dann mehrfach aus unterschiedlichen Winkeln und bei unterschiedlichen Lichtverhältnissen zu lokalisieren.

Wenn Sie cloudbasierte Raumanker anhand des Bezeichners lokalisieren, sollten Sie den Bezeichner des cloudbasierten Raumankers im Back-End-Dienst Ihrer Anwendung speichern und für alle Geräte verfügbar machen, die sich ordnungsgemäß authentifizieren können. Ein entsprechendes Beispiel finden Sie unter [Tutorial: Freigeben von Raumankern für Geräte](../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md).

Instanziieren Sie ein `AnchorLocateCriteria`-Objekt, legen Sie die zu suchenden IDs fest, und rufen Sie die `CreateWatcher`-Methode für die Sitzung auf, indem Sie Ihr `AnchorLocateCriteria`-Objekt angeben.