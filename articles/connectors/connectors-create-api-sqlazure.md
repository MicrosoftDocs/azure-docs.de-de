---
title: Herstellen einer Verbindung mit SQL Server, Azure SQL-Datenbank oder Azure SQL Managed Instance
description: Automatisieren von Aufgaben für SQL-Datenbanken lokal oder in der Cloud mithilfe von Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 10/22/2020
tags: connectors
ms.openlocfilehash: ce7679fff86d2c96588cf2b704d44238535963b3
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130934"
---
# <a name="automate-workflows-for-a-sql-database-by-using-azure-logic-apps"></a>Automatisieren von Workflows für eine SQL-Datenbank mithilfe von Azure Logic Apps

In diesem Artikel wird beschrieben, wie Sie über eine Logik-App mit dem SQL Server-Connector auf Daten in Ihrer SQL-Datenbank zugreifen können. Auf diese Weise können Sie durch Erstellung von Logik-Apps Aufgaben, Prozesse oder Workflows automatisieren, die SQL-Daten und Ressourcen verwalten. Der SQL Server-Connector kann sowohl für [SQL Server](/sql/sql-server/sql-server-technical-documentation) als auch für [Azure SQL-Datenbank](../azure-sql/database/sql-database-paas-overview.md) und [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) verwendet werden.

Sie können Logik-Apps erstellen, deren Ausführung durch Ereignisse in Ihrer SQL-Datenbank oder in anderen Systemen (wie z. B. Dynamics CRM Online) ausgelöst wird. Ihre Logik-Apps können auch Daten abrufen, einfügen und löschen sowie SQL-Abfragen und gespeicherte Prozeduren ausführen. Sie können beispielsweise eine Logik-App erstellen, die Dynamics CRM Online automatisch auf neue Datensätze überprüft, Ihrer SQL-Datenbank Elemente für alle neuen Datensätze hinzufügt und dann E-Mail-Benachrichtigungen über die hinzugefügten Elemente sendet.

Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md) und [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md). Connectorspezifische technische Informationen, Einschränkungen und bekannte Probleme finden Sie auf der [Referenzseite zum SQL Server-Connector](/connectors/sql/).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Eine [SQL Server-Datenbank](/sql/relational-databases/databases/create-a-database), eine [Azure SQL-Datenbank](../azure-sql/database/single-database-create-quickstart.md) oder eine [Azure SQL Managed Instance](../azure-sql/managed-instance/instance-create-quickstart.md).

  Ihre Tabellen müssen Daten enthalten, damit Ihre Logik-App beim Aufrufen von Vorgängen Ergebnisse zurückgeben kann. Wenn Sie eine Azure SQL-Datenbank verwenden, können Sie die enthaltenen Beispieldatenbanken verwenden.

* Der Name Ihres SQL-Servers, Datenbankname, Ihr Benutzername und Ihr Kennwort. Sie benötigen diese Anmeldeinformationen, um Ihre Logik für den Zugriff auf Ihren SQL-Server autorisieren zu können.

  * Für lokale SQL Server finden Sie diese Details in der Verbindungszeichenfolge:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Für eine Azure SQL-Datenbank finden Sie diese Details in der Verbindungszeichenfolge.
  
    Um beispielsweise diese Zeichenfolge im Azure-Portal zu finden, öffnen Sie Ihre Datenbank. Wählen Sie im Menü „Datenbank“ entweder **Verbindungszeichenfolgen** oder **Eigenschaften** aus:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

<a name="multi-tenant-or-ise"></a>

* Je nachdem, ob Ihre Logik-Apps in globalem, mehrinstanzenfähigem Azure oder in einer [Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ausgeführt werden, gelten hier andere Anforderungen für das Herstellen einer Verbindung mit lokalem SQL Server:

  * Für Logik-Apps in globalem, mehrinstanzenfähigem Azure, die eine Verbindung mit lokalem SQL Server herstellen, müssen Sie das [lokale Datengateway](../logic-apps/logic-apps-gateway-install.md) auf einem lokalen Computer installiert haben und eine [Datengatewayressource besitzen, die bereits in Azure erstellt wurde](../logic-apps/logic-apps-gateway-connection.md).

  * Für Logik-Apps in einer ISE, die eine Verbindung mit lokalem SQL Server herstellen und Windows-Authentifizierung verwenden, unterstützt der SQL Server-Connector mit ISE-Versionsverwaltung keine Windows-Authentifizierung. Sie müssen also weiterhin das Datengateway und den Nicht-ISE-Connector von SQL Server verwenden. Für andere Authentifizierungstypen müssen Sie das Datengateway nicht verwenden und können den Connector mit ISE-Versionsverwaltung verwenden.

* Die Logik-App, über die Sie auf Ihre SQL-Datenbank zugreifen müssen. Um Ihre Logik-App mit einem SQL-Trigger starten zu können, benötigen Sie eine [leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Verbinden mit der Datenbank

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Fahren Sie nun mit diesen Schritten fort:

* [Herstellen einer Verbindung mit der cloudbasierten Azure SQL-Datenbank oder der verwalteten Instanz](#connect-azure-sql-db)
* [Herstellen einer Verbindung mit lokalem SQL Server](#connect-sql-server)

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database-or-managed-instance"></a>Herstellen einer Verbindung mit Azure SQL-Datenbank oder einer verwalteten Instanz

Wenn Sie ohne das lokale Datengateway oder die Integrationsdienstumgebung auf eine Azure SQL Managed Instance-Instanz zugreifen möchten, müssen Sie den [öffentlichen Endpunkt für Azure SQL Managed Instance einrichten](../azure-sql/managed-instance/public-endpoint-configure.md). Der öffentliche Endpunkt verwendet Port 3342. Stellen Sie daher sicher, dass Sie diese Portnummer angeben, wenn Sie die Verbindung über Ihre Logik-App herstellen.


Wenn Sie zum ersten Mal entweder einen [SQL-Trigger](#add-sql-trigger) oder eine [SQL-Aktion](#add-sql-action) hinzufügen und zuvor keine Verbindung mit Ihrer Datenbank hergestellt haben, werden Sie aufgefordert, diese Schritte auszuführen:

1. Wählen Sie als **Authentifizierungstyp** die Authentifizierung aus, die für Ihre Datenbank in Azure SQL-Datenbank oder Azure SQL Managed Instance erforderlich und aktiviert ist:

   | Authentifizierung | BESCHREIBUNG |
   |----------------|-------------|
   | [**Azure AD-Integration**](../azure-sql/database/authentication-aad-overview.md) | – Unterstützt sowohl den Non-ISE- als auch den ISE-Connector von SQL Server. <p><p>– Erfordert eine gültige Identität in Azure Active Directory (Azure AD), die Zugriff auf Ihre Datenbank hat. <p>Weitere Informationen finden Sie in den folgenden Themen: <p>- [Azure SQL-Sicherheitsübersicht – Authentifizierung](../azure-sql/database/security-overview.md#authentication) <br>- [Autorisieren des Datenbankzugriffs auf Azure SQL – Authentifizierung und Autorisierung](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) <br>- [Azure SQL – Azure AD-Integrationsauthentifizierung](../azure-sql/database/authentication-aad-overview.md) |
   | [**SQL Server-Authentifizierung**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | – Unterstützt sowohl den Non-ISE- als auch den ISE-Connector von SQL Server. <p><p>– Erfordert einen gültigen Benutzernamen und ein sicheres Kennwort, die in Ihrer Datenbank erstellt und gespeichert werden. <p>Weitere Informationen finden Sie in den folgenden Themen: <p>- [Azure SQL-Sicherheitsübersicht – Authentifizierung](../azure-sql/database/security-overview.md#authentication) <br>- [Autorisieren des Datenbankzugriffs auf Azure SQL – Authentifizierung und Autorisierung](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) |
   |||

   Dieses Beispiel wird mit **Azure AD-Integration** fortgesetzt:

   ![Screenshot: Verbindungsfenster „SQL Server“ mit der geöffneten Liste „Authentifizierungstyp“ und der ausgewählten Option „Azure AD-Integration“](./media/connectors-create-api-sqlazure/select-azure-ad-authentication.png)

1. Nachdem Sie **Azure AD-Integration** ausgewählt haben, wählen Sie **Anmelden** aus. Je nachdem, ob Sie Azure SQL-Datenbank oder Azure SQL Managed Instance verwenden, wählen Sie Ihre Benutzeranmeldeinformationen für die Authentifizierung aus.

1. Wählen Sie dieses Werte für Ihre Datenbank aus:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Servername** | Ja | Die Adresse für Ihren SQL Server, z. B. `Fabrikam-Azure-SQL.database.windows.net`. |
   | **Datenbankname** | Ja | Der Name für Ihre SQL-Datenbank, z. B. `Fabrikam-Azure-SQL-DB`. |
   | **Tabellenname** | Ja | Die Tabelle, die Sie verwenden möchten, z. B. `SalesLT.Customer`. |
   ||||

   > [!TIP]
   > Zur Angabe Ihrer Datenbank- und Tabelleninformationen stehen folgende Optionen zur Verfügung:
   > 
   > * Ermitteln Sie diese Informationen in der Verbindungszeichenfolge Ihrer Datenbank. Suchen Sie z. B Ihre Datenbank im Azure-Portal, und öffnen Sie sie. Wählen Sie im Datenbankmenü entweder **Verbindungszeichenfolgen** oder **Eigenschaften** aus. Dort finden Sie die folgende Zeichenfolge:
   >
   >   `Server=tcp:{your-server-address}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`
   >
   > * Standardmäßig werden Tabellen in Systemdatenbanken herausgefiltert, sodass sie möglicherweise nicht automatisch angezeigt werden, wenn Sie eine Systemdatenbank auswählen. Alternativ können Sie den Tabellennamen manuell eingeben, nachdem Sie in der Datenbankliste die Option **Benutzerdefinierten Wert eingeben** ausgewählt haben.
   >

   Dieses Beispiel zeigt, wie diese Werte aussehen können:

   ![Erstellen einer Verbindung mit der SQL-Datenbank](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Fahren Sie nun mit den Schritten fort, die Sie noch nicht durchgeführt haben, in [Hinzufügen eines SQL-Triggers](#add-sql-trigger) oder [Hinzufügen einer SQL-Aktion](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-on-premises-sql-server"></a>Herstellen einer Verbindung mit lokalem SQL Server

Wenn Sie zum ersten Mal entweder einen [SQL-Trigger](#add-sql-trigger) oder eine [SQL-Aktion](#add-sql-action) hinzufügen und zuvor keine Verbindung mit Ihrer Datenbank hergestellt haben, werden Sie aufgefordert, diese Schritte auszuführen:

1. Stellen Sie für Verbindungen mit Ihrem lokalen SQL Server, der das lokale Datengateway erfordert, sicher, dass Sie [diese Voraussetzungen erfüllt](#multi-tenant-or-ise) haben.

   Andernfalls wird Ihre Datengatewayressource beim Erstellen der Verbindung nicht in der Liste **Verbindungsgateway** angezeigt.

1. Wählen Sie als **Authentifizierungstyp** die Authentifizierung aus, die für Ihre SQL Server erforderlich und aktiviert ist:

   | Authentifizierung | BESCHREIBUNG |
   |----------------|-------------|
   | [**Windows-Authentifizierung**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) | – Unterstützt nur den Nicht-ISE-Connector von SQL Server, der eine Datengatewayressource erfordert, die zuvor für Ihre Verbindung in Azure erstellt wurde, unabhängig davon, ob Sie mehrinstanzenfähiges Azure oder eine ISE verwenden. <p><p>– Erfordert einen gültigen Windows-Benutzernamen und ein Kennwort, um Ihre Identität über Ihr Windows-Konto zu bestätigen. <p>Weitere Informationen finden Sie unter [Windows-Authentifizierung](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication). |
   | [**SQL Server-Authentifizierung**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | – Unterstützt sowohl den Non-ISE- als auch den ISE-Connector von SQL Server. <p><p>– Erfordert einen gültigen Benutzernamen und ein sicheres Kennwort, die in Ihrem SQL Server erstellt und gespeichert werden. <p>Weitere Informationen finden Sie unter [SQL Server-Authentifizierung](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication). |
   |||

   Dieses Beispiel wird mit **Windows-Authentifizierung** fortgesetzt:

   ![Auswählen des zu verwendenden Authentifizierungstyps](./media/connectors-create-api-sqlazure/select-windows-authentication.png)

1. Wählen Sie die folgenden Werte für Ihre SQL-Datenbank aus, oder geben Sie sie an:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **SQL Server-Name** | Ja | Die Adresse für Ihren SQL Server, z. B. `Fabrikam-Azure-SQL.database.windows.net`. |
   | **SQL-Datenbankname** | Ja | Der Name für Ihre SQL Server-Datenbank, z. B. `Fabrikam-Azure-SQL-DB`. |
   | **Benutzername** | Ja | Ihr Benutzername für SQL Server und -Datenbank. |
   | **Kennwort** | Ja | Ihr Kennwort für SQL Server und -Datenbank. |
   | **Abonnement** |  Ja, für Windows-Authentifizierung. | Das Azure-Abonnement für die Datengatewayressource, das Sie zuvor in Azure erstellt haben. |
   | **Verbindungsgateway** | Ja, für Windows-Authentifizierung. | Der Name für die Datengatewayressource, die Sie zuvor in Azure erstellt haben. <p><p>**Tipp** : Wenn Ihr Gateway nicht in der Liste angezeigt wird, überprüfen Sie, ob Sie das [Gateway richtig eingerichtet](../logic-apps/logic-apps-gateway-connection.md) haben. |
   |||

   > [!TIP]
   > Sie finden diese Informationen in der Verbindungszeichenfolge Ihrer Datenbank:
   > 
   > * `Server={your-server-address}`
   > * `Database={your-database-name}`
   > * `User ID={your-user-name}`
   > * `Password={your-password}`

   Dieses Beispiel zeigt, wie diese Werte aussehen können:

   ![Erstellen einer SQL Server-Verbindung abgeschlossen.](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Wählen Sie **Erstellen** aus, wenn Sie fertig sind.

1. Fahren Sie nun mit den Schritten fort, die Sie noch nicht durchgeführt haben, in [Hinzufügen eines SQL-Triggers](#add-sql-trigger) oder [Hinzufügen einer SQL-Aktion](#add-sql-action).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Hinzufügen eines SQL-Triggers

1. Erstellen Sie im [Azure-Portal](https://portal.azure.com) oder in Visual Studio eine leere Logik-App, die den Logik-App-Designer öffnet. Dieses Beispiel wird mit dem Azure-Portal fortgesetzt.

1. Geben Sie im Designer in das Suchfeld `sql server` ein. Wählen Sie in der Triggerliste den gewünschten SQL-Trigger aus. In diesem Beispiel wird der Trigger **Wenn ein Element erstellt wird** verwendet.

   ![Trigger „Wenn ein Element erstellt wird“ auswählen](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Wenn Sie zum ersten Mal eine Verbindung mit Ihrer SQL-Datenbank herstellen, werden Sie aufgefordert, [jetzt Ihre SQL-Datenbankverbindung zu erstellen](#create-connection). Nachdem Sie diese Verbindung hergestellt haben, können Sie mit dem nächsten Schritt fortfahren.

1. Geben Sie im Trigger das Intervall und die Häufigkeit dafür an, wie oft der Trigger die Tabelle überprüfen soll.

1. Öffnen Sie die Liste **Neuen Parameter hinzufügen** , um weitere verfügbare Eigenschaften für diesen Trigger hinzuzufügen.

   Mit diesem Trigger wird nur eine Zeile aus der ausgewählten Tabelle zurückgegeben, sonst nichts. Um andere Aufgaben auszuführen, fahren Sie fort, indem Sie entweder eine [SQL-Connector-Aktion ](#add-sql-action) oder [eine weitere Aktions](../connectors/apis-list.md) hinzufügen, die die nächste Aufgabe ausführt, die Sie in Ihrem Logik-App-Workflow ausführen möchten.
   
   Wenn Sie z.B. die Daten in dieser Zeile anzeigen möchten, können Sie weitere Aktionen hinzufügen, die eine Datei erstellen, die die Felder aus der zurückgegebenen Zeile enthält, und dann E-Mail-Benachrichtigungen senden. Informationen zu anderen verfügbaren Aktionen für diesen Connector finden Sie auf der [Referenzseite zum Connector](/connectors/sql/).

1. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

   Obwohl dieser Schritt ihre Logik-App automatisch in Azure aktiviert und live veröffentlicht, besteht die einzige Aktion, die ihre Logik-App derzeit ausführt, darin, Ihre Datenbank auf Grundlage des von Ihnen angegebenen Intervalls und der Häufigkeit zu überprüfen.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Hinzufügen einer SQL-Aktion

In diesem Beispiel beginnt die Logik-App mit dem [Wiederholungstrigger](../connectors/connectors-native-recurrence.md) und ruft eine Aktion auf, die eine Zeile aus einer SQL-Datenbank abruft.

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) oder in Visual Studio Ihre Logik-App im Logik-App-Designer. Dieses Beispiel wird mit dem Azure-Portal fortgesetzt.

1. Wählen Sie unter dem Trigger oder der Aktion, dem bzw. der Sie die SQL-Aktion hinzufügen möchten, die Option **Neuer Schritt** aus.

   ![Hinzufügen einer Aktion zu Ihrer Logik-App](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Oder um eine Aktion zwischen vorhandenen Schritten hinzuzufügen, bewegen Sie den Mauszeiger über den Verbindungspfeil. Wählen Sie das angezeigte Pluszeichen ( **+** ) aus, und wählen Sie dann **Aktion hinzufügen** aus.

1. Geben Sie unter **Aktion auswählen** den Text `sql server` in das Suchfeld ein. Wählen Sie in der Liste der Aktionen die gewünschte SQL-Aktion aus. In diesem Beispiel wird die Aktion **Zeile abrufen** verwendet, die einen einzelnen Datensatz abruft.

   ![Auswählen der SQL-Aktion „Zeile abrufen“](./media/connectors-create-api-sqlazure/select-sql-get-row-action.png)

1. Wenn Sie zum ersten Mal eine Verbindung mit Ihrer SQL-Datenbank herstellen, werden Sie aufgefordert, [jetzt Ihre SQL-Datenbankverbindung zu erstellen](#create-connection). Nachdem Sie diese Verbindung hergestellt haben, können Sie mit dem nächsten Schritt fortfahren.

1. Wählen Sie den **Tabellennamen** aus, der in diesem Beispiel `SalesLT.Customer` lautet. Geben Sie die **Zeilen-ID** für den gewünschten Datensatz ein.

   ![Auswählen des Tabellennamens und Angeben der Zeilen-ID](./media/connectors-create-api-sqlazure/specify-table-row-id.png)

   Mit dieser Aktion wird nur eine Zeile aus der ausgewählten Tabelle zurückgegeben, sonst nichts. Um also die Daten in dieser Zeile anzuzeigen, können Sie weitere Aktionen hinzufügen, die eine Datei erstellen, die die Felder aus der zurückgegebenen Zeile enthält,und diese Datei in einem Cloudspeicherkonto speichern. Informationen zu anderen verfügbaren Aktionen für diesen Connector finden Sie auf der [Referenzseite zum Connector](/connectors/sql/).

1. Wenn Sie fertig sind, wählen Sie auf der Symbolleiste des Designers die Option **Speichern** aus.

   Durch diesen Schritt wird Ihre Logik-App in Azure automatisch aktiviert und live veröffentlicht.

<a name="handle-bulk-data"></a>

## <a name="handle-bulk-data"></a>Verarbeiten von Massendaten

Gelegentlich müssen Sie mit Resultsets arbeiten, die so groß sind, dass der Connector nicht alle Ergebnisse gleichzeitig zurückgibt. Oder Sie wünschen sich eine bessere Kontrolle über die Größe und Struktur Ihrer Resultsets. Hier sind einige Möglichkeiten, wie Sie derartig große Resultsets verarbeiten können:

* Aktivieren Sie *Paginierung* , um die Ergebnisse in kleineren Gruppen zu verwalten. Weitere Informationen finden Sie unter [Abrufen von Massendaten, Datensätzen und Elementen mithilfe der Paginierung](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md). Weitere Informationen finden Sie unter [SQL-Paginierung für die Massendatenübertragung mit Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx).

* Erstellen Sie eine [*gespeicherte Prozedur*](/sql/relational-databases/stored-procedures/stored-procedures-database-engine), die die Ergebnisse nach Ihren Vorstellungen organisiert. Der SQL-Connector bietet viele Back-End-Funktionen, auf die Sie über Azure Logic Apps zugreifen können, sodass Sie Geschäftsaufgaben, die mit SQL-Datenbanktabellen arbeiten, einfacher automatisieren können.

  Beim Abrufen oder Einfügen mehrerer Zeilen kann Ihre Logik-App diese Zeilen mithilfe einer [*Until-Schleife*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) innerhalb dieser [Grenzwerte](../logic-apps/logic-apps-limits-and-config.md) durchlaufen. Wenn Ihre Logik-App jedoch mit so großen Datensatzgruppen arbeiten muss, die z. B. Tausende oder Millionen von Zeilen umfassen, dass Sie die Kosten für Aufrufe der Datenbank minimieren möchten.

  Um die Ergebnisse in der von Ihnen gewünschten Weise zu organisieren, können Sie eine gespeicherte Prozedur erstellen, die in Ihrer SQL-Instanz ausgeführt wird und die **SELECT - ORDER BY** -Anweisung verwendet. Diese Lösung bietet Ihnen mehr Kontrolle über die Größe und Struktur Ihrer Ergebnisse. Ihre Logik-App ruft die gespeicherte Prozedur mithilfe der Aktion **Gespeicherte Prozedur ausführen** des SQL Server-Connectors auf. Weitere Informationen finden Sie unter [SELECT – ORDER BY-Klausel](/sql/t-sql/queries/select-order-by-clause-transact-sql).

  > [!NOTE]
  > Beim SQL-Connector gilt für gespeicherte Prozeduren ein Timeoutlimit von [unter zwei Minuten](/connectors/sql/#known-issues-and-limitations). Einige gespeicherte Prozeduren dauern möglicherweise länger, was einen Fehler vom Typ `504 Timeout` zur Folge hat. Sie können dieses Problem umgehen, indem Sie einen SQL-Abschlussauslöser, eine native SQL-Pass-Through-Abfrage, eine Zustandstabelle und serverseitiger Aufträge verwenden.
  > 
  > Für diese Aufgabe können Sie den [Azure-Agent für elastische Aufträge](../azure-sql/database/elastic-jobs-overview.md) für [Azure SQL-Datenbank](../azure-sql/database/sql-database-paas-overview.md) nutzen. Für [lokale SQL Server-Instanzen](/sql/sql-server/sql-server-technical-documentation) und [Azure SQL Managed Instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) können Sie den [SQL Server-Agent](/sql/ssms/agent/sql-server-agent) verwenden. Weitere Informationen finden Sie unter [Behandeln von Timeouts bei gespeicherten Prozeduren im SQL-Connector für Azure Logic Apps](../logic-apps/handle-long-running-stored-procedures-sql-connector.md).

### <a name="handle-dynamic-bulk-data"></a>Behandeln dynamischer Massendaten

Wenn Sie eine gespeicherte Prozedur mit dem SQL Server-Connector aufzurufen, ist die zurückgegebene Ausgabe manchmal dynamisch. Führen Sie in diesem Szenario die folgenden Schritte aus:

1. Öffnen Sie Ihre Logik-App über das [Azure-Portal](https://portal.azure.com) im Logik-App-Designer.

1. Zeigen Sie das Ausgabeformat an, indem Sie einen Testlauf ausführen. Kopieren und speichern Sie Ihre Beispielausgabe.

1. Wählen Sie im Designer unter der Aktion, in der die gespeicherte Prozedur aufgerufen wird, **Neuer Schritt** aus.

1. Suchen Sie unter **Aktion auswählen** nach der Aktion [**JSON analysieren**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action), und wählen Sie sie aus.

1. Wählen Sie in der Aktion **Parse JSON** die Option **Beispielnutzlast zum Generieren eines Schemas verwenden**.

1. Fügen Sie in das Feld **Geben oder fügen Sie eine JSON-Beispielnutzlast ein** Ihre Beispielnutzlast ein, und wählen Sie dann **Fertig** aus.

   > [!NOTE]
   > Wenn Sie eine Fehlermeldung erhalten, dass Logic Apps kein Schema generieren kann, überprüfen Sie, ob die Syntax der Beispielausgabe richtig formatiert ist. Wenn Sie das Schema noch immer nicht generieren können, geben Sie es manuell in das Feld **Schema** ein.

1. Wählen Sie auf der Symbolleiste des Designers **Speichern** aus.

1. Um auf die JSON-Inhaltseigenschaften zu verweisen, klicken Sie in die Bearbeitungsfelder, in denen Sie auf diese Eigenschaften verweisen möchten, damit die Liste dynamischer Inhalte angezeigt wird. Wählen Sie in der Liste unter der Überschrift [**JSON analysieren**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) die Datentokens für die gewünschten JSON-Inhaltseigenschaften aus.

## <a name="troubleshoot-problems"></a>Behandeln von Problemen

* Verbindungsprobleme können häufig auftreten. Informationen zur Behebung dieser Probleme finden Sie unter [Beheben von Fehlern bei der Konnektivität mit SQL Server](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server). Hier einige Beispiele:

  * `A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.`

  * `(provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server) (Microsoft SQL Server, Error: 53)`

  * `(provider: TCP Provider, error: 0 - No such host is known.) (Microsoft SQL Server, Error: 11001)`

## <a name="connector-specific-details"></a>Connectorspezifische Details

Technische Informationen zu den Triggern, Aktionen und Grenzwerten dieses Connectors finden Sie auf der [Referenzseite des Connectors](/connectors/sql/), die aus der Swagger-Beschreibung generiert wird.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über andere [Connectors für Azure Logic Apps](../connectors/apis-list.md).
