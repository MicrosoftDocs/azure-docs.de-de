---
title: Übersicht über virtuelle Linux-Computer in Azure | Microsoft-Dokumentation
description: Beschreibt die Azure Compute-, Speicher- und Netzwerkdienste in Bezug auf virtuelle Linux-Computer.
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: rickstercdn
manager: jeconnoc
editor: ''
ms.assetid: 7965a80f-ea24-4cc2-bc43-60b574101902
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/29/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: a5203d75e673a7b8206446553414ff933e00b0b6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "60542525"
---
# <a name="azure-and-linux"></a>Azure und Linux
Microsoft Azure ist eine wachsende Sammlung von integrierten Diensten der öffentlichen Cloud, z.B. für Analysen, virtuelle Computer, Datenbanken, mobile Geräte, Netzwerke, Speicher und Web. Azure eignet sich also perfekt zum Hosten Ihrer Lösungen.  Microsoft Azure stellt eine skalierbare Computingplattform bereit, bei der Sie nur für die tatsächliche gewünschte Nutzung bezahlen – ohne dass Sie in lokale Hardware investieren müssen.  Azure ist darauf ausgelegt, dass Sie Ihre Lösungen wie gewünscht vertikal und horizontal auf den Stand hochskalieren, der für die Erfüllung der Anforderungen Ihrer Kunden erforderlich ist.

Wenn Sie mit den Funktionen der verschiedenen Amazon Web Services (AWS) vertraut sind, sehen Sie sich den [Vergleich der Dienste von Azure und AWS](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/)an.

## <a name="regions"></a>Regionen
Microsoft Azure-Ressourcen sind auf mehrere geografische Regionen weltweit verteilt.  Eine „Region“ umfasst mehrere Rechenzentren in einem bestimmten geografischen Bereich. Azure verfügt aktuell (Stand: August 2018) über 42 allgemein verfügbare Regionen auf der ganzen Welt. Weitere 12 Regionen wurden bereits angekündigt. Das sind mehr globale Regionen als bei jedem anderen Cloudanbieter. Eine aktualisierte Liste mit vorhandenen und neu angekündigten Regionen finden Sie auf der folgenden Seite:

* [Azure-Regionen](https://azure.microsoft.com/regions/)

## <a name="availability"></a>Verfügbarkeit
Für Azure wurde eine branchenweit führende Vereinbarung zum Servicelevel von 99,9 Prozent für Einzelinstanz-VMs angekündigt. Sie gilt unter der Voraussetzung, dass Sie den virtuellen Computer mit Storage Premium für alle Datenträger bereitstellen.  Damit Ihre Bereitstellung die Qualifikation für unsere VM-Standardvereinbarung zum Servicelevel von 99,95 Prozent erreicht, müssen Sie weiterhin mindestens zwei virtuelle Computer bereitstellen, die Ihre Workload innerhalb einer Verfügbarkeitsgruppe ausführen. Durch eine Verfügbarkeitsgruppe wird sichergestellt, dass Ihre virtuellen Computer auf mehrere Fehlerdomänen in den Azure-Rechenzentren verteilt und auf Hosts mit unterschiedlichen Wartungsfenstern bereitgestellt werden. Die vollständige [Azure-SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) erläutert die garantierte Verfügbarkeit von Azure insgesamt.

## <a name="managed-disks"></a>Managed Disks

Der Managed Disks-Dienst erledigt die Erstellungs- und Verwaltungsaufgaben in Ihrem Azure Storage-Konto im Hintergrund und sorgt dafür, dass Sie sich keine Gedanken mehr über die Skalierbarkeitsgrenzen des Speicherkontos machen müssen. Sie geben die Datenträgergröße und die Leistungsstufe (Standard/Premium) an, und Azure kümmert sich um die Erstellung und Verwaltung der Datenträger. So müssen Sie sich beim Hinzufügen von Datenträgern oder beim Skalieren virtueller Computer keine Gedanken mehr über den verwendeten Speicher machen. Wenn Sie neue virtuelle Computer erstellen, [verwenden Sie die Azure-Befehlszeilenschnittstelle](quick-create-cli.md) oder das Azure-Portal, um virtuelle Computer mit verwalteten Datenträgern für Betriebssystem und Daten zu erstellen. Wenn Sie bereits über virtuelle Computer mit nicht verwalteten Datenträgern verfügen, können Sie [Ihre virtuellen Computer für die Verwendung mit verwalteten Datenträgern konvertieren](convert-unmanaged-to-managed-disks.md).

Darüber hinaus können Sie Ihre benutzerdefinierten Images in einem einzelnen Speicherkonto pro Azure-Region verwalten und mit diesen Hunderte von virtuellen Computern im gleichen Abonnement erstellen. Weitere Informationen zu Managed Disk finden Sie in der [Übersicht über Managed Disks](../linux/managed-disks-overview.md).

## <a name="azure-virtual-machines--instances"></a>Virtuelle Azure-Computer und -Instanzen
Microsoft Azure unterstützt die Ausführung einer Reihe von beliebten Linux-Distributionen, die von verschiedenen Partnerunternehmen bereitgestellt und gepflegt werden.  Im Azure Marketplace finden Sie Distributionen wie Red Hat Enterprise, CentOS, SUSE Linux Enterprise, Debian, Ubuntu, CoreOS, RancherOS, FreeBSD und viele weitere. Microsoft arbeitet aktiv mit verschiedenen Linux-Communitys zusammen, um der Liste der [von Azure unterstützten Linux-Distributionen](endorsed-distros.md) weitere Varianten hinzuzufügen.

Falls Ihre bevorzugte Linux-Distribution derzeit nicht im Katalog enthalten ist, können Sie den Ansatz „Bring your own Linux VM“ verfolgen, indem Sie eine [Linux-VHD erstellen und in Azure hochladen](create-upload-generic.md).

Mit Azure Virtual Machines können Sie sehr flexibel eine Vielzahl unterschiedlicher Computinglösungen bereitstellen. Sie können nahezu jede Workload und jede Sprache bereitstellen, und das unter fast jedem Betriebssystem – sei es Windows, Linux oder ein benutzerdefiniert erstelltes Betriebssystem von einem der immer zahlreicher werdenden Partner. Werden Ihre Anforderungen hiermit immer noch nicht erfüllt?  Keine Sorge. Sie können auch eigene Images von Ihrem lokalen Standort verwenden.

## <a name="vm-sizes"></a>VM-Größen
Die [Größe](sizes.md) des virtuellen Computers richtet sich nach der Workload, die Sie ausführen möchten. Von der gewählten Größe hängen Faktoren wie Rechenleistung, Arbeitsspeicher und Speicherplatz ab. Azure bietet eine Vielzahl von Größen zur Unterstützung vieler Anwendungstypen.

Bei Azure wird auf der Grundlage von Größe und Betriebssystem des virtuellen Computers ein [Stundenpreis](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) berechnet. Für angefangene Stunden werden lediglich die in Anspruch genommenen Minuten abgerechnet. Speicherplatz wird separat bewertet und in Rechnung gestellt.

## <a name="automation"></a>Automation
Um eine sinnvolle DevOps-Kultur zu erzielen, muss die gesamte Infrastruktur als Code definiert sein.  Wenn die Infrastruktur vollständig im Codeformat vorliegt, kann sie problemlos neu erstellt werden (Phoenix-Server).  Azure arbeitet mit allen wichtigen Automatisierungstools zusammen, wie z.B. Ansible, Chef, SaltStack und Puppet.  Azure stellt auch eigene Tools für die Automatisierung bereit:

* [Azure-Vorlagen](create-ssh-secured-vm-from-template.md)
* [Azure VMAccess](using-vmaccess-extension.md)

Azure führt Unterstützung für [cloud-init](https://cloud-init.io/) für die meisten Linux-Distributionen ein, die dies unterstützen.  Zurzeit werden die Ubuntu-VMs von Canonical mit standardmäßig aktiviertem cloud-init bereitgestellt.  RHEL, CentOS und Fedora von Red Hat unterstützen zwar „cloud-init“, auf den von Red Hat verwalteten Azure-Images ist „cloud-init“ derzeit jedoch nicht installiert.  Wenn Sie „cloud-init“ unter einem Betriebssystem der Red Hat-Familie verwenden möchten, müssen Sie ein benutzerdefiniertes Image mit installiertem „cloud-init“ erstellen.

* [Verwenden von cloud-init auf virtuellen Linux-Computern in Azure](using-cloud-init.md)

## <a name="quotas"></a>Kontingente
Jedes Azure-Abonnement verfügt über standardmäßige Kontingentgrenzen, durch die die Bereitstellung einer großen Anzahl von virtuellen Computern für Ihr Projekt beeinträchtigt werden kann. Der derzeitige Grenzwert pro Abonnement liegt bei 20 VMs pro Region.  Sie können die Kontingentgrenzen schnell und einfach erhöhen lassen, indem Sie ein Supportticket erstellen und eine Erhöhung anfordern.  Weitere Informationen zu Kontingentgrenzen finden Sie hier:

* [Einschränkungen für Azure-Abonnementdienste](../../azure-subscription-service-limits.md)

## <a name="partners"></a>Partner
Microsoft arbeitet eng mit Partnern zusammen, um sicherzustellen, dass die verfügbaren Images für die Azure-Laufzeit aktualisiert und optimiert sind.  Weitere Informationen zu Azure-Partnern finden Sie unter den folgenden Links:

* [Linux auf von Azure unterstützten Distributionen](endorsed-distros.md)
* SUSE: [Azure Marketplace – SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SLES?tab=Overview)
* Red Hat: [Azure Marketplace – Red Hat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical: [Azure Marketplace – Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian: [Azure Marketplace – Debian 8 „Jessie“](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD: [Azure Marketplace – FreeBSD 10.4](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
* CoreOS: [Azure Marketplace – CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS: [Azure Marketplace – RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami: [Bitnami-Bibliothek für Azure](https://azure.bitnami.com/)
* Mesosphere: [Azure Marketplace – Mesosphere DC/OS on Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker: [Azure Marketplace – Azure Container Service mit Docker Swarm](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins: [Azure Marketplace – CloudBees Jenkins Platform](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudbees.cloudbees-core-contact)

## <a name="getting-started-with-linux-on-azure"></a>Erste Schritte mit Linux in Azure
Um mit der Verwendung von Azure beginnen zu können, benötigen Sie ein Azure-Konto, die installierte Azure-Befehlszeilenschnittstelle und eine Kombination aus öffentlichen und privaten SSH-Schlüsseln.

### <a name="sign-up-for-an-account"></a>Registrieren für ein Konto
Der erste Schritt zur Verwendung der Azure-Cloud besteht darin, sich für ein Azure-Konto zu registrieren.  Besuchen Sie die Seite [Erstellen eines kostenlosen Azure-Kontos](https://azure.microsoft.com/pricing/free-trial/) , um zu beginnen.

### <a name="install-the-cli"></a>Installieren der Befehlszeilenschnittstelle
Mit Ihrem neuen Azure-Konto können Sie sofort mit dem Azure-Portal loslegen, einem webbasierten Verwaltungsbereich.  Um die Azure-Cloud über die Befehlszeile zu verwalten, installieren Sie `azure-cli`.  Installieren Sie die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) auf Ihrem Mac oder Ihrer Linux-Arbeitsstation.

### <a name="create-an-ssh-key-pair"></a>Erstellen eines SSH-Schlüsselpaars
Jetzt verfügen Sie über ein Azure-Konto, das Azure-Webportal und die Azure-Befehlszeilenschnittstelle.  Der nächste Schritt besteht darin, ein SSH-Schlüsselpaar zu erstellen, um über SSH eine Verbindung mit Linux herzustellen, ohne ein Kennwort zu verwenden.  [Erstellen Sie SSH-Schlüssel unter Linux und Mac](mac-create-ssh-keys.md), um Anmeldungen ohne Kennwort und eine höhere Sicherheit zu ermöglichen.

### <a name="create-a-vm-using-the-cli"></a>Erstellen eines virtuellen Computers mit der Befehlszeilenschnittstelle
Die Erstellung eines virtuellen Linux-Computers mit der CLI ist eine schnelle Möglichkeit, einen virtuellen Computer bereitzustellen, ohne das Terminal zu verlassen, an dem Sie gerade arbeiten.  Alle Elemente, die Sie im Webportal festlegen können, stehen auch über ein Flag bzw. eine Option in der Befehlszeile zur Verfügung.  

* [Erstellen eines virtuellen Linux-Computers über die Befehlszeilenschnittstelle](quick-create-cli.md)

### <a name="create-a-vm-in-the-portal"></a>Erstellen eines virtuellen Computers im Portal
Die Erstellung eines virtuellen Linux-Computers im Azure-Webportal ist eine einfache Möglichkeit, per Klick auf verschiedene Optionen zu einer Bereitstellung zu gelangen.  Anstatt manuell Befehlszeilenflags oder -optionen einzugeben, können Sie die verschiedenen Optionen und Einstellungen in einem benutzerfreundlichen Weblayout anzeigen.  Alle über die Befehlszeilenschnittstelle verfügbaren Optionen stehen auch im Portal zur Verfügung.

* [Erstellen eines virtuellen Linux-Computers mithilfe des Portals](quick-create-portal.md)

### <a name="log-in-using-ssh-without-a-password"></a>Anmelden ohne Kennwort über SSH
Der virtuelle Computer wird jetzt in Azure ausgeführt, und Sie können sich anmelden.  Die Verwendung von Kennwörtern zur Anmeldung über SSH ist unsicher und zeitaufwendig.  SSH-Schlüssel sind die sicherste und gleichzeitig schnellste Möglichkeit zur Anmeldung.  Beim Erstellen eines virtuellen Linux-Computers im Portal oder über die Befehlszeilenschnittstelle stehen Ihnen zwei Authentifizierungsoptionen zur Verfügung.  Wenn Sie ein Kennwort für SSH auswählen, konfiguriert Azure den virtuellen Computer so, dass Anmeldungen mit Kennwort zulässig sind.  Wenn Sie sich für einen öffentlichen SSH-Schlüssel entschieden haben, konfiguriert Azure den virtuellen Computer so, dass Anmeldungen nur mit SSH-Schlüsseln möglich sind. Anmeldungen per Kennwort werden deaktiviert. Um Ihren virtuellen Linux-Computer zu sichern, indem Sie nur Anmeldungen per SSH-Schlüssel zulassen, verwenden Sie beim Erstellen des virtuellen Computers im Portal oder über die Befehlszeilenschnittstelle die Option für öffentliche SSH-Schlüssel.

## <a name="related-azure-components"></a>Verwandte Azure-Komponenten
## <a name="storage"></a>Storage
* [Einführung in Microsoft Azure Storage](../../storage/common/storage-introduction.md)
* [Hinzufügen eines Datenträgers zu einem virtuellen Linux-Computer über die Azure-Befehlszeilenschnittstelle](add-disk.md)
* [Anfügen eines Datenträgers an eine Linux-VM im Azure-Portal](attach-disk-portal.md)

## <a name="networking"></a>Netzwerk
* [Virtuelle Netzwerke im Überblick](../../virtual-network/virtual-networks-overview.md)
* [IP-Adressen in Azure](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Öffnen von Ports für eine Linux-VM in Azure](nsg-quickstart.md)
* [Erstellen eines vollständig qualifizierten Domänennamens im Azure-Portal](portal-create-fqdn.md)

## <a name="containers"></a>Container
* [Virtuelle Computer und Container in Azure](containers.md)
* [Einführung in Azure Container Service](../../container-service/container-service-intro.md)
* [Bereitstellen eines Azure Container Service-Clusters](../../container-service/dcos-swarm/container-service-deployment.md)

## <a name="next-steps"></a>Nächste Schritte
Sie haben nun einen Überblick über die Verwendung von Linux in Azure.  Jetzt können Sie richtig loslegen und weitere virtuelle Computer erstellen!

* [Sehen Sie sich die wachsende Liste mit Skriptbeispielen für allgemeine Aufgaben mit der Azure-Befehlszeilenschnittstelle an.](cli-samples.md)
