---
title: Anfängliche Größe von Servergruppen – Hyperscale (Citus) – Azure Database for PostgreSQL
description: Auswählen der richtigen Anfangsgröße für Ihren Anwendungsfall
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 7e68e9f8caad7d7e4bc44bc4e1e55150a78b4a98
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026402"
---
# <a name="pick-initial-size-for-hyperscale-citus-server-group"></a>Auswählen der Anfangsgröße für eine Hyperscale (Citus)-Servergruppe

Die Größe einer Servergruppe bezieht sich auf die Anzahl von Knoten und deren Hardwarekapazität und lässt sich [ganz einfach ändern](howto-hyperscale-scale-grow.md). Für neue Servergruppen müssen Sie aber dennoch eine Anfangsgröße auswählen. Im Folgenden finden Sie einige Tipps für eine sinnvolle Festlegung dieses Werts.

## <a name="multi-tenant-saas-use-case"></a>Anwendungsfall für mehrinstanzenfähige SaaS-Anwendungen

Wenn Sie von einer vorhandenen PostgreSQL-Datenbankinstanz mit einem einzelnen Knoten zu Hyperscale (Citus) migrieren, empfiehlt sich die Auswahl eines Clusters, in dem die Anzahl der virtuellen Workerkerne und der Gesamtarbeitsspeicher den Werten der ursprünglichen Instanz entspricht. In solchen Szenarien konnten wir Leistungssteigerungen auf das Zwei- bis Dreifache feststellen, weil das Sharding die Ressourcennutzung verbessert, kleinere Indizes ermöglicht usw.

Die Anzahl der virtuellen Kerne ist dabei tatsächlich die einzige Entscheidung. Die RAM-Zuweisung wird derzeit basierend auf der Anzahl virtueller Kerne festgelegt. Eine Beschreibung finden Sie auf der Seite mit den [Konfigurationsoptionen für Hyperscale (Citus)](concepts-hyperscale-configuration-options.md).
Der Koordinatorknoten benötigt nicht so viel RAM wie Worker, aber es gibt keine Möglichkeit, RAM und virtuelle Kerne unabhängig voneinander auszuwählen.

## <a name="real-time-analytics-use-case"></a>Anwendungsfall für Echtzeitanalysen

Virtuelle Kerne insgesamt: Wenn Arbeitsdaten in den RAM passen, können Sie von einer linearen Leistungsverbesserung bei Hyperscale (Citus) proportional zur Anzahl der Workerkerne ausgehen. Berücksichtigen Sie beim Ermitteln der richtigen Anzahl von virtuellen Kernen für Ihre Anforderungen die aktuelle Latenz für Abfragen in Ihrer Einzelknoten-Datenbank und die erforderliche Latenz in Hyperscale (Citus). Teilen Sie die aktuelle Wartezeit durch die gewünschte Wartezeit, und runden Sie das Ergebnis.

Worker-RAM: Der beste Fall wäre die Bereitstellung von so viel Speicher, dass der Großteil des Arbeitssatzes in den Speicher passt. Die Art der Abfragen, die Ihre Anwendung verwendet, wirkt sich auf die Arbeitsspeicheranforderungen aus. Sie können EXPLAIN ANALYZE für eine Abfrage ausführen, um zu ermitteln, wie viel Arbeitsspeicher diese Abfrage benötigt. Denken Sie daran, dass virtuelle Kerne und RAM zusammen skaliert werden, wie im Artikel [Konfigurationsoptionen für Hyperscale (Citus)](concepts-hyperscale-configuration-options.md) beschrieben.

## <a name="next-steps"></a>Nächste Schritte

- [Skalieren einer Servergruppe](howto-hyperscale-scale-grow.md)
- Erfahren Sie mehr über [Leistungsoptionen](concepts-hyperscale-configuration-options.md) für Servergruppen.
