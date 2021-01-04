---
title: Datenredundanz in Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Redundanzmechanismen für Metadaten in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: weehyongtok
ms.reviewer: abnarain
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: abnarain
ms.openlocfilehash: 7f95adc264ed91e75eef668b43f674ddeb7d9e89
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350023"
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