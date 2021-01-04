---
title: Verwalten von Firewallregeln – Azure-CLI – Azure Database for MySQL – Flexible Server
description: Erstellen und Verwalten von Firewallregeln für Azure Database for MySQL – Flexible Server mithilfe der Azure CLI.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 9/21/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 84fdd3045d5a1d44ff611134d88fc9793ee203de
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545071"
---
# <a name="create-and-manage-azure-database-for-mysql---flexible-server-firewall-rules-using-the-azure-cli"></a>Erstellen und Verwalten von Firewallregeln für Azure Database for MySQL – Flexible Server mit der Azure CLI

> [!IMPORTANT]
> Azure Database for MySQL Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

Azure Database for MySQL Flexible Server unterstützt zwei Arten von sich gegenseitig ausschließenden Netzwerkverbindungsmethoden, mit denen eine Verbindung mit Ihrem flexiblen Server hergestellt werden kann. Die zwei Optionen sind:

- Öffentlicher Zugriff (zugelassene IP-Adressen)
- Privater Zugriff (VNET-Integration)

Dieser Artikel befasst sich mit der Erstellung von MySQL-Servern mit **öffentlichem Zugriff (zugelassene IP-Adressen)** mithilfe der Azure CLI und enthält eine Übersicht über die Azure CLI-Befehle, die Sie zum Erstellen, Aktualisieren, Löschen, Auflisten und Anzeigen von Firewallregeln nach Erstellung des Servers verwenden können. Mit *Öffentlicher Zugriff (zulässige IP-Adressen)* werden die Verbindungen mit dem MySQL-Server ausschließlich auf zulässige IP-Adressen beschränkt. Die Client-IP-Adressen müssen in Firewallregeln zugelassen werden. Weitere Informationen finden Sie unter [Öffentlicher Zugriff (zugelassene IP-Adressen)](./concepts-networking.md#public-access-allowed-ip-addresses). Die Firewallregeln können zum Zeitpunkt der Servererstellung definiert werden (empfohlen), jedoch können sie auch später hinzugefügt werden.

## <a name="launch-azure-cloud-shell"></a>Starten von Azure Cloud Shell

[Azure Cloud Shell](../../cloud-shell/overview.md) ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert.

Wählen Sie zum Öffnen von Cloud Shell oben rechts in einem Codeblock einfach die Option **Ausprobieren** . Sie können Cloud Shell auch auf einer separaten Browserregisterkarte öffnen, indem Sie zu [https://shell.azure.com/bash](https://shell.azure.com/bash) navigieren. Wählen Sie **Kopieren** aus, um die Codeblöcke zu kopieren. Fügen Sie die Blöcke anschließend in Cloud Shell ein, und wählen Sie **Eingabe** , um sie auszuführen.

Wenn Sie es vorziehen, die CLI lokal zu installieren und zu verwenden, müssen Sie für diesen Schnellstart mindestens Version 2.0 der Azure CLI verwenden. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen sich mit dem Befehl [az login](/cli/azure/reference-index#az-login) bei Ihrem Konto anmelden. Beachten Sie die Eigenschaft **ID** , die auf die **Abonnement-ID** für Ihr Azure-Konto verweist.

```azurecli-interactive
az login
```

Wählen Sie mithilfe des Befehls [az account set](/cli/azure/account#az-account-set) das Abonnement unter Ihrem Konto aus. Notieren Sie sich aus der Ausgabe von **az login** den Wert für **ID** . Sie verwenden ihn im Befehl als Wert für das Argument **subscription** . Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem die Ressource fakturiert sein sollte. Verwenden Sie [az account list](/cli/azure/account#az-account-list), um alle Abonnements abzurufen.

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-firewall-rule-during-flexible-server-create-using-azure-cli"></a>Erstellen einer Firewallregel während der Erstellung eines flexiblen Servers mithilfe der Azure CLI

Sie können den `az mysql flexible-server --public access`-Befehl zum Erstellen des flexiblen Servers mit *öffentlichem Zugriff (zugelassene IP-Adressen)* verwenden und die Firewallregeln während der Erstellung konfigurieren. Sie können die Option **--public-access** verwenden, um die zulässigen IP-Adressen bereitzustellen, die zum Herstellen einer Verbindung mit dem Server verwendet werden können. Sie können einen einzelnen IP-Adressbereich angeben, der in der Liste zulässiger IP-Adressen enthalten sein soll. Der IP-Adressbereich muss durch Bindestriche getrennt sein und darf keine Leerzeichen enthalten. Wie im folgenden Beispiel gezeigt gibt es verschiedene Optionen zum Erstellen eines flexiblen Servers mithilfe der CLI.

Die vollständige Liste von konfigurierbaren CLI-Parametern finden Sie in der [Referenzdokumentation](/cli/azure/mysql/flexible-server) zur Azure CLI. In den folgenden Befehlen können Sie beispielsweise optional die Ressourcengruppe festlegen.

- Erstellen eines flexiblen Servers mit öffentlichem Zugriff und Hinzufügen von Client-IP-Adressen für den Zugriff auf den Server
    ```azurecli-interactive
    az mysql flexible-server create --public-access <my_client_ip>
    ```
- Erstellen eines flexiblen Servers mit öffentlichem Zugriff und Hinzufügen des IP-Adressbereichs mit Zugriff auf den Server

    ```azurecli-interactive
    az mysql flexible-server create --public-access <start_ip_address-end_ip_address>
    ```
- Erstellen eines flexiblen Servers mit öffentlichem Zugriff und Zulassen des Herstellens von Verbindungen mit Ihrem Server über Azure-IP-Adressen für Anwendungen
    ```azurecli-interactive
    az mysql flexible-server create --public-access 0.0.0.0
    ```
    > [!IMPORTANT]
    > Diese Option konfiguriert die Firewall zum Zulassen des öffentlichen Zugriffs über Azure-Dienste und -Ressourcen auf diesen Server, einschließlich Verbindungen aus Abonnements anderer Kunden. Wenn Sie diese Option auswählen, stellen Sie sicher, dass die Anmelde- und die Benutzerberechtigungen den Zugriff nur auf autorisierte Benutzer beschränken.
    >
- Erstellen eines flexiblen Servers mit öffentlichem Zugriff und Zulassen aller IP-Adressen
    ```azurecli-interactive
    az mysql flexible-server create --public-access all
    ```
    >[!Note]
    > Mit dem obigen Befehl wird eine Firewallregel mit der Start-IP-Adresse „0.0.0.0“ und der End-IP-Adresse „255.255.255.255“ erstellt, d. h., es werden keine IP-Adressen blockiert. Jeder Host im Internet kann auf diesen Server zugreifen. Es wird dringend empfohlen, diese Regel nur vorübergehend und ausschließlich für Testserver zu verwenden, die keine vertraulichen Daten enthalten.

- Erstellen eines flexiblen Servers mit öffentlichem Zugriff und ohne IP-Adressen
    ```azurecli-interactive
    az mysql flexible-server create --public-access none
    ```
    >[!Note]
    > Es wird davon abgeraten, einen Server ohne Firewallregeln zu erstellen. Wenn Sie keine Firewallregeln hinzufügen, kann kein Client eine Verbindung mit dem Server herstellen.

## <a name="create-and-manage-firewall-rule-after-server-create"></a>Erstellen und Verwalten von Firewallregeln nach der Servererstellung
Der Befehl **az mysql flexible-server firewall-rule** wird in der Azure CLI zum Erstellen, Löschen, Auflisten, Anzeigen und Aktualisieren von Firewallregeln verwendet.

Befehle:
- **create** : Mit diesem Befehl wird eine Firewallregel für den flexiblen Server erstellt.
- **list** : Mit diesem Befehl werden die Firewallregeln für den flexiblen Server aufgelistet.
- **update** : Mit diesem Befehl wird eine Firewallregel für den flexiblen Server aktualisiert.
- **show** : Mit diesem Befehl werden Details zu einer Firewallregel für den flexiblen Server angezeigt.
- **delete** : Mit diesem Befehl wird eine Firewallregel für den flexiblen Server gelöscht.

Die vollständige Liste von konfigurierbaren CLI-Parametern finden Sie in der [Referenzdokumentation](/cli/azure/mysql/flexible-server) zur Azure CLI. In den folgenden Befehlen können Sie beispielsweise optional die Ressourcengruppe festlegen.

### <a name="create-a-firewall-rule"></a>Erstellen einer Firewallregel
Verwenden Sie den Befehl `az mysql flexible-server firewall-rule create`, um die neue Firewallregel für den Server zu erstellen.
Zum Zulassen eines IP-Adressbereichs geben Sie die Start- und End-IP-Adressen an (siehe Beispiel).
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```

Zum Zulassen des Zugriffs für nur eine einzelne IP-Adresse geben Sie lediglich die einzelne IP-Adresse an (siehe Beispiel).
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 1.1.1.1
```

Geben Sie die IP-Adresse „0.0.0.0“ als Start-IP-Adresse an, um Anwendungen über Azure-IP-Adressen das Herstellen einer Verbindung mit Ihrem flexiblen Server zu erlauben (siehe Beispiel).
```azurecli-interactive
az mysql flexible-server firewall-rule create --name mydemoserver --start-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Diese Option konfiguriert die Firewall zum Zulassen des öffentlichen Zugriffs über Azure-Dienste und -Ressourcen auf diesen Server, einschließlich Verbindungen aus Abonnements anderer Kunden. Wenn Sie diese Option auswählen, stellen Sie sicher, dass die Anmelde- und die Benutzerberechtigungen den Zugriff nur auf autorisierte Benutzer beschränken.
> 

Bei erfolgreicher Ausführung listet die Befehlsausgabe für die Erstellung die Details der von Ihnen erstellten Firewallregel standardmäßig im JSON-Format auf. Falls ein Fehler auftritt, wird in der Ausgabe stattdessen eine Fehlermeldung angezeigt.

### <a name="list-firewall-rules"></a>Auflisten von Firewallregeln 
Verwenden Sie den Befehl `az mysql flexible-server firewall-rule list`, um die vorhandenen Firewallregeln des Servers aufzulisten. Beachten Sie, dass das Attribut für den Servernamen in der Option **--name** angegeben wird.
```azurecli-interactive
az mysql flexible-server firewall-rule list --name mydemoserver
```
Die Ausgabe listet die Regeln (sofern vorhanden) standardmäßig im JSON-Format auf. Sie können die Option „--output table“ verwenden, um die Ergebnisse in einer Tabellenformat auszugeben, das leichter zu lesen ist.
```azurecli-interactive
az mysql flexible-server firewall-rule list --name mydemoserver --output table
```

### <a name="update-a-firewall-rule"></a>Aktualisieren einer Firewallregel
Verwenden Sie den Befehl `az mysql flexible-server firewall-rule update`, um eine vorhandene Firewallregel für den Server zu aktualisieren. Geben Sie den Namen der vorhandenen Firewallregel als Eingabe sowie die zu aktualisierenden Attribute für die Start- und End-IP-Adresse ein.
```azurecli-interactive
az mysql flexible-server firewall-rule update --name mydemoserver --rule-name FirewallRule1 --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
Bei erfolgreicher Ausführung listet die Befehlsausgabe die Details der von Ihnen aktualisierten Firewallregel standardmäßig im JSON-Format auf. Falls ein Fehler auftritt, wird in der Ausgabe stattdessen eine Fehlermeldung angezeigt.

> [!NOTE]
> Falls die Firewallregel nicht vorhanden ist, wird sie vom Befehl „Update“ erstellt.

### <a name="show-firewall-rule-details"></a>Anzeigen der Details einer Firewallregel
Verwenden Sie den Befehl `az mysql flexible-server firewall-rule show`, um Details zu einer vorhandenen Firewallregel des Servers anzuzeigen. Geben Sie den Namen der vorhandenen Firewallregel als Eingabe an.
```azurecli-interactive
az mysql flexible-server firewall-rule show --name mydemoserver --rule-name FirewallRule1
```
Bei erfolgreicher Ausführung listet die Befehlsausgabe die Details der von Ihnen angegebenen Firewallregel standardmäßig im JSON-Format auf. Falls ein Fehler auftritt, wird in der Ausgabe stattdessen eine Fehlermeldung angezeigt.

### <a name="delete-a-firewall-rule"></a>Löschen einer Firewallregel
Verwenden Sie den Befehl `az mysql flexible-server firewall-rule delete`, um eine vorhandene Firewallregel für den Server zu löschen. Geben Sie den Namen der vorhandenen Firewallregel an.
```azurecli-interactive
az mysql flexible-server firewall-rule delete --name mydemoserver --rule-name FirewallRule1
```
Bei erfolgreicher Ausführung wird keine Ausgabe angezeigt. Bei einem Fehler wird eine Fehlermeldung angezeigt.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen finden Sie unter [Netzwerke in Azure Database for MySQL Flexible Server](./concepts-networking.md).
- Erfahren Sie mehr über [Firewallregeln für Azure Database for MySQL Flexible Server](./concepts-networking.md#public-access-allowed-ip-addresses).
- [Erstellen und Verwalten von Firewallregeln für Azure Database for MySQL Flexible Server mit dem Azure-Portal](./how-to-manage-firewall-portal.md).