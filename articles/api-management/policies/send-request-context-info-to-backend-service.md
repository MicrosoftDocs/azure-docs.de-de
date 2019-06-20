---
title: Beispiel für eine Azure API Management-Richtlinie –Senden von Anforderungskontextinformationen an den Back-End-Dienst | Microsoft-Dokumentation
description: 'Beispiel für eine Azure API Management-Richtlinie: Veranschaulicht, wie Anforderungskontextinformationen an den Back-End-Dienst gesendet werden.'
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 3369f3b3349e8daf9ff540b824c10bbd618a1147
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60859952"
---
# <a name="send-request-context-information-to-the-backend-service"></a>Senden von Anforderungskontextinformationen an den Back-End-Dienst

Dieser Artikel enthält ein Beispiel für eine Azure API Management-Richtlinie, das veranschaulicht, wie Anforderungskontextinformationen an den Back-End-Dienst gesendet werden. Um den Code einer Richtlinie festzulegen oder zu bearbeiten, führen Sie die Schritte unter [How to set or edit Azure API Management policies](../set-edit-policies.md) (Festlegen oder Bearbeiten von Azure API Management-Richtlinien) aus. Weitere Beispiele finden Sie unter [API Management-Richtlinienbeispiele](../policy-samples.md).

## <a name="policy"></a>Richtlinie

Fügen Sie den Code in den Block **inbound** ein.

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu APIM-Richtlinien:

+ [Transformationsrichtlinien](../api-management-transformation-policies.md)
+ [API Management-Richtlinienbeispiele](../policy-samples.md)

