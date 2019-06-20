---
title: Vorgehensweise bei einer Azure-Dienstunterbrechung mit Auswirkungen auf Azure-Clouddienste | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie im Fall einer Azure-Dienstunterbrechung mit Auswirkungen auf Azure-Clouddienste vorgehen.
services: cloud-services
documentationcenter: ''
author: mmccrory
manager: timlt
editor: ''
ms.assetid: e52634ab-003d-4f1e-85fa-794f6cd12ce4
ms.service: cloud-services
ms.workload: cloud-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: memccror
ms.openlocfilehash: 976bb43fd3e6d6fdb19c733affd4afa2e49e482c
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967686"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services"></a>Vorgehensweise bei einer Azure-Dienstunterbrechung mit Auswirkungen auf Azure-Clouddienste
Bei Microsoft setzen wir uns mit großen Engagement dafür ein, dass unsere Dienste immer verfügbar sind, wenn Sie sie benötigen. Aufgrund von höherer Gewalt können jedoch gelegentlich ungeplante Dienstausfälle auftreten.

Microsoft stellt für seine Dienste Vereinbarungen zum Servicelevel (Service Level Agreements, SLAs) bereit, um dem Engagement für Verfügbarkeit und Konnektivität Nachdruck zu verleihen. Die SLAs für einzelne Azure-Dienste finden Sie unter [Vereinbarungen zum Servicelevel (SLAs)](https://azure.microsoft.com/support/legal/sla/).

Azure weist bereits viele integrierte Plattformfunktionen auf, die hoch verfügbare Anwendungen unterstützen. Weitere Informationen zu diesen Diensten finden Sie unter [Notfallwiederherstellung und Hochverfügbarkeit für in Microsoft Azure erstellte Anwendungen](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Dieser Artikel behandelt ein echtes Szenario der Notfallwiederherstellung für den Fall, dass eine ganze Region aufgrund einer Naturkatastrophe oder einer umfangreichen Dienstunterbrechung von einem Ausfall betroffen ist. So etwas kommt zwar äußerst selten vor, dennoch müssen Sie für den Ausfall einer gesamten Region vorbereitet sein. Falls eine ganze Region von einer Dienstunterbrechung betroffen ist, sind die lokal redundanten Kopien Ihrer Daten vorübergehend nicht verfügbar. Bei aktivierter Georeplikation sind drei zusätzliche Kopien Ihrer Azure Storage-Blobs und -Tabellen in einer anderen Region gespeichert. Sollte eine gesamte Region ausfallen oder die primäre Region aufgrund einer Katastrophe nicht wiederherstellbar sein, ordnet Azure alle DNS-Einträge der georeplizierten Region neu zu.

> [!NOTE]
> Beachten Sie, dass Sie keine Kontrolle über diesen Prozess haben. Er tritt nur bei rechenzentrumsweiten Dienstunterbrechungen auf. Setzen Sie daher auch auf andere anwendungsspezifische Sicherungsstrategien, um eine möglichst hohe Verfügbarkeit zu erreichen. Weitere Informationen finden Sie unter [Notfallwiederherstellung und Hochverfügbarkeit für in Microsoft Azure erstellte Anwendungen](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md). Wenn Sie das Failover selbst beeinflussen möchten, können Sie beispielsweise [georedundanten Speicher mit Lesezugriff](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage) verwenden, um eine schreibgeschützte Kopie Ihrer Daten in einer anderen Region zu erstellen.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Option 1: Verwenden einer Sicherungsbereitstellung über Azure Traffic Manager
Als zuverlässigste Lösung für die Notfallwiederherstellung empfiehlt es sich, mehrere Bereitstellungen der Anwendung in unterschiedlichen Regionen zu verwalten und dann mithilfe von [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) den Datenverkehr zwischen den Bereitstellungen weiterzuleiten. Azure Traffic Manager stellt mehrere [Routingmethoden](../traffic-manager/traffic-manager-routing-methods.md) bereit, sodass Sie auswählen können, ob ihre Bereitstellungen über ein primäres oder Sicherungsmodell verwaltet werden oder ob der Datenverkehr zwischen den Bereitstellungen aufgeteilt wird.

![Lastenausgleich von Azure Cloud Services zwischen Regionen mit Azure Traffic Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Für die schnellste Reaktion auf den Ausfall einer Region ist es wichtig, dass Sie die [Endpunktüberwachung](../traffic-manager/traffic-manager-monitoring.md) von Traffic Manager konfigurieren.

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Option 2: Bereitstellen der Anwendung in einer neuen Region
Durch die in der vorherigen Option beschriebene Verwaltung mehrerer aktiver Bereitstellungen fallen zusätzliche laufende Kosten an. Wenn Ihr Recovery Time Objective (RTO) die erforderliche Flexibilität bietet und Sie über den ursprünglichen Code oder das kompilierte Cloud Services-Paket verfügen, können Sie eine neue Instanz der Anwendung in einer anderen Region erstellen und die DNS-Einträge so aktualisieren, dass sie auf die neue Bereitstellung verweisen.

Ausführlichere Informationen zum Erstellen und Bereitstellen einer Clouddienstanwendung finden Sie unter [Erstellen und Bereitstellen eines Clouddiensts](cloud-services-how-to-create-deploy-portal.md).

Abhängig von Ihren Anwendungsdatenquellen müssen Sie möglicherweise die Wiederherstellungsvorgänge für Ihre Anwendungsdatenquelle überprüfen.

* Informationen zu Azure Storage-Datenquellen finden Sie unter [Azure Storage-Replikation](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage). Dort können Sie basierend auf dem ausgewählten Replikationsmodell für Ihre Anwendung prüfen, welche Optionen Ihnen zur Verfügung stehen.
* Informationen zu SQL-Datenbankquellen finden Sie unter [Übersicht:  Geschäftskontinuität für die Cloud und Notfallwiederherstellung für Datenbanken mit SQL-Datenbank](../sql-database/sql-database-business-continuity.md). Dort können Sie basierend auf dem ausgewählten Replikationsmodell für Ihre Anwendung prüfen, welche Optionen Ihnen zur Verfügung stehen.


## <a name="option-3-wait-for-recovery"></a>Option 3: Warten auf die Wiederherstellung
In diesem Fall ist keine weitere Aktion erforderlich. Bis zur Wiederherstellung der Region ist Ihr Dienst allerdings nicht verfügbar. Der aktuelle Dienststatus wird auf dem [Dashboard zur Azure-Dienstintegrität](https://azure.microsoft.com/status/) angezeigt.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Implementierung einer Strategie für Notfallwiederherstellung und Hochverfügbarkeit finden Sie unter [Notfallwiederherstellung und Hochverfügbarkeit für in Microsoft Azure erstellte Anwendungen](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md).

Im [technischen Leitfaden zur Resilienz in Azure](../resiliency/resiliency-technical-guidance.md)können Sie detaillierte technische Kenntnisse hinsichtlich der Funktionen einer Cloudplattform erwerben.