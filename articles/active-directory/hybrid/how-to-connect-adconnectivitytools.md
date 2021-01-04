---
title: 'Azure AD Connect: Was ist das PowerShell-Modul „ADConnectivityTool“? | Microsoft-Dokumentation'
description: In diesem Dokument werden das neue PowerShell-Modul ADConnectivity und seine Verwendung für die Problembehandlung vorgestellt.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 4/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7eed3e06ab42671d9674ad3893a88dfe9817e22
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973455"
---
# <a name="troubleshoot-azure-ad-connectivity-with-the-adconnectivitytool-powershell-module"></a>Behandeln von Azure AD-Verbindungsproblemen mit dem PowerShell-Modul ADConnectivity-Tool

Das ADConnectivity-Tool ist ein PowerShell-Modul, das auf eine der folgenden Arten verwendet wird:

- Während der Installation, wenn ein Problem mit der Netzwerkverbindung die erfolgreiche Überprüfung der Active Directory-Anmeldeinformationen verhindert, die der Benutzer im Assistenten angegeben hat.
- Nach der Installation durch einen Benutzer, der die Funktionen aus einer PowerShell-Sitzung aufruft.

Das Tool befindet sich hier: **C:\Programme\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool.psm1** 

## <a name="adconnectivitytool-during-installation"></a>ADConnectivityTool während der Installation

Auf der Seite **Verzeichnisse verbinden**  im Azure AD Connect-Assistenten verwendet das ADConnectivityTool, wenn ein Netzwerkproblem auftritt, automatisch eine seiner Funktionen, um zu bestimmen, was vor sich geht.  Jedes der folgenden kann als Netzwerkproblem betrachtet werden:

- Der vom Benutzer angegebene Name der Gesamtstruktur wurde falsch eingegeben, oder besagte Gesamtstruktur ist nicht vorhanden. 
- UDP-Port 389 ist in den Domänencontrollern, die der vom Benutzer angegebenen Gesamtstruktur zugeordnet sind, geschlossen.
- Die im Fenster „AD-Gesamtstrukturkonto“ angegebenen Anmeldeinformationen verfügen nicht über Berechtigungen zum Abrufen der Domänencontroller, die der Zielgesamtstruktur zugeordnet sind.
- Jeder der TCP-Ports 53, 88 oder 389 ist in den Domänencontrollern, die der vom Benutzer angegebenen Gesamtstruktur zugeordnet sind, geschlossen. 
- Der UDP-Port 389 und ein TCP-Port (oder Ports) sind geschlossen.
- Das DNS konnte für die angegebene Gesamtstruktur und/oder deren zugeordnete Domänencontroller nicht aufgelöst werden.

Bei Auffinden eines dieser Probleme wird eine diesbezügliche Fehlermeldung im AADConnect-Assistenten angezeigt:


![Fehler](media/how-to-connect-adconnectivitytools/error1.png)

Z. B. wenn wir versuchen, im Bildschirm **Verzeichnisse verbinden** ein Verzeichnis hinzuzufügen, muss Azure AD Connect dies überprüfen und erwartet, in der Lage zu sein, mit einem Domänencontroller über Port 389 kommunizieren zu können.  Ist dies nicht möglich, sehen wir den Fehler, der im obigen Screenshot angezeigt wird.  

Was eigentlich hinter den Kulissen passiert, ist, dass Azure AD Connect die Funktion `Start-NetworkConnectivityDiagnosisTools` aufruft.  Diese Funktion wird aufgerufen, wenn die Überprüfung von Anmeldeinformationen aufgrund eines Netzwerkkonnektivitätsproblems fehlschlägt.

Schließlich wird immer, wenn das Tool vom Assistenten aufgerufen wird, eine detaillierte Protokolldatei generiert. Das Protokoll befindet sich unter **C:\ProgramData\AADConnect\ADConnectivityTool-\<date>-\<time>.log**.

## <a name="adconnectivitytools-post-installation"></a>ADConnectivityTools nach der Installation
Nachdem Azure AD Connect installiert wurde, kann jede der Funktionen im PowerShell-Modul „ADConnectivityTools“ verwendet werden.  

Referenzinformationen zu den Funktionen finden Sie in der [Referenz zu ADConnectivityTools](reference-connect-adconnectivitytools.md).

### <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

Wir werden diese Funktion aufrufen, da sie **nur** manuell aufgerufen werden kann, sobald „ADConnectivityTool.psm1“ in die PowerShell importiert wurde. 

Diese Funktion führt dieselbe Logik aus, die der Azure AD Connect-Assistent ausführt, um die angegebenen AD-Anmeldeinformationen zu überprüfen.  Sie bietet allerdings eine wesentlich ausführlichere Erläuterung des Problems sowie eine vorgeschlagene Lösung. 

Die Konnektivitätsüberprüfung besteht aus den folgenden Schritten:
-   Abrufen des Domänen-FQDN (vollqualifizierter Domänenname)
-   Überprüfen, wenn der Benutzer „Neues AD-Konto erstellen“ ausgewählt hat, ob diese Anmeldeinformationen zur Gruppe „Unternehmensadministratoren“ gehören.
-   Abrufen des Gesamtstruktur-FQDN-Objekts
-   Bestätigen, dass mindestens eine Domäne, die dem zuvor abgerufenen Gesamtstruktur-FQDN-Objekt zugeordnet ist, erreichbar ist.
-   Überprüfen, ob die Funktionsebene der Gesamtstruktur Windows Server 2003 oder höher ist.

Der Benutzer kann ein Verzeichnis hinzuzufügen, wenn alle diese Aktionen erfolgreich ausgeführt wurden.

Wenn der Benutzer diese Funktion ausführt, nachdem ein Problem behoben wurde (oder überhaupt kein Problem vorhanden ist), zeigt die Ausgabe dem Benutzer an, dass er zum Azure AD Connect-Assistenten zurückwechseln und erneut versuchen soll, die Anmeldeinformationen einzufügen.



## <a name="next-steps"></a>Nächste Schritte
- [Azure AD Connect: Konten und Berechtigungen](reference-connect-accounts-permissions.md)
- [Expressinstallation](how-to-connect-install-express.md)
- [Benutzerdefinierte Installation](how-to-connect-install-custom.md)
- [Referenz zu ADConnectivityTools](reference-connect-adconnectivitytools.md)

