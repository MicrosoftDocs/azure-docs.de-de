---
title: 'Bereitstellen von Azure SQL Edge mit Docker: Azure SQL Edge'
description: Erfahren Sie mehr zum Bereitstellen von Azure SQL Edge mit Docker.
keywords: SQL Edge, Container, Docker
services: sql-edge
ms.service: sql-edge
ms.topic: quickstart
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: fce098767fffd36376399bbd9396699e3d9fbfd3
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392077"
---
# <a name="deploy-azure-sql-edge-with-docker"></a>Bereitstellen von Azure SQL Edge mit Docker

In diesem Schnellstart verwenden Sie Docker, um das Azure SQL Edge-Containerimage abzurufen und auszuführen. Stellen Sie anschließend eine Verbindung mit **sqlcmd** her, um Ihre erste Datenbank zu erstellen und Abfragen auszuführen.

Dieses Image besteht aus auf Ubuntu 18.04 basierendem Azure SQL Edge. Es kann unter Linux mit der Docker-Engine 1.8 und höher und in Docker für Mac bzw. Windows verwendet werden.

## <a name="prerequisites"></a>Voraussetzungen

- Docker-Engine 1.8 und höher auf unterstütztem Linux-Betriebssystem oder Docker für Mac bzw. Windows. Weitere Informationen finden Sie unter [Install Docker (Installieren von Docker)](https://docs.docker.com/engine/installation/). Da die Azure SQL Edge-Images auf Ubuntu 18.04 basieren, empfiehlt es sich, einen Docker-Host mit Ubuntu 18.04 zu verwenden.
- Docker-Speichertreiber **overlay2**. Dies ist die Standardeinstellung für die meisten Benutzer. Wenn Sie feststellen, dass Sie diesen Speichertreiber nicht verwenden und wechseln müssen, finden Sie weitere Informationen und Warnungen in der [Docker-Dokumentation zur Konfiguration von „overlay2“](https://docs.docker.com/storage/storagedriver/overlayfs-driver/#configure-docker-with-the-overlay-or-overlay2-storage-driver).
- Mindestens 10 GB freier Speicherplatz.
- Mindestens 1 GB RAM.
- [Hardwareanforderungen für Azure SQL Edge](./features.md#hardware-support).


## <a name="pull-and-run-the-container-image"></a>Übertragen mithilfe von Pull und Ausführen von Containerimages

Bevor Sie die folgenden Schritte ausführen, stellen Sie sicher, dass Sie am Anfang dieses Artikels Ihre bevorzugte Shell (bash, PowerShell oder cmd) ausgewählt haben.

1. Pullen Sie das Azure SQL Edge-Containerimage aus Microsoft Container Registry.

    - Pullen des Azure SQL Edge-Containerimages
        ```bash
        sudo docker pull mcr.microsoft.com/azure-sql-edge:latest 
        ```

> [!NOTE]
> Für die Bash-Befehle in diesem Artikel wird `sudo` verwendet. Unter macOS und Windows wird sudo möglicherweise nicht benötigt. Wenn Sie sudo unter Linux nicht zum Ausführen von Docker verwenden möchten, können Sie eine Docker-Gruppe konfigurieren und dieser Gruppe Benutzer hinzufügen. Weitere Informationen finden Sie unter [Post-installation steps for Linux (Schritte nach der Installation unter Linux)](https://docs.docker.com/engine/install/linux-postinstall/).

Der vorherige Befehl pullt die neuesten Azure SQL Edge-Containerimages. Alle verfügbaren Images finden Sie auf der [Docker-Hubseite zu azure-sql-egde](https://hub.docker.com/_/microsoft-azure-sql-edge).

2. Zum Ausführen des Containerimages in Docker können Sie eine Bash-Shell (Linux bzw. macOS) oder eine PowerShell-Eingabeaufforderung mit erhöhten Rechten verwenden. Nachfolgend finden Sie den dafür benötigten Befehl:
    
    - Starten einer Azure SQL Edge-Instanz, die als Developer Edition ausgeführt wird
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```

    - Starten einer Azure SQL Edge-Instanz, die als Premium Edition ausgeführt wird
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -e 'MSSQL_PID=Premium' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```
    > [!NOTE]
    > Wenn Sie zum Ausführen dieser Befehle PowerShell unter Windows verwenden, verwenden Sie doppelte anstelle von einfachen Anführungszeichen.


    > [!NOTE]
    > Das Kennwort muss der Standardrichtlinie für Kennwörter für die Microsoft SQL-Datenbank-Engine entsprechen. Ist dies nicht der Fall, kann der Container die SQL-Engine nicht einrichten und funktioniert nicht mehr. In der Standardeinstellung muss das Kennwort mindestens 8 Zeichen lang sein und Zeichen aus drei der folgenden vier Gruppen enthalten: Großbuchstaben, Kleinbuchstaben, Ziffern und Symbole. Mit dem Befehl [docker logs](https://docs.docker.com/engine/reference/commandline/logs/) können Sie das Fehlerprotokoll untersuchen.
    
    In der folgenden Tabelle finden Sie Beschreibungen der Parameter des vorangegangenen Beispiels für `docker run`:

    | Parameter | BESCHREIBUNG |
    |-----|-----|
    | **-e „ACCEPT_EULA = Y“** |  Legen Sie für die Variable **ACCEPT_EULA** einen beliebigen Wert fest, um Ihre Zustimmung zum [End-User Licensing Agreement (Benutzerlizenzvertrag)](https://go.microsoft.com/fwlink/?linkid=2139274) zu geben. Erforderliche Einstellung für das Azure SQL Edge-Image. |
    | **-e "MSSQL_SA_PASSWORD=yourStrong(!)Password"** | Geben Sie ein sicheres Kennwort ein, das aus mindestens acht Zeichen besteht und den [Kennwortanforderungen von Azure SQL Edge](/sql/relational-databases/security/password-policy) entspricht. Erforderliche Einstellung für das Azure SQL Edge-Image. |
    | **-p 1433:1433** | Ordnen Sie einen TCP-Port in der Hostumgebung (erster Wert) einem TCP-Port im Container zu (zweiter Wert). In diesem Beispiel lauscht Azure SQL Edge an TCP 1433 im Container. Diese Informationen werden Port 1433 auf dem Host verfügbar gemacht. |
    | **--name azuresqledge** | Geben Sie dem Container selbst einen Namen, anstatt einen willkürlich generierten zu verwenden. Wenn Sie mehrere Container ausführen, können Sie nicht denselben Namen mehrfach verwenden. |
    | **-d** | Hiermit wird der Container im Hintergrund ausgeführt (Daemon) |

    Eine vollständige Liste aller Azure SQL Edge-Umgebungsvariablen finden Sie unter [Konfigurieren von Azure SQL Edge mit Umgebungsvariablen](configure.md#configure-by-using-environment-variables). Sie können auch Azure SQL Edge-Container mithilfe der Datei [mssql.conf](configure.md#configure-by-using-an-mssqlconf-file) konfigurieren.

3. Verwenden Sie den Befehl `docker ps`, um Ihre Docker-Container anzeigen zu lassen.
   
   ```bash
    sudo docker ps -a
   ```

4. Wenn die Spalte **STATUS** den Status **Up** (Aktiv) enthält, wird Azure SQL Edge im Container ausgeführt und überwacht den Port, der in der Spalte **PORTS** angegeben ist. Wenn die Spalte **STATUS** für Ihren Azure SQL Edge-Container **Exited** enthält, lesen Sie in der Azure SQL Edge-Dokumentation den Abschnitt „Problembehandlung“.

    Der Parameter `-h` (Hostname) ist ebenfalls nützlich, wird der Einfachheit halber jedoch in diesem Tutorial nicht verwendet. Mit ihm lässt sich der interne Name eines Containers in einen benutzerdefinierten Wert ändern. Dieser Name wird in der folgenden Transact-SQL-Abfrage zurückgegeben:

    ```sql
    SELECT @@SERVERNAME,
        SERVERPROPERTY('ComputerNamePhysicalNetBIOS'),
        SERVERPROPERTY('MachineName'),
        SERVERPROPERTY('ServerName')
    ```

    Wenn Sie für `-h` und `--name` denselben Wert festlegen, kann der Zielcontainer ganz einfach ermittelt werden.

5. Ändern Sie als letzten Schritt das Systemadministratorkennwort, da das `SA_PASSWORD` in der `ps -eax`-Ausgabe sichtbar und in der Umgebungsvariablen mit dem gleichen Namen gespeichert ist. Weitere Informationen finden Sie nachfolgend.

## <a name="change-the-sa-password"></a>Ändern des Systemadministratorkennworts

Das Konto **SA** ist ein Systemadministrator in der Azure SQL Edge-Instanz, das beim Setup erstellt wird. Nach dem Erstellen Ihres Azure SQL Edge-Containers wird die von Ihnen festgelegte Umgebungsvariable `MSSQL_SA_PASSWORD` sichtbar, wenn Sie im Container `echo $SA_PASSWORD` ausführen. Ändern Sie aus Sicherheitsgründen ihr SA-Kennwort.

1. Wählen Sie ein sicheres Kennwort für den SA-Benutzer aus.

2. Verwenden Sie `docker exec` in Transact-SQL **sqlcmd** zum Ausführen und Ändern des Kennworts. Ersetzen Sie im folgenden Beispiel das alte Kennwort, `<YourStrong!Passw0rd>`, und das neue Kennwort, `<YourNewStrong!Passw0rd>`, durch Ihre eigenen Kennwortwerte.

   ```bash
   sudo docker exec -it azuresqledge /opt/mssql-tools/bin/sqlcmd \
      -S localhost -U SA -P "<YourStrong@Passw0rd>" \
      -Q 'ALTER LOGIN SA WITH PASSWORD="<YourNewStrong@Passw0rd>"'
   ```

## <a name="connect-to-azure-sql-edge"></a>Herstellen einer Verbindung mit Azure SQL Edge

In den folgenden Schritten wird das Azure SQL Edge-Befehlszeilentool **sqlcmd** im Container genutzt, um eine Verbindung mit Azure SQL Edge herzustellen.

> [!NOTE]
> Das sqlcmd-Tool ist nicht in der ARM64-Version von SQL Edge-Containern verfügbar.

1. Verwenden Sie den Befehl `docker exec -it`, um in Ihrem laufenden Container eine interaktive Bash-Shell zu starten. Im folgenden Beispiel steht `azuresqledge` für den Namen, den Sie bei der Erstellung des Containers mit dem Parameter `--name` angegeben haben.

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

Die folgenden Abschnitte führen Sie durch die Verwendung von **sqlcmd** und Transact-SQL, um eine neue Datenbank zu erstellen, Daten hinzuzufügen und eine einfache Abfrage auszuführen.

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

Sie können auch über jedes externe Linux-, Windows- oder macOS-Tool, das SQL-Verbindungen unterstützt, auf Ihrem Docker-Computer eine Verbindung mit der Azure SQL Edge-Instanz herstellen. Weitere Informationen zum Herstellen einer Verbindung mit einem SQL Edge-Container von außerhalb finden Sie unter [Herstellen einer Verbindung mit und Abfragen von Azure SQL Edge (Vorschau)](connect.md).

## <a name="remove-your-container"></a>Entfernen Ihres Containers

Wenn Sie den in diesem Tutorial verwendeten Azure SQL Edge-Container entfernen möchten, führen Sie die folgenden Befehle aus:

```bash
sudo docker stop azuresqledge
sudo docker rm azuresqledge
```

> [!WARNING]
> Wenn Sie einen Container beenden oder entfernen, werden die Azure SQL Edge-Daten dauerhaft aus diesem Container gelöscht. Wenn Sie Ihre Daten weiterhin benötigen, [erstellen Sie eine Sicherungskopie des Containers](backup-restore.md), oder nutzen Sie für die Containerdaten eine [Methode zur Datenpersistenz](configure.md#persist-your-data).

## <a name="next-steps"></a>Nächste Schritte

- [Machine Learning und künstliche Intelligenz mit ONNX in SQL Edge](onnx-overview.md)
- [Entwickeln einer End-to-End-IoT-Lösung mit SQL Edge unter Verwendung von IoT Edge](tutorial-deploy-azure-resources.md)
- [Datenstreaming in Azure SQL Edge](stream-data.md)