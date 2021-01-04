---
title: Übersicht über virtuelle Linux-Computer in Azure
description: Übersicht über virtuelle Linux-Computer in Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 54982189a5da584c7daf66855ffb655e403a455a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500511"
---
# <a name="linux-virtual-machines-in-azure"></a>Virtuelle Linux-Computer in Azure

Virtuelle Azure-Computer (Virtual Machines, VMs) sind eine von mehreren [bedarfsgesteuerten, skalierbaren Computerressourcen](/azure/architecture/guide/technology-choices/compute-decision-tree), die von Azure angeboten werden. Virtuelle Computer werden in der Regel verwendet, wenn Sie mehr Kontrolle über Ihre Computerumgebung benötigen als bei den anderen Optionen zur Verfügung steht. In diesem Artikel erfahren Sie, was Sie vor der Erstellung eines virtuellen Computers berücksichtigen sollten und wie Sie ihn erstellen und verwalten.

Ein virtueller Azure-Computer bietet Ihnen die Flexibilität der Virtualisierung, ohne Zeit und Geld für den Kauf und die Verwaltung der Hardware aufwenden zu müssen, mit der der virtuelle Computer betrieben wird. Der virtuelle Computer muss allerdings weiterhin verwaltet werden – beispielsweise durch Konfigurieren, Patchen und Verwalten der darauf ausgeführten Software.

Virtuelle Azure-Computer können auf vielfältige Weise genutzt werden. Beispiele:

* **Entwickeln und Testen:** Virtuelle Azure-Computer stellen eine schnelle und einfache Möglichkeit zum Erstellen eines Computers mit speziellen Konfigurationen dar, die zum Programmieren und Testen einer Anwendung erforderlich sind.
* **Anwendungen in der Cloud:** Da die Nutzung Ihrer Anwendung Schwankungen unterliegen kann, ist es unter Umständen wirtschaftlich sinnvoll, sie auf einem virtuellen Computer in Azure auszuführen. Sie bezahlen für zusätzliche virtuelle Computer, wenn Sie sie benötigen, und fahren sie andernfalls einfach herunter.
* **Erweitertes Datencenter:** Virtuelle Computer in einem virtuellen Azure-Netzwerk lassen sich problemlos mit dem Netzwerk Ihrer Organisation verbinden.

Die Anzahl virtueller Computer, die von Ihrer Anwendung genutzt werden, kann hoch- oder aufskaliert werden, um Ihren jeweiligen Anforderungen gerecht zu werden.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Was muss ich vor dem Erstellen eines virtuellen Computers berücksichtigen?
Beim Einrichten einer Anwendungsinfrastruktur in Azure müssen immer zahlreiche [Designaspekte](/azure/architecture/reference-architectures/n-tier/linux-vm) berücksichtigt werden. Machen Sie sich daher zunächst Gedanken über Folgendes:

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
Die [Größe](../sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) des virtuellen Computers richtet sich nach der Workload, die Sie ausführen möchten. Von der gewählten Größe hängen Faktoren wie Rechenleistung, Arbeitsspeicher und Speicherplatz ab. Azure bietet eine Vielzahl von Größen zur Unterstützung vieler Anwendungstypen.

Bei Azure wird auf der Grundlage von Größe und Betriebssystem des virtuellen Computers ein [Stundenpreis](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) berechnet. Für angefangene Stunden werden lediglich die in Anspruch genommenen Minuten abgerechnet. Speicherplatz wird separat bewertet und in Rechnung gestellt.

## <a name="vm-limits"></a>Grenzwerte für virtuelle Computer
Für Ihr Abonnement gelten standardmäßig bestimmte [Kontingentgrenzen](../../azure-resource-manager/management/azure-subscription-service-limits.md), die die Bereitstellung einer hohen Anzahl virtueller Computer für Ihr Projekt beeinträchtigen können. Der derzeitige Grenzwert pro Abonnement liegt bei 20 VMs pro Region. Zur Erhöhung der Grenzwerte können Sie [ein Supportticket erstellen und eine Erhöhung beantragen](../../azure-portal/supportability/resource-manager-core-quotas-request.md).

## <a name="managed-disks"></a>Managed Disks

Der Managed Disks-Dienst erledigt die Erstellungs- und Verwaltungsaufgaben in Ihrem Azure Storage-Konto im Hintergrund und sorgt dafür, dass Sie sich keine Gedanken mehr über die Skalierbarkeitsgrenzen des Speicherkontos machen müssen. Sie geben die Datenträgergröße und die Leistungsstufe (Standard/Premium) an, und Azure kümmert sich um die Erstellung und Verwaltung der Datenträger. So müssen Sie sich beim Hinzufügen von Datenträgern oder beim Skalieren virtueller Computer keine Gedanken mehr über den verwendeten Speicher machen. Wenn Sie neue virtuelle Computer erstellen, [verwenden Sie die Azure-Befehlszeilenschnittstelle](quick-create-cli.md) oder das Azure-Portal, um virtuelle Computer mit verwalteten Datenträgern für Betriebssystem und Daten zu erstellen. Wenn Sie bereits über virtuelle Computer mit nicht verwalteten Datenträgern verfügen, können Sie [Ihre virtuellen Computer für die Verwendung mit verwalteten Datenträgern konvertieren](convert-unmanaged-to-managed-disks.md).

Darüber hinaus können Sie Ihre benutzerdefinierten Images in einem einzelnen Speicherkonto pro Azure-Region verwalten und mit diesen Hunderte von virtuellen Computern im gleichen Abonnement erstellen. Weitere Informationen zu Managed Disk finden Sie in der [Übersicht über Managed Disks](../managed-disks-overview.md).

## <a name="distributions"></a>Verteilungen 
Microsoft Azure unterstützt die Ausführung einer Reihe von beliebten Linux-Distributionen, die von verschiedenen Partnerunternehmen bereitgestellt und gepflegt werden.  Verfügbare Distributionen finden Sie im Azure Marketplace. Microsoft arbeitet aktiv mit verschiedenen Linux-Communitys zusammen, um der Liste der [von Azure unterstützten Linux-Distributionen](endorsed-distros.md) weitere Varianten hinzuzufügen.

Falls Ihre bevorzugte Linux-Distribution derzeit nicht im Katalog enthalten ist, können Sie den Ansatz „Bring your own Linux VM“ verfolgen, indem Sie eine [Linux-VHD erstellen und in Azure hochladen](create-upload-generic.md).

Microsoft arbeitet eng mit Partnern zusammen, um sicherzustellen, dass die verfügbaren Images für die Azure-Laufzeit aktualisiert und optimiert sind.  Weitere Informationen zu Azure-Partnerangeboten finden Sie unter den folgenden Links:

* [Linux auf von Azure unterstützten Distributionen](endorsed-distros.md)
* SUSE: [Azure Marketplace – SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=suse)
* Red Hat: [Azure Marketplace – Red Hat Enterprise Linux](https://azuremarketplace.microsoft.com/marketplace/apps?search=Red%20Hat%20Enterprise%20Linux)
* Canonical: [Azure Marketplace – Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer)
* Debian: [Azure Marketplace – Debian](https://azuremarketplace.microsoft.com/marketplace/apps?search=Debian&page=1)
* FreeBSD: [Azure Marketplace – FreeBSD](https://azuremarketplace.microsoft.com/marketplace/apps?search=freebsd&page=1)
* Flatcar: [Azure Marketplace – Flatcar Container Linux](https://azuremarketplace.microsoft.com/marketplace/apps?search=Flatcar&page=1)
* RancherOS: [Azure Marketplace – RancherOS](https://azuremarketplace.microsoft.com/marketplace/apps/rancher.rancheros)
* Bitnami: [Bitnami-Bibliothek für Azure](https://azure.bitnami.com/)
* Mesosphere: [Azure Marketplace – Mesosphere DC/OS on Azure](https://azure.microsoft.com/services/kubernetes-service/mesosphere/)
* Docker: [Azure Marketplace – Docker-Images](https://azuremarketplace.microsoft.com/marketplace/apps?search=docker&page=1&filters=virtual-machine-images)
* Jenkins: [Azure Marketplace – CloudBees Jenkins Platform](https://azuremarketplace.microsoft.com/marketplace/apps/cloudbees.cloudbees-core-contact)


## <a name="cloud-init"></a>cloud-init 

Um eine sinnvolle DevOps-Kultur zu erzielen, muss die gesamte Infrastruktur als Code definiert sein.  Wenn die Infrastruktur vollständig im Codeformat vorliegt, kann sie problemlos neu erstellt werden.  Azure arbeitet mit allen wichtigen Automatisierungstools zusammen, wie z.B. Ansible, Chef, SaltStack und Puppet.  Azure stellt auch eigene Tools für die Automatisierung bereit:

* [Azure-Vorlagen](create-ssh-secured-vm-from-template.md)
* [Azure `VMaccess`](../extensions/vmaccess.md)

Azure unterstützt [cloud-init](https://cloud-init.io/) für die meisten Linux-Distributionen ein, die dies unterstützen.  Wir arbeiten aktiv mit unseren Linux-Distributionspartnern zusammen, um cloud-init-fähige Images im Azure Marketplace zur Verfügung zu stellen. Mit diesen Images funktionieren Ihre cloud-init-Bereitstellungen und -Konfigurationen nahtlos mit VMs und VM-Skalierungsgruppen.

* [Verwenden von cloud-init auf virtuellen Linux-Computern in Azure](using-cloud-init.md)

## <a name="storage"></a>Storage
* [Einführung in Microsoft Azure Storage](../../storage/common/storage-introduction.md)
* [Hinzufügen eines Datenträgers zu einem virtuellen Linux-Computer über die Azure-Befehlszeilenschnittstelle](add-disk.md)
* [Anfügen eines Datenträgers an eine Linux-VM im Azure-Portal](attach-disk-portal.md)

## <a name="networking"></a>Netzwerk
* [Virtuelle Netzwerke im Überblick](../../virtual-network/virtual-networks-overview.md)
* [IP-Adressen in Azure](../../virtual-network/public-ip-addresses.md)
* [Öffnen von Ports für eine Linux-VM in Azure](nsg-quickstart.md)
* [Erstellen eines vollständig qualifizierten Domänennamens im Azure-Portal](../create-fqdn.md)


## <a name="data-residency"></a>Datenresidenz

Das Feature zum Aktivieren der Speicherung von Kundendaten in einer einzelnen Region ist in Azure derzeit nur in der Region „Asien, Südosten“ (Singapur) des geografischen Raums „Asien-Pazifik“ und in der Region „Brasilien, Süden“ (São Paulo, Bundesstaat) des geografischen Raums „Brasilien“ verfügbar. Bei allen anderen Regionen werden Kundendaten unter „Geografien“ gespeichert. Weitere Informationen finden Sie unter [Trust Center](https://azuredatacentermap.azurewebsites.net/).


## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie Ihren ersten virtuellen Computer!

- [Portal](quick-create-portal.md)
- [Azure-Befehlszeilenschnittstelle](quick-create-cli.md)
- [PowerShell](quick-create-powershell.md)