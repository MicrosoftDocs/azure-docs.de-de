---
title: Versionsverwaltung
titleSuffix: Language Understanding - Azure Cognitive Services
description: Versionen in LUIS sind mit den aus der herkömmlichen Programmierung bekannten Versionen vergleichbar. Jede Version ist eine Momentaufnahme der App. Bevor Sie Änderungen an der App vornehmen, erstellen Sie eine neue Version. Es ist einfacher, zur vorherigen Version der App zurückzukehren, als die Absicht und die Äußerungen einer App umständlich auf einen früheren Zustand zurückzusetzen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: diberry
ms.openlocfilehash: 9da79e5b744f8ba70c0e265f0d1f0126b37eba49
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60509676"
---
# <a name="understand-how-and-when-to-use-a-luis-version"></a>Grundlegendes zur Verwendung einer LUIS-Version

Versionen in LUIS sind mit den aus der herkömmlichen Programmierung bekannten Versionen vergleichbar. Jede Version ist eine Momentaufnahme der App. Bevor Sie Änderungen an der App vornehmen, erstellen Sie eine neue Version. Es ist einfacher, zur identischen vorherigen Version zurückzukehren, als Absichten und Äußerungen zu entfernen, um einen früheren Zustand zu erhalten.

Mithilfe von [Versionen](luis-how-to-manage-versions.md) können Sie unterschiedliche Modelle derselben App erstellen. 

## <a name="version-id"></a>Versions-ID
Die Versions-ID besteht aus Zeichen, Ziffern oder „.“ und darf nicht länger als 10 Zeichen sein.

## <a name="initial-version"></a>Ursprüngliche Version
Die ursprüngliche Version (0.1) ist standardmäßig die aktive Version. 

## <a name="active-version"></a>Aktive Version
Das [Festlegen einer Version](luis-how-to-manage-versions.md#set-active-version) als die aktive bedeutet, dass sie derzeit auf der [LUIS](luis-reference-regions.md)-Website bearbeitet und getestet wird. Legen Sie eine Version als aktiv fest, um auf ihre Daten zuzugreifen, Updates vorzunehmen und sie zu testen und zu veröffentlichen.

Der Name der derzeit aktiven Version wird im Bereich links oben nach dem Namen der App angezeigt. 

[![Ändern der aktiven Version](./media/luis-concept-version/version-in-nav-bar-inline.png)](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

## <a name="versions-and-publishing-slots"></a>Versionen und Veröffentlichungsslots
Sie veröffentlichen entweder im Staging- oder im Produktslot. Jeder Slot kann eine andere Version oder die gleiche Version aufweisen. Dies ist nützlich, um Unterschiede zwischen Modellversionen über den Endpunkt zu überprüfen, der für Bots oder andere Anwendungen, die LUIS aufrufen, verfügbar ist. 

## <a name="clone-a-version"></a>Klonen einer Version
Sie klonen eine Version, um eine Kopie einer vorhandenen Version zu erstellen und sie als neue Version zu speichern. Klonen Sie eine Version, um den Inhalt der vorhandenen Version als Ausgangspunkt für die neue Version zu verwenden. Nachdem Sie eine Version geklont haben, wird die neue Version die **aktive** Version. 

## <a name="import-and-export-a-version"></a>Importieren und Exportieren von Versionen
Sie können eine Version auf App-Ebene importieren. Diese Version wird die aktive Version und für die Versions-ID in der „versionId“-Eigenschaft der App-Datei verwendet. Sie können auch auf Versionsebene in eine vorhandene App importieren. Die neue Version wird die aktive Version. 

Sie können eine Version auf App- oder auf Versionsebene exportieren. Der einzige Unterschied besteht darin, dass die auf App-Ebene exportierte Version die derzeit aktive Version ist, während Sie beim Exportieren auf Versionsebene auf der Seite **[Einstellungen](luis-how-to-manage-versions.md)** die zu exportierende Version auswählen können. 

Die exportierte Datei enthält keine maschinell erlernten Informationen, da die App nach dem Importieren neu trainiert wird. Die exportierte Datei enthält keine Projektmitarbeiter – Sie müssen diese wieder hinzufügen, nachdem die Version in die neue App importiert wurde.

## <a name="export-each-version-as-app-backup"></a>Exportieren jeder Version als App-Sicherung
Um Ihre LUIS-App zu sichern, exportieren Sie jede Version auf der Seite **[Einstellungen](luis-how-to-manage-versions.md)** .

## <a name="delete-a-version"></a>Löschen von Versionen
Sie können alle Versionen außer der aktiven Version aus der Versionsliste auf der Seite „Einstellungen“ löschen. 

## <a name="version-availability-at-the-endpoint"></a>Versionsverfügbarkeit am Endpunkt
Trainierte Versionen stehen nicht automatisch am App-[Endpunkt](luis-glossary.md#endpoint) zur Verfügung. Sie müssen eine Version [veröffentlichen](luis-how-to-publish-app.md) oder erneut veröffentlichen, damit sie am App-Endpunkt verfügbar ist. Sie können für das **Staging** und die **Produktion** veröffentlichen. Damit stehen Ihnen am Endpunkt bis zu zwei Versionen zur Verfügung. Wenn Sie weitere Versionen der App an einem Endpunkt benötigen, sollten Sie die Version exportieren und in eine neue App importieren. Die neue App hat eine andere App-ID.

## <a name="collaborators"></a>Projektmitarbeiter
Der Besitzer und alle [Projektmitarbeiter](luis-how-to-collaborate.md) haben Vollzugriff auf alle Versionen der App.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie auf der Seite mit den App-Einstellungen eine [Versionsverwaltung](luis-how-to-manage-versions.md) hinzufügen. 

Erfahren Sie, wie Sie [Absichten](luis-concept-intent.md) im Modell entwerfen.
