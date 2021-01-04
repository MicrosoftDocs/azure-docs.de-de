---
title: Verbinden mit Azure App Service – Azure Database for MySQL
description: Anweisungen zum ordnungsgemäßen Verbinden einer vorhandenen Azure App Service-Instanz mit Azure-Datenbank für MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 6355afe6ce5decbed029db4536b1b1b19f5a876c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541503"
---
# <a name="connect-an-existing-azure-app-service-to-azure-database-for-mysql-server"></a>Verbinden vorhandener Azure App Service-Instanzen mit Azure-Datenbank für MySQL
Dieses Thema erläutert das Verbinden vorhandener Azure App Service-Instanzen mit Ihrem Azure Database for MySQL-Server.

## <a name="before-you-begin"></a>Voraussetzungen
Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Erstellen eines Azure-Datenbank für MySQL-Servers Weitere Informationen finden Sie unter [Erstellen von Azure-Datenbank für MySQL-Server im Portal](quickstart-create-mysql-server-database-using-azure-portal.md) oder [Erstellen von Azure-Datenbank für MySQL-Server mithilfe der Befehlszeilenschnittstelle](quickstart-create-mysql-server-database-using-azure-cli.md).

Es gibt derzeit zwei Lösungen zum Aktivieren des Zugriffs von Azure App Service auf Azure-Datenbank für MySQL. Beide Lösungen umfassen das Einrichten von Firewallregeln auf Serverebene.

## <a name="solution-1---allow-azure-services"></a>Lösung 1: Zulassen von Azure-Diensten
Azure-Datenbank für MySQL bietet Zugriffssicherheit über eine Firewall zum Schutz Ihrer Daten. Beim Herstellen einer Verbindung von Azure App Service mit Azure-Datenbank für MySQL-Server sollten Sie bedenken, dass die ausgehenden IP-Adressen von App Service dynamisch sind. Durch Auswahl der Option „Zugriff auf Azure-Dienste erlauben“ wird zugelassen, dass App Service eine Verbindung mit MySQL Server herstellt.

1. Klicken Sie auf dem Blatt des MySQL-Servers unter der Überschrift „Einstellungen“ auf **Verbindungssicherheit**, um das Blatt „Verbindungssicherheit“ für Azure Database for MySQL zu öffnen.

   :::image type="content" source="./media/howto-connect-webapp/1-connection-security.png" alt-text="Azure-Portal – Klicken auf „Verbindungssicherheit“":::

2. Wählen Sie für **Zugriff auf Azure-Dienste erlauben** **ON** (EIN) aus, und klicken Sie dann auf **Speichern**.
   :::image type="content" source="./media/howto-connect-webapp/allow-azure.png" alt-text="Azure-Portal: Zugriff auf Azure-Dienste erlauben":::

## <a name="solution-2---create-a-firewall-rule-to-explicitly-allow-outbound-ips"></a>Lösung 2: Erstellen einer Firewallregel zum expliziten Zulassen ausgehender IP-Adressen
Sie können alle ausgehenden IP-Adressen von Azure App Service explizit hinzufügen.

1. Sehen Sie sich auf dem Blatt „App Service-Eigenschaften“ den Eintrag in **Ausgehende IP-Adressen** an.

   :::image type="content" source="./media/howto-connect-webapp/2_1-outbound-ip-address.png" alt-text="Azure-Portal – ausgehende IP-Adressen anzeigen":::

2. Fügen Sie auf dem MySQL-Blatt „Verbindungssicherheit“ alle ausgehenden IP-Adressen einzeln hinzu.

   :::image type="content" source="./media/howto-connect-webapp/2_2-add-explicit-ips.png" alt-text="Azure-Portal – IP-Adressen einzeln hinzufügen":::

3. Vergessen Sie nicht, Ihre Firewallregeln zu **speichern**.

Obwohl Azure App Service versucht, IP-Adressen im Lauf der Zeit konstant zu halten, gibt es Fälle, in denen sich IP-Adressen ändern können. Dazu gehört beispielsweise, wenn die App wiederverwendet oder ein Skalierungsvorgang durchgeführt wird oder wenn regionalen Azure-Rechenzentren zum Erhöhen der Kapazität neue Computer hinzugefügt werden. Wenn die IP-Adressen geändert werden und dadurch keine Verbindung mit dem MySQL-Server hergestellt werden kann, können bei der App Ausfallzeiten auftreten. Berücksichtigen Sie diese Überlegungen, wenn Sie sich für eine der Lösungen oben entscheiden.

## <a name="ssl-configuration"></a>SSL-Konfiguration
Bei Azure Database for MySQL ist SSL standardmäßig aktiviert. Wenn Ihre Anwendung für Verbindungen mit der Datenbank nicht SSL verwendet, müssen Sie SSL auf dem MySQL-Server deaktivieren. Weitere Informationen zum Konfigurieren von SSL finden Sie unter [Verwenden von SSL mit Azure Database for MySQL](howto-configure-ssl.md).

### <a name="django-pymysql"></a>Django (PyMySQL)
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'quickstartdb',
        'USER': 'myadmin@mydemoserver',
        'PASSWORD': 'yourpassword',
        'HOST': 'mydemoserver.mysql.database.azure.com',
        'PORT': '3306',
        'OPTIONS': {
            'ssl': {'ssl-ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}
        }
    }
}
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Verbindungszeichenfolgen finden Sie unter [Verbindungszeichenfolgen](howto-connection-string.md).
