---
title: Verwalten von Geräten über das Azure-Portal | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Geräte über das Azure-Portal verwalten.
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2019
ms.author: joflore
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48f162e5e6eb29e4a658000826ccf25389086342
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730433"
---
# <a name="manage-device-identity-using-the-azure-portal"></a>Verwalten der Geräteidentität mithilfe des Azure-Portals

Mit der Geräteidentitätsverwaltung in Azure Active Directory (Azure AD) können Sie sicherstellen, dass Benutzer auf Ihre Ressourcen über Geräte zugreifen, die Ihren Standards für Sicherheit und Konformität entsprechen.

Dieser Artikel:

- Setzt voraus, dass Sie die [Einführung in die Geräteidentitätsverwaltung in Azure Active Directory](overview.md) gelesen haben.
- Enthält Informationen zum Verwalten Ihrer Geräteidentitäten mithilfe des Azure AD-Portals.

## <a name="manage-device-identities"></a>Verwalten von Geräteidentitäten

Das Azure AD-Portal bietet Ihnen einen zentralen Ort zum Verwalten Ihrer Geräteidentitäten. Sie können über einen [direkten Link](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) oder mithilfe der folgenden manuellen Schritte zu diesem zentralen Ort gelangen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Administrator an.
2. Klicken Sie auf der linken Navigationsleiste auf **Active Directory**.

   ![Konfigurieren der Geräteeinstellungen](./media/device-management-azure-portal/01.png)

3. Klicken Sie im Bereich **Verwalten** auf **Geräte**.

   ![Konfigurieren der Geräteeinstellungen](./media/device-management-azure-portal/74.png)

Auf der Seite **Geräte** können Sie folgende Aktivitäten ausführen:

- Konfigurieren Ihrer Geräteeinstellungen
- Suchen nach Geräten
- Ausführen von Geräteidentitätsverwaltungsaufgaben
- Überprüfen gerätebezogener Überwachungsprotokolle  
  
## <a name="configure-device-settings"></a>Konfigurieren der Geräteeinstellungen

Damit Ihre Geräteidentitäten im Azure AD-Portal verwaltet werden können, müssen diese in Azure AD [registriert oder eingebunden](overview.md) (d.h. mit Azure AD verknüpft) werden. Als Administrator können Sie den Prozess der Registrierung und Verknüpfung von Geräten optimieren, indem Sie die Geräteeinstellungen konfigurieren.

![Konfigurieren der Geräteeinstellungen](./media/device-management-azure-portal/22.png)

Auf der Seite mit den Geräteeinstellungen können Sie Folgendes konfigurieren:

![Verwalten eines Intune-Geräts](./media/device-management-azure-portal/21.png)

- **Benutzer dürfen Geräte in Azure AD einbinden**: Diese Einstellung ermöglicht Ihnen die Auswahl der Benutzer, die Ihre Geräte als [In Azure AD eingebundene Geräte](overview.md#azure-ad-joined-devices)registrieren können. Die Standardeinstellung ist **Alle**.

>[!NOTE]
> **Benutzer dürfen Geräte in Azure AD einbinden**: Diese Einstellung gilt nur für die Azure AD-Einbindung unter Windows 10.

- **Weitere lokale Administratoren für in Azure AD eingebundene Geräte**: Sie können die Benutzer auswählen, denen lokale Administratorrechte auf einem Gerät erteilt werden. Hier hinzugefügte Benutzer werden der Rolle *Geräteadministratoren* in Azure AD hinzugefügt. Globale Administratoren in Azure AD und Gerätebesitzer erhalten standardmäßig lokale Administratorrechte. Diese Option ist eine Premium Edition-Funktion, die über Produkte wie Azure AD Premium oder die Enterprise Mobility Suite (EMS) zur Verfügung steht.
- **Benutzer dürfen ihre Geräte für Azure AD registrieren**: Sie müssen diese Einstellung konfigurieren, um die [Registrierung](overview.md#azure-ad-registered-devices) von Windows 10-, persönlichen, iOS-, Android- und macOs-Geräten in Azure AD zuzulassen. Bei Auswahl von **Keine** dürfen Geräte nicht bei Azure AD registriert werden. Für die Registrierung bei Microsoft Intune oder der mobilen Geräteverwaltung für Office 365 ist eine Registrierung erforderlich. Wenn Sie einen dieser Dienste konfiguriert haben, wird **ALLE** ausgewählt, und die Option **KEINE** ist nicht verfügbar.
- **Multi-factor Auth zum Hinzufügen von Geräten erforderlich**: Sie können auswählen, ob Benutzer einen sekundären Authentifizierungsfaktor bereitstellen müssen, um ihr Gerät in [Azure AD](overview.md#azure-ad-joined-devices) einzubinden. Der Standardwert lautet **Nein**. Es wird empfohlen, beim Registrieren eines Geräts die mehrstufige Authentifizierung zu verwenden. Bevor Sie die mehrstufige Authentifizierung für diesen Dienst aktivieren, müssen Sie sicherstellen, dass sie für die Benutzer konfiguriert ist, die ihre Geräte registrieren. Weitere Informationen zu verschiedenen Azure-Diensten mit mehrstufiger Authentifizierung finden Sie in den [ersten Schritten mit der mehrstufigen Azure-Authentifizierung](../authentication/concept-mfa-whichversion.md). 

>[!NOTE]
> **Mehrstufige Authentifizierung zum Hinzufügen von Geräten erforderlich**: Diese Einstellung gilt nicht für in Azure AD Hybrid eingebundene Geräte.

- **Maximale Anzahl von Geräten pro Benutzer**: Mit dieser Einstellung können Sie die maximale Anzahl von Geräten festlegen, die ein Benutzer in Azure AD haben kann. Wenn ein Benutzer diese Anzahl erreicht, kann er keine weiteren Geräte hinzufügen, bis mindestens eines der vorhandenen Geräte entfernt wird. Das Gerätekontingent wird für alle Geräte gezählt, die heute mit Azure AD verknüpft oder in Azure AD registriert sind. Der Standardwert ist **20**.

>[!NOTE]
> **Maximale Anzahl von Geräten**: Diese Einstellung gilt nicht für in Azure AD Hybrid eingebundene Geräte.

- **Benutzer können Einstellungen und App-Daten geräteübergreifend synchronisieren**: Diese Einstellung ist standardmäßig auf **KEINE** festgelegt. Wenn Sie bestimmte Benutzer oder Gruppen oder „ALLE“ auswählen, können die Einstellungen und App-Daten der Benutzer auf ihren Windows 10-Geräten synchronisiert werden. Erfahren Sie mehr über die Funktionsweise der Synchronisierung in Windows 10.
Diese Option ist eine Premium-Funktion, die über Produkte wie Azure AD Premium oder die Enterprise Mobility Suite (EMS) zur Verfügung steht.

## <a name="locate-devices"></a>Suchen nach Geräten

Sie haben zwei Optionen, um nach registrierten und eingebundenen Geräten zu suchen:

- **Alle Geräte** im Abschnitt **Verwalten** der Seite **Geräte**  

   ![Alle Geräte](./media/device-management-azure-portal/41.png)

- **Geräte** im Abschnitt **Verwalten** der Seite **Benutzer**

   ![Alle Geräte](./media/device-management-azure-portal/43.png)

Bei beiden Optionen erhalten Sie eine Ansicht, die:

- Ihnen die Suche nach Geräten mit dem Anzeigenamen als Filter ermöglicht.
- Eine detaillierte Übersicht über registrierte und verknüpfte Geräte enthält.
- Ihnen die Durchführung allgemeiner Geräteverwaltungsaufgaben ermöglicht.

![Alle Geräte](./media/device-management-azure-portal/51.png)

Für einige iOS-Geräte werden in Gerätenamen, die Apostrophe enthalten, möglicherweise andere Zeichen verwendet, die wie Apostrophe aussehen. Die Suche nach solchen Geräte ist daher ein wenig kompliziert. Wenn Ihnen Suchergebnisse nicht ordnungsgemäß angezeigt werden, stellen Sie sicher, dass die Suchzeichenfolge ein Zeichen enthält, das einem Apostroph entspricht.

## <a name="device-identity-management-tasks"></a>Geräteidentitätsverwaltungsaufgaben

Als globaler Administrator oder Cloudgeräteadministrator können Sie die registrierten oder verknüpften Geräte verwalten. Intune-Dienstadministratoren können folgende Aktionen ausführen:

- Aktualisieren von Geräten: Beispiele sind tägliche Vorgänge wie das Aktivieren/Deaktivieren von Geräten.
- Löschen von Geräten: Ein Gerät wird außer Betrieb genommen und muss in Azure AD gelöscht werden.

Dieser Abschnitt enthält Informationen zu allgemeinen Geräteidentitätsverwaltungsaufgaben.

### <a name="manage-an-intune-device"></a>Verwalten eines Intune-Geräts

Wenn Sie ein Intune-Administrator sind, können Sie Geräte verwalten, die mit der Kennzeichnung **Microsoft Intune** versehen sind.

![Verwalten eines Intune-Geräts](./media/device-management-azure-portal/31.png)

### <a name="enable--disable-an-azure-ad-device"></a>Aktivieren/Deaktivieren von Azure AD-Geräten

Sie haben zwei Optionen, um ein Gerät zu aktivieren bzw. zu deaktivieren:

- Aufgabenmenü („...“) auf der Seite **Alle Geräte**

   ![Verwalten eines Intune-Geräts](./media/device-management-azure-portal/71.png)

- Symbolleiste auf der Seite **Geräte**

   ![Verwalten eines Intune-Geräts](./media/device-management-azure-portal/32.png)

**Hinweise:**

- Zum Aktivieren oder Deaktivieren eines Geräts müssen Sie ein globaler Administrator oder ein Cloudgeräteadministrator in Azure AD sein. 
- Durch die Aktivierung eines Geräts wird seine erfolgreiche Authentifizierung mit Azure AD verhindert. Dadurch wird wiederum verhindert, dass das Gerät auf Ihre Azure AD-Ressourcen zugreift, die durch eine Gerätezertifizierungsstelle oder mithilfe Ihrer WH4B-Anmeldeinformationen geschützt werden.

### <a name="delete-an-azure-ad-device"></a>Löschen eines Azure AD-Geräts

Sie haben zwei Optionen, um ein Gerät zu löschen:

- Aufgabenmenü („...“) auf der Seite **Alle Geräte**

   ![Verwalten eines Intune-Geräts](./media/device-management-azure-portal/72.png)

- Symbolleiste auf der Seite **Geräte**

   ![Gerät löschen](./media/device-management-azure-portal/34.png)

**Hinweise:**

- Zum Löschen eines Geräts müssen Sie ein globaler Administrator oder ein Intune-Administrator in Azure AD sein.
- Das Löschen eines Geräts:
   - Verhindert, dass über das Gerät auf Ihre Azure AD-Ressourcen zugegriffen wird
   - Entfernt alle Details, die an das Gerät angefügt wurden, z.B. BitLocker-Schlüssel für Windows-Geräte  
   - Stellt eine Aktivität dar, die nicht rückgängig gemacht werden kann, und wird nur empfohlen, wenn das Löschen notwendig ist

Wenn ein Gerät von einer anderen Verwaltungsautorität (z.B. Microsoft Intune) verwaltet wird, sollten Sie vor dem Löschen des Geräts in Azure AD sicherstellen, dass es zurückgesetzt bzw. außer Kraft gesetzt wurde.

### <a name="view-or-copy-device-id"></a>Anzeigen oder Kopieren einer Geräte-ID

Sie können während der Problembehandlung eine Geräte-ID oder PowerShell verwenden, um die ID-Details eines Geräts zu überprüfen. Klicken Sie auf das Gerät, um den Kopierbefehl anzuzeigen.

![Anzeigen der Geräte-ID](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>Anzeigen oder Kopieren von BitLocker-Schlüsseln

Sie können die BitLocker-Schlüssel anzeigen und kopieren, um Benutzern die Wiederherstellung verschlüsselter Laufwerke zu ermöglichen. Diese Schlüssel sind nur für Windows-Geräte verfügbar, die verschlüsselt sind und deren Schlüssel in Azure AD gespeichert sind. Sie können die Schlüssel kopieren, wenn Sie auf die Details des Geräts zugreifen.

![BitLocker-Schlüssel anzeigen](./media/device-management-azure-portal/36.png)

Zum Anzeigen oder Kopieren der BitLocker-Schlüssel müssen Sie entweder der Besitzer des Geräts oder ein Benutzer sein, dem mindestens eine der folgenden Rollen zugewiesen ist:

- Globaler Administrator
- Helpdeskadministrator
- Sicherheitsadministrator
- Sicherheitsleseberechtigter
- Intune-Dienstadministrator

> [!NOTE]
> In Hybrid-Azure AD eingebundene Windows 10-Geräte haben keinen Besitzer. Wenn Sie also ein Gerät nach Besitzer suchen und es nicht finden, suchen Sie nach der Geräte-ID.

## <a name="audit-logs"></a>Überwachungsprotokolle

Geräteaktivitäten sind über die Aktivitätsprotokolle verfügbar. Diese Protokolle enthalten vom Geräteregistrierungsdienst und von Benutzern ausgelöste Aktivitäten:

- Erstellen von Geräten und Hinzufügen von Besitzern/Benutzern auf dem Gerät
- Änderungen der Geräteeinstellungen
- Gerätevorgänge wie beispielsweise das Löschen oder Aktualisieren eines Geräts

Ihr Einstiegspunkt für die Überwachungsdaten ist die Option **Überwachungsprotokolle** im Abschnitt **Aktivität** der Seite **Geräte**.

![Überwachungsprotokolle](./media/device-management-azure-portal/61.png)

Ein Überwachungsprotokoll enthält eine Standardlistenansicht mit folgenden Informationen:

- Datum und Uhrzeit des Auftretens
- Ziele
- Initiator/Akteur einer Aktivität (Wer)
- Aktivität (Was)

![Überwachungsprotokolle](./media/device-management-azure-portal/63.png)

Sie können die Listenansicht anpassen, indem Sie in der Symbolleiste auf **Spalten** klicken.

![Überwachungsprotokolle](./media/device-management-azure-portal/64.png)

Sie können die Überwachungsdaten mit den folgenden Feldern filtern, um die gemeldeten Daten gemäß Ihren Bedürfnissen einzugrenzen:

- Category (Kategorie)
- Aktivitätsressourcentyp
- Aktivität
- Datumsbereich
- Ziel
- Initiiert von (Akteur)

Zusätzlich zu den Filtern können Sie nach bestimmten Einträgen suchen.

![Überwachungsprotokolle](./media/device-management-azure-portal/65.png)

## <a name="next-steps"></a>Nächste Schritte

[Verwalten von veralteten Geräten in Azure AD](manage-stale-devices.md)
