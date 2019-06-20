---
title: Indiziertes Video, Azure SaaS SQL-App | Microsoft-Dokumentation
description: Dieser Artikel indiziert verschiedene Zeitpunkte in unserem 81-minütigen Video zum App-Design für SaaS DB-Mandanten von der Ignite-Konferenz vom 11. Oktober 2017. Sie können direkt zu dem Teil wechseln, der Sie interessiert. Es werden mindestens drei Muster beschrieben. Es werden Azure-Features beschrieben, die die Entwicklung und Verwaltung vereinfachen.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: bbe220780a3c21e7bfb15d0568904af4ed47f765
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61486949"
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>Indiziertes und kommentiertes Video für mehrinstanzenfähige SaaS-App mit Azure SQL-Datenbank

Dieser Artikel ist ein kommentierter Index zu den Zeitpunkten eines 81-minütigen Videos über SaaS-Mandantenmodelle oder -muster. Durch diesen Artikel können Sie im Video direkt zu dem Teil wechseln, der Sie interessiert. Das Video erklärt die wichtigsten Designoptionen für eine mehrinstanzenfähige Datenbankanwendung unter Azure SQL-Datenbank. Das Video enthält Demos, exemplarische Vorgehensweisen zu Verwaltungscode und zuweilen mehr Details, die durch Erfahrungen geprägt werden, als in unserer schriftlichen Dokumentation.

Das Video ergänzt die Informationen in unserer schriftlichen Dokumentation, die Sie hier finden: 
- *Konzeptionell:* Mandantenmuster für mehrinstanzenfähige SaaS-Datenbanken[saas-concept-design-patterns-563e]
- *Tutorials:* [Die Wingtip Tickets SaaS-Anwendung][saas-how-welcome-wingtip-app-679t]

Das Video und die Artikel beschreiben die vielen Phasen der Erstellung einer mehrinstanzenfähigen Anwendung unter Azure SQL-Datenbank in der Cloud. Spezielle Features von Azure SQL-Datenbank erleichtern die Entwicklung und Implementierung mehrinstanzenfähiger Anwendungen, die einfacher zu verwalten und zuverlässig leistungsfähig sind.

Unsere schriftliche Dokumentation wird regelmäßig aktualisiert. Das Video wird nicht bearbeitet oder aktualisiert, sodass irgendwann mehr Details veraltet sein können.



## <a name="sequence-of-38-time-indexed-screenshots"></a>Sequenz von 38 zeitindizierten Screenshots

In diesem Abschnitt wird die zeitliche Position für 38 Diskussionen während des 81-minütigen Videos indiziert. Jeder Zeitindex wird mit einem Screenshot aus dem Video und manchmal mit zusätzlichen Informationen versehen.

Jeder Zeitindex hat das Format *hh:mm:ss*. Beispielsweise beginnt die zweite indizierte zeitliche Position, die mit **Sitzungsziele** bezeichnet ist, ungefähr an der Position **0:03:11**.


### <a name="compact-links-to-video-indexed-time-locations"></a>Kompakte Links zu indizierten zeitlichen Positionen im Video

Die folgenden Titel sind Links zu den entsprechenden kommentierten Abschnitten, die später in diesem Artikel zu finden sind:

- [1. **(Start)**  Folie zur Begrüßung, 0:00:03](#anchor-image-wtip-min00001)
- [2. Sitzungsziele, 0:03:11](#anchor-image-wtip-min00311)
- [3. Agenda, 0:04:17](#anchor-image-wtip-min00417)
- [4. Mehrinstanzenfähige Web-App, 0:05:05](#anchor-image-wtip-min00505)
- [5. App-Webformular in Aktion, 0:05:55](#anchor-image-wtip-min00555)
- [6. Kosten pro Mandant (Staffelung, Isolation, Wiederherstellung), 0:09:31](#anchor-image-wtip-min00931)
- [7. Datenbankmodelle für mehrere Mandanten: Vor- und Nachteile, 0:11:59](#anchor-image-wtip-min01159)
- [8. Hybridmodell kombiniert Vorteile von MT/ST, 0:13:01](#anchor-image-wtip-min01301)
- [9. Einzelner Mandant oder mehrere Mandanten: Vor- und Nachteile, 0:16:44](#anchor-image-wtip-min01644)
- [10. Pools sind bei unvorhersehbaren Workloads kosteneffektiv, 0:19:36](#anchor-image-wtip-min01936)
- [11. Demo für eine Datenbank pro Mandant und Hybridmodell von ST/MT, 0:20:08](#anchor-image-wtip-min02008)
- [12. Live-App-Formular für Dojo, 0:20:29](#anchor-image-wtip-min02029)
- [13. MYOB und keine DBA in Sicht, 0:28:54](#anchor-image-wtip-min02854)
- [14. Nutzungsbeispiel für elastischen MYOB-Pool, 0:29:40](#anchor-image-wtip-min02940)
- [15. Lernen von MYOB und anderen ISVs, 0:31:36](#anchor-image-wtip-min03136)
- [16. Musterzusammenstellung zu einem E2E SaaS-Szenario, 0:43:15 Uhr](#anchor-image-wtip-min04315)
- [17. Mehrinstanzenfähige Canonical-Hybrid-SaaS-App, 0:47:33](#anchor-image-wtip-min04733)
- [18. Wingtip SaaS-Beispiel-App, 0:48:10](#anchor-image-wtip-min04810)
- [19. In den Tutorials untersuchte Szenarien und Muster, 0:49:10](#anchor-image-wtip-min04910)
- [20. Demo zu Tutorials und zum GitHub-Repository, 0:50:18](#anchor-image-wtip-min05018)
- [21. GitHub-Repository Microsoft/WingtipSaaS, 0:50:38](#anchor-image-wtip-min05038)
- [22. Untersuchen der Muster, 0:56:20](#anchor-image-wtip-min05620)
- [23. Bereitstellung von Mandanten und Onboarding, 0:57:44](#anchor-image-wtip-min05744)
- [24. Bereitstellung von Mandanten und Anwendungsverbindung, 0:58:58](#anchor-image-wtip-min05858)
- [25. Demo zu Verwaltungsskripts zum Bereitstellen eines einzelnen Mandanten, 0:59:43](#anchor-image-wtip-min05943)
- [26. PowerShell zur Bereitstellung und Katalogisierung, 1:00:02](#anchor-image-wtip-min10002)
- [27. T-SQL SELECT * FROM TenantsExtended, 1:03:30](#anchor-image-wtip-min10330)
- [28. Verwalten unvorhersehbarer Workloads von Mandanten, 1:04:36](#anchor-image-wtip-min10436)
- [29. Überwachung des Pools für elastische Datenbanken, 1:06:39](#anchor-image-wtip-min10639)
- [30. Lastgenerierung und Leistungsüberwachung, 1:09:42](#anchor-image-wtip-min10942)
- [31. Bedarfsorientierte Schemaverwaltung, 1:10:33](#anchor-image-wtip-min11033)
- [32. Verteilte Abfrage über Mandantendatenbanken hinweg, 1:12:21](#anchor-image-wtip-min11221)
- [33. Demo zur Ticketgenerierung, 1:12:32](#anchor-image-wtip-min11232)
- [34. SSMS – Ad-hoc-Analysen, 1:12:46](#anchor-image-wtip-min11246)
- [35. Extrahieren von Mandantendaten in SQL DW, 1:16:32](#anchor-image-wtip-min11632)
- [36. Diagramm zur täglichen Verteilung von Umsätzen, 1:16:48](#anchor-image-wtip-min11648)
- [37. Zusammenfassung und Handlungsaufforderung, 1:19:52](#anchor-image-wtip-min11952)
- [38. Ressourcen für weitere Informationen, 1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>Kommentierte zeitliche Indexpositionen im Video

Wenn Sie auf einen Screenshot klicken, gelangen Sie zur genauen zeitlichen Position im Video.


&nbsp;<a name="anchor-image-wtip-min00001"/>
#### <a name="1-start-welcome-slide-00001"></a>1. *(Start)* Folie zur Begrüßung, 0:00:01

*Lernen von MYOB: Entwurfsmuster für SaaS-Anwendungen unter Azure SQL-Datenbank – BRK3120*

[![Folie zur Begrüßung][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- Titel: Lernen von MYOB: Entwurfsmuster für SaaS-Anwendungen unter Azure SQL-Datenbank
- Bill.Gibson@microsoft.com
- Principal Program Manager, Azure SQL-Datenbank
- Microsoft Ignite-Sitzung BRK3120, Orlando, Florida, USA, 11. Oktober 2017


&nbsp;<a name="anchor-image-wtip-min00311"/>
#### <a name="2-session-objectives-00153"></a>2. Sitzungsziele, 0:01:53
[![Sitzungsziele][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- Alternative Modelle für mehrinstanzenfähige Apps, mit Vor- und Nachteilen.
- SaaS-Muster zur Reduzierung der Kosten für Entwicklung, Verwaltung und Ressourcen.
- Eine Beispiel-App sowie Skripts.
- PaaS-Features und SaaS-Muster verwandeln die SQL-Datenbank in eine hochgradig skalierbare, kosteneffiziente Datenplattform für mehrinstanzenfähige SaaS-Lösungen.


&nbsp;<a name="anchor-image-wtip-min00417"/>
#### <a name="3-agenda-00409"></a>3. Agenda, 0:04:09
[![Agenda][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp;<a name="anchor-image-wtip-min00505"/>
#### <a name="4-multi-tenant-web-app-00500"></a>4. Mehrinstanzenfähige Web-App, 0:05:00
[![Wingtip SaaS-App: Mehrinstanzenfähige Web-App][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp;<a name="anchor-image-wtip-min00555"/>
#### <a name="5-app-web-form-in-action-00539"></a>5. App-Webformular in Aktion, 0:05:39
[![App-Webformular in Aktion][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp;<a name="anchor-image-wtip-min00931"/>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6. Kosten pro Mandant (Staffelung, Isolation, Wiederherstellung), 0:06:58
[![Kosten pro Mandant (Staffelung, Isolation, Wiederherstellung)][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp;<a name="anchor-image-wtip-min01159"/>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7. Datenbankmodelle für mehrere Mandanten: Vor- und Nachteile, 0:09:52
[![Datenbankmodelle für mehrere Mandanten: Vor- und Nachteile][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp;<a name="anchor-image-wtip-min01301"/>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8. Hybridmodell kombiniert Vorteile von MT/ST, 0:12:29
[![Hybridmodell kombiniert Vorteile von MT/ST][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp;<a name="anchor-image-wtip-min01644"/>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9. Einzelner Mandant oder mehrere Mandanten: Vor- und Nachteile, 0:13:11
[![Einzelner Mandant oder mehrere Mandanten: Vor- und Nachteile][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp;<a name="anchor-image-wtip-min01936"/>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10. Pools sind bei unvorhersehbaren Workloads kosteneffektiv, 0:17:49
[![Pools sind bei unvorhersehbaren Workloads kosteneffektiv][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp;<a name="anchor-image-wtip-min02008"/>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11. Demo für eine Datenbank pro Mandant und Hybridmodell von ST/MT, 0:19:59
[![Demo für eine Datenbank pro Mandant und Hybridmodell von ST/MT][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp;<a name="anchor-image-wtip-min02029"/>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12. Live-App-Formular für Dojo, 0:20:10
[![Live-App-Formular für Dojo][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp;<a name="anchor-image-wtip-min02854"/>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13. MYOB und keine DBA in Sicht, 0:25:06
[![MYOB und keine DBA in Sicht][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp;<a name="anchor-image-wtip-min02940"/>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14. Nutzungsbeispiel für elastischen MYOB-Pool, 0:29:30
[![Nutzungsbeispiel für elastischen MYOB-Pool][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp;<a name="anchor-image-wtip-min03136"/>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15. Lernen von MYOB und anderen ISVs, 0:31:25
[![Lernen von MYOB und anderen ISVs][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp;<a name="anchor-image-wtip-min04315"/>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16. Musterzusammenstellung zu einem E2E SaaS-Szenario, 0:31:42
[![Musterzusammenstellung zu einem E2E SaaS-Szenario][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp;<a name="anchor-image-wtip-min04733"/>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17. Mehrinstanzenfähige Canonical-Hybrid-SaaS-App, 0:46:04
[![Mehrinstanzenfähige Canonical-Hybrid-SaaS-App][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp;<a name="anchor-image-wtip-min04810"/>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18. Wingtip SaaS-Beispiel-App, 0:48:01
[![Wingtip SaaS-Beispiel-App][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp;<a name="anchor-image-wtip-min04910"/>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19. In den Tutorials untersuchte Szenarien und Muster, 0:49:00
[![In den Tutorials untersuchte Szenarien und Muster][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp;<a name="anchor-image-wtip-min05018"/>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20. Demo zu Tutorials und zum GitHub-Repository, 0:50:12
[![Demo zu Tutorials und zum GitHub-Repository][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp;<a name="anchor-image-wtip-min05038"/>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21. GitHub-Repository Microsoft/WingtipSaaS, 0:50:32
[![GitHub-Repository Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp;<a name="anchor-image-wtip-min05620"/>
#### <a name="22-exploring-the-patterns-05615"></a>22. Untersuchen der Muster, 0:56:15
[![Untersuchen der Muster][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp;<a name="anchor-image-wtip-min05744"/>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23. Bereitstellung von Mandanten und Onboarding, 0:56:19
[![Bereitstellung von Mandanten und Onboarding][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp;<a name="anchor-image-wtip-min05858"/>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24. Bereitstellung von Mandanten und Anwendungsverbindung, 0:57:52
[![Bereitstellung von Mandanten und Anwendungsverbindung][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp;<a name="anchor-image-wtip-min05943"/>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25. Demo zu Verwaltungsskripts zum Bereitstellen eines einzelnen Mandanten, 0:59:36
[![Demo zu Verwaltungsskripts zum Bereitstellen eines einzelnen Mandanten][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp;<a name="anchor-image-wtip-min10002"/>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26. PowerShell zur Bereitstellung und Katalogisierung, 0:59:56
[![PowerShell zur Bereitstellung und Katalogisierung][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp;<a name="anchor-image-wtip-min10330"/>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27. T-SQL SELECT * FROM TenantsExtended, 1:03:25
[![T-SQL SELECT * FROM TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp;<a name="anchor-image-wtip-min10436"/>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28. Verwalten unvorhersehbarer Workloads von Mandanten, 1:03:34
[![Verwalten unvorhersehbarer Workloads von Mandanten][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp;<a name="anchor-image-wtip-min10639"/>
#### <a name="29-elastic-pool-monitoring-10632"></a>29. Überwachung des Pools für elastische Datenbanken, 1:06:32
[![Überwachung des Pools für elastische Datenbanken][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp;<a name="anchor-image-wtip-min10942"/>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30. Lastgenerierung und Leistungsüberwachung, 1:09:37
[![Lastgenerierung und Leistungsüberwachung][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp;<a name="anchor-image-wtip-min11033"/>
#### <a name="31-schema-management-at-scale-10940"></a>31. Bedarfsorientierte Schemaverwaltung, 1:09:40
[![Bedarfsorientierte Schemaverwaltung][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp;<a name="anchor-image-wtip-min11221"/>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32. Verteilte Abfrage über Mandantendatenbanken hinweg, 1:11:18
[![Verteilte Abfrage über Mandantendatenbanken hinweg][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp;<a name="anchor-image-wtip-min11232"/>
#### <a name="33-demo-of-ticket-generation-11228"></a>33. Demo zur Ticketgenerierung, 1:12:28
[![Demo zur Ticketgenerierung][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp;<a name="anchor-image-wtip-min11246"/>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34. SSMS – Ad-hoc-Analysen, 1:12:35
[![SSMS – Ad-hoc-Analysen][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp;<a name="anchor-image-wtip-min11632"/>
#### <a name="35-extract-tenant-data-into-sql-dw-11546"></a>35. Extrahieren von Mandantendaten in SQL DW, 1:15:46
[![Extrahieren von Mandantendaten in SQL DW][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp;<a name="anchor-image-wtip-min11648"/>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36. Diagramm zur täglichen Verteilung von Umsätzen, 1:16:38
[![Diagramm zur täglichen Verteilung von Umsätzen][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp;<a name="anchor-image-wtip-min11952"/>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37. Zusammenfassung und Handlungsaufforderung, 1:17:43
[![Zusammenfassung und Handlungsaufforderung][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp;<a name="anchor-image-wtip-min12042"/>
#### <a name="38-resources-for-more-information-12035"></a>38. Ressourcen für weitere Informationen, 1:20:35
[![Ressourcen für weitere Informationen][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [Blogbeitrag, 22. Mai 2017][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *Konzeptionell:* Mandantenmuster für mehrinstanzenfähige SaaS-Datenbanken[saas-concept-design-patterns-563e]

- *Tutorials:* [Die Wingtip Tickets SaaS-Anwendung][saas-how-welcome-wingtip-app-679t]

- GitHub-Repositorys für Varianten der Wingtip Tickets SaaS-Mandantenanwendung:
    - [GitHub-Repository für – Modell „eigenständige Anwendung“][github-wingtip-standaloneapp].
    - [GitHub-Repository für – Modell „DB pro Mandant“][github-wingtip-dbpertenant].
    - [GitHub-Repository für – Modell „DB für mehrere Mandanten“][github-wingtip-multitenantdb].





## <a name="next-steps"></a>Nächste Schritte

- [Erster Tutorialartikel][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "Folie zur Begrüßung"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "Sitzungsziele"

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "Agenda"

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "Wingtip SaaS-App: Mehrinstanzenfähige Web-App"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "App-Webformular in Aktion"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "Kosten pro Mandant (Staffelung, Isolation, Wiederherstellung)"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "Datenbankmodelle für mehrere Mandanten: Vor- und Nachteile"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "Hybridmodell kombiniert Vorteile von MT/ST"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "Einzelner Mandant oder mehrere Mandanten: Vor- und Nachteile"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "Pools sind bei unvorhersehbaren Workloads kosteneffektiv"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "Demo für eine Datenbank pro Mandant und Hybridmodell von ST/MT"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "Live-App-Formular für Dojo"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "MYOB und keine DBA in Sicht"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "Nutzungsbeispiel für elastischen MYOB-Pool"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "Lernen von MYOB und anderen ISVs"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "Musterzusammenstellung zu einem E2E SaaS-Szenario"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "Mehrinstanzenfähige Canonical-Hybrid-SaaS-App"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "Wingtip SaaS-Beispiel-App"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "In den Tutorials untersuchte Szenarien und Muster"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "Demo zu Tutorials und zum GitHub-Repository"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "GitHub-Repository Microsoft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "Untersuchen der Muster"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "Bereitstellung von Mandanten und Onboarding"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "Bereitstellung von Mandanten und Anwendungsverbindung"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "Demo zu Verwaltungsskripts zum Bereitstellen eines einzelnen Mandanten"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "PowerShell zur Bereitstellung und Katalogisierung"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "T-SQL SELECT * FROM TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "Verwalten unvorhersehbarer Workloads von Mandanten"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "Überwachung des Pools für elastische Datenbanken"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "Lastgenerierung und Leistungsüberwachung"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "Bedarfsorientierte Schemaverwaltung"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "Verteilte Abfrage über Mandantendatenbanken hinweg"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "Demo zur Ticketgenerierung"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "SSMS – Ad-hoc-Analysen"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "Extrahieren von Mandantendaten in SQL DW"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "Diagramm zur täglichen Verteilung von Umsätzen"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "Zusammenfassung und Handlungsaufforderung"

[image-wtip-min12042-resources-more-info]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min12042-resources-blog-github-tutorials-get-started.png "Ressourcen für weitere Informationen"




<!-- Article link reference IDs. -->

[saas-concept-design-patterns-563e]: saas-tenancy-app-design-patterns.md

[saas-how-welcome-wingtip-app-679t]: saas-tenancy-welcome-wingtip-tickets-app.md


[video-on-youtube-com-478y]: https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1

[video-on-channel9-479c]: https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3120


[resource-blog-saas-patterns-app-dev-sql-db-768h]: https://azure.microsoft.com/blog/saas-patterns-accelerate-saas-application-development-on-sql-database/


[github-wingtip-standaloneapp]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp/

[github-wingtip-dbpertenant]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/

[github-wingtip-multitenantdb]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/

