---
title: Fehler „Keine Abonnements gefunden“ – Anmeldung beim Azure-Portal | Microsoft-Dokumentation
description: Bietet die Lösung für das Problem, das beim Anmelden bei Azure-Portal oder Azure-Kontocenter die Fehlermeldung „Keine Abonnements gefunden“ angezeigt wird.
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: d1545298-99db-4941-8e97-f24a06bb7cb6
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 094d79026a55e651a1f67a5047dff20c769c359a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371229"
---
# <a name="no-subscriptions-found-sign-in-error-for-azure-portal-or-azure-account-center"></a>Anmeldefehler „Keine Abonnements gefunden“ für das Azure-Portal oder das Azure-Kontocenter

Wenn Sie versuchen, sich beim [Azure-Portal](https://portal.azure.com/) oder [Azure-Kontocenter](https://account.windowsazure.com/Subscriptions) anzumelden, erhalten Sie möglicherweise die Fehlermeldung „Keine Abonnements gefunden“. Dieser Artikel bietet eine Lösung dieses Problems.

## <a name="symptom"></a>Symptom

Wenn Sie versuchen, sich beim [Azure-Portal](https://portal.azure.com/) oder beim [Azure-Kontocenter](https://account.windowsazure.com/Subscriptions) anzumelden, erhalten Sie die folgende Fehlermeldung: Keine Abonnements gefunden.

## <a name="cause"></a>Ursache

Dieses Problem tritt auf, wenn Sie das falsche Verzeichnis ausgewählt haben oder Ihr Konto nicht über ausreichende Berechtigungen verfügt. 

## <a name="solution"></a>Lösung

### <a name="scenario-1-error-message-is-received-in-the-azure-portalhttpsportalazurecom"></a>Szenario 1: Die Fehlermeldung wird im [Azure-Portal](https://portal.azure.com) angezeigt.

So beheben Sie dieses Problem:

* Vergewissern Sie sich, dass das richtige Azure-Verzeichnis ausgewählt ist, indem Sie oben rechts auf Ihr Konto klicken.

  ![Auswählen des Verzeichnisses oben rechts im Azure-Portal](./media/billing-no-subscriptions-found/directory-switch.png)
* Wenn das richtige Azure-Verzeichnis ausgewählt ist, die Fehlermeldung jedoch immer noch ausgegeben wird, [weisen Sie Ihrem Konto die Besitzerrolle zu](../role-based-access-control/role-assignments-portal.md).

### <a name="scenario-2-error-message-is-received-in-the-azure-account-centerhttpsaccountwindowsazurecomsubscriptions"></a>Szenario 2: Die Fehlermeldung wird im [Azure-Kontocenter](https://account.windowsazure.com/Subscriptions) angezeigt.

Überprüfen Sie, ob das Konto, das Sie verwendet haben, das des Kontoadministrators ist. Gehen Sie folgendermaßen vor, um zu überprüfen, wer der Kontoadministrator ist:

1. Melden Sie sich bei der [Ansicht „Abonnements“ im Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) an.
1. Wählen Sie das zu überprüfende Abonnement aus, und sehen Sie unter **Einstellungen** nach.
1. Wählen Sie **Eigenschaften** aus. Der Kontoadministrator des Abonnements wird im Feld **Kontoadministrator** angezeigt.  

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).
