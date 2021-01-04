---
title: Konfigurieren des Lernverhaltens
description: Der Ausbildungsmodus gibt Ihnen Vertrauen in den Personalisierungsdienst und seine Fähigkeiten zum maschinellen Lernen und stellt Ihnen die Metriken bereit, dass der Dienst Informationen erhält, von denen Sie lernen können, ohne den Onlinedatenverkehr zu gefährden.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 05/01/2020
ms.openlocfilehash: 57a03b107678f83200b11f408784f6455cbceffd
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94579290"
---
# <a name="configure-the-personalizer-learning-behavior"></a>Konfigurieren des Lernverhaltens der Personalisierung

Der [Ausbildungsmodus](concept-apprentice-mode.md) gibt Ihnen Vertrauen und Zuversicht in den Personalisierungsdienst und seine Fähigkeiten zum maschinellen Lernen und gibt Ihnen die Gewissheit, dass der Dienst Informationen erhält, von denen Sie lernen können, ohne den Onlinedatenverkehr zu gefährden.

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="configure-apprentice-mode"></a>Konfigurieren des Ausbildungsmodus

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com) für Ihre Personalisierungsressource an.

1. Wählen Sie auf der Seite **Konfiguration** auf der Registerkarte **Lernverhalten** die Option **Baselineaktion zurückgeben, im Ausbildungsmodus lernen** und dann **Speichern** aus.

> [!div class="mx-imgBorder"]
> ![Screenshot: Konfigurieren des Lernverhaltens im Ausbildungsmodus im Azure-Portal](media/settings/configure-learning-behavior-azure-portal.png)

## <a name="changes-to-the-existing-application"></a>Änderungen an der vorhandenen Anwendung

Ihre vorhandene Anwendung sollte nicht die Art und Weise ändern, wie sie derzeit anzuzeigende Aktionen auswählt oder wie die Anwendung den Wert, d. h. die **Relevanz** dieser Aktion bestimmt. Die einzige Änderung an der Anwendung könnte die Reihenfolge der Aktionen sein, die an die Rangfolge-API der Personalisierung gesendet werden. Die Aktion, die Ihre Anwendung derzeit anzeigt, wird als _erste Aktion_ in der Aktionsliste gesendet. Die [Rangfolge-API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) verwendet diese erste Aktion, um Ihr Personalisierungsmodell zu trainieren.

### <a name="configure-your-application-to-call-the-rank-api"></a>Konfigurieren der Anwendung zum Aufrufen der Rangfolge-API

Damit Sie die Personalisierung zu Ihrer Anwendung hinzufügen können, müssen Sie die Rangfolge- und Relevanz-APIs aufrufen.

1. Fügen Sie den Aufruf der [Rangfolge-API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) nach dem Punkt in Ihrer bestehenden Anwendungslogik ein, an dem Sie die Liste der Aktionen und deren Features bestimmen. Die erste Aktion in der Aktionsliste muss die durch Ihre vorhandene Logik ausgewählte Aktion sein.

1. Konfigurieren Sie Ihren Code so, dass die Aktion angezeigt wird, die mit der **Relevanzaktions-ID** der Antwort der Rangfolge-API verknüpft ist.

### <a name="configure-your-application-to-call-reward-api"></a>Konfigurieren der Anwendung zum Aufrufen der Relevanz-API

1. Verwenden Sie Ihre vorhandene Geschäftslogik, um die **Relevanz** der angezeigten Aktion zu berechnen. Der Wert muss zwischen 0 und 1 liegen. Senden Sie diese Relevanz mithilfe der [Relevanz-API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) an die Personalisierung. Der Wert für die Relevanz wird nicht sofort erwartet und kann abhängig von Ihrer Geschäftslogik über einen bestimmten Zeitraum verzögert werden.

1. Wenn Sie die Relevanz nicht innerhalb der konfigurierten **Relevanzwartezeit** zurückgeben, wird stattdessen die Standardrelevanz verwendet.

## <a name="evaluate-apprentice-mode"></a>Auswerten des Ausbildungsmodus

Überprüfen Sie im Azure-Portal auf der Seite **Auswertungen** für Ihre Personalisierungsressource die **Leistung des aktuellen Lernverhaltens**.

> [!div class="mx-imgBorder"]
> ![Screenshot: Überprüfung der Auswertung des Lernverhaltens im Ausbildungsmodus im Azure-Portal](media/settings/evaluate-apprentice-mode.png)

Der Ausbildungsmodus bietet die folgenden **Auswertungsmetriken**:
* **Baseline – durchschnittliche Relevanz**:  Durchschnittliche Relevanzen für die Standardwerte der Anwendung (Baseline).
* **Personalisierung – durchschnittliche Relevanz**: Durchschnitt der gesamten Relevanzen, die Personalisierungsprogramme potenziell erreicht hätten.
* **Erlangte Relevanzen im Verhältnis zu den letzten 1000 Ereignissen**: Verhältnis von Baseline und Personalisierungsrelevanz – normalisiert über die letzten 1000 Ereignisse.

## <a name="switch-behavior-to-online-mode"></a>Wechseln des Verhaltens in den Onlinemodus

Wenn Sie feststellen, dass die Personalisierung mit einem Durchschnitt von 75-85 % im gleitenden Durchschnitt trainiert wird, ist das Modell bereit, in den Onlinemodus zu wechseln.

Wählen Sie im Azure-Portal für Ihre Personalisierungsressource auf der Seite **Konfiguration** auf der Registerkarte **Lernverhalten** die Option **Beste Aktion zurückgeben** und dann **Speichern** aus.

Sie müssen keine Änderungen an den Aufrufen der Rangfolge- und Relevanz-APIs vornehmen.

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Modell- und Lerneinstellungen](how-to-manage-model.md)
