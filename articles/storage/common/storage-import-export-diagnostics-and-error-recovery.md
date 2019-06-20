---
title: Diagnose und Fehlerbehebung für Azure Import/Export-Aufträge | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die ausführliche Protokollierung für Aufträge des Microsoft Azure Import/Export-Diensts aktivieren.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 306b94fbe23e0ae92dcd59f7a87b7bb58ef7c3b6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478809"
---
# <a name="diagnostics-and-error-recovery-for-azure-importexport-jobs"></a>Diagnose und Fehlerbehebung für Azure Import/Export-Aufträge
Für jeden verarbeiteten Datenträger wird vom Azure Import/Export-Dienst im zugeordneten Speicherkonto ein Fehlerprotokoll erstellt. Sie können die ausführliche Protokollierung auch aktivieren, indem Sie die `LogLevel`-Eigenschaft `Verbose` festlegen, wenn Sie die Vorgänge [Put Job](/rest/api/storageimportexport/jobs) oder [Update Job Properties](/rest/api/storageimportexport/jobs) aufrufen.

 Standardmäßig werden Protokolle in einen Container mit dem Namen `waimportexport` geschrieben. Sie können einen anderen Namen angeben, indem Sie beim Aufrufen der Vorgänge `Put Job` oder `Update Job Properties` die `DiagnosticsPath`-Eigenschaft festlegen. Die Protokolle werden mit der folgenden Benennungskonvention als Blockblobs gespeichert: `waies/jobname_driveid_timestamp_logtype.xml`.

 Sie können den URI der Protokolle für einen Auftrag abrufen, indem Sie den Vorgang [Get Job](/rest/api/storageimportexport/jobs) aufrufen. Der URI für das ausführliche Protokoll wird in der `VerboseLogUri`-Eigenschaft für jedes Laufwerk zurückgegeben, während der URI für das Fehlerprotokoll in der `ErrorLogUri`-Eigenschaft zurückgegeben wird.

Sie können die Protokollierungsdaten verwenden, um die folgenden Probleme zu ermitteln.

## <a name="drive-errors"></a>Laufwerkfehler

Folgende Fehler sind als Laufwerkfehler klassifiziert:

-   Fehler beim Zugreifen auf oder Lesen der Manifestdatei

-   Fehlerhafte BitLocker-Schlüssel

-   Lese-/Schreibfehler des Laufwerks

## <a name="blob-errors"></a>Blobfehler

Folgende Fehler sind als Blobfehler klassifiziert:

-   Falsche oder in Konflikt stehende Blobs oder Namen

-   Fehlende Dateien

-   Nicht gefundenes Blob

-   Abgeschnittene Dateien (die Dateien auf dem Datenträger sind kleiner als im Manifest angegeben)

-   Beschädigter Dateiinhalt (für Importaufträge, Erkennung per MD5-Prüfsummenfehler)

-   Beschädigte Blobmetadaten und Eigenschaftsdateien (Erkennung per MD5-Prüfsummenfehler)

-   Fehlerhaftes Schema für die Blobeigenschaften bzw. Metadatendateien

Es kann vorkommen, dass einige Teile eines Import- oder Exportauftrags nicht vollständig abgeschlossen werden, während der Gesamtauftrag trotzdem abgeschlossen wird. In diesem Fall können Sie die fehlenden Teile der Daten über das Netzwerk hoch- oder herunterladen, oder Sie können einen neuen Auftrag erstellen, um die Daten zu übertragen. Informationen dazu, wie Sie die Daten über das Netzwerk reparieren, finden Sie unter [Azure Import/Export-Tool – Referenz](storage-import-export-tool-how-to-v1.md).

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden der REST-API des Import/Export-Diensts](storage-import-export-using-the-rest-api.md)
