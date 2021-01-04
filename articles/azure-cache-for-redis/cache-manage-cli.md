---
title: Verwalten von Azure Cache for Redis mit der klassischen Azure CLI
description: Erfahren Sie, wie Sie die klassische Azure CLI auf einer beliebigen Plattform installieren, eine Verbindung mit Ihrem Azure-Konto herstellen und über die klassische CLI einen Azure Cache for Redis erstellen und verwalten.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: yegu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7643f882d5ac330046c169e0a3f2fa4920331d4e
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92537693"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>Erstellen und Verwalten von Azure Cache for Redis mit der klassischen Azure CLI
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Klassische Azure-Befehlszeilenschnittstelle](cache-manage-cli.md)
>

Die klassische Azure CLI (Befehlszeilenschnittstelle) ist eine hervorragende Methode, um Ihre Azure-Infrastruktur von einer beliebigen Plattform aus zu verwalten. In diesem Artikel wird das Erstellen und Verwalten Ihrer Azure Cache for Redis-Instanzen mit der klassischen Azure CLI beschrieben.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> Die Beispielskripts für die aktuelle Azure CLI finden Sie unter [Azure CLI-Beispiele für Azure Cache for Redis ](cli-samples.md).

## <a name="prerequisites"></a>Voraussetzungen
Zum Erstellen und Verwalten von Azure Cache for Redis-Instanzen mithilfe der klassischen Azure CLI müssen Sie die folgenden Schritte ausführen.

* Sie benötigen ein Azure-Konto. Wenn Sie dies noch nicht haben, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.
* [Installieren Sie die klassische Azure CLI](/cli/azure/install-classic-cli).
* Verbinden Sie die Installation der Azure CLI mit einem persönlichen Azure-Konto oder einem Geschäfts-, Schul- oder Unikonto für Azure, und melden Sie sich mit dem Befehl `azure login` über die klassische Azure CLI an.
* Wechseln Sie vor dem Ausführen eines der folgenden Befehle in der klassischen Azure CLI in den Resource Manager-Modus, indem Sie den Befehl `azure config mode arm` ausführen. Weitere Informationen finden Sie unter [Verwalten von Azure-Ressourcen und -Ressourcengruppen mithilfe der klassischen Azure CLI](../azure-resource-manager/management/manage-resources-cli.md).

## <a name="azure-cache-for-redis-properties"></a>Azure Cache for Redis-Eigenschaften
Die folgenden Eigenschaften werden beim Erstellen und Aktualisieren von Azure Cache for Redis-Instanzen verwendet.

| Eigenschaft | Schalter | BESCHREIBUNG |
| --- | --- | --- |
| name |-n, --name |Der Name des Azure Cache for Redis. |
| Ressourcengruppe |-g, --resource-group |Der Name der Ressourcengruppe. |
| location |-l, --location |Speicherort zum Erstellen des Cache. |
| size |-z, --size |Die Größe des Azure Cache for Redis. Gültige Werte: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku |-x, --sku |Redis-SKU. Sollte einer der folgenden Werte sein: [Basic, Standard, Premium] |
| EnableNonSslPort |-e, --enable-non-ssl-port |EnableNonSslPort-Eigenschaft des Azure Cache for Redis. Fügen Sie dieses Flag hinzu, wenn Sie den TLS-/SSL-fremden Port für Ihren Cache aktivieren möchten. |
| Redis-Konfiguration |-c, --redis-configuration |Redis-Konfiguration. Geben Sie hier eine JSON-formatierte Zeichenfolge des Konfigurationsschlüssels und der -werte ein. Format:"{"":"","":""}" |
| Redis-Konfiguration |-f, --redis-configuration-file |Redis-Konfiguration. Geben Sie hier den Pfad einer Datei mit Konfigurationsschlüssel und -werten ein. Format für den Dateieintrag: {"":"","":""} |
| Shard-Anzahl |-r, --shard-count |Die Anzahl der zu erstellenden Shards auf einem Premium-Clustercache mit Clustering. |
| Virtual Network |-v, --virtual-network |Gibt die genaue ARM-Ressourcen-ID des Virtual Network an, in dem der Cache bereitgestellt wird, wenn Sie den Azure Cache for Redis in einem VNET hosten. Beispielformat: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| key type |-t, --key-type |Der Typ des zu erneuernden Schlüssels. Gültige Werte: [Primary, Secondary] |
| StaticIP |-p, --static-ip \<static-ip\> |Wenn Sie den Cache in einem VNET hosten, geben Sie hiermit eine eindeutige IP-Adresse im Subnetz für den Cache an. Wird keine IP-Adresse angegeben, wird eine für Sie aus dem Subnetz ausgewählt. |
| Subnet |t, --subnet \<subnet\> |Wenn Sie den Cache in einem VNET hosten, geben Sie hiermit den Namen des Subnetzes an, in dem der Cache bereitgestellt wird. |
| VirtualNetwork |-v, --virtual-network \<virtual-network\> |Gibt die genaue ARM-Ressourcen-ID des Virtual Network an, in dem der Cache bereitgestellt wird, wenn Sie den Azure Cache for Redis in einem VNET hosten. Beispielformat: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Subscription |-s, --subscription |Die Abonnement-ID. |

## <a name="see-all-azure-cache-for-redis-commands"></a>Siehe alle Azure Cache for Redis-Befehle
Wenn Sie alle Azure Cache for Redis-Befehle und deren Parameter anzeigen möchten, verwenden Sie den `azure rediscache -h` -Befehl.

```azurecli
C:\>azure rediscache -h
help:    Commands to manage your Azure Cache for Redis(s)
help:
help:    Create an Azure Cache for Redis
help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
help:
help:    Delete an existing Azure Cache for Redis
help:      rediscache delete [--name <name> --resource-group <resource-group> ]
help:
help:    List all Azure Cache for Redis within your Subscription or Resource Group
help:      rediscache list [options]
help:
help:    Show properties of an existing Azure Cache for Redis
help:      rediscache show [--name <name> --resource-group <resource-group>]
help:
help:    Change settings of an existing Azure Cache for Redis
help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
help:
help:    Renew the authentication key for an existing Azure Cache for Redis
help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
help:
help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help  output usage information
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="create-an-azure-cache-for-redis"></a>Erstellen einer Azure Cache for Redis-Instanz
Verwenden Sie zum Erstellen eines Azure Cache for Redis den folgenden Befehl:

```azurecli
    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
```

Wenn Sie weitere Informationen zu diesem Befehl benötigen, führen Sie den `azure rediscache create -h` -Befehl aus.

```azurecli
C:\>azure rediscache create -h
help:    Create an Azure Cache for Redis
help:
help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
help:
help:    Options:
help:      -h, --help                                               output usage information
help:      -v, --verbose                                            use verbose output
help:      -vv                                                      more verbose with debug output
help:      --json                                                   use json output
help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>                    Name of the Resource Group
help:      -l, --location <location>                                Location to create cache.
help:      -z, --size <size>                                        Size of the Azure Cache for Redis. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Azure Cache for Redis. Add this flag if you want to enable the non-TLS/SSL Port for your cache
help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
help:      -t, --subnet <subnet>                                    Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
help:      -p, --static-ip <static-ip>                              Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
help:      -s, --subscription <id>                                  the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="delete-an-existing-azure-cache-for-redis"></a>Löschen eines vorhandenen Azure Cache for Redis
Verwenden Sie zum Löschen eines Azure Cache for Redis den folgenden Befehl:

```azurecli
    azure rediscache delete [--name <name> --resource-group <resource-group> ]
```

Wenn Sie weitere Informationen zu diesem Befehl benötigen, führen Sie den `azure rediscache delete -h` -Befehl aus.

```azurecli
C:\>azure rediscache delete -h
help:    Delete an existing Azure Cache for Redis
help:
help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="list-all-azure-cache-for-redis-within-your-subscription-or-resource-group"></a>Auflisten aller Azure Cache for Redis-Instanzen in Ihrem Abonnement oder Ihrer Ressourcengruppe
Verwenden Sie zum Auflisten aller Azure Cache for Redis-Instanzen in Ihrem Abonnement oder Ihrer Ressourcengruppe den folgenden Befehl:

```azurecli
    azure rediscache list [options]
```

Wenn Sie weitere Informationen zu diesem Befehl benötigen, führen Sie den `azure rediscache list -h` -Befehl aus.

```azurecli
C:\>azure rediscache list -h
help:    List all Azure Cache for Redis within your Subscription or Resource Group
help:
help:    Usage: rediscache list [options]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -g, --resource-group <resource-group>  Name of the Resource Group
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>Anzeigen der Eigenschaften eines vorhandenen Azure Cache for Redis
Verwenden Sie zum Anzeigen der Eigenschaften eines vorhandenen Azure Cache for Redis den folgenden Befehl:

```azurecli
    azure rediscache show [--name <name> --resource-group <resource-group>]
```

Wenn Sie weitere Informationen zu diesem Befehl benötigen, führen Sie den `azure rediscache show -h` -Befehl aus.

```azurecli
C:\>azure rediscache show -h
help:    Show properties of an existing Azure Cache for Redis
help:
help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

<a name="scale"></a>

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>Ändern der Eigenschaften eines vorhandenen Azure Cache for Redis
Verwenden Sie zum Ändern der Eigenschaften eines vorhandenen Azure Cache for Redis den folgenden Befehl:

```azurecli
    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
```

Wenn Sie weitere Informationen zu diesem Befehl benötigen, führen Sie den `azure rediscache set -h` -Befehl aus.

```azurecli
C:\>azure rediscache set -h
help:    Change settings of an existing Azure Cache for Redis
help:
help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
help:
help:    Options:
help:      -h, --help                                               output usage information
help:      -v, --verbose                                            use verbose output
help:      -vv                                                      more verbose with debug output
help:      --json                                                   use json output
help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>                    Name of the Resource Group
help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
help:      -s, --subscription <subscription>                        the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>Erneuern des Authentifizierungsschlüssels für einen vorhandenen Azure Cache for Redis
Verwenden Sie zum Erneuern des Authentifizierungsschlüssels für einen vorhandenen Azure Cache for Redis den folgenden Befehl:

```azurecli
    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]
```

Geben Sie `Primary` oder `Secondary` für `key-type` an.

Wenn Sie weitere Informationen zu diesem Befehl benötigen, führen Sie den `azure rediscache renew-key -h` -Befehl aus.

```azurecli
C:\>azure rediscache renew-key -h
help:    Renew the authentication key for an existing Azure Cache for Redis
help:
help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>Auflisten der primären und sekundären Schlüssel eines vorhandenen Azure Cache for Redis
Verwenden Sie zum Auflisten der primären und sekundären Schlüssel eines vorhandenen Azure Cache for Redis den folgenden Befehl:

```azurecli
    azure rediscache list-keys [--name <name> --resource-group <resource-group>]
```

Wenn Sie weitere Informationen zu diesem Befehl benötigen, führen Sie den `azure rediscache list-keys -h` -Befehl aus.

```azurecli
C:\>azure rediscache list-keys -h
help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
help:
help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```