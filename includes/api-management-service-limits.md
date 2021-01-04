---
title: include file
description: include file
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 04/14/2020
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: cf051d96651161658796689639b5d268230d4b5a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95556360"
---
| Resource | Begrenzung |
| ---------------------------------------------------------------------- | -------------------------- |
| Maximale Anzahl von Skalierungseinheiten | Zehn pro Region<sup>1</sup> |
| Cachegröße | 5 GiB pro Einheit<sup>2</sup> |
| Gleichzeitige Back-End-Verbindungen<sup>3</sup> pro HTTP-Autorität | 2\.048 pro Einheit<sup>4</sup> |
| Maximale Größe zwischengespeicherter Antworten | 2 MiB |
| Maximale Größe eines Richtliniendokuments | 256 KiB<sup>5</sup> |
| Maximale Anzahl benutzerdefinierter Gatewaydomänen pro Dienstinstanz<sup>6</sup> | 20 |
| Maximale Anzahl von CA-Zertifikaten pro Dienstinstanz<sup>7</sup> | 10 |
| Maximale Anzahl von Dienstinstanzen pro Abonnement<sup>8</sup> | 20 |
| Maximale Anzahl von Abonnements pro Dienstinstanz<sup>8</sup> | 500 |
| Maximale Anzahl von Clientzertifikaten pro Dienstinstanz<sup>8</sup> | 50 |
| Maximale Anzahl von APIs pro Dienstinstanz<sup>8</sup> | 50 |
| Maximale Anzahl von API-Vorgängen pro Dienstinstanz<sup>8</sup> | 1\.000 |
| Maximale Gesamtdauer der Anforderungen<sup>8</sup> | 30 Sekunden |
| Maximale gepufferte Nutzlastgröße<sup>8</sup> | 2 MiB |
| Maximale Größe der Anforderungs-URL<sup>9</sup> | 4096 Bytes |
| Maximale Anzahl von selbstgehosteten Gateways<sup>10</sup> | 25 |

<sup>1</sup>Die Skalierungsgrenzen sind vom Tarif abhängig. Details zu den Tarifen und den dazugehörigen Grenzwerten für die Skalierung finden Sie unter [API Management – Preise](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup>Die Cachegröße pro Einheit ist vom Tarif abhängig. Die Tarife und die dazugehörigen Grenzwerte für die Skalierung finden Sie unter [API Management – Preise](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup>Verbindungen werden in Pools zusammengefasst und wiederverwendet, sofern sie nicht explizit durch das Back-End geschlossen wurden.<br/>
<sup>4</sup>Dieser Grenzwert gilt pro Einheit der Tarife „Basic“, „Standard“ und „Premium“. Der Developer-Tarif ist auf 1.024 beschränkt. Dieser Grenzwert betrifft den Tarif „Consumption“ nicht.<br/>
<sup>5</sup>Dieser Grenzwert gilt für die Tarife „Basic“, „Standard“ und „Premium“. Im Tarif „Consumption“ ist die Größe des Richtliniendokuments auf 16 KiB beschränkt.<br/>
<sup>6</sup>Mehrere benutzerdefinierte Domänen werden nur in den Tarifen „Developer“ und „Premium“ unterstützt.<br/>
<sup>7</sup>Zertifizierungsstellenzertifikate werden im Tarif „Consumption“ nicht unterstützt.<br/>
<sup>8</sup>Dieser Grenzwert gilt nur für den Tarif „Consumption“. In diesen Kategorien gibt es keine Grenzwerte für andere Tarife.<br/>
<sup>9</sup> Gilt für nur den Tarif „Consumption“. Enthält eine Abfragezeichenfolge der Länge bis zu 2048 Bytes.<br/>
<sup>10</sup>Selbstgehostete Gateways werden nur in den Tarifen „Developer“ und „Premium“ unterstützt. Der Grenzwert gilt für die Anzahl von [selbstgehosteten Gatewayressourcen](/rest/api/apimanagement/2019-12-01/gateway). Wenden Sie sich an den [Support](https://azure.microsoft.com/support/options/), um diesen Grenzwert zu erhöhen. Beachten Sie, dass die Anzahl von Knoten (oder Replikaten), die einer selbstgehosteten Gatewayressource zugeordnet sind, im Premium-Tarif unbegrenzt ist und im Developer-Tarif auf einen einzelnen Knoten begrenzt ist.