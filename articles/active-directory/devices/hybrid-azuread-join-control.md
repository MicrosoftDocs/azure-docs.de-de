---
title: Kontrollierte Überprüfung von Azure AD Hybrid Join – Azure AD
description: Hier erfahren Sie, wie Sie eine kontrollierte Überprüfung von Azure AD Hybrid Join vornehmen, bevor Sie den Dienst in der gesamten Organisation aktivieren.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38754b9e349e27afcff58dac27a616e3e4fb5319
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860931"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Kontrollierte Überprüfung der Azure AD-Hybrideinbindung

Wenn alle Voraussetzungen erfüllt sind, werden Windows-Geräte automatisch als Geräte bei Ihrem Azure AD-Mandanten registriert. Der Zustand dieser Geräteidentitäten in Azure AD wird als Azure AD Hybrid Join bezeichnet. Weitere Informationen zu den in diesem Artikel beschriebenen Konzepten finden Sie in den Artikeln [Was ist eine Geräteidentität?](overview.md) und [Anleitung: Planen der Implementierung einer Azure Active Directory-Hybrideinbindung](hybrid-azuread-join-plan.md).

Organisationen sollten eine kontrollierte Überprüfung von Azure AD Hybrid Join durchführen, bevor sie den Dienst in der gesamten Organisation aktivieren. In diesem Artikel erfahren Sie, wie Sie eine kontrollierte Überprüfung von Azure AD Hybrid Join durchführen.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Kontrollierte Überprüfung von Azure AD Hybrid Join für aktuelle Windows-Geräte

Für Geräte, auf denen das Windows-Desktopbetriebssystem ausgeführt wird, wird die Version Windows 10 Anniversary Update (Version 1607) oder höher unterstützt. Es wird empfohlen, ein Upgrade auf die aktuelle Version von Windows 10 durchzuführen.

Voraussetzungen für eine kontrollierte Überprüfung von Azure AD Hybrid Join für aktuelle Windows-Geräte:

1. Entfernen Sie den Eintrag des Dienstverbindungspunkts (Service Connection Point, SCP) aus Active Directory (AD), sofern vorhanden.
1. Konfigurieren Sie mithilfe eines Gruppenrichtlinienobjekts (Group Policy Object, GPO) die clientseitige Registrierungseinstellung für SCP auf Ihren in die Domäne eingebundenen Computern.
1. Bei Verwendung von AD FS müssen Sie mithilfe eines Gruppenrichtlinienobjekts auch die clientseitige Registrierungseinstellung für SCP auf Ihrem AD FS-Server konfigurieren.  
1. Möglicherweise müssen Sie auch in Azure AD Connect die [Synchronisierungsoptionen anpassen](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect), um die Gerätesynchronisierung zu aktivieren. 


### <a name="clear-the-scp-from-ad"></a>Entfernen des SCP aus AD

Verwenden Sie zum Ändern der SCP-Objekte in AD den Schnittstellen-Editor der Active Directory-Dienste (ADSI Edit).

1. Starten Sie die Desktopanwendung **ADSI Edit** als Unternehmensadministrator auf einer Verwaltungsarbeitsstation oder auf einem Domänencontroller.
1. Stellen Sie eine Verbindung mit dem **Konfigurationsnamenskontext** Ihrer Domäne her.
1. Navigieren Sie zu **CN=Configuration,DC=contoso,DC=com** > **CN=Services** > **CN=Device Registration Configuration**.
1. Klicken Sie mit der rechten Maustaste auf das Blattobjekt **CN=62a0ff2e-97b9-4513-943f-0d221bd30080**, und wählen Sie **Eigenschaften** aus.
   1. Wählen Sie im Fenster **Attribut-Editor** die Option **Schlüsselwörter** aus, und klicken Sie auf **Bearbeiten**
   1. Wählen Sie nacheinander die Werte **azureADId** und **azureADName** aus, und klicken Sie auf **Entfernen**
1. Schließen Sie **ADSI Edit**.


### <a name="configure-client-side-registry-setting-for-scp"></a>Konfigurieren der clientseitigen Registrierungseinstellung für SCP

Verwenden Sie das folgende Beispiel, um ein Gruppenrichtlinienobjekt (Group Policy Object, GPO) für die Bereitstellung einer Registrierungseinstellung zu erstellen, die in der Registrierung Ihrer Geräte einen SCP-Eintrag konfiguriert.

1. Öffnen Sie eine Gruppenrichtlinien-Verwaltungskonsole, und erstellen Sie ein neues Gruppenrichtlinienobjekt in Ihrer Domäne.
   1. Geben Sie Ihrem neu erstellten Gruppenrichtlinienobjekt einen Namen (beispielsweise „ClientSideSCP“).
1. Bearbeiten Sie das Gruppenrichtlinienobjekt, und suchen Sie nach dem folgenden Pfad: **Computerkonfiguration** > **Voreinstellungen** > **Windows-Einstellungen** > **Registrierung**
1. Klicken Sie mit der rechten Maustaste auf die Registrierung, und wählen Sie **Neu** > **Registrierungselement** aus.
   1. Konfigurieren Sie auf der Seite **Allgemein** Folgendes:
      1. Aktion: **Aktualisieren**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Schlüsselpfad: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Wertname: **TenantId**
      1. Werttyp: **REG_SZ**
      1. Wertdaten: Die GUID oder **Verzeichnis-ID** Ihrer Azure AD-Instanz. (Dieser Wert befindet sich unter **Azure-Portal** > **Azure Active Directory** > **Eigenschaften** > **Verzeichnis-ID**.)
   1. Klicken Sie auf **OK**
1. Klicken Sie mit der rechten Maustaste auf die Registrierung, und wählen Sie **Neu** > **Registrierungselement** aus.
   1. Konfigurieren Sie auf der Seite **Allgemein** Folgendes:
      1. Aktion: **Aktualisieren**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Schlüsselpfad: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Wertname: **TenantName**
      1. Werttyp: **REG_SZ**
      1. Wertdaten: Der überprüfte **Domänenname**, wenn Sie eine Verbundumgebung wie Active Directory-Verbunddienste (AD FS) verwenden. Der überprüfte **Domänenname** oder beispielsweise Ihr Domänenname auf „onmicrosoft.com“, `contoso.onmicrosoft.com`, wenn Sie eine verwaltete Umgebung verwenden.
   1. Klicken Sie auf **OK**
1. Schließen Sie den Editor für das neu erstellte Gruppenrichtlinienobjekt.
1. Verknüpfen Sie das neu erstellte Gruppenrichtlinienobjekt mit der gewünschten Organisationseinheit, die die in die Domäne eingebundenen Computer Ihrer kontrollierten Rolloutelemente enthält.

### <a name="configure-ad-fs-settings"></a>Konfigurieren von AD FS-Einstellungen

Bei Verwendung von AD FS müssen Sie gemäß der weiter oben angegebenen Anleitung zunächst den clientseitigen SCP konfigurieren. Dabei muss das Gruppenrichtlinienobjekt allerdings mit Ihren AD FS-Servern verknüpft werden. Das SCP-Objekt definiert die Autoritätsquelle für Geräteobjekte. Dabei kann es sich um die lokale Umgebung oder um Azure AD handeln. Wenn der clientseitige SCP für AD FS konfiguriert ist, wird die Quelle für Geräteobjekte als Azure AD eingerichtet.

> [!NOTE]
> Wenn Sie den clientseitigen SCP nicht auf Ihren AD FS-Servern konfigurieren konnten, wird die Quelle für Geräteidentitäten als lokal betrachtet. AD FS beginnt dann nach Ablauf des im Attribut „MaximumInactiveDays“ der AD FS-Geräteregistrierung festgelegten Zeitraums mit dem Löschen von Geräteobjekten aus dem lokalen Verzeichnis. AD FS-Geräteregistrierungsobjekte können mithilfe des [Cmdlets „Get-AdfsDeviceRegistration“](/powershell/module/adfs/get-adfsdeviceregistration) gefunden werden.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Kontrollierte Überprüfung von Azure AD Hybrid Join für kompatible Windows-Geräte

Zur Registrierung von kompatiblen Windows-Geräten müssen Organisationen [Microsoft Workplace Join für Computer installieren, auf denen nicht Windows 10 ausgeführt wird](https://www.microsoft.com/download/details.aspx?id=53554) (verfügbar im Microsoft Download Center).

Sie können das Paket mithilfe eines Softwareverteilungssystems wie  [Microsoft Endpoint Configuration Manager](/configmgr/) bereitstellen. Das Paket unterstützt die Standardoptionen für die Installation im Hintergrund unter Verwendung des quiet-Parameters. Configuration Manager Current Branch bietet zusätzliche Vorteile gegenüber früheren Versionen, z.B. die Möglichkeit zur Nachverfolgung abgeschlossener Registrierungen.

Das Installationsprogramm erstellt einen geplanten Task für das System, der im Kontext des Benutzers ausgeführt wird. Der Task wird ausgelöst, wenn sich der Benutzer bei Windows anmeldet. Nach der Authentifizierung durch Azure AD verknüpft die Aufgabe das Gerät unter Verwendung der Benutzeranmeldeinformationen mit Azure AD.

Zur Kontrolle der Registrierung dürfen Sie das Windows Installer-Paket nur für Ihre spezifische Gruppe kompatibler Windows-Geräte bereitstellen.

> [!NOTE]
> Ist in AD kein SCP konfiguriert, müssen Sie gemäß der Anleitung zum [Konfigurieren der clientseitigen Registrierungseinstellung für SCP](#configure-client-side-registry-setting-for-scp) vorgehen, um die Konfiguration auf Ihren in die Domäne eingebundenen Computern mithilfe eines Gruppenrichtlinienobjekts (Group Policy Object, GPO) vorzunehmen.


Nachdem Sie sich vergewissert haben, dass alles wie erwartet funktioniert, können Sie Ihre restlichen aktuellen und kompatiblen Windows-Geräte automatisch bei Azure AD registrieren, indem Sie [SCP mithilfe von Azure AD Connect konfigurieren](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Nächste Schritte

[Planen der Implementierung von Azure Active Directory (Azure AD) Hybrid Join](hybrid-azuread-join-plan.md)
