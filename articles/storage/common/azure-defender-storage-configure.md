---
title: Konfigurieren von Azure Defender für Storage
titleSuffix: Azure Storage
description: Konfigurieren Sie Azure Defender für Storage, um Anomalien in Bezug auf die Kontoaktivität zu erkennen und Benachrichtigungen über potenziell schädliche Zugriffsversuche auf Ihr Konto zu erhalten.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: 0bda32aaab301fe9ed685f0bfd6d4596fab4e5db
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789011"
---
# <a name="configure-azure-defender-for-storage"></a>Konfigurieren von Azure Defender für Storage

Azure Defender für Storage ermöglicht die Nutzung intelligenter Sicherheitsfunktionen zur Erkennung von ungewöhnlichen und möglicherweise schädlichen Versuchen, auf Speicherkonten zuzugreifen oder diese unbefugt zu nutzen. Aufgrund dieser Schutzebene können Sie Bedrohungen begegnen, ohne ein Sicherheitsexperte zu sein oder Systeme für die Überwachung der Sicherheit verwalten zu müssen.

Bei Anomalien im Rahmen von Aktivitäten werden Sicherheitswarnungen ausgelöst. Diese Sicherheitswarnungen sind in [Azure Security Center](https://azure.microsoft.com/services/security-center/) integriert und werden mit Informationen zu verdächtigen Aktivitäten und Empfehlungen zur Untersuchung und Beseitigung von Bedrohungen auch per E-Mail an Abonnementadministratoren gesendet.

Der Dienst erfasst Ressourcenprotokolle von an Blob Storage und Azure Files gesendete Lese-, Schreib- und Löschanforderungen, um Bedrohungen zu erkennen. Wenn Sie Warnungen von Azure Defender untersuchen möchten, können Sie mithilfe von Storage Analytics Logging entsprechende Speicheraktivitäten anzeigen. Weitere Informationen finden Sie unter **Konfigurieren der Protokollierung** in [Überwachen eines Speicherkontos im Azure-Portal](storage-monitor-storage-account.md#configure-logging).

## <a name="availability"></a>Verfügbarkeit

Azure Defender für Storage ist derzeit für Blob Storage, Azure Files und Azure Data Lake Storage Gen2 verfügbar. Zu den Kontotypen, die Azure Defender unterstützen, gehören Konten vom Typ „Allgemein v2“ sowie Blockblob- und Blob Storage-Konten. Azure Defender für Storage ist in allen öffentlichen Clouds und Clouds der US-Regierungsbehörden verfügbar, nicht aber in anderen Sovereign Cloud- oder Azure Government-Cloudregionen.

Konten mit für Data Lake Storage aktivierten hierarchischen Namespaces unterstützen Transaktionen, die sowohl die Azure Blob Storage-APIs als auch die Data Lake Storage-APIs verwenden. Azure-Dateifreigaben unterstützen Transaktionen über SMB.

Preisdetails, einschließlich einer kostenlosen 30-Tage-Testversion, finden Sie auf der [Seite mit der Preisübersicht zu Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/).

Die folgende Liste biete eine Übersicht über die Verfügbarkeit von Azure Defender für Storage:

- Status des Release:
  - [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) (allgemeine Verfügbarkeit)
  - [Azure Files](../files/storage-files-introduction.md) (allgemeine Verfügbarkeit)
  - Azure Data Lake Storage Gen2 (allgemeine Verfügbarkeit)
- Clouds:<br>
    ✔ Kommerzielle Clouds<br>
    ✔ US Gov<br>
    ✘ China Gov, andere Gov

## <a name="set-up-azure-defender"></a>Einrichten von Azure Defender

Sie können Azure Defender für Storage auf verschiedene Arten konfigurieren, die in den folgenden Abschnitten beschrieben werden.

### <a name="azure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

Wenn Sie den Standard-Tarif in Azure Security Center abonnieren, wird Azure Defender automatisch in all Ihren Speicherkonten eingerichtet. Sie können Azure Defender für Ihre Speicherkonten in einem bestimmten Abonnement folgendermaßen aktivieren oder deaktivieren:

1. Starten Sie **Azure Security Center** im [Azure-Portal](https://portal.azure.com).
1. Wählen Sie im Hauptmenü unter **Verwaltung** die Option **Preise und Einstellungen** aus.
1. Wählen Sie das Abonnement aus, für das Sie Azure Defender aktivieren bzw. deaktivieren möchten.
1. Wählen Sie **Azure Defender ein** aus, um Azure Defender für das Abonnement zu aktivieren.
1. Suchen Sie unter **Azure Defender-Plan nach Ressourcentyp auswählen** nach der Zeile **Storage** , und wählen Sie in der Spalte **Plan** die Option **Aktiviert** aus.
1. Speichern Sie die Änderungen.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Screenshot: Aktivieren von Azure Defender für Storage in Security Center":::

Azure Defender ist nun für alle Speicherkonten in diesem Abonnement aktiviert.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Starten Sie das [Azure-Portal](https://portal.azure.com/).
1. Navigieren Sie zum Speicherkonto. Wählen Sie unter **Einstellungen** die Option **Erweiterte Sicherheit** aus.
1. Wählen Sie **Azure Defender für Storage aktivieren** aus.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Screenshot: Aktivieren von Azure Defender für Storage in Security Center":::

Azure Defender ist nun für dieses Speicherkonto aktiviert.

### <a name="template"></a>[Vorlage](#tab/template)

Verwenden Sie zur Bereitstellung eines Azure Storage-Kontos, bei dem Azure Defender aktiviert ist, eine Azure Resource Manager-Vorlage. Weitere Informationen finden Sie unter [Storage-Konto mit Advanced Threat Protection](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Verwenden Sie eine Azure Policy-Instanz zum Aktivieren von Azure Defender für Speicherkonten unter einem bestimmten Abonnement oder einer bestimmten Ressourcengruppe.

1. Starten Sie die Azure-Seite **Richtlinie – Definitionen** .
1. Suchen Sie nach der Richtlinie **Azure Defender für Speicherkonten bereitstellen** .

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="Screenshot: Aktivieren von Azure Defender für Storage in Security Center":::

1. Wählen Sie ein Azure-Abonnement oder eine Ressourcengruppe aus.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="Screenshot: Aktivieren von Azure Defender für Storage in Security Center":::

1. Weisen Sie die Richtlinie zu.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="Screenshot: Aktivieren von Azure Defender für Storage in Security Center":::

### <a name="rest-api"></a>[REST-API](#tab/rest-api)

Verwenden Sie REST-API-Befehle, um die Azure Defender-Einstellung für ein bestimmtes Speicherkonto zu erstellen, zu aktualisieren oder abzurufen.

- [Advanced Threat Protection – Create (Erstellen)](/rest/api/securitycenter/advancedthreatprotection/create)
- [Advanced Threat Protection – Get (Abrufen)](/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie die folgenden PowerShell-Cmdlets:

- [Enable Advanced Threat Protection (Advanced Threat Protection aktivieren)](/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
- [Get Advanced Threat Protection (Advanced Threat Protection abrufen)](/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
- [Disable Advanced Threat Protection (Advanced Threat Protection deaktivieren)](/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Untersuchen von sicherheitsrelevanten Anomalien

Wenn für die Speicheraktivität Anomalien auftreten, erhalten Sie eine E-Mail-Benachrichtigung mit Informationen zum verdächtigen Sicherheitsereignis. Einzelheiten des Ereignisses sind:

- Art der Anomalie
- Speicherkontoname
- Ereigniszeit
- Speichertyp
- Mögliche Ursachen
- Untersuchungsschritte
- Schritte zur Bereinigung

Die E-Mail enthält auch Details zu möglichen Ursachen und empfohlenen Aktionen zur Untersuchung und Eindämmung der potenziellen Bedrohung.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="Screenshot: Aktivieren von Azure Defender für Storage in Security Center":::

Sie können Ihre aktuellen Sicherheitswarnungen in Azure Security Center über die [Kachel Sicherheitswarnungen](../../security-center/security-center-managing-and-responding-alerts.md) anzeigen und verwalten. Durch Klicken auf eine bestimmte Warnung werden Details und Aktionen zum Untersuchen der aktuellen Bedrohung und zum Begegnen zukünftiger Bedrohungen bereitgestellt.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="Screenshot: Aktivieren von Azure Defender für Storage in Security Center":::

## <a name="security-alerts"></a>Sicherheitswarnungen

Warnungen werden bei ungewöhnlichen und potenziell schädlichen Zugriffsversuchen oder Exploit-Vorgängen für Speicherkonten generiert. Eine Liste mit Warnungen für Azure Storage finden Sie unter [Warnungen für Azure Storage](../../security-center/alerts-reference.md#alerts-azurestorage).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [Protokollen in Azure Storage-Konten](/rest/api/storageservices/About-Storage-Analytics-Logging)
- Weitere Informationen zu [Azure Security Center](../../security-center/security-center-introduction.md)