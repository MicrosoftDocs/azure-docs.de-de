---
title: Erfassen der Linux-Anwendungsleistung in Azure Monitor | Microsoft-Dokumentation
description: Dieser Artikel enthält Details zum Konfigurieren des Log Analytics-Agents für Linux, um Leistungsindikatoren für MySQL und Apache HTTP Server zu erfassen.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: f1d5bde4-6b86-4b8e-b5c1-3ecbaba76198
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: magoedte
ms.openlocfilehash: ea74440a5c8a9a2584e742ec72ccf888b6bb5ad9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60628913"
---
# <a name="collect-performance-counters-for-linux-applications-in-azure-monitor"></a>Erfassen von Leistungsindikatoren für Linux-Anwendungen in Azure Monitor 
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]
Dieser Artikel enthält Details zum Konfigurieren des [Log Analytics-Agents für Linux](https://github.com/Microsoft/OMS-Agent-for-Linux), um Leistungsindikatoren für bestimmte Anwendungen in Azure Monitor zu erfassen.  Folgende Anwendungen sind in diesem Artikel enthalten:  

- [MySQL](#mysql)
- [Apache HTTP Server](#apache-http-server)

## <a name="mysql"></a>MySQL
Falls MySQL-Server oder MariaDB-Server auf dem Computer erkannt wird, wenn der Log Analytics-Agent installiert wird, wird automatisch ein Leistungsüberwachungsanbieter für MySQL-Server installiert. Dieser Anbieter stellt eine Verbindung mit dem lokalen MySQL/MariaDB-Server her, um Leistungsstatistiken verfügbar zu machen. MySQL-Benutzeranmeldeinformationen müssen konfiguriert werden, damit der Anbieter auf den MySQL-Server zugreifen kann.

### <a name="configure-mysql-credentials"></a>Konfigurieren von MySQL-Anmeldeinformationen
Der MySQL-OMI-Anbieter erfordert einen vorkonfigurierten MySQL-Benutzer und installierte MySQL-Clientbibliotheken, um eine Abfrage der Leistungs- und Integritätsinformationen der MySQL-Instanz durchzuführen.  Diese Anmeldeinformationen werden in einer Authentifizierungsdatei gespeichert, die auf dem Linux-Agent gespeichert ist.  Die Authentifizierungsdatei gibt an, welche mit „bind-address“ festgelegte Adresse und welcher Port die MySQL-Instanz überwacht und welche Anmeldeinformationen verwendet werden müssen, um Metriken zu sammeln.  

Während der Installation des Log Analytics-Agents für Linux scannt der MySQL-OMI-Anbieter die „my.cnf“-Konfigurationsdateien von MySQL (Standardspeicherorte) für die mit „bind-address“ festgelegte Adresse sowie den Port und legt teilweise die MySQL-OMI-Authentifizierungsdatei fest.

Die MySQL-Authentifizierungsdatei befindet sich in `/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth`.


### <a name="authentication-file-format"></a>Authentifizierungsdateiformat
Die MySQL-OMI-Authentifizierungsdatei hat das folgende Format

    [Port]=[Bind-Address], [username], [Base64 encoded Password]
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    (Port)=(Bind-Address), (username), (Base64 encoded Password)
    AutoUpdate=[true|false]

In der folgenden Tabelle werden die Einträge aus der Authentifizierungsdatei beschrieben.

| Eigenschaft | Beschreibung |
|:--|:--|
| Port | Stellt den aktuellen Port dar, den die MySQL-Instanz überwacht Port 0 gibt an, dass die folgenden Eigenschaften für die Standardinstanz verwendet werden. |
| Bind-Adresse| Aktuelle MySQL-Bind-Adresse |
| username| MySQL-Benutzer, der zum Überwachen der MySQL Server-Instanz verwendet wird |
| Base64 codiertes Kennwort| Das mit Base64 codierte Kennwort des Benutzers, mit dem Sie MySQL überwachen möchten |
| AutoUpdate| Gibt an, ob die Datei my.cnf erneut auf Änderungen geprüft und die MySQL-OMI-Authentifizierungsdatei überschrieben werden soll, wenn der MySQL OMI-Anbieter aktualisiert wird |

### <a name="default-instance"></a>Standardinstanz
Die MySQL-OMI-Authentifizierungsdatei kann eine Standardinstanz und Portnummer definieren, und erleichtert so das Verwalten mehrerer MySQL-Instanzen auf einem Linux-Host.  Die Standardinstanz wird durch eine Instanz mit Port 0 gekennzeichnet. Alle zusätzlichen Instanzen erben Eigenschaften, die von der Standardinstanz festgelegt werden, es sei denn, sie geben unterschiedliche Werte an. Wenn beispielsweise die MySQL-Instanz hinzugefügt wird, die einen Port „3308“ überwacht, werden die mit „bind-address“ festgelegte Adresse, der Benutzername und das Base64 codierte Kennwort der Standardinstanz verwendet, um die Instanz, die 3308 überwacht, zu testen und zu überwachen. Wenn die Instanz auf 3308 an eine andere Adresse gebunden ist und das gleiche MySQL-Benutzernamen- und Kennwortpaar verwendet, ist nur die mit „bind-address“ festgelegte Adresse erforderlich, und die anderen Eigenschaften werden geerbt.

Die folgende Tabelle enthält Beispielinstanzeinstellungen 

| Beschreibung | Datei |
|:--|:--|
| Standardinstanz und Instanz mit Port 3308 | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=, ,`<br>`AutoUpdate=true` |
| Standardinstanz und Instanz mit Port 3308 und anderem Benutzernamen und Kennwort | `0=127.0.0.1, myuser, cnBwdA==`<br>`3308=127.0.1.1, myuser2,cGluaGVhZA==`<br>`AutoUpdate=true` |


### <a name="mysql-omi-authentication-file-program"></a>MySQL-OMI-Authentifizierungsdateiprogramm
In der Installation von MySQL-OMI-Anbieter enthalten ist ein MySQL-OMI-Authentifizierungsdateiprogramm, mit dem die MySQL-OMI-Authentifizierungsdatei bearbeitet werden kann. Das Authentifizierungsdateiprogramm befindet sich an folgendem Speicherort.

    /opt/microsoft/mysql-cimprov/bin/mycimprovauth

> [!NOTE]
> Die Datei mit den Anmeldeinformationen muss vom omsagent-Konto gelesen werden können. Es wird empfohlen, den „mycimprovauth“-Befehl als omsagent auszuführen.

Die folgende Tabelle enthält Details zur Syntax für die Verwendung von mycimprovauth.

| Vorgang | Beispiel | BESCHREIBUNG
|:--|:--|:--|
| autoupdate *„false“ oder „true“* | mycimprovauth autoupdate false | Legt fest, ob die Authentifizierungsdatei bei Neustart oder bei Aktualisierung automatisch aktualisiert wird |
| default *bind-address Benutzername Kennwort* | mycimprovauth default 127.0.0.1 root pwd | Legt die Standardinstanz in der MySQL-OMI-Authentifizierungsdatei fest<br>Das Kennwortfeld sollte als Nur-Text eingegeben werden, das Kennwort in der MySQL-OMI-Authentifizierungsdatei ist Base 64-codiert. |
| delete *„default“ oder „port_num“* | mycimprovauth 3308 | Löscht die angegebene Instanz entweder standardmäßig oder nach Portnummer |
| help | mycimprov help | Druckt eine Liste der zu verwendenden Befehle |
| print | mycimprov print | Druckt eine leicht zu lesende MySQL-OMI-Authentifizierungsdatei |
| update port_num *bind-address Benutzername Kennwort* | mycimprov update 3307 127.0.0.1 root pwd | Aktualisiert die angegebene Instanz oder fügt die Instanz hinzu, sofern nicht vorhanden |

Mit den folgenden Beispielbefehlen wird ein Standardbenutzerkonto für den MySQL-Server auf „localhost“ definiert.  In das Kennwortfeld sollte Nur-Text eingegeben werden, das Kennwort in der MySQL-OMI-Authentifizierungsdatei ist Base 64-codiert

    sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'
    sudo /opt/omi/bin/service_control restart

### <a name="database-permissions-required-for-mysql-performance-counters"></a>Datenbankberechtigungen für MySQL-Leistungsindikatoren
Der MySQL-Benutzer benötigt Zugriff auf die folgenden Abfragen zum Erfassen von MySQL-Server-Leistungsdaten. 

    SHOW GLOBAL STATUS;
    SHOW GLOBAL VARIABLES:


Der MySQL-Benutzer benötigt auch SELECT-Zugriff auf die folgenden Standardtabellen.

- information_schema
- mysql 

Diese Berechtigungen können durch Ausführen der folgenden „grant“-Befehle erteilt werden.

    GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
    GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;


> [!NOTE]
> Der gewährende Benutzer muss über die „GRANT-Option“-Berechtigung sowie über die Berechtigung verfügen, die gewährt wird, um einem Benutzer der MySQL-Überwachung Berechtigungen zu gewähren.

### <a name="define-performance-counters"></a>Definieren von Leistungsindikatoren

Nachdem Sie den Log Analytics-Agent für Linux zum Senden von Daten an Azure Monitor konfiguriert haben, müssen Sie die Leistungsindikatoren für die Erfassung konfigurieren.  Verwenden Sie das in [Windows- und Linux-Leistungsdatenquellen in Azure Monitor](data-sources-performance-counters.md) beschriebene Verfahren mit den Leistungsindikatoren in der folgenden Tabelle.

| Objektname | Name des Leistungsindikators |
|:--|:--|
| MySQL Database | Disk Space in Bytes |
| MySQL Database | Tabellen |
| MySQL Server | Aborted Connection Pct |
| MySQL Server | Connection Use Pct |
| MySQL Server | Disk Space Use in Bytes |
| MySQL Server | Full Table Scan Pct |
| MySQL Server | InnoDB Buffer Pool Hit Pct |
| MySQL Server | InnoDB Buffer Pool Use Pct |
| MySQL Server | InnoDB Buffer Pool Use Pct |
| MySQL Server | Key Cache Hit Pct |
| MySQL Server | Key Cache Use Pct |
| MySQL Server | Key Cache Write Pct |
| MySQL Server | Query Cache Hit Pct |
| MySQL Server | Query Cache Prunes Pct |
| MySQL Server | Query Cache Use Pct |
| MySQL Server | Table Cache Hit Pct |
| MySQL Server | Table Cache Use Pct |
| MySQL Server | Table Lock Contention Pct |

## <a name="apache-http-server"></a>Apache HTTP Server 
Falls Apache HTTP Server auf dem Computer erkannt wird, wenn das omsagent-Paket installiert wird, wird ein Leistungsüberwachungsanbieter für Apache HTTP Server automatisch installiert. Dieser Anbieter basiert auf einem Apache-Modul, das in den Apache HTTP Server geladen werden muss, um auf Leistungsdaten zuzugreifen. Das Modul kann mithilfe des folgenden Befehls geladen werden:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

Führen Sie folgenden Befehl aus, um das Apache-Überwachungsmodul zu entfernen:
```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```

### <a name="define-performance-counters"></a>Definieren von Leistungsindikatoren

Nachdem Sie den Log Analytics-Agent für Linux zum Senden von Daten an Azure Monitor konfiguriert haben, müssen Sie die Leistungsindikatoren für die Erfassung konfigurieren.  Verwenden Sie das in [Windows- und Linux-Leistungsdatenquellen in Azure Monitor](data-sources-performance-counters.md) beschriebene Verfahren mit den Leistungsindikatoren in der folgenden Tabelle.

| Objektname | Name des Leistungsindikators |
|:--|:--|
| Apache HTTP Server | Busy Workers |
| Apache HTTP Server | Idle Workers |
| Apache HTTP Server | Pct Busy Workers |
| Apache HTTP Server | Total Pct CPU |
| Apache Virtual Host | Errors per Minute – Client |
| Apache Virtual Host | Errors per Minute – Server |
| Apache Virtual Host | KB per Request |
| Apache Virtual Host | Requests KB per Second |
| Apache Virtual Host | Requests per Second |



## <a name="next-steps"></a>Nächste Schritte
* [Erfassen von Leistungsindikatoren](data-sources-performance-counters.md) von Linux-Agents
* Erfahren Sie mehr über [Protokollabfragen](../log-query/log-query-overview.md) zum Analysieren der aus Datenquellen und Lösungen gesammelten Daten. 