---
title: 'Schnellstart: Erstellen einer Azure Database for MySQL-Instanz mithilfe von „az mysql up“'
description: Schnellstart zum Erstellen von Azure Database for MySQL-Servern mithilfe des Befehls „up“ der Azure-Befehlszeilenschnittstelle.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 3/18/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d89cc41ed26124ae4ad2e6689be6d59278c3d9da
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542166"
---
# <a name="quickstart-create-an-azure-database-for-mysql-using-a-simple-azure-cli-command---az-mysql-up-preview"></a>Schnellstart: Erstellen einer Azure Database for MySQL-Instanz mithilfe eines einfachen Azure CLI-Befehls – az mysql up (Vorschau)

> [!IMPORTANT]
> Der Befehl [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) der Azure-Befehlszeilenschnittstelle befindet sich in der Vorschauphase.

Azure-Datenbank für MySQL ist ein verwalteter Dienst, mit dem Sie hochverfügbare MySQL-Datenbanken in der Cloud ausführen, verwalten und skalieren können. Die Azure CLI dient zum Erstellen und Verwalten von Azure-Ressourcen über die Befehlszeile oder mit Skripts. In diesem Schnellstart wird veranschaulicht, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle und des Befehls [az mysql up](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up) einen Azure Database for MySQL-Server erstellen. Zusätzlich zum Server erstellt der Befehl `az mysql up` auch eine Beispieldatenbank und einen Root-Benutzer in der Datenbank, er öffnet die Firewall für Azure-Dienste und erstellt Firewallregeln für den Clientcomputer. Dies trägt zur Beschleunigung des Entwicklungsprozesses bei.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

Für den Artikel müssen Sie mindestens Version 2.0 der Azure-Befehlszeilenschnittstelle lokal ausführen. Führen Sie den Befehl `az --version` aus, um die installierte Version anzuzeigen. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

Sie müssen sich mithilfe des Befehls [az login](/cli/azure/authenticate-azure-cli) bei Ihrem Konto anmelden. Beachten Sie die Eigenschaft **id** aus der Befehlsausgabe für den entsprechenden Abonnementnamen.

```azurecli
az login
```

Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem die Ressource fakturiert sein sollte. Wählen Sie mithilfe des Befehls [az account set](/cli/azure/account) die Abonnement-ID unter Ihrem Konto aus. Ersetzen Sie den Platzhalter für die Abonnement-ID durch die **subscription ID**-Eigenschaft der Ausgabe von **az login** für Ihr Abonnement.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-an-azure-database-for-mysql-server"></a>Erstellen eines Servers für Azure-Datenbank für MySQL

Damit Sie die Befehle verwenden können, installieren Sie die Erweiterung [db-up](/cli/azure/ext/db-up). Falls ein Fehler auftritt, vergewissern Sie sich, dass die neueste Version der Azure-Befehlszeilenschnittstelle installiert ist. Weitere Informationen finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).

```azurecli
az extension add --name db-up
```

Erstellen Sie mit dem folgenden Befehl einen Azure Database for MySQL-Server:

```azurecli
az mysql up
```

Der Server wird mit den folgenden Standardwerten erstellt (sofern Sie diese nicht manuell überschreiben):

**Einstellung** | **Standardwert** | **Beschreibung**
---|---|---
Servername | Systemgeneriert | Ein eindeutiger Name, der Ihren Server für Azure-Datenbank für MySQL identifiziert.
resource-group | Systemgeneriert | Eine neue Azure-Ressourcengruppe
sku-name | GP_Gen5_2 | Der Name der SKU. Folgt der Konvention „{Tarif}\_{Computegeneration}\_{virtuelle Kerne}“ in Kurzform. Der Standardwert ist ein Gen5-Server vom Typ „Universell“ mit zwei virtuellen Kernen. Informationen zu den Tarifen finden Sie auf der [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/mysql/).
backup-retention | 7 | Gibt die Aufbewahrungsdauer für eine Sicherung an. Die Einheit ist Tage.
geo-redundant-backup | Disabled | Gibt an, ob georedundante Sicherungen für diesen Server aktiviert werden sollen.
location | westus2 | Der Azure-Standort für den Server.
ssl-enforcement | Aktiviert | Gibt an, ob SSL für diesen Server aktiviert werden soll.
storage-size | 5120 | Die Speicherkapazität des Servers (Einheit: MB).
version | 5.7 | Die MySQL-Hauptversion.
admin-user | Systemgeneriert | Der Benutzername für die Administratoranmeldung.
admin-password | Systemgeneriert | Das Kennwort des Administratorbenutzers.

> [!NOTE]
> Weitere Informationen zum Befehl `az mysql up` und den zugehörigen zusätzlichen Parametern finden Sie in der [Dokumentation der Azure-Befehlszeilenschnittstelle](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-up).

Nach der Erstellung weist Ihr Server die folgenden Einstellungen auf:

- Es wird eine Firewallregel namens „devbox“ erstellt. Die Azure-Befehlszeilenschnittstelle versucht, die IP-Adresse des Computers zu ermitteln, auf dem der Befehl `az mysql up` ausgeführt wird, und lässt diese IP-Adresse zu.
- „Zugriff auf Azure-Dienste zulassen“ ist aktiviert. Mit dieser Einstellung wird die Firewall des Servers so konfiguriert, dass sie Verbindungen von allen Azure-Ressourcen akzeptiert – auch von Ressourcen, die nicht in Ihrem Abonnement enthalten sind.
- Der Parameter `wait_timeout` ist auf 8 Stunden festgelegt.
- Es wird eine leere Datenbank mit der Bezeichnung „sampledb“ erstellt.
- Es wird ein neuer Benutzer mit dem Namen „root“ und Berechtigungen für „sampledb“ erstellt.

> [!NOTE]
> Azure Database for MySQL kommuniziert über Port 3306. Wenn Sie eine Verbindung aus einem Unternehmensnetzwerk heraus herstellen, wird der ausgehende Datenverkehr über Port 3306 von der Firewall Ihres Netzwerks unter Umständen nicht zugelassen. Ihre IT-Abteilung muss Port 3306 öffnen, damit Sie eine Verbindung mit Ihrem Server herstellen können.

## <a name="get-the-connection-information"></a>Abrufen der Verbindungsinformationen

Nach Abschluss des Befehls `az mysql up` wird eine Liste von Verbindungszeichenfolgen für verbreitete Programmiersprachen an Sie zurückgegeben. Diese Verbindungszeichenfolgen sind mit bestimmten Attributen Ihres neu erstellten Azure Database for MySQL-Servers vorkonfiguriert.

Sie können den Befehl [az mysql show-connection-string](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-show-connection-string) verwenden, um diese Verbindungszeichenfolgen erneut aufzulisten.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Bereinigen Sie alle im Schnellstart erstellten Ressourcen mit dem folgenden Befehl. Dieser Befehl löscht den Azure Database for MySQL-Server und die Ressourcengruppe.

```azurecli
az mysql down --delete-group
```

Wenn Sie nur den neu erstellten Server löschen möchten, können Sie den Befehl [az mysql down](/cli/azure/ext/db-up/mysql#ext-db-up-az-mysql-down) ausführen.

```azurecli
az mysql down
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Entwerfen einer MySQL-Datenbank mit der Azure CLI](./tutorial-design-database-using-cli.md)
