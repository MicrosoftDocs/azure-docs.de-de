---
title: Zugriff auf und Verwalten von IBM DB2-Ressourcen
description: Lesen, Bearbeiten, Aktualisieren und Verwalten von IBM DB2-Ressourcen durch Erstellen automatisierter Workflows mithilfe von Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: plarsen, logicappspm
ms.topic: conceptual
ms.date: 11/19/2020
tags: connectors
ms.openlocfilehash: 765bb66b572f0c046222cfb617fe4caa80925256
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967400"
---
# <a name="access-and-manage-ibm-db2-resources-by-using-azure-logic-apps"></a>Zugreifen auf und Verwalten von IBM DB2-Ressourcen mithilfe von Azure Logic Apps

Mit [Azure Logic Apps](../logic-apps/logic-apps-overview.md) und dem [IBM DB2-Connector](/connectors/db2/) können Sie automatisierte Aufgaben und Workflows auf Grundlage der in Ihrer DB2-Datenbank gespeicherten Ressourcen erstellen. Ihre Workflows können eine Verbindung zu den Ressourcen in Ihrer Datenbank herstellen, Ihre Datenbanktabellen auslesen und auflisten und Zeilen hinzufügen, ändern, löschen usw. Sie können Aktionen in Ihre Logik-Apps integrieren, die Antworten von Ihrer Datenbank erhalten und die Ausgabe für andere Aktionen verfügbar machen.

In diesem Artikel wird gezeigt, wie Sie eine Logik-App erstellen können, die verschiedene Datenbankvorgänge durchführt. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md).

## <a name="supported-platforms-and-versions"></a>Unterstützte Plattformen und Versionen

Dieser DB2-Connector enthält einen Microsoft-Client zum Kommunizieren mit DB2-Remoteserverservern über ein TCP/IP-Netzwerk. Sie können diesen Connector für den Zugriff auf Clouddatenbanken verwenden, z. B. IBM DB2 für Windows, die in der Azure-Virtualisierung ausgeführt werden. Nach der [Installation und Einrichtung des lokalen Datengateway](../logic-apps/logic-apps-gateway-connection.md) können Sie auch auf lokale DB2-Datenbanken zugreifen.

Der IBM DB2-Connector unterstützt diese IBM DB2-Plattformen und -Versionen sowie IBM DB2-kompatible Produkte, die Distributed Relational Database Architecture (DRDA) SQL Access Manager (SQLAM) Version 10 und 11 unterstützen:

| Plattform | Version | 
|----------|---------|
| IBM DB2 für z/OS | 12, 11.1, 10.1 |
| IBM DB2 für i | 7.3, 7.2, 7.1 |
| IBM DB2 für LUW 11 | 11, 10.5 |
|||

## <a name="supported-database-operations"></a>Unterstützte Datenbankvorgänge

Der IBM DB2-Connector unterstützt diese Datenbankvorgänge, die den entsprechenden Aktionen im Connector zugeordnet sind:

| Datenbankvorgang | Connectoraktion |
|--------------------|------------------|
| Datenbanktabellen auflisten | Tabellen abrufen |
| Eine Zeile mit SELECT lesen | Zeile abrufen |
| Alle Zeilen mit SELECT lesen | Zeilen abrufen |
| Eine Zeile mit INSERT hinzufügen | Zeile einfügen |
| Eine Zeile mit UPDATE bearbeiten | Zeile aktualisieren |
| Eine Zeile mit DELETE entfernen | Zeile löschen |
|||

## <a name="prerequisites"></a>Voraussetzungen

* ein Azure-Abonnement Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Ein IBM DB2-Datenbank, entweder cloudbasiert oder lokal

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Die Logik-App, in der Sie auf Ihre DB2-Datenbank zugreifen möchten. Dieser Connector stellt nur Aktionen bereit, daher müssen Sie zum Starten Ihrer Logik-App einen separaten Trigger auswählen, z.B. einen **Wiederholungstrigger**.
In den Beispielen dieses Artikels wird der **Wiederholungstrigger** verwendet.

<a name="add-db2-action"></a>

## <a name="add-db2-action---get-tables"></a>DB2-Aktion hinzufügen – Tabellen abrufen

1. Öffnen Sie Ihre Logik-App über das [Azure-Portal](https://portal.azure.com) im Logik-App-Designer, wenn sie nicht bereits geöffnet ist.

1. Wählen Sie unter dem Trigger die Option **Neuer Schritt** aus.

1. Geben Sie im Suchfeld den Begriff „db2“ als Filter ein. Wählen Sie in diesem Beispiel in der Aktionsliste diese Aktion: **Tabellen abrufen (Vorschau)**

   ![Aktion select](./media/connectors-create-api-db2/select-db2-action.png)

   Sie werden nun aufgefordert, Verbindungsdetails für die DB2-Datenbank anzugeben.

1. Führen Sie die Schritte zum Erstellen von Verbindungen für [cloudbasierte Datenbanken](#cloud-connection) oder [lokale Datenbanken](#on-premises-connection) aus.

<a name="cloud-connection"></a>

## <a name="connect-to-cloud-db2"></a>Verbinden mit Cloud-DB2

Um die Verbindung einzurichten, geben Sie diese Verbindungsdetails ein, wenn Sie dazu aufgefordert werden, wählen Sie **Erstellen**, und speichern Sie Ihre Logik-App:

| Eigenschaft | Erforderlich | BESCHREIBUNG |
|----------|----------|-------------|
| **Connect via on-premises gateway** (Über lokales Datengateway verbinden) | Nein | Gilt nur für lokale Verbindungen. |
| **Verbindungsname** | Ja | Der Name Ihrer Verbindung, z.B. „MyLogicApp-DB2-connection“ |
| **Server** | Ja | Die Adresse oder Aliasportnummer für Ihren DB2-Server, z.B. „myDB2server.cloudapp.net:50000“ <p><p>**Hinweis**: Dieser Wert ist eine Zeichenfolge im IPv4- oder IPv6-Format, die für eine TCP/IP-Adresse oder einen Alias steht, gefolgt von einem Doppelpunkt und einer TCP/IP-Portnummer. |
| **Datenbank** | Ja | Der Name Ihrer Datenbank <p><p>**Hinweis**: Dieser Wert ist eine Zeichenfolge, die einen DRDA-Namen einer relationalen Datenbank (RDBNAM) darstellt: <p>- DB2 für z/OS akzeptiert eine 16-Byte-Zeichenfolge, wobei die Datenbank ein Speicherort vom Typ „IBM DB2 für z/OS“ ist. <br>- DB2 für i akzeptiert eine 18-Byte-Zeichenfolge, wobei die Datenbank eine relationale Datenbank vom Typ „IBM DB2 für i“ ist. <br>- DB2 für LUW akzeptiert eine 8-Byte-Zeichenfolge. |
| **Benutzername** | Ja | Ihr Benutzername für die Datenbank <p><p>**Hinweis**: Dieser Wert ist eine Zeichenfolge, deren Länge auf der betreffenden Datenbank basiert: <p><p>- DB2 für z/OS akzeptiert eine 8-Byte-Zeichenfolge. <br>- DB2 für i akzeptiert eine 10-Byte-Zeichenfolge. <br>- DB2 für Linux oder UNIX akzeptiert eine 8-Byte-Zeichenfolge. <br>- DB2 für Windows akzeptiert eine 30-Byte-Zeichenfolge. |
| **Kennwort** | Ja | Ihr Kennwort für die Datenbank |
||||

Beispiel:

![Verbindungsdetails für cloudbasierte Datenbanken](./media/connectors-create-api-db2/create-db2-cloud-connection.png)

<a name="on-premises-connection"></a>

## <a name="connect-to-on-premises-db2"></a>Herstellen einer Verbindung mit einer lokalen DB2-Datenbank

Vor dem Herstellen einer Verbindung muss Ihr lokales Datengateway bereits installiert sein. Andernfalls können Sie keine Verbindung einrichten. Wenn Ihr Gateway installiert ist, geben Sie die Verbindungsdetails ein, und wählen Sie dann **Erstellen**.

| Eigenschaft | Erforderlich | BESCHREIBUNG |
|----------|----------|-------------|
| **Connect via on-premises gateway** (Über lokales Datengateway verbinden) | Ja | Gilt, wenn Sie eine lokale Verbindung herstellen möchten, und zeigt die lokale Verbindungseigenschaften an. |
| **Verbindungsname** | Ja | Der Name Ihrer Verbindung, z.B. „MyLogicApp-DB2-connection“ | 
| **Server** | Ja | Die Adresse oder Aliasportnummer für Ihren DB2-Server, z.B. „myDB2server:50000“ <p><p>**Hinweis**: Dieser Wert ist eine Zeichenfolge im IPv4- oder IPv6-Format, die für eine TCP/IP-Adresse oder einen Alias steht, gefolgt von einem Doppelpunkt und einer TCP/IP-Portnummer. |
| **Datenbank** | Ja | Der Name Ihrer Datenbank <p><p>**Hinweis**: Dieser Wert ist eine Zeichenfolge, die einen DRDA-Namen einer relationalen Datenbank (RDBNAM) darstellt: <p>- DB2 für z/OS akzeptiert eine 16-Byte-Zeichenfolge, wobei die Datenbank ein Speicherort vom Typ „IBM DB2 für z/OS“ ist. <br>- DB2 für i akzeptiert eine 18-Byte-Zeichenfolge, wobei die Datenbank eine relationale Datenbank vom Typ „IBM DB2 für i“ ist. <br>- DB2 für LUW akzeptiert eine 8-Byte-Zeichenfolge. |
| **Authentifizierung** | Ja | Der Authentifizierungstyp für Ihre Verbindung, z.B. „Basic“ <p><p>**Hinweis**: Wählen Sie diesen Wert aus der Liste, die „Basic“ oder „Windows“ (Kerberos) enthält. |
| **Benutzername** | Ja | Ihr Benutzername für die Datenbank <p><p>**Hinweis**: Dieser Wert ist eine Zeichenfolge, deren Länge auf der betreffenden Datenbank basiert: <p><p>- DB2 für z/OS akzeptiert eine 8-Byte-Zeichenfolge. <br>- DB2 für i akzeptiert eine 10-Byte-Zeichenfolge. <br>- DB2 für Linux oder UNIX akzeptiert eine 8-Byte-Zeichenfolge. <br>- DB2 für Windows akzeptiert eine 30-Byte-Zeichenfolge. |
| **Kennwort** | Ja | Ihr Kennwort für die Datenbank |
| **Gateway** | Ja | Der Name Ihres installierten lokalen Datengateways <p><p>**Hinweis**: Wählen Sie diesen Wert aus der Liste, die alle installierten Datengateways in Ihrem Azure-Abonnement und der Ressourcengruppe enthält. |
||||

Beispiel:

![Verbindungsdetails für die lokalen Datenbanken](./media/connectors-create-api-db2/create-db2-on-premises-connection.png)

### <a name="view-output-tables"></a>Anzeigen von Ausgabetabellen

Um Ihre Logik-App manuell auszuführen, wählen Sie in der Designersymbolleiste **Ausführen**. Nachdem Ihre Logik-App ausgeführt wurde, können Sie die Ausgabe aus der Ausführung anzeigen.

1. Wählen Sie in Ihrem Logik-App-Menü **Übersicht** aus.

1. Wählen Sie unter **Zusammenfassung** im Abschnitt **Ausführungsverlauf** die letzte Ausführung (erstes Element in der Liste).

   ![Anzeigen des Ausführungsverlaufs](./media/connectors-create-api-db2/run-history.png)

1. Unter **Logik-App-Ausführung** können Sie jetzt den Status, Eingaben und Ausgaben für jeden Schritt in Ihrer Logik-App anschauen.
Erweitern Sie die Aktion **Tabellen abrufen**.

   ![Aktion erweitern](./media/connectors-create-api-db2/expand-action-step.png)

1. Um die Eingaben anzuzeigen, wählen Sie **Unformatierte Eingaben anzeigen**.

1. Um die Ausgaben anzuzeigen, wählen Sie **Unformatierte Ausgaben anzeigen**.

   Die Ausgaben enthalten eine Liste der Tabellen.

   ![Anzeigen von Ausgabetabellen](./media/connectors-create-api-db2/db2-connector-get-tables-outputs.png)

## <a name="get-row"></a>Zeile abrufen

Um einen Datensatz in einer DB2-Datenbanktabelle abzurufen, verwenden Sie die Aktion **Zeile abrufen** in Ihrer Logik-App. Diese Aktion führt eine DB2-`SELECT WHERE`-Anweisung aus, z.B. `SELECT FROM AREA WHERE AREAID = '99999'`.

1. Wenn Sie in Ihrer Logik-App bisher noch keine DB2-Aktionen verwendet haben, schauen Sie sich die Schritte in [DB2-Aktion hinzufügen – Tabellen abrufen](#add-db2-action) an, aber fügen Sie stattdessen die Aktion **Zeile abrufen** hin, und kehren Sie anschließend an diese Stelle zurück, um fortzufahren.

   Nachdem Sie die Aktion **Zeile abrufen** hinzugefügt haben, sieht Ihre Beispiel-Logik-App folgendermaßen aus:

   ![Aktion „Zeile abrufen“](./media/connectors-create-api-db2/db2-get-row-action.png)

1. Geben Sie Werte für alle erforderlichen Eigenschaften (*) an. Nachdem Sie eine Tabelle ausgewählt haben, zeigt die Aktion die relevanten Eigenschaften an, die sich Datensätze in dieser Tabelle beziehen.

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Tabellenname** | Ja | Die Tabelle, die den gewünschten Datensatz enthält, wie z.B. „AREA“ in diesem Beispiel |
   | **Bereichs-ID** | Ja | Die ID für den gewünschten Datensatz, wie z.B. „99999“ in diesem Beispiel |
   ||||

   ![Screenshot: Aktion „Zeile abrufen (Vorschau)“ mit der geöffneten Liste „Tabellenname“ und dem ausgewählten Wert „AREA“](./media/connectors-create-api-db2/db2-get-row-action-select-table.png)

1. Wenn Sie fertig sind, wählen Sie auf der Symbolleiste des Designers die Option **Speichern** aus.

### <a name="view-output-row"></a>Ausgabezeile anzeigen

Um Ihre Logik-App manuell auszuführen, wählen Sie in der Designersymbolleiste **Ausführen**. Nachdem Ihre Logik-App ausgeführt wurde, können Sie die Ausgabe aus der Ausführung anzeigen.

1. Wählen Sie in Ihrem Logik-App-Menü **Übersicht** aus.

1. Wählen Sie unter **Zusammenfassung** im Abschnitt **Ausführungsverlauf** die letzte Ausführung (erstes Element in der Liste).

1. Unter **Logik-App-Ausführung** können Sie jetzt den Status, Eingaben und Ausgaben für jeden Schritt in Ihrer Logik-App anschauen.
Erweitern Sie die Aktion **Zeile abrufen**.

1. Um die Eingaben anzuzeigen, wählen Sie **Unformatierte Eingaben anzeigen**.

1. Um die Ausgaben anzuzeigen, wählen Sie **Unformatierte Ausgaben anzeigen**.

   Die Ausgaben enthalten die angegebene Zeile.

   ![Ausgabezeile anzeigen](./media/connectors-create-api-db2/db2-connector-get-row-outputs.png)

## <a name="get-rows"></a>Zeilen abrufen

Um alle Datensätze in einer DB2-Datenbanktabelle abzurufen, verwenden Sie die Aktion **Zeilen abrufen** in Ihrer Logik-App. Diese Aktion führt eine DB2-`SELECT`-Anweisung aus, z.B. `SELECT * FROM AREA`.

1. Wenn Sie in Ihrer Logik-App bisher noch keine DB2-Aktionen verwendet haben, schauen Sie sich die Schritte in [DB2-Aktion hinzufügen – Tabellen abrufen](#add-db2-action) an, aber fügen Sie stattdessen die Aktion **Zeilen abrufen** hin, und kehren Sie anschließend an diese Stelle zurück, um fortzufahren.

   Nachdem Sie die Aktion **Zeilen abrufen** hinzugefügt haben, sieht Ihre Beispiel-Logik-App folgendermaßen aus:

   ![Aktion „Zeilen abrufen“](./media/connectors-create-api-db2/db2-get-rows-action.png)

1. Öffnen Sie die Liste **Tabellenname** und wählen Sie die gewünschte Tabelle, in diesem Beispiel „AREA“:

   ![Screenshot: Aktion „Zeile abrufen (Vorschau)“ mit dem ausgewählten Wert „AREA“ in der Liste „Tabellenname“](./media/connectors-create-api-db2/db2-get-rows-action-select-table.png)

1. Um einen Filter oder eine Abfrage für die Ergebnisse anzugeben, wählen Sie **Erweiterte Optionen anzeigen**.

1. Wenn Sie fertig sind, wählen Sie auf der Symbolleiste des Designers die Option **Speichern** aus.

### <a name="view-output-rows"></a>Ausgabezeilen anzeigen

Um Ihre Logik-App manuell auszuführen, wählen Sie in der Designersymbolleiste **Ausführen**. Nachdem Ihre Logik-App ausgeführt wurde, können Sie die Ausgabe aus der Ausführung anzeigen.

1. Wählen Sie in Ihrem Logik-App-Menü **Übersicht** aus.

1. Wählen Sie unter **Zusammenfassung** im Abschnitt **Ausführungsverlauf** die letzte Ausführung (erstes Element in der Liste).

1. Unter **Logik-App-Ausführung** können Sie jetzt den Status, Eingaben und Ausgaben für jeden Schritt in Ihrer Logik-App anschauen.
Erweitern Sie die Aktion **Zeilen abrufen**.

1. Um die Eingaben anzuzeigen, wählen Sie **Unformatierte Eingaben anzeigen**.

1. Um die Ausgaben anzuzeigen, wählen Sie **Unformatierte Ausgaben anzeigen**.

   Die Ausgaben enthalten alle Datensätze aus der angegebenen Tabelle.

   ![Ausgabezeilen anzeigen](./media/connectors-create-api-db2/db2-connector-get-rows-outputs.png)

## <a name="insert-row"></a>Zeile einfügen

Um einen einzelnen Datensatz zu einer DB2-Datenbanktabelle hinzuzufügen, verwenden Sie die Aktion **Zeile einfügen** in Ihrer Logik-App. Diese Aktion führt eine DB2-`INSERT`-Anweisung aus, z.B. `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

1. Wenn Sie in Ihrer Logik-App bisher noch keine DB2-Aktionen verwendet haben, schauen Sie sich die Schritte in [DB2-Aktion hinzufügen – Tabellen abrufen](#add-db2-action) an, aber fügen Sie stattdessen die Aktion **Zeile einfügen** hin, und kehren Sie anschließend an diese Stelle zurück, um fortzufahren.

   Nachdem Sie die Aktion **Zeile einfügen** hinzugefügt haben, sieht Ihre Beispiel-Logik-App folgendermaßen aus:

   ![Aktion „Zeile einfügen“](./media/connectors-create-api-db2/db2-insert-row-action.png)

1. Geben Sie Werte für alle erforderlichen Eigenschaften (*) an. Nachdem Sie eine Tabelle ausgewählt haben, zeigt die Aktion die relevanten Eigenschaften an, die sich Datensätze in dieser Tabelle beziehen.

   Für dieses Beispiel werden folgende Eigenschaften verwendet:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Tabellenname** | Ja | Die Tabelle, in der der Datensatz hinzugefügt werden soll, z.B. „AREA“ |
   | **Bereichs-ID** | Ja | Die ID für den hinzuzufügenden Bereich, z.B. „99999“ |
   | **Bereichsbeschreibung** | Ja | Die Beschrei für den hinzuzufügenden Bereich, z.B. „Area 99999“ |
   | **Regions-ID** | Ja | Die ID für die hinzuzufügenden Region, z.B. „102“ |
   |||| 

   Beispiel:

   ![Screenshot: Logik-App-Designer mit der Aktion „Insert row (Preview)“ (Zeile einfügen (Vorschau)) und Beispieleigenschaftswerten](./media/connectors-create-api-db2/db2-insert-row-action-select-table.png)

1. Wenn Sie fertig sind, wählen Sie auf der Symbolleiste des Designers die Option **Speichern** aus.

### <a name="view-insert-row-outputs"></a>Ausgabe vom Einfügen der Zeile anzeigen

Um Ihre Logik-App manuell auszuführen, wählen Sie in der Designersymbolleiste **Ausführen**. Nachdem Ihre Logik-App ausgeführt wurde, können Sie die Ausgabe aus der Ausführung anzeigen.

1. Wählen Sie in Ihrem Logik-App-Menü **Übersicht** aus.

1. Wählen Sie unter **Zusammenfassung** im Abschnitt **Ausführungsverlauf** die letzte Ausführung (erstes Element in der Liste).

1. Unter **Logik-App-Ausführung** können Sie jetzt den Status, Eingaben und Ausgaben für jeden Schritt in Ihrer Logik-App anschauen.
Erweitern Sie die Aktion **Zeile einfügen**.

1. Um die Eingaben anzuzeigen, wählen Sie **Unformatierte Eingaben anzeigen**.

1. Um die Ausgaben anzuzeigen, wählen Sie **Unformatierte Ausgaben anzeigen**.

   Die Ausgaben enthalten alle Datensätze, die Sie zur angegebenen Tabelle hinzugefügt haben.

   ![Anzeigen der Ausgabe mit der eingefügten Zeile](./media/connectors-create-api-db2/db2-connector-insert-row-outputs.png)

## <a name="update-row"></a>Zeile aktualisieren

Um einen einzelnen Datensatz in einer DB2-Datenbanktabelle zu aktualisieren, verwenden Sie die Aktion **Zeile aktualisieren** in Ihrer Logik-App. Diese Aktion führt eine DB2-`UPDATE`-Anweisung aus, z.B. `UPDATE AREA SET AREAID = '99999', AREADESC = 'Updated 99999', REGIONID = 102)`.

1. Wenn Sie in Ihrer Logik-App bisher noch keine DB2-Aktionen verwendet haben, schauen Sie sich die Schritte in [DB2-Aktion hinzufügen – Tabellen abrufen](#add-db2-action) an, aber fügen Sie stattdessen die Aktion **Zeile aktualisieren** hin, und kehren Sie anschließend an diese Stelle zurück, um fortzufahren.

   Nachdem Sie die Aktion **Zeile aktualisieren** hinzugefügt haben, sieht Ihre Beispiel-Logik-App folgendermaßen aus:

   ![Aktion „Zeile aktualisieren“](./media/connectors-create-api-db2/db2-update-row-action.png)

1. Geben Sie Werte für alle erforderlichen Eigenschaften (*) an. Nachdem Sie eine Tabelle ausgewählt haben, zeigt die Aktion die relevanten Eigenschaften an, die sich Datensätze in dieser Tabelle beziehen.

   Für dieses Beispiel werden folgende Eigenschaften verwendet:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Tabellenname** | Ja | Die Tabelle, in der der Datensatz aktualisiert werden soll, z.B. „AREA“ |
   | **Zeilen-ID** | Ja | Die ID für den zu aktualisierenden Datensatz, z.B. „99999“ |
   | **Bereichs-ID** | Ja | Die neue Bereich-ID, z.B. „99999“ |
   | **Bereichsbeschreibung** | Ja | Die neue Bereichsbeschreibung, z.B. „Updated 99999“ |
   | **Regions-ID** | Ja | Die neue Regions-ID, z.B. „102“ |
   ||||

   Beispiel:

   ![Screenshot: Logik-App-Designer mit der Aktion „Update row (Preview)“ (Zeile aktualisieren (Vorschau)), unter der Sie eine Tabelle auswählen](./media/connectors-create-api-db2/db2-update-row-action-select-table.png)

1. Wenn Sie fertig sind, wählen Sie auf der Symbolleiste des Designers die Option **Speichern** aus.

### <a name="view-update-row-outputs"></a>Anzeigen der Ausgabe zum Aktualisieren der Zeile

Um Ihre Logik-App manuell auszuführen, wählen Sie in der Designersymbolleiste **Ausführen**. Nachdem Ihre Logik-App ausgeführt wurde, können Sie die Ausgabe aus der Ausführung anzeigen.

1. Wählen Sie in Ihrem Logik-App-Menü **Übersicht** aus.

1. Wählen Sie unter **Zusammenfassung** im Abschnitt **Ausführungsverlauf** die letzte Ausführung (erstes Element in der Liste).

1. Unter **Logik-App-Ausführung** können Sie jetzt den Status, Eingaben und Ausgaben für jeden Schritt in Ihrer Logik-App anschauen.
Erweitern Sie die Aktion **Zeile aktualisieren**.

1. Um die Eingaben anzuzeigen, wählen Sie **Unformatierte Eingaben anzeigen**.

1. Um die Ausgaben anzuzeigen, wählen Sie **Unformatierte Ausgaben anzeigen**.

   Die Ausgaben enthalten den Datensatz, den Sie in der angegebenen Tabelle aktualisiert haben.

   ![Anzeigen der Ausgabe mit der aktualisierten Zeile](./media/connectors-create-api-db2/db2-connector-update-row-outputs.png)

## <a name="delete-row"></a>Zeile löschen

Um einen einzelnen Datensatz aus einer DB2-Datenbanktabelle zu löschen, verwenden Sie die Aktion **Zeile löschen** in Ihrer Logik-App. Diese Aktion führt eine DB2-`DELETE`-Anweisung aus, z.B. `DELETE FROM AREA WHERE AREAID = '99999'`.

1. Wenn Sie in Ihrer Logik-App bisher noch keine DB2-Aktionen verwendet haben, schauen Sie sich die Schritte in [DB2-Aktion hinzufügen – Tabellen abrufen](#add-db2-action) an, aber fügen Sie stattdessen die Aktion **Zeile löschen** hin, und kehren Sie anschließend an diese Stelle zurück, um fortzufahren.

   Nachdem Sie die Aktion **Zeile löschen** hinzugefügt haben, sieht Ihre Beispiel-Logik-App folgendermaßen aus:

   ![Aktion „Zeile löschen“](./media/connectors-create-api-db2/db2-delete-row-action.png)

1. Geben Sie Werte für alle erforderlichen Eigenschaften (*) an. Nachdem Sie eine Tabelle ausgewählt haben, zeigt die Aktion die relevanten Eigenschaften an, die sich Datensätze in dieser Tabelle beziehen.

   Für dieses Beispiel werden folgende Eigenschaften verwendet:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Tabellenname** | Ja | Die Tabelle, in der der Datensatz gelöscht werden soll, z.B. „AREA“ |
   | **Zeilen-ID** | Ja | Die ID für den zu löschenden Datensatz, z.B. „99999“ |
   ||||

   Beispiel:

   ![Screenshot: Logik-App-Designer mit der Aktion „Delete row (Preview)“ (Zeile löschen (Vorschau)), unter der Sie eine zu löschende Tabelle auswählen](./media/connectors-create-api-db2/db2-delete-row-action-select-table.png)

1. Wenn Sie fertig sind, wählen Sie auf der Symbolleiste des Designers die Option **Speichern** aus.

### <a name="view-delete-row-outputs"></a>Anzeigen der Ausgabe mit der gelöschten Zeile

Um Ihre Logik-App manuell auszuführen, wählen Sie in der Designersymbolleiste **Ausführen**. Nachdem Ihre Logik-App ausgeführt wurde, können Sie die Ausgabe aus der Ausführung anzeigen.

1. Wählen Sie in Ihrem Logik-App-Menü **Übersicht** aus.

1. Wählen Sie unter **Zusammenfassung** im Abschnitt **Ausführungsverlauf** die letzte Ausführung (erstes Element in der Liste).

1. Unter **Logik-App-Ausführung** können Sie jetzt den Status, Eingaben und Ausgaben für jeden Schritt in Ihrer Logik-App anschauen.
Erweitern Sie die Aktion **Zeile löschen**.

1. Um die Eingaben anzuzeigen, wählen Sie **Unformatierte Eingaben anzeigen**.

1. Um die Ausgaben anzuzeigen, wählen Sie **Unformatierte Ausgaben anzeigen**.

   Die Ausgaben enthalten nicht mehr den Datensatz, den Sie aus der angegebenen Tabelle gelöscht haben.

   ![Anzeigen der Ausgabe ohne die gelöschte Zeile](./media/connectors-create-api-db2/db2-connector-delete-row-outputs.png)

## <a name="connector-reference"></a>Connector-Referenz

Weitere technische Details zu diesem Connector, z. B. Trigger, Aktionen und Grenzwerte, wie sie in der Swagger-Datei des Connectors beschrieben werden, finden Sie auf der [Referenzseite des Connectors](/connectors/db2/).

> [!NOTE]
> Für Logik-Apps in einer [Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) verwendet die mit ISE bezeichnete Version dieses Connectors stattdessen die [ISE-Nachrichtengrenzwerte](../logic-apps/logic-apps-limits-and-config.md#message-size-limits).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)

