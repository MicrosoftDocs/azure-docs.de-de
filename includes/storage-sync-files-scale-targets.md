---
title: include file
description: include file
services: storage
author: wmgries
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: wgries
ms.custom: include file
ms.openlocfilehash: 2614c9290bf31813d59ee753a31622bccf0682b8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178298"
---
| Resource | Ziel | Harte Grenze |
|----------|--------------|------------|
| Speichersynchronisierungsdienste pro Region | 20 Speichersynchronisierungsdienste | Ja |
| Synchronisierungsgruppen pro Speichersynchronisierungsdienst | 100 Synchronisierungsgruppen | Ja |
| Registrierte Server pro Speichersynchronisierungsdienst | 99 Server | Ja |
| Cloudendpunkte pro Synchronisierungsgruppe | 1 Cloudendpunkt | Ja |
| Serverendpunkte pro Synchronisierungsgruppe | 50 Serverendpunkte | Nein |
| Serverendpunkte pro Server | 30 Serverendpunkte | Ja |
| Dateisystemobjekte (Verzeichnisse und Dateien) pro Synchronisierungsgruppe | 25 Millionen Objekte | Nein |
| Maximale Anzahl von Dateisystemobjekten (Verzeichnisse und Dateien) in einem Verzeichnis | 1 Million Objekte | Ja |
| Maximale Sicherheitsbeschreibung des Objekts (Verzeichnisse und Dateien) | 64 KiB | Ja |
| Dateigröße | 100 GB | Nein |
| Minimale Dateigröße für die Unterteilung einer Datei | 64 KiB | Ja |
| Gleichzeitige Synchronisierungssitzungen | Mindestens V4-Agent: Das Limit variiert basierend auf verfügbaren Systemressourcen. <BR> V3-Agent: Zwei aktive Synchronisierungssitzungen pro Prozessor oder maximal acht aktive Synchronisierungssitzungen pro Server. | Ja

> [!Note]  
> Ein Endpunkt für Azure-Dateisynchronisierung kann auf die Größe einer Azure-Dateifreigabe zentral hochskaliert werden. Wenn die maximale Größe der Azure-Dateifreigabe erreicht ist, kann keine Synchronisierung durchgeführt werden.