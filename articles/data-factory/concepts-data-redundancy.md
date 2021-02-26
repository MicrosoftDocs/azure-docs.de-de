---
title: Datenredundanz in Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Redundanzmechanismen für Metadaten in Azure Data Factory.
author: nabhishek
ms.reviewer: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: abnarain
ms.openlocfilehash: 9d1c22b9ac6912f99838733a4326cb4082f49a6c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369695"
---
# <a name="azure-data-factory-data-redundancy"></a>**Datenredundanz in Azure Data Factory**

Zu den Azure Data Factory-Daten gehören Metadaten (Pipeline, Datasets, verknüpfte Dienste, Integration Runtime und Trigger) und Überwachungsdaten (Pipeline, Trigger und Aktivitätsausführungen). 

In allen Regionen (mit Ausnahme von „Brasilien, Süden“ und „Asien, Südosten“) werden Azure Data Factory-Daten in der [gepaarten Region](../best-practices-availability-paired-regions.md#azure-regional-pairs) gespeichert und repliziert, um Schutz vor dem Verlust der Metadaten zu bieten. Wenn regionale Rechenzentren ausfallen, initiiert Microsoft möglicherweise ein regionales Failover Ihrer Azure Data Factory-Instanz. In den meisten Fällen ist von Ihrer Seite keine Aktion erforderlich. Wenn das von Microsoft verwaltete Failover abgeschlossen ist, können Sie in der Failoverregion auf Ihre Azure Data Factory-Instanz zugreifen. 

Aufgrund der Anforderungen an die Datenresidenz in den Regionen „Brasilien, Süden“ und „Asien, Südosten“ werden Azure Data Factory-Daten [nur in der lokalen Region](../storage/common/storage-redundancy.md#locally-redundant-storage) gespeichert. Für die Region „Asien, Südosten“ werden alle Daten in Singapur gespeichert. Für „Brasilien, Süden“ erfolgt die Speicherung sämtlicher Daten in Brasilien. Wenn die Region aufgrund einer Katastrophe ausfällt, kann Microsoft Ihre Azure Data Factory-Daten nicht wiederherstellen.  

> [!NOTE]
> Das von Microsoft verwaltete Failover gilt nicht für selbstgehostete Integration Runtimes (SHIR), da eine solche Infrastruktur in der Regel vom Kunden verwaltet wird. Wenn die SHIR auf einer Azure-VM eingerichtet wurde, wird [Azure Site Recovery](../site-recovery/site-recovery-overview.md) für das Ausführen eines [Failovers der Azure-VM](../site-recovery/azure-to-azure-architecture.md) in eine andere Region empfohlen.



## <a name="using-source-control-in-azure-data-factory"></a>**Verwenden der Quellcodeverwaltung in Azure Data Factory**

Damit Sie Änderungen an Ihren Azure-Data Factory-Metadaten sicher nachverfolgen und überwachen können, sollten Sie die Einrichtung einer Quellcodeverwaltung für Ihre Azure Data Factory-Instanz in Erwägung ziehen. Sie ermöglichen damit auch den Zugriff auf Ihre JSON-Metadatendateien für Pipelines, Datasets, verknüpfte Dienste und Trigger. Azure Data Factory ermöglicht Ihnen das Arbeiten mit unterschiedlichen Git-Repositorys (Azure DevOps und GitHub). 

 Erfahren Sie, wie Sie die [Quellcodeverwaltung in Azure Data Factory](./source-control.md) einrichten. 

> [!NOTE]
> Bei einem Notfall (Ausfall der Region) können neue Data Factorys manuell oder automatisiert bereitgestellt werden. Nachdem die neue Data Factory erstellt wurde, können Sie Ihre JSON-Daten für Pipelines, Datasets und verknüpfte Dienste aus dem vorhandenen Git-Repository wiederherstellen. 



## <a name="data-stores"></a>**Datenspeicher**

Azure Data Factory ermöglicht auch das Verschieben von Daten zwischen lokalen Datenspeichern und Clouddatenspeichern. Um die Geschäftskontinuität mit Ihren Datenspeichern sicherzustellen, sollten Sie die Empfehlungen zur Geschäftskontinuität für die einzelnen Datenspeicher konsultieren. 

 

## <a name="see-also"></a>Siehe auch

- [Azure-Regionspaare](../best-practices-availability-paired-regions.md)
- [Datenresidenz in Azure](https://azure.microsoft.com/global-infrastructure/data-residency/)