---
title: Verwalten von Firewallregeln – Azure-Befehlszeilenschnittstelle – Azure Database for PostgreSQL (Einzelserver)
description: In diesem Artikel wird beschrieben, wie Sie mithilfe der Azure CLI-Befehlszeile Firewallregeln in Azure Database for PostgreSQL (Einzelserver) erstellen und verwalten.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3a559b8c65ab57b0144b807a3b4cc1faa912d430
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422738"
---
# <a name="create-and-manage-firewall-rules-in-azure-database-for-postgresql---single-server-using-azure-cli"></a>Erstellen und Verwalten von Firewallregeln in Azure Database for PostgreSQL (Einzelserver) mit der Azure-Befehlszeilenschnittstelle
Mithilfe von Firewallregeln auf Serverebene kann der Zugriff auf einen Azure-Datenbank für PostgreSQL-Server über eine bestimmte IP-Adresse oder über einen IP-Adressbereich verwaltet werden. Dank praktischer Azure CLI-Befehle können Sie zum Verwalten Ihres Servers Firewallregeln erstellen, aktualisieren, löschen, auflisten und anzeigen. Eine Übersicht über Firewallregeln für Azure-Datenbank für PostgreSQL finden Sie unter [Firewallregeln für Azure-Datenbank für PostgreSQL-Server](concepts-firewall-rules.md).

Virtual Network-Regeln (VNET) können auch verwendet werden, um den Zugriff auf Ihren Server zu sichern. Weitere Informationen finden Sie unter [Erstellen und Verwalten von VNET-Dienstendpunkten und -Regeln mithilfe der Azure CLI](howto-manage-vnet-using-cli.md).

## <a name="prerequisites"></a>Voraussetzungen
Zum Ausführen der Schritte in dieser Anleitung benötigen Sie Folgendes:
- Installieren Sie die [Azure CLI](/cli/azure/install-azure-cli), oder verwenden Sie Azure Cloud Shell im Browser.
- Einen [Server und eine Datenbank für Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md).

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Konfigurieren von Firewallregeln für Azure-Datenbank für PostgreSQL
Die [az postgres server firewall-rule](/cli/azure/postgres/server/firewall-rule)-Befehle werden zum Konfigurieren von Firewallregeln verwendet.

## <a name="list-firewall-rules"></a>Auflisten von Firewallregeln 
Führen Sie zum Auflisten der vorhandenen Serverfirewallregeln den [az postgres server firewall-rule list](/cli/azure/postgres/server/firewall-rule)-Befehl aus.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver
```
Die Ausgabe listet die Firewallregeln (sofern vorhanden) standardmäßig im JSON-Format auf. Sie können mit dem Switch `--output table` ein besser lesbares Tabellenformat als die Ausgabe erzeugen.
```azurecli-interactive
az postgres server firewall-rule list --resource-group myresourcegroup --server-name mydemoserver --output table
```
## <a name="create-firewall-rule"></a>Erstellen einer Firewallregel
Erstellen Sie mit dem Befehl [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) eine neue Firewallregel auf dem Server. 


Damit nur der Zugriff auf eine einzelne IP-Adresse gewährt wird, geben Sie wie in diesem Beispiel für `--start-ip-address` und `--end-ip-address` dieselbe Adresse an. Ersetzen Sie dabei die hier gezeigte IP-Adresse durch die von Ihnen benötigte IP-Adresse.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowSingleIpAddress --start-ip-address 13.83.152.1 --end-ip-address 13.83.152.1
```
Um Anwendungen die Verbindung von Azure-IP-Adressen mit dem Azure Database for PostgreSQL-Server zu erlauben, geben Sie wie in diesem Beispiel die IP-Adresse 0.0.0.0 als Start-IP und End-IP an.
```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server-name mydemoserver --name AllowAllAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!IMPORTANT]
> Diese Option konfiguriert die Firewall derart, dass alle von Azure ausgehenden Verbindungen zugelassen werden, einschließlich Verbindungen von den Abonnements anderer Kunden. Wenn Sie diese Option auswählen, stellen Sie sicher, dass die Anmelde- und die Benutzerberechtigungen den Zugriff nur auf autorisierte Benutzer beschränken.
> 

Bei erfolgreicher Ausführung listet die Befehlsausgabe die Details der von Ihnen erstellten Firewallregel standardmäßig im JSON-Format auf. Falls ein Fehler auftritt, wird in der Ausgabe stattdessen eine Fehlermeldung angezeigt.

## <a name="update-firewall-rule"></a>Aktualisieren einer Firewallregel 
Aktualisieren Sie mit dem Befehl [az postgres server firewall-rule update](/cli/azure/postgres/server/firewall-rule) eine vorhandene Firewallregel auf dem Server. Geben Sie den Namen der vorhandenen Firewallregel als Eingabe und die zu aktualisierenden Attribute für Start-IP und End-IP ein.
```azurecli-interactive
az postgres server firewall-rule update --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.0
```
Bei erfolgreicher Ausführung listet die Befehlsausgabe die Details der von Ihnen aktualisierten Firewallregel standardmäßig im JSON-Format auf. Falls ein Fehler auftritt, wird in der Ausgabe stattdessen eine Fehlermeldung angezeigt.
> [!NOTE]
> Falls die Firewallregel nicht vorhanden ist, wird sie vom Updatebefehl erstellt.

## <a name="show-firewall-rule-details"></a>Anzeigen der Details einer Firewallregel
Sie können auch die Details einer vorhandenen Firewallregel auf Serverebene anzeigen, indem Sie den Befehl [az postgres server firewall-rule show](/cli/azure/postgres/server/firewall-rule) ausführen.
```azurecli-interactive
az postgres server firewall-rule show --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Bei erfolgreicher Ausführung listet die Befehlsausgabe die Details der von Ihnen angegebenen Firewallregel standardmäßig im JSON-Format auf. Falls ein Fehler auftritt, wird in der Ausgabe stattdessen eine Fehlermeldung angezeigt.

## <a name="delete-firewall-rule"></a>Löschen einer Firewallregel
Um den Zugriff für einen IP-Adressbereich auf den Server zu widerrufen, löschen Sie eine vorhandene Firewallregel durch Ausführen des Befehls [az postgres server firewall-rule delete](/cli/azure/postgres/server/firewall-rule). Geben Sie den Namen der vorhandenen Firewallregel an.
```azurecli-interactive
az postgres server firewall-rule delete --resource-group myresourcegroup --server-name mydemoserver --name AllowIpRange
```
Bei erfolgreicher Ausführung wird keine Ausgabe angezeigt. Bei einem Fehler wird eine Fehlermeldung zurückgegeben.

## <a name="next-steps"></a>Nächste Schritte
- Sie können auch einen Webbrowser zum [Erstellen und Verwalten von Firewallregeln für Azure Database for PostgreSQL mithilfe des Azure-Portals](howto-manage-firewall-using-portal.md) verwenden.
- Weitere Informationen zu den [Firewallregeln des Servers für Azure Database for PostgreSQL](concepts-firewall-rules.md).
- Sichern Sie den Zugriff auf Ihren Server noch mehr, indem Sie [VNET-Dienstendpunkte und -Regeln mithilfe der Azure CLI erstellen und verwalten](howto-manage-vnet-using-cli.md).
- Wenn Sie Unterstützung beim Herstellen einer Verbindung mit einem Server für Azure Database for PostgreSQL benötigen, lesen Sie die Informationen unter [Datenverbindungsbibliotheken für Azure Database for PostgreSQL](concepts-connection-libraries.md).
