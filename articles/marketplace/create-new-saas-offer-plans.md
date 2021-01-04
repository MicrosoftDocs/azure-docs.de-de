---
title: Erstellen von Plänen für Ihr SaaS-Angebot in Microsoft Partner Center
description: Hier erfahren Sie, wie Sie Pläne für ein neues SaaS-Angebot (Software-as-a-Service) über das Portal des kommerziellen Marketplace von Microsoft im Partner Center erstellen.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 09/02/2020
ms.openlocfilehash: 238ef9ec80b01470e28535d0eb42dbb3a377b005
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746436"
---
# <a name="how-to-create-plans-for-your-saas-offer"></a>Erstellen von Plänen für Ihr SaaS-Angebot

Angebote, die über den kommerziellen Marketplace von Microsoft verkauft werden, müssen mindestens einen Plan aufweisen. Sie können eine Vielzahl von Plänen mit unterschiedlichen Optionen im gleichen Angebot erstellen. Diese Pläne (auch als SKUs bezeichnet) können sich in Bezug auf Version, Monetarisierung und Dienstebenen unterscheiden. Ausführliche Anleitungen zu Plänen finden Sie unter [Pläne und Preise für Angebote im kommerziellen Marketplace](plans-pricing.md).

> [!NOTE]
> Wenn Sie Transaktionen unabhängig verarbeiten, wird diese Option nicht angezeigt. In diesem Fall können Sie mit [Verkaufen Ihres SaaS-Angebots](create-new-saas-offer-marketing.md) fortfahren.

## <a name="create-a-plan"></a>Erstellen von Plänen

1. Wählen Sie oben auf der Registerkarte **Planübersicht** die Option **+ Neuen Plan erstellen** aus.

1. Geben Sie im angezeigten Dialogfeld im Feld **Plan-ID** eine eindeutige Plan-ID ein. Verwenden Sie bis zu 50 Kleinbuchstaben, Ziffern, Bindestriche und Unterstriche. Die Plan-ID kann nicht mehr geändert werden, nachdem Sie **Erstellen** ausgewählt haben.

1. Geben Sie im Feld **Planname** einen eindeutigen Namen für diesen Plan ein. Es sind maximal 50 Zeichen zulässig.

1. Klicken Sie auf **Erstellen**.

## <a name="define-the-plan-listing"></a>Definieren des Planlistings

Auf der Registerkarte **Planlisting** können Sie den Plannamen und die Beschreibung so definieren, wie sie im kommerziellen Marketplace angezeigt werden sollen.

1. Im Feld **Planname** wird der Name angezeigt, den Sie zuvor für diesen Plan angegeben haben. Sie können diesen jederzeit ändern. Dieser Name wird als Titel des Softwareplans für Ihr Angebot im kommerziellen Marketplace angezeigt.

1. Erläutern Sie im Feld **Planbeschreibung**, was diesen Softwareplan einzigartig macht und von anderen Softwareplänen in Ihrem Angebot unterscheidet. Diese Beschreibung kann bis zu 500 Zeichen umfassen.
1. Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte **Preise und Verfügbarkeit** fortfahren.

## <a name="define-markets-pricing-and-availability"></a>Definieren von Märkten, Preisen und Verfügbarkeit

Jeder Plan muss in mindestens einem Markt verfügbar sein. Auf der Registerkarte **Preise und Verfügbarkeit** können Sie die Märkte, in denen der Plan verfügbar ist, das gewünschte Monetarisierungsmodell, den Preis und den Abrechnungszeitraum konfigurieren. Darüber hinaus können Sie angeben, ob der Plan für alle Benutzer oder nur für bestimmte Kunden angezeigt werden soll (auch als privater Plan bezeichnet).

1. Wählen Sie unter **Märkte** den Link **Märkte bearbeiten** aus.
1. Wählen Sie im angezeigten Dialogfeld die Marktregionen aus, in denen Sie Ihren Plan anbieten möchten. Sie müssen mindestens einen von höchstens 141 Märkten auswählen.

   > [!NOTE]
   > Dieses Dialogfeld enthält ein Suchfeld und eine Option zum Herausfiltern von Ländern der Kategorie „Bezahlte Steuern“, in denen Microsoft die Mehrwert- und Verbrauchssteuer für Sie abführt.

1. Wählen Sie **Speichern** aus, um das Dialogfeld zu schließen.

## <a name="define-a-pricing-model"></a>Definieren eines Preismodells

Sie müssen jedem Plan ein Preismodell zuordnen. Sie haben die Wahl zwischen _Pauschalgebühr_ und _Pro Benutzer_. Alle Pläne in einem Angebot müssen dasselbe Preismodell aufweisen. Beispielsweise kann ein Angebot nicht gleichzeitig über Pläne mit Pauschalgebühr und Pläne mit Preis pro Benutzer verfügen. Weitere Informationen finden Sie unter [SaaS-Preismodell](plan-saas-offer.md#saas-pricing-models).

> [!IMPORTANT]
> Nachdem das Angebot veröffentlicht wurde, können Sie das Preismodell nicht mehr ändern. Darüber hinaus müssen alle Pläne für das gleiche Angebot dasselbe Preismodell aufweisen.

### <a name="configure-flat-rate-pricing"></a>Konfigurieren eines Pauschalpreises

1. Wählen Sie auf der Registerkarte **Preise und Verfügbarkeit** unter **Preise** die Option **Pauschalgebühr** aus.
1. Aktivieren Sie das Kontrollkästchen **Monatlich** und/oder **Jährlich**, und geben Sie dann den Preis ein.

### <a name="add-a-custom-meter-dimension"></a>Hinzufügen einer benutzerdefinierten Dimension für die Verbrauchseinheit

Diese Option ist nur verfügbar, wenn Sie „Pauschalgebühr“ ausgewählt haben. Weitere Informationen finden Sie unter [Volumenabrechnung für SaaS mit dem Messungsdienst für den kommerziellen Marketplace](./partner-center-portal/saas-metered-billing.md).

1. Wählen Sie unter **Dimensionen des Marketplace-Messungsdiensts** den Link **Benutzerdefinierte Dimension für Verbrauchseinheit hinzufügen (max. 30)** aus.
1. Geben Sie im Feld **ID** die unveränderliche Kennungsreferenz für die Ausgabe von Nutzungsereignissen ein.
1. Geben Sie im Feld **Anzeigename** den Anzeigenamen ein, der der Dimension zugeordnet ist. Beispiel: „Gesendete Textnachrichten“.
1. Geben Sie im Feld **Maßeinheit** die Beschreibung der Abrechnungseinheit ein. Beispiel: „pro Textnachricht“ oder „pro 100 E-Mails“.
1. Geben Sie im Feld **Preis pro Einheit in USD** den Preis für eine Einheit der Dimension ein.
1. Geben Sie im Feld **Grundsätzlich enthaltene monatliche Menge** die Menge der Dimension (als ganze Zahl) ein, die für Kunden, die die wiederkehrende monatliche Gebühr zahlen, jeden Monat inklusive ist. Aktivieren Sie stattdessen das Kontrollkästchen, wenn Sie eine unbegrenzte Anzahl festlegen möchten.
1. Geben Sie im Feld **Grundsätzlich enthaltene jährliche Menge** die Menge der Dimension (als ganze Zahl) ein, die für Kunden, die die wiederkehrende jährliche Gebühr zahlen, jeden Monat inklusive ist. Aktivieren Sie stattdessen das Kontrollkästchen, wenn Sie eine unbegrenzte Anzahl festlegen möchten.
1. Zum Hinzufügen einer weiteren benutzerdefinierten Dimension für die Verbrauchseinheit wählen Sie den Link **Weitere Dimension hinzufügen** aus, und wiederholen Sie dann die Schritte 1 bis 7.

### <a name="configure-per-user-pricing"></a>Konfigurieren von Preisen pro Benutzer

1. Wählen Sie auf der Registerkarte **Preise und Verfügbarkeit** unter **Preise** die Option **Pro Benutzer** aus.
2. Geben Sie ggf. unter **Benutzerlimits** die Mindest- und Höchstzahl von Benutzern für diesen Plan an.
3. Geben Sie unter **Abrechnungszeitraum** einen Monats- und/oder Jahrespreis an.

### <a name="validate-custom-prices"></a>Überprüfen benutzerdefinierter Preise

Wenn Sie benutzerdefinierte Preise für einen einzelnen Markt festlegen möchten, exportieren, ändern und importieren Sie die Preistabelle. Sie sind für die Überprüfung des Preismodells und diese Einstellungen verantwortlich. Ausführliche Informationen finden Sie unter [Benutzerdefinierte Preise](plans-pricing.md#custom-prices).

1. Sie müssen Ihre Preisänderungen speichern, damit die Preisdaten exportiert werden können. Wählen Sie im unteren Bereich der Registerkarte **Preise und Verfügbarkeit** die Option **Entwurf speichern** aus.
1. Wählen Sie unter **Preise** den Link **Preisdaten exportieren** aus.
1. Öffnen Sie die Datei „exportedPrice.xlsx“ in Microsoft Excel.
1. Nehmen Sie in der Tabelle die gewünschten Änderungen an den Preisinformationen vor, und speichern Sie die CSV-Datei.<br> Möglicherweise müssen Sie die Bearbeitung in Excel aktivieren, damit Sie die Datei aktualisieren können.
2. Wählen Sie auf der Registerkarte **Preise und Verfügbarkeit** unter **Preise** den Link **Preisdaten importieren** aus.
3. Klicken Sie im angezeigten Dialogfeld auf **Ja**.
4. Wählen Sie die aktualisierte Datei „exportedPrice.xlsx“ aus, und klicken Sie dann auf **Öffnen**.

### <a name="enable-a-free-trial"></a>Aktivieren einer kostenlosen Testversion

Sie können für jeden Plan in Ihrem Angebot eine kostenlose Testversion konfigurieren. Aktivieren Sie das Kontrollkästchen, um eine einmonatige kostenlose Testversion zuzulassen. Dieses Kontrollkästchen ist bei Plänen, die den Marketplace-Messungsdienst verwenden, nicht verfügbar. Weitere Informationen finden Sie unter [Kostenlose Testversionen](plans-pricing.md#free-trials).

> [!IMPORTANT]
> Nachdem Ihr transaktionsfähiges Angebot mit einer kostenlosen Testversion veröffentlicht wurde, kann es für den betreffenden Plan nicht mehr deaktiviert werden. Vergewissern Sie sich vor der Veröffentlichung des Angebots, dass diese Einstellung korrekt ist, damit der Plan nicht neu erstellt werden muss.

- Aktivieren Sie unter **Kostenlose Testversion** das Kontrollkästchen **Kostenlose Testversion von einem Monat zulassen**.

## <a name="choose-who-can-see-your-plan"></a>Auswählen der Sichtbarkeit des Plans

Sie können festlegen, dass jeder Plan für alle Benutzer oder nur für eine bestimmte Zielgruppe angezeigt werden soll. Der Zugriff auf einen privaten Plan wird über Mandanten-IDs gewährt. Dabei kann eine Beschreibung jeder zugewiesenen Mandanten-ID angegeben werden. Sie können bis zu 10 Mandanten-IDs manuell oder bis zu 20.000 Mandanten-IDs über eine CSV-Datei hinzufügen.

> [!NOTE]
> Wenn Sie einen privaten Plan veröffentlichen, können Sie die Sichtbarkeit später in „Öffentlich“ ändern. Wenn Sie hingegen einen öffentlichen Plan veröffentlichen, können Sie dessen Sichtbarkeit nicht in „Privat“ ändern.

### <a name="make-your-plan-public"></a>Festlegen eines Plans als öffentlich

1. Wählen Sie unter **Sichtbarkeit des Plans** das Feld **Öffentlich** aus.
1. Wählen Sie **Entwurf speichern** aus, und wählen Sie dann in der oberen linken Ecke der Registerkarte **Planübersicht** aus, um zur Registerkarte **Planübersicht** zurückzukehren.
1. Um einen weiteren Plan für dieses Angebot zu erstellen, wählen Sie im oberen Bereich der Registerkarte **Planübersicht** die Option **+ Neuen Plan erstellen** aus. Wiederholen Sie dann die Schritte im Abschnitt [Erstellen von Plänen](#create-a-plan). Fahren Sie andernfalls mit [Anzeigen der Pläne](#view-your-plans) fort.

### <a name="manually-add-tenant-ids-for-a-private-plan"></a>Manuelles Hinzufügen von Mandanten-IDs für einen privaten Plan 

1. Aktivieren Sie unter **Plansichtbarkeit** das Kontrollkästchen **Dies ist ein privater Plan**.
1. Geben Sie im eingeblendeten Feld **Mandanten-ID** die Azure AD-Mandanten-ID der Zielgruppe ein, der Sie Zugriff auf diesen privaten Plan gewähren möchten. Es ist mindestens eine Mandanten-ID erforderlich.
1. (Optional) Geben Sie im Feld **Beschreibung** eine Beschreibung der Zielgruppe ein.
1. Um eine weitere Mandanten-ID hinzuzufügen, wiederholen Sie die Schritte 2 und 3.
1. Sobald Sie das Hinzufügen von Mandanten-IDs abgeschlossen haben, wählen Sie **Entwurf speichern** aus, und wählen Sie dann in der oberen linken Ecke der Registerkarte **Planübersicht** aus, um zur Registerkarte **Planübersicht** zurückzukehren.
1. Um einen weiteren Plan für dieses Angebot zu erstellen, wählen Sie im oberen Bereich der Registerkarte **Planübersicht** die Option **+ Neuen Plan erstellen** aus. Wiederholen Sie dann die Schritte im Abschnitt [Erstellen von Plänen](#create-a-plan). Fahren Sie andernfalls mit [Anzeigen der Pläne](#view-your-plans) fort.

### <a name="use-a-csv-file-for-a-private-plan"></a>Verwenden einer CSV-Datei für einen privaten Plan

1. Aktivieren Sie unter **Plansichtbarkeit** das Kontrollkästchen **Dies ist ein privater Plan**.
2. Wählen Sie den Link **Zielgruppe exportieren (CSV)** aus.
3. Öffnen Sie die CSV-Datei, und fügen Sie die Azure-IDs, denen Sie Zugriff auf das private Angebot gewähren möchten, in der Spalte **ID** hinzu.
4. (Optional) Geben Sie in der Spalte **Description** eine Beschreibung für jede Zielgruppe ein.
5. Fügen Sie in jeder Zeile mit einer Azure-ID „TenantID“ in der Spalte **Type** hinzu.
6. Speichern Sie die CSV-Datei.
7. Wählen Sie auf der Registerkarte **Preise und Verfügbarkeit** unter **Plansichtbarkeit** den Link **Zielgruppe importieren (CSV)** aus.
8. Wählen Sie im angezeigten Dialogfeld **Ja** aus.
9. Wählen Sie die CSV-Datei und dann **Öffnen** aus.
10. Wählen Sie **Entwurf speichern** aus, und wählen Sie dann in der oberen linken Ecke der Registerkarte **Planübersicht** aus, um zur Registerkarte **Planübersicht** zurückzukehren.
11. Um einen weiteren Plan für dieses Angebot zu erstellen, wählen Sie im oberen Bereich der Registerkarte **Planübersicht** die Option **+ Neuen Plan erstellen** aus. Wiederholen Sie dann die Schritte im Abschnitt [Erstellen von Plänen](#create-a-plan). Wenn Sie das Erstellen von Plänen abgeschlossen haben, fahren Sie mit dem nächsten Abschnitt **Anzeigen der Pläne** fort.

## <a name="view-your-plans"></a>Anzeigen der Pläne

Nachdem Sie mindestens einen Plan erstellt haben, werden auf der Registerkarte **Planübersicht** der Planname, die Plan-ID, das Preismodell, die Verfügbarkeit (öffentlich oder privat), der aktuelle Veröffentlichungsstatus und alle verfügbaren Aktionen angezeigt.

Die Aktionen, die in der Spalte **Aktion** der Registerkarte **Planübersicht** verfügbar sind, hängen vom Status Ihres Plans ab und können Folgendes umfassen:

- Wenn der Planstatus **Entwurf** lautet, wird in der Spalte **Aktion** die Option **Entwurf löschen** angezeigt.
- Wenn der Planstatus **Live** lautet, wird in der Spalte **Aktion** der Link **Stop sell plan** (Verkauf des Plans einstellen) oder der Link **Sync private audience** (Private Zielgruppe synchronisieren) angezeigt. Über den Link **Sync private audience** (Private Zielgruppe synchronisieren) werden nur die Änderungen an Ihren privaten Zielgruppen veröffentlicht, ohne dass weitere Aktualisierungen veröffentlicht werden, die Sie möglicherweise am Angebot vorgenommen haben.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie die Programme **Co-Selling mit Microsoft** und **Verkaufen über CSPs** zum [Verkaufen Ihres SaaS-Angebots](create-new-saas-offer-marketing.md) nutzen.
- [Testen und Veröffentlichen eines SaaS-Angebots im kommerziellen Marketplace](test-publish-saas-offer.md)
