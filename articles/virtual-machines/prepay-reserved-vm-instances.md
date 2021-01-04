---
title: Bezahlen virtueller Azure-Computer im Voraus zum Einsparen von Kosten
description: Erfahren Sie, wie Sie Azure Reserved Virtual Machine Instances kaufen, um Computekosten einzusparen.
author: vikramdesai01
manager: vikramdesai01
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 10/30/2017
ms.author: vikdesai
ms.openlocfilehash: e513bdaa7efa2f1d0f18ff3548d9abfdc5e085d7
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007397"
---
# <a name="save-costs-with-azure-reserved-vm-instances"></a>Kostensparen mit reservierten Azure-VM-Instanzen



Wenn Sie an eine reservierte Azure-VM-Instanz committen, können Sie Geld sparen. Der Reservierungsrabatt wird automatisch auf die Anzahl der ausgeführten virtuellen Computer angewendet, die dem Reservierungsbereich und den Reservierungsattributen entsprechen. Sie müssen einem virtuellen Computer keine Reservierung zuweisen, um die Rabatte zu erhalten. Beim Erwerb reservierter Instanzen wird nur der Computeteil ihrer VM-Nutzung abgedeckt. Bei Windows-VMs wird die Verwendungsmessung in zwei separate Verbrauchseinheiten aufgeteilt. Es gibt eine Computeverbrauchseinheit, die mit der Linux-Verbrauchseinheit identisch ist, und eine Windows-IP-Verbrauchseinheit. Die Gebühren, die Ihnen beim Kauf angezeigt werden, gelten nur für die Computekosten. Gebühren enthalten keine Windows-Softwarekosten. Weitere Informationen zu Softwarekosten finden Sie unter [In Azure Reserved VM Instances nicht enthaltene Softwarekosten](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md).

## <a name="determine-the-right-vm-size-before-you-buy"></a>Bestimmen der passenden Größe des virtuellen Computers vor dem Kauf

Bevor Sie eine Reservierung erwerben, sollten Sie die Größe des von Ihnen benötigten virtuellen Computers ermitteln. Mithilfe der folgenden Abschnitte können Sie die richtige VM-Größe bestimmen.

### <a name="use-reservation-recommendations"></a>Verwenden von Reservierungsempfehlungen

Sie können Reservierungsempfehlungen verwenden, um zu ermitteln, welche Reservierungen Sie erwerben sollten.

- Kaufempfehlungen und die empfohlene Menge werden angezeigt, wenn Sie eine reservierte VM-Instanz im Azure-Portal erwerben.
- Azure Advisor bietet Kaufempfehlungen für einzelne Abonnements.  
- Sie können die APIs verwenden, um Kaufempfehlungen für den Bereich „Freigegeben“ und den Bereich „Einzelabonnement“ abzurufen. Weitere Informationen finden Sie unter [Reserved instance purchase recommendation APIs for enterprise customers (Kaufempfehlungs-APIs zu reservierten Instanzen für Enterprise-Kunden)](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Für EA-Kunden (Enterprise Agreement) und MCA-Kunden (Microsoft Customer Agreement, Microsoft-Kundenvereinbarung) sind Kaufempfehlungen zu den Bereichen „Freigegeben“ und „Einzelabonnement“ im Rahmen des [Azure Consumption Insights Power BI-Inhaltspakets](/power-bi/service-connect-to-azure-consumption-insights) verfügbar.

### <a name="services-that-get-vm-reservation-discounts"></a>Dienste, die VM-Reservierungsrabatte erhalten

Ihre VM-Reservierungen können für die von mehreren Diensten ausgegebene VM-Nutzung gelten, nicht allein für Ihre VM-Bereitstellungen. Welche Ressourcen Reservierungsrabatte erhalten, ändert sich abhängig von der Einstellung für die Instanzgrößenflexibilität.

#### <a name="instance-size-flexibility-setting"></a>Einstellung der Instanzgrößenflexibilität

Die Einstellung der Instanzgrößenflexibilität bestimmt, welche Dienste reservierte Instanzrabatte erhalten.

Unabhängig davon, ob die Einstellung ein- oder ausgeschaltet ist, gelten Reservierungsrabatte automatisch für jede übereinstimmende VM-Nutzung, wenn der *ConsumedService* gleich `Microsoft.Compute` ist. Überprüfen Sie daher ihre Nutzungsdaten auf den Wert *ConsumedService*. Beispiele hierfür sind:

- Virtuelle Computer
- VM-Skalierungsgruppen
- Containerdienst
- Azure Batch-Bereitstellungen (im Benutzerabonnementmodus)
- Azure Kubernetes Service (AKS)
- Service Fabric

Wenn die Einstellung eingeschaltet ist, gelten Reservierungsrabatte automatisch für jede übereinstimmende VM-Nutzung, wenn der *ConsumedService* einem der folgenden Elemente entspricht:

- Microsoft.Compute
- Microsoft.ClassicCompute
- Microsoft.Batch
- Microsoft.MachineLearningServices
- Microsoft.Kusto

Überprüfen Sie den Wert *ConsumedService* in ihren Nutzungsdaten, um zu ermitteln, ob die Nutzung zu Reservierungsrabatten berechtigt ist.

Weitere Informationen zur Instanzgrößenflexibilität finden Sie unter [Flexibilität bei der VM-Größe mit reservierten VM-Instanzen](reserved-vm-instance-size-flexibility.md).

### <a name="analyze-your-usage-information"></a>Analysieren Ihrer Nutzungsinformationen

Analysieren Sie Ihre Nutzungsinformationen, um besser ermitteln zu können, welche Reservierungen Sie erwerben sollten. Nutzungsdaten sind in der Nutzungsdatendatei und in APIs verfügbar. Verwenden Sie beides gemeinsam, um zu ermitteln, welche Reservierung Sie erwerben sollten. Prüfen Sie, ob VM-Instanzen vorhanden sind, die an allen Tagen eine hohe Nutzung aufweisen, um die Anzahl der zu erwerbenden Reservierungen zu ermitteln. Vermeiden Sie die Unterkategorie `Meter` und das Feld `Product` der Nutzungsdaten. Diese unterscheiden nicht zwischen VM-Größen, die Storage Premium verwenden. Wenn Sie diese Felder verwenden, um die VM-Größe für den Reservierungserwerb zu ermitteln, erwerben Sie möglicherweise eine falsche Größe. In diesem Fall erhalten Sie nicht den erwarteten Rabatt auf Ihre Reservierung. Beachten Sie stattdessen das Feld `AdditionalInfo` in Ihrer Nutzungsdatei oder die Nutzungs-API, um die richtige VM-Größe zu bestimmen.

Die Nutzungsdatei enthält Ihre Gebühren nach Abrechnungszeitraum und Daten zur täglichen Nutzung. Weitere Informationen zum Herunterladen der Nutzungsdatei finden Sie unter [Anzeigen und Herunterladen der Azure-Nutzung und -Gebühren](../cost-management-billing/understand/download-azure-daily-usage.md). Anhand der Informationen in der Nutzungsdatei können Sie dann [die zu erwerbende Reservierung ermitteln](../cost-management-billing/reservations/determine-reservation-purchase.md).

### <a name="purchase-restriction-considerations"></a>Überlegungen zu Kaufeinschränkungen

Reservierte VM-Instanzen sind bis auf wenige Ausnahmen für die meisten VM-Größen verfügbar. Rabatte für Reservierungen gelten nicht für die folgenden VMs:

- **VM-Serien** – A-Serie, Av2-Serie oder G-Serie

- **Vorschau- oder Promo-VMs** – Jede VM-Serie oder -Größe in der Vorschauversion oder die Werbeverbrauchseinheiten verwendet.

- **Clouds** – Reservierungen können in den Regionen Deutschland und China nicht käuflich erworben werden.

- **Nicht genügend Kontingent** – Für eine Reservierung, die einem einzelnen Abonnement zugeordnet ist, muss im Abonnement vCPU-Kontingent für die neue reservierte Instanz (RI) verfügbar sein. Beispiel: Wenn für das Zielabonnement eine Kontingentgrenze von zehn vCPUs für die D-Serie gilt, können Sie keine Reservierung für elf Standard_D1-Instanzen erwerben. Bei der Kontingentüberprüfung für Reservierungen werden die im Abonnement bereits bereitgestellten virtuellen Computer berücksichtigt. Beispiel: Wenn im Abonnement ein Kontingent von zehn vCPUs für die D-Serie enthalten ist und zwei Standard_D1-Instanzen bereitgestellt sind, können Sie eine Reservierung für zehn Standard_D1-Instanzen in diesem Abonnement erwerben. Um dieses Problem zu beheben, können Sie eine [Anforderung zur Kontingenterhöhung erstellen](../azure-portal/supportability/resource-manager-core-quotas-request.md).

- **Kapazitätsbeschränkungen** – In seltenen Fällen beschränkt Azure den Kauf neuer Reservierungen für eine Teilmenge der VM-Größen wegen geringer Kapazität in einer Region.

## <a name="buy-a-reserved-vm-instance"></a>Kaufen einer reservierten VM-Instanz

Sie können eine reservierte VM-Instanz im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D) erwerben. Bezahlen Sie die Reservierung [im Voraus oder monatlich](../cost-management-billing/reservations/prepare-buy-reservation.md).
Diese Anforderungen gelten für den Erwerb einer reservierten VM-Instanz:

- Sie müssen in einer „Besitzer“-Rolle für mindestens ein EA-Abonnement oder ein Abonnement mit einem Satz für nutzungsbasierte Bezahlung sein.
- Bei EA-Abonnements muss im [EA-Portal](https://ea.azure.com/) die Option **Reservierte Instanzen hinzufügen** aktiviert werden. Wenn diese Einstellung deaktiviert ist, müssen Sie ein EA-Administrator für das Abonnement sein.
- Für das Cloud Solution Provider (CSP)-Programm können nur die Administrator- oder Vertriebs-Agents Reservierungen kaufen.

So kaufen Sie ein Instanz:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie auf **Alle Dienste** > **Reservierungen**.
1. Klicken Sie auf **Hinzufügen**, um eine neue Reservierung zu erwerben, und klicken Sie dann auf **Virtueller Computer**.
1. Geben Sie die erforderlichen Felder ein. Ausgeführte VM-Instanzen, die den ausgewählten Attributen entsprechen, sind für den Reservierungsrabatt berechtigt. Die tatsächliche Anzahl der VM-Instanzen, die den Rabatt erhalten, hängt vom ausgewählten Bereich und von der ausgewählten Menge ab.

Wenn Sie über eine EA-Vereinbarung verfügen, können Sie die Option **Weitere hinzufügen** verwenden, um schnell weitere Instanzen hinzuzufügen. Die Option ist für andere Abonnementtypen nicht verfügbar.


| Feld      | Beschreibung|
|------------|--------------|
|Subscription|Das zum Bezahlen für die Reservierung verwendete Abonnement. Die Zahlungsmethode für das Abonnement wird mit Zahlungen für die Reservierung belastet. Der Abonnementtyp muss „Enterprise Agreement“ (Angebotsnummern: MS-AZR-0017P oder MS-AZR-0148P) oder „Microsoft-Kundenvereinbarung“ oder ein einzelnes Abonnement mit Sätzen für nutzungsbasierte Bezahlung (Angebotsnummern: MS-AZR-0003P oder MS-AZR-0023P) sein. Die Gebühren werden vom Guthaben in Bezug auf den Mindestverbrauch abgezogen oder als Überschreitung belastet. Bei einem Abonnement mit Sätzen für nutzungsbasierte Zahlung wird die Kreditkarte mit den Gebühren belastet, oder die Gebühren werden für Zahlung auf Rechnung in Rechnung gestellt.|    
|`Scope`       |Der Bereich der Reservierung kann ein Abonnement oder mehrere Abonnements (freigegebener Bereich) umfassen. Optionen: <ul><li>**Einzelne Ressourcengruppe**: Wendet den Reservierungsrabatt nur auf die entsprechenden Ressourcen in der ausgewählten Ressourcengruppe an.</li><li>**Einzelnes Abonnement**: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen im ausgewählten Abonnement an.</li><li>**Gemeinsam genutzt**: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen in berechtigten Abonnements innerhalb des Abrechnungskontexts an. Für EA-Kunden ist der Abrechnungskontext die Registrierung. Für Kunden mit individuellen Abonnements mit nutzungsbasierten Tarifen handelt es sich beim Abrechnungsbereich um alle berechtigten Abonnements, die vom Kontoadministrator erstellt wurden.</li></ul>|
|Region    |Die Azure-Region, die durch die Reservierung abgedeckt wird|    
|Größe des virtuellen Computers     |Die Größe der VM-Instanzen|
|Optimiert für     |„Flexibilität bei der VM-Instanzgröße“ ist standardmäßig ausgewählt. Klicken Sie auf **Erweiterte Einstellungen**, um den Wert der Instanzgrößenflexibilität zu ändern und den Reservierungsrabatt auf weitere VMs in derselben [VM-Größengruppe](reserved-vm-instance-size-flexibility.md) anzuwenden. Die Option „Kapazitätspriorität“ priorisiert Rechenzentrumskapazität für Ihre Bereitstellungen. So erhalten Sie zusätzliche Sicherheit, dass die VM-Instanzen gestartet werden können, wenn Sie sie benötigen. Die Kapazitätspriorität ist nur für den Reservierungsumfang „Einzelabonnement“ verfügbar. |
|Begriff        |Ein Jahr oder drei Jahre Es ist auch eine Laufzeit von 5 Jahren nur für HBv2-VMs verfügbar.|
|Menge    |Die Anzahl von Instanzen, die innerhalb der Reservierung erworben werden. Die Menge ist die Anzahl der ausgeführten VM-Instanzen, auf die der Abrechnungsrabatt angewendet werden kann. Beispiel: Wenn Sie zehn VMs vom Typ „10 Standard_D2“ in der Region „USA, Osten“ ausführen, geben Sie als Menge „10“ an, um den Vorteil für alle ausgeführten VMs zu maximieren. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

## <a name="usage-data-and-reservation-utilization"></a>Nutzungsdaten und Reservierungsnutzung

Ihre Nutzungsdaten haben bei der Nutzung, für die der Reservierungsrabatt gilt, effektiv den Preis „0“. Sie können sehen, welche VM-Instanz den Reservierungsrabatt für jede Reservierung erhalten hat.

Wenn Sie ein EA-Kunde sind, finden Sie weitere Informationen zur Anzeige von Reservierungsrabatten in Nutzungsdaten unter [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md). Wenn Sie ein einzelnes Abonnement haben, lesen Sie [Grundlegendes zur Nutzung von Azure-Reservierungen für Ihr Abonnement mit nutzungsbasierter Bezahlung](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Ändern einer Reservierung nach dem Kauf

Nach dem Kauf können Sie folgende Änderungen an einer Reservierung vornehmen:

- Reservierungsumfang aktualisieren
- Instanzgrößenflexibilität (sofern zutreffend)
- Besitz

Sie können eine Reservierung auch in kleinere Blöcke aufteilen und bereits aufgeteilte Reservierungen zusammenführen. Keine dieser Änderungen führt zu einer neuen Geschäftstransaktion, und das Enddatum der Reservierung bleibt von den Änderungen unberührt.

Folgendes können Sie nach dem Kauf nicht direkt ändern:

- Die Region einer vorhandenen Reservierung
- SKU
- Menge
- Duration

Sie können jedoch eine Reservierung *umtauschen*, wenn Sie Änderungen vornehmen möchten.

## <a name="cancel-exchange-or-refund-reservations"></a>Stornieren, Umtauschen oder Rückerstatten von Reservierungen

Reservierungen können unter bestimmten Einschränkungen storniert, umgetauscht oder rückerstattet werden. Weitere Informationen finden Sie unter [Self-Service-Umtausch und -Rückerstattungen für Azure-Reservierungen](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Verwalten einer Reservierung finden Sie unter [Verwalten von Azure-Reservierungen](../cost-management-billing/reservations/manage-reserved-vm-instance.md).
- Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:
    - [Was sind Azure-Reservierungen?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
    - [Verwalten von Reservierungen für Ressourcen in Azure](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
    - [Grundlegendes zur Anwendung des Rabatts für Azure-Reservierungen auf virtuelle Computer](../cost-management-billing/manage/understand-vm-reservation-charges.md)
    - [Grundlegendes zur Reservierungsnutzung bei einem Abonnement mit Sätzen für nutzungsbasierte Bezahlung](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
    - [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    - [Nicht in Azure-Reservierungen enthaltene Windows-Softwarekosten](../cost-management-billing/reservations/reserved-instance-windows-software-costs.md)
    - [Verkaufen Microsoft Azure Reserved Instances](/partner-center/azure-reservations)