---
title: Glossarbericht zu Ihren Daten mithilfe von Purview-Erkenntnissen
description: In dieser Schrittanleitung wird beschrieben, wie Sie die Glossarberichterstellung mit Purview-Erkenntnissen für Ihre Daten anzeigen und verwenden können.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: f61d99a61cb50886d70489b586d948bfa751e196
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576773"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Glossarerkenntnisse über Ihre Daten in Azure Purview

In dieser Schrittanleitung wird beschrieben, wie Sie auf Purview-Glossar-Erkenntnisberichte für Ihre Daten zugreifen, sie anzeigen und filtern können.

In dieser Schrittanleitung lernen Sie Folgendes:

> [!div class="checklist"]
> - Wechseln zu Erkenntnissen aus Ihrem Purview-Konto
> - Erhalten eines Überblicks über Ihre Daten

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vor dem Beginn mit Purview--Erkenntnissen sicher, dass Sie die folgenden Schritte ausgeführt haben:

- Einrichten Ihrer Azure-Ressourcen und Auffüllen des Kontos mit Daten

- Einrichten und Ausführen eines Scans für den Quelltyp

- Einrichten eines Glossars und Anfügen von Ressourcen an Glossarbegriffe

Weitere Informationen finden Sie unter [Verwalten von Datenquellen in Azure Purview (Vorschau)](manage-data-sources.md).

## <a name="use-purview-glossary-insights"></a>Verwenden von Purview-Glossarerkenntnissen

In Azure Purview können Sie Glossarbegriffe erstellen und diese Ressourcen anfügen. Später können Sie die Glossarverteilung in „Glossarerkenntnisse“ anzeigen. So erfahren Sie den Status Ihres Glossars gemäß der Begriffe, die Ressourcen angefügt sind. Außerdem werden Begriffe nach Status und Verteilung der Rollen nach Anzahl der Benutzer aufgeführt.

**So zeigen Sie „Glossarerkenntnisse“ an:**

1. Wechseln Sie zum **Azure Purview**-[Instanzbildschirm im Azure-Portal](https://aka.ms/purviewportal), und wählen Sie Ihr Purview-Konto aus.

1. Wählen Sie auf der Seite **Übersicht** im Abschnitt **Erste Schritte** die Kontokachel **Purview starten** aus.

   :::image type="content" source="./media/glossary-insights/portal-access.png" alt-text="„Purview starten“ im Azure-Portal":::

1. Wählen Sie auf der Purview-**Startseite** die Kachel **Erkenntnisse anzeigen** aus, um auf Ihren **Erkenntnisse**-Bereich :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: zuzugreifen.

   :::image type="content" source="./media/glossary-insights/view-insights.png" alt-text="Anzeigen Ihrer Erkenntnisse im Azure-Portal":::

1. Wählen Sie im Bereich **Erkenntnisse** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false"::: die Option **Glossar** aus, um den **Glossarerkenntnisse**-Bericht von Purview anzuzeigen.

Auf der Seite **Glossarerkenntnisse** werden die folgenden Bereiche angezeigt:
1. **Allgemeine KPIs** zum Anzeigen von Glossarbegriffen und Katalogbenutzern

2. **Oberste Glossarbegriffe und Anzahl von Ressourcen** zeigt die obersten 5 Glossarbegriffe mit den ihnen zugeordneten Ressourcen an. Alle anderen Ressourcen werden im Diagramm in der Kategorie „Sonstige“ berücksichtigt.

3. **Glossarbegriffe nach Begriffsstatus** zeigt die Verteilung von Glossarbegriffen nach Status an, z. B. „Entwurf“, „Genehmigt“, „Warnung“ und „Abgelaufen“. 

1. Zeigen Sie auf den Slice des Diagramms mit einem Status, bzw. klicken Sie darauf, und notieren Sie sich die Anzahl der Begriffe mit diesem Status.

1. **Verteilung der Rollen nach Anzahl der Benutzer** zeigt die Verteilung der Rollen nach Anzahl der Benutzer pro Rolle in Purview an.

   :::image type="content" source="./media/glossary-insights/glossary-insights1.png" alt-text="Glossarerkenntnisse anzeigen":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Purview-Erkenntnisberichten finden Sie in [Ressourcenerkenntnisse zu Ihren Daten in Azure Purview](./asset-insights.md).