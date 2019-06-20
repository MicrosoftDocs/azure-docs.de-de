---
title: Renderingfunktionen – Azure Batch
description: Verwenden von Azure Batch-Renderingfunktionen
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 2dff44f0b5b4b02c39c4c63f23ff64d55ca9d833
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60337601"
---
# <a name="using-azure-batch-rendering"></a>Verwenden des Renderings mit Azure Batch

Es gibt mehrere Möglichkeiten, das Rendering mit Azure Batch zu verwenden:

* APIs:
  * Schreiben Sie Code, indem Sie eine der Batch-APIs verwenden.  Entwickler können Azure Batch-Funktionen in ihre vorhandenen Anwendungen oder den Workflow integrieren (in der Cloud oder lokal).
* Befehlszeilentools:
  * Die [Azure-Befehlszeile](https://docs.microsoft.com/cli/azure/) oder [PowerShell](https://docs.microsoft.com/powershell/azure/overview) können verwendet werden, um Skripts für die Batch-Nutzung zu erstellen.
  * Besonders die [Unterstützung für die Batch-CLI-Vorlage](https://docs.microsoft.com/azure/batch/batch-cli-templates) vereinfacht die Erstellung von Pools und die Übermittlung von Aufträgen.
* Batch Explorer-Benutzeroberfläche:
  * [Batch Explorer](https://github.com/Azure/BatchLabs) ist ein plattformübergreifendes Clienttool, mit dem Batch-Konten verwaltet und überwacht werden können.
  * Für die einzelnen Renderinganwendungen werden verschiedene Pool- und Auftragsvorlagen bereitgestellt, die genutzt werden können, um auf einfache Weise Pools zu erstellen und Aufträge zu übermitteln.  Auf der Benutzeroberfläche der Anwendung sind einige Vorlagen aufgeführt, und auf die Vorlagendateien wird auf GitHub zugegriffen.
  * Benutzerdefinierte Vorlagen können ganz neu erstellt werden, oder die bereitgestellten Vorlagen auf GitHub können kopiert und geändert werden.
* Plug-Ins für Clientanwendung:
  * Es sind Plug-Ins verfügbar, mit denen das Batch-Rendering direkt in den Anwendungen für den Cliententwurf und die Modellierung verwendet werden kann.  Die Plug-Ins rufen hauptsächlich die Batch Explorer-Anwendung mit Kontextinformationen zum aktuellen 3D-Modell auf und enthalten Features für die Verwaltung von Medienobjekten.

Die beste Möglichkeit zum Ausprobieren des Renderings mit Azure Batch und die einfachste Vorgehensweise für Endbenutzer, bei denen es sich nicht um Entwickler oder Azure-Experten handelt, ist die Verwendung der Batch Explorer-Anwendung – entweder direkt oder per Aufruf über ein Plug-In der Clientanwendung.

## <a name="using-batch-explorer"></a>Verwenden von Batch Explorer

Ein Schritt-für-Schritt-Tutorial zur Verwendung von Batch Explorer für das Rendering finden Sie im [Blender-Tutorial](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender).

### <a name="download-and-install"></a>Herunterladen und Installieren

Batch Explorer-[Downloads sind für Windows, OSX und Linux verfügbar](https://azure.github.io/BatchExplorer/).

### <a name="using-templates-to-create-pools-and-run-jobs"></a>Verwenden von Vorlagen zum Erstellen von Pools und Ausführen von Aufträgen

Für die Nutzung mit Batch Explorer ist ein umfassender Satz mit Vorlagen verfügbar, mit denen das Erstellen von Pools und Übermitteln von Aufträgen für die verschiedenen Renderinganwendungen vereinfacht wird. Es müssen nicht alle Eigenschaften angegeben werden, die zum Erstellen von Pools, Aufträgen und Aufgaben direkt mit Batch erforderlich sind.  Die in Batch Explorer verfügbaren Vorlagen werden in einem [GitHub-Repository](https://github.com/Azure/BatchExplorer-data/tree/master/ncj) gespeichert und können dort angezeigt werden.

![Batch Explorer-Katalog](./media/batch-rendering-using/batch-explorer-gallery.png)

Es werden Vorlagen bereitgestellt, mit denen alle Anwendungen abgedeckt sind, die in den Rendering-VM-Images auf dem Marketplace vorhanden sind.  Für jede Anwendung sind mehrere Vorlagen vorhanden, z.B. Poolvorlagen für CPU- und GPU-Pools, Windows- und Linux-Pools. Zu den Auftragsvorlagen gehören Vorlagen für das Blender-Rendering (vollständiger Frame oder Kacheln) und das verteilte V-Ray-Rendering. Die bereitgestellten Vorlagen werden im Laufe der Zeit erweitert, um andere Batch-Funktionen abzudecken, z.B. die automatische Skalierung von Pools.

Es ist auch möglich, benutzerdefinierte Vorlagen zu erstellen – entweder ganz neu oder durch das Ändern der bereitgestellten Vorlagen. Benutzerdefinierte Vorlagen können verwendet werden, indem im Abschnitt „Katalog“ von Batch Explorer die Option „Lokale Vorlagen“ gewählt wird.

### <a name="file-system-and-data-movement"></a>Dateisystem und Datenverschiebung

Im Abschnitt „Daten“ in Batch Explorer können Dateien zwischen einem lokalen Dateisystem und Azure Storage-Konten kopiert werden.

## <a name="client-application-plug-ins"></a>Plug-Ins für Clientanwendung

Für einige Clientanwendungen sind Plug-Ins verfügbar.  Mit den Plug-Ins können Pools und Aufträge direkt aus der Anwendung erstellt werden, oder Batch Explorer kann aufgerufen werden.

* [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)
* [Autodesk 3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
* [Autodesk Maya](https://github.com/Azure/azure-batch-maya)

## <a name="next-steps"></a>Nächste Schritte

Beispiele für das Rendering mit Batch finden Sie in den folgenden beiden Tutorials:

* [Tutorial: Rendern einer Szene mit Azure Batch](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Tutorial: Rendern einer Blender-Szene mithilfe von Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)