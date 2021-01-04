---
title: Problembehandlung bei Azure-Reservierungsempfehlungen
description: Dieser Artikel enthält grundlegende Informationen zu Azure-Reservierungsempfehlungen, die im Azure-Portal angezeigt werden. Außerdem erfahren Sie, wie Sie Probleme in diesem Zusammenhang behandeln.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/19/2020
ms.openlocfilehash: a3137d779908bf2791ca396068a8c9edf5d56739
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92492236"
---
# <a name="troubleshoot-azure-reservation-recommendations"></a>Problembehandlung bei Azure-Reservierungsempfehlungen

Dieser Artikel enthält grundlegende Informationen zu Azure-Reservierungsempfehlungen, die im Azure-Portal angezeigt werden. Außerdem erfahren Sie, wie Sie Probleme in diesem Zusammenhang behandeln. Möglicherweise werden keine Empfehlungen oder nur solche angezeigt, die nicht Ihren Erwartungen entsprechen. Beispielsweise könnten Sie bereits über eine reservierte Instanz für eine bestimmte VM-Konfiguration verfügen. Vielleicht erwarten Sie, dass eine Empfehlung für eine ähnliche VM-Konfiguration angezeigt wird.

## <a name="symptoms"></a>Symptome

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu **Reservierungen**.
2. Wählen Sie **+ Hinzufügen** aus, und wählen Sie dann ein Produkt aus.
3. Auf der Registerkarte **Empfohlen** werden möglicherweise keine Empfehlungen oder nur solche angezeigt, die nicht Ihren Erwartungen entsprechen.

## <a name="cause"></a>Ursache

Die Liste der empfohlenen Produkte wird generiert, indem die Nutzungsmenge für Ihren (freigegebenen oder einzelnen) Bereich mit den Kosten einer Reservierung für das Produkt verglichen wird. Wenn die Empfehlungs-Engine Einsparungen erkennt, empfiehlt sie ein Produkt für den Kauf. Wenn die Engine jedoch keine Einsparungen identifiziert, empfiehlt sie auch kein Produkt.

Wenn Sie eine Ressource mit einer bestimmten Konfiguration ausführen, ist nicht gewährleistet, dass Sie Geld sparen, indem Sie eine Reservierung für diese Konfiguration erwerben. Beispielsweise kann eine sporadische Nutzung vorliegen. In diesem Fall lassen sich angesichts der Gesamtkosten für die Reservierung über die Laufzeit des Reservierungszeitraums möglicherweise keine Kosten einsparen.

Sie sollten auch verstehen, wie sich die Bereichsauswahl auf Empfehlungen auswirkt. Wenn der Bereich auf **Shared** (Freigegeben) festgelegt ist, werden durch die Empfehlungen in der Liste reservierte Instanzen angezeigt, bei denen Azure Einsparungen für die gesamte Registrierung ermittelt hat, die dem Abrechnungsabonnement zugeordnet ist. Wenn der Bereich auf **Single** (Einzeln) festgelegt ist, gelten Empfehlungen in der Liste nur für Ressourcen, die im Abonnement ausgeführt werden. Es ist möglich, dass einige VM-Größen für einen Bereich empfohlen werden, für einen anderen aber nicht. Beispielsweise kann eine aggregierte Nutzung von **Standard_B1ls** während der Registrierung hoch genug sein, um die Kosten für den Erwerb einer Reservierung im Registrierungsbereich zu rechtfertigen. Allerdings ist die Nutzung eines einzelnen Abonnements in der Registrierung möglicherweise nicht hoch genug, um die Kosten für den Erwerb einer Reservierung im Bereich zu rechtfertigen. Eine Änderung des Bereichs von **Shared** in **Single** und umgekehrt kann zu unterschiedlichen Empfehlungen führen.

Azure kann abhängig von den ermittelten Kosteneinsparungen bei bestimmten Laufzeiten den Kauf einer Reservierung empfehlen und bei anderen Laufzeiten nicht. Insbesondere gelten für dreijährige Laufzeiten höhere Rabatte als für einjährige Laufzeiten. Es ist wahrscheinlicher, dass Azure Einsparungen für eine Laufzeit von drei Jahren im Gegensatz zu einer Laufzeit von einem Jahr ermittelt.

Wenn Sie verstehen möchten, warum Azure eine bestimmte Ressourcengröße und -menge empfiehlt, wählen Sie **&lt;Quantity&gt; See Details** (<Menge> Details anzeigen) aus, um eine ausführliche Visualisierung zu sehen, die die potenziellen Einsparungen im Laufe der Zeit veranschaulicht.

:::image type="content" source="./media/troubleshoot-reservation-recommendation/see-details-link.png" alt-text="Beispiel mit der Reservierungsempfehlung und dem Link „See details“ (Details anzeigen)" lightbox="./media/troubleshoot-reservation-recommendation/see-details-link.png" :::

## <a name="solution"></a>Lösung

Sie können eine beliebige Reservierungsmenge für eine von Ihnen gewünschte Laufzeit erwerben. Der Kauf einer Reservierung mit einer Menge und Laufzeit, die von der Empfehlung abweichen, wird wahrscheinlich zu weniger optimalen Einsparungen und einer weniger optimalen Nutzung führen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Reservierungsempfehlungen finden Sie unter [Empfehlungen für den Reservierungserwerb](determine-reservation-purchase.md).
