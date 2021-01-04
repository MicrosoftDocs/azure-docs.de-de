---
title: include file
description: include file
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 29518fb971649087d8a5afa39d69c7fc2c014f98
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330980"
---
| Resource | Ziel | Harte Grenze |
|----------|--------------|------------|
| Speichersynchronisierungsdienste pro Region | 100 Speichersynchronisierungsdienste | Ja |
| Synchronisierungsgruppen pro Speichersynchronisierungsdienst | 200 Synchronisierungsgruppen | Ja |
| Registrierte Server pro Speichersynchronisierungsdienst | 99 Server | Ja |
| Cloudendpunkte pro Synchronisierungsgruppe | 1 Cloudendpunkt | Ja |
| Serverendpunkte pro Synchronisierungsgruppe | 100 Serverendpunkte | Ja |
| Serverendpunkte pro Server | 30 Serverendpunkte | Ja |
| Dateisystemobjekte (Verzeichnisse und Dateien) pro Synchronisierungsgruppe | 100 Millionen Objekte | Nein |
| Maximale Anzahl von Dateisystemobjekten (Verzeichnisse und Dateien) in einem Verzeichnis | 5 Millionen Objekte | Ja |
| Maximale Sicherheitsbeschreibung des Objekts (Verzeichnisse und Dateien) | 64 KiB | Ja |
| Dateigröße | 100 GB | Nein |
| Minimale Dateigröße für die Unterteilung einer Datei | V9 und höher: Basiert auf der Größe des Dateisystemclusters (doppelte Größe des Dateisystemclusters). Wenn die Größe des Dateisystemclusters z. B. 4 KB beträgt, ist die minimale Dateigröße 8 KB.<br> V8 und älter: 64 KiB  | Ja |

> [!Note]  
> Ein Endpunkt für Azure-Dateisynchronisierung kann auf die Größe einer Azure-Dateifreigabe hochskaliert werden. Wenn die maximale Größe der Azure-Dateifreigabe erreicht ist, kann keine Synchronisierung durchgeführt werden.
