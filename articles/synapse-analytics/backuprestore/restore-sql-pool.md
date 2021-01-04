---
title: Wiederherstellen eines vorhandenen dedizierten SQL-Pools
description: Anleitung zum Wiederherstellen eines vorhandenen dedizierten SQL-Pools.
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 10/29/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 86150107273b247bef2c74e5b8c5272d7148587e
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331991"
---
# <a name="restore-an-existing-dedicated-sql-pool"></a>Wiederherstellen eines vorhandenen dedizierten SQL-Pools

In diesem Artikel erfahren Sie, wie Sie einen vorhandenen dedizierten SQL-Pool in Azure Synapse Analytics im Azure Portal und mit Synapse Studio wiederherstellen können. Dieser Artikel gilt für normale und Geowiederherstellungen. 

## <a name="restore-an-existing-dedicated-sql-pool-through-the-synapse-studio"></a>Wiederherstellen eines vorhandenen dedizierten SQL-Pools mit Synapse Studio

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Navigieren Sie zu Ihrem Synapse-Arbeitsbereich. 
3. Wählen Sie unter „Erste Schritte“ > „Synapse Studio öffnen“ die Option **Öffnen** aus.

    ![ Synapse Studio](../media/sql-pools/open-synapse-studio.png)
4. Wählen Sie im linken Navigationsbereich **Daten** aus.
5. Wählen Sie **Pools verwalten** aus. 
6. Wählen Sie **+ Neu** aus, um einen neuen dedizierten SQL-Pool zu erstellen. 
7. Wählen Sie auf der Registerkarte „Zusätzliche Einstellungen“ einen Wiederherstellungspunkt aus, von dem aus die Wiederherstellung erfolgen soll. 

    Wenn Sie eine Geowiederherstellung durchführen möchten, wählen Sie den Arbeitsbereich und den dedizierten SQL-Pool aus, den Sie wiederherstellen möchten. 

8. Wählen Sie entweder die Option **Automatische Wiederherstellungspunkte** oder **Benutzerdefinierte Wiederherstellungspunkte** aus. 

    ![Wiederherstellungspunkte](../media/sql-pools/restore-point.PNG)

    Wenn der dedizierten SQL-Pool keine automatischen Wiederherstellungspunkte umfasst, warten Sie einige Stunden, oder erstellen Sie einen benutzerdefinierten Wiederherstellungspunkt, bevor Sie eine Wiederherstellung durchführen. Wählen Sie für „Benutzerdefinierte Wiederherstellungspunkte“ einen vorhandenen benutzerdefinierten Wiederherstellungspunkt aus, oder erstellen Sie einen neuen.

    Wenn Sie eine Geosicherung wiederherstellen, wählen Sie einfach den Arbeitsbereich in der Quellregion und den dedizierten SQL-Pool aus, den Sie wiederherstellen möchten. 

9. Klicken Sie auf **Überprüfen + erstellen**.

## <a name="restore-an-existing-dedicated-sql-pool-through-the-azure-portal"></a>Wiederherstellen eines vorhandenen dedizierten SQL-Pools im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Navigieren Sie zum dedizierten SQL-Pool, mit dem Sie die Wiederherstellung durchführen möchten.
3. Wählen Sie oben im Blatt „Übersicht“ die Option **Wiederherstellen** aus.

    ![ Wiederherstellung – Übersicht](../media/sql-pools/restore-sqlpool-01.png)

4. Wählen Sie entweder die Option **Automatische Wiederherstellungspunkte** oder **Benutzerdefinierte Wiederherstellungspunkte** aus. 

    Wenn der dedizierten SQL-Pool keine automatischen Wiederherstellungspunkte umfasst, warten Sie einige Stunden, oder erstellen Sie einen benutzerdefinierten Wiederherstellungspunkt, bevor Sie eine Wiederherstellung durchführen. 

    Wenn Sie eine Geowiederherstellung durchführen möchten, wählen Sie den Arbeitsbereich und den dedizierten SQL-Pool aus, den Sie wiederherstellen möchten. 

5. Klicken Sie auf **Überprüfen + erstellen**.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines Wiederherstellungspunkts](sqlpool-create-restore-point.md)
