---
title: Herstellen einer Verbindung mit einer IBM Informix-Datenbank – Azure Logic Apps | Microsoft-Dokumentation
description: Verwalten von Ressourcen mit IBM Informix-REST-APIs und Azure Logic Apps
author: gplarsen
manager: jeconnoc
ms.author: plarsen
ms.date: 09/26/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 6004c02f190bbfcf374b3b5d2a5c478f0e52c961
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60690904"
---
# <a name="get-started-with-the-informix-connector"></a>Erste Schritte mit dem Informix-Connector
Mit dem Microsoft Connector für Informix werden Logik-Apps mit Ressourcen verbunden, die in einer IBM Informix-Datenbank gespeichert sind. Der Informix-Connector enthält einen Microsoft-Client zum Kommunizieren mit Informix-Remoteservercomputern über ein TCP/IP-Netzwerk. Dies umfasst Clouddatenbanken, z.B. IBM Informix für Windows, in Azure sowie lokale Datenbanken unter Verwendung des lokalen Datengateways. Siehe die [Liste mit unterstützten IBM Informix-Plattformen und -Versionen](connectors-create-api-informix.md#supported-informix-platforms-and-versions) (in diesem Thema).

Der Connector unterstützt die folgenden Datenbankvorgänge:

* Datenbanktabellen auflisten
* Eine Zeile mit SELECT lesen
* Alle Zeilen mit SELECT lesen
* Eine Zeile mit INSERT hinzufügen
* Eine Zeile mit UPDATE ändern
* Eine Zeile mit DELETE entfernen

In diesem Thema wird veranschaulicht, wie Sie den Connector in einer Logik-App zum Verarbeiten von Datenbankvorgängen verwenden.

Weitere Informationen zu Logik-Apps finden Sie unter [Erstellen einer Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="available-actions"></a>Verfügbare Aktionen
Der Connector unterstützt die folgenden Logik-App-Aktionen:

* GetTables
* GetRow
* GetRows
* InsertRow
* UpdateRow
* DeleteRow

## <a name="list-tables"></a>Auflisten von Tabellen
Das Erstellen einer Logik-App für einen beliebigen Vorgang umfasst viele Schritte, die über das Microsoft Azure-Portal ausgeführt werden.

Innerhalb der Logik-App können Sie eine Aktion zum Auflisten von Tabellen in einer Informix-Datenbank hinzufügen. Die Aktion weist den Connector an, eine Informix-Schemaanweisung zu verarbeiten, z. B. `CALL SYSIBM.SQLTABLES`.

### <a name="create-a-logic-app"></a>Erstellen einer Logik-App
1. Wählen Sie im **Azure-Startmenü** die Optionen **+** (Pluszeichen), **Web und mobil** und dann **Logik-App**.
2. Geben Sie **Name**, z. B. `InformixgetTables`, **Abonnement**, **Ressourcengruppe**, **Speicherort** und **App Service-Plan** ein. Wählen Sie **An Dashboard anheften** und dann **Erstellen** aus.

### <a name="add-a-trigger-and-action"></a>Hinzufügen eines Triggers und einer Aktion
1. Klicken Sie im **Logik-App-Designer** in der Liste **Vorlagen** auf **Leere Logik-App**.
2. Klicken Sie in der Liste **Trigger** auf **Wiederholung**. 
3. Klicken Sie für den Trigger **Wiederholung** auf **Bearbeiten**. Wählen Sie die Dropdownliste **Häufigkeit** aus, um die Option **Tag** auszuwählen, und legen Sie **Intervall** auf den Typ **7** fest.  
4. Wählen Sie das Feld **+ Neuer Schritt** und dann **Aktion hinzufügen** aus.
5. Geben Sie in der Liste **Aktionen** im Bearbeitungsfeld **Nach weiteren Aktionen suchen** den Text **informix** ein, und wählen Sie dann **Informix - Get tables (Preview)** (Informix – Tabellen abrufen [Vorschau]) aus.
   
   ![](./media/connectors-create-api-informix/InformixconnectorActions.png)  
6. Aktivieren Sie im Konfigurationsbereich **Informix - Get tables** (Informix – Tabellen abrufen) das **Kontrollkästchen**, um **Verbinden über lokales Datengateway** zu aktivieren. Sie sehen, dass die Cloudeinstellungen in die lokalen Einstellungen geändert werden.
   
   * Geben Sie einen Wert für **Server** als Adresse bzw. in der Form „Alias:Portnummer“ ein. Geben Sie beispielsweise `ibmserver01:9089`.
   * Geben Sie einen Wert für **Datenbank** ein. Geben Sie beispielsweise `nwind`.
   * Wählen Sie einen Wert für **Authentifizierung** aus. Wählen Sie z. B. **Standard**.
   * Geben Sie einen Wert für **Benutzername** ein. Geben Sie beispielsweise `informix`.
   * Geben Sie einen Wert für **Kennwort** ein. Geben Sie beispielsweise `Password1`.
   * Wählen Sie einen Wert für **Gateway** aus. Wählen Sie z. B. **datagateway01** aus.
7. Wählen Sie **Erstellen** und dann **Speichern** aus. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)
8. Wählen Sie auf dem Blatt **InformixgetTables** in der Liste **Alle Testläufe** unter **Zusammenfassung** den zuerst aufgeführten Eintrag (letzte Ausführung) aus.
9. Wählen Sie auf dem Blatt **Logik-App-Ausführung** die Option **Testlaufdetails** aus. Wählen Sie in der Liste **Aktion** den Eintrag **Get_tables** aus. Der Wert für **Status** sollte **Erfolgreich** lauten. Wählen Sie den Link **Eingaben** aus, um die Eingaben anzuzeigen. Wählen Sie den Link **Ausgaben**aus, um die Ausgaben anzuzeigen, die eine Liste mit Tabellen enthalten sollten.
   
   ![](./media/connectors-create-api-informix/InformixconnectorGetTablesLogicAppRunOutputs.png)

## <a name="create-the-connections"></a>Erstellen der Verbindungen
Der Connector unterstützt Verbindungen mit lokal und in der Cloud gehosteten Datenbanken durch Verwenden der folgenden Verbindungseigenschaften. 

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| server |Erforderlich. Akzeptiert einen Zeichenfolgenwert im IPv4- oder IPv6-Format, der für eine TCP/IP-Adresse oder einen Alias steht, gefolgt von einer mit Doppelpunkt angefügten TCP/IP-Portnummer. |
| database |Erforderlich. Akzeptiert einen Zeichenfolgenwert , der einen DRDA-Namen einer relationalen Datenbank darstellt. Informix akzeptiert eine 128-Byte-Zeichenfolge („database“ ist ein „IBM Informix database name“ (dbname)). |
| authentication |Optional. Akzeptiert einen Listenelementwert, und zwar entweder „Basic“ oder „Windows“ (Kerberos). |
| userName |Erforderlich. Akzeptiert einen Zeichenfolgenwert. |
| password |Erforderlich. Akzeptiert einen Zeichenfolgenwert. |
| gateway |Erforderlich. Akzeptiert einen Listenelementwert, der für das lokale Datengateway steht, das für Logik-Apps in der Speichergruppe definiert ist. |

## <a name="create-the-on-premises-gateway-connection"></a>Erstellen einer Verbindung über das lokale Gateway
Dieser Connector kann auf eine lokale Informix-Datenbank über das lokale Datengateway zugreifen. Weitere Informationen finden Sie in den Themen zum Gateway. 

1. Aktivieren Sie im Konfigurationsbereich **Gateways** das **Kontrollkästchen**, um **Connect via gateway** (Über Gateway verbinden) zu aktivieren. Sie sehen, dass die Cloudeinstellungen in die lokalen Einstellungen geändert werden.
2. Geben Sie einen Wert für **Server** als Adresse bzw. in der Form „Alias:Portnummer“ ein. Geben Sie beispielsweise `ibmserver01:9089`.
3. Geben Sie einen Wert für **Datenbank** ein. Geben Sie beispielsweise `nwind`.
4. Wählen Sie einen Wert für **Authentifizierung** aus. Wählen Sie z. B. **Standard**.
5. Geben Sie einen Wert für **Benutzername** ein. Geben Sie beispielsweise `informix`.
6. Geben Sie einen Wert für **Kennwort** ein. Geben Sie beispielsweise `Password1`.
7. Wählen Sie einen Wert für **Gateway** aus. Wählen Sie z. B. **datagateway01** aus.
8. Klicken Sie auf **Erstellen** , um fortzufahren. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorOnPremisesDataGatewayConnection.png)

## <a name="create-the-cloud-connection"></a>Erstellen der Cloudverbindung
Der Connector kann auf eine Informix-Clouddatenbank zugreifen. 

1. Lassen Sie im Konfigurationsbereich **Gateways** das **Kontrollkästchen** deaktiviert (**Connect via gateway** (Verbinden über Gateway)). 
2. Geben Sie einen Wert für **Verbindungsname** ein. Geben Sie beispielsweise `hisdemo2`.
3. Geben Sie einen Wert für **Informix-Servername** als Adresse bzw. in der Form „Alias:Portnummer“ ein. Geben Sie beispielsweise `hisdemo2.cloudapp.net:9089`.
4. Geben Sie einen Wert für **Informix-Datenbankname** ein. Geben Sie beispielsweise `nwind`.
5. Geben Sie einen Wert für **Benutzername** ein. Geben Sie beispielsweise `informix`.
6. Geben Sie einen Wert für **Kennwort** ein. Geben Sie beispielsweise `Password1`.
7. Klicken Sie auf **Erstellen** , um fortzufahren. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)

## <a name="fetch-all-rows-using-select"></a>Abrufen aller Zeilen mit SELECT
Sie können eine Logik-App-Aktion zum Abrufen aller Zeilen in einer Informix-Tabelle erstellen. Diese Aktion weist den Connector an, eine Informix SELECT-Anweisung zu verarbeiten, z.B. `SELECT * FROM AREA`.

### <a name="create-a-logic-app"></a>Erstellen einer Logik-App
1. Wählen Sie im **Azure-Startmenü** die Optionen **+** (Pluszeichen), **Web und mobil** und dann **Logik-App**.
2. Geben Sie **Name** (z. B. „**InformixgetRows**“), **Abonnement**, **Ressourcengruppe**, **Speicherort** und **App Service-Plan** ein. Wählen Sie **An Dashboard anheften** und dann **Erstellen** aus.

### <a name="add-a-trigger-and-action"></a>Hinzufügen eines Triggers und einer Aktion
1. Klicken Sie im **Logik-App-Designer** in der Liste **Vorlagen** auf **Leere Logik-App**.
2. Klicken Sie in der Liste **Trigger** auf **Wiederholung**. 
3. Klicken Sie für den Trigger **Wiederholung** auf **Bearbeiten**. Wählen Sie die Dropdownliste **Häufigkeit** aus, um die Option **Tag** auszuwählen, und legen Sie **Intervall** auf den Typ **7** fest. 
4. Wählen Sie das Feld **+ Neuer Schritt** und dann **Aktion hinzufügen** aus.
5. Geben Sie in der Liste **Aktionen** im Bearbeitungsfeld **Nach weiteren Aktionen suchen** den Text **informix** ein, und wählen Sie dann **Informix - Get rows (Preview)** (Informix – Zeilen abrufen [Vorschau]) aus.
6. Wählen Sie in der Aktion **Get rows (Preview)** (Zeilen abrufen [Vorschau]) **Verbindung ändern** aus.
7. Wählen Sie im Konfigurationsbereich **Verbindungen** die Option **Neu erstellen** aus. 
   
    ![](./media/connectors-create-api-informix/InformixconnectorNewConnection.png)
8. Lassen Sie im Konfigurationsbereich **Gateways** das **Kontrollkästchen** deaktiviert (**Connect via gateway** (Verbinden über Gateway)).
   
   * Geben Sie einen Wert für **Verbindungsname** ein. Geben Sie beispielsweise `HISDEMO2`.
   * Geben Sie einen Wert für **Informix-Servername** als Adresse bzw. in der Form „Alias:Portnummer“ ein. Geben Sie beispielsweise `HISDEMO2.cloudapp.net:9089`.
   * Geben Sie einen Wert für **Informix-Datenbankname** ein. Geben Sie beispielsweise `NWIND`.
   * Geben Sie einen Wert für **Benutzername** ein. Geben Sie beispielsweise `informix`.
   * Geben Sie einen Wert für **Kennwort** ein. Geben Sie beispielsweise `Password1`.
9. Klicken Sie auf **Erstellen** , um fortzufahren.
   
    ![](./media/connectors-create-api-informix/InformixconnectorCloudConnection.png)
10. Wählen Sie in der Liste **Tabellenname** den **Pfeil nach unten** und dann **BEREICH** aus.
11. Optional können Sie **Erweiterte Optionen anzeigen** auswählen, um Abfrageoptionen anzugeben.
12. Wählen Sie **Speichern** aus. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsTableName.png)
13. Wählen Sie auf dem Blatt **InformixgetRows** in der Liste **Alle Testläufe** unter **Zusammenfassung** den zuerst aufgeführten Eintrag (letzte Ausführung) aus.
14. Wählen Sie auf dem Blatt **Logik-App-Ausführung** die Option **Testlaufdetails** aus. Wählen Sie in der Liste **Aktion** den Eintrag **Get_rows** aus. Der Wert für **Status** sollte **Erfolgreich** lauten. Wählen Sie den Link **Eingaben** aus, um die Eingaben anzuzeigen. Wählen Sie den Link **Ausgaben**aus, um die Ausgaben anzuzeigen, die eine Liste mit Zeilen enthalten sollten.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowsOutputs.png)

## <a name="add-one-row-using-insert"></a>Eine Zeile mit INSERT hinzufügen
Sie können eine Logik-App-Aktion zum Hinzufügen einer Zeile zu einer Informix-Tabelle erstellen. Die Aktion weist den Connector an, eine INSERT-Anweisung von Informix zu verarbeiten, z. B. `INSERT INTO AREA (AREAID, AREADESC, REGIONID) VALUES ('99999', 'Area 99999', 102)`.

### <a name="create-a-logic-app"></a>Erstellen einer Logik-App
1. Wählen Sie im **Azure-Startmenü** die Optionen **+** (Pluszeichen), **Web und mobil** und dann **Logik-App**.
2. Geben Sie **Name**, z. B. `InformixinsertRow`, **Abonnement**, **Ressourcengruppe**, **Speicherort** und **App Service-Plan** ein. Wählen Sie **An Dashboard anheften** und dann **Erstellen** aus.

### <a name="add-a-trigger-and-action"></a>Hinzufügen eines Triggers und einer Aktion
1. Klicken Sie im **Logik-App-Designer** in der Liste **Vorlagen** auf **Leere Logik-App**.
2. Klicken Sie in der Liste **Trigger** auf **Wiederholung**. 
3. Klicken Sie für den Trigger **Wiederholung** auf **Bearbeiten**. Wählen Sie die Dropdownliste **Häufigkeit** aus, um die Option **Tag** auszuwählen, und legen Sie **Intervall** auf den Typ **7** fest. 
4. Wählen Sie das Feld **+ Neuer Schritt** und dann **Aktion hinzufügen** aus.
5. Geben Sie in der Liste **Aktionen** im Bearbeitungsfeld **Nach weiteren Aktionen suchen** den Text **informix** ein, und wählen Sie dann **Informix - Insert row (Preview)** (Informix – Zeile einfügen [Vorschau]) aus.
6. Wählen Sie in der Aktion **Get rows (Preview)** (Zeilen abrufen [Vorschau]) **Verbindung ändern** aus. 
7. Wählen Sie im Konfigurationsbereich **Verbindungen** eine Verbindung aus. Wählen Sie z. B. **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Wählen Sie in der Liste **Tabellenname** den **Pfeil nach unten** und dann **BEREICH** aus.
9. Geben Sie Werte für alle erforderlichen Spalten ein (siehe rotes Sternchen). Geben Sie beispielsweise `99999` für **AREAID**, `Area 99999` und `102` für **REGIONID** ein. 
10. Wählen Sie **Speichern** aus.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowValues.png)
11. Wählen Sie auf dem Blatt **InformixinsertRow** in der Liste **Alle Testläufe** unter **Zusammenfassung** den zuerst aufgeführten Eintrag (letzte Ausführung) aus.
12. Wählen Sie auf dem Blatt **Logik-App-Ausführung** die Option **Testlaufdetails** aus. Wählen Sie in der Liste **Aktion** den Eintrag **Get_rows** aus. Der Wert für **Status** sollte **Erfolgreich** lauten. Wählen Sie den Link **Eingaben** aus, um die Eingaben anzuzeigen. Wählen Sie den Link **Ausgaben**aus, um die Ausgaben anzuzeigen, die die neue Zeile enthalten sollten.
    
    ![](./media/connectors-create-api-informix/InformixconnectorInsertRowOutputs.png)

## <a name="fetch-one-row-using-select"></a>Abrufen einer Zeile mit SELECT
Sie können eine Logik-App-Aktion zum Abrufen einer Zeile aus einer Informix-Tabelle erstellen. Diese Aktion weist den Connector an, eine SELECT WHERE-Anweisung von Informix zu verarbeiten, z. B. `SELECT FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Erstellen einer Logik-App
1. Wählen Sie im **Azure-Startmenü** die Optionen **+** (Pluszeichen), **Web und mobil** und dann **Logik-App**.
2. Geben Sie **Name**, z. B. `InformixgetRow`, **Abonnement**, **Ressourcengruppe**, **Speicherort** und **App Service-Plan** ein. Wählen Sie **An Dashboard anheften** und dann **Erstellen** aus.

### <a name="add-a-trigger-and-action"></a>Hinzufügen eines Triggers und einer Aktion
1. Klicken Sie im **Logik-App-Designer** in der Liste **Vorlagen** auf **Leere Logik-App**.
2. Klicken Sie in der Liste **Trigger** auf **Wiederholung**. 
3. Klicken Sie für den Trigger **Wiederholung** auf **Bearbeiten**. Wählen Sie die Dropdownliste **Häufigkeit** aus, um die Option **Tag** auszuwählen, und legen Sie **Intervall** auf den Typ **7** fest. 
4. Wählen Sie das Feld **+ Neuer Schritt** und dann **Aktion hinzufügen** aus.
5. Geben Sie in der Liste **Aktionen** im Bearbeitungsfeld **Nach weiteren Aktionen suchen** den Text **informix** ein, und wählen Sie dann **Informix - Get rows (Preview)** (Informix – Zeilen abrufen [Vorschau]) aus.
6. Wählen Sie in der Aktion **Get rows (Preview)** (Zeilen abrufen [Vorschau]) **Verbindung ändern** aus. 
7. Wählen Sie im Konfigurationsbereich **Verbindungen** eine vorhandene Verbindung aus. Wählen Sie z. B. **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Wählen Sie in der Liste **Tabellenname** den **Pfeil nach unten** und dann **BEREICH** aus.
9. Geben Sie Werte für alle erforderlichen Spalten ein (siehe rotes Sternchen). Geben Sie beispielsweise `99999` für **AREAID**ein. 
10. Optional können Sie **Erweiterte Optionen anzeigen** auswählen, um Abfrageoptionen anzugeben.
11. Wählen Sie **Speichern** aus. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowValues.png)
12. Wählen Sie auf dem Blatt **InformixgetRow** in der Liste **Alle Testläufe** unter **Zusammenfassung** den zuerst aufgeführten Eintrag (letzte Ausführung) aus.
13. Wählen Sie auf dem Blatt **Logik-App-Ausführung** die Option **Testlaufdetails** aus. Wählen Sie in der Liste **Aktion** den Eintrag **Get_rows** aus. Der Wert für **Status** sollte **Erfolgreich** lauten. Wählen Sie den Link **Eingaben** aus, um die Eingaben anzuzeigen. Wählen Sie den Link **Ausgaben**aus, um die Ausgaben anzuzeigen, die die Zeile enthalten sollten.
    
    ![](./media/connectors-create-api-informix/InformixconnectorGetRowOutputs.png)

## <a name="change-one-row-using-update"></a>Ändern einer Zeile mit UPDATE
Sie können eine Logik-App-Aktion zum Ändern einer Zeile in einer Informix-Tabelle erstellen. Die Aktion weist den Connector an, eine UPDATE-Anweisung von Informix zu verarbeiten, z. B. `UPDATE AREA SET AREAID = '99999', AREADESC = 'Area 99999', REGIONID = 102)`.

### <a name="create-a-logic-app"></a>Erstellen einer Logik-App
1. Wählen Sie im **Azure-Startmenü** die Optionen **+** (Pluszeichen), **Web und mobil** und dann **Logik-App**.
2. Geben Sie **Name**, z. B. `InformixupdateRow`, **Abonnement**, **Ressourcengruppe**, **Speicherort** und **App Service-Plan** ein. Wählen Sie **An Dashboard anheften** und dann **Erstellen** aus.

### <a name="add-a-trigger-and-action"></a>Hinzufügen eines Triggers und einer Aktion
1. Klicken Sie im **Logik-App-Designer** in der Liste **Vorlagen** auf **Leere Logik-App**.
2. Klicken Sie in der Liste **Trigger** auf **Wiederholung**. 
3. Klicken Sie für den Trigger **Wiederholung** auf **Bearbeiten**. Wählen Sie die Dropdownliste **Häufigkeit** aus, um die Option **Tag** auszuwählen, und legen Sie **Intervall** auf den Typ **7** fest. 
4. Wählen Sie das Feld **+ Neuer Schritt** und dann **Aktion hinzufügen** aus.
5. Geben Sie in der Liste **Aktionen** im Bearbeitungsfeld **Nach weiteren Aktionen suchen** den Text **informix** ein, und wählen Sie dann **Informix - Update row (Preview)** (Informix – Zeile aktualisieren [Vorschau]) aus.
6. Wählen Sie in der Aktion **Get rows (Preview)** (Zeilen abrufen [Vorschau]) **Verbindung ändern** aus. 
7. Wählen Sie im Konfigurationsbereich **Verbindungen** eine vorhandene Verbindung aus. Wählen Sie z. B. **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Wählen Sie in der Liste **Tabellenname** den **Pfeil nach unten** und dann **BEREICH** aus.
9. Geben Sie Werte für alle erforderlichen Spalten ein (siehe rotes Sternchen). Geben Sie beispielsweise `99999` für **AREAID**, `Updated 99999` und `102` für **REGIONID** ein. 
10. Wählen Sie **Speichern** aus. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowValues.png)
11. Wählen Sie auf dem Blatt **InformixupdateRow** in der Liste **Alle Testläufe** unter **Zusammenfassung** den zuerst aufgeführten Eintrag (letzte Ausführung) aus.
12. Wählen Sie auf dem Blatt **Logik-App-Ausführung** die Option **Testlaufdetails** aus. Wählen Sie in der Liste **Aktion** den Eintrag **Get_rows** aus. Der Wert für **Status** sollte **Erfolgreich** lauten. Wählen Sie den Link **Eingaben** aus, um die Eingaben anzuzeigen. Wählen Sie den Link **Ausgaben**aus, um die Ausgaben anzuzeigen, die die neue Zeile enthalten sollten.
    
    ![](./media/connectors-create-api-informix/InformixconnectorUpdateRowOutputs.png)

## <a name="remove-one-row-using-delete"></a>Eine Zeile mit DELETE entfernen
Sie können eine Logik-App-Aktion zum Entfernen einer Zeile aus einer Informix-Tabelle erstellen. Die Aktion weist den Connector an, eine UPDATE-Anweisung von Informix zu verarbeiten, z. B. `DELETE FROM AREA WHERE AREAID = '99999'`.

### <a name="create-a-logic-app"></a>Erstellen einer Logik-App
1. Wählen Sie im **Azure-Startmenü** die Optionen **+** (Pluszeichen), **Web und mobil** und dann **Logik-App**.
2. Geben Sie **Name**, z. B. `InformixdeleteRow`, **Abonnement**, **Ressourcengruppe**, **Speicherort** und **App Service-Plan** ein. Wählen Sie **An Dashboard anheften** und dann **Erstellen** aus.

### <a name="add-a-trigger-and-action"></a>Hinzufügen eines Triggers und einer Aktion
1. Klicken Sie im **Logik-App-Designer** in der Liste **Vorlagen** auf **Leere Logik-App**.
2. Klicken Sie in der Liste **Trigger** auf **Wiederholung**. 
3. Klicken Sie für den Trigger **Wiederholung** auf **Bearbeiten**. Wählen Sie die Dropdownliste **Häufigkeit** aus, um die Option **Tag** auszuwählen, und legen Sie **Intervall** auf den Typ **7** fest. 
4. Wählen Sie das Feld **+ Neuer Schritt** und dann **Aktion hinzufügen** aus.
5. Geben Sie in der Liste **Aktionen** im Bearbeitungsfeld **Nach weiteren Aktionen suchen** den Text **informix** ein, und wählen Sie dann **Informix - Delete row (Preview)** (Informix – Zeile löschen [Vorschau]) aus.
6. Wählen Sie in der Aktion **Get rows (Preview)** (Zeilen abrufen [Vorschau]) **Verbindung ändern** aus. 
7. Wählen Sie im Konfigurationsbereich **Verbindungen** eine vorhandene Verbindung aus. Wählen Sie z. B. **hisdemo2**.
   
    ![](./media/connectors-create-api-informix/InformixconnectorChangeConnection.png)
8. Wählen Sie in der Liste **Tabellenname** den **Pfeil nach unten** und dann **BEREICH** aus.
9. Geben Sie Werte für alle erforderlichen Spalten ein (siehe rotes Sternchen). Geben Sie beispielsweise `99999` für **AREAID**ein. 
10. Wählen Sie **Speichern** aus. 
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowValues.png)
11. Wählen Sie auf dem Blatt **InformixdeleteRow** in der Liste **Alle Testläufe** unter **Zusammenfassung** den zuerst aufgeführten Eintrag (letzte Ausführung) aus.
12. Wählen Sie auf dem Blatt **Logik-App-Ausführung** die Option **Testlaufdetails** aus. Wählen Sie in der Liste **Aktion** den Eintrag **Get_rows** aus. Der Wert für **Status** sollte **Erfolgreich** lauten. Wählen Sie den Link **Eingaben** aus, um die Eingaben anzuzeigen. Wählen Sie den Link **Ausgaben**aus, um die Ausgaben anzuzeigen, die die gelöschte Zeile enthalten sollten.
    
    ![](./media/connectors-create-api-informix/InformixconnectorDeleteRowOutputs.png)

## <a name="supported-informix-platforms-and-versions"></a>Unterstützte Informix-Plattformen und -Versionen
Dieser Connector unterstützt die folgenden IBM Informix-Versionen, wenn diese für die Unterstützung von Clientverbindungen vom Typ „Distributed Relational Database Architecture“ (DRDA) konfiguriert sind.

* IBM Informix 12.1
* IBM Informix 11.7

## <a name="connector-specific-details"></a>Connectorspezifische Details

Zeigen Sie die in Swagger definierten Trigger und Aktionen sowie mögliche Beschränkungen in den [Connectordetails](/connectors/informix/) an. 

## <a name="next-steps"></a>Nächste Schritte
[Erstellen einer Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md) Informieren Sie sich in unserer [API-Liste](apis-list.md)über die anderen verfügbaren Connectors für Logik-Apps.

