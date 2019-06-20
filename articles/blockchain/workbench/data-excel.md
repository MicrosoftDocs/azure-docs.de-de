---
title: Verwenden von Azure Blockchain Workbench-Daten in Microsoft Excel
description: Erfahren Sie, wie Sie Daten der SQL-Datenbank von Azure Blockchain Workbench in Microsoft Excel laden und anzeigen.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/09/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 215d8b8fbc49e9f38dc89655981edce37984163a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65510668"
---
# <a name="view-azure-blockchain-workbench-data-with-microsoft-excel"></a>Verwenden von Azure Blockchain Workbench-Daten mit Microsoft Excel

Sie können Microsoft Excel verwenden, um Daten in der SQL-Datenbank von Azure Blockchain Workbench anzuzeigen. Dieser Artikel beschreibt die Schritte, die erforderlich sind, um die folgenden Aktionen auszuführen:

* Herstellen einer Verbindung mit der Blockchain Workbench-Datenbank aus Microsoft Excel
* Anzeigen von Blockchain Workbench-Datenbanktabellen und -Sichten
* Laden von Daten der Blockchain Workbench-Sicht in Excel

## <a name="connect-to-the-blockchain-workbench-database"></a>Herstellen einer Verbindung mit der Blockchain Workbench-Datenbank

So stellen Sie eine Verbindung mit der Blockchain Workbench-Datenbank her:

1. Öffnen Sie Microsoft Excel.
2. Wählen Sie auf der Registerkarte **Daten** die Option **Daten abrufen** aus.
3. Wählen Sie **Aus Azure** aus, und wählen Sie dann **Aus Azure SQL-Datenbank** aus.

   ![Herstellen einer Verbindung mit Azure SQL-Datenbank](./media/data-excel/connect-sql-db.png)

4. Gehen Sie im Dialogfeld **SQL Server-Datenbank** folgendermaßen vor:

    * Geben Sie für **Server** den Namen des Blockchain Workbench-Servers ein.
    * Geben Sie für **Datenbank (optional)** den Namen der Datenbank ein.

   ![Angeben des Datenbankservers und der Datenbank](./media/data-excel/provide-server-db.png)

5. Wählen Sie auf der Dialogfeld-Navigationsleiste **SQL Server-Datenbank** die Option **Datenbank** aus. Geben Sie Ihren **Benutzernamen** und Ihr **Kennwort** ein, und klicken Sie dann auf **Verbinden**.

    > [!NOTE]
    > Wenn Sie die Anmeldeinformationen verwenden, die während des Bereitstellungsprozesses von Azure Blockchain Workbench erstellt wurden, lautet der **Benutzername** `dbadmin`. Das **Kennwort** ist das Kennwort, das Sie bei der Bereitstellung von Blockchain Workbench erstellt haben.
    
   ![Angeben der Anmeldeinformationen für den Zugriff auf die Datenbank](./media/data-excel/provide-credentials.png)

## <a name="look-at-database-tables-and-views"></a>Anzeigen von Datenbanktabellen und -sichten

Das Dialogfeld „Excel-Navigator“ wird geöffnet, nachdem Sie eine Verbindung mit der Datenbank hergestellt haben. Sie können den Navigator verwenden, um die Tabellen und Sichten in der Datenbank anzuzeigen. Die Sichten dienen der Berichterstellung, und ihre Namen tragen alle das Präfix **vw**.

   ![Excel-Navigator-Vorschau einer Sicht](./media/data-excel/excel-navigator.png)

## <a name="load-view-data-into-an-excel-workbook"></a>Laden von Sichtdaten in eine Excel-Arbeitsmappe

Im nächste Beispiel wird gezeigt, wie Sie Daten aus einer Sicht in eine Excel-Arbeitsmappe laden können.

1. Wählen Sie in der **Navigator**-Bildlaufleiste die Sicht **vwContractAction** aus. Die **vwContractAction**-Vorschau zeigt alle Aktionen an, die im Zusammenhang mit einem Vertrag in der Blockchain Workbench-Datenbank stehen.
2. Wählen Sie **Laden** zum Abrufen aller Daten in der Sicht aus, und fügen Sie diese in Ihre Excel-Arbeitsmappe ein.

   ![Aus einer Sicht geladene Daten](./media/data-excel/view-data.png)

Da Sie die Daten nun geladen haben, können Sie Excel-Features zum Erstellen eigener Berichte mithilfe der Metadaten und Transaktionsdaten aus der Azure Blockchain Workbench-Datenbank verwenden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Datenbanksichten in Azure Blockchain Workbench](database-views.md)