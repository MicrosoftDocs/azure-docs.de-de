---
title: Herstellen einer Verbindung mit SQL-Datenbank mit C und C++
description: Verwenden Sie den Beispielcode in diesem Schnellstart-Artikel zum Erstellen einer modernen Anwendung in C++, die mit Azure SQL-Datenbank durch eine leistungsfähige relationale Datenbank in der Cloud unterstützt wird.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: cpp
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/12/2018
ms.openlocfilehash: e891c5797c9ce93e6cab7a07d2f68de1a9157249
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92674771"
---
# <a name="connect-to-sql-database-using-c-and-c"></a>Herstellen einer Verbindung mit SQL-Datenbank mit C und C++
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Dieser Beitrag ist für C- und C++-Entwickler gedacht, die eine Verbindung mit Azure SQL-Datenbank herstellen möchten. Er ist in Abschnitte unterteilt, damit Sie direkt auf den für Sie interessanten Abschnitt zugreifen können.

## <a name="prerequisites-for-the-cc-tutorial"></a>Voraussetzungen für das C/C++-Tutorial

Stellen Sie sicher, dass Sie über Folgendes verfügen:

* Ein aktives Azure-Konto. Wenn Sie kein Konto haben, können Sie sich für eine [kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/)registrieren.
* [Visual Studio](https://www.visualstudio.com/downloads/). Sie müssen die C++-Sprachkomponenten installieren, um dieses Beispiel erstellen und ausführen zu können.
* [Visual Studio-Linux-Entwicklung](/cpp/linux/?view=vs-2019). Wenn Sie unter Linux entwickeln, müssen Sie auch die Linux-Erweiterung für Visual Studio installieren.

## <a name="azure-sql-database-and-sql-server-on-virtual-machines"></a><a id="AzureSQL"></a>Azure SQL-Datenbank und SQL Server auf virtuellen Computern

Azure SQL-Datenbank basiert auf Microsoft SQL Server und ist dafür ausgelegt, einen hochverfügbaren, leistungsfähigen und skalierbaren Dienst bereitzustellen. Die Verwendung von Azure SQL-Datenbank hat gegenüber der Nutzung Ihrer eigenen lokal ausgeführten Datenbank viele Vorteile. Mit Azure SQL-Datenbank müssen Sie die Datenbank nicht installieren, einrichten, warten oder verwalten, sondern sich nur um den Inhalt und die Struktur der Datenbank kümmern. Typische Aspekte wie Fehlertoleranz und Redundanz, um die es bei Datenbanken geht, sind bereits vorhanden.

Azure verfügt derzeit über zwei Optionen zum Hosten von SQL Server-Workloads: Azure SQL-Datenbank, Database as a Service und SQL Server auf virtuellen Computern (VMs). Wir gehen hier nicht näher auf die Unterschiede zwischen diesen beiden Optionen ein, aber Azure SQL-Datenbank ist die beste Möglichkeit für neue cloudbasierte Anwendungen. Mit diesem Ansatz können Sie Kosteneinsparungen erzielen und kommen in den Genuss der mit Clouddiensten verbundenen Leistungsoptimierung. Wenn Sie mit dem Gedanken spielen, Ihre lokalen Anwendungen in die Cloud zu migrieren bzw. in die Cloud zu erweitern, ist SQL Server auf virtuellen Azure-Computern ggf. besser für Sie geeignet. Der Einfachheit halber erstellen Sie in diesem Artikel eine Azure SQL-Datenbank-Instanz.

## <a name="data-access-technologies-odbc-and-ole-db"></a><a id="ODBC"></a>Datenzugriffstechnologien: ODBC und OLE DB

Das Herstellen einer Verbindung mit Azure SQL-Datenbank ist nicht viel anders. Derzeit gibt es zwei Möglichkeiten, eine Verbindung mit Datenbanken herzustellen: ODBC (Open Database Connectivity) und OLE DB (Object Linking and Embedding Database). In den letzten Jahren hat Microsoft eine [Anpassung an ODBC in Bezug auf den Zugriff auf native relationale Daten](/archive/blogs/sqlnativeclient/microsoft-is-aligning-with-odbc-for-native-relational-data-access) durchgeführt. ODBC ist relativ einfach und außerdem deutlich schneller als OLE DB. Der einzige Nachteil ist, dass für ODBC eine ältere API im C-Stil verwendet wird.

## <a name="step-1--creating-your-azure-sql-database"></a><a id="Create"></a>Schritt 1:  Erstellen der Azure SQL-Datenbank

Auf der [Seite für erste Schritte](single-database-create-quickstart.md) erhalten Sie Informationen zum Erstellen einer Beispieldatenbank.  Alternativ hierzu können Sie sich an dieses [kurze zweiminütige Video](https://azure.microsoft.com/documentation/videos/azure-sql-database-create-dbs-in-seconds/) halten, um mit dem Azure-Portal eine Azure SQL-Datenbank zu erstellen.

## <a name="step-2--get-connection-string"></a><a id="ConnectionString"></a>Schritt 2:  Abrufen der Verbindungszeichenfolge

Nachdem Ihre Azure SQL-Datenbank-Instanz bereitgestellt wurde, müssen Sie die folgenden Schritte ausführen, um die Verbindungsinformationen zu ermitteln und Ihre Client-IP-Adresse für den Firewallzugriff hinzuzufügen.

Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zur ODBC-Verbindungszeichenfolge für Ihre Azure SQL-Datenbank-Instanz, indem Sie die Option **Datenbank-Verbindungszeichenfolgen anzeigen** im Übersichtsbereich für Ihre Datenbank verwenden:

![ODBCConnectionString](./media/develop-cplusplus-simple/azureportal.png)

![ODBCConnectionStringProps](./media/develop-cplusplus-simple/dbconnection.png)

Kopieren Sie den Inhalt der Zeichenfolge **ODBC (Includes Node.js) [SQL authentication]** . Wir verwenden diese Zeichenfolge später, um die Verbindung vom C++-ODBC-Befehlszeileninterpreter herzustellen. Diese Zeichenfolge enthält Details wie Treiber, Server und andere Parameter für die Datenbankverbindung.

## <a name="step-3--add-your-ip-to-the-firewall"></a><a id="Firewall"></a>Schritt 3:  Hinzufügen Ihrer IP zur Firewall

Navigieren Sie zum Firewallabschnitt für Ihren Server, und [fügen Sie die Client-IP-Adresse der Firewall mit diesen Schritten hinzu](firewall-configure.md), um sicherzustellen, dass die Verbindung erfolgreich hergestellt werden kann:

![AddyourIPWindow](./media/develop-cplusplus-simple/ip.png)

An diesem Punkt haben Sie Ihre Azure SQL-Datenbank-Instanz konfiguriert und können die Verbindung aus Ihrem C++-Code herstellen.

## <a name="step-4-connecting-from-a-windows-cc-application"></a><a id="Windows"></a>Schritt 4: Herstellen einer Verbindung von einer Windows-C/C++-Anwendung

Sie können auf einfache Weise [für Azure SQL-Datenbank per ODBC unter Windows eine Verbindung herstellen, indem Sie dieses Beispiel verwenden](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29), das mit Visual Studio erstellt wird. Im Beispiel wird ein ODBC-Befehlszeileninterpreter implementiert, der verwendet werden kann, um eine Verbindung mit der Azure SQL-Datenbank-Instanz herzustellen. In diesem Beispiel wird entweder eine Datenbankquellenname-Datei (DSN) als Befehlszeilenargument oder die ausführliche Verbindungszeichenfolge verwendet, die wir zuvor aus dem Azure-Portal kopiert haben. Rufen Sie die Eigenschaftenseite für dieses Projekt auf, und fügen Sie die Verbindungszeichenfolge wie hier dargestellt als Befehlsargument ein:

![DSN Propsfile](./media/develop-cplusplus-simple/props.png)

Achten Sie darauf, dass Sie die richtigen Authentifizierungsdetails für Ihre Datenbank als Teil der Datenbank-Verbindungszeichenfolge angeben.

Starten Sie die Anwendung, um die Erstellung durchzuführen. Das folgende Fenster mit dem Hinweis, dass die Verbindung erfolgreich hergestellt wurde, sollte angezeigt werden. Sie können sogar einige einfache SQL-Befehle wie **Create Table** ausführen, um die Datenbankverbindung zu überprüfen:

![SQL-Befehle](./media/develop-cplusplus-simple/sqlcommands.png)

Alternativ hierzu können Sie auch eine DSN-Datei mit dem Assistenten erstellen, der gestartet wird, wenn keine Befehlsargumente angegeben werden. Wir empfehlen Ihnen, diese Option ebenfalls auszuprobieren. Sie können diese DSN-Datei für die Automation und den Schutz Ihrer Authentifizierungseinstellungen verwenden:

![Erstellen der DSN-Datei](./media/develop-cplusplus-simple/datasource.png)

Glückwunsch! Sie haben mit C++ und ODBC unter Windows jetzt eine Verbindung mit Azure SQL hergestellt. Sie können weiterlesen, um sich auch über die Vorgehensweise für die Linux-Plattform zu informieren.

## <a name="step-5-connecting-from-a-linux-cc-application"></a><a id="Linux"></a>Schritt 5: Herstellen einer Verbindung von einer Linux-C/C++-Anwendung

Falls Sie es noch nicht gehört haben: Mit Visual Studio können Sie jetzt auch C++-Linux-Anwendungen entwickeln. Informationen zu diesem neuen Szenario finden Sie im Blog [Visual C++ for Linux Development](https://blogs.msdn.microsoft.com/vcblog/20../../visual-c-for-linux-development/) (Visual C++ für Linux-Entwicklung). Für die Linux-Erstellung benötigen Sie einen Remotecomputer, auf dem Ihre Linux-Distribution ausgeführt wird. Falls Sie keinen Remotecomputer haben, können Sie einen solchen schnell einrichten, indem Sie die Informationen unter [Virtuelle Azure Linux-Computer](../../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nutzen.

Für die Zwecke dieses Tutorials nehmen wir an, dass Sie eine Linux-Distribution vom Typ Ubuntu 16.04 eingerichtet haben. Die hier angegebenen Schritte sollten auch für Ubuntu 15.10, Red Hat 6 und Red Hat 7 gelten.

Mit den folgenden Schritten werden die erforderlichen Bibliotheken für SQL und ODBC für Ihre Distribution installiert:

```console
    sudo su
    sh -c 'echo "deb [arch=amd64] https://apt-mo.trafficmanager.net/repos/mssql-ubuntu-test/ xenial main" > /etc/apt/sources.list.d/mssqlpreview.list'
    sudo apt-key adv --keyserver apt-mo.trafficmanager.net --recv-keys 417A0893
    apt-get update
    apt-get install msodbcsql
    apt-get install unixodbc-dev-utf16 #this step is optional but recommended*
```

Starten Sie Visual Studio. Fügen Sie unter „Extras“ > „Optionen“ > „Plattformübergreifend“ > „Verbindungs-Manager“ eine Verbindung zum Linux-Feld hinzu:

![Tooloptionen](./media/develop-cplusplus-simple/tools.png)

Erstellen Sie nach dem Herstellen der Verbindung per SSH eine leere Projektvorlage (Linux):

![Neue Projektvorlage](./media/develop-cplusplus-simple/template.png)

Anschließend können Sie eine [neue C-Quelldatei hinzufügen und diesen Inhalt einfügen](https://github.com/Microsoft/VCSamples/blob/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29/odbcconnector/odbcconnector.c). Mithilfe von ODBC APIs SQLAllocHandle, SQLSetConnectAttr und SQLDriverConnect sollten Sie eine Verbindung mit Ihrer Datenbank initialisieren und einrichten können.
Wie beim Windows-ODBC-Beispiel auch, müssen Sie den SQLDriverConnect-Aufruf durch die Details aus Ihren Parametern der Datenbank-Verbindungszeichenfolge ersetzen, die Sie zuvor aus dem Azure-Portal kopiert haben.

```c
     retcode = SQLDriverConnect(
        hdbc, NULL, "Driver=ODBC Driver 13 for SQL"
                    "Server;Server=<yourserver>;Uid=<yourusername>;Pwd=<"
                    "yourpassword>;database=<yourdatabase>",
        SQL_NTS, outstr, sizeof(outstr), &outstrlen, SQL_DRIVER_NOPROMPT);
```

Der letzte Schritt vor dem Kompilieren ist das Hinzufügen von **odbc** als Bibliotheksabhängigkeit:

![Hinzufügen von ODBC als Eingabebibliothek](./media/develop-cplusplus-simple/lib.png)

Rufen Sie die Linux-Konsole über das Menü **Debuggen** auf, um die Anwendung zu starten:

![Linux-Konsole](./media/develop-cplusplus-simple/linuxconsole.png)

Wenn die Verbindung erfolgreich hergestellt wurde, wird in der Linux-Konsole jetzt der aktuelle Datenbankname angezeigt:

![Ausgabefenster der Linux-Konsole](./media/develop-cplusplus-simple/linuxconsolewindow.png)

Glückwunsch! Sie haben das Tutorial erfolgreich abgeschlossen und können über C++ auf Windows- und Linux-Plattformen jetzt eine Verbindung mit Ihrer Azure SQL-Datenbank-Instanz herstellen.

## <a name="get-the-complete-cc-tutorial-solution"></a><a id="GetSolution"></a>Herunterladen der vollständigen Projektmappe für das C/C++-Tutorial

Sie finden die GetStarted-Projektmappe mit allen Beispielen dieses Artikels auf GitHub:

* [ODBC-Beispiel für Windows C++](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28windows%29): Laden Sie das Windows C++-ODBC-Beispiel herunter, um eine Verbindung mit Azure SQL herzustellen.
* [C++-ODBC-Beispiel für Linux](https://github.com/Microsoft/VCSamples/tree/master/VC2015Samples/ODBC%20database%20sample%20%28linux%29): Laden Sie das C++-ODBC-Beispiel für Linux herunter, um eine Verbindung mit Azure SQL herzustellen.

## <a name="next-steps"></a>Nächste Schritte

* Lesen Sie die [Übersicht über die Entwicklung von SQL-Datenbanken](develop-overview.md)
* Weitere Informationen: [ODBC-API-Referenz](/sql/odbc/reference/syntax/odbc-api-reference/)

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Entwurfsmuster für mehrinstanzenfähige SaaS-Anwendungen und Azure SQL-Datenbank](saas-tenancy-app-design-patterns.md)
* Entdecken Sie alle [Funktionen von SQL-Datenbank](https://azure.microsoft.com/services/sql-database/)