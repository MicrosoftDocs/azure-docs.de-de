---
title: Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS) in Azure Cosmos DB
description: Dieser Artikel beschreibt das Konfigurieren der Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS) in Azure Cosmos DB im Azure-Portal und über Azure Resource Manager-Vorlagen.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/11/2019
ms.author: dech
ms.openlocfilehash: eba49ff45ba9ab1f5cfaa1d75973d656ac32ca6a
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339894"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Konfigurieren der Ressourcenfreigabe zwischen verschiedenen Ursprüngen (CORS)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

CORS (Cross Origin Resource Sharing; Ressourcenfreigabe zwischen verschiedenen Ursprüngen) ist eine HTTP-Funktion, die einer Webanwendung, die in einer Domäne ausgeführt wird, den Zugriff auf Ressourcen in einer anderen Domäne ermöglicht. In Webbrowser ist eine Sicherheitseinschränkung implementiert, die als „Same Origin Policy“ bekannt ist und verhindert, dass eine Webseite APIs in einer anderen Domäne aufruft. CORS bietet jedoch eine sichere Methode, um der Ursprungsdomäne den Aufruf von APIs in einer anderen Domäne zu erlauben. Die Core-API (SQL) in Azure Cosmos DB unterstützt jetzt CORS (Cross-Origin Resource Sharing, Ressourcenfreigabe zwischen verschiedenen Ursprüngen) mithilfe des Headers „allowedOrigins“. Nachdem Sie die CORS-Unterstützung für Ihr Azure Cosmos-Konto aktiviert haben, werden nur authentifizierte Anforderungen ausgewertet, um festzustellen, ob sie entsprechend den angegebenen Regeln zulässig sind.

Sie können die CORS-Einstellung im Azure-Portal oder über eine Azure Resource Manager-Vorlage konfigurieren. Für Cosmos-Konten, die die Core-API (SQL) verwenden, unterstützt Azure Cosmos DB eine JavaScript-Bibliothek, die sowohl mit Node.js als auch in browsergestützten Umgebungen verwendet werden kann. Diese Bibliothek kann nun bei Verwendung des Gateway-Modus die Vorteile der CORS-Unterstützung nutzen. Um dieses Feature zu verwenden, ist clientseitig keine Konfiguration erforderlich. Mit der CORS-Unterstützung können Ressourcen in einem Browser über die [JavaScript-Bibliothek](https://www.npmjs.com/package/@azure/cosmos) oder direkt über die [REST-API](/rest/api/cosmos-db/) direkt auf Azure Cosmos DB zugreifen. Das vereinfacht die Vorgänge.

> [!NOTE]
> Die CORS-Unterstützung steht nur für die Azure Cosmos DB-Core-API (SQL) zur Verfügung und wird auch nur dort unterstützt. Sie trifft nicht auf die Azure Cosmos DB-APIs für Cassandra, Gremlin oder MongoDB zu, da diese Protokolle kein HTTP für die Kommunikation zwischen Client und Server verwenden.

## <a name="enable-cors-support-from-azure-portal"></a>Aktivieren der CORS-Unterstützung im Azure-Portal

Führen Sie die folgenden Schritte aus, um CORS im Azure-Portal zu aktivieren:

1. Navigieren Sie zu Ihrem Azure Cosmos DB-Konto. Öffnen Sie das Blatt **CORS**.

2. Geben Sie eine durch Kommas getrennte Liste von Ursprüngen an, die ursprungsübergreifende Aufrufe an Ihr Cosmos DB-Konto richten können. Beispiel: `https://www.mydomain.com`, `https://mydomain.com`, `https://api.mydomain.com`. Sie können auch einen Platzhalter „\*“ verwenden, um alle Ursprünge zuzulassen. Wählen Sie dann **Senden** aus. 

   > [!NOTE]
   > Derzeit können Sie Platzhalter nicht in Domänennamen verwenden. Das Format `https://*.mydomain.net` beispielsweise wird noch nicht unterstützt. 

   :::image type="content" source="./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png" alt-text="Aktivieren von CORS im Azure-Portal":::

## <a name="enable-cors-support-from-resource-manager-template"></a>Aktivieren der CORS-Unterstützung über eine Azure Resource Manager-Vorlage

Zum Aktivieren von CORS mithilfe einer Azure Resource Manager-Vorlage fügen Sie einer beliebigen vorhandenen Vorlage den Abschnitt „cors“ mit der Eigenschaft „allowedOrigins“ hinzu. Das folgende JSON-Beispiel zeigt eine Vorlage, die ein neues Azure Cosmos-Konto erstellt, bei dem CORS aktiviert ist.

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "cors": [
      {
        "allowedOrigins": "*"
      }
    ]
  }
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>Verwenden der JavaScript-Bibliothek von Azure Cosmos DB in einem Browser

Zurzeit wird die JavaScript-Bibliothek von Azure Cosmos DB nur mit der CommonJS-Version der Bibliothek ausgeliefert. Wenn Sie die Bibliothek im Browser verwenden möchten, müssen Sie ein Tool wie Rollup oder Webpack verwenden, um eine browserkompatible Bibliothek zu erstellen. Für bestimmte Node.js-Bibliotheken sollten Browser-Mocks bereitgestellt werden. Im Folgenden finden Sie ein Beispiel für eine Webpack-Konfigurationsdatei mit den erforderlichen Mock-Einstellungen.

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  node: {
    net: "mock",
    tls: "mock"
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist")
  }
};
```
 
Dieses [Codebeispiel](https://github.com/christopheranderson/cosmos-browser-sample) verwendet TypeScript und Webpack mit der JavaScript-SDK-Bibliothek von Azure Cosmos DB zum Erstellen einer Todo-App, die Echtzeit-Updates sendet, wenn neue Elemente erstellt werden.
Als bewährte Methode empfiehlt es sich, nicht den Primärschlüssel für die Kommunikation mit Azure Cosmos DB über den Browser zu verwenden. Verwenden Sie stattdessen Ressourcentoken. Weitere Informationen zu Ressourcentoken finden Sie unter [Sichern des Zugriffs auf Daten in Azure Cosmos DB](secure-access-to-data.md#resource-tokens).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu anderen Möglichkeiten zum Schutz Ihres Azure Cosmos-Kontos finden Sie in den folgenden Artikeln:

* [Konfigurieren einer Firewall für Azure Cosmos DB](how-to-configure-firewall.md)

* [Konfigurieren des VNET- und subnetzbasierten Zugriffs für Ihr Azure Cosmos DB-Konto](how-to-configure-vnet-service-endpoint.md)
