---
title: Horizontales Skalieren
description: SaaS-Entwickler (Software-as-a-Service) können mit diesen Tools problemlos elastische und skalierbare Datenbanken in der Cloud erstellen.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 2eb7984097b4edf34ed2f0214e1453246e12916f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92786750"
---
# <a name="scaling-out-with-azure-sql-database"></a>Horizontales Hochskalieren mit Azure SQL-Datenbank
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Mithilfe der Tools für **elastische Datenbanken** können Sie Datenbanken in Azure SQL-Datenbank problemlos aufskalieren. Mit diesen Tools und Features können Sie Datenbankressourcen von **Azure SQL-Datenbank** zum Erstellen von Lösungen für transaktionale Workloads und insbesondere SaaS-Anwendungen (Software-as-a-Service) verwenden. Die Features für elastische Datenbanken umfassen folgende:

* [Clientbibliothek für elastische Datenbanken](elastic-database-client-library.md): Die Clientbibliothek ist ein Feature, mit dem Sie Sharddatenbanken erstellen und verwalten können.  Weitere Informationen finden Sie unter [Erste Schritte mit Tools für elastische Datenbanken](elastic-scale-get-started.md).
* [Split-Merge-Tool für elastische Datenbanken:](elastic-scale-overview-split-and-merge.md)Zum Verschieben von Daten zwischen Sharddatenbanken. Dieses Tool ist nützlich zum Verschieben von Daten aus einer mehrinstanzenfähigen Datenbank in eine Datenbank mit einzelnem Mandanten (oder umgekehrt). Siehe [Tutorial zum Split-Merge-Tool für elastische Datenbanken](elastic-scale-configure-deploy-split-and-merge.md).
* [Aufträge für die elastische Datenbank](elastic-jobs-overview.md): Verwalten Sie eine große Anzahl von Datenbanken in Azure SQL-Datenbank mithilfe von Aufträgen. Führen Sie mithilfe von Aufträgen ganz einfach administrative Vorgänge aus, z.B. Schemaänderungen, die Verwaltung von Anmeldeinformationen, Verweisdatenupdates, die Leistungsdatensammlung oder die Mandanten(Kunden)-Telemetrieerfassung.
* [Abfrage für elastische Datenbanken](elastic-query-overview.md) (Vorschau): Mit diesem Feature können Sie eine Transact-SQL-Abfrage über mehrere Datenbanken hinweg ausführen. Dies ermöglicht eine Verknüpfung mit Berichtstools wie Excel, Power BI, Tableau usw.
* [Elastische Transaktionen](elastic-transactions-overview.md): Mit diesem Feature können Sie Transaktionen über mehrere Datenbanken hinweg ausführen. Elastische Datenbanktransaktionen stehen für .NET-Anwendungen über ADO.NET zur Verfügung und lassen sich mithilfe der Klassen vom Typ [System.Transaction](/dotnet/api/system.transactions)in die vertraute Programmierumgebung integrieren.

Die folgende Grafik zeigt eine Architektur mit den **Features für elastische Datenbanken** im Kontext einer Sammlung von Datenbanken.

Die Datenbankfarbe symbolisiert Schemas. Datenbanken mit der gleichen Farbe verwenden die gleichen Schemas.

1. Eine Reihe von **SQL-Datenbanken** wird von Azure unter Verwendung einer Shardingarchitektur gehostet.
2. Die **Clientbibliothek für elastische Datenbanken** dient zum Verwalten einer Shardgruppe.
3. Eine Teilmenge der Datenbanken wird einem **Pool für elastische Datenbanken** zugewiesen. (Siehe [Was ist ein Pool?](elastic-pool-overview.md)).
4. Ein **Auftrag für elastische Datenbanken** wird geplant ausgeführt, oder Ad-hoc-T-SQL-Skripts werden für alle Datenbanken ausgeführt.
5. Mit dem **Split-Merge-Tool** werden Daten zwischen Shards verschoben.
6. Mit der **Abfrage für elastische Datenbanken** können Sie eine Abfrage für alle Datenbanken in der Shardgruppe erstellen.
7. Mit **elastischen Transaktionen** können Sie Transaktionen ausführen, die sich über mehrere Datenbanken erstrecken. 

![Tools für elastische Datenbanken][1]

## <a name="why-use-the-tools"></a>Gründe für die Verwendung der Tools

Flexibilität und Skalierbarkeit für Cloudanwendungen lassen sich bei virtuellen Computern und Blob Storage durch einfaches Hinzufügen oder Entfernen von Einheiten oder durch Erhöhen der Leistung erreichen. Bei der zustandsbehafteten Datenverarbeitung in relationalen Datenbanken ist es jedoch eine Herausforderung geblieben. Herausforderungen treten in folgenden Szenarios auf:

* Vergrößern und Verkleinern der Kapazität für den relationalen Datenbankteil der Workload.
* Verwalten von Hotspots, die Auswirkungen auf eine bestimmte Teilmenge von Daten haben können – z.B. auf aktive Endkunden (Mandanten).

In der Vergangenheit wurden Szenarien wie diese durch eine Investition in größere Server zur Unterstützung der Anwendung gelöst. Diese Option ist in der Cloud jedoch beschränkt, wo für die gesamte Verarbeitung vordefinierte Standardhardware verwendet wird. Stattdessen stellt die Verteilung von Daten und ihrer Verarbeitung auf mehrere identisch strukturierte Datenbanken (die als „Sharding“ bezeichnete horizontale Hochskalierung) eine Alternative zur herkömmlichen Hochskalierung im Hinblick auf Kosten und Elastizität dar.

## <a name="horizontal-and-vertical-scaling"></a>Horizontale und vertikale Skalierung

Die folgende Abbildung zeigt die horizontalen und vertikalen Skalierungsdimensionen. Hierbei handelt es sich um die grundlegenden Skalierungsmöglichkeiten für elastische Datenbanken.

![Horizontale und vertikale Skalierung][2]

Horizontale Skalierung meint das Hinzufügen oder Entfernen von Datenbanken zur Anpassung der Kapazität oder der allgemeinen Leistung. Sharding ist eine gängige Methode für die horizontale Skalierung, bei der Daten in einer Sammlung von identisch strukturierten Datenbanken partitioniert werden.  

Vertikale Skalierung bezeichnet das Erhöhen oder Verringern der Computegröße einer einzelnen Datenbank. Dies wird auch als „Hochskalieren“ bezeichnet.

Die meisten Datenbankanwendungen für Clouds verwenden eine Kombination aus diesen beiden Ansätzen. Eine SaaS-Anwendung (Software as a Service) kann z.B. die horizontale Skalierung für Bereitstellungen an neue Endkunden nutzen und die vertikale Skalierung, damit die Ressourcen für die Datenbanken der einzelnen Endkunden bei entsprechender Workload vergrößert oder verkleinert werden können.

* Die horizontale Skalierung wird mithilfe der [Clientbibliothek für elastische Datenbanken](elastic-database-client-library.md)verwaltet.
* Bei der vertikalen Skalierung wird mithilfe von Azure PowerShell-Cmdlets die Dienstebene geändert, oder es werden Datenbanken einem Pool für elastische Datenbanken zugewiesen.

## <a name="sharding"></a>Sharding (Horizontales Partitionieren)

Mit *Sharding* (horizontalem Partitionieren) werden große Mengen identisch strukturierter Daten auf mehrere unabhängige Datenbanken verteilt. Sharding ist besonders bei Cloudentwicklern beliebt, die SaaS-Angebote (Software as a Service) für Endbenutzer oder Unternehmen erstellen. Diese Endbenutzer werden häufig als „Mandanten“ bezeichnet. Sharding kann aus verschiedenen Gründen erforderlich sein:  

* Die Gesamtdatenmenge ist so groß, dass sie aufgrund einschlägiger Beschränkungen nicht in einer einzelnen Datenbank gespeichert werden kann.
* Der Transaktionsdurchsatz der Workload überschreitet die Fähigkeiten einer einzelnen Datenbank.
* Mandanten erfordern u. U. eine physische Trennung von anderen Mandaten, sodass für jeden Mandanten eine eigene Datenbank erforderlich ist.
* Verschiedene Abschnitte einer Datenbank müssen sich aufgrund von Compliance- oder Leistungsanforderungen oder aus geopolitischen Gründen an verschiedenen geografischen Standorten befinden.

In anderen Szenarien, z. B. beim Erfassen von Daten von verteilten Geräten, kann das Sharding eingesetzt werden, um eine Gruppe von Datenbanken über einen Zeitraum verteilt zu füllen. Beispielsweise kann jedem Wochentag oder jeder Woche eine eigene Datenbank zugeordnet werden. In diesem Fall kann der Sharding-Schlüssel eine Ganzzahl sein, die das Datum darstellt (und in allen Zeilen der partitionierten Tabellen vorhanden ist). Abfragen zum Abruf von Informationen für einen Datumsbereich müssen von der Anwendung an die Teilmenge von Datenbanken, welche den fraglichen Bereich abdecken, weitergeleitet werden.

Das Sharding funktioniert am besten, wenn jede Transaktion in einer Anwendung auf einen einzelnen Wert eines Sharding-Schlüssels begrenzt werden kann. Damit wird sichergestellt, dass alle Transaktionen für eine bestimmte Datenbank spezifisch sind.

## <a name="multi-tenant-and-single-tenant"></a>Mehrere Mandanten und einzelner Mandant

Einige Anwendungen verwenden den einfachsten Ansatz und erstellen für jeden Mandanten eine eigene Datenbank. Dieser Ansatz ist das **Shardingmuster mit einzelnen Mandanten** , das Isolation, Sicherungs-/Wiederherstellungsfunktion und Ressourcenskalierung auf Ebene einzelner Mandanten bietet. Beim Sharding mit einzelnen Mandanten ist jede Datenbank mit einem bestimmten Mandanten-ID-Wert (oder Kundenschlüsselwert) verknüpft, wobei der Schlüssel jedoch nicht immer in den Daten vorhanden sein muss. Es ist Aufgabe der Anwendung, die einzelnen Anforderungen an die entsprechende Datenbank weiterzuleiten – dies wird durch die Clientbibliothek vereinfacht.

![Einzelinstanzen und Mehrinstanzen][4]

In anderen Szenarien werden mehrere Mandanten in Datenbanken zusammengefasst, statt sie in getrennten Datenbanken zu isolieren. Bei diesem Muster handelt es sich um ein typisches **Shardingszenario mit mehreren Mandanten** , dem die Tatsache zugrunde liegen kann, dass eine Anwendung eine große Anzahl von kleinen Mandanten verwalten muss. Beim Sharding mit mehreren Mandanten sollen die Zeilen in den Datenbanktabellen einen Schlüssel enthalten, der die Mandanten-ID oder den Sharding-Schlüssel eindeutig identifiziert. Auch hier ist es Aufgabe der Anwendungsschicht, die Anforderung eines Mandanten an die entsprechende Datenbank weiterzuleiten. Dieser Vorgang kann durch die Clientbibliothek für elastische Datenbanken unterstützt werden. Darüber hinaus kann die Sicherheit auf Zeilenebene zum Filtern der Zeilen verwendet werden, auf die jeder Mandant Zugriff hat. Weitere Informationen hierzu finden Sie unter [Mehrinstanzenfähige Anwendungen mit Tools für elastische Datenbanken und zeilenbasierter Sicherheit](saas-tenancy-elastic-tools-multi-tenant-row-level-security.md). Beim Sharding mit mehreren Mandanten ist möglicherweise eine Neuverteilung der Daten zwischen den Datenbanken erforderlich, die mit dem Split-Merge-Tool für elastische Datenbanken durchgeführt wird. Weitere Informationen zu Entwurfsmustern für SaaS-Anwendungen, für die Pools für elastische Datenbanken verwendet werden, finden Sie unter [Entwurfsmuster für SaaS-Anwendungen mit mehreren Mandanten und Azure SQL-Datenbank](saas-tenancy-app-design-patterns.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Verschieben von Daten aus Datenbanken mit mehreren Mandanten in Datenbanken mit Einzelmandant
Wenn Sie eine SaaS-Anwendung erstellen, wird Interessenten üblicherweise eine Testversion der Software bereitgestellt. In diesem Fall ist es aus Kostengründen sinnvoll, für die Daten eine Datenbank mit mehreren Mandanten zu verwenden. Wenn aus dem Interessenten allerdings ein Kunde wird, ist eine Datenbank mit nur einem Mandanten die bessere Wahl, da diese eine bessere Leistung bietet. Falls der Kunde während des Testzeitraums Daten erstellt hat, können Sie diese mithilfe des [Split-Merge-Tools](elastic-scale-overview-split-and-merge.md) aus der Datenbank mit mehreren Mandanten in die neue Einzelmandantendatenbank verschieben.

## <a name="next-steps"></a>Nächste Schritte
Unter [Erste Schritte mit Tools für elastische Datenbanken](elastic-scale-get-started.md)finden Sie eine Beispiel-App zur Veranschaulichung der Clientbibliothek.

Informationen zum Umwandeln von vorhandenen Datenbanken für die Verwendung der Tools finden Sie unter [Migrieren vorhandener Datenbanken für die Aufskalierung](elastic-convert-to-use-elastic-tools.md).

Einzelheiten zum Pool für elastische Datenbanken finden Sie unter [Überlegungen zum Preis und zur Leistung von Pools für elastische Datenbanken](elastic-pool-overview.md). Informationen zum Erstellen eines neuen Pools finden Sie unter [Erstellen und Verwalten eines Pools für elastische Datenbanken über das Azure-Portal](elastic-pool-manage.md).  

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/elastic-scale-introduction/tools.png
[2]:./media/elastic-scale-introduction/h_versus_vert.png
[3]:./media/elastic-scale-introduction/overview.png
[4]:./media/elastic-scale-introduction/single_v_multi_tenant.png