---
title: Sicherungsanleitung für SAP HANA in Azure Virtual Machines | Microsoft-Dokumentation
description: In der Sicherungsanleitung für SAP HANA werden die beiden wichtigsten Sicherungsmöglichkeiten für SAP HANA auf virtuellen Azure-Computern beschrieben.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 522af4bf6cc711bbfdfd30d0443ee58dad56b87e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950022"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Sicherungsanleitung für SAP HANA in Azure Virtual Machines

## <a name="getting-started"></a>Erste Schritte

In der Sicherungsanleitung für SAP HANA auf virtuellen Azure-Computern (Azure Virtual Machines) werden nur Azure-spezifische Themen beschrieben. Informationen zu allgemeinen Aspekten der SAP HANA-Sicherung finden Sie in der Dokumentation zu SAP HANA. Wir erwarten, dass Sie mit den grundsätzlichen Sicherungsstrategien für Datenbanken sowie den Gründen und Motiven für eine zuverlässige und effektive Sicherungsstrategie vertraut sind. Außerdem sollten Sie mit den Anforderungen Ihres Unternehmens an das Sicherungsverfahren, der Aufbewahrungsdauer der Sicherungen und dem Wiederherstellungsverfahren vertraut sein.

SAP HANA wird offiziell von verschiedenen Azure-VM-Typen, z.B der Azure M-Serie, unterstützt. Eine vollständige Liste der für SAP HANA zertifizierten Azure-VMs und Einheiten für SAP HANA (große Instanzen) finden Sie unter [Find Certified IaaS Platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) (Zertifizierte IaaS-Plattformen). Microsoft Azure bietet eine Reihe von Einheiten, in denen SAP HANA nicht virtualisiert auf physischen Servern läuft. Dieser Dienst heißt [HANA (große Instanzen)](hana-overview-architecture.md). In diesem Leitfaden werden Sicherungsprozesse und Tools für HANA (große Instanzen) nicht behandelt. Wir beschränken uns auf virtuelle Azure-Computer. Einzelheiten zu Sicherungs-/Wiederherstellungsverfahren für HANA (große Instanzen) finden Sie im Artikel [Sichern und Wiederherstellen](./hana-backup-restore.md).

Der Schwerpunkt dieses Artikels liegt auf drei Sicherungsmöglichkeiten für SAP HANA auf virtuellen Azure-Computern:

- HANA-Sicherung per [Azure Backup-Dienste](../../../backup/backup-overview.md) 
- HANA-Sicherung im Dateisystem eines virtuellen Azure-Computers mit Linux (siehe [SAP HANA-Azure-Sicherung auf Dateiebene](sap-hana-backup-file-level.md))
- HANA-Sicherung basierend auf Speichermomentaufnahmen per manueller Nutzung der Azure Storage Blob-Momentaufnahmenfunktion oder des Azure Backup-Diensts


SAP HANA verfügt über eine Sicherungs-API, sodass Sicherungstools von Drittanbietern direkt in SAP HANA integriert werden können. Produkte wie der Azure Backup-Dienst oder [Commvault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/) nutzen diese proprietäre Schnittstelle, um SAP HANA-Datenbank- oder -Redo-Log-Sicherungen auszulösen. 


Informationen dazu, wie Sie herausfinden können, welche SAP-Software in Azure unterstützt wird, finden Sie im Artikel [Welche SAP-Software wird für Azure-Bereitstellungen unterstützt?](./sap-supported-product-on-azure.md).

## <a name="azure-backup-service"></a>Azure Backup-Dienst

Im ersten gezeigten Szenario werden der Azure Backup-Dienst und die SAP HANA-Schnittstelle `backint` verwendet, um eine Streamingsicherung einer SAP HANA-Datenbank durchzuführen. Alternativ können Sie auch eine allgemeinere Funktion des Azure Backup-Diensts einsetzen, um eine anwendungskonsistente Datenträger-Momentaufnahme zu erstellen und diese in den Azure Backup-Dienst übertragen zu lassen.

Azure Backup ist eine integrierte und zertifizierte Sicherungslösung für SAP HANA, die die proprietäre SAP HANA-Schnittstelle [backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) verwendet. Weitere Einzelheiten zur Lösung, ihren Fähigkeiten und den Azure-Regionen, in denen sie verfügbar ist, finden Sie im Artikel [Unterstützungsmatrix für die Sicherung von SAP HANA-Datenbanken auf virtuellen Azure-Computern](../../../backup/sap-hana-backup-support-matrix.md#scenario-support). Einzelheiten und grundsätzliche Informationen zum Azure Backup-Dienst für HANA finden Sie im Artikel [Informationen zur SAP HANA-Datenbanksicherung in Azure Virtual Machines](../../../backup/sap-hana-db-about.md). 

Die zweite Möglichkeit, den Azure Backup-Dienst zu nutzen, ist die Erstellung einer anwendungskonsistenten Sicherung mithilfe von Datenträger-Momentaufnahmen von Azure Storage Premium. Andere für HANA zertifizierte Azure-Speicher wie [Azure Ultra Disk](../../disks-enable-ultra-ssd.md) und [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) unterstützen diese Art von Momentaufnahmen über den Azure Backup-Dienst nicht. Lesen Sie diese Artikel:

- [Planen der Sicherungsinfrastruktur für virtuelle Computer in Azure](../../../backup/backup-azure-vms-introduction.md)
- [Anwendungskonsistente Sicherung von virtuellen Linux-Computern in Azure](../../../backup/backup-azure-linux-app-consistent.md) 

Diese Abfolge von Aktivitäten ergibt sich:

- Azure Backup muss ein Skript vor dem Erstellen einer Momentaufnahme ausführen, das die Anwendung, in diesem Fall SAP HANA, in einen konsistenten Zustand versetzt.
- Sobald dieser konsistente Zustand bestätigt ist, führt Azure Backup die Datenträger-Momentaufnahmen aus.
- Nach Abschluss der Momentaufnahmen macht Azure Backup die im Skript vor dem Erstellen einer Momentaufnahme durchgeführte Aktivität rückgängig.
- Nach erfolgreicher Ausführung streamt Azure Backup die Daten in den Azure Backup-Tresor.

Bei SAP HANA verwenden die meisten Kunden für die Volumes mit dem SAP HANA-Redo-Log die Azure-Schreibbeschleunigung. Der Azure Backup-Dienst schließt diese Volumes automatisch aus den Momentaufnahmen aus. Dieser Ausschluss beeinträchtigt nicht die Fähigkeit zur Wiederherstellung von HANA. Allerdings würde es die Möglichkeit der Wiederherstellung bei fast allen anderen von SAP unterstützten DBMS verhindern.

Die Schattenseite dieser Möglichkeit ist die Tatsache, dass Sie Ihr eigenes Skript zur Ausführung vor und nach Erstellung der Momentaufnahme entwickeln müssen. Das vor Erstellung der Momentaufnahme ausgeführte Skript muss eine HANA-Momentaufnahme erstellen und etwaige Ausnahmefälle behandeln. Dagegen muss das nach der Erstellung der Momentaufnahme ausgeführte Skript die HANA-Momentaufnahme wieder löschen. Wenn Sie weitere Informationen zur erforderlichen Logik benötigen, beginnen Sie mit [SAP-Supporthinweis 2039883](https://launchpad.support.sap.com/#/notes/2039883). Die Überlegungen im Abschnitt zur „SAP HANA-Datenkonsistenz bei der Erstellung von Speichermomentaufnahmen“ in diesem Artikel gelten in vollem Umfang für diese Art der Sicherung.

> [!NOTE]
> Auf Momentaufnahmen von Datenträgern basierende Sicherungen für SAP HANA in Bereitstellungen, in denen mehrere Datenbankcontainer genutzt werden, erfordern mindestens die Version HANA 2.0 SP04.
> 

Weitere Informationen zu Speichermomentaufnahmen finden Sie weiter unten in diesem Dokument.

![Abbildung mit Darstellung von zwei Möglichkeiten zum Speichern des aktuellen VM-Status](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>Andere HANA-Sicherungsmethoden
Es gibt drei weitere Sicherungsmethoden, die in Erwägung gezogen werden können:

- Sichern mithilfe einer NFS-Freigabe, die auf Azure NetApp Files (ANF) basiert. ANF bietet seinerseits die Möglichkeit, Momentaufnahmen der Volumes zu erstellen, auf denen Sie Sicherungen speichern. Angesichts des Durchsatzes, den Sie letztlich zum Schreiben der Sicherungen benötigen, könnte diese Lösung kostspielig werden. Die Einrichtung ist jedoch einfach, da HANA die Sicherungen direkt in die native NFS-Freigabe in Azure schreiben kann.
- Ausführen der HANA-Sicherung mittels an VM angefügter Datenträger des Typs „SSD Standard“ oder „Azure Storage Premium“. Im nächsten Schritt können Sie diese Sicherungsdateien in Azure Blob Storage kopieren. Diese Strategie könnte wirtschaftlich attraktiv sein.
- Ausführen der HANA-Sicherung mittels an VM angefügter Datenträger des Typs „SSD Standard“ oder „Azure Storage Premium“. Im nächsten Schritt wird regelmäßig eine Momentaufnahme des Datenträgers erstellt. Nach der ersten Momentaufnahme können inkrementelle Momentaufnahmen verwendet werden, um Kosten zu senken.

![Abbildung mit Optionen zur Erstellung einer SAP HANA-Dateisicherung auf der VM](media/sap-hana-backup-guide/other-hana-backup-paths.png)

In dieser Abbildung sind Optionen zum Erstellen einer SAP HANA-Dateisicherung auf der VM und zum anschließenden Speichern der HANA-Sicherungsdateien an einem anderen Speicherort mit unterschiedlichen Tools dargestellt. Allerdings weisen alle Lösungen, die nicht mit einem Sicherungsdienst eines Drittanbieters oder dem Azure-Backup-Dienst verbunden sind, mehrere gemeinsame Hürden auf. Einige von ihnen können aufgelistet werden, wie z. B. die Verwaltung der Aufbewahrung, der automatische Wiederherstellungsprozess und die Bereitstellung einer automatischen Zeitpunktwiederherstellung, wie sie von Azure Backup oder anderen spezialisierten Sicherungsprogrammen und -diensten von Drittanbietern angeboten werden. Viele dieser Dienste von Drittanbietern können in Azure ausgeführt werden. 


## <a name="sap-resources-for-hana-backup"></a>SAP-Ressourcen für die HANA-Sicherung

### <a name="sap-hana-backup-documentation"></a>Dokumentation zur SAP HANA-Sicherung

- [Introduction to SAP HANA Administration](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US) (Einführung in die SAP HANA-Administration)
- [Planning Your Backup and Recovery Strategy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) (Planen Ihrer Sicherungs- und Wiederherstellungsstrategie)
- [Schedule HANA Backup using ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html) (Planen der HANA-Sicherung mit ABAP DBACOCKPIT)
- [Schedule Data Backups (SAP HANA Cockpit)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm) (Planen von Datensicherungen(SAP HANA Cockpit))
- Häufig gestellte Fragen zur SAP HANA-Sicherung im [SAP-Hinweis 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Häufig gestellte Fragen zur SAP HANA-Datenbank und zu Speichermomentaufnahmen im [SAP-Hinweis 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Ungeeignete Netzwerkdateisysteme für Sicherung und Wiederherstellung im [SAP-Hinweis 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Sicherstellen der Korrektheit von SAP HANA-Sicherungen
Unabhängig von Ihrer Sicherungsmethode ist die Durchführung einer Testwiederherstellung auf einem anderen System eine absolute Notwendigkeit. Bei diesem Ansatz kann sichergestellt werden, dass eine Sicherung korrekt ist und die internen Prozesse für die Sicherung und Wiederherstellung wie erwartet funktionieren. Während die Wiederherstellung von Sicherungen aufgrund der erforderlichen lokalen Infrastruktur eine Hürde darstellen könnte, ist sie in der Cloud viel einfacher zu bewerkstelligen, indem die dafür notwendigen Ressourcen zeitweilig zur Verfügung gestellt werden. Es stimmt, dass mit HANA Tools zur Verfügung gestellt werden, mit denen sich Sicherungsdateien auf ihre Wiederherstellbarkeit überprüfen lassen. Der Zweck häufiger Wiederherstellungsübungen besteht jedoch darin, den Prozess einer Datenbankwiederherstellung zu testen und das Betriebspersonal darin zu schulen.

Beachten Sie, dass es nicht ausreicht, eine einfache Wiederherstellung durchzuführen und zu überprüfen, ob HANA betriebsbereit ist. Sie müssen eine Überprüfung der Konsistenz von Tabellen durchführen, um sich zu vergewissern, dass die wiederhergestellte Datenbank fehlerfrei ist. SAP HANA verfügt über mehrere Arten von Konsistenzprüfungen, die im [SAP-Hinweis 1977584](https://launchpad.support.sap.com/#/notes/1977584) beschrieben werden.

Informationen zur Überprüfung der Konsistenz von Tabellen finden Sie außerdem auf der SAP-Website unter [Table and Catalog Consistency Checks](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b) (Konsistenzprüfungen für Tabellen und Kataloge).

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Vor- und Nachteile einer HANA-Sicherung gegenüber einer Speichermomentaufnahme

Im SAP-System besteht keine Präferenz in Bezug auf die HANA-Sicherung bzw. die Speichermomentaufnahme. Die Vor- und Nachteile werden aufgeführt, damit Benutzer je nach Situation und verfügbarer Speichertechnologie ermitteln können, welches Verfahren am besten geeignet ist (siehe [Planning Your Backup and Recovery Strategy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) (Planen Ihrer Sicherungs- und Wiederherstellungsstrategie)).

Beachten Sie in Azure, dass bei der Azure-Blob-Momentaufnahmenfunktion keine Dateisystemkonsistenz auf mehreren Datenträgern garantiert ist (siehe [Using blob snapshots with PowerShell](/archive/blogs/cie/using-blob-snapshots-with-powershell) [Verwenden von Blob-Momentaufnahmen mit PowerShell]). 

Außerdem sollten Sie über die Abrechnungsaspekte informiert sein, wenn Sie Blob-Momentaufnahmen häufiger nutzen. Dies ist in diesem Artikel beschrieben: [Understanding How Snapshots Accrue Charges](/rest/api/storageservices/understanding-how-snapshots-accrue-charges) (Informationen zu den Gebühren, die für Momentaufnahmen entstehen). Dies ist nicht so einfach wie die Verwendung von virtuellen Azure-Datenträgern.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Konsistenz von SAP HANA-Daten beim Erstellen von Speichermomentaufnahmen

Wie zuvor dokumentiert, ist die Beschreibung der Azure Backup-Sicherungsfunktionen zum Erstellen von Momentaufnahmen sowie der Dateisystem- und Anwendungskonsistenz bei der Erstellung von Speichermomentaufnahmen obligatorisch. Die einfachste Möglichkeit zur Verhinderung von Problemen ist das Herunterfahren von SAP HANA oder sogar des gesamten virtuellen Computers. Dies ist etwas, das für eine Produktionsinstanz nicht machbar ist.

> [!NOTE]
> Auf Momentaufnahmen von Datenträgern basierende Sicherungen für SAP HANA in Bereitstellungen, in denen mehrere Datenbankcontainer genutzt werden, erfordern mindestens die Version HANA 2.0 SP04.
> 

Azure Storage bietet keine Dateisystemkonsistenz auf mehreren Datenträgern oder Volumes, die während der Erstellung der Momentaufnahme an eine VM angefügt sind. Das bedeutet, dass die Anwendungskonsistenz während der Momentaufnahme von der Anwendung, in diesem Fall SAP HANA selbst, gewährleistet werden muss. Der [SAP-Hinweis 2039883](https://launchpad.support.sap.com/#/notes/2039883) enthält wichtige Informationen zu SAP HANA-Sicherungen mithilfe von Speichermomentaufnahmen. Beispielsweise ist es beim XFS-Dateisystem erforderlich, **xfs\_freeze** auszuführen, bevor eine Speichermomentaufnahme gestartet wird, um Anwendungskonsistenz zu ermöglichen (Details zu **xfs\_freeze** finden Sie unter [xfs\_freeze(8) – Linux man page](https://linux.die.net/man/8/xfs_freeze)).

Angenommen, ein XFS-Dateisystem umfasst vier virtuelle Azure-Datenträger. Mit den folgenden Schritten wird eine konsistente Momentaufnahme bereitgestellt, die den HANA-Datenbereich darstellt:

1. Vorbereiten der Erstellung einer Momentaufnahme von HANA-Daten
1. Einfrieren der Dateisysteme aller Datenträger/Volumes (verwenden Sie z. B. **xfs\_freeze**)
1. Erstellen aller erforderlichen Blob-Momentaufnahmen in Azure
1. Aufheben des Einfrierens des Dateisystems
1. Bestätigen der Momentaufnahme der HANA-Daten (löscht die Momentaufnahme)

Wenn Sie die Azure Backup-Funktion zur Durchführung anwendungskonsistenter Momentaufnahmensicherungen verwenden, muss in Schritt 1 das vor der Momentaufnahme auszuführende Skript von Ihnen programmiert/geschrieben werden. Der Azure Backup-Dienst führt die Schritte 2 und 3 aus. Die Schritte 4 und 5 müssen von Ihrem Code im nach der Momentaufnahme auszuführenden Skript erneut bereitgestellt werden. Wenn Sie nicht den Azure Backup-Dienst nutzen, müssen Sie auch die Schritte 2 und 3 selbst programmieren/schreiben.
Weitere Informationen zum Erstellen von Momentaufnahmen von HANA-Daten finden Sie in den folgenden Artikeln:

- [HANA data snapshots] (Momentaufnahmen von HANA-Daten) (https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- Weitere Informationen zum Ausführen von Schritt 1 finden Sie im Artikel [Create a Data Snapshot (Native SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) (Erstellen einer Momentaufnahme von Daten [Native SQL]). 
- Details zum bedarfsabhängigen Bestätigen/Löschen von Momentaufnahmen von HANA-Daten in Schritt 5 finden Sie im Artikel [Create a Data Snapshot (Native SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) (Erstellen einer Momentaufnahme von Daten [Native SQL]). 

Es ist wichtig, die HANA-Momentaufnahme zu bestätigen. Aufgrund des &quot;Copy-on-Write&quot;-Vorgangs ist für SAP HANA in diesem Vorbereitungsmodus für Momentaufnahmen ggf. kein zusätzlicher Datenträger-Speicherplatz erforderlich. Das Starten neuer Sicherungen ist ebenfalls erst möglich, nachdem die SAP HANA-Momentaufnahme bestätigt wurde.


### <a name="sap-hana-backup-scheduling-strategy"></a>Strategie für die Zeitplanung für SAP HANA-Sicherungen

Der SAP HANA-Artikel [Planning Your Backup and Recovery Strategy](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) (Planen Ihrer Sicherungs- und Wiederherstellungsstrategie) enthält einen einfachen Sicherungsplan. Setzen Sie bei der Definition der Sicherungs-/Wiederherstellungsstrategie und des Prozesses für SAP HANA auf die SAP-Dokumentation rund um HANA und Ihre Erfahrungen mit anderen Datenbank-Managementsystemen. Die Reihenfolge der verschiedenen Arten von Backups und die Aufbewahrungsdauer hängen stark von den SLAs ab, die Sie bieten müssen.


### <a name="sap-hana-backup-encryption"></a>SAP HANA-Sicherungsverschlüsselung

SAP HANA ermöglicht die Verschlüsselung von Daten und Protokollen. Wenn SAP HANA-Daten und -Protokolle nicht verschlüsselt werden, sind standardmäßig auch die Sicherungen nicht verschlüsselt. SAP HANA bietet jedoch eine separate Sicherungsverschlüsselung an, die unter [SAP HANA-Sicherungsverschlüsselung](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html) dokumentiert ist. Wenn Sie ältere Versionen von SAP HANA nutzen, müssen Sie möglicherweise prüfen, ob die Verschlüsselung von Sicherungen bereits Teil der gebotenen Funktionalität war.  


## <a name="next-steps"></a>Nächste Schritte
* Unter [SAP HANA-Sicherung mit Azure Backup auf Dateiebene](sap-hana-backup-file-level.md) wird die dateibasierte Sicherung beschrieben.
* Informationen zur Erzielung von Hochverfügbarkeit und zur Planung der Notfallwiederherstellung für SAP HANA in Azure (große Instanzen) finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung für SAP HANA in Azure (große Instanzen)](hana-overview-high-availability-disaster-recovery.md).
