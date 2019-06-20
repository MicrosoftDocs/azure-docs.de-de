---
title: Metriken für Azure NetApp Files | Microsoft-Dokumentation
description: Enthält eine Beschreibung der Metriken für Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: b-juche
ms.openlocfilehash: 1563b60ef26ac5e4d40f45095d0109dd9dd71570
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61084938"
---
# <a name="metrics-for-azure-netapp-files"></a>Metriken für Azure NetApp Files

Azure NetApp Files verfügt über Metriken für Folgendes: zugeteilter Speicher, tatsächliche Speichernutzung, Volumedurchsatz, IOPS und Latenz. Durch die Analyse dieser Metriken können Sie ein besseres Verständnis des Nutzungsmusters und der Volumeleistung Ihrer NetApp-Konten entwickeln.  

## <a name="capacity_pools"></a>Nutzungsmetriken für Kapazitätspools

- *Zugeordnete Größe des Volumepools*  
    Dies ist die Größe (GiB) des bereitgestellten Kapazitätspools.  
- *Verwendeter zugeordneter Volumepool*  
    Dies ist die Gesamtsumme des Volumekontingents (GiB) eines bestimmten Kapazitätspools (Gesamtsumme der bereitgestellten Größen der Volumes im Kapazitätspool). Diese Größe haben Sie beim Erstellen des Volumes ausgewählt.  
- *Gesamte logische Größe des Volumepools*  
    Dies ist die Gesamtsumme des logischen Speichers (GiB), der in einem Kapazitätspool volumeübergreifend genutzt wird.  
- *Größe der Gesamtmomentaufnahme des Volumepools*  
    Dies ist die Gesamtsumme des inkrementellen logischen Speichers, der von den Momentaufnahmen genutzt wird.  

## <a name="volumes"></a>Nutzungsmetriken für Volumes

- *Zugeordnete Größe des Volumes*   
    Dies ist die Größe des bereitgestellten Volumes (Kontingent) in GiB.  
- *Logische Größe des Volumes*   
    Dies ist der gesamte logische Speicher (GiB), der auf einem Volume genutzt wird. In diesem Größenwert ist der logische Speicherplatz enthalten, der von aktiven Dateisystemen und Momentaufnahmen verwendet wird.  
- *Größe der Volumemomentaufnahme*   
    Dies ist der inkrementelle logische Speicherplatz, der von Momentaufnahmen auf einem Volume verwendet wird.  

## <a name="next-steps"></a>Nächste Schritte

* [Grundlegendes zur Speicherhierarchie von Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Einrichten eines Kapazitätspools](azure-netapp-files-set-up-capacity-pool.md)
* [Erstellen eines Volumes für Azure NetApp Files](azure-netapp-files-create-volumes.md)
