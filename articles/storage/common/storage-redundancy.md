---
title: Datenredundanz in Azure Storage | Microsoft-Dokumentation
description: Die Daten in Ihrem Microsoft Azure Storage-Konto werden stets repliziert, um Beständigkeit und Hochverfügbarkeit sicherzustellen. Die Redundanzoptionen umfassen den lokal redundanten Speicher (LRS), den zonenredundanten Speicher (ZRS), den georedundanten Speicher (GRS) und den georedundanten Speicher mit Lesezugriff (RA-GRS).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/18/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 078c62913b903eafe9e0fcfcef4189f5ca735d0f
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002824"
---
# <a name="azure-storage-redundancy"></a>Azure Storage-Redundanz

Die Daten in Ihrem Microsoft Azure-Speicherkonto werden stets repliziert, um Beständigkeit und Hochverfügbarkeit sicherzustellen. Azure Storage kopiert Ihre Daten so, dass sie vor geplanten und ungeplanten Ereignissen geschützt sind – von vorübergehend auftretenden Hardwarefehlern und Netzwerk- oder Stromausfällen bis zu schweren Naturkatastrophen usw. Sie können Ihre Daten wahlweise im selben Rechenzentrum, Rechenzentren in derselben Region und sogar übergreifend über geografisch getrennte Regionen replizieren.

Mit der Replikation wird sichergestellt, dass Ihr Speicherkonto auch bei Ausfällen die [Servicelevelvereinbarung (SLA) für Storage](https://azure.microsoft.com/support/legal/sla/storage/) erfüllt. Die Servicelevelvereinbarung enthält Informationen zu Azure Storage-Garantien in Bezug auf Dauerhaftigkeit und Verfügbarkeit.

Azure Storage überprüft regelmäßig die Integrität der gespeicherten Daten mithilfe von Cyclic Redundancy Checks (CRCs). Wenn eine Datenbeschädigung erkannt wird, wird sie unter Verwendung von redundanten Daten repariert. Azure Storage berechnet auch die Prüfsummen für den gesamten Netzwerkdatenverkehr, um eine Beschädigung von Datenpaketen beim Speichern oder Abrufen von Daten zu erkennen.

## <a name="choosing-a-redundancy-option"></a>Wahl einer Redundanzoption

Wenn Sie ein Speicherkonto erstellen, können Sie eine der folgenden Redundanzoptionen auswählen:

* [Lokal redundanter Speicher (LRS)](storage-redundancy-lrs.md)
* [Zonenredundanter Speicher (ZRS)](storage-redundancy-zrs.md)
* [Georedundanter Speicher (GRS)](storage-redundancy-grs.md)
* [Georedundanter Speicher mit Lesezugriff (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)

Die folgende Tabelle zeigt in einer kurzen Übersicht, in welchem Umfang die jeweilige Replikationsstrategie bei einem bestimmten Ereignistyp (oder einem Ereignis ähnlicher Auswirkung) Dauerhaftigkeit und Verfügbarkeit bietet.

| Szenario                                                                                                 | LRS                             | ZRS                              | GRS                                  | RA-GRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Nichtverfügbarkeit von Knoten innerhalb eines Rechenzentrums                                                                 | Ja                             | Ja                              | Ja                                  | Ja                                  |
| Ein gesamtes Rechenzentrum (zonal oder nicht zonal) ist nicht mehr verfügbar.                                           | Nein                              | Ja                              | Ja                                  | Ja                                  |
| Ein regionsweiter Ausfall                                                                                     | Nein                              | Nein                               | Ja                                  | Ja                                  |
| Lesezugriff auf Ihre Daten (in einer georeplizierten Remoteregion) bei regionsweiter Nichtverfügbarkeit | Nein                              | Nein                               | Nein                                   | Ja                                  |
| Konzipiert zum Bereitstellen von \_\_ Dauerhaftigkeit von Objekten über ein bestimmtes Jahr                                          | mindestens 99,999999999 % (11 mal die 9) | mindestens 99,9999999999 % (12 mal die 9) | mindestens 99,99999999999999 % (16 mal die 9) | mindestens 99,99999999999999 % (16 mal die 9) |
| Unterstützte Speicherkontotypen                                                                   | GPv2, GPv1, Blob                | GPv2                             | GPv2, GPv1, Blob                     | GPv2, GPv1, Blob                     |
| Verfügbarkeits-SLA für Leseanforderungen | mindestens 99,9 % (99 % bei der kalten Zugriffsebene) | mindestens 99,9 % (99 % bei der kalten Zugriffsebene) | mindestens 99,9 % (99 % bei der kalten Zugriffsebene) | mindestens 99,99 % (99,9 % bei der kalten Zugriffsebene) |
| Verfügbarkeits-SLA für Schreibanforderungen | mindestens 99,9 % (99 % bei der kalten Zugriffsebene) | mindestens 99,9 % (99 % bei der kalten Zugriffsebene) | mindestens 99,9 % (99 % bei der kalten Zugriffsebene) | mindestens 99,9 % (99 % bei der kalten Zugriffsebene) |

Die Preisen für die verschiedenen Redundanzoptionen finden Sie unter [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/). 

Informationen zu Azure Storage-Garantien in Bezug auf Dauerhaftigkeit und Verfügbarkeit finden Sie im [SLA für Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/).

> [!NOTE]
> Storage Premium unterstützt nur lokal redundanten Speicher (Locally Redundant Storage, LRS).

## <a name="changing-replication-strategy"></a>Ändern der Replikationsstrategie
Sie können die Replikationsstrategie für Ihr Speicherkonto mithilfe von [Azure-Portal](https://portal.azure.com/), [Azure PowerShell](storage-powershell-guide-full.md), [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) oder einer der [ Azure-Clientbibliotheken](https://docs.microsoft.com/azure/index#pivot=sdkstools) ändern. Bei einer Änderung des Replikationstyps Ihres Speicherkontos treten keine Ausfallzeiten auf.

   > [!NOTE]
   > Derzeit können Sie weder das Portal noch die API verwenden, um Ihr Konto in ZRS zu konvertieren. Ausführliche Informationen zum Konvertieren der Replikation Ihres Kontos in ZRS finden Sie unter [Zonenredundanter Speicher (ZRS): Hochverfügbare Azure Storage-Anwendungen](storage-redundancy-zrs.md).
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Entstehen mir Kosten für die Änderung der Replikationsstrategie meines Kontos?
Es hängt von Ihrem Konvertierungspfad ab. Vom billigsten bis zum teuersten Redundanzangebot bieten wir LRS, ZRS, GRS und RA-GRS an. Beispielsweise fallen beim Wechsel *von* LRS zu einem beliebigen anderen Angebot zusätzliche Kosten an, da Sie in eine komplexere Redundanzebene wechseln. Beim Wechsel *zu* GRS oder RA-GRS fallen Kosten für eine ausgehende Bandbreite an, da Ihre Daten (in Ihrer primären Region) in Ihre sekundäre Remoteregion repliziert werden. Dies sind einmalige Kosten bei der anfänglichen Einrichtung. Nachdem die Daten kopiert sind, fallen keine weiteren Kosten für die Konvertierung an. Ihnen werden nur ggf. die Replikation neuer oder die Aktualisierung vorhandener Daten in Rechnung gestellt. Ausführliche Informationen zu Bandbreitengebühren finden Sie auf der [Seite mit Informationen zu Azure Storage-Preisen](https://azure.microsoft.com/pricing/details/storage/blobs/).

Wenn Sie Ihr Speicherkonto von GRS in LRS umwandeln, entstehen keine zusätzlichen Kosten, aber Ihre replizierten Daten werden am sekundären Standort gelöscht.

Wenn Sie Ihr Speicherkonto von RA-GRS in GRS oder LRS umwandeln, wird das Konto nach dem Umstellungsdatum für weitere 30 Tage als RA-GRS abgerechnet.

## <a name="see-also"></a>Weitere Informationen

- [Lokal redundanter Speicher (LRS): Kostengünstige Datenredundanz für Azure Storage](storage-redundancy-lrs.md)
- [Zonenredundanter Speicher (ZRS): Hochverfügbare Azure Storage-Anwendungen](storage-redundancy-zrs.md)
- [Georedundanter Speicher (GRS): Regionsübergreifende Replikation für Azure Storage](storage-redundancy-grs.md)
- [Skalierbarkeits- und Leistungsziele für Azure-Speicher](storage-scalability-targets.md)
- [Entwerfen hochverfügbarer Anwendungen mithilfe von RA-GRS-Speicher](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure Storage Redundancy Options and Read Access Geo Redundant Storage](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) (Redundanzoptionen für Microsoft Azure Storage und Lesezugriff auf georedundanten Speicher)
- [SOSP-Bericht: Azure Storage: A highly available cloud storage service with strong consistency](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx) (Hochverfügbarer Cloudspeicherdienst mit starker Konsistenz)
