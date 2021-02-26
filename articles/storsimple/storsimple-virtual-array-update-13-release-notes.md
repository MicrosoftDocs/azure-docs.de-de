---
title: Versionsanmerkungen zu Update 1.3 für Microsoft Azure StorSimple Virtual Array | Microsoft-Dokumentation
description: Dieser Artikel beschreibt wichtige offene Probleme und Lösungen für Azure StorSimple Virtual Array mit Update 1.3.
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 01/22/2021
ms.author: alkohli
ms.openlocfilehash: 52b30730ee8ab126521ad0760204ee48ef6a63fe
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576007"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>Versionsanmerkungen zu Update 1.3 für StorSimple Virtual Array

Die folgenden Versionsanmerkungen beschreiben die wichtigsten offenen und gelösten Probleme für Updates des Microsoft Azure StorSimple Virtual Arrays.

Die Anmerkungen zu dieser Version werden fortlaufend aktualisiert. Wenn schwerwiegende Probleme festgestellt werden, die eine Problemumgehung erfordern, werden sie hinzugefügt. Lesen Sie vor der Bereitstellung Ihres StorSimple Virtual Arrays die Informationen in den Versionsanmerkungen sorgfältig durch.

Update 1.3 entspricht der Softwareversion 10.0.10319.0.

> [!IMPORTANT]
> - Update 1.3 ist ein wichtiges Update. Wir empfehlen Ihnen dringend, es so schnell wie möglich zu installieren.
> - Sie können Update 1.3 nur auf Geräten installieren, auf denen Update 1.2 ausgeführt wird.
> - Die Updates führen zu einer Unterbrechung und starten Ihr Gerät neu. Falls gerade E/A-Vorgänge ausgeführt werden, kommt es auf dem Gerät zu Ausfällen. Ausführliche Anweisungen zu Paketen, mit denen dieses Update angewendet wird, finden Sie unter [Herunterladen von Update 1.3](#download-update-13).

## <a name="whats-new-in-update-13"></a>Neuerungen in Update 1.3

Dieses Update enthält die folgenden Verbesserungen: KB4540725

- Transport Layer Security (TLS) 1.2 ist ein obligatorisches Update und muss installiert werden. Ab dieser Version wird TLS 1.2 das Standardprotokoll für die gesamte Kommunikation im Azure-Portal.
- Garbage Collection-Fehlerbehebungen, die die Leistung des Garbage Collection-Zyklus verbessern, wenn sich das Geräte- und das Speicherkonto in zwei entfernten Regionen befinden.
- Fix für Sicherungsfehler aufgrund von Blobtimeouts.
- Aktualisierte OS/. NET Framework-Sicherheitspatches:
  - [KB4540725](https://support.microsoft.com/topic/servicing-stack-update-for-windows-8-1-rt-8-1-and-server-2012-r2-march-10-2020-cfa082a3-0b58-a8a3-7dc7-ab424de91b86): SSU (Wartungsstapelaktualisierung) vom März 2020
  - [KB4565541](https://support.microsoft.com/topic/july-14-2020-kb4565541-monthly-rollup-fed6b2b1-3d23-5981-34df-9215a8d8ce01): Rollup vom Juli 2020
  - [KB4565622](https://support.microsoft.com/topic/security-and-quality-rollup-for-net-framework-4-6-4-6-1-4-6-2-4-7-4-7-1-4-7-2-for-windows-8-1-rt-8-1-and-windows-server-2012-r2-kb4565622-b7320848-1889-a624-da01-719f55ee8a00): .NET Framework-Update Juli 2020

## <a name="download-update-13"></a>Update 1.3 herunterladen

Zum Herunterladen dieses Updates wechseln Sie zum [Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Home.aspx)-Server. Laden Sie dann das Paket KB4575898 herunter. Dieses Paket enthält die folgenden Pakete:

- **KB4540725**, das kumulative Windows-Updates für 2012 R2 bis März 2020 enthält. Weitere Informationen zu den Inhalten dieses Rollups finden Sie im [monatlichen Sicherheitsrollup für März](https://support.microsoft.com/help/4540725).
- **KB4565541**, das kumulative Windows-Updates für 2012 R2 bis Juli 2020 enthält. Weitere Informationen zu den Inhalten dieses Rollups finden Sie im [monatlichen Sicherheitsrollup für Februar](https://support.microsoft.com/help/4565541).
- **KB4565622**, das kumulative .NET Framework-Updates bis Juli 2020 enthält. Weitere Informationen zu den Inhalten dieses Rollups finden Sie im [monatlichen Sicherheitsrollup für Februar](https://support.microsoft.com/help/4565622).
- **KB3011067**, das Gerätesoftwareupdates enthält.

Laden Sie KB4575898 herunter, und folgen Sie diesen Anweisungen, um [das Update über die lokale Webbenutzeroberfläche anzuwenden](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="known-issues-in-update-13"></a>Bekannte Probleme in Update 1.3
Es wurden keine neuen Probleme in die Versionsanmerkungen für Update 1.3 aufgenommen. Alle in den Versionsanmerkungen enthaltenen Probleme werden aus früheren Versionen übernommen. Eine Zusammenfassung der bekannten Problemen aus früheren Versionen finden Sie unter [Bekannte Probleme in Update 1.2](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12).

## <a name="next-steps"></a>Nächste Schritte
Laden Sie KB4575898 herunter, und [wenden Sie das Update über die lokale Webbenutzeroberfläche an](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui).

## <a name="references"></a>References
Suchen Sie nach älteren Versionsanmerkungen? Wechseln Sie zu:

- [Versionsanmerkungen zu Update 1.2 für StorSimple Virtual Array](./storsimple-virtual-array-update-12-release-notes.md)
- [Versionsanmerkungen zu Update 1.0 für StorSimple Virtual Array](./storsimple-virtual-array-update-1-release-notes.md)
- [Versionsanmerkungen zu Update 0.6 für StorSimple Virtual Array](./storsimple-virtual-array-update-06-release-notes.md)
- [Versionsanmerkungen zu Update 0.5 für StorSimple Virtual Array](./storsimple-virtual-array-update-05-release-notes.md)
- [Versionsanmerkungen zu Update 0.4 für StorSimple Virtual Array](./storsimple-virtual-array-update-04-release-notes.md)
- [Versionsanmerkungen zu Update 0.3 für StorSimple Virtual Array](./storsimple-ova-update-03-release-notes.md)
- [Versionsanmerkungen zu Update 0.1 und Update 0.2 für das StorSimple Virtual Array](./storsimple-ova-update-01-release-notes.md)
- [StorSimple Virtual Array – Version mit allgemeiner Verfügbarkeit – Versionsanmerkungen](https://review.docs.microsoft.com/en-us/azure/storsimple/storsimple-ova-pp-release-notes)