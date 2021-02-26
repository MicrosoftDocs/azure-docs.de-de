---
author: msftradford
ms.author: parkerra
ms.date: 01/28/2021
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 386c2f8a7cc32cf65381d9d62e2e6940754e3606
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214191"
---
## <a name="configure-the-sensor-fingerprint-provider"></a>Konfigurieren des Anbieters für Fingerabdrucksensoren

Der erste Schritt besteht darin, einen Anbieter für Fingerabdrucksensoren zu erstellen und zu konfigurieren. Der Anbieter für Fingerabdrucksensoren liest die plattformspezifischen Sensoren auf Ihrem Gerät und konvertiert die gelesenen Daten in eine allgemeine Darstellung, die von der Raumanker-Cloudsitzung genutzt wird.

> [!IMPORTANT]
> Überprüfen Sie [hier](../articles/spatial-anchors/concepts/coarse-reloc.md#platform-availability), ob die von Ihnen aktivierten Sensoren für Ihre Plattform verfügbar sind.