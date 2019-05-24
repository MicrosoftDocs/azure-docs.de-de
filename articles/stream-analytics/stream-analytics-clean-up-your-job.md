---
title: Bereinigen Ihres Azure Stream Analytics-Auftrags
description: Dieser Artikel macht Sie mit verschiedenen Methoden zum Löschen Ihrer Azure Stream Analytics-Aufträge bekannt.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: e43e1034abe4bbe3d31a46ab3b98b0efe612b852
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66159449"
---
# <a name="clean-up-your-azure-stream-analytics-job"></a>Bereinigen Ihres Azure Stream Analytics-Auftrags

Azure Stream Analytics-Aufträge können auf einfache Weise über das Azure-Portal, Azure PowerShell, das Azure SDK für .NET oder die REST-API gelöscht werden. Ein Stream Analytics-Auftrag kann nicht wiederhergestellt werden, nachdem er gelöscht wurde.

>[!NOTE] 
>Wenn Sie Ihren Stream Analytics-Auftrag beenden, werden die Daten nur im Eingabe- und Ausgabespeicher beibehalten, z.B. in Event Hubs oder Azure SQL-Datenbank. Wenn Sie die Daten aus Azure entfernen müssen, führen Sie das folgende Verfahren zum Entfernen der Eingabe- und Ausgaberessourcen Ihres Stream Analytics-Auftrags aus.

## <a name="stop-a-job-in-azure-portal"></a>Beenden eines Auftrags im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

2. Suchen Sie nach Ihrem ausgeführten Stream Analytics-Auftrag, und wählen Sie ihn aus.

3. Klicken Sie auf der Seite für Stream Analytics-Aufträge auf **Beenden**, um den Auftrag zu beenden. 

   ![Beenden eines Azure Stream Analytics-Auftrags](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Löschen eines Auftrags im Azure-Portal

1. Melden Sie sich beim Azure-Portal an. 

2. Suchen Sie Ihren vorhandenen Stream Analytics-Auftrag, und wählen Sie ihn aus.

3. Klicken Sie auf der Seite für Stream Analytics-Aufträge auf **Löschen**, um den Auftrag zu löschen. 

   ![Löschen eines Azure Stream Analytics-Auftrags](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Beenden oder Löschen eines Auftrags mit PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Um einen Auftrag mithilfe von PowerShell zu beenden, verwenden Sie das Cmdlet [Stop-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/stop-azstreamanalyticsjob). Um einen Auftrag mithilfe von PowerShell zu löschen, verwenden Sie das Cmdlet [Remove-AzStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/az.streamanalytics/Remove-azStreamAnalyticsJob).

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Beenden oder Löschen eines Auftrags mit dem Azure SDK für .NET

Um einen Auftrag mit dem Azure SDK für .NET zu beenden, verwenden Sie die Methode [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet). Um einen Auftrag mit dem Azure SDK für .NET zu löschen, verwenden Sie die Methode [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet).

## <a name="stop-or-delete-a-job-using-rest-api"></a>Beenden oder Löschen eines Auftrags mit der REST-API

Um einen Auftrag mithilfe der REST-API zu beenden, verwenden Sie die Methode [Stop](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop). Um einen Auftrag mithilfe der REST-API zu löschen, verwenden Sie die Methode [Delete](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete).
