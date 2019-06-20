---
title: Herstellen einer Verbindung mit SQL Server oder Azure SQL-Datenbank – Azure Logic Apps | Microsoft Docs
description: Hier erfahren Sie, wie Sie durch die Automatisierung von Workflows mit Azure Logic Apps auf lokale oder cloudbasierte SQL-Datenbanken zugreifen und diese verwalten.
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/15/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 998fcba50636cd92b14bdbe1633c2548e84a6bfc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64696419"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Herstellen einer Verbindung mit SQL Server oder Azure SQL-Datenbank über Azure Logic Apps

In diesem Artikel wird beschrieben, wie Sie über eine Logik-App mit dem SQL Server-Connector auf Daten in Ihrer SQL-Datenbank zugreifen können. Auf diese Weise können Sie durch Erstellung von Logik-Apps Aufgaben, Prozesse und Workflows automatisieren, die SQL-Daten und Ressourcen verwalten. Der Connector funktioniert sowohl für [lokale SQL Server-Instanzen](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) als auch für [Azure SQL-Datenbank in der Cloud](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview). 

Sie können Logik-Apps erstellen, deren Ausführung durch Ereignisse in Ihrer SQL-Datenbank oder in anderen Systemen (wie z. B. Dynamics CRM Online) ausgelöst wird. Ihre Logik-Apps können auch Daten abrufen, einfügen und löschen sowie SQL-Abfragen und gespeicherte Prozeduren ausführen. Sie können beispielsweise eine Logik-App erstellen, die Dynamics CRM Online automatisch auf neue Datensätze überprüft, Ihrer SQL-Datenbank Elemente für alle neuen Datensätze hinzufügt und dann E-Mail-Benachrichtigungen sendet.

Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md) und [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md). Connectorspezifische technische Informationen finden Sie in der <a href="https://docs.microsoft.com/connectors/sql/" target="blank">SQL Server-Connector-Referenz</a>.

## <a name="prerequisites"></a>Voraussetzungen

* Die Logik-App, über die Sie auf Ihre SQL-Datenbank zugreifen müssen. Um Ihre Logik-App mit einem SQL-Trigger starten zu können, benötigen Sie eine [leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Eine [Azure SQL-Datenbank](../sql-database/sql-database-get-started-portal.md) oder eine [SQL Server-Datenbank](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  Ihre Tabellen müssen Daten enthalten, damit Ihre Logik-App beim Aufrufen von Vorgängen Ergebnisse zurückgeben kann. Wenn Sie eine Azure SQL-Datenbank erstellen, können Sie die enthaltenen Beispieldatenbanken verwenden. 

* Der Name Ihres SQL-Servers, Datenbankname, Ihr Benutzername und Ihr Kennwort. Sie benötigen diese Anmeldeinformationen, um Ihre Logik für den Zugriff auf Ihren SQL-Server autorisieren zu können. 

  * Für Azure SQL-Datenbank finden Sie diese Details in der Verbindungszeichenfolge oder im Azure-Portal unter den Eigenschaften der SQL-Datenbank:

    „Server=tcp:<*NameIhresServers*>.database.windows.net,1433;Initial Catalog=<*NameIhrerDatenbank*>;Persist Security Info=False;User ID=<*IhrBenutzername*>;Password=<*IhrKennwort*>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;“

  * Für SQL Server finden Sie diese Details in der Verbindungszeichenfolge: 

    „Server=<*IhreServeradresse*>;Database=<*NameIhrerDatenbank*>;User Id=<*Ihr Benutzername*>;Password=<*IhrKennwort*>;“

* Bevor Sie Logik-Apps mit lokalen Systemen wie SQL Server verbinden können, müssen Sie [ein lokales Datengateway einrichten](../logic-apps/logic-apps-gateway-install.md). Auf diese Weise können Sie das Gateway auswählen, wenn Sie die SQL-Verbindung für Ihre Logik-App erstellen.

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>Hinzufügen eines SQL-Triggers

In Azure Logic Apps muss jede Logik-App mit einem [Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) beginnen, der ausgelöst wird, wenn ein bestimmtes Ereignis eintritt oder eine bestimmte Bedingung erfüllt wird. Bei jeder Auslösung des Triggers erstellt das Logic Apps-Modul eine Logik-App-Instanz und startet die Ausführung des Workflows Ihrer App.

1. Erstellen Sie im Azure-Portal oder in Visual Studio eine leere Logik-App, die den Logic Apps-Designer öffnet. In diesem Beispiel wird das Azure-Portal verwendet.

2. Geben Sie im Suchfeld den Begriff „sql server“ als Filter ein. Wählen Sie in der Triggerliste den gewünschten SQL-Trigger aus. 

   Wählen Sie für dieses Beispiel den folgenden Trigger aus: **SQL Server – Wenn ein Element erstellt wird**

   ![Trigger „SQL Server - Wenn ein Element erstellt wird“ auswählen](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. Wenn Sie zur Angabe von Verbindungsdetails aufgefordert werden, [erstellen Sie jetzt Ihre SQL-Verbindung](#create-connection). 
   Oder, falls Ihre Verbindung bereits vorhanden ist, wählen Sie in der Liste den gewünschten **Tabellennamen** aus.

   ![Tabelle auswählen](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. Legen Sie die Eigenschaften **Intervall** und **Häufigkeit** fest, die angeben, wie oft Ihre Logik-App die Tabelle überprüft.

   In diesem Beispiel wird nur die ausgewählte Tabelle überprüft, sonst nichts. 
   Wenn Sie etwas Interessanteres tun möchten, fügen Sie Aktionen hinzu, die die gewünschten Aufgaben ausführen. 
   
   Um beispielsweise das neue Element in der Tabelle anzuzeigen, können Sie weitere Aktionen hinzufügen, z. B. eine Datei mit Feldern aus der Tabelle erstellen und dann E-Mail-Benachrichtigungen senden. 
   Wenn Sie mehr über weitere Aktionen für diesen Connector oder andere Connectors erfahren möchten, lesen Sie [Logic Apps-Connectors](../connectors/apis-list.md).

5. Wenn Sie fertig sind, wählen Sie auf der Symbolleiste des Designers die Option **Speichern** aus. 

   Durch diesen Schritt wird Ihre Logik-App in Azure automatisch aktiviert und live veröffentlicht. 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>Hinzufügen einer SQL-Aktion

In Azure Logic Apps handelt es sich bei einer [Aktion](../logic-apps/logic-apps-overview.md#logic-app-concepts) um einen Schritt in Ihrem Workflow, der einem Trigger oder einer anderen Aktion folgt. In diesem Beispiel beginnt die Logik-App mit dem [Wiederholungstrigger](../connectors/connectors-native-recurrence.md) und ruft eine Aktion auf, die eine Zeile aus einer SQL-Datenbank abruft.

1. Öffnen Sie im Azure-Portal oder in Visual Studio Ihre Logik-App im Logic Apps-Designer. In diesem Beispiel wird das Azure-Portal verwendet.

2. Wählen Sie im Logik-App-Designer unter dem Trigger oder der Aktion die Optionen **Neuer Schritt** > **Aktion hinzufügen** aus.

   ![„Neuer Schritt“, „Aktion hinzufügen“ auswählen](./media/connectors-create-api-sqlazure/add-action.png)
   
   Um eine Aktion zwischen vorhandenen Schritten hinzuzufügen, bewegen Sie den Mauszeiger über den Verbindungspfeil. 
   Wählen Sie das daraufhin angezeigte Pluszeichen ( **+** ) aus, und wählen Sie dann **Aktion hinzufügen** aus.

2. Geben Sie im Suchfeld den Begriff „sql server“ als Filter ein. Wählen Sie in der Liste der Aktionen die gewünschte SQL-Aktion aus. 

   Wählen Sie in diesem Beispiel die folgende Aktion aus, die einen einzelnen Datensatz abruft: **SQL Server – Zeile abrufen**

   ![„sql server“ eingeben, „SQL Server - Zeile abrufen“ auswählen](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. Wenn Sie zur Angabe von Verbindungsdetails aufgefordert werden, [erstellen Sie jetzt Ihre SQL-Verbindung](#create-connection). 
   Oder, falls Ihre Verbindung bereits vorhanden ist, wählen Sie einen **Tabellennamen** aus, und geben Sie die **Zeilen-ID** für den gewünschten Datensatz ein.

   ![Den Tabellennamen und die Zeilen-ID eingeben](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   In diesem Beispiel wird nur eine Zeile aus der ausgewählten Tabelle zurückgegeben, sonst nichts. 
   Um die Daten in dieser Zeile anzuzeigen, können Sie weitere Aktionen hinzufügen, die eine Datei mit Feldern aus der Zeile zur späteren Überprüfung erstellen und diese Datei in einem Cloudspeicherkonto speichern. Weitere Informationen zu anderen Aktionen in diesem Connector oder anderen Connectors finden Sie unter [Logic Apps-Connectors](../connectors/apis-list.md).

4. Wenn Sie fertig sind, wählen Sie auf der Symbolleiste des Designers die Option **Speichern** aus. 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Verbinden mit der Datenbank

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Verarbeiten von Massendaten

Gelegentlich müssen Sie möglicherweise mit Resultsets arbeiten, die so groß sind, dass der Connector nicht alle Ergebnisse gleichzeitig zurückgibt. Oder Sie wünschen sich eine bessere Kontrolle über die Größe und Struktur Ihrer Resultsets. Hier sind einige Möglichkeiten, wie Sie derartig große Resultsets verarbeiten können:

* Aktivieren Sie *Paginierung*, um die Ergebnisse in kleineren Gruppen zu verwalten. Weitere Informationen finden Sie unter [Abrufen von Massendaten, Datensätzen und Elementen mithilfe der Paginierung](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Erstellen Sie eine gespeicherte Prozedur, die die Ergebnisse nach Ihren Vorstellungen organisiert.

  Beim Abrufen oder Einfügen mehrerer Zeilen kann Ihre Logik-App diese Zeilen mithilfe einer [*Until-Schleife*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) innerhalb dieser [Grenzwerte](../logic-apps/logic-apps-limits-and-config.md) durchlaufen. 
  Wenn Ihre Logik-App jedoch mit so großen Datensatzgruppen arbeiten muss, die z. B. Tausende oder Millionen von Zeilen umfassen, dass Sie die Kosten für Aufrufe der Datenbank minimieren möchten.

  Um die Ergebnisse in der von Ihnen gewünschten Weise zu organisieren, können Sie eine [*gespeicherte Prozedur*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) erstellen, die in Ihrer SQL-Instanz ausgeführt wird und die **SELECT - ORDER BY**-Anweisung verwendet. 
  Diese Lösung bietet Ihnen mehr Kontrolle über die Größe und Struktur Ihrer Ergebnisse. 
  Ihre Logik-App ruft die gespeicherte Prozedur mithilfe der Aktion **Gespeicherte Prozedur ausführen** des SQL Server-Connectors auf.

  Details dieser Lösung finden Sie in den folgenden Artikeln:

  * [SQL-Paginierung für die Massendatenübertragung mit Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx)

  * [SELECT - ORDER BY-Klausel](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Connectorspezifische Details

Technische Informationen zu den Triggern, Aktionen und Limits dieses Connectors finden Sie in den [Details der Connector-Referenz](/connectors/sql/). 

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)

