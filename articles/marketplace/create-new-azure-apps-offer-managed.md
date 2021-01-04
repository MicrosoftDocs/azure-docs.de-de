---
title: Konfigurieren eines Plans für verwaltete Anwendungen
description: Erfahren Sie, wie Sie einen Plan für eine verwaltete Anwendung für Ihr Azure-Anwendungsangebot in Partner Center konfigurieren.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a67a721b8d94112347282e1cd9be4d43f4ac85da
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744634"
---
# <a name="configure-a-managed-application-plan"></a>Konfigurieren eines Plans für verwaltete Anwendungen

Dieser Artikel bezieht sich nur auf Pläne für verwaltete Anwendungen in Azure-Anwendungsangeboten. Wenn Sie einen Plan für eine Lösungsvorlage konfigurieren, lesen Sie [Konfigurieren eines Plans für eine Lösungsvorlage](create-new-azure-apps-offer-solution.md).

## <a name="define-markets-pricing-and-availability"></a>Definieren von Märkten, Preisen und Verfügbarkeit

Jeder Plan muss in mindestens einem Markt verfügbar sein. Auf der Registerkarte **Preise und Verfügbarkeit** können Sie die Märkte, in denen dieser Plan verfügbar sein soll, sowie den Preis konfigurieren und festlegen, ob der Plan für alle Benutzer oder nur für bestimmte Kunden (als privater Plan bezeichnet) sichtbar sein soll.

1. Wählen Sie unter **Märkte** den Link **Märkte bearbeiten** aus.
1. Wählen Sie im angezeigten Dialogfeld die Marktregionen aus, in denen Sie Ihren Plan anbieten möchten. Sie müssen mindestens einen von höchstens 141 Märkten auswählen.

    > [!NOTE]
    > Dieses Dialogfeld enthält ein Suchfeld und eine Option zum Herausfiltern von Ländern der Kategorie „Bezahlte Steuern“, in denen Microsoft die Mehrwert- und Verbrauchssteuer für Sie abführt.

1. Wählen Sie **Speichern** aus, um das Dialogfeld zu schließen.

## <a name="define-pricing"></a>Festlegen von Preisen

Geben Sie im Feld **Preis** den Preis pro Monat für diesen Plan an. Dieser Preis gilt zusätzlich zu allen Azure-Infrastruktur- oder verbrauchsbasierten Kosten, die durch die von dieser Lösung bereitgestellten Ressourcen entstehen.

Zusätzlich zu den monatlichen Preisen können Sie die Preise für die Nutzung von nicht standardmäßigen Einheiten mithilfe der [getakteten Abrechnung](partner-center-portal/azure-app-metered-billing.md) festlegen. Wenn Sie möchten, können Sie den monatlichen Preis auf null (0) festlegen und ausschließlich die getaktete Abrechnung verwenden.

Preise in USD (USD = US-Dollar) werden beim Speichern gemäß dem aktuellen Wechselkurs in die lokale Währung aller ausgewählten Märkte konvertiert. Überprüfen Sie vor der Veröffentlichung die Preise in jedem Markt, indem Sie die Preistabellenkalkulation exportieren. Wenn Sie benutzerdefinierte Preise in einem einzelnen Markt festlegen möchten, ändern und importieren Sie die Preistabellenkalkulation.

### <a name="add-a-custom-meter-dimension-optional"></a>Hinzufügen einer benutzerdefinierten Dimension für die Verbrauchseinheit (optional)

1. Wählen Sie unter **Dimensionen des Marketplace-Messungsdiensts** den Link **Benutzerdefinierte Dimension für Verbrauchseinheit hinzufügen (max. 18)** aus.
1. Geben Sie im Feld **ID** die unveränderliche Kennungsreferenz für die Ausgabe von Nutzungsereignissen ein.
1. Geben Sie im Feld **Anzeigename** den Anzeigenamen ein, der der Dimension zugeordnet ist. Beispiel: „Gesendete Textnachrichten“.
1. Geben Sie im Feld **Maßeinheit** die Beschreibung der Abrechnungseinheit ein. Beispiel: „pro Textnachricht“ oder „pro 100 E-Mails“.
1. Geben Sie im Feld **Preis pro Einheit in USD** den Preis für eine Einheit der Dimension ein.
1. Geben Sie im Feld **Grundsätzlich enthaltene monatliche Menge** die Menge der Dimension (als ganze Zahl) ein, die für Kunden, die die wiederkehrende monatliche Gebühr zahlen, jeden Monat inklusive ist. Aktivieren Sie stattdessen das Kontrollkästchen, wenn Sie eine unbegrenzte Anzahl festlegen möchten.
1. Wiederholen Sie die Schritte 1 bis 7, um eine weitere benutzerdefinierte Dimension für Verbrauchseinheiten hinzuzufügen.

### <a name="set-custom-prices-optional"></a>Festlegen benutzerdefinierter Preise (optional)

Preise in USD (USD = US-Dollar) werden beim Speichern gemäß dem aktuellen Wechselkurs in die lokale Währung aller ausgewählten Märkte konvertiert. Überprüfen Sie vor der Veröffentlichung die Preise in jedem Markt, indem Sie die Preistabellenkalkulation exportieren. Wenn Sie benutzerdefinierte Preise in einem einzelnen Markt festlegen möchten, ändern und importieren Sie die Preistabellenkalkulation.

Überprüfen Sie Ihre Preise vor der Veröffentlichung sorgfältig, da nicht alle Elemente eines Plans nach seiner Veröffentlichung geändert werden können.

> [!NOTE]
> Nachdem ein Preis für einen Markt in Ihrem Plan veröffentlicht wurde, kann er nicht mehr geändert werden.

Wenn Sie benutzerdefinierte Preise für einen einzelnen Markt festlegen möchten, exportieren, ändern und importieren Sie die Preistabelle. Sie sind für die Überprüfung des Preismodells und diese Einstellungen verantwortlich. Ausführliche Informationen finden Sie unter [Benutzerdefinierte Preise](plans-pricing.md#custom-prices).

1. Sie müssen Ihre Preisänderungen speichern, damit die Preisdaten exportiert werden können. Wählen Sie im unteren Bereich der Registerkarte **Preise und Verfügbarkeit** die Option **Entwurf speichern** aus.
1. Wählen Sie unter **Preise** den Link **Preisdaten exportieren** aus.
1. Öffnen Sie die Datei „exportedPrice.xlsx“ in Microsoft Excel.
1. Nehmen Sie auf dem Arbeitsblatt die gewünschten Änderungen an den Preisinformationen vor, und speichern Sie die Datei.

   Möglicherweise müssen Sie die Bearbeitung in Excel aktivieren, damit Sie die Datei aktualisieren können.

1. Wählen Sie auf der Registerkarte **Preise und Verfügbarkeit** unter **Preise** den Link **Preisdaten importieren** aus.
1. Klicken Sie im angezeigten Dialogfeld auf **Ja**.
1. Wählen Sie die aktualisierte Datei „exportedPrice.xlsx“ aus, und klicken Sie dann auf **Öffnen**.

## <a name="choose-who-can-see-your-plan"></a>Auswählen der Sichtbarkeit des Plans

Sie können festlegen, dass jeder Plan für alle Benutzer oder nur für eine bestimmte Zielgruppe angezeigt werden soll. Der Zugriff für eine private Zielgruppe wird über Azure-Abonnement-IDs zugewiesen. Dabei kann eine Beschreibung für jede zugewiesene Abonnement-ID angegeben werden. Sie können maximal 10 Abonnement-IDs manuell eingeben oder über eine CSV-Datei bis zu 10.000 Abonnement-IDs angeben. Azure-Abonnement-IDs werden als GUIDs dargestellt. Buchstaben müssen in Kleinbuchstaben angegeben werden.

> [!NOTE]
> Wenn Sie einen privaten Plan veröffentlichen, können Sie die Sichtbarkeit später in „Öffentlich“ ändern. Wenn Sie hingegen einen öffentlichen Plan veröffentlichen, können Sie dessen Sichtbarkeit nicht in „Privat“ ändern.

Führen Sie unter **Sichtbarkeit des Plans** einen der folgenden Schritte aus:

- Um den Plan öffentlich zu machen, wählen Sie das Optionsfeld **Öffentlich** aus.
- Um den Plan als privat zu kennzeichnen, wählen Sie das Optionsfeld **Privat** aus, und fügen Sie die Azure-Abonnement-IDs manuell oder über eine CSV-Datei hinzu.

> [!NOTE]
> Eine private oder eingeschränkte Zielgruppe unterscheidet sich von der auf der Registerkarte **Vorschau** definierten Vorschauzielgruppe. Eine Vorschauzielgruppe kann auf das Angebot zugreifen, bevor es in den Marketplaces live veröffentlicht wird. Die Auswahl der privaten Zielgruppe gilt nur für einen bestimmten Plan, aber die private Zielgruppe kann alle Pläne (private und nicht private Pläne) zu Validierungszwecken anzeigen.

### <a name="manually-add-azure-subscription-ids-for-a-private-plan"></a>Manuelles Hinzufügen von Azure-Abonnement-IDs zu einem privaten Plan

1. Wählen Sie unter **Sichtbarkeit des Plans** das Optionsfeld **Privat** aus.
1. Geben Sie im eingeblendeten Feld **Azure-Abonnement-ID** die Azure-Abonnement-ID der Zielgruppe ein, der Sie Zugriff auf diesen privaten Plan gewähren möchten. Es muss mindestens eine Abonnement-ID angegeben werden.
1. (Optional) Geben Sie im Feld **Beschreibung** eine Beschreibung der Zielgruppe ein.
1. Wählen Sie zum Hinzufügen einer weiteren Abonnement-ID den Link **ID hinzufügen (max. 10)** aus, und wiederholen Sie die Schritte 2 und 3.

### <a name="use-a-csv-file-to-add-azure-subscription-ids-for-a-private-plan"></a>Hinzufügen von Azure-Abonnement-IDs zu einem privaten Plan mithilfe einer CSV-Datei

1. Wählen Sie unter **Sichtbarkeit des Plans** das Optionsfeld **Privat** aus.
1. Wählen Sie den Link **Zielgruppe exportieren (CSV)** aus.
1. Öffnen Sie die CSV-Datei, und fügen Sie die Azure-Abonnement-IDs, denen Sie Zugriff auf das private Angebot gewähren möchten, in der Spalte **ID** hinzu.
1. (Optional) Geben Sie in der Spalte **Description** eine Beschreibung für jede Zielgruppe ein.
1. Fügen Sie in der Spalte **Type** (Typ) für jede Zeile mit einer Abonnement-ID die Zeichenfolge „SubscriptionId“ hinzu.
1. Speichern Sie die CSV-Datei.
1. Wählen Sie auf der Registerkarte **Verfügbarkeit** unter **Sichtbarkeit des Plans** den Link **Zielgruppe importieren (CSV)** aus.
1. Wählen Sie im angezeigten Dialogfeld **Ja** aus.
1. Wählen Sie die CSV-Datei und dann **Öffnen** aus. In einer Meldung wird angezeigt, dass die CSV-Datei erfolgreich importiert wurde.

## <a name="define-the-technical-configuration"></a>Definieren der technischen Konfiguration

Auf der Registerkarte **Technische Konfiguration** können Sie das Bereitstellungspaket hochladen, mit dem Kunden Ihren Plan bereitstellen können, und eine Versionsnummer für das Paket angeben. Außerdem stellen Sie hier weitere technische Informationen bereit.

> [!NOTE]
> Diese Registerkarte wird nicht angezeigt, wenn Sie auf der Registerkarte **Plansetup** festgelegt haben, dass Pakete aus einem anderen Plan wiederverwendet werden. Fahren Sie in diesem Fall mit [Anzeigen der Pläne](#view-your-plans) fort.

### <a name="assign-a-version-number-for-the-package"></a>Zuweisen einer Versionsnummer für das Paket

Geben Sie im Feld **Version** die aktuelle Version der technischen Konfiguration an. Erhöhen Sie diese Version jedes Mal, wenn Sie eine Änderung auf dieser Seite veröffentlichen. Die Versionsnummer muss folgendes Format aufweisen: „Integer.Integer.Integer“. Beispiel: `1.0.2`.

### <a name="upload-a-package-file"></a>Hochladen einer Paketdatei

Ziehen Sie unter **Paketdatei (ZIP)** die Paketdatei in das graue Feld, oder wählen Sie den Link zum **Suchen Ihrer Dateien** aus.

> [!NOTE]
> Wenn beim Hochladen von Dateien ein Problem auftritt, vergewissern Sie sich, dass das lokale Netzwerk den von Partner Center verwendeten Dienst `https://upload.xboxlive.com` nicht blockiert.

#### <a name="previously-published-packages"></a>Zuvor veröffentlichte Pakete

Auf der Unterregisterkarte **Zuvor veröffentlichte Pakete** können Sie alle veröffentlichten Versionen der technischen Konfiguration anzeigen.

### <a name="enable-just-in-time-jit-access-optional"></a>Aktivieren des Just-In-Time-Zugriffs (JIT, optional)

Aktivieren Sie das Kontrollkästchen **Just-in-Time-Zugriff (JIT) aktivieren**, um den Just-In-Time-Zugriff für diesen Plan zu aktivieren. Lassen Sie diese Option deaktiviert, um festzulegen, dass Ihnen von den Consumern Ihrer verwalteten Anwendung dauerhaft Zugriff gewährt werden muss. Weitere Informationen zu dieser Option finden Sie unter [Just-In-Time-Zugriff (JIT)](plan-azure-app-managed-app.md#just-in-time-jit-access).

### <a name="select-a-deployment-mode"></a>Auswählen eines Bereitstellungsmodus

Wählen Sie als Bereitstellungsmodus entweder **Vollständig** oder **Inkrementell** aus.

- Im Modus **Vollständig** führt eine erneute Bereitstellung der Anwendung durch den Kunden zur Entfernung von Ressourcen aus der verwalteten Ressourcengruppe, wenn die Ressourcen nicht in der Datei [mainTemplate.json](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template) definiert sind.
- Im Modus **Inkrementell** bleiben bestehende Ressourcen bei einer erneuten Bereitstellung der Anwendung unverändert.

Weitere Informationen zu den Bereitstellungsmodi finden Sie unter [Azure Resource Manager-Bereitstellungsmodi](../azure-resource-manager/templates/deployment-modes.md).

### <a name="provide-a-notification-endpoint-url"></a>Angeben einer URL für den Benachrichtigungsendpunkt

Geben Sie im Feld **Benachrichtigungsendpunkt-URL** einen HTTPS-Webhookendpunkt an, um Benachrichtigungen zu allen CRUD-Vorgängen für Instanzen verwalteter Anwendungen dieser Planversion zu erhalten.

### <a name="customize-allowed-customer-actions-optional"></a>Anpassen der zulässigen Kundenaktionen (optional)

1. Wählen Sie das Feld **Zulässige Kundenaktionen anpassen** aus, um festzulegen, welche Aktionen Kunden neben den standardmäßig verfügbaren `*/read`-Aktionen für verwaltete Ressourcen ausführen können.
1. Geben Sie in den angezeigten Feldern zusätzliche Steuerungsaktionen und zulässige Datenaktionen, die Sie für Ihre Kunden aktivieren möchten, getrennt durch Semikolons an. Wenn Sie Kunden beispielsweise das Neustarten von VMs erlauben möchten, fügen Sie im Feld **Zulässige Steuerungsaktionen** `Microsoft.Compute/virtualMachines/restart/action` hinzu.

### <a name="choose-who-can-manage-the-application"></a>Festlegen des Verwaltungszugriffs auf die Anwendung

Geben Sie an, wer in den ausgewählten Azure-Regionen über Verwaltungszugriff auf diese verwaltete Anwendung verfügen soll: _Globales Azure_ oder _Azure Government Cloud_. Benutzer, Gruppen oder Anwendungen, denen Berechtigungen für die verwaltete Ressourcengruppe erteilt werden sollen, werden mithilfe von Azure AD-Identitäten angegeben. Weitere Informationen finden Sie unter [Planen eines Azure-Anwendungsangebots für den kommerziellen Marketplace](plan-azure-application-offer.md).

Führen Sie die folgenden Schritte für „Globales Azure“ bzw. „Azure Government Cloud“ aus.

1. Geben Sie im Feld **Azure Active Directory-Mandanten-ID** die Azure AD-Mandanten-ID (auch als Verzeichnis-ID bezeichnet) ein, die die Identitäten der Benutzer, Gruppen oder Anwendungen enthält, denen Sie Berechtigungen erteilen möchten.
1. Geben Sie im Feld **Prinzipal-ID** die Azure AD-Objekt-ID des Benutzers, der Gruppe oder der Anwendung an, dem bzw. der die Berechtigung für die verwaltete Ressourcengruppe erteilt werden soll. Sie können den Benutzer anhand seiner Prinzipal-ID auf dem Blatt [Azure Active Directory Benutzer](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) im Azure-Portal identifizieren.
1. Wählen Sie in der Liste **Rollendefinition** eine in Azure AD integrierte Rolle aus. Die ausgewählte Rolle beschreibt die Berechtigungen, die der Prinzipal für die Ressourcen im Kundenabonnement erhält.
1. Wählen Sie zum Hinzufügen einer weiteren Autorisierung den Link **Autorisierung hinzufügen (max. 100)** aus, und wiederholen Sie die Schritte 1 bis 3.

### <a name="policy-settings-optional"></a>Richtlinieneinstellungen (optional)

Sie können maximal fünf Richtlinien und nur eine Instanz jeder einzelnen Richtlinienoption konfigurieren. Einige Richtlinien erfordern zusätzliche Parameter.

1. Wählen Sie unter **Richtlinieneinstellungen** den Link **+ Richtlinie hinzufügen (max. 5)** aus.
1. Geben Sie im Feld **Name** den Namen der Richtlinienzuweisung ein (max. 50 Zeichen).
1. Wählen Sie im Listenfeld **Richtlinien** die Azure-Richtlinie aus, die auf von der verwalteten Anwendung im Kundenabonnement erstellte Ressourcen angewandt wird.
1. Geben Sie im Feld **Richtlinienparameter** den Parameter an, auf den die Richtlinien für Überwachungs- und Diagnoseeinstellungen angewandt werden sollen.
1. Wählen Sie im Listenfeld **Richtlinien-SKU** den Typ der Richtlinien-SKU aus.

    > [!NOTE]
    > Für Überwachungsrichtlinien muss die Richtlinien-SKU _Standard_ ausgewählt werden.

## <a name="view-your-plans"></a>Anzeigen der Pläne

- Wählen Sie **Entwurf speichern** und dann in der linken oberen Ecke der Seite **Planübersicht** aus, um zur Seite **Planübersicht** zurückzukehren.

Nachdem Sie mindestens einen Plan erstellt haben, werden auf der Registerkarte **Planübersicht** der Planname, die Plan-ID, der Plantyp, die Verfügbarkeit (öffentlich oder privat), der aktuelle Veröffentlichungsstatus und alle verfügbaren Aktionen angezeigt.

Die Aktionen, die in der Spalte **Aktion** der Registerkarte **Planübersicht** verfügbar sind, hängen vom Status Ihres Plans ab und können Folgendes umfassen:

- Wenn der Planstatus **Entwurf** lautet, wird in der Spalte **Aktion** die Option **Entwurf löschen** angezeigt.
- Wenn der Planstatus **Live** lautet, wird in der Spalte **Aktion** entweder **Verkauf des Plans einstellen** oder **Private Zielgruppe synchronisieren** angezeigt. Über den Link **Sync private audience** (Private Zielgruppe synchronisieren) werden nur die Änderungen an Ihren privaten Zielgruppen veröffentlicht, ohne dass weitere Aktualisierungen veröffentlicht werden, die Sie möglicherweise am Angebot vorgenommen haben.
- Um einen weiteren Plan für dieses Angebot zu erstellen, wählen Sie im oberen Bereich der Registerkarte **Planübersicht** die Option **+ Neuen Plan erstellen** aus. Wiederholen Sie dann die Schritte unter [Erstellen von Plänen für Ihr Azure-Anwendungsangebot](create-new-azure-apps-offer-plans.md). Wenn Sie das Erstellen von Plänen abgeschlossen haben, fahren Sie mit dem nächsten Abschnitt „Nächste Schritte“ fort.

## <a name="next-steps"></a>Nächste Schritte

- [Testen und Veröffentlichen eines Azure-Anwendungsangebots](create-new-azure-apps-offer-test-publish.md)
- Erfahren Sie, wie Sie die Programme „Co-Selling mit Microsoft“ und „Verkaufen über CSPs“ für das [Vermarkten Ihres Azure-Anwendungsangebots](create-new-azure-apps-offer-marketing.md) nutzen.