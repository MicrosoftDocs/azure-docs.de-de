---
title: Anfordern des Just-In-Time-Zugriffs
description: Beschreibt, wie Herausgeber von Azure Managed Applications den Just-In-time-Zugriff auf eine verwaltete Anwendung anfordern.
author: MSEvanhi
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: evanhi
ms.openlocfilehash: f839eb04e0c036b5059e35fac16d9c702646cbd7
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435687"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Aktivieren und Anfordern des Just-In-time-Zugriffs für Azure Managed Applications

Consumer Ihrer verwalteten Anwendung scheuen sich möglicherweise, Ihnen permanenten Zugriff auf die Gruppe der verwalteten Ressourcen zu gewähren. Als Herausgeber einer verwalteten Anwendung bevorzugen Sie es möglicherweise, dass die Consumer genau wissen, wann Sie auf die verwalteten Ressourcen zugreifen müssen. Damit Consumer eine umfassendere Kontrolle über die Erteilung des Zugriffs auf verwaltete Ressourcen haben, bietet Azure Managed Applications ein Feature namens Just-In-Time-Zugriff (JIT). Diese Funktion steht derzeit als Vorschau zur Verfügung.

Der JIT-Zugriff ermöglicht es Ihnen, erhöhte Zugriffsrechte auf die Ressourcen einer verwalteten Anwendung zur Problembehandlung oder Wartung anzufordern. Sie verfügen immer über Lesezugriff auf die Ressourcen, aber für einen bestimmten Zeitraum können Sie über erhöhte Zugriffsrechte verfügen.

Der Workflow für die Erteilung des Zugriffs ist:

1. Sie fügen dem Marketplace eine verwaltete Anwendung hinzu und geben an, dass der JIT-Zugriff verfügbar ist.

1. Während der Bereitstellung aktiviert der Consumer den JIT-Zugriff für diese Instanz der verwalteten Anwendung.

1. Nach der Bereitstellung kann der Consumer die Einstellungen für den JIT-Zugriff ändern.

1. Sie senden eine Zugriffsanforderung, wenn Sie für die verwalteten Ressourcen die Problembehandlung oder Aktualisierung durchführen müssen.

1. Der Consumer genehmigt Ihre Anforderung.

Dieser Artikel konzentriert sich auf die Maßnahmen, die Herausgeber durchführen, um den JIT-Zugriff zu ermöglichen und Anforderungen zu übermitteln. Informationen zum Genehmigen von JIT-Zugriffsanforderungen finden Sie unter [Genehmigen des Just-In-time-Zugriffs in Azure Managed Applications](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Hinzufügen des Schritts für den JIT-Zugriff zur Benutzeroberfläche

Nehmen Sie in die Datei „CreateUiDefinition.json“ einen Schritt auf, in dem Consumer JIT-Zugriff aktivieren können. Damit Ihr Angebot die JIT-Funktionen unterstützt, fügen Sie den folgenden Inhalt zu Ihrer Datei „CreateUiDefinition.json“ hinzu:

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
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> Der JIT-Zugriff befindet sich in der Vorschauversion. Das Schema für die JIT-Konfiguration könnte sich in zukünftigen Versionen ändern.

## <a name="enable-jit-access"></a>Aktivieren des JIT-Zugriffs

Aktivieren Sie beim Erstellen Ihres Angebots in Partner Center unbedingt JIT-Zugriff.

1. Melden Sie sich in [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) beim kommerziellen Marketplace-Portal an.

1. Anleitungen zum Erstellen einer neuen verwalteten Anwendung finden Sie unter [Erstellen eines Azure-Anwendungsangebots](../../marketplace/create-new-azure-apps-offer.md).

1. Aktivieren Sie auf der Seite **Technische Konfiguration** das Kontrollkästchen **Just-in-Time-Zugriff (JIT) aktivieren**.

   :::image type="content" source="./media/request-just-in-time-access/enable-just-in-time-access.png" alt-text="Aktivieren des Just-in-Time-Zugriffs":::

Sie haben Ihrer Benutzeroberfläche einen JIT-Konfigurationsschritt hinzugefügt und den JIT-Zugriff für das Angebot im kommerziellen Marketplace aktiviert. Wenn Consumer Ihre verwaltete Anwendung bereitstellen, können sie den [JIT-Zugriff für ihre Instanz aktivieren](approve-just-in-time-access.md#enable-during-deployment).

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
