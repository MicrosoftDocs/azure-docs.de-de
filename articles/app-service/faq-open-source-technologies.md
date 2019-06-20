---
title: 'Häufig gestellte Fragen zu Open Source-Technologien: Azure App Service | Microsoft-Dokumentation'
description: Hier erhalten Sie Antworten auf häufig gestellte Fragen zu Open-Source-Technologien in Azure App Service-Web-Apps.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 7831e5e989835b2c9432dbd61a242584a7b6244d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61270202"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Häufig gestellte Fragen zu Open-Source-Technologien für Web-Apps in Azure | Microsoft-Dokumentation

In diesem Artikel erhalten Sie Antworten auf häufig gestellte Fragen zu Problemen mit Open-Source-Technologien bei [Azure App Service-Web-Apps](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Wie aktiviere ich die PHP-Protokollierung, um PHP-Probleme zu beheben?

So aktivieren Sie die PHP-Protokollierung

1. Melden Sie sich bei Ihrer [Kudu-Website](https://*yourwebsitename*.scm.azurewebsites.net) an.
2. Wählen Sie im obersten Menü **Debug Console** > **CMD** aus.
3. Wählen Sie den Ordner **Site** aus.
4. Wählen Sie den Ordner **wwwroot** aus.
5. Klicken Sie auf das Symbol **+** , und wählen Sie **New File** (Neue Datei) aus.
6. Legen Sie den Dateinamen auf **.user.ini** fest.
7. Aktivieren Sie das Stiftsymbol neben **.user.ini**.
8. Fügen Sie der Datei diesen Code hinzu: `log_errors=on`
9. Wählen Sie **Speichern** aus.
10. Aktivieren Sie das Stiftsymbol neben **wp-config.php**.
11. Ändern Sie den Text in den folgenden Code:
    ```php
    //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
    //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
    ```
12. Starten Sie im Azure-Portal im Menü „Web-App“ Ihre Web-App neu.

Weitere Informationen finden Sie unter [Aktivieren von WordPress-Fehlerprotokollen](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Wie protokolliere ich Python-Anwendungsfehler in Apps, die in App Service gehostet werden?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Wie ändere ich die Version der Node.js-Anwendung, die in App Service gehostet wird?

Um die Version der Node.js-Anwendung zu ändern, können Sie eine der folgenden Optionen wählen:

* Öffnen Sie im Azure-Portal **App-Einstellungen**.
  1. Navigieren Sie im Azure-Portal zu Ihrer Web-App.
  2. Klicken Sie auf dem Blatt **Einstellungen** auf **Anwendungseinstellungen**.
  3. In **App-Einstellungen** können Sie WEBSITE_NODE_DEFAULT_VERSION als Schlüssel und die gewünschte Version von Node.js als Wert hinzufügen.
  4. Wechseln Sie zu Ihrer [Kudu-Konsole](https://*yourwebsitename*.scm.azurewebsites.net).
  5. Um die Node.js-Version zu überprüfen, geben Sie den folgenden Befehl ein:  
     ```
     node -v
     ```
* Bearbeiten Sie die Datei „iisnode.yml“. Durch Ändern der Node.js-Version in der Datei „iisnode.yml“ wird nur die Laufzeitumgebung festgelegt, die iisnode verwendet. Kudu CMD u.a. nutzen weiter die Node.js-Version, die **App-Einstellungen** im Azure-Portal festgelegt ist.

  Um „iisnode.yml“ manuell festzulegen, erstellen Sie die Datei „iisnode.yml“ im Stammordner der App. Fügen Sie der Datei die folgende Zeile hinzu:
  ```yml
  nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
  ```
   
* Legen Sie die Datei „iisnode.yml“ mithilfe der Datei „package.json“ während der Bereitstellung der Quellcodeverwaltung fest.
  Der Azure-Quellcodeverwaltungsprozess umfasst die folgenden Schritte:
  1. Das Verschieben von Inhalt in die Azure-Web-App.
  2. Das Erstellen eines Standardbereitstellungsskripts, wenn keines vorhanden ist (deploy.cmd, Bereitstellungsdateien), im Stammordner der Web-App.
  3. Das Ausführen eines Bereitstellungsskripts, in dem die Datei „iisnode.yml“ erstellt wird, wenn Sie die Node.js-Version in der Datei „package.json“ unter „engines“ angeben: `"engines": {"node": "5.9.1","npm": "3.7.3"}`
  4. Die Datei „iisnode.yml“ enthält die folgende Codezeile:
      ```yml
      nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
      ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>Mir wird die Meldung „Fehler beim Herstellen einer Datenbankverbindung“ in meiner WordPress-App angezeigt, die in App Service gehostet wird. Wie kann ich das Problem beheben?

Wenn Ihnen dieser Fehler in der Azure-WordPress-App angezeigt wird, führen Sie zum Aktivieren von „php_errors.log“ und „debug.log“ die Schritte unter [Aktivieren von WordPress-Fehlerprotokollen](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/) aus.

Wenn die Protokolle aktiviert sind, reproduzieren Sie den Fehler, und überprüfen Sie die Protokolle, um zu prüfen, ob Verbindungen knapp werden:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Wenn Sie diesen Fehler in Ihren Dateien „debug.log“ oder „php_errors.log“ sehen, hat Ihre App die Anzahl der Verbindungen überschritten. Wenn das Hosten in ClearDB erfolgt, überprüfen Sie die Anzahl der Verbindungen, die in Ihrem [Serviceplan](https://www.cleardb.com/pricing.view) verfügbar sind.

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Wie debugge ich eine Node.js-app, die in App Service gehostet wird?

1.  Wechseln Sie zu Ihrer [Kudu-Konsole](https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole).
2.  Wechseln Sie zu Ihrem Ordner mit den Anwendungsprotokollen (D:\home\LogFiles\Application).
3.  Überprüfen Sie den Inhalt der Datei „logging_errors.txt“.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Wie installiere ich native Python-Module in einer App Service-Web-App oder -API-App?

Einige Pakete lassen sich ggf. nicht mithilfe von pip in Azure installieren. Das Paket ist möglicherweise nicht im Python Package Index verfügbar, oder ggf. ist ein Compiler erforderlich (auf dem Computer, auf dem die Web-App in App Service ausgeführt wird, ist kein Compiler verfügbar). Informationen zum Installieren nativer Module in App Service-Web-Apps und -API-Apps finden Sie unter [Installieren von Python-Modulen in App Service](https://blogs.msdn.microsoft.com/azureossds/2015/06/29/install-native-python-modules-on-azure-web-apps-api-apps/).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Wie stelle ich eine Django-App in App Service mithilfe von Git und der neuen Version von Python bereit?

Informationen zum Installieren von Django finden Sie unter [Bereitstellen einer Django-App in App Service](https://blogs.msdn.microsoft.com/azureossds/2016/08/25/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python/).

## <a name="where-are-the-tomcat-log-files-located"></a>Wo befinden sich die Tomcat-Protokolldateien?

Für Azure Marketplace und benutzerdefinierte Bereitstellungen:

* Speicherort des Ordners: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* Dateien von Interesse:
    * catalina.*JJJJ-MM-TT*.log
    * host-manager.*JJJJ-MM-TT*.log
    * localhost.*JJJJ-MM-TT*.log
    * manager.*JJJJ-MM-TT*.log
    * site_access_log.*JJJJ-MM-TT*.log


Für Bereitstellungen über das Portal unter **Anwendungseinstellungen**:

* Speicherort des Ordners: D:\home\LogFiles
* Dateien von Interesse:
    * catalina.*JJJJ-MM-TT*.log
    * host-manager.*JJJJ-MM-TT*.log
    * localhost.*JJJJ-MM-TT*.log
    * manager.*JJJJ-MM-TT*.log
    * site_access_log.*JJJJ-MM-TT*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Wie behebe ich JDBC-Treiberverbindungsfehler?

In Ihren Tomcat-Protokollen wird ggf. die folgende Meldung angezeigt:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

So beheben Sie den Fehler

1. Entfernen Sie die Datei „sqljdbc*.jar-“ aus Ihrem Ordner „app/lib“.
2. Bei Verwendung des benutzerdefinierten Tomcat- oder Azure Marketplace Tomcat-Webservers kopieren Sie diese JAR-Datei in den Tomcat-Ordner „lib“.
3. Wenn Sie Java im Azure-Portal aktivieren (wählen Sie **Java 1.8** > **Tomcat-Server** aus), kopieren Sie die JAR-Datei „sqljdbc“ in den Ordner, der parallel zu Ihrer App vorhanden ist. Fügen Sie dann die folgende Klassenpfadeinstellung der Datei „web.config“ hinzu:

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Warum erhalte ich Fehlermeldungen beim Versuch, Liveprotokolldateien zu kopieren?

Wenn Sie versuchen, Liveprotokolldateien für eine Java-App (z.B. Tomcat) zu kopieren, kann dieser FTP-Fehler angezeigt werden:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

Die Fehlermeldung kann je nach FTP-Client variieren.

Alle Java-Apps haben dieses Sperrproblem. Nur Kudu unterstützt das Herunterladen dieser Datei, während die App ausgeführt wird.

Das Beenden der App ermöglicht FTP-Zugriff auf diese Dateien.

Eine weitere Möglichkeit besteht darin, einen WebJob zu schreiben, der gemäß einem Zeitplan ausgeführt wird und diese Dateien in ein anderes Verzeichnis kopiert. Ein Beispielprojekt finden Sie im Projekt [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob).

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Wo finde ich die Protokolldateien für Jetty?

Für Marketplace- und benutzerdefinierte Bereitstellungen befindet sich die Protokolldatei im Ordner „D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs“. Beachten Sie, dass der Speicherort des Ordners von der verwendeten Version von Jetty abhängt. Der hier angegebene Pfad gilt z.B. für Jetty 9.1.2. Suchen Sie nach „jetty_*YYYY_MM_DD*.stderrout.log“.

Für Bereitstellungen über „App-Einstellungen“ im Portal befindet sich die Protokolldatei in „D:\home\LogFiles“. Suchen Sie nach „jetty_*YYYY_MM_DD*.stderrout.log“.

## <a name="can-i-send-email-from-my-azure-web-app"></a>Kann ich E-Mail aus meiner Azure-Web-App senden?

App Service bietet keine integrierte E-Mail-Funktion. Einige guten Alternativen für das Senden von E-Mail aus Ihrer App finden Sie in dieser [Diskussion auf Stack Overflow](https://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Warum leitet mich meine WordPress-Website zu einer anderen URL um?

Wenn Sie vor Kurzem zu Azure migriert sind, leitet WordPress Sie zur alten Domänen-URL um. Dies wird durch eine Einstellung in der MySQL-Datenbank verursacht.

WordPress Buddy+ ist eine Erweiterung für Azure-Websites, mit deren Hilfe Sie die Umleitungs-URL direkt in der Datenbank aktualisieren können. Weitere Informationen zur Verwendung von WordPress Buddy+ finden Sie unter [WordPress tools and MySQL migration with WordPress Buddy+](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/) (WordPress-Tools und MySQL-Migration mit WordPress Buddy+).

Wenn Sie die Umleitungs-URL lieber manuell mithilfe von SQL-Abfragen oder PHPMyAdmin aktualisieren, gehen Sie wie unter [WordPress: Redirecting to wrong URL](https://blogs.msdn.microsoft.com/azureossds/2016/07/12/wordpress-redirecting-to-wrong-url/) (WordPress: Umleitung zur falschen URL) beschrieben vor.

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Wie ändere ich mein WordPress-Anmeldungskennwort?

Wenn Sie Ihr WordPress-Anmeldungskennwort vergessen haben, können es mit WordPress Buddy+ aktualisieren. Um Ihr Kennwort zurückzusetzen, installieren Sie die Erweiterung für Azure-Websites WordPress Buddy+, und führen Sie die Schritte unter [WordPress tools and MySQL migration with WordPress Buddy+](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/) (WordPress-Tools und MySQL-Migration mit WordPress Buddy+) aus.

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Ich kann mich nicht bei WordPress anmelden. Wie löse ich dieses Problem?

Wenn Sie sich nach einer vor Kurzem erfolgten Installation eines Plug-Ins aus WordPress ausgesperrt haben, verfügen Sie möglicherweise über ein fehlerhaftes Plug-In. WordPress Buddy+ ist eine Erweiterung für Azure-Websites, mit der Sie WordPress-Plug-Ins deaktivieren können. Weitere Informationen finden Sie unter [WordPress tools and MySQL migration with WordPress Buddy+](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/) (WordPress-Tools und MySQL-Migration mit WordPress Buddy+).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Wie migriere ich meine WordPress-Datenbank?

Sie haben mehrere Optionen zum Migrieren der MySQL-Datenbank, die mit Ihrer WordPress-Website verbunden ist:

* Entwickler: Verwenden Sie die [Eingabeaufforderung oder PHPMyAdmin](https://blogs.msdn.microsoft.com/azureossds/2016/03/02/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service/).
* Andere Benutzer: Verwenden Sie [WordPress Buddy+](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Wie kann ich WordPress sicherer machen?

Informationen zu bewährten Sicherheitsmethoden für WordPress finden Sie unter [Best practices for WordPress security in Azure](https://blogs.msdn.microsoft.com/azureossds/2016/12/26/best-practices-for-wordpress-security-on-azure/) (Bewährte Methoden für die WordPress-Sicherheit in Azure).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Ich versuche, PHPMyAdmin zu verwenden, und erhalte die Fehlermeldung „Zugriff verweigert“. Wie löse ich dieses Problem?

Dieses Problem kann auftreten, wenn die MySQL-In-App-Funktion noch nicht in dieser App Service-Instanz ausgeführt wird. Um das Problem zu beheben, versuchen Sie, auf Ihre Website zuzugreifen. Dadurch werden die erforderlichen Prozesse einschließlich des MySQL-In-App-Prozesses gestartet. Um zu prüfen, ob der MySQL-In-App-Prozess ausgeführt wird, vergewissern Sie sich im Prozess-Explorer, dass „mysqld.exe“ in den Prozessen aufgeführt ist.

Nachdem Sie sichergestellt haben, dass der MySQL-In-App-Prozess ausgeführt wird, versuchen Sie, PHPMyAdmin zu verwenden.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Ich erhalte die HTTP-Fehlermeldung 403, wenn ich versuche, meine MySQL-In-App-Datenbank mithilfe von PHPMyadmin zu importieren oder zu exportieren. Wie löse ich dieses Problem?

Wenn Sie eine ältere Version von Chrome verwenden, ist möglicherweise ein bekannter Fehler aufgetreten. Um das Problem zu beheben, aktualisieren Sie auf eine neuere Version von Chrome. Oder verwenden Sie einen anderen Browser wie Internet Explorer oder Microsoft Edge, in dem das Problem nicht auftritt.
