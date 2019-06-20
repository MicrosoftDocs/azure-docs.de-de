---
title: API-Überlegungen | Azure Marketplace
description: Versionsverwaltung, Fehlerbehandlung und Autorisierungsprobleme beim Verwenden der Marketplace-APIs.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6bf27db27daee50f78552344ae1b2b116d48a5c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935577"
---
# <a name="api-considerations"></a>API-Überlegungen


<a name="api-versioning"></a>API-Versionsverwaltung
--------------

Es gibt möglicherweise mehrere Versionen der API, die gleichzeitig verfügbar sind. Clients müssen angeben, welche Version sie aufrufen möchten. Dazu wird in ihnen der `api-version`-Parameter als Bestandteil der Abfragezeichenfolge bereitgestellt.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

Die Antwort auf eine Anforderung mit einer unbekannten oder ungültigen API-Version ist ein HTTP-Code von 400. Bei diesem Fehler wird die Auflistung der bekannten API-Versionen im Antworttext zurückgegeben.

``` json
    {
        "error”: { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Errors
------

Die API reagiert auf Fehler mit den entsprechenden HTTP-Statuscodes und ggf. zusätzlichen Informationen in der Antwort, die als JSON serialisiert ist.
Wenn Sie eine Fehlermeldung erhalten, insbesondere einen Fehler der Klasse 400, sollten Sie die Anforderung erst erneut versuchen, nachdem Sie die Fehlerursache beseitigt haben. Korrigieren Sie z. B. in der obigen Beispielantwort den API-Versionsparameter, bevor Sie die Anforderung erneut senden.

<a name="authorization-header"></a>Authorization header (Autorisierungsheader)
--------------------

Für alle APIs in dieser Referenz müssen Sie den Autorisierungsheader zusammen mit dem Bearer-Token übergeben, das aus Azure Active Directory (Azure AD) abgerufen wurde. Dieser Header ist erforderlich, um eine gültige Antwort zu empfangen. Ist er nicht vorhanden, wird ein `401 Unauthorized`-Fehler zurückgegeben. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
