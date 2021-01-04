---
title: 'Schnellstart: Verwenden der Azure-Befehlszeilenschnittstelle zum Erstellen einer Service Bus-Warteschlange | Microsoft-Dokumentation'
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie mit der Azure CLI einen Service Bus-Namespace und darin dann eine Warteschlange erstellen.
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 14892b114f3bb8a0bb6c07f7cdd01a174b931cf9
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95810625"
---
# <a name="use-the-azure-cli-to-create-a-service-bus-namespace-and-a-queue"></a>Verwenden der Azure CLI zum Erstellen eines Service Bus-Namespace und einer Warteschlange
In dieser Schnellstartanleitung wird veranschaulicht, wie Sie mit der Azure CLI einen Service Bus-Namespace und eine Warteschlange erstellen. Darüber hinaus wird beschrieben, wie Sie Anmeldeinformationen für die Autorisierung abrufen, die von einer Clientanwendung zum Senden bzw. Empfangen von Nachrichten für eine Warteschlange genutzt werden können. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Voraussetzungen
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto][free account] erstellen, bevor Sie beginnen.

In dieser Schnellstartanleitung verwenden Sie Azure Cloud Shell. Diesen Dienst können Sie nach der Anmeldung beim Azure-Portal starten. Ausführliche Informationen zu Azure Cloud Shell finden Sie unter [Übersicht über Azure Cloud Shell](../cloud-shell/overview.md). Sie können auch Azure PowerShell auf Ihrem Computer [installieren](/cli/azure/install-azure-cli) und verwenden. 

## <a name="provision-resources"></a>Bereitstellen von Ressourcen
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Starten Sie Azure Cloud Shell über das in der folgenden Abbildung gezeigte Symbol. Wechseln Sie in den **Bash**-Modus, wenn Cloud Shell im **PowerShell**-Modus ausgeführt wird. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Cloud Shell starten":::
3. Führen Sie den folgenden Befehl aus, um eine Azure-Ressourcengruppe zu erstellen. Aktualisieren Sie ggf. den Namen der Ressourcengruppe und des Standorts. 

    ```azurecli-interactive
    az group create --name ContosoRG --location eastus
    ```
4. Führen Sie den folgenden Befehl aus, um einen Service Bus-Messagingnamespace zu erstellen.

    ```azurecli-interactive
    az servicebus namespace create --resource-group ContosoRG --name ContosoSBusNS --location eastus
    ```
5. Führen Sie den folgenden Befehl aus, um eine Warteschlange in dem Namespace zu erstellen, den Sie im vorherigen Schritt erstellt haben. In diesem Beispiel ist `ContosoRG` die Ressourcengruppe, die Sie im vorherigen Schritt erstellt haben. `ContosoSBusNS` ist der Name des Service Bus-Namespace, der in dieser Ressourcengruppe erstellt wurde. 

    ```azurecli-interactive
    az servicebus queue create --resource-group ContosoRG --namespace-name ContosoSBusNS --name ContosoOrdersQueue
    ```
6. Führen Sie den folgenden Befehl aus, um die primäre Verbindungszeichenfolge für den Namespace abzurufen. Sie verwenden diese Verbindungszeichenfolge, um eine Verbindung mit der Warteschlange herzustellen und Nachrichten zu senden und zu empfangen. 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group ContosoRG --namespace-name ContosoSBusNS --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    Notieren Sie sich die Verbindungszeichenfolge und den Namen der Warteschlange. Sie verwenden diese Angaben zum Senden und Empfangen von Nachrichten. 


## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie einen Service Bus-Namespace und darin dann eine Warteschlange erstellt. Informationen zum Senden bzw. Empfangen von Nachrichten für die Warteschlange finden Sie in einer der folgenden Schnellstartanleitungen im Abschnitt **Senden und Empfangen von Nachrichten**. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

