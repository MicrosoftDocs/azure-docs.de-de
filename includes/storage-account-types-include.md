---
title: include file
description: include file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/05/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b8a72640ebe8daffedb196456df7d40bc41b7b1b
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95561300"
---
Azure Storage bietet mehrere Arten von Speicherkonten. Jeder Typ unterstützt unterschiedliche Features und verfügt über ein eigenes Preismodell. Informieren Sie sich vor dem Erstellen eines Speicherkontos genau über diese Unterschiede, um den Kontotyp zu ermitteln, der sich für Ihre Anwendungen am besten eignet. Folgende Speicherkontotypen stehen zur Verfügung:

- **Konten vom Typ „Allgemein v2“:** Grundlegender Speicherkontotyp für Blobs, Dateien, Warteschlangen und Tabellen. Empfohlen für die meisten Azure Storage-Szenarien.
- **Konten vom Typ „Allgemein v1“:** Legacy-Speicherkontotyp für Blobs, Dateien, Warteschlangen und Tabellen. Verwenden Sie nach Möglichkeit Konten vom Typ „Allgemein v2“.
- **BlockBlobStorage-Konten**: Speicherkonten mit Premium-Leistungsmerkmalen für Blockblobs und Anfügeblobs. Empfohlen für Szenarien mit hohen Transaktionsraten oder Szenarien, die kleinere Objekte verwenden oder aber eine gleichbleibend geringe Speicherlatenz erfordern.
- **FileStorage-Konten**: Reine Dateispeicherkonten mit Premium-Leistungsmerkmalen Empfohlen für Unternehmens- oder Hochleistungsanwendungen
- **BlobStorage-Konten**: Ältere reine Blobspeicherkonten. Verwenden Sie nach Möglichkeit Konten vom Typ „Allgemein v2“.

Die folgende Tabelle beschreibt die Speicherkontotypen und ihre jeweiligen Funktionen:

| Speicherkontotyp | Unterstützte Dienste                       | Unterstützte Leistungsstufen      | Unterstützte Zugriffsebenen         | Replikationsoptionen               | Bereitstellungsmodell<div role="complementary" aria-labelledby="deployment-model"><sup>1</sup></div> | Verschlüsselung<div role="complementary" aria-labelledby="encryption"><sup>2</sup></div> |
|----------------------|------------------------------------------|-----------------------------|--------------------------------|-----------------------------------|------------------------------|------------------------|
| Allgemein v2   | Blob, Datei, Warteschlange, Tabelle, Datenträger und Data Lake Gen2<div role="complementary" aria-labelledby="data-lake-gen2"><sup>6</sup></div>      | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | Heiß, Kalt, Archiv<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS, ZRS, GZRS (Vorschau), RA-GZRS (Vorschau)<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div> | Ressourcen-Manager             | Verschlüsselt              |
| Allgemein v1   | Blob, Datei, Warteschlange, Tabelle und Datenträger       | Standard, Premium<div role="complementary" aria-labelledby="premium-performance"><sup>5</sup></div> | –                            | LRS, GRS, RA-GRS                  | Resource Manager, klassisch    | Verschlüsselt              |
| BlockBlobStorage   | Blob (nur Blockblobs und Anfügeblobs) | Premium                       | –                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Ressourcen-Manager             | Verschlüsselt              |
| FileStorage   | Nur Datei | Premium                       | –                            | LRS, ZRS<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>4</sup></div>                               | Ressourcen-Manager             | Verschlüsselt              |
| BlobStorage         | Blob (nur Blockblobs und Anfügeblobs) | Standard                      | Heiß, Kalt, Archiv<div role="complementary" aria-labelledby="archive"><sup>3</sup></div> | LRS, GRS, RA-GRS                  | Ressourcen-Manager             | Verschlüsselt              |

<div id="deployment-model"><sup>1</sup>Die Verwendung des Azure Resource Manager-Bereitstellungsmodells wird empfohlen. Speicherkonten, die das klassische Bereitstellungsmodell verwenden, können an einigen Standorten weiterhin erstellt werden, und vorhandene klassische Konten werden weiterhin unterstützt. Weitere Informationen finden Sie unter <a href="/azure/azure-resource-manager/resource-manager-deployment-model">Azure Resource Manager-Bereitstellung im Vergleich zur klassischen Bereitstellung: Grundlegendes zu Bereitstellungsmodellen und zum Status von Ressourcen</a>.</div><br/>

<div id="encryption"><sup>2</sup>Alle Speicherkonten sind per Storage Service Encryption (SSE) für ruhende Daten verschlüsselt. Weitere Informationen finden Sie unter <a href="/azure/storage/common/storage-service-encryption">Azure Storage Service Encryption für ruhende Daten</a>.</div><br/>

<div id="archive"><sup>3</sup> Für die Archivspeicherebene und das Blobebenentiering werden nur Blockblobs unterstützt. Die Ebene „Archiv“ ist nur auf Ebene der einzelnen Blobs verfügbar, nicht auf Ebene des Speicherkontos. Weitere Informationen finden Sie unter <a href="/azure/storage/blobs/storage-blob-storage-tiers">Azure Blob Storage: Speicherebenen „Premium“ (Vorschauversion), „Heiß“, „Kalt“ und „Archiv“</a>.</div><br/>

<div id="zone-redundant-storage"><sup>4</sup>Zonenredundanter Speicher (ZRS) und geozonenredundanter Speicher (GZRS/RA-GZRS) (Vorschau) sind in bestimmten Regionen nur für Standardkonten des Typs „General Purpose V2“ sowie für Konten des Typs „BlockBlobStorage“ und „FileStorage“ verfügbar. Weitere Informationen zu den Azure Storage-Redundanzoptionen finden Sie unter <a href="/azure/storage/common/storage-redundancy">Azure Storage-Redundanz</a>.</div><br/>

<div id="premium-performance"><sup>5</sup>Premium-Leistung für Konten vom Typ „Allgemein v2“ und „Allgemein v1“ ist nur für Datenträger und Seitenblob verfügbar. Premium-Leistung für Block- oder Anfügeblobs ist nur für BlockBlobStorage-Konten verfügbar. Premium-Leistung für Dateien ist nur für FileStorage-Konten verfügbar.</div><br/>

<div id="data-lake-gen2"><sup>6</sup>Azure Data Lake Storage Gen2 setzt auf Azure Blob Storage auf und bietet eine Reihe von Funktionen für die Big Data-Analyse. Data Lake Storage Gen2 wird nur für Speicherkonten vom Typ „GPv2“ mit aktiviertem hierarchischen Namespace unterstützt. Weitere Informationen zu Data Lake Storage Gen2 finden Sie in der <a href="/azure/storage/blobs/data-lake-storage-introduction">Einführung in Azure Data Lake Storage Gen2</a>.</div>