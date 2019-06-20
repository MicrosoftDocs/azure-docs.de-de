---
title: Advanced Threat Protection für Azure Storage
description: Es wird beschrieben, wie Sie die Advanced Threat Protection von Azure Storage zum Erkennen von Anomalien in Bezug auf die Kontoaktivität erkennen und Benachrichtigungen über potenziell schädliche Zugriffsversuche auf Ihr Konto erhalten.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: ed263fec271801ac9c46bbc3125c71ca15b9a330
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153971"
---
# <a name="advanced-threat-protection-for-azure-storage"></a>Advanced Threat Protection für Azure Storage

Advanced Threat Protection für Azure Storage ermöglicht die Nutzung intelligenter Sicherheitsfunktionen zur Erkennung von ungewöhnlichen und möglicherweise schädlichen Versuchen, auf Speicherkonten zuzugreifen oder diese unbefugt zu nutzen. Aufgrund dieser Schutzebene können Sie Bedrohungen begegnen, ohne dass Sie ein Sicherheitsexperte sein oder Systeme für die Überwachung der Sicherheit verwalten müssen. 

Bei Anomalien im Rahmen von Aktivitäten werden Sicherheitswarnungen ausgelöst.  Diese Sicherheitswarnungen sind in [Azure Security Center](https://azure.microsoft.com/services/security-center/) integriert und werden mit Informationen zu verdächtigen Aktivitäten und Empfehlungen zur Untersuchung und Beseitigung von Bedrohungen auch per E-Mail an Abonnementadministratoren gesendet.

> [!NOTE]
> * Advanced Threat Protection für Azure Storage ist derzeit nur für Blob Storage verfügbar.
> * Preisdetails, einschließlich einer kostenlosen 30-Tage-Testversion, finden Sie auf der [Seite mit der Preisübersicht zu Azure Security Center]( https://azure.microsoft.com/pricing/details/security-center/).
> * ATP für Azure Storage ist derzeit in Azure Government- und Sovereign Cloud-Regionen nicht verfügbar.

Bei Advanced Threat Protection für Azure Storage werden Diagnoseprotokolle von an Blob Storage gesendeten Lese-, Schreib- und Löschanforderungen erfasst, um Bedrohungen zu erkennen. Wenn Sie die Warnungen von Advanced Threat Protection untersuchen möchten, können Sie mithilfe von Storage Analytics Logging entsprechende Speicheraktivitäten anzeigen. Weitere Informationen finden Sie unter [Konfigurieren der Protokollierung](storage-monitor-storage-account.md#configure-logging).

## <a name="set-up-advanced-threat-protection"></a>Einrichten von Advanced Threat Protection 

### <a name="using-the-portal"></a>Verwenden des Portals

1. Starten Sie das Azure-Portal unter [https://portal.azure.com](https://portal.azure.com/).

2. Navigieren Sie zur Konfigurationsseite des Azure Storage-Kontos, das Sie schützen möchten. Wählen Sie auf der Seite **Einstellungen** die Option **Advanced Threat Protection**.

3. Gehen Sie auf dem Konfigurationsblatt **Advanced Threat Protection** wie folgt vor:
    * Legen Sie *Advanced Threat Protection* auf **EIN** fest.
    * Klicken Sie auf **Speichern**, um die neue oder aktualisierte Advanced Threat Protection-Richtlinie zu speichern. (Die Preise in der Abbildung dienen nur als Beispiel.)

![Aktivieren von Azure Storage Advanced Threat Protection](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="using-azure-security-center"></a>Verwenden von Azure Security Center
Wenn Sie den Standard-Tarif in Azure Security Center abonnieren, wird Advanced Threat Protection in Ihren Speicherkonten eingerichtet. Weitere Informationen finden Sie unter [Upgrade auf den Standard-Tarif von Azure Security Center für erhöhte Sicherheit](https://docs.microsoft.com/azure/security-center/security-center-pricing). (Die Preise in der Abbildung dienen nur als Beispiel.)

![Standard-Tarif in ASC](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing.png)

### <a name="using-azure-resource-manager-templates"></a>Verwenden von Azure-Ressourcen-Manager-Vorlagen

Verwenden Sie zur Bereitstellung eines Azure Storage-Kontos, bei dem Advanced Threat Protection aktiviert ist, eine Azure Resource Manager-Vorlage.
Weitere Informationen finden Sie unter [Storage account with Advanced Threat Protection (Storage-Konto mit Advanced Threat Protection)](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/).

### <a name="using-azure-policy"></a>Verwenden von Azure Policy

Verwenden Sie eine Azure Policy-Instanz zum Aktivieren von Advanced Threat Protection für Speicherkonten unter einem bestimmten Abonnement oder einer bestimmten Ressourcengruppe.

1. Starten Sie die Azure-Seite **Richtlinie – Definitionen**.

1. Suchen Sie nach der Richtlinie **Advanced Threat Protection für Speicherkonten bereitstellen**.

     ![Richtlinie suchen](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)
  
1. Wählen Sie ein Azure-Abonnement oder eine Ressourcengruppe aus.

    ![Wählen Sie „Abonnement“ oder „Gruppe“ aus.](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Weisen Sie die Richtlinie zu.

    ![Seite „Richtliniendefinitionen“](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="using-rest-api"></a>Verwenden der REST-API
Verwenden Sie REST-API-Befehle, um die Advanced Threat Protection-Einstellung für ein bestimmtes Speicherkonto zu erstellen, zu aktualisieren oder abzurufen.

* [Advanced Threat Protection – Create (Erstellen)](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Advanced Threat Protection – Get (Abrufen)](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="using-azure-powershell"></a>Verwenden von Azure PowerShell

Verwenden Sie die folgenden PowerShell-Cmdlets:

  * [Enable Advanced Threat Protection (Advanced Threat Protection aktivieren)](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
  * [Get Advanced Threat Protection (Advanced Threat Protection abrufen)](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
  * [Disable Advanced Threat Protection (Advanced Threat Protection deaktivieren)](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

## <a name="explore-security-anomalies"></a>Untersuchen von sicherheitsrelevanten Anomalien

Wenn für die Speicheraktivität Anomalien auftreten, erhalten Sie eine E-Mail-Benachrichtigung mit Informationen zum verdächtigen Sicherheitsereignis. Einzelheiten des Ereignisses sind:

* Art der Anomalie
* Speicherkontoname
* Ereigniszeit
* Speichertyp
* Mögliche Ursachen 
* Untersuchungsschritte
* Schritte zur Bereinigung


Die E-Mail enthält auch Details zu möglichen Ursachen und empfohlenen Aktionen zur Untersuchung und Eindämmung der potenziellen Bedrohung.

![Azure Storage Advanced Threat Protection – Warnungs-E-Mail](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

Sie können Ihre aktuellen Sicherheitswarnungen in Azure Security Center über die [Kachel „Sicherheitswarnungen“](../../security-center/security-center-managing-and-responding-alerts.md#managing-security-alerts) anzeigen und verwalten. Durch Klicken auf eine bestimmte Warnung werden Details und Aktionen zum Untersuchen der aktuellen Bedrohung und zum Begegnen zukünftiger Bedrohungen bereitgestellt.

![Azure Storage Advanced Threat Protection – Warnungs-E-Mail](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="protection-alerts"></a>Protection-Warnungen

Warnungen werden bei ungewöhnlichen und potenziell schädlichen Zugriffsversuchen oder Exploit-Vorgängen für Speicherkonten generiert. Diese Ereignisse können die folgenden Warnungen auslösen:

### <a name="anomalous-access-pattern-alerts"></a>Warnungen zu ungewöhnlichen Zugriffsmustern

* **Zugriff von einem ungewöhnlichen Ort**: Diese Warnung wird ausgelöst, wenn sich am Zugriffsmuster eines Speicherkontos etwas ändert. Ein Beispiel hierfür ist der Fall, in dem eine Person von einem ungewöhnlichen geografischen Ort aus auf ein Speicherkonto zugreift.
Mögliche Ursachen:
   * Ein Angreifer hat auf Ihr Speicherkonto zugegriffen.
   * Ein berechtigter Benutzer hat von einem neuen Ort aus auf Ihr Speicherkonto zugegriffen.
 
* **Anwendungsanomalie**: Diese Warnung gibt an, dass auf dieses Speicherkonto über eine ungewöhnliche Anwendung zugegriffen wurde. Mögliche Ursachen:
   * Ein Angreifer hat mit einer neuen Anwendung auf Ihr Speicherkonto zugegriffen.
   * Ein berechtigter Benutzer hat für den Zugriff auf Ihr Speicherkonto eine neue Anwendung oder einen neuen Browser verwendet.

* **Anonymer Zugriff**: Diese Warnung gibt an, dass sich am Zugriffsmuster eines Speicherkontos etwas geändert hat. Auf dieses Konto wurde beispielsweise auf ungewöhnliche Weise (d. h. ohne Authentifizierung) zugegriffen, was im Vergleich zum letzten Zugriffsmuster für dieses Konto ein unerwartetes Verhalten ist.
Mögliche Ursachen:
   * Ein Angreifer hat den öffentlichen Lesezugriff auf einen Container ausgenutzt.
   * Ein berechtigter Benutzer oder eine berechtigte Anwendung hat den öffentlichen Lesezugriff auf einen Container genutzt.

### <a name="anomalous-extractupload-alerts"></a>Warnungen zu ungewöhnlichen Extrahierungs- oder Hochladevorgängen

* **Datenexfiltration**: Diese Warnung gibt an, dass eine große Datenmenge extrahiert wurde, die im Vergleich zur letzten Aktivität bei diesem Speichercontainer ungewöhnlich ist. Mögliche Ursachen:
   * Ein Angreifer hat eine große Datenmenge von einem Container extrahiert. (Beispiel: Datenexfiltration/Sicherheitsverletzung, nicht autorisierte Datenübertragung)
   * Ein berechtigter Benutzer oder eine berechtigte Anwendung hat eine ungewöhnlich große Datenmenge von einem Container extrahiert. (Beispiel: Wartungsaktivitäten)

* **Unerwarteter Löschvorgang**: Diese Warnung gibt an, dass in einem Speicherkonto mindestens ein Löschvorgang aufgetreten ist, der im Vergleich zur letzten Aktivität für dieses Konto unerwartet ist. Mögliche Ursachen:
   * Ein Angreifer hat Daten im Speicherkonto gelöscht.
   * Ein berechtigter Benutzer hat einen außergewöhnlichen Löschvorgang durchgeführt.

* **Upload eines Azure Cloud Service-Pakets**: Diese Warnung gibt an, dass ein Azure Cloud Service-Paket (CSPKG-Datei) auf eine Weise in ein Speicherkonto hochgeladen wurde, die im Vergleich zur letzten Aktivität für das Konto ungewöhnlich ist. Mögliche Ursachen: 
   * Ein Angreifer hat die Bereitstellung von bösartigem Code über Ihr Speicherkonto für einen Azure-Clouddienst vorbereitet.
   * Ein berechtigter Benutzer hat eine berechtigte Dienstbereitstellung vorbereitet.

### <a name="suspicious-storage-activities-alerts"></a>Warnungen zu verdächtigen Speicheraktivitäten

* **Änderung der Zugriffsberechtigung**: Diese Warnung gibt an, dass die Zugriffsberechtigungen für diesen Speichercontainer auf ungewöhnliche Weise geändert wurden. Mögliche Ursachen: 
   * Ein Angreifer hat die Berechtigungen für einen Container zur Herabsetzung der Sicherheit geändert.
   * Ein berechtigter Benutzer hat die Berechtigungen für einen Container geändert.

* **Überprüfung des Zugriffs**: Diese Warnung gibt an, dass die Zugriffsberechtigungen eines Speicherkontos auf eine Weise überprüft wurden,die im Vergleich zur letzten Aktivität für dieses Konto ungewöhnlich ist. Mögliche Ursachen: 
   * Ein Angreifer hat für einen späteren Angriff eine Reconnaissance durchgeführt.
   * Ein berechtigter Benutzer hat für das Speicherkonto Wartungsarbeiten durchgeführt.

* **Datenanalyse**: Diese Warnung gibt an, dass Blobs oder Container in einem Speicherkonto in einer Weise aufgezählt wurden, die im Vergleich zur letzten Aktivität für dieses Konto ungewöhnlich ist. Mögliche Ursachen: 
   * Ein Angreifer hat für einen späteren Angriff eine Reconnaissance durchgeführt.
   * Ein berechtigter Benutzer oder eine berechtigte Anwendungslogik hat Daten im Speicherkonto untersucht.






## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu [Protokollen in Azure Storage-Konten](/rest/api/storageservices/About-Storage-Analytics-Logging)

* Weitere Informationen zu [Azure Security Center](../../security-center/security-center-intro.md)
