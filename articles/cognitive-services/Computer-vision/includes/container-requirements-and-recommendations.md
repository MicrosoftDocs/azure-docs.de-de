---
title: Containeranforderungen und -empfehlungen
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/23/2020
ms.author: aahi
ms.openlocfilehash: 4697be519eee96778eecdf37f7b358a88ad886c6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006905"
---
> [!NOTE]
> Die Anforderungen und Empfehlungen basieren auf Benchmarks mit einer einzelnen Anforderung pro Sekunde, wobei ein 8 MB großes Bild eines gescannten Geschäftsbriefs mit 29 Zeilen und insgesamt 803 Zeichen verwendet wird.

In der folgenden Tabelle werden die minimale und empfohlene Zuordnung von Ressourcen für jeden Container für das Lesen beschrieben.

| Container | Minimum | Empfohlen |
|-----------|---------|-------------|
| Read 2.0-preview | 1 Kern, 8 GB Arbeitsspeicher |  8 Kerne, 16 GB Arbeitsspeicher |
| Read 3.2-preview | 8 Kerne, 16 GB Arbeitsspeicher | 8 Kerne, 24 GB Arbeitsspeicher |

* Jeder Kern muss eine Geschwindigkeit von mindestens 2,6 GHz aufweisen.

Kern und Arbeitsspeicher entsprechen den Einstellungen `--cpus` und `--memory`, die im Rahmen des Befehls `docker run` verwendet werden.
