---
title: include file
description: include file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/04/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 372342611265640a2a64100f003880a430d61ca0
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96620941"
---
In der Vorschau weist NFS die folgenden Einschränkungen auf:

- NFS 4.1 unterstützt derzeit nur die meisten Features aus der [Protokollspezifikation](https://tools.ietf.org/html/rfc5661). Einige Features wie Delegierungen und Rückrufe aller Art, Upgrades und Downgrades von Sperren, Kerberos-Authentifizierung und Verschlüsselung werden nicht unterstützt.
- Wenn sich die meisten Ihrer Anforderungen schwerpunktmäßig auf Metadaten beziehen, ist die Wartezeit im Vergleich zu Lese-/Schreib-/Aktualisierungsvorgängen schlechter.
- Es muss ein neues Speicherkonto erstellt werden, um eine NFS-Freigabe zu erstellen.
- Nur die REST-APIs der Verwaltungsebene werden unterstützt. REST-APIs der Datenebene sind nicht verfügbar. Das bedeutet, dass Tools wie der Speicher-Explorer mit NFS-Freigaben nicht funktionieren und Sie keine NFS-Freigabedaten im Azure-Portal durchsuchen können.
- AzCopy wird derzeit nicht unterstützt.
- Nur für den Premium-Tarif verfügbar.
- NFS-Freigaben akzeptieren nur numerische UID/GID. Um zu verhindern, dass Ihre Clients eine alphanumerische UID/GID senden, sollten Sie die ID-Zuordnung deaktivieren.
- Freigaben können nur aus einem Speicherkonto auf einem einzelnen virtuellen Computer eingebunden werden, wenn private Verbindungen verwendet werden. Der Versuch, Freigaben aus anderen Speicherkonten einzubinden, schlägt fehl.

### <a name="azure-storage-features-not-yet-supported"></a>Noch nicht unterstützte Azure Storage-Funktionen

Auch die folgenden Features von Azure Files sind bei NFS-Freigaben nicht verfügbar:

- Identitätsbasierte Authentifizierung
- Azure Backup-Unterstützung
- Momentaufnahmen
- Vorläufiges Löschen
- Vollständige Unterstützung der Verschlüsselung während der Übertragung (Details finden Sie unter [NFS-Sicherheit](../articles/storage/files/storage-files-compare-protocols.md#security))
- Azure-Dateisynchronisierung (nur für Windows-Clients verfügbar, die von NFS 4.1 nicht unterstützt werden)
