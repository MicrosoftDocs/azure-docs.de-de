---
title: 'Verwalten von Versionen: LUIS'
titleSuffix: Azure Cognitive Services
description: Mithilfe von Versionen können Sie verschiedene Modelle erstellen und veröffentlichen. Es ist eine bewährte Methode, das aktuell aktive Modell in eine andere Version der App zu klonen, bevor Änderungen am Modell vorgenommen werden.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: b93e9fd4047ee3731ef6fe4652bb92ce5145987d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018819"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Verwenden von Versionen, um Staging- und Produktions-Apps nicht durch Bearbeitungsschritte oder Tests zu beeinträchtigen

Mithilfe von Versionen können Sie verschiedene Modelle erstellen und veröffentlichen. Es ist eine bewährte Methode, das aktuell aktive Modell in eine andere [Version](./luis-concept-app-iteration.md) der App zu klonen, bevor Änderungen am Modell vorgenommen werden.

Die aktive Version ist die Version, die Sie im LUIS-Portal im Abschnitt **Erstellen** mit Absichten, Entitäten, Features und Mustern bearbeiten. Bei Verwendung der Erstellungs-APIs muss die aktive Version nicht festgelegt werden, da die versionsspezifischen REST-API-Aufrufe die Version in der Route enthalten.

Öffnen Sie zum Arbeiten mit Versionen Ihre App, indem Sie den jeweiligen Namen auf der Seite **Meine Apps**, dann in der oberen Leiste **Verwalten** und anschließend im linken Navigationsbereich **Versionen** auswählen.

Aus der Liste der Versionen geht hervor, welche Versionen veröffentlicht wurden, wo sie veröffentlicht wurden und welche Version aktuell aktiv ist.

## <a name="clone-a-version"></a>Klonen einer Version

1. Wählen Sie die Version aus, die Sie klonen möchten, und wählen Sie dann auf der Symbolleiste **Klonen** aus.

2. Geben Sie im Dialogfeld **Version klonen** einen Namen für die neue Version ein, z.B. „0.2“.

   ![Dialogfeld „Version klonen“](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)

     > [!NOTE]
     > Die Versions-ID darf nur Zeichen, Ziffern oder „.“ enthalten und nicht länger als 10 Zeichen sein.

   Eine neue Version mit dem angegebenen Namen wird erstellt und als aktive Version festgelegt.

## <a name="set-active-version"></a>Festlegen der aktiven Version

Wählen Sie in der Liste eine Version und dann auf der Symbolleiste **Aktivieren** aus.

## <a name="import-version"></a>Importversion

Sie können eine `.json`- oder eine `.lu`-Version Ihrer Anwendung importieren.

1. Wählen Sie auf der Symbolleiste die Option **Importieren** und dann das Format aus.

2. Geben Sie im Popupfenster **Neue Version importieren** den neuen, aus zehn Zeichen bestehenden Namen der Version ein. Wenn die Version in der Datei in der App bereits vorhanden ist, müssen Sie lediglich eine Versions-ID festlegen.

    ![Abschnitt „Verwalten“, Seite „Versionen“, Importieren einer neuen Version](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Nachdem Sie eine Version importiert haben, wird die neue Version die aktive Version.

### <a name="import-errors"></a>Importfehler

* Tokenizer-Fehler: Wenn Sie beim Importieren einen **Tokenizer-Fehler** erhalten, versuchen Sie, eine Version zu importieren, die einen anderen [Tokenizer](luis-language-support.md#custom-tokenizer-versions) verwendet als die aktuell verwendete App. Informationen zum Beheben dieses Problems finden Sie unter [Migrieren zwischen Tokenizer-Versionen](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Andere Aktionen

* Um eine Version zu **löschen**, wählen Sie eine Version in der Liste und dann auf der Symbolleiste **Löschen** aus. Klicken Sie auf **OK**.
* Um eine Version **umzubenennen**, wählen Sie eine Version in der Liste und dann auf der Symbolleiste **Umbenennen** aus. Geben Sie den neuen Namen ein, und wählen Sie **Fertig** aus.
* Um eine Version zu **exportieren**, wählen Sie eine Version in der Liste und dann auf der Symbolleiste **Exportieren** aus. Wählen Sie JSON oder LU zum Durchführen eines Exports für die Sicherung oder zum Speichern in der Quellcodeverwaltung aus; wählen Sie **Export for container** (Für Container exportieren) aus, um [diese App in einem LUIS-Container zu verwenden](luis-container-howto.md).