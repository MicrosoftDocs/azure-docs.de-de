---
title: Konfigurieren von SSL – Azure Database for MariaDB
description: Anweisungen zum ordnungsgemäßen Konfigurieren der Azure Database for MariaDB und zugehörigen Anwendungen für die richtige Verwendung von SSL-Verbindungen
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 07/08/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 8f541e2302bc1f1de132de76e0638f9843d7003f
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592480"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mariadb"></a>Konfigurieren von SSL-Verbindungen in der Anwendung für eine sichere Verbindung mit Azure Database for MariaDB
Azure Database for MariaDB unterstützt die Verbindung Ihres Servers mit Azure Database for MariaDB mit Clientanwendungen, die Secure Sockets Layer (SSL) verwenden. Das Erzwingen von SSL-Verbindungen zwischen dem Datenbankserver und Clientanwendungen trägt zum Schutz vor Man-in-the-Middle-Angriffen bei, indem der Datenstrom zwischen dem Server und der Anwendung verschlüsselt wird.

## <a name="obtain-ssl-certificate"></a>Abrufen eines SSL-Zertifikats


Laden Sie das Zertifikat, das für die SSL-Kommunikation mit Ihrem Azure Database for MariaDB-Server erforderlich ist, von [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) herunter, und speichern Sie die Zertifikatsdatei auf dem lokalen Laufwerk. (In diesem Tutorial wird als Beispiel „c:\ssl“ verwendet.)
**Für Microsoft Internet Explorer und Microsoft Edge**: Benennen Sie nach dem Download das Zertifikat in „BaltimoreCyberTrustRoot.crt.pem“ um.

>[!NOTE]
> Basierend auf dem Feedback von Kunden haben wir die eingestellte Unterstützung des Stammzertifikats für unsere vorhandene Baltimore-Stammzertifizierungsstelle bis zum 15. Februar 2021 (15.02.2021) verlängert.

> [!IMPORTANT] 
> Das SSL-Stammzertifikat wird ab dem 15. Februar 2021 (15.02.2021) auslaufen. Aktualisieren Sie Ihre Anwendung bitte mithilfe des [neuen Zertifikats](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem). Weitere Informationen finden Sie unter [Geplante Zertifikatupdates](concepts-certificate-rotation.md)

Zertifikate für Server in Sovereign Clouds finden Sie unter den folgenden Links: [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure China](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) und [Azure Deutschland](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

## <a name="bind-ssl"></a>Binden von SSL

### <a name="connecting-to-server-using-mysql-workbench-over-ssl"></a>Herstellen einer Verbindung mit dem Server mithilfe von MySQL Workbench über SSL
Konfigurieren Sie MySQL Workbench, um eine sichere Verbindung über SSL herzustellen. 

1. Navigieren Sie im Dialogfeld „Setup New Connection“ (Neue Verbindung einrichten) zur Registerkarte **SSL**. 

1. Aktualisieren Sie das Feld **Use SSL** (SSL verwenden) auf „Require“ (Anfordern).

1. Geben Sie in das Feld für die **SSL-CA-Datei** den Speicherort der Datei **BaltimoreCyberTrustRoot.crt.pem** ein. 
    
    ![SSL-Konfiguration speichern](./media/howto-configure-ssl/mysql-workbench-ssl.png)

Für vorhandene Verbindungen können Sie SSL binden, indem Sie mit der rechten Maustaste auf das Verbindungssymbol klicken und dann „Bearbeiten“ auswählen. Navigieren Sie dann zur Registerkarte **SSL** und binden die Zertifikatsdatei.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Herstellen einer Verbindung mit dem Server mithilfe der MySQL-CLI über SSL
Sie können das SSL-Zertifikat auch mithilfe der MySQL-Befehlszeilenschnittstelle und der folgenden Befehle binden: 

```bash
mysql.exe -h mydemoserver.mariadb.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> Wenn Sie die MySQL-Befehlszeilenschnittstelle unter Windows verwenden, erhalten Sie möglicherweise eine Fehlermeldung: `SSL connection error: Certificate signature check failed`. Ersetzen Sie in diesem Fall den Parameter `--ssl-mode=REQUIRED --ssl-ca={filepath}` durch `--ssl`.

## <a name="enforcing-ssl-connections-in-azure"></a>Erzwingen von SSL-Verbindungen in Azure 

### <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals
Rufen Sie über das Azure-Portal Ihren Server mit Azure Database for MariaDB auf, und klicken Sie auf **Verbindungssicherheit**. Verwenden Sie die Umschaltfläche, um die Einstellung **SSL-Verbindung erzwingen** zu aktivieren/deaktivieren, und klicken Sie dann auf **Speichern**. Microsoft empfiehlt, die Einstellung **SSL-Verbindung erzwingen** immer zu aktivieren, um die Sicherheit zu erhöhen.
![„enable-ssl“ für MariaDB-Server](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle
Sie können den **ssl-enforcement**-Parameter in der Azure-Befehlszeilenschnittstelle mit den entsprechenden Werten aktivieren oder deaktivieren.
```azurecli-interactive
az mariadb server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="verify-the-ssl-connection"></a>Überprüfen der SSL-Verbindung
Führen Sie den MySQL-Befehl **status** aus, um sicherzustellen, dass die Verbindung mit dem MariaDB-Server über SSL hergestellt wurde:
```sql
status
```
Um sicherzustellen, dass die Verbindung verschlüsselt ist, überprüfen Sie die Ausgabe, die Folgendes anzeigen sollte:  **SSL: Verwendete Verschlüsselung ist AES256-SHA** 

## <a name="sample-code"></a>Beispielcode
Um in Ihrer Anwendung über SSL eine sichere Verbindung mit Azure Database for MariaDB einzurichten, können Sie die folgenden Codebeispiele verwenden:

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mariadb.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn = mysql.connector.connect(user='myadmin@mydemoserver',
                                   password='yourpassword',
                                   database='quickstartdb',
                                   host='mydemoserver.mariadb.database.azure.com',
                                   ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user='myadmin@mydemoserver',
                       password='yourpassword',
                       database='quickstartdb',
                       host='mydemoserver.mariadb.database.azure.com',
                       ssl={'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'})
```

### <a name="ruby"></a>Ruby
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mariadb.database.azure.com', 
        :username => 'myadmin@mydemoserver',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :sslca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
        :ssl_mode => 'required'
    )
```
#### <a name="ruby-on-rails"></a>Ruby on Rails
```ruby
default: &default
  adapter: mysql2
  username: username@mydemoserver
  password: yourpassword
  host: mydemoserver.mariadb.database.azure.com
  sslca: BaltimoreCyberTrustRoot.crt.pem
  sslverify: true
```

### <a name="golang"></a>Golang
```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mariadb.database.azure.com', 'quickstartdb') 
db, _ := sql.Open("mysql", connectionString)
```
### <a name="java-jdbc"></a>Java (JDBC)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="java-mariadb"></a>Java (MariaDB)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mariadb.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)
```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin@mydemoserver",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    CACertificateFile = "BaltimoreCyberTrustRoot.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

<!-- 
## Next steps
Review various application connectivity options following [Connection libraries for Azure Database for MariaDB](concepts-connection-libraries.md)
-->
