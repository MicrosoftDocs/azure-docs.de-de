---
title: 'Tutorial: Verwalten von Kosten mit Cloudyn in Azure | Microsoft-Dokumentation'
description: In diesem Tutorial wird beschrieben, wie Sie Kosten per Kostenzuteilung und mit Showback- und Chargeback-Berichten verwalten.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: tutorial
ms.service: cost-management
ms.custom: seodec18
manager: benshy
ms.openlocfilehash: b72e03f6901fbb2b904328992107e31021c76be6
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969126"
---
# <a name="tutorial-manage-costs-by-using-cloudyn"></a>Tutorial: Verwalten von Kosten mithilfe von Cloudyn

Sie verwalten Kosten und erstellen Showback-Berichte in Cloudyn, indem Sie Kosten basierend auf Tags zuteilen. Beim Prozess der Kostenzuteilung werden Kosten Ihren verbrauchten Cloudressourcen zugewiesen. Die Kosten werden vollständig zugeteilt, wenn Ihre gesamten Ressourcen mit Tags kategorisiert sind. Nachdem die Kosten zugeteilt wurden, können Sie für Ihre Benutzer über Dashboards und Berichte Showback- oder Chargeback-Informationen bereitstellen. Es kann aber sein, dass viele Ressourcen kein Tag aufweisen oder nicht mit einem Tag versehen werden können, wenn Sie mit der Nutzung von Cloudyn beginnen.

Beispielsweise bemühen Sie sich um die Erstattung von Engineering-Kosten. Sie müssen in der Lage sein, für Ihr Engineering-Team darzustellen, dass Sie basierend auf den Ressourcenkosten einen bestimmten Betrag benötigen. Sie können einen Bericht mit allen verbrauchten Ressourcen präsentieren, die über das Tag *Engineering* verfügen.

In diesem Artikel werden „Tags“ und „Kategorien“ bisweilen synonym verwendet. Kategorien sind große Auflistungen und können viele Formen annehmen. Sie können Geschäftseinheiten, Kostenstellen, Webdienste und alle Elemente mit Tag enthalten. Tags sind Name/Wert-Paare, die Ihnen das Kategorisieren von Ressourcen und die Anzeige und Verwaltung von konsolidierten Abrechnungsinformationen ermöglichen, indem Sie dasselbe Tag auf mehrere Ressourcen und Ressourcengruppen anwenden. In früheren Versionen des Azure-Portals wurde ein *Tag-Name* als *Schlüssel* bezeichnet. Tags werden für ein einzelnes Azure-Abonnement erstellt und von diesem gespeichert. Tags in AWS bestehen aus Schlüssel/Wert-Paaren. Da sowohl Azure als auch AWS die Benennung *Schlüssel* verwendet haben, verwendet auch Cloudyn diese Benennung. Der Kategorie-Manager verwendet Schlüssel (Tag-Namen), um Tags zusammenzuführen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Verwenden von benutzerdefinierten Tags zum Zuteilen von Kosten
> * Erstellen von Showback- und Chargeback-Berichten

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen ein Azure-Konto.
- Sie müssen entweder über eine Registrierung für die Testversion oder über ein kostenpflichtiges Abonnement für Cloudyn verfügen.
- [Nicht aktivierte Konten müssen im Cloudyn-Portal aktiviert werden](activate-subs-accounts.md).
- [Überwachung auf Gastebene](azure-vm-extended-metrics.md) muss auf Ihren virtuellen Computern aktiviert sein.


## <a name="use-custom-tags-to-allocate-costs"></a>Verwenden von benutzerdefinierten Tags zum Zuteilen von Kosten

Cloudyn ruft Tag-Daten für Ressourcengruppen aus Azure ab und verteilt automatisch Tag-Informationen an Ressourcen. In der Kostenzuteilung können Sie Kosten nach Ressourcen-Tags anzeigen.

Mithilfe des Kostenzuteilungsmodells definieren Sie Kategorien (Tags), die intern auf nicht kategorisierte Ressourcen (ohne Tags) angewendet werden, um Ihre Kosten zu gruppieren und Regeln zum Verarbeiten der Kosten ohne Tags zu definieren. Kostenzuteilungsregeln sind Ihre gespeicherten Anweisungen für die Verteilung der Kosten eines Diensts an einen anderen Dienst. Anschließend zeigen Ressourcen Tags/Kategorien in *Kostenzuteilungsberichten* an, wenn Sie das von Ihnen erstellte Modell auswählen.

Denken Sie daran, dass Tag-Informationen für diese Ressourcen in *Kostenanalyseberichten* nicht angezeigt werden. Außerdem werden mithilfe der Kostenzuteilung in Cloudyn angewendete Tags nicht an Azure gesendet, daher werden sie im Azure-Portal nicht angezeigt.

Zu Beginn der Kostenzuteilung definieren Sie zuerst den Bereich, indem Sie ein Kostenmodell verwenden. Mit dem Kostenmodell werden die Kosten nicht geändert, sondern verteilt. Wenn Sie ein Kostenmodell erstellen, segmentieren Sie Ihre Daten nach Kostenentität, Konto oder Abonnement und nach mehreren Tags. Zu den häufig verwendeten Beispieltags gehören beispielsweise ein Abrechnungscode, eine Kostenstelle oder ein Gruppenname. Außerdem sind Tags beim Durchführen von Showback- oder Chargeback-Vorgängen mit anderen Teilen Ihrer Organisation nützlich.

Wählen Sie zum Erstellen eines benutzerdefinierten Kostenzuteilungsmodells im Menü des Berichts die Optionen **Kosten** &gt; **Cost Management** &gt; **Cost Allocation 360**.

![Beispiel für ein Dashboard, in dem Sie Cost Allocation 360 auswählen](./media/tutorial-manage-costs/cost-allocation-360.png)

Wählen Sie auf der Seite **Cost Allocation 360°** (Kostenzuteilung 360°) die Option **Hinzufügen**, und geben Sie dann einen Namen und eine Beschreibung für Ihr Kostenmodell ein. Wählen Sie entweder alle Konten oder einzelne Konten aus. Wenn Sie mehrere einzelne Konten verwenden möchten, können Sie mehrere Konten unterschiedlicher Clouddienstanbieter auswählen. Klicken Sie als Nächstes auf **Kategorisierung**, um die ermittelten Tags auszuwählen, mit denen Ihre Kostendaten kategorisiert sind. Wählen Sie die Tags (Kategorien) aus, die Sie in Ihr Modell einbeziehen möchten. Im folgenden Beispiel ist das Tag **Unit** (Einheit) ausgewählt.

![Beispiel für Kostenmodellkategorisierung](./media/tutorial-manage-costs/cost-model01.png)

Im Beispiel ist zu sehen, dass 19.680 US-Dollar nicht kategorisiert sind (keine Tags aufweisen).

Wählen Sie als Nächstes die Option **Uncategorized Resources** (Nicht kategorisierte Ressourcen), und wählen Sie Dienste aus, die über nicht zugeteilte Kosten verfügen. Definieren Sie anschließend Regeln zum Zuteilen von Kosten.

Es kann beispielsweise sein, dass Sie Ihre Azure-Speicherkosten gleichmäßig auf virtuelle Azure-Computer (VMs) verteilen möchten. Wählen Sie hierzu den Dienst **Azure/Storage** und dann die Optionen **Proportional to Categorized** (Proportional zu kategorisiert) und **Azure/VM** aus. Wählen Sie anschließend **Erstellen**.

![Beispiel: Kostenmodell-Zuteilungsregel für die gleichmäßige Verteilung](./media/tutorial-manage-costs/cost-model02.png)



Ein anderes Beispiel ist ein Fall, in dem Sie Ihre gesamten Azure-Netzwerkkosten einer bestimmten Geschäftseinheit Ihrer Organisation zuteilen möchten. Wählen Sie dazu den Dienst **Azure/Network** (Azure/Netzwerk) und dann unter **Define Allocation Rule** (Zuteilungsregel definieren) die Option **Explicit Distribution** (Explizite Verteilung). Legen Sie den Verteilungsprozentsatz anschließend auf den Wert 100 fest, und wählen Sie die Geschäftseinheit aus. In der folgenden Abbildung ist dies **G&amp;A**:

![Beispiel: Kostenmodell-Zuteilungsregel für eine bestimmte Geschäftseinheit](./media/tutorial-manage-costs/cost-model03.png)



Erstellen Sie für alle verbleibenden nicht kategorisierten Ressourcen zusätzliche Zuteilungsregeln.

Falls Sie über nicht zugeteilte reservierte AWS-Instanzen (Amazon Web Services) verfügen, können Sie sie mit der Option **Reservierte Instanzen** getaggten Kategorien zuweisen.

Wählen Sie **Zusammenfassung**, um Informationen zu der Auswahl anzuzeigen, die Sie für das Zuteilen von Kosten getroffen haben. Wählen Sie **Als Entwurf speichern**, um Ihre Informationen zu speichern und später an zusätzlichen Regeln weiterzuarbeiten. Sie können auch **Speichern und aktivieren** wählen, um Ihre Informationen zu speichern und die Verarbeitung Ihres Kostenzuteilungsmodells von Cloudyn starten zu lassen.

In der Liste mit den Kostenmodellen wird Ihr neues Kostenmodell mit dem **Verarbeitungsstatus** angezeigt. Es kann einige Zeit dauern, bis die Cloudyn-Datenbank mit Ihrem Kostenmodell aktualisiert wird. Nach Abschluss der Verarbeitung wird der Status in **Abgeschlossen** geändert. Sie können dann Daten aus Ihrem Kostenmodell im Bericht mit der Kostenanalyse unter **Extended Filters** (Erweiterte Filter) &gt; **Cost Model** (Kostenmodell) anzeigen.

### <a name="category-manager"></a>Kategorie-Manager:

Der Kategorie-Manager ist ein Datenbereinigungstool, mit dem Sie die Werte mehrerer Kategorien (Tags) zusammenführen können, um neue Werte zu erstellen. Es ist ein einfaches regelbasiertes Tool, bei dem Sie eine Kategorie auswählen und Regeln erstellen, um vorhandene Werte zusammenzuführen. Beispielsweise verfügen Sie über vorhandene Kategorien für **F&amp;E** und **dev**, die beide für die Entwicklungsgruppe stehen.

Klicken Sie im Cloudyn-Portal auf das Zahnradsymbol in der rechten oberen Ecke, und wählen Sie **Category Manager** (Kategorie-Manager) aus. Wählen Sie zum Erstellen einer neuen Kategorie das Plussymbol (**+**). Geben Sie einen Namen für die Kategorie ein, und geben Sie unter **Schlüssel** dann die Kategorieschlüssel an, die Sie in die neue Kategorie einbeziehen möchten.

Beim Definieren einer Regel können Sie mehrere Werte mit einer OR-Bedingung hinzufügen. Außerdem können Sie einige einfache Zeichenfolgenvorgänge durchführen. Klicken Sie in beiden Fällen auf das Auslassungszeichen (**…**) rechts von **Regel**.

Erstellen Sie im Bereich **Regeln** eine neue Regel, um eine neue Regel zu definieren. Geben Sie unter **Regeln** beispielsweise **dev** und unter **Aktionen** dann **F&amp;E** ein. Speichern Sie Ihre neue Kategorie, wenn Sie fertig sind.

Die folgende Abbildung enthält ein Beispiel für Regeln, die für eine neue Kategorie mit dem Namen **Work-Load** erstellt wurden:

![Beispiel für die neue Kategorie „Work-Load“](./media/tutorial-manage-costs/category01.png)

### <a name="tag-sources-and-reports"></a>Tag-Quellen und -Berichte

Tag-Daten, die Sie in Cloudyn-Berichten sehen, stammen von drei Stellen:

- Ressourcen-APIs des Cloudanbieters
- Abrechnungs-APIs des Cloudanbieters
- Manuell erstellte Tags aus den folgenden Quellen:
    - Cloudyn Entitätstags – benutzerdefinierte Metadaten, die auf Cloudyn Entitäten angewendet werden
    - Kategorie-Manager – ein Datenbereinigungstool, das neue Tags anhand von Regeln erstellt, die auf vorhandene Tags angewendet werden

Wenn Sie Cloudanbieter-Tags in Cloudyn-Kostenberichten anzeigen möchten, müssen Sie mit „Cost Allocation 360“ ein benutzerdefiniertes Kostenzuteilungsmodell erstellen. Klicken Sie dazu auf **Kosten** > **Cost Management** > **Cost Allocation 360**, wählen Sie die gewünschten Tags aus, und definieren Sie dann Regeln zum Verarbeiten von Kosten, die keine Tags haben. Erstellen Sie anschließend ein neues Kostenmodell. Anschließend können Sie Berichte in „Cost Allocation Analysis“ anzeigen, um Ihre Azure-Ressourcentags anzuzeigen, zu filtern und nach ihnen zu sortieren.

Azure-Ressourcentags werden nur in **Kosten** > **Cost Allocation Analysis**-Berichten angezeigt.

Abrechnungstags von Cloudanbietern werden in allen Kostenberichten angezeigt.

Cloudyn Entitätstags und Tags, die Sie manuell erstellen, werden in allen Kostenberichten angezeigt.


## <a name="create-showback-and-chargeback-reports"></a>Erstellen von Showback- und Chargeback-Berichten

Das Verfahren, das von Organisationen zum Durchführen von Showback- und Chargeback-Vorgängen genutzt wird, kann stark variieren. Sie können aber alle Dashboards und Berichte im Cloudyn-Portal als Basis für beide Zwecke nutzen. Sie können allen Benutzern in Ihrer Organisation Zugriff gewähren, damit diese bei Bedarf Dashboards und Berichte anzeigen können. Showback wird für alle Kostenanalyseberichte unterstützt, da Benutzern darin die Ressourcen angezeigt werden, die sie verbraucht haben. Außerdem können Benutzer einen Drilldown für Kosten- oder Nutzungsdaten durchführen, die jeweils für ihre Gruppe der Organisation gelten.

Öffnen Sie zum Anzeigen der Ergebnisse einer Kostenzuteilung den Kostenanalysebericht, und wählen Sie das von Ihnen erstellte Kostenmodell aus. Fügen Sie anschließend eine Gruppierung nach einem oder mehreren Tags hinzu, die im Kostenmodell ausgewählt werden.

![Der Kostenanalysebericht zeigt ein Beispiel der Daten von den neuen Kosten](./media/tutorial-manage-costs/cost-analysis.png)

Sie können leicht Berichte erstellen und speichern, bei denen der Schwerpunkt auf bestimmten Diensten liegt, die von bestimmten Gruppen genutzt werden. Beispielsweise verfügen Sie ggf. über eine Abteilung, in der Azure-VMs häufig verwendet werden. Sie können einen Bericht erstellen, der nach Azure-VMs gefiltert ist, um die Nutzung und die Kosten anzuzeigen.

Falls Sie für andere Teams Momentaufnahmedaten bereitstellen müssen, können Sie alle Berichte im PDF- oder CSV-Format exportieren.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Verwenden von benutzerdefinierten Tags zum Zuteilen von Kosten
> * Erstellen von Showback- und Chargeback-Berichten



Im nächsten Tutorial erfahren Sie, wie Sie den Zugriff auf Daten steuern.

> [!div class="nextstepaction"]
> [Steuern des Zugriffs auf Daten](tutorial-user-access.md)
