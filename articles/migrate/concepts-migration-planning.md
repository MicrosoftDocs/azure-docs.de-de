---
title: Erstellen eines Migrationsplans mit Azure Migrate
description: Enthält Anleitungen zum Erstellen eines Migrationsplans mit Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: ef916e0e8b32c96382a731d4a307e2b2a98ba1ea
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753857"
---
# <a name="build-migration-plan-with-azure-migrate"></a>Erstellen eines Migrationsplans mit Azure Migrate

Befolgen Sie die Anweisungen in diesem Artikel, um mithilfe von [Azure Migrate](migrate-services-overview.md) einen Plan für die Migration zu Azure zu erstellen. 

## <a name="define-cloud-migration-goals"></a>Definieren von Cloudmigrationszielen

Bevor Sie beginnen, sollten Sie sich Ihre [Beweggründe](/azure/cloud-adoption-framework/strategy/motivations) für die Migration zur Cloud bewusst machen und diese bewerten, um ein erfolgreiches Ergebnis zu erzielen. Wie auf der Seite zum [Cloud Adoption Framework](/azure/cloud-adoption-framework) erläutert, sind mehrere Auslöser und Ergebnisse möglich.   

**Geschäftsereignis** | **Ergebnis der Migration**
--- | ---
Ausstieg aus Rechenzentrum | Kosten 
Fusionen, Übernahme oder Veräußerung | Reduzierung der Anbieterkomplexität/technischen Komplexität
Reduzierung der Kapitalkosten | Optimierung interner Vorgänge
Ende des Supports für unternehmenskritische Technologien | Erhöhen der Unternehmensflexibilität
Reaktion auf Änderungen bezüglich der Einhaltung gesetzlicher Bestimmungen | Vorbereitung auf neue technische Funktionen und Möglichkeiten
Neue Anforderungen an die Datenhoheit | Skalierung entsprechend den Marktanforderungen
Reduzierung von Unterbrechungen und Verbesserungen an der IT-Stabilität | Skalierung entsprechend den geografischen Anforderungen

Das Ermitteln Ihrer Beweggründe hilft Ihnen beim Festlegen Ihrer strategischen Migrationsziele. Der nächste Schritt ist die Ermittlung und Planung eines Migrationspfads, der auf Ihre Workloads zugeschnitten ist. Das Tool [Azure Migrate- Serverbewertung](migrate-services-overview.md#azure-migrate-server-assessment-tool) hilft Ihnen bei der Bewertung lokaler Workloads und stellt Anleitungen und Tools zur Verfügung, die Sie bei der Migration unterstützen.

## <a name="understand-your-digital-estate"></a>Verstehen Ihrer digitalen Ressourcen

Ermitteln Sie zunächst Ihre lokale Infrastruktur, Anwendungen und Abhängigkeiten. So können Sie Workloads für die Migration zu Azure identifizieren und optimierte Kostenprojektionen erfassen. Mit dem Serverbewertungstool können Sie die derzeit verwendeten Workloads, die Abhängigkeiten zwischen Workloads und die Optimierung der Workloads ermitteln.

### <a name="workloads-in-use"></a>Derzeit verwendete Workloads

Azure Migrate verwendet eine einfache Azure Migrate-Appliance, um Ihre lokalen VMware-VMs, Hyper-V-VMs, anderen VMs und physischen Servern ohne Agent zu ermitteln. Bei der kontinuierlichen Ermittlung werden Computerkonfigurationsinformationen, Leistungsmetadaten und Anwendungsdaten erfasst. Von lokalen Computern erfasst die Appliance Folgendes: 

- Computer- und Datenträgermetadaten sowie NIC-Metadaten

- Installierte Anwendungen, Rollen und Features

- Leistungsdaten, einschließlich CPU- und Arbeitsspeicherauslastung, Datenträger-IOPS und Durchsatz

Nachdem Sie die Daten gesammelt haben, können Sie die Anwendungsbestandsliste exportieren, um nach auf Ihren Computern ausgeführten Apps und SQL Server-Instanzen zu suchen. Sie können Azure Migrate: Datenbankbewertungstool verwenden, um die SQL Server-Bereitschaft nachzuvollziehen.

 ![Anwendungsbestand im Portal](./media/concepts-migration-planning/application-inventory-portal.png)

 ![Export des Anwendungsbestands](./media/concepts-migration-planning/application-inventory-export.png)

Zusammen mit den durch das Serverbewertungstool entdeckten Daten können Sie die Daten aus Ihrer Konfigurationsverwaltungsdatenbank (CMDB) zum Erstellen einer Ansicht Ihrer Server- und Datenbankbestände verwenden. Zudem können Sie nachvollziehen, wie Ihre Server über Geschäftseinheiten, Anwendungsbesitzer, geografische Regionen usw. verteilt sind. Dies hilft bei der Entscheidung, welche Workloads für die Migration priorisiert werden sollen. 

### <a name="dependencies-between-workloads"></a>Abhängigkeiten zwischen Workloads

Nach der Serverermittlung können Sie [Abhängigkeiten analysieren](concepts-dependency-visualization.md), um serverübergreifende Abhängigkeiten und Optimierungsstrategien für die Verlagerung voneinander abhängiger Server zu Azure zu visualisieren und zu identifizieren. Die Visualisierung hilft zu verstehen, ob bestimmte Computer verwendet werden oder ob sie stillgelegt werden können, anstatt migriert zu werden.  Die Analyse von Abhängigkeiten trägt dazu bei, dass nichts übersehen wird. Außerdem werden unerwartete Ausfälle während der Migration vermieden. Nachdem die Anwendungsinventarisierung und Abhängigkeitsanalyse abgeschlossen sind, können Sie Servergruppen mit hoher Vertrauenswürdigkeit erstellen und beginnen, diese zu bewerten.

 ![Zuordnung der Abhängigkeiten](./media/concepts-migration-planning/expand-client-group.png)

### <a name="optimization-and-sizing"></a>Optimierung und Dimensionierung

Dank der Flexibilität von Azure können Sie die Größe Ihrer Cloudkapazität im Laufe der Zeit ändern. Die Migration bietet Ihnen eine Gelegenheit, die Ihren Servern zugewiesenen CPU- und Arbeitsspeicherressourcen zu optimieren. Durch die Bewertung auf von Ihnen ermittelten Servern können Sie den Leistungsverlauf Ihrer Workload besser nachvollziehen. Dies ist entscheidend für die richtige Dimensionierung von Azure-VM-SKUs sowie für Datenträgerempfehlungen in Azure.

## <a name="assess-migration-readiness"></a>Bewerten der Bereitschaft zur Migration


### <a name="readinesssuitability-analysis"></a>Bereitschafts-/Eignungsanalyse

Sie können den Bewertungsbericht exportieren und nach diesen Kategorien filtern, um die Azure-Bereitschaft nachvollziehen zu können:

- **Bereit für Azure**: Computer können ohne Änderungen zu Azure migriert werden. 
- **Bedingt bereit für Azure:** Computer können zu Azure migriert werden, müssen entsprechend des in der Bewertung bereitgestellten Wartungsleitfadens jedoch geringfügig geändert werden.
- **Nicht bereit für Azure**: Computer können ohne Änderungen nicht zu Azure migriert werden. Vor der Migration müssen die Probleme entsprechend des Wartungsleitfadens behoben werden. 
- **Bereitschaft unbekannt**: Azure Migrate kann die Bereitschaft des Computers aufgrund unzureichender Metadaten nicht ermitteln.

Mithilfe von Datenbankbewertungen können Sie die Bereitschaft Ihrer SQL Server-Datenbestände für die Migration zu Azure SQL-Datenbank oder Instanzen von Azure SQL Managed Instance beurteilen. Die Bewertung zeigt für jede Ihrer SQL Server-Instanzen den prozentualen Status der Bereitschaft für die Migration an. Außerdem werden für jede Instanz das empfohlene Ziel in Azure, potenzielle Migrationshindernisse, die Anzahl von Breaking Changes, die Bereitschaft für Azure SQL-Datenbank/Azure SQL-VM und der Kompatibilitätsgrad angezeigt. Sie können sich eingehender mit den Auswirkungen der Migrationshindernisse und Empfehlungen zu deren Behebung beschäftigen.

 ![Datenbankbewertungen](./media/concepts-migration-planning/database-assessment-portal.png)

### <a name="sizing-recommendations"></a>Größenempfehlungen

Nachdem ein Computer als bereit für Azure markiert wurde, erhalten Sie bei der Serverbewertung Größenempfehlungen zur Identifizierung der Azure-VM-SKU und des Datenträgertyps für Ihre Computer. Sie können sich die Größenempfehlungen basierend auf dem Leistungsverlauf (zum Optimieren von Ressourcen bei der Migration) oder basierend auf den lokalen Computereinstellungen, ohne Leistungsverlauf, anzeigen lassen. Bei einer Datenbankbewertung können Sie Empfehlungen zu Datenbank-SKU, Tarif und Computeebene einsehen.  

### <a name="get-compute-costs"></a>Ermitteln der Computekosten

Mithilfe der leistungsbasierten Dimensionierungsoption in Azure Migrate können Sie die richtige Größe für VMs festlegen. Diese sollte als bewährte Methode zum Optimieren von Workloads in Azure verwendet werden. Neben der Dimensionierung gibt es noch einige weitere Optionen, mit denen Sie Kosten für Azure sparen können: 

- **Reservierte Instanzen**: Mit [reservierten Instanzen (RI)](https://azure.microsoft.com/pricing/reserved-vm-instances/) können Sie die Kosten im Vergleich zur [nutzungsbasierten Bezahlung](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) erheblich verringern.
- **Azure Hybrid Benefit** (Azure-Hybridvorteil): Mit dem [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) können Sie lokale Windows Server-Lizenzen mit aktiver Software Assurance oder Linux-Abonnements zu Azure übertragen und mit der Option für reservierte Instanzen kombinieren.
- **Enterprise Agreement**: Mit Azure [Enterprise Agreements (EA)](../cost-management-billing/manage/ea-portal-agreements.md) können Sie von Rabatten für Azure-Abonnements und -Dienste profitieren.
- **Angebote**: Es gibt mehrere [Azure-Angebote](https://azure.microsoft.com/support/legal/offer-details/). Beispiele sind die Angebote [Dev/Test Pay-As-You-Go](https://azure.microsoft.com/pricing/dev-test/) oder [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/), bei denen Sie von niedrigeren Tarifen für Dev/Test-VMs profitieren.
- **VM-Uptime:** Sie können überprüfen, an wie vielen Tagen pro Monat und an wie vielen Stunden pro Tag Azure-VMs ausgeführt werden. Durch das Herunterfahren von Computern bei Nichtverwendung können Kosten gespart werden (gilt nicht für RIs).
- **Zielregion**: Sie können Bewertungen in verschiedenen Regionen erstellen, um herauszufinden, ob die Migration zu einer bestimmten Region kostengünstiger sein könnte. 

### <a name="visualize-data"></a>Visualisieren von Daten

Serverbewertungsberichte (mit Informationen zur Azure-Bereitschaft und der monatlichen Kostenverteilung) können im Portal angezeigt werden. Sie können die Bewertung auch exportieren und Ihren Migrationsplan mit zusätzlichen Visualisierungen erweitern. Sie können mehrere Bewertungen mit verschiedenen Kombinationen von Eigenschaften erstellen und sich dann für die Eigenschaften entscheiden, die sich für Ihr Unternehmen am besten eignen.  

 ![Bewertungsübersicht](./media/concepts-migration-planning/assessment-summary.png)

### <a name="evaluate-gapsblockers"></a>Auswerten von Lücken/Hindernissen

Identifizieren Sie beim Ermitteln der zu migrierenden Apps und Workloads Downtimeeinschränkungen für diese, und suchen Sie nach operativen Abhängigkeiten zwischen Ihren Apps und der zugrunde liegenden Infrastruktur. Diese Analyse hilft Ihnen bei der Planung von Migrationen, die Ihre Wiederherstellungszeit-Zielsetzung (RTO) erfüllen und gewährleisten, dass nur minimal bis gar keine Daten verloren gehen. Vor der Migration empfiehlt es sich, alle Kompatibilitätsprobleme oder nicht unterstützten Features zu überprüfen und zu beheben, die die Servermigration/SQL-Datenbank-Migration blockieren könnten. Der Serverbewertungsbericht und die Datenbankbewertung von Azure Migrate können Sie dabei unterstützen. 

### <a name="prioritize-workloads"></a>Priorisieren von Workloads

Nachdem Sie Informationen zu Ihrem Bestand gesammelt haben, können Sie ermitteln, welche Apps und Workloads Sie zuerst migrieren sollten. Entwickeln Sie einen „Anwenden und Lernen“-Ansatz, um Apps systematisch und kontrollierbar zu migrieren, sodass Sie alle Mängel beseitigen können, bevor Sie mit einer umfassenden Migration beginnen.

Für die Priorisierung der Migrationsreihenfolge können Sie strategische Faktoren wie Komplexität, Migrationszeit, geschäftliche Dringlichkeit, Überlegungen zu Produktion/Nichtproduktion, Compliance- und Sicherheitsanforderungen, Anwendungswissen usw. berücksichtigen. 

Einige Empfehlungen:

- **Schnelle Erfolge priorisieren:** Ermitteln Sie mithilfe der Bewertungsberichte einfache Ziele, beispielsweise die Server und Datenbanken, die vollständig bereit sind und nur minimalen Aufwand für die Migration zu Azure erfordern. In der Tabelle sind einige Möglichkeiten dazu zusammengefasst.

    **State** | **Aktion**
    --- | ---
    **Für Azure bereite VMs** | Exportieren Sie den Bewertungsbericht, und filtern Sie nach allen Computern mit dem Status *Bereit für Azure*. Dies könnte die erste Gruppe von Computern sein, die Sie mithilfe des [Servermigrationstools von Azure Migrate](migrate-services-overview.md#azure-migrate-server-migration-tool) zu Azure migrieren.
    **Betriebssysteme am Ende des Supports** | Exportieren Sie den Bewertungsbericht, und filtern Sie nach allen Computern, die unter Windows Server 2008 R2/Windows Server 2008 ausgeführt werden. Für diese Betriebssysteme endet der Support, und nur Azure bietet Ihnen drei Jahre lang kostenlose Sicherheitsupdates, wenn Sie diese zu Azure migrieren. Wenn Sie den Azure-Hybridvorteil kombinieren und RIs verwenden, könnten Sie noch viel mehr einsparen.
    **SQL Server-Migration** | Verwenden Sie die Empfehlungen aus der Datenbankbewertung, um Datenbanken, die für Azure SQL-Datenbank bereit sind, mithilfe des Datenbankmigrationstools von Azure Migrate zu migrieren. Migrieren Sie die für die Azure SQL-VM bereiten Datenbanken mithilfe des Servermigrationstools von Azure Migrate.
    **Software am Ende des Supports** | Exportieren Sie Ihren Anwendungsbestand, und filtern Sie nach Software/Erweiterungen, die womöglich das Ende des Supports erreichen. Priorisieren Sie diese Anwendungen für die Migration.
    **Gering ausgelastete Computer** | Exportieren Sie den Bewertungsbericht, und filtern Sie nach Computern mit geringer CPU-Auslastung (%) und Arbeitsspeicherauslastung (%).  Migrieren Sie diese zu einer richtig dimensionierten Azure-VM, und sparen Sie Kosten für gering ausgelastete Ressourcen.
    **Überlastete Computer** | Exportieren Sie den Bewertungsbericht, und filtern Sie nach Computern mit hoher CPU-Auslastung (%) und Arbeitsspeicherauslastung (%).  Lösen Sie Kapazitätseinschränkungen, verhindern Sie den Ausfall von überlasteten Computern, und steigern Sie die Leistung, indem Sie diese Computer zu Azure migrieren. Verwenden Sie in Azure die Funktionen zur automatischen Skalierung, um die Anforderungen zu erfüllen.<br/><br/> Analysieren Sie Bewertungsberichte, um Speichereinschränkungen zu untersuchen. Analysieren Sie den Datenträger-IOPS und den Durchsatz sowie den empfohlenen Datenträgertyp.

- **Fangen Sie klein an, und steigern Sie sich dann:** Beginnen Sie mit dem Verschieben von Apps und Workloads, die ein minimales Risiko bergen und weniger komplex sind, um Vertrauen in Ihre Migrationsstrategie zu schaffen. Analysieren Sie Azure Migrate-Bewertungsempfehlungen in Verbindung mit Ihrem CMDB-Repository, um Dev/Test-Workloads zu suchen und zu migrieren, die sich als Kandidaten für Pilotmigrationen eignen könnten. Feedback und Erkenntnisse aus Pilotmigrationen können beim Start der Migration von Produktionsworkloads hilfreich sein.  
- **Compliance:** Azure verwaltet mit seinem breiten und detaillierten Angebot das größte Complianceportfolio der gesamten Branche. Verwenden Sie Complianceanforderungen zum Priorisieren von Migrationen, sodass Apps und Workloads die nationalen, regionalen und branchenspezifischen Standards und Gesetze einhalten. Dies gilt insbesondere für Organisationen, die mit unternehmenskritischen Prozessen arbeiten, vertrauliche Informationen aufbewahren oder in streng regulierten Branchen angesiedelt sind. Bei solchen Organisationen gilt es viele Standards und gesetzliche Regelungen zu berücksichtigen. Da sich diese oft ändern können, ist es schwer, den Überblick zu behalten.  

## <a name="finalize-the-migration-plan"></a>Fertigstellen des Migrationsplans

Beachten Sie vor dem Fertigstellen Ihres Migrationsplans unbedingt andere potenzielle Hindernisse wie die folgenden, und beseitigen Sie diese: 

- **Netzwerkanforderungen**: Bewerten Sie Einschränkungen bezüglich Netzwerkbandbreite und -latenz, die zu unvorhergesehenen Verzögerungen führen und die Replikationsgeschwindigkeit der Migration stören könnten.
- **Tests/Anpassungen nach der Migration:** Planen Sie einen zeitlichen Puffer ein, um Leistungs- und Benutzerakzeptanztests für migrierte Apps durchzuführen oder Apps nach der Migration zu konfigurieren bzw. anzupassen, beispielsweise durch Aktualisieren von Datenbank-Verbindungszeichenfolgen, Konfigurieren von Webservern, Durchführen von Übernahmen/Bereinigungen usw.
- **Berechtigungen**: Überprüfen Sie empfohlene Azure-Berechtigungen und für die Migration erforderliche Rollen und Berechtigungen für den Server-/Datenbankzugriff.
- **Aus- und Weiterbildung:** Bereiten Sie Ihre Organisation auf die digitale Transformation vor. Eine solide Schulungsgrundlage ist wichtig für eine erfolgreiche Organisationsänderung. Informieren Sie sich über kostenlose Aus- und Weiterbildungsmöglichkeiten unter [Microsoft Learn](/learn/azure/?ocid=CM_Discovery_Checklist_PDF), beispielsweise Kurse zu Azure-Grundlagen, zu Lösungsarchitekturen und zur Sicherheit. Ermutigen Sie Ihr Team, sich über  [Azure-Zertifizierungen](https://www.microsoft.com/learning/certification-overview.aspx?ocid=CM_Discovery_Checklist_PDF) zu informieren.  
- **Unterstützung bei der Implementierung:** Lassen Sie sich bei Bedarf bei Ihrer Implementierung unterstützen. Viele Organisationen holen sich externe Unterstützung für ihre Cloudmigration. Ziehen Sie für einen schnellen und zuverlässigen Wechsel zu Azure mit persönlicher Unterstützung einen  [Azure Expert-MSP](https://www.microsoft.com/solution-providers/search?cacheId=9c2fed4f-f9e2-42fb-8966-4c565f08f11e&ocid=CM_Discovery_Checklist_PDF) oder  [Microsoft FastTrack for Azure](https://azure.microsoft.com/programs/azure-fasttrack/?ocid=CM_Discovery_Checklist_PDF) in Erwägung.  


Erstellen Sie einen effektiven Cloudmigrationsplan, der detaillierte Informationen über die zu migrierenden Apps, über die Verfügbarkeit der Apps und Datenbanken, Downtimeeinschränkungen sowie über Migrationsmeilensteine enthält. Im Plan ist die Zeit für den Datenkopiervorgang inklusive einem realistischen Puffer für Tests und Übernahmeaktivitäten nach der Migration festgehalten. 

Ein Plan für Tests nach der Migration sollte Funktions-, Integrations-, Sicherheits- und Leistungstests sowie Anwendungsfälle umfassen, um sicherzustellen, dass die migrierten Apps wie erwartet funktionieren und alle Datenbankobjekte und Datenbeziehungen erfolgreich in die Cloud übertragen wurden.  

Erstellen Sie eine Migrationsroadmap, und deklarieren Sie ein Wartungsfenster, um Ihre Apps und Datenbanken mit minimalen oder ganz ohne Ausfallzeiten zu migrieren und die potenziellen betrieblichen und geschäftlichen Auswirkungen während der Migration einzuschränken.  

## <a name="migrate"></a>Migrieren

Es wird empfohlen, dass Sie eine Testmigration in Azure Migrate durchführen, bevor Sie eine vollständige Migration starten. Mithilfe einer Testmigration können Sie die benötigte Zeit besser einschätzen und Ihren Migrationsplan anpassen. Sie verschafft Ihnen die Möglichkeit, potenzielle Probleme zu erkennen und vor der vollständigen Migration zu beheben.

Wenn Sie für die Migration bereit sind, verwenden Sie das Servermigrationstool von Azure Migrate und Azure Database Migration Service (DMS) für eine nahtlose und integrierte Migration mit durchgehender Nachverfolgung.

- Mit dem Servermigrationstool können Sie lokale VMs und Server oder VMs aus anderen privaten oder öffentlichen Clouds (einschließlich AWS und GCP) nahezu ohne Downtime migrieren.
- Bei Azure DMS handelt es sich um einen vollständig verwalteten Dienst, der die nahtlose Migration von mehreren Datenbankquellen zu Azure-Datenplattformen mit minimaler Downtime ermöglicht.  

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich den Artikel zur  [Cloudmigration](/azure/architecture/cloud-adoption/getting-started/migrate)  des Frameworks für die Cloudeinführung (Cloud Adoption Framework) an.
- Verschaffen Sie sich einen [kurzen Überblick](migrate-services-overview.md) über Azure Migrate, und sehen Sie sich ein [Video zu den ersten Schritten](https://youtu.be/wFfq3YPxYHE) an.
- Informieren Sie sich weiter über das Bewerten von VMs für die Migration zu [virtuellen Azure-Computern](concepts-assessment-calculation.md).
