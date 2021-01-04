---
title: Beheben von Problemen bei dynamischen Gruppenmitgliedschaften – Azure AD | Microsoft-Dokumentation
description: Tipps zur Problembehandlung bei dynamischen Gruppenmitgliedschaften in Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: troubleshooting
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41bfdf11bad28ab772b68839a5a7bf7776eb4dff
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548102"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Beheben von Problemen bei Gruppen

## <a name="troubleshooting-group-creation-issues"></a>Problembehandlung bei der Gruppenerstellung

**Ich habe die Erstellung von Sicherheitsgruppen im Azure-Portal deaktiviert, Gruppen können jedoch weiterhin über PowerShell erstellt werden** Mit der Einstellung **Benutzer können Sicherheitsgruppen in Azure-Portalen erstellen** im Azure-Portal wird gesteuert, ob Benutzer ohne Administratorrechte Sicherheitsgruppen im Zugriffsbereich oder Azure-Portal erstellen können. Die Erstellung von Sicherheitsgruppen über PowerShell wird damit nicht gesteuert.

So deaktivieren Sie die Gruppenerstellung für Benutzer ohne Administratorrechte in PowerShell
1. Prüfen Sie, ob Benutzer ohne Administratorrechte zum Erstellen von Gruppen berechtigt sind:
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. Wird `UsersPermissionToCreateGroupsEnabled : True` zurückgegeben, sind Benutzer ohne Administratorrechte zum Erstellen von Gruppen berechtigt. So deaktivieren Sie das Feature:
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**Beim Erstellen einer dynamischen Gruppe in PowerShell wird ein Fehler zur maximal zulässigen Anzahl von Gruppen angezeigt**<br/>
Wenn in PowerShell die Fehlermeldung _Die maximal zulässige Anzahl von Gruppen dynamischer Gruppenrichtlinien wurde erreicht_ angezeigt wird, bedeutet das, dass Sie das maximale Limit für dynamische Gruppen in Ihrer Organisation erreicht haben. Die maximale Anzahl von dynamischen Gruppen pro Organisation beträgt 5.000.

Um neue dynamische Gruppen erstellen zu können, müssen Sie zunächst einige vorhandene dynamische Gruppen löschen. Das Limit kann nicht erhöht werden.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Problembehandlung bei dynamischen Mitgliedschaften für Gruppen

**Ich habe eine Regel für eine Gruppe konfiguriert, die Mitgliedschaften in der Gruppe werden jedoch nicht aktualisiert.**<br/>
1. Überprüfen Sie die Werte für Benutzer- oder Geräteattribute in der Regel. Stellen Sie sicher, dass Benutzer vorhanden sind, die die Regel erfüllen. Überprüfen Sie für Geräte die Geräteeigenschaften, um sicherzustellen, dass synchronisierte Attribute die erwarteten Werte enthalten.<br/>
2. Überprüfen Sie den Status der Mitgliedschaftsverarbeitung, um zu sehen, ob sie abgeschlossen ist. Auf der Seite **Übersicht** für die Gruppe können Sie den [Verarbeitungsstatus der Mitgliedschaft](groups-create-rule.md#check-processing-status-for-a-rule) und das zuletzt geänderte Datum prüfen.

Wenn alles korrekt aussieht, warten Sie einen Augenblick, bis die Gruppe aufgefüllt ist. Je nach Größe Ihrer Azure AD-Organisation kann das erstmalige Auffüllen der Gruppe oder das Auffüllen nach einer Änderung der Regel bis zu 24 Stunden dauern.

**Ich habe eine Regel konfiguriert, aber jetzt werden die vorhandenen Mitglieder der Regel entfernt.**<br/>Dieses Verhalten wird erwartet. Vorhandene Mitglieder der Gruppe werden entfernt, wenn eine Regel aktiviert oder geändert wird. Die nach der Auswertung der Regel verbleibenden Benutzer werden der Gruppe als Mitglieder hinzugefügt.

**Warum werden Änderungen an der Mitgliedschaft nicht sofort angezeigt, wenn ich eine Regel hinzufüge oder ändere?**<br/> Die dedizierte Mitgliedschaftsauswertung erfolgt in regelmäßigen Abständen in einem asynchronen Hintergrundprozess. Die Dauer dieses Prozesses hängt von der Anzahl der Benutzer in Ihrem Verzeichnis und der Größe der Gruppe, die aufgrund der Regel erstellt wird, ab. Bei Verzeichnissen mit einer geringen Benutzeranzahl werden Änderungen der Gruppenmitgliedschaft üblicherweise innerhalb weniger Minuten angezeigt. Bei Verzeichnissen mit einer hohen Benutzeranzahl kann das Auffüllen 30 Minuten oder länger dauern.

**Wie kann ich erzwingen, dass die Gruppe jetzt verarbeitet wird?**<br/>
Derzeit besteht keine Möglichkeit, die bedarfsgesteuerte Verarbeitung der Gruppe automatisch auszulösen. Sie können jedoch die erneute Verarbeitung manuell auslösen, indem Sie die Mitgliedschaftsregel aktualisieren und am Ende ein Leerzeichen hinzufügen.  

**Bei mir ist ein Regelverarbeitungsfehler aufgetreten.**<br/>In der folgenden Tabelle werden häufige Fehler mit Regeln für eine dynamische Mitgliedschaft und deren Behandlung aufgeführt.

| Regelparserfehler | Fehlerverwendung | Korrigierte Verwendung |
| --- | --- | --- |
| Error: Das Attribut wird nicht unterstützt. |(user.invalidProperty -eq "Value") |(user.department -eq "value")<br/><br/>Stellen Sie sicher, dass das Attribut in der [Liste der unterstützten Eigenschaften](groups-dynamic-membership.md#supported-properties) aufgeführt wird. |
| Error: Der Operator wird für das Attribut nicht unterstützt. |(user.accountEnabled -contains true) |(user.accountEnabled -eq true)<br/><br/>Der Operator wird für den Eigenschaftentyp nicht unterstützt (in diesem Beispiel kann „-contains“ nicht für den Booleschen Typ verwendet werden). Verwenden Sie die richtige Operatoren für den Eigenschaftentyp. |
| Error: Abfragekompilierungsfehler. | 1. (user.department -eq "Sales") (user.department -eq "Marketing")<br>2. (user.userPrincipalName -match "*@domain.ext") | 1. Fehlender Operator. Verwenden Sie „-and“ oder „-or“, um zwei Prädikate zu verknüpfen.<br>(user.department -eq "Sales") -or (user.department -eq "Marketing")<br>2. Fehler im regulären Ausdruck, der mit „-match“ verwendet wird<br>(user.userPrincipalName -match ".*@domain.ext")<br>oder alternativ: (user.userPrincipalName -match "@domain.ext$") |

## <a name="next-steps"></a>Nächste Schritte

Diese Artikel enthalten zusätzliche Informationen zu Azure Active Directory.

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](../fundamentals/active-directory-manage-groups.md)
* [Anwendungsverwaltung in Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Was ist Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](../hybrid/whatis-hybrid-identity.md)