---
title: Programmgesteuertes Erstellen von Azure-Abonnements
description: In diesem Artikel werden die verfügbaren Optionen für die programmgesteuerte Erstellung von Azure-Abonnements beschrieben.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/13/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 546ed24b5f9e7892f40c9d425b668f60ad705f8f
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493887"
---
# <a name="create-azure-subscriptions-programmatically"></a>Programmgesteuertes Erstellen von Azure-Abonnements

In diesem Artikel werden die verfügbaren Optionen für die programmgesteuerte Erstellung von Azure-Abonnements beschrieben.

Mit verschiedenen REST-APIs können Sie ein Abonnement für die folgenden Arten von Azure-Vereinbarungen erstellen:

- Enterprise Agreement (EA)
- Microsoft-Kundenvereinbarung (Microsoft Customer Agreement, MCA)
- Microsoft-Partnervereinbarung (MPA)

Die programmgesteuerte Erstellung zusätzlicher Abonnements für andere Arten von Vereinbarungen mit REST-APIs ist nicht möglich.

Die Anforderungen und Details der Erstellung von Abonnements unterscheiden sich je nach Vereinbarung und API-Version. Sehen Sie sich die folgenden Artikel an (wie jeweils für Ihre Situation zutreffend):

Neueste APIs:

- [Erstellen von EA-Abonnements](programmatically-create-subscription-enterprise-agreement.md)
- [Erstellen von MCA-Abonnements](programmatically-create-subscription-microsoft-customer-agreement.md)
- [Erstellen von MPA-Abonnements](programmatically-create-subscription-microsoft-partner-agreement.md)

Falls Sie noch [Vorschau-APIs](programmatically-create-subscription-preview.md) verwenden, können Sie damit auch weiterhin Abonnements erstellen. 

Darüber hinaus können Sie [Abonnements auch mit einer ARM-Vorlage erstellen](create-subscription-template.md). Eine ARM-Vorlage dient als Hilfe bei der Automatisierung des Prozesses zum Erstellen von Abonnements mit REST-APIs. 

## <a name="next-steps"></a>Nächste Schritte

* Nachdem Sie nun ein Abonnement erstellt haben, können Sie diese Vorgehensweise auch für andere Benutzer und Dienstprinzipale ermöglichen. Weitere Informationen finden Sie unter [Gewähren des Zugriffs zum Erstellen von Azure Enterprise-Abonnements (Vorschau) ](grant-access-to-create-subscription.md).
* Weitere Informationen zum Verwalten einer großen Anzahl von Abonnements mithilfe von Verwaltungsgruppen finden Sie unter [Was sind Azure-Verwaltungsgruppen?](../../governance/management-groups/overview.md).
