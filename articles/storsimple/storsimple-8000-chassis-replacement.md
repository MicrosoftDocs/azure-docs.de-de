---
title: Ersetzen des Gehäuses eines Geräts der StorSimple 8000-Serie | Microsoft-Dokumentation
description: Beschreibt das Entfernen und Ersetzen des primären StorSimple- oder EBOD-Gehäuses.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 7dfc39f4d08c8a49d1564a0a5bd7e3ef4156e3fe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61312444"
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Ersetzen des Gehäuses des StorSimple-Geräts
## <a name="overview"></a>Übersicht
In diesem Tutorial wird erläutert, wie Sie das Gehäuse eines StorSimple-Geräts der 8000-Serie austauschen oder entfernen. Das StorSimple-Modell 8100 ist ein Gerät mit einem einzelnen Gehäuse, das Modell 8600 ein Gerät mit zwei Gehäusen. Beim Modell 8600 können potenziell zwei Gehäuse im Gerät ausfallen: das primäre Gehäuse oder das EBOD-Gehäuse.

In beiden Fällen wird ein leeres Ersatzgehäuse von Microsoft ausgeliefert. Die Gehäuse enthalten keine Stromversorgungs- und Kühleinheiten (PCMs), Controllermodule, SSDs (Solid-State-Festplatten), HDDs (Festplattenlaufwerke) oder EBOD-Module.

> [!IMPORTANT]
> Vor dem Entfernen und Austauschen des Gehäuses überprüfen Sie die Sicherheitsinformationen unter [Austauschen von StorSimple-Hardwarekomponenten](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-chassis"></a>Entfernen des Gehäuses
Führen Sie die folgenden Schritte aus, um das Gehäuse des StorSimple-Geräts zu entfernen.

#### <a name="to-remove-a-chassis"></a>So entfernen Sie ein Gehäuse
1. Stellen Sie sicher, dass das StorSimple-Gerät ausgeschaltet und von der Stromversorgung getrennt ist.
2. Entfernen Sie ggf. alle Netzwerk- und SAS-Kabel.
3. Entfernen Sie die Einheit aus dem Rack.
4. Entfernen Sie alle Laufwerke, und notieren Sie sich die Steckplätze, aus denen sie entfernt werden. Weitere Informationen finden Sie unter [Entfernen der Festplatte](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive).
5. Entfernen Sie die EBOD-Controllermodule aus dem EBOD-Gehäuse (falls dies das fehlerhafte Gehäuse ist). Weitere Informationen finden Sie unter [Entfernen eines EBOD-Controllers](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller).
   
    Entfernen Sie die Controller aus dem primären Gehäuse (falls dies das fehlerhafte Gehäuse ist), und notieren Sie sich die Steckplätze, aus denen sie entfernt werden. Weitere Informationen finden Sie unter [Entfernen eines Controllers](storsimple-8000-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Installieren des Gehäuses
Führen Sie die folgenden Schritte aus, um das Gehäuse in Ihrem StorSimple-Gerät zu installieren.

#### <a name="to-install-a-chassis"></a>So installieren Sie ein Gehäuse
1. Installieren Sie das Gehäuse im Rack. Weitere Informationen finden Sie unter [Einbauen des StorSimple 8100-Geräts in ein Rack](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) oder [Einbauen des StorSimple 8600-Geräts in ein Rack](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. Nachdem Sie das Gehäuse in das Rack eingebaut haben, installieren Sie die Controllermodule an den gleichen Positionen, an denen sie sich zuvor befanden.
3. Installieren Sie die Laufwerke an den gleichen Positionen und Steckplätzen, an denen sie zuvor installiert waren.
   
   > [!NOTE]
   > Wir empfehlen, dass Sie zuerst die SSDs installieren und dann die Festplatten.
  
4. Nach dem Einbau des Geräts und der Installation der Komponenten verbinden Sie das Gerät mit den entsprechenden Stromquellen, und schalten Sie es ein. Weitere Informationen finden Sie unter [Verkabeln des StorSimple 8100-Geräts](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) bzw. [Verkabeln des StorSimple 8600-Geräts](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Austauschen von StorSimple-Hardwarekomponenten](storsimple-8000-hardware-component-replacement.md).

