---
title: Konfigurieren von Azure SQL Edge
description: Informationen zum Konfigurieren von Azure SQL Edge
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 0c49f5ab9f10456c32f7f8516cba0e851fa80e74
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392332"
---
# <a name="configure-azure-sql-edge"></a>Konfigurieren von Azure SQL Edge

Azure SQL Edge unterstützt die Konfiguration mit einer der folgenden beiden Optionen:

- Umgebungsvariablen
- Verwenden der Datei „mssql.conf“, die im Ordner „/var/opt/mssql“ abgelegt wurde.

> [!NOTE]
> Durch das Festlegen von Umgebungsvariablen werden die in der Datei „mssql.conf“ angegebenen Einstellungen überschrieben.

## <a name="configure-by-using-environment-variables"></a>Konfigurieren mithilfe von Umgebungsvariablen

Azure SQL Edge stellt mehrere verschiedene Umgebungsvariablen bereit, mit denen der SQL Edge-Container konfiguriert werden kann. Diese Umgebungsvariablen sind eine Teilmenge derjenigen, die unter Linux für SQL Server verfügbar sind. Weitere Informationen zu Umgebungsvariablen für SQL Server unter Linux finden Sie unter [Umgebungsvariablen](/sql/linux/sql-server-linux-configure-environment-variables/).

Die folgenden neuen Umgebungsvariablen wurden zu Azure SQL Edge hinzugefügt. 

| Umgebungsvariable | BESCHREIBUNG | Werte |     
|-----|-----| ---------- | 
| **PlanId** | Gibt die Azure SQL Edge-SKU an, die während der Initialisierung verwendet werden soll. Diese Umgebungsvariable ist nur erforderlich, wenn Sie Azure SQL Edge-Instanzen mithilfe von Azure IoT Edge bereitstellen. | **asde-developer-on-iot-edge** oder **asde-premium-on-iot-edge** | 
| **MSSQL_TELEMETRY_ENABLED** | Aktivieren oder Deaktivieren der Sammlung von Nutzung- und Diagnosedaten | TRUE oder FALSE |  
| **MSSQL_TELEMETRY_DIR** | Legt das Zielverzeichnis für die Überwachungsdateien zur Sammlung von Nutzungs- und Diagnosedaten fest. | Speicherort des Ordners im SQL Edge-Container. Dieser Ordner kann über Bereitstellungspunkte oder Datenvolumes einem Hostvolume zugeordnet werden. | 
| **MSSQL_PACKAGE** | Gibt den Speicherort des dacpac-oder bacpac-Pakets an, das bereitgestellt werden soll. | Ordner, Datei oder SAS-URL, der oder die die dacpac- oder bacpac-Pakete enthält. Weitere Informationen finden Sie unter [Bereitstellen von SQL-Datenbank-DACPAC- und -BACPAC-Paketen in SQL Edge](deploy-dacpac.md). |


Die folgenden Umgebungsvariablen für SQL Server unter Linux werden von Azure SQL Edge nicht unterstützt. Falls definiert, werden sie während der Containerinitialisierung ignoriert.

| Umgebungsvariable | BESCHREIBUNG |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Aktivieren der Verfügbarkeitsgruppe. Beispielsweise bedeutet **1** aktiviert und **0** deaktiviert. |

> [!IMPORTANT]
> Die **MSSQL_PID** -Umgebungsvariable für SQL Edge akzeptiert nur **Premium** und **Developer** als gültige Werte. Azure SQL Edge unterstützt keine Initialisierung mithilfe eines Product Key.

### <a name="specify-the-environment-variables"></a>Angeben der Umgebungsvariablen

Geben Sie Umgebungsvariablen für SQL Edge an, wenn Sie den Dienst über das [Azure-Portal](deploy-portal.md) bereitstellen. Sie können sie entweder im Abschnitt **Umgebungsvariablen** der Modulbereitstellung oder als Teil der **Optionen für die Containererstellung** hinzufügen.

Fügen Sie Werte in **Umgebungsvariablen** hinzu.

![Festlegen mithilfe einer Liste mit Umgebungsvariablen](media/configure/set-environment-variables.png)

Fügen Sie Werte in **Optionen für die Containererstellung** hinzu.

![Festlegen mithilfe von Optionen für die Containererstellung](media/configure/set-environment-variables-using-create-options.png)

> [!NOTE]
> Im getrennten Bereitstellungsmodus können Umgebungsvariablen mithilfe der Optionen `-e` oder `--env` oder `--env-file` des `docker run`-Befehls angegeben werden.

## <a name="configure-by-using-an-mssqlconf-file"></a>Konfigurieren mithilfe der Datei „mssql.conf“

Azure SQL Edge enthält nicht wie SQL Server unter Linux das [Konfigurationshilfsprogramm mssql-conf](/sql/linux/sql-server-linux-configure-mssql-conf/). Sie müssen die Datei „mssql.conf“ manuell konfigurieren und auf dem persistenten Speicherlaufwerk ablegen, das dem Ordner „/var/opt/mssql/“ im Modul „SQL Edge“ zugeordnet ist. Beim Bereitstellen von SQL Edge über Azure Marketplace wird diese Zuordnung als Option **Mounts** in den **Optionen zur Containererstellung** angegeben.

```json
    {
        "Mounts": [
          {
            "Type": "volume",
            "Source": "sqlvolume",
            "Target": "/var/opt/mssql"
          }
        ]
      }
    }
```

Die folgenden neuen „mssql.conf“-Optionen wurden für Azure SQL Edge hinzugefügt. 

|Option|Beschreibung|
|:---|:---|
|**customerfeedback** | Festlegen, ob SQL Server Feedback an Microsoft sendet. Weitere Informationen finden Sie unter [Deaktivieren der Nutzungs- und Diagnosedatensammlung](usage-and-diagnostics-data-configuration.md#disable-usage-and-diagnostic-data-collection).|      
|**userrequestedlocalauditdirectory** | Legt das Zielverzeichnis für die Überwachungsdateien zur Sammlung von Nutzungs- und Diagnosedaten fest. Weitere Informationen finden Sie unter [Lokale Überwachung der Nutzungs- und -Diagnosedatensammlung](usage-and-diagnostics-data-configuration.md#local-audit-of-usage-and-diagnostic-data-collection). |        

Die folgenden Optionen von „mssql.conf“ gelten nicht für SQL Edge:

|Option|BESCHREIBUNG|
|:---|:---|
|**Kundenfeedback** | Festlegen, ob SQL Server Feedback an Microsoft sendet. |
|**Datenbank-E-Mail-Profil** | Festlegen des Standardprofils von Datenbank-E-Mails für SQL Server für Linux. |
|**Hochverfügbarkeit** | Aktivieren von Verfügbarkeitsgruppen. |
|**Microsoft Distributed Transaction Coordinator** | Konfigurieren von MS DTC unter Linux und Durchführen einer Problembehandlung. Zusätzliche Konfigurationsoptionen für verteilte Transaktionen werden für SQL Edge ebenfalls nicht unterstützt. Weitere Informationen zu diesen zusätzlichen Konfigurationsoptionen finden Sie unter [Konfigurieren von MSDTC](/sql/linux/sql-server-linux-configure-mssql-conf#msdtc). |
|**Lizenzbedingungen für ML-Dienste** | Akzeptieren Sie für Azure Machine Learning-Pakete die EULAs für R und Python. Gilt nur für SQL Server 2019.|
|**outboundnetworkaccess** |Aktivieren des Zugriffs auf ausgehenden Netzwerkdatenverkehr für R-, Python- und Java-Erweiterungen für [Machine Learning Services](/sql/linux/sql-server-linux-setup-machine-learning/).|

Die folgende Beispieldatei für „mssql.conf“ funktioniert für SQL Edge. Weitere Informationen zum Format der Datei „mssql.conf“ finden Sie unter [mssql.conf-Format](/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

```ini
[EULA]
accepteula = Y

[coredump]
captureminiandfull = true
coredumptype = full

[filelocation]
defaultbackupdir = /var/opt/mssql/backup/
defaultdatadir = /var/opt/mssql/data/
defaultdumpdir = /var/opt/mssql/data/
defaultlogdir = /var/opt/mssql/log/

[language]
lcid = 1033

[memory]
memorylimitmb = 6144

[sqlagent]
errorlogfile = /var/opt/mssql/log/sqlagentlog.log
errorlogginglevel = 7

[traceflag]
traceflag0 = 3604
traceflag1 = 3605
traceflag2 = 1204
```

## <a name="run-azure-sql-edge-as-non-root-user"></a>Ausführen von Azure SQL Edge als Nicht-root-Benutzer

Azure SQL Edge-Container werden standardmäßig als Nicht-root-Benutzer bzw. -Gruppe ausgeführt. Bei der Bereitstellung über Azure Marketplace (oder mit docker run) starten SQL Edge-Container als mssql-Benutzer (nicht root), wenn kein anderer Benutzer bzw. keine andere Gruppe angegeben wird. Um während der Bereitstellung einen anderen Nicht-root-Benutzer anzugeben, fügen Sie das `*"User": "<name|uid>[:<group|gid>]"*`-Schlüssel-Wert-Paar unter den Optionen zum Erstellen von Containern hinzu. Im folgenden Beispiel ist SQL Edge so konfiguriert, dass es als `*IoTAdmin*`-Benutzer gestartet wird.

```json
{
    ..
    ..
    ..
    "User": "IoTAdmin",
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "MSSQL_PID=Premium"
    ]
}
```

Um dem Nicht-root-Benutzer den Zugriff auf DB-Dateien zu ermöglichen, die sich auf bereitgestellten Volumes befinden, stellen Sie sicher, dass der Benutzer bzw. die Gruppe, unter dem/r Sie den Container ausführen, Lese- und Schreibberechtigungen für den persistenten Dateispeicher besitzt. Im folgenden Beispiel legen wir den Nicht-root-Benutzer mit „user_id 10001“ als Besitzer der Dateien fest. 

```bash
chown -R 10001:0 <database file dir>
```

### <a name="upgrading-from-earlier-ctp-releases"></a>Upgrade von früheren CTP-Versionen

Frühere CTP-Versionen von Azure SQL Edge waren so konfiguriert, dass sie als root-Benutzer ausgeführt wurden. Die folgenden Optionen sind verfügbar, wenn Sie ein Upgrade aus früheren CTP-Versionen durchführen.

- Root-Benutzer weiterhin verwenden: Um weiterhin den root-Benutzer zu verwenden, fügen Sie das `*"User": "0:0"*`-Schlüssel-Wert-Paar unter den Optionen zum Erstellen von Containern hinzu.
- Mssql-Standardbenutzer verwenden: Um den mssql-Standardbenutzer zu verwenden, führen Sie die folgenden Schritte aus.
  - Fügen Sie einen Benutzer mit dem Namen „mssql“ auf dem Docker-Host hinzu. Im folgenden Beispiel fügen wir einen Benutzer „mssql“ mit der ID 10001 hinzu. Dieser Benutzer wird auch der root-Gruppe hinzugefügt.
    ```bash
    sudo useradd -M -s /bin/bash -u 10001 -g 0 mssql
    ```
  - Ändern Sie die Berechtigung für das Verzeichnis/Bereitstellungsvolume, in dem sich die Datenbankdatei befindet. 
    ```bash
    sudo chgrp -R 0 /var/lib/docker/volumes/kafka_sqldata/
    sudo chmod -R g=u /var/lib/docker/volumes/kafka_sqldata/
    ```
- Ein anderes Nicht-root-Benutzerkonto verwenden: Um ein anderes Nicht-root-Benutzerkonto zu verwenden.
  - Aktualisieren Sie die Optionen zum Erstellen von Containern, um das `*"User": "user_name | user_id*`-Schlüssel-Wert-Paar unter den Optionen zum Erstellen von Containern hinzuzufügen. Ersetzen Sie user_name oder user_id durch einen tatsächlichen user_name oder eine tatsächliche user_id von Ihrem Docker-Host. 
  - Ändern Sie die Berechtigungen für das Verzeichnis/Bereitstellungsvolume.

## <a name="persist-your-data"></a> Beibehalten von Daten

Die Azure SQL Edge-Konfigurationsänderungen und -Datenbankdateien werden im Container beibehalten, auch wenn Sie den Container mit `docker stop` und `docker start` neu starten. Wenn Sie den Container jedoch mit `docker rm` entfernen, werden sämtliche Inhalte des Containers gelöscht, einschließlich Azure SQL Edge und Ihrer Datenbanken. Im folgenden Abschnitt wird erläutert, wie **Datenvolumes** verwendet werden, um Ihre Datenbankdateien beizubehalten, auch wenn die zugeordneten Container gelöscht werden.

> [!IMPORTANT]
> Für Azure SQL Edge ist es wichtig, sich mit der Datenpersistenz in Docker auseinanderzusetzen. Weitere Informationen finden Sie in der Docker-Dokumentation unter [Verwalten von Daten in Docker-Containern](https://docs.docker.com/engine/tutorials/dockervolumes/).

### <a name="mount-a-host-directory-as-data-volume"></a>Einbinden eines Hostverzeichnisses als Datenvolume

Die erste Option besteht darin, ein Verzeichnis auf dem Host als Datenvolume in Ihrem Container einzubinden. Verwenden Sie hierzu den Befehl `docker run` mit dem Flag `-v <host directory>:/var/opt/mssql`. Dadurch können die Daten zwischen den Containerausführungen wiederhergestellt werden.

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge
```

Mit dieser Methode können Sie die Dateien auch außerhalb von Docker auf dem Host freigeben und anzeigen.

> [!IMPORTANT]
> Die Hostvolumezuordnung für **Docker unter Windows** unterstützt derzeit nicht die Zuordnung des kompletten `/var/opt/mssql`-Verzeichnisses. Sie können dem Hostcomputer jedoch ein Unterverzeichnis wie `/var/opt/mssql/data` zuordnen.

> [!IMPORTANT]
> Die Hostvolumezuordnung für **Docker unter Mac** mit dem Azure SQL Edge-Image wird derzeit nicht unterstützt. Verwenden Sie stattdessen Datenvolumecontainer. Diese Einschränkung gilt nur für das Verzeichnis `/var/opt/mssql`. Das Lesen aus einem bereitgestellten Verzeichnis funktioniert ordnungsgemäß. Sie können beispielsweise ein Hostverzeichnis mithilfe von-v auf einem Mac einbinden und eine Sicherung aus einer BAK-Datei wiederherstellen, die sich auf dem Host befindet.

### <a name="use-data-volume-containers"></a>Verwenden von Datenvolumecontainern

Die zweite Option besteht in der Verwendung eines Datenvolumecontainers. Sie können einen Datenvolumecontainer erstellen, indem Sie mit dem Parameter `-v` einen Volumenamen anstelle eines Hostverzeichnisses angeben. Im folgenden Beispiel wird ein freigegebenes Datenvolume mit dem Namen **sqlvolume** erstellt.

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge
```

> [!NOTE]
> Dieses Verfahren zum impliziten Erstellen eines Datenvolumes im run-Befehl funktioniert nicht mit älteren Docker-Versionen. Wenden Sie in diesem Fall die expliziten Schritte an, die in der Docker-Dokumentation zum [Erstellen und Einbinden eines Datenvolumecontainers](https://docs.docker.com/engine/tutorials/dockervolumes/#creating-and-mounting-a-data-volume-container) beschrieben werden.

Auch wenn Sie diesen Container anhalten und entfernen, bleibt das Datenvolume erhalten. Sie können es mit dem Befehl `docker volume ls` anzeigen.

```bash
docker volume ls
```

Wenn Sie dann einen weiteren Container mit dem gleichen Volumenamen erstellen, verwendet der neue Container die gleichen Azure SQL Edge-Daten, die auch im Volume enthalten sind.

Verwenden Sie den Befehl `docker volume rm`, um einen Datenvolumecontainer zu entfernen.

> [!WARNING]
> Wenn Sie den Datenvolumecontainer löschen, werden alle Azure SQL Edge-Daten im Container *dauerhaft* gelöscht.


## <a name="next-steps"></a>Nächste Schritte

- [Herstellen einer Verbindung mit Azure SQL Edge](connect.md)
- [Erstellen einer End-to-End-IoT-Lösung mit SQL Edge](tutorial-deploy-azure-resources.md)