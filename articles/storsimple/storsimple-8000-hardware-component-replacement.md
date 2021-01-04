---
title: Austauschen von Hardwarekomponenten auf Geräten der StorSimple 8000-Serie | Microsoft-Dokumentation
description: Beschreibt, wie PCMs, Batterien, Controllermodule, EBOD-Controller, Laufwerke und Gehäuse eines StorSimple-Gerät sicher ausgetauscht werden.
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
ms.custom: ''
ms.openlocfilehash: 12ab5a9598cc0222f5a3e64985be2e2ea9e7e2fd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014855"
---
# <a name="replace-a-hardware-component-on-your-storsimple-8000-series-device"></a>Austauschen von Hardwarekomponenten auf Geräten der StorSimple 8000-Serie

## <a name="overview"></a>Übersicht
In den Tutorials zum Austausch von Hardwarekomponenten werden die Hardwarekomponenten Ihres Microsoft Azure StorSimple-Gerät der Serie 8000 sowie die erforderlichen Schritte zum Ausbauen und Austauschen dieser Komponenten erläutert. Dieser Artikel beschreibt die Sicherheitssymbole und enthält Verweise auf die ausführlichen Tutorials. Darüber hinaus sind die austauschbaren Komponenten aufgeführt.

> [!IMPORTANT]
> Bevor Sie versuchen, StorSimple-Komponenten auszubauen oder auszutauschen, sollten Sie sich mit den [Konventionen für Sicherheitssymbole](#safety-icon-conventions) und anderen [Sicherheitsvorkehrungen](storsimple-8000-safety.md) vertraut machen.

### <a name="safety-icon-conventions"></a>Konventionen für Sicherheitssymbole

In der folgenden Tabelle sind die in diesen Tutorials verwendeten Sicherheitssymbole beschrieben. Achten Sie bei den Schritten zum Ausbauen und Austauschen von Gerätekomponenten auf diese Sicherheitssymbole.

| Symbol | Text | Zusätzliche Informationen |
|:--- |:--- |:--- |
| ![Warnungssymbol](./media/storsimple-hardware-component-replacement/Warning.png) |**GEFAHR!** |Zeigt eine Gefahrensituation an, die zum Tod oder schweren Verletzungen führen wird, wenn sie nicht vermieden wird. Dieses Signalwort wird nur für die extremsten Situationen verwendet. |
| ![Warnungssymbol](./media/storsimple-hardware-component-replacement/Warning.png) |**WARNUNG!** |Zeigt eine Gefahrensituation an, die zum Tod oder schweren Verletzungen führen könnte, wenn sie nicht vermieden wird. |
| ![Symbol "Vorsicht"](./media/storsimple-hardware-component-replacement/Caution.png) |**VORSICHT!** |Zeigt eine Gefahrensituation an, die zu leichten bis mittelschweren Verletzungen führen könnte, wenn sie nicht vermieden wird. |
| ![Symbol "Hinweis"](./media/storsimple-hardware-component-replacement/NoticeIcon.png) |**HINWEIS:** |Zeigt als wichtig erachtete Informationen an, die allerdings nicht mit Gefahren in Verbindung stehen. |
| ![Symbol "Stromschlag"](./media/storsimple-hardware-component-replacement/Electric.png) |**Gefahr eines Stromschlags** |Gibt Hochspannung an. |
| ![Symbol für hohes Gewicht](./media/storsimple-hardware-component-replacement/Weight.png) |**Hohes Gewicht** | |
| ![Symbol "Keine durch Benutzer zu wartenden Teile"](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png) |**Keine vom Benutzer zu wartenden Teile** |Zugriff nur bei ausreichender Schulung. |
| ![Symbol "Anweisungen lesen"](./media/storsimple-hardware-component-replacement/ReadInstructions.png) |**Alle Anleitungen zuvor lesen** | |
| ![Symbol "Kippgefahr"](./media/storsimple-hardware-component-replacement/TipHazard.png) |**Kippgefahr** | |

### <a name="before-you-begin"></a>Voraussetzungen

Machen Sie sich mit den sicherheitsrelevanten Informationen zu Ihrem Gerät und den in diesem Tutorial verwendeten Sicherheitssymbolen vertraut. Umfassende Informationen finden Sie unter [Sichere Installation und Verwendung des StorSimple-Geräts](storsimple-8000-safety.md) . Überprüfen Sie die [Sicherheitsvorkehrungen](storsimple-8000-safety.md#handling-precautions) , bevor Sie Ihr StorSimple-Gerät verwenden.

Bevor Sie versuchen, eine Komponente auszutauschen, beachten Sie die folgenden Informationen.

![Symbol „Warnung“](./media/storsimple-hardware-component-replacement/Warning.png) ![Symbol „Vorsicht Stromschlag“](./media/storsimple-hardware-component-replacement/Electric.png) **WARNUNG!**

* Erden Sie sich ordnungsgemäß mithilfe elektrostatischer Entladung oder einer Antistatikmatte, bevor Sie mit Modulen und Komponenten Ihres StorSimple-Gerät-Geräts hantieren.
* Berühren Sie keine elektrischen Schaltungen. Verwenden Sie die bereitgestellten Griffe und Führungen, wenn Sie mit Komponenten hantieren, die ggf. elektrische Schaltungen haben.

![Symbol „Warnung“](./media/storsimple-hardware-component-replacement/Warning.png) ![Symbol „Hinweis“](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **HINWEIS:**

Wenn Sie ein Modul austauschen, lassen Sie **NIEMALS ein Fach auf der Rückseite des Gehäuses leer**. Besorgen Sie ein Austausch- oder leeres Modul, bevor Sie das fehlerhafte Teil ausbauen.

## <a name="hardware-component-replacement-procedures"></a>Vorgehensweisen zum Austausch von Hardwarekomponenten

Ihr StorSimple-Gerät der Serie 8000 besteht aus mehreren Steckmodulen im primären und/oder EBOD-Gehäuse. Das Modell 8100 hat ein einziges primäres Gehäuse, während das Modell 8600 ein Gerät mit zwei Gehäusen (einem primären Gehäuse und einem EBOD-Gehäuse) ist.

Die wesentlichen Hardwarekomponenten des Geräts sind in der folgenden Tabelle zusammengefasst. Klicken Sie in der Spalte **Austauschverfahren** auf den Link, um zum dazugehörigen Tutorial zu gelangen.

| Komponenten | Vorhandene Anzahl | Steckmodul | Austauschverfahren |
|:--- |:--- |:--- |:--- |
| Gehäuse |1 |Nein |[Austauschen des Gehäuses des StorSimple-Geräts](storsimple-8000-chassis-replacement.md) |
| Primäre Controller |2 |Ja |[Austauschen eines Controllermoduls eines StorSimple-Geräts](storsimple-8000-controller-replacement.md) |
| Module für Stromversorgung und Kühlung (PCMs) mit 764 W |2 |Ja |[Austauschen eines Moduls für Stromversorgung und Kühlung eines StorSimple-Geräts](storsimple-8000-power-cooling-module-replacement.md) |
| Pufferbatterie |2 |Ja |[Austauschen des Pufferbatteriemoduls eines StorSimple-Geräts](storsimple-8000-battery-replacement.md) |
| Festplattenlaufwerke |12 |Ja |[Austauschen eines Festplattenlaufwerks im StorSimple-Gerät](storsimple-8000-disk-drive-replacement.md) |

**Tabelle 1** Hardwarekomponenten im primären Gehäuse

Das primäre Gehäuse und das EBOD-Gehäuse unterscheiden sich bei den E/A-Modulen. Außerdem haben die PCMs unterschiedliche Wattzahlen. Die PCMs im primären Gehäuse haben 764 W, wogegen die PCMs im EBOD-Gehäuse 580 W haben. Zusätzlich enthalten die PCMs im primären Gehäuse ein Pufferbatteriemodul.

| Komponenten | Vorhandene Anzahl | Steckmodul | Austauschverfahren |
|:--- |:--- |:--- |:--- |
| Gehäuse |1 |Nein |[Austauschen des Gehäuses des StorSimple-Geräts](storsimple-8000-chassis-replacement.md) |
| EBOD-Controller |2 |Ja |[Austauschen des EBOD-Controllers eines StorSimple-Geräts](storsimple-8000-ebod-controller-replacement.md) |
| Module für Stromversorgung und Kühlung (PCMs) mit 580 W |2 |Ja |[Austauschen eines Moduls für Stromversorgung und Kühlung eines StorSimple-Geräts](storsimple-8000-power-cooling-module-replacement.md) |
| Festplattenlaufwerke |12 |Ja |[Austauschen eines Festplattenlaufwerks im StorSimple-Gerät](storsimple-8000-disk-drive-replacement.md) |

**Tabelle 2** Hardwarekomponenten im EBOD-Gehäuse

Die Steckmodule im Gerät sind in den folgenden Abbildungen der Vorder- und Rückansicht markiert. Anhand dieser Abbildungen können Sie die Einbaupositionen der verschiedenen Steckmodule ermitteln, wenn ein Austausch erforderlich ist. In der Vorderansicht sind die Laufwerke zu sehen. In der Rückansicht des EBOD-Gehäuses und des primären Gehäuses sind die Steckmodule zu sehen.

![Vorderseite des Geräts mit Festplattenlaufwerken](./media/storsimple-hardware-component-replacement/IC741028.png)

**Abbildung 1** Vorderseite des Geräts

| Bezeichnung | BESCHREIBUNG |
|:--- |:--- |
| 0-11 |Festplattenlaufwerke (insgesamt 12) |

Sowohl das primäre Gehäuse als auch das EBOD-Gehäuse haben Laufwerksträgermodule. Das Gehäuse enthält zwölf 3,5-Zoll-Laufwerke, die in einem 3x4-Format angeordnet sind.

![Rückwand des Geräts – Module für primäre Gehäuse](./media/storsimple-hardware-component-replacement/IC740994.png)

**Abbildung 2** Rückseite des primären Gehäuses

| Bezeichnung | BESCHREIBUNG |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |Controller 0 |
| 4 |Controller 1 |

![Rückwand des Geräts – Steckmodule für EBOD-Gehäuse](./media/storsimple-hardware-component-replacement/IC769599.png)

**Abbildung 3** Rückseite des EBOD-Gehäuses

| Bezeichnung | BESCHREIBUNG |
|:--- |:--- |
| 1 |PCM 0 |
| 2 |PCM 1 |
| 3 |EBOD-Controller 0 |
| 4 |EBOD-Controller 1 |

## <a name="field-replaceable-units"></a>FRUs (Field Replaceable Units)

Die folgenden FRUs (Field Replaceable Units) sind für Ihr StorSimple-Gerät verfügbar:

* Gehäuse (einschließlich des integrierten Bedienfelds)
* PCM mit 764 W, Wechselstrom
* PCM mit 580 W, Wechselstrom
* Festplattenlaufwerk mit Laufwerkträgermodul
* Controllermodul
* EBOD-Controllermodul
* Pufferbatteriemodul
* Schienensatz für Gestellmontage

[Wenden Sie sich an den Microsoft-Support](storsimple-8000-contact-microsoft-support.md) , um diese Ersatzteile zu bestellen.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die [Sicherheitsinformationen](storsimple-8000-safety.md) vollständig, bevor Sie versuchen, eine StorSimple-Hardwarekomponente auszutauschen.
