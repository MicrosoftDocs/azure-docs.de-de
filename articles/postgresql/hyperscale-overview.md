---
title: Übersicht über Azure Database for PostgreSQL Hyperscale (Citus)
description: Dies ist eine Übersicht über die Bereitstellungsoption „Hyperscale (Citus)“.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 09/01/2020
ms.openlocfilehash: 1734128384d63749d3c777cf6315278fced9d140
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025139"
---
# <a name="what-is-azure-database-for-postgresql---hyperscale-citus"></a>Was ist Azure Database for PostgreSQL Hyperscale (Citus)?

Azure Database for PostgreSQL ist ein relationaler Datenbankdienst in der Microsoft Cloud für Entwickler. Er basiert auf der Communityversion der [PostgreSQL](https://www.postgresql.org/)-Open-Source-Datenbank-Engine.

Hyperscale (Citus) ist eine Bereitstellungsoption, bei der Abfragen mithilfe von Sharding computerübergreifend horizontal skaliert werden. Die Abfrage-Engine parallelisiert eingehende SQL-Abfragen auf diesen Servern, um die Antwortzeiten bei großen Datasets zu verkürzen. Sie eignet sich für Anwendungen, die eine größere Skalierbarkeit und höhere Leistung als andere Bereitstellungsoptionen erfordern, und wird im Allgemeinen für Workloads mit bis zu 100 GB an Daten (oder auch mehr) eingesetzt.

Hyperscale (Citus) bietet Folgendes:

- Horizontale Skalierung auf mehreren Computern mithilfe von Sharding
- Parallelisierung von Abfragen auf den Servern für kürzere Antwortzeiten bei großen Datasets
- hervorragende Unterstützung für mehrinstanzenfähige Anwendungen, operative Echtzeitanalyse und Transaktionsworkloads mit hohem Durchsatz

Bei der Option „Hyperscale (Citus)“ können für PostgreSQL erstellte Anwendungen verteilte Abfragen mit [Standardverbindungsbibliotheken](./concepts-connection-libraries.md) und minimalen Änderungen ausführen.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen Sie zunächst Ihre erste](./quickstart-create-hyperscale-portal.md) Azure Database for PostgreSQL Hyperscale (Citus)-Servergruppe.
- Auf der Seite mit der [Preisgestaltung](https://azure.microsoft.com/pricing/details/postgresql/) finden Sie Kostenvergleiche und Rechner. Hyperscale (Citus) bietet auch Rabatte für reservierte Instanzen. Weitere Informationen finden Sie unter [Vorauszahlung für Azure Database for PostgreSQL – Hyperscale (Citus)-Computeressourcen mit reservierter Kapazität](concepts-hyperscale-reserved-pricing.md).
- Bestimmen der besten [Anfangsgröße](howto-hyperscale-scale-initial.md) für Ihre Servergruppe
