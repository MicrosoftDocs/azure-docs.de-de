---
title: Behandeln von Problemen mit der kombinierten Registrierung – Azure Active Directory
description: Problembehandlung für die MFA (Multi-Factor Authentication, mehrstufige Authentifizierung) von Azure AD und die Self-Service-Kennwortzurücksetzung mit kombinierten Registrierungen
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 04/15/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ba1e280e3c14a24e33246799ca0d7ef8221294f
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741693"
---
# <a name="troubleshooting-combined-security-information-registration"></a>Problembehandlung für die kombinierte Registrierung von Sicherheitsinformationen

Mit den in diesem Artikel bereitgestellten Informationen können Administratoren Probleme von Benutzern mit kombinierten Registrierungen beheben.

## <a name="audit-logs"></a>Überwachungsprotokolle

Die für kombinierte Registrierungen protokollierten Ereignisse befinden sich in den Azure AD-Überwachungsprotokollen unter der Kategorie „Authentifizierungsmethoden“.

![Schnittstelle für Azure AD-Überwachungsprotokolle, die Registrierungsereignisse anzeigt](media/howto-registration-mfa-sspr-combined-troubleshoot/combined-security-info-audit-log.png)

In der folgenden Tabelle werden alle Überwachungsereignisse aufgeführt, die mit der kombinierten Registrierung generiert werden:

| Aktivität | Status | `Reason` | BESCHREIBUNG |
| --- | --- | --- | --- |
| User registered all required security info (Der Benutzer hat alle erforderlichen Sicherheitsinformationen registriert) | Erfolg | Der Benutzer hat alle erforderlichen Sicherheitsinformationen registriert. | Dieses Ereignis tritt auf, wenn ein Benutzer die Registrierung erfolgreich abgeschlossen hat.|
| User registered all required security info (Der Benutzer hat alle erforderlichen Sicherheitsinformationen registriert) | Fehler | Der Benutzer hat die Registrierung der Sicherheitsinformationen abgebrochen. | Dieses Ereignis tritt auf, wenn ein Benutzer die Registrierung über den Interruptmodus abbricht.|
| User registered security info (Der Benutzer hat Sicherheitsinformationen registriert) | Erfolg | Der Benutzer hat eine *Methode* registriert. | Dieses Ereignis tritt auf, wenn ein Benutzer eine individuelle Methode registriert. Bei der *Methode* kann es sich um eine Authenticator-App, ein Mobilgerät, eine E-Mail, Sicherheitsfragen, ein App-Kennwort, ein alternatives Mobilgerät usw. handeln.| 
| User reviewed security info (Der Benutzer hat die Sicherheitsinformationen überprüft) | Erfolg | Der Benutzer hat die Sicherheitsinformationen erfolgreich überprüft. | Dieses Ereignis tritt ein, wenn ein Benutzer auf der Seite „Sicherheitsinformationen überprüfen“ die Option **Sieht gut aus** auswählt.|
| User reviewed security info (Der Benutzer hat die Sicherheitsinformationen überprüft) | Fehler | Die Überprüfung der Sicherheitsinformationen durch den Benutzer ist fehlgeschlagen. | Dieses Ereignis tritt ein, wenn ein Benutzer auf der Seite „Sicherheitsinformationen überprüfen“ die Option **Sieht gut aus** auswählt, jedoch ein Fehler am Back-End auftritt.|
| User deleted security info (Der Benutzer hat die Sicherheitsinformationen gelöscht) | Erfolg | Der Benutzer hat die *Methode* gelöscht. | Dieses Ereignis tritt auf, wenn ein Benutzer eine individuelle Methode löscht. Bei der *Methode* kann es sich um eine Authenticator-App, ein Mobilgerät, eine E-Mail, Sicherheitsfragen, ein App-Kennwort, ein alternatives Mobilgerät usw. handeln.|
| User deleted security info (Der Benutzer hat die Sicherheitsinformationen gelöscht) | Fehler | Der Benutzer konnte die *Methode* nicht löschen. | Dieses Ereignis tritt ein, wenn der Benutzer versucht eine Methode zu löschen, dies jedoch aus irgendeinem Grund zu einem Fehler führt. Bei der *Methode* kann es sich um eine Authenticator-App, ein Mobilgerät, eine E-Mail, Sicherheitsfragen, ein App-Kennwort, ein alternatives Mobilgerät usw. handeln.|
| User changed default security info (Der Benutzer hat die Standardsicherheitsinformationen geändert) | Erfolg | Der Benutzer hat die Standardsicherheitsinformationen für die *Methode* geändert. | Dieses Ereignis tritt ein, wenn ein Benutzer die Standardmethode ändert. Folgende *Methoden* sind möglich: „Benachrichtigung in Authenticator-App“, „Code aus meiner Authentifikator-App oder Token eingeben“, „+X XXXXXXXXXX anrufen“, „SMS mit Code an +X XXXXXXXXX senden“ usw.|
| User changed default security info (Der Benutzer hat die Standardsicherheitsinformationen geändert) | Fehler | Der Benutzer konnte die Standardsicherheitsinformationen für die *Methode* nicht ändern. | Dieses Ereignis tritt ein, wenn der Benutzer versucht, die Standardmethode zu ändern, dies jedoch aus irgendeinem Grund zu einem Fehler führt. Folgende *Methoden* sind möglich: „Benachrichtigung in Authenticator-App“, „Code aus meiner Authentifikator-App oder Token eingeben“, „+X XXXXXXXXXX anrufen“, „SMS mit Code an +X XXXXXXXXX senden“ usw.|

## <a name="troubleshooting-interrupt-mode"></a>Problembehandlung des Interruptmodus

| Symptom | Schritte zur Problembehandlung |
| --- | --- |
| Es werden nicht die erwarteten Methoden angezeigt. | 1. Überprüfen Sie, ob der Benutzer über die Azure AD-Administratorrolle verfügt. Wenn ja, überprüfen Sie die Unterschiede in der SSPR-Administratorrichtlinie. <br> 2. Ermitteln Sie, ob der Benutzer aufgrund der Erzwingung von Multi-Factor Authentication- oder SSPR-Registrierungen unterbrochen wird. Sehen Sie sich das [Flussdiagramm](../../active-directory/authentication/concept-registration-mfa-sspr-combined.md#combined-registration-modes) unter „Modi der kombinierten Registrierung“ an, um zu ermitteln, welche Methoden angezeigt werden sollten. <br> 3. Ermitteln Sie, wie aktuell die Änderungen an der MFA- (Multi-Factor Authentication) oder SSPR-Richtlinie sind. Wenn die Änderung erst kürzlich vorgenommen wurden, kann es einige Zeit dauern, bis die aktualisierte Richtlinie verteilt wird.|

## <a name="troubleshooting-manage-mode"></a>Problembehandlung für den Verwaltungsmodus

| Symptom | Schritte zur Problembehandlung |
| --- | --- |
| Es steht keine Option zum Hinzufügen einer bestimmten Methode zur Verfügung. | 1. Ermitteln Sie, ob die Methode für die mehrstufige Authentifizierung oder SSPR aktiviert ist. <br> 2. Wenn die Methode aktiviert ist, speichern Sie die Richtlinien erneut, und warten Sie ein bis zwei Stunden, bevor Sie es erneut versuchen. <br> 3. Wenn die Methode aktiviert ist, stellen Sie sicher, dass der Benutzer diese Methode nicht bereits so oft eingerichtet hat, wie ihm maximal erlaubt ist.|

## <a name="disable-combined-registration"></a>Deaktivieren der kombinierten Registrierung

Wenn ein Benutzer eine Telefonnummer und/oder mobile App mit dem neuen Feature für die kombinierte Registrierung registriert, versieht der Dienst eine Gruppe von Flags (StrongAuthenticationMethods) mit einem Stempel für diese Methoden für diesen Benutzer. Über diese Funktionalität kann der Benutzer immer die mehrstufige Authentifizierung (MFA) mit diesen Methoden ausführen, wenn eine mehrstufige Authentifizierung erforderlich ist.

Wenn ein Administrator die Vorschauversion aktiviert, registrieren Benutzer diese über die neue Oberfläche, und der Administrator deaktiviert anschließend die Vorschauversion, können Benutzer unwissentlich auch für die mehrstufige Authentifizierung registriert werden.

Wenn ein Benutzer, der eine kombinierte Registrierung durchgeführt hat, die aktuelle Registrierungsseite für die Self-Service-Kennwortzurücksetzung (SSPR) unter [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) aufruft, wird er dazu aufgefordert, die mehrstufige Authentifizierung auszuführen, um auf die Seite zuzugreifen. Dieser Schritt ist aus technischer Sicht zu erwarten, doch für Benutzer, die vorher lediglich für die SSPR registriert waren, ist dies neu. Obwohl dieser zusätzliche Schritt das Sicherheitsniveau des Benutzers durch die Bereitstellung einer zusätzlichen Sicherheitsstufe verbessert, sollten Administratoren ein Rollback für ihre Benutzer ausführen, damit sie nicht mehr die mehrstufige Authentifizierung ausführen können.  

### <a name="how-to-roll-back-users"></a>Gewusst wie: Ausführen eines Rollbacks für Benutzer

Wenn Sie als Administrator Einstellungen für die mehrstufige Authentifizierung eines Benutzers zurücksetzen möchten, können Sie das im nächsten Abschnitt bereitgestellte PowerShell-Skript verwenden. Das Skript löscht die StrongAuthenticationMethods-Eigenschaft für die mobile App oder die Rufnummer des Benutzers. Wenn Sie dieses Skript für Ihre Benutzer ausführen, müssen diese sich erneut für die mehrstufige Authentifizierung registrieren, wenn sie diese benötigen. Es wird empfohlen, das Rollback vor der Ausführung eines Rollbacks für alle betroffenen Benutzer bei einem oder zwei Benutzern zu testen.

Die folgenden Schritte zeigen, wie Sie ein Rollback für einen Benutzer oder eine Gruppe von Benutzern ausführen.

#### <a name="prerequisites"></a>Voraussetzungen

1. Installieren Sie die entsprechenden Azure AD-PowerShell-Module. Führen Sie in einem PowerShell-Fenster die folgenden Befehle zum Installieren der Module aus:

   ```powershell
   Install-Module -Name MSOnline
   Import-Module MSOnline
   ```

1. Speichern Sie die Liste der betroffenen Benutzerobjekt-IDs auf Ihrem Computer als Textdatei, in der jede ID in einer separaten Zeile aufgeführt wird. Notieren Sie sich den Speicherort der Datei.
1. Speichern Sie das folgende Skript auf Ihrem Computer, und notieren Sie sich den Speicherort des Skripts:

   ```powershell
   <# 
   //********************************************************
   //*                                                      *
   //*   Copyright (C) Microsoft. All rights reserved.      *
   //*                                                      *
   //********************************************************
   #>

   param($path)

   # Define Remediation Fn
   function RemediateUser {

       param  
       (
           $ObjectId
       )

       $user = Get-MsolUser -ObjectId $ObjectId

       Write-Host "Checking if user is eligible for rollback: UPN: "  $user.UserPrincipalName  " ObjectId: "  $user.ObjectId -ForegroundColor Yellow

       $hasMfaRelyingParty = $false
       foreach($p in $user.StrongAuthenticationRequirements)
       {
           if ($p.RelyingParty -eq "*")
           {
               $hasMfaRelyingParty = $true
               Write-Host "User was enabled for per-user MFA." -ForegroundColor Yellow
           }
       }

       if ($user.StrongAuthenticationMethods.Count -gt 0 -and -not $hasMfaRelyingParty)
       {
           Write-Host $user.UserPrincipalName " is eligible for rollback" -ForegroundColor Yellow
           Write-Host "Rolling back user ..." -ForegroundColor Yellow
           Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName $user.UserPrincipalName
           Write-Host "Successfully rolled back user " $user.UserPrincipalName -ForegroundColor Green
       }
       else
       {
           Write-Host $user.UserPrincipalName " is not eligible for rollback. No action required."
       }

       Write-Host ""
       Start-Sleep -Milliseconds 750
   }

   # Connect
   Import-Module MSOnline
   Connect-MsolService

   foreach($line in Get-Content $path)
   {
       RemediateUser -ObjectId $line
   }
   ```

#### <a name="rollback"></a>Rollback

Führen Sie in einem PowerShell-Fenster den folgenden Befehl aus, und geben Sie das Skript und die Speicherorte der Benutzerdateien an. Geben Sie bei entsprechender Aufforderung die Anmeldeinformationen des globalen Administrators ein. Das Skript gibt das Ergebnis der einzelnen Benutzerupdatevorgänge aus.

`<script location> -path <user file location>`

### <a name="disable-the-updated-experience"></a>Deaktivieren der aktualisierten Oberfläche

Um die aktualisierte Oberfläche für Ihre Benutzer zu deaktivieren, führen Sie die folgenden Schritte aus:

1. Melden Sie sich als Benutzeradministrator beim Azure-Portal an.
2. Navigieren Sie zu **Azure Active Directory** > **Benutzereinstellungen** > **Einstellungen für Zugriffspanel-Vorschaufeatures verwalten**.
3. Legen Sie für **Benutzer können Vorschaufeatures zum Registrieren und Verwalten von Sicherheitsinformationen verwenden** die Option **Keine** fest, und wählen Sie dann **Speichern** aus.

Benutzer werden nicht mehr aufgefordert, sich bei der aktualisierten Oberfläche zu registrieren.

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie mehr über die kombinierte Registrierung für die Self-Service-Kennwortzurücksetzung und Azure AD Multi-Factor Authentication](concept-registration-mfa-sspr-combined.md)
