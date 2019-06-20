---
title: Installieren von Visual Studio und SSDT für SQL Data Warehouse | Microsoft Docs
description: Installieren von Visual Studio und SQL Server Data Tools (SSDT) für Azure SQL Data Warehouse
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/05/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: b2e34f1f72b1b0aa76d4a3031102d052118dae5f
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304133"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>Installieren von Visual Studio und SSDT für SQL Data Warehouse
Verwenden Sie Visual Studio 2019 zum Entwickeln von Anwendungen für SQL Data Warehouse. Aktuell wird Visual Studio 2019 SSDT für SQL Data Warehouse nicht unterstützt. 

Die Verwendung von Visual Studio mit SSDT ermöglicht die Nutzung des SQL Server-Objekt-Explorers zum Visualisieren von Tabellen, Ansichten, gespeicherten Prozeduren und zahlreichen weiteren Objekten in SQL Data Warehouse. Außerdem können Sie Abfragen ausführen.

> [!NOTE]
> SQL Data Warehouse unterstützt noch keine Visual Studio-Datenbankprojekte. Um regelmäßig Aktualisierungen zu dieser Funktion zu erhalten, stimmen Sie auf [UserVoice] ab.
> 
> 

## <a name="step-1-install-visual-studio"></a>Schritt 1: Installieren von Visual Studio
Verwenden Sie die folgenden Links, um Visual Studio herunterzuladen und zu installieren. Falls Sie bereits Visual Studio 2013 oder eine höhere Version installiert haben, fahren Sie mit Schritt 2 fort, um SSDT zu installieren.

1. [Laden Sie Visual Studio herunter][].
2. Führen Sie die Schritte der auf MSDN bereitgestellten Anleitung [Installieren von Visual Studio][Installing Visual Studio] aus, und wählen Sie die Standardkonfigurationen aus.

## <a name="step-2-install-ssdt"></a>Schritt 2: Installieren von SSDT
Führen Sie zum Installieren von SSDT für Visual Studio zuerst die folgenden Schritte aus, um in Visual Studio nach einem SSDT-Update zu suchen:

1. Klicken Sie in Visual Studio auf **Tools** / **Erweiterungen und Updates...** / **Updates**
2. Wählen Sie **Produktupdates** aus, und suchen Sie nach **Microsoft SQL Server-Update für Datenbanktools**.

Die neueste Version ist wahrscheinlich bereits installiert, wenn kein Update gefunden wird. Klicken Sie auf **Hilfe** / **Info**, und suchen Sie in der Liste nach „SQL Server Data Tools“, um sich zu vergewissern, dass SSDT installiert ist. Falls die Option zur Installation in Visual Studio nicht verfügbar ist, können Sie SSDT auf der Seite für den [SSDT-Download][SSDT Download] manuell herunterladen und installieren.

## <a name="next-steps"></a>Nächste Schritte
Da Sie jetzt die neueste Version von SSDT verwenden, sind Sie bereit, eine [Verbindung][connect] mit SQL Data Warehouse herzustellen.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Laden Sie Visual Studio herunter]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
