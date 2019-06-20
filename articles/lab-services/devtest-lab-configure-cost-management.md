---
title: Anzeigen des monatlichen geschätzten Labkostentrends in Azure DevTest Labs | Microsoft Docs
description: Informieren Sie sich über das Diagramm „Monatlicher geschätzter Kostentrend“ in Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: spelluru
ms.openlocfilehash: f761af3a5a3f08e4da89d8869aea5d666ecd69d0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60868249"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Verfolgen von Kosten eines Labs in Azure DevTest Labs
Dieser Artikel enthält Informationen dazu, wie Sie die Kosten Ihres Labs nachverfolgen. Sie erfahren darin, wie Sie den geschätzten Kostentrend des Labs für den aktuellen Kalendermonat anzeigen. Außerdem erfahren Sie, wie Sie pro Ressource im Lab die Kosten für den bisherigen Kalendermonat anzeigen.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Anzeigen des monatlichen geschätzten Labkostentrends 
In diesem Abschnitt lernen Sie, wie Sie das Diagramm **Monatlicher geschätzter Kostentrend** verwenden, um die bisherigen geschätzten Kosten für den aktuellen Kalendermonat sowie die veranschlagten Kosten am Monatsende für den aktuellen Kalendermonat anzuzeigen. Außerdem lernen Sie, wie Sie die Labkosten verwalten, indem Sie Ausgabenziele und Schwellenwerte festlegen, bei deren Erreichen das Melden der Ergebnisse durch DevTest Labs ausgelöst wird.

Führen Sie folgende Schritte aus, um das Diagramm „Monatlicher geschätzter Kostentrend“ anzuzeigen: 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.
3. Wählen Sie in der Liste mit den Labs Ihr Lab aus.  
4. Wählen Sie im linken Menü die Option **Configuration and policies** (Konfiguration und Richtlinien) aus.  
4. Wählen Sie im linken Menü im Abschnitt **Cost tracking** (Kostennachverfolgung) die Option **Cost trend** (Kostentrend) aus. Der folgende Screenshot zeigt ein Beispiel eines Kostendiagramms. 
   
    ![Kostendiagramm](./media/devtest-lab-configure-cost-management/graph.png)

    Der Wert für **Estimated cost** (Geschätzte Kosten) entspricht den geschätzten Kosten im aktuellen Kalendermonat bis zum heutigen Tag. Der Wert für **Projected cost** (Prognostizierte Kosten) entspricht den geschätzten Kosten für den gesamten aktuellen Kalendermonat, berechnet anhand der Labkosten für die vergangenen fünf Tage.

    Die Kostenbeträge werden auf die nächste ganze Zahl aufgerundet. Beispiel: 

   * 5.01 wird auf 6 aufgerundet. 
   * 5.50 wird auf 6 aufgerundet.
   * 5.99 wird auf 6 aufgerundet.

     Oberhalb des Diagramms wird darauf hingewiesen, dass es sich bei den in diesem Diagramm standardmäßig angezeigten Kosten um *geschätzte* Kosten handelt, die anhand der geltenden Preise für die [nutzungsbasierte Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/) kalkuliert werden. Sie können auch Ihre eigenen Ausgabenziele festlegen, die in den Diagrammen angezeigt werden, indem Sie die [Kostenziele für Ihr Lab verwalten](#managing-cost-targets-for-your-lab).

     Bei der Kostenkalkulation werden folgende Kosten *nicht* berücksichtigt:

   * CSP- und Dreamspark-Abonnements werden derzeit nicht unterstützt, da Azure DevTest Labs die [Azure-Abrechnungs-APIs](../billing/billing-usage-rate-card-overview.md) zum Berechnen der Labkosten verwendet und diese APIs keine CSP- oder Dreamspark-Abonnements unterstützen.
   * Sonderpreise. Zurzeit können Sie keine Sonderpreise (wie in Ihrem Abonnement angezeigt) verwenden, die Sie mit Microsoft oder Microsoft-Partnern vereinbart haben. Es werden nur Preise für die nutzungsbasierte Bezahlung verwendet.
   * Steuern.
   * Rabatte.
   * Rechnungswährung. Zurzeit werden die Labkosten nur in USD angezeigt.

### <a name="managing-cost-targets-for-your-lab"></a>Verwalten von Kostenzielen für Ihr Lab
Mit DevTest Labs können Sie die Kosten für Ihr Lab besser verwalten, indem Sie ein Ausgabenziel festlegen, das Sie dann im Diagramm „Monatlicher geschätzter Kostentrend“ anzeigen können. DevTest Labs ermöglicht auch das Senden einer Benachrichtigung, wenn das angegebene Ausgabenziel oder der Schwellenwert erreicht ist. 

1. Wählen Sie auf der Seite **Cost trend** (Kostentrend) die Option **Manage target** (Ziel verwalten) aus.

    ![Schaltfläche „Ziel verwalten“](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Geben Sie auf der Seite **Manage target** (Ziel verwalten) ein Ausgabenziel und Schwellenwerte an. Sie können auch festlegen, ob die einzelnen ausgewählten Schwellenwerte im Kostentrenddiagramm oder über eine Webhookbenachrichtigung gemeldet werden.

    ![Bereich „Ziel verwalten“](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Wählen Sie einen Zeitraum, für den die Kostenziele nachverfolgt werden sollen.
      - **Monatlich**: Die Kostenziele werden pro Monat nachverfolgt.
      - **Fest:** Die Kostenziele werden für den Datumsbereich nachverfolgt, den Sie als Start- und Enddatum angeben. Diese Werte geben in der Regel an, wie lange die Ausführung Ihres Projekts geplant ist.
   - Geben Sie **Zielkosten** an. Zum Beispiel den Betrag, den Sie für dieses Lab im definierten Zeitraum aufwenden möchten.
   - Aktivieren bzw. deaktivieren Sie die Schwellenwerte, die gemeldet werden sollen – in Inkrementen von 25% bis zum Höchstwert von 125% der angegebenen **Zielkosten**.
      - **Benachrichtigen:** Wenn dieser Schwellenwert erreicht ist, werden Sie über eine Webhook-URL benachrichtigt, die Sie angegeben haben.
      - **Plot on chart** (Im Diagramm zeichnen): Wenn dieser Schwellenwert erreicht ist, werden die Ergebnisse im Kostentrendgraphen dargestellt, den Sie wie unter Anzeigen des Diagramms „Monatlicher geschätzter Kostentrend“ beschrieben anzeigen können.
   - Wenn Sie für die Erreichung des Schwellenwerts die Option **Benachrichtigen** wählen, müssen Sie eine Webhook-URL angeben. Wählen Sie im Bereich „Kostenintegrationen“ die Option **Klicken Sie hier, um eine Integration hinzuzufügen**. Geben Sie im Bereich „Benachrichtigung konfigurieren“ eine **Webhook-URL** ein, und wählen Sie **OK** aus.

       ![Bereich „Benachrichtigung konfigurieren“](./media/devtest-lab-configure-cost-management/configure-notification.png)

     - Wenn Sie **Benachrichtigen** angeben, müssen Sie eine Webhook-URL definieren.
     - Entsprechend müssen Sie beim Definieren einer Webhook-URL im Bereich „Kostenschwellenwert“ die Option **Benachrichtigung** auf **Ein** festlegen.
     - Vor der Eingabe ist es erforderlich, dass Sie einen Webhook erstellen.  

       Weitere Informationen zu Webhooks finden Sie unter [Erstellen eines Webhooks oder einer API-Azure-Funktion](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

## <a name="view-cost-by-resource"></a>Anzeigen von Kosten nach Ressource 
Mit der Funktion für den monatlichen Kostentrend in Labs können Sie sehen, wie viel Sie im aktuellen Kalendermonat ausgegeben haben. Außerdem wird die Projektion der Ausgaben bis zum Monatsende angezeigt, die auf Ihren Ausgaben der letzten sieben Tage basiert. Sie können die Funktion **Kosten nach Ressource** verwenden, die Ihnen die Kosten für den bisherigen Kalendermonat **pro Ressource** in einer Tabelle anzeigt, um besser zu verstehen, warum die Ausgaben im Lab früh die Schwellenwerte erreichen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.
3. Wählen Sie in der Liste der Labs das gewünschte Lab aus.  
4. Wählen Sie im linken Menü die Option **Configuration and policies** (Konfiguration und Richtlinien) aus.
5. Wählen Sie im linken Menü im Abschnitt **Cost tracking** (Kostennachverfolgung) die Option **Kosten nach Ressource** aus. Daraufhin werden die Kosten für jede Ressource einem Lab zugeordnet. 

    ![Kosten nach Ressource](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Mit dieser Funktion können Sie leicht die Ressourcen identifizieren, die die höchsten Kosten verursachen, um Maßnahmen zur Reduzierung der Ausgaben für das Lab zu ergreifen. Beispielsweise sind die Kosten für eine VM von der Größe der VM abhängig. Je größer die VM, desto höher die Kosten. Sie können auf einfache Weise die Größe einer VM und deren Besitzer herausfinden, um mit diesem zu besprechen, warum diese VM-Größe erforderlich ist und ob die Möglichkeit besteht, die Größe zu verringern.

Die [Richtlinie zum automatischen Herunterfahren](devtest-lab-get-started-with-lab-policies.md#set-auto-shutdown) hilft Ihnen dabei, die Kosten zu senken, indem Lab-VMs zu einer bestimmten Tageszeit heruntergefahren werden. Der Benutzer eines Labs kann diese Richtlinie jedoch deaktivieren, wodurch sich die Kosten für die Ausführung der VM erhöhen. Sie können eine VM in der Tabelle auswählen, um zu sehen, ob dafür die Richtlinie zum automatischen Herunterfahren deaktiviert wurde. Wenn dies der Fall ist, können Sie mit dem Besitzer der VM besprechen, warum die Richtlinie für die VM deaktiviert wurde.
 
## <a name="next-steps"></a>Nächste Schritte
Diese Schritte könnten Sie als Nächstes ausführen:

* [Definieren von Labrichtlinien](devtest-lab-set-lab-policy.md): Erfahren Sie, wie Sie die verschiedenen Richtlinien festlegen, mit denen Ihr Lab und die zugehörigen virtuellen Computer verwendet werden. 
* [Erstellen eines benutzerdefinierten Images](devtest-lab-create-template.md): Wenn Sie eine VM erstellen, geben Sie eine Basis an. Dabei kann es sich entweder um ein benutzerdefiniertes Image oder ein Marketplace-Image handeln. In diesem Artikel erfahren Sie, wie Sie ein benutzerdefiniertes Image aus einer VHD-Datei erstellen.
* [Konfigurieren von Marketplace-Images](devtest-lab-configure-marketplace-images.md): DevTest Labs unterstützt die Erstellung virtueller Computer auf der Basis von Azure Marketplace-Images. In diesem Artikel erfahren Sie, wie Sie ggf. angeben, welche Azure Marketplace-Images zum Erstellen virtueller Computer in einem Lab verwendet werden können.
* [Erstellen einer VM in einem Lab:](devtest-lab-add-vm.md) In diesem Artikel wird veranschaulicht, wie Sie eine VM aus einem Basisimage erstellen (entweder aus einem benutzerdefinierten Image oder einem Marketplace-Image) und wie Sie mit Artefakten auf Ihrer VM arbeiten.

