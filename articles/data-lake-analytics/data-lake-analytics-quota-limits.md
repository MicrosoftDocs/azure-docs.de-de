---
title: Anpassen von Kontingenten und Grenzwerten in Azure Data Lake Analytics
description: Hier erfahren Sie, wie Sie Kontingente und Grenzwerte in ADLA-Konten (Azure Data Lake Analytics) anpassen und erhöhen.
ms.service: data-lake-analytics
author: omidm1
ms.author: omidm
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 03/15/2018
ms.openlocfilehash: fd032235f286b5db1930e9c9c6d730b5424aa4eb
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220838"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Anpassen von Kontingenten und Grenzwerten in Azure Data Lake Analytics

Hier erfahren Sie, wie Sie das Kontingent und die Grenzwerte in ADLA-Konten (Azure Data Lake Analytics) anpassen und erhöhen. Diese Grenzen zu kennen, hilft dabei, das Verhalten von U-SQL-Aufträgen besser zu verstehen. Kontingentgrenzen sind nicht fix. Wenden Sie sich an den Azure-Support, um diese Höchstgrenzen zu erhöhen.

## <a name="azure-subscriptions-limits"></a>Azure-Abonnementgrenzen

**Maximale Anzahl von ADLA-Konten pro Region und Abonnement:**  5

Beim Versuch, ein sechstes ADLA-Konto zu erstellen, erhalten Sie folgenden Fehler : Sie haben die maximale Anzahl von Data Lake Analytics-Konten (5) in „Region“ für das Abonnement „Name“ erreicht.

Wenn Sie diesen Grenzwert überschreiten möchten, können Sie diese Optionen versuchen:

- Wählen Sie nach Möglichkeit eine andere Region aus
- Wenden Sie sich zur Anforderung einer Kontingenterhöhung durch [Öffnen eines Supporttickets](#increase-maximum-quota-limits) an den Azure-Support.

## <a name="default-adla-account-limits"></a>Standardmäßige ADLA-Kontobegrenzungen

**Maximale Anzahl von Analytics-Einheiten (AUs) pro Konto:** 250, Standard 32

Dies ist die maximale Anzahl von AUs, die in Ihrem Konto gleichzeitig ausgeführt werden können. Wenn die Gesamtzahl von in allen Aufträgen ausgeführten AUs diesen Grenzwert überschreitet, werden neuere Aufträge automatisch in die Warteschlange eingereiht. Beispiel:

- Sie führen einen Auftrag mit 32 AUs aus. Wenn Sie einen zweiten Auftrag übermitteln, wartet dieser in der Auftragswarteschlange, bis der erste Auftrag abgeschlossen ist.
- Wenn bereits vier Aufträge mit jeweils 8 AUs ausgeführt werden und Sie einen fünften Auftrag übermitteln, der 8 AUs benötigt, wartet dieser Auftrag in der Auftragswarteschlange, bis 8 AUs verfügbar sind.

    ![Seite mit Azure Data Lake Analytics-Limits und -Kontingenten](./media/data-lake-analytics-quota-limits/adjust-quota-limits.png)

**Maximale Anzahl von Analytics-Einheiten (AUs) pro Auftrag:** 250, Standard 32

Dies ist die maximale Anzahl von AUs, die jedem einzelnen Auftrag in Ihrem Konto zugewiesen werden können. Aufträge, denen mehr als diese Begrenzung zugewiesen wird, werden abgelehnt, es sei denn,für den Absender gilt eine Computerichtlinie (Auftragsübermittlungsbegrenzung), die ihm mehr AUs pro Auftrag gewährt. Die Obergrenze dieses Werts ist die AU-Begrenzung für das Konto.

**Maximale Anzahl gleichzeitiger U-SQL-Aufträge pro Konto:** 20

Dies ist die maximale Anzahl von Aufträgen, die in Ihrem Konto gleichzeitig ausgeführt werden können. Aufträge, die diesen Wert überschreiten, werden automatisch in die Warteschlange eingereiht.

## <a name="adjust-adla-account-limits"></a>Anpassen von ADLA-Kontobegrenzungen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
2. Wählen Sie ein vorhandenes ADLA-Konto aus.
3. Klicken Sie auf **Eigenschaften** .
4. Passen Sie die Werte für **Maximal zulässige AUs** , **Höchstzahl ausgeführter Aufträge** und **Grenzwerte bei der Auftragsübermittlung** Ihren Anforderungen entsprechend an.

## <a name="increase-maximum-quota-limits"></a>Erhöhen der Kontingentobergrenzen

Weitere Informationen zu Azure-Grenzwerten finden Sie in der [Dokumentation zu dienstspezifischen Azure-Grenzwerten](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-analytics-limits).

1. Öffnen Sie eine Supportanfrage im Azure-Portal.

   ![Azure Data Lake Analytics-Portal – Hilfe und Support](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

   ![Azure Data Lake Analytics-Portal – Neue Supportanfrage](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)

2. Wählen Sie als Problemtyp **Kontingent** aus.

3. Wählen Sie Ihr **Abonnement** aus (stellen Sie sicher, dass es kein Testabonnement ist).

4. Wählen Sie als Kontingenttyp **Data Lake Analytics** aus.

   ![Azure Data Lake Analytics – Kontingenttyp für Supportanfrage](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Erläutern Sie auf der Problemseite Ihre Anforderung für die Erhöhung der Grenze, und geben Sie **Details** an, aus welchem Grund Sie diese zusätzliche Kapazität benötigen.

   ![Azure Data Lake Analytics – Details für Supportanfrage](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Überprüfen Sie Ihre Kontaktinformationen, und erstellen Sie die Supportanfrage.

Microsoft prüft Ihre Anforderung und versucht, Ihre geschäftlichen Anforderungen so bald wie möglich zu erfüllen.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Verwalten von Azure Data Lake Analytics mithilfe von Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
- [Überwachung und Problembehandlung von Azure Data Lake Analytics-Aufträgen mithilfe des Azure-Portals](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
