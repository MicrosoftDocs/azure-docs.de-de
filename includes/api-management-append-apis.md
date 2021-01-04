---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: 2bfa356deeede1c16bd5a464ea7081132a67faf6
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183822"
---
## <a name="append-other-apis"></a>Anfügen weiterer APIs

Eine API kann sich aus APIs zusammensetzen, die von verschiedenen Diensten verfügbar gemacht werden. Hierzu zählen beispielsweise die OpenAPI-Spezifikation, eine SOAP-API, das API-Apps-Feature von Azure App Service, eine Azure-Funktionen-App, Azure Logic Apps und Azure Service Fabric.

![Importieren einer API](./media/api-management-append-apis/import.png)

Um eine andere API an eine vorhandene API anzufügen, führen Sie die folgenden Schritte aus. Wenn Sie eine andere API importieren, werden die Vorgänge an Ihre aktuelle API angefügt.

1. Navigieren Sie im Azure-Portal zu Ihrer Azure API Management-Instanz.
2. Wählen Sie im Menü links **APIs** aus.
3. Klicken Sie neben der API, die Sie an eine andere API anfügen möchten, auf **...** .
4. Wählen Sie im Dropdownmenü **Importieren** aus.
5. Wählen Sie einen Dienst aus, aus dem eine API importiert werden soll.