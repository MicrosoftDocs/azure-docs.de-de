---
title: Verbinden mit der Azure Media Services v3-API – Node.js
description: In diesem Artikel wird demonstriert, wie Sie mit Node.js eine Verbindung mit der Media Services v3-API herstellen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: c6ea238edd68413646dda59b22d1c0dc2557d57e
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94916830"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Verbinden mit der Media Services v3-API – Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Dieser Artikel zeigt Ihnen, wie Sie mit der Methode der Dienstprinzipalanmeldung eine Verbindung zum Azure Media Services v3 Node.js SDK herstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Installieren Sie [Node.js](https://nodejs.org/en/download/).
- [Erstellen Sie ein Media Services-Konto.](./create-account-howto.md) Merken Sie sich unbedingt den Namen der Ressourcengruppe und den Namen des Media Services-Kontos.

> [!IMPORTANT]
> Informieren Sie sich über die [Namenskonventionen](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>Erstellen von package.json

1. Erstellen Sie eine package.json-Datei mit Ihrem bevorzugten Editor.
1. Öffnen Sie die Datei, und fügen Sie den folgenden Code ein:

   Verwenden Sie unbedingt die aktuelle Version des [AzureMediaServices SDK für JavaScript](https://www.npmjs.com/package/@azure/arm-mediaservices).

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^8.0.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

Die folgenden Pakete sollten angegeben werden:

|Paket|BESCHREIBUNG|
|---|---|
|`azure-arm-mediaservices`|Azure Media Services SDK <br/>Um sicherzustellen, dass Sie das neueste Azure Media Services-Paket verwenden, überprüfen Sie [NPM-Installation azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|Storage SDK Wird beim Hochladen von Dateien in Medienobjekte verwendet.|
|`ms-rest-azure`| Wird zur Anmeldung verwendet.|

Sie können den folgenden Befehl ausführen, um sicherzustellen, dass Sie das neueste Paket verwenden:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Verbindung zum Node.js-Client herstellen

1. Erstellen Sie eine JS-Datei mit Ihrem bevorzugten Editor.
1. Öffnen Sie die Datei, und fügen Sie den folgenden Code ein.
1. Legen Sie die Werte im Abschnitt „Endpunktkonfiguration“ auf Werte fest, die Sie von [Zugriffs-APIs](./access-api-howto.md) erhalten haben.

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>Ausführen der App

Öffnen Sie eine Eingabeaufforderung. Navigieren Sie zum Verzeichnis des Beispiels, und führen Sie die folgenden Befehle aus:

```
npm install 
node index.js
```

## <a name="see-also"></a>Weitere Informationen

- [Media Services-Konzepte](concepts-overview.md)
- [NPM-Installation azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich mit der [Node.js-Referenzdokumentation](/javascript/api/overview/azure/mediaservices/management) für Media Services sowie mit [Beispielen](https://github.com/Azure-Samples/media-services-v3-node-tutorials) vertraut, die die Verwendung der Media Services-API mit Node.js veranschaulichen.
