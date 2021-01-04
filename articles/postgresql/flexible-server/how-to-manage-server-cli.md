---
title: Verwalten von Servern für Azure Database for PostgreSQL – Flexible Server mithilfe der Azure CLI
description: In diesem Artikel erfahren Sie, wie Sie Azure Database for PostgreSQL – Flexible Server über die Azure CLI verwalten.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 278f8f816909a7e365d7e45d04c5169950e79a65
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493677"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-by-using-the-azure-cli"></a>Verwalten von Azure Database for PostgreSQL – flexibler Server mithilfe der Azure CLI

> [!IMPORTANT]
> Azure Database for PostgreSQL – flexibler Server befindet sich in der Vorschau.

In diesem Artikel erfahren Sie, wie Sie Ihren in Azure bereitgestellten flexiblen Server verwalten. Zu den Verwaltungsaufgaben gehören die Compute- und Speicherskalierung, das Zurücksetzen des Administratorkennworts und das Anzeigen von Serverdetails.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. 

Sie müssen Version 2.0 oder höher der Azure CLI ausführen. Führen Sie den Befehl `az --version` aus, um die installierte Version anzuzeigen. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](/cli/azure/install-azure-cli).

Melden Sie sich mithilfe des Befehls [az login](/cli/azure/reference-index#az-login) bei Ihrem Konto an. 

```azurecli-interactive
az login
```

Wählen Sie mit dem Befehl [az account set](/cli/azure/account) Ihr Abonnement aus. Notieren Sie sich aus der Ausgabe von **az login** den Wert für **id**. Diesen verwenden Sie im folgenden Befehl als Wert für das Argument **subscription**. Wenn Sie über mehrere Abonnements verfügen, wählen Sie das Abonnement aus, in dem die Ressource fakturiert werden soll. Führen Sie den Befehl [az account list](/cli/azure/account#az-account-list) aus, um eine Liste Ihrer sämtlichen Abonnements zu erhalten.

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Wenn Sie noch keinen flexiblen Server erstellt haben, müssen Sie dies jetzt tun, um diese Anleitung weiter befolgen zu können.

## <a name="scale-compute-and-storage"></a>Skalieren von Compute und Speicher

Mit dem folgenden Befehl können Sie Ihren Computetarif, Ihre virtuelle Kerne und Ihren Speicher ganz einfach hochskalieren. Eine Liste aller ausführbaren Servervorgänge finden Sie in der Übersicht zu [az postgres flexible-server](/cli/azure/postgres/flexible-server).

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Im Folgenden sehen Sie die Details zu den Argumenten im vorangehenden Code:

**Einstellung** | **Beispielwert** | **Beschreibung**
---|---|---
name | mydemoserver | Geben Sie einen eindeutigen Namen für Ihren Server ein. Der Servername darf nur Kleinbuchstaben, Zahlen und den Bindestrich (-) enthalten. Er muss zwischen 3 und 63 Zeichen aufweisen.
resource-group | myresourcegroup | Geben Sie den Namen der Azure-Ressourcengruppe an.
sku-name|Standard_D4ds_v3|Geben Sie den Namen des Computetarifs und der Computegröße ein. Der Wert folgt der Konvention *Standard_{VM size}* in Kurzform. Weitere Informationen finden Sie unter [Azure Database for MySQL – Tarife](../concepts-pricing-tiers.md).
storage-size | 6.144 | Geben Sie die Speicherkapazität des Servers in Megabytes ein. Der Minimalwert beträgt 5120 MB und kann in 1024-MB-Schritten erhöht werden.

> [!IMPORTANT]
> Der Speicher kann nicht herunterskaliert werden. 

## <a name="manage-postgresql-databases-on-a-server"></a>Verwalten von PostgreSQL-Datenbanken auf einem Server

Es gibt verschiedene Anwendungen, mit denen Sie eine Verbindung mit Ihrem Azure-Datenbank für PostgreSQL-Server herstellen können. Wenn auf Ihrem Clientcomputer PostgreSQL installiert ist, können Sie eine lokale Instanz von [psql](https://www.postgresql.org/docs/current/static/app-psql.html) verwenden. Stellen Sie jetzt mit dem Befehlszeilentool „psql“ eine Verbindung mit dem Azure Database for PostgreSQL-Server her.

1. Führen Sie den folgenden **psql**-Befehl aus:

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Der folgende Befehl beispielsweise verwendet Ihre Zugriffsanmeldeinformationen und stellt eine Verbindung mit der Standarddatenbank **postgres** auf Ihrem PostgreSQL-Server **mydemoserver.postgres.database.azure.com** her. Wenn Sie dazu aufgefordert werden, geben Sie das von Ihnen ausgewählte `<server_admin_password>` ein.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Nachdem die Verbindung hergestellt wurde, zeigt das psql-Tool eine **postgres**-Eingabeaufforderung an, an der Sie SQL-Befehle eingeben können. In der Ausgabe der Erstverbindung wird eine Warnung angezeigt, wenn die verwendete psql-Version von der Version auf dem Azure Database for PostgreSQL-Server abweicht.

   psql-Beispielausgabe:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Wenn die Firewall nicht zum Zulassen der IP-Adresse Ihres Clients konfiguriert ist, tritt der folgende Fehler auf:
   >
   > "psql: FATAL:  no pg_hba.conf entry for host `<IP address>`, user "myadmin", database "postgres", SSL on FATAL: SSL connection is required. Specify SSL options and retry."
   >
   > Vergewissern Sie sich, dass die IP-Adresse Ihres Clients in den Firewallregeln zulässig ist.

2. Erstellen Sie eine leere Datenbank namens **postgresdb**, indem Sie an der Eingabeaufforderung folgenden Befehl eingeben:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. Führen Sie an der Eingabeaufforderung den folgenden Befehl aus, um zur Verbindung mit der neu erstellten Datenbank **postgresdb** zu wechseln:

    ```bash
    \c postgresdb
    ```

4. Geben Sie `\q` ein, und drücken Sie dann die EINGABETASTE, um „psql“ zu beenden.

In diesem Abschnitt haben Sie über „psql“ eine Verbindung mit dem Azure Database for PostgreSQL-Server hergestellt und eine leere Benutzerdatenbank erstellt.

## <a name="reset-the-admin-password"></a>Zurücksetzen des Administratorkennworts

Mit dem folgenden Befehl können Sie das Kennwort für die Administratorrolle ändern:

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Wählen Sie ein Kennwort aus, das mindestens 8 Zeichen und höchstens 128 Zeichen lang ist. Das Kennwort muss Zeichen aus drei der folgenden Kategorien enthalten: 
> - Englische Großbuchstaben
> - Englische Kleinbuchstaben
> - Zahlen
> - Nicht alphanumerische Zeichen

## <a name="delete-a-server"></a>Löschen eines Servers

Um Azure Database for PostgreSQL – flexibler Server zu löschen, führen Sie den Befehl [az postgres flexible-server delete](/cli/azure/postgres/flexible-server#az-PostgreSQL-flexible-server-delete) aus.

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nächste Schritte

- [Grundlegendes zu Sicherungs- und Wiederherstellungskonzepten](concepts-backup-restore.md)
- [Optimieren und Überwachen des Servers](concepts-monitoring.md)