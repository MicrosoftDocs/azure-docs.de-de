---
title: Authentication
titleSuffix: Azure Cognitive Services
description: 'Es gibt drei Möglichkeiten zum Authentifizieren einer Anforderung an eine Azure Cognitive Services-Ressource: einen Abonnementschlüssel, ein Bearertoken und ein Abonnement für mehrere Dienste. In diesem Artikel erfahren Sie mehr über die einzelnen Methoden und das Ausführen einer Anforderung.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: erhopf
ms.openlocfilehash: 06cc5509598cb6227a64e2cf375a5686c89312eb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67163675"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Authentifizieren von Anforderungen an Azure Cognitive Services

Jede Anforderung an Azure Cognitive Service muss einen Authentifizierungsheader enthalten. Dieser Header übergibt einen Abonnementschlüssel oder ein Zugriffstoken, mit dem Ihr Abonnement für einen Dienst oder eine Gruppe von Diensten überprüft wird. In diesem Artikel lernen Sie die drei Möglichkeiten zum Authentifizieren von Anforderungen und die jeweiligen Voraussetzungen kennen.

* [Authentifizieren mit einem Schlüssel für ein Abonnement für einen einzelnen Dienst](#authenticate-with-a-single-service-subscription-key)
* [Authentifizieren mit einem Schlüssel für ein Abonnement für mehrere Dienste](#authenticate-with-a-multi-service-subscription-key)
* [Authentifizieren mit einem Token](#authenticate-with-an-authentication-token)

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie eine Anforderung übermitteln können, benötigen Sie ein Azure-Konto und ein Azure Cognitive Services-Abonnement. Wenn Sie bereits über ein Konto verfügen, können Sie mit dem nächsten Abschnitt fortfahren. Wenn Sie noch kein Konto haben, sind Sie mit der folgenden Anleitung in wenigen Minuten startbereit: [Erstellen eines Cognitive Services-Kontos für Azure](cognitive-services-apis-create-account.md).

Sie können Ihren Abonnementschlüssel über das [Azure-Portal](cognitive-services-apis-create-account.md#access-your-resource) abrufen, nachdem Sie Ihr Konto erstellt oder eine [kostenlose Testversion](https://azure.microsoft.com/try/cognitive-services/my-apis) aktiviert haben.

## <a name="authentication-headers"></a>Authentifizierungsheader

Betrachten wir zunächst kurz die verfügbaren Authentifizierungsheader für die Verwendung mit Azure Cognitive Services.

| Header | BESCHREIBUNG |
|--------|-------------|
| Ocp-Apim-Subscription-Key | Verwenden Sie diesen Header für die Authentifizierung mit einem Abonnementschlüssel für einen bestimmten Dienst oder für mehrere Dienste. |
| Ocp-Apim-Subscription-Region | Dieser Header ist nur bei Verwendung eines Schlüssels zu einem Abonnement für mehrere Dienste mit der [Textübersetzungs-API](./Translator/reference/v3-0-reference.md) erforderlich. Verwenden Sie diesen Header, um die Abonnementregion anzugeben. |
| Autorisierung | Verwenden Sie diesen Header, wenn Sie ein Authentifizierungstoken verwenden. Die Schritte zum Ausführen eines Tokenaustauschs werden in den folgenden Abschnitten beschrieben. Der angegebene Wert weist folgendes Format auf: `Bearer <TOKEN>`. |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Authentifizieren mit einem Schlüssel für ein Abonnement für einen einzelnen Dienst

Die erste Option zum Authentifizieren einer Anforderung nutzt einen Abonnementschlüssel für einen bestimmten Dienst wie Textübersetzung. Die Schlüssel stehen im Azure-Portal für jede Ressource, die Sie erstellt haben, zur Verfügung. Wenn Sie einen Abonnementschlüssel zum Authentifizieren einer Anforderung verwenden möchten, müssen Sie diesen als `Ocp-Apim-Subscription-Key`-Header übergeben.

Diese Beispielanforderungen veranschaulichen die Verwendung des `Ocp-Apim-Subscription-Key`-Headers. Wenn Sie dieses Beispiel verwenden möchten, müssen Sie einen gültigen Abonnementschlüssel einfügen.

Dies ist ein Beispielaufruf der Bing-Websuche-API:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Dies ist ein Beispielaufruf der Textübersetzungs-API:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

Das folgende Video veranschaulicht die Verwendung eines Cognitive Services-Schlüssels.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Authentifizieren mit einem Schlüssel für ein Abonnement für mehrere Dienste

>[!WARNING]
> Zum derzeitigen Zeitpunkt unterstützen diese Dienste Schlüssel für mehrere Dienste **nicht**: QnA Maker, Speech Services und Custom Vision.

Bei dieser Option wird ebenfalls ein Abonnementschlüssel zum Authentifizieren von Anforderungen verwendet. Der Hauptunterschied besteht darin, dass der Abonnementschlüssel nicht an einen bestimmten Dienst gebunden ist, sondern dass ein einzelner Schlüssel zum Authentifizieren von Anforderungen für mehrere Cognitive Services-Dienste verwendet werden kann. Weitere Informationen zur regionalen Verfügbarkeit, den unterstützten Funktionen und den Preisen finden Sie unter [Cognitive Services – Preise](https://azure.microsoft.com/pricing/details/cognitive-services/).

Der Abonnementschlüssel wird in jeder Anforderung als `Ocp-Apim-Subscription-Key`-Header bereitgestellt.

[![Demonstration eines Abonnementschlüssels für mehrere Dienste für Cognitive Services](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Unterstützte Regionen

Wenn Sie für eine Anforderung an `api.cognitive.microsoft.com` einen Schlüssel zu einem Abonnement für mehrere Dienste verwenden, müssen Sie die Region in die URL einschließen. Beispiel: `westus.api.cognitive.microsoft.com`.

Wenn Sie einen Schlüssel zu einem Abonnement für mehrere Dienste mit der Textübersetzungs-API verwenden, müssen Sie die Region des Abonnements im `Ocp-Apim-Subscription-Region`-Header angeben.

Die Authentifizierung für mehrere Dienste wird in den folgenden Regionen unterstützt:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>Beispielanforderungen

Dies ist ein Beispielaufruf der Bing-Websuche-API:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Dies ist ein Beispielaufruf der Textübersetzungs-API:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Authentifizieren mit einem Authentifizierungstoken

Ein Teil von Azure Cognitive Services akzeptiert (und in einigen Fällen erfordert) ein Authentifizierungstoken. Derzeit unterstützen diese Dienste Authentifizierungstoken:

* Textübersetzungs-API
* Speech Services: Spracherkennungs-REST-API
* Speech Services: Text-to-Speech-REST-API

>[!NOTE]
> QnA Maker verwendet ebenfalls den Autorisierungsheader, erfordert allerdings einen Endpunktschlüssel. Weitere Informationen finden Sie unter [QnA Maker: Erhalten einer Antwort von der Wissensdatenbank](./qnamaker/quickstarts/get-answer-from-kb-using-curl.md).

>[!WARNING]
> Die Dienste, die Authentifizierungstoken unterstützen, können sich im Lauf der Zeit ändern. Sehen Sie in der API-Referenz zu einem Dienst nach, bevor Sie diese Authentifizierungsmethode verwenden.

Sowohl Schlüssel zu Abonnements für einen Dienst als auch die zu Abonnements für mehrere Dienste können mit Authentifizierungstoken ausgetauscht werden. Authentifizierungstoken sind zehn Minuten lang gültig.

Authentifizierungstoken werden als `Authorization`-Header in eine Anforderung eingefügt. Dem bereitgestellten Tokenwert muss `Bearer` vorangestellt werden, z.B.: `Bearer YOUR_AUTH_TOKEN`.

### <a name="sample-requests"></a>Beispielanforderungen

Verwenden Sie diese URL, um einen Abonnementschlüssel durch ein Authentifizierungstoken zu ersetzen: `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`.

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Diese Regionen für mehrere Dienste unterstützen den Tokenaustausch:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

Nach dem Erhalt eines Authentifizierungstokens müssen Sie dieses in jeder Anforderung als `Authorization`-Header übergeben. Dies ist ein Beispielaufruf der Textübersetzungs-API:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="see-also"></a>Weitere Informationen

* [Was ist Cognitive Services?](welcome.md)
* [Cognitive Services – Preise](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [Erstellen eines Kontos](cognitive-services-apis-create-account.md)
