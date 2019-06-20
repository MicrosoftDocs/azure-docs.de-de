---
title: Aktivieren und Anfordern des Just-In-time-Zugriffs für Azure Managed Applications
description: Beschreibt, wie Herausgeber von Azure Managed Applications den Just-In-time-Zugriff auf eine verwaltete Anwendung anfordern.
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: ea933f5382cb42c01de523326b094c1813401132
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482044"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Aktivieren und Anfordern des Just-In-time-Zugriffs für Azure Managed Applications

Consumer Ihrer verwalteten Anwendung scheuen sich möglicherweise, Ihnen permanenten Zugriff auf die Gruppe der verwalteten Ressourcen zu gewähren. Als Herausgeber einer verwalteten Anwendung bevorzugen Sie es möglicherweise, dass die Consumer genau wissen, wann Sie auf die verwalteten Ressourcen zugreifen müssen. Damit Consumer eine umfassendere Kontrolle über die Erteilung des Zugriffs auf verwaltete Ressourcen haben, bietet Azure Managed Applications ein Feature namens Just-In-time-Zugriff (JIT), das sich derzeit in der Vorschauversion befindet.

Der JIT-Zugriff ermöglicht es Ihnen, erhöhte Zugriffsrechte auf die Ressourcen einer verwalteten Anwendung zur Problembehandlung oder Wartung anzufordern. Sie verfügen immer über Lesezugriff auf die Ressourcen, aber für einen bestimmten Zeitraum können Sie über erhöhte Zugriffsrechte verfügen.

Der Workflow für die Erteilung des Zugriffs ist:

1. Sie fügen dem Marketplace eine verwaltete Anwendung hinzu und geben an, dass der JIT-Zugriff verfügbar ist.

1. Während der Bereitstellung aktiviert der Consumer den JIT-Zugriff für diese Instanz der verwalteten Anwendung.

1. Nach der Bereitstellung kann der Consumer die Einstellungen für den JIT-Zugriff ändern.

1. Sie senden eine Zugriffsanforderung, wenn Sie für die verwalteten Ressourcen die Problembehandlung oder Aktualisierung durchführen müssen.

1. Der Consumer genehmigt Ihre Anforderung.

Dieser Artikel konzentriert sich auf die Maßnahmen, die Herausgeber durchführen, um den JIT-Zugriff zu ermöglichen und Anforderungen zu übermitteln. Informationen zum Genehmigen von JIT-Zugriffsanforderungen finden Sie unter [Genehmigen des Just-In-time-Zugriffs in Azure Managed Applications](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Hinzufügen des Schritts für den JIT-Zugriff zur Benutzeroberfläche

Ihre „CreateUiDefinition.json“-Datei entspricht genau der Datei der Benutzeroberfläche, die Sie für den permanenten Zugriff erstellen, mit der Ausnahme, dass Sie einen Schritt hinzufügen müssen, mit dem Consumer den JIT-Zugriff aktivieren können. Weitere Informationen zum Veröffentlichen Ihres ersten Angebots für verwaltete Anwendungen im Azure Marketplace finden Sie unter [Azure Managed Applications im Marketplace](publish-marketplace-app.md).

Damit Ihr Angebot die JIT-Funktionen unterstützt, fügen Sie den folgenden Inhalt zu Ihrer „CreateUiDefinition.json“-Datei hinzu:

Unter „steps“ (Schritte):

```json
{
    "name": "jitConfiguration",
    "label": "JIT Configuration",
    "subLabel": {
        "preValidation": "Configure JIT settings for your application",
        "postValidation": "Done"
    },
    "bladeTitle": "JIT Configuration",
    "elements": [
        {
          "name": "jitConfigurationControl",
          "type": "Microsoft.Solutions.JitConfigurator",
          "label": "JIT Configuration"
        }
    ]
}
```
 
Unter „outputs“ (Ausgaben):

```json
"jitAccessPolicy": "[parse(concat('{\"jitAccessEnabled\":', string(steps('jitConfiguration').jitConfigurationControl.jitEnabled), ',\"jitApprovalMode\":\"', steps('jitConfiguration').jitConfigurationControl.jitApprovalMode, '\",\"maximumJitAccessDuration\":\"', steps('jitConfiguration').jitConfigurationControl.maxAccessDuration, '\",\"jitApprovers\":', string(steps('jitConfiguration').jitConfigurationControl.approvers), '}'))]"
```

> [!NOTE]
> Der JIT-Zugriff befindet sich in der Vorschauversion. Das Schema für die JIT-Konfiguration könnte sich in zukünftigen Versionen ändern.

## <a name="enable-jit-access"></a>Aktivieren des JIT-Zugriffs

Achten Sie bei der Definition Ihres Angebots im Marketplace darauf, dass Sie den JIT-Zugriff aktivieren.

1. Melden Sie sich beim [Cloud-Partnerveröffentlichungsportal](https://cloudpartner.azure.com) an.

1. Stellen Sie Werte bereit, um Ihre verwaltete Anwendung im Marketplace zu veröffentlichen. Wählen Sie **Ja** für **JIT-Zugriff aktivieren?** aus.

   ![Aktivieren des Just-in-Time-Zugriffs](./media/request-just-in-time-access/marketplace-enable.png)

Sie haben Ihrer Benutzeroberfläche einen JIT-Konfigurationsschritt hinzugefügt und den JIT-Zugriff für das Marketplace-Angebot aktiviert. Wenn Consumer Ihre verwaltete Anwendung bereitstellen, können sie den [JIT-Zugriff für ihre Instanz aktivieren](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Zugriff anfordern

Wenn Sie auf die verwalteten Ressourcen des Consumers zugreifen müssen, senden Sie eine Anforderung für eine bestimmte Rolle, Zeit und Dauer. Der Consumer muss dann die Anforderung genehmigen.

So senden Sie eine JIT-Zugriffsanforderung

1. Wählen Sie **JIT-Zugriff** für die verwaltete Anwendung, auf die Sie zugreifen müssen.

1. Wählen Sie **Berechtigte Rollen** und dann **Aktivieren** in der Spalte „AKTION“ für die gewünschte Rolle aus.

   ![Aktivieren der Zugriffsanforderung](./media/request-just-in-time-access/send-request.png)

1. Wählen Sie im Formular **Rolle aktivieren** eine Startzeit und Dauer aus, für die Ihre Rolle aktiv ist. Wählen Sie **Aktivieren** aus, um die Anforderung zu senden.

   ![Aktivieren des Zugriffs](./media/request-just-in-time-access/activate-access.png) 

1. Überprüfen Sie die Benachrichtigungen, um sicherzustellen, dass die neue JIT-Anforderung erfolgreich an den Consumer gesendet wurde.

   ![Benachrichtigung](./media/request-just-in-time-access/in-progress.png)

   Jetzt müssen Sie warten, bis der Consumer Ihre [Anforderung genehmigt](approve-just-in-time-access.md#approve-requests) hat.

1. Um den Status aller JIT-Anforderungen für eine verwaltete Anwendung anzuzeigen, wählen Sie **JIT-Zugriff** und **Anforderungsverlauf** aus.

   ![Anzeigen des Status](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Bekannte Probleme

Die Prinzipal-ID des Kontos, das den JIT-Zugriff anfordert, muss in der Definition der verwalteten Anwendung explizit angegeben werden. Das Konto kann nicht nur über eine Gruppe einbezogen werden, die im Paket angegeben ist. Diese Einschränkung wird in einer späteren Version behoben.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Genehmigen von JIT-Zugriffsanforderungen finden Sie unter [Genehmigen des Just-In-time-Zugriffs in Azure Managed Applications](approve-just-in-time-access.md).
