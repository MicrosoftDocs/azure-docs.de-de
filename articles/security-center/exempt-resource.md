---
title: Ausschließen einer Ressource aus Azure Security Center-Sicherheitsempfehlungen und der Sicherheitsbewertung
description: Es wird beschrieben, wie Sie eine Ressource aus den Sicherheitsempfehlungen und der Sicherheitsbewertung ausschließen.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: b7780a0ef70a89a88070d5883cc840319a67fa3d
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96122361"
---
# <a name="exempt-a-resource-from-recommendations-and-secure-score"></a>Ausschließen einer Ressource aus Empfehlungen und der Sicherheitsbewertung

Für jedes Sicherheitsteam hat die Sicherstellung, dass sich die Analysten auf die für die Organisation wichtigen Aufgaben und Incidents konzentrieren können, eine sehr hohe Priorität. Security Center verfügt über viele Features zum Anpassen der Informationen, die die höchste Priorität haben. Hiermit können Sie sicherstellen, dass Ihre Sicherheitsbewertung die Sicherheitsentscheidungen Ihrer Organisation angemessen widerspiegelt. Eines dieser Features ist das Ausschließen von Ressourcen.

Wenn Sie in Azure Security Center eine Sicherheitsempfehlung untersuchen, überprüfen Sie unter anderem zunächst die Liste mit den betroffenen Ressourcen.

Es kann gelegentlich vorkommen, dass eine Ressource aufgeführt ist, die Ihrer Meinung nach nicht enthalten sein sollte. Unter Umständen wurde dafür eine Lösungsmaßnahme mit einem Prozess durchgeführt, der von Security Center nicht nachverfolgt wird. Es kann auch sein, dass Ihre Organisation die Entscheidung getroffen hat, das Risiko für die entsprechende Ressource zu akzeptieren. 

In diesen Fällen können Sie eine Ausnahmeregel erstellen und sicherstellen, dass die Ressource in Zukunft nicht mehr in der Liste mit den fehlerhaften Ressourcen enthalten ist und keine Auswirkung mehr auf Ihre Sicherheitsbewertung hat. 

Die Ressource wird als nicht zutreffend aufgeführt, und als Grund wird „Ausgenommen“ mit der von Ihnen ausgewählten Begründung angezeigt.

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Vorschau<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|Preise:|Dies ist eine Premium-Richtlinienfunktion von Azure, die für Azure Defender-Kunden kostenlos zur Verfügung steht. Für andere Benutzer fallen hierfür in Zukunft unter Umständen Gebühren an.|
|Erforderliche Rollen und Berechtigungen:|**Abonnementbesitzer** oder **Mitwirkender an Richtlinien** für die Erstellung einer Ausnahme<br>Zum Erstellen einer Regel benötigen Sie Berechtigungen zum Bearbeiten von Richtlinien in Azure Policy.<br>Weitere Informationen finden Sie unter [Azure RBAC-Berechtigungen in Azure Policy](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy).|
|Clouds:|![Ja](./media/icons/yes-icon.png) Kommerzielle Clouds<br>![Nein](./media/icons/no-icon.png) National/Sovereign (US Gov, China Gov, andere Gov)|
|||


## <a name="create-an-exemption-rule"></a>Erstellen einer Ausnahmeregel

1. Wählen Sie in der Liste mit den fehlerhaften Ressourcen für die Ressource, die Sie ausschließen möchten, das Menü über die Auslassungszeichen („...“) aus.

    :::image type="content" source="./media/exempt-resource/create-exemption.png" alt-text="Option „Ausnahme erstellen“ im Kontextmenü":::

    Der Bereich „Ausnahme erstellen“ wird geöffnet.

    :::image type="content" source="./media/exempt-resource/exemption-rule-options.png" alt-text="Bereich „Ausnahme erstellen“":::

1. Geben Sie Ihre Kriterien ein, und wählen Sie ein Kriterium als Grund aus, warum die Ressource ausgeschlossen werden soll:
    - **Entschärft**: Dieses Problem ist für die Ressource nicht relevant, weil es mit einem anderen als dem vorgeschlagenen Tool oder Prozess verarbeitet wurde.
    - **Verzicht**: Sie akzeptieren das Risiko für die Ressource.
1. Wählen Sie **Speichern** aus.
1. Nach Ablauf des Wartezeitraums (kann bis zu 24 Stunden dauern):
    - Die Ressource wirkt sich nicht mehr auf Ihre Sicherheitsbewertung aus.
    - Die Ressource ist auf der Seite mit den Empfehlungsdetails auf der Registerkarte **Nicht anwendbar** aufgeführt.
    - In der Informationsleiste am oberen Rand der Seite mit den Empfehlungsdetails ist die Anzahl von ausgeschlossenen Ressourcen angegeben:
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="Anzahl von ausgeschlossenen Ressourcen":::

1. Öffnen Sie die Registerkarte **Nicht anwendbar**, um Ihre ausgeschlossenen Ressourcen anzuzeigen.

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="Ändern einer Ausnahme":::

    Der Grund für die einzelnen Ausnahmen ist jeweils in der Tabelle angegeben (1).

    Wählen Sie über die Auslassungszeichen („...“) das Menü aus (wie unter Punkt 2 dargestellt), um eine Ausnahme zu ändern oder zu löschen.


## <a name="review-all-of-the-exemption-rules-on-your-subscription"></a>Überprüfen aller Ausnahmeregeln Ihres Abonnements

Für Ausnahmeregeln wird Azure Policy verwendet, um in der Richtlinienzuweisung eine Ausnahme für die Ressource zu erstellen.

Mit Azure Policy können Sie Ihre gesamten Ausnahmen über die Seite **Ausnahme** nachverfolgen:

:::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Azure Policy-Seite „Ausnahme“":::



## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde beschrieben, wie Sie eine Ressource aus einer Empfehlung ausschließen, damit sie sich nicht mehr auf Ihre Sicherheitsbewertung auswirkt. Weitere Informationen zur Sicherheitsbewertung finden Sie unter:

- [Secure Score in Azure Security Center](secure-score-security-controls.md)