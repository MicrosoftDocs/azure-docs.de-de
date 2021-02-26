---
title: Unterstützungsmatrix für Azure Migrate
description: Enthält eine Zusammenfassung der Unterstützungseinstellungen und Einschränkungen für den Azure Migrate-Dienst.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 0e1cb33ca0a5fb9a1c7ddb2c4625dfef4acd3a1e
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627396"
---
# <a name="azure-migrate-support-matrix"></a>Unterstützungsmatrix für Azure Migrate

Mit dem [Azure Migrate-Dienst](./migrate-services-overview.md) können Sie Computer bewerten und in die Microsoft Azure-Cloud migrieren. Dieser Artikel enthält eine Zusammenfassung der allgemeinen Supporteinstellungen und Einschränkungen für Azure Migrate-Szenarien und -Bereitstellungen.

## <a name="supported-assessmentmigration-scenarios"></a>Unterstützte Bewertungs-/Migrationsszenarien

Die Tabelle enthält eine Übersicht über die unterstützten Ermittlungs-, Bewertungs- und Migrationsszenarien.

**Bereitstellung** | **Details** 
--- | --- 
**Ermittlung** | Sie können Metadaten von Computern und dynamische Leistungsdaten ermitteln.
**App-Ermittlung** | Sie können die Apps, Rollen und Features ermitteln, die auf VMware-VMs ausgeführt werden. Diese Funktion ist zurzeit nur auf die Ermittlung beschränkt. Die Bewertung erfolgt zurzeit auf der Computerebene. Es sind noch keine app-, rollen- oder featurebasierte Bewertungen verfügbar. 
**Bewertung** | Bewerten Sie lokale Workloads und Daten, die auf VMware-VMs, Hyper-V-VMs und physischen Servern ausgeführt werden. Verwenden Sie für die Bewertung die Azure Migrate-Serverbewertung, den Microsoft-Datenmigrations-Assistenten (DMA) sowie andere Tools und ISV-Angebote.
**Migration** | Migrieren Sie auf physischen Servern, VMware-VMs, Hyper-V-VMs und cloudbasierten VMs ausgeführte Workloads und Daten zu Azure. Verwenden Sie für die Migration die Azure Migrate-Serverbewertung und Azure Database Migration Service (DMS) sowie andere Tools und ISV-Angebote.

> [!NOTE]
> Zurzeit können ISV-Tools in Azure Government keine Daten an Azure Migrate senden. Unabhängig davon können Sie integrierte Microsoft-Tools oder Partnertools verwenden.

## <a name="supported-tools"></a>Unterstützte Tools


Die Informationen zur Toolunterstützung sind in der Tabelle zusammengefasst.

**Tool** | **Bewerten** | **Migrieren** 
--- | --- | ---
Von der Azure Migrate-Serverbewertung | Dient zum Bewerten von [VMware-VMs](./tutorial-discover-vmware.md), [Hyper-V-VMs](./tutorial-discover-hyper-v.md) und [physischen Servern](./tutorial-discover-physical.md). |  Nicht verfügbar (N/V)
Azure Migrate: Servermigration | – | Dient zum Migrieren von [VMware-VMs](tutorial-migrate-vmware.md), [Hyper-V-VMs](tutorial-migrate-hyper-v.md) und [physischen Servern](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | – | Dient zum Migrieren von VMware-VMs, Hyper-V-VMs, physischen Servern und Public Cloud-Workloads. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Dient zum Bewerten von VMware-VMs, Hyper-V-VMs, physischen Servern und Public Cloud-Workloads. | –
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Dient zum Bewerten und Migrieren von VMware-VMs, Hyper-V-VMs, physischen Servern und Public Cloud-Workloads. |  Dient zum Migrieren von VMware-VMs, Hyper-V-VMs, physischen Servern und Public Cloud-Workloads.
[Device42](https://go.microsoft.com/fwlink/?linkid=2097158) | Dient zum Bewerten von VMware-VMs, Hyper-V-VMs, physischen Servern und Public Cloud-Workloads.| –
[DMA](/sql/dma/dma-overview?view=sql-server-2017) | Überprüfen von SQL Server-Datenbanken | –
[DMS](../dms/dms-overview.md) | – | Dient zum Migrieren von SQL Server, Oracle, MySQL, PostgreSQL und MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Dient zum Bewerten der Virtual Desktop Infrastructure (VDI). | –
[Movere](https://www.movere.io/) | Dient zum Bewerten von VMware-VMs, Hyper-V-VMs, Xen-VMs, physischen Computern, Arbeitsstationen (z. B. VDI) und Public Cloud-Workloads. | –
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | – | Dient zum Migrieren von VMWare-VMs, Hyper-V-VMs, Xen-VMs, KVM-VMs, physischen Computern und Public Cloud-Workloads. 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Dient zum Bewerten von VMware-VMs, Hyper-V-VMs, physischen Servern und Public Cloud-Workloads. | –
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Dient zum Bewerten von VMware-VMs, Hyper-V-VMs, physischen Servern, Public Cloud-Workloads und SQL Server-Datenbanken. | –
[Migrations-Assistent für Web-Apps](https://appmigration.microsoft.com/) | Dient zum Bewerten von Web-Apps. | Dient zum Migrieren von Web-Apps.


## <a name="azure-migrate-projects"></a>Azure Migrate-Projekte

**Unterstützung** | **Details**
--- | ---
Subscription | Ein Abonnement kann mehrere Azure Migrate-Projekte aufweisen.
Azure-Berechtigungen | Sie benötigen Berechtigungen für Mitwirkende oder Eigentümer im Abonnement, um ein Azure Migrate-Projekt zu erstellen.
Virtuelle VMware-Computer  | Bewerten Sie bis zu 35.000 VMware-VMs in einem einzigen Projekt.
Virtuelle Hyper-V-Computer    | Bewerten Sie bis zu 35.000 Hyper-V-VMs in einem einzigen Projekt.

Ein Projekt kann im Rahmen der Bewertungseinschränkungen sowohl VMware-VMs als auch Hyper-V-VMs umfassen.

## <a name="azure-permissions"></a>Azure-Berechtigungen

Damit Azure Migrate mit Azure verwendet werden kann, benötigen Sie die folgenden Berechtigungen, um mit der Bewertung und Migration von Computern beginnen zu können.

**Aufgabe** | **Berechtigungen** | **Details**
--- | --- | ---
Erstellen eines Azure Migrate-Projekts | Ihr Azure-Konto benötigt Berechtigungen zum Erstellen eines Projekts. | Einrichtung für [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account), [Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) oder [physische Server](./tutorial-discover-physical.md#prepare-an-azure-user-account)
Registrieren der Azure Migrate-Appliance| Azure Migrate verwendet eine schlanke [Azure Migrate-Appliance](migrate-appliance.md), um Computer mit der Azure Migrate-Serverbewertung zu bewerten und eine [Migration ohne Agent](server-migrate-overview.md) für VMware-VMs auszuführen mit der Azure Migrate- Servermigration. Diese Appliance ermittelt Computer und sendet Meta- und Leistungsdaten an Azure Migrate.<br/><br/> Bei der Registrierung werden die Registrierungsanbieter (Microsoft.OffAzure, Microsoft.Migrate und Microsoft.KeyVault) bei dem Abonnement registriert, das in der Appliance ausgewählt wurde, sodass das Abonnement mit dem Ressourcenanbieter funktioniert. Um sich zu registrieren, benötigen Sie Mitwirkender- oder Besitzerzugriff auf das Abonnement.<br/><br/> **VMware-** : Während des Onboardings erstellt Azure Migrate zwei Azure Active Directory-Apps (Azure AD-Apps). Die erste App kommuniziert mit den Appliance-Agents und dem Azure Migrate-Dienst. Die App verfügt nicht über Berechtigungen zum Ausführen von Azure Resource Management-Aufrufen und über keinen Azure RBAC-Zugriff auf Ressourcen. Die zweite App greift nur auf einen Azure Key Vault zu, der im Benutzerabonnement nur für die VMware-Migration ohne Agent erstellt wurde. Bei der Migration ohne Agents erstellt Azure Migrate eine Key Vault-Instanz, um Zugriffsschlüssel für das Replikationsspeicherkonto in Ihrem Abonnement zu verwalten. Azure Migrate verfügt über Azure RBAC-Zugriff auf Azure Key Vault (im Kundenmandanten), wenn die Ermittlung von der Appliance ausgelöst wird.<br/><br/> **Hyper-V**: Während des Onboardings. Azure Migrate erstellt eine Azure AD-App. Die App kommuniziert mit den Appliance-Agents und dem Azure Migrate-Dienst. Die App verfügt nicht über Berechtigungen zum Ausführen von Azure Resource Management-Aufrufen und über keinen Azure RBAC-Zugriff auf Ressourcen. | Einrichtung für [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account), [Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) oder [physische Server](./tutorial-discover-physical.md#prepare-an-azure-user-account)
Erstellen eines Schlüsseltresors für die VMware-Migration ohne Agent | Für das Migrieren von VMware-VMs ohne Agents mit der Azure Migrate-Servermigration erstellt Azure Migrate eine Key Vault-Instanz, um Zugriffsschlüssel für das Replikationsspeicherkonto in Ihrem Abonnement zu verwalten. Für die Tresorerstellung müssen Berechtigungen (Besitzer oder Mitwirkender und Benutzerzugriffsadministrator) für die Ressourcengruppe festgelegt werden, in der sich das Azure Migrate-Projekt befindet. | [Einrichten von Berechtigungen](./tutorial-discover-vmware.md#prepare-an-azure-user-account)

## <a name="supported-geographies-public-cloud"></a>Unterstützte geografische Regionen (öffentliche Cloud)

Sie können Azure Migrate-Projekte in verschiedenen geografischen Regionen in der öffentlichen Cloud erstellen.

- Obwohl Sie Projekte nur in diesen geografischen Regionen erstellen können, haben Sie die Möglichkeit, Computer für andere Zielstandorte zu bewerten und dorthin zu migrieren.
- Die Projektgeografie wird nur zum Speichern der ermittelten Metadaten verwendet.
- Wenn Sie ein Projekt erstellen, wählen Sie eine Geografie aus. Das Projekt und die damit verbundenen Ressourcen werden in einer der Regionen in der Geografie erstellt. Die Region wird durch den Azure Migrate-Dienst zugeordnet.

**Geografie** | **Speicherort der Metadaten**
--- | ---
Asien-Pazifik | „Asien, Osten“ und „Asien, Südosten“
Australien | „Australien, Osten“ oder „Australien, Südosten“
Brasilien | Brasilien Süd
Canada | „Kanada, Mitte“ oder „Kanada, Osten“
Europa | „Europa, Norden“ oder „Europa, Westen“
Frankreich | Frankreich, Mitte
Indien | „Indien, Mitte“ oder „Indien, Süden“
Japan |  „Japan, Osten“ oder „Japan, Westen“
Korea | „Südkorea, Mitte“ oder „Südkorea, Süden“
Schweiz | Schweiz, Norden
United Kingdom | „Vereinigtes Königreich, Süden“ oder „Vereinigtes Königreich, Westen“
USA | „USA, Mitte“ oder „USA, Westen 2“

> [!NOTE]
> In der Geografie für die Schweiz ist „Schweiz, Westen“ nur für REST-API-Benutzer verfügbar und erfordert ein genehmigtes Abonnement.

## <a name="supported-geographies-azure-government"></a>Unterstützte geografische Regionen (Azure Government)

**Aufgabe** | **Geografie** | **Details**
--- | --- | ---
Projekt erstellen | USA | Metadaten werden in „US Gov Arizona“ und „US Gov Virginia“ gespeichert.
Zielbewertung | USA | Zielregionen: „US Gov Arizona“, „US Gov Virginia“, „US Gov Texas“
Zielreplikation | USA | Zielregionen: „US DoD, Mitte“, „US DoD, Osten“, „US Gov Arizona“, „US Gov Iowa“, „US Gov Texas“, „US Gov Virginia“


## <a name="vmware-assessment-and-migration"></a>VMware-Bewertung und -Migration

[Überprüfen](migrate-support-matrix-vmware.md) Sie für die Azure Migrate- Serverbewertung und Azure Migrate: Servermigration die Unterstützungsmatrix für VMware-VMs.

## <a name="hyper-v-assessment-and-migration"></a>Hyper-V-Bewertung und -Migration

[Überprüfen](migrate-support-matrix-hyper-v.md) Sie für die Azure Migrate- Serverbewertung und Azure Migrate: Servermigration die Unterstützungsmatrix für Hyper-V-VMs.



## <a name="azure-migrate-versions"></a>Azure Migrate-Versionen

Es sind zwei Versionen des Azure Migrate-Diensts verfügbar:

- **Aktuelle Version**: Verwenden Sie diese Version, um neue Azure Migrate-Projekte zu erstellen, lokale Bewertungen durchzuführen und Bewertungen und Migrationen zu orchestrieren. [Weitere Informationen](whats-new.md)
- **Vorherige Version**: Kunden, die die Vorgängerversion von Azure Migrate verwendet haben (es wird nur die Bewertung von lokalen VMware-VMs unterstützt), sollten ab sofort die aktuelle Version verwenden. In der vorherigen Version können Sie keine neuen Azure Migrate-Projekte erstellen oder neue Ermittlungen durchführen.

## <a name="next-steps"></a>Nächste Schritte

- [Bewerten von virtuellen VMware-Computern](./tutorial-assess-vmware-azure-vm.md) für die Migration.
- [Bewerten von virtuellen Hyper-V-Computern](tutorial-assess-hyper-v.md) für die Migration.
