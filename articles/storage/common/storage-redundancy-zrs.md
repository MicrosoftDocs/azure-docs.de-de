---
title: Erstellen von hochverfügbaren Azure Storage-Anwendungen für zonenredundante Speicher (ZRS) | Microsoft-Dokumentation
description: Zonenredundante Speicher (ZRS) bieten eine einfache Möglichkeit zum Erstellen von hochverfügbaren Anwendungen. ZRS schützen vor Hardwareausfällen im Rechenzentrum und vor regionalen Notfällen.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/24/2018
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 111167584fb2e0e2ee5977e0e24b3ebf07b170c1
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238005"
---
# <a name="zone-redundant-storage-zrs-highly-available-azure-storage-applications"></a>Zonenredundanter Speicher (ZRS): Hochverfügbare Azure Storage-Anwendungen
[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-zrs.md)]

## <a name="support-coverage-and-regional-availability"></a>Supportabdeckung und regionale Verfügbarkeit
ZRS unterstützt derzeit Standardkonten vom Typ „Allgemein v2“. Weitere Informationen zu Arten von Speicherkontotypen finden Sie unter [Übersicht über Azure Storage-Konten](storage-account-overview.md).

ZRS ist für Blockblobs, Seitenblobs ohne Datenträger, Dateien, Tabellen und Warteschlangen verfügbar

ZRS ist in den folgenden Regionen verfügbar:

- Asien, Südosten
- Europa, Westen
- Europa, Norden
- Frankreich, Mitte
- Japan, Osten
- UK, Süden
- USA, Mitte
- USA, Osten
- USA (Ost 2)
- USA, Westen 2

Microsoft arbeitet daran, ZRS in weiteren Azure-Regionen zu aktivieren. Sehen Sie regelmäßig auf der Seite [Azure-Updates](https://azure.microsoft.com/updates/) nach, um Informationen zu neuen Regionen zu erhalten.

## <a name="what-happens-when-a-zone-becomes-unavailable"></a>Was geschieht, wenn eine Zone nicht mehr verfügbar ist?
Auf Ihre Daten kann weiterhin von Lese- und Schreibvorgängen zugegriffen werden, auch wenn eine Zone nicht mehr verfügbar ist. Microsoft empfiehlt, Vorgehensweisen für die Behandlung vorübergehender Fehler anzuwenden. Zu diesen Vorgehensweisen gehört die Implementierung von Wiederholungsrichtlinien mit exponentiellem Backoff.

Wenn eine Zone nicht verfügbar ist, führt Azure Netzwerkupdates durch, z.B. durch die Festlegung neuer DNS-Ziele. Diese Updates können sich auf Ihre Anwendung auswirken, wenn Sie auf Ihre Daten zugreifen, bevor die Updates abgeschlossen sind.

ZRS kann Ihre Daten nicht vor einem regionalen Notfall schützen, bei dem mehrere Zonen dauerhaft betroffen sind. ZRS bietet stattdessen Resilienz für Ihre Daten bei einem temporären Ausfall. Für den Schutz vor regionalen Ausfällen empfiehlt Microsoft die Verwendung von georedundantem Speicher (GRS). Weitere Informationen zum georedundanten Speicher finden Sie unter [Georedundanter Speicher (GRS): Regionsübergreifende Replikation für Azure Storage](storage-redundancy-grs.md).

## <a name="converting-to-zrs-replication"></a>Wechseln zur ZRS-Replikation
Die Migration von oder zu LRS, GRS und RA-GRS ist ein einfacher Prozess. Verwenden Sie das Azure-Portal oder die Speicherressourcenanbieter-API, um den Redundanztyp Ihres Kontos zu ändern. Azure repliziert Ihre Daten dann entsprechend. 

Für die Migration von Daten zu ZRS ist eine andere Strategie erforderlich. Die ZRS-Migration umfasst die physische Verlagerung von Daten von einem einzelnen Speicherstempel auf mehrere Stempel in einer Region.

Ihnen stehen in erster Linie zwei Optionen für die Migration zu ZRS zur Verfügung: 

- Kopieren oder verschieben Sie Daten manuell aus einem vorhandenen Konto in ein neues ZRS-Konto.
- Fordern Sie eine Livemigration an.

Microsoft empfiehlt dringend die Ausführung einer manuellen Migration. Eine manuelle Migration bietet mehr Flexibilität als eine Livemigration. Bei einer manuellen Migration können Sie den zeitlichen Ablauf steuern.

Für eine manuelle Migration stehen Ihnen zwei Optionen zur Verfügung:
- Verwenden Sie vorhandene Tools wie AzCopy, eine der Azure Storage-Clientbibliotheken oder zuverlässige Drittanbietertools.
- Wenn Sie mit Hadoop oder HDInsight vertraut sind, fügen Sie sowohl das Quell- als auch das Zielkonto (ZRS) an Ihren Cluster an. Danach parallelisieren Sie den Datenkopiervorgang mit einem Tool wie z.B. DistCp.
- Verwenden Sie eine der Azure Storage-Clientbibliotheken, um eigene Tools zu erstellen.

Eine manuelle Migration kann zu Ausfallzeiten der Anwendung führen. Wenn Ihre Anwendung eine hohe Verfügbarkeit erfordert, bietet Microsoft auch eine Option für eine Livemigration. Eine Livemigration ist eine direkte Migration. 

Während einer Livemigration können Sie Ihr Speicherkonto weiter verwenden, während Ihre Daten zwischen dem Quell- und dem Zielspeicher migriert werden. Während des Migrationsprozesses verfügen Sie über das gleiche Maß an Dauerhaftigkeit und Verfügbarkeit gemäß SLA, wie es normalerweise der Fall ist.

Beachten Sie die folgenden Einschränkungen bei Livemigrationen:

- Auch wenn Microsoft auf Ihre Anforderung zur Livemigration sofort reagiert, gibt es keine Garantie, zu welchem Zeitpunkt eine Livemigration abgeschlossen sein wird. Wenn Ihre Daten bis zu einem bestimmten Datum zu ZRS migriert werden müssen, empfiehlt Microsoft die Durchführung einer manuellen Migration. Grundsätzlich gilt: Je mehr Daten in Ihrem Konto vorhanden sind, desto länger dauert die Migration dieser Daten. 
- Die Livemigration wird nur für Speicherkonten unterstützt, die LRS- oder GRS-Replikation nutzen. Wenn Ihr Konto RA-GRS verwendet, müssen Sie zuerst den Replikationstyp Ihres Kontos auf LRS oder GRS ändern, bevor Sie fortfahren. Dieser Zwischenschritt entfernt vor der Migration den sekundären schreibgeschützten Endpunkt, der von RA-GRS bereitgestellt wird.
- Ihr Konto muss Daten enthalten.
- Sie können Daten nur innerhalb der gleichen Region migrieren. Wenn Sie Ihre Daten in ein ZRS-Konto zu einer anderen Region als das Quellkonto migrieren möchten, müssen Sie eine manuelle Migration ausführen.
- Nur Standard-Speicherkontotypen unterstützen die Livemigration. Premium-Speicherkonten müssen manuelle migriert werden.
- Die Livemigration von ZRS zu LRS, GRS oder RA-GRS wird nicht unterstützt. Sie müssen diese Daten manuell in ein neues oder vorhandenes Speicherkonto verschieben.
- Verwaltete Datenträger sind nur für LRS verfügbar und können nicht zu ZRS migriert werden. Informationen zur Integration in Verfügbarkeitsgruppen finden Sie unter [Einführung in verwaltete Azure-Datenträger](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#integration-with-availability-sets). Sie können Momentaufnahmen und Images für verwaltete SSD Standard-Datenträger auf HDD Standard Storage speichern und [zwischen LRS- und ZRS-Optionen wählen](https://azure.microsoft.com/pricing/details/managed-disks/). 

Sie können eine Livemigration über das [Azure-Support-Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) anfordern. Wählen Sie im Portal das Speicherkonto aus, das Sie in ZRS konvertieren möchten.
1. Wählen Sie **Neue Supportanfrage** aus.
2. Geben Sie die **Grundlagen** basierend auf den Informationen Ihres Kontos an. Wählen Sie im Abschnitt **Dienst** den Eintrag **Speicherkontoverwaltung** und die Ressource aus, die Sie in ZRS konvertieren möchten. 
3. Klicken Sie auf **Weiter**. 
4. Geben Sie im Abschnitt **Problem** die folgenden Werte an: 
    - **Schweregrad**: Behalten Sie den Standardwert bei.
    - **Problemtyp**: Wählen Sie **Datenmigration** aus.
    - **Kategorie**: Wählen Sie **Innerhalb einer Region zu ZRS migrieren** aus.
    - **Titel**: Geben Sie einen aussagekräftigen Titel ein, z.B. **ZRS-Kontomigration**.
    - **Details**: Geben Sie im Feld **Details** weitere Informationen ein, z.B. „Ich möchte von [LRS, GRS] in der Region „\_\_“ zu ZRS migrieren“. 
5. Klicken Sie auf **Weiter**.
6. Überprüfen Sie, ob die Kontaktinformationen auf dem Blatt **Kontaktinformationen** korrekt sind.
7. Klicken Sie auf **Erstellen**.

Ein Supportmitarbeiter wird sich mit Ihnen in Verbindung setzen und Sie nach Bedarf und Wunsch unterstützen.

## <a name="live-migration-to-zrs-faq"></a>Häufig gestellte Fragen zur Livemigration zu ZRS

**Sollte ich mich während der Migration auf Downtimes einstellen?**

Durch die Migration wird keine Downtime verursacht. Während einer Livemigration können Sie Ihr Speicherkonto weiter verwenden, während Ihre Daten zwischen dem Quell- und dem Zielspeicher migriert werden. Während des Migrationsprozesses verfügen Sie über das gleiche Maß an Dauerhaftigkeit und Verfügbarkeit gemäß SLA, wie es normalerweise der Fall ist.

**Kann es durch die Migration zu Datenverlust kommen?**

Durch die Migration kommt es nicht zu Datenverlust. Während des Migrationsprozesses verfügen Sie über das gleiche Maß an Dauerhaftigkeit und Verfügbarkeit gemäß SLA, wie es normalerweise der Fall ist.

**Müssen Anwendungen nach Abschluss der Migration aktualisiert werden?**

Nach Abschluss der Migration ändert sich der Replikationstyp der Konten in „Zonenredundanter Speicher (ZRS)“. Dienstendpunkte, Zugriffsschlüssel, SAS und andere Konfigurationsoption für Konten bleiben unverändert und funktionsfähig.

**Kann ich eine Livemigration von einem Allgemein v1-Konto zu ZRS anfordern?**

ZRS unterstützt nur Allgemein v2-Konten. Bevor Sie also eine Anforderung für eine Livemigration zu ZRS übermitteln, sollten Sie Ihre Konten auf „Allgemein v2“ upgraden. Weitere Informationen finden Sie unter [Übersicht über Azure Storage-Konten](https://docs.microsoft.com/azure/storage/common/storage-account-overview) und [Durchführen eines Upgrades auf ein Speicherkonto vom Typ „Allgemein v2“](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).

**Kann ich eine Livemigration meines georedundanten Speicherkontos mit Lesezugriff (RA-GRS) zu ZRS anfordern?**

Bevor Sie eine Livemigration zu ZRS anfordern, sollten Sie sicherstellen, dass Ihre Anwendungen oder Workloads keinen Zugriff auf die schreibgeschützten sekundären Endpunkte mehr benötigen. Ändern Sie außerdem den Replikationstyp Ihres Speicherkontos in „Georedundanter Speicher (GRS)“. Weitere Informationen finden Sie unter [Ändern der Replikationsstrategie](https://docs.microsoft.com/azure/storage/common/storage-redundancy#changing-replication-strategy).

**Kann ich eine Livemigration von einem Speicherkonto zu ZRS in einer anderen Region anfordern?**

Wenn Sie Ihre Daten in ein ZRS-Konto in einer anderen Region als der Region des Quellkontos migrieren möchten, müssen Sie eine manuelle Migration durchführen.

## <a name="zrs-classic-a-legacy-option-for-block-blobs-redundancy"></a>ZRS (klassisch): Eine ältere Option für die Redundanz von Blockblobs
> [!NOTE]
> Microsoft wird ZRS Classic-Konten am 31. März 2021 als veraltet markieren und migrieren. Bevor die Option als veraltet gekennzeichnet wird, erhalten ZRS Classic-Kunden weitere Informationen. 
>
> Sobald der zonenredundante Speicher in einer Region [allgemein verfügbar](#support-coverage-and-regional-availability) wird, können Kunden im Portal in dieser Region kein klassisches ZRS-Konto mehr erstellen. Die Verwendung von Microsoft PowerShell und der Azure CLI zum Erstellen von ZRS Classic-Konten ist eine Option, bis ZRS Classic als veraltet gekennzeichnet wird.

ZRS Classic repliziert Daten asynchron zwischen Rechenzentren in einer oder zwei Regionen. Replizierte Daten sind möglicherweise erst wieder verfügbar, wenn Microsoft das Failover auf die sekundäre Region initiiert. Ein ZRS Classic-Konto kann nicht zu oder aus LRS, GRS oder RA-GRS konvertiert werden. ZRS Classic-Konten unterstützen zudem weder Metriken noch Protokollierungen.

ZRS Classic ist nur für **Blockblobs** in Speicherkonten vom Typ „Allgemein v1 (GPv1)“ verfügbar. Weitere Informationen zu Speicherkonten finden Sie unter [Azure-Speicherkonto – Übersicht](storage-account-overview.md).

Um ZRS-Kontodaten manuell zu oder von einem LRS-, ZRS (klassisch)-, GRS- oder RA-GRS-Konto zu migrieren, verwenden Sie eins der folgenden Tools: AzCopy, Azure Storage-Explorer, Azure PowerShell oder Azure-Befehlszeilenschnittstelle. Sie können auch Ihre eigene Migrationslösung mit einer der Azure Storage-Clientbibliotheken erstellen.

Sie können Ihre klassischen ZRS-Konten in den Regionen, in denen ZRS verfügbar ist, zudem im Portal, über Azure PowerShell oder die Azure CLI zu ZRS upgraden.

Navigieren Sie zum Abschnitt „Konfiguration“ des Kontos, und klicken Sie auf „Aktualisieren“, um ein Upgrade zu ZRS im Portal vorzunehmen:![Upgrade von ZRS (klassisch) zu ZRS im Portal](media/storage-redundancy-zrs/portal-zrs-classic-upgrade.jpg)

Führen Sie folgenden Befehl aus, um mithilfe von PowerShell ein Upgrade auf ZRS durchzuführen:
```powershell
Set-AzStorageAccount -ResourceGroupName <resource_group> -AccountName <storage_account> -UpgradeToStorageV2
```

Führen Sie folgenden Befehl aus, um mithilfe der CLI ein Upgrade auf ZRS durchzuführen:
```cli
az storage account update -g <resource_group> -n <storage_account> --set kind=StorageV2
```

## <a name="see-also"></a>Weitere Informationen
- [Azure Storage-Replikation](storage-redundancy.md)
- [Lokal redundanter Speicher (LRS): Kostengünstige Datenredundanz für Azure Storage](storage-redundancy-lrs.md)
- [Georedundanter Speicher (GRS): Regionsübergreifende Replikation für Azure Storage](storage-redundancy-grs.md)
