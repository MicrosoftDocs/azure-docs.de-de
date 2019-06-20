---
title: Regionale Verfügbarkeit von StorSimple | Microsoft-Dokumente
description: Erläutert die Azure-Regionen, in denen die verschiedenen StorSimple-Gerätemodelle verfügbar sind.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: e290feb278a1cddf1cfecfcb66458d8290ec122a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943594"
---
# <a name="available-regions-for-your-storsimple"></a>Verfügbare Regionen für Ihr StorSimple

## <a name="overview"></a>Übersicht

Die Azure-Datencenter werden in verschiedenen geografischen Regionen der Welt betrieben, um die Ansprüche der Kunden hinsichtlich Leistung und Anforderungen sowie die Präferenzen der Kunden hinsichtlich des Datenspeicherorts zu erfüllen. Bei einem Azure-Gebiet handelt es sich um einen definierten Bereich der Erde, der mindestens eine Azure-Region enthält. Eine Azure-Region ist ein Bereich in einem Gebiet mit mindestens einem Rechenzentrum.

Das Auswählen einer Azure-Region ist sehr wichtig, und die Wahl der Region wird von Faktoren wie Datenresidenz und Datenhoheit, Dienstverfügbarkeit, Leistung, Kosten und Redundanz beeinflusst. Weitere Informationen dazu, wie eine Region ausgewählt wird, finden Sie unter [Welche Azure-Region ist für mich die richtige?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

Für die StorSimple-Lösung wird die Wahl der Region speziell durch die folgenden Faktoren bestimmt:

- Regionen, in denen der StorSimple-Geräte-Manager-Dienst verfügbar ist
- Die Länder/Regionen, in denen StorSimple als physisches, Cloud- oder virtuelles Gerät verfügbar ist
- Die Regionen, in denen sich die Speicherkonten, in denen StorSimple-Daten gespeichert werden, befinden sollten, um eine optimale Leistung zu erzielen

In diesem Tutorial wird die regionale Verfügbarkeit für den StorSimple-Geräte-Manager-Dienst sowie die lokalen physischen Geräte und die Cloudgeräte beschrieben. Die Informationen in diesem Artikel gelten für Geräte der StorSimple 8000- und 1200-Serie.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Regionale Verfügbarkeit für den StorSimple-Geräte-Manager-Dienst

Der StorSimple-Geräte-Manager-Dienst wird derzeit in 12 öffentlichen Regionen und 2 Azure Government-Regionen unterstützt.

Sie definieren eine Region oder einen Standort, wenn Sie den StorSimple-Geräte-Manager-Dienst erstmalig erstellen. Üblicherweise sollte ein Standort ausgewählt werden, der möglichst nah zu der geografischen Region liegt, in der das Gerät bereitgestellt wird. Das Gerät und der Dienst können aber auch in anderen Standorten bereitgestellt werden.

Es folgt eine Liste der Regionen, in denen der StorSimple-Geräte-Manager-Dienst für die öffentliche Azure-Cloud verfügbar ist und bereitgestellt werden kann.

![StorSimple-Geräte-Manager-Dienst-Regionen](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Für Azure Government Cloud ist der StorSimple-Geräte-Manager-Dienst in den Datencentern US Gov Iowa und US Gov Virginia verfügbar.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Regionale Verfügbarkeit für Daten, die in StorSimple gespeichert sind

StorSimple-Daten sind physisch in Azure-Speicherkonten gespeichert, und diese Konten sind in allen Azure-Regionen verfügbar. Wenn Sie ein Azure-Speicherkonto erstellen, wird der primäre Standort des Speicherkontos ausgewählt, und dadurch ist die Region bestimmt, in der sich die Daten befinden.

Wenn Sie einen StorSimple-Geräte-Manager-Dienst erstmalig erstellen und diesen einem Speicherkonto zuordnen, können sich der StorSimple-Geräte-Manager-Dienst und der Azure-Speicher in zwei verschiedenen Standorten befinden. In einem solchen Fall müssen Sie das StorSimple-Geräte-Manager- und das Azure-Speicherkonto separat erstellen.

Grundsätzlich sollten Sie die Region auswählen, die für den Dienst für Ihr Speicherkonto am nächsten liegt. Es kann aber sein, dass die nächstgelegene Microsoft Azure-Region nicht die Region mit der kürzesten Wartezeit ist. Die Wartezeit ist der Punkt, der die Leistung des Netzwerkdienst und somit die Leistung der Lösung bestimmt. Daher müssen Sie, wenn Sie ein Speicherkonto in einer anderen Region auswählen, wissen, welche Wartezeiten es zwischen Ihrem Dienst und der Region gibt, die Ihrem Speicherkonto zugeordnet ist.

Wenn Sie eine StorSimple Cloud Appliance verwenden, empfiehlt es sich, dass sich der Dienst und das zugeordnete Speicherkonto in derselben Region befinden. Speicherkonten in einer anderen Region können zu Leistungseinbußen führen.

## <a name="availability-of-storsimple-device"></a>Verfügbarkeit von StorSimple-Geräten

Je nach Modell können die StorSimple-Geräte in unterschiedlichen geografischen Gebieten oder Ländern/Regionen verfügbar sein.

### <a name="storsimple-physical-device-models-81008600"></a>Physisches StorSimple-Gerät (Modelle 8100/8600)

Wenn Sie ein physisches StorSimple 8100- oder 8600-Gerät verwenden, ist es in den folgenden Ländern/Regionen verfügbar.

| #  | Land/Region        | #  | Land/Region     | #  | Land/Region      | #  | Land/Region             |
|----|-----------------------|----|--------------------|----|---------------------|----|----------------------------|
| 1  | Australien             | 16 | Hongkong (SAR)      | 31 | Neuseeland         | 46 | Südafrika               |
| 2  | Österreich               | 17 | Ungarn            | 32 | Nigeria             | 47 | Südkorea                |
| 3  | Bahrain               | 18 | Island            | 33 | Norwegen              | 48 | Spanien                      |
| 4  | Belgien               | 19 | Indien              | 34 | Peru                | 49 | Sri Lanka                  |
| 5  | Brasilien                | 20 | Indonesien          | 35 | Philippinen         | 50 | Schweden                     |
| 6  | Kanada                | 21 | Irland            | 36 | Polen              | 51 | Schweiz                |
| 7  | Chile                 | 22 | Israel             | 37 | Portugal            | 52 | Taiwan                     |
| 8  | Kolumbien              | 23 | Italien              | 38 | Puerto Rico         | 53 | Thailand                   |
| 9  | Tschechische Republik        | 24 | Japan              | 11,9 | Katar               | 54 | Türkei                     |
| 10 | Dänemark               | 25 | Kenia              | 40 | Rumänien             | 55 | Ukraine                    |
| 11 | Ägypten                 | 26 | Kuwait             | 41 | Russland              | 56 | Vereinigte Arabische Emirate       |
| 12 | Finnland               | 27 | Macau (SAR)          | 42 | Saudi-Arabien        | 57 | Vereinigtes Königreich             |
| 13 | Frankreich                | 28 | Malaysia           | 43 | Singapur           | 58 | USA              |
| 14 | Deutschland               | 29 | Mexiko             | 44 | Slowakei            | 59 | Vietnam                    |
| 15 | Griechenland                | 30 | Niederlande        | 45 | Slowenien            | 60 | Kroatien                    |

Diese Liste ändert sich, wenn weitere Länder/Regionen hinzugefügt werden. Die jeweils aktuellste Liste der Länder finden Sie in den [Produktbestimmungen](https://www.microsoft.com/en-us/licensing/product-licensing/products) im Anhang zu den Bestimmungen für Speicherarrays.

Microsoft kann physische Hardware sowie Hardware-Ersatzteile für StorSimple in die Länder liefern, die in der vorangehenden Liste aufgeführt sind.

> [!IMPORTANT]
> Sie sollten kein physisches StorSimple-Gerät in einer Region platzieren, in der StorSimple nicht unterstützt wird. Microsoft hat keine Möglichkeit, irgendwelche Ersatzteile in Länder/Regionen zu liefern, in denen StorSimple nicht unterstützt wird.

### <a name="storsimple-cloud-appliance-models-80108020"></a>StorSimple Cloud Appliance (Modelle 8010/8020)

Wird eine StorSimple Cloud Appliance 8010 oder 8020 verwendet, wird das Gerät unterstützt, und das Gerät ist in allen Regionen verfügbar, in denen der zugrunde liegende virtuelle Computer unterstützt wird. Für die 8010 wird ein virtueller Computer des Typs _Standard_A3_ verwendet, der in allen Azure-Regionen unterstützt wird.

Für die 8020 werden Storage Premium und ein virtueller Computer des Typs _Standard_DS3_ verwendet, um ein Cloudgerät zu erstellen. Die 8020 wird in Azure-Regionen unterstützt, in denen Storage Premium und virtuelle Computer des Typs _Standard_DS3_ unterstützt werden. Verwenden Sie [diese Liste](https://azure.microsoft.com/regions/services/), um zu ermitteln, ob in Ihrer Region sowohl **Virtuelle Computer > DS-Serie** als auch **Storage > Disk Storage** verfügbar ist.

### <a name="storsimple-virtual-array-model-1200"></a>StorSimple Virtual Array (Modell 1200)

Wenn Sie ein StorSimple Virtual Array der 1200-Serie verwenden, wird das virtuelle Datenträgerimage in allen Azure-Regionen unterstützt.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen über die [Preise für verschiedene StorSimple-Modelle](https://azure.microsoft.com/pricing/calculator/#storsimple2)
* Weitere Informationen über das [Verwalten des StorSimple-Speicherkontos](storsimple-8000-manage-storage-accounts.md)
* Weitere Informationen über das [Verwalten Ihres StorSimple-Geräts mithilfe des StorSimple-Geräte-Manager-Diensts](storsimple-8000-manager-service-administration.md)
