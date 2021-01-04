---
title: Data Lake Storage und WANdisco LiveData Platform for Azure (Vorschauversion)
description: Migrieren Sie lokale Hadoop-Daten mithilfe der WANdisco LiveData Platform for Azure nach Azure Data Lake Storage Gen2.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 11/06/2020
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: db95f22a17c3776d84f12249693fb23b3d2d94e6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95914438"
---
# <a name="meet-demanding-migration-requirements-with-wandisco-livedata-platform-for-azure-preview"></a>WANdisco LiveData Platform for Azure (Vorschauversion) für anspruchsvolle Migrationsanforderungen

Migrieren Sie lokale Hadoop-Daten mithilfe der [WANdisco LiveData Platform for Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) nach Azure Data Lake Storage Gen2. Mit dieser Plattform umgehen Sie Anwendungsausfallzeiten und die Gefahr eines Datenverlusts und stellen sicher, dass die Datenkonsistenz während der Fortführung des lokalen Betriebs gewahrt bleibt.  

> [!NOTE]
> WANdisco LiveData Platform for Azure befindet sich in der öffentlichen Vorschauphase. Informationen zur regionalen Verfügbarkeit finden Sie unter [Supported regions](https://docs.wandisco.com/live-data-platform/docs/prereq#supported-regions) (Unterstützte Regionen).

Die Plattform besteht aus zwei Diensten: [LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) für die Migration aktiv genutzter Daten aus lokalen Umgebungen in Azure Storage und [LiveData Plane for Azure](https://www.wandisco.com/products/livedata-plane-for-azure) zur Sicherstellung der konsistenten Replikation aller geänderten oder erfassten Daten. 

> [!div class="mx-imgBorder"]
> ![Übersichtsschaubild zur LiveData Platform](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

Sie können beide Dienste über das Azure-Portal und die Azure CLI verwalten, und beide folgen dem gleichen getakteten Abrechnungsmodell mit nutzungsbasierter Bezahlung wie alle anderen Azure-Dienste. Die Nutzung der LiveData Platform for Azure wird auf der gleichen monatlichen Azure-Rechnung aufgeführt, sodass Sie die Nutzung konsistent und bequem nachverfolgen können.

Im Gegensatz zur _Offlinemigration_ von Daten durch [Kopieren statischer Informationen in Azure Data Box](./data-lake-storage-migrate-on-premises-hdfs-cluster.md) und zur Verwendung von Hadoop-Tools wie [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html) bleiben Ihre Unternehmenssysteme während der _Onlinemigration_ mit WANdisco LiveData for Azure vollständig funktionsfähig. Ihre Big Data-Umgebungen bleiben auch beim Verschieben der Daten nach Azure in Betrieb.

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>Wichtige Funktionen der WANdisco LiveData Platform für Azure

[WANdisco LiveData Platform für Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) verwendet ein einzigartiges Konsensmodul mit WAN-Unterstützung, um Datenkonsistenz zu erzielen, und führt Datenreplikation im großen Stil durch, während die replizierten Daten weiterhin von Anwendungen geändert werden können.  

Einige der wichtigsten Funktionen der Plattform:

- **Datenkonsistenz**: Bewältigen Sie die Herausforderungen bei der Migration großer Datenvolumes zwischen Umgebungen. Die Konsistenz der Daten zwischen Speichersystemen bleibt während der Migration gewahrt, sogar wenn diese ständig geändert werden. Verwenden Sie das einzigartige WAN-fähige Konsensmodul von WANdisco direkt in Azure, um Datenkonsistenz zu erzielen und Daten mit garantierter Konsistenz während Änderungen an den Daten zu migrieren.

- **Wartungsvorgänge**: Da Daten während der Migration weiterhin erstellt, geändert, gelesen und gelöscht werden können, entfällt die Notwendigkeit von Betriebsunterbrechungen oder Wartungsfenstern für die Migration von Big Data zu Azure. Führen Sie Anwendungen, Analyseinfrastrukturen, Erfassungsaufträge und andere Verarbeitungsvorgänge weiterhin aus.

- **Überprüfen des Ergebnisses**: Für die End-to-End-Überprüfung, die sicherstellt, dass Ihre Daten nach der Migration zu Azure effektiv verwendet werden können, ist es erforderlich, dass Sie Workloads für Produktionsanwendungen für sie ausführen. Nur ein LiveData-Dienst bietet diese Möglichkeit, ohne Datenabweichungen zu riskieren, indem die Datenkonsistenz unabhängig davon gewahrt bleibt, ob die Änderungen bei der Quelle oder beim Ziel der Migration erfolgen. Testen und validieren Sie das Anwendungsverhalten risikofrei und ohne Änderungen an Ihren Prozessen und Systemen.

- **Geringere Komplexität**: Dank der automatisierten Datenmigration müssen keine Aufträge zum Kopieren von Daten erstellt und verwaltet werden. Verwenden Sie die umfassende Integration in Azure als Steuerungsebene, um den Migrationsstatus zu verwalten und zu überwachen, u. a. hinsichtlich selektiver Datenreplikation, Hive-Metadaten, Sicherheit und Vertraulichkeit von Daten.

- **Effizienz**: Sorgen Sie für einen hohen Durchsatz und eine hohe Leistung, und skalieren Sie Big Data-Volumes ganz einfach. Über die Steuerung der Bandbreitennutzung können Sie sicherstellen, dass Sie Ihre Migrationsziele ohne Beeinträchtigung anderer Systemvorgänge erreichen.

## <a name="migrate-big-data-faster-without-risk"></a>Schnelleres risikofreies Migrieren von Big Data

Der eine Dienst der WANdisco LiveData Platform for Azure ist [LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure), eine Lösung für die Migration aktiv genutzter Daten aus lokalen Umgebungen in Azure Storage. LiveData Migrator for Azure wird vollständig über das Azure-Portal oder die Azure CLI bereitgestellt und verwaltet. Er wird parallel zum lokalen Hadoop-Cluster ausgeführt, ohne dass Änderungen an der Konfiguration oder an Anwendungen vorgenommen oder Dienste neu gestartet werden müssen, um direkt mit der Datenmigration zu beginnen.

> [!div class="mx-imgBorder"]
> ![LiveData Migrator for Azure Architecture](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture.png)

Big Data-Migrationen können komplex und schwierig sein. Ein Verschieben von Informationen im Petabytebereich ohne Unterbrechung der Geschäftsvorgänge war mit Offlineverfahren zum Kopieren von Daten nicht möglich. [LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) lässt sich einfach bereitstellen und kann zum Einrichten eines LiveData-Diensts mit fortlaufender Datenmigration und -replikation verwendet werden, während die migrierten Daten von Anwendungen gelesen, geschrieben und geändert werden.

Die Migration umfasst die folgenden drei einfachen Schritte:

1. Stellen Sie die LiveData Migrator-Instanz über das Azure-Portal in Ihrem lokalen Hadoop-Cluster bereit. Es sind keine Änderungen am Cluster oder Ausfallzeiten erforderlich, und Anwendungen können weiterhin ausgeführt werden.

   > [!div class="mx-imgBorder"]
   >![Erstellen einer LiveData Migrator-Instanz](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

2. Definieren Sie das Speicherkonto mit aktiviertem Azure Data Lake Storage Gen2.

   > [!div class="mx-imgBorder"]
   >![Erstellen eines LiveData Migrator-Ziels](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

3. Definieren Sie den Speicherort der Daten, die Sie migrieren möchten, z. B. `/user/hive/warehouse`, und starten Sie die Migration.

   > [!div class="mx-imgBorder"]
   > ![Erstellen einer LiveData Migrator-Migration](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

Überwachen Sie den Migrationsstatus mithilfe von Standard-Azure-Tools wie der Azure CLI und dem Azure-Portal, und verwenden Sie Ihre lokale Umgebung ohne Unterbrechungen. Machen Sie sich mit den [Voraussetzungen](https://docs.wandisco.com/live-data-platform/docs/prereq/) vertraut, bevor Sie beginnen.

## <a name="replicate-data-under-active-change"></a>Replizieren von aktiv geänderten Daten

Bei umfangreichen Migrationen von lokalen Data Lakes zu Azure sind Anwendungstests und -überprüfungen erforderlich. Dies muss möglich sein, ohne das Risiko von Änderungen an den Daten einzugehen, die zu mehreren autoritativen Quellen führen würden, die schwer zu vereinheitlichen sind. Nur so ist ein Wechsel zu Azure ohne Risiko und mit minimalen Kosten möglich. [LiveData Plane for Azure](https://www.wandisco.com/products/livedata-plane-for-azure) vermeidet diese Probleme, indem die Koordinationsmodultechnologie von WANdisco verwendet wird.

> [!div class="mx-imgBorder"]
> ![LiveData Plane for Azure Architecture](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

Sorgen Sie nach der Ausgangsmigration mit LiveData Plane for Azure dafür, dass Ihre Daten über lokale Hadoop-Cluster und Azure Storage hinweg konsistent bleiben:

1. Stellen Sie LiveData Plane for Azure lokal und in Azure bereit. Verwenden Sie dazu zunächst das Azure-Portal. Es sind keine Änderungen an Anwendungen erforderlich.
2. Konfigurieren Sie Replikationsregeln, die die Datenspeicherorte abdecken, die konsistent bleiben sollen, z. B.: `/user/contoso/sales/region/WA`.
3. Führen Sie nach Bedarf Anwendungen aus, die auf Daten an einem der beiden Speicherorte als Hadoop-kompatibles Dateisystem zugreifen und diese ändern.

LiveData Plane for Azure stellt die Datenkonsistenz ohne nennenswerte Auswirkungen auf die Leistung von Clustervorgängen und Anwendungen sicher. Ändern oder erfassen Sie Daten, während alle Änderungen konsistent repliziert werden.

## <a name="next-steps"></a>Nächste Schritte

- [LiveData Platform for Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) kann wie jede andere Azure-Ressource verwendet werden und ist jetzt als Vorschauversion verfügbar. 

- Machen Sie sich mit den [Voraussetzungen](https://docs.wandisco.com/live-data-platform/docs/prereq/) vertraut, planen Sie die Migration, und führen Sie eine umfassende Migration mit LiveData Migrator for Azure in kürzester Zeit durch.

- Testen Sie den LiveData Migrator, auch wenn Sie über keinen lokalen Hadoop-Cluster verfügen, in der [HDFS Sandbox](https://docs.wandisco.com/live-data-platform/docs/create-sandbox-intro/).

## <a name="see-also"></a>Weitere Informationen

- [LiveData Migrator for Azure auf Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=Overview)

- [LiveData Plane for Azure auf Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=Overview)

- [LiveData Migrator for Azure – Pläne + Preise](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=PlansAndPrice)

- [LiveData Plane for Azure – Pläne + Preise](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=PlansAndPrice) 

- [LiveData Platform for Azure – Häufig gestellte Fragen](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [LiveData Platform for Azure – Bekannte Probleme](https://docs.wandisco.com/live-data-platform/docs/known-issues/)

- [Einführung in Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)