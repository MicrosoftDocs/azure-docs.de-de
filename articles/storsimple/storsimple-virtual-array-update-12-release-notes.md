---
title: Versionsanmerkungen zu Update 1.2 für Microsoft Azure StorSimple Virtual Array | Microsoft-Dokumentation
description: Dieser Artikel beschreibt wichtige offene Probleme und Lösungen für StorSimple Virtual Array mit Update 1.2.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: 1118dfcec67a49365f1f6e5e522e98b97694d052
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960189"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>Versionsanmerkungen zu Update 1.2 für StorSimple Virtual Array

Die folgenden Versionsanmerkungen beschreiben die wichtigsten offenen und gelösten Probleme für Updates des Microsoft Azure StorSimple Virtual Arrays.

Die Anmerkungen zu dieser Version werden fortlaufend aktualisiert. Wenn schwerwiegende Probleme festgestellt werden, die eine Problemumgehung erfordern, werden sie hinzugefügt. Lesen Sie vor der Bereitstellung Ihres StorSimple Virtual Arrays die Informationen in den Versionsanmerkungen sorgfältig durch.

Update 1.2 entspricht der Softwareversion **10.0.10311.0**.

> [!IMPORTANT]
> - Die Updates führen zu einer Unterbrechung und starten Ihr Gerät neu. Falls gerade E/A-Vorgänge ausgeführt werden, kommt es auf dem Gerät zu Ausfällen. Ausführliche Anweisungen zu Paketen, mit denen dieses Update angewendet wird, finden Sie unter [Herunterladen von Update 1.2](#download-update-12).
> - Update 1.2 ist über das Azure-Portal nur verfügbar, wenn auf Ihrem Gerät Update 1.0 oder 1.1 ausgeführt wird.

## <a name="whats-new-in-update-12"></a>Neuerungen in Update 1.2

Dieses Update enthält die folgenden Fehlerbehebungen:

- Verbesserte Resilienz bei der Verarbeitung gelöschter Dateien
- Verbesserte Behandlung von Ausnahmen im Startpfad des Codes, was zu weniger Fehlern bei Sicherung, Wiederherstellung, Cloudlesevorgängen und automatisierter Speicherplatzrückgewinnung führt

## <a name="download-update-12"></a>Herunterladen von Update 1.2

Zum Herunterladen dieses Updates wechseln Sie zum [Microsoft Update-Katalog](https://www.catalog.update.microsoft.com/Home.aspx), und laden Sie das Paket KB4502035 herunter. Dieses Paket enthält die folgenden Pakete:

 - **KB4493446**, das kumulative Windows-Updates für 2012 R2 bis April 2019 enthält. Weitere Informationen zu den Inhalten dieses Rollups finden Sie im [monatlichen Sicherheitsrollup für April](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446).
 - **KB3011067**, bei dem es sich um eine eigenständige Paketdatei von Microsoft Update, WindowsTH-KB3011067-x64, handelt. Diese Datei wird verwendet, um die Gerätesoftware zu aktualisieren.

Laden Sie KB4502035 herunter, und folgen Sie diesen Anweisungen, um [das Update über die lokale Webbenutzeroberfläche anzuwenden](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="issues-fixed-in-update-12"></a>Behobene Probleme in Update 1.2

Die folgende Tabelle enthält eine Zusammenfassung der Probleme, die in dieser Version behoben wurden:

| Nein. | Funktion | Problem |
| --- | --- | --- |
| 1 |Löschen| In früheren Versionen der Software gab es ein Problem, dass sich die Nutzung des Geräts nicht änderte, selbst wenn Dateien gelöscht wurden. Dieses Problem wurde in dieser Version behoben. Das Tiering des Codepfads ist nun bei der Verarbeitung gelöschter Dateien resilienter.|
| 2 |Ausnahmebehandlung| In früheren Versionen der Software gab es ein Problem nach dem Neustart des Systems, das potenziell zu Fehlern bei Sicherung, Wiederherstellung, Lesen aus der Cloud und automatisierter Speicherplatzrückgewinnung führen konnte. Diese Version enthält Änderungen in Bezug auf die Behandlung von Ausnahmen im Startpfad.|

## <a name="known-issues-in-update-12"></a>Bekannte Probleme in Update 1.2

Es wurden keine neuen Probleme in die Versionsanmerkungen für Update 1.2 aufgenommen. Alle in den Versionsanmerkungen enthaltenen Probleme werden aus früheren Versionen übernommen. Eine Zusammenfassung der bekannten Problemen aus früheren Versionen finden Sie unter [Bekannte Probleme in Update 1.1](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11).

## <a name="next-steps"></a>Nächste Schritte

Laden Sie KB4502035 herunter, und [wenden Sie das Update über die lokale Webbenutzeroberfläche an](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="references"></a>References

Suchen Sie nach älteren Versionsanmerkungen? Wechseln Sie zu:
* [Versionsanmerkungen zu Update 1.1 für StorSimple Virtual Array](storsimple-virtual-array-update-11-release-notes.md)
* [Versionsanmerkungen zu Update 1.0 für StorSimple Virtual Array](storsimple-virtual-array-update-1-release-notes.md)
* [Versionsanmerkungen zu Update 0.6 für StorSimple Virtual Array](storsimple-virtual-array-update-06-release-notes.md)
* [Versionsanmerkungen zu Update 0.5 für StorSimple Virtual Array](storsimple-virtual-array-update-05-release-notes.md)
* [Versionsanmerkungen zu Update 0.4 für StorSimple Virtual Array](storsimple-virtual-array-update-04-release-notes.md)
* [Versionsanmerkungen zu Update 0.3 für StorSimple Virtual Array](storsimple-ova-update-03-release-notes.md)
* [Versionsanmerkungen zu Update 0.1 und Update 0.2 für das StorSimple Virtual Array](storsimple-ova-update-01-release-notes.md)
* [StorSimple Virtual Array – Version mit allgemeiner Verfügbarkeit – Versionsanmerkungen](./storsimple-virtual-array-update-06-release-notes.md)