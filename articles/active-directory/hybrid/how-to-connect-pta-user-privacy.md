---
title: Datenschutz und Passthrough-Authentifizierung von Azure Active Directory | Microsoft-Dokumentation
description: In diesem Artikel geht es um die Azure AD-Passthrough-Authentifizierung (Azure Active Directory) und die Einhaltung der DSGVO.
services: active-directory
keywords: Passthrough-Authentifizierung mit Azure AD Connect, DSGVO, erforderliche Komponenten für Azure AD, SSO, einmaliges Anmelden
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/23/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 396344ba90aa3850d7d23dc40d6df95f6d1f6c3f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996575"
---
# <a name="user-privacy-and-azure-active-directory-pass-through-authentication"></a>Datenschutz und Passthrough-Authentifizierung von Azure Active Directory


[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview"></a>Übersicht

Durch die Azure AD-Passthrough-Authentifizierung wird der folgende Protokolltyp erstellt, der ggf. personenbezogene Daten enthält:

- Azure AD Connect-Ablaufverfolgungsprotokoll-Dateien
- Ablaufverfolgungsprotokoll-Dateien für Authentifizierungs-Agent
- Windows-Ereignisprotokolldateien

Verbessern Sie den Datenschutz der Benutzer bei der Passthrough-Authentifizierung wie folgt:

1.  Extrahieren von Daten für eine Person und Entfernen der Daten dieser Person aus Installationen auf Anforderung
2.  Sicherstellen, dass keine Daten länger als 48 Stunden beibehalten werden

Wir raten Ihnen dringend, die zweite Option zu verwenden, da diese einfacher zu implementieren und zu verwalten ist. Hier sind die Anleitungen für die einzelnen Protokolltypen angegeben:

### <a name="delete-azure-ad-connect-trace-log-files"></a>Löschen von Azure AD Connect-Ablaufverfolgungsprotokoll-Dateien

Überprüfen Sie den Inhalt des Ordners **%ProgramData%\AADConnect**, und löschen Sie den Inhalt des Ablaufverfolgungsprotokolls (**trace-\*.log**-Dateien) des Ordners innerhalb von 48 Stunden nach dem Installieren oder Aktualisieren von Azure AD Connect oder dem Ändern der Passthrough-Authentifizierungskonfiguration. Der Grund ist, dass mit dieser Aktion ggf. Daten erstellt werden, die unter die DSGVO fallen.

>[!IMPORTANT]
>Löschen Sie nicht die Datei **PersistedState.xml** in diesem Ordner, da diese Datei zum Verwalten des Zustands der vorherigen Installation von Azure AD Connect und nach einer Upgradeinstallation verwendet wird. Diese Datei enthält niemals Daten zu einer Person und sollte nie gelöscht werden.

Sie können diese Ablaufverfolgungsprotokoll-Dateien entweder mit Windows-Explorer überprüfen und löschen, oder Sie können das folgende PowerShell-Skript verwenden, um die erforderlichen Aktionen durchzuführen:

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

Speichern Sie das Skript in einer Datei mit der Erweiterung „.PS1“. Führen Sie dieses Skript je nach Bedarf aus.

Weitere Informationen zu verwandten DSGVO-Anforderungen für Azure AD Connect finden Sie in [diesem Artikel](reference-connect-user-privacy.md).

### <a name="delete-authentication-agent-event-logs"></a>Löschen der Ereignisprotokolle des Authentifizierungs-Agents

Mit diesem Produkt können auch **Windows-Ereignisprotokolle** erstellt werden. Weitere Informationen finden Sie in [diesem Artikel](/windows/win32/wes/windows-event-log).

Öffnen Sie zum Anzeigen von Protokollen zum Agent für die Passthrough-Authentifizierung die **Ereignisanzeige** auf dem Server, und sehen Sie unter **Anwendungs- und Dienstprotokolle\Microsoft\AzureAdConnect\AuthenticationAgent\Admin** nach.

### <a name="delete-authentication-agent-trace-log-files"></a>Löschen der Ablaufverfolgungsprotokoll-Dateien für den Authentifizierungs-Agent

Sie sollten den Inhalt von **%ProgramData%\Microsoft\Azure AD Connect Authentication Agent\Trace** regelmäßig überprüfen und den Inhalt dieses Ordners alle 48 Stunden löschen. 

>[!IMPORTANT]
>Wenn der Authentifizierungs-Agent ausgeführt wird, ist es nicht möglich, die aktuelle Protokolldatei im Ordner zu löschen. Beenden Sie den Dienst, bevor Sie es erneut versuchen. Um Benutzeranmeldefehler zu vermeiden, sollten Sie die Passthrough-Authentifizierung für [Hochverfügbarkeit](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) bereits konfiguriert haben.

Sie können diese Dateien mithilfe von Windows-Explorer überprüfen und löschen, oder Sie können das folgende Skript verwenden, um die erforderlichen Aktionen durchzuführen:

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

Führen Sie diese Schritte aus, um für das Skript die Ausführung nach jeweils 48 Stunden zu planen:

1.  Speichern Sie das Skript in einer Datei mit der Erweiterung „.PS1“.
2.  Öffnen Sie die **Systemsteuerung**, und klicken Sie auf **System und Sicherheit**.
3.  Klicken Sie unter der Überschrift **Verwaltung** auf **Aufgaben planen**.
4.  Klicken Sie im **Taskplaner** mit der rechten Maustaste auf **Aufgabenplanungsbibliothek**, und klicken Sie auf **Einfache Aufgabe erstellen...** .
5.  Geben Sie den Namen für die neue Aufgabe ein, und klicken Sie auf **Weiter**.
6.  Wählen Sie unter **Aufgabentrigger** die Option **Täglich**, und klicken Sie auf **Weiter**.
7.  Legen Sie die Wiederholung auf zwei Tage fest, und klicken Sie auf **Weiter**.
8.  Wählen Sie als Aktion **Programm starten** aus, und klicken Sie auf **Weiter**.
9.  Geben Sie in das Feld für das Programm/Skript **PowerShell** und in das Feld **Argumente hinzufügen (optional)** den vollständigen Pfad zu dem zuvor erstellten Skript ein. Klicken Sie anschließend auf **Weiter**.
10. Auf dem nächsten Bildschirm wird eine Zusammenfassung der Aufgabe angezeigt, die Sie erstellen möchten. Überprüfen Sie die Werte, und klicken Sie auf **Fertig stellen**, um die Aufgabe zu erstellen:
 
### <a name="note-about-domain-controller-logs"></a>Hinweis zu Domänencontrollerprotokollen

Wenn die Überwachungsprotokollierung aktiviert ist, kann dieses Produkt Sicherheitsprotokolle für Ihre Domänencontroller generieren. Weitere Informationen zum Konfigurieren von Überwachungsrichtlinien finden Sie in [diesem Artikel](/previous-versions/tn-archive/dd277403(v=technet.10)).

## <a name="next-steps"></a>Nächste Schritte
* [Die Microsoft-Datenschutzrichtlinie im Trust Center](https://www.microsoft.com/trustcenter)
* [**Problembehandlung**](tshoot-connect-pass-through-authentication.md) – Beheben von häufig auftretenden Problemen mit diesem Feature