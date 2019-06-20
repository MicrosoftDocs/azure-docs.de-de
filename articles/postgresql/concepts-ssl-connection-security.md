---
title: Konfigurieren von SSL-Verbindungen in Azure Database for PostgreSQL – Einzelserver
description: Hier finden Sie Anweisungen und Informationen zum Konfigurieren von Azure Database for PostgreSQL (Einzelserver) und zugehörigen Anwendungen für die ordnungsgemäße Verwendung von SSL-Verbindungen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 56611267872ca79d7d2fe3a08c9b9f49a9b1840b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067405"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Konfigurieren von SSL-Verbindungen in Azure Database for PostgreSQL – Einzelserver
Azure-Datenbank für PostgreSQL stellt Verbindungen zwischen Clientanwendungen und dem PostgreSQL-Dienst hauptsächlich mittels Secure Sockets Layer (SSL) her. Das Erzwingen von SSL-Verbindungen zwischen dem Datenbankserver und Clientanwendungen trägt zum Schutz vor Man-in-the-Middle-Angriffen bei, indem der Datenstrom zwischen dem Server und der Anwendung verschlüsselt wird.

Standardmäßig ist der PostgreSQL-Datenbankdienst so konfiguriert, dass SSL-Verbindungen erforderlich sind. Wenn Ihre Clientanwendung keine SSL-Konnektivität unterstützt, können Sie optional die Erzwingung von SSL zum Herstellen einer Verbindung mit dem Datenbankdienst deaktivieren. 

## <a name="enforcing-ssl-connections"></a>Erzwingen von SSL-Verbindungen
Bei allen Azure-Datenbank für PostgreSQL-Servern, die über das Azure-Portal und die CLI bereitgestellt werden, ist die Erzwingung von SSL-Verbindungen standardmäßig aktiviert. 

Entsprechend enthalten Verbindungszeichenfolgen, die im Azure-Portal unter dem Server in den Verbindungszeichenfolgen-Einstellungen vorab definiert sind, die erforderlichen Parameter für gängige Sprachen für die Verbindung mit dem Datenbankserver mithilfe von SSL. Der SSL-Parameter variiert je nach Connector, z.B. „ssl=true“ oder „sslmode=require“ oder „sslmode=required“ und weitere Variationen.

## <a name="configure-enforcement-of-ssl"></a>Konfigurieren der Erzwingung von SSL
Sie können die Erzwingung von SSL-Verbindungen optional deaktivieren. Microsoft Azure empfiehlt, die Einstellung **SSL-Verbindung erzwingen** immer zu aktivieren, um die Sicherheit zu erhöhen.

### <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals
Rufen Sie Ihren Azure-Datenbank für PostgreSQL-Server auf, und klicken Sie auf **Verbindungssicherheit**. Verwenden Sie die Umschaltfläche, um die Einstellung **SSL-Verbindung erzwingen** zu aktivieren/deaktivieren. Klicken Sie dann auf **Speichern**. 

![Verbindungssicherheit – Erzwingung von SSL deaktivieren](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Sie können die Einstellung auf der Seite **Übersicht** bestätigen, indem Sie den Indikator für den **SSL-Erzwingungsstatus** anzeigen.

### <a name="using-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle
Sie können den Parameter **ssl-enforcement** in der Azure CLI mit den Werten `Enabled` bzw. `Disabled` aktivieren oder deaktivieren.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Sicherstellen, dass die Anwendung oder das Framework SSL-Verbindungen unterstützt
Viele gängige Anwendungsframeworks, die PostgreSQL für ihre Datenbankdienste verwenden, z. B. Drupal und Django, aktivieren SSL nicht standardmäßig während der Installation. SSL-Verbindungen müssen nach der Installation oder über bestimmte CLI-Befehle die Anwendung aktiviert werden. Wenn Ihr PostgreSQL-Server SSL-Verbindungen erzwingt und die zugehörige Anwendung nicht ordnungsgemäß konfiguriert wurde, kann die Anwendung möglicherweise keine Verbindung mit dem Datenbankserver herstellen. Lesen Sie in der Dokumentation Ihrer Anwendung nach, wie SSL-Verbindungen aktiviert werden.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Anwendungen, die eine Zertifikatüberprüfung für SSL-Verbindungen erfordern
In einigen Fällen erfordern Anwendungen eine lokale Zertifikatdatei, die aus der Zertifikatdatei (CER) einer vertrauenswürdigen Zertifizierungsstelle (Certificate Authority, CA) generiert wurde, um eine sichere Verbindung herzustellen. Führen Sie die folgenden Schritte aus, um die CER-Datei abzurufen, das Zertifikat zu decodieren und sie an Ihre Anwendung zu binden.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>Herunterladen der Zertifikatdatei von der Zertifizierungsstelle (CA) 
Das erforderliche Zertifikat für die Kommunikation mit Ihrem Azure-Datenbank für PostgreSQL-Server über SSL befindet sich [hier](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt). Laden Sie die Zertifikatdatei lokal herunter.

### <a name="install-a-cert-decoder-on-your-machine"></a>Installieren eines Zertifikatdecoders auf Ihrem Computer 
Zum Decodieren der Zertifikatdatei, die Ihre Anwendung für die sichere Verbindung mit dem Datenbankserver benötigt, können Sie [OpenSSL](https://github.com/openssl/openssl) verwenden. Wie Sie OpenSSL installieren, können Sie den [Anweisungen zur Installation von OpenSSL](https://github.com/openssl/openssl/blob/master/INSTALL) entnehmen. 


### <a name="decode-your-certificate-file"></a>Decodieren der Zertifikatdatei
Die heruntergeladene Stamm-CA-Datei weist ein verschlüsseltes Format auf. Verwenden Sie OpenSSL, um die Zertifikatdatei zu decodieren. Führen Sie hierzu folgenden OpenSSL-Befehl aus:

```
openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>Herstellen einer Verbindung mit Azure-Datenbank für PostgreSQL mit SSL-Zertifikatauthentifizierung
Jetzt haben Sie Ihr Zertifikat erfolgreich decodiert und können über SSL eine sichere Verbindung mit Ihrem Datenbankserver herstellen. Um eine Überprüfung des Serverzertifikats zu ermöglichen, muss das Zertifikat in die Datei „~/.postgresql/root.crt“ im Basisverzeichnis des Benutzers eingefügt werden. (Bei Microsoft Windows heißt die Datei „%APPDATA%\postgresql\root.crt“.) 

#### <a name="connect-using-psql"></a>Herstellen einer Verbindung mit psql
Das folgende Beispiel zeigt, wie Sie einen PostgreSQL-Server mithilfe des psql-Befehlszeilen-Hilfsprogramms erfolgreich verbinden können. Verwenden Sie die erstellte `root.crt`-Datei und die Option `sslmode=verify-ca` oder `sslmode=verify-full`.

Führen Sie über die PostgreSQL-Befehlszeilenschnittstelle den folgenden Befehl aus:
```bash
psql "sslmode=verify-ca sslrootcert=root.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=mylogin@mydemoserver"
```
Daraufhin sollten Sie folgende Ausgabe erhalten:
```bash
Password for user mylogin@mydemoserver:
psql (9.6.2)
WARNING: Console code page (437) differs from Windows code page (1252)
     8-bit characters might not work correctly. See psql reference
     page "Notes for Windows users" for details.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=>
```

## <a name="next-steps"></a>Nächste Schritte
Überprüfen verschiedener Anwendungskonnektivitätsoptionen gemäß [Datenverbindungsbibliotheken für Azure Database for PostgreSQL](concepts-connection-libraries.md).
