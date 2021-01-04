---
title: Muster für mehrinstanzenfähige SaaS-Anwendungen
description: Erfahren Sie mehr über die Anforderungen und allgemeinen Datenarchitekturmuster von mehrinstanzenfähigen SaaS-Datenbankanwendungen (Software-as-a-Service), die in einer Azure-Cloudumgebung ausgeführt werden.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 8a13c641d50a68d9661b4aa6caf8effb82d53dd7
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92793227"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Mandantenmuster für mehrinstanzenfähige SaaS-Datenbanken
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Artikel werden die verschiedenen Mandantenmodelle beschrieben, die für eine mehrinstanzenfähige SaaS-Anwendung verfügbar sind.

Wenn Sie eine mehrinstanzenfähige SaaS-Anwendung entwerfen, wählen Sie nach sorgfältiger Überlegung das Mandantenmodell aus, das den Anforderungen Ihrer Anwendung am besten gerecht wird.  Ein Mandantenmodell legt fest, wie die Daten eines jeden Mandanten dem Speicher zugeordnet werden.  Die Wahl des Mandantenmodells wirkt sich auf den Entwurf und die Verwaltung der Anwendung aus.  Ein späterer Wechsel zu einem anderen Modell kann sich mitunter als kostspielig erweisen.

## <a name="a-saas-concepts-and-terminology"></a>A. SaaS-Konzepte und -Terminologie

Im Software-as-a-Service-Modell (SaaS) verkauft Ihr Unternehmen keine *Lizenzen* für Ihre Software. Stattdessen zahlt jeder Kunde eine Leihgebühr an Ihr Unternehmen, sodass jeder Kunde ein *Mandant* Ihres Unternehmens wird.

Als Gegenleistung für die Leihgebühr erhält jeder Mandant Zugriff auf Ihre SaaS-Anwendungskomponenten, und seine Daten werden im SaaS-System gespeichert.

Der Begriff *Mandantenmodell* bezieht sich auf die Art und Weise, in der die gespeicherten Daten des Mandanten organisiert sind:

- *Einzelinstanzenfähigkeit:* &nbsp; Jede Datenbank speichert Daten eines einzelnen Mandanten.
- *Mehrinstanzenfähigkeit:* &nbsp; Jede Datenbank speichert Daten mehrerer Mandanten (mit Mechanismen zum Datenschutz).
- Hybride Mandantenmodelle sind ebenfalls verfügbar.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. Vorgehensweise zum Auswählen des geeigneten Mandantenmodells

Das Mandantenmodell wirkt sich in der Regel nicht auf die Funktionsweise einer Anwendung aus, hat jedoch sehr wahrscheinlich Auswirkungen auf andere Aspekte der gesamten Lösung.  Für die Bewertung der einzelnen Modelle werden folgende Kriterien herangezogen:

- **Skalierbarkeit:**
    - Anzahl der Mandanten
    - Speicher pro Mandant
    - Aggregierter Speicher
    - Workload

- **Mandantenisolation:** &nbsp; Datenisolation und Leistung (Auswirkungen der Workload eines Mandanten auf andere)

- **Kosten pro Mandant:** &nbsp; Kosten für Datenbank

- **Komplexität der Entwicklung:**
    - Änderungen am Schema
    - Änderungen an Abfragen (für das Muster erforderlich)

- **Komplexität des Betriebs:**
    - Überwachen und Verwalten der Leistung
    - Verwalten des Schemas
    - Wiederherstellen eines Mandanten
    - Notfallwiederherstellung

- **Anpassbarkeit:** &nbsp; Einfache Durchführung von speziell für Mandanten oder Mandantenklassen geltenden Schemaanpassungen

Die Informationen zu Mandanten beziehen sich hauptsächlich auf die *Datenschicht* .  Im Folgenden sollten wir jedoch einen kurzen Blick auf die *Anwendungsschicht* werfen.  Die Anwendungsschicht ist als monolithische Einheit zu betrachten.  Wenn Sie die Anwendung in viele kleine Komponenten unterteilen, kann dies die Wahl Ihres Mandantenmodells ändern.  Einige Komponenten können je nach Mandant sowie verwendeter Speichertechnologie oder Plattform unterschiedlich behandelt werden.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. Eigenständige Einzelinstanz-App mit einer Einzelinstanzdatenbank

#### <a name="application-level-isolation"></a>Isolation der Anwendungsebene

In diesem Modell wird die gesamte Anwendung mehrmals installiert, nämlich einmal pro Mandant.  Da jede Instanz der App eine eigenständige Instanz darstellt, interagiert sie nie mit einer anderen eigenständigen Instanz.  Jede App-Instanz umfasst nur einen Mandanten und erfordert daher nur eine Datenbank.  Die gesamte Datenbank ist somit ausschließlich für den jeweiligen Mandanten bestimmt.

![Entwurf einer eigenständigen App mit einer Einzelinstanzdatenbank][image-standalone-app-st-db-111a]

Jede App-Instanz wird in einer separaten Azure-Ressourcengruppe installiert.  Die Ressourcengruppe kann einem Abonnement zugeordnet sein, das dem Softwarehersteller oder Mandanten gehört.  In beiden Fällen kann der Hersteller die Software für den Mandanten verwalten.  Jede Anwendungsinstanz ist so konfiguriert, dass sie eine Verbindung mit der entsprechenden Datenbank herstellt.

Jede Mandantendatenbank wird als Einzeldatenbank bereitgestellt.  Dieses Modell ermöglicht die umfangreichste Datenbankisolation.  Für die Isolation müssen jeder Datenbank jedoch genügend Ressourcen zugewiesen werden, damit Spitzenlasten verarbeitet werden können.  Hier ist entscheidend, dass Pools für elastische Datenbanken nicht für Datenbanken verwendet werden können, die in verschiedenen Ressourcengruppen oder für verschiedene Abonnements bereitgestellt werden.  Aufgrund dieser Beschränkung zählt dieses Modell einer eigenständigen Einzelinstanz-App hinsichtlich der gesamten Datenbankkosten zur teuersten Lösung.

#### <a name="vendor-management"></a>Herstellerseitige Verwaltung

Der Hersteller kann auf alle Datenbanken in sämtlichen eigenständigen App-Instanzen zugreifen, auch wenn die App-Instanzen in unterschiedlichen Mandantenabonnements installiert sind.  Der Zugriff erfolgt über SQL-Verbindungen.  Mit einem solchen instanzübergreifenden Zugriff kann der Hersteller die Schemaverwaltung und die datenbankübergreifende Abfrage für Berichterstellungs- oder Analysezwecke zentralisieren.  Falls diese Art der zentralen Verwaltung erforderlich ist, muss ein Katalog für die Zuordnung zwischen Mandanten-IDs und Datenbank-URIs bereitgestellt werden.  Azure SQL-Datenbank stellt eine gemeinsam verwendete Shardingbibliothek für die Bereitstellung eines Katalogs bereit.  Die Shardingbibliothek wird offiziell als [Clientbibliothek für elastische Datenbanken][docu-elastic-db-client-library-536r] bezeichnet.

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D: Mehrinstanzenfähige App mit einer Datenbank pro Mandant

Beim nächsten Muster kommt eine mehrinstanzenfähige Anwendung mit vielen Einzelinstanzdatenbanken zum Einsatz.  Für jeden neuen Mandanten wird eine neue Datenbank bereitgestellt.  Die Logikschicht wird vertikal *zentral hochskaliert* , indem weitere Ressourcen pro Knoten hinzugefügt werden.  Alternativ wird die App durch Hinzufügen weiterer Knoten *horizontal erweitert* .  Die Skalierung basiert auf der Workload und hängt von der Anzahl oder dem Umfang der einzelnen Datenbanken ab.

![Entwurf einer mehrinstanzenfähigen App mit einer Datenbank pro Mandant][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Durchführen von Anpassungen für einen Mandanten

Wie beim Muster einer eigenständigen App ermöglicht auch die Verwendung von Einzelinstanzdatenbanken eine umfassende Mandantenisolation.  In jeder App, deren Modell ausschließlich Einzelinstanzdatenbanken angibt, kann das Schema für eine bestimmte Datenbank an den jeweiligen Mandanten angepasst und entsprechend optimiert werden.  Diese Anpassung wirkt sich nicht auf andere Mandanten in der App aus. Eventuell benötigt ein Mandant Daten, die über die standardmäßigen Datenfelder, die alle Mandanten benötigen, hinausgehen.  Des Weiteren kann es sein, dass für das zusätzliche Datenfeld ein Index erforderlich ist.

Bei einer Datenbank pro Mandant lässt sich das Schema für einen oder mehrere einzelne Mandanten problemlos realisieren.  Der Hersteller der Anwendung muss Verfahren entwerfen, um sorgfältig Schemaanpassungen im großen Maßstab zu verwalten.

#### <a name="elastic-pools"></a>Pools für elastische Datenbanken

Wenn Datenbanken in derselben Ressourcengruppe bereitgestellt werden, können sie in Pools für elastische Datenbanken gruppiert werden.  Die Pools bieten eine kosteneffiziente Methode, um Ressourcen auf eine Vielzahl von Datenbanken aufzuteilen.  Eine solche Pooloption ist günstiger, als die einzelnen Datenbanken so groß anzulegen, dass auftretende Verbrauchsspitzen verarbeitet werden können.  Auch wenn Pooldatenbanken gemeinsam auf Ressourcen zugreifen, können sie dennoch einen hohen Grad an Leistungsisolation erzielen.

![Entwurf einer mehrinstanzenfähigen App mit einer Datenbank pro Mandant unter Verwendung eines Pools für elastische Datenbanken][image-mt-app-db-per-tenant-pool-153p]

Azure SQL-Datenbank stellt die Tools zur Verfügung, die erforderlich sind, um die Freigabe zu konfigurieren, zu überwachen und zu verwalten.  Sowohl Leistungsmetriken auf Pool- als auch auf Datenbankebene werden über das Azure-Portal und über Azure Monitor-Protokolle zur Verfügung gestellt.  Die Metriken können umfassenden Einblick in die aggregierte und mandantenspezifische Leistung bieten.  Einzelne Datenbanken können zwischen Pools verschoben werden, um einem bestimmten Mandanten reservierte Ressourcen zur Verfügung zu stellen.  Durch diese Tools können Sie auf kosteneffiziente Weise eine hohe Leistung sicherstellen.

#### <a name="operations-scale-for-database-per-tenant"></a>Skalierung des Betriebs für das Modell mit einer Datenbank pro Mandant

Azure SQL-Datenbank verfügt über viele Verwaltungsfeatures, die für die Verwaltung einer großen Anzahl von Datenbanken ausgelegt sind (z. B. weit über 100.000 Datenbanken).  Aufgrund dieser Eigenschaften erweist sich das Muster mit einer Datenbank pro Mandant als sinnvolle Option.

Nehmen wir beispielsweise an, dass ein System ausschließlich auf einer Datenbank mit 1000 Mandanten basiert.  Die Datenbank kann 20 Indizes enthalten.  Wenn das System nun auf 1000 Einzelinstanzdatenbanken umgestellt wird, steigt die Zahl der Indizes folglich auf 20.000.  Standardmäßig sind die automatischen Indizierungsfunktionen in Azure SQL-Datenbank im Rahmen der [automatischen Optimierung][docu-sql-db-automatic-tuning-771a] aktiviert.  Bei der automatischen Indizierung werden alle 20.000 Indizes sowie die entsprechenden laufenden Optimierungen im Hinblick auf Erstellungs- und Reduzierungsvorgänge für Sie verwaltet.  Diese automatisierten Aktionen finden in einer einzelnen Datenbank statt und werden nicht durch ähnliche Aktionen in anderen Datenbanken koordiniert oder eingeschränkt.  Die automatische Indizierung behandelt Indizes in einer ausgelasteten Datenbank anders als in einer weniger stark ausgelasteten Datenbank.  Diese Art der Indexverwaltungsanpassung wäre bei manueller Ausführung einer derart umfangreichen Verwaltungsaufgabe im Hinblick auf eine Datenbank pro Mandant nicht praktikabel.

Zu anderen Verwaltungsfunktionen, die sich gute skalieren lassen, zählen Folgende:

- Integrierte Sicherungen
- Hochverfügbarkeit.
- Datenträgerverschlüsselung
- Leistungstelemetriedaten

#### <a name="automation"></a>Automation

Für die Verwaltungsvorgänge können Skripts erstellt werden, und sie können über ein [DevOps][http-visual-studio-devops-485m]-Modell zur Verfügung gestellt werden.  Die Vorgänge können sogar automatisiert und in der Anwendung sichtbar gemacht werden.

Beispielsweise könnten Sie die Wiederherstellung eines einzelnen Mandanten zu einem früheren Zeitpunkt automatisieren.  Bei der Wiederherstellung muss lediglich eine Einzelinstanzdatenbank wiederhergestellt werden, in der der Mandant gespeichert wird.  Diese Wiederherstellung hat keine Auswirkungen auf andere Mandanten, was wiederum zeigt, dass Verwaltungsvorgänge in den einzelnen Mandanten auf hochgradig granularer Ebene stattfinden.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. Mehrinstanzenfähige App mit mehrinstanzenfähigen Datenbanken

Ein weiteres verfügbares Muster besteht darin, viele Mandanten in einer mehrinstanzenfähigen Datenbank zu speichern.  Die Anwendungsinstanz kann beliebig viele mehrinstanzenfähige Datenbanken umfassen.  Das Schema einer mehrinstanzenfähigen Datenbank muss eine oder mehrere Spalten für die Mandanten-ID enthalten, damit die Daten eines beliebigen Mandanten selektiv abgerufen werden können.  Zudem sind für das Schema eventuell einige Tabellen oder Spalten erforderlich, die nur von einer Teilmenge von Mandanten verwendet werden.  Statische Codes und Referenzdaten werden jedoch nur einmal gespeichert und von allen Mandanten gemeinsam genutzt.

#### <a name="tenant-isolation-is-sacrificed"></a>Eingeschränkte Mandantenisolation

*Daten:* &nbsp; Eine mehrinstanzenfähige Datenbank schränkt zwangsläufig die Mandantenisolation ein.  Die Daten mehrerer Mandanten werden zusammen in einer Datenbank gespeichert.  Achten Sie bei der Entwicklung darauf, dass bei Abfragen niemals Daten von mehr als einem Mandanten offengelegt werden.  SQL-Datenbank unterstützt die [Sicherheit auf Zeilenebene][docu-sql-svr-db-row-level-security-947w], wodurch erzwungen werden kann, dass die von einer Abfrage zurückgegebenen Daten auf einen einzelnen Mandanten beschränkt werden.

*Verarbeitung:* &nbsp; Eine mehrinstanzenfähige Datenbank teilt Compute- und Speicherressourcen auf alle seine Mandanten auf.  Um einen ordnungsgemäßen Betrieb sicherzustellen, kann die gesamte Datenbank überwacht werden.  Allerdings weist das Azure-System keine integrierte Option auf, um die Nutzung dieser Ressourcen durch einen einzelnen Mandanten zu überwachen oder zu verwalten.  Daher besteht bei einer mehrinstanzenfähigen Datenbank ein höheres Risiko eines sogenannten „Noisy Neighbor“-Effekts, bei dem die Workload eines übermäßig aktiven Mandanten die Leistung anderer Mandanten in derselben Datenbank beeinträchtigt.  Durch zusätzliche Überwachung auf Anwendungsebene kann die Leistung auf Mandantenebene überwacht werden.

#### <a name="lower-cost"></a>Kostensenkung

Im Allgemeinen weisen mehrinstanzenfähige Datenbanken die niedrigsten Kosten pro Mandant auf.  Die Ressourcenkosten für eine Einzeldatenbank sind niedriger als die für einen gleichwertig großen Pool für elastische Datenbanken.  Darüber hinaus können Millionen von Mandanten in Szenarien, in denen Mandanten nur einen begrenzten Speicher benötigen, eventuell in einer einzigen Datenbank gespeichert werden.  Es gibt keine Pools für elastische Datenbanken, die Millionen von Datenbanken enthalten können.  Eine Lösung mit 1000 Pools – wobei ein Pool 1000 Datenbanken enthält – kann jedoch eine Größenordnung in Millionenhöhe erreichen und damit eine umständliche Verwaltung nach sich ziehen.

Im Folgenden werden zwei Varianten eines mehrinstanzenfähigen Datenbankmodells erläutert, wobei das mehrinstanzenfähige Modell mit Sharding das flexibelste Modell mit der höchsten Skalierbarkeit darstellt.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>F. Mehrinstanzenfähige App mit einer Einzelinstanzdatenbank

Das einfachste Muster einer mehrinstanzenfähigen Datenbank verwendet eine einzelne Datenbank, um Daten für alle Mandanten zu hosten.  Bei einer steigenden Anzahl von Mandanten muss die Datenbank mit weiteren Speicher- und Computeressourcen zentral hochskaliert werden.  Eine solche Hochskalierung könnte Abhilfe leisten, wobei es jedoch immer eine finale Begrenzung für die Skalierung gibt.  Allerdings wird die Datenbankverwaltung lange vor Erreichen dieser Begrenzung zunehmend umständlicher.

Bei mehrinstanzenfähigen Datenbanken ist die Implementierung von Verwaltungsvorgängen, die sich auf einzelne Mandanten konzentrieren, komplexer.  Mit steigender Größenordnung könnte dies dazu führen, dass diese Vorgänge unzumutbar langsam werden.  Ein Beispiel ist die Point-in-Time-Wiederherstellung von Daten für nur einen Mandanten.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>G. Mehrinstanzenfähige App mit mehrinstanzenfähigen Datenbanken mit Sharding

Die meisten SaaS-Anwendungen greifen auf die Daten von jeweils nur einem Mandanten zu.  Bei diesem Zugriffsmuster können Mandantendaten auf mehrere Datenbanken oder Shards aufgeteilt werden, wobei alle Daten eines beliebigen Mandanten in einem Shard enthalten sind.  In Kombination mit einem Muster einer mehrinstanzenfähigen Datenbank ermöglicht ein Modell in Shards eine nahezu unbegrenzte Skalierbarkeit.

![Entwurf einer mehrinstanzenfähigen App mit mehrinstanzenfähigen Datenbanken mit Sharding][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Verwalten von Shards

Sharding erhöht die Komplexität sowohl im Hinblick auf den Entwurf als auch auf den Betrieb.  Für die Verwaltung der Zuordnung zwischen Mandanten und Datenbanken ist ein Katalog erforderlich.  Darüber hinaus sind Verwaltungsverfahren erforderlich, um die Anzahl der Shards und Mandanten zu verwalten.  Beispielsweise müssen Verfahren entworfen werden, um Shards hinzuzufügen und zu entfernen und Mandantendaten zwischen Shards zu verschieben.  Eine Möglichkeit zur Skalierung besteht darin, einen neuen Shard hinzuzufügen und ihn mit neuen Mandanten zu befüllen.  In anderen Fällen kann ein dicht befüllter Shard in zwei weniger dicht befüllte Shards aufgeteilt werden.  Nachdem mehrere Mandanten verschoben oder außer Kraft gesetzt wurden, können Sie wenig befüllte Shards zusammenführen.  Die Zusammenführung würde zu einer kosteneffizienteren Ressourcenverwendung führen.  Mandanten können auch zwischen den Shards verschoben werden, um für ausgeglichene Workloads zu sorgen.

Die SQL-Datenbank bietet ein Tool zum Aufteilen bzw. Zusammenführen, das in Verbindung mit der Shardingbibliothek und der Katalogdatenbank verwendet werden kann.  Die bereitgestellte App kann Shards aufteilen bzw. zusammenführen und Mandantendaten zwischen Shards verschieben.  Die App verwaltet während dieser Vorgänge auch den Katalog, wobei die betroffenen Mandanten vor der Verschiebung als offline gekennzeichnet werden.  Nach der Verschiebung aktualisiert die App den Katalog wieder mit der neuen Zuordnung und markiert den Mandanten wieder als online.

#### <a name="smaller-databases-more-easily-managed"></a>Einfachere Verwaltung kleinerer Datenbanken

Durch die Verteilung der Mandanten auf mehrere Datenbanken führt der Einsatz der mehrinstanzenfähigen Lösung mit Shards zu kleineren Datenbanken, die sich einfacher verwalten lassen.  Wenn Sie beispielsweise einen bestimmten Mandanten zu einem früheren Zeitpunkt wiederherstellen möchten, müssen Sie nun nicht mehr eine größere Datenbank mit allen Mandanten wiederherstellen, sondern nur noch eine einzelne kleinere Datenbank aus einer Sicherung. Die Datenbankgröße und die Anzahl der Mandanten pro Datenbank können so gewählt werden, das ein ausgewogenes Verhältnis zwischen Workload und Verwaltungsaufwand besteht.

#### <a name="tenant-identifier-in-the-schema"></a>Mandanten-ID im Schema

Abhängig von der verwendeten Shardingmethode können in Bezug auf das Datenbankschema zusätzliche Einschränkungen vorgenommen werden.  Die SQL-Datenbankanwendung zum Aufteilen bzw. Zusammenführen erfordert, dass das Schema den Shardingschlüssel – üblicherweise die Mandanten-ID – enthält.  Die Mandanten-ID ist das führende Element des Primärschlüssels aller Shardingtabellen.  Mit der Mandanten-ID kann die Anwendung zum Aufteilen bzw. Zusammenführen, die einem bestimmten Mandanten zugeordneten Daten schnell lokalisieren und verschieben.

#### <a name="elastic-pool-for-shards"></a>Pool für elastische Datenbanken für Shards

Mehrinstanzenfähige Datenbanken mit Shards können in Pools für elastische Datenbanken platziert werden.  Die Verwaltung von vielen Einzelinstanzdatenbanken in einem Pool ist in der Regel genauso kosteneffizient wie die Verwaltung vieler Mandanten in mehreren mehrinstanzenfähigen Datenbanken.  Mehrinstanzenfähige Datenbanken erweisen sich vor allem dann als nützlich, wenn eine große Anzahl von relativ inaktiven Mandanten vorliegt.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>H. Hybrides mehrinstanzenfähiges Datenbankmodell mit Sharding

Im hybriden Modell enthalten alle Datenbanken im Schema die Mandanten-ID.  Alle Datenbanken können mehr als einen Mandanten speichern und in Shards aufgeteilt werden.  Hinsichtlich des Schemas handelt es sich also bei allen Datenbanken um mehrinstanzenfähige Datenbanken.  In der Praxis enthalten einige dieser Datenbanken jedoch nur einen Mandanten.  Nichtsdestotrotz hat die Anzahl der in einer Datenbank gespeicherten Mandanten keinen Einfluss auf das Datenbankschema.

#### <a name="move-tenants-around"></a>Wechseln von Mandanten

Sie können einen bestimmten Mandanten jederzeit in eine eigene mehrinstanzenfähige Datenbank verschieben.  Sie können dies jederzeit rückgängig machen und den Mandanten wieder zu einer Datenbank mit mehreren Mandanten wechseln.  Sie können einem Mandanten bei der Bereitstellung einer neuen Datenbank auch eine neue Einzelinstanzdatenbank zuordnen.

Das hybride Modell erweist sich vor allem dann als hilfreich, wenn große Unterschiede zwischen den Ressourcenanforderungen der identifizierbarer Mandantengruppen herrschen.  Nehmen wir zum Beispiel an, dass Mandanten mit einer kostenlosen Testversion nicht dieselbe hohe Leistungsebene garantiert wird wie Mandanten mit einem Abonnement.  Die Richtlinie könnte vorschreiben, dass Mandanten im kostenlosen Testzeitraum in einer mehrinstanzenfähigen Datenbank, die von allen Mandanten mit der kostenlosen Testversion gemeinsam genutzt wird, gespeichert werden sollen.  Wenn ein Mandant mit der kostenlosen Testversion ein Abonnement für die Dienstebene „Basic“ erwirbt, kann der Mandant in eine andere mehrinstanzenfähige Datenbank verschoben werden, die möglicherweise weniger Mandanten aufweist.  Ein Abonnent, der für die Dienstebene „Premium“ bezahlt, kann in seine eigene neue Datenbank mit nur einem Mandanten verschoben werden.

#### <a name="pools"></a>Pools

In diesem hybriden Modell können Einzelinstanzdatenbanken für Mandanten mit Abonnement in Ressourcenpools platziert werden, um die Datenbankkosten pro Mandant zu senken.  Dies wird über das Modell mit einer Datenbank pro Mandant erreicht.

## <a name="i-tenancy-models-compared"></a>I. Vergleich von Mandantenmodellen

In der folgenden Tabelle werden die Unterschiede zwischen den wichtigsten Mandantenmodellen erläutert:

| Messung | Eigenständige App | Eine Datenbank pro Mandant | Mehrinstanzenfähige Datenbank mit Shards |
| :---------- | :------------- | :------------------ | :------------------- |
| Skalieren | Medium<br />1 – mehrere 100 | Sehr hoch<br />1 – mehrere 100.000 | Unbegrenzt<br />1 – mehrere 1.000.000 |
| Mandantenisolation | Sehr hoch | High | Niedrig, ausgenommen für einen (in einer MT-Datenbank einzelnen) Einzelmandanten. |
| Datenbankkosten pro Mandant | Hoch, für Spitzen dimensioniert | Niedrig, unter Verwendung von Pools | Am niedrigsten, für kleine Mandanten in MT-Datenbanken |
| Leistungsüberwachung und -verwaltung | Nur pro Mandant | Aggregiert und pro Mandant | Aggregiert, jedoch nur für das Modell pro Mandant für Einzeldatenbanken. |
| Komplexität der Entwicklung | Niedrig | Niedrig | Mittel, aufgrund von Sharding |
| Komplexität des Betriebs | Niedrig bis hoch Einzeln einfach, aber im großen Maßstab komplex | Niedrig bis mittel Muster als Abhilfemaßnahme für die Komplexität bei Skalierungen | Niedrig bis hoch Die Verwaltung einzelner Mandanten ist komplex. |
| &nbsp; ||||

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen und Untersuchen einer mehrinstanzenfähigen Wingtip-Anwendung mit dem SaaS-Modell mit einer Datenbank pro Mandant – Azure SQL-Datenbank][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Willkommen bei der Beispielmandanten-SaaS-App Wingtip Tickets für Azure SQL-Datenbank][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: /sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]:elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: saas-dbpertenant-get-started-deploy.md
[docu-sql-db-automatic-tuning-771a]:automatic-tuning-overview.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Entwurf einer eigenständigen App mit einer Einzelinstanzdatenbank"

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Entwurf einer mehrinstanzenfähigen App mit einer Datenbank pro Mandant"

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Entwurf einer mehrinstanzenfähigen App mit einer Datenbank pro Mandant unter Verwendung eines Pools für elastische Datenbanken"

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Entwurf einer mehrinstanzenfähigen App mit mehrinstanzenfähigen Datenbanken mit Sharding"