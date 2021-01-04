---
title: 'Schnellstart: Erstellen einer Azure Notification Hubs-Instanz mithilfe der Azure-Befehlszeilenschnittstelle | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle eine Azure Notification Hubs-Instanz erstellen.
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: dbradish
ms.reviewer: thsomasu
ms.lastreviewed: 03/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6c565be3aea43fc4dc86fd793e0dc2dea0b08d4c
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94563748"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>Schnellstart: Erstellen einer Azure Notification Hubs-Instanz mithilfe der Azure-Befehlszeilenschnittstelle

Azure Notification Hubs bietet ein benutzerfreundliches, horizontal skalierbares Pushmodul, mit dem Sie von einem beliebigen Back-End (cloudbasiert oder lokal) Benachrichtigungen an sämtliche Plattformen (iOS, Android, Windows, Kindle, Baidu usw.) senden können. Weitere Informationen zu diesem Dienst finden Sie unter [Was ist Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

In dieser Schnellstartanleitung erstellen Sie einen Notification Hub mithilfe der Azure-Befehlszeilenschnittstelle. Im ersten Abschnitt erfahren Sie, wie Sie einen Notification Hubs-Namespace erstellen. Im zweiten Abschnitt erfahren Sie, wie Sie einen Notification Hub in einem bereits vorhandenen Namespace erstellen. Außerdem erfahren Sie, wie Sie eine benutzerdefinierte Zugriffsrichtlinie erstellen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> Für Notification Hubs ist mindestens die Version 2.0.67 der Azure-Befehlszeilenschnittstelle erforderlich. Führen Sie [az version](/cli/azure/reference-index#az_version) aus, um die installierte Version und die abhängigen Bibliotheken zu ermitteln. Führen Sie [az upgrade](/cli/azure/reference-index#az_upgrade) aus, um das Upgrade auf die aktuelle Version durchzuführen.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Azure Notification Hubs muss genau wie alle anderen Azure-Ressourcen in einer Ressourcengruppe bereitgestellt werden.  Mit Ressourcengruppen können verwandte Azure-Ressourcen organisiert und verwaltet werden.  Lesen Sie [Was ist Azure Resource Manager?](../azure-resource-manager/management/overview.md), um mehr über Ressourcengruppen zu erfahren.

Erstellen Sie für diese Schnellstartanleitung mithilfe des folgenden Befehls vom Typ [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe namens **spnhubrg** am Standort **eastus**.

```azurecli
az group create --name spnhubrg --location eastus
```

## <a name="create-a-notification-hubs-namespace"></a>Erstellen eines Notification Hubs-Namespace

1. Erstellen Sie einen Namespace für Ihre Benachrichtigungs-Hubs.

   Ein Namespace enthält mindestens einen Hub, und der Name muss über alle Azure-Abonnements hinweg eindeutig und mindestens sechs Zeichen lang sein. Verwenden Sie den Befehl [az notification-hub namespace check-availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability), um die Verfügbarkeit eines Namens zu prüfen.

   ```azurecli
   az notification-hub namespace check-availability --name spnhubns
   ```

   Die Azure CLI reagiert mit der folgenden Konsolenausgabe auf Ihre Verfügbarkeitsanfrage:

   ```shell
   {
   "id": "/subscriptions/yourSubscriptionID/providers/Microsoft.NotificationHubs/checkNamespaceAvailability",
   "isAvailable": true,
   "location": null,
   "name": "spnhubns",
   "properties": false,
   "sku": null,
   "tags": null,
   "type": "Microsoft.NotificationHubs/namespaces/checkNamespaceAvailability"
   }
   ```

   Beachten Sie die zweite Zeile in der Antwort der Azure CLI: `"isAvailable": true`. In dieser Zeile ist `false` angegeben, wenn der gewünschte Name, den Sie für den Namespace angegeben haben, nicht verfügbar ist. Nachdem Sie die Verfügbarkeit des Namens bestätigt haben, führen Sie den Befehl [az notification-hub namespace create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) aus, um Ihren Namespace zu erstellen.  

   ```azurecli
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

   Wenn der Wert für `--name`, den Sie für den Befehl `az notification-hub namespace create` angegeben haben, nicht verfügbar ist oder die [Benennungsregeln und -einschränkungen für Azure-Ressourcen](../azure-resource-manager/management/resource-name-rules.md) nicht erfüllt, reagiert die Azure CLI mit der folgenden Konsolenausgabe:

   ```shell
   #the name is not available
   The specified name is not available. For more information visit https://aka.ms/eventhubsarmexceptions.

   #the name is invalid
   The specified service namespace is invalid.
   ```

   Wenn der erste von Ihnen ausprobierte Name nicht erfolgreich ist, wählen Sie einen anderen Namen für Ihren neuen Namespace aus, und führen Sie den Befehl `az notification-hub namespace create` erneut aus.

   > [!NOTE]
   > Ab diesem Schritt müssen Sie den Wert des Parameters `--namespace` in jedem Azure CLI-Befehl ersetzen, den Sie aus dieser Schnellstartanleitung kopieren.

2. Rufen Sie eine Liste der Namespaces ab.

   Verwenden Sie den Befehl [az notification-hub namespace list](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-list), um die Details zum neuen Namespace anzuzeigen. Der Parameter `--resource-group` ist optional, wenn Sie alle Namespaces für ein Abonnement anzeigen möchten.

   ```azurecli
   az notification-hub namespace list --resource-group spnhubrg
   ```

## <a name="create-notification-hubs"></a>Erstellen von Notification Hubs

1. Erstellen Sie Ihren ersten Notification Hub.

   In dem neuen Namespace können nun Benachrichtigungs-Hubs erstellt werden. Führen Sie den Befehl [az notification-hub create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) aus, um einen Notification Hub zu erstellen.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. Erstellen Sie einen zweiten Notification Hub.

   In einem einzelnen Namespace können mehrere Notification Hubs erstellt werden. Führen Sie erneut den Befehl `az notification-hub create` aus, um einen zweiten Notification Hub im gleichen Namespace zu erstellen.

   ```azurecli
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

3. Rufen Sie eine Liste der Benachrichtigungs-Hubs ab.

   Die Azure CLI gibt mit jedem ausgeführten Befehl eine Erfolgsmeldung oder eine Fehlermeldung zurück. Sie haben jedoch auch die Möglichkeit, eine Liste der Benachrichtigungs-Hubs abzurufen. Zu diesem Zweck wurde der Befehl [az notification-hub list](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-list) entworfen.

   ```azurecli
   az notification-hub list --resource-group spnhubrg --namespace-name spnhubns --output table
   ```

## <a name="work-with-access-policies"></a>Verwenden von Zugriffsrichtlinien

1. Azure Notification Hubs nutzt [SAS-Sicherheit (Shared Access Signature)](./notification-hubs-push-notification-security.md) durch die Verwendung von Zugriffsrichtlinien. Zwei Richtlinien werden automatisch erstellt, wenn Sie einen Benachrichtigungs-Hub erstellen. Die Verbindungszeichenfolgen aus diesen Richtlinien sind zum Konfigurieren von Pushbenachrichtigungen erforderlich. Mit dem Befehl [az notification-hub authorization-rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) wird eine Liste mit Richtliniennamen und den entsprechenden Ressourcengruppen bereitgestellt.

   ```azurecli
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > Verwenden Sie in Ihrer Anwendung nicht die Richtlinie _DefaultFullSharedAccessSignature_. Diese Richtlinie ist nur für die Verwendung in Ihrem Back-End vorgesehen. Verwenden Sie in Ihrer Clientanwendung nur Zugriffsrichtlinien vom Typ `Listen`.

2. Wenn Sie weitere Autorisierungsregeln mit aussagekräftigen Namen erstellen möchten, können Sie eigene Zugriffsrichtlinien erstellen und anpassen. Verwenden Sie dazu den Befehl [az notification-hub authorization-rule create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create). Der Parameter `--rights` ist eine durch Leerzeichen getrennte Liste der Berechtigungen, die Sie zuweisen möchten.

   ```azurecli
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Manage Send
   ```

3. Jede Zugriffsrichtlinie verfügt über zwei Gruppen von Schlüsseln und Verbindungszeichenfolgen. Sie werden später zum [Konfigurieren eines Benachrichtigungs-Hubs](./configure-notification-hub-portal-pns-settings.md) benötigt. Führen Sie den Befehl [az notification-hub authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) aus, um die Schlüssel und Verbindungszeichenfolgen für eine Notification Hubs-Zugriffsrichtlinie aufzulisten.

   ```azurecli
   # query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output table
   ```

   ```azurecli
   # query the keys and connection strings for a custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > Ein [Notification Hubs-Namespace](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) und ein [Benachrichtigungshub](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) haben jeweils separate Zugriffsrichtlinien. Achten Sie beim Abfragen von Schlüsseln und Verbindungszeichenfolgen auf die Verwendung der korrekten Azure CLI-Referenz.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe und alle zugehörigen Ressourcen nicht mehr benötigen, können Sie sie mit dem Befehl [az group delete](/cli/azure/group) löschen.

```azurecli
az group delete --name spnhubrg
```

## <a name="next-steps"></a>Nächste Schritte

* In dieser Schnellstartanleitung haben Sie einen Notification Hub erstellt. Unter [Schnellstart: Einrichten von Pushbenachrichtigungen in einem Notification Hub](configure-notification-hub-portal-pns-settings.md) erfahren Sie, wie Sie den Hub mit PNS-Einstellungen (Platform Notification System, Plattformbenachrichtigungssystem) konfigurieren.

* Informieren Sie sich über die umfassenden Funktionen, die für die Verwaltung von Benachrichtigungshubs über die Azure-Befehlszeilenschnittstelle zur Verfügung stehen:

  [Notification Hubs: Vollständige Referenzliste](/cli/azure/ext/notification-hub/notification-hub)

  [Notification Hubs: Namespacereferenzliste](/cli/azure/ext/notification-hub/notification-hub/namespace)

  [Notification Hubs: Referenzliste für die Autorisierungsregel](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)

  [Notification Hubs: Referenzliste für Anmeldeinformationen](/cli/azure/ext/notification-hub/notification-hub/credential)