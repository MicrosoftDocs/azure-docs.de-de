---
title: Entwurf Ihrer ersten relationalen Datenbank mit C#
description: Hier erfahren Sie, wie Sie Ihre erste relationale Datenbank in Azure SQL-Datenbank mit C# und ADO.NET entwerfen.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-csharp
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: sstein
ms.date: 07/29/2019
ms.openlocfilehash: 4b3235f457f1c6475c18045886c49d3dd2ca2242
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92671178"
---
# <a name="tutorial-design-a-relational-database-in-azure-sql-database-cx23-and-adonet"></a>Tutorial: Entwerfen einer relationalen Datenbank in Azure SQL-Datenbank mit C&#x23; und ADO.NET
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL-Datenbank ist eine relationale DBaaS-Lösung (Database-as-a-Service) in Microsoft Cloud (Azure). In diesem Tutorial erfahren Sie, wie Sie das Azure-Portal und ADO.NET mit Visual Studio für folgende Aufgaben verwenden:

> [!div class="checklist"]
>
> * Erstellen einer Datenbank über das Azure-Portal
> * Einrichten einer IP-Firewallregel auf Serverebene mit dem Azure-Portal
> * Herstellen einer Verbindung für die Datenbank mit ADO.NET und Visual Studio
> * Erstellen von Tabellen mit ADO.NET
> * Einfügen, Aktualisieren und Löschen von Daten mit ADO.NET
> * Abfragen von Daten mit ADO.NET

*Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/), bevor Sie beginnen.

> [!TIP]
> In dem folgenden kostenlosen Microsoft Learn-Modul lernen Sie, wie Sie eine [ASP.NET-Anwendung entwickeln und konfigurieren, die eine Azure SQL-Datenbank abfragt](/learn/modules/develop-app-that-queries-azure-sql/). Sie erfahren u. a., wie Sie eine einfache Datenbank erstellen.

## <a name="prerequisites"></a>Voraussetzungen

Eine Installation von [Visual Studio 2019](https://www.visualstudio.com/downloads/) oder höher

## <a name="create-a-blank-database-in-azure-sql-database"></a>Erstellen einer leeren Datenbank in Azure SQL-Datenbank

Eine Datenbank in Azure SQL-Datenbank wird mit einer definierten Gruppe von Compute- und Speicherressourcen erstellt. Die Datenbank wird in einer [Azure-Ressourcengruppe](../../active-directory-b2c/overview.md) erstellt und mit einer [logischen SQL Server-Instanz](logical-servers.md) verwaltet.

Führen Sie die folgenden Schritte zum Erstellen einer leeren Datenbank aus.

1. Klicken Sie im Azure-Portal links oben auf **Ressource erstellen** .
2. Wählen Sie auf der Seite **Neu** im Abschnitt „Azure Marketplace“ die Option **Datenbanken** aus, und klicken Sie dann im Abschnitt **Empfohlen** auf **SQL-Datenbank** .

   ![Leere Datenbank erstellen](./media/design-first-database-csharp-tutorial/create-empty-database.png)

3. Geben Sie die folgenden Informationen in das **SQL-Datenbank** -Formular ein, wie in der obigen Abbildung dargestellt:

    | Einstellung       | Vorgeschlagener Wert | BESCHREIBUNG |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Datenbankname** | *yourDatabase* | Gültige Datenbanknamen finden Sie unter [Datenbankbezeichner](/sql/relational-databases/databases/database-identifiers). |
    | **Abonnement** | *yourSubscription*  | Ausführliche Informationen zu Ihren Abonnements finden Sie unter [Abonnements](https://account.windowsazure.com/Subscriptions). |
    | **Ressourcengruppe** | *yourResourceGroup* | Gültige Ressourcengruppennamen finden Sie unter [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Benennungsregeln und Einschränkungen). |
    | **Quelle auswählen** | Leere Datenbank | Gibt an, dass eine leere Datenbank erstellt werden soll. |

4. Klicken Sie auf **Server** , um einen vorhandenen Server zu verwenden oder um einen neuen Server zu erstellen und zu konfigurieren. Wählen Sie einen vorhandenen Server aus, oder klicken Sie auf **Neuen Server erstellen** , und geben Sie im Formular **Neuer Server** die folgenden Informationen ein:

    | Einstellung       | Vorgeschlagener Wert | BESCHREIBUNG |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Servername** | Ein global eindeutiger Name | Gültige Servernamen finden Sie unter [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Benennungsregeln und Einschränkungen). |
    | **Serveradministratoranmeldung** | Ein gültiger Name | Gültige Anmeldenamen finden Sie unter [Datenbankbezeichner](/sql/relational-databases/databases/database-identifiers). |
    | **Kennwort** | Ein gültiges Kennwort | Ihr Kennwort muss mindestens acht Zeichen umfassen und Zeichen aus drei der folgenden Kategorien enthalten: Großbuchstaben, Kleinbuchstaben, Zahlen und nicht alphanumerische Zeichen. |
    | **Location** | Gültiger Standort | Informationen zu Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/regions/). |

    ![Erstellung des Datenbankservers](./media/design-first-database-csharp-tutorial/create-database-server.png)

5. Klicken Sie auf **Auswählen** .
6. Klicken Sie auf **Tarif** , um die Dienstebene, die Anzahl von DTUs oder virtuellen Kernen und die Speichermenge anzugeben. Sie können sich mit den Optionen für die Anzahl von DTUs/virtuellen Kernen sowie mit den Speicheroptionen vertraut machen, die für die einzelnen Dienstebenen verfügbar sind.

    Klicken Sie auf **Übernehmen** , wenn Sie die Dienstebene, die Anzahl von DTUs oder virtuellen Kernen und die Menge an Speicherplatz ausgewählt haben.

7. Geben Sie eine **Sortierung** für die leere Datenbank ein (verwenden Sie in diesem Tutorial den Standardwert). Weitere Informationen über Sortierungen finden Sie unter [Sortierungen](/sql/t-sql/statements/collations).

8. Nachdem Sie das **SQL-Datenbank** -Formular ausgefüllt haben, können Sie auf **Erstellen** klicken, um die Datenbank bereitzustellen. Dieser Schritt kann einige Minuten dauern.

9. Klicken Sie in der Symbolleiste auf **Benachrichtigungen** , um den Bereitstellungsprozess zu überwachen.

   ![Screenshot, in dem Benachrichtigungen im Azure-Portal mit „Bereitstellung wird durchgeführt“ gezeigt sind](./media/design-first-database-csharp-tutorial/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Erstellen einer IP-Firewallregel auf Serverebene

Azure SQL-Datenbank erstellt eine IP-Firewall auf Serverebene. Diese Firewall verhindert, dass externe Anwendungen und Tools eine Verbindung mit dem Server und Datenbanken auf dem Server herstellen, sofern die IP-Adresse nicht durch eine Firewallregel zugelassen wird. Um externe Verbindungen mit Ihrer Datenbank zuzulassen, müssen Sie zunächst eine IP-Firewallregel für Ihre IP-Adresse (oder einen IP-Adressbereich) hinzufügen. Führen Sie die folgenden Schritte aus, um eine [IP-Firewallregel auf Serverebene](firewall-configure.md) zu erstellen.

> [!IMPORTANT]
> SQL-Datenbank kommuniziert über Port 1433. Wenn Sie versuchen, mit diesem Dienst eine Verbindung aus einem Unternehmensnetzwerk heraus herzustellen, wird der ausgehende Datenverkehr über Port 1433 von der Firewall Ihres Netzwerks unter Umständen nicht zugelassen. In diesem Fall können Sie nur dann eine Verbindung mit der Datenbank herstellen, wenn Ihr Administrator Port 1433 öffnet.

1. Klicken Sie nach Abschluss der Bereitstellung im Menü auf der linken Seite auf **SQL-Datenbanken** , und klicken Sie dann auf der Seite **SQL-Datenbanken** auf *yourDatabase* . Die Übersichtsseite für Ihre Datenbank wird geöffnet, die den vollqualifizierten **Servernamen** (z.B. *yourserver.database.windows.net* ) und Optionen für die weitere Konfiguration enthält.

2. Kopieren Sie diesen vollqualifizierten Servernamen, um in SQL Server Management Studio eine Verbindung mit Ihrem Server und den Datenbanken herzustellen.

   ![Servername](./media/design-first-database-csharp-tutorial/server-name.png)

3. Klicken Sie auf der Symbolleiste auf **Serverfirewall festlegen** . Die Seite **Firewalleinstellungen** für den Server wird geöffnet.

   ![IP-Firewallregel auf Serverebene](./media/design-first-database-csharp-tutorial/server-firewall-rule.png)

4. Klicken Sie auf der Symbolleiste auf **Client-IP-Adresse hinzufügen** , um Ihre aktuelle IP-Adresse einer neuen IP-Firewallregel hinzuzufügen. Eine IP-Firewallregel kann Port 1433 für eine einzelne IP-Adresse oder einen Bereich von IP-Adressen öffnen.

5. Klicken Sie auf **Speichern** . Für Ihre aktuelle IP-Adresse wird eine IP-Firewallregel auf Serverebene erstellt, und auf dem Server wird der Port 1433 geöffnet.

6. Klicken Sie auf **OK** , und schließen Sie anschließend die Seite **Firewalleinstellungen** .

Die IP-Adresse kann nun die IP-Firewall passieren. Nun können Sie mit SQL Server Management Studio oder einem anderen Tool Ihrer Wahl die Verbindung mit der Datenbank herstellen. Verwenden Sie das Serveradministratorkonto, das Sie zuvor erstellt haben.

> [!IMPORTANT]
> Standardmäßig ist der Zugriff über die SQL-Datenbank-IP-Firewall für alle Azure-Dienste aktiviert. Klicken Sie auf dieser Seite auf **AUS** , um den Zugriff für alle Azure-Dienste zu deaktivieren.

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden grundlegende Datenbankaufgaben behandelt, z.B. das Erstellen einer Datenbank und von Tabellen, das Herstellen einer Verbindung mit der Datenbank, das Laden von Daten und das Ausführen von Abfragen. Sie haben Folgendes gelernt:

> [!div class="checklist"]
>
> * Erstellen einer Datenbank über das Azure-Portal
> * Einrichten einer IP-Firewallregel auf Serverebene mit dem Azure-Portal
> * Herstellen einer Verbindung für die Datenbank mit ADO.NET und Visual Studio
> * Erstellen von Tabellen mit ADO.NET
> * Einfügen, Aktualisieren und Löschen von Daten mit ADO.NET
> * Abfragen von Daten mit ADO.NET

Im nächsten Tutorial erhalten Sie Informationen zur Datenmigration.

> [!div class="nextstepaction"]
> [Offlinemigration von SQL Server zu Azure SQL-Datenbank mit DMS](../../dms/tutorial-sql-server-to-azure-sql.md)