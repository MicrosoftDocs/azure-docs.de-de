---
title: Leistungsempfehlungen – Azure Database for MariaDB
description: In diesem Artikel wird das Feature „Leistungsempfehlung“ in Azure Database for MariaDB beschrieben.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: a20b8a43c8955e1afea7a7157e3e73425fb0d806
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540466"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Leistungsempfehlungen in Azure Database for MariaDB

**Anwendungsbereich:** Azure Database for MariaDB 10.2

Mit dem Feature „Leistungsempfehlungen“ werden Ihre Datenbanken analysiert, um angepasste Vorschläge für eine verbesserte Leistung zu erstellen. Um die Empfehlungen zu generieren, untersucht die Analyse verschiedene Datenbankmerkmale einschließlich des Schemas. Aktivieren Sie den [Abfragespeicher](concepts-query-store.md) auf Ihren Server, um das Feature „Leistungsempfehlungen“ vollständig nutzen zu können. Wenn das Leistungsschema auf AUS festgelegt ist, werden durch das Einschalten des Abfragespeichers „performance_schema“ und ein Teil der Leistungsschemainstrumente aktiviert, die für das Feature benötigt werden. Nach der Implementierung von Leistungsempfehlungen sollten Sie die Leistung testen, um die Auswirkungen dieser Änderungen auszuwerten.

## <a name="permissions"></a>Berechtigungen

Zum Ausführen einer Analyse mit dem Leistungsempfehlungen-Feature sind die Berechtigungen **Besitzer** oder **Mitwirkender** erforderlich.

## <a name="performance-recommendations"></a>Empfehlungen zur Leistung

Das Feature [Leistungsempfehlungen](concepts-performance-recommendations.md) analysiert Workloads auf Ihrem Server zum Identifizieren von Indizes mit der Möglichkeit zur Verbesserung der Leistung.

Öffnen Sie im Abschnitt **Intelligente Leistung** in der Menüleiste der Azure-Portalseite für Ihren MariaDB-Server die Option **Leistungsempfehlungen**.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-page.png" alt-text="Landing Page für Leistungsempfehlungen":::

Wählen Sie **Analysieren** aus, und wählen Sie eine Datenbank aus, mit der die Analyse beginnt. Je nach Workload kann die Analyse einige Minuten dauern. Wenn die Analyse abgeschlossen ist, wird im Portal eine Benachrichtigung angezeigt. Die Analyse führt eine umfassende Untersuchung der Datenbank aus. Es wird empfohlen, Analysen während der Nebenzeiten durchzuführen.

Im Fenster **Empfehlungen** wird eine Liste mit Empfehlungen angezeigt, falls welche gefunden wurden, sowie die zugehörige Abfrage-ID, mit der diese Empfehlung generiert wurde. Mit der Abfrage-ID können Sie die Ansicht [mysql.query_store](concepts-query-store.md#mysqlquery_store) verwenden, um weitere Informationen zur Abfrage zu erhalten.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-result.png" alt-text="Neue Seite „Leistungsempfehlungen“":::

Empfehlungen werden nicht automatisch angewendet. Kopieren Sie zum Anwenden der Empfehlung den Abfragetext, und führen Sie ihn auf dem gewünschten Client aus. Denken Sie daran, die implementierte Empfehlung zu testen und zu überwachen, um sie auszuwerten.

## <a name="recommendation-types"></a>Empfehlungstypen

### <a name="index-recommendations"></a>Indexempfehlungen

Empfehlungen des Typs *Index erstellen* schlagen neue Indizes vor, um die am häufigsten ausgeführten oder zeitaufwändigen Abfragen in der Workload zu beschleunigen. Für diese Art von Empfehlung muss der [Abfragespeicher](concepts-query-store.md) aktiviert sein. Der Abfragespeicher erfasst Abfrageinformationen und stellt die detaillierten Statistiken zur Laufzeit und Häufigkeit der Abfrage zur Verfügung, die die Analyse verwendet, um die Empfehlung abzugeben.

### <a name="query-recommendations"></a>Abfrageempfehlungen

In Abfrageempfehlungen werden Optimierungen und Umschreibungen für Abfrage in der Workload vorgeschlagen. Die Leistung zeitaufwendiger Abfragen lässt sich verbessern, indem Antimuster bei MariaDB-Abfragen identifiziert werden und die Abfragesyntax korrigiert wird. Für diese Art von Empfehlung muss der Abfragespeicher aktiviert sein. Der Abfragespeicher erfasst Abfrageinformationen und stellt die detaillierten Statistiken zur Laufzeit und Häufigkeit der Abfrage zur Verfügung, die die Analyse verwendet, um die Empfehlung abzugeben.
## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Überwachung und Optimierung](concepts-monitoring.md) in Azure Database for MariaDB.