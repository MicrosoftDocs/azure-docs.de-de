---
title: Behandeln von Problemen mit Azure Automation-Konten
description: In diesem Artikel erfahren Sie, wie Sie Probleme mit einem Azure-Konto behandeln und beheben können.
services: automation
ms.subservice: ''
ms.date: 03/24/2020
ms.topic: troubleshooting
ms.openlocfilehash: 06c15136e9d2fabdf50031c8b4be455cf2f7bbca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896578"
---
# <a name="troubleshoot-azure-automation-account-issues"></a>Behandeln von Problemen mit Azure Automation-Konten

In diesem Artikel werden Lösungen für Probleme beschrieben, die bei Verwendung eines Azure Automation-Kontos auftreten können. Allgemeine Informationen zu Automation-Konten finden Sie unter [Übersicht über die Azure Automation-Kontoauthentifizierung](../automation-security-overview.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Szenario: Der Automation-Ressourcenanbieter für Abonnements kann nicht registriert werden.

### <a name="issue"></a>Problem

Beim Arbeiten mit Verwaltungsfeatures, wie z. B. der Updateverwaltung, in Ihrem Automation-Konto tritt der folgende Fehler auf:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Ursache

Der Automation-Ressourcenanbieter ist nicht im Abonnement registriert.

### <a name="resolution"></a>Lösung

Führen Sie im Azure-Portal die folgenden Schritte aus, um den Automation-Ressourcenanbieter zu registrieren:

1. Navigieren Sie in Ihrem Browser zum [Azure-Portal](https://portal.azure.com).

2. Wählen Sie unter **Abonnements** Ihr Abonnement aus.   

3. Wählen Sie unter **Einstellungen** die Option **Ressourcenanbieter** aus.

4. Überprüfen Sie in der Liste der Ressourcenanbieter, ob der Ressourcenanbieter **Microsoft.Automation** registriert ist.

5. Wenn der Anbieter nicht aufgeführt ist, registrieren Sie ihn, so wie unter [Beheben von Fehlern bei der Ressourcenanbieterregistrierung](../../azure-resource-manager/templates/error-register-resource-provider.md) beschrieben.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie Ihr Problem nicht mit diesem Artikel lösen konnten, besuchen Sie einen der folgenden Kanäle, um weitere Unterstützung zu erhalten:

* Erhalten Sie Antworten von Azure-Experten über [Azure-Foren](https://azure.microsoft.com/support/forums/).
* Wenden Sie sich an [@AzureSupport](https://twitter.com/azuresupport) – Dies ist das offizielle Microsoft Azure-Konto, über das die Azure-Community Zugriff auf die richtigen Ressourcen erhält: Antworten, Support und Experten.
* Erstellen Sie einen Azure-Supportfall. Wechseln Sie zur [Azure-Supportwebsite](https://azure.microsoft.com/support/options/), und wählen Sie **Support erhalten** aus.
