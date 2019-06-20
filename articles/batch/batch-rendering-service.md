---
title: Übersicht über das Rendering – Azure Batch
description: Enthält eine Einführung in die Verwendung von Azure für das Rendering und eine Übersicht über Azure Batch-Renderingfunktionen.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: d4423b22c4c8afea5afa9c7040e081665b17ba87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60774028"
---
# <a name="rendering-using-azure"></a>Rendern mit Azure

Das „Rendering“ ist der Prozess, bei dem 3D-Modelle in 2D-Bilder konvertiert werden. Dateien mit 3D-Szenen werden in Anwendungen wie Autodesk 3ds Max, Autodesk Maya und Blender erstellt.  Mit Renderinganwendungen, z.B. Autodesk Maya, Autodesk Arnold, Chaos Group V-Ray und Blender Cycles, werden 2D-Bilder erstellt.  In einigen Fällen werden aus den Szenendateien einzelne Bilder erstellt. Es ist aber eher üblich, mehrere Bilder zu modellieren und zu rendern und dann in einer Animation zu kombinieren.

Die Renderingworkload wird häufig für Spezialeffekte (VFX) in der Medien- und Unterhaltungsbranche eingesetzt. Rendering wird auch in vielen anderen Branchen verwendet, z. B. in der Werbebranche, im Einzelhandel, in der Öl-und Gasindustrie sowie in der Fertigungsindustrie.

Der Renderingprozess ist rechenintensiv. Es kann sein, das viele Frames bzw. Bilder erstellt werden müssen, und das Rendern jedes Bilds kann viele Stunden dauern.  Das Rendering ist daher perfekt für die Batchverarbeitung von Workloads geeignet, indem Azure und Azure Batch genutzt wird, um viele Rendervorgänge parallel auszuführen.

## <a name="why-use-azure-for-rendering"></a>Warum sollte Azure für das Rendering verwendet werden?

Das Rendering ist aus vielen Gründen eine Workload, die für Azure und Azure Batch perfekt geeignet ist:

* Renderingaufträge können in viele Teile zerlegt werden, die mithilfe von mehreren VMs parallel ausgeführt werden können:
  * Animationen bestehen aus vielen Frames, und jeder Frame kann parallel gerendert werden.  Je mehr VMs für die Verarbeitung der einzelnen Frames verfügbar sind, desto schneller können alle Frames und die Animation erstellt werden.
  * Bei einigen Softwareanwendungen für das Rendering können einzelne Frames in mehrere Teile zerlegt werden, z.B. Kacheln oder Segmente.  Die Teile können jeweils separat gerendert und dann zum fertigen Bild kombiniert werden, wenn alle Vorgänge abgeschlossen sind.  Je mehr VMs verfügbar sind, desto schneller kann ein Frame gerendert werden.
* Für Renderingprojekte kann ein großer Skalierungsaufwand anfallen:
  * Einzelne Frames können komplex sein, und auch bei Verwendung von Highend-Hardware kann das Rendern viele Stunden dauern. Animationen können aus mehreren Hunderttausend Frames bestehen.  Ein riesiger Computeaufwand ist erforderlich, um in einem angemessenen Zeitraum Animationen von hoher Qualität zu rendern.  In einigen Fällen wurden mehr als 100.000 Kerne genutzt, um Tausende Frames parallel zu rendern.
* Renderingprojekte sind projektbasiert, und der Computeaufwand variiert jeweils:
  * Ordnen Sie je nach Bedarf Compute- und Speicherkapazität zu, führen Sie in Abhängigkeit der Auslastung während eines Projekts das vertikale Hoch- oder Herunterskalieren durch, und heben Sie die Zuordnung auf, wenn ein Projekt beendet ist.
  * Hierbei zahlen Sie für die Kapazität, wenn diese zugeordnet ist, aber es fallen keine Kosten an, wenn keine Auslastung vorhanden ist, z.B. zwischen Projekten.
  * Seien Sie auf Auslastungsspitzen aufgrund von unerwarteten Änderungen vorbereitet. Erhöhen Sie die Skalierung, wenn es in einer späten Phase eines Projekts zu unerwarteten Änderungen kommt und diese Änderungen nach einem knappen Zeitplan verarbeitet werden müssen.
* Wählen Sie basierend auf Anwendung, Workload und Zeitraum aus einem großen Hardwareangebot:
  * Für Azure besteht ein großes Angebot an Hardware, die mit Batch zugeordnet und verwaltet werden kann.
  * Je nach Projekt kann das beste Preis-Leistungs-Verhältnis oder auch die beste Gesamtleistung am wichtigsten sein.  Unterschiedliche Szenen bzw. Renderinganwendungen verfügen über unterschiedliche Arbeitsspeicheranforderungen.  Bei einigen Renderinganwendungen können GPUs genutzt werden, um die beste Leistung oder bestimmte Features zu erhalten. 
* Kostenreduzierung durch VMs mit niedriger Priorität:
  * VMs mit niedriger Priorität sind im Vergleich zu regulären On-Demand-VMs zu einem günstigen Rabattpreis erhältlich und für einige Auftragsarten geeignet.
  * VMs mit niedriger Priorität können von Azure Batch zugeordnet werden, und über Batch wird ein flexibler Einsatz ermöglicht, um einen großen Bereich von Anforderungen abzudecken.  Batch-Pools können sowohl dedizierte VMs als auch VMs mit niedriger Priorität enthalten, wobei die Mischung von VM-Typen jederzeit geändert werden kann.

## <a name="options-for-rendering-on-azure"></a>Optionen für das Rendering in Azure

Es gibt eine Reihe von Azure-Funktionen, die zum Rendern von Workloads verwendet werden können.  Welche Funktionen genutzt werden, richtet sich nach der vorhandenen Umgebung und den Anforderungen.

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>Vorhandene lokale Renderingumgebung mit einer Anwendung für die Renderverwaltung

Der Normalfall ist, dass eine vorhandene lokale Renderfarm mit einer Anwendung für die Renderverwaltung verwaltet wird, z.B. PipelineFX Qube, Royal Render oder Thinkbox Deadline.  Die Anforderung besteht darin, die Kapazität der lokalen Renderfarm mit Azure-VMs zu erweitern.

Entweder ist die Azure-Unterstützung in die Software für die Renderverwaltung integriert, oder wir stellen Plug-Ins zur Verfügung, mit denen die Azure-Unterstützung hinzugefügt wird. Weitere Informationen zu den unterstützten Anwendungen für die Renderverwaltung und den aktivierten Funktionen finden Sie im Artikel zum [Verwenden von Render-Managern](https://docs.microsoft.com/azure/batch/batch-rendering-render-managers).

### <a name="custom-rendering-workflow"></a>Workflow für benutzerdefiniertes Rendern

Die Anforderung besteht darin, dass eine vorhandene Renderfarm mit VMs erweitert wird.  Über Azure Batch-Pools kann eine große Zahl von VMs zugeordnet werden, und VMs mit niedriger Priorität können verwendet und mit Vollpreis-VMs auf dynamische Weise automatisch skaliert werden. Außerdem kann für beliebte Renderinganwendungen eine nutzungsbasierte Lizenzierung in Anspruch genommen werden.

### <a name="no-existing-render-farm"></a>Keine vorhandene Renderfarm

Mit Clientarbeitsstationen kann das Rendering zwar durchgeführt werden, aber die Renderingworkload nimmt zu, und der Vorgang dauert zu lange, wenn nur die Kapazität der Arbeitsstationen verwendet wird.  Azure Batch kann verwendet werden, um sowohl Computeressourcen für die Renderfarm zuzuordnen, als auch die Renderaufträge für die Azure-Renderfarm zu planen.

## <a name="azure-batch-rendering-capabilities"></a>Azure Batch-Renderingfunktionen

Azure Batch ermöglicht die Ausführung von parallelen Workloads in Azure.  Außerdem wird die Erstellung und Verwaltung einer großen Zahl von VMs ermöglicht, auf denen Anwendungen installiert und ausgeführt werden können.  Darüber hinaus sind umfassende Funktionen für die Auftragsplanung verfügbar, um Instanzen dieser Anwendungen auszuführen und die Zuweisung von Aufgaben zu VMs, Einreihung in Warteschlangen, Anwendungsüberwachung usw. durchzuführen.

Azure Batch wird für viele Workloads genutzt, aber die folgenden Funktionen sind speziell dafür verfügbar, die Ausführung von Renderingworkloads einfacher und schneller zu machen.

* VM-Images mit vorinstallierten Grafik- und Renderinganwendungen:
  * Es sind Azure Marketplace-VM-Images verfügbar, die beliebte Grafik- und Renderinganwendungen enthalten. Sie müssen die Anwendungen also nicht selbst installieren und keine benutzerdefinierten Images mit den installierten Anwendungen erstellen. 
* Nutzungsbasierte Lizenzierung für Renderinganwendungen:
  * Sie können die Option wählen, bei der Sie für die Anwendungen nach Minuten und zusätzlich für die Compute-VMs bezahlen. So vermeiden Sie, dass Sie Lizenzen erwerben und ggf. einen Lizenzserver für die Anwendungen konfigurieren müssen.  Nutzungsbasierte Bezahlung bedeutet auch, dass Sie auf variierende und unerwartete Lasten vorbereitet sind, da es keine feste Anzahl von Lizenzen gibt.
  * Es ist auch möglich, die vorinstallierten Anwendungen mit Ihren eigenen Lizenzen zu nutzen und nicht die nutzungsbasierte Lizenzierung zu verwenden. Dazu müssen Sie in der Regel einen lokalen oder auf Azure basierenden Lizenzserver installieren und das virtuelle Azure-Netzwerk verwenden, um den Renderingpool mit dem Lizenzserver zu verbinden.
* Plug-Ins für Anwendungen für den Cliententwurf und die Modellierung:
  * Mit Plug-Ins können Endbenutzer Azure Batch direkt über die Clientanwendung nutzen, z.B. Autodesk Maya, und so Pools erstellen, Aufträge übermitteln und weitere Computekapazität nutzen, um schnellere Rendervorgänge zu erzielen.
* Integration eines Render-Managers:
  * Azure Batch wird in Anwendungen für die Renderverwaltung integriert, oder es sind Plug-Ins verfügbar, um die Azure Batch-Integration bereitzustellen.

Es gibt mehrere Möglichkeiten, um Azure Batch zu verwenden. Diese gelten auch für das Rendering mit Azure Batch.

* APIs:
  * Schreiben Sie Code mit [REST](https://docs.microsoft.com/rest/api/batchservice), [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch), [Python](https://docs.microsoft.com/python/api/overview/azure/batch), [Java](https://docs.microsoft.com/java/api/overview/azure/batch) oder anderen unterstützten APIs.  Entwickler können Azure Batch-Funktionen in ihre vorhandenen Anwendungen oder den Workflow integrieren (in der Cloud oder lokal).  Für das [Autodesk Maya-Plug-In](https://github.com/Azure/azure-batch-maya) wird die Batch-Python-API verwendet, um Batch aufzurufen und Pools zu erstellen und zu verwalten, Aufträge und Aufgaben zu übermitteln und den Status zu überwachen.
* Befehlszeilentools:
  * Die [Azure-Befehlszeile](https://docs.microsoft.com/cli/azure/) oder [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) können verwendet werden, um Skripts für die Batch-Nutzung zu erstellen.
  * Besonders die Unterstützung für die Batch-CLI-Vorlage vereinfacht die Erstellung von Pools und die Übermittlung von Aufträgen.
* Benutzeroberflächen:
  * [Batch Explorer](https://github.com/Azure/BatchExplorer) ist ein plattformübergreifendes Clienttool, mit dem auch Batch-Konten verwaltet und überwacht werden können. Im Vergleich zur Benutzeroberfläche des Azure-Portals verfügt es aber über umfassendere Funktionen.  Es werden verschiedene Pool- und Auftragsvorlagen bereitgestellt, die für jede unterstützte Anwendung angepasst sind und verwendet werden können, damit Pools leicht erstellt und Aufträge übermittelt werden können.
  * Das Azure-Portal kann verwendet werden, um Azure Batch zu verwalten und zu überwachen.
* Plug-Ins für Clientanwendungen:
  * Es sind Plug-Ins verfügbar, mit denen das Batch-Rendering direkt in den Anwendungen für den Cliententwurf und die Modellierung verwendet werden kann. Die Plug-Ins rufen hauptsächlich die Batch Explorer-Anwendung mit Kontextinformationen zum aktuellen 3D-Modell auf.
  * Die folgenden Plug-Ins sind verfügbar:
    * [Azure Batch für Maya](https://github.com/Azure/azure-batch-maya)
    * [3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Erste Schritte mit Azure Batch-Rendering

Sehen Sie sich die folgenden Einführungstutorials an, um das Rendering mit Azure Batch auszuprobieren:

* [Tutorial: Rendern einer Blender-Szene mithilfe von Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
* [Tutorial: Rendern einer Szene mit Azure Batch](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)

## <a name="next-steps"></a>Nächste Schritte

Ermitteln Sie die Liste mit den Renderinganwendungen und Versionen, die in den Azure Marketplace-VM-Images enthalten sind, in [diesem Artikel](https://docs.microsoft.com/azure/batch/batch-rendering-applications).