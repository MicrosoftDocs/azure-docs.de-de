---
title: include file
description: include file
services: virtual-machines
author: jpconnock
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: jeconnoc
ms.custom: include file
ms.openlocfilehash: 289912e1519a68ae607ace7766e35731af0016b9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178065"
---
# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Plattformgestützte Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager
Dieser Artikel beschreibt, wie Sie IaaS-Ressourcen (Infrastructure-as-a-Service) vom klassischen Bereitstellungsmodell zum Resource Manager-Bereitstellungsmodell migrieren. Der Artikel enthält zudem Informationen dazu, wie Sie Ressourcen aus den beiden Bereitstellungsmodellen, die in Ihrem Abonnement nebeneinander existieren, mithilfe von Standort-zu-Standort-Gateways miteinander verbinden. Informieren Sie sich weiter über [Features und Vorteile von Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md). 

## <a name="goal-for-migration"></a>Ziel der Migration
Resource Manager ermöglicht die Bereitstellung komplexer Anwendungen über Vorlagen, konfiguriert virtuelle Computer mit VM-Erweiterungen und bietet Möglichkeiten zur Zugriffsverwaltung und Markierung. Azure Resource Manager umfasst die skalierbare, parallele Bereitstellung für virtuelle Computer in Verfügbarkeitsgruppen. Das neue Bereitstellungsmodell bietet auch die unabhängige Lebenszyklusverwaltung der Bereiche Compute, Netzwerk und Speicher. Zudem liegt ein Schwerpunkt auf der standardmäßigen Sicherstellung der Sicherheit, indem virtuelle Computer in einem virtuellen Netzwerk verwendet werden.

Nahezu alle Funktionen des klassischen Bereitstellungsmodells werden für die Bereiche Compute, Netzwerk und Speicher unter Azure Resource Manager unterstützt. Um die neuen Funktionen in Azure Resource Manager zu nutzen, können Sie vorhandene Bereitstellungen aus dem klassischen Bereitstellungsmodell migrieren.

## <a name="supported-resources-for-migration"></a>Unterstützte Ressourcen für die Migration
Bei der Migration werden folgende klassische IaaS-Ressourcen unterstützt:

* Virtual Machines
* Verfügbarkeitsgruppen
* Cloud Services mit Virtual Machines
* Speicherkonten
* Virtuelle Netzwerke
* VPN-Gateways
* ExpressRoute-Gateways _(nur im gleichen Abonnement wie das virtuelle Netzwerk)_
* Netzwerksicherheitsgruppen
* Routingtabellen
* Reservierte IP-Adressen

## <a name="supported-scopes-of-migration"></a>Unterstützte Migrationsbereiche
Es gibt vier verschiedene Möglichkeiten, Compute-, Netzwerk- und Speicherressourcen zu migrieren:

* [Migration virtueller Computer (NICHT in einem virtuellen Netzwerk)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Migration virtueller Computer (in einem virtuellen Netzwerk)](#migration-of-virtual-machines-in-a-virtual-network)
* [Migration von Speicherkonten](#migration-of-storage-accounts)
* [Migration von nicht zugewiesenen Ressourcen](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migration virtueller Computer (NICHT in einem virtuellen Netzwerk)
Beim Resource Manager-Bereitstellungsmodell wird die Sicherheit Ihrer Anwendungen standardmäßig erzwungen. Beim Resource Manager-Modell müssen sich alle virtuellen Computer in einem virtuellen Netzwerk befinden. Die Azure-Plattform startet die virtuellen Computer im Rahmen der Migration neu (`Stop`, `Deallocate` und `Start`). Bei den virtuellen Netzwerken, in die die virtuellen Computer migriert werden, haben Sie zwei Möglichkeiten:

* Sie können für die Plattform die Erstellung eines neuen virtuellen Netzwerks anfordern und den virtuellen Computer zum neuen virtuellen Netzwerk migrieren.
* Sie können den virtuellen Computer zu einem vorhandenen virtuellen Netzwerk in Resource Manager migrieren.

> [!NOTE]
> In diesem Migrationsbereich sind die Vorgänge auf Verwaltungsebene und Datenebene für einen Zeitraum während der Migration unter Umständen nicht zulässig.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migration virtueller Computer (in einem virtuellen Netzwerk)
Bei den meisten VM-Konfigurationen werden nur die Metadaten zwischen dem klassischen und dem Resource Manager-Bereitstellungsmodell migriert. Die zugrunde liegenden virtuellen Computer werden auf derselben Hardware, in demselben Netzwerk und mit demselben Speicher ausgeführt. Vorgänge auf Verwaltungsebene sind während der Migration unter Umständen für einen bestimmten Zeitraum nicht zulässig. Die Datenebene funktioniert jedoch weiterhin. Dies bedeutet, dass für Ihre Anwendungen, die auf virtuellen Computern ausgeführt werden (klassisch), während der Migration keine Downtime auftritt.

Die folgenden Konfigurationen werden derzeit nicht unterstützt: Wenn die Unterstützung später hinzukommt, kann für einige virtuelle Computer in dieser Konfiguration eine Downtime auftreten (VM-Vorgänge zum Beenden, Aufheben der Zuordnung und Neustart werden durchlaufen).

* Verwendung mehrerer Verfügbarkeitsgruppen in einem Clouddienst.
* Verwendung von mindestens einer Verfügbarkeitsgruppe und von virtuellen Computern, die keiner Verfügbarkeitsgruppe angehören, in einem einzigen Clouddienst.

> [!NOTE]
> In diesem Migrationsbereich ist die Verwaltungsebene für einen Zeitraum während der Migration unter Umständen nicht zulässig. Für bestimmte Konfigurationen kann, wie zuvor beschrieben, eine Downtime der Datenebene auftreten.
>

### <a name="migration-of-storage-accounts"></a>Migration von Speicherkonten
Um eine nahtlose Migration zu ermöglichen, können Sie Resource Manager-VMs in einem klassischen Speicherkonto bereitstellen. Mit dieser Funktion können und sollen Compute- und Netzwerkressourcen unabhängig von Speicherkonten migriert werden. Sobald Sie Ihre virtuellen Computer und das virtuelle Netzwerk migrieren, müssen Sie Ihre Speicherkonten migrieren, um den Migrationsprozess abzuschließen.

Wenn Ihrem Speicherkonto weder Datenträger noch VM-Daten zugeordnet sind und das Konto nur Blobs, Dateien, Tabellen und Warteschlangen enthält, dann kann die Migration zu Azure Resource Manager als eigenständige Migration ohne Abhängigkeiten erfolgen.

> [!NOTE]
> Beim Resource Manager-Bereitstellungsmodell gilt das Konzept von klassischen Images und Datenträgern nicht. Wenn das Speicherkonto migriert wird, sind klassische Images und Datenträger im Resource Manager-Stapel nicht sichtbar, die Sicherungs-VHDs bleiben jedoch im Speicherkonto.

Die folgenden Screenshots zeigen, wie Sie ein klassisches Speicherkonto mit dem Azure-Portal auf ein Azure Resource Manager-Speicherkonto aktualisieren können:
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Navigieren Sie zum Speicherkonto.
3. Klicken Sie im Abschnitt **Einstellungen** auf **Zu ARM migrieren**.
4. Klicken Sie auf **Überprüfen**, um die Durchführbarkeit der Migration festzustellen.
5. Wenn die Überprüfung erfolgreich war, klicken Sie auf **Vorbereiten**, um ein migriertes Speicherkonto zu erstellen.
6. Geben Sie **Ja** ein, um die Migration zu bestätigen, und klicken Sie auf **Commit**, um die Migration abzuschließen.

    ![Überprüfen des Speicherkontos](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![Vorbereiten des Speicherkontos](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![Abschließen der Migration des Speicherkontos](../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>Migration nicht zugeordneter Ressourcen
Speicherkonten ohne zugeordnete Datenträger oder virtuelle Computer können unabhängig migriert werden.

Netzwerksicherheitsgruppen, Routingtabellen und reservierte IP-Adressen, die keinem virtuellen Computer und keinem virtuellen Netzwerk zugeordnet sind, können unabhängig migriert werden.

<br>

## <a name="unsupported-features-and-configurations"></a>Nicht unterstützte Features und Konfigurationen
Einige Features und Konfigurationen werden derzeit nicht unterstützt. In den folgenden Abschnitten werden die Empfehlungen von Microsoft für diese Features und Konfigurationen erläutert.

### <a name="unsupported-features"></a>Nicht unterstützte Funktionen
Die folgenden Funktionen werden derzeit nicht unterstützt: Optional können Sie diese Einstellungen entfernen, die virtuellen Computer migrieren und die Einstellungen dann im Resource Manager-Bereitstellungsmodell wieder aktivieren.

| Ressourcenanbieter | Feature | Empfehlung |
| --- | --- | --- |
| Compute | Nicht zugeordnete VM-Datenträger. | Die diesen Datenträgern zugrunde liegenden VHD-Blobs werden zusammen mit dem Speicherkonto migriert. |
| Compute | VM-Images. | Die diesen Datenträgern zugrunde liegenden VHD-Blobs werden zusammen mit dem Speicherkonto migriert. |
| Netzwerk | Endpunkt-ACLs. | Entfernen Sie Endpunkt-ACLs, und wiederholen Sie die Migration. |
| Netzwerk | Application Gateway | Entfernen Sie die Application Gateway-Instanz, bevor Sie mit der Migration beginnen, und erstellen Sie sie nach Abschluss der Migration neu. |
| Netzwerk | Virtuelle Netzwerke mit VNet-Peering. | Migrieren Sie das virtuelle Netzwerk zu Resource Manager, und führen Sie dann das Peering durch. Weitere Informationen finden Sie unter [Peering in virtuellen Netzwerken](../articles/virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Nicht unterstützte Konfigurationen
Die folgenden Konfigurationen werden derzeit nicht unterstützt:

| Dienst | Konfiguration | Empfehlung |
| --- | --- | --- |
| Ressourcen-Manager |Rollenbasierte Zugriffssteuerung (RBAC) für klassische Ressourcen |Da der URI der Ressourcen nach der Migration geändert wird, sollten Sie die RBAC-Richtlinienaktualisierungen planen, die nach der Migration ausgeführt werden müssen. |
| Compute |Mehrere Subnetze, die einem virtuellen Computer zugeordnet sind |Aktualisieren Sie die Subnetzkonfiguration so, dass nur auf ein Subnetz verwiesen wird. Dazu müssen Sie möglicherweise eine sekundäre NIC (die auf ein anderes Subnetz verweist) vom virtuellen Computer entfernen und nach Abschluss der Migration wieder hinzufügen. |
| Compute |Virtuelle Computer, die zu einem virtuellen Netzwerk gehören, aber denen kein explizites Subnetz zugewiesen ist |Sie können die VM optional löschen. |
| Compute |Virtuelle Computer mit Warnungen, Richtlinien für automatische Skalierung |Die Migration wird durchlaufen, und diese Einstellungen werden gelöscht. Wir empfehlen Ihnen dringend, vor der Migration Ihre Umgebung auszuwerten. Alternativ hierzu können Sie die Warnungseinstellungen nach Abschluss der Migration neu konfigurieren. |
| Compute |XML-VM-Erweiterungen (BGInfo 1.*, Visual Studio Debugger, Web Deploy und Remotedebuggen) |Dies wird nicht unterstützt. Es wird empfohlen, dass Sie diese Erweiterungen vom virtuellen Computer entfernen, um die Migration fortzusetzen. Andernfalls werden sie während der Migration automatisch gelöscht. |
| Compute |Startdiagnose mit Storage Premium |Deaktivieren Sie die Funktion „Startdiagnose“ für die virtuellen Computer, bevor Sie mit der Migration fortfahren. Sobald die Migration abgeschlossen ist, können Sie die Startdiagnose im Resource Manager-Stapel erneut aktivieren. Darüber hinaus sollten für Screenshots und serielle Protokolle verwendete Blobs gelöscht werden, damit diese Blobs nicht mehr berechnet werden. |
| Compute | Clouddienste, die Web-/Workerrollen enthalten | Dies wird derzeit nicht unterstützt. |
| Compute | Clouddienste mit mehreren Verfügbarkeitsgruppen. |Dies wird derzeit nicht unterstützt. Verschieben Sie die virtuellen Computer vor der Migration in dieselbe Verfügbarkeitsgruppe. |
| Compute | Virtueller Computer mit Azure Security Center-Erweiterung | Azure Security Center installiert die Erweiterungen automatisch auf Ihren virtuellen Computern, um deren Sicherheit zu überwachen und Warnungen auszulösen. Diese Erweiterungen werden in der Regel automatisch installiert, wenn die Azure Security Center-Richtlinie für das Abonnement aktiviert ist. Um die virtuellen Computer zu migrieren, deaktivieren Sie die Security Center-Richtlinie in dem Abonnement, in dem die Security Center-Überwachungserweiterung von den virtuellen Computern entfernt wird. |
| Compute | Virtueller Computer mit Sicherungs- oder Momentaufnahmeerweiterung | Diese Erweiterungen werden auf einem virtuellen Computer installiert, der mit dem Azure Backup-Dienst konfigurierten wurde. Auch wenn die Migration dieser virtuellen Computer nicht unterstützt wird, befolgen Sie [diese Anleitung](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#vault), um Sicherungen beizubehalten, die vor der Migration ausgeführt wurden.  |
| Netzwerk |Virtuelle Netzwerke, die virtuelle Computer und Web-/Workerrollen enthalten |Dies wird derzeit nicht unterstützt. Verschieben Sie die Web-/Workerrollen vor der Migration in ein eigenes virtuelles Netzwerk. Nach der Migration des klassischen virtuellen Netzwerks kann ein Peering des migrierten virtuellen Azure Resource Manager-Netzwerks mit dem klassischen virtuellen Netzwerk eingerichtet werden, um eine ähnliche Konfiguration wie zuvor zu erhalten.|
| Netzwerk | Klassische ExpressRoute-Verbindungen |Dies wird derzeit nicht unterstützt. Diese Verbindungen müssen vor dem Beginn der IaaS-Migration zu Azure Resource Manager migriert werden. Weitere Informationen finden Sie unter [Verschieben von ExpressRoute-Verbindungen vom klassischen zum Resource Manager-Bereitstellungsmodell](../articles/expressroute/expressroute-move.md).|
| Azure App Service |Virtuelle Netzwerke, die App Service-Umgebungen enthalten |Dies wird derzeit nicht unterstützt. |
| Azure HDInsight |Virtuelle Netzwerke, die HDInsight-Dienste enthalten |Dies wird derzeit nicht unterstützt. |
| Microsoft Dynamics Lifecycle Services |Virtuelle Netzwerke, die virtuelle Computer enthalten, die von Dynamics Lifecycle Services verwaltet werden |Dies wird derzeit nicht unterstützt. |
| Azure AD Domain Services |Virtuelle Netzwerke, die Azure AD Domain Services enthalten |Dies wird derzeit nicht unterstützt. |
| Azure API Management |Virtuelle Netzwerke, die Azure API Management-Bereitstellungen enthalten |Dies wird derzeit nicht unterstützt. Um das IaaS-VNET zu migrieren, ändern Sie das VNET der API Management-Bereitstellung – hierbei treten keine Ausfallzeiten auf. |
