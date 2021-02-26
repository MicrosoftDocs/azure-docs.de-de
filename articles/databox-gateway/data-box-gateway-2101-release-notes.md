---
title: Versionshinweise zu Azure Data Box Gateway 2101 | Microsoft-Dokumentation
description: In diesem Artikel werden schwerwiegende ungelöste Probleme und Lösungen für Azure Data Box Gateway mit dem Release 2101 beschrieben.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 01/29/2021
ms.author: alkohli
ms.openlocfilehash: 510f2677673363791ab5eb0f2c4dbcd25b697934
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072498"
---
# <a name="azure-data-box-gateway-2101-release-notes"></a>Versionshinweise zu Azure Data Box Gateway 2101

In den folgenden Versionshinweisen werden die schwerwiegenden offenen und die gelösten Probleme für das Release 2101 von Azure Data Box Gateway beschrieben.

Die Anmerkungen zu dieser Version werden fortlaufend aktualisiert. Wenn schwerwiegende Probleme festgestellt werden, die eine Problemumgehung erfordern, werden sie hinzugefügt. Lesen Sie vor der Bereitstellung von Azure Data Box Gateway die Informationen in den Versionshinweisen sorgfältig durch.  

Dieses Release entspricht den folgenden Softwareversionen:

- **Data Box Gateway 2101 (1.6.1475.2528)** : KB 4603462

> [!NOTE]
> Das Update 2101 kann nur auf Geräte angewendet werden, auf denen allgemein verfügbare Versionen (oder höhere Versionen) der Software ausgeführt werden.

## <a name="whats-new"></a>Neues

Dieses Release enthält die folgenden Fehlerbehebungen:

- **Uploadproblem:** Dieses Release behebt ein Uploadproblem, bei dem fehlerbedingte Neustarts von Uploads zu einer langsameren Uploadabschlussrate führen können. Dieses Problem kann auftreten, wenn ein Dataset hochgeladen wird, das hauptsächlich aus Dateien besteht, die im Verhältnis zur verfügbaren Bandbreite sehr groß sind, insbesondere, aber nicht ausschließlich dann, wenn die Bandbreiteneinschränkung aktiviert ist. Diese Änderung sorgt dafür, dass dem Abschluss von Uploads genügend Zeit eingeräumt wird, bevor der Upload für eine bestimmte Datei neu gestartet wird.

Dieses Release enthält auch die folgenden Updates:

- Alle kumulativen Windows-Updates und .NET Framework-Updates, die bis einschließlich Oktober 2020 veröffentlicht wurden.
- Die statische IP-Adresse für Azure Data Box Gateway bleibt auch nach Softwareupdates erhalten.

## <a name="known-issues-in-this-release"></a>Bekannte Probleme in dieser Version

Für dieses Release werden keine neuen Probleme aufgeführt. Alle in den Versionshinweisen enthaltenen Probleme wurden aus früheren Releases übernommen. Um eine Liste der bekannten Probleme anzuzeigen, wechseln Sie zu [Bekannte Probleme in der Version für die allgemeine Verfügbarkeit](data-box-gateway-release-notes.md#known-issues-in-ga-release).

## <a name="next-steps"></a>Nächste Schritte

- [Vorbereiten der Bereitstellung von Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
