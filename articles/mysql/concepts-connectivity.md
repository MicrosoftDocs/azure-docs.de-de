---
title: Behandeln vorübergehender Konnektivitätsfehler – Azure Database for MySQL
description: Hier erfahren Sie, wie Sie vorübergehende Verbindungsfehler behandeln und eine effiziente Verbindung mit Azure Database for MySQL herstellen können.
keywords: MySQL-Verbindung,Verbindungszeichenfolge,Verbindungsprobleme, vorübergehender Fehler,Verbindungsfehler,effizientes Verbinden
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 89673c14c38947dc5aeb91cacde1eb2755e84138
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542608"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>Behandeln vorübergehender Fehler und effizientes Verbinden mit Azure Database for MySQL

In diesem Artikel wird beschrieben, wie Sie vorübergehende Fehler behandeln und eine effiziente Verbindung mit Azure Database for MySQL herstellen können.

## <a name="transient-errors"></a>Vorübergehende Fehler

Bei einem vorübergehenden Fehler handelt es sich um einen Fehler, der sich von alleine löst. Diese Fehler zeigen sich meist dadurch, dass die Verbindung mit dem Datenbankserver getrennt wird. Außerdem können keine neuen Verbindungen mit einem Server hergestellt werden. Vorübergehende Fehler können z.B. bei Hardware- oder Netzwerkfehlern auftreten. Ein weiterer Grund kann die Einführung einer neuen Version des PaaS-Diensts sein. Meist werden diese Ereignisse in weniger als 60 Sekunden automatisch vom System behoben. Sie sollten beim Entwerfen und Entwickeln von Anwendungen in der Cloud vorübergehende Fehler einkalkulieren. Gehen Sie davon aus, dass sie jederzeit und in jeder Komponente auftreten können, und entwickeln Sie entsprechende Logik zum Umgang mit diesen Situationen.

## <a name="handling-transient-errors"></a>Behandeln vorübergehender Fehler

Vorübergehende Fehler sollten mit Wiederholungslogik behandelt werden. Folgende Situationen sollten berücksichtigt werden:

* Beim Versuch, eine Verbindung zu öffnen, tritt ein Fehler auf.
* Eine Verbindung im Leerlauf wird auf Serverseite getrennt. Ein übermittelter Befehl kann nicht ausgeführt werden.
* Eine aktive Verbindung, die derzeit einen Befehl ausführt, wird getrennt.

Die ersten beiden Fälle können relativ unkompliziert behandelt werden. Versuchen Sie, die Verbindung erneut herzustellen. Wenn Sie Erfolg haben, wurde der vorübergehende Fehler durch das System behoben. Sie können Ihre Instanz von Azure Database for MySQL wiederverwenden. Es wird empfohlen, vor dem Wiederherstellen der Verbindung Wartezeiten einzufügen. Wenn die ersten Verbindungsversuche zu Fehlern führen, brechen Sie die Versuche ab. Auf diese Weise kann das System alle Ressourcen nutzen, um den Fehler zu beheben. Wenden Sie das folgende bewährte Muster an:

* Warten Sie vor dem ersten Wiederholungsversuch fünf Sekunden.
* Erhöhen Sie bei jeder folgenden Wiederholung die Wartezeit exponentiell auf bis zu 60 Sekunden.
* Legen Sie eine maximale Anzahl von Wiederholungen fest, nach denen Ihre Anwendung den Vorgang als fehlerhaft ansieht.

Wenn bei einer Verbindung mit einer aktiven Transaktion ein Fehler auftritt, ist die ordnungsgemäße Wiederherstellung schwieriger. Es gibt zwei Fälle: Wenn die Transaktion schreibgeschützt war, können Sie die Verbindung ohne Gefahr erneut herstellen und die Transaktion wiederholen. Wenn die Transaktion aber auch in die Datenbank geschrieben hat, müssen Sie ermitteln, ob dafür ein Rollback ausgeführt wurde oder ob sie vor dem Auftreten des vorübergehenden Fehlers erfolgreich war. In diesem Fall haben Sie vielleicht einfach nur keine Bestätigung für den Commit vom Datenbankserver empfangen.

Sie können dazu z.B. eine eindeutige ID auf dem Client generieren, die für alle Wiederholungen verwendet wird. Übergeben Sie diese eindeutige ID als Teil der Transaktion an den Server, und speichern Sie sie in einer Spalte mit einer unique-Einschränkung. Auf diese Weise können Sie die Transaktion ohne Risiko erneut ausführen. Sie wird gelingen, wenn für die vorherige Transaktion ein Rollback ausgeführt wurde und die vom Client generierte eindeutige ID im System noch nicht vorhanden ist. Sie führt zu einem Fehler, wenn ein doppelter Schlüssel vorhanden ist. Dies ist der Fall, wenn die eindeutige ID zuvor gespeichert wurde, da die vorherige Transaktion erfolgreich abgeschlossen wurde.

Wenn Ihr Programm über Middleware eines Drittanbieters mit Azure Database for MySQL kommuniziert, erkundigen Sie sich beim Anbieter, ob die Middleware Wiederholungslogik für vorübergehende Fehler enthält.

Testen Sie die Wiederholungslogik unbedingt vorab. Versuchen Sie beispielsweise, Ihren Code auszuführen, während Sie die Computeressourcen Ihrer Azure Database for MySQL-Instanz zentral hoch- oder herunterskalieren. Die Anwendung sollte die kurze Ausfallzeit, die während dieses Vorgangs auftritt, ohne Probleme behandeln.

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>Herstellen einer effizienten Verbindung mit Azure Database for MySQL

Weil es sich bei Datenbankverbindungen um eine eingeschränkte Ressource handelt, wird die Leistung durch effektive Nutzung des Verbindungspoolings für den Zugriff auf Azure Database for MySQL optimiert. Im folgenden Abschnitt wird erläutert, wie mithilfe von Verbindungspooling oder permanenten Verbindungen auf Azure Database for MySQL effektiver zugegriffen werden kann.

## <a name="access-databases-by-using-connection-pooling-recommended"></a>Zugreifen auf Datenbanken mithilfe von Verbindungspooling (empfohlen)

Das Verwalten von Datenbankverbindungen kann sich auf die Leistung der gesamten Anwendung erheblich auswirken. Wenn Sie die Leistung Ihrer Anwendung optimieren möchten, sollte Ihr Ziel darin bestehen, die Häufigkeit, mit der Verbindungen hergestellt werden, und die Zeit für das Herstellen von Verbindungen in wichtigen Codepfaden zu verringern. Es wird dringend empfohlen, das Datenbankverbindungs-Pooling oder permanente Verbindungen zum Herstellen einer Verbindung mit Azure Database for MySQL zu verwenden. Das Datenbankverbindungs-Pooling sorgt für die Erstellung, Verwaltung und Zuordnung von Datenbankverbindungen. Wenn ein Programm eine Datenbankverbindung anfordert, priorisiert es die Zuordnung vorhandener Datenbankverbindungen im Leerlauf, statt eine neue Verbindung zu erstellen. Nachdem das Programm die Datenbankverbindung verwendet hat, wird sie in Vorbereitung auf weitere Verwendung wiederhergestellt und nicht einfach geschlossen.

Zur besseren Veranschaulichung enthält dieser Artikel [einen Teil des Beispielcodes](./sample-scripts-java-connection-pooling.md), in dem JAVA als Beispiel verwendet wird. Weitere Informationen finden Sie in unter [Apache common DBCP](https://commons.apache.org/proper/commons-dbcp/).

> [!NOTE]
> Der Server konfiguriert einen Zeitüberschreitungsmechanismus zum Schließen einer Verbindung, die seit einiger Zeit im Leerlauf war, um so Ressourcen freizugeben. Richten Sie unbedingt das Überprüfungssystem ein, um die Effektivität von permanenten Verbindungen bei deren Verwendung sicherzustellen. Weitere Informationen finden Sie unter [Configure verification systems on the client side to ensure the effectiveness of persistent connections](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections) (Konfigurieren von Überprüfungssystemen auf der Clientseite, um die Effektivität von permanenten Verbindungen sicherzustellen).

## <a name="access-databases-by-using-persistent-connections-recommended"></a>Zugreifen auf Datenbanken mithilfe von permanenten Verbindungen (empfohlen)

Das Konzept von permanenten Verbindungen ist ähnlich wie das vom Verbindungspooling. Zum Ersetzen kurzer Verbindungen durch permanente Verbindungen sind nur geringfügige Änderungen am Code erforderlich, doch dies wirkt sich auf die Leistungsverbesserung in vielen typischen Anwendungsszenarien erheblich aus.

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>Zugreifen auf Datenbanken mithilfe des Warte- und Wiederholungsmechanismus (wait and retry) mit kurzen Verbindungen

Wenn es Ressourceneinschränkungen gibt, wird dringend empfohlen, dass Sie auf Datenbanken mithilfe von Datenbankpooling oder permanenten Verbindungen zugreifen. Wenn Ihre Anwendung kurze Verbindungen verwendet und fast vor dem Erreichen der Obergrenze für die Anzahl gleichzeitiger Verbindungen Verbindungsfehler auftreten, können Sie den Warte- und Wiederholungsmechanismus ausprobieren. Nach dem ersten Versuch können Sie eine entsprechende (kürzere) Wartezeit festlegen. Anschließend können Sie versuchen, mehrmals auf Ereignisse zu warten.

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>Konfigurieren von Überprüfungsmechanismen in Clients zur Bestätigung der Effektivität permanenter Verbindungen

Der Server konfiguriert einen Zeitüberschreitungsmechanismus zum Schließen einer Verbindung, die seit einiger Zeit im Leerlauf war, um so Ressourcen freizugeben. Wenn der Client erneut auf die Datenbank zugreift, entspricht dies dem Erstellen einer neuen Verbindungsanforderung zwischen Client und Server. Um die Effektivität von Verbindungen während deren Verwendung sicherzustellen, konfigurieren Sie einen Überprüfungsmechanismus auf dem Client. Wie im folgenden Beispiel gezeigt wird, können Sie diesen Überprüfungsmechanismus mithilfe des Tomcat JDBC-Verbindungspoolings konfigurieren.

Wenn Sie den Parameter „TestOnBorrow“ festlegen und es eine neue Anforderung gibt, überprüft der Verbindungspool automatisch die Effektivität aller verfügbaren Verbindungen im Leerlauf. Wenn eine solche Verbindung effektiv ist, wird sie von deren direkt zurückgegebenem anderweitigem Verbindungspool zurückgezogen. Danach erstellt der Verbindungspool eine neue effektive Verbindung und gibt sie zurück. Auf diese Weise wird ein effektiver Zugriff auf die Datenbank sichergestellt. 

Informationen zu den spezifischen Einstellungen finden Sie unter [JDBC connection pool official introduction document](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes) (JDBC-Verbindungspool – Offizielles Einführungsdokument). Sie müssen hauptsächlich die folgenden drei Parameter festlegen: „TestOnBorrow“ (auf „true“ festlegen), „ValidationQuery“ (auf „SELECT 1“ festlegen) und „ValidationQueryTimeout“ (auf „1“ festlegen). Der spezifische Beispielcode wird unten gezeigt:

```java
public class SimpleTestOnBorrowExample {
      public static void main(String[] args) throws Exception {
          PoolProperties p = new PoolProperties();
          p.setUrl("jdbc:mysql://localhost:3306/mysql");
          p.setDriverClassName("com.mysql.jdbc.Driver");
          p.setUsername("root");
          p.setPassword("password");
            // The indication of whether objects will be validated by the idle object evictor (if any). 
            // If an object fails to validate, it will be dropped from the pool. 
            // NOTE - for a true value to have any effect, the validationQuery or validatorClassName parameter must be set to a non-null string. 
          p.setTestOnBorrow(true); 

            // The SQL query that will be used to validate connections from this pool before returning them to the caller.
            // If specified, this query does not have to return any data, it just can't throw a SQLException.
          p.setValidationQuery("SELECT 1");

            // The timeout in seconds before a connection validation queries fail. 
            // This works by calling java.sql.Statement.setQueryTimeout(seconds) on the statement that executes the validationQuery. 
            // The pool itself doesn't timeout the query, it is still up to the JDBC driver to enforce query timeouts. 
            // A value less than or equal to zero will disable this feature.
          p.setValidationQueryTimeout(1);
            // set other useful pool properties.
          DataSource datasource = new DataSource();
          datasource.setPoolProperties(p);

          Connection con = null;
          try {
            con = datasource.getConnection();
            // execute your query here
          } finally {
            if (con!=null) try {con.close();}catch (Exception ignore) {}
          }
      }
  }
```

## <a name="next-steps"></a>Nächste Schritte

* [Beheben von Verbindungsproblemen mit Azure Database for MySQL](howto-troubleshoot-common-connection-issues.md)
