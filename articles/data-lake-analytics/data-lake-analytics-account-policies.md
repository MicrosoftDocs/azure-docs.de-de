---
title: Verwalten von Azure Data Lake Analytics-Kontorichtlinien
description: Erfahren Sie, wie Sie Kontorichtlinien zum Steuern der Verwendung eines Data Lake Analytics-Kontos verwenden, z. B. über die maximale Anzahl von AUs und Aufträgen.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 04/30/2018
ms.openlocfilehash: ba69098f32e131714a15923aef64c3f6ba17e18f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013308"
---
# <a name="manage-azure-data-lake-analytics-using-account-policies"></a>Verwalten von Azure Data Lake Analytics mithilfe von Kontorichtlinien

Mit Kontorichtlinien können Sie steuern, wie Ressourcen eines Azure Data Lake Analytics-Kontos verwendet werden. Diese Richtlinien erlauben Ihnen ebenfalls, die Kosten der Verwendung von Azure Data Lake Analytics zu kontrollieren. Mit diesen Richtlinien können Sie z. B. unerwartete Kostenspitzen verhindern, indem Sie einschränken, wie viele AUs gleichzeitig verwendet werden können.## Richtlinien auf Kontoebene

Diese Richtlinien gelten für alle Aufträge in einem Data Lake Analytics-Konto.

## <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Maximale Anzahl von AUs in einem Data Lake Analytics-Konto

Eine Richtlinie steuert die Gesamtanzahl der Analytics-Einheiten (Analytics Units, AUs), die Ihr Data Lake Analytics-Konto verwenden kann. Standardmäßig ist der Wert auf 250 festgelegt. Wenn dieser Wert beispielsweise auf 250 AUs festgelegt ist, können Sie einen Auftrag haben, der mit 250 zugewiesenen AUs ausgeführt wird, oder 10 Aufträge, die jeweils mit 25 AUs ausgeführt werden. Zusätzliche Aufträge, die übermittelt werden, bleiben in der Warteschlange, bis die ausgeführten Aufträge abgeschlossen sind. Wenn ausgeführte Aufträge abgeschlossen sind, werden die AUs für die auszuführenden Aufträge in der Warteschlange freigegeben.

So ändern Sie die Anzahl der AUs für Ihr Data Lake Analytics-Konto:

1. Gehen Sie im Azure-Portal zu Ihrem Data Lake Analytics-Konto.
2. Klicken Sie auf **Grenzwerte und Richtlinien**.
3. Bewegen Sie unter **Maximal zulässige AUs** den Schieberegler, um einen Wert auszuwählen, oder geben Sie den Wert in das Textfeld ein.
4. Klicken Sie auf **Speichern**.

   > [!NOTE]
   > Wenn Sie mehr als die Standard-AUs (250) benötigen, klicken Sie im Portal auf **Hilfe und Support**, um eine Supportanfrage zu übermitteln. Die Anzahl der in Ihrem Data Lake Analytics-Konto verfügbaren AUs kann erhöht werden.

## <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Maximale Anzahl der Aufträge, die gleichzeitig ausgeführt werden können

Diese Richtlinie schränkt die Anzahl der Aufträge ein, die gleichzeitig ausgeführt werden können. Standardmäßig ist dieser Wert auf 20 festgelegt. Wenn bei Ihrem Data Lake Analytics AUs verfügbar sind, werden neue Aufträge sofort ausgeführt, bis die Gesamtanzahl der ausgeführten Aufträge den Wert dieser Richtlinie erreicht. Wenn Sie die maximale Anzahl der Aufträge erreicht haben, die gleichzeitig ausgeführt werden können, werden nachfolgende Aufträge in der Reihenfolge der Priorität in die Warteschlange aufgenommen, bis mindestens ein ausgeführter Auftrag abgeschlossen ist (je nach AU-Verfügbarkeit).

So ändern Sie die Anzahl der Aufträge, die gleichzeitig ausgeführt werden können:

1. Gehen Sie im Azure-Portal zu Ihrem Data Lake Analytics-Konto.
2. Klicken Sie auf **Grenzwerte und Richtlinien**.
3. Bewegen Sie unter **Höchstzahl ausgeführter Aufträge** den Schieberegler, um einen Wert auszuwählen, oder geben Sie den Wert in das Textfeld ein.
4. Klicken Sie auf **Speichern**.

   > [!NOTE]
   > Wenn Sie mehr als die Standardanzahl von Aufträgen (20) ausführen müssen, klicken Sie im Portal auf **Hilfe und Support**, um eine Supportanfrage zu übermitteln. Die Anzahl der Aufträge, die in Ihrem Data Lake Analytics-Konto gleichzeitig ausgeführt werden können, kann erhöht werden.

## <a name="how-long-to-keep-job-metadata-and-resources"></a>Aufbewahrungsdauer von Auftragsmetadaten und -ressourcen

Wenn Ihre Benutzer U-SQL-Aufträge ausführen, bewahrt der Data Lake Analytics-Dienst alle zugehörigen Dateien auf. Zu diesen Dateien zählen das U-SQL-Skript, die DLL-Dateien, auf die im U-SQL-Skript verwiesen wird, die kompilierten Ressourcen und die Statistiken. Die Dateien befinden sich im Ordner „/system/“ des Standard-Azure Data Lake Storage-Kontos. Diese Richtlinie steuert, wie lange diese Ressourcen gespeichert werden, bis sie automatisch gelöscht werden (die Standardzeit beträgt 30 Tage). Sie können diese Dateien zum Debuggen und zur Leistungsoptimierung von Aufträgen verwenden, die Sie künftig erneut ausführen werden.

So ändern Sie die Aufbewahrungsdauer von Auftragsmetadaten und -ressourcen:

1. Gehen Sie im Azure-Portal zu Ihrem Data Lake Analytics-Konto.
2. Klicken Sie auf **Grenzwerte und Richtlinien**.
3. Bewegen Sie unter **Abfrageaufbewahrungsdauer in Tagen** den Schieberegler, um einen Wert auszuwählen, oder geben Sie den Wert in das Textfeld ein.  
4. Klicken Sie auf **Speichern**.

## <a name="job-level-policies"></a>Richtlinien auf Auftragsebene

Mit Richtlinien auf Auftragsebene können Sie die maximale Anzahl der AUs und die maximale Priorität steuern, die einzelne Benutzer (oder Mitglieder bestimmter Sicherheitsgruppen) für Aufträge festlegen können, die sie übermitteln. Mit dieser Richtlinie können Sie die Kosten kontrollieren, die durch jeden Benutzer entstehen. Darüber hinaus können Sie kontrollieren, welchen Effekt geplante Aufträge auf Produktionsaufträge mit hoher Priorität haben könnten, die im selben Data Lake Analytics-Konto ausgeführt werden.

Data Lake Analytics verfügt über zwei Richtlinien, die Sie auf Auftragsebene festlegen können:

- **AU-Grenzwert pro Auftrag**: Benutzer können nur Aufträge übermitteln, die über bis zu dieser Anzahl von AUs verfügen. Standardmäßig stimmt dieser Grenzwert mit dem Höchstwert für AUs für das Konto überein.

- **Priorität:** Benutzer können nur Aufträge übermitteln, deren Priorität maximal diesem Wert entspricht. Eine höhere Anzahl bedeutet eine niedrigere Priorität. Standardmäßig ist dieser Höchstwert auf 1 festgelegt, die höchstmögliche Priorität.

Für jedes Konto ist eine Standardrichtlinie festgelegt. Die Standardrichtlinie gilt für alle Benutzer des Kontos. Sie können zusätzliche Richtlinien für bestimmte Benutzer und Gruppen erstellen.

> [!NOTE]
> Richtlinien auf Kontoebene und Richtlinien auf Auftragsebene gelten gleichzeitig.

## <a name="add-a-policy-for-a-specific-user-or-group"></a>Hinzufügen einer Richtlinie für einen bestimmten Benutzer oder eine bestimmte Gruppe

1. Gehen Sie im Azure-Portal zu Ihrem Data Lake Analytics-Konto.

2. Klicken Sie auf **Grenzwerte und Richtlinien**.

3. Klicken Sie unter **Grenzwerte bei der Auftragsübermittlung** auf die Schaltfläche **Richtlinie hinzufügen**. Wählen Sie dann die folgenden Einstellungen aus, oder geben Sie sie ein:

   1. **Name der Computerichtlinie:** Geben Sie einen Richtliniennamen ein, der den Zweck der Richtlinie benennt.

   2. **Benutzer oder Gruppe auswählen:** Wählen Sie den Benutzer oder die Gruppe aus, für den oder die diese Richtlinie gilt.

   3. **Den AU-Grenzwert für den Auftrag festlegen:** Legen Sie den AU-Grenzwert fest, der für den ausgewählten Benutzer oder die ausgewählte Gruppe gilt.

   4. **Maximale Priorität festlegen:** Legen Sie die maximale Priorität fest, die für den ausgewählten Benutzer oder die ausgewählte Gruppe gilt.

4. Klicken Sie auf **OK**.

5. Die neue Richtlinie wird in der **Standardrichtlinientabelle** unter **Grenzwerte bei der Auftragsübermittlung** aufgeführt.

## <a name="delete-or-edit-an-existing-policy"></a>Löschen oder Bearbeiten einer vorhandenen Richtlinie

1. Gehen Sie im Azure-Portal zu Ihrem Data Lake Analytics-Konto.

2. Klicken Sie auf **Grenzwerte und Richtlinien**.

3. Suchen Sie unter **Grenzwerte bei der Auftragsübermittlung** nach der Richtlinie, die Sie bearbeiten möchten.

4. Um die Optionen **Löschen** und **Bearbeiten** in der Spalte ganz rechts in der Tabelle anzuzeigen, klicken Sie auf `...`.## Zusätzliche Ressourcen für Auftragsrichtlinien

- [Blogbeitrag zur Richtlinienübersicht](/archive/blogs/azuredatalake/managing-your-azure-data-lake-analytics-compute-resources-overview)
- [Blogbeitrag zu Richtlinien auf Kontoebene](/archive/blogs/azuredatalake/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy)
- [Blogbeitrag zu Richtlinien auf Auftragsebene](/archive/blogs/azuredatalake/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy)

## <a name="next-steps"></a>Nächste Schritte

- [Azure Data Lake Analytics – Übersicht](data-lake-analytics-overview.md)
- [Erste Schritte mit Azure Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-get-started-portal.md)
- [Verwalten von Azure Data Lake Analytics mithilfe von Azure PowerShell](data-lake-analytics-manage-use-powershell.md)