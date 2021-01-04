---
title: include file
description: include file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f730b2abad244132b06c0fa21f07a171559d22f7
ms.sourcegitcommit: ac7029597b54419ca13238f36f48c053a4492cb6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/29/2020
ms.locfileid: "96309947"
---
Nein: Das Entfernen eines Serverendpunkts entspricht nicht dem Neustart eines Servers! Das Entfernen und erneute Erstellen des Serverendpunkts ist fast nie die optimale Lösung für das Beheben von Problemen mit der Synchronisierung, dem Cloudtiering oder anderen Aspekten der Azure-Dateisynchronisierung. Das Entfernen eines Serverendpunkts ist ein destruktiver Vorgang. Es kann zu Datenverlust führen, wenn mehrstufige Dateien außerhalb des Namespaces des Serverendpunkts vorhanden sind. Unter [Warum sind mehrstufige Dateien außerhalb des Serverendpunkt-Namespaces vorhanden?](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint) finden Sie weitere Informationen. Oder es kann bei mehrstufigen Dateien innerhalb des Namespaces des Serverendpunkts zu nicht zugänglichen Dateien führen. Diese Probleme werden nicht dadurch gelöst, dass der Serverendpunkt neu erstellt wird. Auf Ihrem Serverendpunkt sind möglicherweise auch dann mehrstufige Dateien vorhanden, wenn Sie das Cloudtiering nie aktiviert haben. Aus diesem Grund wird empfohlen, den Serverendpunkt nur zu entfernen, wenn Sie die Azure-Dateisynchronisierung mit diesem spezifischen Ordner nicht mehr verwenden möchten oder von einem Microsoft-Supportmitarbeiter ausdrücklich dazu aufgefordert wurden. Weitere Informationen zum Entfernen von Serverendpunkten finden Sie unter [Entfernen eines Serverendpunkts](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint).    
