---
title: 'Verwenden des aktiven Lernens mit Wissensdatenbank: QnA Maker'
description: Erfahren Sie, wie Sie durch aktives Lernen die Qualität Ihrer Wissensdatenbank verbessern können. Sie können Einträge überprüfen, annehmen oder ablehnen und hinzufügen, ohne vorhandene Fragen zu entfernen oder zu ändern.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 1ac5e32b454cfc1adafb1f54b01d2a1a302908a4
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96600434"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Nutzen des aktiven Lernens zum Verbessern Ihrer Wissensdatenbank

Durch [aktives Lernen](../Concepts/active-learning-suggestions.md) lässt sich die Qualität Ihrer Wissensdatenbank verbessern, indem alternative Fragen vorgeschlagen werden. Benutzereingaben werden berücksichtigt und als Vorschläge in der Liste mit alternativen Fragen angezeigt. Sie können diese Vorschläge entweder als alternative Fragen hinzufügen oder ablehnen.

Ihre Wissensdatenbank wird nicht automatisch geändert. Damit Änderungen wirksam werden, müssen Sie die Vorschläge akzeptieren. Mit diesen Vorschlägen werden Fragen hinzugefügt, vorhandene Fragen werden aber weder geändert noch entfernt.


## <a name="upgrade-runtime-version-to-use-active-learning"></a>Upgraden der Laufzeitversion, um aktives Lernen zu nutzen

Das Feature für aktives Lernen wird in der Runtimeversion 4.4.0 und höher unterstützt. Wenn Ihre Wissensdatenbank mit einer Vorgängerversion erstellt wurde, [führen Sie ein Upgrade Ihrer Runtime durch](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates), um dieses Feature zu nutzen.

## <a name="turn-on-active-learning-for-alternate-questions"></a>Aktivieren des aktiven Lernens für alternative Fragen

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

Das Feature für aktives Lernen ist standardmäßig deaktiviert. Aktivieren Sie das Feature, um die vorgeschlagenen Fragen anzuzeigen. Nachdem Sie aktives Lernen aktiviert haben, müssen Sie Informationen aus der Client-App an QnA Maker senden. Weitere Informationen finden Sie unter [Architekturfluss für die Verwendung der GenerateAnswer- und der Trainings-API über einen Bot](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Wählen Sie **Veröffentlichen** aus, um die Wissensdatenbank zu veröffentlichen. Aktive Lernabfragen werden nur vom Vorhersageendpunkt der GenerateAnswer-API erfasst. Die Abfragen im Bereich „Test“ im QnA Maker-Portal besitzen keine Auswirkungen auf das aktive Lernen.

1. Wenn Sie aktives Lernen im QnA Maker-Portal aktivieren möchten, wählen Sie in der rechten oberen Ecke Ihren **Namen** aus, und navigieren Sie zu [**Diensteinstellungen**](https://www.qnamaker.ai/UserSettings).

    ![Aktivieren Sie auf der Seite „Diensteinstellungen“ Alternativen für die vom Feature für aktives Lernen vorgeschlagenen Fragen. Wählen Sie im Menü rechts oben Ihren Benutzernamen und anschließend „Diensteinstellungen“ aus.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Suchen Sie nach dem QnA Maker-Dienst, und aktivieren Sie die Option **Active Learning**.

    > [!div class="mx-imgBorder"]
    > [![Aktivieren Sie auf der Seite „Diensteinstellungen“ das Feature für aktives Lernen. Wenn Sie das Feature nicht aktivieren können, müssen Sie möglicherweise ein Upgrade des Diensts ausführen.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > Die genaue Version auf der vorherigen Abbildung ist nur als Beispiel dargestellt. Ihre Version kann abweichen.

    Nachdem Sie **Aktives Lernen** aktiviert haben, schlägt die Wissensdatenbank basierend auf den von Benutzern übermittelten Fragen in regelmäßigen Abständen neue Fragen vor. Sie können **Active Learning** deaktivieren, indem Sie die Einstellung wieder umschalten.
    
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

Das Feature für aktives Lernen ist in QnA Maker verwaltet (Vorschau) standardmäßig **aktiviert**. Verwenden Sie zum Anzeigen der vorgeschlagenen alternativen Fragen die [Anzeigeoptionen](../How-To/improve-knowledge-base.md#view-suggested-questions) auf der Seite „Bearbeiten“.

---

## <a name="review-suggested-alternate-questions"></a>Überprüfen von Vorschlägen für alternative Fragen

Auf der Seite **Bearbeiten** jeder Wissensdatenbank können Sie die [Vorschläge für alternative Fragen überprüfen](improve-knowledge-base.md).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Wissensdatenbank](./manage-knowledge-bases.md)
