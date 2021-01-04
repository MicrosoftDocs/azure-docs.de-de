---
title: Optionen für die Datenmigration von StorSimple-Geräten der Serie 8000
description: Bietet eine Übersicht über die Optionen zum Migrieren von Daten aus StorSimple-Serie 8000.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 03/25/2020
ms.author: alkohli
ms.openlocfilehash: 4839f8211e678f5fc2fb3572c7eaa545fbee6c6c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961191"
---
# <a name="options-to-migrate-data-from-storsimple-8000-series"></a>Optionen zum Migrieren von Daten aus StorSimple-Serie 8000

> [!IMPORTANT]
> Ab dem 31. Dezember 2022 wird die StorSimple-Serie 8000 nicht mehr unterstützt. Es wird empfohlen, dass Kunden der StorSimple-Serie 8000 zu einer der in diesem Dokument beschriebenen Alternativen migrieren.

Im Dezember 2022 erreicht die StorSimple-Serie 8000 das [Ende des Supports](https://support.microsoft.com/lifecycle/search?alpha=Azure%20StorSimple%208000%20Series). Kunden, die StorSimple-Serie 8000 ausführen, können ein Upgrade auf andere Erstanbieter-Hybriddienste von Azure durchführen. In diesem Artikel werden die verfügbaren Azure-Hybridoptionen zum Migrieren von Daten beschrieben.

## <a name="migration-options"></a>Migrationsoptionen

Für Kunden der StorSimple-Serie 8000 stehen Azure- oder Drittanbieteroptionen zur Verfügung.

### <a name="azure-options"></a>Azure-Optionen

#### <a name="migrate-to-azure-file-sync"></a>Migrieren zur Azure-Dateisynchronisierung

Diese völlig neue Migrationsoption ermöglicht es Kunden, die Dateifreigaben ihrer Organisation in Azure Files zu speichern. Diese Dateifreigaben werden dann mithilfe der Azure-Dateisynchronisierung (Azure File Sync, AFS) für den lokalen Zugriff zentralisiert. AFS kann auf einem Windows Server-Host bereitgestellt werden. Die tatsächliche Datenmigration wird dann als Hostkopie oder mit dem Migrationstool ausgeführt.

Weitere Informationen zum Migrieren von Daten mithilfe der Azure-Dateisynchronisierung finden Sie unter [StorSimple 8100- und 8600-Migration zur Azure-Dateisynchronisierung](../storage/files/storage-files-migration-storsimple-8000.md).

### <a name="third-party-options"></a>Drittanbieteroptionen

#### <a name="migrate-to-panzura-freedom-nas"></a>Migration zu Panzura Freedom NAS

Kunden, die die StorSimple 5000–7000-Serie und StorSimple 8000-Serie nutzen, können zu Panzura Freedom NAS migrieren, um ihre Daten in Azure beizubehalten. Die Panzura Freedom-Lösung bietet eine NAS-Lösung, die Rechenzentren, Niederlassungen sowie öffentliche und private Clouds umfasst. Die Lösung ermöglicht lokale, Hybrid- und In-Cloud-Datenworkflows für NFS, SMB und mobile Clients.

Diese Migration wird von Panzura unterstützt, und können Kunden als ersten Schritt Migrationsunterstützung auf der [Panzura-Website](https://panzura.com/migrate-storsimple-panzura/) anfordern.

#### <a name="migrate-to-nasuni"></a>Migrieren zu Nasuni

Das Umstellen Ihrer gesamten StorSimple-Umgebung auf eine stabile, sichere und hochleistungsfähige Dateidienstplattform ist mit Nasuni einfach. Nasuni bietet die Sicherheit und Leistung lokaler Dateispeicherung, jedoch kombiniert mit der Skalierbarkeit und Dauerhaftigkeit von Azure.  Als führender unabhängiger Softwarehersteller (Independent Software Vendor, ISV) von Azure bietet Nasuni alle erforderlichen Tools zur Auslagerung Ihrer StorSimple-Daten auf eine moderne Plattform, die übergreifendes Teilen und Zusammenarbeit an ihren Dateien über mehrere Standorte hinweg ermöglicht.

Legen Sie noch heute los: [Nasuni-Website](https://info.nasuni.com/storsimple8000-webinar).

<!-- 04/09/2020 v-grpr (priestlg) - As per request, commenting out this section because the information that will go into this section is forthcoming
#### Migrate to Cohesity

Cohesity enables you to migrate data from your current StorSimple 5000–7000 to the Cohesity Data Platform on Azure. The Cohesity Data Platform is a software-defined web-scale solution that consolidates files, backups, objects, and VMs onto a single cloud-native solution. After migration to the Data Platform, you can manage, protect, and provision data and apps from cloud to core through a single pane of glass. With Cohesity, start with as few as three nodes. 

Learn more on [migration to the Cohesity Data Platform](https://info.cohesity.com/migrate-from-storsimple-to-cohesity.html).

#### Migrate to Nasuni

Nasuni makes it easy for StorSimple 5000-7000 customers to migrate and keep their data in Azure.  Nasuni is a leading Azure-based NAS storage solution, giving customers the performance and security they expect from on-prem solutions, with cloud economics and scale.  In addition to high performance file storage, Nasuni and Azure handle backup and DR, while allowing you to share and collaborate on your data around the globe with centralized file storage management. 

Nasuni has the experience to make your migration easy – get started today: https://info.nasuni.com/nasuni-storsimple-migration

#### Migrate to Talon FAST

Talon makes it easy for StorSimple 5000-7000 customers to continue to leverage the benefits they valued so much in the StorSimple platform (small on-site footprint backed by unlimited cloud resources) with even greater function.  With the Talon FAST solution, customers can migrate and keep their data in Azure, while now having an even smaller software-only onsite footprint and adding benefits such as global file locking, global namespace, and multi-site collaboration.  Talon is a leading Azure ecosystem solution, working with global customers to migrate their on-premises file server workloads into a consolidated, Azure-based footprint without compromising user workflow or experience.  

Learn more about how to evolve to a cloud-consolidated enterprise at https://www.talonstorage.com/alliances/microsoft-storsimple.
-->

## <a name="migration---frequently-asked-questions"></a>Häufig gestellte Fragen zur Migration

### <a name="q-when-do-the-storsimple-8000-series-devices-reach-end-of-service"></a>Q. Wenn ist das Dienstende der StorSimple-Geräte der Serie 8000 erreicht?

A. Im Dezember 2022 erreicht die StorSimple-Serie 8000 das [Ende des Supports](https://support.microsoft.com/[lifecycle/search?alpha=Azure%20StorSimple%208000%20Series). Das Ende des Supports bedeutet, dass Microsoft nach Dezember 2022 weder für Hardware noch Software dieser Geräte Support leisten kann. Sie sollten jetzt unbedingt beginnen, einen Plan zum Migrieren der Daten von Ihren Geräten zu formulieren.

### <a name="q-what-happens-to-the-data-i-have-stored-in-azure"></a>Q. Was geschieht mit den Daten, die ich in Azure gespeichert habe?  

A. Sie können die Daten weiterhin in Azure verwenden, nachdem Sie sie zu einem neueren Dienst migriert haben.

### <a name="q-what-happens-to-the-data-i-have-stored-locally-on-my-storsimple-device"></a>Q. Was geschieht mit den Daten, die ich lokal auf meinem StorSimple-Gerät gespeichert habe?

A. Die auf dem lokalen Gerät gespeicherten Daten können wie in den Dokumenten für die Migration beschrieben auf den neueren Dienst kopiert werden.

### <a name="q-what-happens-if-i-want-to-keep-my-storsimple-8000-series-appliance"></a>Q. Was geschieht, wenn ich mein Gerät der StorSimple-Serie 8000 behalten möchte?

A. Die Dienste könnten zwar weiterhin funktionieren, aber Microsoft wird nicht mehr in der Lage sein, Support für Hardware und Software zu leisten. Die Migration wird im Interesse der Geschäftskontinuität dringend empfohlen.

### <a name="q-what-options-are-available-to-migrate-data-from-storsimple-8000-series-devices"></a>Q. Welche Optionen sind verfügbar, um Daten von StorSimple-Geräten der Serie 8000 zu migrieren?

A. Abhängig vom jeweiligen Szenario können Benutzer der StorSimple-Serie 8000 die folgenden Migrationsoptionen nutzen:

* **Migrieren zur Azure-Dateisynchronisierung**: Verwenden Sie diese Option, wenn Sie zum nativen Azure-Format wechseln möchten. Sie können die Azure-Dateisynchronisierung zur zentralen Verwaltung von Dateifreigaben verwenden.

* **Weitere Optionen**: Um hier nicht aufgeführte Migrationsoptionen zu erörtern, kontaktieren Sie den Microsoft-Support.

### <a name="q-is-migration-to-other-storage-solutions-supported"></a>Q. Wird die Migration zu anderen Speicherlösungen unterstützt?

A. Ja. Migration zu anderen Speicherlösungen mithilfe der Hostkopie der Daten wird unterstützt.

### <a name="q-is-migration-supported-by-microsoft"></a>Q. Wird die Migration von Microsoft unterstützt?

A. Die Migration von der Serie 8000 wird vollständig unterstützt. Microsoft empfiehlt Ihnen generell, sich vor Beginn der Migration an den Support zu wenden. Die Migration ist derzeit ein unterstützter Vorgang. Wenn Sie beabsichtigen, Daten von Ihrem StorSimple-Gerät der Serie 8000 zu migrieren, [wenden Sie sich an den StorSimple-Support](mailto:storsimp@microsoft.com).

### <a name="q-what-is-the-pricing-model-for-migration-to-azure-file-sync"></a>Q. Welches Preismodell gilt für die Migration zur Azure-Dateisynchronisierung?

A. Bei Nutzung der Azure-Dateisynchronisierung können Abonnementgebühren für den Dienst anfallen. Kunden müssen auch die laufenden Speicherkosten zahlen. Eine Schätzung finden Sie unter [AFS-Preise]( https://azure.microsoft.com/pricing/details/storage/files/).

### <a name="q-how-long-does-it-take-to-complete-a-migration"></a>Q. Wie lange dauert es, bis die Migration abgeschlossen ist?

A. Die Dauer der Datenmigration hängt von der Menge der Daten und der ausgewählten Upgradeoption ab.

## <a name="next-steps"></a>Nächste Schritte

* [Migrieren von Daten aus StorSimple-Serie 8000 zur Azure-Dateisynchronisierung](../storage/files/storage-files-migration-storsimple-8000.md)