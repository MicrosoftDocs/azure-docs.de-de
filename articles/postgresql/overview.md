---
title: Übersicht über den relationalen Datenbankdienst Azure Database for PostgreSQL
description: Bietet eine Übersicht über den relationalen Datenbankdienst Azure-Datenbank für PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 05/06/2019
ms.openlocfilehash: f4023fa84215a0319669de0d812d8306b62278e3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "65073267"
---
# <a name="what-is-azure-database-for-postgresql"></a>Was ist Azure-Datenbank für PostgreSQL?
Azure Database for PostgreSQL ist ein relationaler Datenbankdienst in der Microsoft Cloud für Entwickler. Er basiert auf der Communityversion der Open-Source-[PostgreSQL](https://www.postgresql.org/)-Datenbank-Engine und wird mit zwei Bereitstellungsoptionen angeboten: „Einzelserver“ und „Hyperscale (Citus)“ (Vorschauversion).

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL (Einzelserver)
Die Bereitstellungsoption „Einzelserver“ bietet Folgendes:

- Integrierte Hochverfügbarkeit ohne zusätzliche Kosten (SLA von 99,99 %)
- Vorhersagbare Leistung inklusive nutzungsbasierter Bezahlung
- Vertikale Skalierung nach Bedarf in Sekundenschnelle
- Überwachung und Warnung zur schnellen Bewertung der Auswirkungen der Skalierung
- Schutz von sensiblen Daten in Ruhe und während der Übertragung
- Automatische Sicherungen und Point-in-Time-Wiederherstellung für bis zu 35 Tage
- Sicherheit und Konformität auf Unternehmensniveau

All diese Funktionen erfordern fast keine Administration und werden alle ohne zusätzliche Kosten bereitgestellt. Sie ermöglichen es Ihnen, sich auf die schnelle Anwendungsentwicklung und die Beschleunigung der Markteinführung zu konzentrieren, anstatt wertvolle Zeit und Ressourcen für die Verwaltung von VMs und der Infrastruktur aufzuwenden. Sie können Ihre Anwendung weiterhin mit den Open-Source-Tools und der Plattform Ihrer Wahl entwickeln, ohne sich neue Fähigkeiten aneignen zu müssen.

Für die Bereitstellungsoption „Einzelserver“ stehen drei Tarife zur Verfügung: „Basic“, „Allgemein“ und „Arbeitsspeicheroptimiert“. Jeder Tarif bietet unterschiedliche Ressourcenfunktionen für Ihre Datenbankworkloads. Sie können Ihre erste App mit einer kleinen Datenbank für wenige USD im Monat erstellen und die Skalierung dann so ändern, dass sie den Anforderungen Ihrer Lösung entspricht. Durch die dynamische Skalierung kann Ihre Datenbank transparent auf schnell wechselnde Ressourcenanforderungen reagieren. Sie bezahlen nur für die benötigten Ressourcen und auch nur dann, wenn Sie sie verwenden. Einzelheiten hierzu finden Sie unter  [Tarife](concepts-pricing-tiers.md).

## <a name="azure-database-for-postgresql---hyperscale-citus-preview"></a>Azure Database for PostgreSQL: Citus (Vorschauversion)
Bei der Option „Hyperscale (Citus)“ werden Abfragen mithilfe von Sharding horizontal über mehrere Computer hinweg skaliert. Die Abfrage-Engine parallelisiert eingehende SQL-Abfragen auf diesen Servern, um die Antwortzeiten bei großen Datasets zu verkürzen. Sie eignet sich für Anwendungen, die eine größere Skalierbarkeit und höhere Leistung erfordern, und wird im Allgemeinen für Workloads mit bis zu 100 GB an Daten (oder auch mehr) eingesetzt.

Die Bereitstellungsoption „Hyperscale (Citus)“ bietet Folgendes:

- Horizontale Skalierung auf mehreren Computern mithilfe von Sharding
- Parallelisierung von Abfragen auf den Servern für kürzere Antwortzeiten bei großen Datasets
- Hervorragende Unterstützung für mehrinstanzenfähige Anwendungen, operative Echtzeitanalyse und Transaktionsworkloads mit hohem Durchsatz

Bei der Option „Hyperscale (Citus)“ können für PostgreSQL erstellte Anwendungen verteilte Abfragen mit [Standardverbindungsbibliotheken](./concepts-connection-libraries.md) und minimalen Änderungen ausführen.

Beachten Sie, dass Citus derzeit als öffentliche Vorschauversion verfügbar ist und daher noch ohne Vereinbarung zum Servicelevel (Service Level Agreement, SLA) angeboten wird.

## <a name="data-security"></a>Datensicherheit
Azure-Datenbankdienste sind für ihre Datensicherheit bekannt. Diese Tradition wird auch von Azure Database for PostgreSQL fortgeführt. Der Dienst bietet Features zur Einschränkung des Zugriffs, zum Schutz von Daten (sowohl in Ruhe als auch während der Übertragung) sowie zur Überwachung von Aktivitäten. Weitere Informationen zur Sicherheit der Azure-Plattform finden Sie im [Azure Trust Center](https://azure.microsoft.com/overview/trusted-cloud/).

Der Azure Database for PostgreSQL-Dienst verwendet Speicherverschlüsselung für ruhende Daten und ist mit FIPS 140-2 konform. Daten (auch Sicherungen) werden auf dem Datenträger verschlüsselt. Der Dienst verwendet das in der Azure Storage-Verschlüsselung enthaltene AES-256-Bit-Verschlüsselungsverfahren, und die Schlüssel werden vom System verwaltet. Die Speicherverschlüsselung ist immer aktiviert und kann nicht deaktiviert werden. Für den Schutz von Daten während der Übertragung erfordert der Azure Database for PostgreSQL-Dienst standardmäßig sichere Verbindungen innerhalb des Netzwerks sowie zwischen der Datenbank und Clientanwendung.

## <a name="contacts"></a>Kontakte
Sollten Sie Fragen oder Vorschläge zur Verwendung von Azure Database for PostgreSQL haben, senden Sie eine E-Mail an das Azure Database for PostgreSQL-Team ([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Wenden Sie sich bei allgemeinen Fragen an diese Adresse, anstatt ein Supportticket zu öffnen.

Weitere Kontaktmöglichkeiten:
- Wenn Sie den Azure-Support kontaktieren oder ein Problem mit Ihrem Konto beheben lassen möchten, [erstellen Sie im Azure-Portal ein Ticket](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Wenn Sie Feedback abgeben oder Vorschläge für neue Features einreichen möchten, erstellen Sie einen Eintrag über [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Nächste Schritte
- Auf der Seite mit der [Preisgestaltung](https://azure.microsoft.com/pricing/details/postgresql/) finden Sie Kostenvergleiche und Rechner.
- Erstellen Sie Ihre erste [Einzelserverinstanz](./quickstart-create-server-database-portal.md) oder [Citus-Instanz (Vorschauversion)](./quickstart-create-hyperscale-portal.md) von Azure Database for PostgreSQL.
- Erstellen Ihrer ersten App in Python, PHP, Ruby, C\#, Java oder Node.js: [Verbindungsbibliotheken](./concepts-connection-libraries.md)
