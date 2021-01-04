---
title: Was ist der Azure AD Connect-Administrator-Agent – Azure AD Connect | Microsoft-Dokumentation
description: Beschreibt die Tools, die zum Synchronisieren und Überwachen Ihrer lokalen Umgebung mit Azure AD verwendet werden.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/04/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da4e1cfc68dff4ad2bc0552c6d35fe1230779306
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2020
ms.locfileid: "91312956"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Was ist der Azure AD Connect-Administrator-Agent? 
Der Azure AD Connect-Administrator-Agent ist eine neue Komponente von Azure Active Directory Connect, die auf einem Azure Active Directory Connect-Server installiert werden kann. Er wird zum Sammeln spezifischer Daten aus der Active Directory-Umgebung verwendet, die einem Microsoft-Supporttechniker bei der Problembehandlung helfen, wenn Sie eine Supportanfrage senden. 

>[!NOTE]
>Standardmäßig ist der Administrator-Agent nicht installiert und aktiviert.  Sie müssen den Agent installieren, um Daten zur Unterstützung bei Supportfällen sammeln zu können.

Nach der Installation wartet der Azure AD Connect-Administrator-Agent auf bestimmte Datenanforderungen aus Azure Active Directory, ruft die angeforderten Daten aus der Synchronisierungsumgebung ab und sendet sie an Azure Active Directory, wo sie dem Microsoft-Supporttechniker zur Verfügung gestellt werden. 

Die Informationen, die der Azure AD Connect-Administrator-Agent aus der Umgebung abruft, werden in keiner Weise gespeichert. Sie werden lediglich dem Microsoft-Supporttechniker angezeigt, damit dieser die von Ihnen gesendete Supportanfrage zu Azure Active Directory Connect untersuchen und mögliche Probleme behandeln kann. Der Azure AD Connect-Administrator-Agent ist nicht standardmäßig auf dem Azure AD Connect-Server installiert. 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Installieren des Azure AD Connect-Administrator-Agents auf dem Azure AD Connect-Server 

Voraussetzungen:
1.    Azure AD Connect ist auf dem Server installiert.
2.    Azure AD Connect Health ist auf dem Server installiert.

![Administrator-Agent](media/whatis-aadc-admin-agent/adminagent0.png)

Die Binärdateien für den Azure AD Connect-Administrator-Agent-befinden sich auf dem AAD Connect-Server. Gehen Sie zum Installieren des Agents wie folgt vor:

1.    Öffnen Sie Powershell im Administratormodus.
2.    Navigieren Sie zu dem Verzeichnis, in dem sich die Anwendung befindet: cd „C:\Programme\Microsoft \Microsoft Azure Active Directory Connect\Tools“.
3.    Führen Sie „ConfigureAdminAgent.ps1“ aus.

Geben Sie, wenn Sie dazu aufgefordert werden, Ihre Anmeldeinformationen als globaler Azure AD-Administrator ein. Dabei sollte es sich um dieselben Anmeldeinformationen handeln, die bei der Installation von Azure AD Connect verwendet wurden.

Nach der Installation des Agents werden die folgenden beiden neuen Programme auf Ihrem Server in der Systemsteuerung in der Liste „Programme hinzufügen/entfernen“ angezeigt: 

![Screenshot: Liste „Programme hinzufügen/entfernen“, die die von Ihnen hinzugefügten neuen Programme enthält](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Welche Daten in meinem Synchronisierungsdienst werden dem Microsoft-Supporttechniker angezeigt? 
Wenn Sie eine Supportanfrage senden, kann der Microsoft-Supporttechniker für einen bestimmten Benutzer die entsprechenden Daten in Active Directory, den Azure Active Directory-Connectorbereich auf dem Azure Active Directory Connect-Server und die Metaverse auf dem Azure Active Directory Connect-Server anzeigen. 

Der Microsoft-Supporttechniker kann in Ihrem System weder Daten ändern, noch Kennwörter sehen. 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Welche Möglichkeiten habe ich, falls ich nicht möchte, dass der Microsoft-Supporttechniker auf meine Daten zugreift? 
Wenn der Microsoft-Supporttechniker nach der Installation des Agents bei einer Supportanfrage nicht auf Ihre Daten zugreifen soll, können Sie die Dienstkonfigurationsdatei wie nachstehend beschrieben ändern und so die Funktion deaktivieren: 

1. Öffnen Sie im Editor **C:\Programme\Microsoft Azure AD Connect Administration Agent\AzureADConnectAdministrationAgentService.exe.config**.
2. Deaktivieren Sie die Einstellung **UserDataEnabled**, wie unten dargestellt. Wenn die Einstellung **UserDataEnabled** vorhanden und auf „true“ festgelegt ist, setzen Sie diese auf „false“. Wenn die Einstellung nicht vorhanden ist, fügen Sie diese, wie unten gezeigt, hinzu.    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3. Speichern Sie die Konfigurationsdatei.
4. Starten Sie den Dienst des Azure AD Connect-Administrator-Agents neu, wie unten dargestellt.

![Screenshot, der zeigt, wo der Azure AD-Administrator-Agent neu gestartet wird](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
