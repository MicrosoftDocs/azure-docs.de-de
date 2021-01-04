---
title: Skalieren von Ressourcen
description: In diesem Artikel erfahren Sie, wie Sie Ihre Datenbank in Azure SQL-Datenbank und SQL Managed Instance durch Hinzufügen oder Entfernen von zugeordneten Ressourcen skalieren.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: wiassaf, sstein
ms.date: 06/25/2019
ms.openlocfilehash: 453d7e118b946d60eb3d84c6a66abdbea7db2410
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96499219"
---
# <a name="dynamically-scale-database-resources-with-minimal-downtime"></a>Dynamisches Skalieren von Datenbankressourcen bei minimaler Downtime
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Mit Azure SQL-Datenbank und SQL Managed Instance können Sie mit minimaler [Downtime](https://azure.microsoft.com/support/legal/sla/sql-database) zusätzliche Ressourcen dynamisch zu Ihrer Datenbank hinzufügen. Allerdings wird die Verbindung mit der Datenbank während einer Umschaltzeit kurzzeitig unterbrochen, was jedoch durch Wiederholungslogik abgeschwächt werden kann.

## <a name="overview"></a>Übersicht

Wenn die Nachfrage nach Ihrer App von einigen wenigen Geräten und Kunden bis in den Millionenbereich zunimmt, können Azure SQL-Datenbank und SQL Managed Instance im laufenden Betrieb mit minimaler Downtime skaliert werden. Die Skalierbarkeit ist eines der wichtigsten Merkmale von PaaS (Platform-as-a-Service), mit dem Sie Ihrem Dienst bei Bedarf dynamisch weitere Ressourcen hinzufügen können. Mit Azure SQL-Datenbank können Sie Ressourcen (CPU-Leistung, Arbeitsspeicher, E/A-Durchsatz und Speicher), die Ihren Datenbanken zugeordnet sind, leicht ändern.

Sie können Leistungsprobleme beheben, die aufgrund der vermehrten Nutzung Ihrer Anwendung auftreten und sich per Indizierung oder mit Methoden zum Umschreiben von Abfragen nicht beseitigen lassen. Durch das Hinzufügen von weiteren Ressourcen können Sie schnell reagieren, wenn Ihre Datenbank die derzeitigen Ressourcenlimits erreicht und eine höhere Leistung benötigt, um die eingehende Workload verarbeiten zu können. Mit Azure SQL-Datenbank können Sie die Ressourcen auch zentral herunterskalieren, wenn sie nicht benötigt werden, um die Kosten zu senken.

Sie müssen sich nicht mit dem Kauf von Hardware und der Änderung der zugrunde liegenden Infrastruktur befassen. Sie können eine Datenbank einfach im Azure-Portal über einen Schieberegler skalieren.

![Skalieren der Datenbankleistung](./media/scale-resources/scale-performance.svg)

Azure SQL-Datenbank bietet das [DTU-basierte Kaufmodell](service-tiers-dtu.md) und das [vCore-basierte Kaufmodell](service-tiers-vcore.md), während Azure SQL Managed Instance nur das [vCore-basierte Kaufmodell](service-tiers-vcore.md) bietet. 

- Das [DTU-basierte Kaufmodell](service-tiers-dtu.md) bietet zur Unterstützung von einfachen bis hin zu komplexen Datenbankworkloads eine Mischung aus Compute-, Arbeitsspeicher- und E/A-Ressourcen auf drei Dienstebenen: Basic, Standard und Premium. Leistungsstufen auf den einzelnen Ebenen bieten unterschiedliche Ressourcenzusammenstellungen, durch zusätzliche Speicherressourcen ergänzt werden können.
- Beim [vCore-basierten Kaufmodell](service-tiers-vcore.md) können Sie die Anzahl virtueller Kerne, die Arbeitsspeichermenge sowie Menge und Geschwindigkeit des Speichers auswählen. Dieses Kaufmodell bietet drei Dienstebenen: „Universell“, „Unternehmenskritisch“ und „Hyperscale“.

Sie können zu einer geringen monatlichen Gebühr Ihre erste App in einer kleinen Einzeldatenbank in den Dienstebenen „Basic“, „Standard“ oder „Universell“ erstellen und diese dann jederzeit manuell oder programmgesteuert in die Dienstebenen „Premium“ oder „Unternehmenskritisch“ ändern, um die Anforderungen Ihrer Lösung zu erfüllen. Die Leistungsanpassung ist möglich, ohne dass es für die App oder für Ihre Kunden zu Ausfallzeiten kommt. Dank der dynamischen Skalierbarkeit kann Ihre Datenbank in transparenter Form auf sich schnell ändernde Ressourcenanforderungen reagieren, und Sie zahlen nur für die Ressourcen, die Sie jeweils benötigen.

> [!NOTE]
> Dynamische Skalierbarkeit ist nicht dasselbe wie automatische Skalierung. Bei der automatischen Skalierung wird ein Dienst automatisch auf der Grundlage von Kriterien skaliert. Die dynamische Skalierbarkeit ermöglicht dagegen eine manuelle Skalierung mit minimaler Downtime.

Einzeldatenbanken in Azure SQL-Datenbank unterstützen die manuelle dynamische Skalierbarkeit, aber keine Autoskalierung. Ein höheres Maß an *Automatisierung* lässt sich bei Bedarf mithilfe von Pools für elastische Datenbanken erzielen, die die gemeinsame Nutzung eines Ressourcenpools auf der Grundlage individueller Datenbankanforderungen ermöglichen.
Es gibt allerdings auch Skripts, die Sie bei der Automatisierung der Skalierbarkeit für eine einzelne Datenbank in Azure SQL-Datenbank unterstützen. Ein Beispiel finden Sie unter [Überwachen und Skalieren einer einzelnen SQL­-Datenbank mit PowerShell](scripts/monitor-and-scale-database-powershell.md).

Sie können [DTU-Dienstebenen](service-tiers-dtu.md) oder [V-Kern-Merkmale](resource-limits-vcore-single-databases.md) jederzeit ändern und die Ausfallzeiten für Ihre Anwendung dabei gering halten (im Durchschnitt meist unter vier Sekunden). Für viele Unternehmen und Apps genügt es, wenn Datenbanken erstellt werden können und sich die Leistung nach oben oder unten anpassen lässt – insbesondere, wenn die Nutzungsmuster relativ gut vorhersagbar sind. Bei unvorhersagbaren Nutzungsmustern kann es jedoch schwer sein, die Kosten und Ihr Geschäftsmodell zu verwalten. In diesem Szenario verwenden Sie einen Pool für elastische Datenbanken mit einer bestimmten Anzahl von eDTUs, die von mehreren Datenbanken im Pool gemeinsam genutzt werden.

![Einführung in SQL-Datenbank: Einzeldatenbank-DTUs nach Dienst- und Leistungsebene](./media/scale-resources/single_db_dtus.png)

Azure SQL-Datenbank ermöglicht ein dynamisches Skalieren Ihrer Datenbanken:

- In einer [Einzeldatenbank](single-database-scale.md) können Sie entweder [DTU](resource-limits-dtu-single-databases.md)- oder [V-Kern](resource-limits-vcore-single-databases.md)-Modelle nutzen, um die maximale Menge von Ressourcen zu definieren, die den einzelnen Datenbanken zugewiesen werden.
- Bei [Pools für elastische Datenbanken](elastic-pool-scale.md) können Sie das maximale Ressourcenlimit pro Datenbankgruppe im Pool definieren.

In Azure SQL Managed Instance ist ebenfalls ein Skalieren möglich: 

- [SQL Managed Instance](../managed-instance/sql-managed-instance-paas-overview.md) verwendet den Modus [Virtuelle Kerne](../managed-instance/sql-managed-instance-paas-overview.md#vcore-based-purchasing-model) und ermöglicht Ihnen das Definieren der maximalen Anzahl von CPU-Kernen und des maximalen Speichers für Ihre Instanz. Alle Datenbanken innerhalb der verwalteten Instanz nutzen die der Instanz zugeordneten Ressourcen gemeinsam.

Wenn Sie die Aktion zum Hoch- oder Herunterskalieren in einer der Varianten initiieren, wird der Datenbank-Engine-Prozess neu gestartet und bei Bedarf auf einen anderen virtuellen Computer verschoben. Das Verschieben des Datenbank-Engine-Prozesses auf einen neuen virtuellen Computer ist ein **Onlineprozess**, bei dem Sie den vorhandenen Azure SQL-Datenbank-Dienst weiterhin verwenden können, während der Prozess ausgeführt wird. Sobald die Zieldatenbank-Engine vollständig initialisiert und zum Verarbeiten der Abfragen bereit ist, werden die Verbindungen [von der Quell- zur Zieldatenbank-Engine umgeleitet](single-database-scale.md#impact).

> [!NOTE]
> Sie müssen mit einer kurzen Unterbrechung der Verbindung rechnen, wenn das Hoch-/Herunterskalieren abgeschlossen ist. Wenn Sie [Wiederholungslogik bei vorübergehenden Standardfehlern](troubleshoot-common-connectivity-issues.md#retry-logic-for-transient-errors) implementiert haben, bemerken Sie den Failover nicht.

## <a name="alternative-scale-methods"></a>Alternative Skalierungsmethoden

Die Skalierung von Ressourcen ist die einfachste und effektivste Möglichkeit, die Leistung Ihrer Datenbank zu verbessern, ohne die Datenbank oder den Anwendungscode zu ändern. Manchmal kann es vorkommen, dass auch die höchsten Dienstebenen, Computegrößen und Leistungsoptimierungen nicht zu einer erfolgreichen und kostengünstigen Verarbeitung Ihrer Workload führen. In diesen Fällen haben Sie weitere Möglichkeiten zum Skalieren Ihrer Datenbank:

- Die [horizontale Leseskalierung](read-scale-out.md) ist ein verfügbares Feature, bei dem Sie ein schreibgeschütztes Replikat Ihrer Daten erhalten, über das Sie anspruchsvolle schreibgeschützte Abfragen (z. B. Berichte) ausführen können. Mit einem schreibgeschützten Replikat wird Ihre schreibgeschützte Workload verarbeitet, ohne dass sich Auswirkungen auf die Ressourcenverwendung in Ihrer primären Datenbank ergeben.
- Das [Datenbank-Sharding](elastic-scale-introduction.md) umfasst eine Reihe von Verfahren, mit denen Sie Ihre Daten in mehrere Datenbanken aufteilen und unabhängig voneinander skalieren können.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Verbesserung der Datenbankleistung durch das Ändern des Datenbankcodes finden Sie unter [Suchen und Anwenden von Empfehlungen zur Leistung](database-advisor-find-recommendations-portal.md).
- Informationen zur Optimierung Ihrer Datenbank mit integrierter Datenbank-Intelligence finden Sie unter [Automatische Optimierung](automatic-tuning-overview.md).
- Informationen zur horizontalen Leseskalierung in Azure SQL-Datenbank finden Sie unter [Verwenden von schreibgeschützten Replikaten für den Lastenausgleich schreibgeschützter Abfrageworkloads](read-scale-out.md).
- Informationen zum Datenbank-Sharding finden Sie unter [Horizontales Hochskalieren mit Azure SQL-Datenbank](elastic-scale-introduction.md).
