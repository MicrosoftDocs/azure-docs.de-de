---
title: Ressourceneinschränkungen für Azure SQL-Datenbank – verwaltete Instanz | Microsoft-Dokumentation
description: Dieser Artikel bietet eine Übersicht über die Ressourceneinschränkungen für verwaltete Azure SQL-Datenbank-Instanzen.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
manager: craigg
ms.date: 05/22/2019
ms.openlocfilehash: 7ff8405bba39e274c4f9f0cbacb7c295564c877e
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66303214"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Übersicht über Ressourceneinschränkungen für verwaltete Azure SQL-Datenbank-Instanzen

Dieser Artikel bietet eine Übersicht zu den Ressourceneinschränkungen für verwaltete Azure SQL-Datenbank-Instanzen und erläutert, wie Sie eine Anforderung zur Erhöhung dieser Grenzwerte erstellen.

> [!NOTE]
> Unterschiede bei den unterstützten Funktionen und T-SQL-Anweisungen sind unter [Funktionsunterschiede](sql-database-features.md) und [Unterstützung von T-SQL-Anweisungen](sql-database-managed-instance-transact-sql-information.md) zu finden.

## <a name="instance-level-resource-limits"></a>Ressourceneinschränkungen auf Instanzebene

Eine verwaltete Instanz weist Merkmale und Ressourceneinschränkungen auf, die von der zugrunde liegende Infrastruktur und Architektur abhängen. Die Grenzwerte hängen von der Hardwaregeneration und der Dienstebene ab.

### <a name="hardware-generation-characteristics"></a>Merkmale der Hardwaregeneration

Eine verwaltete Azure SQL-Datenbank-Instanz kann auf zwei Hardwaregenerationen bereitgestellt werden: Gen4 und Gen5. Hardwaregenerationen weisen unterschiedliche Merkmale auf, die in der folgenden Tabelle beschrieben sind.

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Hardware | Intel E5-2673 v3-Prozessoren (Haswell) mit 2,4 GHz, angefügte SSD, virtueller Kern = 1 physischer Kern | Intel E5-2673 v4-Prozessoren (Broadwell) mit 2,3 GHz, schnelle NVMe-SSD, virtueller Kern = 1 LP (Hyperthread) |
| V-Kerne | 8, 16, 24 virtuelle Kerne | 8, 16, 24, 32, 40, 64, 80 V-Kerne |
| Arbeitsspeicher | 7 GB pro V-Kern | 5,1 GB pro virtuellem Kern |
| Max. In-Memory-OLTP-Arbeitsspeicher | 3GB pro virtuellem Kern | 2,6GB pro virtuellem Kern |
| Max. Instanzspeichergröße (allgemeiner Zweck) |  8 TB | 8 TB |
| Max. Instanzspeichergröße (unternehmenskritisch) | 1 TB | 1 TB, 2 TB oder 4 TB, je nach Anzahl der Kerne |

### <a name="service-tier-characteristics"></a>Merkmale des Diensttarifs

Eine verwaltete Instanz verfügt über zwei Dienstebenen: „Universell“und „Unternehmenskritisch“. Diese Tarife bieten verschiedene Funktionen, die in der folgenden Tabelle beschrieben sind.

| **Feature** | **Allgemeiner Zweck** | **Unternehmenskritisch** |
| --- | --- | --- |
| Anzahl der virtuellen Kerne\* | Gen4: 8, 16, 24<br/>Gen5: 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24, 32 <br/> Gen5: 8, 16, 24, 32, 40, 64, 80 |
| Arbeitsspeicher | Gen4: 56GB – 168GB (7GB/V-Kern)<br/>Gen5: 40,8GB – 408GB (5,1GB/V-Kern) | Gen4: 56GB – 168GB (7GB/V-Kern)<br/>Gen5: 40,8GB – 408GB (5,1GB/V-Kern) |
| Max. Instanzspeichergröße | 8 TB | Gen4: 1 TB <br/> Gen5: <br/>- 1 TB für 8, 16 virtuelle Kerne<br/>- 2 TB für 24 virtuelle Kerne<br/>- 4 TB für 32, 40, 64, 80 virtuelle Kerne |
| Max. Speicherkapazität pro Datenbank | Bestimmt durch die maximale Speichergröße pro Instanz | Bestimmt durch die maximale Speichergröße pro Instanz |
| Max. Anzahl von Datenbanken pro Instanz | 100 | 100 |
| Max. Datenbankdateien pro Instanz | Bis zu 280 | 32.767 Dateien pro Datenbank |
| Daten-/Protokoll-IOPS (ungefähr) | 500 bis 7.500 pro Datei<br/>\*[Abhängig von der Dateigröße](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 11K – 110K (1.375/V-Kern) |
| Protokolldurchsatz | 3 MB/Sek. pro virtuellem Kern<br/>Max. 22 MB/Sek. pro Instanz | 4 MB/Sek. pro virtuellem Kern<br/>Max. 48 MB/Sek. pro Instanz|
| Datendurchsatz (ungefähr) | 100 bis 250 MB/Sek. pro Datei<br/>\*[Abhängig von der Dateigröße](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | |
| E/A-Wartezeit (ungefähr) | 5 – 10 ms | 1 – 2 ms |
| Max. TempDB-Größe | 192 bis 1.920 GB (24 GB pro virtuellem Kern) | Keine Einschränkungen – beschränkt durch die maximale Instanzspeichergröße |
| Max. Sitzungen | 30000 | 30000 |

**Hinweise**:

- Sowohl die Daten- als auch die Protokolldateigröße in den Benutzer- und Systemdatenbanken sind in der Instanzspeichergröße enthalten, die mit dem Grenzwert für die maximale Speichergröße verglichen wird. Ermitteln Sie mithilfe der Systemansicht <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> den von Datenbanken verwendeten Gesamtspeicherplatz. Fehlerprotokolle werden nicht beibehalten und sind nicht in der Größe enthalten. Sicherungen sind nicht in der Speichergröße enthalten.
- Durchsatz und IOPS hängen auch von der Seitengröße ab, die nicht explizit durch die verwaltete Instanz eingeschränkt wird.

## <a name="supported-regions"></a>Unterstützte Regionen

Eine verwaltete Instanz kann nur in [unterstützten Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all) erstellt werden. Wenn Sie eine verwaltete Instanz in einer Region erstellen möchten, die derzeit nicht unterstützt wird, können Sie eine [Supportanfrage über das Azure-Portal senden](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Unterstützte Abonnementtypen

Für eine verwaltete Instanz wird derzeit nur die Bereitstellung bei folgenden Abonnementtypen unterstützt:

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Nutzungsbasierte Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Clouddienstanbieter (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)

> [!NOTE]
> Diese Einschränkung ist vorübergehend. Es werden in Zukunft neue Abonnementtypen bereitgestellt.

## <a name="regional-resource-limitations"></a>Regionale Ressourcenbeschränkungen

Unterstützte Abonnementtypen können eine begrenzte Anzahl von Ressourcen pro Region umfassen. Eine verwaltete Instanz weist zwei Standardbeschränkungen pro Azure-Region je nach Abonnementtyp auf:

- **Subnetzlimit**: Die maximale Anzahl von Subnetzen bei Bereitstellung verwalteter Instanzen in einer einzelnen Region.
- **Limit für die Anzahl der Instanzen**: Die maximale Anzahl von Instanzen, die in einer einzelnen Region bereitgestellt werden können.

> [!Note]
> Diese Limits sind Standardeinstellungen und keine technischen Einschränkungen. Diese Limits können bei Bedarf erhöht werden, indem Sie eine spezielle [Supportanfrage im Azure-Portal](#obtaining-a-larger-quota-for-sql-managed-instance) erstellen, falls Sie mehr verwaltete Instanzen in der aktuellen Region benötigen. Alternativ können Sie auch neue verwaltete Instanzen in einer anderen Azure-Region erstellen, ohne Supportanfragen zu senden.

In der folgenden Tabelle sind regionale Standardlimits für unterstützte Abonnements angegeben:

|Abonnementtyp| Max. Anzahl von Subnetzen für verwaltete Instanzen | Max. Anzahl von Instanzen |Max. Anzahl von universellen verwalteten Instanzen*|Max. Anzahl von unternehmenskritischen verwalteten Instanzen*|
| :---| :--- | :--- |:--- |:--- |
|Nutzungsbasierte Bezahlung|1*|4*|4*|1*|
|CSP |1*|4*|4*|1*|
|Pay-as-you-go Dev/Test|1*|4*|4*|1*|
|Enterprise Dev/Test|1*|4*|4*|1*|
|EA|3**|12**|12**|3**|

\* Sie können entweder 1 unternehmenskritische oder 4 universelle Instanzen in einem Subnetz bereitstellen, sodass die Gesamtanzahl von „Instanzeinheiten“ im Subnetz niemals höher als 4 ist.

** Die maximale Anzahl von Instanzen in einer Dienstebene trifft dann zu, wenn keine Instanzen in einer anderen Dienstebene vorhanden sind. Falls Sie beabsichtigen, universelle und unternehmenskritische Instanzen im selben Subnetz zu kombinieren, verwenden Sie den folgenden Abschnitt als Referenz für zulässige Kombinationen. Als einfache Faustregel gilt: Die Gesamtanzahl von Subnetzen darf nicht höher als 3 und die Gesamtanzahl von Instanzeinheiten nicht höher als 12 sein.


> [!IMPORTANT]
> Beachten Sie beim Planen Ihrer Bereitstellungen, dass eine unternehmenskritische Instanz (aufgrund zusätzlicher Redundanz) im allgemeinen 4 x mehr Kapazität als eine universelle Instanz benötigt. Dies bedeutet für Ihre Berechnungen: 1 universelle Instanz = 1 Instanzeinheit und 1 unternehmenskritische Instanz = 4 Instanzeinheiten. Um die Nutzungsanalyse hinsichtlich der Standardgrenzwerte zu vereinfachen, fassen Sie die Instanzeinheiten für alle Subnetze in der Region zusammen, in der verwaltete Instanzen bereitgestellt werden, und vergleichen Sie die Ergebnisse mit den Grenzwerten für Instanzeinheiten Ihres Abonnementtyps.

## <a name="strategies-for-deploying-mixed-general-purpose-and-business-critical-instances"></a>Strategien für die Bereitstellung einer Kombination aus universellen und unternehmenskritischen Instanzen

Abonnements vom Typ [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) können Kombinationen aus universellen und unternehmenskritischen Instanzen umfassen. Es gibt jedoch einige Einschränkungen in Bezug auf die Platzierung der Instanzen im Subnetz.

> [!Note]
> Bei Abonnements vom Typ [Nutzungsbasierte Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/) und [Clouddienstanbieter (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources) können entweder eine unternehmenskritische oder bis zu vier universelle Instanzen vorhanden sein.

Bei den folgenden Beispielen handelt es sich um Bereitstellungsfälle mit nicht leeren Subnetzen und einer Kombination der Dienstebenen „Universell“ und „Unternehmenskritisch“.

|Anzahl von Subnetzen|Subnetz 1|Subnetz 2|Subnetz 3|
|:---|:---|:---|:---|
|1|1 universelle und bis zu 8 unternehmenskritische<br>2 universelle und bis zu 4 unternehmenskritische|–| –|
|2|0 universelle, bis zu 4 unternehmenskritische|1 universelle, bis zu 4 unternehmenskritische<br>2 universelle, 0 unternehmenskritische|–|
|2|1 universelle, 0 unternehmenskritische|0 universelle, bis zu 8 unternehmenskritische<br>1 universelle, bis zu 4 unternehmenskritische|–|
|2|2 universelle, 0 unternehmenskritische|0 universelle, bis zu 4 unternehmenskritische|–|
|3|1 universelle, 0 unternehmenskritische|1 universelle, 0 unternehmenskritische|0 universelle, bis zu 4 unternehmenskritische|
|3|1 universelle, 0 unternehmenskritische|0 universelle, bis zu 4 unternehmenskritische|0 universelle, bis zu 4 unternehmenskritische|

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Abrufen eines größeren Kontingents für verwaltete SQL-Instanz

Wenn Sie mehr verwaltete Instanzen in Ihren aktuellen Regionen benötigen, senden Sie eine Supportanfrage zum Erweitern des Kontingents über das Azure-Portal.
Leiten Sie den Prozess zum Abrufen eines größeren Kontingents auf folgende Weise ein:

1. Öffnen Sie **Hilfe + Support**, und klicken Sie auf **Neue Supportanfrage**.

   ![Hilfe und Support](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Führen Sie auf der Registerkarte „Grundlagen“ für die neue Supportanfrage die folgenden Schritte aus:
   - Wählen Sie unter **Problemtyp** den Eintrag **Grenzwerte für Dienste und Abonnements (Kontingente)** aus.
   - Wählen Sie unter **Abonnement** Ihr Abonnement aus.
   - Wählen Sie unter **Kontingenttyp** den Eintrag **Verwaltete SQL-Datenbank-Instanz** aus.
   - Wählen Sie unter **Supportplan** Ihren Supportplan aus.

     ![Problemtyp „Kontingent“](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Klicken Sie auf **Weiter**.
4. Führen Sie auf der Registerkarte „Problem“ für die neue Supportanfrage die folgenden Schritte aus:
   - Wählen Sie unter **Schweregrad** den Schweregrad des Problems aus.
   - Geben Sie unter **Details** zusätzliche Informationen zu Ihrem Problem an, einschließlich Fehlermeldungen.
   - Fügen Sie unter **Dateiupload** eine Datei mit weiteren Informationen an (bis zu 4 MB).

     ![Problemdetails](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Eine gültige Anforderung sollte Folgendes enthalten:
     > - Region, in der der Grenzwert für Abonnements erhöht werden muss
     > - Erforderliche Anzahl von Instanzen pro Dienstebene in vorhandenen Subnetzen nach Erhöhung des Kontingents (wenn eines der vorhandenen Subnetze erweitert werden muss)
     > - Erforderliche Anzahl neuer Subnetze und Gesamtanzahl der Instanzen pro Dienstebene in den neuen Subnetzen (wenn Sie verwaltete Instanzen in neuen Subnetzen bereitstellen müssen)

5. Klicken Sie auf **Weiter**.
6. Geben Sie auf der Registerkarte „Kontaktinformationen“ für die neue Supportanfrage Ihre bevorzugte Kontaktmethode (E-Mail oder Telefon) und die Kontaktdetails ein.
7. Klicken Sie auf **Create**.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur verwalteten Instanz finden Sie unter [Was ist eine verwaltete Instanz?](sql-database-managed-instance.md).
- Preisinformationen finden Sie unter [Preise – verwaltete Azure SQL-Datenbank-Instanzen ](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Im [Schnellstarthandbuch](sql-database-managed-instance-get-started.md) erfahren Sie, wie Sie Ihre erste verwaltete Instanz erstellen.