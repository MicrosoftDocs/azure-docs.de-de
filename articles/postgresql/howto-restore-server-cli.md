---
title: Sichern und Wiederherstellen eines Servers in Azure Database for PostgreSQL (Einzelserver)
description: Hier erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle einen Server in Azure Database for PostgreSQL (Einzelserver) sichern und wiederherstellen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 85fb00ad221ae982e4d3ddc9d2d5d20dd4f2793d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65069095"
---
# <a name="how-to-back-up-and-restore-a-server-in-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>Sichern und Wiederherstellen eines Servers in Azure Database for PostgreSQL (Einzelserver) mithilfe der Azure-Befehlszeilenschnittstelle

## <a name="backup-happens-automatically"></a>Automatische Sicherung
Azure Database for PostgreSQL-Server werden regelmäßig gesichert, um Wiederherstellungsfunktionen zu ermöglichen. Mithilfe dieses Features können Sie für den Server und alle dazugehörigen Datenbanken einen Zustand zu einem früheren Zeitpunkt auf einem neuen Server wiederherstellen.

## <a name="prerequisites"></a>Voraussetzungen
Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:
- [Einen Server und eine Datenbank für Azure-Datenbank für PostgreSQL](quickstart-create-server-database-azure-cli.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

 

> [!IMPORTANT]
> Diese Anleitung setzt die Verwendung von Azure CLI-Version 2.0 oder höher voraus. Geben Sie zum Bestätigen der Version an der Eingabeaufforderung von Azure CLI `az --version` ein. Informationen zum Ausführen einer Installation oder eines Upgrades finden Sie unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="set-backup-configuration"></a>Festlegen der Sicherungskonfiguration

Beim Konfigurieren des Servers treffen Sie die Entscheidung, ob dafür lokal redundante oder georedundante Sicherungen erstellt werden sollen. 

> [!NOTE]
> Nachdem ein Server erstellt wurde, kann die Redundanzart (georedundant oder lokal redundant) nicht mehr gewechselt werden.
>

Beim Erstellen eines Servers über den `az postgres server create`-Befehl legt der `--geo-redundant-backup`-Parameter Ihre Sicherungsredundanzoption fest. Bei `Enabled` werden georedundante Sicherungen vorgenommen. Bei `Disabled` werden lokal redundante Sicherungen vorgenommen. 

Der Aufbewahrungszeitraum für Sicherungen wird durch den Parameter `--backup-retention-days` festgelegt. 

Weitere Informationen zum Festlegen dieser Werte während der Erstellung finden Sie unter [Schnellstart: Erstellen einer Azure Database for PostgreSQL-Instanz per Azure CLI](quickstart-create-server-database-azure-cli.md).

Der Aufbewahrungszeitraum für Sicherungen kann für einen Server folgendermaßen geändert werden:

```azurecli-interactive
az postgres server update --name mydemoserver --resource-group myresourcegroup --backup-retention 10
```

Das obige Beispiel ändert den Aufbewahrungszeitraum für Sicherungen von „mydemoserver“ in 10 Tage.

Mit „Aufbewahrungszeit für Sicherung“ wird auch gesteuert, für welchen zurückliegenden Zeitraum eine Point-in-Time-Wiederherstellung durchgeführt werden kann, da dies auf den verfügbaren Sicherungen basiert. Die Point-in-Time-Wiederherstellung wird im folgenden Abschnitt näher beschrieben.

## <a name="server-point-in-time-restore"></a>Point-in-Time-Wiederherstellung des Servers
Sie können den Zustand des Servers zu einem früheren Zeitpunkt wiederherstellen. Die wiederhergestellten Daten werden auf einen neuen Server kopiert, und der vorhandene Server bleibt unverändert. Wenn zum Beispiel zur Mittagszeit am heutigen Tag eine Tabelle versehentlich gelöscht wird, können Sie den Zustand kurz vor Mittag wiederherstellen. Dann können Sie die fehlende Tabelle und die fehlenden Daten aus der wiederhergestellten Kopie des Servers abrufen. 

Verwenden Sie zur Wiederherstellung des Servers den Azure CLI-Befehl [az postgres server restore](/cli/azure/postgres/server).

### <a name="run-the-restore-command"></a>Ausführen des Befehls „restore“

Geben Sie zum Wiederherstellen des Servers an der Azure CLI-Eingabeaufforderung den folgenden Befehl ein:

```azurecli-interactive
az postgres server restore --resource-group myresourcegroup --name mydemoserver-restored --restore-point-in-time 2018-03-13T13:59:00Z --source-server mydemoserver
```

Für den Befehl `az postgres server restore` sind folgende Parameter erforderlich:

| Einstellung | Empfohlener Wert | BESCHREIBUNG  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Die Ressourcengruppe, in der sich der Quellserver befindet.  |
| name | mydemoserver-restored | Der Name des neuen Servers, der durch den Befehl „restore“ erstellt wird. |
| restore-point-in-time | 2018-03-13T13:59:00Z | Wählen Sie einen Zeitpunkt aus, dessen Zustand wiederhergestellt werden soll. Datum und Zeit müssen innerhalb des Aufbewahrungszeitraums für Sicherungen des Quellservers liegen. Verwenden Sie das Datums- und Zeitformat nach ISO 8601. Beispielsweise können Sie Ihre eigene lokale Zeitzone wie `2018-03-13T05:59:00-08:00` verwenden. Sie können z.B. auch das UTC-Zulu-Format verwenden, `2018-03-13T13:59:00Z`. |
| source-server | mydemoserver | Der Name oder die ID des Quellservers, über den die Wiederherstellung durchgeführt wird. |

Wenn Sie den Zustand eines Servers zu einem früheren Zeitpunkt wiederherstellen, wird ein neuer Server erstellt. Der ursprüngliche Server und seine Datenbanken aus dem angegebenen Zeitpunkt werden auf den neuen Server kopiert.

Die Werte zum Standort und Tarif des wiederhergestellten Servers bleiben mit denen des ursprünglichen Servers identisch. 

Suchen Sie nach Abschluss der Wiederherstellung den neuen Server, um zu überprüfen, ob die Daten wie erwartet wiederhergestellt wurden.

Der neue Server, der während einer Wiederherstellung erstellt wird, weist nicht die Firewallregeln auf, die auf dem ursprünglichen Server vorhanden waren. Firewallregeln müssen separat für diesen neuen Server eingerichtet werden.

## <a name="geo-restore"></a>Geowiederherstellung
Wenn Sie Ihren Server für georedundante Sicherungen konfiguriert haben, kann aus der Sicherung dieses vorhandenen Servers ein neuer Server erstellt werden. Dieser neue Server kann in allen Regionen erstellt werden, in denen Azure Database for PostgreSQL verfügbar ist.  

Um einen Server mithilfe einer georedundanten Sicherung zu erstellen, verwenden Sie den Azure CLI-Befehl `az postgres server georestore`.

> [!NOTE]
> Für einen neu erstellten Server kann möglicherweise nicht sofort eine Geowiederherstellung durchgeführt werden. Es kann einige Stunden dauern, bis die erforderlichen Metadaten aufgefüllt wurden.
>

Geben Sie zur Geowiederherstellung des Servers an der Azure CLI-Eingabeaufforderung den folgenden Befehl ein:

```azurecli-interactive
az postgres server georestore --resource-group myresourcegroup --name mydemoserver-georestored --source-server mydemoserver --location eastus --sku-name GP_Gen4_8 
```
Dieser Befehl erstellt einen neuen Server namens *mydemoserver-georestored* in „USA, Osten“, der zu *myresourcegroup* gehört. Es ist ein Gen 4-Server vom Typ „Allgemein“ mit 8 virtuellen Kernen. Der Server wird aus der georedundanten Sicherung von *mydemoserver* erstellt, die auch in der Ressourcengruppe *myresourcegroup* vorhanden ist.

Wenn Sie den neuen Server in einer anderen Ressourcengruppe als der, in der sich der vorhandene Server befindet, erstellen möchten, bestimmen Sie im `--source-server`-Parameter den Servernamen wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az postgres server georestore --resource-group newresourcegroup --name mydemoserver-georestored --source-server "/subscriptions/$<subscription ID>/resourceGroups/$<resource group ID>/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver" --location eastus --sku-name GP_Gen4_8

```

Für den Befehl `az postgres server georestore` sind folgende Parameter erforderlich:

| Einstellung | Empfohlener Wert | BESCHREIBUNG  |
| --- | --- | --- |
|resource-group| myresourcegroup | Der Name der Ressourcengruppe, zu der der neue Server gehören soll.|
|name | mydemoserver-georestored | Der Name des neuen Servers. |
|source-server | mydemoserver | Der Name des vorhandenen Servers, dessen georedundanten Sicherungen verwendet werden. |
|location | eastus | Der Speicherort des neuen Servers. |
|sku-name| GP_Gen4_8 | Dieser Parameter legt Tarif, Computegeneration und Anzahl der virtuellen Kerne des neuen Servers fest. GP_Gen4_8 wird einem Gen 4-Server vom Typ „Allgemein“ mit 8 virtuellen Kernen zugeordnet.|


>[!Important]
>Beim Erstellen von einer Geowiederherstellung erbt ein neuer Server Speichergröße und Tarif vom Quellserver. Diese Werte können während der Erstellung nicht geändert werden. Sobald der neue Server erstellt ist, kann die Speichergröße zentral hochskaliert werden.

Suchen Sie nach Abschluss der Wiederherstellung den neuen Server, um zu überprüfen, ob die Daten wie erwartet wiederhergestellt wurden.

Der neue Server, der während einer Wiederherstellung erstellt wird, weist nicht die Firewallregeln auf, die auf dem ursprünglichen Server vorhanden waren. Firewallregeln müssen separat für diesen neuen Server eingerichtet werden.

## <a name="next-steps"></a>Nächste Schritte
- Informieren Sie sich über die [Sicherungen](concepts-backup.md) des Diensts.
- Informieren Sie sich über die Optionen in Bezug auf die [Geschäftskontinuität](concepts-business-continuity.md).
