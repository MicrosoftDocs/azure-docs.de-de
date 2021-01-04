---
title: Verwalten von Servern – Azure-Befehlszeilenschnittstelle – Azure Database for MySQL
description: Erfahren Sie, wie Sie Azure Database for MySQL-Server über die Azure CLI verwalten.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: bd068f0cf76a8edefca854d72d5240c0becaf8fc
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542064"
---
# <a name="manage-an-azure-database-for-mysql-single-server-using-the-azure-cli"></a>Verwalten eines Azure Database for MySQL Single Server über die Azure CLI

In diesem Artikel erfahren Sie, wie Sie Ihre in Azure bereitgestellten Einzelserver verwalten. Zu den Verwaltungsaufgaben gehören die Compute- und Speicherskalierung, das Zurücksetzen des Administratorkennworts und das Anzeigen von Serverdetails.

## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. Für den Artikel müssen Sie mindestens Version 2.0 der Azure-Befehlszeilenschnittstelle lokal ausführen. Führen Sie den Befehl `az --version` aus, um die installierte Version anzuzeigen. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

Sie müssen sich mithilfe des Befehls [az login](/cli/azure/reference-index#az-login) bei Ihrem Konto anmelden. Beachten Sie die Eigenschaft **id**, die auf die **Abonnement-ID** für Ihr Azure-Konto verweist.

```azurecli-interactive
az login
```

Wählen Sie mithilfe des Befehls [az account set](/cli/azure/account) das Abonnement unter Ihrem Konto aus. Notieren Sie sich aus der Ausgabe von **az login** den Wert für **id**. Sie verwenden ihn im Befehl als Wert für das Argument **subscription**. Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem die Ressource fakturiert sein sollte. Verwenden Sie [az account list](/cli/azure/account#az-account-list), um alle Abonnements abzurufen.

```azurecli
az account set --subscription <subscription id>
```

Wenn Sie noch keinen Server erstellt haben, lesen Sie diesen [Schnellstart](quickstart-create-mysql-server-database-using-azure-cli.md), um einen erstellen zu können.

## <a name="scale-compute-and-storage"></a>Skalieren von Compute und Speicher
Mit dem folgenden Befehl können Sie Ihren Tarif sowie Compute und Speicher problemlos hochskalieren. Unter [az mysql server overview](/cli/azure/mysql/server) können Sie einen Überblick über den gesamten Servervorgang anzeigen, den Sie durchführen können.

```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

Hier sind die Details zu den obigen Argumenten aufgeführt:

**Einstellung** | **Beispielwert** | **Beschreibung**
---|---|---
name | mydemoserver | Geben Sie einen eindeutigen Namen für Ihren Azure Database for MySQL-Server ein. Der Servername darf nur Kleinbuchstaben, Zahlen und den Bindestrich (-) enthalten. Es muss zwischen drei und 63 Zeichen lang sein.
resource-group | myresourcegroup | Geben Sie den Namen der Azure-Ressourcengruppe an.
sku-name|GP_Gen5_2|Geben Sie den Namen des Tarifs und der Computekonfiguration ein. Folgt der Konvention „{Tarif} _{Computegeneration}_ {virtuelle Kerne}“ in Kurzform. Weitere Informationen finden Sie unter [Azure Database for MySQL – Tarife](./concepts-pricing-tiers.md).
storage-size | 6.144 | Die Speicherkapazität des Servers (Einheit: MB). Mindestens 5.120 und erhöht sich in Inkrementen von 1.024.

> [!Important]
> - Der Speicher kann hochskaliert werden (ein Herunterskalieren ist jedoch nicht möglich).
> - Das Hochskalieren von „Basic“ zum Tarif „Universell“ oder „Arbeitsspeicheroptimiert“ wird nicht unterstützt. Sie können [mithilfe eines Bash-Skripts](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404) oder [mithilfe von MySQL Workbench](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134) manuell hochskalieren.


## <a name="manage-mysql-databases-on-a-server"></a>Verwalten von MySQL-Datenbanken auf einem Server
Sie können die Eigenschaften einer Datenbank auf Ihrem Server mit einem der folgenden Befehle erstellen, löschen, auflisten und anzeigen.

| Cmdlet | Verwendung| BESCHREIBUNG |
| --- | ---| --- |
|[az mysql db create](/cli/azure/sql/db#az-mysql-db-create)|```az mysql db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Erstellt eine Datenbank|
|[az mysql db delete](/cli/azure/sql/db#az-mysql-db-delete)|```az mysql db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Löscht eine Datenbank von Ihrem Server. Mit diesem Befehl wird der Server nicht gelöscht. |
|[az mysql db list](/cli/azure/sql/db#az-mysql-db-list)|```az mysql db list -g myresourcegroup -s mydemoserver```|Listet alle Datenbanken auf dem Server auf.|
|[az mysql db show](/cli/azure/sql/db#az-mysql-db-show)|```az mysql db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Zeigt weitere Details der Datenbank an.|

## <a name="update-admin-password"></a>Aktualisieren des Administratorkennworts
Mit diesem Befehl können Sie das Kennwort für die Administratorrolle ändern.
```azurecli-interactive
az mysql server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Stellen Sie sicher, dass das Kennwort aus mindestens 8 Zeichen und maximal 128 Zeichen besteht.
> Das Kennwort muss Zeichen aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen und nicht alphanumerische Zeichen.

## <a name="delete-a-server"></a>Löschen eines Servers
Wenn Sie den MySQL Single Server löschen möchten, können Sie den Befehl [az mysql server delete](/cli/azure/mysql/server#az-mysql-server-delete) ausführen.

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nächste Schritte
- [Neustarten eines Servers](howto-restart-server-cli.md)
- [Wiederherstellen eines Servers in einem fehlerhaften Zustand](howto-restore-server-cli.md)
- [Überwachen und Optimieren des Servers](concepts-monitoring.md)