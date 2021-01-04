---
title: Anpassen der Größe des Kapazitätspools oder eines Volumes für Azure NetApp Files | Microsoft-Dokumentation
description: Erfahren Sie, wie die Größe eines Kapazitätspools oder eines Volumes geändert wird. Durch eine Größenänderung des Kapazitätspools wird die erworbene Azure NetApp Files-Kapazität geändert.
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
ms.topic: how-to
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 62bf154c1dbf1a0d3f12e2cef916b37059ce985b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012475"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Ändern der Größe eines Kapazitätspools oder Volumes
Sie können die Größe eines Kapazitätspools oder eines Volumes nach Bedarf anpassen. 

## <a name="resize-the-capacity-pool"></a>Anpassen der Größe des Kapazitätspools 

Sie können die Größe des Kapazitätspools in 1-TiB-Schritten erhöhen oder verringern. Allerdings kann die Größe des Kapazitätspools nicht kleiner als 4 TiB sein. Durch eine Größenänderung des Kapazitätspools wird die erworbene Azure NetApp Files-Kapazität geändert.

1. Klicken Sie auf dem Blatt „Manage NetApp Account“ (NetApp-Konto verwalten) auf den Kapazitätspool, dessen Größe Sie anpassen möchten. 
2. Klicken Sie mit der rechten Maustaste auf den Namen des Kapazitätspools, oder klicken Sie am Ende der Zeile mit dem Kapazitätspool auf das Symbol „...“, um das Kontextmenü anzuzeigen. 
3. Mithilfe der Kontextmenüoptionen können Sie die Größe des Kapazitätspools ändern oder ihn löschen.

## <a name="resize-a-volume"></a>Ändern der Größe eines Volumes

Sie können die Größe eines Volumes nach Bedarf anpassen. Der Kapazitätsverbrauch eines Volumes wird mit der bereitgestellten Kapazität des dazugehörigen Pools verrechnet.

1. Klicken Sie auf dem Blatt „Manage NetApp Account“ auf **Volumes**. 
2. Klicken Sie mit der rechten Maustaste auf den Namen des Volumes, dessen Größe Sie anpassen möchten, oder klicken Sie auf am Ende der Zeile mit dem Volume auf das Symbol „...“, um das Kontextmenü anzuzeigen.
3. Mithilfe der Kontextmenüoptionen können Sie die Größe des Volumes ändern oder es löschen.

## <a name="next-steps"></a>Nächste Schritte

- [Einrichten eines Kapazitätspools](azure-netapp-files-set-up-capacity-pool.md)
- [Verwalten eines manuellen QoS-Kapazitätspools](manage-manual-qos-capacity-pool.md)
- [Dynamisches Ändern der Dienstebene eines Volumes](dynamic-change-volume-service-level.md) 