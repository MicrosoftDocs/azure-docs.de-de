---
title: Sparen von Computekosten mit reservierter Kapazität
description: Es wird beschrieben, wie Sie reservierte Kapazitäten für einen Azure Data Factory-Datenfluss erwerben, um Computekosten zu sparen.
ms.topic: conceptual
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.date: 02/05/2021
ms.openlocfilehash: d41646ef1ef964db38245009717554eaeb783824
ms.sourcegitcommit: e3151d9b352d4b69c4438c12b3b55413b4565e2f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100526305"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-data-factory-data-flows"></a>Sparen von Kosten für Ressourcen mit reservierter Kapazität: Azure Data Factory-Datenflüsse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Sparen Sie Kosten für Azure Data Factory-Datenflüsse im Vergleich zur nutzungsbasierten Bezahlung, indem Sie sich auf eine Reservierung für Computeressourcen festlegen. Mit reservierter Kapazität legen Sie sich für die Nutzung von ADF-Datenflüssen für einen Zeitraum von einem oder drei Jahren fest und profitieren dafür von einem erheblichen Rabatt bei den Computekosten. Sie müssen die Azure-Region, den Computetyp, die Kernanzahl und den Zeitraum angeben, um reservierte Kapazität zu erwerben.

Es ist nicht erforderlich, dass Sie die Reservierung einer bestimmten Factory oder Integration Runtime zuweisen. Für vorhandene oder neu bereitgestellte Factorys gilt der Vorteil automatisch. Beim Kauf einer Reservierung zahlen Sie für einen Zeitraum von einem oder drei Jahren die für die Nutzung anfallenden Datenfluss-Computekosten. Sobald Sie eine Reservierung erworben haben, werden die Computegebühren, die den Reservierungsattributen entsprechen, nicht mehr zu den Preisen der nutzungsbasierten Bezahlung abgerechnet. 

Sie können [reservierte Kapazitäten](https://portal.azure.com) erwerben, indem Sie Reservierungen [im Voraus oder monatlich bezahlen](https://docs.microsoft.com/azure/cost-management-billing/reservations/prepare-buy-reservation.md). So erwerben Sie reservierte Kapazität:

- Ihnen muss die Besitzerrolle für mindestens ein Enterprise-Abonnement oder ein individuelles Abonnement mit nutzungsbasierter Bezahlung zugeordnet sein.
- Bei Enterprise-Abonnements muss im [EA-Portal](https://ea.azure.com) die Option **Reservierte Instanzen hinzufügen** aktiviert werden. Wenn diese Einstellung deaktiviert ist, müssen Sie ein EA-Administrator für das Abonnement sein. Reservierte Kapazität.

Weitere Informationen zur Berechnung der Reservierung von Kapazitäten für Unternehmenskunden und Kunden mit nutzungsbasierter Bezahlung finden Sie unter [Grundlegendes zur Nutzung von Azure-Reservierungen für die Enterprise-Registrierung](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea) und [Informationen zur Azure-Reservierungsnutzung bei Ihrem einzelnen Abonnement mit nutzungsbasierter Bezahlung](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage).

> [!NOTE]
> Ein Erwerb einer reservierten Kapazität hat nicht zur Folge, dass bestimmte Infrastrukturressourcen (virtuelle Computer oder Cluster) für ihre Verwendung vorab zugeordnet oder reserviert werden.

## <a name="determine-proper-azure-ir-sizes-needed-before-purchase"></a>Ermitteln der richtigen Azure IR-Größen vor dem Kauf

Die Größe der Reservierung sollte auf der Gesamtmenge der Computeressourcen basieren, die von den bereits vorhandenen oder in Kürze bereitzustellenden Datenflüssen unter demselben Computetarif verwendet werden.

Angenommen, bei Ihnen wird eine Pipeline stündlich mit dem Typ „Arbeitsspeicheroptimiert“ und 32 Kernen ausgeführt. Darüber hinaus planen Sie, innerhalb des nächsten Monats die Bereitstellung einer weiteren Pipeline vom Typ „Universell“ mit 64 Kernen. Darüber hinaus wissen Sie, dass Sie diese Ressourcen mindestens 1 Jahr benötigen. Geben Sie in diesem Fall die Anzahl der für jeden Computetyp pro Stunde benötigten Kerne ein. Suchen Sie im Azure-Portal nach „Reservierungen“. Klicken Sie auf „Data Factory“ > „Data Flows“ (Datenflüsse), und geben Sie dann „32“ für „Arbeitsspeicheroptimiert“ und „64“ für „Allgemein“ ein.

## <a name="buy-reserved-capacity"></a>Erwerben von reservierter Kapazität

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie auf **Alle Dienste** > **Reservierungen**.
3. Wählen Sie **Hinzufügen** und dann im Bereich **Reservierungen erwerben** die Option **ADF-Datenflüsse** aus, um eine neue Reservierung für ADF-Datenflüsse zu erwerben.
4. Füllen Sie die erforderlichen Felder aus, und geben Sie die Attribute an. Anhand dieser Angaben wird überprüft, ob Sie beim Erwerb von reservierter Kapazität für den Rabatt qualifiziert sind. Die tatsächliche Anzahl von Datenflüssen, für die der Rabatt gilt, hängt vom ausgewählten Bereich und von der ausgewählten Menge ab.
5. Überprüfen Sie die Kosten für die Kapazitätsreservierung im Abschnitt **Kosten**.
6. Wählen Sie die Option **Kaufen**.
7. Klicken Sie auf **Diese Reservierung anzeigen**, um den Status des Einkaufs anzuzeigen.

## <a name="cancel-exchange-or-refund-reservations"></a>Stornieren, Umtauschen oder Rückerstatten von Reservierungen

Reservierungen können unter bestimmten Einschränkungen storniert, umgetauscht oder rückerstattet werden. Weitere Informationen finden Sie unter [Self-Service-Umtausch und -Rückerstattungen für Azure-Reservierungen](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:

- [Grundlegendes zum Rabatt für Azure-Reservierungen](data-flow-understand-reservation-charges.md)
