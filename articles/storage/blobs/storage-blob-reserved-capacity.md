---
title: Optimieren der Kosten für Blobspeicher mit reservierter Kapazität
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie reservierte Azure Storage-Kapazität kaufen, um Kosten für Blockblobs und Azure Data Lake Storage Gen2-Ressourcen zu sparen.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/08/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 1c957330fbd54660367fa350d6985929f0bbd3d1
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95531388"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>Optimieren der Kosten für Blobspeicher mit reservierter Kapazität

Mit reservierter Azure Storage-Kapazität Sie können Geld bei den Speicherkosten für Blobdaten sparen. Reservierte Azure Storage-Kapazität bietet einen Rabatt auf die Kapazität für Blockblobs und Azure Data Lake Storage Gen2-Daten in Standardspeicherkonten, wenn Sie eine Reservierung für ein Jahr oder drei Jahre vornehmen. Eine Reservierung stellt eine feste Speicherkapazität für den Zeitraum der Reservierung zur Verfügung.

Reservierte Azure Storage-Kapazität kann die Kapazitätskosten für Blockblobs und Azure Data Lake Storage Gen2-Daten erheblich verringern. Welche Kosteneinsparungen erzielt werden, hängt von der Dauer der Reservierung, von der Gesamtkapazität, die Sie reservieren möchten, sowie von der Zugriffsebene und der Art der Redundanz ab, die Sie für Ihr Speicherkonto ausgewählt haben. Die reservierte Kapazität ermöglicht lediglich einen Abrechnungsrabatt, der keine Auswirkungen auf den Zustand Ihrer Azure Storage-Ressourcen hat.

Informationen zu den Preisen für Azure Storage-Reservierungen finden Sie in der [Preisübersicht für Blockblobs](https://azure.microsoft.com/pricing/details/storage/blobs/) und in der [Preisübersicht für Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="reservation-terms-for-azure-storage"></a>Reservierungsbedingungen für Azure Storage

In den folgenden Abschnitten werden die Bedingungen einer Azure Storage-Reservierung beschrieben.

### <a name="reservation-capacity"></a>Reservierungskapazität

Sie können reservierte Azure Storage-Kapazität in Einheiten von 100 TiB und 1 PB pro Monat für eine Laufzeit von einem Jahr oder drei Jahren erwerben.

### <a name="reservation-scope"></a>Reservierungsumfang

Reservierte Azure Storage-Kapazität ist für ein einzelnes Abonnement oder für mehrere Abonnements (freigegebener Bereich) verfügbar. Bei Zuordnung zu einem einzelnen Abonnement wird der Reservierungsrabatt nur auf das ausgewählte Abonnement angewendet. Bei Zuordnung zu mehreren Abonnements wird der Reservierungsrabatt auf diese Abonnements innerhalb des Abrechnungskontexts des Kunden aufgeteilt.

Wenn Sie reservierte Azure Storage-Kapazität erwerben, können Sie Ihre Reservierung für Blockblob- und Azure Data Lake Storage Gen2-Daten verwenden. Die Reservierung gilt für Ihre Nutzung im erworbenen Umfang. Die Reservierung kann nicht auf ein bestimmtes Speicherkonto, einen bestimmten Container oder ein bestimmtes Objekt innerhalb des Abonnements beschränkt werden.

Eine Azure Storage-Reservierung deckt nur die Menge der Daten ab, die in einem Abonnement oder einer freigegebenen Ressourcengruppe gespeichert werden. Gebühren für vorzeitiges Löschen, Betrieb, Bandbreite und Datenübertragung sind nicht in der Reservierung enthalten. Sobald Sie eine Reservierung erworben haben, werden die Kapazitätsgebühren für die jeweiligen Reservierungsattribute rabattiert und nicht mehr zu den Preisen der nutzungsbasierten Bezahlung abgerechnet. Weitere Informationen zu Azure-Reservierungen finden Sie unter [Was sind Azure-Reservierungen?](../../cost-management-billing/reservations/save-compute-costs-reservations.md).

### <a name="supported-account-types-tiers-and-redundancy-options"></a>Unterstützte Kontotypen, Ebenen und Redundanzoptionen

Reservierte Azure Storage-Kapazität ist für Ressourcen in Standardspeicherkonten verfügbar, einschließlich universellen v2- (GPv2) und Blobspeicherkonten.

Für Reservierungen werden alle Zugriffsebenen (heiß, kalt und Archiv) unterstützt. Weitere Informationen zu Zugriffsebenen finden Sie unter [Azure Blob Storage: Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“](storage-blob-storage-tiers.md).

Für Reservierungen werden alle Redundanztypen unterstützt. Weitere Informationen zu Redundanzoptionen finden Sie unter [Azure Storage-Redundanz](../common/storage-redundancy.md).

> [!NOTE]
> Reservierte Azure Storage-Kapazität ist für Storage Premium-Konten, universelle v1-Speicherkonten (GPv1), Azure Data Lake Storage Gen1, Seitenblobs, Azure Queue Storage, Azure Table Storage oder Azure Files nicht verfügbar.  

### <a name="security-requirements-for-purchase"></a>Sicherheitsanforderungen für den Erwerb

So erwerben Sie reservierte Kapazität:

- Sie müssen für mindestens ein Enterprise-Abonnement oder ein individuelles Abonnement mit nutzungsbasierter Bezahlung über die Rolle **Besitzer** verfügen.
- Bei Enterprise-Abonnements muss **Reservierte Instanzen hinzufügen** im EA-Portal aktiviert sein. Wenn diese Einstellung deaktiviert ist, müssen Sie ein EA-Administrator für das Abonnement sein.
- Für das CSP-Programm (Cloud Solution Provider) können nur Administrator- und Vertriebs-Agents reservierte Azure Blob Storage-Kapazität erwerben.

## <a name="determine-required-capacity-before-purchase"></a>Ermitteln der erforderlichen Kapazität vor dem Erwerb

Wenn Sie eine Azure Storage-Reservierung erwerben, müssen Sie Region, Zugriffsebene und Redundanzoption für die Reservierung auswählen. Die Reservierung gilt nur für Daten, die in der betreffenden Region, Zugriffsebene und Redundanzebene gespeichert werden. Nehmen Sie beispielsweise an, dass Sie eine Reservierung für „USA, Westen“ für die heiße Zugriffsebene mit zonenredundantem Speicher (ZRS) erwerben. Sie können diese Reservierung nicht für Daten in „USA, Osten“, Daten in der Archivzugriffsebene oder Daten in georedundantem Speicher (GRS) verwenden. Sie können jedoch eine weitere Reservierung für die zusätzlichen Anforderungen erwerben.  

Reservierungen sind heute für 100 TiB- oder 1 PB-Blöcke möglich (mit höheren Rabatten für 1 PiB-Blöcke). Wenn Sie eine Reservierung im Azure-Portal erwerben, kann Microsoft Ihnen Empfehlungen auf der Grundlage Ihrer bisherigen Nutzung geben, damit Sie die Reservierung bestimmen können, die Sie erwerben sollten.

## <a name="purchase-azure-storage-reserved-capacity"></a>Erwerben reservierter Azure Storage-Kapazität

Sie können reservierte Azure Storage-Kapazität über das [Azure-Portal](https://portal.azure.com) erwerben. Sie können die Reservierung im Voraus oder monatlich bezahlen. Weitere Informationen zum Erwerb mit monatlichen Zahlungen finden Sie unter [Erwerben von Azure-Reservierungen mit Vorauszahlung oder monatlicher Zahlung](../../cost-management-billing/reservations/prepare-buy-reservation.md).

Hilfe bei der Ermittlung der geeigneten Reservierungsbedingungen für Ihr Szenario finden Sie unter [Grundlegendes zur Rabattierung von reservierter Azure Storage-Kapazität](../../cost-management-billing/reservations/understand-storage-charges.md).

Gehen Sie folgendermaßen vor, um reservierte Kapazität zu erwerben:

1. Navigieren Sie zum Bereich [Reservierungen erwerben](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) im Azure-Portal.  
1. Wählen Sie **Azure Blob Storage** aus, um eine neue Reservierung zu erwerben.  
1. Füllen Sie die Pflichtfelder aus, wie in der folgenden Tabelle beschrieben:

    ![Screenshot, der den Erwerb reservierter Kapazität veranschaulicht](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |Feld  |BESCHREIBUNG  |
   |---------|---------|
   |**Umfang**   |  Zeigt an, wie viele Abonnements die mit der Reservierung verbundenen Abrechnungsvorteile nutzen können. Sie steuert auch, wie die Reservierung auf bestimmte Abonnements angewendet wird. <br/><br/> Wenn Sie **Gemeinsam** auswählen, gilt der Reservierungsrabatt für die Azure Storage-Kapazität in einem beliebigen Abonnement innerhalb des Abrechnungskontexts. Der Abrechnungskontext basiert darauf, wie Sie sich für Azure registriert haben. Für Enterprise-Kunden stellt der freigegebene Bereich die Registrierung dar und umfasst alle Abonnements in der Registrierung. Für Kunden mit nutzungsbasierter Bezahlung umfasst der freigegebene Umfang alle Abonnements mit Preisen für nutzungsbasierte Bezahlung, die vom Kontoadministrator erstellt wurden.  <br/><br/>  Wenn Sie **Einzelabonnement** auswählen, gilt der Reservierungsrabatt für die Azure Storage-Kapazität im ausgewählten Abonnement. <br/><br/> Wenn Sie die Option **Einzelne Ressourcengruppe** wählen, gilt der Reservierungsrabatt für die Azure Storage-Kapazität im ausgewählten Abonnement und in der ausgewählten Ressourcengruppe in diesem Abonnement. <br/><br/> Sie können den Reservierungsumfang nach dem Erwerb der Reservierung ändern.  |
   |**Abonnement**  | Das Abonnement, das für die Bezahlung der Azure Storage-Reservierung verwendet wird. Die Zahlungsmethode für das ausgewählte Abonnement wird für das Inrechnungstellen der Kosten verwendet. Es muss einer der folgenden Abonnementtypen vorliegen: <br/><br/>  Enterprise Agreement (Angebotsnummer: MS-AZR-0017P oder MS-AZR-0148P): Bei einem Enterprise-Abonnement werden die Gebühren vom Verpflichtungsguthaben der Reservierung abgezogen oder als Überschreitung belastet. <br/><br/> Einzelnes Abonnement mit Preisen für nutzungsbasierte Bezahlung (Angebotsnummern: MS-AZR-0003P oder MS-AZR-0023P): Bei einem individuellen Abonnement mit Preisen für nutzungsbasierte Bezahlung wird die Kreditkarte mit den Gebühren belastet, oder die Gebühren werden für Zahlung auf Rechnung berechnet.    |
   | **Region** | Die Region, in der die Reservierung wirksam ist. |
   | **Zugriffsebene** | Die Zugriffsebene, für die die Reservierung wirksam ist. Die Optionen umfassen *Heiß*, *Kalt* und *Archiv*. Weitere Informationen zu Zugriffsebenen finden Sie unter [Azure Blob Storage: Zugriffsebenen „Heiß“ (Hot), „Kalt“ (Cool) und „Archiv“](storage-blob-storage-tiers.md). |
   | **Redundanz** | Die Redundanzoption für die Reservierung. Die Optionen umfassen *LRS*, *ZRS*, *GRS*, *GZRS*, *RA-GRS* und *RA-GZRS*. Weitere Informationen zu Redundanzoptionen finden Sie unter [Azure Storage-Redundanz](../common/storage-redundancy.md). |
   | **Fakturierungsintervall** | Gibt an, wie oft das Konto für die Reservierung belastet wird. Die Optionen umfassen *Monatlich* oder *Im Voraus*. |
   | **Größe** | Die Menge der zu reservierenden Kapazität |
   |**Begriff**  | Ein Jahr oder drei Jahre   |

1. Nach Auswahl der Parameter für die Reservierung werden die Kosten im Azure-Portal angezeigt. Das Portal zeigt auch den prozentualen Rabatt im Vergleich zur nutzungsbasierten Zahlung an.

1. Prüfen Sie im Bereich **Reservierungen erwerben** die Gesamtkosten der Reservierung. Sie können auch einen Namen für die Reservierung angeben.

    ![Screenshot, der den Erwerb einer Reservierung veranschaulicht](media/storage-blob-reserved-capacity/purchase-reservations.png)

Nach dem Erwerb einer Reservierung wird diese automatisch für alle Azure Storage-Blockblobs oder Azure Data Lake Storage Gen2-Ressourcen verwendet, die den Reservierungsbedingungen entsprechen. Wenn Sie noch keine Azure Storage-Ressourcen erstellt haben, wird die Reservierung angewendet, wenn Sie eine Ressource erstellen, die den Reservierungsbedingungen entspricht. In beiden Fällen gelten die Reservierungsbedingungen sofort nach dem erfolgreichen Erwerb.

## <a name="exchange-or-refund-a-reservation"></a>Umtauschen oder Rückerstatten von Reservierungen

Reservierungen können unter bestimmten Einschränkungen umgetauscht oder rückerstattet werden. Diese Einschränkungen werden in den folgenden Abschnitten beschrieben.

Navigieren Sie zum Umtauschen oder Rückerstatten von Reservierungen zu den Reservierungsdetails im Azure-Portal. Wählen Sie **Umtauschen** oder **Rückerstatten** aus und befolgen Sie die Anweisungen, um eine Supportanfrage zu übermitteln. Nachdem die Anfrage verarbeitet wurde, erhalten Sie von Microsoft eine E-Mail, die die Erledigung der Anfrage bestätigt.

Weitere Informationen zu Azure-Reservierungsrichtlinien finden Sie unter [Self-Service-Umtausch und -Rückerstattungen für Azure-Reservierungen](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Umtauschen einer Reservierung

Durch Umtausch einer Reservierung können Sie eine anteilige Rückerstattung für den nicht genutzten Teil der Reservierung erhalten. Sie können die Rückerstattung dann auf den Kaufpreis einer neuen Azure Storage-Reservierung anwenden.

Die Anzahl der möglichen Umtausche ist nach oben hin nicht begrenzt. Zudem ist der Umtausch gebührenfrei. Der Wert der neu erworbenen Reservierung muss größer-gleich der anteiligen Gutschrift der ursprünglichen Reservierung sein. Eine Azure Storage-Reservierung kann nur für eine andere Azure Storage-Reservierung und nicht für eine Reservierung eines anderen Azure-Diensts umgetauscht werden.

### <a name="refund-a-reservation"></a>Rückerstatten einer Reservierung

Sie können eine Azure Storage-Reservierung jederzeit stornieren. Bei der Stornierung erhalten Sie eine anteilige Rückerstattung basierend auf der Restlaufzeit der Reservierung abzüglich einer Gebühr für vorzeitige Kündigung in Höhe von 12 Prozent. Die maximale Erstattung pro Jahr beträgt 50.000 USD.

Bei der Stornierung einer Reservierung wird die Reservierung sofort beendet und die verbleibenden Monate werden an Microsoft zurückgegeben. Der verbleibende anteilige Saldo wird in der ursprünglichen Kaufform erstattet, abzüglich der Gebühr.

## <a name="expiration-of-a-reservation"></a>Ablauf einer Reservierung

Beim Ablauf einer Reservierung wird jegliche Azure Storage-Kapazität, die Sie nach der Reservierung weiter nutzen, nutzungsabhängig in Rechnung gestellt. Reservierungen werden nicht automatisch verlängert.

Sie erhalten 30 Tage vor dem Ablauf der Reservierung und am Ablaufdatum eine E-Mail-Benachrichtigung. Um die Vorteile der Kosteneinsparungen einer Reservierung weiter zu nutzen, muss die Reservierung spätestens am Ablaufdatum verlängert werden.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

- [Was sind Azure-Reservierungen?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Grundlegendes zur Anwendung des Rabatts für Reservierungen auf Azure Storage](../../cost-management-billing/reservations/understand-storage-charges.md)