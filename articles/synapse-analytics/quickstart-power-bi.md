---
title: 'Schnellstart: Verknüpfen eines Power BI-Arbeitsbereichs mit einem Synapse-Arbeitsbereich'
description: Verknüpfen Sie anhand der Schritte in diesem Leitfaden einen Power BI-Arbeitsbereich mit einem Azure Synapse Analytics-Arbeitsbereich.
services: synapse-analytics
author: jocaplan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: business-intelligence
ms.date: 10/27/2020
ms.author: jocaplan
ms.reviewer: jrasnick
ms.openlocfilehash: 996233e2526b8b69c13abd4340ead4c5e2175916
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96450669"
---
# <a name="quickstart-linking-a-power-bi-workspace-to-a-synapse-workspace"></a>Schnellstart: Verknüpfen eines Power BI-Arbeitsbereichs mit einem Synapse-Arbeitsbereich

In dieser Schnellstartanleitung erfahren Sie, wie Sie einen Power BI-Arbeitsbereich mit einem Azure Synapse Analytics-Arbeitsbereich verbinden, um neue Power BI-Berichte und -Datasets aus Synapse Studio zu erstellen.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- [Erstellen eines Azure Synapse-Arbeitsbereichs und eines zugehörigen Speicherkontos](quickstart-create-workspace.md)
- [Ein Power BI Professional- oder Premium-Arbeitsbereich](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces)

## <a name="link-power-bi-workspace-to-your-synapse-workspace"></a>Verknüpfen eines Power BI-Arbeitsbereichs mit Ihrem Synapse-Arbeitsbereich

1. Klicken Sie, beginnend in Synapse Studio, auf **Verwalten**.

    ![Auf „Verwalten“ klicken in Synapse Studio.](media/quickstart-link-powerbi/synapse-studio-click-manage.png)

2. Klicken Sie unter **Externe Verbindungen** auf **Verknüpfte Dienste**.

    ![Hervorgehobene verknüpfte Dienste.](media/quickstart-link-powerbi/manage-click-linked-services.png)

3. Klicken Sie auf **+ NEU**.

    ![„+ Neue verknüpfte Dienste“ ist hervorgehoben.](media/quickstart-link-powerbi/new-highlighted.png)

4. Klicken Sie auf **Power BI** und dann auf **Weiter**.

    ![Verknüpfter Power BI-Dienst wird angezeigt.](media/quickstart-link-powerbi/powerbi-linked-service.png)

5. Geben Sie einen Namen für den verknüpften Dienst ein, und wählen Sie einen Arbeitsbereich aus der Dropdownliste aus.

    ![Setup für verknüpfte Power BI-Dienste wird angezeigt.](media/quickstart-link-powerbi/workspace-link-dialog.png)

6. Klicken Sie auf **Erstellen**.

## <a name="view-power-bi-workspace-in-synapse-studio"></a>Anzeigen des Power BI-Arbeitsbereichs in Synapse Studio

Nachdem Ihre Arbeitsbereiche verknüpft sind, können Sie Ihre Power BI-Datasets durchsuchen und neue Power BI-Berichte in Synapse Studio bearbeiten/erstellen.

1. Klicken Sie auf **Entwickeln**.

    ![Auf „Entwickeln“ klicken in Synapse Studio.](media/quickstart-link-powerbi/synapse-studio-click-develop.png)

2. Erweitern Sie Power BI und den Arbeitsbereich, den Sie verwenden möchten.

    ![Power BI und Arbeitsbereich erweitern.](media/quickstart-link-powerbi/develop-expand-powerbi.png)

Neue Berichte können erstellt werden, indem Sie auf oben auf der Registerkarte **Entwickeln** auf **+** klicken. Vorhandene Berichte können bearbeitet werden, indem Sie auf den Berichtsnamen klicken. Alle gespeicherten Änderungen werden in den Power BI-Arbeitsbereich zurückgeschrieben.

![Power BI-Bericht anzeigen und bearbeiten.](media/quickstart-link-powerbi/powerbi-report.png)


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Erstellen eines Power BI-Berichts für in Azure Storage gespeicherte Dateien](sql/tutorial-connect-power-bi-desktop.md).
