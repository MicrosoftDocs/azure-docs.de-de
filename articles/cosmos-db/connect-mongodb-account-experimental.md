---
title: Verbinden einer MongoDB-Anwendung mit Azure Cosmos DB
description: In diesem Artikel erfahren Sie, wie Sie durch Abrufen der Verbindungszeichenfolge über das Portal eine MongoDB-App mit Azure Cosmos DB verbinden.
author: gahl-levy
ms.author: gahllevy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 02/08/2021
ms.reviewer: sngun
robots: noindex
ms.openlocfilehash: bd244b0bb0aa5c5b2377b5a5675466645da3256c
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99982066"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Verbinden einer MongoDB-Anwendung mit Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

In diesem Artikel erfahren Sie, wie Sie Ihre MongoDB-App mithilfe einer MongoDB-Verbindungszeichenfolge mit Azure Cosmos DB verbinden. Anschließend können Sie eine Azure Cosmos-Datenbank als Datenspeicher für Ihre MongoDB-App verwenden. Zusätzlich zum nachstehenden Tutorial können Sie MongoDB-[Beispiele](mongodb-samples.md) mit der Azure Cosmos DB-API für MongoDB erkunden.

Dieses Tutorial zeigt zwei Möglichkeiten, um Informationen zur Verbindungszeichenfolge abzurufen:

- [Die Schnellstartmethode](#get-the-mongodb-connection-string-by-using-the-quick-start) zur Verwendung mit .NET-, Node.js-, MongoDB Shell-, Java- und Python-Treibern
- [Die Methode mit benutzerdefinierten Verbindungszeichenfolgen](#get-the-mongodb-connection-string-to-customize) zur Verwendung mit anderen Treibern

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto. Wenn Sie noch kein Azure-Konto besitzen, erstellen Sie jetzt ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/).
- Ein Cosmos-Konto. Anweisungen hierzu finden Sie unter [Erstellen einer Web-App mit der API für MongoDB von Azure Cosmos DB und dem .NET SDK](create-mongodb-dotnet.md).

## <a name="get-the-mongodb-connection-string-by-using-the-quick-start"></a>Abrufen der MongoDB-Verbindungszeichenfolge mithilfe der Schnellstartmethode

1. Melden Sie sich über einen Internetbrowser beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie die API auf dem Blatt **Azure Cosmos DB** aus.
3. Klicken Sie im linken Bereich des Kontoblatts auf **Schnellstart**.
4. Wählen Sie Ihre Plattform aus ( **.NET**, **Node.js**, **MongoDB Shell**, **Java**, **Python**). Wenn der gewünschte Treiber oder das gewünschte Tool nicht aufgeführt wird, machen Sie sich keine Sorgen: Wir stellen kontinuierlich weitere Ausschnitte von Verbindungscodes zur Verfügung. Tragen Sie im unteren Bereich ein, welche Inhalte Sie sehen möchten. Informationen darüber, wie Sie eine eigene Verbindung erstellen können, finden Sie unter [Abrufen der Informationen zur Verbindungszeichenfolge für das Konto](#get-the-mongodb-connection-string-to-customize).
5. Kopieren Sie den Codeausschnitt, und fügen Sie ihn in Ihre MongoDB-App ein.

    :::image type="content" source="./media/connect-mongodb-account/QuickStartBlade.png" alt-text="Blatt „Schnellstart“":::

## <a name="get-the-mongodb-connection-string-to-customize"></a>Abrufen der MongoDB-Verbindungszeichenfolge zum Anpassen

1. Melden Sie sich über einen Internetbrowser beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie die API auf dem Blatt **Azure Cosmos DB** aus.
3. Klicken Sie im linken Bereich des Kontoblatts auf **Verbindungszeichenfolge**.
4. Das Blatt **Verbindungszeichenfolge** wird geöffnet. Es enthält alle erforderlichen Informationen, um mithilfe eines Treibers für MongoDB eine Verbindung mit dem Konto herzustellen, einschließlich einer vorab erstellten Verbindungszeichenfolge.

   :::image type="content" source="./media/connect-mongodb-account/ConnectionStringBlade.png" alt-text="Blatt „Verbindungszeichenfolge“" lightbox= "./media/connect-mongodb-account/ConnectionStringBlade.png" :::

## <a name="connection-string-requirements"></a>Anforderungen an die Verbindungszeichenfolge

> [!Important]
> Für Azure Cosmos DB gelten strenge Sicherheitsanforderungen und -standards. Azure Cosmos DB-Konten erfordern eine Authentifizierung und eine sichere Kommunikation über *TLS*.

Azure Cosmos DB unterstützt das standardmäßige URI-Format für MongoDB-Verbindungszeichenfolgen. Es gelten jedoch einige besondere Anforderungen: Azure Cosmos DB-Konten erfordern eine Authentifizierung und eine sichere Kommunikation über TLS. Folglich sieht das Format der Verbindungszeichenfolge wie folgt aus:

`mongodb://username:password@host:port/[database]?ssl=true`

Die Werte dieser Zeichenfolge werden auf dem zuvor gezeigten Blatt **Verbindungszeichenfolge** angezeigt:

* Benutzername (erforderlich): Cosmos-Kontoname.
* Kennwort (erforderlich): Cosmos-Kontokennwort.
* Host (erforderlich): FQDN des Cosmos-Kontos.
* Port (erforderlich): 10255
* Datenbank (optional): Die Datenbank, die die Verbindung verwendet. Wenn keine Datenbank angegeben wird, wird standardmäßig die Datenbank „test“ verwendet.
* ssl=true (erforderlich)

Sehen Sie sich beispielsweise das Konto auf dem Blatt **Verbindungszeichenfolge** an. Eine gültige Verbindungszeichenfolge ist:

`mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true`

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Studio 3T](mongodb-mongochef.md) mit der API für MongoDB von Azure Cosmos DB verwenden.
- Erfahren Sie, wie Sie [Robo 3T](mongodb-robomongo.md) mit der API für MongoDB von Azure Cosmos DB verwenden.
- Untersuchen Sie MongoDB-[Beispiele](mongodb-samples.md) mit der API für MongoDB von Azure Cosmos DB.
