---
author: yashesvi
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/14/2019
ms.openlocfilehash: c7ff7ab0800449c2a3aa9d58bd036981caccaa1c
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178059"
---
# <a name="prepay-for-virtual-machines-with-azure-reserved-vm-instances-ri"></a>Vorauszahlen für virtuelle Computer mit Azure Reserved VM Instances (RI)

Sie können für virtuelle Computer mit Azure Reserved VM Instances im Voraus bezahlen und Kosten sparen. Weitere Informationen finden Sie unter [Angebot – Azure Reserved VM Instances](https://azure.microsoft.com/pricing/reserved-vm-instances/).

Sie können eine reservierte VM-Instanz im [Azure-Portal](https://portal.azure.com) erwerben. So kaufen Sie ein Instanz:

- Ihnen muss die Besitzerrolle für mindestens ein Enterprise-Abonnement oder ein Abonnement mit nutzungsbasierter Zahlung zugeordnet sein.
- Bei Enterprise-Abonnements muss im [EA-Portal](https://ea.azure.com) die Option **Reservierte Instanzen hinzufügen** aktiviert werden. Wenn diese Einstellung deaktiviert ist, müssen Sie ein EA-Administrator für das Abonnement sein.
- Für das Cloud Solution Provider (CSP)-Programm können nur die Administrator- oder Vertriebs-Agents Reservierungen kaufen.

Der Reservierungsrabatt wird automatisch auf die Anzahl der ausgeführten virtuellen Computer angewendet, die dem Reservierungsbereich und den Reservierungsattributen entsprechen. Sie können den Bereich der Reservierung über das [Azure-Portal](https://portal.azure.com), PowerShell, die CLI oder die API aktualisieren.

## <a name="determine-the-right-vm-size-before-you-buy"></a>Bestimmen der passenden Größe des virtuellen Computers vor dem Kauf

Bevor Sie eine Reservierung erwerben, sollten Sie die Größe des von Ihnen benötigten virtuellen Computers ermitteln. Mithilfe der folgenden Abschnitte können Sie die richtige VM-Größe bestimmen.

### <a name="use-reservation-recommendations"></a>Verwenden von Reservierungsempfehlungen

Sie können Reservierungsempfehlungen verwenden, um zu ermitteln, welche Reservierungen Sie erwerben sollten.

- Kaufempfehlungen und die empfohlene Menge werden angezeigt, wenn Sie eine reservierte VM-Instanz im Azure-Portal erwerben.
- Azure Advisor bietet Kaufempfehlungen für einzelne Abonnements.  
- Sie können die APIs verwenden, um Kaufempfehlungen für den Bereich „Freigegeben“ und den Bereich „Einzelabonnement“ abzurufen. Weitere Informationen finden Sie unter [Reserved instance purchase recommendation APIs for enterprise customers (Kaufempfehlungs-APIs zu reservierten Instanzen für Enterprise-Kunden)](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Für EA-Kunden sind Kaufempfehlungen zu den Bereich „Freigegeben“ und „Einzelabonnement“ im Rahmen des [Azure Consumption Insights Power BI-Inhaltspakets](/power-bi/service-connect-to-azure-consumption-insights) verfügbar.

### <a name="classic-vms-and-cloud-services"></a>Klassische VMs und Clouddienste

Reservierte VM-Instanzen gelten automatisch sowohl für klassische virtuelle Computer als auch für Clouddienste, wenn Instanzgrößenflexibilität aktiviert ist. Es gibt keine speziellen SKUs für klassische VMs oder Clouddienste. Für sie werden die gleichen VM-SKUs angewendet.

So können Sie beispielsweise Ihre klassischen VMs oder Clouddienste in Azure Resource Manager-basierte VMs konvertieren. In diesem Beispiel gilt der Reservierungsrabatt automatisch für übereinstimmende VMs. Eine bestehende reservierte Instanz muss nicht *ausgetauscht* werden – sie wird automatisch angewendet.

### <a name="analyze-your-usage-information"></a>Analysieren Ihrer Nutzungsinformationen
Sie sollten Ihre Nutzungsinformationen analysieren, um besser bestimmen zu können, welche Reservierungen Sie erwerben sollten.

Nutzungsdaten sind in der Nutzungsdatendatei und in APIs verfügbar. Verwenden Sie beides gemeinsam, um zu ermitteln, welche Reservierung Sie erwerben sollten. Sie sollten prüfen, ob VM-Instanzen vorhanden sind, die an allen Tagen eine hohe Nutzung aufweisen, um die Anzahl der zu erwerbenden Reservierungen zu bestimmen.

Vermeiden Sie die Unterkategorie `Meter` und das Feld `Product` der Nutzungsdaten. Diese unterscheiden nicht zwischen VM-Größen, die Storage Premium verwenden. Wenn Sie diese Felder verwenden, um die VM-Größe für den Reservierungserwerb zu ermitteln, erwerben Sie möglicherweise eine falsche Größe. In diesem Fall erhalten Sie nicht den erwarteten Rabatt auf Ihre Reservierung. Beachten Sie stattdessen das Feld `AdditionalInfo` in Ihrer Nutzungsdatei oder die Nutzungs-API, um die richtige VM-Größe zu bestimmen.

### <a name="purchase-restriction-considerations"></a>Überlegungen zu Kaufeinschränkungen

Reservierte VM-Instanzen sind bis auf wenige Ausnahmen für die meisten VM-Größen verfügbar. Rabatte für Reservierungen gelten nicht für die folgenden VMs:

- **VM-Serien** – A-Serie, Av2-Serie oder G-Serie

- **Virtuelle Computer in Vorschauversionen** – jede VM-Serie oder -Größe in einer Vorschauversion

- **Clouds** – Reservierungen können in den Regionen Deutschland und China nicht käuflich erworben werden.

- **Nicht genügend Kontingent** – Für eine Reservierung, die einem einzelnen Abonnement zugeordnet ist, muss im Abonnement vCPU-Kontingent für die neue reservierte Instanz (RI) verfügbar sein. Beispiel: Wenn für das Zielabonnement eine Kontingentgrenze von zehn vCPUs für die D-Serie gilt, können Sie keine Reservierung für elf Standard_D1-Instanzen erwerben. Bei der Kontingentüberprüfung für Reservierungen werden die im Abonnement bereits bereitgestellten virtuellen Computer berücksichtigt. Beispiel: Wenn im Abonnement ein Kontingent von zehn vCPUs für die D-Serie enthalten ist und zwei Standard_D1-Instanzen bereitgestellt sind, können Sie eine Reservierung für zehn Standard_D1-Instanzen in diesem Abonnement erwerben. Um dieses Problem zu beheben, können Sie eine [Anforderung zur Kontingenterhöhung erstellen](../articles/azure-supportability/resource-manager-core-quotas-request.md).

- **Kapazitätsbeschränkungen** – In seltenen Fällen beschränkt Azure den Kauf neuer Reservierungen für eine Teilmenge der VM-Größen aufgrund geringer Kapazität in einer Region.

## <a name="buy-a-reserved-vm-instance"></a>Kaufen einer reservierten VM-Instanz

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie auf **Alle Dienste** > **Reservierungen**.
3. Klicken Sie auf **Hinzufügen**, um eine neue Reservierung zu erwerben.
4. Füllen Sie die erforderlichen Felder aus. Ausgeführte VM-Instanzen, die den ausgewählten Attributen entsprechen, sind für den Reservierungsrabatt berechtigt. Die tatsächliche Anzahl der VM-Instanzen, die den Rabatt erhalten, hängt vom ausgewählten Bereich und von der ausgewählten Menge ab.

    | Feld      | BESCHREIBUNG|
    |------------|--------------|
    |NAME        |Der Name dieser Reservierung|
    |Abonnement|Das zum Bezahlen für die Reservierung verwendete Abonnement. Die Zahlungsmethode für das Abonnement wird mit Vorauszahlungen für die Reservierung belastet. Der Abonnementtyp muss „Enterprise Agreement“ (Angebotsnummern: MS-AZR-0017P oder MS-AZR-0148P) oder „Nutzungsbasierte Zahlung“ (Angebotsnummern: MS-AZR-0003P oder MS-AZR-0023P) sein. Bei einem Enterprise-Abonnement werden die Gebühren vom Verpflichtungsguthaben der Reservierung abgezogen oder als Überschreitung belastet. Bei einem Abonnement mit nutzungsbasierter Zahlung wird die Kreditkarte mit den Gebühren belastet, oder die Gebühren werden für die Zahlung auf Rechnung in Rechnung gestellt.|    
    |`Scope`       |Der Bereich der Reservierung kann ein Abonnement oder mehrere Abonnements (freigegebener Bereich) umfassen. Optionen: <ul><li>Einzelnes Abonnement: Der Reservierungsrabatt wird auf die virtuellen Computern in diesem Abonnement angewendet. </li><li>Freigegeben: Der Reservierungsrabatt wird auf virtuelle Computer angewendet, die in einem beliebigen Abonnement innerhalb des Abrechnungskontexts ausgeführt werden. Für Enterprise-Kunden stellt der freigegebene Bereich die Registrierung dar und umfasst alle Abonnements in der Registrierung. Für Kunden mit nutzungsbasierter Zahlung stellt der freigegebene Bereich alle Abonnements mit nutzungsbasierter Zahlung dar, die vom Kontoadministrator erstellt wurden.</li></ul>|
    |Region    |Die Azure-Region, die durch die Reservierung abgedeckt wird|    
    |Größe des virtuellen Computers     |Die Größe der VM-Instanzen|
    |Optimiert für     |Mithilfe der Option für Flexibilität bei der VM-Instanzgröße wird der Rabatt für Reservierungen auf weitere VMs in derselben [VM-Größengruppe](https://aka.ms/RIVMGroups) angewendet. Die Option „Kapazitätspriorität“ priorisiert Rechenzentrumskapazität für Ihre Bereitstellungen. So erhalten Sie zusätzliche Sicherheit, dass die VM-Instanzen gestartet werden können, wenn sie benötigt werden. Die Kapazitätspriorität ist nur für den Reservierungsumfang „Einzelabonnement“ verfügbar. |
    |Begriff        |Ein Jahr oder drei Jahre|
    |Menge    |Die Anzahl von Instanzen, die innerhalb der Reservierung erworben werden. Die Menge ist die Anzahl der ausgeführten VM-Instanzen, auf die der Abrechnungsrabatt angewendet werden kann. Beispiel: Wenn Sie zehn virtuelle Computer vom Typ „10 Standard_D2“ in der Region „USA, Osten“ ausführen, geben Sie als Menge 10 an, um den Vorteil für alle ausgeführten Computer zu maximieren. |

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2PjmT]

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

## <a name="cancellations-and-exchanges"></a>Stornierungen und Umtausch

Wenn Sie Ihre Reservierung stornieren möchten, wird unter Umständen eine Gebühr für die vorzeitige Kündigung in Höhe von 12% berechnet. Rückerstattungen basieren auf dem niedrigsten Preis (entweder Ihrem Kaufpreis oder dem aktuellen Preis für die Reservierung). Rückerstattungen sind auf 50.000 US-Dollar pro Jahr beschränkt. Die Rückerstattung, die Sie erhalten, umfasst den verbleibenden anteiligen Saldo abzüglich der Gebühr für die vorzeitige Kündigung in Höhe von 12%. Navigieren Sie zum Anfordern einer Stornierung im Azure-Portal zu der Reservierung, und wählen Sie **Erstattung** aus, um eine Supportanfrage zu erstellen.

Wenn Sie Ihre Reservierung der reservierten VM-Instanzen in eine andere Region, eine andere VM-Größengruppe oder einen anderen Zeitraum ändern müssen, können Sie sie gegen eine andere Reservierung austauschen, die den gleichen oder einen höheren Wert hat. Das Startdatum der Laufzeit für die neue Reservierung wird nicht von der umgetauschten Reservierung übernommen. Die Laufzeit von einem oder drei Jahren beginnt ab der Erstellung der neuen Reservierung. Navigieren Sie zum Anfordern eines Umtauschs im Azure-Portal zu der Reservierung, und wählen Sie **Umtausch** aus, um eine Supportanfrage zu erstellen.

Weitere Informationen zum Umtausch oder zur Rückerstattung von Reservierungen finden Sie unter [Reservierungsumtausch und -rückerstattung](../articles/billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Verwalten einer Reservierung finden Sie unter [Verwalten von Azure-Reservierungen](../articles/billing/billing-manage-reserved-vm-instance.md).
- Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:
    - [Was sind Azure-Reservierungen?](../articles/billing/billing-save-compute-costs-reservations.md)
    - [Verwalten von Reservierungen für Ressourcen in Azure](../articles/billing/billing-manage-reserved-vm-instance.md)
    - [Grundlegendes zur Anwendung des Rabatts für Azure-Reservierungen auf virtuelle Computer](../articles/billing/billing-understand-vm-reservation-charges.md)
    - [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](../articles/billing/billing-understand-reserved-instance-usage.md)
    - [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](../articles/billing/billing-understand-reserved-instance-usage-ea.md)
    - [Nicht in Azure-Reservierungen enthaltene Windows-Softwarekosten](../articles/billing/billing-reserved-instance-windows-software-costs.md)
    - [Verkaufen Microsoft Azure Reserved Instances](https://docs.microsoft.com/partner-center/azure-reservations)
