---
title: Verwenden von Azure Blockchain Workbench-Daten in Microsoft Power BI
description: Erfahren Sie, wie Sie Daten der SQL-Datenbank von Azure Blockchain Workbench in Microsoft Power BI laden und anzeigen.
ms.date: 04/22/2020
ms.topic: how-to
ms.reviewer: sunri
ms.openlocfilehash: 7e0e585ce45616c2402972c725b502f4b704d1cd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000146"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Verwenden von Azure Blockchain Workbench-Daten mit Microsoft Power BI

Microsoft Power BI bietet die Möglichkeit, auf einfache Weise leistungsstarke Berichte aus SQL-Datenbanken mithilfe von Power BI Desktop zu generieren und diese dann auf [https://www.powerbi.com](https://www.powerbi.com) zu veröffentlichen.

Dieser Artikel enthält eine schrittweise exemplarische Vorgehensweise zum Herstellen einer Verbindung mit der SQL-Datenbank von Azure Blockchain Workbench aus Power BI Desktop, zum Erstellen eines Berichts und zum Bereitstellen des Berichts auf powerbi.com.

## <a name="prerequisites"></a>Voraussetzungen

* Laden Sie [Power BI Desktop](https://powerbi.microsoft.com/desktop/) herunter.

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>Verbinden von Power BI mit Daten in Azure Blockchain Workbench

1.  Öffnen Sie Power BI Desktop.
2.  Wählen Sie **Daten abrufen** aus.

    ![Datensammlung](./media/data-powerbi/get-data.png)
3.  Wählen Sie **SQL Server** aus der Liste der Datenquellentypen aus.

4.  Geben Sie den Server- und Datenbanknamen im Dialogfeld an. Geben an, ob Sie die Daten importieren oder eine **DirectQuery** ausführen möchten. Klicken Sie auf **OK**.

    ![Auswählen von „SQL Server“](./media/data-powerbi/select-sql.png)

5.  Geben Sie die Datenbankanmeldeinformationen für den Zugriff auf Azure Blockchain Workbench an. Wählen Sie **Datenbank** aus, und geben Sie Ihre Anmeldeinformationen ein.

    Wenn Sie die beim Azure Blockchain Workbench-Bereitstellungsprozess erstellten Anmeldeinformationen verwenden, lautet der Benutzername **dbadmin**, und Sie müssen das Kennwort angeben, das Sie während der Bereitstellung festgelegt haben.

    ![Einstellungen der SQL-Datenbank](./media/data-powerbi/db-settings.png)

6.  Sobald Sie eine Verbindung mit der Datenbank hergestellt haben, zeigt das Dialogfeld **Navigator** die in der Datenbank verfügbaren Tabellen und Sichten an. Die Sichten dienen der Berichterstellung und tragen alle das Präfix **vw**.

    ![Bildschirmerfassung von Power BI Desktop mit Auswahl des Dialogfelds „Navigator“ und von vwContractAction.](./media/data-powerbi/navigator.png)

7.  Wählen Sie die Sichten aus, die Sie einschließen möchten. Zu Demonstrationszwecken schließen wir **vwContractAction** ein. Diese Sicht enthält die Details für die Aktionen, die für einen Vertrag ausgeführt wurden.

    ![Auswählen von Sichten](./media/data-powerbi/select-views.png)

Sie können jetzt Berichte wie gewohnt mit Power BI erstellen und veröffentlichen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Datenbanksichten in Azure Blockchain Workbench](database-views.md)