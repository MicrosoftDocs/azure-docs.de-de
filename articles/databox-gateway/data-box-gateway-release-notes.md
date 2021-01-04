---
title: Anmerkungen zum Release zur allgemeinen Verfügbarkeit von Azure Data Box Gateway | Microsoft-Dokumentation
description: In diesem Artikel werden schwerwiegende offene Probleme und Lösungen im Release zur allgemeinen Verfügbarkeit von Azure Data Box Gateway beschrieben.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 2984f7990b9570c5ec57633de7f7e50162fb6f46
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581091"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Anmerkungen zum Release zur allgemeinen Verfügbarkeit von Azure Data Box Edge/Azure Data Box Gateway

## <a name="overview"></a>Übersicht

In den folgenden Versionshinweisen werden die schwerwiegenden offenen und die gelösten Probleme für das Release zur allgemeinen Verfügbarkeit (General Availability, GA) von Azure Data Box Edge und Azure Data Box Gateway beschrieben. 

Die Versionshinweise werden fortlaufend aktualisiert, und wenn schwerwiegende Probleme festgestellt werden, die eine Problemumgehung erfordern, werden sie hinzugefügt. Lesen Sie vor der Bereitstellung Ihres Data Box Edge/Data Box Gateway die Informationen in den Anmerkungen zu dieser Version sorgfältig durch.

Das Release zur allgemeinen Verfügbarkeit entspricht den folgenden Softwareversionen:

- **Data Box Gateway 1903 (1.5.814.447)**
- **Data Box Edge 1903 (1.5.814.447)**


## <a name="whats-new"></a>Neues

- **Neue Images für virtuelle Datenträger** – Im Azure-Portal stehen jetzt neue VHDX- und VMDK-Dateien zur Verfügung. Laden Sie diese Images zum Bereitstellen und Konfigurieren von neuen Data Box Gateway-Geräten zur allgemeinen Verfügbarkeit herunter. Die in den früheren Vorschauversionen erstellten Data Box Gateway-Geräte können nicht auf diese Version aktualisiert werden. Weitere Informationen finden Sie unter [Vorbereiten der Bereitstellung von Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- **NFS-Unterstützung** – NFS-Unterstützung steht derzeit in der Vorschauversion sowie für v3.0- und v4.1-Clients zur Verfügung, die auf die Data Box Edge- und Data Box Gateway-Geräte zugreifen.
- **Speicherresilienz** – Ihr Data Box Edge-Gerät kann dem Ausfall eines Datenträgers mit dem Feature „Speicherresilienz“ standhalten. Diese Funktion steht derzeit als Vorschau zur Verfügung. Sie können die Speicherresilienz aktivieren, indem Sie in der lokalen Webbenutzeroberfläche unter den **Speichereinstellungen** die Option **Resilient** auswählen.


## <a name="known-issues-in-ga-release"></a>Bekannte Probleme im Release zur allgemeinen Verfügbarkeit

Die folgende Tabelle enthält eine Zusammenfassung der bekannten Probleme bei der Data Box Gateway-Version.

| Nein. | Funktion | Problem | Problemumgehung/Kommentare |
| --- | --- | --- | --- |
| **1.** |Dateitypen | Die folgenden Dateitypen werden nicht unterstützt: Zeichendateien, Blockdateien, Sockets, Pipes, symbolische Verknüpfungen.  |Das Kopieren dieser Dateien führt dazu, dass auf der NFS-Freigabe Dateien mit Nulllänge erstellt werden. Diese Dateien verbleiben in einem Fehlerzustand und werden außerdem in *error.xml* gemeldet. <br> Symbolische Verknüpfungen mit Verzeichnissen führen dazu, dass Verzeichnisse niemals als offline markiert werden. Infolgedessen wird möglicherweise kein graues Kreuz auf den Verzeichnissen angezeigt, mit dem gekennzeichnet wird, dass die Verzeichnisse offline sind und alle zugehörigen Inhalte vollständig nach Azure hochgeladen wurden. |
| **2.** |Löschen | Aufgrund eines Fehlers in dieser Version wird beim Löschen einer NFS-Freigabe die Freigabe möglicherweise nicht gelöscht. Als Status der Freigabe wird *Wird gelöscht* angezeigt.  |Dies erfolgt nur, wenn die Freigabe einen nicht unterstützten Dateinamen verwendet. |
| **3.** |Kopieren | Beim Kopieren von Daten wird folgender Fehler ausgegeben:  Der angeforderte Vorgang konnte aufgrund einer Dateisystemeinschränkung nicht ausgeführt werden.  |Der alternative Datenstrom (ADS), der einer Dateigröße von mehr als 128KB zugeordnet wurde, wird nicht unterstützt.   |


## <a name="next-steps"></a>Nächste Schritte

- [Vorbereiten der Bereitstellung von Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
- [Vorbereiten der Bereitstellung von Azure Data Box Edge](../databox-online/azure-stack-edge-deploy-prep.md)
