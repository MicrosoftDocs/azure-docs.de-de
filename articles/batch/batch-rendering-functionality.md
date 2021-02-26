---
title: Renderingfunktionen
description: Azure Batch-Standardfunktionen werden verwendet, um Renderingworkloads und -Apps auszuführen. Batch enthält bestimmte Funktionen zur Unterstützung von Renderingworkloads.
author: mscurrell
ms.author: markscu
ms.date: 02/01/2021
ms.topic: how-to
ms.openlocfilehash: c748c51311a70cd3b4fc6d435e97a33d709467b9
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99429801"
---
# <a name="azure-batch-rendering-capabilities"></a>Azure Batch-Renderingfunktionen

Azure Batch-Standardfunktionen werden verwendet, um Renderingworkloads und -anwendungen auszuführen. Batch enthält zudem bestimmte Funktionen zur Unterstützung von Renderingworkloads.

Eine Übersicht über Batch-Konzepte, z.B. Pools, Aufträge und Aufgaben, finden Sie in [diesem Artikel](./batch-service-workflow-features.md).

## <a name="batch-pools-using-custom-vm-images-and-standard-application-licensing"></a>Batch-Pools mit benutzerdefinierten VM-Images und Standardanwendungslizenzierung

Wie bei anderen Workloads und Anwendungstypen kann ein benutzerdefiniertes VM-Image mit den erforderlichen Renderinganwendungen und Plug-Ins erstellt werden. Das benutzerdefinierte VM-Image wird in der [Shared Image Gallery](../virtual-machines/shared-image-galleries.md) gespeichert und kann zum [Erstellen von Batch-Pools](batch-sig-images.md) verwendet werden.

Die Befehlszeilen-Zeichenfolgen für eine Aufgabe müssen auf die Anwendungen und Pfade verweisen, die beim Erstellen des benutzerdefinierten VM-Images verwendet wurden.

Die meisten Renderinganwendungen benötigen Lizenzen, die von einem Lizenzserver abgerufen werden. Wenn ein lokaler Lizenzserver vorhanden ist, müssen sich der Pool und der Lizenzserver im selben [virtuellen Netzwerk](../virtual-network/virtual-networks-overview.md) befinden. Es ist auch möglich, einen Lizenzserver auf einer Azure-VM auszuführen. Dann müssen sich der Batch-Pool und die Lizenzserver-VM im selben virtuellen Netzwerk befinden.

## <a name="batch-pools-using-rendering-vm-images"></a>Batch-Pools mit VM-Images für das Rendering

### <a name="rendering-application-installation"></a>Installation von Renderinganwendungen

In der Poolkonfiguration kann ein Rendering-VM-Image von Azure Marketplace angegeben werden, wenn nur die vorinstallierten Anwendungen verwendet werden sollen.

Zur Verfügung stehen ein Windows-Image und ein CentOS-Image.  Im [Azure Marketplace](https://azuremarketplace.microsoft.com) finden Sie die VM-Images, indem Sie nach „Batch-Rendering“ suchen.

Ein Beispiel für eine Poolkonfiguration finden Sie im [Tutorial zum Rendern von Azure CLI](./tutorial-rendering-cli.md).  Das Azure-Portal und Batch Explorer umfassen GUI-Tools zum Auswählen eines Rendering-VM-Images beim Erstellen eines Pools.  Wenn Sie eine Batch-API verwenden, geben Sie beim Erstellen eines Pools die folgenden Eigenschaftswerte für [ImageReference](/rest/api/batchservice/pool/add#imagereference) an:

| Herausgeber | Angebot | Sku | Version |
|---------|---------|---------|--------|
| Batch | rendering-centos73 | Rendering | latest |
| Batch | rendering-windows2016 | Rendering | latest |

Andere Optionen sind verfügbar, wenn weitere Anwendungen für die virtuellen Computer des Pools erforderlich sind:

* Ein benutzerdefiniertes Image aus Shared Image Gallery:
  * Diese Option ermöglicht das Konfigurieren Ihrer VM mit den genauen Anwendungen und den jeweiligen Versionen, die Sie benötigen. Weitere Informationen finden Sie unter [Erstellen eines Pools mit Shared Image Gallery](batch-sig-images.md). Arnold und V-Ray wurden von Autodesk bzw. Chaos Group für einen Azure Batch-Lizenzierungsdienst angepasst. Stellen Sie sicher, dass Sie über die Versionen dieser Anwendungen mit der entsprechenden Unterstützung verfügen, da die Lizenzierung mit nutzungsbasierter Bezahlung andernfalls nicht funktioniert. Für die aktuellen Versionen von Maya oder 3ds Max wird kein Lizenzserver benötigt, wenn die Ausführung monitorlos erfolgt (im Batch-/Befehlszeilenmodus). Wenden Sie sich an den Azure-Support, wenn Sie nicht sicher sind, wie Sie bei dieser Option vorgehen sollen.
* [Anwendungspakete:](./batch-application-packages.md)
  * Packen Sie die Anwendungsdateien in einer oder mehreren ZIP-Dateien, laden Sie sie über das Azure-Portal hoch, und geben Sie das Paket in der Poolkonfiguration an. Wenn Pool-VMs erstellt werden, werden die ZIP-Dateien heruntergeladen und die Dateien extrahiert.
* Ressourcendateien:
  * Anwendungsdateien werden in Azure Blob Storage hochgeladen, und Sie geben Dateiverweise in der [StartTask für den Pool](/rest/api/batchservice/pool/add#starttask) an. Wenn Pool-VMs erstellt werden, werden die Ressourcendateien auf die einzelnen virtuellen Computer heruntergeladen.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Nutzungsbasierte Lizenzierung für vorinstallierte Anwendungen

Die Anwendungen, die verwendet werden und für die eine Lizenzgebühr anfällt, müssen in der Poolkonfiguration angegeben werden.

* Geben Sie beim [Erstellen eines Pools](/rest/api/batchservice/pool/add#request-body) die `applicationLicenses`-Eigenschaft an.  Die folgenden Werte können im Array von Zeichenfolgen angegeben werden: „vray“, „arnold“, „3dsmax“ und „maya“.
* Wenn Sie eine oder mehrere Anwendungen angeben, werden die Kosten für diese Anwendungen mit den Kosten der virtuellen Computer addiert.  Anwendungspreise sind auf der [Seite mit den Preisen für Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering) aufgeführt.

> [!NOTE]
> Wenn Sie stattdessen eine Verbindung mit einem Lizenzserver für die Verwendung der Renderinganwendungen herstellen, geben Sie die `applicationLicenses`-Eigenschaft nicht an.

Sie können das Azure-Portal oder Batch Explorer zum Auswählen von Anwendungen und Anzeigen der Preise für die Anwendungen verwenden.

Wenn versucht wird, eine Anwendung zu verwenden, die Anwendung jedoch in der `applicationLicenses`-Eigenschaft der Poolkonfiguration nicht angegeben wurde oder keinen Lizenzserver erreicht hat, treten bei der Ausführung der Anwendung ein Lizenzierungsfehler und ein Exitcode ungleich null auf.

### <a name="environment-variables-for-pre-installed-applications"></a>Umgebungsvariablen für vorinstallierte Anwendungen

Um die Befehlszeile für Renderingaufgaben erstellen zu können, muss der Installationsspeicherort der ausführbaren Dateien der Renderinganwendung angegeben werden.  Für die VM-Images von Azure Marketplace wurden Systemumgebungsvariablen erstellt, die verwendet werden können, anstatt die eigentlichen Pfade angeben zu müssen.  Diese Umgebungsvariablen ergänzen die für jede Aufgabe erstellten [Batch-Standardumgebungsvariablen](./batch-compute-node-environment-variables.md).

|Application|Ausführbare Datei der Anwendung|Umgebungsvariable|
|---------|---------|---------|
|Autodesk 3ds Max 2021|3dsmaxcmdio.exe|3DSMAX_2021_EXEC|
|Autodesk Maya 2020|render.exe|MAYA_2020_EXEC|
|Chaos Group V-Ray Standalone|vray.exe|VRAY_4.10.03_EXEC|
|Arnold 2020-Befehlszeile|kick.exe|ARNOLD_2020_EXEC|
|Blender|blender.exe|BLENDER_2018_EXEC|

## <a name="azure-vm-families"></a>Azure-VM-Familien

Wie bei anderen Workloads variieren die Systemanforderungen für Renderinganwendungen, und die Leistungsanforderungen fallen je nach Aufträgen und Projekten unterschiedlich aus.  Abhängig von Ihren Anforderungen (z.B. niedrigste Kosten, bestes Preis-Leistungs-Verhältnis, beste Leistung usw.) stehen in Azure verschiedenste VM-Familien zur Verfügung.
Einige Renderinganwendungen, z.B. Arnold, sind CPU-basiert. In anderen wie V-Ray und Blender Cycles können CPUs und/oder GPUs verwendet werden.
Eine Beschreibung der verfügbaren VM-Familien und VM-Größen finden Sie unter [Arten und Größen von virtuellen Computern](../virtual-machines/sizes.md).

## <a name="low-priority-vms"></a>Virtuelle Computer mit niedriger Priorität

Wie bei anderen Workloads können virtuelle Computer mit niedriger Priorität auch in Batch-Pools für das Rendering verwendet werden.  Virtuelle Computer mit niedriger Priorität weisen die gleiche Leistung wie reguläre dedizierte virtuelle Computer auf, nutzen jedoch überschüssige Kapazitäten in Azure und sind zu einem günstigen Rabattpreis erhältlich.  Die Verwendung von VMs mit niedriger Priorität hat jedoch auch Nachteile: Möglicherweise stehen diese VMs für eine Zuweisung gerade nicht zur Verfügung, und sie können jederzeit vorzeitig entfernt werden, je nach der verfügbaren Kapazität. Aus diesem Grund eignen sich virtuelle Computer mit niedriger Priorität nicht für alle Renderingaufträge. Wenn das Rendering von Images beispielsweise viele Stunden dauert, ist es wahrscheinlich, dass das Rendering der Images unterbrochen und dann neu gestartet werden muss, da die vorzeitige Entfernung der virtuellen Computer nicht akzeptabel ist.

Weitere Informationen zu den Merkmalen von virtuellen Computern mit niedriger Priorität und den verschiedenen Möglichkeiten, diese mithilfe von Batch zu konfigurieren, finden Sie unter [Verwenden von VMs mit niedriger Priorität mit Batch](./batch-low-pri-vms.md).

## <a name="jobs-and-tasks"></a>Aufträge und Aufgaben

Für Aufträge und Aufgaben ist keine Rendering-spezifische Unterstützung erforderlich.  Das wichtigste Konfigurationselement ist die Befehlszeile für Aufgaben, über die auf die gewünschte Anwendung verwiesen werden muss.
Wenn die VM-Images von Azure Marketplace verwendet werden, empfiehlt es sich, den Pfad und die ausführbare Datei der Anwendung mithilfe der Umgebungsvariablen anzugeben.

## <a name="next-steps"></a>Nächste Schritte

Beispiele für das Rendering mit Batch finden Sie in den folgenden beiden Tutorials:

* [Tutorial: Rendern einer Szene mit Azure Batch](./tutorial-rendering-cli.md)
* [Rendern einer Blender-Szene mithilfe von Batch Explorer](./tutorial-rendering-batchexplorer-blender.md)