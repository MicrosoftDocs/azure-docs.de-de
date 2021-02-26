---
title: Benachrichtigung zur automatischen Aktualisierung und zum Ablauf der selbstgehosteten Integration Runtime
description: Erfahren Sie mehr über die Benachrichtigung zur automatischen Aktualisierung und zum Ablauf der selbstgehosteten Integration Runtime.
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 12/25/2020
ms.openlocfilehash: 972015f0f42a8a869de0edcc8f0e921ae7278cd1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376257"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>Benachrichtigung zur automatischen Aktualisierung und zum Ablauf der selbstgehosteten Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie die selbstgehostete Integration Runtime automatisch auf die neueste Version aktualisiert wird und wie die Versionen der selbstgehosteten Integration Runtime in ADF verwaltet werden.

## <a name="self-hosted-integration-runtime-auto-update"></a>Automatische Aktualisierung der selbstgehosteten Integration Runtime
Wenn Sie eine selbstgehostete Integration Runtime auf Ihrem lokalen Computer oder einer Azure-VM installieren, haben Sie in der Regel zwei Möglichkeiten, die Version der selbstgehosteten Integration Runtime zu verwalten: durch automatische Aktualisierung oder durch manuelle Wartung. Normalerweise werden in ADF monatlich zwei neue Versionen der selbstgehosteten Integration Runtime veröffentlicht, die neue Featurereleases, Fixes oder Verbesserungen beinhalten. Daher empfehlen wir Benutzern ein Update auf die neueste Version, damit sie die neuesten Features und Erweiterungen erhalten.

Die einfachste Möglichkeit besteht darin, beim Erstellen oder Bearbeiten der selbstgehosteten Integration Runtime die automatische Aktualisierung zu aktivieren. Daraufhin wird sie automatisch auf die jeweils neueste Version aktualisiert. Sie können das Update auch für den am besten geeigneten Zeitraum planen.

![Aktivieren der automatischen Aktualisierung](media/create-self-hosted-integration-runtime/shir-auto-update.png)

Sie können Datum und Uhrzeit der letzten Aktualisierung auf dem Client der selbstgehosteten Integration Runtime überprüfen.

![Screenshot der Überprüfung der Aktualisierungszeit](media/create-self-hosted-integration-runtime/shir-auto-update-2.png)

> [!NOTE]
> Obwohl wir jeden Monat zwei Versionen veröffentlichen, wird die selbstgehostete Integration Runtime nur einmal im Monat automatisch aktualisiert, um Stabilität zu gewährleisten. Gelegentlich werden Sie daher feststellen, dass es sich bei der automatisch aktualisierten Version nicht um die aktuelle, sondern um die vorherige Version handelt. Wenn Sie die neueste Version erhalten möchten, wechseln Sie zum [Download Center](https://www.microsoft.com/download/details.aspx?id=39717).

## <a name="self-hosted-integration-runtime-expire-notification"></a>Benachrichtigung zum Ablauf der selbstgehosteten Integration Runtime
Wenn Sie die Version der selbstgehosteten Integration Runtime manuell steuern möchten, können Sie die Einstellung zur automatischen Aktualisierung deaktivieren und die Integration Runtime manuell installieren. Jede Version der selbstgehosteten Integration Runtime läuft nach einem Jahr ab. Die Ablaufnachricht wird im ADF-Portal und auf dem Client der selbstgehosteten Integration Runtime **90 Tage** vor dem Ablaufdatum angezeigt.

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie [Integration Runtime-Konzepte in Azure Data Factory](./concepts-integration-runtime.md).

- Informieren Sie sich über das [Erstellen einer selbstgehosteten Integration Runtime im Azure-Portal](./create-self-hosted-integration-runtime.md).
