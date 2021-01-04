---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/16/2020
ms.author: alkohli
ms.openlocfilehash: 27503d1d60d961bac6dd41ebfe4fb083948cb251
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96580985"
---
Sie können auch die Metriken zur Überwachung der Leistung des Geräts und in einigen Fällen zur Problembehandlung bei Geräteproblemen anzeigen.

Führen Sie die folgenden Schritte im Azure-Portal aus, um ein Diagramm für ausgewählte Gerätemetriken zu erstellen.

1. Wechseln Sie für Ihre Ressource im Azure-Portal zu **Überwachung > Metriken**, und wählen Sie **Metrik hinzufügen** aus.

    ![Hinzufügen von Metriken](media/data-box-gateway-view-metrics/view-metrics-add-metric.png)

2. Die Ressource wird automatisch aufgefüllt.  

    ![Aktuelle Ressource](media/data-box-gateway-view-metrics/view-metrics-current-resource.png)

    Um eine andere Ressource anzugeben, wählen Sie die Ressource aus. Wählen Sie auf dem Blatt **Ressource auswählen** das Abonnement, die Ressourcengruppe, den Ressourcentyp und die bestimmte Ressource aus, für die Sie die Metriken anzeigen möchten, und wählen Sie dann **Anwenden** aus.

    ![Auswählen einer anderen Ressource](media/data-box-gateway-view-metrics/view-metrics-choose-another-resource.png)

3. Wählen Sie aus der Dropdownliste eine Metrik aus, um Ihr Gerät zu überwachen. Eine vollständige Liste dieser Metriken finden Sie unter [Metriken auf Ihrem Gerät](#metrics-on-your-device).

4. Wenn eine Metrik aus der Dropdownliste ausgewählt wird, kann auch eine Aggregation definiert werden. „Aggregation“ bezeichnet den tatsächlichen Wert, der über einen bestimmten Zeitraum aggregiert wird. Die aggregierten Werte können durchschnittlich, minimal oder maximal sein. Wählen Sie die Aggregation aus „Avg“, „Max“ oder „Min“ aus.

    ![Diagramm anzeigen](media/data-box-gateway-view-metrics/view-metrics-view-chart.png)

5. Wenn die von Ihnen ausgewählte Metrik mehrere Instanzen aufweist, haben Sie die Möglichkeit zur Aufteilung. Wählen Sie **Teilen anwenden** und dann den Wert aus, der für die Aufschlüsselung verwendet werden soll.

    ![Anwenden des Teilens](media/data-box-gateway-view-metrics/view-metrics-apply-splitting.png)

6. Wenn Sie jetzt die Aufschlüsselung nur noch für einige wenige Instanzen anzeigen möchten, können Sie die Daten filtern. Wenn Sie in diesem Fall z. B. den Netzwerkdurchsatz nur für die beiden angeschlossenen Netzwerkschnittstellen auf Ihrem Gerät anzeigen möchten, können Sie diese Schnittstellen filtern. Wählen Sie **Filter hinzufügen** aus, und geben Sie den Namen der Netzwerkschnittstelle für die Filterung an.

    ![Filter hinzufügen](media/data-box-gateway-view-metrics/view-metrics-add-filter.png)

7. Sie können das Diagramm auch für den einfachen Zugriff an das Dashboard anheften.

    ![An Dashboard anheften](media/data-box-gateway-view-metrics/view-metrics-pin-to-dashboard.png)

8. Um Diagrammdaten in eine Excel-Tabelle zu exportieren oder einen Link zum Diagramm abzurufen, den Sie freigeben können, wählen Sie die Freigabeoption auf der Befehlsleiste aus.

    ![Exportieren von Daten](media/data-box-gateway-view-metrics/view-metrics-export-data.png)
