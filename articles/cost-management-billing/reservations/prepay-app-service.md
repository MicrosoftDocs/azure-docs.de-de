---
title: Speichern für Azure App Service mit reservierter Kapazität
description: Hier erfahren Sie, wie Sie Kosten für reservierte Instanzen von Azure App Service Premium V3 und für die Stempelgebühr (gesondert) sparen können.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 02/01/2021
ms.author: banders
ms.custom: references_regions
ms.openlocfilehash: 92a315121ad8ae6fadcadbf6d531eb3e99ae69a9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374540"
---
# <a name="save-costs-with-azure-app-service-reserved-instances"></a>Sparen von Kosten mit reservierten Azure App Service-Instanzen

In diesem Artikel erfahren Sie, wie Sie mit reservierten Azure App Service-Instanzen Kosten für Premium V3-Instanzen und Stempelgebühren (gesondert) sparen können.

## <a name="save-with-premium-v3-reserved-instances"></a>Sparen mit reservierten Premium V3-Instanzen

Durch die Verwendung einer reservierten Azure App Service-Instanz im Premium V3-Tarif können Sie Kosten sparen. Der Reservierungsrabatt wird automatisch auf die Anzahl ausgeführter Instanzen angewendet, die dem Reservierungsumfang und den Reservierungsattributen entsprechen. Sie müssen einer Instanz keine Reservierung zuweisen, um die Rabatte zu erhalten.

## <a name="determine-the-right-reserved-instance-size-before-you-buy"></a>Bestimmen der passenden Größe der reservierten Instanz vor dem Kauf

Bevor Sie eine Reservierung erwerben, müssen Sie die benötigte Größe der reservierten Premium V3-Instanz ermitteln. Die folgenden Abschnitte helfen Ihnen dabei, die passende Größe der reservierten Premium V3-Instanz zu bestimmen.

### <a name="use-reservation-recommendations"></a>Verwenden von Reservierungsempfehlungen

Sie können Reservierungsempfehlungen verwenden, um zu ermitteln, welche Reservierungen Sie erwerben sollten.

- Kaufempfehlungen und die empfohlene Menge werden angezeigt, wenn Sie eine reservierte Premium V3-Instanz im Azure-Portal erwerben.
- Azure Advisor bietet Kaufempfehlungen für einzelne Abonnements.
- Sie können die APIs verwenden, um Kaufempfehlungen für den Bereich „Freigegeben“ und den Bereich „Einzelabonnement“ abzurufen. Weitere Informationen finden Sie unter [Reserved instance purchase recommendation APIs for enterprise customers (Kaufempfehlungs-APIs zu reservierten Instanzen für Enterprise-Kunden)](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Für EA-Kunden (Enterprise Agreement) und MCA-Kunden (Microsoft Customer Agreement, Microsoft-Kundenvereinbarung) sind Kaufempfehlungen zu den Bereichen „Freigegeben“ und „Einzelabonnement“ im Rahmen des [Azure Consumption Insights Power BI-Inhaltspakets](/power-bi/service-connect-to-azure-consumption-insights) verfügbar.

### <a name="analyze-your-usage-information"></a>Analysieren Ihrer Nutzungsinformationen

Analysieren Sie Ihre Nutzungsinformationen, um besser ermitteln zu können, welche Reservierungen Sie erwerben sollten. Nutzungsdaten sind in der Nutzungsdatendatei und in APIs verfügbar. Verwenden Sie beides gemeinsam, um zu ermitteln, welche Reservierung Sie erwerben sollten. Prüfen Sie, ob Premium V3-Instanzen vorhanden sind, die täglich stark genutzt werden, um die Menge der zu erwerbenden Reservierungen zu ermitteln.

Die Nutzungsdatei enthält Ihre Gebühren nach Abrechnungszeitraum und Daten zur täglichen Nutzung. Weitere Informationen zum Herunterladen der Nutzungsdatei finden Sie unter [Anzeigen und Herunterladen der Azure-Nutzung und -Gebühren](../understand/download-azure-daily-usage.md). Anhand der Informationen in der Nutzungsdatei können Sie dann [die zu erwerbende Reservierung ermitteln](determine-reservation-purchase.md).


## <a name="buy-a-premium-v3-reserved-instance"></a>Erwerben einer reservierten Premium V3-Instanz

Sie können eine reservierte Premium V3-Instanz im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D) erwerben. Bezahlen Sie die Reservierung [im Voraus oder monatlich](prepare-buy-reservation.md). Für den Erwerb einer reservierten Premium V3-Instanz gelten folgende Anforderungen:

- Sie müssen in einer „Besitzer“-Rolle für mindestens ein EA-Abonnement oder ein Abonnement mit einem Satz für nutzungsbasierte Bezahlung sein.
- Bei EA-Abonnements muss im [EA-Portal](https://ea.azure.com/) die Option **Reservierte Instanzen hinzufügen** aktiviert werden. Wenn diese Einstellung deaktiviert ist, müssen Sie ein EA-Administrator für das Abonnement sein.
- Für das Cloud Solution Provider (CSP)-Programm können nur die Administrator- oder Vertriebs-Agents Reservierungen kaufen.

So kaufen Sie ein Instanz:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Klicken Sie auf **Alle Dienste** > **Reservierungen**.
3. Klicken Sie auf **Hinzufügen**, um eine neue Reservierung zu erwerben, und klicken Sie dann auf **Instanz**.
4. Geben Sie die erforderlichen Felder ein. Ausgeführte reservierte Premium V3-Instanzen, die den ausgewählten Attributen entsprechen, sind für den Reservierungsrabatt qualifiziert. Die tatsächliche Anzahl Ihrer reservierten Premium V3-Instanzen, die den Rabatt erhalten, hängt vom ausgewählten Umfang und von der ausgewählten Menge ab.

Wenn Sie über eine EA-Vereinbarung verfügen, können Sie die Option **Weitere hinzufügen** verwenden, um schnell weitere Instanzen hinzuzufügen. Die Option ist für andere Abonnementtypen nicht verfügbar.

| **Feld** | **Beschreibung** |
|---|---|
| Subscription | Das zum Bezahlen für die Reservierung verwendete Abonnement. Die Zahlungsmethode für das Abonnement wird mit Zahlungen für die Reservierung belastet. Der Abonnementtyp muss „Enterprise Agreement“ (Angebotsnummern: MS-AZR-0017P oder MS-AZR-0148P) oder „Microsoft-Kundenvereinbarung“ oder ein einzelnes Abonnement mit Sätzen für nutzungsbasierte Bezahlung (Angebotsnummern: MS-AZR-0003P oder MS-AZR-0023P) sein. Die Gebühren werden vom Guthaben in Bezug auf den Mindestverbrauch abgezogen oder als Überschreitung belastet. Bei einem Abonnement mit Sätzen für nutzungsbasierte Zahlung wird die Kreditkarte mit den Gebühren belastet, oder die Gebühren werden für Zahlung auf Rechnung in Rechnung gestellt. |
| `Scope` | Der Bereich der Reservierung kann ein Abonnement oder mehrere Abonnements (freigegebener Bereich) umfassen. Optionen: <ul><li>**Einzelne Ressourcengruppe**: Wendet den Reservierungsrabatt nur auf die entsprechenden Ressourcen in der ausgewählten Ressourcengruppe an. </li><li>**Einzelnes Abonnement**: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen im ausgewählten Abonnement an.</li><li>**Gemeinsam genutzt**: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen in berechtigten Abonnements innerhalb des Abrechnungskontexts an. Für EA-Kunden ist der Abrechnungskontext die Registrierung. Für Kunden mit individuellen Abonnements mit nutzungsbasierten Tarifen handelt es sich beim Abrechnungsbereich um alle berechtigten Abonnements, die vom Kontoadministrator erstellt wurden.</li></ul> |
| Region | Die Azure-Region, die durch die Reservierung abgedeckt wird |
| Größe der reservierten Premium V3-Instanz | Die Größe der reservierten Premium V3-Instanzen. |
| Begriff | Ein Jahr oder drei Jahre Es ist auch eine Laufzeit von fünf Jahren verfügbar (allerdings nur für reservierte HBv2-Premium V3-Instanzen). |
| Menge | Die Anzahl von Instanzen, die innerhalb der Reservierung erworben werden. Die Menge ist die Anzahl ausgeführter reservierter Premium V3-Instanzen, auf die der Abrechnungsrabatt angewendet werden kann. Wenn Sie also beispielsweise zehn reservierte Premium V3-Instanzen vom Typ „Standard\_D2“ in der Region „USA, Osten“ ausführen, geben Sie als Menge „10“ an, um den Vorteil für alle ausgeführten reservierten Premium V3-Instanzen zu maximieren. |

## <a name="save-with-isolated-stamp-fees"></a>Sparen mit Stempelgebühren (gesondert)

Sie können bei den Stempelgebühren für Azure App Service (isoliert) sparen, indem Sie sich für eine Reservierung Ihrer Stempelnutzung für drei Jahre entscheiden. Um reservierte Kapazität für die Stempelgebühr (gesondert) zu erwerben, müssen Sie sowohl die Azure-Region, in der der Stempel bereitgestellt wird, als auch die Anzahl der zu erwerbenden Stempel auswählen.

Wenn Sie eine Reservierung erwerben, wird die Nutzung der Stempelgebühr (gesondert), die den Reservierungsattributen entspricht, nicht mehr zu den Preisen für die nutzungsbasierte Bezahlung abgerechnet. Die Reservierung wird automatisch auf die Anzahl gesonderter Stempel angewendet, die dem reservierten Kapazitätsspektrum und der Region entsprechen. Es ist nicht erforderlich, einem gesonderten Stempel eine Reservierung zuzuweisen. Die Reservierung gilt nicht für Worker. Jede andere dem Stempel zugeordnete Ressource wird also separat abgerechnet.

Nach Ablauf der reservierten Kapazität werden gesonderte Stempel zwar weiterhin ausgeführt, dann aber zu den Preisen für die nutzungsbasierte Bezahlung abgerechnet. Reservierungen werden nicht automatisch verlängert.

## <a name="determine-the-right-isolated-stamp-reservation-to-purchase"></a>Bestimmen der passenden zu erwerbenden Reservierung für Stempel (gesondert)

Wenn Sie eine Reservierung erwerben, entscheiden Sie sich für die Nutzung reservierter Mengen innerhalb der nächsten drei Jahre. Ermitteln Sie anhand Ihrer Nutzungsdaten, wie viele Stempel für App Service (isoliert) Sie konsistent nutzen und voraussichtlich in Zukunft nutzen werden.

Machen Sie sich außerdem damit vertraut, wie der gesonderte Stempel Linux- oder Windows-Verbrauchseinheiten ausgibt.

- Ein leerer gesonderter Stempel gibt standardmäßig die Windows-Verbrauchseinheit aus – also beispielsweise ohne bereitgestellte Worker. Diese Verbrauchseinheit wird weiter ausgegeben, wenn Windows-Worker für den Stempel bereitgestellt werden.
- Wenn Sie einen Linux-Worker bereitstellen, ändert sich die Verbrauchseinheit in die Verbrauchseinheit für Linux-Stempel.
- Wurden sowohl Linux- als auch Windows-Worker bereitgestellt, gibt der Stempel die Windows-Verbrauchseinheit aus.

Die Verbrauchseinheit kann sich also im Laufe der Lebensdauer des Stempels zwischen Windows und Linux ändern.

Erwerben Sie Windows-Stempelreservierungen, wenn für den Stempel mindestens ein Windows-Worker vorhanden ist. Linux-Stempelreservierung sollten nur erworben werden, wenn Sie _ausschließlich_ Linux-Worker für den Stempel verwenden möchten.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Erwerben von reservierter Kapazität für gesonderte Stempel

Reservierte Kapazität für gesonderte Stempel kann über das [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D) erworben werden. Bezahlen Sie die Reservierung [im Voraus oder monatlich](./prepare-buy-reservation.md). Für den Erwerb von reservierter Kapazität müssen Sie über die Besitzerrolle für mindestens ein Enterprise- oder Einzelabonnement mit nutzungsbasierten Tarifen verfügen.

- Bei Enterprise-Abonnements muss im [EA-Portal](https://ea.azure.com/) die Option **Reservierte Instanzen hinzufügen** aktiviert werden. Sollte diese Einstellung deaktiviert sein, müssen Sie EA-Administrator sein.
- Für das Cloud Solution Provider-Programm (CSP) können nur die Administratoren oder Vertriebsmitarbeiter reservierte Azure Synapse Analytics-Kapazität erwerben.

**Vorgehensweise für den Erwerb:**

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Wählen Sie ein Abonnement aus. Wählen Sie in der Liste **Abonnement** das Abonnement aus, das für die Bezahlung der reservierten Kapazität verwendet wird. Die Zahlungsmethode für das Abonnement wird mit den Kosten für die Reservekapazität belastet. Der Abonnementtyp muss „Enterprise Agreement“ (Angebotsnummern: MS-AZR-0017P oder MS-AZR-0148P) oder „Nutzungsbasierte Zahlung“ (Angebotsnummern: MS-AZR-0003P oder MS-AZR-0023P) oder ein CSP-Abonnement sein.
    - Bei einem Enterprise-Abonnement werden die Gebühren vom Saldo der Azure-Vorauszahlung (zuvor als „Mindestverbrauch“ bezeichnet) der Reservierung abgezogen oder als Überschreitung belastet.
    - Bei einem Abonnement mit nutzungsbasierter Zahlung wird die Kreditkarte mit den Gebühren belastet, oder die Gebühren werden für die Zahlung auf Rechnung in Rechnung gestellt.
1. Wählen Sie unter **Bereich** einen Abonnementbereich aus.
    - **Einzelne Ressourcengruppe**: Wendet den Reservierungsrabatt nur auf die entsprechenden Ressourcen in der ausgewählten Ressourcengruppe an.
    - **Einzelnes Abonnement**: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen im ausgewählten Abonnement an.
    - **Gemeinsam genutzt**: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen in berechtigten Abonnements innerhalb des Abrechnungskontexts an. Für Kunden mit einem Enterprise Agreement ist der Abrechnungskontext die Registrierung. Für Kunden mit individuellen Abonnements mit nutzungsbasierten Tarifen handelt es sich beim Abrechnungsbereich um alle berechtigten Abonnements, die vom Kontoadministrator erstellt wurden.
1. Wählen Sie unter **Region** eine Azure-Region aus, die durch die reservierte Kapazität abgedeckt ist, und fügen Sie die Reservierung dem Warenkorb hinzu.
1. Wählen Sie einen isolierten Plantyp aus, und klicken Sie anschließend auf **Auswählen**.  
    ![Beispiel ](./media/prepay-app-service/app-service-isolated-stamp-select.png)
1. Geben Sie die Anzahl der zu reservierenden Stempel für App Service (isoliert) ein. Wenn Sie also beispielsweise die Menge „3“ angeben, erhalten Sie drei reservierte Stempel pro Region. Klicken Sie auf **Weiter: Review + Buy** (Weiter: Überprüfen und kaufen).
1. Überprüfen Sie die Angaben, und klicken Sie auf **Jetzt kaufen**.

Nach dem Erwerb können Sie unter [Reservierungen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) jederzeit den Status des Erwerbs anzeigen und überwachen.

## <a name="cancel-exchange-or-refund-reservations"></a>Stornieren, Umtauschen oder Rückerstatten von Reservierungen

Reservierungen können unter bestimmten Einschränkungen storniert, umgetauscht oder rückerstattet werden. Weitere Informationen finden Sie unter [Self-Service-Umtausch und -Rückerstattungen für Azure-Reservierungen](exchange-and-refund-azure-reservations.md).

## <a name="discount-application-shown-in-usage-data"></a>Anwendung des Rabatts in den Nutzungsdaten

Ihre Nutzungsdaten haben bei der Nutzung, für die der Reservierungsrabatt gilt, effektiv den Preis „0“. Die Nutzungsdaten zeigen den Reservierungsrabatt für jede Stempelinstanz in jeder Reservierung.

Weitere Informationen zur Anzeige von Reservierungsrabatt in Nutzungsdaten für EA-Kunden (Enterprise Agreement) finden Sie unter [Abrufen von Reservierungskosten und Nutzung laut Enterprise Agreement](understand-reserved-instance-usage-ea.md). Informationen für andere Kunden finden Sie unter [Informationen zur Azure-Reservierungsnutzung bei Ihrem einzelnen Abonnement mit nutzungsbasierter Bezahlung](understand-reserved-instance-usage.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:
  - [Was sind Azure-Reservierungen?](save-compute-costs-reservations.md)
  - [How reservation discounts apply to Azure App Service Isolated Stamps](reservation-discount-app-service.md) (Anwendung von Reservierungsrabatten auf Azure App Service-Stempelgebühren (gesondert))
  - [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](understand-reserved-instance-usage-ea.md)
