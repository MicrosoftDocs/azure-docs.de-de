---
title: Einrichten der Migration von Media Services v2 zu v3
description: Dieser Artikel unterstützt Sie beim Einrichten Ihrer Umgebung für die Migration von Azure Media Services v2 zu v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, Migration, Streamen, Übertragen, live, SDK
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2021
ms.author: inhenkel
ms.openlocfilehash: 1f9e0816a3533008a01f26ca7c0e712abfa23e8e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946238"
---
# <a name="step-3---set-up-to-migrate-to-the-v3-rest-api-or-client-sdk"></a>Schritt 3: Einrichten der Migration zur V3-REST-API oder zum Client-SDK

![Logo des Migrationsleitfadens](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![Schritte bei der Migration 2](./media/migration-guide/steps-3.svg)

Im Folgenden werden die Schritte beschrieben, mit denen Sie Ihre Umgebung für die Verwendung der Media Services V3-API einrichten.

## <a name="sdk-model"></a>SDK-Modell

In der V2-API gab es zwei verschiedene Client-SDKs: eines für die Verwaltungs-API, das die programmgesteuerte Erstellung von Konten ermöglichte, und eines für die Ressourcenverwaltung.

Bisher nutzten Entwickler die Client-ID und den geheimen Clientschlüssel eines Azure-Dienstprinzipals für einen bestimmten Endpunkt der V2-REST-API für ihr AMS-Konto.

Bei der V3-API bildet Azure Resource Manager (ARM) die Basis. Die API verwendet die IDs und Schlüssel des Azure Active Directory-Dienstprinzipals (Azure AD), um eine Verbindung mit der API herzustellen. Entwickler müssen Dienstprinzipale oder verwaltete Identitäten erstellen, um eine Verbindung mit der API herzustellen. In der V3-API verwendet die API Standard-ARM-Endpunkte und damit ein ähnliches (und somit konsistentes) Modell wie alle anderen Azure-Dienste.

Kunden, die zuvor Version 2015-10-01 der ARM-Verwaltungs-API zum Verwalten Ihrer V2-Konten verwendet haben, sollten auf Version 2020-05-01 der ARM-Verwaltungs-API umsteigen, die für den Zugriff auf die V3-API unterstützt wird.

## <a name="create-a-new-media-services-account-for-testing"></a>Erstellen eines neuen Media Services-Konto für Tests

Befolgen Sie die Schritte im Schnellstart zum [Einrichten Ihrer Umgebung](how-to-set-azure-subscription.md?tabs=portal) mithilfe des Azure-Portals. Wählen Sie den API-Zugriff und die Authentifizierung per Dienstprinzipal aus, um eine neue Azure AD-Anwendungs-ID und Geheimnisse für die Verwendung mit diesem Testkonto zu generieren.

[Erstellen Sie ein Media Services-Konto.](create-account-howto.md?tabs=portal)
[Rufen Sie die Anmeldeinformationen für den Zugriff auf die Media Services-API ab.](access-api-howto.md?tabs=portal)

## <a name="download-client-sdk-of-your-choice-and-set-up-your-environment"></a>Herunterladen des gewünschten Client-SDK und Einrichten Ihrer Umgebung

- Es sind SDKs für [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet&preserve-view=true), .NET Core, [Node.js](https://docs.microsoft.com/javascript/api/overview/azure/mediaservices/management?view=azure-node-latest&preserve-view=true), [Python](https://docs.microsoft.com/python/api/overview/azure/mediaservices/management?view=azure-python&preserve-view=true), [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices/management?view=azure-java-stable&preserve-view=true), [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media) und [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md) verfügbar.
- Die Integration in die [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest&preserve-view=true) sorgt für eine einfache Skriptunterstützung.

> [!NOTE]
> Ein PHP SDK der Community ist für Azure Media Services V3 nicht mehr verfügbar. Wenn Sie PHP unter V2 verwenden, sollten Sie direkt in Ihrem Code zur REST-API migrieren.

## <a name="open-api-specifications"></a>Spezifikation der offenen API

- V3 basiert auf einer vereinheitlichten API-Oberfläche, die sowohl Verwaltungs- als auch Betriebsfunktionen auf der Basis von Azure Resource Manager bereitstellt. Azure Resource Manager-Vorlagen können zum Erstellen und Bereitstellen von Transformationen, Streamingendpunkten, Liveereignissen und mehr verwendet werden.

- Im Dokument der [OpenAPI-Spezifikation](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01) (ehemals Swagger) wird das Schema für alle Dienstkomponenten erläutert.

- Alle Client-SDKs sind von der auf GitHub veröffentlichten Spezifikation der offenen API abgeleitet und wurden mit dieser generiert. Zum Zeitpunkt der Veröffentlichung dieses Artikels werden die neuesten Spezifikationen der offenen API öffentlich auf GitHub verwaltet. Version 2020-05-01 ist das [neueste stabile Release](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01).

## <a name="rest"></a>[REST](#tab/rest)

Verwenden Sie [Postman](https://docs.microsoft.com/azure/media-services/latest/media-rest-apis-with-postman) für Aufrufe der REST-API von Media Services V3.
Lesen Sie die [REST-API-Referenzseiten](https://docs.microsoft.com/rest/api/media/).

Sie sollten die Versionszeichenfolge „2020-05-01“ in der Postman-Sammlung verwenden.

## <a name="net"></a>[.NET](#tab/net)

Lesen Sie den Artikel [Verbinden mit der Media Services V3-API – .NET](configure-connect-dotnet-howto.md), um Ihre Umgebung einzurichten.

Wenn Sie nur das neueste SDK mit PackageManager installieren möchten, verwenden Sie den folgenden Befehl:

```Install-Package Microsoft.Azure.Management.Media```

Sie können auch das neueste SDK mithilfe der .NET-Befehlszeilenschnittstelle installieren. Verwenden Sie dazu den folgenden Befehl:

```dotnet add package Microsoft.Azure.Management.Media```

Außerdem sind in [Azure-Samples/media-services-v3-dotnet](https://github.com/Azure-Samples/media-services-v3-dotnet) vollständige .NET-Beispiele für verschiedene Szenarien verfügbar. Die Projekte in diesem Repository veranschaulichen, wie unterschiedliche Azure Media Services-Szenarien mithilfe von V3 implementiert werden.

### <a name="get-started-adjusting-your-code"></a>Erste Schritte bei der Anpassung Ihres Codes

Durchsuchen Sie Ihre Codebasis nach Instanzen von `CloudMediaContext`, um den Upgradevorgang auf die V3-API zu starten.

Der folgende Code zeigt, wie bisher mit Version 2 des .NET SDK auf die V2-API zugegriffen wurde. Entwickler erstellen zunächst einen `CloudMediaContext` und eine Instanz mit einem `AzureAdTokenCredentials`-Objekt.

```dotnet

class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

```

## <a name="java"></a>[Java](#tab/java)

Lesen Sie den Artikel [Verbinden mit der Media Services V3-API – Java](configure-connect-java-howto.md), um Ihre Umgebung einzurichten.

## <a name="python"></a>[Python](#tab/python)

Lesen Sie den Artikel [Verbinden mit der Media Services V3-API – Python](configure-connect-python-howto.md), um Ihre Umgebung einzurichten.

## <a name="nodejs"></a>[Node.js](#tab/nodejs)

Lesen Sie den Artikel [Verbinden mit der Media Services V3-API – Node.js](configure-connect-nodejs-howto.md), um Ihre Umgebung einzurichten.

## <a name="ruby"></a>[Ruby](#tab/ruby)

- Rufen Sie das SDK für [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md) von GitHub ab.

## <a name="go"></a>[Go](#tab/go)

Laden Sie das SDK für [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media) herunter.

---

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]