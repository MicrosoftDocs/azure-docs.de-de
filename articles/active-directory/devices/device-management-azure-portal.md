---
title: Verwalten von Geräten über das Azure-Portal | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Geräte über das Azure-Portal verwalten.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 09/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: hafowler
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4077ade7de93470aaf03acef1dc1cefca8bd3d4
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94837361"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Verwalten der Geräteidentität mithilfe des Azure-Portals

Azure AD bietet Ihnen einen zentralen Ort zum Verwalten von Geräteidentitäten.

Auf der Seite **Alle Geräte** können Sie folgende Aktivitäten ausführen:

- Identifizieren von Geräten, einschließlich der folgenden:
   - Geräte, die in Azure AD eingebunden oder registriert sind
   - Geräte, die mithilfe von [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) bereitgestellt wurden
   - Drucker mit [Universal Print](/universal-print/fundamentals/universal-print-getting-started)
- Führen Sie Aufgaben zur Verwaltung von Geräteidentitäten wie das Aktivieren, Deaktivieren, Löschen oder Verwalten aus.
   - [Drucker](/universal-print/fundamentals/) und [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)-Geräte verfügen in Azure AD über eingeschränkte Verwaltungsoptionen. Sie müssen über die entsprechenden Verwaltungsoberflächen verwaltet werden.
- Konfigurieren Sie Ihre Geräteidentitätseinstellungen.
- Aktivieren oder deaktivieren Sie Enterprise State Roaming.
- Überprüfen gerätebezogener Überwachungsprotokolle

[![Ansicht „Alle Geräte“ im Azure-Portal](./media/device-management-azure-portal/all-devices-azure-portal.png)](./media/device-management-azure-portal/all-devices-azure-portal.png#lightbox)

Sie können mit folgenden Schritten auf das Geräteportal zugreifen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu **Azure Active Directory** > **Geräte**.

## <a name="manage-devices"></a>Verwalten von Geräten

Es gibt zwei Orte für die Verwaltung von Geräten in Azure AD:

- **Azure-Portal** > **Azure Active Directory** > **Geräte**
- **Azure-Portal** > **Azure Active Directory** > **Benutzer** > Wählen Sie einen Benutzer aus > **Geräte**

Beide Optionen ermöglichen Administratoren Folgendes:

- Suchen von Geräten
- Anzeigen von Gerätedetails, einschließlich:
    - Mediumname
    - Geräte-ID
    - Betriebssystem und Version
    - Join-Typ
    - Besitzer
    - Mobile Geräteverwaltung und Compliance
    - BitLocker-Wiederherstellungsschlüssel
- Führen Sie Aufgaben zur Verwaltung von Geräteidentitäten wie das Aktivieren, Deaktivieren, Löschen oder Verwalten aus.
   - [Drucker](/universal-print/fundamentals/) und [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)-Geräte verfügen in Azure AD über eingeschränkte Verwaltungsoptionen. Sie müssen über die entsprechenden Verwaltungsoberflächen verwaltet werden.

> [!TIP]
> - In Hybrid-Azure AD eingebundene Windows 10-Geräte haben keinen Besitzer. Wenn Sie ein Gerät nach Besitzer suchen und es nicht finden, suchen Sie nach der Geräte-ID.
>
> - Wenn in der Spalte „REGISTRIERT“ ein Gerät mit dem Zusatz „In Hybrid-Azure AD eingebunden“ und dem Zustand „Ausstehend“ angezeigt wird, deutet das darauf hin, dass das Gerät von Azure AD Connect synchronisiert wurde und auf die vollständige Registrierung vom Client wartet. Weitere Informationen finden Sie unter [Planen der Implementierung einer Azure Active Directory-Hybrideinbindung](hybrid-azuread-join-plan.md). Weitere Informationen finden Sie im Artikel mit [häufig gestellten Fragen zu Geräten](faq.md).
>
> - Für einige iOS-Geräte werden in Gerätenamen, die Apostrophe enthalten, möglicherweise andere Zeichen verwendet, die wie Apostrophe aussehen. Die Suche nach solchen Geräte ist daher ein wenig kompliziert. Wenn Ihnen Suchergebnisse nicht ordnungsgemäß angezeigt werden, stellen Sie sicher, dass die Suchzeichenfolge ein Zeichen enthält, das einem Apostroph entspricht.

### <a name="manage-an-intune-device"></a>Verwalten eines Intune-Geräts

Wenn Sie ein Intune-Administrator sind, können Sie Geräte verwalten, deren MDM mit **Microsoft Intune** gekennzeichnet ist. Wenn das Gerät nicht bei Microsoft Intune registriert ist, wird die Option „Verwalten“ abgeblendet angezeigt.

### <a name="enable-or-disable-an-azure-ad-device"></a>Aktivieren oder Deaktivieren von Azure AD-Geräten

Sie haben zwei Optionen, um Geräte zu aktivieren bzw. zu deaktivieren:

- Die Symbolleiste auf der Seite **Alle Geräte**, nachdem Sie ein oder mehrere Geräte ausgewählt haben
- Die Symbolleiste nach dem Drilldown auf ein bestimmtes Gerät

> [!IMPORTANT]
> - Zum Aktivieren oder Deaktivieren eines Geräts müssen Sie ein globaler Administrator oder ein Cloudgeräteadministrator in Azure AD sein. 
> - Durch die Deaktivierung eines Geräts wird seine erfolgreiche Authentifizierung mit Azure AD verhindert. Dadurch wird wiederum verhindert, dass das Gerät auf Ihre Azure AD-Ressourcen zugreift, die durch gerätebasierten bedingten Zugriff oder mithilfe Ihrer Anmeldeinformationen für Windows Hello for Business geschützt werden.
> - Durch Deaktivieren eines Geräts werden sowohl das primäre Aktualisierungstoken (Primary Refresh Token, PRT) als auch etwaige Aktualisierungstoken (Refresh Tokens, RT) auf dem Gerät widerrufen.
> - Drucker können nicht in Azure AD aktiviert oder deaktiviert werden.

### <a name="delete-an-azure-ad-device"></a>Löschen eines Azure AD-Geräts

Sie haben zwei Optionen, um ein Gerät zu löschen:

- Die Symbolleiste auf der Seite **Alle Geräte**, nachdem Sie ein oder mehrere Geräte ausgewählt haben
- Die Symbolleiste nach dem Drilldown auf ein bestimmtes Gerät

> [!IMPORTANT]
> - Zum Löschen eines Geräts muss Ihnen in Azure AD eine der Rollen „Cloudgeräteadministrator“, „Intune-Administrator“ oder „Globaler Administrator“ zugewiesen sein.
> - Drucker und Windows Autopilot-Geräte können in Azure AD nicht gelöscht werden.
> - Das Löschen eines Geräts:
>    - Verhindert, dass über das Gerät auf Ihre Azure AD-Ressourcen zugegriffen wird
>    - Entfernt alle Details, die an das Gerät angefügt wurden, z.B. BitLocker-Schlüssel für Windows-Geräte  
>    - Stellt eine Aktivität dar, die nicht rückgängig gemacht werden kann, und wird nur empfohlen, wenn das Löschen notwendig ist

Wenn ein Gerät von einer anderen Verwaltungsautorität (z.B. Microsoft Intune) verwaltet wird, sollten Sie vor dem Löschen des Geräts in Azure AD sicherstellen, dass es zurückgesetzt bzw. außer Kraft gesetzt wurde. Informieren Sie sich über das [Verwalten veralteter Geräte](manage-stale-devices.md), bevor Sie Geräte löschen.

### <a name="view-or-copy-device-id"></a>Anzeigen oder Kopieren einer Geräte-ID

Sie können während der Problembehandlung eine Geräte-ID oder PowerShell verwenden, um die ID-Details eines Geräts zu überprüfen. Klicken Sie auf das Gerät, um den Kopierbefehl anzuzeigen.

![Anzeigen der Geräte-ID](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Anzeigen oder Kopieren von BitLocker-Schlüsseln

Sie können die BitLocker-Schlüssel anzeigen und kopieren, um Benutzern die Wiederherstellung verschlüsselter Laufwerke zu ermöglichen. Diese Schlüssel sind nur für Windows-Geräte verfügbar, die verschlüsselt sind und deren Schlüssel in Azure AD gespeichert sind. Sie finden diese Schlüssel beim Zugriff auf die Details eines Geräts, indem Sie **Wiederherstellungsschlüssel anzeigen** auswählen. Wenn Sie **Wiederherstellungsschlüssel anzeigen** auswählen, wird ein Überwachungsprotokoll generiert, das Sie in der Kategorie `KeyManagement` finden.

![BitLocker-Schlüssel anzeigen](./media/device-management-azure-portal/device-details-show-bitlocker-key.png)

Zum Anzeigen oder Kopieren der BitLocker-Schlüssel müssen Sie entweder der Besitzer des Geräts oder ein Benutzer sein, dem mindestens eine der folgenden Rollen zugewiesen ist:

- Cloudgeräteadministrator
- Globaler Administrator
- Helpdeskadministrator
- Intune-Dienstadministrator
- Sicherheitsadministrator
- Sicherheitsleseberechtigter

### <a name="device-list-filtering-preview"></a>Filtern der Geräteliste (Vorschau)

Bisher konnten Sie die Geräteliste nur nach Aktivität und Aktivierungszustand filtern. In dieser Vorschauversion können Sie nun die Geräteliste nach den folgenden Attributen eines Geräts filtern:

- Aktivierungszustand
- Konformitätszustand
- Jointyp (in Azure AD eingebunden, hybrid in Azure AD eingebunden, in Azure AD registriert)
- Aktivitätszeitstempel
- OS
- Gerätetyp (Drucker, sichere virtuelle Computer, freigegebene Geräte, registrierte Geräte)

So aktivieren Sie die Filterfunktionen (Vorschau) in der Ansicht **Alle Geräte**

![Aktivieren der Filterfunktionen (Vorschau)](./media/device-management-azure-portal/device-filter-preview-enable.png)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Navigieren Sie zu **Azure Active Directory** > **Geräte**.
1. Wählen Sie das Banner aus, das besagt: **Testen Sie die Verbesserungen des neuen Gerätefilters. Klicken Sie hier, um die Vorschau zu aktivieren.**

Sie haben jetzt die Möglichkeit, mit **Filter hinzufügen** der Ansicht **Alle Geräte** Filter hinzuzufügen.

## <a name="configure-device-settings"></a>Konfigurieren der Geräteeinstellungen

Damit Geräteidentitäten im Azure AD-Portal verwaltet werden können, müssen diese in Azure AD [registriert oder eingebunden](overview.md) werden. Als Administrator können Sie den Prozess der Registrierung und Einbindung von Geräten steuern, indem Sie die Geräteeinstellungen konfigurieren.

Sie müssen einer der folgenden Rollen zugewiesen sein, um die Geräteeinstellungen im Azure-Portal anzeigen oder verwalten zu können:

- Globaler Administrator
- Cloudgeräteadministrator
- Globaler Leser
- Verzeichnisleseberechtigter

![Geräteeinstellungen in Verbindung mit Azure AD](./media/device-management-azure-portal/device-settings-azure-portal.png)

- **Benutzer dürfen Geräte in Azure AD einbinden**: Diese Einstellung ermöglicht Ihnen die Auswahl der Benutzer, die ihre Geräte als in Azure AD eingebundene Geräte registrieren können. Die Standardeinstellung ist **Alle**.

> [!NOTE]
> **Benutzer dürfen Geräte in Azure AD einbinden**: Diese Einstellung gilt nur für die Azure AD-Einbindung unter Windows 10.

- **Weitere lokale Administratoren für in Azure AD eingebundene Geräte**: Sie können die Benutzer auswählen, denen lokale Administratorrechte auf einem Gerät erteilt werden. Diese Benutzer werden der Rolle *Geräteadministratoren* in Azure AD hinzugefügt. Globale Administratoren in Azure AD und Gerätebesitzer erhalten standardmäßig lokale Administratorrechte. Diese Option ist eine Premium Edition-Funktion, die über Produkte wie Azure AD Premium oder die Enterprise Mobility Suite (EMS) zur Verfügung steht.
- **Benutzer dürfen ihre Geräte für Azure AD registrieren**: Sie müssen diese Einstellung konfigurieren, um die Registrierung von Windows 10-, persönlichen, iOS-, Android- und macOS-Geräten in Azure AD zuzulassen. Bei Auswahl von **Keine** dürfen Geräte nicht bei Azure AD registriert werden. Für die Registrierung bei Microsoft Intune oder der mobilen Geräteverwaltung (MDM) für Microsoft 365 ist eine Registrierung erforderlich. Wenn Sie einen dieser Dienste konfiguriert haben, wird **ALLE** ausgewählt, und die Option **KEINE** ist nicht verfügbar.
- **Multi-Factor Auth zum Hinzufügen von Geräten erforderlich**: Sie können auswählen, ob Benutzer einen zusätzlichen Authentifizierungsfaktor bereitstellen müssen, um ihr Gerät in Azure AD einzubinden. Der Standardwert ist **No**. Es wird empfohlen, beim Registrieren eines Geräts die mehrstufige Authentifizierung zu verwenden. Bevor Sie die mehrstufige Authentifizierung für diesen Dienst aktivieren, müssen Sie sicherstellen, dass sie für die Benutzer konfiguriert ist, die ihre Geräte registrieren. Weitere Informationen zu den verschiedenen Azure AD Multi-Factor Authentication-Diensten finden Sie unter [Erste Schritte mit Azure AD Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md). 

> [!NOTE]
> Die Einstellung **Mehrstufige Authentifizierung zum Hinzufügen von Geräten erforderlich** gilt für Geräte, die entweder in Azure AD eingebunden oder bei Azure AD registriert sind. Diese Einstellung gilt nicht für in Azure AD Hybrid eingebundene Geräte.

- **Maximale Anzahl von Geräten pro Benutzer**: Mit dieser Einstellung können Sie die maximale Anzahl von in Azure AD eingebundenen oder bei Azure AD registrierten Geräten festlegen, die ein Benutzer in Azure AD verwenden kann. Wenn ein Benutzer diese Anzahl erreicht, kann er keine weiteren Geräte hinzufügen, bis mindestens eines der vorhandenen Geräte entfernt wird. Der Standardwert lautet **50**.

> [!NOTE]
> Die Einstellung **Maximale Anzahl von Geräten pro Benutzer** gilt für Geräte, die entweder in Azure AD eingebunden oder bei Azure AD registriert sind. Diese Einstellung gilt nicht für in Azure AD Hybrid eingebundene Geräte.

- [Enterprise State Roaming](enterprise-state-roaming-overview.md)

## <a name="audit-logs"></a>Überwachungsprotokolle

Geräteaktivitäten sind über die Aktivitätsprotokolle verfügbar. Diese Protokolle enthalten vom Geräteregistrierungsdienst und von Benutzern ausgelöste Aktivitäten:

- Erstellen von Geräten und Hinzufügen von Besitzern/Benutzern auf dem Gerät
- Änderungen der Geräteeinstellungen
- Gerätevorgänge wie beispielsweise das Löschen oder Aktualisieren eines Geräts

Ihr Einstiegspunkt für die Überwachungsdaten ist die Option **Überwachungsprotokolle** im Abschnitt **Aktivität** der Seite **Geräte**.

Das Überwachungsprotokoll enthält eine Standardlistenansicht mit folgenden Informationen:

- Datum und Uhrzeit des Auftretens
- Ziele
- Initiator/Akteur einer Aktivität (Wer)
- Aktivität (Was)

:::image type="content" source="./media/device-management-azure-portal/63.png" alt-text="Screenshot: Tabelle im Abschnitt „Aktivität“ der Seite „Geräte“. Die Tabelle enthält Datum, Ziel, Akteur und Aktivität für vier Überwachungsprotokolle." border="false":::

Sie können die Listenansicht anpassen, indem Sie in der Symbolleiste auf **Spalten** klicken.

:::image type="content" source="./media/device-management-azure-portal/64.png" alt-text="Screenshot: Symbolleiste auf der Seite „Geräte“. Das Element „Spalten“ ist hervorgehoben." border="false":::

Sie können die Überwachungsdaten mit den folgenden Feldern filtern, um die gemeldeten Daten gemäß Ihren Bedürfnissen einzugrenzen:

- Category
- Aktivitätsressourcentyp
- Aktivität
- Datumsbereich
- Ziel
- Initiiert von (Akteur)

Zusätzlich zu den Filtern können Sie nach bestimmten Einträgen suchen.

:::image type="content" source="./media/device-management-azure-portal/65.png" alt-text="Screenshot: Filtersteuerelemente für Überwachungsdaten mit Feldern für Kategorie, Aktivitätsressourcentyp, Aktivität, Datumsbereich, Ziel und Akteur sowie einem Suchfeld" border="false":::

## <a name="next-steps"></a>Nächste Schritte

[Verwalten von veralteten Geräten in Azure AD](manage-stale-devices.md)

[Enterprise State Roaming](enterprise-state-roaming-overview.md)