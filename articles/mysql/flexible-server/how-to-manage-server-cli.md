---
title: Verwalten eines Servers – Azure CLI – Azure Database for MySQL Flexible Server
description: Erfahren Sie, wie Sie Azure Database for MySQL Flexible Server über die Azure CLI verwalten.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: b58a9dd7901f85c59b09bc4ccb197d012bce2200
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545054"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-the-azure-cli"></a>Verwalten einer Azure Database for MySQL – Flexible Server-Instanz (Vorschau) mithilfe der Azure CLI

> [!IMPORTANT]
> Azure Database for MySQL Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

In diesem Artikel erfahren Sie, wie Sie Ihre in Azure bereitgestellten Flexible Server-Instanzen (Vorschau) verwalten. Zu den Verwaltungsaufgaben gehören die Compute- und Speicherskalierung, das Zurücksetzen des Administratorkennworts und das Anzeigen von Serverdetails.

## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. Für den Artikel müssen Sie mindestens Version 2.0 der Azure-Befehlszeilenschnittstelle lokal ausführen. Führen Sie den Befehl `az --version` aus, um die installierte Version anzuzeigen. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

Sie müssen sich mithilfe des Befehls [az login](/cli/azure/reference-index#az-login) bei Ihrem Konto anmelden. Beachten Sie die Eigenschaft **id** , die auf die **Abonnement-ID** für Ihr Azure-Konto verweist.

```azurecli-interactive
az login
```

Wählen Sie mithilfe des Befehls [az account set](/cli/azure/account) das Abonnement unter Ihrem Konto aus. Notieren Sie sich aus der Ausgabe von **az login** den Wert für **id** . Sie verwenden ihn im Befehl als Wert für das Argument **subscription** . Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem die Ressource fakturiert sein sollte. Verwenden Sie [az account list](/cli/azure/account#az-account-list), um alle Abonnements abzurufen.

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Wenn Sie noch keinen flexiblen Server erstellt haben, erstellen Sie nun einen, um mit dieser exemplarischen Vorgehensweise zu beginnen.

## <a name="scale-compute-and-storage"></a>Skalieren von Compute und Speicher

Mithilfe des folgenden Befehls können Sie Ihren Computetarif, Ihre virtuelle Kerne und Ihren Speicher mühelos hochskalieren. Unter [az mysql flexible-server update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update) können Sie den gesamten Servervorgang anzeigen, den Sie durchführen können.

```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v4 --storage-size 6144
```

Hier sind die Details zu den obigen Argumenten aufgeführt:

**Einstellung** | **Beispielwert** | **Beschreibung**
---|---|---
name | mydemoserver | Geben Sie einen eindeutigen Namen für Ihren Azure Database for MySQL-Server ein. Der Servername darf nur Kleinbuchstaben, Zahlen und den Bindestrich (-) enthalten. Es muss zwischen drei und 63 Zeichen lang sein.
resource-group | myresourcegroup | Geben Sie den Namen der Azure-Ressourcengruppe an.
sku-name|Standard_D4ds_v4|Geben Sie den Namen des Computetarifs und der Computegröße ein. Unterliegt der Konvention Standard_{VM size} (Kurzform) Weitere Informationen finden Sie unter [Azure Database for MySQL – Tarife](../concepts-pricing-tiers.md).
storage-size | 6.144 | Die Speicherkapazität des Servers (Einheit: MB). Mindestens 5.120 und erhöht sich in Inkrementen von 1.024.

> [!Important]
> - Der Speicher kann hochskaliert werden (ein Herunterskalieren ist jedoch nicht möglich).


## <a name="manage-mysql-databases-on-a-server"></a>Verwalten von MySQL-Datenbanken auf einem Server
Sie können die Eigenschaften einer Datenbank auf Ihrem Server mit einem der folgenden Befehle erstellen, löschen, auflisten und anzeigen.

| Cmdlet | Verwendung| BESCHREIBUNG |
| --- | ---| --- |
|[az mysql flexible-server db create](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_create)|```az mysql flexible-server db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Erstellt eine Datenbank|
|[az mysql flexible-server db delete](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_delete)|```az mysql flexible-server db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Löscht eine Datenbank von Ihrem Server. Mit diesem Befehl wird der Server nicht gelöscht. |
|[az mysql flexible-server db list](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_list)|```az mysql flexible-server db list -g myresourcegroup -s mydemoserver```|Listet alle Datenbanken auf dem Server auf.|
|[az mysql flexible-server db show](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_show)|```az mysql flexible-server db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Zeigt weitere Details der Datenbank an.|

## <a name="update-admin-password"></a>Aktualisieren des Administratorkennworts
Mit diesem Befehl können Sie das Kennwort für die Administratorrolle ändern.
```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Stellen Sie sicher, dass das Kennwort aus mindestens 8 Zeichen und maximal 128 Zeichen besteht.
> Das Kennwort muss Zeichen aus drei der folgenden Kategorien enthalten: Englische Großbuchstaben, englische Kleinbuchstaben, Zahlen und nicht alphanumerische Zeichen.

## <a name="delete-a-server"></a>Löschen eines Servers
Wenn Sie nur den flexiblen MySQL Flexible Server löschen möchten, können Sie den Befehl [az mysql flexible-server server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete) ausführen.

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Nächste Schritte
- [Erfahren Sie mehr über das Starten oder Anhalten eines Servers](how-to-stop-start-server-portal.md)
- [Erfahren Sie mehr über das Verwalten von virtuellen Netzwerken](how-to-manage-virtual-network-cli.md)
- [Beheben von Verbindungsproblemen](how-to-troubleshoot-common-connection-issues.md)
- [Erstellen und Verwalten einer Firewall](how-to-manage-firewall-cli.md)