---
title: include file
description: include file
services: azure-policy
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/05/2019
ms.author: dacoulte
ms.custom: include file
ms.openlocfilehash: ea87e51e66985f860cd6d10595a32facde1dc639
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133275"
---
Für jeden Objekttyp für Azure Policy gilt eine maximale Anzahl. Ein _Scope_-Eintrag (Bereich) gilt entweder für das Abonnement oder die [Verwaltungsgruppe](../articles/governance/management-groups/overview.md).

| Hierbei gilt: | Was | Maximale Anzahl |
|---|---|---|
| `Scope` | Richtliniendefinitionen | 500 |
| `Scope` | Initiativdefinitionen | 100 |
| Tenant | Initiativdefinitionen | 1\.000 |
| `Scope` | Richtlinien- oder Initiativenzuweisungen | 100 |
| Richtliniendefinition | Parameter | 20 |
| Initiativdefinition | Richtlinien | 100 |
| Initiativdefinition | Parameter | 100 |
| Richtlinien- oder Initiativenzuweisungen | Ausschlüsse (notScopes) | 400 |
| Richtlinienregel | Geschachtelte konditionelle Abschnitte | 512 |
