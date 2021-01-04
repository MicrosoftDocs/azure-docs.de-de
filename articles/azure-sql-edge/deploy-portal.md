---
title: Bereitstellen von Azure SQL Edge mithilfe des Azure-Portals
description: Erfahren Sie, wie Sie Azure SQL Edge über das Azure-Portal bereitstellen.
keywords: SQL Edge bereitstellen
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 5d7d232ada814d5d3c30e7b012c6289f847d641f
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395086"
---
# <a name="deploy-azure-sql-edge"></a>Bereitstellen von Azure SQL Edge 

Azure SQL Edge ist eine optimierte Relationale-Datenbank-Engine für IoT- und Azure IoT Edge-Bereitstellungen. Azure SQL Database Edge bietet die Möglichkeit zur Erstellung eines Hochleistungs-Datenspeichers und einer Verarbeitungsschicht für IoT-Anwendungen und -Lösungen. In dieser Schnellstartanleitung erfahren Sie, wie Sie mit dem Erstellen eines Azure SQL Edge-Moduls durch Azure IoT Edge über das Azure-Portal beginnen.

## <a name="before-you-begin"></a>Voraussetzungen

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen.
* Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
* Erstellen Sie einen [Azure IoT Hub](../iot-hub/iot-hub-create-through-portal.md).
* Erstellen Sie ein [Azure IoT Edge-Gerät](../iot-edge/how-to-install-iot-edge.md).

> [!NOTE]   
> Informationen zum Bereitstellen einer Azure Linux-VM als IoT Edge-Gerät finden Sie in dieser [Schnellstartanleitung](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-edge-module-from-azure-marketplace"></a>Bereitstellen eines SQL Edge-Moduls über Azure Marketplace

Azure Marketplace ist ein Onlinemarktplatz für Anwendungen und Dienste, in dem sie eine breite Palette an Unternehmensanwendungen und -lösungen durchsuchen können, die für die Ausführung unter Azure, einschließlich [IoT Edge-Modulen](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules), zertifiziert und optimiert sind. Azure SQL Edge kann über den Marketplace auf einem Edgegerät bereitgestellt werden.

1. Suchen Sie das Azure SQL Edge-Modul im Azure Marketplace.<br><br>

   ![SQL Edge im Marketplace](media/deploy-portal/find-offer-marketplace.png)

2. Wählen Sie den Softwareplan aus, der Ihren Anforderungen am besten entspricht, und klicken Sie auf **Erstellen**. <br><br>

   ![Auswählen des richtigen Softwareplans](media/deploy-portal/pick-correct-plan.png)

3. Geben Sie auf der Seite „Zielgeräte für IoT Edge-Modul“ die folgenden Details an, und klicken Sie auf **Erstellen**.

   |**Feld**  |**Beschreibung**  |
   |---------|---------|
   |Subscription  |  Das Azure-Abonnement, unter dem der IoT Hub erstellt wurde |
   |IoT Hub   |  Der Name des IoT Hubs, auf dem das IoT Edge-Gerät registriert wird. Wählen Sie dann die Option „Für Gerät bereitstellen“ aus.|
   |Name des IoT Edge-Geräts  |  Der Name des IoT Edge-Geräts, auf dem SQL Edge bereitgestellt würde |

4. Klicken Sie auf der Seite **Module für Gerät festlegen:** auf das Azure SQL Edge-Modul unter **IoT Edge-Module**. Der Standardmodulname ist auf *AzureSQLEdge* festgelegt. 

5. Geben Sie im Abschnitt *Moduleinstellungen* des Blatts **IoT Edge-Modul aktualisieren** die gewünschten Werte für den *IoT Edge-Modulnamen* , die *Neustartrichtlinie* und den *Gewünschten Status* an. 

   > [!IMPORTANT]    
   > Ändern oder aktualisieren Sie nicht die Einstellungen des **Image-URI** für das Modul.

6. Geben Sie im Abschnitt *Umgebungsvariablen* des Blatts **IoT Edge-Modul aktualisieren** die gewünschten Werte für die Umgebungsvariablen an. Eine vollständige Liste der Azure SQL Edge-Umgebungsvariablen finden Sie unter [Konfigurieren mithilfe von Umgebungsvariablen](configure.md#configure-by-using-environment-variables). Die folgenden Standardumgebungsvariablen sind für das Modul definiert. 

   |**Parameter**  |**Beschreibung**|
   |---------|---------|
   | MSSQL_SA_PASSWORD  | Ändern Sie den Standardwert, um ein sicheres Kennwort für das SQL Edge-Administratorkonto anzugeben. |
   | MSSQL_LCID   | Ändern Sie den Standardwert, um die gewünschte Sprach-ID festzulegen, die für SQL Edge verwendet werden soll. Beispielsweise steht „1036“ für „Französisch“. |
   | MSSQL_COLLATION | Ändern Sie den Standardwert, um die Standardsortierung für SQL Edge festzulegen. Diese Einstellung setzt die Standardzuordnung der Sprach-ID (LCID) für die Sortierung außer Kraft. |

   > [!IMPORTANT]    
   > Ändern oder aktualisieren Sie die **ACCEPT_EULA** -Umgebungsvariable für das Modul nicht.

7. Aktualisieren Sie die folgenden Optionen im Abschnitt *Containererstellungsoptionen* des Blatts **IoT Edge-Modul aktualisieren** nach Bedarf. 
   - **Hostport:** Ordnen Sie den angegebenen Hostport Port 1433 (SQL-Standardport) im Container zu.
   - **Bindungen** und **Einbindungen:** Wenn Sie mehrere SQL Edge-Module bereitstellen müssen, müssen Sie die „mounts“-Option aktualisieren, um ein neues Quelle-Ziel-Paar für das persistente Volume zu erstellen. Weitere Informationen zu Bereitstellungen und Volumes finden Sie auf der Seite zum [Verwenden von Volumes](https://docs.docker.com/storage/volumes/) in der Docker-Dokumentation. 

   ```json
   {
    "HostConfig": {
        "CapAdd": [
            "SYS_PTRACE"
        ],
        "Binds": [
            "sqlvolume:/sqlvolume"
        ],
        "PortBindings": {
            "1433/tcp": [
                {
                    "HostPort": "1433"
                }
            ]
        },
        "Mounts": [
            {
                "Type": "volume",
                "Source": "sqlvolume",
                "Target": "/var/opt/mssql"
            }
        ]
    },
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "PlanId=asde-developer-on-iot-edge"
    ]
   }
   ```
   > [!IMPORTANT]    
   > Ändern Sie die `PlanId`-Umgebungsvariable nicht, die in der Einstellung zum Erstellen der Konfiguration definiert ist. Wenn dieser Wert geändert wird, wird der Azure SQL Edge-Container nicht gestartet. 
   
8. Klicken Sie im Bereich **IoT Edge-Modul aktualisieren** auf **Aktualisieren**.
9. Klicken Sie auf der Seite **Module für Gerät festlegen** auf **Weiter: Routen>** , wenn Sie Routen für Ihre Bereitstellung definieren müssen. Klicken Sie andernfalls auf **Überprüfen und erstellen**. Weitere Informationen zum Konfigurieren von Routen finden Sie unter [Bereitstellen von Modulen und Einrichten von Routen in IoT Edge](../iot-edge/module-composition.md).
11. Klicken Sie auf der Seite **Module für Gerät festlegen** auf **Erstellen**.

## <a name="connect-to-azure-sql-edge"></a>Herstellen einer Verbindung mit Azure SQL Edge

In den folgenden Schritten wird das Azure SQL Edge-Befehlszeilentool **sqlcmd** im Container genutzt, um eine Verbindung mit Azure SQL Edge herzustellen.

> [!NOTE]      
> SQL-Befehlszeilentools (sqlcmd) sind innerhalb der ARM64-Version von Azure SQL Edge-Containern nicht verfügbar.

1. Verwenden Sie den Befehl `docker exec -it`, um in Ihrem laufenden Container eine interaktive Bash-Shell zu starten. Im folgenden Beispiel steht `azuresqledge` für den Namen, der vom Parameter `Name` Ihres IoT Edge-Moduls angegeben wird.

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. Stellen Sie eine lokale Verbindung mit „sqlcmd“ her. „Sqlcmd“ verwendet nicht automatisch den richtigen Pfad. Sie müssen daher selbst den vollständigen Pfand angeben.

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]    
   > Sie können das Kennwort in der Befehlszeile auslassen, damit Sie aufgefordert werden, es einzugeben.

3. Wenn dies erfolgreich war, sollten zu einer **sqlcmd** Eingabeaufforderung: `1>` gelangen.

## <a name="create-and-query-data"></a>Erstellen und Abfragen von Daten

Die folgenden Abschnitte führen Sie durch die Verwendung von **sqlcmd** und Transact-SQL, um eine neue Datenbank zu erstellen, Daten hinzuzufügen und eine Abfrage auszuführen.

### <a name="create-a-new-database"></a>Erstellen einer neuen Datenbank

Mit den folgenden Schritten wird eine neue Datenbank mit dem Namen `TestDB` erstellt.

1. Fügen Sie aus der **sqlcmd** -Eingabeaufforderung den folgenden Transact-SQL-Befehl zur Erstellung einer Testdatenbank ein:

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. Schreiben Sie in der nächsten Zeile eine Abfrage, um den Namen all Ihrer Datenbanken auf Ihrem Server zurückzugeben:

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>Einfügen von Daten

Erstellen Sie als Nächstes eine neue Tabelle, `Inventory`, und fügen Sie zwei neue Zeilen ein.

1. Wechseln Sie den Kontext aus der **sqlcmd** -Eingabeaufforderung zur neuen `TestDB`-Datenbank:

   ```sql
   USE TestDB
   ```

2. Erstellen Sie eine neue Tabelle mit dem Namen `Inventory`:

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. Fügen Sie Daten in die neue Tabelle ein:

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. Geben Sie `GO` ein, um die zuvor eingegebenen Befehle auszuführen:

   ```sql
   GO
   ```

### <a name="select-data"></a>Auswählen von Daten

Führen Sie nun eine Abfrage zum Zurückgeben von Daten aus der `Inventory`-Tabelle aus.

1. Geben Sie aus der **sqlcmd** -Eingabeaufforderung eine Abfrage ein, die Reihen aus der `Inventory`-Tabelle zurückgibt, bei denen die Menge größer als 152 ist:

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. Führen Sie den folgenden Befehl aus:

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>Beenden der sqlcmd-Eingabeaufforderung

1. Zum Beenden der **sqlcmd** -Sitzung, geben Sie `QUIT` ein:

   ```sql
   QUIT
   ```

2. Geben Sie `exit` ein, um die interaktive Befehlszeile in Ihrem Container zu beenden. Der Container wird auch nach dem Beenden der interaktiven Bash-Shell weiter ausgeführt.

## <a name="connect-from-outside-the-container"></a> Herstellen einer Verbindung von außerhalb des Containers

Sie können über jedes externe Linux-, Windows- oder macOS-Tool, das SQL-Verbindungen unterstützt, eine Verbindung mit der SQL Edge-Instanz herstellen und SQL-Abfragen durchführen. Weitere Informationen zum Herstellen einer Verbindung mit einem SQL Edge-Container von außerhalb finden Sie unter [Herstellen einer Verbindung mit und Abfragen von Azure SQL Edge (Vorschau)](./connect.md).

In diesem Schnellstart haben Sie ein SQL Edge-Modul auf einem IoT Edge-Gerät bereitgestellt. 

## <a name="next-steps"></a>Nächste Schritte

- [Machine Learning und künstliche Intelligenz mit ONNX in SQL Edge](onnx-overview.md)
- [Entwickeln einer End-to-End-IoT-Lösung mit SQL Edge unter Verwendung von IoT Edge](tutorial-deploy-azure-resources.md)
- [Datenstreaming in Azure SQL Edge](stream-data.md)
- [Behandeln von Bereitstellungsfehlern](troubleshoot.md)