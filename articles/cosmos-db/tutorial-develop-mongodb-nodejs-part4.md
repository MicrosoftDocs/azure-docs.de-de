---
title: Erstellen einer Angular-App mit der Azure Cosmos DB-API für MongoDB (Teil 1)
description: Teil 4 der Tutorialreihe zur Erstellung einer MongoDB-App mit Angular und Note in Azure Cosmos DB unter Verwendung der gleichen APIs wie für MongoDB
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: 4ca7286676c441b2fa96883e0c187497f59d6222
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93082623"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-cosmos-account"></a>Erstellen einer Angular-App mit der Azure Cosmos DB-API für MongoDB: Erstellen eines Cosmos-Kontos
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Dieses mehrteilige Tutorial zeigt, wie Sie eine in Node.js geschriebene neue App mit Express und Angular erstellen und mit Ihrem [Cosmos-Konto verbinden, das mit der API für MongoDB von Cosmos DB](mongodb-introduction.md) konfiguriert wurde.

Teil 4 des Tutorials baut auf [Teil 3](tutorial-develop-mongodb-nodejs-part3.md) auf und umfasst folgende Aufgaben:

> [!div class="checklist"]
> * Erstellen einer Azure-Ressourcengruppe mithilfe der Azure-Befehlszeilenschnittstelle
> * Erstellen eines Cosmos-Kontos mit der Azure-Befehlszeilenschnittstelle

## <a name="video-walkthrough"></a>Exemplarische Vorgehensweise per Video

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>Voraussetzungen

Für diesen Teil des Tutorials wird vorausgesetzt, dass Sie die Schritte aus [Teil 3](tutorial-develop-mongodb-nodejs-part3.md) des Tutorials ausgeführt haben. 

In diesem Tutorial können Sie entweder Azure Cloud Shell (in Ihrem Internetbrowser) oder die lokal installierte [Azure CLI](/cli/azure/install-azure-cli) verwenden.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> In diesem Tutorial wird die Anwendungserstellung Schritt für Schritt erläutert. Für den Fall, dass Sie das fertige Projekt herunterladen möchten, steht die fertige Anwendung auf GitHub im [Repository „angular-cosmosdb“](https://github.com/Azure-Samples/angular-cosmosdb) zur Verfügung.

## <a name="create-an-azure-cosmos-db-account"></a>Erstellen eines Azure Cosmos DB-Kontos

Erstellen Sie mithilfe des Befehls [`az cosmosdb create`](/cli/azure/cosmosdb#az-cosmosdb-create) ein Azure Cosmos DB-Konto.

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* Geben Sie für `<cosmosdb-name>` Ihren eigenen Azure Cosmos DB-Kontonamen an. Der Name muss für alle Azure Cosmos DB-Kontonamen in Azure eindeutig sein.
* Die Einstellung `--kind MongoDB` ermöglicht Azure Cosmos DB die Verwendung von MongoDB-Clientverbindungen.

Die Ausführung des Befehls dauert unter Umständen etwas. Nach Abschluss des Befehls werden im Terminalfenster Informationen zur neuen Datenbank angezeigt. 

Gehen Sie nach der Erstellung des Azure Cosmos DB-Kontos wie folgt vor:
1. Öffnen Sie ein neues Browserfenster, und navigieren Sie zu [https://portal.azure.com](https://portal.azure.com).
1. Klicken Sie auf der linken Leiste auf das Azure Cosmos DB-Logo :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-icon.png":::. Daraufhin werden alle verfügbaren Azure Cosmos DB-Instanzen angezeigt.
1. Klicken Sie auf das soeben erstellte Azure Cosmos DB-Konto, wählen Sie die Registerkarte **Übersicht** aus, und scrollen Sie nach unten, um die Karte mit dem Standort der Datenbank anzuzeigen. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-angular-portal.png" alt-text="Screenshot: Übersicht über ein Azure Cosmos DB-Konto":::

4. Scrollen Sie im linken Navigationsbereich nach unten, und klicken Sie auf die Registerkarte **Daten global replizieren**. Daraufhin erscheint eine Karte mit den verschiedenen Regionen, die als Ziele für die Replikation zur Verfügung stehen. So können Sie beispielsweise auf „Australien, Südosten“ oder „Australien, Osten“ klicken und Ihre Daten in Australien replizieren. Weitere Informationen zur globalen Replikation finden Sie unter [Wie werden Daten mit Azure Cosmos DB global verteilt?](distribute-data-globally.md). Der Einfachheit halber verwenden wir vorerst nur eine einzelne Instanz. Bei Bedarf wissen Sie nun aber, wie die Replikation funktioniert.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-replicate-portal.png" alt-text="Screenshot: Azure Cosmos DB-Konto, für das „Daten global replizieren“ ausgewählt ist":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Teil des Tutorials haben Sie die folgenden Aufgaben ausgeführt:

> [!div class="checklist"]
> * Erstellen einer Azure-Ressourcengruppe mithilfe der Azure-Befehlszeilenschnittstelle
> * Erstellen eines Azure Cosmos DB-Kontos mithilfe der Azure-Befehlszeilenschnittstelle

Im nächsten Teil des Tutorials wird Azure Cosmos DB unter Verwendung von Mongoose mit Ihrer App verbunden.

> [!div class="nextstepaction"]
> [Herstellen einer Verbindung mit Azure Cosmos DB mithilfe von Mongoose](tutorial-develop-mongodb-nodejs-part5.md)