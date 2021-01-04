---
title: include file
description: include file
services: storage
author: twooley
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: twooley
ms.custom: include file
ms.openlocfilehash: 7098f23e9b5b6f56fbbe761335afc65375aea680
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509182"
---
**Azure Data Lake Storage Gen2** ist kein dedizierter Dienst- oder Speicherkontotyp. Es handelt sich dabei um das aktuelle Release von Funktionen für Big Data-Analysen.  Diese Funktionen sind in einem Speicherkonto vom Typ „Allgemein v2“ oder „BlockBlobStorage“ verfügbar, und Sie können sie durch Aktivieren des Features **Hierarchischer Namespace** des Kontos abrufen. Informationen zu Skalierungszielen finden Sie in den folgenden Artikeln: 

- [Skalierbarkeitsziele für Blob Storage](../articles/storage/blobs/scalability-targets.md#scale-targets-for-blob-storage)
- [Skalierbarkeitsziele für Standardspeicherkonten](../articles/storage/common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#scale-targets-for-standard-storage-accounts)

**Azure Data Lake Storage Gen1** ist ein dedizierter Dienst. Es handelt sich dabei um ein unternehmensweites riesiges Repository für Big Data-Analyseworkloads. Sie können mit Data Lake Storage Gen1 Daten von beliebiger Größe, Art und Erfassungsgeschwindigkeit zur Durchführung operativer und explorativer Analysen an einem einzigen Ort erfassen. Es gibt keine Beschränkung für die Datenmenge, die Sie in einem Data Lake Storage Gen1-Konto speichern können.

| **Ressource** | **Begrenzung** | **Kommentare** |
| --- | --- | --- |
| Maximale Anzahl von Data Lake Storage Gen1-Konten pro Abonnement pro Region |10 | Wenden Sie sich an den Support, um eine Erhöhung dieses Grenzwerts anzufordern. |
| Maximale Anzahl von Zugriffs-ACLs pro Datei oder Ordner |32 | Dies ist eine harte Grenze. Verwenden von Gruppen zum Verwalten des Zugriffs mit weniger Einträgen. |
| Maximale Anzahl von standardmäßiger ACLs pro Datei oder Ordner |32 | Dies ist eine harte Grenze. Verwenden von Gruppen zum Verwalten des Zugriffs mit weniger Einträgen. |