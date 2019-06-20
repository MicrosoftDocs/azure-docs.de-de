---
title: Herstellen einer Verbindung mit SQL-Datenbank mit C und C++ | Microsoft-Dokumentation
description: Verwenden Sie den Beispielcode in diesem Schnellstart-Artikel zum Erstellen einer modernen Anwendung in C++, die mit Azure SQL-Datenbank durch eine leistungsfähige relationale Datenbank in der Cloud unterstützt wird.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: cpp
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 12/12/2018
ms.openlocfilehash: 00a3904bd78f3bb76266c726af28582770b23921
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60723986"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Herstellen einer Verbindung mit SQL-Datenbank mit C und C++

Dieser Beitrag ist für C- und C++-Entwickler gedacht, die eine Verbindung mit Azure SQL-Datenbank herstellen möchten. Er ist in Abschnitte unterteilt, damit Sie direkt auf den für Sie interessanten Abschnitt zugreifen können.

## <a name="prerequisites-for-the-cc-tutorial"></a>Voraussetzungen für das C/C++-Tutorial

Stellen Sie sicher, dass Sie über Folgendes verfügen:

* Ein aktives Azure-Konto. Wenn Sie kein Konto haben, können Sie sich für eine [kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/)registrieren.
* [Visual Studio](https://www.visualstudio.com/downloads/) Sie müssen die C++-Sprachkomponenten installieren, um dieses Beispiel erstellen und ausführen zu können.
* [Visual Studio-Linux-Entwicklung](https://visualstudiogallery.msdn.microsoft.com/725025cf-7067-45c2-8d01-1e0fd359ae6e). Wenn Sie unter Linux entwickeln, müssen Sie auch die Linux-Erweiterung für Visual Studio installieren.

## <a id="AzureSQL"></a>Azure SQL-Datenbank und SQL Server auf virtuellen Computern
Azure SQL basiert auf Microsoft SQL Server und ist dafür ausgelegt, einen leistungsfähigen und skalierbaren Dienst mit hoher Verfügbarkeit zu bieten. Die Verwendung von SQL Azure hat gegenüber der Nutzung Ihrer eigenen lokal ausgeführten Datenbank viele Vorteile. Bei SQL Azure müssen Sie die Datenbank nicht installieren, einrichten, warten oder verwalten, sondern nur den Inhalt und die Struktur der Datenbank. Typische Aspekte wie Fehlertoleranz und Redundanz, um die es bei Datenbanken geht, sind bereits vorhanden.

Azure verfügt derzeit über zwei Optionen zum Hosten von SQL Server-Workloads: Azure SQL-Datenbank (Database as a Service) und SQL Server auf virtuellen Computern (VMs). Wir gehen hier nicht näher auf die Unterschiede zwischen diesen beiden Optionen ein, aber Azure SQL-Datenbank ist die beste Möglichkeit für neue cloudbasierte Anwendungen. Mit diesem Ansatz können Sie Kosteneinsparungen erzielen und kommen in den Genuss der mit Clouddiensten verbundenen Leistungsoptimierung. Wenn Sie mit dem Gedanken spielen, Ihre lokalen Anwendungen in die Cloud zu migrieren bzw. in die Cloud zu erweitern, ist SQL Server auf virtuellen Azure-Computern ggf. besser für Sie geeignet. Der Einfachheit halber erstellen wir in diesem Artikel eine Azure SQL-Datenbank.

## <a id="ODBC"></a>Datenzugriffstechnologien: ODBC und OLE DB
Das Herstellen einer Verbindung mit Azure SQL-Datenbank ist nicht viel anders. Derzeit gibt es zwei Möglichkeiten, eine Verbindung mit Datenbanken herzustellen: ODBC (Open Database Connectivity) und OLE DB (Object Linking and Embedding Database). In den letzten Jahren hat Microsoft eine [Anpassung an ODBC in Bezug auf den Zugriff auf native relationale Daten](https://blogs.msdn.microsoft.com/sqlnativeclient/20../../microsoft-is-aligning-with-odbc-for-native-relational-data-access/) durchgeführt. ODBC ist relativ einfach und außerdem deutlich schneller als OLE DB. Der einzige Nachteil ist, dass für ODBC eine ältere API im C-Stil verwendet wird.

## <a id="Create"></a>Schritt 1:  Erstellen der Azure SQL-Datenbank
Auf der [Seite für erste Schritte](sql-database-single-database-get-started.md) erhalten Sie Informationen zum Erstellen einer Beispieldatenbank.  Alternativ hierzu können Sie sich an dieses [kurze zweiminütige Video](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) halten, um mit dem Azure-Portal eine Azure SQL-Datenbank zu erstellen.

## <a id="ConnectionString"></a>Schritt 2:  Abrufen der Verbindungszeichenfolge
Nachdem Ihre Azure SQL-Datenbank bereitgestellt wurde, müssen Sie die folgenden Schritte ausführen, um die Verbindungsinformationen zu ermitteln und Ihre Client-IP für den Firewallzugriff hinzuzufügen.

Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer ODBC-Verbindungszeichenfolge für Azure SQL-Datenbank, indem Sie die Option **Datenbank-Verbindungszeichenfolgen anzeigen** im Übersichtsbereich für Ihre Datenbank verwenden:

![ODBCConnectionString](./media/sql-database-develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/sql-database-develop-cplusplus-simple/dbconnection.png)

Kopieren Sie den Inhalt der Zeichenfolge **ODBC (Includes Node.js) [SQL authentication]** . Wir verwenden diese Zeichenfolge später, um die Verbindung vom C++-ODBC-Befehlszeileninterpreter herzustellen. Diese Zeichenfolge enthält Details wie Treiber, Server und andere Parameter für die Datenbankverbindung.

## <a id="Firewall"></a>Schritt 3:  Hinzufügen Ihrer IP zur Firewall
Navigieren Sie zum Firewallabschnitt für Ihren Datenbankserver, und [fügen Sie die Client-IP der Firewall mit diesen Schritten hinzu](sql-database-configure-firewall-settings.md), um sicherzustellen, dass die Verbindung erfolgreich hergestellt werden kann:

![AddyourIPWindow](./media/sql-database-develop-cplusplus-simple/ip.png)

An diesem Punkt haben Sie Ihre Instanz von Azure SQL-Datenbank konfiguriert und können die Verbindung für Ihren C++-Code herstellen.

## <a id="Windows"></a>Schritt 4: Herstellen einer Verbindung von einer Windows-C/C++-Anwendung
Sie können auf einfache Weise [für Azure SQL-Datenbank per ODBC unter Windows eine Verbindung herstellen, indem Sie dieses Beispiel verwenden](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), das mit Visual Studio erstellt wird. Im Beispiel wird ein ODBC-Befehlszeileninterpreter implementiert, der verwendet werden kann, um eine Verbindung mit der Instanz von Azure SQL-Datenbank herzustellen. In diesem Beispiel wird entweder eine Datenbankquellenname-Datei (DSN) als Befehlszeilenargument oder die ausführliche Verbindungszeichenfolge verwendet, die wir zuvor aus dem Azure-Portal kopiert haben. Rufen Sie die Eigenschaftenseite für dieses Projekt auf, und fügen Sie die Verbindungszeichenfolge wie hier dargestellt als Befehlsargument ein:

![DSN Propsfile](./media/sql-database-develop-cplusplus-simple/props.png)

Achten Sie darauf, dass Sie die richtigen Authentifizierungsdetails für Ihre Datenbank als Teil der Datenbank-Verbindungszeichenfolge angeben.

Starten Sie die Anwendung, um die Erstellung durchzuführen. Das folgende Fenster mit dem Hinweis, dass die Verbindung erfolgreich hergestellt wurde, sollte angezeigt werden. Sie können sogar einige einfache SQL-Befehle wie **Create Table** ausführen, um die Datenbankverbindung zu überprüfen:

![SQL-Befehle](./media/sql-database-develop-cplusplus-simple/sqlcommands.png)

Alternativ hierzu können Sie auch eine DSN-Datei mit dem Assistenten erstellen, der gestartet wird, wenn keine Befehlsargumente angegeben werden. Wir empfehlen Ihnen, diese Option ebenfalls auszuprobieren. Sie können diese DSN-Datei für die Automation und den Schutz Ihrer Authentifizierungseinstellungen verwenden:

![Erstellen der DSN-Datei](./media/sql-database-develop-cplusplus-simple/datasource.png)

Glückwunsch! Sie haben mit C++ und ODBC unter Windows jetzt eine Verbindung mit Azure SQL hergestellt. Sie können weiterlesen, um sich auch über die Vorgehensweise für die Linux-Plattform zu informieren.

## <a id="Linux"></a>Schritt 5: Herstellen einer Verbindung von einer Linux-C/C++-Anwendung
Falls Sie es noch nicht gehört haben: Mit Visual Studio können Sie jetzt auch C++-Linux-Anwendungen entwickeln. Informationen zu diesem neuen Szenario finden Sie im Blog [Visual C++ for Linux Development](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) (Visual C++ für Linux-Entwicklung). Für die Linux-Erstellung benötigen Sie einen Remotecomputer, auf dem Ihre Linux-Distribution ausgeführt wird. Falls Sie keinen Remotecomputer haben, können Sie diesen schnell einrichten, indem Sie die Informationen unter [Erstellen eines virtuellen Linux-Computers mithilfe der Azure-Befehlszeilenschnittstelle 2.0 (Vorschau)](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nutzen.

Für die Zwecke dieses Tutorials nehmen wir an, dass Sie eine Linux-Distribution vom Typ Ubuntu 16.04 eingerichtet haben. Die hier angegebenen Schritte sollten auch für Ubuntu 15.10, Red Hat 6 und Red Hat 7 gelten.

Mit den folgenden Schritten werden die erforderlichen Bibliotheken für SQL und ODBC für Ihre Distribution installiert:

    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*

Starten Sie Visual Studio. Fügen Sie unter „Extras“ > „Optionen“ > „Plattformübergreifend“ > „Verbindungs-Manager“ eine Verbindung zum Linux-Feld hinzu:

![Tooloptionen](./media/sql-database-develop-cplusplus-simple/tools.png)

Erstellen Sie nach dem Herstellen der Verbindung per SSH eine leere Projektvorlage (Linux):

![Neue Projektvorlage](./media/sql-database-develop-cplusplus-simple/template.png)

Anschließend können Sie eine [neue C-Quelldatei hinzufügen und diesen Inhalt einfügen](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Mithilfe von ODBC APIs SQLAllocHandle, SQLSetConnectAttr und SQLDriverConnect sollten Sie eine Verbindung mit Ihrer Datenbank initialisieren und einrichten können.
Wie beim Windows-ODBC-Beispiel auch, müssen Sie den SQLDriverConnect-Aufruf durch die Details aus Ihren Parametern der Datenbank-Verbindungszeichenfolge ersetzen, die Sie zuvor aus dem Azure-Portal kopiert haben.

     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);

Der letzte Schritt vor dem Kompilieren ist das Hinzufügen von **odbc** als Bibliotheksabhängigkeit:

![Hinzufügen von ODBC als Eingabebibliothek](./media/sql-database-develop-cplusplus-simple/lib.png)

Rufen Sie die Linux-Konsole über das Menü **Debuggen** auf, um die Anwendung zu starten:

![Linux-Konsole](./media/sql-database-develop-cplusplus-simple/linuxconsole.png)

Wenn die Verbindung erfolgreich hergestellt wurde, wird in der Linux-Konsole jetzt der aktuelle Datenbankname angezeigt:

![Ausgabefenster der Linux-Konsole](./media/sql-database-develop-cplusplus-simple/linuxconsolewindow.png)

Glückwunsch! Sie haben das Tutorial erfolgreich abgeschlossen und können über C++ auf Windows- und Linux-Plattformen jetzt eine Verbindung mit Ihrer Instanz von Azure SQL-Datenbank herstellen.

## <a id="GetSolution"></a>Herunterladen der vollständigen Projektmappe für das C/C++-Tutorial
Sie finden die GetStarted-Projektmappe mit allen Beispielen dieses Artikels auf GitHub:

* [ODBC-Beispiel für Windows C++](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29): Laden Sie das Windows C++-ODBC-Beispiel herunter, um eine Verbindung mit Azure SQL herzustellen.
* [C++-ODBC-Beispiel für Linux](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29): Laden Sie das C++-ODBC-Beispiel für Linux herunter, um eine Verbindung mit Azure SQL herzustellen.

## <a name="next-steps"></a>Nächste Schritte
* Lesen Sie die [Übersicht über die Entwicklung von SQL-Datenbanken](sql-database-develop-overview.md)
* Weitere Informationen: [ODBC-API-Referenz](https://docs.microsoft.com/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Zusätzliche Ressourcen
* [Entwurfsmuster für mehrinstanzenfähige SaaS-Anwendungen und Azure SQL-Datenbank](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Entdecken Sie alle [Funktionen von SQL-Datenbank](https://azure.microsoft.com/services/sql-database/)

