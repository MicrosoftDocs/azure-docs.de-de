---
title: Verwalten eines Kontos im kommerziellen Marketplace in Microsoft Partner Center
description: Erfahren Sie, wie Sie ein Konto im kommerziellen Marketplace in Microsoft Partner Center verwalten.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: parthpandyaMSFT
ms.author: parthp
ms.date: 11/02/2020
ms.openlocfilehash: cc853285e193dd3ac4e348525b8bf0db9f8cb023
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533943"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Verwalten Ihres Kontos im kommerziellen Marketplace in Partner Center

Nachdem Sie [ein Partner Center-Konto erstellt](./create-account.md) haben, können Sie das [Dashboard des kommerziellen Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) verwenden, um Ihr Konto und Ihre Angebote zu verwalten.

In diesem Artikel wird erläutert, wie Sie Ihr Partner Center-Konto verwalten. Dabei wird Folgendes beschrieben:

- [Zugreifen auf Ihre Partner Center-Kontoeinstellungen](#access-your-account-settings)
- [Suchen Ihrer Herausgeber-ID, Symantec-ID, Verkäufer-ID, Benutzer-ID, MPN-ID und Azure AD-Mandanten](#account-details)
- [Aktualisieren von Kontaktinformationen](#contact-info)
- [Einrichten der Nachverfolgung von GUIDs für die Überwachung der Kundennutzung](#tracking-guids)
- [Verwalten von Benutzern](#manage-users)
- [Verwalten von Gruppen](#manage-groups)
- [Verwalten von Azure AD-Anwendungen](#manage-azure-ad-applications)
- [Definieren von Benutzerrollen und Berechtigungen](#define-user-roles-and-permissions)
- [Verwalten von Azure AD-Mandanten (Geschäftskonten)](#manage-tenants)
- [Verwalten von Partner Center-Vereinbarungen](#agreements)

## <a name="access-your-account-settings"></a>Zugreifen auf Ihre Kontoeinstellungen

Wenn nicht bereits geschehen, sollten Sie (oder der Administrator Ihrer Organisation) zu folgenden Zwecken auf die [Kontoeinstellungen](https://partner.microsoft.com/dashboard/account/management) Ihres Partner Center-Kontos zugreifen:

- Überprüfen des Verifizierungsstatus Ihres Unternehmenskontos
- Bestätigen Ihrer Symantec-ID, Verkäufer-ID, Microsoft Partner Network-ID (MPN-ID), Herausgeber-ID und Kontaktinformationen, einschließlich der Kontaktinformationen der genehmigenden Person im Unternehmen und des Verkäufers
- Erstellen von Benutzerkonten für alle Benutzer, die Ihr Geschäftskonto in Partner Center verwenden

### <a name="open-account-settings"></a>Öffnen von „Kontoeinstellungen“

Wählen Sie in Ihrem [Dashboard „Kommerzieller Marketplace“](https://partner.microsoft.com/dashboard/commercial-marketplace) in Partner Center oben rechts **Einstellungen** (Zahnradsymbol) und dann die Option **Kontoeinstellungen** aus.

![Menü „Kontoeinstellungen“ in Partner Center](./media/dashboard-developer-settings.png)

In **Kontoeinstellungen** können Sie Folgendes anzeigen:

- **Kontodetails**: Kontotyp und Kontostatus
- **Herausgeber-IDs**: Verkäufer-ID, Benutzer-ID, Herausgeber-ID, Azure AD-Mandanten usw.
- **Kontaktinformationen**: Anzeigename des Herausgebers, Kontaktname des Verkäufers, E-Mail-Adresse, Telefonnummer und Adresse
- **Nachverfolgungs-GUIDs**: Alle Ihrem Konto zugeordneten Nachverfolgung-GUIDs

### <a name="account-details"></a>Kontodetails

Im Abschnitt „Kontodetails“ werden grundlegende Informationen angezeigt, zum Beispiel Ihr **Kontotyp** (Unternehmen oder Person) und der **Überprüfungsstatus** Ihres Kontos. Während der Überprüfung Ihres Kontos wird in diesen Einstellungen jeder erforderliche Schritt angezeigt, darunter die E-Mail-Überprüfung, die Beschäftigungsüberprüfung und die Unternehmensüberprüfung. Sie können hier auch Ihre E-Mail-Adresse aktualisieren und die Überprüfung bei Bedarf erneut senden.

### <a name="publisher-ids"></a>Herausgeber-IDs:

Im Abschnitt „Herausgeber-IDs“ werden Ihre **Symantec-ID**, **Verkäufer-ID**, **Benutzer-ID**, **MPN-ID** und **Azure AD-Mandanten** angezeigt. Diese Werte werden von Microsoft zur eindeutigen Identifizierung Ihres Entwicklerkontos zugewiesen und können nicht bearbeitet werden.

### <a name="contact-info"></a>Kontaktinformationen

Im Abschnitt „Kontaktinformation“ werden Ihr **Anzeigename des Herausgebers**, die **Kontaktinfos des Verkäufers** (Kontaktname, E-Mail-Adresse, Telefonnummer und Adresse des Verkäufers des Unternehmens) und der **Genehmiger des Unternehmens** (Name, E-Mail-Adresse und Telefonnummer der Person mit der Berechtigung, Entscheidungen im Namen des Unternehmens zu genehmigen) angezeigt.

#### <a name="payout-account"></a>Auszahlungskonto

Ein Auszahlungskonto ist das Bankkonto, auf das Ihre Verkaufserlöse übertragen werden. Dieses Bankkonto muss sich in demselben Land/derselben Region befinden, wo Sie Ihr Partner Center-Konto registriert haben.

So richten Sie Ihr Auszahlungskonto ein

1. Navigieren Sie in Partner Center zur [Übersichtsseite des kommerziellen Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview).
2. Wählen Sie im Abschnitt „Profil“ neben **Auszahlungsprofil** die Option **Aktualisieren** aus.
3. **Zahlungsmethode auswählen**: Bankkonto oder PayPal.
4. **Zahlungsinformationen hinzufügen**: Unter anderem müssen Sie hier möglicherweise einen Kontotyp auswählen (Giro- oder Sparkonto) und den Namen des Kontoinhabers, die Kontonummer, Bankleitzahl, Rechnungsadresse, Telefonnummer oder die PayPal-E-Mail-Adresse eingeben. \* Weitere Informationen über die Verwendung von PayPal als Zahlungsmethode für Ihr Konto und darüber, ob diese in Ihrer Marktregion unterstützt wird, finden Sie unter [PayPal-Informationen](/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> Wenn Sie Ihr Zahlungskonto ändern, kann dies dazu führen, dass sich Ihre Zahlungen um maximal einen Zahlungszyklus verzögern. Diese Verzögerung tritt auf, weil wir die Kontoänderung überprüfen müssen. Dies ist der gleiche Vorgang wie bei der ersten Einrichtung des Auszahlungskontos. Nachdem das Konto bestätigt wurde, erhalten Sie trotzdem den vollen Betrag. Zahlungen, die für den aktuellen Zahlungszyklus fällig sind, werden im nächsten Zyklus hinzugefügt.  

#### <a name="tax-profile"></a>Steuerprofil

Überprüfen Sie Ihren aktuellen Steuerprofilstatus, und stellen Sie sicher, dass der angezeigte **Entitätstyp** und die **Steuerzertifikatinformationen** korrekt sind. Wählen Sie **Bearbeiten**, um alle erforderlichen Formulare zu aktualisieren oder auszufüllen.

Um Ihren Steuerstatus festzulegen, müssen Sie das Land/die Region Ihres Wohnsitzes und Ihre Staatsangehörigkeit angeben und die entsprechenden Steuerformulare für Ihr Land/Ihre Region ausfüllen.

Unabhängig von dem Land/der Region, wo sich Ihr Wohnsitz befindet, und Ihrer Staatsangehörigkeit müssen Sie die US-Steuerformulare ausfüllen, um Angebote über Microsoft zu verkaufen. Partner, die bestimmte Anforderungen im Zusammenhang mit der US-amerikanischen Staatsbürgerschaft erfüllen, müssen ein IRS W-9-Formular ausfüllen. Andere Partner außerhalb der USA müssen ein IRS W-8 Formular ausfüllen. Sie können diese Formulare beim Erstellen Ihres Steuerprofils online ausfüllen.

Es ist keine US-Steueridentifikationsnummer (ITIN) erforderlich, um Zahlungen von Microsoft zu erhalten oder um Begünstigungen von Steuerabkommen in Anspruch zu nehmen.

Sie können Ihre Steuerformulare elektronisch in Partner Center ausfüllen und übermitteln. In den meisten Fällen müssen Sie keine Formulare drucken und per E-Mail senden.

In verschiedenen Ländern und Regionen gelten unterschiedliche Steueranforderungen. Wie viele Steuern Sie genau abführen müssen, hängt von den Ländern und Regionen ab, in denen Sie Ihre Angebote verkaufen. Microsoft zahlt in einigen Ländern/Regionen Mehrwertsteuer und Verbrauchssteuer für Sie. Diese Länder/Regionen werden beim Auflisten Ihres Angebots angegeben. Abhängig davon, wo Sie registriert sind, müssen Sie in anderen Ländern/Regionen möglicherweise die Mehrwert- und Verbrauchssteuer für Ihre Verkäufe direkt an die örtliche Steuerbehörde abführen. Darüber hinaus können Ihre erhaltenen Verkaufserlöse unter Umständen als Einkommen steuerbar sein. Es wird dringend empfohlen, sich an die entsprechende Behörde für Ihr Land bzw. Ihre Region zu wenden. Diese kann Ihnen am besten dabei helfen, die richtige Besteuerung für Ihre Microsoft-Verkaufstransaktionen zu bestimmen.

##### <a name="withholding-rates"></a>Quellensteuer

Die Informationen, die Sie in den Steuerformularen übermitteln, bestimmen die entsprechende Quellensteuer. Der Quellensteuer betrifft nur Verkäufe in die USA. Verkäufe an Orte außerhalb der USA unterliegen nicht der Quellensteuer. Die Quellensteuer variiert, aber bei den meisten Entwicklern, die außerhalb der USA registriert sind, liegt der Standardwert bei 30 %. Sie haben die Möglichkeit, diesen Wert zu reduzieren, wenn Ihr Land/Ihre Region ein Einkommenssteuerabkommen mit den USA geschlossen hat.

##### <a name="tax-treaty-benefits"></a>Begünstigungen durch Steuerabkommen

Wenn Sie sich außerhalb der USA befinden, können Sie möglicherweise von Begünstigungen durch Steuerabkommen profitieren. Diese Begünstigungen unterschieden sich je nach Land/Region und ermöglichen Ihnen unter Umständen, den Steuerbetrag zu reduzieren, den Microsoft einbehält. Begünstigungen durch Steuerabkommen können Sie in Anspruch nehmen, indem Sie Teil II des Formulars W-8BEN ausfüllen. Es wird empfohlen, mit den entsprechenden Ressourcen in Ihrem Land oder in Ihrer Region zu kommunizieren, um zu ermitteln, ob diese Begünstigungen für Sie infrage kommen.

[Erfahren Sie mehr über Steuerdetails für Entwickler von Windows-Apps/-Spielen und Azure Marketplace-Herausgeber](/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Aufbewahrungsstatus für Auszahlungen

Standardmäßig sendet Microsoft Zahlungen auf monatlicher Basis. Sie können Ihre Auszahlungen jedoch zurückhalten, sodass keine Zahlungen an Ihr Konto gesendet werden. Wenn Sie Ihre Auszahlungen zurückhalten möchten, werden wir weiterhin alle Ihre Umsätze erfassen und die Details in Ihrer **Auszahlungszusammenfassung** aufführen. Allerdings werden wir keine Zahlungen auf Ihr Konto überweisen, bis Sie die Haltesperre aufheben.

Um Ihre Zahlungen zurückzuhalten, wechseln Sie zu **Kontoeinstellungen**. Stellen Sie unter **Finanzielle Details** im Abschnitt **Aufbewahrungsstatus für Auszahlungen** den Schieberegler auf **Ein**. Sie können den Aufbewahrungsstatus für Auszahlungen jederzeit ändern, aber beachten Sie, dass sich Ihre Entscheidung auf die nächste monatliche Auszahlung auswirkt. Wenn Sie zum Beispiel die Auszahlung für April zurückhalten möchten, legen Sie den Aufbewahrungsstatus für Auszahlungen vor Ende März auf **Ein** fest.

Sobald Sie den Aufbewahrungsstatus für Auszahlungen auf **Ein** festgelegt haben, werden alle Auszahlungen zurückgehalten, bis Sie den Schieberegler wieder auf **Aus** festlegen. In diesem Fall werden Sie in den nächsten monatlichen Auszahlungszyklus eingeschlossen (vorausgesetzt, alle geltenden Zahlungsschwellenwerte sind erfüllt). Wenn Sie Ihre Auszahlungen zum Beispiel zurückgehalten haben, aber eine Auszahlung im Juni veranlassen möchten, legen Sie den Aufbewahrungsstatus für Auszahlungen vor Ende Mai auf **Aus** fest.

> [!NOTE]
> Ihre Auswahl für den **Aufbewahrungsstatus für Auszahlungen** gilt für **alle** Umsatzquellen, die über das Microsoft Partner Center bezahlt werden, einschließlich von Azure Marketplace, AppSource, Microsoft Store, Werbung usw.). Es ist nicht möglich, für jede Umsatzquelle einen anderen Aufbewahrungsstatus für Auszahlungen festzulegen.

### <a name="devices"></a>Geräte

Der Einstellungen für die Geräteverwaltung gelten nur für UWP-Veröffentlichungen. [Weitere Informationen](/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info)

### <a name="tracking-guids"></a>Nachverfolgungs-GUIDs

GUIDs (Globally Unique Identifiers) sind eindeutige Referenznummern (mit 32 Hexadezimalstellen), die für die Nachverfolgung Ihrer Azure-Nutzung verwendet werden können. 

Zum Erstellen einer GUID für die Nachverfolgung sollten Sie einen GUID-Generator verwenden. Das Azure Storage-Team hat ein [Formular für den GUID-Generator](https://aka.ms/StoragePartners) erstellt, über das Sie per E-Mail eine GUID im richtigen Format erhalten. Dieses Formular kann für verschiedene Nachverfolgungssysteme wiederverwendet werden.

Es empfiehlt sich, für jedes Produkt eine eindeutige GUID für jedes Angebot und jeden Distributionskanal zu erstellen. Sie können festlegen, dass eine einzige GUID für die vielfältigen Verteilungskanäle des Produkts verwendet wird, wenn die Berichterstellung nicht aufgeteilt werden soll.

Wenn Sie ein Produkt mithilfe einer Vorlage bereitstellen und das Produkt sowohl im Azure Marketplace als auch auf GitHub verfügbar ist, können Sie zwei unterschiedliche GUIDs erstellen und registrieren:

- Produkt A im Azure Marketplace
- Produkt A auf GitHub

Die Berichterstellung erfolgt über den Partnerwert (Microsoft-Partner-ID) und die GUIDs. Sie können GUIDs auch auf einer detaillierteren Ebene nachverfolgen und an jedem Plan innerhalb Ihres Angebots ausrichten.

Weitere Informationen finden Sie unter [Häufig gestellte Fragen zur Nachverfolgung der Azure-Kundennutzung mit GUIDs](../azure-partner-customer-usage-attribution.md#faq).

## <a name="create-a-billing-profile"></a>Erstellen eines Abrechnungsprofils

Wenn Sie ein Angebot für [Dynamics 365 for Customer Engagement und Power Apps](./create-new-customer-engagement-offer.md) oder [Dynamics 365 for Operations](./create-new-operations-offer.md) veröffentlichen, müssen Sie ein **Abrechnungsprofil** ausfüllen.

Die Abrechnungsadresse ist von Ihrer juristischen Entität bereits ausgefüllt. Sie können diese Adresse später aktualisieren. Die Felder „Steuer“ und „Umsatzsteuer-ID“ sind optional.  Der Name des Landes/der Region und der Firmenname können nicht bearbeitet werden.

## <a name="multi-user-account-management"></a>Kontoverwaltung für mehrere Benutzer

Partner Center nutzt [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) für den Zugriff auf Mehrbenutzerkonten und deren Verwaltung. Das Azure AD Ihrer Organisation wird im Rahmen des Registrierungsprozesses automatisch mit Ihrem Partner Center-Konto verknüpft.

## <a name="manage-users"></a>Verwalten von Benutzern

In Partner Center können Sie (unter **Kontoeinstellungen**) im Abschnitt **Benutzer** Azure AD verwenden, um die Benutzer, Gruppen und Azure AD-Anwendungen zu verwalten, die über Zugriff auf Ihr Partner Center-Konto verfügen. Ihr Konto muss über Berechtigungen auf [**Managerebene**](#define-user-roles-and-permissions) für das [Geschäftskonto (Azure AD-Mandant)](./company-work-accounts.md) verfügen, in dem Sie Benutzer hinzufügen oder bearbeiten möchten. Zum Verwalten von Benutzern in einem anderen Geschäftskonto/Mandanten müssen Sie sich abmelden und dann als Benutzer mit **Manager**-Berechtigungen für das betreffende Geschäftskonto/den betreffenden Mandanten wieder anmelden.

Wenn Sie bei Ihrem Geschäftskonto (Azure AD-Mandanten) angemeldet sind, können Sie folgende Schritte ausführen:

- [Hinzufügen oder Entfernen von Benutzern](#add-existing-users)
- [Ändern eines Benutzerkennworts](#change-a-user-password)
- [Hinzufügen oder Entfernen von Gruppen](#manage-groups)
- [Hinzufügen oder Entfernen von Azure AD-Anwendungen](#add-new-azure-ad-applications)
- [Verwalten von Schlüsseln für eine Azure AD-Anwendung](#manage-keys-for-an-azure-ad-application)
- [Definieren von Benutzerrollen und Berechtigungen](#define-user-roles-and-permissions)

Denken Sie daran, dass alle Partner Center-Benutzer (einschließlich von Gruppen und Azure AD-Anwendungen) über ein aktives Geschäftskonto in einem [Azure AD-Mandanten](#manage-tenants) verfügen müssen, das Ihrem Partner Center-Konto zugeordnet ist.

### <a name="add-existing-users"></a>Hinzufügen vorhandener Benutzer

So fügen Sie Ihrem Partner Center-Konto Benutzer hinzu, die bereits im [Geschäftskonto (Azure AD-Mandant)](./company-work-accounts.md) Ihres Unternehmens vorhanden sind

1. Wechseln Sie zu **Benutzer** (unter **Kontoeinstellungen**), und wählen Sie **Benutzer hinzufügen** aus.
2. Wählen Sie einen oder mehrere Benutzer aus der angezeigten Liste aus. Über das Suchfeld können Sie bestimmte Benutzer suchen.
\* Wenn Sie mehrere Benutzer auswählen, die Ihrem Partner Center-Konto hinzugefügt werden sollen, müssen Sie diesen dieselbe Rolle oder denselben Satz benutzerdefinierter Berechtigungen zuweisen. Um mehrere Benutzer mit unterschiedlichen Rollen/Berechtigungen hinzuzufügen, wiederholen Sie diese Schritte für jede Rolle bzw. für jeden Satz benutzerdefinierter Berechtigungen.
3. Wenn Sie die Auswahl von Benutzern abgeschlossen haben, wählen Sie **Ausgewählte hinzufügen** aus.
4. Geben Sie im Abschnitt **Rollen** die Rolle(n) oder die benutzerdefinierten Berechtigungen für den oder die ausgewählten Benutzer an.
5. Wählen Sie **Speichern** aus.

### <a name="create-new-users"></a>Erstellen neuer Benutzer

Um neue Benutzerkonten zu erstellen, benötigen Sie ein Konto mit den Berechtigungen [**Globaler Administrator**](../../active-directory/roles/permissions-reference.md).

1. Wechseln Sie zu **Benutzer** (unter **Kontoeinstellungen**), und wählen Sie **Benutzer hinzufügen** und dann **Neue Benutzer erstellen** aus.
1. Geben Sie einen Vornamen, einen Nachnamen und einen Benutzernamen für jeden neuen Benutzer ein. 
1. Wenn Sie dem neuen Benutzer ein globales Administratorkonto im Verzeichnis Ihrer Organisation zuweisen möchten, aktivieren Sie das Kontrollkästchen **Diesen Benutzer als globalen Administrator in Azure AD festlegen, mit vollständiger Kontrolle über alle Verzeichnisressourcen**. Dadurch erhält der Benutzer vollständigen Zugriff auf alle Administratorfunktionen in Ihrem Unternehmens-Azure AD. Dieser Benutzer kann dann Benutzer im Geschäftskonto (Azure AD-Mandant) Ihres Unternehmens hinzufügen und verwalten, allerdings nicht in Partner Center, es sei denn, Sie gewähren dem Konto die entsprechende Rolle/Berechtigungen.
1. Wenn Sie das Kontrollkästchen aktiviert haben, um **diesen Benutzer als globalen Administrator festzulegen**, müssen Sie eine **E-Mail-Adresse zur Kennwortwiederherstellung** für den Benutzer angeben, damit dieser bei Bedarf sein Kennwort wiederherstellen kann.
1. Wählen Sie im Abschnitt **Gruppenmitgliedschaft** alle Gruppen aus, zu denen der neue Benutzer gehören soll.
1. Geben Sie im Abschnitt **Rollen** die Rolle(n) oder die benutzerdefinierten Berechtigungen für den Benutzer an.
1. Wählen Sie **Speichern** aus.

Wenn Sie einen neuen Benutzer in Partner Center erstellen, wird für diesen Benutzer auch ein Konto im Geschäftskonto (Azure AD-Mandant) erstellt, in dem Sie angemeldet sind. Wenn Sie Änderungen am Namen eines Benutzers in Partner Center vornehmen, werden diese Änderungen im Geschäftskonto (Azure AD-Mandant) Ihrer Organisation ebenfalls vorgenommen.

### <a name="invite-new-users-by-email"></a>Einladen neuer Benutzer per E-Mail

Um Benutzer per E-Mail einzuladen, die derzeit nicht zum Geschäftskonto (Azure AD-Mandant) Ihres Unternehmens gehören, benötigen Sie ein Konto mit Berechtigungen als [**globaler Administrator**](../../active-directory/roles/permissions-reference.md).

1. Wechseln Sie zu **Benutzer** (unter **Kontoeinstellungen**), und wählen Sie dann **Benutzer hinzufügen** und anschließend **Benutzer per E-Mail einladen** aus.
2. Geben Sie bis zu 10 E-Mail-Adressen getrennt durch Kommas oder Semikolons ein.
3. Geben Sie im Abschnitt **Rollen** die Rolle(n) oder die benutzerdefinierten Berechtigungen für den Benutzer an.
4. Wählen Sie **Speichern** aus.

Die von Ihnen eingeladenen Benutzer erhalten eine E-Mail-Einladung zur Teilnahme an Ihrem Partner Center-Konto. In Ihrem Geschäftskonto (Azure AD-Mandant) wird ein neues Gastbenutzerkonto erstellt. Jeder Benutzer muss zunächst die Einladung akzeptieren, bevor er auf Ihr Konto zugreifen kann.

Wenn Sie eine Einladung erneut senden möchten, wechseln Sie auf die Seite **Benutzer**, suchen Sie die Einladung in der Benutzerliste, und wählen Sie die betreffende E-Mail-Adresse aus (oder den Text *Die Einladung steht aus*). Wählen Sie dann am unteren Rand der Seite **Einladung erneut senden** aus.

> [!NOTE]
> Wenn Ihre Organisation [Verzeichnisintegration](/previous-versions/azure/azure-services/jj573653(v=azure.100)) verwendet, um den lokalen Verzeichnisdienst mit Ihrem Azure AD zu synchronisieren, können Sie keine neuen Benutzer, Gruppen oder Azure AD-Anwendungen in Partner Center erstellen. Diese müssen Sie (oder ein anderer Administrator in Ihrem lokalen Verzeichnis) direkt im lokalen Verzeichnis erstellen, bevor Sie sie im Partner Center anzeigen und hinzufügen können.

### <a name="remove-a-user"></a>Benutzer entfernen

Um einen Benutzer aus Ihrem Geschäftskonto (Azure AD-Mandant) zu entfernen, wechseln Sie zu **Benutzer** (unter **Kontoeinstellungen**), wählen Sie über das Kontrollkästchen in der Spalte ganz rechts den Benutzer aus, den Sie entfernen möchten, und wählen Sie anschließend aus den verfügbaren Aktionen **Entfernen** aus. In einem Popupfenster können Sie bestätigen, dass Sie den bzw. die ausgewählten Benutzer entfernen möchten.

### <a name="change-a-user-password"></a>Ändern eines Benutzerkennworts

Ein Benutzer kann sein Kennwort bei Bedarf selbst ändern, sofern Sie beim Erstellen des Benutzerkontos eine **E-Mail-Adresse zur Kenntwortwiederherstellung** angegeben haben. Sie können das Kennwort eines Benutzers auch aktualisieren, indem Sie die folgenden Schritte ausführen. Um das Kennwort eines Benutzers im Geschäftskonto (Azure AD-Mandant) Ihres Unternehmens zu ändern, müssen Sie bei einem Konto mit den Berechtigungen als [**globaler Administrator**](../../active-directory/roles/permissions-reference.md) angemeldet sein. Hierdurch werden das Kennwort des Benutzers in Ihrem Azure AD-Mandanten und das Kennwort geändert, das er zum Zugriff auf Partner Center verwendet.

1. Wählen Sie auf der Seite **Benutzer** (unter **Kontoeinstellungen**) den Namen des Benutzerkontos aus, das Sie bearbeiten möchten.
2. Wählen Sie unten auf der Seite die Schaltfläche **Kennwort zurücksetzen** aus.
3. Auf einer Bestätigungsseite werden die Anmeldeinformationen des Benutzers einschließlich eines temporären Kennworts angezeigt. Da Sie nach Verlassen dieser Seite nicht mehr auf das temporäre Kennwort zugreifen können, sollten Sie diese Informationen unbedingt drucken oder kopieren und dem Benutzer zur Verfügung stellen.

## <a name="manage-groups"></a>Verwalten von Gruppen

Mithilfe von Gruppen können Sie mehrere Benutzerrollen und Berechtigungen gleichzeitig verwalten.

### <a name="add-an-existing-group"></a>Hinzufügen einer vorhandenen Gruppe

So fügen Sie Ihrem Partner Center-Konto eine Gruppe hinzu, die bereits im Geschäftskonto (Azure AD-Mandant) Ihrer Organisation vorhanden ist

1. Wählen Sie auf der Seite **Benutzer** (unter **Kontoeinstellungen**) die Option **Gruppen hinzufügen** aus.
2. Wählen Sie eine oder mehrere Gruppen aus der angezeigten Liste aus. Über das Suchfeld können Sie bestimmte Gruppen suchen.
Wenn Sie mehrere Gruppen auswählen, die Ihrem Partner Center-Konto hinzugefügt werden sollen, müssen Sie diesen dieselbe Rolle oder denselben Satz benutzerdefinierter Berechtigungen zuweisen. Um mehrere Gruppen mit unterschiedlichen Rollen/Berechtigungen hinzuzufügen, wiederholen Sie diese Schritte für jede Rolle bzw. für jeden Satz benutzerdefinierter Berechtigungen.
3. Wenn Sie die Auswahl von Gruppen abgeschlossen haben, wählen Sie **Ausgewählte hinzufügen** aus.
4. Geben Sie im Abschnitt **Rollen** die Rolle(n) oder die benutzerdefinierten Berechtigungen für die ausgewählten Gruppen an. Alle Mitglieder der Gruppe erhalten Zugriff auf Ihr Partner Center-Konto mit den Berechtigungen, die Sie der Gruppe gewähren, unabhängig von den Rollen und Berechtigungen, die ihren jeweiligen einzelnen Konten zugewiesen sind.
5. Wählen Sie **Speichern** aus.

Wenn Sie eine vorhandene Gruppe hinzufügen, kann jeder Benutzer, der Mitglied dieser Gruppe ist, mit den Berechtigungen auf Ihr Partner Center-Konto zugreifen, die der zugewiesenen Rolle der Gruppe zugeordnet sind.

### <a name="add-a-new-group"></a>Neue Gruppe hinzufügen

So fügen Sie Ihrem Partner Center-Konto eine neue Gruppe hinzu

1. Wählen Sie auf der Seite **Benutzer** (unter **Kontoeinstellungen**) die Option **Gruppen hinzufügen** aus.
2. Wählen Sie auf der nächsten Seite **Neue Gruppe** aus.
3. Geben Sie den Anzeigenamen für die neue Gruppe ein.
4. Geben Sie die Rolle(n) oder die benutzerdefinierten Berechtigungen für die Gruppe an. Alle Mitglieder der Gruppe erhalten Zugriff auf Ihr Partner Center-Konto mit den Berechtigungen, die Sie hier gewähren, unabhängig von den Rollen und Berechtigungen, die ihren jeweiligen einzelnen Konten zugewiesen sind.
5. Wählen Sie aus der angezeigten Liste Benutzer für die neue Gruppe aus. Über das Suchfeld können Sie bestimmte Benutzer suchen.
6. Wenn Sie alle Benutzer ausgewählt haben, wählen Sie **Ausgewählte hinzufügen** aus, um sie der neuen Gruppe hinzuzufügen.
7. Wählen Sie **Speichern** aus.

Diese neue Gruppe wird im Geschäftskonto (Azure AD-Mandant) Ihrer Organisation erstellt und nicht nur in Ihrem Partner Center-Konto.

### <a name="remove-a-group"></a>Entfernen einer Gruppe

Um eine Gruppe aus Ihrem Geschäftskonto (Azure AD-Mandant) zu entfernen, wechseln Sie zu **Benutzer** (unter **Kontoeinstellungen**), wählen Sie über das Kontrollkästchen in der Spalte ganz rechts die Gruppe aus, die Sie entfernen möchten, und wählen Sie anschließend aus den verfügbaren Aktionen **Entfernen** aus. In einem Popupfenster können Sie bestätigen, dass Sie die ausgewählten Gruppen entfernen möchten.

## <a name="manage-azure-ad-applications"></a>Verwalten von Azure AD-Anwendungen

Sie können Anwendungen oder Diensten, die zum Azure AD Ihres Unternehmen gehören, Zugriff auf Ihr Partner Center-Konto gewähren.

### <a name="add-existing-azure-ad-applications"></a>Hinzufügen vorhandener Azure AD-Anwendungen

So fügen Sie Anwendungen hinzu, die bereits im Azure Active Directory Ihres Unternehmens vorhanden sind

1. Wählen Sie auf der Seite **Benutzer** (unter **Kontoeinstellungen**) die Option **Azure AD-Anwendungen hinzufügen** aus.
2. Wählen Sie eine oder mehrere Azure AD-Anwendungen aus der angezeigten Liste aus. Über das Suchfeld können Sie bestimmte Azure AD-Anwendungen suchen. Wenn Sie mehrere Azure AD-Anwendungen auswählen, die Ihrem Partner Center-Konto hinzugefügt werden sollen, müssen Sie diesen dieselbe Rolle oder denselben Satz benutzerdefinierter Berechtigungen zuweisen. Um mehrere Azure AD-Anwendungen mit unterschiedlichen Rollen/Berechtigungen hinzuzufügen, wiederholen Sie diese Schritte für jede Rolle bzw. für jeden Satz benutzerdefinierter Berechtigungen.
3. Wenn Sie alle Azure AD-Anwendungen ausgewählt haben, wählen Sie **Ausgewählte hinzufügen** aus.
4. Geben Sie im Abschnitt **Rollen** die Rolle(n) oder die benutzerdefinierten Berechtigungen für die ausgewählten Azure AD-Anwendungen an.
5. Wählen Sie **Speichern** aus.

### <a name="add-new-azure-ad-applications"></a>Hinzufügen neuer Azure AD-Anwendungen

Wenn Sie einem neuen Azure AD-Anwendungskonto Partner Center-Zugriff gewähren möchten, erstellen Sie dieses im Abschnitt **Benutzer**. Hierdurch wird ein neues Konto im Geschäftskonto (Azure AD-Mandant) Ihres Unternehmens und nicht nur in Ihrem Partner Center-Konto erstellt. Wenn Sie diese Azure AD-Anwendung in erster Linie für die Partner Center-Authentifizierung verwenden und die Benutzer keinen direkten Zugriff benötigen, können Sie alle gültigen Adressen für die **Antwort-URL** und den **App-ID-URI** eingeben, sofern diese Werte noch nicht von einer anderen Azure AD-Anwendung in Ihrem Verzeichnis verwendet werden.

1. Wählen Sie auf der Seite **Benutzer** (unter **Kontoeinstellungen**) die Option **Azure AD-Anwendungen hinzufügen** aus.
2. Wählen Sie auf der nächsten Seite **Neue Azure AD-Anwendung** aus.
3. Geben Sie die **Antwort-URL** für die neue Azure AD-Anwendung ein. Dies ist die URL, über die sich die Benutzer anmelden und Ihre Azure AD-Anwendung verwenden können (auch bekannt als App-URL oder Anmelde-URL). Die **Antwort-URL** darf nicht länger als 256 Zeichen sein und muss in Ihrem Verzeichnis eindeutig sein.
4. Geben Sie den **App-ID-URI** für die neue Azure AD-Anwendung ein. Dies ist ein logischer Bezeichner für die Azure AD-Anwendung, der angezeigt wird, wenn eine Anforderung für einmaliges Anmelden an Azure AD gesendet wird. Der **App-ID-URI** muss für jede Azure AD-Anwendung in Ihrem Verzeichnis eindeutig sein. Diese ID darf nicht länger als 256 Zeichen sein. Weitere Informationen zum App-ID-URI finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](../../active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts).
5. Geben Sie im Abschnitt **Rollen** die Rolle(n) oder die benutzerdefinierten Berechtigungen für die Azure AD-Anwendung an.
6. Wählen Sie **Speichern** aus.

Nachdem Sie eine Azure AD-Anwendung hinzugefügt oder erstellt haben, können Sie zum Abschnitt **Benutzer** zurückkehren und den Anwendungsnamen auswählen, um die Einstellungen für die Anwendung zu überprüfen, zum Beispiel die Mandanten-ID, Client-ID, Antwort-URL und App-ID-URI.

### <a name="remove-an-azure-ad-application"></a>Entfernen einer Azure AD-Anwendung

Um eine Anwendung aus Ihrem Geschäftskonto (Azure AD-Mandant) zu entfernen, wechseln Sie zu **Benutzer** (unter **Kontoeinstellungen**), wählen Sie über das Kontrollkästchen in der Spalte ganz rechts die Anwendung aus, die Sie entfernen möchten, und wählen Sie anschließend aus den verfügbaren Aktionen **Entfernen** aus. In einem Popupfenster können Sie bestätigen, dass Sie die ausgewählten Anwendungen entfernen möchten.

### <a name="manage-keys-for-an-azure-ad-application"></a>Verwalten von Schlüsseln für eine Azure AD-Anwendung

Wenn Ihre Azure AD-Anwendung Daten in Microsoft Azure AD liest und schreibt, benötigt sie einen Schlüssel. Sie können Schlüssel für eine Azure AD-Anwendung erstellen, indem Sie die zugehörigen Informationen in Partner Center bearbeiten. Außerdem können Sie nicht mehr benötigte Schlüssel entfernen.

1. Wählen Sie auf der Seite **Benutzer** (unter **Kontoeinstellungen**) den Namen der Azure AD-Anwendung aus. Alle aktiven Schlüssel der Azure AD-Anwendung werden angezeigt, einschließlich des Datums, an dem der Schlüssel erstellt wurde, und des Ablaufdatums (50).
2. Um einen Schlüssel zu entfernen, der nicht mehr benötigt wird, wählen Sie **Entfernen** aus.
3. Wählen Sie zum Hinzufügen eines neuen Schlüssels **Neuen Schlüssel hinzufügen** aus.
4. In einem Bildschirm werden die **Client-ID** und die **Schlüsselwerte** angezeigt. Da Sie nach Verlassen der Seite nicht mehr auf diese Informationen zugreifen können, sollten Sie sie unbedingt drucken oder kopieren.
5. Wenn Sie weitere Schlüssel erstellen möchten, wählen Sie **Weiteren Schlüssel hinzufügen** aus.

## <a name="define-user-roles-and-permissions"></a>Definieren von Benutzerrollen und Berechtigungen

Im Programm für den kommerziellen Marketplace in Partner Center können den Benutzern Ihres Unternehmens die folgenden Rollen und Berechtigungen zugewiesen werden:

- **Besitzer**
  - Die Person, die das Konto ursprünglich erstellt hat und über vollständigen Zugriff darauf verfügt – einschließlich der Möglichkeit, alle Kontobenutzer zu erstellen und zu bearbeiten und alle Finanz- und Kontoeinstellungen zu ändern. Ein Konto hat in der Regel nur einen einzelnen Besitzer.
- **Manager**
  - Kann auf alle Microsoft-Kontofeatures mit Ausnahme von Steuer- und Auszahlungseinstellungen zugreifen
  - Kann Benutzer, Rollen und Geschäftskonten (Mandanten) verwalten
- **Developer**
  - Kann Angebote verwalten und veröffentlichen
  - Kann einige Herausgeberberichte anzeigen

> [!NOTE]
> Für das Programm „Kommerzieller Marketplace“ werden die Rollen „Globaler Administrator“, „Mitwirkender im Geschäftsbereich“, „Mitwirkender im Finanzbereich“ und „Händler“ nicht verwendet. Das Zuweisen dieser Rollen zu Benutzern besitzt keine Auswirkungen. Nur die Rollen „Manager“ und „Developer“ erteilen Benutzern Berechtigungen.

Weitere Informationen zum Verwalten von Rollen und Berechtigungen in anderen Bereichen von Partner Center, zum Beispiel Azure Active Directory (AD), Cloud Solution Provider (CSP), Control Panel Vendor (CPV), Gastbenutzer oder Microsoft Partner Network (MPN) finden Sie unter [Zuweisen von Rollen und Berechtigungen in Partner Center](/partner-center/permissions-overview).

## <a name="manage-tenants"></a>Verwalten von Mandanten

Ein Azure AD-Mandant (Active Directory), in dieser Dokumentation auch als „Geschäftskonto“ bezeichnet, spiegelt Ihre Organisationseinrichtung im Azure-Portal wider und hilft Ihnen, eine bestimmte Instanz der Microsoft-Clouddienste für Ihre internen und externen Benutzer zu verwalten. Wenn Ihre Organisation einen Microsoft-Clouddienst wie Azure, Microsoft Intune oder Microsoft 365 abonniert hat, wurde ein Azure AD-Mandant für Sie erstellt.

Sie können mehrere Mandanten für die Verwendung mit Partner Center einrichten. Jeder Benutzer mit der **Manager**-Rolle im Partner Center-Konto hat die Möglichkeit, Azure AD-Mandanten im Konto hinzuzufügen und zu entfernen.  

### <a name="add-an-existing-tenant"></a>Hinzufügen eines vorhandenen Mandanten

So ordnen Sie einen anderen Azure AD-Mandanten zu Ihrem Partner Center-Konto hinzu

1. Wählen Sie auf der Seite **Mandanten** (unter **Kontoeinstellungen**) die Option **Anderen Azure AD-Mandanten zuordnen** aus.
2. Geben Sie Ihre Azure AD-Anmeldeinformationen für den Mandanten ein, den Sie zuordnen möchten.
3. Überprüfen Sie die Organisation und den Domänennamen für Ihren Azure AD-Mandanten. Wählen Sie zum Abschließen der Zuordnung **Bestätigen** aus.

Wenn die Zuordnung erfolgreich ist, können Sie anschließend in Partner Center im Abschnitt **Benutzer** Kontobenutzer hinzufügen und verwalten.

### <a name="create-a-new-tenant"></a>Erstellen eines neuen Mandanten

So erstellen Sie einen neuen Azure AD-Mandanten mit Ihrem Partner Center-Konto

1. Wählen Sie auf der Seite **Mandanten** (unter **Kontoeinstellungen**) die Option **Neuen Azure AD-Mandanten erstellen** aus.
2. Geben Sie die Verzeichnisinformationen für das neue Azure AD ein:
    - **Domänenname**: Der eindeutige Name, der für die Azure AD-Domäne verwendet wird, zusammen mit „. onmicrosoft.com“. Wenn Sie beispielsweise „Beispiel“ eingegeben haben, würde Ihre Azure AD-Domäne „Beispiel.onmicrosoft.com“ lauten.
    - **Kontakt-E-Mail**: Eine E-Mail-Adresse, über die wir Sie bei Bedarf bezüglich Ihres Konto kontaktieren können.
    - **Benutzerkontoinformationen für Globaler Administrator**: Der Vorname, Nachname, Benutzername und das Kennwort, die Sie für das neue globale Administratorkonto verwenden möchten.
3. Wählen Sie **Erstellen** aus, um die neuen Domänen- und Kontoinformationen zu bestätigen.
4. Melden Sie sich mit Ihrem neuen Benutzernamen und Kennwort als globaler Azure AD-Administrator an, um [Benutzer hinzufügen und zu verwalten](#manage-users).

Weitere Informationen zum Erstellen neuer Mandanten im Azure-Portal statt im Partner Center-Portal finden Sie im Artikel [Erstellen eines neuen Mandanten in Azure Active Directory](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

### <a name="remove-a-tenant"></a>Entfernen eines Mandanten

Um einen Mandanten aus Ihrem Partner Center-Konto zu entfernen, suchen Sie dessen Namen auf der Seite **Mandanten** (unter **Kontoeinstellungen**), und wählen Sie dann **Entfernen** aus. Sie werden gefragt, ob Sie den Mandanten wirklich entfernen möchten. Sobald Sie ihn entfernt haben, kann sich kein Benutzer in diesem Mandanten mehr beim Partner Center-Konto anmelden, und alle Berechtigungen, die Sie für diese Benutzer konfiguriert haben, werden entfernt.

Wenn Sie einen Mandanten entfernen, kann sich kein Benutzer mehr bei dem Konto anmelden, der über diesen Mandanten zum Partner Center-Konto hinzugefügt wurde.

> [!TIP]
> Ein Mandant kann nicht entfernt werden, wenn Sie derzeit über ein Konto im betreffenden Mandanten in Partner Center angemeldet sind. Um einen Mandanten zu entfernen, müssen Sie sich in Partner Center als **Manager** für einen anderen Mandanten anmelden, der dem Konto zugeordnet ist. Wenn dem Konto nur ein einziger Mandant zugeordnet, kann dieser Mandant erst nach der Anmeldung bei dem Microsoft-Konto entfernt werden, das das Konto eröffnet hat.

## <a name="agreements"></a>Vereinbarungen

Im Abschnitt **Verträge** in Partner Center (unter **Kontoeinstellungen**) können Sie eine Liste der veröffentlichten Verträge anzeigen, die Sie autorisiert haben. Diese Verträge sind nach Name und Versionsnummer aufgeführt. Ebenfalls angezeigt werden das Datum, an dem sie akzeptiert wurden, und der Name des Benutzers, der den Vertrag akzeptiert hat.

Möglicherweise wird am oberen Rand dieser Seite **Aktionen erforderlich** angezeigt, wenn Vertragsaktualisierungen vorliegen, die Ihre Aufmerksamkeit erfordern. Um einen aktualisierten Vertrag zu akzeptieren, müssen Sie zunächst die verknüpfte Vertragsversion lesen. Wählen Sie dann **Verträge akzeptieren** aus.
