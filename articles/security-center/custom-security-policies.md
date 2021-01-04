---
title: Erstellen benutzerdefinierter Sicherheitsrichtlinien in Azure Security Center | Microsoft-Dokumentation
description: Von Azure Security Center überwachte benutzerdefinierte Azure Policy-Definitionen.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 12/03/2020
ms.author: memildin
ms.openlocfilehash: 8d2b43ab57ea7a3b1dc1d13bcdea9932ccecb9dc
ms.sourcegitcommit: 65a4f2a297639811426a4f27c918ac8b10750d81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96559030"
---
# <a name="using-custom-security-policies"></a>Verwenden benutzerdefinierter Sicherheitsrichtlinien

Um Ihre Systeme und Ihre Umgebung zu sichern, generiert Azure Security Center Sicherheitsempfehlungen. Diese Empfehlungen basieren auf bewährten Methoden der Branche, die in die generische Standardsicherheitsrichtlinie integriert sind, die für alle Kunden bereitgestellt wird. Sie können auch aus Branchenwissen von Security Center und regulatorische Normen stammen.

Mit diesem Feature können Sie eigene *benutzerdefinierte* Initiativen hinzufügen. Sie erhalten dann Empfehlungen, wenn Ihre Umgebung die von Ihnen erstellten Richtlinien nicht einhält. Alle von Ihnen erstellten benutzerdefinierten Initiativen werden neben den integrierten Initiativen im Compliance-Dashboard angezeigt, wie im Tutorial [Verbessern der Einhaltung gesetzlicher Vorschriften](security-center-compliance-dashboard.md) beschrieben wird.

Wie in der [Azure Policy-Dokumentation](../governance/policy/concepts/definition-structure.md#definition-location) erläutert, müsse Sie als Speicherort für Ihre benutzerdefinierte Initiative eine Verwaltungsgruppe oder ein Abonnement angeben. 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>So fügen Sie eine benutzerdefinierte Initiative zu Ihrem Abonnement hinzu 

1. Öffnen Sie auf der Security Center-Randleiste die Seite **Sicherheitsrichtlinie**.

1. Wählen Sie ein Abonnement oder eine Verwaltungsgruppe aus, dem bzw. der Sie eine benutzerdefinierte Initiative hinzufügen möchten.

    [![Auswählen eines Abonnements, für das Sie die benutzerdefinierte Richtlinie erstellen](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Sie müssen benutzerdefinierte Standards auf Abonnementebene (oder höher) hinzufügen, damit diese ausgewertet und in Security Center angezeigt werden. 
    >
    > Wenn Sie einen benutzerdefinierten Standard hinzufügen, wird diesem Bereich eine *Initiative* zugewiesen. Daher empfiehlt es sich, den größtmöglichen Bereich auszuwählen, der für diese Zuweisung erforderlich.

1. Klicken Sie auf der Seite „Sicherheitsrichtlinie“ unter „Ihre benutzerdefinierten Initiativen“ auf **Benutzerdefinierte Initiative hinzufügen**.

    [![Klicken Sie auf „Benutzerdefinierte Initiative hinzufügen“.](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    Die folgende Seite wird angezeigt:

    ![Erstellen oder Hinzufügen einer Richtlinie](media/custom-security-policies/create-or-add-custom-policy.png)

1. Überprüfen Sie auf der Seite „Benutzerdefinierte Initiativen hinzufügen“ die Liste der benutzerdefinierten Richtlinien aus, die in Ihrer Organisation bereits erstellt wurden. Sehen Sie eine, die Sie Ihrem Abonnement zuweisen möchten, klicken Sie auf **Hinzufügen**. Wenn in der Liste keine Initiative enthalten ist, die Ihren Anforderungen entspricht, überspringen Sie diesen Schritt.

1. So erstellen Sie eine neue benutzerdefinierte Initiative

    1. Klicken Sie auf **Neu erstellen**.
    1. Geben Sie den Speicherort und den Namen der Definition ein.
    1. Wählen Sie die einzuschließenden Richtlinien aus, und klicken Sie auf **Hinzufügen**.
    1. Geben Sie die gewünschten Parameter ein.
    1. Klicken Sie auf **Speichern**.
    1. Klicken Sie auf der Seite „Benutzerdefinierte Initiativen hinzufügen“ auf „Aktualisieren“. Ihre neue Initiative wird als verfügbar angezeigt.
    1. Klicken Sie auf **Hinzufügen**, und weisen Sie sie Ihrem Abonnement zu.

    > [!NOTE]
    > Zum Erstellen neuer Initiativen sind Abonnementbesitzer-Anmeldeinformationen erforderlich. Weitere Informationen zu Azure-Rollen finden Sie unter [Berechtigungen in Azure Security Center](security-center-permissions.md).

    Ihre neue Initiative tritt in Kraft, und Sie können die Auswirkung auf die folgenden beiden Arten anzeigen:

    * Wählen Sie in der Randleiste von Security Center unter „Richtlinie und Konformität“ die Option **Einhaltung gesetzlicher Bestimmungen** aus. Das Compliance-Dashboard wird geöffnet und zeigt Ihre neue benutzerdefinierte Initiative neben den integrierten Initiativen an.
    
    * Sie erhalten dann Empfehlungen, wenn Ihre Umgebung die von Ihnen definierten Richtlinien nicht einhält.

1. Um die resultierenden Empfehlungen für Ihre Richtlinie anzuzeigen, klicken Sie in der Randleiste auf **Empfehlungen**, um die Seite „Empfehlungen“ zu öffnen. Die Empfehlungen werden mit der Bezeichnung „Benutzerdefiniert“ angezeigt und sind innerhalb einer Stunde verfügbar.

    [![Benutzerdefinierte Empfehlungen](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

## <a name="enhance-your-custom-recommendations-with-detailed-information"></a>Verbessern der benutzerdefinierten Empfehlungen mit ausführlichen Informationen

Die integrierten Empfehlungen, die mit Azure Security Center bereitgestellt werden, enthalten Details wie Schweregrade und Korrekturanweisungen. Wenn Sie Ihren benutzerdefinierten Empfehlungen diese Art von Informationen hinzufügen möchten, damit Sie im Azure-Portal oder wenn Sie auf Ihre Empfehlungen zugreifen angezeigt werden, müssen Sie die REST-API verwenden. 

Folgende zwei Arten von Informationen können Sie hinzufügen:

- **RemediationDescription**: Zeichenfolge
- **Severity**: Enumeration [Niedrig, Mittel, Hoch]

Die Metadaten sollten der Richtliniendefinition für eine Richtlinie hinzugefügt werden, die Teil der benutzerdefinierten Initiative ist. Sie sollten in der Eigenschaft „securityCenter“ vorliegen, wie hier gezeigt:

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High"
    },
```

Im Folgenden finden Sie ein Beispiel für eine benutzerdefinierte Richtlinie mit der Eigenschaft metadata/securityCenter:

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "RemediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "Severity": "High"
        }
    },
    "parameters": {
        "expressRouteLockLevel": {
            "type": "String",
            "metadata": {
                "displayName": "Lock level",
                "description": "Required lock level for ExpressRoute resource groups."
            },
            "allowedValues": [
                "CanNotDelete",
                "ReadOnly"
            ]
        }
    },
    "policyRule": {
        "if": {
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        "then": {
            "effect": "auditIfNotExists",
            "details": {
                "type": "Microsoft.Authorization/locks",
                "existenceCondition": {
                    "field": "Microsoft.Authorization/locks/level",
                    "equals": "[parameters('expressRouteLockLevel')]"
                }
            }
        }
    }
}
}
  ```

Ein weiteres Beispiel für die Verwendung der securityCenter-Eigenschaft finden Sie in [diesem Abschnitt der REST-API-Dokumentation](/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples).


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie benutzerdefinierte Sicherheitsrichtlinien erstellt werden. 

Weitere verwandte Informationen finden Sie in den folgenden Artikeln: 

- [Übersicht über Sicherheitsrichtlinien](tutorial-security-policy.md)
- [Liste der integrierten Sicherheitsrichtlinien](./policy-reference.md)