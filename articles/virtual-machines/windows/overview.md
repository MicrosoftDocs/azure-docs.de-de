---
title: Übersicht über virtuelle Windows-Computer in Azure
description: Übersicht über virtuelle Windows-Computer in Azure.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 26905b746efb0bfcc877e0fa8fad797672bb6447
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483214"
---
# <a name="windows-virtual-machines-in-azure"></a>Virtuelle Windows-Computer in Azure

Virtuelle Azure-Computer (Virtual Machines, VMs) sind eine von mehreren [bedarfsgesteuerten, skalierbaren Computerressourcen](/azure/architecture/guide/technology-choices/compute-decision-tree), die von Azure angeboten werden. Virtuelle Computer werden in der Regel verwendet, wenn Sie mehr Kontrolle über Ihre Computerumgebung benötigen als bei den anderen Optionen zur Verfügung steht. In diesem Artikel erfahren Sie, was Sie vor der Erstellung eines virtuellen Computers berücksichtigen sollten und wie Sie ihn erstellen und verwalten.

Ein virtueller Azure-Computer bietet Ihnen die Flexibilität der Virtualisierung, ohne Zeit und Geld für den Kauf und die Verwaltung der Hardware aufwenden zu müssen, mit der der virtuelle Computer betrieben wird. Der virtuelle Computer muss allerdings weiterhin verwaltet werden – beispielsweise durch Konfigurieren, Patchen und Verwalten der darauf ausgeführten Software.

Virtuelle Azure-Computer können auf vielfältige Weise genutzt werden. Beispiele:

* **Entwickeln und Testen:** Virtuelle Azure-Computer stellen eine schnelle und einfache Möglichkeit zum Erstellen eines Computers mit speziellen Konfigurationen dar, die zum Programmieren und Testen einer Anwendung erforderlich sind.
* **Anwendungen in der Cloud:** Da die Nutzung Ihrer Anwendung Schwankungen unterliegen kann, ist es unter Umständen wirtschaftlich sinnvoll, sie auf einem virtuellen Computer in Azure auszuführen. Sie bezahlen für zusätzliche virtuelle Computer, wenn Sie sie benötigen, und fahren sie andernfalls einfach herunter.
* **Erweitertes Datencenter:** Virtuelle Computer in einem virtuellen Azure-Netzwerk lassen sich problemlos mit dem Netzwerk Ihrer Organisation verbinden.

Die Anzahl virtueller Computer, die von Ihrer Anwendung genutzt werden, kann hoch- oder aufskaliert werden, um Ihren jeweiligen Anforderungen gerecht zu werden.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Was muss ich vor dem Erstellen eines virtuellen Computers berücksichtigen?
Beim Einrichten einer Anwendungsinfrastruktur in Azure müssen immer zahlreiche [Designaspekte](/azure/architecture/reference-architectures/n-tier/windows-vm) berücksichtigt werden. Machen Sie sich daher zunächst Gedanken über Folgendes:

* Die Namen Ihrer Anwendungsressourcen
* Den Speicherort der Ressourcen
* Die Größe des virtuellen Computers
* Die maximal erstellbare Anzahl virtueller Computer
* Das Betriebssystem für den virtuellen Computer
* Die Konfiguration des virtuellen Computers nach dem Start
* Die zugehörigen Ressourcen, die der virtuelle Computer benötigt

### <a name="locations"></a>Standorte
Alle in Azure erstellten Ressourcen werden auf [geografische Regionen](https://azure.microsoft.com/regions/) auf der ganzen Welt verteilt. Bei der Erstellung eines virtuellen Computers wird die Region in der Regel als **Standort** bezeichnet. Der Standort gibt für einen virtuellen Computer an, wo die virtuellen Festplatten gespeichert sind.

Die folgende Tabelle enthält einige Methoden, mit denen Sie eine Liste verfügbarer Standorte abrufen können:

| Methode | BESCHREIBUNG |
| --- | --- |
| Azure-Portal |Wählen Sie beim Erstellen eines virtuellen Computers einen Standort aus der Liste aus. |
| Azure PowerShell |Verwenden Sie den Befehl [Get-AzLocation](/powershell/module/az.resources/get-azlocation). |
| REST-API |Verwenden Sie den Vorgang [List locations](/rest/api/resources/subscriptions) (Standorte auflisten). |
| Azure CLI |Verwenden Sie den Vorgang [az account list-locations](/cli/azure/account?view=azure-cli-latest). |

### <a name="singapore-data-residency"></a>Data Residency in Singapur

Das Feature zum Aktivieren der Speicherung von Kundendaten in einer einzelnen Region ist in Azure derzeit nur in der Region „Asien, Südosten“ (Singapur) des geografischen Raums „Asien-Pazifik“ verfügbar. Bei allen anderen Regionen werden Kundendaten unter „Geografien“ gespeichert. Weitere Informationen finden Sie unter [Trust Center](https://azuredatacentermap.azurewebsites.net/).

## <a name="availability"></a>Verfügbarkeit
Für Azure wurde eine branchenweit führende Vereinbarung zum Servicelevel von 99,9 Prozent für Einzelinstanz-VMs angekündigt. Sie gilt unter der Voraussetzung, dass Sie den virtuellen Computer mit Storage Premium für alle Datenträger bereitstellen.  Damit Ihre Bereitstellung die Qualifikation für unsere VM-Standardvereinbarung zum Servicelevel von 99,95 Prozent erreicht, müssen Sie weiterhin mindestens zwei virtuelle Computer bereitstellen, die Ihre Workload innerhalb einer Verfügbarkeitsgruppe ausführen. Durch eine Verfügbarkeitsgruppe wird sichergestellt, dass Ihre virtuellen Computer auf mehrere Fehlerdomänen in den Azure-Rechenzentren verteilt und auf Hosts mit unterschiedlichen Wartungsfenstern bereitgestellt werden. Die vollständige [Azure-SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) erläutert die garantierte Verfügbarkeit von Azure insgesamt.


## <a name="vm-size"></a>Größe des virtuellen Computers
Die [Größe](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) des virtuellen Computers richtet sich nach der Workload, die Sie ausführen möchten. Von der gewählten Größe hängen Faktoren wie Rechenleistung, Arbeitsspeicher und Speicherplatz ab. Azure bietet eine Vielzahl von Größen zur Unterstützung vieler Anwendungstypen.

Bei Azure wird auf der Grundlage von Größe und Betriebssystem des virtuellen Computers ein [Stundenpreis](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) berechnet. Für angefangene Stunden werden lediglich die in Anspruch genommenen Minuten abgerechnet. Speicherplatz wird separat bewertet und in Rechnung gestellt.

## <a name="vm-limits"></a>Grenzwerte für virtuelle Computer
Für Ihr Abonnement gelten standardmäßig bestimmte [Kontingentgrenzen](../../azure-resource-manager/management/azure-subscription-service-limits.md), die die Bereitstellung einer hohen Anzahl virtueller Computer für Ihr Projekt beeinträchtigen können. Der derzeitige Grenzwert pro Abonnement liegt bei 20 VMs pro Region. Zur Erhöhung der Grenzwerte können Sie [ein Supportticket erstellen und eine Erhöhung beantragen](../../azure-portal/supportability/resource-manager-core-quotas-request.md).

### <a name="operating-system-disks-and-images"></a>Betriebssystem-Datenträger und Images
Betriebssystem (Operating System, OS) und Daten werden bei virtuellen Computern auf [virtuellen Festplatten (Virtual Hard Disks, VHDs)](../managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) gespeichert. VHDs werden auch für die Images verwendet, die Sie auswählen können, um ein Betriebssystem zu installieren. 

Azure bietet zahlreiche [Marketplace-Images](https://azuremarketplace.microsoft.com/marketplace/apps?filters=virtual-machine-images%3Bwindows&page=1) für verschiedene Versionen und Arten von Windows Server-Betriebssystemen. Marketplace-Images werden anhand von Herausgeber, Angebot, SKU und Version (üblicherweise die aktuelle Version) identifiziert. Es werden nur 64-Bit-Betriebssysteme unterstützt. Weitere Informationen zu den unterstützten Gastbetriebssystemen, Rollen und Features finden Sie unter [Microsoft-Serversoftwaresupport für virtuelle Microsoft Azure-Computer](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

Die folgende Tabelle enthält einige Methoden zur Ermittlung von Informationen zu einem Image:

| Methode | BESCHREIBUNG |
| --- | --- |
| Azure-Portal |Die Werte werden automatisch angegeben, wenn Sie ein zu verwendendes Image auswählen. |
| Azure PowerShell |[Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher) -Location *Speicherort*<BR>[Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer) -Location *Speicherort* -Publisher *Herausgebername*<BR>[Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) -Location *Speicherort* -Publisher *Herausgebername* -Offer *Angebotsname* |
| REST-APIs |[List image publishers](/rest/api/compute/platformimages/platformimages-list-publishers) (Imageherausgeber auflisten)<BR>[List image offers](/rest/api/compute/platformimages/platformimages-list-publisher-offers) (Imageangebote auflisten)<BR>[List image skus](/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) (Image-SKUs auflisten) |
| Azure CLI |[az vm image list-publishers](/cli/azure/vm/image?view=azure-cli-latest) --location *location*<BR>[az vm image list-offers](/cli/azure/vm/image?view=azure-cli-latest) --location *location* --publisher *publisherName*<BR>[az vm image list-skus](/cli/azure/vm?view=azure-cli-latest) --location *location* --publisher *publisherName* --offer *offerName*|

Sie können auch [ein eigenes Image hochladen und verwenden](upload-generalized-managed.md). In diesem Fall werden Herausgebername, Angebot und SKU nicht verwendet.

### <a name="extensions"></a>Erweiterungen
Durch [Erweiterungen](../extensions/features-windows.md?toc=/azure/virtual-machines/windows/toc.json) können Sie den Funktionsumfang Ihres virtuellen Computers mittels Konfiguration nach der Bereitstellung sowie mittels automatisierter Aufgaben erweitern.

Mit Erweiterungen können folgende allgemeine Aufgaben umgesetzt werden:

* **Ausführen benutzerdefinierter Skripts:** Die [benutzerdefinierte Skripterweiterung](../extensions/custom-script-windows.md?toc=/azure/virtual-machines/windows/toc.json) unterstützt Sie beim Konfigurieren von Workloads auf dem virtuellen Computer, indem beim Bereitstellen des virtuellen Computers Ihr Skript ausgeführt wird.
* **Bereitstellen und Verwalten von Konfigurationen:** Die [PowerShell-Erweiterung zum Konfigurieren des gewünschten Zustands](../extensions/dsc-overview.md?toc=/azure/virtual-machines/windows/toc.json) unterstützt Sie beim Einrichten der Konfiguration des gewünschten Zustands auf einem virtuellen Computer, um Konfigurationen und Umgebungen zu verwalten.
* **Sammeln von Diagnosedaten:** Die [Azure-Diagnoseerweiterung](../extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json) unterstützt Sie dabei, den virtuellen Computer für die Sammlung von Diagnosedaten zu konfigurieren, die zum Überwachen der Integrität Ihrer Anwendung verwendet werden können.

### <a name="related-resources"></a>Zugehörige Ressourcen
Die Ressourcen in der folgenden Tabelle werden vom virtuellen Computer verwendet und müssen beim Erstellen des virtuellen Computers vorhanden sein oder erstellt werden:

| Resource | Erforderlich | BESCHREIBUNG |
| --- | --- | --- |
| [Ressourcengruppe](../../azure-resource-manager/management/overview.md) |Ja |Der virtuelle Computer muss sich in einer Ressourcengruppe befinden. |
| [Speicherkonto](../../storage/common/storage-account-create.md) |Ja |Der virtuelle Computer benötigt das Speicherkonto zum Speichern der virtuellen Festplatten. |
| [Virtuelles Netzwerk](../../virtual-network/virtual-networks-overview.md) |Ja |Der virtuelle Computer muss einem virtuellen Netzwerk angehören. |
| [Öffentliche IP-Adresse](../../virtual-network/public-ip-addresses.md) |Nein |Für den Remotezugriff kann dem virtuellen Computer eine öffentliche IP-Adresse zugewiesen werden. |
| [Netzwerkschnittstelle](../../virtual-network/virtual-network-network-interface.md) |Ja |Der virtuelle Computer benötigt die Netzwerkschnittstelle für die Kommunikation im Netzwerk. |
| [Datenträger](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |Nein |Der virtuelle Computer kann Datenträger enthalten, um die Speicherkapazität zu erhöhen. |


## <a name="data-residency"></a>Datenresidenz

Das Feature zum Aktivieren der Speicherung von Kundendaten in einer einzelnen Region ist in Azure derzeit nur in der Region „Asien, Südosten“ (Singapur) des geografischen Raums „Asien-Pazifik“ und in der Region „Brasilien, Süden“ (São Paulo, Bundesstaat) des geografischen Raums „Brasilien“ verfügbar. Bei allen anderen Regionen werden Kundendaten unter „Geografien“ gespeichert. Weitere Informationen finden Sie unter [Trust Center](https://azuredatacentermap.azurewebsites.net/).


## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie Ihren ersten virtuellen Computer!

- [Portal](quick-create-portal.md)
- [PowerShell](quick-create-powershell.md)
- [Azure-Befehlszeilenschnittstelle](quick-create-cli.md)