---
title: Sichern und Wiederherstellen – Azure PowerShell – Azure Database for MySQL
description: Informationen zum Sichern und Wiederherstellen eines Servers in Azure Database for MySQL mithilfe von Azure PowerShell
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurepowershel
ms.topic: how-to
ms.date: 4/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 43ce39a1fc05c8ffedd1ae8404cc20c1a498a73f
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94539015"
---
# <a name="how-to-back-up-and-restore-an-azure-database-for-mysql-server-using-powershell"></a>Sichern und Wiederherstellen eines Azure Database for MySQL-Servers mithilfe von PowerShell

Azure Database for MySQL-Server werden regelmäßig gesichert, um Wiederherstellungsfeatures zu aktivieren. Mithilfe dieses Features können Sie für den Server und alle dazugehörigen Datenbanken einen Zustand zu einem früheren Zeitpunkt auf einem neuen Server wiederherstellen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Durcharbeiten dieses Leitfadens benötigen Sie Folgendes:

- Lokale Installation des [Moduls „Az PowerShell“](/powershell/azure/install-az-ps) oder von [Azure Cloud Shell](https://shell.azure.com/) im Browser
- Ein [Azure Database for MySQL-Server](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Solange nur eine Vorschauversion des PowerShell-Moduls „Az.MySql“ verfügbar ist, müssen Sie es separat über das AZ PowerShell-Modul installieren. Verwenden Sie dazu den folgenden Befehl: `Install-Module -Name Az.MySql -AllowPrerelease`.
> Sobald das PowerShell-Modul „Az.MySql“ allgemein verfügbar ist, wird es in die zukünftigen Releases des Az PowerShell-Moduls integriert und in Azure Cloud Shell nativ zur Verfügung gestellt.

Wenn Sie PowerShell lieber lokal verwenden möchten, stellen Sie mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) eine Verbindung mit Ihrem Azure-Konto her.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-backup-configuration"></a>Festlegen der Sicherungskonfiguration

Bei der Servererstellung treffen Sie die Entscheidung, ob Ihr Server für lokal redundante oder georedundante Sicherungen konfiguriert werden soll.

> [!NOTE]
> Nach der Erstellung eines Servers kann die Redundanzart (georedundant oder lokal redundant) nicht mehr geändert werden.

Beim Erstellen eines Servers über den Befehl `New-AzMySqlServer` legt der Parameter **GeoRedundantBackup** Ihre Option für die Sicherungsredundanz fest. Wenn die Option **Aktiviert** festgelegt ist, werden georedundante Sicherungen vorgenommen. Wenn die Option **Deaktiviert** festgelegt ist, werden lokal redundante Sicherungen vorgenommen.

Der Aufbewahrungszeitraum für Sicherungen wird durch den Parameter **BackupRetentionDay** festgelegt.

Weitere Informationen zum Festlegen dieser Werte während der Servererstellung finden Sie unter [Erstellen eines Azure Database for MySQL-Servers mithilfe von PowerShell](quickstart-create-mysql-server-database-using-azure-powershell.md).

Der Aufbewahrungszeitraum für Sicherungen kann für einen Server folgendermaßen geändert werden:

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -BackupRetentionDay 10
```

Das obige Beispiel ändert den Aufbewahrungszeitraum für Sicherungen von „mydemoserver“ in 10 Tage.

Durch die Festlegung eines Aufbewahrungszeitraums für Sicherungen wird auch gesteuert, für welchen zurückliegenden Zeitraum eine Point-in-Time-Wiederherstellung durchgeführt werden kann, da dieser abhängig von den verfügbaren Sicherungen ist. Die Point-in-Time-Wiederherstellung wird im folgenden Abschnitt näher beschrieben.

## <a name="server-point-in-time-restore"></a>Point-in-Time-Wiederherstellung des Servers

Sie können den Zustand des Servers zu einem früheren Zeitpunkt wiederherstellen. Die wiederhergestellten Daten werden auf einen neuen Server kopiert, und der vorhandene Server bleibt unverändert. Wenn zum Beispiel eine Tabelle versehentlich gelöscht wird, können Sie den Zustand kurz vor dem Löschen wiederherstellen. Dann können Sie die fehlende Tabelle und die fehlenden Daten aus der wiederhergestellten Kopie des Servers abrufen.

Verwenden Sie zum Wiederherstellen des Servers das PowerShell-Cmdlet `Restore-AzMySqlServer`.

### <a name="run-the-restore-command"></a>Ausführen des Befehls „restore“

Führen Sie das folgende Beispiel aus PowerShell aus, um den Server wiederherzustellen.

```azurepowershell-interactive
$restorePointInTime = (Get-Date).AddMinutes(-10)
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-restored -ResourceGroupName myresourcegroup -RestorePointInTime $restorePointInTime -UsePointInTimeRestore
```

Die **PointInTimeRestore**-Parameter des `Restore-AzMySqlServer`-Cmdlet müssen die folgenden Parameter umfassen:

| Einstellung | Vorgeschlagener Wert | BESCHREIBUNG  |
| --- | --- | --- |
| ResourceGroupName |  myresourcegroup |  Die Ressourcengruppe, in der sich der Quellserver befindet.  |
| Name | mydemoserver-restored | Der Name des neuen Servers, der durch den Befehl „restore“ erstellt wird. |
| RestorePointInTime | 2020-03-13T13:59:00Z | Wählen Sie einen Zeitpunkt für die Wiederherstellung aus. Datum und Zeit müssen innerhalb des Aufbewahrungszeitraums für Sicherungen des Quellservers liegen. Verwenden Sie das Datums- und Zeitformat nach ISO 8601. Beispielsweise können Sie Ihre eigene lokale Zeitzone wie **2020-03-13T05:59:00-08:00** verwenden. Sie können auch das Format „UTC-Zulu“ verwenden, z. B. **2018-03-13T13:59:00Z**. |
| UsePointInTimeRestore | `<SwitchParameter>` | Verwenden Sie den Point-in-Time-Modus für die Wiederherstellung. |

Wenn Sie den Zustand eines Servers zu einem früheren Zeitpunkt wiederherstellen, wird ein neuer Server erstellt. Der ursprüngliche Server und seine Datenbanken zum angegebenen Zeitpunkt werden auf den neuen Server kopiert.

Die Werte zum Standort und Tarif des wiederhergestellten Servers bleiben mit denen des ursprünglichen Servers identisch.

Suchen Sie nach Abschluss der Wiederherstellung den neuen Server, um zu überprüfen, ob die Daten wie erwartet wiederhergestellt wurden. Der neue Server verfügt über den gleichen Anmeldenamen für den Serveradministrator (und das dazugehörige Kennwort), der für den vorhandenen Server beim Start der Wiederherstellung gültig war. Sie können das Kennwort auf der Seite **Übersicht** des neuen Servers ändern.

Der neue Server, der während einer Wiederherstellung erstellt wird, weist nicht die VNET-Dienstendpunkte auf, die auf dem ursprünglichen Server vorhanden waren. Diese Regeln müssen separat für den neuen Server eingerichtet werden. Firewallregeln vom ursprünglichen Server werden wiederhergestellt.

## <a name="geo-restore"></a>Geowiederherstellung

Wenn Sie Ihren Server für georedundante Sicherungen konfiguriert haben, kann aus der Sicherung dieses vorhandenen Servers ein neuer Server erstellt werden. Dieser neue Server kann in allen Regionen erstellt werden, in denen Azure Database for MySQL verfügbar ist.

Wenn Sie einen Server mithilfe einer georedundanten Sicherung erstellen möchten, verwenden Sie den `Restore-AzMySqlServer`-Befehl mit dem Parameter **UseGeoRestore**.

> [!NOTE]
> Für einen neu erstellten Server kann möglicherweise nicht sofort eine Geowiederherstellung durchgeführt werden. Es kann einige Stunden dauern, bis die erforderlichen Metadaten aufgefüllt wurden.

Führen Sie das folgende Beispiel aus PowerShell aus, um eine Geowiederherstellung des Servers durchzuführen:

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-georestored -ResourceGroupName myresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

In diesem Beispiel wird ein neuer Server mit dem Namen **mydemoserver-georestored** in der Region „USA, Osten“ erstellt, der der Ressourcengruppe **myresourcegroup** angehört. Es ist ein Gen 5-Server vom Typ „Universell“ mit acht virtuellen Kernen. Der Server wird aus der georedundanten Sicherung von **mydemoserver** erstellt, die ebenfalls der Ressourcengruppe **myresourcegroup** angehört.

Wenn Sie den neuen Server in einer anderen Ressourcengruppe als der des vorhandenen Servers erstellen möchten, geben Sie wie im folgenden Beispiel gezeigt den Namen der neuen Ressourcengruppe unter Verwendung des Parameters **ResourceGroupName** an:

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  Restore-AzMySqlServer -Name mydemoserver-georestored -ResourceGroupName newresourcegroup -Location eastus -Sku GP_Gen5_8 -UseGeoRestore
```

Die **GeoRestore**-Parameter des `Restore-AzMySqlServer`-Cmdlet müssen die folgenden Parameter umfassen:

| Einstellung | Vorgeschlagener Wert | BESCHREIBUNG  |
| --- | --- | --- |
|ResourceGroupName | myresourcegroup | Der Name der Ressourcengruppe, der der neue Server angehört.|
|Name | mydemoserver-georestored | Der Name des neuen Servers. |
|Standort | eastus | Der Speicherort des neuen Servers. |
|UseGeoRestore | `<SwitchParameter>` | Verwenden Sie den Geomodus für die Wiederherstellung. |

Beim Erstellen eines neuen Servers mit Geowiederherstellung erbt dieser die Speichergröße und den Tarif des Quellservers, es sei denn, der Parameter **Sku** wird angegeben.

Suchen Sie nach Abschluss der Wiederherstellung den neuen Server, um zu überprüfen, ob die Daten wie erwartet wiederhergestellt wurden. Der neue Server verfügt über den gleichen Anmeldenamen für den Serveradministrator (und das dazugehörige Kennwort), der für den vorhandenen Server beim Start der Wiederherstellung gültig war. Sie können das Kennwort auf der Seite **Übersicht** des neuen Servers ändern.

Der neue Server, der während einer Wiederherstellung erstellt wird, weist nicht die VNET-Dienstendpunkte auf, die auf dem ursprünglichen Server vorhanden waren. Diese Regeln müssen separat für diesen neuen Server eingerichtet werden. Firewallregeln vom ursprünglichen Server werden wiederhergestellt.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Generieren einer Azure Database for MySQL-Verbindungszeichenfolge mit PowerShell](howto-connection-string-powershell.md)
