---
title: Konfigurieren von VM-Angebotseigenschaften im Azure Marketplace
description: Hier erfahren Sie, wie Sie die Eigenschaften von VM-Angeboten im Azure Marketplace konfigurieren.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: mingshen
ms.date: 10/19/2020
ms.openlocfilehash: 281553782774c31ec8cfaf614542fd739c4d3dd9
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629528"
---
# <a name="how-to-configure-virtual-machine-offer-properties"></a>Konfigurieren von VM-Angebotseigenschaften

Auf der Seite **Eigenschaften** (Option in Partner Center im linken Navigationsmenü) definieren Sie die Kategorien zum Gruppieren Ihres VM-Angebots im Azure Marketplace, die Anwendungsversion und die Verträge für Ihr Angebot.

## <a name="select-a-category"></a>Kategorie auswählen

Wählen Sie Kategorien und Unterkategorien aus, um Ihr Angebot in den geeigneten Azure Marketplace-Suchbereichen zu platzieren. Vergessen Sie nicht, an späterer Stelle in der Angebotsbeschreibung anzugeben, inwiefern Ihr Angebot diesen Kategorien entspricht.

- Wählen Sie eine primäre Kategorie aus.
- Zum Hinzufügen einer zweiten optionalen Kategorie (sekundäre Kategorie) wählen Sie den Link **+ Kategorien** aus.
- Wählen Sie bis zu zwei Unterkategorien für die primäre und/oder sekundäre Kategorie aus. Wenn keine Unterkategorie auf Ihr Angebot anwendbar ist, wählen Sie **Nicht zutreffend** aus. Klicken Sie bei gedrückter STRG-TASTE, um eine zweite Unterkategorie auszuwählen.

Eine vollständige Liste der Kategorien und Unterkategorien finden Sie unter [Bewährte Methoden für Angebotslistung](gtm-offer-listing-best-practices.md). Angebote für virtuelle Computer werden im Azure Marketplace immer unter der Kategorie **Compute** angezeigt.

## <a name="provide-terms-and-conditions"></a>Angeben von Geschäftsbedingungen

Geben Sie unter **Rechtliche Hinweise** Nutzungsbedingungen für das Angebot an. Sie haben zwei Möglichkeiten:

- [Verwenden des Standardvertrags mit optionalen Änderungen](#use-the-standard-contract)
- [Verwenden Ihrer eigenen Geschäftsbedingungen](#use-your-own-terms-and-conditions)

Informationen zum Standardvertrag und zu optionalen Änderungen finden Sie unter [Standardvertrag für den kommerziellen Microsoft-Marketplace](standard-contract.md). Sie können den [Standardvertrag](https://go.microsoft.com/fwlink/?linkid=2041178) als PDF-Datei herunterladen (achten Sie darauf, Ihren Popupblocker zu deaktivieren).

### <a name="use-the-standard-contract"></a>Verwenden des Standardvertrags

Zur Vereinfachung des Beschaffungsprozesses für Kunden und zur Reduzierung der rechtlichen Komplexität für Softwareanbieter stellt Microsoft eine Vorlage für Standardverträge bereit, die Sie für Ihre Angebote im kommerziellen Marketplace verwenden können. Wenn Sie Ihre Software unter dem Standardvertrag anbieten, müssen Kunden ihn nur einmal lesen und akzeptieren, und Sie müssen keine benutzerdefinierten Geschäftsbedingungen erstellen.

1. Aktivieren Sie das Kontrollkästchen **Verwenden Sie den Standardvertrag für den kommerziellen Microsoft-Marketplace**.

   ![Abbildung des Kontrollkästchens „Verwenden Sie den Standardvertrag für den kommerziellen Microsoft-Marketplace“.](partner-center-portal/media/use-standard-contract.png)

1. Wählen Sie im Dialogfeld **Bestätigung** die Option **Akzeptieren** aus. Je nach Größe Ihres Bildschirms müssen Sie möglicherweise nach oben scrollen, um die Option anzuzeigen.
1. Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

   > [!NOTE]
   > Nachdem Sie ein Angebot mit dem Standardvertrag für den kommerziellen Marketplace veröffentlicht haben, können Sie keine eigenen benutzerdefinierten Geschäftsbedingungen mehr verwenden. Entweder bieten Sie Ihre Lösung im Rahmen des Standardvertrags mit optionalen Änderungen oder mit Ihren eigenen Geschäftsbedingungen an.

#### <a name="add-amendments-to-the-standard-contract-optional"></a>Hinzufügen von Änderungen zum Standardvertrag (optional)

Es stehen zwei Arten von Änderungen zur Verfügung: *universell* und *benutzerdefiniert*.

##### <a name="add-universal-amendment-terms"></a>Hinzufügen von universellen Zusatzvereinbarungen

Geben Sie im Feld **Dem Standardvertrag für den kommerziellen Microsoft-Marketplace allgemeine Zusatzbedingungen hinzufügen** Ihre universellen Zusatzvereinbarungen ein. Sie können eine unbegrenzte Anzahl von Zeichen in dieses Feld eingeben. Die Bestimmungen werden Kunden während des Ermittlungs- und Kaufprozesses in AppSource, dem Azure Marketplace und dem Azure-Portal angezeigt.

##### <a name="add-one-or-more-custom-amendments"></a>Hinzufügen benutzerdefinierter Änderungen

1. Wählen Sie unter **Dem Standardvertrag für den kommerziellen Microsoft-Marketplace benutzerdefinierte Zusatzbedingungen hinzufügen** den Link **Benutzerdefinierte Zusatzbedingung hinzufügen (maximal 10)** aus.
2. Geben Sie Ihre Zusatzbedingungen in das Feld **Benutzerdefinierte Zusatzbedingungen** ein.
3. Geben Sie die **Mandanten-ID** in das Feld ein. Diese benutzerdefinierten Vereinbarungen werden im Azure-Portal im Rahmen der Kaufabwicklung für das Angebot nur Kunden mit den für sie angegebenen Mandanten-IDs angezeigt.

   > [!TIP]
   > Die Mandanten-ID identifiziert Ihren Kunden in Azure. Sie können Ihren Kunden nach dieser ID fragen. Kunden finden sie unter [ **https://portal.azure.com**](https://portal.azure.com) > **Azure Active Directory** > **Eigenschaften**. Der Wert der Verzeichnis-ID ist die Mandanten-ID (z. B. `50c464d3-4930-494c-963c-1e951d15360e`). Sie können die von Ihrer Organisation zugeteilte Mandanten-ID Ihres Kunden auch nachschlagen, indem Sie unter [Wie lautet meine Mandanten-ID für Microsoft Azure und Office 365?](https://www.whatismytenantid.com/) die Domänennamen-URL des Kunden verwenden.

4. Geben Sie optional eine benutzerfreundliche **Beschreibung** für die Mandanten-ID ein. Diese Beschreibung hilft Ihnen dabei, den Kunden anzugeben, für den der Zusatz gedacht ist.
5. Wählen Sie zum Hinzufügen einer weiteren Mandanten-ID den Link **Mandanten-ID eines Kunden hinzufügen (maximal 10)** aus, und wiederholen Sie die Schritte 3 und 4. Sie können bis zu 20 Mandanten-IDs hinzufügen.
6. Um weitere Zusatzstimmungen hinzuzufügen, wiederholen Sie die Schritte 1 bis 5. Sie können bis zu zehn benutzerdefinierte Zusatzvereinbarungen pro Angebot angeben.
7. Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

### <a name="use-your-own-terms-and-conditions"></a>Verwenden Ihrer eigenen Geschäftsbedingungen

Sie können anstelle des Standardvertrags Ihre eigenen Geschäftsbedingungen angeben. Kunden müssen diesen Bestimmungen zustimmen, bevor sie Ihr Angebot testen können.

1. Deaktivieren Sie unter **Rechtliche Hinweise** das Kontrollkästchen **Verwenden Sie den Standardvertrag für den kommerziellen Microsoft-Marketplace**.
1. Geben Sie im Feld **Geschäftsbedingungen** bis zu 10.000 Zeichen Text ein.

   > [!NOTE]
   > Wenn Sie eine längere Beschreibung benötigen, geben Sie eine einzelne Webadresse ein, unter der Ihre Geschäftsbedingungen zu finden sind. Sie wird Kunden als aktiver Link angezeigt.

1. Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte im linken Navigationsmenü (**Angebotsliste**) fortfahren.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren eines VM-Angebotslistings](azure-vm-create-listing.md)
