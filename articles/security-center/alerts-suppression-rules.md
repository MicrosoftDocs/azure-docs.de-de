---
title: Verwenden von Unterdrückungsregeln für Warnungen, um falsch positive oder andere unerwünschte Sicherheitswarnungen in Azure Security Center zu unterdrücken.
description: In diesem Artikel wird erläutert, wie Sie mithilfe der Unterdrückungsregeln von Azure Security Center unerwünschte Sicherheitswarnungen ausblenden können.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 09/10/2020
ms.service: security-center
ms.topic: how-to
ms.openlocfilehash: 1ca2ded69b0279a60d8ed83cf310a58dadf1a337
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751987"
---
# <a name="suppress-alerts-from-azure-defender"></a>Unterdrücken von Warnungen von Azure Defender

Auf dieser Seite wird erläutert, wie Sie mithilfe von Unterdrückungsregeln für Warnungen False Positives oder andere unerwünschte Sicherheitswarnungen von Azure Defender unterdrücken können.

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:| Allgemein verfügbar |
|Preise:|Kostenlos<br>(Die meisten Sicherheitswarnungen sind nur in Azure Defender verfügbar.)|
|Erforderliche Rollen und Berechtigungen:|**Sicherheitsadministrator** und **Besitzer** können Regeln erstellen und löschen.<br>**Sicherheitsleseberechtigter** und **Leser** können Regeln anzeigen.|
|Clouds:|![Ja](./media/icons/yes-icon.png) Kommerzielle Clouds<br>![Ja](./media/icons/yes-icon.png) National/Sovereign (US Gov, China Gov, andere Gov)|
|||


## <a name="what-are-suppression-rules"></a>Was sind Warnungsunterdrückungsregeln?

Die verschiedenen Azure Defender-Pläne erkennen Bedrohungen in einem bestimmten Bereich Ihrer Umgebung und generieren entsprechende Sicherheitswarnungen.

Wenn eine einzelne Warnung nicht interessant oder relevant ist, können Sie sie manuell verwerfen. Alternativ können Sie die Funktion „Unterdrückungsregeln“ verwenden, um ähnliche Warnungen zukünftig automatisch zu verwerfen. In der Regel verwenden Sie eine Unterdrückungsregel in folgenden Fällen:

- Unterdrücken von Warnungen, die Sie als falsch positiv identifiziert haben

- Unterdrücken von Warnungen, die zu oft ausgelöst werden und daher nicht nützlich sind

Mit Unterdrückungsregeln definieren Sie die Kriterien, nach denen Warnungen automatisch verworfen werden sollen.

> [!CAUTION]
> Durch das Unterdrücken von Sicherheitswarnungen wird die Effektivität des Bedrohungsschutzes von Azure Defender verringert. Sie sollten die potenziellen Auswirkungen der Unterdrückungsregeln sorgfältig überprüfen und diese über einen bestimmten Zeitraum überwachen.

:::image type="content" source="./media/alerts-suppression-rules/create-suppression-rule.gif" alt-text="Warnungsunterdrückungsregel erstellen":::

## <a name="create-a-suppression-rule"></a>Erstellen einer Unterdrückungsregel

Es gibt einige Möglichkeiten, wie Sie Regeln zum Unterdrücken unerwünschter Sicherheitswarnungen erstellen können:

- Warnungen auf Verwaltungsgruppenebene können Sie mithilfe von Azure Policy unterdrücken.
- Um Warnungen auf Abonnementebene zu unterdrücken, können Sie wie im Folgenden erläutert das Azure-Portal oder die REST-API verwenden.

Mit Unterdrückungsregeln können nur Warnungen verworfen werden, die für die ausgewählten Abonnements bereits ausgelöst wurden.

So erstellen Sie eine Regel direkt im Azure-Portal

1. Auf der Security Center-Seite für Sicherheitswarnungen:

    - Suchen Sie die spezifische Warnung, die nicht mehr angezeigt werden soll, und wählen Sie im Menü mit den Auslassungspunkten (...) für die Warnung die Option **Create suppression rule** (Unterdrückungsregel erstellen) aus:

        [![Option **Create suppression rule**](media/alerts-suppression-rules/auto-dismiss-future-option.png)](media/alerts-suppression-rules/auto-dismiss-future-option.png#lightbox) (Unterdrückungsregel erstellen)

    - Oder wählen Sie oben auf der Seite den Link **Unterdrückungsregeln** und dann auf der Seite der Unterdrückungsregeln die Option **Neue Unterdrückungsregel erstellen** aus:

        ![Schaltfläche „Neue Unterdrückungsregel erstellen“](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. Geben Sie im Bereich „Neue Unterdrückungsregel (Vorschau)“ Details zur neuen Regel ein.
    - Mit der Regel kann die Warnung für **alle Ressourcen** verworfen werden, sodass zukünftig keine derartige Regel mehr angezeigt wird.     
    - Mit der Regel kann die Warnung **nach bestimmten Kriterien** verworfen werden, z. B. wenn die Warnung sich auf eine bestimmte IP-Adresse, einen Prozessnamen, ein Benutzerkonto, eine Azure-Ressource oder einen Standort bezieht.

    > [!TIP]
    > Wenn Sie die Seite für die neue Regel über eine bestimmte Warnung geöffnet haben, werden die Warnung und das Abonnement automatisch in der neuen Regel konfiguriert. Wenn Sie den Link **Neue Unterdrückungsregel erstellen** verwendet haben, entsprechen die ausgewählten Abonnements dem aktuellen Filter im Portal.

    [![Bereich zum Erstellen von Unterdrückungsregeln](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)
1. Geben Sie Details zur Regel ein:
    - **Name:** ein Name für die Regel. Regelnamen müssen mit einem Buchstaben oder einer Ziffer beginnen, müssen zwischen 2 und 50 Zeichen lang sein und dürfen keine anderen Symbole als Bindestriche (-) oder Unterstriche (_) enthalten. 
    - **Status:** „Aktiviert“ oder „Deaktiviert“.
    - **Grund:** Wählen Sie einen der integrierten Gründe oder „Sonstiger“ aus, wenn diese Ihren Anforderungen nicht entsprechen.
    - **Ablaufdatum:** ein Ablaufdatum und eine Ablaufzeit für die Regel. Regeln können bis zu sechs Monate lang gelten.
1. Optional können Sie die Regel über die Schaltfläche **Simulate** (Simulieren) testen, um zu sehen, wie viele Warnungen verworfen werden, wenn die Regel aktiv ist.
1. Speichern Sie die Regel. 


## <a name="edit-a-suppression-rules"></a>Bearbeiten einer Unterdrückungsregel

Die erstellten Regeln können Sie auf der Seite der Unterdrückungsregeln bearbeiten.

1. Wählen Sie oben auf der Security Center-Seite für Sicherheitswarnungen den Link **Unterdrückungsregeln** aus.
1. Die Seite der Unterdrückungsregeln wird mit allen Regeln für das ausgewählte Abonnement geöffnet.

    [![Liste der Unterdrückungsregeln](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. Um eine einzelne Regel zu bearbeiten, öffnen Sie das Menü mit den Auslassungspunkten (...) für die Regel, und wählen Sie **Bearbeiten** aus.
1. Nehmen Sie die gewünschten Änderungen vor, und wählen Sie dann **Anwenden** aus. 

## <a name="delete-a-suppression-rule"></a>Löschen einer Unterdrückungsregel

Wenn Sie eine oder mehrere der erstellten Regeln löschen möchten, verwenden Sie die Seite „Unterdrückungsregeln“.

1. Wählen Sie oben auf der Security Center-Seite für Sicherheitswarnungen den Link **Unterdrückungsregeln** aus.
1. Die Seite der Unterdrückungsregeln wird mit allen Regeln für das ausgewählte Abonnement geöffnet.
1. Um eine einzelne Regel zu löschen, öffnen Sie das Menü mit den Auslassungspunkten (...) für die Regel, und wählen Sie **Löschen** aus.
1. Wenn Sie mehrere Regeln löschen möchten, aktivieren Sie die Kontrollkästchen für die zu löschenden Regeln, und wählen Sie **Löschen** aus.
    ![Löschen einer oder mehrerer Unterdrückungsregeln](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="view-suppressed-alerts"></a>Anzeigen unterdrückter Warnungen

Warnungen, die den aktivierten Unterdrückungsregeln entsprechen, werden zwar weiterhin generiert, ihr Status wird jedoch auf **Abgelehnt** festgelegt. Der Status wird im Azure-Portal oder an dem Ort angezeigt, über den Sie auf die Security Center-Sicherheitswarnungen zugreifen. 

> [!TIP]
> [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) erstellt keine Vorfälle für unterdrückte Warnungen. Bei anderen SIEMs können Sie unterdrückte Warnungen durch Verwendung des Status der Warnungen („Abgelehnt“) filtern.

Verwenden Sie den Filter in Security Center, um die Warnungen anzuzeigen, die durch die Regeln verworfen wurden.

* Öffnen Sie auf der Security Center-Seite für Sicherheitswarnungen die Filteroptionen, und wählen Sie **Abgelehnt** aus.  

   [![Anzeigen verworfener Warnungen](media/alerts-suppression-rules/view-dismissed-alerts.png)](media/alerts-suppression-rules/view-dismissed-alerts.png#lightbox)


## <a name="create-and-manage-suppression-rules-with-the-api"></a>Erstellen und Verwalten von Unterdrückungsregeln über die API

Sie können Unterdrückungsregeln für Warnungen über die Security Center-REST-API erstellen, anzeigen oder löschen. 

Die relevanten HTTP-Methoden für Unterdrückungsregeln in der REST-API lauten wie folgt:

- **PUT**: Erstellen oder Aktualisieren einer Unterdrückungsregel in einem angegebenen Abonnement

- **GET**:

    - Auflisten aller Regeln, die für ein bestimmtes Abonnement konfiguriert sind. Diese Methode gibt ein Array der anwendbaren Regeln zurück.

    - Abrufen der Details einer spezifischen Regel für ein bestimmtes Abonnement. Diese Methode gibt eine Unterdrückungsregel zurück.

    - Simulieren der Auswirkung einer Unterdrückungsregel in der Entwurfsphase. Mit diesem Aufruf ermitteln Sie, welche der vorhandenen Warnungen verworfen werden, wenn die Regel aktiv ist.

- **DELETE**: Löschen einer vorhandenen Regel (der Status der durch die Regel bereits verworfenen Warnungen wird jedoch nicht geändert).

Ausführliche Informationen und Verwendungsbeispiele finden Sie in der [API-Dokumentation](/rest/api/securitycenter/). 


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden Unterdrückungsregeln in Azure Security Center beschrieben, mit denen unerwünschte Warnungen automatisch verworfen werden.

Weitere Informationen zu Sicherheitswarnungen in Azure Defender finden Sie auf den folgenden Seiten:

- [Sicherheitswarnungen und Kill Chain für Absichten:](alerts-reference.md) Eine Referenzanleitung zu den Sicherheitswarnungen, die Sie möglicherweise von Azure Defender erhalten.