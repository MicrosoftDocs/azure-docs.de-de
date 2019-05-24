---
title: Includedatei
description: Includedatei
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 33dff710d83bd12a8db343a89c6e4576d1397ba7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114574"
---
Nein: Das Entfernen eines Serverendpunkts entspricht nicht dem Neustart eines Servers! Das Entfernen und erneute Erstellen des Serverendpunkts ist fast nie die optimale Lösung für das Beheben von Problemen mit der Synchronisierung, dem Cloudtiering oder anderen Aspekten der Azure-Dateisynchronisierung. Das Entfernen eines Serverendpunkts ist ein destruktiver Vorgang, der möglicherweise zu Datenverlust führen kann, wenn sich mehrstufige Dateien außerhalb des Endpunktnamespace befinden (siehe [Warum existieren mehrstufige Dateien außerhalb des Endpunktnamespace](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint)) oder wenn Dateien für mehrstufige Dateien innerhalb des Endpunktnamespace unzugänglich sind. Diese Probleme werden nicht dadurch gelöst, dass der Serverendpunkt neu erstellt wird. Auf Ihrem Serverendpunkt sind möglicherweise auch dann mehrstufige Dateien vorhanden, wenn Sie das Cloudtiering nie aktiviert haben. Aus diesem Grund wird empfohlen, dass Sie den Serverendpunkt nur entfernen, wenn Sie die Azure-Dateisynchronisierung mit diesem spezifischen Ordner nicht mehr verwenden möchten oder von einem Microsoft-Supportmitarbeiter ausdrücklich dazu aufgefordert wurden. Weitere Informationen zum Entfernen von Serverendpunkten finden Sie unter [Entfernen eines Serverendpunkts](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    