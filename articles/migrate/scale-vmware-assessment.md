---
title: Bewerten einer großen Anzahl von VMware-VMs für die Migration zu Azure mit Azure Migrate
description: Erfahren Sie, wie Sie eine große Anzahl von VMware-VMs mithilfe des Azure Migrate-Diensts für die Migration zu Azure bewerten.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 206df399736dbd3b5d1d52531a249bbd37646514
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753670"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Bewerten einer großen Anzahl von VMware-VMs für die Migration zu Azure


In diesem Artikel wird beschrieben, wie eine große Anzahl von lokalen VMware-VMs (1.000 bis 35.000) mithilfe des Azure Migrate-Serverbewertungstools für die Migration zu Azure bewertet wird.

[Azure Migrate](migrate-services-overview.md) stellt einen Hub mit Tools bereit, die Ihnen dabei helfen, Apps, Infrastrukturen und Workloads zu ermitteln, zu bewerten und zu Microsoft Azure zu migrieren. Der Hub umfasst Azure Migrate-Tools sowie Angebote von unabhängigen Drittanbietern (Independent Software Vendors, ISVs). 

In diesem Artikel werden folgende Vorgehensweisen behandelt:
> [!div class="checklist"]
> * Planen Sie die Bewertung in gewünschtem Umfang.
> * Konfigurieren Sie Azure-Berechtigungen, und bereiten Sie VMware für die Bewertung vor.
> * Erstellen Sie ein Azure Migrate-Projekt, und erstellen Sie eine Bewertung.
> * Überprüfen Sie die Bewertung beim Planen der Migration.


> [!NOTE]
> Wenn Sie einen Proof of Concept ausprobieren möchten, um einige virtuelle Computer zu bewerten, bevor Sie eine Bewertung im gewünschtem Umfang durchführen, lesen Sie unsere [Tutorialreihe](./tutorial-discover-vmware.md).

## <a name="plan-for-assessment"></a>Planen für die Bewertung

Bei der Planung für die Bewertung einer großen Anzahl von VMware-VMs sind einige Dinge zu berücksichtigen:

- **Planen von Azure Migrate-Projekten**: Finden Sie heraus, wie Azure Migrate-Projekte bereitgestellt werden. Wenn sich Ihre Rechenzentren beispielsweise in verschiedenen geografischen Regionen befinden oder Sie ermittlungs-, bewertungs- oder migrationsspezifische Metadaten in einer anderen geografischen Region speichern müssen, benötigen Sie möglicherweise mehrere Projekte. 
- **Planen von Appliances**: Azure Migrate verwendet für die kontinuierliche Erkennung von VMs eine lokale Azure Migrate-Appliance, die als VMware-VM bereitgestellt wird. Die Appliance überwacht Umgebungsänderungen wie z.B. das Hinzufügen von VMs, Datenträgern oder Netzwerkadaptern. Sie sendet außerdem deren Meta- und Leistungsdaten an Azure. Sie müssen herausfinden, wie viele Appliances Sie bereitstellen sollten.
- **Planen von Konten für die Ermittlung:** Die Azure Migrate-Appliance verwendet ein Konto mit Zugriff auf vCenter Server, um VMs für die Bewertung und Migration zu ermitteln. Wenn Sie mehr als 10.000 VMs entdecken, richten Sie mehrere Konten ein, da es keine Überlappungen zwischen VMs gibt, die von zwei beliebigen Appliances in einem Projekt entdeckt wurden. 

> [!NOTE]
> Wenn Sie mehrere Appliances einrichten, stellen Sie sicher, dass es keine Überlappungen zwischen den VMs der bereitgestellten vCenter-Konten gibt. Eine Entdeckung mit einer solchen Überlappung ist ein nicht unterstütztes Szenario. Wenn eine VM von mehr als einer Appliance erkannt wird, führt dies zu Duplikaten bei der Erkennung und zu Problemen, während gleichzeitig die Replikation für die VM über das Azure-Portal bei der Servermigration ermöglicht wird.

## <a name="planning-limits"></a>Planen von Einschränkungen
 
Orientieren Sie sich bei der Planung an den in dieser Tabelle zusammengefassten Einschränkungen.

**Planung** | **Einschränkungen**
--- | --- 
**Azure Migrate-Projekte** | Bewerten Sie bis zu 35.000 VMs in einem Projekt.
**Azure Migrate-Appliance** | Eine Appliance kann bis zu 10.000 VMs in einer vCenter Server-Instanz ermitteln.<br/> Eine Appliance kann nur eine Verbindung mit einer einzelnen vCenter Server-Instanz herstellen.<br/> Eine einzelne Appliance kann nur einer einzelnen Azure Migrate-Ressource zugeordnet werden.<br/>  Einer einzelnen Azure Migrate-Ressource können beliebig viele Appliances zugeordnet werden. <br/><br/> 
**Gruppe** | Sie können einer einzelnen Gruppe bis zu 35.000 VMs hinzufügen.
**Azure Migrate-Bewertung** | Sie können bis zu 35.000 virtuelle Computer in einem einzelnen Vorgang bewerten.

In Anbetracht dieser Einschränkungen sind hier einige Beispiele für Bereitstellungen aufgeführt:


**vCenter-Server** | **VMs auf Server** | **Empfehlung** | **Aktion**
---|---|---|---
Eine | < 10.000 | Ein Azure Migrate-Projekt<br/> Eine Appliance<br/> Ein vCenter-Konto für die Ermittlung | Richten Sie die Appliance ein, und stellen Sie eine Verbindung mit vCenter Server mit einem Konto her.
Eine | > 10.000 | Ein Azure Migrate-Projekt<br/> Mehrere Appliances<br/> Mehrere vCenter-Konten | Richten Sie eine Appliance für je 10.000 VMs ein.<br/><br/> Richten Sie vCenter-Konten ein, und teilen Sie das Inventar auf, um den Zugriff auf ein Konto auf weniger als 10.000 VMs zu beschränken.<br/> Stellen Sie für jede Appliance eine Verbindung mit der vCenter Server-Instanz mit einem Konto her.<br/> Sie können Abhängigkeiten zwischen Computern analysieren, die mit unterschiedlichen Appliances ermittelt wurden. <br/> <br/> Stellen Sie sicher, dass es keine Überlappungen zwischen den VMs auf den bereitgestellten vCenter-Konten gibt. Eine Entdeckung mit einer solchen Überlappung ist ein nicht unterstütztes Szenario. Wenn ein virtueller Computer von mehreren Geräten erkannt wird, führt dies zu einem Duplikat bei der Ermittlung und bei Problemen beim Aktivieren der Replikation für den virtuellen Computer mithilfe des Azure-Portal in der Server Migration.
Mehrere | < 10.000 |  Ein Azure Migrate-Projekt<br/> Mehrere Appliances<br/> Ein vCenter-Konto für die Ermittlung | Richten Sie Appliances ein, und stellen Sie eine Verbindung mit vCenter Server mit einem Konto her.<br/> Sie können Abhängigkeiten zwischen Computern analysieren, die mit unterschiedlichen Appliances ermittelt wurden.
Mehrere | > 10.000 | Ein Azure Migrate-Projekt<br/> Mehrere Appliances<br/> Mehrere vCenter-Konten | Wenn die vCenter Server-Ermittlung weniger als 10.000 VMs umfasst, richten Sie für jede vCenter Server-Instanz eine Appliance ein.<br/><br/> Wenn die vCenter Server-Ermittlung mehr als 10.000 VMs umfasst, richten Sie eine Appliance für je 10.000 VMs ein.<br/> Richten Sie vCenter-Konten ein, und teilen Sie das Inventar auf, um den Zugriff auf ein Konto auf weniger als 10.000 VMs zu beschränken.<br/> Stellen Sie für jede Appliance eine Verbindung mit der vCenter Server-Instanz mit einem Konto her.<br/> Sie können Abhängigkeiten zwischen Computern analysieren, die mit unterschiedlichen Appliances ermittelt wurden. <br/><br/> Stellen Sie sicher, dass es keine Überlappungen zwischen den VMs auf den bereitgestellten vCenter-Konten gibt. Eine Entdeckung mit einer solchen Überlappung ist ein nicht unterstütztes Szenario. Wenn ein virtueller Computer von mehreren Geräten erkannt wird, führt dies zu einem Duplikat bei der Ermittlung und bei Problemen beim Aktivieren der Replikation für den virtuellen Computer mithilfe des Azure-Portal in der Server Migration.



## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Planen der Ermittlung in einer Umgebung mit mehreren Mandanten

Wenn Sie eine Umgebung mit mehreren Mandanten planen, können Sie die Ermittlung auf die vCenter Server-Instanz beschränken.

- Sie können den Umfang der Appliance-Ermittlung auf vCenter Server-Rechenzentren, Cluster, Clusterordner, Hosts, Hostordner oder einzelne VMs festlegen.
- Wenn Ihre Umgebung für mehrere Mandanten gemeinsam genutzt wird und Sie jeden Mandanten separat ermitteln möchten, können Sie den Zugriff auf das vCenter-Konto einschränken, das die Appliance für die Ermittlung verwendet. 
    - Sie können den Umfang nach VM-Ordnern festlegen, wenn die Mandanten Hosts gemeinsam nutzen. Azure Migrate kann keine VMs erkennen, wenn dem vCenter-Konto auf Ebene des vCenter-VM-Ordners Zugriff gewährt wird. Wenn Ihre Ermittlung auf VM-Ordnern basieren soll, muss das vCenter-Konto reinen Lesezugriff auf der VM-Ebene haben. [Weitere Informationen](set-discovery-scope.md)

## <a name="prepare-for-assessment"></a>Vorbereiten für die Bewertung

Bereiten Sie Azure und VMware für die Serverbewertung vor. 

1. Überprüfen Sie die [VMware-Unterstützungsanforderungen und -einschränkungen](migrate-support-matrix-vmware.md).
2. Richten Sie Berechtigungen für Ihr Azure-Konto zur Interaktion mit Azure Migrate ein.
3. Bereiten Sie VMware für die Bewertung vor.

Befolgen Sie die Anweisungen in [diesem Tutorial](./tutorial-discover-vmware.md), um diese Einstellungen zu konfigurieren.


## <a name="create-a-project"></a>Erstellen eines Projekts

Führen Sie in Übereinstimmung mit Ihren Planungsanforderungen folgende Schritte aus:

1. Erstellen Sie Azure Migrate-Projekte.
2. Fügen Sie den Projekten das Azure Migrate-Serverbewertungstool hinzu.

[Weitere Informationen](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Erstellen und Überprüfen einer Bewertung

1. Erstellen Sie Bewertungen für VMware-VMs.
1. Überprüfen Sie die Bewertungen in Vorbereitung auf die Migrationsplanung.


Befolgen Sie die Anweisungen in [diesem Tutorial](./tutorial-assess-vmware-azure-vm.md), um diese Einstellungen zu konfigurieren.
    

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel führen Sie folgende Schritte aus:
 
> [!div class="checklist"] 
> * Planen von Azure Migrate-Bewertungen für VMware-VMs im gewünschten Umfang
> * Vorbereiten von Azure und VMware auf die Bewertung
> * Erstellen eines Azure Migrate-Projekts und Ausführen von Bewertungen
> * Überprüfen der Bewertungen in Vorbereitung auf die Migrationsplanung.

[Erfahren Sie nun](concepts-assessment-calculation.md), wie Bewertungen berechnet und [geändert](how-to-modify-assessment.md) werden.