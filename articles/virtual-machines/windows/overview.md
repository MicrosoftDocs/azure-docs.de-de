---
title: Übersicht über Virtuelle Windows-Computer – Azure | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie virtuelle Windows-Computer in Azure erstellen und verwalten.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: fbae9c8e-2341-4ed0-bb20-fd4debb2f9ca
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 604a47ef73d50a2d127d1569b0b6a240a7a27d73
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65506841"
---
# <a name="overview-of-windows-virtual-machines-in-azure"></a>Übersicht über virtuelle Windows-Computer in Azure

Virtuelle Azure-Computer (Virtual Machines, VMs) sind eine von mehreren [bedarfsgesteuerten, skalierbaren Computerressourcen](/azure/architecture/guide/technology-choices/compute-decision-tree), die von Azure angeboten werden. Virtuelle Computer werden in der Regel verwendet, wenn Sie mehr Kontrolle über Ihre Computerumgebung benötigen als bei den anderen Optionen zur Verfügung steht. In diesem Artikel erfahren Sie, was Sie vor der Erstellung eines virtuellen Computers berücksichtigen sollten und wie Sie ihn erstellen und verwalten.

Ein virtueller Azure-Computer bietet Ihnen die Flexibilität der Virtualisierung, ohne Zeit und Geld für den Kauf und die Verwaltung der Hardware aufwenden zu müssen, mit der der virtuelle Computer betrieben wird. Der virtuelle Computer muss allerdings weiterhin verwaltet werden – beispielsweise durch Konfigurieren, Patchen und Verwalten der darauf ausgeführten Software.

Virtuelle Azure-Computer können auf vielfältige Weise genutzt werden. Hier einige Beispiele:

* **Entwickeln und Testen:** Virtuelle Azure-Computer stellen eine schnelle und einfache Möglichkeit zum Erstellen eines Computers mit speziellen Konfigurationen dar, die zum Programmieren und Testen einer Anwendung erforderlich sind.
* **Anwendungen in der Cloud:** Da die Nutzung Ihrer Anwendung Schwankungen unterliegen kann, ist es unter Umständen wirtschaftlich sinnvoll, sie auf einem virtuellen Computer in Azure auszuführen. Sie bezahlen für zusätzliche virtuelle Computer, wenn Sie sie benötigen, und fahren sie andernfalls einfach herunter.
* **Erweitertes Datencenter:** Virtuelle Computer in einem virtuellen Azure-Netzwerk lassen sich problemlos mit dem Netzwerk Ihrer Organisation verbinden.

Die Anzahl virtueller Computer, die von Ihrer Anwendung genutzt werden, kann zentral oder horizontal hochskaliert werden, um Ihren jeweiligen Anforderungen gerecht zu werden.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Was muss ich vor dem Erstellen eines virtuellen Computers berücksichtigen?
Beim Einrichten einer Anwendungsinfrastruktur in Azure müssen immer zahlreiche [Designaspekte](/azure/architecture/reference-architectures/virtual-machines-windows?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) berücksichtigt werden. Machen Sie sich daher zunächst Gedanken über Folgendes:

* Die Namen Ihrer Anwendungsressourcen
* Den Speicherort der Ressourcen
* Die Größe des virtuellen Computers
* Die maximal erstellbare Anzahl virtueller Computer
* Das Betriebssystem für den virtuellen Computer
* Die Konfiguration des virtuellen Computers nach dem Start
* Die zugehörigen Ressourcen, die der virtuelle Computer benötigt

### <a name="naming"></a>Benennung
Einem virtuellen Computer wird ein [Name](/azure/architecture/best-practices/naming-conventions) zugewiesen, und im Zusammenhang mit dem Betriebssystem wird ein Computername konfiguriert. Der Name eines virtuellen Computers kann bis zu 15 Zeichen lang sein.

Wenn Sie den Betriebssystem-Datenträger mithilfe von Azure erstellen, sind der Computername und der Name des virtuellen Computers identisch. Wenn Sie [ein eigenes Image hochladen und verwenden](upload-generalized-managed.md), das ein vorab konfiguriertes Betriebssystem enthält, und auf dessen Grundlage einen virtuellen Computer erstellen, können die Namen unterschiedlich sein. Beim Hochladen einer eigenen Imagedatei empfiehlt es sich, für den Computernamen im Betriebssystem und für den Namen des virtuellen Computers den gleichen Namen zu verwenden.

### <a name="locations"></a>Standorte
Alle in Azure erstellten Ressourcen werden auf [geografische Regionen](https://azure.microsoft.com/regions/) auf der ganzen Welt verteilt. Bei der Erstellung eines virtuellen Computers wird die Region in der Regel als **Standort** bezeichnet. Der Standort gibt für einen virtuellen Computer an, wo die virtuellen Festplatten gespeichert sind.

Die folgende Tabelle enthält einige Methoden, mit denen Sie eine Liste verfügbarer Standorte abrufen können:

| Methode | BESCHREIBUNG |
| --- | --- |
| Azure-Portal |Wählen Sie beim Erstellen eines virtuellen Computers einen Standort aus der Liste aus. |
| Azure PowerShell |Verwenden Sie den Befehl [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation). |
| REST-API |Verwenden Sie den Vorgang [List locations](https://docs.microsoft.com/rest/api/resources/subscriptions) (Standorte auflisten). |
| Azure-Befehlszeilenschnittstelle |Verwenden Sie den Vorgang [az account list-locations](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest). |

### <a name="vm-size"></a>Größe des virtuellen Computers
Die [Größe](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) des virtuellen Computers richtet sich nach der Workload, die Sie ausführen möchten. Von der gewählten Größe hängen Faktoren wie Rechenleistung, Arbeitsspeicher und Speicherplatz ab. Azure bietet eine Vielzahl von Größen zur Unterstützung vieler Anwendungstypen.

Bei Azure wird auf der Grundlage von Größe und Betriebssystem des virtuellen Computers ein [Stundenpreis](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) berechnet. Für angefangene Stunden werden lediglich die in Anspruch genommenen Minuten abgerechnet. Speicherplatz wird separat bewertet und in Rechnung gestellt.

### <a name="vm-limits"></a>Grenzwerte für virtuelle Computer
Für Ihr Abonnement gelten standardmäßig bestimmte [Kontingentgrenzen](../../azure-subscription-service-limits.md), die die Bereitstellung einer hohen Anzahl virtueller Computer für Ihr Projekt beeinträchtigen können. Der derzeitige Grenzwert pro Abonnement liegt bei 20 VMs pro Region. Zur Erhöhung der Grenzwerte können Sie [ein Supportticket erstellen und eine Erhöhung beantragen](../../azure-supportability/resource-manager-core-quotas-request.md).

### <a name="operating-system-disks-and-images"></a>Betriebssystem-Datenträger und Images
Betriebssystem (Operating System, OS) und Daten werden bei virtuellen Computern auf [virtuellen Festplatten (Virtual Hard Disks, VHDs)](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) gespeichert. VHDs werden auch für die Images verwendet, die Sie auswählen können, um ein Betriebssystem zu installieren. 

Azure bietet zahlreiche [Marketplace-Images](https://azure.microsoft.com/marketplace/virtual-machines/) für verschiedene Versionen und Arten von Windows Server-Betriebssystemen. Marketplace-Images werden anhand von Herausgeber, Angebot, SKU und Version (üblicherweise die aktuelle Version) identifiziert. Es werden nur 64-Bit-Betriebssysteme unterstützt. Weitere Informationen zu den unterstützten Gastbetriebssystemen, Rollen und Features finden Sie unter [Microsoft-Serversoftwaresupport für virtuelle Microsoft Azure-Computer](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

Die folgende Tabelle enthält einige Methoden zur Ermittlung von Informationen zu einem Image:

| Methode | BESCHREIBUNG |
| --- | --- |
| Azure-Portal |Die Werte werden automatisch angegeben, wenn Sie ein zu verwendendes Image auswählen. |
| Azure PowerShell |[Get-AzVMImagePublisher](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagepublisher) -Location *Speicherort*<BR>[Get-AzVMImageOffer](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimageoffer) -Location *Speicherort* -Publisher *Herausgebername*<BR>[Get-AzVMImageSku](https://docs.microsoft.com/powershell/module/az.compute/get-azvmimagesku) -Location *Speicherort* -Publisher *Herausgebername* -Offer *Angebotsname* |
| REST-APIs |[List image publishers](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publishers) (Imageherausgeber auflisten)<BR>[List image offers](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offers) (Imageangebote auflisten)<BR>[List image skus](https://docs.microsoft.com/rest/api/compute/platformimages/platformimages-list-publisher-offer-skus) (Image-SKUs auflisten) |
| Azure-Befehlszeilenschnittstelle |[az vm image list-publishers](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest) --location *location*<BR>[az vm image list-offers](https://docs.microsoft.com/cli/azure/vm/image?view=azure-cli-latest) --location *location* --publisher *publisherName*<BR>[az vm image list-skus](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest) --location *location* --publisher *publisherName* --offer *offerName*|

Sie können auch [ein eigenes Image hochladen und verwenden](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account). In diesem Fall werden Herausgebername, Angebot und SKU nicht verwendet.

### <a name="extensions"></a>Erweiterungen
Durch [Erweiterungen](extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) können Sie den Funktionsumfang Ihres virtuellen Computers mittels Konfiguration nach der Bereitstellung sowie mittels automatisierter Aufgaben erweitern.

Mit Erweiterungen können folgende allgemeine Aufgaben umgesetzt werden:

* **Ausführen benutzerdefinierter Skripts:** Die [benutzerdefinierte Skripterweiterung](extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) unterstützt Sie beim Konfigurieren von Workloads auf dem virtuellen Computer, indem beim Bereitstellen des virtuellen Computers Ihr Skript ausgeführt wird.
* **Bereitstellen und Verwalten von Konfigurationen:** Die [PowerShell-Erweiterung zum Konfigurieren des gewünschten Zustands](extensions-dsc-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) unterstützt Sie beim Einrichten der Konfiguration des gewünschten Zustands auf einem virtuellen Computer, um Konfigurationen und Umgebungen zu verwalten.
* **Sammeln von Diagnosedaten:** Die [Azure-Diagnoseerweiterung](extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) unterstützt Sie dabei, den virtuellen Computer für die Sammlung von Diagnosedaten zu konfigurieren, die zum Überwachen der Integrität Ihrer Anwendung verwendet werden können.

### <a name="related-resources"></a>Zugehörige Ressourcen
Die Ressourcen in der folgenden Tabelle werden vom virtuellen Computer verwendet und müssen beim Erstellen des virtuellen Computers vorhanden sein oder erstellt werden:

| Resource | Erforderlich | BESCHREIBUNG |
| --- | --- | --- |
| [Ressourcengruppe](../../azure-resource-manager/resource-group-overview.md) |Ja |Der virtuelle Computer muss sich in einer Ressourcengruppe befinden. |
| [Speicherkonto](../../storage/common/storage-create-storage-account.md) |Ja |Der virtuelle Computer benötigt das Speicherkonto zum Speichern der virtuellen Festplatten. |
| [Virtuelles Netzwerk](../../virtual-network/virtual-networks-overview.md) |Ja |Der virtuelle Computer muss einem virtuellen Netzwerk angehören. |
| [Öffentliche IP-Adresse](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) |Nein |Für den Remotezugriff kann dem virtuellen Computer eine öffentliche IP-Adresse zugewiesen werden. |
| [Netzwerkschnittstelle](../../virtual-network/virtual-network-network-interface.md) |Ja |Der virtuelle Computer benötigt die Netzwerkschnittstelle für die Kommunikation im Netzwerk. |
| [Datenträger](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |Nein |Der virtuelle Computer kann Datenträger enthalten, um die Speicherkapazität zu erhöhen. |

## <a name="how-do-i-create-my-first-vm"></a>Wie erstelle ich meinen ersten virtuellen Computer?
Ein virtueller Computer kann auf unterschiedliche Weise erstellt werden. Die Vorgehensweise hängt von Ihrer Umgebung ab. 

Die folgende Tabelle enthält hilfreiche Informationen zur Erstellung Ihres ersten virtuellen Computers:

| Methode | Artikel |
| --- | --- |
| Azure-Portal |[Create a virtual machine running Windows using the portal (Erstellen eines virtuellen Windows-Computers mithilfe des Portals)](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Vorlagen |[Erstellen Sie einen virtuellen Windows-Computer mit einer Resource Manager-Vorlage](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Azure PowerShell |[Create a Windows VM using PowerShell (Erstellen eines virtuellen Windows-Computers mithilfe von PowerShell)](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| Client-SDKs |[Bereitstellen von Azure-Ressourcen mit C#](csharp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| REST-APIs |[Erstellen oder Aktualisieren eines virtuellen Computers](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-create-or-update) |
| Azure-Befehlszeilenschnittstelle |[Erstellen eines virtuellen Computers mit der Azure CLI](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-cli-sample-create-vm) |

Man hofft zwar immer, dass es nicht dazu kommt, gelegentlich tritt dann aber doch der eine oder andere Fehler auf. In diesem Fall finden Sie unter [Behandeln von Problemen beim Erstellen eines neuen virtuellen Windows-Computers in Azure (Resource Manager-Bereitstellungsmodell)](../troubleshooting/troubleshoot-deployment-new-vm-windows.md) hilfreiche Informationen.

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Wie verwalte ich den erstellten virtuellen Computer?
Virtuelle Computer können über ein browserbasiertes Portal, über Befehlszeilentools mit Skriptunterstützung oder direkt über APIs verwaltet werden. Gängige Verwaltungsaufgaben sind das Abrufen von Informationen zu einem virtuellen Computer, das Anmelden bei einem virtuellen Computer, das Verwalten der Verfügbarkeit und die Durchführung von Sicherungen.

### <a name="get-information-about-a-vm"></a>Abrufen von Informationen zu einem virtuellen Computer
Die folgende Tabelle enthält einige der Methoden, mit denen Sie Informationen zu einem virtuellen Computer abrufen können:

| Methode | BESCHREIBUNG |
| --- | --- |
| Azure-Portal |Klicken Sie im Hub-Menü auf **Virtual Machines**, und wählen Sie dann in der Liste den gewünschten virtuellen Computer aus. Auf dem Blatt für den virtuellen Computer finden Sie Übersichtsinformationen, Einstellungswerte und Überwachungsmetriken. |
| Azure PowerShell |Informationen zum Verwenden von PowerShell für die Verwaltung von virtuellen Computern finden Sie unter [Erstellen und Verwalten von virtuellen Windows-Computern mit dem Azure PowerShell-Modul](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| REST-API |Verwenden Sie den Vorgang [Get VM information](https://docs.microsoft.com/rest/api/compute/virtualmachines/virtualmachines-get) (VM-Informationen abrufen), um Informationen zu einem virtuellen Computer abzurufen. |
| Client-SDKs |Informationen zum Verwalten von virtuellen Computern mithilfe von C# finden Sie unter [Verwalten von virtuellen Azure-Computern mit Azure Resource Manager und C#](csharp-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Azure-Befehlszeilenschnittstelle |Informationen zur Verwendung der Azure-Befehlszeilenschnittstelle zum Verwalten von VMs finden Sie in der [Azure CLI-Referenz](https://docs.microsoft.com/cli/azure/vm). |

### <a name="log-on-to-the-vm"></a>Anmelden beim virtuellen Computer
Verwenden Sie zum [Starten einer Remotedesktopsitzung](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) die Schaltfläche „Verbinden“ im Azure-Portal. Bei Remoteverbindungen können gelegentlich Probleme auftreten. In diesem Fall finden Sie unter [Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](../troubleshooting/troubleshoot-rdp-connection.md) hilfreiche Informationen.

### <a name="manage-availability"></a>Verwalten der Verfügbarkeit
Informieren Sie sich darüber, wie Sie die [Hochverfügbarkeit für Ihre Anwendung sicherstellen](manage-availability.md). Diese Konfiguration umfasst das Erstellen mehrerer virtueller Computer, um zu gewährleisten, dass mindestens einer davon aktiv ist.

Damit Ihre Bereitstellung den Verfügbarkeitswert von 99,95 Prozent unserer VM-Vereinbarung zum Servicelevel erreicht, müssen Sie mindestens zwei virtuelle Computer bereitstellen, die Ihre Workload innerhalb einer [Verfügbarkeitsgruppe](tutorial-availability-sets.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ausführen. So wird sichergestellt, dass Ihre virtuellen Computer auf mehrere Fehlerdomänen verteilt und auf Hosts mit unterschiedlichen Wartungsfenstern bereitgestellt werden. Die vollständige [Azure-SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) erläutert die garantierte Verfügbarkeit von Azure insgesamt.

### <a name="back-up-the-vm"></a>Sichern des virtuellen Computers
Ein [Recovery Services-Tresor](../../backup/backup-introduction-to-azure-backup.md) dient zum Schutz von Daten und Assets in Azure Backup und Azure Site Recovery. Mit einem Recovery Services-Tresor können Sie [Sicherungen für mit Resource Manager bereitgestellte virtuelle Computer mithilfe von PowerShell bereitstellen und verwalten](../../backup/backup-azure-vms-automation.md). 

## <a name="next-steps"></a>Nächste Schritte
* Wenn Sie virtuelle Linux-Computer verwenden möchten, sehen Sie sich [Azure und Linux](../linux/overview.md) an.
* Weitere Informationen zu den Richtlinien im Zusammenhang mit der Einrichtung Ihrer Infrastruktur finden Sie unter [Exemplarische Vorgehensweise für eine Azure-Beispielinfrastruktur](infrastructure-example.md).
