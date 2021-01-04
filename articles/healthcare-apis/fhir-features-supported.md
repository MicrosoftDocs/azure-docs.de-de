---
title: Unterstützte FHIR-Features in Azure – Azure API for FHIR
description: In diesem Artikel wird erläutert, welche Features der FHIR-Spezifikation in Azure API for FHIR implementiert sind.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: cavoeg
ms.openlocfilehash: 9a4c331d82695aecb53990fd604ade82f3361959
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452923"
---
# <a name="features"></a>Features

Azure API for FHIR bietet eine vollständig verwaltete Bereitstellung des Microsoft FHIR-Servers für Azure. Der Server ist eine Implementierung des [FHIR](https://hl7.org/fhir)-Standards. In diesem Dokument sind die wichtigsten Features des FHIR-Servers aufgeführt.

## <a name="fhir-version"></a>FHIR-Version

Letzte unterstützte Version: `4.0.1`

Derzeit ebenfalls unterstützte vorherige Versionen: `3.0.2`

## <a name="rest-api"></a>REST-API

| API                            | Unterstützt: PaaS | Unterstützt: OSS (SQL) | Unterstützt: OSS (Cosmos DB) | Comment                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| Lesen                           | Ja       | Ja       | Ja       |                                                     |
| vread                          | Ja       | Ja       | Ja       |                                                     |
| aktualisieren                         | Ja       | Ja       | Ja       |                                                     |
| Update mit optimistischer Sperre | Ja       | Ja       | Ja       |                                                     |
| update (bedingt)           | Ja       | Ja       | Ja       |                                                     |
| patch                          | Nein        | Nein        | Nein        |                                                     |
| delete                         | Ja       | Ja       | Ja       |                                                     |
| delete (bedingt)           | Nein        | Nein        | Nein        |                                                     |
| history                        | Ja       | Ja       | Ja       |                                                     |
| create                         | Ja       | Ja       | Ja       | Unterstützung für POST/PUT                               |
| create (bedingt)           | Ja       | Ja       | Ja       | Issue [#1382](https://github.com/microsoft/fhir-server/issues/1382) |
| search                         | Partial   | Teilweise   | Teilweise   | Siehe unten                                           |
| Verkettete Suche                 | Nein        | Ja       | Nein        |                                           |
| Umgekehrte verkettete Suche         | Nein        | Nein        | Nein        |                                            |
| capabilities                   | Ja       | Ja       | Ja       |                                                     |
| Batch                          | Ja       | Ja       | Ja       |                                                     |
| transaction                    | Nein        | Ja       | Nein        |                                                     |
| paging                         | Teilweise   | Teilweise   | Teilweise   | `self` und `next` werden unterstützt                     |
| intermediaries                 | Nein        | Nein        | Nein        |                                                     |

## <a name="search"></a>Suchen,

Es werden alle Suchparametertypen unterstützt. 

| Suchparametertyp | Unterstützt: PaaS | Unterstützt: OSS (SQL) | Unterstützt: OSS (Cosmos DB) | Comment |
|-----------------------|-----------|-----------|-----------|---------|
| Number                | Ja       | Ja       | Ja       |         |
| Date/DateTime         | Ja       | Ja       | Ja       |         |
| String                | Ja       | Ja       | Ja       |         |
| Token                 | Ja       | Ja       | Ja       |         |
| Verweis             | Ja       | Ja       | Ja       |         |
| Composite             | Ja       | Ja       | Ja       |         |
| Menge              | Ja       | Ja       | Ja       |         |
| URI                   | Ja       | Ja       | Ja       |         |
| Sonderfunktionen               | Nein        | Nein        | Nein        |         |


| Modifizierer             | Unterstützt: PaaS | Unterstützt: OSS (SQL) | Unterstützt: OSS (Cosmos DB) | Comment |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Ja       | Ja       | Ja       |         |
|`:exact`               | Ja       | Ja       | Ja       |         |
|`:contains`            | Ja       | Ja       | Ja       |         |
|`:text`                | Ja       | Ja       | Ja       |         |
|`:in` (Token)          | Nein        | Nein        | Nein        |         |
|`:below` (Token)       | Nein        | Nein        | Nein        |         |
|`:above` (Token)       | Nein        | Nein        | Nein        |         |
|`:not-in` (Token)      | Nein        | Nein        | Nein        |         |
|`:[type]` (Referenz)  | Nein        | Nein        | Nein        |         |
|`:below` (URI)         | Ja       | Ja       | Ja       |         |
|`:not`                 | Nein        | Nein        | Nein        |         |
|`:above` (URI)         | Nein        | Nein        | Nein        | Problem [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| Allgemeiner Suchparameter | Unterstützt: PaaS | Unterstützt: OSS (SQL) | Unterstützt: OSS (Cosmos DB) | Comment |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Ja       | Ja       | Ja       |         |
| `_lastUpdated`          | Ja       | Ja       | Ja       |         |
| `_tag`                  | Ja       | Ja       | Ja       |         |
| `_profile`              | Ja       | Ja       | Ja       |         |
| `_security`             | Ja       | Ja       | Ja       |         |
| `_text`                 | Nein        | Nein        | Nein        |         |
| `_content`              | Nein        | Nein        | Nein        |         |
| `_list`                 | Ja       | Ja       | Ja       |         |
| `_has`                  | Nein        | Nein        | Nein        |         |
| `_type`                 | Ja       | Ja       | Ja       |         |
| `_query`                | Nein        | Nein        | Nein        |         |
| `_filter`               | Nein        | Nein        | Nein        |         |

| Suchergebnisparameter | Unterstützt: PaaS | Unterstützt: OSS (SQL) | Unterstützt: OSS (Cosmos DB) | Comment |
|-------------------------|-----------|-----------|-----------|---------|
| `_sort`                 | Partial        | Teilweise   | Teilweise        |   `_sort=_lastUpdated` wird unterstützt       |
| `_count`                | Ja       | Ja       | Ja       | `_count` ist auf 100 Zeichen beschränkt. Wenn er auf einen höheren Wert als 100 festgelegt ist, werden nur 100 zurückgegeben, und im Paket wird eine Warnung zurückgegeben. |
| `_include`              | Ja       | Ja       | Ja       |Enthaltene Elemente sind auf 100 beschränkt. Einschließen in PaaS und OSS in Cosmos DB umfasst keine Unterstützung von :iterate.|
| `_revinclude`           | Ja       | Ja       | Ja       | Enthaltene Elemente sind auf 100 beschränkt. Einschließen in PaaS und OSS in Cosmos DB umfasst keine Unterstützung von :iterate.|
| `_summary`              | Partial   | Partial   | Partial   | `_summary=count` wird unterstützt |
| `_total`                | Partial   | Partial   | Partial   | _total=non und _total=accurate      |
| `_elements`             | Ja       | Ja       | Ja       |         |
| `_contained`            | Nein        | Nein        | Nein        |         |
| `containedType`         | Nein        | Nein        | Nein        |         |
| `_score`                | Nein        | Nein        | Nein        |         |

## <a name="extended-operations"></a>Erweiterte Vorgänge

Alle unterstützten Vorgänge zur Erweiterung der RESTful-API.

| Suchparametertyp | Unterstützt: PaaS | Unterstützt: OSS (SQL) | Unterstützt: OSS (Cosmos DB) | Comment |
|------------------------|-----------|-----------|-----------|---------|
| $export (gesamtes System) | Ja       | Ja       | Ja       |         |
| Patient/$export        | Ja       | Ja       | Ja       |         |
| Gruppe/$export          | Ja       | Ja       | Ja       |         |

## <a name="persistence"></a>Persistenz

Der Microsoft FHIR-Server verfügt über ein austauschbares Persistenzmodul (siehe [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence)).

Derzeit umfasst der Open-Source-Code für FHIR-Server eine Implementierung für [Azure Cosmos DB](../cosmos-db/index-overview.md) und [SQL-Datenbank](https://azure.microsoft.com/services/sql-database/).

Cosmos DB ist eine global verteilte Datenbank, die mehrere Modelle (SQL-API, MongoDB-API, usw.) unterstützt. Es unterstützt verschiedene [Konsistenzebenen](../cosmos-db/consistency-levels.md). Die standardmäßige Bereitstellungsvorlage konfiguriert den FHIR-Server mit `Strong`-Konsistenz, aber die Konsistenzrichtlinie kann auf Anforderungsbasis unter Verwendung des Anforderungsheaders `x-ms-consistency-level` geändert (allgemein gelockert) werden.

## <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung

Der FHIR-Server verwendet [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) für die Zugriffssteuerung. Insbesondere wird die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) erzwungen, wenn der Konfigurationsparameter `FhirServer:Security:Enabled` auf `true` festgelegt ist, und bei allen Anforderungen (außer `/metadata`) an den FHIR-Server muss der Anforderungsheader `Authorization` auf `Bearer <TOKEN>` festgelegt sein. Das Token muss eine oder mehrere Rollen gemäß der Definition im `roles`-Anspruch enthalten. Eine Anforderung ist zulässig, wenn der Token eine Rolle enthält, die die angegebene Aktion für die angegebene Ressource gestattet.

Derzeit werden die zulässigen Aktionen für eine bestimmte Rolle *global* auf die API angewendet.

## <a name="service-limits"></a>Diensteinschränkungen

* [**Anforderungseinheiten (Request Units, RUs):** ](../cosmos-db/concepts-limits.md) Sie können bis zu 10.000 RUs im Portal für Azure API for FHIR konfigurieren. Sie benötigen mindestens 400 RUs oder 10 RUs/GB (je nachdem, welcher Wert größer ist). Wenn Sie mehr als 10.000 RUs benötigen, können Sie über ein Supportticket eine Erhöhung des Werts anfordern. Maximal sind 1.000.000 RUs verfügbar.

* **Gleichzeitige Verbindungen** und **Instanzen**: Standardmäßig verfügen Sie über fünf gleichzeitige Verbindungen auf zwei Instanzen im Cluster (und somit insgesamt 10 gleichzeitige Anforderungen). Wenn Sie weitere gleichzeitige Anforderungen benötigen, öffnen Sie ein Supportticket mit Details zu Ihren Anforderungen.

* **Paketgröße:** Jedes Paket ist auf 500 Elemente beschränkt.

* **Datengröße:** Daten/Dokumente müssen jeweils etwas kleiner als 2 MB sein.

## <a name="performance-expectations"></a>Erwartung an die Leistung

Die Leistung des Systems hängt von der Anzahl der RUs, den gleichzeitigen Verbindungen und der Art der ausgeführten Vorgänge ab (PUT, POST usw.). Im Folgenden finden Sie einige allgemeine Bereiche, die auf der Grundlage der konfigurierten RUs zu erwarten sind. Im Allgemeinen wird die Leistung linear mit einer Erhöhung der RUs skaliert:

| Anzahl der RUs | Ressourcen/s |
|----------|---------------|
| 400      | 5-10          |
| 1\.000    | 100–150       |
| 10.000   | 225–400       |
| 100.000  | 2\.500–4.000   |

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Informationen zu den unterstützten FHIR-Features in Azure API for FHIR erhalten. Stellen Sie als Nächstes die Azure API for FHIR bereit.
 
>[!div class="nextstepaction"]
>[Bereitstellen von Azure API for FHIR](fhir-paas-portal-quickstart.md)