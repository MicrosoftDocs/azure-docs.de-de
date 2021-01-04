---
title: Kopieren oder Sichern von Azure Stream Analytics-Aufträgen
description: In diesem Artikel wird beschrieben, wie Sie einen Azure Stream Analytics-Auftrag kopieren oder sichern.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/11/2019
ms.openlocfilehash: ba63358347cf9722d2cafa35598b9b3b37f49dc3
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129455"
---
# <a name="copy-or-back-up-azure-stream-analytics-jobs"></a>Kopieren oder Sichern von Azure Stream Analytics-Aufträgen

Sie können Ihre bereitgestellten Azure Stream Analytics-Aufträge mit Visual Studio Code oder Visual Studio kopieren oder sichern. Beim Kopieren eines Auftrags in eine andere Region wird der letzte Ausgabezeitpunkt nicht mitkopiert. Daher können Sie beim Starten des kopierten Auftrags die Option [**Zeitpunkt der letzten Beendigung**](./start-job.md#start-options) nicht verwenden.

## <a name="before-you-begin"></a>Voraussetzungen
* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen.

* Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

* Installieren Sie die [Azure Stream Analytics-Erweiterung für Visual Studio Code](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension) oder die [Azure Stream Analytics-Tools für Visual Studio](quick-create-visual-studio-code.md#install-the-azure-stream-analytics-tools-extension).  

## <a name="visual-studio-code"></a>Visual Studio Code

1. Klicken Sie auf der Aktivitätsleiste von Visual Studio Code auf das **Azure** -Symbol, und erweitern Sie dann den Knoten **Stream Analytics**. Ihre Aufträge sollten unter Ihren Abonnements angezeigt werden.

   ![Öffnen von Stream Analytics-Explorer](./media/vscode-explore-jobs/open-explorer.png)

2. Suchen Sie den zu exportierenden Auftrag im **Stream Analytics-Explorer** in Visual Studio Code, um einen Auftrag in ein lokales Projekt zu exportieren. Wählen Sie dann einen Ordner für Ihr Projekt aus.

    ![Suchen von ASA-Aufträgen in Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

    Das Projekt wird in den von Ihnen ausgewählten Ordner exportiert und zu Ihrem aktuellen Arbeitsbereich hinzugefügt.

    ![Exportieren von ASA-Aufträgen in Visual Studio Code](./media/stream-analytics-manage-job/copy-backup-stream-analytics-jobs.png)

3. Um den Auftrag in einer anderen Region zu veröffentlichen oder unter einem anderen Namen zu sichern, wählen Sie **Zum Veröffentlichen aus Ihren Abonnements auswählen** im Abfrage-Editor (\*.asaql) aus, und befolgen Sie die Anweisungen.

    ![Veröffentlichen in Azure in Visual Studio Code](./media/quick-create-visual-studio-code/submit-job.png)

## <a name="visual-studio"></a>Visual Studio

1. Befolgen Sie die [Anweisungen zum Exportieren eines bereitgestellten Azure Stream Analytics-Auftrags zu einem Projekt](./stream-analytics-vs-tools.md#export-jobs-to-a-project).

2. Öffnen Sie im Abfrage-Editor die \*.asaql-Datei, wählen Sie **An Azure übermitteln** im Skript-Editor aus, und befolgen Sie die Anweisungen, um den Auftrag in einer anderen Region zu veröffentlichen oder unter einem neuen Namen zu sichern.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe von Visual Studio Code](quick-create-visual-studio-code.md)
* [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe von Visual Studio](stream-analytics-quick-create-vs.md)
* [Bereitstellen eines Azure Stream Analytics-Auftrags mit CI/CD mithilfe von Azure Pipelines](stream-analytics-tools-visual-studio-cicd-vsts.md)