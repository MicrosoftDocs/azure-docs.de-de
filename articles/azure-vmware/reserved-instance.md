---
title: Sparen von Kosten mit einer reservierten Azure VMware Solution-Instanz
description: Es wird beschrieben, wie Sie eine reservierte Instanz für Azure VMware Solution erwerben.
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 227ea9274f70cc58468e7e0b3ddcf8239f5b7f5b
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550974"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Sparen von Kosten mit Azure VMware Solution

Wenn Sie sich für eine reservierte Instanz von [Azure VMware Solution](introduction.md) entscheiden, sparen Sie Kosten.  Der Reservierungsrabatt wird automatisch auf die ausgeführten Azure VMware Solution-Hosts angewendet, die dem Reservierungsbereich und den Reservierungsattributen entsprechen. Bei Erwerb reservierter Instanzen wird nur der Computebereich Ihrer Nutzung abgedeckt, und es werden Softwarelizenzierungskosten berücksichtigt. 

## <a name="purchase-restriction-considerations"></a>Überlegungen zu Kaufeinschränkungen

Reservierte Instanzen sind mit einigen Ausnahmen verfügbar.

-   **Clouds**:Reservierungen sind nur in den Regionen verfügbar, die auf der Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) aufgeführt sind.

-   **Kontingent reicht nicht aus**: Für eine Reservierung, die einem einzelnen/gemeinsamen Abonnement zugeordnet ist, muss im Abonnement ein Hostkontingent für die neue reservierte Instanz verfügbar sein. Um dieses Problem zu beheben, können Sie eine [Anforderung zur Kontingenterhöhung erstellen](enable-azure-vmware-solution.md).

-   **Angebotsberechtigung**: Sie benötigen ein [Azure Enterprise Agreement (EA)](../cost-management-billing/manage/ea-portal-agreements.md) mit Microsoft.

-   **Kapazitätsbeschränkungen**: In seltenen Fällen beschränkt Azure den Erwerb neuer Reservierungen für Azure VMware Solution-Host-SKUs aufgrund geringer Kapazität in einer Region.

## <a name="buy-a-reservation"></a>Kaufen einer Reservierung

Sie können eine reservierte Instanz einer Azure VMware Solution-Hostinstanz im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D) erwerben.

Sie können die Reservierung [im Voraus oder monatlich bezahlen](../cost-management-billing/reservations/prepare-buy-reservation.md).

Diese Anforderungen gelten für den Erwerb einer reservierten Instanz eines dedizierten Hosts:

-   Sie müssen die Rolle *Besitzer* für mindestens ein EA-Abonnement oder ein Abonnement mit einem Satz für nutzungsbasierte Bezahlung haben.

-   Bei EA-Abonnements muss im [EA-Portal](https://ea.azure.com/) die Option **Reservierte Instanzen hinzufügen** aktiviert werden. Wenn diese Option deaktiviert ist, müssen Sie ein EA-Administrator für das Abonnement sein, um sie zu aktivieren.

-   Bei Abonnements mit einem Azure-Plan für Cloud Solution Provider (CSP) muss der Partner die reservierten Instanzen im Azure-Portal für den Kunden erwerben. 

### <a name="buy-reserved-instances-for-an-ea-subscription"></a>Erwerben von reservierten Instanzen für ein EA-Abonnement

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

2. Klicken Sie auf **Alle Dienste** > **Reservierungen**.

3. Wählen Sie **Jetzt kaufen** und dann **Azure VMware Solution** aus.

4. Füllen Sie die Pflichtfelder aus. Die ausgewählten Attribute, die ausgeführten Azure VMware Solution-Hosts entsprechen, führen zur Berechtigung für den Reservierungsrabatt.  Zu den Attributen zählen die SKU, Regionen (falls zutreffend) und der Bereich. Mit dem Reservierungsbereich wird ausgewählt, wo die Reservierungseinsparungen gelten.

   Wenn Sie über eine EA-Vereinbarung verfügen, können Sie die Option **Weitere hinzufügen** verwenden, um schnell Instanzen hinzuzufügen. Die Option ist für andere Abonnementtypen nicht verfügbar.

   | Feld        |  BESCHREIBUNG |
   | ------------ | ------------ |
   | Subscription | Das zum Bezahlen für die Reservierung verwendete Abonnement. Die Zahlungsmethode für das Abonnement wird mit Zahlungen für die Reservierung belastet. Der Abonnementtyp muss „Enterprise Agreement“ (Angebotsnummern: MS-AZR-0017P oder MS-AZR-0148P) „Microsoft-Kundenvereinbarung“ oder ein einzelnes Abonnement mit Sätzen für nutzungsbasierte Bezahlung (Angebotsnummern: MS-AZR-0003P oder MS-AZR-0023P) sein. Die Gebühren werden ggf. von der Azure-Vorauszahlung (zuvor als „Mindestverbrauch“ bezeichnet) abgezogen oder als Überschreitung belastet. Bei einem Abonnement mit Sätzen für nutzungsbasierte Zahlung wird die Kreditkarte mit den Gebühren belastet, oder die Gebühren werden für Zahlung auf Rechnung in Rechnung gestellt. |
   | `Scope`        | Der Bereich der Reservierung kann ein Abonnement oder mehrere Abonnements (freigegebener Bereich) umfassen. Optionen:<br><ul><li><b>Einzelne Ressourcengruppe</b>: Wendet den Reservierungsrabatt nur auf die entsprechenden Ressourcen in der ausgewählten Ressourcengruppe an.</li><li><b>Einzelabonnement</b>: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen im ausgewählten Abonnement an.</li><li><b>Gemeinsam genutzt</b>: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen in berechtigten Abonnements innerhalb des Abrechnungskontexts an. Für EA-Kunden ist der Abrechnungskontext die Registrierung. Für Kunden mit individuellen Abonnements mit nutzungsbasierten Tarifen handelt es sich beim Abrechnungsbereich um alle berechtigten Abonnements, die vom Kontoadministrator erstellt wurden.</li></ul>       |
   | Region       | Die Azure-Region, die durch die Reservierung abgedeckt wird   |
   | Hostgröße    | AV36    |
   | Begriff         | Ein Jahr oder drei Jahre  |
   | Menge     | Die Anzahl der innerhalb der Reservierung zu erwerbenden Instanzen Die Menge ist die Anzahl von ausgeführten Azure VMware Solution-Hosts, die den Abrechnungsrabatt erhalten können.    |

### <a name="buy-reserved-instances-for-a-csp-subscription"></a>Erwerben von reservierten Instanzen für ein CSP-Abonnement

CSPs, die reservierte Instanzen für ihre Kunden erwerben möchten, müssen die Prozedur **Administrator im Namen von** (Admin on Behalf Of, AOBO) aus der [Partner Center-Dokumentation](/partner-center/azure-plan-manage) verwenden. Weitere Informationen finden Sie im Video zu [Administrator im Namen von (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO).

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com) an.

2. Wählen Sie **CSP** aus, um auf den **Kundenbereich** zuzugreifen.

3. Erweitern Sie die Kundendetails, und wählen Sie **Microsoft Azure-Verwaltungsportal** aus. 

   :::image type="content" source="media/reserved-instances/csp-partner-center-aobo.png" alt-text="Microsoft Partner Center, Kundenbereich" lightbox="media/reserved-instances/csp-partner-center-aobo.png":::

4. Wählen Sie im Azure-Portal die Option **Alle Dienste** > **Reservierungen** aus.

5. Wählen Sie **Jetzt kaufen** und dann **Azure VMware Solution** aus.

   :::image type="content" source="media/reserved-instances/csp-buy-ri-azure-portal.png" alt-text="Microsoft Azure-Portal, Reservierungen" lightbox="media/reserved-instances/csp-buy-ri-azure-portal.png":::

6. Füllen Sie die Pflichtfelder aus. Die ausgewählten Attribute, die ausgeführten Azure VMware Solution-Hosts entsprechen, führen zur Berechtigung für den Reservierungsrabatt.  Zu den Attributen zählen die SKU, Regionen (falls zutreffend) und der Bereich. Mit dem Reservierungsbereich wird ausgewählt, wo die Reservierungseinsparungen gelten.

   | Feld        |  BESCHREIBUNG |
   | ------------ | ------------ |
   | Subscription | Das zum Bezahlen für die Reservierung verwendete Abonnement. Die Zahlungsmethode für das Abonnement wird mit Zahlungen für die Reservierung belastet. Der Abonnementtyp muss berechtigt sein, wie in diesem Fall ein CSP-Abonnement.|
   | `Scope`        | Der Bereich der Reservierung kann ein Abonnement oder mehrere Abonnements (freigegebener Bereich) umfassen. Optionen:<br><ul><li><b>Einzelne Ressourcengruppe</b>: Wendet den Reservierungsrabatt nur auf die entsprechenden Ressourcen in der ausgewählten Ressourcengruppe an.</li><li><b>Einzelabonnement</b>: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen im ausgewählten Abonnement an.</li><li><b>Gemeinsam genutzt</b>: Wendet den Reservierungsrabatt auf die entsprechenden Ressourcen in berechtigten Abonnements innerhalb des Abrechnungskontexts an. Für EA-Kunden ist der Abrechnungskontext die Registrierung. Für Kunden mit individuellen Abonnements mit nutzungsbasierten Tarifen handelt es sich beim Abrechnungsbereich um alle berechtigten Abonnements, die vom Kontoadministrator erstellt wurden.</li></ul>       |
   | Region       | Die Azure-Region, die durch die Reservierung abgedeckt wird   |
   | Hostgröße    | AV36    |
   | Begriff         | Ein Jahr oder drei Jahre  |
   | Menge     | Die Anzahl der innerhalb der Reservierung zu erwerbenden Instanzen Die Menge ist die Anzahl von ausgeführten Azure VMware Solution-Hosts, die den Abrechnungsrabatt erhalten können.     |

Weitere Informationen zum Anzeigen der erworbenen Reservierungen für Ihren Kunden finden Sie im Artikel [Anzeigen von Azure-Reservierungen als Cloud Solution Provider (CSP)](../cost-management-billing/reservations/how-to-view-csp-reservations.md).

## <a name="usage-data-and-reservation-usage"></a>Nutzungsdaten und Reservierungsnutzung

Ihre Nutzung, für die der Reservierungsrabatt gilt, hat effektiv den Preis „0“. Sie können sehen, welche Azure VMware Solution-Instanz den Reservierungsrabatt für jede Reservierung erhalten hat.

Weitere Informationen zur Anzeige von Reservierungsrabatten in Nutzungsdaten:

- Informationen für EA-Kunden finden Sie unter [Grundlegendes zur Nutzung von Azure-Reservierungen für die Enterprise-Registrierung](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md).
- Für Einzelabonnements lesen Sie [Grundlegendes zur Nutzung von Azure-Reservierungen für Ihr Abonnement mit nutzungsbasierter Zahlung](../cost-management-billing/reservations/understand-reserved-instance-usage.md).

## <a name="change-a-reservation-after-purchase"></a>Ändern einer Reservierung nach dem Kauf

Nach dem Kauf können Sie folgende Änderungen an einer Reservierung vornehmen:

-   Reservierungsumfang aktualisieren

-   Instanzgrößenflexibilität (sofern zutreffend)

-   Besitz

Sie können eine Reservierung auch in kleinere Blöcke aufteilen oder Reservierungen zusammenführen. Keine dieser Änderungen führt zu einer neuen Geschäftstransaktion, und das Enddatum der Reservierung bleibt von den Änderungen unberührt.

Ausführliche Informationen zu von CSP verwalteten Reservierungen finden Sie unter [Verkaufen von Microsoft Azure-Reservierungen an Kunden, die Partner Center, das Azure-Portal oder APIs verwenden](/partner-center/azure-reservations).



>[!NOTE]
>Nachdem Sie Ihre Reservierung erworben haben, können Sie die folgenden Arten von Änderungen nicht mehr direkt vornehmen:
>
> - Die Region einer vorhandenen Reservierung
> - SKU
> - Menge
> - Duration
>
>Sie können jedoch eine Reservierung *umtauschen*, wenn Sie Änderungen vornehmen möchten.

## <a name="cancel-exchange-or-refund-reservations"></a>Stornieren, Umtauschen oder Rückerstatten von Reservierungen

Reservierungen können unter bestimmten Einschränkungen storniert, umgetauscht oder rückerstattet werden. Weitere Informationen finden Sie unter [Self-Service-Umtausch und -Rückerstattungen für Azure-Reservierungen](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

CSP können für ihre Kunden erworbene Reservierungen mit bestimmten Einschränkungen stornieren, umtauschen oder rückerstatten. Weitere Informationen finden Sie unter [Verwalten, Abbrechen, Umtauschen oder Rückerstatten von Microsoft Azure-Reservierungen für Kunden](/partner-center/azure-reservations-manage).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie jetzt erfahren haben, wie Sie eine reservierte Instanz von Azure VMware Solution erwerben, möchten Sie vielleicht mehr über Folgendes erfahren:

- [Erstellen einer Azure VMware Solution-Bewertung](../migrate/how-to-create-azure-vmware-solution-assessment.md)
- [Verwalten von DHCP in Azure VMware Solution](manage-dhcp.md)
- [Lebenszyklusverwaltung von Azure VMware Solution-VMs](lifecycle-management-of-azure-vmware-solution-vms.md)
 