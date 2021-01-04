---
title: Planen einer von Azure verwalteten Anwendung für ein Azure-Anwendungsangebot
description: Erfahren Sie, was zum Erstellen eines Plans für eine verwaltete Anwendung für ein neues Azure-Anwendungsangebot erforderlich ist, wenn Sie das Portal für den kommerziellen Marketplace in Microsoft Partner Center verwenden.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 694f501efc565ed498c1c8d8e2e38326277e8605
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621415"
---
# <a name="plan-an-azure-managed-application-for-an-azure-application-offer"></a>Planen einer von Azure verwalteten Anwendung für ein Azure-Anwendungsangebot

Ein Plan für eine von Azure _verwaltete Anwendung_ ist eine Möglichkeit, ein Azure-Anwendungsangebot im Azure Marketplace zu veröffentlichen. Lesen Sie den Artikel [Planen eines Azure-Anwendungsangebots für den kommerziellen Marketplace](plan-azure-application-offer.md), wenn Sie dies noch nicht getan haben.

Verwaltete Anwendungen sind Transaktionsangebote, die über den Azure Marketplace bereitgestellt und abgerechnet werden. Den Benutzern wird die Listenoption Jetzt kaufen angezeigt.

Verwenden einer Azure-Anwendung: Plan für die verwaltete Anwendung, wenn die folgenden Bedingungen gelten:

- Sie stellen eine abonnementbasierte Lösung für Ihren Kunden bereit, indem Sie entweder einen virtuellen Computer (Virtual Machine, VM) oder eine gesamte auf Infrastructure-as-a-Service (IaaS) basierende Lösung verwenden.
- Sie oder Ihr Kunde benötigen/benötigt eine von einem Partner verwaltete Lösung. Ein Partner kann z. B. ein Systemintegrator oder ein Anbieter für verwaltete Dienste (Managed Service Provider, MSP) sein.

## <a name="managed-application-offer-requirements"></a>Anforderungen für Angebote verwalteter Anwendungen

| Requirements (Anforderungen) | Details |
| ------------ | ------------- |
| Ein Azure-Abonnement | Verwaltete Anwendungen müssen im Abonnement eines Kunden bereitgestellt werden, können aber von einem Drittanbieter verwaltet werden. |
| Abrechnung und Messung | Die Ressourcen werden im Azure-Abonnement eines Kunden bereitgestellt. Bei VMs, für die das Modell der nutzungsbasierten Bezahlung verwendet wird, erfolgt die Transaktion mit dem Kunden über Microsoft, und die Abrechnung wird über das Azure-Abonnement des Kunden abgewickelt. <br><br> Bei Bring-Your-Own-License-VMs rechnet Microsoft die angefallenen Infrastrukturkosten im Kundenabonnement ab. Die Softwarelizenzgebühren rechnen Sie jedoch direkt mit dem Kunden ab. |
| Azure-kompatible virtuelle Festplatte (VHD) | VMs müssen unter Windows oder Linux erstellt werden. Weitere Informationen finden Sie unter<br> • [Erstellen einer technischen Azure-VM-Ressource](./azure-vm-create-certification-faq.md#address-a-vulnerability-or-an-exploit-in-a-vm-offer) (für Windows-VHDs).<br> • [Von Azure unterstützte Distributionen von Linux](../virtual-machines/linux/endorsed-distros.md) (für Linux-VHDs). |
| Zuordnen der Nutzung durch Kunden | Alle neuen Azure-Anwendungsangebote müssen auch eine [GUID zum Zuordnen der Nutzung durch Kunden von Azure-Partnern](azure-partner-customer-usage-attribution.md) enthalten. Weitere Informationen zur Zuordnung der Nutzung durch Kunden sowie zu deren Aktivierung finden Sie unter [Zuordnung der Nutzung durch Kunden von Azure-Partnern](azure-partner-customer-usage-attribution.md). |
| Bereitstellungspaket | Sie benötigen ein Bereitstellungspaket, mit dem Kunden Ihren Plan bereitstellen können. Wenn Sie mehrere Pläne erstellen, die dieselbe technische Konfiguration erfordern, können Sie auch dasselbe Paket verwenden. Weitere Details finden Sie im nächsten Abschnitt: Bereitstellungspaket. |
|||

> [!NOTE]
> Verwaltete Anwendungen müssen über den Azure Marketplace bereitgestellt werden können. Wenn die Kundenkommunikation von Bedeutung ist, wenden Sie sich an interessierte Kunden, nachdem Sie den gemeinsamen Zugriff auf Leads aktiviert haben.

## <a name="deployment-package"></a>Bereitstellungspaket

Das Bereitstellungspaket enthält alle für diesen Plan erforderlichen Vorlagendateien sowie alle zusätzlichen Ressourcen in einer ZIP-Datei.

Alle Azure-Anwendungen müssen diese beiden Dateien im Stammordner eines ZIP-Archivs enthalten:

- Eine Resource Manager-Vorlagendatei mit dem Namen [mainTemplate.json](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template). In dieser Vorlage werden die Ressourcen definiert, die im Azure-Abonnement des Kunden bereitgestellt werden sollen. Beispiele für Resource Manager-Vorlagen finden Sie im [Katalog der Azure-Schnellstartvorlagen](https://azure.microsoft.com/documentation/templates/) oder im entsprechenden Repository [GitHub: Azure Resource Manager Quickstart Templates](https://github.com/azure/azure-quickstart-templates) (Azure Resource Manager-Schnellstartvorlagen).
- Eine Benutzeroberflächendefinition für die Oberfläche zum Erstellen von Azure-Anwendungen mit dem Namen [createUiDefinition.json](../azure-resource-manager/managed-applications/create-uidefinition-overview.md). Auf der Benutzeroberfläche geben Sie Elemente an, die Kunden die Angabe von Parameterwerten ermöglichen.

Unterstützte maximale Dateigrößen:

- Bis zu 1 GB komprimierte Gesamtgröße des ZIP-Archivs
- Bis zu 1 GB für jede einzelne dekomprimierte Datei aus dem ZIP-Archiv

Alle neuen Azure-Anwendungsangebote müssen auch eine [GUID zum Zuordnen der Nutzung durch Kunden von Azure-Partnern](azure-partner-customer-usage-attribution.md) enthalten.

## <a name="azure-regions"></a>Azure-Regionen

Sie können Ihren Plan in öffentlichen Azure-Regionen und Azure Government-Regionen veröffentlichen. Testen und validieren Sie Ihren Plan vor dem Veröffentlichen für [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md) in der Umgebung, da sich bestimmte Endpunkte möglicherweise unterscheiden. Fordern Sie für Einrichten und Testen Ihres Plans ein Testkonto bei [Microsoft Azure Government-Test](https://azure.microsoft.com/global-infrastructure/government/request/) an.

Als Herausgeber sind Sie für alle Kompatibilitätskontrollen, Sicherheitsmaßnahmen und bewährten Methoden verantwortlich. Azure Government verwendet physisch isolierte Rechenzentren und Netzwerke (die sich ausschließlich in den USA befinden).

Eine Liste der Länder und Regionen, die vom kommerziellen Marketplace unterstützt werden, finden Sie unter [Geografische Verfügbarkeit und Unterstützung von Währungen](marketplace-geo-availability-currencies.md).

Azure Government-Dienste verarbeiten Daten, die bestimmten behördlichen Vorschriften und Anforderungen unterliegen. Beispielsweise FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 und CJIS. Um bei Ihren Zertifizierungen ein Bewusstsein für diese Programme zu schaffen, können Sie bis zu 100 Links bereitstellen, in denen sie beschrieben werden. Diese können entweder direkt mit ihrem Listing im Programm verknüpft sein oder zu Beschreibungen der Konformität mit diesen Programmen auf Ihren eigenen Websites weiterleiten. Diese Links sind nur für Azure Government-Kunden sichtbar.

## <a name="choose-who-can-see-your-plan"></a>Auswählen der Sichtbarkeit des Plans

Sie können festlegen, dass jeder Plan für alle Benutzer (öffentlich) oder nur für eine bestimmte Zielgruppe (privat) angezeigt werden soll. Sie können bis zu 100 Pläne erstellen, von denen bis zu 45 privat sein können. Möglicherweise möchten Sie einen privaten Plan erstellen, um bestimmten Kunden abweichende Preisoptionen oder technische Konfigurationen anzubieten.

Der Zugriff auf einen privaten Plan wird über Azure-Abonnement-IDs zugewiesen. Dabei kann eine Beschreibung jeder zugewiesenen Abonnement-ID angegeben werden. Sie können maximal 10 Abonnement-IDs manuell eingeben oder über eine CSV-Datei bis zu 10.000 Abonnement-IDs angeben. Azure-Abonnement-IDs werden als GUIDs dargestellt. Buchstaben müssen in Kleinbuchstaben angegeben werden.

Private Pläne werden bei Azure-Abonnements, die über einen Handelspartner des Cloud Solution Provider-Programms (CSP) eingerichtet wurden, nicht unterstützt. Weitere Informationen finden Sie unter [Private Angebote im kommerziellen Microsoft-Marketplace](private-offers.md).

> [!NOTE]
> Wenn Sie einen privaten Plan veröffentlichen, können Sie die Sichtbarkeit später in „Öffentlich“ ändern. Wenn Sie hingegen einen öffentlichen Plan veröffentlichen, können Sie dessen Sichtbarkeit nicht in „Privat“ ändern.

## <a name="define-pricing"></a>Festlegen von Preisen

Sie müssen für jeden Plan den Preis pro Monat angeben. Dieser Preis gilt zusätzlich zu allen Azure-Infrastruktur- oder nutzungsbasierten Softwarekosten, die durch die von dieser Lösung bereitgestellten Ressourcen entstehen.

Zusätzlich zu den monatlichen Preisen können Sie die Preise für die Nutzung von nicht standardmäßigen Einheiten mithilfe der [getakteten Abrechnung](partner-center-portal/azure-app-metered-billing.md) festlegen. Wenn Sie möchten, können Sie den monatlichen Preis auf null (0) festlegen und ausschließlich die getaktete Abrechnung verwenden.

Preise in USD (USD = US-Dollar) werden beim Speichern gemäß dem aktuellen Wechselkurs in die lokale Währung aller ausgewählten Märkte konvertiert. Sie können aber auch für jeden Markt Kundenpreise festlegen.

## <a name="just-in-time-jit-access"></a>Just-In-Time-Zugriff (JIT)

Der JIT-Zugriff ermöglicht es Ihnen, erhöhte Zugriffsrechte auf die Ressourcen einer verwalteten Anwendung zur Problembehandlung oder Wartung anzufordern. Sie verfügen immer über Lesezugriff auf die Ressourcen, aber für einen bestimmten Zeitraum können Sie über erhöhte Zugriffsrechte verfügen. Weitere Informationen finden Sie unter [Aktivieren und Anfordern des Just-In-time-Zugriffs für Azure Managed Applications](../azure-resource-manager/managed-applications/request-just-in-time-access.md).

> [!NOTE]
> Stellen Sie sicher, dass Sie Ihre `createUiDefinition.json`-Datei aktualisiert wurde, damit dieses Feature unterstützt wird.

## <a name="deployment-mode"></a>Bereitstellungsmodus

Sie können einen Plan für eine verwaltete Anwendung so konfigurieren, dass entweder der Bereitstellungsmodus **Vollständig** oder **Inkrementell** verwendet wird. Im Modus „Vollständig“ führt eine erneute Bereitstellung der Anwendung durch den Kunden zur Entfernung von Ressourcen aus der verwalteten Ressourcengruppe, wenn die Ressourcen nicht in der Datei [mainTemplate.json](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template) definiert sind. Im inkrementellen Modus bleiben bestehende Ressourcen bei einer erneuten Bereitstellung der Anwendung unverändert. Weitere Informationen finden Sie unter [Azure Resource Manager-Bereitstellungsmodi](../azure-resource-manager/templates/deployment-modes.md).

## <a name="notification-endpoint-url"></a>Benachrichtigungsendpunkt-URL

Sie können optional einen HTTPS-Webhookendpunkt angeben, um Benachrichtigungen zu allen CRUD-Vorgängen bei Instanzen einer verwalteten Anwendung eines Plans zu erhalten.

## <a name="customize-allowed-customer-actions-optional"></a>Anpassen der zulässigen Kundenaktionen (optional)

Sie können optional angeben, welche Aktionen Kunden neben den standardmäßig verfügbaren `*/read`-Aktionen für verwaltete Ressourcen ausführen können.

Wenn Sie diese Option auswählen, müssen Sie entweder die Steuerungsaktionen oder die zulässigen Datenaktionen oder beides bereitstellen. Weitere Informationen finden Sie unter Weitere Informationen finden Sie unter [Grundlegendes zu Ablehnungszuweisungen für Azure-Ressourcen](../role-based-access-control/deny-assignments.md). Die verfügbaren Aktionen finden Sie unter [Vorgänge für Azure Resource Manager-Ressourcenanbieter](../role-based-access-control/resource-provider-operations.md). Beispiel: Wenn Sie Kunden das Neustarten virtueller Computer erlauben möchten, fügen Sie den zulässigen Aktionen `Microsoft.Compute/virtualMachines/restart/action` hinzu.

## <a name="choose-who-can-manage-the-application"></a>Festlegen des Verwaltungszugriffs auf die Anwendung

Sie müssen angeben, wer eine verwaltete Anwendung in den einzelnen ausgewählten Clouds verwalten kann: _Öffentliches Azure_ und _Azure Government Cloud_. Sammeln Sie die folgenden Informationen:

- **Azure Active Directory-Mandanten-ID**: Hierbei handelt es sich um die Azure AD-Mandanten-ID (auch als Verzeichnis-ID bezeichnet), die die Identitäten der Benutzer, Gruppen oder Anwendungen enthält, denen Sie Berechtigungen erteilen möchten. Sie finden Ihre Azure AD-Mandanten-ID im Azure-Portal unter [Eigenschaften für Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).
- **Autorisierungen:** Fügen Sie die Azure Active Directory-Objekt-ID jedes Benutzers, jeder Gruppe oder Anwendung hinzu, dem oder der die Berechtigung für die verwaltete Ressourcengruppe erteilt werden soll. Sie können den Benutzer anhand seiner Prinzipal-ID auf dem [Blatt „Azure Active Directory Benutzer“ im Azure-Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) identifizieren.

Ordnen Sie jeder Prinzipal-ID eine der in Azure AD integrierten Rollen zu (Besitzer oder Mitwirkender). Die ausgewählte Rolle beschreibt die Berechtigungen, die der Prinzipal für die Ressourcen im Kundenabonnement erhält. Weitere Informationen finden Sie unter [Integrierte Azure-Rollen](../role-based-access-control/built-in-roles.md). Weitere Informationen zur rollenbasierten Zugriffssteuerung (RBAC) finden Sie unter [Erste Schritte mit RBAC im Azure-Portal](../role-based-access-control/overview.md).

> [!NOTE]
> Obwohl Sie bis zu 100 Autorisierungen pro Azure-Region hinzufügen können, ist es im Allgemeinen einfacher, eine Active Directory-Benutzergruppe zu erstellen und ihre ID in der „Prinzipal-ID“ anzugeben. Dies ermöglicht Ihnen, nach der Bereitstellung des Plans der Verwaltungsgruppe weitere Benutzer hinzuzufügen, und verringert die Notwendigkeit, den Plan zu aktualisieren, nur um weitere Autorisierungen hinzuzufügen.

## <a name="policy-settings"></a>Richtlinieneinstellungen

Sie können [Azure-Richtlinien](../governance/policy/index.yml) auf Ihre verwaltete Anwendung anwenden, um Complianceanforderungen für die bereitgestellte Lösung anzugeben. Informationen zu Richtliniendefinitionen und zum Format der Parameterwerte finden Sie unter [Azure Policy-Beispiele](../governance/policy/samples/index.md).

Sie können maximal fünf Richtlinien und nur eine Instanz jedes einzelnen Richtlinientyps konfigurieren. Einige Richtlinientypen erfordern zusätzliche Parameter.

| Richtlinientyp | Richtlinienparameter erforderlich |
| ------------ | ------------- |
| Azure SQL-Datenbankverschlüsselung | Nein |
| Azure SQL Server-Überwachungseinstellungen | Ja |
| Azure Data Lake Store-Verschlüsselung | Nein |
| Diagnoseeinstellung überwachen | Ja |
| Konformität des Ressourcenstandorts überwachen | Nein |
|||

Für jeden Richtlinientyp, den Sie hinzufügen, müssen Sie die SKU „Standard“ oder „Freie Richtlinie“ zuordnen. Der Standard-SKU ist für Überwachungsrichtlinien erforderlich. Richtliniennamen sind auf 50 Zeichen beschränkt.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines Azure-Anwendungsangebots im kommerziellen Marketplace](create-new-azure-apps-offer.md)
