---
title: 'Schnellstart: Anhalten und Fortsetzen von Computeressourcen im dedizierten SQL-Pool (vormals SQL DW) über das Azure-Portal'
description: Verwenden Sie das Azure-Portal, um Computeressourcen für den SQL-Pool anzuhalten und Kosten zu sparen. Setzen Sie die Computeressourcen fort, wenn Sie das Data Warehouse verwenden möchten.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 88f6782a0f81b94977b37bd8f87bf26a0d4c8a72
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96457679"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-formerly-sql-dw-via-the-azure-portal"></a>Schnellstart: Anhalten und Fortsetzen von Computeressourcen im dedizierten SQL-Pool (vormals SQL DW) über das Azure-Portal

Mit dem Azure-Portal können Sie die Computeressourcen des dedizierten SQL-Pools (vormals SQL DW) anhalten und fortsetzen. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="before-you-begin"></a>Voraussetzungen

Verwenden Sie [Erstellen und Verbinden – Portal](../quickstart-create-sql-pool-portal.md), um einen dedizierten SQL-Pool namens **mySampleDataWarehouse** zu erstellen. 

## <a name="pause-compute"></a>Anhalten von Computeressourcen

Um Kosten zu senken, können Sie Computeressourcen bei Bedarf anhalten und fortsetzen. Wenn Sie die Datenbank z.B. nachts oder am Wochenende nicht verwenden, können Sie sie während dieser Zeiträume anhalten und während des Tages wieder fortsetzen.
 
>[!NOTE]
>Computeressourcen werden Ihnen nicht in Rechnung gestellt, während die Datenbank angehalten ist. Allerdings wird Ihnen der Speicherplatz weiter in Rechnung gestellt. 

Führen Sie die folgenden Schritte aus, um einen dedizierten SQL-Pool anzuhalten:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Klicken Sie auf der linken Navigationsseite des Azure-Portals auf **Dedizierter SQL-Pool (vormals SQL DW)** .
2. Wählen Sie auf der Seite **Dedizierter SQL-Pool (vormals SQL DW)** die Option **mySampleDataWarehouse** aus, um den SQL-Pool zu öffnen. 
3. Beachten Sie, dass auf der Seite **mySampleDataWarehouse** für **Status** der Wert **Online** angezeigt wird.

    ![Computerressourcen online](././media/pause-and-resume-compute-portal/compute-online.png)

4. Klicken Sie zum Anhalten des dedizierten SQL-Pools auf die Schaltfläche **Anhalten**. 
5. Sie werden in einer Meldung aufgefordert, das Fortsetzen des Vorgangs zu bestätigen. Klicken Sie auf **Ja**.
6. Warten Sie einen Moment, und beachten Sie, dass der **Status** als **Wird angehalten** angezeigt wird.

    ![Screenshot, in dem das Azure-Portal für ein Beispiel-Data Warehouse mit dem „Status“-Wert „Wird angehalten“ gezeigt ist](./media/pause-and-resume-compute-portal/pausing.png)

7. Nach Abschluss des Anhaltevorgangs lautet der Status **Angehalten**, und für die Optionsschaltfläche wird **Starten** angezeigt.
8. Die Computeressourcen für den dedizierten SQL-Pool sind nun offline. Ihnen werden erst wieder Computeressourcen in Rechnung gestellt, wenn Sie den Dienst fortsetzen.

    ![Computeressourcen offline](././media/pause-and-resume-compute-portal/compute-offline.png)


## <a name="resume-compute"></a>Fortsetzen von Computeressourcen

Führen Sie die folgenden Schritte aus, um einen dedizierten SQL-Pool fortzusetzen:

1. Klicken Sie auf der linken Seite des Azure-Portals auf **Dedizierter SQL-Pool (vormals SQL DW)** .
2. Wählen Sie auf der Seite **Dedizierter SQL-Pool (vormals SQL DW)** die Option **mySampleDataWarehouse** aus, um die Seite für den SQL-Pool zu öffnen. 
3. Beachten Sie, dass auf der Seite **mySampleDataWarehouse** für **Status** der Wert **Angehalten** angezeigt wird.

    ![Computeressourcen offline](././media/pause-and-resume-compute-portal/compute-offline.png)

1. Klicken Sie zum Fortsetzen des SQL-Pools auf **Fortsetzen**. 
1. Sie werden in einer Meldung aufgefordert, den Startvorgang zu bestätigen. Klicken Sie auf **Ja**.
1. Beachten Sie, dass der **Status** als **Wird fortgesetzt** angezeigt wird.

    ![Screenshot, in dem das Azure-Portal für ein Beispiel-Data Warehouse mit ausgewählter Schaltfläche „Starten“ und dem „Status“-Wert „Wird fortgesetzt“ gezeigt ist](./media/pause-and-resume-compute-portal/resuming.png)

1. Wenn der SQL-Pool wieder online ist, lautet der Status **Online**, und für die Optionsschaltfläche wird **Anhalten** angezeigt.
1. Die Computeressourcen für den SQL-Pool sind nun online, und Sie können den Dienst verwenden. Es fallen wieder Kosten für die Computeressourcen an.

    ![Computerressourcen online](././media/pause-and-resume-compute-portal/compute-online.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Ihnen werden Gebühren für Data Warehouse-Einheiten und die in Ihrem dedizierten SQL-Pool gespeicherten Daten in Rechnung gestellt. Diese Compute- und Speicherressourcen werden separat in Rechnung gestellt. 

- Wenn Sie die Daten im Speicher beibehalten möchten, halten Sie die Computeressourcen an.
- Wenn künftig keine Gebühren mehr anfallen sollen, können Sie den dedizierten SQL-Pool löschen. 

Führen Sie die folgenden Schritte aus, um Ressourcen nach Wunsch zu bereinigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie Ihren dedizierten SQL-Pool aus.

    ![Bereinigen von Ressourcen](./media/pause-and-resume-compute-portal/clean-up-resources.png)

1. Zum Anhalten von Computeressourcen klicken Sie auf die Schaltfläche **Anhalten**. 

1. Wenn Sie den dedizierten SQL-Pool entfernen möchten, damit keine Gebühren für Compute- oder Speicherressourcen anfallen, klicken Sie auf **Löschen**.



## <a name="next-steps"></a>Nächste Schritte

Sie haben die Computeressourcen für Ihren dedizierten SQL-Pool angehalten und fortgesetzt. Im nächsten Artikel finden Sie weitere Informationen zum [Laden von Daten in einen dedizierten SQL-Pool](load-data-from-azure-blob-storage-using-polybase.md). Weitere Informationen zum Verwalten von Computefunktionen finden Sie im Artikel [Verwalten von Computeressourcen im Azure Synapse Analytics-Data Warehouse](sql-data-warehouse-manage-compute-overview.md). 

