---
title: MongoDB-, React- und Node.js-Tutorial für Azure
description: In dieser videobasierten Tutorialreihe erfahren Sie, wie Sie in Azure Cosmos DB eine MongoDB-App mit React und Node.js erstellen und dabei die gleichen APIs verwenden wie für MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: jopapa
ms.reviewer: sngun
ms.custom: devx-track-js
ms.openlocfilehash: 1425b89e42450123c1696ddcee4458e1f69b8a6c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348567"
---
# <a name="create-a-mongodb-app-with-react-and-azure-cosmos-db"></a>Erstellen einer MongoDB-App mit React und Azure Cosmos DB  
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Dieses mehrteilige Videotutorial veranschaulicht, wie Sie eine Heldenverfolgungs-App mit einem React-Front-End erstellen. Die App verwendet Node und Express für den Server, stellt eine Verbindung mit der Cosmos-Datenbank her, die mit der [Azure Cosmos DB-API für MongoDB](mongodb-introduction.md) konfiguriert ist, und stellt dann eine Verbindung zwischen dem React-Front-End und dem Serverteil der App her. Das Tutorial vermittelt auch, wie Sie im Azure-Portal eine Point-and-Click-Skalierung von Cosmos DB durchführen und die App im Internet bereitstellen, damit alle Benutzer ihre Lieblingshelden nachverfolgen können. 

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) unterstützt die Wire Protocol-Kompatibilität mit MongoDB, sodass Clients Azure Cosmos DB anstelle von MongoDB verwenden können.  

Dieses mehrteilige Tutorial umfasst folgende Aufgaben:

> [!div class="checklist"]
> * Einführung
> * Einrichten des Projekts
> * Erstellen der Benutzeroberfläche mit React
> * Erstellen eines Azure Cosmos DB-Kontos über das Azure-Portal
> * Herstellen einer Verbindung mit Azure Cosmos DB mithilfe von Mongoose
> * Hinzufügen von React-, Erstellungs-, Aktualisierungs- und Löschvorgängen zur App

Diese App kann auch mit Angular erstellt werden. Die Tutorialvideoreihe für Angular finden Sie [hier](tutorial-develop-mongodb-nodejs.md).

## <a name="prerequisites"></a>Voraussetzungen
* [Node.js](https://www.nodejs.org)

### <a name="finished-project"></a>Fertiges Projekt
Die fertige Anwendung steht auf [GitHub](https://github.com/Azure-Samples/react-cosmosdb) zur Verfügung.

## <a name="introduction"></a>Einführung 

In diesem Video stellt Burke Holland Azure Cosmos DB vor und führt Sie schrittweise durch die App, die in dieser Videoreihe erstellt wird. 

> [!VIDEO https://www.youtube.com/embed/58IflnJbYJc]

## <a name="project-setup"></a>Projekteinrichtung

In diesem Video erfahren Sie, wie Sie Express und React im gleichen Projekt einrichten. Anschließend folgt eine Erläuterung des im Projekt enthaltenen Codes.

> [!VIDEO https://www.youtube.com/embed/ytFUPStJJds]

## <a name="build-the-ui"></a>Erstellen der Benutzeroberfläche

In diesem Video erfahren Sie, wie Sie mithilfe von React die Benutzeroberfläche der Anwendung erstellen. 

> [!NOTE]
> Das CSS, von dem in diesem Video die Rede ist, finden Sie im [GitHub-Repository „react-cosmosdb“](https://github.com/Azure-Samples/react-cosmosdb/blob/master/src/index.css).

> [!VIDEO https://www.youtube.com/embed/SzHzX0fTUUQ]

## <a name="connect-to-azure-cosmos-db"></a>Herstellen der Verbindung mit Azure Cosmos DB

In diesem Video erfahren Sie, wie Sie über das Azure-Portal ein Azure Cosmos DB-Konto erstellen, das MongoDB- und das Mongoose-Paket erstellen und dann die App mithilfe der Azure Cosmos DB-Verbindungszeichenfolge mit dem neu erstellten Konto verbinden. 

> [!VIDEO https://www.youtube.com/embed/0U2jV1thfvs]

## <a name="read-and-create-heroes-in-the-app"></a>Lesen und Erstellen von Helden in der App

In diesem Video erfahren Sie, wie Sie Hero-Elemente in der Cosmos-Datenbank lesen und erstellen und wie Sie die entsprechenden Methoden mit Postman und der React-Benutzeroberfläche testen. 

> [!VIDEO https://www.youtube.com/embed/AQK9n_8fsQI] 

## <a name="delete-and-update-heroes-in-the-app"></a>Löschen und Aktualisieren von Helden in der App

In diesem Video erfahren Sie, wie Sie Helden über die App löschen und aktualisieren und wie Sie die Aktualisierungen auf der Benutzeroberfläche anzeigen. 

> [!VIDEO https://www.youtube.com/embed/YmaGT7ztTQM] 

## <a name="complete-the-app"></a>Vervollständigen der App

In diesem Video erfahren Sie, wie Sie die App vervollständigen und die Integration der Benutzeroberfläche in die Back-End-API abschließen. 

> [!VIDEO https://www.youtube.com/embed/TcSm2ISfTu8]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie diese App nicht weiterhin verwenden, löschen Sie mit den folgenden Schritten im Azure-Portal sämtliche Ressourcen, die mit diesem Tutorial erstellt wurden. 

1. Klicken Sie im Azure-Portal im Menü auf der linken Seite auf **Ressourcengruppen**, und klicken Sie auf den Namen der erstellten Ressource. 
2. Klicken Sie auf der Seite mit Ihrer Ressourcengruppe auf **Löschen**, geben Sie im Textfeld den Namen der zu löschenden Ressource ein, und klicken Sie dann auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Erstellen einer App mit React, Node, Express und Azure Cosmos DB 
> * Erstellen eines Azure Cosmos DB-Kontos
> * Verbinden der App mit dem Azure Cosmos DB-Konto
> * Testen der App mithilfe von Postman
> * Ausführen der Anwendung und Hinzufügen von Helden zur Datenbank

Im nächsten Tutorial erfahren Sie, wie Sie MongoDB-Daten in Azure Cosmos DB importieren.  

> [!div class="nextstepaction"]
> [Import MongoDB data into Azure Cosmos DB (Importieren von Daten aus MongoDB in Azure Cosmos DB)](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
