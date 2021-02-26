---
title: Abfrageeinheiten in Azure Digital Twins
titleSuffix: Azure Digital Twins
description: Grundlegendes zum Abrechnungskonzept von Abfrageeinheiten in Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 0e1c5f08c4292e4f3dfec448d8bf54d5d5601840
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99050497"
---
# <a name="query-units-in-azure-digital-twins"></a>Abfrageeinheiten in Azure Digital Twins 

Die Azure Digital Twins-**Abfrageeinheit (Query Unit, QU)** wird für die bedarfsgesteuerte Berechnung zum Ausführen von [Azure Digital Twins-Abfragen](how-to-query-graph.md) mithilfe der [Abfrage-API](/rest/api/digital-twins/dataplane/query) verwendet. 

Sie bietet eine Abstraktion der Systemressourcen, z. B. CPU, IOPS und Arbeitsspeicher, die für die Durchführung der von Azure Digital Twins unterstützten Abfragen erforderlich sind. So können Sie die Verwendung der Ressourcen in Abfrageeinheiten nachverfolgen.

Die Menge der Abfrageeinheiten, die zum Ausführen einer Abfrage verbraucht werden, hängt ab von...

* der Komplexität der Abfrage
* der Größe des Resultsets (daher verbraucht eine Abfrage, die 10 Ergebnisse zurückgibt, mehr Abfrageeinheiten als eine Abfrage ähnlicher Komplexität, die nur ein Ergebnis zurückgibt).

In diesem Artikel werden die Ermittlung der Abfrageeinheiten und die Nachverfolgung ihres Verbrauchs erläutert.

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>Ermitteln des Verbrauchs von Abfrageeinheiten in Azure Digital Twins

Wenn Sie mit der [Abfrage-API-](/rest/api/digital-twins/dataplane/query) von Azure Digital Twins eine Abfrage ausführen, können Sie dem Antwortheader die Anzahl der von der Abfrage verbrauchten Abfrageeinheiten entnehmen. Suchen Sie in der von Azure Digital Twins zurückgesendeten Antwort nach „query-charge“.

Mit den Azure Digital Twins-[SDKs](how-to-use-apis-sdks.md) können Sie den query-charge-Header aus der auslagerbaren Antwort extrahieren. In diesem Abschnitt wird gezeigt, wie Sie digitale Zwillinge abfragen und die auslagerbare Antwort durchlaufen, um den query-charge-Header zu extrahieren. 

Der folgende Codeausschnitt veranschaulicht das Extrahieren der durch den Aufruf der Abfrage-API anfallenden Abfragegebühren. Zuerst werden die Antwortseiten durchlaufen, um auf den query-charge-Header zuzugreifen, und dann werden die Ergebnisse der digitalen Zwillinge auf den einzelnen Seiten durchlaufen. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/getQueryCharges.cs":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über das Abfragen von Azure Digital Twins finden Sie in den folgenden Artikeln:

* [*Konzepte: Abfragesprache*](concepts-query-language.md)
* [*Verwenden Abfragen des Zwillingsgraphen*](how-to-query-graph.md)
* [Abfrage-API-Referenzdokumentation](/rest/api/digital-twins/dataplane/query/querytwins)

Sie finden die abfragenbezogenen Azure Digital Twins-Einschränkungen unter [*Diensteinschränkungen*](reference-service-limits.md).