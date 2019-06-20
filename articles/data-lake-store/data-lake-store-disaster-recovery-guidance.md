---
title: Leitfaden zur Notfallwiederherstellung für Azure Data Lake Storage Gen1 | Microsoft-Dokumentation
description: Leitfaden zur Notfallwiederherstellung für Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: twooley
ms.openlocfilehash: b3f1888a73baf2b7f9efa9f5e7cdb3305aa9f90d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60878289"
---
# <a name="disaster-recovery-guidance-for-data-in-azure-data-lake-storage-gen1"></a>Leitfaden zur Notfallwiederherstellung für Daten in Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 bietet lokal redundanten Speicher (Locally-Redundant Storage, LRS). Die Daten in Ihrem Data Lake Storage Gen1-Konto sind daher über automatisierte Replikate vor vorübergehenden Hardwareausfällen innerhalb eines Rechenzentrums geschützt. So wird für Dauerhaftigkeit und Hochverfügbarkeit gesorgt, und die Vereinbarung zum Servicelevel (SLA) für Data Lake Storage Gen1 wird erfüllt. In diesem Artikel wird beschrieben, wie Sie Ihre Daten weiter vor selten auftretenden regionsweiten Ausfällen oder dem versehentlichen Löschen schützen.

## <a name="disaster-recovery-guidance"></a>Leitfäden zur Notfallwiederherstellung
Es ist wichtig für jeden Kunden, den eigenen Notfallwiederherstellungsplan zu erstellen. Lesen Sie die Informationen in diesem Artikel, um Ihren Wiederherstellungsplan zu erstellen. Hier sind einige Ressourcen angegeben, die beim Erstellen Ihres eigenen Plans hilfreich sind.

* [Notfallwiederherstellung und Hochverfügbarkeit für Azure-Anwendungen](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)
* [Technischer Leitfaden zur Resilienz in Azure](../resiliency/resiliency-technical-guidance.md)

### <a name="best-practices"></a>Bewährte Methoden
Es empfiehlt sich, Ihre kritischen Daten in ein anderes Data Lake Storage Gen1-Konto in einer anderen Region zu kopieren und die Häufigkeit des Vorgangs an die Anforderungen Ihres Plans für die Notfallwiederherstellung anzupassen. Es gibt viele verschiedene Methoden zum Kopieren von Daten, z.B. [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) oder [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md). Azure Data Factory ist ein nützlicher Dienst zum regelmäßigen Erstellen und Bereitstellen von Pipelines für die Datenverschiebung.

Im Falle eines regionalen Ausfalls können Sie auf Ihre Daten in der Region zugreifen, in die die Daten kopiert wurden. Über das [Dashboard zur Azure-Dienstintegrität](https://azure.microsoft.com/status/) können Sie den weltweiten Azure-Dienststatus ermitteln.

## <a name="data-corruption-or-accidental-deletion-recovery-guidance"></a>Anleitung zur Wiederherstellung bei Datenbeschädigung oder unbeabsichtigtem Löschen
Dank automatisierter Replikate sorgt Data Lake Storage Gen1 zwar für Datenresilienz, die Lösung kann aber nicht verhindern, dass Ihre Anwendungen (oder Entwickler/Benutzer) Daten beschädigen oder versehentlich löschen.

### <a name="best-practices"></a>Bewährte Methoden
Zur Verhinderung des versehentlichen Löschens empfiehlt es sich, geeignete Zugriffsrichtlinien für Ihr Data Lake Storage Gen1-Konto festzulegen.  Dies beinhaltet auch die Anwendung von [Azure-Ressourcensperren](../azure-resource-manager/resource-group-lock-resources.md), um wichtige Ressourcen zu sperren, sowie die Anwendung einer Zugriffssteuerung auf Konto- und Dateiebene mithilfe der verfügbaren [Data Lake Storage Gen1-Sicherheitsfeatures](data-lake-store-security-overview.md). Darüber hinaus ist es ratsam, routinemäßig Kopien Ihrer kritischen Daten per [ADLCopy](data-lake-store-copy-data-azure-storage-blob.md), [Azure PowerShell](data-lake-store-get-started-powershell.md) oder [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) in einem anderen Data Lake Storage Gen1-Konto, Ordner oder Azure-Abonnement zu erstellen.  Diese Vorgehensweise kann verwendet werden, um Daten nach einer Beschädigung oder nach dem Löschen wiederherzustellen. Azure Data Factory ist ein nützlicher Dienst zum regelmäßigen Erstellen und Bereitstellen von Pipelines für die Datenverschiebung.

Organisationen können auch die [Diagnoseprotokollierung](data-lake-store-diagnostic-logs.md) für ihr Data Lake Storage Gen1-Konto aktivieren, um Überwachungsdaten zum Datenzugriff zu erfassen. Diese Daten liefern Informationen dazu, wer ggf. eine Datei gelöscht oder aktualisiert hat.

## <a name="next-steps"></a>Nächste Schritte
* [Erste Schritte mit Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Schützen von Daten in Data Lake Storage Gen1](data-lake-store-secure-data.md)

