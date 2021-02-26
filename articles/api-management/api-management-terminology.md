---
title: Azure API Management – Terminologie | Microsoft-Dokumentation
description: Dieser Artikel enthält Definitionen für Begriffe, die für API Management spezifisch sind.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 10/11/2017
ms.author: apimpm
ms.openlocfilehash: 002ae9f99865114dd8bf52b53efc9303a0706a82
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491823"
---
# <a name="azure-api-management-terminology"></a>Azure API Management-Terminologie

Dieser Artikel enthält Definitionen für Begriffe, die für API Management (APIM) spezifisch sind.

## <a name="term-definitions"></a>Begriffsdefinitionen

* **Back-End-API** – ein HTTP-Dienst, der Ihre API und deren Vorgänge implementiert Weitere Informationen finden Sie unter [Back-Ends](backends.md).
* **Front-End-API**/**APIM-API**: Eine APIM-API hostet keine APIs, sondern erstellt Fassaden für Ihre APIs. Sie können die Fassade gemäß Ihren Anforderungen anpassen, ohne die Back-End-API ändern zu müssen. Weitere Informationen finden Sie unter [Importieren und Veröffentlichen einer API](import-and-publish.md).
* **APIM-Produkt** – Produkte enthalten eine oder mehrere APIs sowie ein Nutzungskontingent und Nutzungsbedingungen. Sie können eine Reihe von APIs einfügen und sie Entwicklern über das Entwicklerportal zur Verfügung stellen. Weitere Informationen finden Sie unter [Erstellen und Bearbeiten von Produkten](api-management-howto-add-products.md).
* **APIM-API-Vorgang** – jede APIM-API stellt Entwicklern einen Satz von Vorgängen zur Verfügung. Jede APIM-API enthält einen Verweis auf den Back-End-Dienst, der die API implementiert, und die Vorgänge der API sind den Operationen des Back-End-Diensts zugeordnet. Weitere Informationen finden Sie unter [Simulieren von API-Antworten](mock-api-responses.md).
* **Version** – manchmal möchten Sie neue oder andere API-Features für einige Benutzer veröffentlichen, während andere weiterhin die API verwenden möchten, die derzeit für sie geeignet ist. Weitere Informationen finden Sie unter [Veröffentlichen mehrerer Versionen Ihrer API](api-management-get-started-publish-versions.md).
* **Revision** – wenn Ihre API einsatzbereit ist und von Entwicklern verwendet wird, müssen Sie in der Regel bei Änderungen an der API vorsichtig sein und darauf achten, keine Aufrufe Ihrer API zu beeinträchtigen. Außerdem empfiehlt es sich, Entwickler über die vorgenommenen Änderungen zu informieren. Weitere Informationen finden Sie unter [Verwenden von Revisionen](api-management-get-started-revise-api.md).
* **Entwicklerportal** – Ihre Kunden (Entwickler) sollten das Entwicklerportal verwenden, um auf die APIs zuzugreifen. Das Entwicklerportal kann angepasst werden. Weitere Informationen finden Sie unter [Anpassen des Entwicklerportals](api-management-customize-styles.md).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Instanz](get-started-create-service-instance.md)

