---
title: Konfigurieren der Angebotseigenschaften Ihrer Azure-Anwendung
description: Hier erfahren Sie, wie Sie die Angebotseigenschaften für Ihre Azure-Anwendung in Partner Center konfigurieren.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: 6f92c138a02c9dbdc1d22ca04c733cfbee69dcd0
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488518"
---
# <a name="how-to-configure-your-azure-application-offer-properties"></a>Konfigurieren der Angebotseigenschaften Ihrer Azure-Anwendung

In diesem Artikel wird beschrieben, wie Sie die Angebotseigenschaften für die Azure-Anwendung im kommerziellen Marketplace konfigurieren.

Auf der Seite **Eigenschaften** definieren Sie die für Ihr Angebot geltenden Kategorien und rechtlichen Verträge. Achten Sie darauf, dass Sie auf dieser Seite vollständige und genaue Angaben zu Ihrem Angebot machen, damit es richtig angezeigt und der richtigen Kundengruppe angeboten wird.

## <a name="select-a-category-for-your-offer"></a>Auswählen einer Kategorie für Ihr Angebot

Klicken Sie unter **Kategorien** auf **Kategorien**, und wählen Sie mindestens eine und maximal zwei Kategorien aus, um Ihr Angebot in die entsprechenden Suchbereiche des kommerziellen Marketplace einzuteilen. Wählen Sie bis zu zwei Unterkategorien für jede primäre und sekundäre Kategorie aus. Wenn keine Unterkategorie auf Ihr Angebot anwendbar ist, wählen Sie **Nicht zutreffend** aus.

## <a name="provide-terms-and-conditions"></a>Angeben von Geschäftsbedingungen

Geben Sie unter **Rechtliche Hinweise** Nutzungsbedingungen für das Angebot an. Sie haben zwei Möglichkeiten:

- [Verwenden des Standardvertrags mit optionalen Änderungen](#use-the-standard-contract)
- [Verwenden Ihrer eigenen Geschäftsbedingungen](#use-your-own-terms-and-conditions)

Informationen zum Standardvertrag und zu optionalen Änderungen finden Sie unter [Standardvertrag für den kommerziellen Microsoft-Marketplace](standard-contract.md). Sie können den [Standardvertrag](https://go.microsoft.com/fwlink/?linkid=2041178) als PDF-Datei herunterladen (achten Sie darauf, Ihren Popupblocker zu deaktivieren).

### <a name="use-the-standard-contract"></a>Verwenden des Standardvertrags

Zur Vereinfachung des Beschaffungsprozesses für Kunden und zur Reduzierung der rechtlichen Komplexität für Softwareanbieter stellt Microsoft eine Vorlage für Standardverträge bereit, die Sie für Ihre Angebote im kommerziellen Marketplace verwenden können. Wenn Sie Ihre Software unter dem Standardvertrag anbieten, müssen Kunden ihn nur einmal lesen und akzeptieren, und Sie müssen keine benutzerdefinierten Geschäftsbedingungen erstellen.

1. Aktivieren Sie das Kontrollkästchen **Verwenden Sie den Standardvertrag für den kommerziellen Microsoft-Marketplace**.

   ![Abbildung des Kontrollkästchens „Verwenden Sie den Standardvertrag für den kommerziellen Microsoft-Marketplace“.](partner-center-portal/media/use-standard-contract.png)

1. Wählen Sie im Dialogfeld **Bestätigung** die Option **Akzeptieren** aus. Unter Umständen müssen Sie nach oben scrollen, um die Option anzuzeigen.
1. Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

> [!NOTE]
> Nachdem Sie ein Angebot mit dem Standardvertrag für den kommerziellen Marketplace veröffentlicht haben, können Sie keine eigenen benutzerdefinierten Geschäftsbedingungen mehr verwenden. Entweder bieten Sie Ihre Lösung im Rahmen des Standardvertrags mit optionalen Änderungen oder mit Ihren eigenen Geschäftsbedingungen an.

### <a name="add-amendments-to-the-standard-contract-optional"></a>Hinzufügen von Änderungen zum Standardvertrag (optional)

Es stehen zwei Arten von Änderungen zur Verfügung: _universell_ und _benutzerdefiniert_.

#### <a name="add-universal-amendment-terms"></a>Hinzufügen von universellen Zusatzvereinbarungen

Geben Sie im Feld **Dem Standardvertrag für den kommerziellen Microsoft-Marketplace allgemeine Zusatzbedingungen hinzufügen** Ihre universellen Zusatzvereinbarungen ein. Sie können eine unbegrenzte Anzahl von Zeichen in dieses Feld eingeben. Die Bestimmungen werden Kunden während des Ermittlungs- und Kaufprozesses in AppSource, dem Azure Marketplace und dem Azure-Portal angezeigt.

#### <a name="add-one-or-more-custom-amendments"></a>Hinzufügen benutzerdefinierter Änderungen

1. Wählen Sie unter **Dem Standardvertrag für den kommerziellen Microsoft-Marketplace benutzerdefinierte Zusatzbedingungen hinzufügen** den Link **Benutzerdefinierte Zusatzbedingung hinzufügen (maximal 10)** aus.
1. Geben Sie im Feld **Benutzerdefinierte Zusatzbedingungen** Ihre Zusatzbestimmungen ein.
1. Geben Sie im Feld **Mandanten-ID** eine Mandanten-ID ein. Diese benutzerdefinierten Vereinbarungen werden im Azure-Portal im Rahmen der Kaufabwicklung für das Angebot nur Kunden mit den für sie angegebenen Mandanten-IDs angezeigt.

   > [!TIP]
   > Die Mandanten-ID identifiziert Ihren Kunden in Azure. Sie können Ihren Kunden nach dieser ID fragen. Kunden finden sie unter [ **https://portal.azure.com**](https://portal.azure.com) > **Azure Active Directory** > **Eigenschaften**. Der Wert der Verzeichnis-ID ist die Mandanten-ID (z. B. `50c464d3-4930-494c-963c-1e951d15360e`). Sie können die von Ihrer Organisation zugeteilte Mandanten-ID Ihres Kunden auch nachschlagen, indem Sie unter [Wie lautet meine Mandanten-ID für Microsoft Azure und Office 365?](https://www.whatismytenantid.com/) die Domänennamen-URL des Kunden verwenden.

1. Geben Sie im Feld **Beschreibung** optional eine benutzerfreundliche Beschreibung für die Mandanten-ID ein. Diese Beschreibung hilft Ihnen dabei, den Kunden anzugeben, für den der Zusatz gedacht ist.
1. Wählen Sie zum Hinzufügen einer weiteren Mandanten-ID den Link **Mandanten-ID eines Kunden hinzufügen** aus, und wiederholen Sie die Schritte 3 und 4. Sie können bis zu 20 Mandanten-IDs hinzufügen.
1. Um weitere Zusatzstimmungen hinzuzufügen, wiederholen Sie die Schritte 1 bis 5. Sie können bis zu zehn benutzerdefinierte Zusatzvereinbarungen pro Angebot angeben.
1. Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

### <a name="use-your-own-terms-and-conditions"></a>Verwenden Ihrer eigenen Geschäftsbedingungen

Sie können anstelle des Standardvertrags Ihre eigenen Geschäftsbedingungen angeben. Kunden müssen diesen Bestimmungen zustimmen, bevor sie Ihr Angebot testen können.

1. Achten Sie unter **Rechtliche Hinweise** darauf, dass das Kontrollkästchen **Verwenden Sie den Standardvertrag für den kommerziellen Microsoft-Marketplace** deaktiviert ist.
1. Geben Sie im Feld **Geschäftsbedingungen** bis zu 10.000 Zeichen Text ein.
1. Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte fortfahren: Angebotsliste

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren der Angebotsdetails für die Azure-Anwendung](create-new-azure-apps-offer-listing.md)
