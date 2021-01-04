---
title: Beheben von Problemen beim Herunterladen der Nutzungsdetails für Azure-Reservierungen
description: Dieser Artikel enthält grundlegende Informationen dazu, warum der Download der Nutzungsdetails zu reservierten Instanzen im Azure-Portal nicht verfügbar ist. Außerdem erfahren Sie, wie Sie Probleme in diesem Zusammenhang behandeln.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 10/30/2020
ms.openlocfilehash: 85584626b050be8052f59c80ab294cc62747daed
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147325"
---
# <a name="troubleshoot-azure-reservation-download-usage-details"></a>Beheben von Problemen beim Herunterladen der Nutzungsdetails für Azure-Reservierungen

Dieser Artikel enthält grundlegende Informationen dazu, warum der Download der Nutzungsdetails zu reservierten Instanzen im Azure-Portal nicht verfügbar ist. Außerdem erfahren Sie, wie Sie Probleme in diesem Zusammenhang behandeln.

## <a name="symptoms"></a>Symptome

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu **Reservierungen**.
1. Wählen Sie eine Reservierung aus.
1. Auf der Seite mit der Reservierungsübersicht wird in der Standardansicht die Nutzung der letzten sieben Tage angezeigt. Sie können **Als CSV herunterladen** auswählen, um die Nutzungsdetails zur Reservierung herunterzuladen.
1. Wenn Sie den Zeitbereich ändern, steht die Option **Als CSV herunterladen** nicht mehr zur Verfügung.
    :::image type="content" source="./media/troubleshoot-download-usage/download-csv-unavailable.png" alt-text="Beispiel dafür, dass „Als CSV herunterladen“ nicht mehr verfügbar ist" lightbox="./media/troubleshoot-download-usage/download-csv-unavailable.png" :::

## <a name="cause"></a>Ursache

Aufgrund technischer Einschränkungen wird das Herunterladen von Daten für einen Zeitraum von mehr als sieben Tagen von Azure nicht unterstützt. Lange Zeiträume für Kunden mit großen Mengen an Reservierungen generieren große Datenmengen. Durch die Rückgabe von Daten über APIs werden Azure-Ressourcen belastet.

## <a name="solution"></a>Lösung

Uns ist bewusst, dass Kunden Daten für längere Zeiträume herunterladen möchten. Derzeit gibt es jedoch keine Möglichkeit, Reservierungsnutzungsdaten für längere Zeiträume herunterzuladen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Reservierungen finden Sie unter [Was sind Azure-Reservierungen?](save-compute-costs-reservations.md).