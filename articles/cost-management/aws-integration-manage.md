---
title: Verwalten von AWS-Kosten und -Nutzung in Azure Cost Management
description: Dieser Artikel hilft Ihnen zu verstehen, wie Sie Ihre AWS-Kosten und -Nutzung mithilfe von Kostenanalysen und -budgets in Azure Cost Management verwalten.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: 007b6c409dde248a4dde7a15fd16b543add234bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64871151"
---
# <a name="manage-aws-costs-and-usage-in-azure"></a>Verwalten von AWS-Kosten und -Nutzung in Azure

Nach dem Einrichten und Konfigurieren der Integration von AWS-Kosten- und -Nutzungsberichten für Azure Cost Management sind Sie bereit, mit der Verwaltung Ihrer AWS-Kosten und -Nutzung zu beginnen. Dieser Artikel hilft Ihnen zu verstehen, wie Sie Ihre AWS-Kosten und -Nutzung mithilfe von Kostenanalysen und -budgets in Azure Cost Management verwalten.

Wenn Sie die Integration noch nicht konfiguriert haben, ziehen Sie [Einrichten und Konfigurieren der Integration von AWS-Kosten- und -Nutzungsberichten](aws-integration-set-up-configure.md) zurate.

_Voraussetzungen_: Wenn Sie mit der Kostenanalyse nicht vertraut sind, lesen Sie den Schnellstart [Ermitteln und Analysieren von Kosten mit der Kostenanalyse](quick-acm-cost-analysis.md). Und wenn Sie mit Budgets in Azure nicht vertraut sind, lesen Sie das Tutorial [Erstellen und Verwalten von Azure-Budgets](tutorial-acm-create-budgets.md).

## <a name="view-aws-costs-in-cost-analysis"></a>Anzeigen von AWS-Kosten mithilfe der Kostenanalyse

AWS Kosten stehen in Azure Cost Management für die folgenden Bereiche zur Verfügung:

- Verknüpfte AWS-Konten in einer Verwaltungsgruppe
- Kosten eines verknüpften AWS-Kontos
- Kosten eines konsolidierten AWS­-Kontos

In den nächsten Abschnitten wird beschrieben, wie Sie die Bereiche verwenden, um Kosten und Nutzung von Daten für jeden einzelnen Bereich anzuzeigen.

### <a name="view-aws-linked-accounts-under-a-management-group"></a>Anzeigen verknüpfter AWS-Konten unter einer Verwaltungsgruppe

Die Anzeige der Kosten unter Verwendung des Verwaltungsgruppenbereichs ist die einzige Möglichkeit, aggregierte Kosten anzuzeigen, die aus verschiedenen Abonnements und verknüpften Konten stammen. Die Verwendung einer Verwaltungsgruppe ermöglicht eine cloudübergreifende Ansicht.

Öffnen Sie in der Kostenanalyse die Bereichsauswahl, und wählen Sie die Verwaltungsgruppe aus, die Ihre verknüpften AWS-Konten enthält. Hier sehen Sie ein Beispielbild im Azure-Portal:

![Beispiel der Ansicht „Bereich auswählen“](./media/aws-integration-manage/select-scope01.png)



Hier sehen Sie ein Beispiel, in dem die Kosten der Verwaltungsgruppe in der Kostenanalyse dargestellt werden, gruppiert nach Anbieter (Azure und AWS).

![Beispiel der Azure- und AWS-Kosten für ein Quartal in der Kostenanalyse](./media/aws-integration-manage/cost-analysis-aws-azure.png)

### <a name="view-aws-linked-account-costs"></a>Anzeigen der Kosten für verknüpfte AWS-Konten

Um die Kosten für verknüpfte AWS-Konten anzuzeigen, öffnen Sie die Bereichsauswahl und wählen das verknüpfte AWS-Konto aus. Beachten Sie, dass verknüpfte Konten einer Verwaltungsgruppe zugeordnet sind, wie im AWS-Connector definiert.

Hier sehen Sie ein Beispiel, das die Auswahl eines verknüpften AWS-Kontobereichs zeigt.

![Beispiel der Ansicht „Bereich auswählen“](./media/aws-integration-manage/select-scope02.png)



### <a name="view-aws-consolidated-account-costs"></a>Anzeigen der Kosten eines konsolidierten AWS­-Kontos

Um die Kosten des konsolidierten AWS-Kontos anzuzeigen, öffnen Sie die Bereichsauswahl und wählen das konsolidierte AWS-Konto aus. Hier sehen Sie ein Beispiel, das die Auswahl eines konsolidierten AWS-Kontobereichs zeigt.

![Beispiel der Ansicht „Bereich auswählen“](./media/aws-integration-manage/select-scope03.png)



Dieser Bereich bietet eine aggregierte Ansicht aller verknüpften AWS-Konten, die dem konsolidierten AWS-Konto zugeordnet sind. Hier sehen Sie ein Beispiel der Kosten für ein konsolidiertes AWS-Konto, gruppiert nach Dienstnamen.

![Beispiel der konsolidierten AWS-Kosten in der Kostenanalyse](./media/aws-integration-manage/cost-analysis-aws-consolidated.png)

### <a name="dimensions-available-for-filtering-and-grouping"></a>Zum Filtern und Gruppieren verfügbare Dimensionen

Die folgende Tabelle beschreibt die Dimensionen, die zum Gruppieren und Filtern in der Kostenanalyse verfügbar sind.

| Dimension | Amazon CUR-Header | Bereiche | Kommentare |
| --- | --- | --- | --- |
| Verfügbarkeitszone | lineitem/AvailabilityZone | Alle |   |
| Location | product/Region | Alle |   |
| Zähler |   | Alle |   |
| Meter category | lineItem/ProductCode | Alle |   |
| Meter subcategory | lineitem/UsageType | Alle |   |
| Vorgang | lineItem/Operation | Alle |   |
| Resource | lineItem/ResourceId | Alle |   |
| Ressourcentyp | product/instanceType | Alle | Wenn „product/instanceType“ null ist, wird „lineItem/UsageType“ verwendet. |
| ResourceGuid | – | Alle | GUID der Azure-Verbrauchseinheit. |
| Dienstname | product/ProductName | Alle | Wenn „product/ProductName“ null ist, wird „lineItem/ProductCode“ verwendet. |
| Dienstebene |   |   |   |
| Abonnement-ID | lineItem/UsageAccountId | Konsolidiertes Konto und Verwaltungsgruppe |   |
| Abonnementname | – | Konsolidiertes Konto und Verwaltungsgruppe | Kontonamen werden mithilfe der AWS-Organisations-API erfasst. |
| Tag | resourceTags/\* | Alle | Das Präfix _user:_ wird aus benutzerdefinierten Tags entfernt, um cloudübergreifende Tags zu ermöglichen. Das Präfix _aws:_ bleibt unverändert. |
| ID des Abrechnungskontos | bill/PayerAccountId | Verwaltungsgruppe |   |
| Name des Abrechnungskontos | – | Verwaltungsgruppe | Kontonamen werden mithilfe der AWS-Organisations-API erfasst. |
| Anbieter | – | Verwaltungsgruppe | AWS oder Azure. |

## <a name="set-budgets-on-aws-scopes"></a>Festlegen von Budgets für AWS-Bereiche

Verwenden Sie Budgets, um Kosten proaktiv zu verwalten und Verantwortung in Ihrer Organisation zu fördern. Budgets werden für die Bereiche des konsolidierten AWS-Kontos und des verknüpften AWS-Kontos festgelegt. Hier sehen Sie ein Beispiel des Budgets für ein konsolidiertes AWS-Konto in Azure Cost Management:

![Beispiel, das Budgets für ein konsolidiertes AWS-Konto darstellt](./media/aws-integration-manage/budgets-aws-consolidated-account01.png)

## <a name="aws-data-collection-process"></a>Prozess der AWS-Datensammlung

Nach dem Einrichten des AWS-Connectors beginnen die Datenerfassungs- und -ermittlungsprozesse. Es kann einige Stunden dauern, bis alle Nutzungsdaten gesammelt wurden. Die Dauer hängt von folgenden Faktoren ab:

- Zeit, die benötigt wird, um die CUR-Dateien zu verarbeiten, die sich im AWS S3-Bucket befinden.
- Zeit, die benötigt wird, um die Bereiche des konsolidierten AWS-Kontos und des verknüpften AWS-Kontos zu erstellen.
- Zeit und Häufigkeit der AWS-Schreibvorgänge für Kosten-/Nutzungsbericht-Dateien im S3-Bucket

## <a name="aws-integration-pricing"></a>AWS-Integration – Preise

Jeder AWS-Connector erhält 90 kostenlose Testtage. Während der öffentlichen Vorschauphase fallen keine Kosten für das Anwendungsgateway an.

Der Listenpreis beträgt 1 % Ihrer AWS-Monatskosten. Jeden Monat werden die Kosten auf der Grundlage Ihrer fakturierten Kosten des Vormonats berechnet.

Der Zugriff auf AWS-APIs kann zusätzliche Kosten verursachen.

## <a name="aws-integration-limitations"></a>Einschränkungen für AWS-Integration

- Azure Cost Management unterstützt keine Kostenberichte, die mehrere Währungstypen enthalten. Eine Fehlermeldung wird angezeigt, wenn Sie einen Bereich auswählen, der mehrere Währungen enthält.
- „AWS GovCloud (USA)“, „AWS Gov“ oder „AWS China“ werden von Cloudconnectors nicht unterstützt.
- Azure Cost Management zeigt nur die AWS-_Nutzungskosten_ an. Steuern, Support, Rückerstattungen, RI, Gutschriften oder andere Gebührenarten werden noch nicht unterstützt.

## <a name="troubleshooting-aws-integration"></a>Problembehandlung bei der AWS-Integration

Verwenden Sie die folgenden Informationen zur Problembehandlung, um häufig auftretende Problemen zu beheben.

### <a name="no-permission-to-aws-linked-accounts"></a>Keine Berechtigung für verknüpfte AWS-Konten

Es gibt zwei Möglichkeiten, Berechtigungen für den Zugriff auf die Kosten verknüpfter AWS-Konten zu erhalten:

- Lassen Sie sich den Zugriff auf die Verwaltungsgruppe erteilen, die die verknüpften AWS-Konten enthält.
- Lassen Sie sich die Berechtigung für das verknüpfte AWS-Konto geben.

Standardmäßig ist der AWS-Connectorersteller der Besitzer aller Objekte, die der Connector erstellt hat, einschließlich des konsolidierten AWS-Kontos und des verknüpften AWS-Kontos.

### <a name="collection-failed-with-assumerole"></a>Fehler bei der Sammlung: AssumeRole

Dieser Fehler bedeutet, dass Azure Cost Management die AWS AssumeRole-API nicht aufrufen kann. Dieses Problem kann aufgrund eines Problems mit der Rollendefinition auftreten. Vergewissern Sie sich, dass die folgenden Bedingungen erfüllt sind:

- Die externe ID entspricht der ID in der Rollendefinition und der Connectordefinition.
- Der Rollentyp ist festgelegt auf **ein anderes AWS-Konto, das Ihnen oder einem Drittanbieter gehört**.
- Die Option **MFA anfordern** ist deaktiviert.
- Das vertrauenswürdige AWS-Konto in der AWS-Rolle ist _432263259397_.

### <a name="collection-failed-with-access-denied"></a>Fehler bei der Sammlung: Zugriff verweigert

Diese Fehlermeldung bedeutet, dass Azure Cost Management nicht auf die im Amazon S3-Bucket gespeicherten CUR-Dateien zugreifen kann. Stellen Sie sicher, dass die mit der Rolle verknüpfte AWS JSON-Richtlinie dem Beispiel am Ende des Abschnitts [Erstellen einer Rolle und Richtlinie AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) entspricht.

### <a name="connector-error-with-failedtofindreport"></a>Connectorfehler: FailedToFindReport

Dieser Fehler bedeutet, dass Azure Cost Management den im Connector definierten Kosten- und Nutzungsbericht nicht finden kann. Stellen Sie sicher, dass er nicht gelöscht wurde und dass die mit der Rolle verknüpfte AWS JSON-Richtlinie dem Beispiel am Ende des Abschnitts [Erstellen einer Rolle und Richtlinie AWS](aws-integration-set-up-configure.md#create-a-role-and-policy-in-aws) entspricht.

## <a name="next-steps"></a>Nächste Schritte

- Wenn Sie Ihre Azure-Umgebung nicht bereits mit Verwaltungsgruppen konfiguriert haben, finden Sie unter [Erstmalige Einrichtung von Verwaltungsgruppen](../governance/management-groups/index.md#initial-setup-of-management-groups) weitere Informationen.
