---
title: include file
description: include file
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: fc945a7e9389c8aec48a6a1dba969fbf92002d3a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178494"
---
| Resource | Begrenzung |
| --- | --- |
| Maximale Anzahl von Skalierungseinheiten | Zehn pro Region<sup>1</sup> |
| Cachegröße | 5 GB pro Einheit<sup>2</sup> |
| Gleichzeitige Back-End-Verbindungen<sup>3</sup> pro HTTP-Autorität | 2\.048 pro Einheit<sup>4</sup> |
| Maximale Größe zwischengespeicherter Antworten | 2 MB |
| Maximale Größe eines Richtliniendokuments | 256 KB<sup>5</sup> | 
| Maximale Anzahl benutzerdefinierter Gatewaydomänen pro Dienstinstanz<sup>6</sup> | 20 |
| Maximale Anzahl von CA-Zertifikaten pro Dienstinstanz | 10 | 
| Maximale Anzahl von Instanzen pro Abonnement<sup>7</sup> | 20 | 
| Maximale Anzahl von Abonnements pro Dienstinstanz<sup>7</sup> | 500 |
| Maximale Anzahl von Clientzertifikaten pro Dienstinstanz<sup>7</sup> | 50 | 
| Maximale Anzahl von APIs pro Dienstinstanz<sup>7</sup> | 50 | 
| Maximale Anzahl von API-Vorgängen pro Dienstinstanz<sup>7</sup> | 1\.000 | 
| Maximale Gesamtdauer der Anforderungen<sup>7</sup> | 30 Sekunden | 
| Maximale gepufferte Nutzlastgröße<sup>7</sup> | 2 MB | 


<sup>1</sup>Die Skalierungsgrenzen sind vom Tarif abhängig. Die Tarife und die dazugehörigen Grenzwerte für die Skalierung finden Sie unter [API Management – Preise](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup>Die Cachegröße pro Einheit ist vom Tarif abhängig. Die Tarife und die dazugehörigen Grenzwerte für die Skalierung finden Sie unter [API Management – Preise](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup>Verbindungen werden in Pools zusammengefasst und wiederverwendet, sofern sie nicht explizit durch das Back-End geschlossen wurden.<br/>
<sup>4</sup>Dieser Grenzwert gilt pro Einheit der Tarife „Basic“, „Standard“ und „Premium“. Der Developer-Tarif ist auf 1.024 beschränkt. Dieser Grenzwert betrifft den Tarif „Consumption“ nicht.<br/> 
<sup>5</sup>Dieser Grenzwert gilt für die Tarife „Basic“, „Standard“ und „Premium“. Im Tarif „Consumption“ ist die Größe des Richtliniendokuments auf 4 KB beschränkt.<br/>
<sup>6</sup>Diese Ressource ist nur im Tarif „Premium“ verfügbar.<br/>
<sup>7</sup>Diese Ressource betrifft nur den Tarif „Consumption“.<br/>



