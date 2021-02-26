---
title: include file
description: include file
author: danielstocker
ms.service: azure-quantum
ms.topic: include
ms.date: 01/08/2021
ms.author: dasto
ms.openlocfilehash: 2106a48a583f120f8b4dde4eb32a30f1a1b1d85b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947358"
---
### <a name="provider-limits--quota"></a>Grenzwerte und Kontingente für Anbieter

Der Azure Quantum-Dienst unterstützt sowohl Erst- als auch Drittanbieter von Diensten. Drittanbieter besitzen eigene Grenzwerte und Kontingente. Benutzer können Angebote und Grenzwerte im Azure-Portal anzeigen, wenn sie Drittanbieter auf dem Blatt für Anbieter konfigurieren. 

Nachfolgend finden Sie die veröffentlichten Kontingentgrenzwerte für den Erstanbieter von Optimierungslösungen von Microsoft. 

#### <a name="learn--develop-sku"></a>SKU für Lernen und Entwickeln (Learn & Develop)

| Resource | Begrenzung |
| --- | --- |
| CPU-basierte gleichzeitige Aufträge | bis zu fünf gleichzeitige Aufträge |
| FPGA-basierte gleichzeitige Aufträge | bis zu zwei gleichzeitige Aufträge |
| CPU-basierte Solver-Stunden | 20 Stunden pro Monat  |
| FPGA-basierte Solver-Stunden | 1 Stunde pro Monat  |

Wenn Sie die SKU für Lernen und Entwickeln verwenden, können Sie **keine** Erhöhung Ihrer Kontingentgrenzwerte anfordern. Stattdessen sollten Sie zur SKU „Leistung im großen Stil“ (Performance at Scale) wechseln.

#### <a name="performance-at-scale-sku"></a>SKU „Leistung im großen Stil“ (Performance at Scale)

| Resource | Standardlimit | Maximales Limit |
| --- | --- | --- |
| CPU-basierte gleichzeitige Aufträge | bis zu 100 gleichzeitige Aufträge | wie Standardgrenzwert |
| FPGA-basierte gleichzeitige Aufträge | bis zu 10 gleichzeitige Aufträge | wie Standardgrenzwert |
| Solver-Stunden | 1\.000 Stunden pro Monat  | bis zu 50.000 Stunden pro Monat |

Wenn Sie eine Erhöhung des Grenzwerts anfordern müssen, wenden Sie sich an den Azure-Support. 

Weitere Informationen finden Sie in der [Preisübersicht zu Azure Quantum](https://aka.ms/AQ/Pricing).
Informationen zu Angeboten von Drittanbietern finden Sie auf der entsprechenden Anbieterseite im Azure-Portal.
