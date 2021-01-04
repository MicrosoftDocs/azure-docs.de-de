---
title: Willkommen bei der Wingtips-App
description: Erfahren Sie etwas über Datenbank-Mandantenmodelle und über die Beispielanwendung Wingtips SaaS für Azure SQL-Datenbank in der Cloudumgebung.
keywords: Tutorial zur SQL-Datenbank
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: a2969ce6ceda0d1b71ec991b32f5b10acf9bfa12
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92780375"
---
# <a name="the-wingtip-tickets-saas-application"></a>Die Wingtip Tickets SaaS-Anwendung
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In jedem der drei Beispiele wird die gleiche SaaS-Anwendung *Wingtip Tickets* implementiert. Die App ist eine einfache SaaS-App für die Auflistung von Veranstaltungsorten, z.B. Theater oder Clubs, und den Verkauf von Eintrittskarten. Jeder Veranstaltungsort ist ein Mandant der App und umfasst spezifische Daten: Details zum Ort, eine Liste der Veranstaltungen, Kunden, Kartenbestellungen usw.  Die App veranschaulicht zusammen mit den Verwaltungsskripts und Tutorials ein vollständiges SaaS-Szenario. Hierzu gehört Folgendes: Bereitstellung von Mandanten, Überwachung und Verwaltung der Leistung, Schemaverwaltung sowie mandantenübergreifende Berichterstellung und Analyse.

## <a name="three-saas-application-and-tenancy-patterns"></a>Drei Muster für SaaS-Anwendung und Mandanten

Es stehen drei Versionen der App zur Verfügung. Jede dieser Versionen steht für ein anderes Datenbankmandantenmuster in Azure SQL-Datenbank.  Die erste Version verwendet eine eigenständige Anwendung pro Mandant mit eigener Datenbank. Die zweite verwendet eine mehrinstanzenfähige App mit einer Datenbank pro Mandant. Das dritte verwendet eine mehrinstanzenfähige App mit mehrinstanzenfähigen Datenbanken mit Sharding.

![Drei Mandantenmuster][image-three-tenancy-patterns]

 Jedes Beispiel enthält den Anwendungscode sowie Verwaltungsskripts und Tutorials mit verschiedenen Entwurfs- und Verwaltungsmustern.  Jedes Beispiel lässt sich innerhalb von fünf Minuten bereitstellen.  Alle drei Beispiele können nebeneinander bereitgestellt werden, sodass Sie die Unterschiede hinsichtlich Entwurf und Verwaltung miteinander vergleichen können.

## <a name="standalone-application-per-tenant-pattern"></a>Muster mit einer eigenständigen Anwendung pro Mandant

Das Muster mit einer eigenständigen Anwendung pro Mandant besteht aus einer einzelnen Mandantenanwendung und einer Datenbank für jeden Mandanten. Die Anwendung jedes Mandanten wird mit der zugehörigen Datenbank in einer separaten Azure-Ressourcengruppe bereitgestellt. Die Ressourcengruppe kann im Abonnement des Dienstanbieters oder im Abonnement des Mandanten bereitgestellt und vom Anbieter im Auftrag des Mandanten verwaltet werden. Das Muster mit einer eigenständigen Anwendung pro Mandant bietet das höchste Maß an Isolierung für die Mandanten, ist aber in der Regel die teuerste Lösung, da Ressourcen nicht von mehreren Mandanten gemeinsam genutzt werden können.  Dieses Muster eignet sich gut für Anwendungen, die möglicherweise komplexer sind und für eine kleinere Anzahl von Mandanten bereitgestellt werden.  Bei eigenständigen Bereitstellungen kann die Anwendung für den einzelnen Mandanten einfacher als in anderen Mustern angepasst werden.  

Weitere Informationen finden Sie in den [Tutorials][docs-tutorials-for-wingtip-sa] und den Code auf GitHub: [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa].

## <a name="database-per-tenant-pattern"></a>Muster mit einer Datenbank pro Mandant

Das Muster mit einer Datenbank pro Mandant ist eine effektive Version für Dienstanbieter, die sowohl ihre Mandanten isolieren müssen als auch einen zentralisierten Dienst ausführen möchten, der eine kosteneffiziente Verwendung gemeinsam genutzter Ressourcen ermöglicht. Für jeden Veranstaltungsort (also jeden Mandanten) wird eine Datenbank erstellt, und alle Datenbanken werden zentral verwaltet. Datenbanken können in Pools für elastische Datenbanken gehostet werden, um eine kosteneffiziente und einfache Leistungsverwaltung zu ermöglichen, die von den nicht vorhersehbaren Workloadmustern der Mandanten profitiert. Eine Katalogdatenbank enthält die Zuordnung zwischen Mandanten und ihren Datenbanken. Diese Zuordnung wird mithilfe der Verwaltungsfeatures der [Clientbibliothek für elastische Datenbanken](elastic-database-client-library.md) für die Shardzuordnung verwaltet, die eine effiziente Verbindungsverwaltung für die Anwendung ermöglicht.

Weitere Informationen finden Sie in den [Tutorials][docs-tutorials-for-wingtip-dpt] und den Code auf GitHub: [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt].

## <a name="sharded-multi-tenant-database-pattern"></a>Muster für mehrinstanzenfähige Datenbanken mit Sharding

Mehrinstanzenfähige Datenbanken sind eine effektive Möglichkeit für Dienstanbieter, die die Kosten pro Mandant senken möchten und für die ein geringeres Maß an Isolation für die Mandanten kein Problem darstellt. Mit diesem Muster lässt sich eine große Anzahl von Mandanten mit einer einzelnen Datenbank verwalten, wodurch die Kosten pro Mandant sinken. Durch Sharding (horizontales Partitionieren) der Mandanten über mehrere Datenbanken hinweg ist eine nahezu unbegrenzte Skalierung möglich. Die Mandanten werden über eine Katalogdatenbank den Datenbanken zugeordnet.  

Dieses Muster ermöglicht auch ein *hybrides* Modell, bei dem Sie durch Verwenden einer Datenbank für mehrere Mandanten die Kosten optimieren oder mit einem einzelnen Mandanten mit eigener Datenbank für eine optimale Isolierung sorgen können. Diese Entscheidung kann für jeden Mandanten einzeln getroffen werden, entweder während der Bereitstellung des Mandanten oder später – ohne Auswirkungen auf die Anwendung.  Dieses Modell lässt sich effektiv einsetzen, wenn Gruppen von Mandanten unterschiedlich behandelt werden müssen. Beispielsweise können kostengünstige Mandanten freigegebenen Datenbanken zugewiesen werden, während Premium-Mandanten eigene Datenbanken erhalten. 

Weitere Informationen finden Sie in den [Tutorials][docs-tutorials-for-wingtip-mt] und den Code auf GitHub: [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt].

## <a name="next-steps"></a>Nächste Schritte

#### <a name="conceptual-descriptions"></a>Beschreibungen der Konzepte

- Eine genauere Erläuterung der Mandantenmuster für Anwendungen finden Sie unter [Mandantenmuster für mehrinstanzenfähige SaaS-Datenbanken][saas-tenancy-app-design-patterns-md].

#### <a name="tutorials-and-code"></a>Tutorials und Code

- Eigenständige Anwendung pro Mandant:
    - [Tutorials für eigenständige Anwendung][docs-tutorials-for-wingtip-sa]
    - [Code für eigenständige Anwendung auf GitHub][github-code-for-wingtip-sa]

- Eine Datenbank pro Mandant:
    - [Tutorials für eine Datenbank pro Mandant][docs-tutorials-for-wingtip-dpt]
    - [Code für eine Datenbank pro Mandant auf GitHub][github-code-for-wingtip-dpt]

- Mehrinstanzenfähige Datenbank mit Shards:
    - [Tutorials für mehrinstanzenfähige Datenbanken mit Shards][docs-tutorials-for-wingtip-mt]
    - [Code für mehrinstanzenfähige Datenbanken mit Shards auf GitHub][github-code-for-wingtip-mt]



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png "Drei Mandantenmuster"

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: ./saas-standaloneapp-get-started-deploy.md
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: ./saas-dbpertenant-wingtip-app-overview.md
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: ./saas-multitenantdb-get-started-deploy.md
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb