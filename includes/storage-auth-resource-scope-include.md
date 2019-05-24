---
title: Includedatei
description: Includedatei
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 518c57bc3327511b70deef143826f2a1b9df8639
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66115570"
---
Bevor Sie einem Sicherheitsprinzipal eine RBAC-Rolle zuweisen, legen Sie den Zugriffsbereich fest, den der Sicherheitsprinzipal haben soll. Es hat sich als am besten bewährt, stets nur den kleinstmöglichen Umfang an Zugriffsrechten zu gewähren.

In der folgenden Liste werden die Ebenen beschrieben, auf denen Sie den Zugriff auf Azure-Blob- und -Warteschlangenressourcen einschränken können, beginnend mit dem kleinstmöglichen Umfang:

- **Ein einzelner Container**. Bei diesem Umfang gilt eine Rollenzuweisung für alle Blobs im Container sowie für Containereigenschaften und Metadaten.
- **Eine einzelne Warteschlange**. Bei diesem Umfang gilt eine Rollenzuweisung für Nachrichten in der Warteschlange sowie für Warteschlangeneigenschaften und Metadaten.
- **Das Speicherkonto**. Bei diesem Umfang gilt eine Rollenzuweisung für alle Container und deren Blobs oder für alle Warteschlangen und deren Nachrichten.
- **Die Ressourcengruppe**. Bei diesem Umfang gilt eine Rollenzuweisung für alle Container oder Warteschlangen in allen Speicherkonten der Ressourcengruppe.
- **Das Abonnement**. Bei diesem Umfang gilt eine Rollenzuweisung für alle Container oder Warteschlangen in allen Speicherkonten in allen Ressourcengruppen des Abonnements.

> [!IMPORTANT]
> Wenn Ihr Abonnement einen Azure DataBricks-Namespace enthält, werden Rollen, die im Abonnementumfang zugewiesen wurden, so gesperrt, dass sie keinen Zugriff auf Blob- und Warteschlangendaten gewähren können.
