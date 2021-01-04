---
title: Abrufen von Azure Blockchain Workbench-Datenbankinformationen
description: Erfahren Sie, wie Sie Informationen zu Datenbank und Datenbankserver von Azure Blockchain Workbench Preview abrufen.
ms.date: 09/05/2019
ms.topic: how-to
ms.reviewer: mmercuri
ms.openlocfilehash: 898360d8e22803e17ff4da7d3b63f5595cad47ba
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015484"
---
# <a name="get-information-about-your-azure-blockchain-workbench-database"></a>Abrufen von Informationen zu Ihrer Azure Blockchain Workbench-Datenbank

In diesem Artikel wird erläutert, wie Sie ausführliche Informationen zu Ihrer Azure Blockchain Workbench Preview-Datenbank abrufen.

## <a name="overview"></a>Übersicht

Informationen zu Anwendungen, Workflows und intelligenter Vertragsausführung werden unter Verwendung von Datenbanksichten in der Blockchain Workbench-SQL-Datenbank bereitgestellt. Entwickler können diese Informationen bei Verwendung von Tools wie Microsoft Excel, Power BI, Visual Studio und SQL Server Management Studio nutzen.

Bevor Entwickler eine Verbindung mit der Datenbank herstellen können, ist Folgendes erforderlich:

* In der Datenbankfirewall wird der externe Clientzugriff zugelassen. In diesem Artikel zum Konfigurieren eines Datenbankfirewallartikels wird erläutert, wie der Zugriff zugelassen wird.
* Datenbankservername und Datenbankname.

## <a name="connect-to-the-blockchain-workbench-database"></a>Herstellen einer Verbindung mit der Blockchain Workbench-Datenbank

So stellen Sie die Verbindung mit der Datenbank her:

1. Melden Sie sich beim Azure-Portal mit einem Konto an, das über die Berechtigungen **Besitzer** für die Azure Blockchain Workbench-Ressourcen verfügt.
2. Wählen Sie im linken Navigationsbereich die Option **Ressourcengruppen** aus.
3. Wählen Sie den Namen der Ressourcengruppe für die Blockchain Workbench-Bereitstellung aus.
4. Wählen Sie **Typ** aus, um die Ressourcenliste zu sortieren, und wählen Sie dann Ihren **SQL-Server** aus. Die sortierte Liste im nächsten Bildschirm zeigt zwei Datenbanken (eine „master“-Datenbank und eine weitere mit dem **Ressourcenpräfix** „lhgn“).

   ![Sortierte Blockchain Workbench-Ressourcenliste](./media/getdb-details/sorted-workbench-resource-list.png)

5. Um ausführliche Informationen zur Blockchain Workbench-Datenbank anzuzeigen, wählen Sie den Link für die Datenbank mit dem für die Bereitstellung von Blockchain Workbench bereitgestellten **Ressourcenpräfix** aus.

   ![Datenbankdetails](./media/getdb-details/workbench-db-details.png)

Unter Verwendung des Datenbankservernamens und Datenbanknamens können Sie mit Ihrem Entwicklungs- oder Berichtstool die Verbindung zur Blockchain Workbench-Datenbank herstellen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Datenbanksichten in Azure Blockchain Workbench](database-views.md)