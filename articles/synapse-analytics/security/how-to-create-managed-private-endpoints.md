---
title: Erstellen eines verwalteten privaten Endpunkts zum Verbinden mit Ihren Datenquellenergebnissen
description: In diesem Artikel erfahren Sie, wie Sie einen verwalteten privaten Endpunkt zu Ihren Datenquellen aus einem Azure Synapse-Arbeitsbereich erstellen.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: e0309b4c96b2ae25eb568e390717ba76cfd84fa5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461309"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source"></a>Erstellen eines verwalteten privaten Endpunkts zu Ihrer Datenquelle

In diesem Artikel erfahren Sie, wie Sie einen verwalteten privaten Endpunkt zu Ihrer Datenquelle in Azure erstellen. Weitere Informationen finden Sie unter [Verwalten privater Endpunkte](./synapse-workspace-managed-private-endpoints.md).

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Schritt 1: Öffnen Ihres Azure Synapse-Arbeitsbereichs im Azure-Portal

Sie können einen verwalteten privaten Endpunkt zu Ihrer Datenquelle aus Azure Synapse Studio erstellen. Wählen Sie im Azure-Portal die Registerkarte **Übersicht** und dann im Abschnitt „Erste Schritte“ auf der Karte „Synapse Studio öffnen“ die Option **Öffnen** aus.

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>Schritt 2: Navigieren zur Registerkarte „Verwaltete virtuelle Netzwerke“ in Synapse Studio

Wählen Sie in Azure Synapse Studio im linken Navigationsbereich die Registerkarte **Verwalten** aus. Wählen Sie **Verwaltete private Endpunkte** und dann **+ Neu** aus.
![Erstellen eines neuen verwalteten privaten Endpunkts](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>Schritt 3: Auswählen des Datenquellentyps

Wählen Sie den Datenquellentyp aus. In diesem Fall ist die Zieldatenquelle ein ADLS Gen2-Konto. Wählen Sie **Weiter**.
![Auswählen des Typs einer Zieldatenquelle](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>Schritt 4: Eingeben von Informationen über die Datenquelle

Im nächsten Fenster geben Sie die Informationen zu der Datenquelle ein. In diesem Beispiel erstellen Sie einen verwalteten privaten Endpunkt für ein ADLS Gen2-Konto. Geben Sie einen **Namen** für den verwalteten privaten Endpunkt ein. Geben Sie ein **Azure-Abonnement** und einen **Speicherkontonamen** an. Klicken Sie auf **Erstellen**.
![Eingeben von Details der Zieldatenquelle](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>Schritt 5: Überprüfen, ob Ihr verwalteter privater Endpunkt erfolgreich erstellt wurde

Nachdem Sie die Anforderung übermittelt haben, wird ihr Status angezeigt. Um die erfolgreiche Erstellung Ihres verwalteten privaten Endpunkts, überprüfen Sie seinen *Bereitstellungsstatus*. Möglicherweise müssen Sie 1 Minute warten und **Aktualisieren** auswählen, um den Bereitstellungsstatus zu aktualisieren. Sie können sehen, dass der verwaltete private Endpunkt für das ADLS Gen2-Konto erfolgreich erstellt wurde.

Sie können auch sehen, dass der *Genehmigungszustand* *Ausstehend* lautet. Der Besitzer der Zielressource kann die Verbindungsanforderung des privaten Endpunkts genehmigen oder ablehnen. Wenn der Besitzer die Verbindungsanforderung des privaten Endpunkts genehmigt, wird ein privater Link hergestellt. Wird sie abgelehnt, wird kein privater Link hergestellt.
![Status der Erstellungsanforderung eines privaten Endpunkts](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu [Verwalteten privaten Endpunkten](./synapse-workspace-managed-private-endpoints.md).