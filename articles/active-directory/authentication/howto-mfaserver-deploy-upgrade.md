---
title: Upgraden von Azure MFA Server – Azure Active Directory
description: In diesem Artikel finden Sie Anleitungen zum Upgraden des Azure Multi-Factor Authentication-Servers auf eine neuere Version.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/12/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1755404a06d8586968801aa22f2af532da278802
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742322"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Aktualisieren des Azure Multi-Factor Authentication-Servers

Dieser Artikel führt Sie durch den Upgradevorgang von Azure Multi-Factor Authentication (MFA) Server v6.0 oder höher. Wenn Sie eine alte Version des PhoneFactor-Agents aktualisieren müssen finden Sie weitere Information dazu unter [Upgraden des PhoneFactor-Agents auf den Azure Multi-Factor Authentication-Server](howto-mfaserver-deploy-upgrade-pf.md).

Wenn Sie ein Upgrade von v6.x oder niedriger auf v7.x oder höher durchführen, ändern sich alle Komponenten von .NET 2.0 auf .NET 4.5. Alle Komponenten erfordern auch Microsoft Visual C++ 2015 Redistributable Update 1 oder höher. Das Installationsprogramm für den MFA-Server installiert die Versionen x86 und x64, wenn sie nicht schon vorher installiert wurden. Wenn das Benutzerportal und der Webdienst der mobilen App auf verschiedenen Servern ausgeführt werden, müssen Sie diese Pakete installieren, bevor Sie diese Komponenten aktualisieren. Sie können im [Microsoft Download Center](https://www.microsoft.com/download/) nach dem aktuellsten Visual C++ 2015 Redistributable Update suchen. 

> [!IMPORTANT]
> Seit dem 1. Juli 2019 bietet Microsoft für neue Bereitstellungen keine MFA-Server mehr an. Neue Kunden, die für die Anmeldung der Benutzer mehrstufige Authentifizierung (MFA) anfordern möchten, sollten cloudbasierte Multi-Factor Authentication von Azure AD verwenden.
>
> Informationen zu den ersten Schritten mit der cloudbasierten MFA finden Sie im [Tutorial: Schützen von Benutzeranmeldeereignissen mit Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Bestandskunden, die ihren MFA-Server vor dem 1. Juli 2019 aktiviert haben, können weiterhin die neuesten Versionen und zukünftige Updates herunterladen sowie Anmeldedaten zur Aktivierung generieren.

Upgradeschritte auf einen Blick:

* Upgrade von Azure MFA-Servern (untergeordnete Server, dann Master)
* Upgrade der Benutzerportalinstanzen
* Upgrade der AD FS-Adapterinstanzen

## <a name="upgrade-azure-mfa-server"></a>Upgrade des Azure MFA-Servers

1. Befolgen Sie die Anweisungen in [Herunterladen des Azure Multi-Factor Authentication-Servers](howto-mfaserver-deploy.md#download-the-mfa-server), um die neueste Version des Azure MFA-Server-Installers zu erhalten.
2. Erstellen Sie eine Sicherung der Datendatei von MFA-Server, die sich unter „C:\Programme\Multi-Factor Authentication-Server\Daten\PhoneFactor.pfdata“ befindet (der Standard-Installationsspeicherort), auf Ihrem MFA-Masterserver.
3. Wenn Sie für Hochverfügbarkeit mehrere Server ausführen, ändern Sie die Clientsysteme, die eine Authentifizierung bei MFA-Server durchführen so, dass sie keinen Datenverkehr an die Server senden, die gerade aktualisiert werden. Wenn Sie einen Lastenausgleich verwenden, entfernen Sie einen untergeordneten MFA-Server aus dem Lastenausgleich, führen Sie das Upgrade durch, und fügen Sie den Server anschließend wieder zur Farm hinzu.
4. Führen Sie das neue Installationsprogramm für jeden MFA-Server aus. Aktualisieren Sie untergeordnete Server zuerst, da diese die alten Datendateien lesen können, die vom Master repliziert werden.

   > [!NOTE]
   > Beim Upgrade eines Servers muss dieser aus allen Aktivitäten im Zusammenhang mit Lastenausgleich und Austausch von Datenverkehr mit anderen MFA-Servern entfernt werden.
   >
   > Sie müssen Ihren aktuellen MFA-Server nicht deinstallieren, bevor Sie das Installationsprogramm ausführen. Das Installationsprogramm wird als direktes Upgrade ausgeführt. Der Installationspfad wird aus der Registrierung der vorherigen Installation übernommen. Die Installation erfolgt also unter dem gleichen Pfad (beispielsweise „C:\Programme\Multi-Factor Authentication-Server“).
  
5. Folgen Sie der Aufforderung, das Microsoft Visual C++ 2015 Redistributable-Updatepaket zu installieren. Es werden sowohl die x86-Versionen als auch die x64-Versionen des Pakets installiert.
6. Wenn Sie das Webdienst-SDK verwenden, werden Sie aufgefordert, das neue Webdienst-SDK zu installieren. Wenn Sie das neue Webdienst-SDK installieren, müssen Sie sichergehen, dass der Name des virtuellen Verzeichnisses dem vorher installierten virtuellen Verzeichnis entspricht (z.B. MultiFactorAuthWebServiceSdk).
7. Wiederholen Sie diese Schritte auf allen untergeordneten Servern. Stufen Sie einen der untergeordneten Server zum neuen Master herauf, und aktualisieren Sie dann den alten Masterserver.

## <a name="upgrade-the-user-portal"></a>Aktualisieren des Benutzerportals

Schließen Sie das Upgrade Ihrer MFA-Server ab, bevor Sie mit diesem Abschnitt fortfahren.

1. Erstellen Sie eine Sicherung der Datei „web.config“, die sich im virtuellen Verzeichnis am Speicherort des Benutzerportals befindet (z.B. „C:\inetpub\wwwroot\MultiFactorAuth“). Wenn Sie Änderungen am Standarddesign vorgenommen haben, erstellen Sie auch eine Sicherung des Ordners „App_Themes\Standard“. Es ist besser, eine Kopie des Ordners „Standard“ und ein neues Design zu erstellen, anstatt das Standarddesign zu verändern.
2. Wenn das Benutzerportal auf dem gleichen Server ausgeführt wird wie die anderen MFA-Server-Komponenten, werden Sie bei der Installation von MFA-Server dazu aufgefordert, ein Update des Benutzerportals durchzuführen. Folgen Sie der Aufforderung und installieren Sie das Update des Benutzerportals. Überprüfen Sie dabei, dass der Name des virtuellen Verzeichnisses dem vorher installierten virtuellen Verzeichnis entspricht, (z.B. „MultiFactorAuth“).
3. Wenn sich das Benutzerportal auf einem eigenen Server befindet, kopieren Sie die Datei „MultiFactorAuthenticationUserPortalSetup64.msi“ vom Installationsspeicherort eines der MFA-Server in den Webserver des Benutzerportals. Führen Sie das Installationsprogramm aus.

   Falls ein Fehler mit der Nachricht auftritt, dass Microsoft Visual C++ 2015 Redistributable Update 1 oder höher benötigt wird, können Sie das aktuellste Updatepaket aus dem [Microsoft Download Center](https://www.microsoft.com/download/) herunterladen und installieren. Installieren Sie sowohl die x86- als auch die x64-Version.

4. Nachdem die aktualisierte Benutzerportal-Software installiert ist, vergleichen Sie die Sicherung der Datei „web.config“, die Sie in Schritt 1 erstellt haben, mit der neuen Datei „web.config“. Wenn sich in der neuen Datei „web.config“ keine neuen Attribute befinden, kopieren Sie Ihre Sicherungsdatei „web.config“ in das virtuelle Verzeichnis, um die neue Datei zu überschreiben. Eine andere Möglichkeit besteht darin, die Werte von appSettings und die Webdienst-SDK-URL durch Kopieren/Einfügen aus der Sicherungsdatei in die neue Datei „web.config“ einzufügen.

Wenn sich Ihr Benutzerportal auf mehreren Servern befindet, wiederholen Sie die Installation auf allen diesen Servern.

## <a name="upgrade-the-mobile-app-web-service"></a>Upgrade des Webdiensts der mobilen App

> [!NOTE]
> Wenn Sie für eine Azure MFA-Server-Version vor 8.0 ein Upgrade auf eine höhere Version als 8.0 durchführen, kann der Webdienst der mobilen App nach dem Upgrade deinstalliert werden.

## <a name="upgrade-the-ad-fs-adapters"></a>Aktualisieren des AD FS-Adapters

Schließen Sie das Upgrade Ihrer MFA-Server und des Benutzerportals ab, bevor Sie mit diesem Abschnitt fortfahren.

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Falls MFA auf anderen Servern als AD FS ausgeführt wird

Diese Informationen gelten nur, wenn Sie Multi-Factor Authentication-Server getrennt von Ihren AD FS-Servern ausführen. Wenn beide Dienste auf dem gleichen Server ausgeführt werden, können Sie diesen Abschnitt überspringen und direkt mit den Schritten zur Installation fortfahren. 

1. Speichern Sie eine Kopie der Datei „MultiFactorAuthenticationAdfsAdapter.config“, die in AD FS registriert war, oder exportieren Sie die Konfiguration mithilfe des folgenden PowerShell-Befehls: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]`. Der Name des Adapters ist entweder „WindowsAzureMultiFactorAuthentication“ oder „AzureMfaServerAuthentication“, je nachdem welche Version vorher installiert war.
2. Kopieren Sie die folgenden Dateien aus dem Installationsspeicherort des MFA-Servers auf die AD FS-Server:

   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config

3. Bearbeiten Sie das Skript „Register-MultiFactorAuthenticationAdfsAdapter.ps1“ durch Hinzufügen von `-ConfigurationFilePath [path]` am Ende des Befehls `Register-AdfsAuthenticationProvider`. Ersetzen Sie *[Pfad]* durch den vollständigen Pfad zur „MultiFactorAuthenticationAdfsAdapter.config“ oder zur Konfigurationsdatei, im vorherigen Schritt exportiert wurde.

   Überprüfen Sie die Attribute in der neuen Datei „MultiFactorAuthenticationAdfsAdapter.config“ um zu sehen, ob sie der alten „config“-Datei entsprechen. Wenn Attribute in der neuen Version hinzugefügt oder geändert wurden, können Sie die Attributwerte aus der alten Konfigurationsdatei in die neue Datei kopieren, oder die alte Konfigurationsdatei entsprechend ändern.

### <a name="install-new-ad-fs-adapters"></a>Installieren von neuen AD FS-Adaptern

> [!IMPORTANT]
> Ihre Benutzer müssen während der Schritte 3-8 in diesem Abschnitt keine zweistufige Überprüfung durchführen. Wenn AD FS bei Ihnen in mehreren Clustern konfiguriert ist, können Sie jeden Cluster in der Farm unabhängig von den anderen Clustern entfernen, upgraden und wiederherstellen, um Ausfallzeiten zu vermeiden.

1. Entfernen Sie einige AD FS-Server aus der Farm. Aktualisieren Sie diese Server, während die anderen noch ausgeführt werden.
2. Installieren Sie den neuen AD FS-Adapter auf jedem Server, der aus der AD FS-Farm entfernt wurde. Wenn der MFA-Server auf jedem AD FS-Server installiert ist, können Sie das Update auch über die Administratoren-UX des MFA-Servers durchführen. Führen Sie das Update andernfalls durch Ausführen von „MultiFactorAuthenticationAdfsAdapterSetup64.msi“ durch.

   Falls ein Fehler mit der Nachricht auftritt, dass Microsoft Visual C++ 2015 Redistributable Update 1 oder höher benötigt wird, können Sie das aktuellste Updatepaket aus dem [Microsoft Download Center](https://www.microsoft.com/download/) herunterladen und installieren. Installieren Sie sowohl die x86- als auch die x64-Version.

3. Wechseln Sie zu **AD FS** > **Authentifizierungsrichtlinien** > **Globale mehrstufige Authentifizierungsrichtlinie bearbeiten**. Deaktivieren Sie **WindowsAzureMultiFactorAuthentication** oder **AzureMfaServerAuthentication** (abhängig von der aktuell installierten Version).

   Nachdem dieser Schritt abgeschlossen ist, ist die zweistufige Überprüfung durch den MFA-Server in diesem AD FS-Cluster nicht verfügbar, bis Sie Schritt 8 abschließen.

4. Heben Sie die Registrierung der älteren Version des AD FS-Adapters durch Ausführen des Skripts „Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell“ auf. Stellen Sie sicher, dass der Parameter *-Name* (entweder „WindowsAzureMultiFactorAuthentication“ oder „AzureMFAServerAuthentication“), dem Namen entspricht, der in Schritt 3 angezeigt wurde. Dies gilt für alle Server im gleichen AD FS-Cluster, da es sich um eine zentrale Konfiguration handelt.
5. Registrieren Sie den neuen AD FS-Adapter durch Ausführen des Skripts „Register-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell“. Dies gilt für alle Server im gleichen AD FS-Cluster, da es sich um eine zentrale Konfiguration handelt.
6. Starten Sie den AD FS-Dienst auf jedem Server neu, der aus der AD FS-Farm entfernt wurde.
7. Fügen Sie die aktualisierten Server wieder zur AD FS-Farm hinzu, und entfernen Sie die anderen Server aus der Farm.
8. Wechseln Sie zu **AD FS** > **Authentifizierungsrichtlinien** > **Globale mehrstufige Authentifizierungsrichtlinie bearbeiten**. Überprüfen Sie **AzureMfaServerAuthentication**.
9. Wiederholen Sie Schritt 2, um die Server zu aktualisieren, die jetzt aus der AD FS-Farm entfernt wurden, und starten Sie den AD FS-Dienst auf diesen Servern neu.
10. Fügen Sie diese Server wieder zur AD FS-Farm hinzu.

## <a name="next-steps"></a>Nächste Schritte

* Hier finden Sie Beispiele von[erweiterten Szenarios mit Azure Multi-Factor Authentication und Drittanbieter-VPNs](howto-mfaserver-nps-vpn.md)

* [Synchronize MFA Server with Windows Server Active Directory (Synchronisieren mit Windows Server Active Directory)](howto-mfaserver-dir-ad.md)

* [Configure Windows Authentication (Konfigurieren von Windows-Authentifizierung)](howto-mfaserver-windows.md) für Ihre Anwendungen.
