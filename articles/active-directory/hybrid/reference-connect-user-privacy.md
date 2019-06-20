---
title: Azure AD Connect und Datenschutz | Microsoft-Dokumentation
description: Dieses Dokument beschreibt, wie Sie DSGVO-Konformität mit Azure AD Connect erzielen.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f5d3125b7b77e8ce7a943f640c44615049ab160
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60455783"
---
# <a name="user-privacy-and-azure-ad-connect"></a>Datenschutz und Azure AD Connect 

[!INCLUDE [Privacy](../../../includes/gdpr-intro-sentence.md)]

>[!NOTE] 
>Dieser Artikel befasst sich mit Azure AD Connect und dem Schutz der Benutzerdaten.  Informationen zu Azure AD Connect Health und zum Datenschutz finden Sie in [diesem Artikel](reference-connect-health-user-privacy.md).

Es gibt zwei Methoden zum Verbessern des Datenschutzes bei Azure AD Connect-Installationen:

1.  Extrahieren von Daten für eine Person und Entfernen der Daten dieser Person aus Installationen auf Anforderung
2.  Sicherstellen, dass keine Daten länger als 48 Stunden beibehalten werden

Das Azure AD Connect-Team empfiehlt die zweite Option, da sie viel einfacher zu implementieren und zu verwalten ist.

Ein Azure AD Connect-Synchronisierungsserver speichert die folgenden Benutzerdaten, die unter den Datenschutz fallen:
1.  Daten über eine Person in der **Azure AD Connect-Datenbank**
2.  Daten in **Windows-Ereignisprotokolldateien**, die Informationen zu einer Person enthalten können
3.  Daten in **Azure AD Connect-Installationsprotokolldateien**, die Informationen zu einer Person enthalten können

Beim Entfernen von Benutzerdaten sollten Azure AD Connect-Kunden die folgenden Richtlinien anwenden:
1.  Löschen Sie den Inhalt des Ordners mit den Azure AD Connect-Installationsprotokolldateien in regelmäßigen Abständen – mindestens alle 48 Stunden.
2.  Dieses Produkt kann auch Ereignisprotokolle erstellen.  Weitere Informationen zu Ereignisprotokollen finden Sie in [dieser Dokumentation](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx).

Daten über eine Person werden automatisch aus der Azure AD Connect-Datenbank entfernt, wenn die Daten dieser Person aus dem Quellsystem, aus dem sie stammen, entfernt werden. Es ist keine spezielle Aktion durch Administratoren erforderlich, um hier DSGVO-Konformität herzustellen.  Die Azure AD Connect-Daten müssen jedoch mindestens alle zwei Tage mit Ihrer Datenquelle synchronisiert werden.

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Löschen des Inhalts aus dem Ordner mit den Azure AD Connect-Installationsprotokolldateien
Überprüfen Sie regelmäßig den Inhalt des Ordners **C:\programdata\aadconnect**, und löschen Sie ihn – mit Ausnahme der Datei **PersistedState.xml**. Diese Datei behält den Zustand der vorherigen Installation von Azure AD Connect bei. Sie wird bei einer Upgradeinstallation verwendet. Diese Datei enthält keine Daten zu einzelnen Personen und sollte nicht gelöscht werden.

>[!IMPORTANT]
>Löschen Sie nicht die Datei „PersistedState.xml“.  Diese Datei enthält keine Benutzerinformationen und behält den Status der letzten Installation bei.

Sie können diese Dateien mithilfe von Windows-Explorer überprüfen und löschen, oder Sie können ein Skript wie das folgende verwenden, um die erforderlichen Aktionen auszuführen:


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>Planen der Skriptausführung alle 48 Stunden
Verwenden Sie die folgenden Schritte, um die Ausführung des Skripts alle 48 Stunden zu planen.

1.  Speichern Sie das Skript in einer Datei mit der Erweiterung **&#46;PS1**, öffnen Sie dann die Systemsteuerung, und klicken Sie auf **Systeme und Sicherheit**.
    ![System](./media/reference-connect-user-privacy/gdpr2.png)

2.  Klicken Sie unter der Überschrift „Verwaltung“ auf **Aufgaben planen**.
    ![Aufgabe](./media/reference-connect-user-privacy/gdpr3.png)
3.  Klicken Sie im Taskplaner mit der rechten Maustaste auf **Aufgabenplanungsbibliothek**, und klicken Sie auf **Einfache Aufgabe erstellen**.
4.  Geben Sie den Namen für die neue Aufgabe ein, und klicken Sie auf **Weiter**.
5.  Wählen Sie als Aufgabenauslöser **Täglich** aus, und klicken Sie auf **Weiter**.
6.  Legen Sie die Wiederholung auf **2 Tage** fest, und klicken Sie auf **Weiter**.
7.  Wählen Sie als Aktion **Programm starten** aus, und klicken Sie auf **Weiter**.
8.  Geben Sie in das Feld für das Programm/Skript **PowerShell** und in das Feld **Argumente hinzufügen (optional)** den vollständigen Pfad zu dem Skript, das Sie zuvor erstellt haben, ein. Klicken Sie anschließend auf **Weiter**.
9.  Auf dem nächsten Bildschirm wird eine Zusammenfassung der Aufgabe angezeigt, die Sie erstellen möchten. Überprüfen Sie die Werte, und klicken Sie dann auf **Fertig stellen**, um die Aufgabe zu erstellen.



## <a name="next-steps"></a>Nächste Schritte
* [Die Microsoft-Datenschutzrichtlinie im Trust Center](https://www.microsoft.com/trustcenter)
* [Azure AD Connect Health und Datenschutz](reference-connect-health-user-privacy.md)
