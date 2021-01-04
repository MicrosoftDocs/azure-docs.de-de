---
title: include file
description: include file
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: include
ms.reviewer: hux
ms.custom: include file
ms.openlocfilehash: a369eb7000fb8622a69f4205ffcc232ae9c9d242
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545929"
---
Wenn Sie Daten aus dem Archivspeicher lesen möchten, müssen Sie zuerst die Ebene des Blobs in „Hot“ oder „Cool“ ändern. Dieser Prozess wird als Aktivierung bezeichnet und kann mehrere Stunden dauern. Wir empfehlen die Verwendung großer Blobs, um eine optimale Aktivierungsleistung zu erzielen. Die Dauer kann sich ggf. erhöhen, wenn mehrere kleine Blobs gleichzeitig aktiviert werden. Derzeit stehen zwei Aktivierungsprioritäten zur Verfügung: „Hohe Priorität“ und „Standardpriorität“. Diese können über die optionale Eigenschaft *x-ms-rehydrate-priority* bei Vorgängen vom Typ [Blobtarif festlegen](/rest/api/storageservices/set-blob-tier) oder [Blob kopieren](/rest/api/storageservices/copy-blob) festgelegt werden.

* **Standardpriorität**: Die Aktivierungsanforderung gemäß der Eingangsreihenfolge verarbeitet und kann bis zu 15 Stunden dauern.
* **Hohe Priorität:** Die Aktivierungsanforderung wird gegenüber Standardanforderungen bevorzugt behandelt und kann für Objekte, die kleiner als zehn GB sind, bereits nach weniger als einer Stunde abgeschlossen sein. 

> [!NOTE]
> Die Standardpriorität ist die Standardaktivierungsoption für „Archiv“. Die hohe Priorität ist eine schnellere Option, die mehr kostet als die Aktivierung mit Standardpriorität und in der Regel nur in Notfallsituationen verwendet wird, in denen Daten dringend wiederhergestellt werden müssen.
>
> Hohe Priorität kann je nach Blobgröße und aktueller Auslastung länger als eine Stunde dauern. Anforderungen mit hoher Priorität werden gegenüber Anforderungen mit Standardpriorität garantiert bevorzugt behandelt.

Sobald eine Aktivierungsanforderung initiiert wurde, kann sie nicht mehr abgebrochen werden. Während des Aktivierungsvorgangs wird die Blobeigenschaft *x-ms-access-tier* weiterhin als Archiv angezeigt, bis die Aktivierung auf einer Onlineebene abgeschlossen ist. Um den Aktivierungsstatus und den Fortschritt zu bestätigen, können Sie [Get Blob Properties](/rest/api/storageservices/get-blob-properties) (Blobeigenschaften abrufen) aufrufen, um die Blobeigenschaften *x-ms-archive-status* und *x-ms-rehydrate-priority* zu überprüfen. Je nach Zielebene der Aktivierung lautet der Archivstatus entweder „rehydrate-pending-to-hot“ (Aktivierung für heiße Ebene ausstehend) oder „rehydrate-pending-to-cool“ (Aktivierung für kalte Ebene ausstehend). Die Aktivierungspriorität zeigt die Geschwindigkeit für „Hohe Priorität“ oder „Standardpriorität“ an. Nach Abschluss werden die Eigenschaften für Archivstatus und Aktivierungspriorität entfernt, und die Eigenschaft zum Zugreifen auf die Blobebene wird entsprechend der ausgewählten Ebene „Heiß“ oder „Kalt“ aktualisiert.