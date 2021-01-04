---
title: Verwenden von Infrastrukturautomatisierungstools
description: Erfahren Sie, wie mit Infrastrukturautomatisierungstools wie Ansible, Chef, Puppet, Terraform und Packer virtuelle Computer in Azure erstellt und verwaltet werden.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 07/17/2020
ms.author: cynthn
ms.openlocfilehash: dfa0efaa49fbca0b927bfd32511348f3ded7e6c7
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302816"
---
# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Verwenden von Infrastrukturautomatisierungstools mit virtuellen Computern in Azure

Für die umfangreiche konsistente Erstellung und Verwaltung virtueller Azure-Computer (Virtual Machines, VMs) empfiehlt sich in der Regel eine Form der Automatisierung. Es gibt zahlreiche Tools und Lösungen, mit denen Sie die gesamte Azure-Infrastrukturbereitstellung und den gesamten Verwaltungslebenszyklus automatisieren können. In diesem Artikel werden einige der Infrastrukturautomatisierungstools vorgestellt, die Sie in Azure verwenden können. Diese Tools lassen sich in der Regel einem der folgenden Konzepte zuordnen:

- Automatisieren der Konfiguration virtueller Computer
    - Die Tools umfassen [Ansible](#ansible), [Chef](#chef), [Puppet](#puppet) und die [Azure Resource Manager-Vorlage](#azure-resource-manager-template).
    - Spezifische Tools für die Anpassung virtueller Computer sind [cloud-init](#cloud-init) für virtuelle Linux-Computer sowie [PowerShell DSC (Desired State Configuration)](#powershell-dsc) und die [benutzerdefinierte Skripterweiterung von Azure](#azure-custom-script-extension) für alle virtuellen Azure-Computer.

- Automatisieren der Infrastrukturverwaltung
    - Zur Verfügung stehen Tools wie [Packer](#packer) zum Automatisieren benutzerdefinierter VM-Image-Builds und [Terraform](#terraform) zum Automatisieren des Infrastrukturbuildprozesses.
    - [Azure Automation](#azure-automation) kann Aktionen für Ihre Azure-Infrastruktur und Ihre lokale Infrastruktur ausführen.

- Automatisieren der Anwendungsbereitstellung und -übermittlung
    - Beispiele sind [Azure DevOps Services](#azure-devops-services) und [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) ist ein Automatisierungsmodul für die Konfigurationsverwaltung, VM-Erstellung oder Anwendungsbereitstellung. Für die Authentifizierung und Verwaltung der Zielcomputer kommt bei Ansible ein Agent-loses Modell (üblicherweise mit SSH-Schlüsseln) zum Einsatz. Konfigurationsaufgaben werden in Playbooks definiert. Dabei steht eine Reihe von Ansible-Modulen zur Ausführung bestimmter Aufgaben zur Verfügung. Weitere Informationen finden Sie unter [How Ansible works](https://www.ansible.com/how-ansible-works) (Funktionsweise von Ansible).

In diesem Artikel werden folgende Themen erläutert:

- [Installieren und Konfigurieren von Ansible unter Linux für die Verwendung mit Azure](/azure/developer/ansible/install-on-linux-vm)
- [Erstellen eines virtuellen Linux-Computers](/azure/developer/ansible/vm-configure)
- [Verwalten eines virtuellen Linux-Computers](/azure/developer/ansible/vm-manage)


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) ist eine Automatisierungsplattform, mit der Sie die Konfiguration, Bereitstellung und Verwaltung Ihrer Infrastruktur definieren können. Zusätzlich verfügbare Komponenten sind Chef Habitat für die Automatisierung des Anwendungslebenszyklus (anstelle der Infrastruktur) und Chef InSpec zur Automatisierung der Einhaltung von Sicherheits- und Richtlinienanforderungen. Auf den Zielcomputern werden Chef-Clients installiert, und es ist mindestens ein zentraler Chef-Server zum Speichern und Verwalten der Konfigurationen vorhanden. Weitere Informationen finden Sie in der [Übersicht über Chef](https://docs.chef.io/chef_overview.html).

In diesem Artikel werden folgende Themen erläutert:

- [Bereitstellen von Chef Automate über den Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview)
- [Installieren von Chef unter Windows und Erstellen virtueller Azure-Computer](/azure/developer/chef/windows-vm-configure)


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) ist eine für Unternehmen geeignete Automatisierungsplattform zur Anwendungsübermittlung und -bereitstellung. Auf den Zielcomputern werden Agents installiert, um Puppet Master das Ausführen von Manifesten zu ermöglichen, die die gewünschte Konfiguration der Azure-Infrastruktur und der virtuellen Computer definieren. Zur Optimierung des DevOps-Workflows kann Puppet mit anderen Lösungen wie Jenkins und GitHub kombiniert werden. Weitere Informationen finden Sie unter [How Puppet works](https://puppet.com/products/how-puppet-works) (Funktionsweise von Puppet).

In diesem Artikel werden folgende Themen erläutert:

- [Bereitstellen von Puppet](https://puppet.com/docs/puppet/5.5/install_windows.html).


## <a name="cloud-init"></a>cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) ist ein weit verbreiteter Ansatz zum Anpassen einer Linux-VM beim ersten Start. Sie können mit cloud-init Pakete installieren und Dateien schreiben oder Benutzer und Sicherheit konfigurieren. Da cloud-init während des ersten Startvorgangs aufgerufen wird, müssen Sie keine zusätzlichen Schritte oder Agents auf Ihre Konfiguration anwenden.  Weitere Informationen zum ordnungsgemäßen Formatieren Ihrer `#cloud-config`-Dateien finden Sie auf der [cloud-init-Dokumentationswebsite](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config`-Dateien sind Base64-codierte Textdateien.

Cloud-init funktioniert auch Distributionen übergreifend. Verwenden Sie z.B. nicht **apt-get install** oder **yum install**, um ein Paket zu installieren. Stattdessen können Sie eine Liste mit zu installierenden Paketen definieren. „cloud-init“ verwendet automatisch das native Paketverwaltungstool für die ausgewählte Distribution.

Wir arbeiten aktiv mit unseren Linux-Distributionspartnern zusammen, um cloud-init-fähige Images im Azure Marketplace zur Verfügung zu stellen. Mit diesen Images funktionieren Ihre cloud-init-Bereitstellungen und -Konfigurationen nahtlos bei VMs und Skalierungsgruppen von virtuellen Computern (VMSS).
Erfahren Sie mehr über cloud-init in Azure:

- [Anpassen einer Linux-VM in Azure mit cloud-init | Microsoft-Dokumentation](./linux/using-cloud-init.md)
- [Gewusst wie: Anpassen eines virtuellen Linux-Computers beim ersten Start](./linux/tutorial-automate-vm-deployment.md)


## <a name="powershell-dsc"></a>PowerShell DSC
[PowerShell DSC (Desired State Configuration)](/powershell/scripting/dsc/overview/overview) ist eine Verwaltungsplattform zum Definieren der Konfiguration von Zielcomputern. DSC kann über den [OMI-Server (Open Management Infrastructure)](https://collaboration.opengroup.org/omi/) auch unter Linux verwendet werden.

DSC-Konfigurationen definieren, was auf einem Computer installiert und wie der Host konfiguriert werden soll. Eine LCM-Engine (Local Configuration Manager) wird auf jedem Zielknoten ausgeführt, der angeforderte Aktionen auf der Grundlage gepushter Konfigurationen verarbeitet. Ein Pull-Server ist ein Webdienst, der auf einem zentralen Host ausgeführt wird, um die DSC-Konfigurationen und die dazugehörigen Ressourcen zu speichern. Der Pull-Server kommuniziert mit der LCM-Engine auf den einzelnen Zielhosts, um die erforderlichen Konfigurationen bereitzustellen und Complianceberichte zu erstellen.

In diesem Artikel werden folgende Themen erläutert:

- [Erstellen einer einfachen DSC-Konfiguration](/powershell/scripting/dsc/quickstarts/website-quickstart)
- [Konfigurieren eines DSC-Pull-Servers](/powershell/scripting/dsc/pull-server/pullserver)
- [Verwenden von DSC für Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted)


## <a name="azure-custom-script-extension"></a>Benutzerdefinierte Skripterweiterung von Azure
Die benutzerdefinierte Skripterweiterung von Azure (verfügbar für [Linux](./extensions/custom-script-linux.md) und [Windows](./extensions/custom-script-windows.md)) lädt Skripts auf virtuelle Azure-Computer herunter und führt sie dort aus. Die Erweiterung kann beim Erstellen eines virtuellen Computers oder zu einem beliebigen Zeitpunkt für einen verwendeten virtuellen Computer verwendet werden.

Skripts können aus Azure-Speicher oder von einem anderen öffentlichen Speicherort (etwa einem GitHub-Repository) heruntergeladen werden. Mit der benutzerdefinierten Skripterweiterung können Sie Skripts in einer beliebigen Sprache schreiben, die auf dem virtuellen Quellcomputer ausgeführt werden kann. Mit diesen Skripts können Sie Anwendungen installieren oder den virtuellen Computer nach Ihren Vorstellungen konfigurieren. Zum Schutz von Anmeldeinformationen können vertrauliche Informationen wie etwa Kennwörter in einer geschützten Konfiguration gespeichert werden. Diese Anmeldeinformationen werden erst auf dem virtuellen Computer entschlüsselt.

In diesem Artikel werden folgende Themen erläutert:

- [Erstellen eines virtuellen Linux-Computers mit der Azure-Befehlszeilenschnittstelle und Verwenden der benutzerdefinierten Skripterweiterung](./scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json)
- [Erstellen eines virtuellen Windows-Computers mit Azure PowerShell und Verwenden der benutzerdefinierten Skripterweiterung](./scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json)


## <a name="packer"></a>Packer
[Packer](https://www.packer.io) automatisiert den Buildprozess beim Erstellen eines benutzerdefinierten VM-Images in Azure. Mit Packer können Sie das Betriebssystem definieren und Skripts nach der Konfiguration ausführen, um den virtuellen Computer für Ihre speziellen Anforderungen anzupassen. Der konfigurierte virtuelle Computer wird dann als verwaltetes Datenträgerimage erfasst. Packer automatisiert das Erstellen des virtuellen Quellcomputers sowie der Netzwerk- und Speicherressourcen, das Ausführen der Konfigurationsskripts und das anschließende Erstellen des VM-Images.

In diesem Artikel werden folgende Themen erläutert:

- [Erstellen eines Linux-VM-Images in Azure mithilfe von Packer](./linux/build-image-with-packer.md)
- [Erstellen eines Windows-VM-Images in Azure mithilfe von Packer](./windows/build-image-with-packer.md)


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) ist ein Automatisierungstool, mit dem Sie eine gesamte Azure-Infrastruktur mit einer einzelnen Vorlagenformatsprache – der HashiCorp Configuration Language (HCL) – definieren und erstellen können. Mit Terraform definieren Sie Vorlagen, die die Erstellung von Netzwerk-, Speicher- und VM-Ressourcen für eine bestimmte Anwendungslösung automatisieren. Ihre vorhandenen Terraform-Vorlagen können Sie für andere Plattformen mit Azure verwenden, um die Konsistenz zu gewährleisten und die Infrastrukturbereitstellung ohne Umwandlung in eine Azure Resource Manager-Vorlage zu vereinfachen.

In diesem Artikel werden folgende Themen erläutert:

- [Installieren und Konfigurieren von Terraform mit Azure](/azure/developer/terraform/getting-started-cloud-shell)
- [Erstellen einer Azure-Infrastruktur mit Terraform](/azure/developer/terraform/create-linux-virtual-machine-with-infrastructure)


## <a name="azure-automation"></a>Azure-Automatisierung
[Azure Automation](https://azure.microsoft.com/services/automation/) verwendet Runbooks, um eine Reihe von Aufgaben auf Ihren virtuellen Zielcomputern zu verarbeiten. Azure Automation dient zur Verwaltung vorhandener virtueller Computer und nicht zur Erstellung einer Infrastruktur. Azure Automation kann für virtuelle Linux- und Windows-Computer sowie für lokale virtuelle oder physische Computer mit einem Hybrid Runbook Worker ausgeführt werden. Runbooks können in einem Quellcodeverwaltungs-Repository (beispielsweise GitHub) gespeichert werden. Diese Runbooks können dann manuell oder nach einem definierten Zeitplan ausgeführt werden.

Azure Automation bietet auch einen DSC-Dienst (Desired State Configuration), mit dem Sie Definitionen für die Konfiguration einer bestimmten Gruppe von virtuellen Computern erstellen können. DSC stellt dann sicher, dass die erforderliche Konfiguration angewendet wird und der virtuelle Computer konsistent bleibt. Azure Automation DSC kann auf Computern unter Windows und Linux ausgeführt werden.

In diesem Artikel werden folgende Themen erläutert:

- [Erstellen eines PowerShell-Runbooks](../automation/learn/automation-tutorial-runbook-textual-powershell.md)
- [Verwalten von lokalen Ressourcen mithilfe eines Hybrid Runbook Workers](../automation/automation-hybrid-runbook-worker.md)
- [Verwenden von Azure Automation DSC](../automation/automation-dsc-getting-started.md)


## <a name="azure-devops-services"></a>Azure DevOps Services
[Azure DevOps Services](https://www.visualstudio.com/team-services/) ist eine Suite von Tools, mit denen Sie Code freigeben und nachverfolgen, automatisierte Builds verwenden und eine vollständige CI/CD-Pipeline (Continuous Integration/Continuous Development) erstellen können. Azure DevOps Services lässt sich zur Vereinfachung in Visual Studio und andere Editoren integrieren. Mit Azure DevOps Services können Sie auch virtuelle Azure-Computer erstellen und konfigurieren und anschließend Code für sie bereitstellen.

Weitere Informationen:

- [Azure DevOps Services](/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://jenkins.io) ist ein Continuous Integration-Server zum Bereitstellen und Testen von Anwendungen sowie zum Erstellen automatisierter Pipelines für die Codeübermittlung. Die Kernplattform von Jenkins lässt sich mit hunderten von Plug-Ins erweitern und über Webhooks auch in zahlreiche andere Produkte und Lösungen integrieren. Sie können Jenkins manuell auf einem virtuellen Azure-Computer installieren oder innerhalb eines Docker-Containers ausführen oder ein vorgefertigtes Azure Marketplace-Image verwenden.

In diesem Artikel werden folgende Themen erläutert:

- [Erstellen einer Entwicklungsinfrastruktur auf einem virtuellen Linux-Computer in Azure mit Jenkins, GitHub und Docker](/azure/developer/jenkins/pipeline-with-github-and-docker)


## <a name="azure-resource-manager-template"></a>Azure Resource Manager-Vorlage
[Azure Resource Manager](../azure-resource-manager/templates/overview.md) ist der Bereitstellungs- und Verwaltungsdienst für Azure. Er bietet eine Verwaltungsebene, die das Erstellen, Aktualisieren und Löschen von Ressourcen in Ihrem Azure-Abonnement ermöglicht. Mithilfe von Verwaltungsfeatures wie Zugriffssteuerung, Sperren und Tags können Sie Ihre Ressourcen nach der Bereitstellung schützen und organisieren.

In diesem Artikel werden folgende Themen erläutert:

- [Bereitstellen von Spot-VMs mithilfe einer Resource Manager-Vorlage](./linux/spot-template.md)
- [Erstellen eines virtuellen Windows-Computers mit einer Resource Manager-Vorlage](./windows/ps-template.md)
- [Herunterladen einer Vorlage für einen virtuellen Computer](./windows/download-template.md)
- [Erstellen einer Azure Image Builder-Vorlage](./linux/image-builder-json.md)

## <a name="next-steps"></a>Nächste Schritte
Infrastrukturautomatisierungstools können in Azure auf unterschiedlichste Weise verwendet werden. Sie können sich flexibel für die Lösung entscheiden, die am besten zu Ihren Anforderungen und zu Ihrer Umgebung passt. Falls Sie noch keine Erfahrung haben und einige der in Azure integrierten Tools ausprobieren möchten, informieren Sie sich über die Automatisierung der Anpassung eines virtuellen Computers unter [Linux](./linux/tutorial-automate-vm-deployment.md) oder [Windows](./windows/tutorial-automate-vm-deployment.md).