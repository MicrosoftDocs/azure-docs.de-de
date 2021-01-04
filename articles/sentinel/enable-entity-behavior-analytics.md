---
title: Verwenden von Entity Behavior Analytics zum Erkennen komplexer Bedrohungen | Microsoft-Dokumentation
description: Aktivieren von User and Entity Behavior Analytics in Azure Sentinel und Konfigurieren von Datenquellen
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2020
ms.author: yelevin
ms.openlocfilehash: 140228a65be166bc172e81267c4449b49621e02c
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425778"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>Aktivieren von User and Entity Behavior Analytics (UEBA) in Azure Sentinel 

> [!IMPORTANT]
>
> - Die Features UEBA und Entitätsseiten sind nun in den folgenden Azure Sentinel-Regionen **allgemein verfügbar**:
>    - in den US-amerikanischen Regionen
>    - in der Region Europa, Westen
>    - in den australischen Regionen
>
> - In allen anderen Geografien und Regionen bleiben diese Features bis auf Weiteres in der **Vorschau**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="prerequisites"></a>Voraussetzungen

So aktivieren oder deaktivieren Sie dieses Feature (diese Voraussetzungen müssen nicht erfüllt sein, um das Feature zu verwenden)

- Der Benutzer muss Mitglied des Azure Active Directory Ihrer Organisation und darf kein Gastbenutzer sein.

- Ihrem Benutzer muss die Rolle **Globaler Administrator** oder **Sicherheitsadministrator** in Azure AD zugewiesen sein.

- Ihrem Benutzer muss mindestens eine der folgenden **Azure-Rollen**  zugewiesen sein ([Weitere Informationen zu Azure RBAC](roles.md)):
    - **Azure Sentinel-Mitwirkender** auf Arbeitsbereichs -oder Ressourcengruppenebene.
    - **Log Analytics-Mitwirkender** auf Ressourcengruppen- oder Abonnementebene.

- In Ihrem Arbeitsbereich dürfen keine Azure-Ressourcensperren aktiviert sein. [Erfahren Sie mehr über Azure-Ressourcensperren](../azure-resource-manager/management/lock-resources.md).

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>Aktivieren von User and Entity Behavior Analytics

1. Wählen Sie im Navigationsmenü von Azure Sentinel **Entity Behavior** aus.

1. Schalten Sie den Umschalter unter der Überschrift **Aktivieren** auf **Ein** um.

1. Klicken Sie auf die Schaltfläche **Datenquellen auswählen**.

1. Aktivieren Sie im Bereich **Datenquellenauswahl** die Kontrollkästchen neben den Datenquellen, für die Sie UEBA aktivieren möchten, und wählen Sie dann **Anwenden** aus.

    > [!NOTE]
    >
    > In der unteren Hälfte des Bereichs **Datenquellenauswahl** wird eine Liste der von UEBA unterstützten Datenquellen angezeigt, die Sie noch nicht aktiviert haben. 
    >
    > Nachdem Sie UEBA aktiviert haben, können Sie beim Herstellen einer Verbindung mit neuen Datenquellen diese im Bereich des Datenconnectors direkt für UEBA aktivieren (sofern sie UEBA-fähig sind).

1. Wählen Sie **Go to entity search** (Zur Entitätsssuche) aus. Dadurch gelangen Sie zum Bereich für die Entitätssuche, der in Zukunft angezeigt wird, wenn Sie im Hauptmenü von Azure Sentinel **Entity behavior** auswählen.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie die User and Entity Behavior Analytics (UEBA) in Azure Sentinel aktivieren und konfigurieren. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).
