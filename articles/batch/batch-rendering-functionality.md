---
title: Renderingfunktionen – Azure Batch
description: Spezifische Renderingfunktionen in Azure Batch
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: be6c0f9a8874507433606903bcbd58c7723d6a8a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62118686"
---
# <a name="azure-batch-rendering-capabilities"></a>Azure Batch-Renderingfunktionen

Azure Batch-Standardfunktionen werden verwendet, um Renderingworkloads und -anwendungen auszuführen. Batch enthält zudem bestimmte Funktionen zur Unterstützung von Renderingworkloads.

Eine Übersicht über Batch-Konzepte, z.B. Pools, Aufträge und Aufgaben, finden Sie in [diesem Artikel](https://docs.microsoft.com/azure/batch/batch-api-basics).

## <a name="batch-pools"></a>Batch-Pools

### <a name="rendering-application-installation"></a>Installation von Renderinganwendungen

In der Poolkonfiguration kann ein Rendering-VM-Image von Azure Marketplace angegeben werden, wenn nur die vorinstallierten Anwendungen verwendet werden sollen.

Zur Verfügung stehen ein Windows 2016-Image und ein CentOS-Image.  Im [Azure Marketplace](https://azuremarketplace.microsoft.com) finden Sie die VM-Images, indem Sie nach „Batch-Rendering“ suchen.

Ein Beispiel für eine Poolkonfiguration finden Sie im [Tutorial zum Rendern von Azure CLI](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli).  Das Azure-Portal und Batch Explorer umfassen GUI-Tools zum Auswählen eines Rendering-VM-Images beim Erstellen eines Pools.  Wenn Sie eine Batch-API verwenden, geben Sie beim Erstellen eines Pools die folgenden Eigenschaftswerte für [ImageReference](https://docs.microsoft.com/rest/api/batchservice/pool/add#imagereference) an:

| Herausgeber | Angebot | Sku | Version |
|---------|---------|---------|--------|
| Batch | rendering-centos73 | Rendering | neueste |
| Batch | rendering-windows2016 | Rendering | neueste |

Andere Optionen sind verfügbar, wenn weitere Anwendungen für die virtuellen Computer des Pools erforderlich sind:

* Benutzerdefiniertes Image basierend auf einem Marketplace-Standardimage:
  * Diese Option ermöglicht das Konfigurieren Ihrer VM mit den genauen Anwendungen und den jeweiligen Versionen, die Sie benötigen. Weitere Informationen finden Sie unter [Verwenden eines benutzerdefinierten Images zum Erstellen eines VM-Pools](https://docs.microsoft.com/azure/batch/batch-custom-images). Arnold und V-Ray wurden von Autodesk bzw. Chaos Group für einen Azure Batch-Lizenzierungsdienst angepasst. Stellen Sie sicher, dass Sie über die Versionen dieser Anwendungen mit der entsprechenden Unterstützung verfügen, da die Lizenzierung mit nutzungsbasierter Bezahlung andernfalls nicht funktioniert. Für die aktuellen Versionen von Maya oder 3ds Max wird kein Lizenzserver benötigt, wenn die Ausführung monitorlos erfolgt (im Batch-/Befehlszeilenmodus). Wenden Sie sich an den Azure-Support, wenn Sie nicht sicher sind, wie Sie bei dieser Option vorgehen sollen.
* [Anwendungspakete:](https://docs.microsoft.com/azure/batch/batch-application-packages)
  * Packen Sie die Anwendungsdateien in einer oder mehreren ZIP-Dateien, laden Sie sie über das Azure-Portal hoch, und geben Sie das Paket in der Poolkonfiguration an. Wenn Pool-VMs erstellt werden, werden die ZIP-Dateien heruntergeladen und die Dateien extrahiert.
* Ressourcendateien:
  * Anwendungsdateien werden in Azure Blob Storage hochgeladen, und Sie geben Dateiverweise in der [StartTask für den Pool](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) an. Wenn Pool-VMs erstellt werden, werden die Ressourcendateien auf die einzelnen virtuellen Computer heruntergeladen.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Nutzungsbasierte Lizenzierung für vorinstallierte Anwendungen

Die Anwendungen, die verwendet werden und für die eine Lizenzgebühr anfällt, müssen in der Poolkonfiguration angegeben werden.

* Geben Sie beim [Erstellen eines Pools](https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body) die `applicationLicenses`-Eigenschaft an.  Die folgenden Werte können im Array von Zeichenfolgen angegeben werden: „vray“, „arnold“, „3dsmax“ und „maya“.
* Wenn Sie eine oder mehrere Anwendungen angeben, werden die Kosten für diese Anwendungen mit den Kosten der virtuellen Computer addiert.  Anwendungspreise sind auf der [Seite mit den Preisen für Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering) aufgeführt.

> [!NOTE]
> Wenn Sie stattdessen eine Verbindung mit einem Lizenzserver für die Verwendung der Renderinganwendungen herstellen, geben Sie die `applicationLicenses`-Eigenschaft nicht an.

Sie können das Azure-Portal oder Batch Explorer zum Auswählen von Anwendungen und Anzeigen der Preise für die Anwendungen verwenden.

Wenn versucht wird, eine Anwendung zu verwenden, die Anwendung jedoch in der `applicationLicenses`-Eigenschaft der Poolkonfiguration nicht angegeben wurde oder keinen Lizenzserver erreicht hat, treten bei der Ausführung der Anwendung ein Lizenzierungsfehler und ein Exitcode ungleich null auf.

### <a name="environment-variables-for-pre-installed-applications"></a>Umgebungsvariablen für vorinstallierte Anwendungen

Um die Befehlszeile für Renderingaufgaben erstellen zu können, muss der Installationsspeicherort der ausführbaren Dateien der Renderinganwendung angegeben werden.  Für die VM-Images von Azure Marketplace wurden Systemumgebungsvariablen erstellt, die verwendet werden können, anstatt die eigentlichen Pfade angeben zu müssen.  Diese Umgebungsvariablen ergänzen die für jede Aufgabe erstellten [Batch-Standardumgebungsvariablen](https://docs.microsoft.com/azure/batch/batch-compute-node-environment-variables).

|Anwendung|Ausführbare Datei der Anwendung|Umgebungsvariable|
|---------|---------|---------|
|Autodesk 3ds Max 2018|3dsmaxcmdio.exe|3DSMAX_2018_EXEC|
|Autodesk 3ds Max 2019|3dsmaxcmdio.exe|3DSMAX_2019_EXEC|
|Autodesk Maya 2017|render.exe|MAYA_2017_EXEC|
|Autodesk Maya 2018|render.exe|MAYA_2018_EXEC|
|Chaos Group V-Ray Standalone|vray.exe|VRAY_3.60.4_EXEC|
Arnold 2017-Befehlszeile|kick.exe|ARNOLD_2017_EXEC|
|Arnold 2018-Befehlszeile|kick.exe|ARNOLD_2018_EXEC|
|Blender|blender.exe|BLENDER_2018_EXEC|

### <a name="azure-vm-families"></a>Azure-VM-Familien

Wie bei anderen Workloads variieren die Systemanforderungen für Renderinganwendungen, und die Leistungsanforderungen fallen je nach Aufträgen und Projekten unterschiedlich aus.  Abhängig von Ihren Anforderungen (z.B. niedrigste Kosten, bestes Preis-Leistungs-Verhältnis, beste Leistung usw.) stehen in Azure verschiedenste VM-Familien zur Verfügung.
Einige Renderinganwendungen, z.B. Arnold, sind CPU-basiert. In anderen wie V-Ray und Blender Cycles können CPUs und/oder GPUs verwendet werden.
Eine Beschreibung der verfügbaren VM-Familien und VM-Größen finden Sie unter [Arten und Größen von virtuellen Computern](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### <a name="low-priority-vms"></a>Virtuelle Computer mit niedriger Priorität

Wie bei anderen Workloads können virtuelle Computer mit niedriger Priorität auch in Batch-Pools für das Rendering verwendet werden.  Virtuelle Computer mit niedriger Priorität weisen die gleiche Leistung wie reguläre dedizierte virtuelle Computer auf, nutzen jedoch überschüssige Kapazitäten in Azure und sind zu einem günstigen Rabattpreis erhältlich.  Die Verwendung von VMs mit niedriger Priorität hat jedoch auch Nachteile: Möglicherweise stehen diese VMs für eine Zuweisung gerade nicht zur Verfügung, und sie können jederzeit vorzeitig entfernt werden, je nach der verfügbaren Kapazität. Aus diesem Grund eignen sich virtuelle Computer mit niedriger Priorität nicht für alle Renderingaufträge. Wenn das Rendering von Images beispielsweise viele Stunden dauert, ist es wahrscheinlich, dass das Rendering der Images unterbrochen und dann neu gestartet werden muss, da die vorzeitige Entfernung der virtuellen Computer nicht akzeptabel ist.

Weitere Informationen zu den Merkmalen von virtuellen Computern mit niedriger Priorität und den verschiedenen Möglichkeiten, diese mithilfe von Batch zu konfigurieren, finden Sie unter [Verwenden von VMs mit niedriger Priorität mit Batch](https://docs.microsoft.com/azure/batch/batch-low-pri-vms).

## <a name="jobs-and-tasks"></a>Aufträge und Aufgaben

Für Aufträge und Aufgaben ist keine Rendering-spezifische Unterstützung erforderlich.  Das wichtigste Konfigurationselement ist die Befehlszeile für Aufgaben, über die auf die gewünschte Anwendung verwiesen werden muss.
Wenn die VM-Images von Azure Marketplace verwendet werden, empfiehlt es sich, den Pfad und die ausführbare Datei der Anwendung mithilfe der Umgebungsvariablen anzugeben.

## <a name="next-steps"></a>Nächste Schritte

Beispiele für das Rendering mit Batch finden Sie in den folgenden beiden Tutorials:

* [Tutorial: Rendern einer Szene mit Azure Batch](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)
* [Tutorial: Rendern einer Blender-Szene mithilfe von Batch Explorer](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
