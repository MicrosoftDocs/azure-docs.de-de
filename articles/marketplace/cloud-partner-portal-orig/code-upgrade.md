---
title: Upgraden des Codes auf die neueste Plattform | Azure Marketplace
description: In diesem Thema erfahren Sie, wie Sie Ihre Microsoft Dynamics 365 for Operations-Plattform auf die neueste Plattformversion upgraden.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: aedc2c7474de0fe068a329eb2205e9bb08e62c3a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935282"
---
# <a name="upgrading-code-to-the-latest-platform"></a>Upgraden des Codes auf die neueste Plattform

In diesem Artikel erfahren Sie, wie Sie Ihre Microsoft Dynamics 365 for Operations-Plattform auf die neueste Plattformversion upgraden.

## <a name="overview"></a>Übersicht

Die Microsoft Dynamics 365 for Operations-Plattform umfasst folgende Komponenten:

Binärdateien der Dynamics 365 for Operations-Plattform wie Application Object Server (AOS), das Datenverwaltungsframework, das Berichts- und BI-Framework (Business Intelligence), Entwicklungstools und Analysedienste. Folgende AOT-Pakete (Application Object Tree):

1. Anwendungsplattform
2. Anwendungsgrundlage
3. Grundlegende Testkomponenten

**Wichtig**: Wenn Sie ein Upgrade auf die neueste Dynamics 365 for Operations-Plattform durchführen möchten, dürfen für Ihre Dynamics 365 for Operations-Implementierung keine Anpassungen (Überlagerungen) von AOT-Paketen vorhanden sein, die zu der Plattform gehören. Diese Einschränkung wurde im Plattformupdate 3 eingeführt, um nahtlose kontinuierliche Plattformupdates zu ermöglichen. Lesen Sie bei Verwendung einer älteren Plattform (vor dem Plattformupdate 3) den Abschnitt „Upgrading to platform update 3 from an earlier build“ (Upgraden eines älteren Builds auf Plattformupdate 3) am Ende dieses Artikels.

Weitere Informationen zum Codeupgrade finden Sie [hier](https://docs.microsoft.com/dynamics365/operations/dev-itpro/migration-upgrade/upgrade-latest-platform-update).