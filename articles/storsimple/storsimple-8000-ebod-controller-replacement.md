---
title: Austauschen des EBOD-Controllers auf einem StorSimple-Gerät des Typs 8600 | Microsoft-Dokumentation
description: In diesem Thema wird erläutert, wie Sie ein oder beide EBOD-Controllermodule in einem StorSimple 8600-Gerät ausbauen oder austauschen.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: bdafb4288bf818928aba43d09f15d4dd75092b24
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014838"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Austauschen des EBOD-Controllers eines StorSimple-Geräts

## <a name="overview"></a>Übersicht
In diesem Tutorial wird erläutert, wie ein fehlerhaftes EBOD-Controllermodul in Ihrem Microsoft Azure StorSimple-Gerät ausgetauscht wird. Führen Sie zum Austauschen eines EBOD-Controllermoduls die folgenden Schritte aus:

* Ausbauen des fehlerhaften EBOD-Controllers
* Einbauen eines neuen EBOD-Controllers

Bevor Sie beginnen, sollten Sie Folgendes beachten:

* In alle nicht verwendeten Einschubfächer müssen leere EBOD-Module eingesetzt werden. Das Gehäuse wird nicht ordnungsgemäß gekühlt, wenn ein Einschubfach leer bleibt.
* Der EBOD-Controller kann bei laufendem Betrieb ausgebaut bzw. ausgetauscht werden. Bauen Sie ein fehlerhaftes Modul erst aus, wenn ein Austauschmodul verfügbar ist. Sobald Sie den Austauschvorgang eingeleitet haben, muss dieser binnen 10 Minuten abgeschlossen werden.

> [!IMPORTANT]
> Bevor Sie versuchen, StorSimple-Komponenten auszubauen oder auszutauschen, sollten Sie sich mit den [Konventionen für Sicherheitssymbole](storsimple-8000-safety.md#safety-icon-conventions) und anderen [Sicherheitsvorkehrungen](storsimple-8000-safety.md) vertraut machen.

## <a name="remove-an-ebod-controller"></a>Ausbauen eines EBOD-Controllers
Bevor Sie das fehlerhafte EBOD-Controllermodul in Ihrem StorSimple-Gerät austauschen, stellen Sie sicher, dass das andere EBOD-Controllermodul aktiv ist und ausgeführt wird. Im folgenden Verfahren sowie in der Tabelle wird erläutert, wie das EBOD-Controllermodul ausgebaut wird.

#### <a name="to-remove-an-ebod-module"></a>So bauen Sie ein EBOD-Modul aus
1. Öffnen Sie das Azure-Portal.
2. Navigieren Sie auf Ihrem Gerät zu **Einstellungen** > **Hardwareintegrität**, und prüfen Sie, ob der Status der LED für das aktive EBOD-Controllermodul grün und der LED für das fehlerhafte EBOD-Controllermodul rot ist.
3. Suchen Sie auf der Rückseite des Geräts das fehlerhafte EBOD-Controllermodul.
4. Trennen Sie die Kabel, die das EBOD-Controllermodul mit dem Controller verbinden, bevor Sie das EBOD-Modul aus dem System ausbauen.
5. Notieren Sie sich den genauen SAS-Anschluss des EBOD-Controllermoduls, das mit dem Controller verbunden war. Sie müssen das System in dieser Konfiguration wiederherstellen, nachdem Sie das EBOD-Modul ausgetauscht haben.
   
   > [!NOTE]
   > Normalerweise handelt es sich um Anschluss A, der in der folgenden Abbildung als **Host ein** bezeichnet wird.
   
    ![Rückwand des EBOD-Controllers](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **Abbildung 1** Rückseite des EBOD-Moduls
   
   | Bezeichnung | BESCHREIBUNG |
   |:--- |:--- |
   | 1 |Fehler-LED |
   | 2 |Stromversorgungs-LED |
   | 3 |SAS-Anschlüsse |
   | 4 |SAS-LEDs |
   | 5 |Serielle Anschlüsse ausschließlich für die Verwendung durch den Hersteller |
   | 6 |Anschluss A (Host ein) |
   | 7 |Anschluss B (Host aus) |
   | 8 |Anschluss C (nur werkseitig verwenden) |

## <a name="install-a-new-ebod-controller"></a>Einbauen eines neuen EBOD-Controllers
Im folgenden Verfahren sowie in der Tabelle wird erläutert, wie ein EBOD-Controllermodul in ein StorSimple-Gerät eingebaut wird.

#### <a name="to-install-an-ebod-controller"></a>So bauen Sie einen EBOD-Controller ein
1. Prüfen Sie auf das EBOD-Gerät Beschädigungen, insbesondere den Schnittstellenstecker. Bauen Sie das neue EBOD-Modul nicht ein, wenn einer der Stifte verbogen sein sollte.
2. Schieben Sie das Modul bei geöffnetem Riegel in das Gehäuse, bis der Riegel einrastet.
   
    ![Einbauen des EBOD-Controllers](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **Abbildung 2**: Einbauen des EBOD-Controllermoduls
3. Schließen Sie den Riegel. Sie sollten ein Klicken hören, wenn der Riegel einrastet.
   
    ![Lösen des EBOD-Riegels](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **Abbildung 3**: Schließen des EBOD-Modulriegels
4. Schließen Sie die Kabel wieder an. Verwenden Sie die genaue Konfiguration, die vor dem Austausch vorhanden war. Der folgenden Abbildung und Tabelle können Sie die Einzelheiten entnehmen, wie die Kabel anzuschließen sind.
   
    ![Stromverkabelung des 4 HE-Geräts](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **Abbildung 4**. Erneutes Anschließen der Kabel
   
   | Bezeichnung | BESCHREIBUNG |
   |:--- |:--- |
   | 1 |Primäres Gehäuse |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Controller 0 |
   | 5 |Controller 1 |
   | 6 |EBOD-Controller 0 |
   | 7 |EBOD-Controller 1 |
   | 8 |EBOD-Gehäuse |
   | 9 |Power Distribution Units |

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Austauschen von StorSimple-Hardwarekomponenten](storsimple-8000-hardware-component-replacement.md).

