---
title: Einrichten von PostgreSQL auf einem virtuellen Linux-Computer | Microsoft Docs
description: Erfahren Sie, wie Sie PostgreSQL auf einem virtuellen Linux-Computer in Azure installieren und konfigurieren.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 1a747363-0cc5-4ba3-9be7-084dfeb04651
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: 5fe5e819c4f1079b6eb1fa8bb19d337ecfed600d
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65955153"
---
# <a name="install-and-configure-postgresql-on-azure"></a>Installieren und Konfigurieren von PostgreSQL in Azure
PostgreSQL ist eine erweiterte Open-Source-Datenbank, die Oracle und DB2 ähnlich ist. Sie umfasst Unternehmensfunktionen wie vollständige ACID-Kompatibilität, zuverlässige Transaktionsverarbeitung und Parallelitätssteuerung mit mehreren Versionen. Darüber hinaus unterstützt sie Standards wie ANSI SQL und SQL/MED (einschließlich Fremddatenwrappern für Oracle, MySQL, MongoDB und viele andere). Sie ist in hohem Maße erweiterbar und bietet Unterstützung für mehr als 12 prozedurale Sprachen, GIN- und GIST-Indizes, Unterstützung für räumliche Daten und mehrere NoSQL-ähnliche Funktionen für JSON-Anwendungen oder Anwendungen auf Schlüssel-Wert-Basis.

In diesem Artikel erfahren Sie, wie Sie PostgreSQL auf einem virtuellen Azure-Computer, auf dem Linux ausgeführt wird, installieren und konfigurieren.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-postgresql"></a>Installieren von PostgreSQL
> [!NOTE]
> Sie benötigen einen virtuellen Azure-Computer mit Linux, um dieses Tutorial auszuführen. Bevor Sie fortfahren, sollten Sie die Informationen zum Erstellen und Einrichten eines virtuellen Linux-Computers im [Tutorial zu virtuellen Linux-Computern in Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lesen.
> 
> 

Verwenden Sie in diesem Fall Port 1999 als PostgreSQL-Port.  

Stellen Sie über PuTTY eine Verbindung mit dem virtuellen Linux-Computer her. Falls Sie zum ersten Mal einen virtuellen Linux-Computer in Azure verwenden, finden Sie unter [Verwenden von SSH mit Linux in Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Informationen zum Herstellen einer Verbindung mit einem virtuellen Linux-Computer mithilfe von PuTTY.

1. Führen Sie den folgenden Befehl aus, um in den Stammordner (Administrator) zu wechseln:
   
        # sudo su -
2. Für einige Distributionen gelten bestimmte Abhängigkeiten, die Sie vor der Installation von PostgreSQL installieren müssen. Suchen Sie Ihre Distribution in dieser Liste, und führen Sie den entsprechenden Befehl aus:
   
   * Linux auf Red Hat-Basis:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Linux auf Debian Basis:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * Suse Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. Laden Sie PostgreSQL in das Stammverzeichnis, und entpacken Sie das Paket:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    Oben ist ein Beispiel aufgeführt. Das ausführliche Downloadverzeichnis finden Sie im [Index von "/pub/source/"](https://ftp.postgresql.org/pub/source/).
4. Um den Build zu starten, führen Sie folgende Befehle aus:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Wenn Sie einen vollständigen Build, einschließlich der Dokumentation (HTML- und Handbuchseiten) sowie zusätzlicher Module (contrib), erstellen möchten, führen Sie stattdessen den folgenden Befehl aus:
   
        # gmake install-world
   
    Die folgende Bestätigungsmeldung wird angezeigt:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Konfigurieren von PostgreSQL
1. (Optional) Erstellen Sie eine symbolische Verknüpfung zum Verkürzen der PostgreSQL-Referenz, sodass die Versionsnummer nicht enthalten ist:
   
        # ln -s /opt/postgresql-9.3.5 /opt/pgsql
2. Erstellen Sie ein Verzeichnis für die Datenbank:
   
        # mkdir -p /opt/pgsql_data
3. Erstellen Sie einen Benutzer ohne Root-Rechte, und ändern Sie das Profil des Benutzers. Wechseln Sie zu dem neuen Benutzer (in diesem Beispiel *postgres* ):
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > Aus Gründen der Sicherheit verwendet PostgreSQL einen Benutzer ohne Root-Rechte zum Initialisieren, Starten oder Herunterfahren der Datenbank.
   > 
   > 
4. Bearbeiten Sie die Datei *bash_profile*, indem Sie die folgenden Befehle eingeben. Diese Zeilen werden am Ende der Datei *bash_profile* hinzugefügt:
   
        cat >> ~/.bash_profile <<EOF
        export PGPORT=1999
        export PGDATA=/opt/pgsql_data
        export LANG=en_US.utf8
        export PGHOME=/opt/pgsql
        export PATH=\$PATH:\$PGHOME/bin
        export MANPATH=\$MANPATH:\$PGHOME/share/man
        export DATA=`date +"%Y%m%d%H%M"`
        export PGUSER=postgres
        alias rm='rm -i'
        alias ll='ls -lh'
        EOF
5. Führen Sie die *bash_profile*-Datei aus:
   
        $ source .bash_profile
6. Überprüfen Sie die Installation mit dem folgenden Befehl:
   
        $ which psql
   
    Wenn die Installation erfolgreich war, wird die folgende Antwort angezeigt:
   
        /opt/pgsql/bin/psql
7. Sie können auch die PostgreSQL-Version überprüfen:
   
        $ psql -V

8. Initialisieren Sie die Datenbank:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    Daraufhin sollten Sie folgende Ausgabe erhalten:

![image](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Einrichten von PostgreSQL
<!--    [postgres@ test ~]$ exit -->

Führen Sie die folgenden Befehle aus:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Ändern Sie zwei Variablen in der Datei "/etc/init.d/postgresql". Das Präfix wird auf den Installationspfad von PostgreSQL festgelegt: **/opt/pgsql**. PGDATA wird auf den Datenspeicherpfad von PostgreSQL festgelegt: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/postgresql-install/no2.png)

Ändern Sie die Datei, um sie ausführbar zu machen:

    # chmod +x /etc/init.d/postgresql

Starten Sie PostgreSQL:

    # /etc/init.d/postgresql start

Überprüfen Sie, ob der PostgreSQL-Endpunkt aktiv ist:

    # netstat -tunlp|grep 1999

Die folgende Ausgabe wird angezeigt.

![image](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Herstellen einer Verbindung mit der Postgres-Datenbank
Wechseln Sie erneut zum Benutzer „postgres“:

    # su - postgres

Erstellen Sie eine Postgres-Datenbank:

    $ createdb events

Stellen Sie eine Verbindung mit der soeben erstellten Ereignisdatenbank her:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Erstellen und Löschen einer Postgres-Tabelle
Da jetzt eine Verbindung mit der Datenbank besteht, können Sie Tabellen in ihr erstellen.

Erstellen Sie mit dem folgenden Befehl beispielsweise eine neue Postgres-Beispieltabelle:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

Sie haben nun eine Tabelle mit vier Spalten mit den folgenden Spaltennamen und Einschränkungen eingerichtet:

1. Die Spalte "Name" wurde mit dem VARCHAR-Befehl auf unter 20 Zeichen beschränkt.
2. Die Spalte "Food" gibt die Speisen an, die die einzelnen Personen mitbringen. VARCHAR beschränkt diesen Text auf unter 30 Zeichen.
3. Die Spalte "confirmed" zeichnet auf, ob die Person zugestimmt hat, einen Beitrag zum Buffet zu leisten. Die zulässigen Werte lauten "Y" und "N".
4. Die Spalte „date“ wird angezeigt, wenn sich eine Person für das Ereignis angemeldet hat. Postgres erfordert, dass Datumsangaben im Format jjjj-mm-tt geschrieben werden

Wenn die Tabelle erfolgreich erstellt wurde, sollte folgende Ausgabe angezeigt werden:

![image](./media/postgresql-install/no4.png)

Sie können auch die Tabellenstruktur mit dem folgenden Befehl überprüfen:

![image](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Hinzufügen von Daten zu einer Tabelle
Fügen Sie zunächst Informationen in eine Zeile ein:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Die folgende Ausgabe sollte angezeigt werden:

![image](./media/postgresql-install/no6.png)

Sie können einige weitere Personen zur Tabelle hinzufügen. Hier finden Sie einige Optionen, Sie können aber auch eigene erstellen:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Anzeigen von Tabellen
Verwenden Sie den folgenden Befehl zum Anzeigen einer Tabelle:

    select * from potluck;

Die Ausgabe ist:

![image](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Löschen von Daten in einer Tabelle
Mit dem folgenden Befehl können Sie Daten in einer Tabelle löschen:

    delete from potluck where name=’John’;

Dadurch werden alle Informationen in der Zeile „John“ gelöscht. Die Ausgabe ist:

![image](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Aktualisieren von Daten in einer Tabelle
Mit dem folgenden Befehl können Sie Daten in einer Tabelle aktualisieren: In diesem Beispiel hat Sandy bestätigt, dass sie teilnimmt, sodass wir ihren Wert für „confirmed“ von „N“ in „Y“ ändern können:

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>Abrufen weiterer Informationen zu PostgreSQL
Sie haben die Installation von PostgreSQL auf einem virtuellen Linux-Computer in Azure abgeschlossen und können die Datenbank nun in Azure nutzen. Weitere Informationen zu PostgreSQL finden Sie auf der [PostgreSQL-Website](https://www.postgresql.org/).

