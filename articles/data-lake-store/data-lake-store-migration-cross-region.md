---
title: Regionsübergreifende Migration in Azure Data Lake Storage Gen1 | Microsoft Docs
description: Erfahren Sie mehr über die regionsübergreifende Migration für Azure Data Lake Storage Gen1.
services: data-lake-store
documentationcenter: ''
author: swums
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: stewu
ms.openlocfilehash: 0bf0843314f38c0de28820c82e95b7921297bf40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60518454"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>Regionsübergreifendes Migrieren von Azure Data Lake Storage Gen1

Wenn Azure Data Lake Storage Gen1 in neuen Regionen verfügbar wird, möchten Sie vielleicht eine einmalige Migration durchführen, um die neue Region zu nutzen. Erfahren Sie, was bei der Planung und Durchführung der Migration zu berücksichtigen ist.

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Azure-Abonnement**. Weitere Informationen finden Sie unter [Erstellen Sie noch heute Ihr kostenloses Azure-Konto](https://azure.microsoft.com/pricing/free-trial/).
* **Ein Data Lake Storage Gen1-Konto in zwei verschiedenen Regionen**. Weitere Informationen finden Sie unter [Erste Schritte mit Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Azure Data Factory** Weitere Informationen finden Sie unter [Einführung in Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Überlegungen zur Migration

Ermitteln Sie zuerst die Migrationsstrategie, die am besten zu Ihrer Anwendung passt, die Daten in Data Lake Storage Gen1 schreibt, liest oder verarbeitet. Berücksichtigen Sie bei der Auswahl einer Strategie die Verfügbarkeitsanforderungen Ihrer Anwendung und die bei einer Migration entstehende Ausfallzeit. Die einfachste Vorgehensweise könnte z. B. die Verwendung des Cloudmigrationsmodells „Lift & Shift“ sein. Bei dieser Vorgehensweise halten Sie die Anwendung in Ihrer vorhandenen Region an, während Ihre gesamten Daten in die neue Region kopiert werden. Wenn der Kopiervorgang abgeschlossen ist, können Sie Ihre Anwendung in der neuen Region wieder aufnehmen; löschen Sie anschließend das alte Data Lake Storage Gen1-Konto. Eine Ausfallzeit während der Migration ist erforderlich.

Um Ausfallzeiten zu reduzieren, können Sie sofort damit beginnen, neue Daten in der neuen Region zu erfassen. Wenn die mindestens erforderlichen Daten vorhanden sind, führen Sie die Anwendung in der neuen Region aus. Im Hintergrund fahren Sie mit dem Kopieren älterer Daten aus dem alten Data Lake Storage Gen1-Konto in das neue Data Lake Storage Gen1-Konto in der neuen Region fort. Mithilfe dieses Ansatzes können Sie den Wechsel in die neue Region mit wenig Ausfallzeit durchführen. Nachdem alle älteren Daten kopiert wurden, löschen Sie das alte Data Lake Storage Gen1-Konto.

Weitere wichtige Details, die beim Planen der Migration zu berücksichtigen sind:

* **Datenvolumen**. Die Datenmenge (in Gigabyte, die Anzahl der Dateien und Ordnern usw.) wirkt sich auf die Zeit und Ressourcen aus, die für die Migration benötigt werden.

* **Data Lake Storage Gen1-Kontoname**. Der neue Kontoname in der neuen Region muss global eindeutig sein. Beispielsweise könnte der Name Ihres alten Data Lake Storage Gen1-Kontos in der Region „USA, Osten 2“ „contosoeastus2.azuredatalakestore.net“ sein. Sie könnten Ihr neues Data Lake Storage Gen1-Konto in der Region „EU, Norden“ z.B. „contosonortheu.azuredatalakestore.net“ nennen.

* **Tools**. Es wird empfohlen, die [Kopieraktivität von Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) zu verwenden, um Data Lake Storage Gen1-Dateien zu kopieren. Data Factory unterstützt das Verschieben von Daten mit hoher Leistung und Zuverlässigkeit. Bedenken Sie, dass Data Factory nur die Ordnerhierarchie und den Inhalt der Dateien kopiert. Sie müssen manuell alle Zugriffssteuerungslisten (access control lists, ACLs), die Sie im alten Konto verwenden, auf das neue Konto anwenden. Weitere Informationen, einschließlich der Leistungsziele für die günstigsten Szenarios, finden Sie unter [Handbuch zur Leistung und Optimierung der Kopieraktivität](../data-factory/copy-activity-performance.md). Wenn Sie Daten schneller kopieren möchten, müssen Sie möglicherweise zusätzliche Einheiten für Clouddatenverschiebungen verwenden. Einige andere Tools, z.B. AdlCopy, unterstützen kein Kopieren von Daten zwischen Regionen.  

* **Bandbreitengebühren**. [Bandbreitengebühren](https://azure.microsoft.com/pricing/details/bandwidth/) werden geltend gemacht, da Daten aus einer Azure-Region hinaus übertragen werden.

* **ACLs für Ihre Daten**. Sichern Sie Ihre Daten in der neuen Region durch Anwenden von ACLs auf Dateien und Ordner. Weitere Informationen finden Sie unter [Schützen von Daten, die in Azure Data Lake Storage Gen1 gespeichert sind](data-lake-store-secure-data.md). Es wird empfohlen, dass Sie die Migration zum Aktualisieren und Anpassen Ihrer ACLs verwenden. Möglicherweise möchten die Einstellungen, die mit Ihrer aktuellen Einstellungen ähnliche verwenden. Sie können die ACLs, die auf alle Dateien angewendet werden, mithilfe vom Azure-Portal, [PowerShell-Cmdlets](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission) oder SDKs anzeigen.  

* **Speicherort von Analysediensten**. Für optimale Leistung sollten sich Ihre Analysedienste, wie etwa Azure Data Lake Analytics oder Azure HDInsight, in der gleichen Region wie Ihre Daten befinden.  

## <a name="next-steps"></a>Nächste Schritte
* [Übersicht über Azure Data Lake Storage Gen1](data-lake-store-overview.md)
