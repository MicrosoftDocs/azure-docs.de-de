---
title: Vergleichen der Baselines mit der Überwachung der Dateiintegrität in Azure Security Center | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Baselines mit der Überwachung der Dateiintegrität in Azure Security Center vergleichen.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c8a2a589-b737-46c1-b508-7ea52e301e8f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2019
ms.author: monhaber
ms.openlocfilehash: e403a9bd4d3f8668544dab1d81e9052b37839bef
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358040"
---
# <a name="compare-baselines-using-file-integrity-monitoring-fim"></a>Vergleichen der Baselines mithilfe der Überwachung der Dateiintegrität (FIM)

Die Überwachung der Dateiintegrität (FIM) informiert Sie darüber, wenn Änderungen an sensiblen Bereichen in Ihren Ressourcen vorgenommen werden, damit Sie nicht autorisierten Aktivitäten untersuchen und behandeln können. FIM überwacht Windows-Dateien, die Windows-Registrierung und Linux-Dateien.

In diesem Thema wird erläutert, wie Sie FIM für die Dateien und Registrierungen aktivieren. Weitere Informationen zu FIM finden Sie unter [Überwachung der Dateiintegrität in Azure Security Center](security-center-file-integrity-monitoring.md).

## <a name="why-use-fim"></a>Gründe für die Verwendung von FIM

Betriebssystem, Anwendungen und zugehörigen Konfigurationen steuern den Verhalten und den Sicherheitszustand Ihrer Ressourcen. Angreifer zielen daher auf die Dateien ab, die Ihre Ressourcen steuern, um das Betriebssystem einer Ressource zu übernehmen und/oder unbemerkt Aktivitäten auszuführen.

Im Grunde erfordern viele regulatorische Konformitätsstandards wie PCI-DSS & ISO 17799 die Implementierung von FIM-Kontrollen.  

## <a name="enable-built-in-recursive-registry-checks"></a>Aktivieren von integrierten rekursiven Registrierungsüberprüfungen

Die Standardeinstellungen der FIM-Registrierungsstruktur bieten eine bequeme Möglichkeit, rekursive Änderungen innerhalb gemeinsamer Sicherheitsbereiche zu überwachen.  Angenommen, ein Angreifer konfiguriert ein Skript, das im LOCAL_SYSTEM-Kontext ausgeführt wird, indem er eine Ausführung beim Hoch- oder Herunterfahren konfiguriert.  Um solche Änderungen zu überwachen, aktivieren Sie die integrierte Überprüfung.  

![Registrierung](./media/security-center-file-integrity-monitoring-baselines/baselines-registry.png)

>[!NOTE]
> Rekursive Überprüfungen gelten nur für die empfohlenen Sicherheitsstrukturen und nicht für benutzerdefinierte Registrierungspfade.  

## <a name="adding-a-custom-registry-check"></a>Hinzufügen einer benutzerdefinierten Registrierungsüberprüfung

FIM-Baselines beginnen damit, die Merkmale eines als funktionierend bekannten Zustands für das Betriebssystem und die unterstützende Anwendung zu identifizieren.  In diesem Beispiel liegt der Schwerpunkt auf der Kennwortrichtlinienkonfigurationen für Windows Server 2008 und höher.


|Richtlinienname                 | Registrierungseinstellung|
|---------------------------------------|-------------|
|Domänencontroller: Änderungen von Computerkontenkennwörtern ablehnen| MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RefusePasswordChange|
|Domänenmitglied: Daten des sicheren Kanals (immer) digital verschlüsseln oder signieren|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireSignOrSeal|
|Domänenmitglied: Daten des sicheren Kanals (wenn möglich) digital verschlüsseln|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\SealSecureChannel|
|Domänenmitglied: Daten des sicheren Kanals (wenn möglich) digital signieren|MACHINE\System\CurrentControlSet\Services   \Netlogon\Parameters\SignSecureChannel|
|Domänenmitglied: Änderungen von Computerkontenkennwörtern deaktivieren|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DisablePasswordChange|
|Domänenmitglied: Maximales Alter von Computerkontenkennwörtern|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\MaximumPasswordAge|
|Domänenmitglied: Starker Sitzungsschlüssel (Windows 2000 oder höher) erforderlich|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RequireStrongKey|
|Netzwerksicherheit: NTLM einschränken:  NTLM-Authentifizierung in dieser Domäne|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\RestrictNTLMInDomain|
|Netzwerksicherheit: NTLM einschränken: Serverausnahmen in dieser Domäne hinzufügen|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\DCAllowedNTLMServers|
|Netzwerksicherheit: NTLM einschränken: NTLM-Authentifizierung in dieser Domäne überwachen|MACHINE\System\CurrentControlSet\Services  \Netlogon\Parameters\AuditNTLMInDomain|

> [!NOTE]
> Weitere Informationen zu Registrierungseinstellungen, die von verschiedenen Betriebssystemversionen unterstützt werden, finden Sie in der [Referenztabelle der Gruppenrichtlinieneinstellungen](https://www.microsoft.com/en-us/download/confirmation.aspx?id=25250).

*So konfigurieren Sie FIM zum Überwachen von Registrierungsbaselines:*

1. Geben Sie im Fenster **Windows-Registrierung für Änderungsnachverfolgung hinzufügen** in das Feld **Windows-Registrierungsschlüssel** den Registrierungsschlüssel ein.

    <code>

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters
    </code>

      ![Aktivieren von FIM für eine Registrierung](./media/security-center-file-integrity-monitoring-baselines/baselines-add-registry.png)

## <a name="tracking-changes-to-windows-files"></a>Nachverfolgen von Änderungen an Windows-Dateien

1. Geben Sie im Fenster **Windows-Datei für Änderungsnachverfolgung hinzufügen** in das Feld **Pfad eingeben** den Ordner ein, der die Dateien enthält, die Sie nachverfolgen möchten. Im Beispiel in der folgenden Abbildung befindet sich die **Contoso Web-App** im Laufwerk D:\ innerhalb der Ordnerstruktur **ContosWebApp**.  
1. Erstellen Sie einen benutzerdefinierten Windows-Dateieintrag, indem Sie einen Namen der Einstellungsklasse angeben, die Rekursion aktivieren und den obersten Ordner mit einem Platzhaltersuffix (*) angeben.

    ![Aktivieren von FIM für eine Datei](./media/security-center-file-integrity-monitoring-baselines/baselines-add-file.png)

## <a name="retrieving-change-data"></a>Abrufen von Änderungsdaten

Die Daten der Dateiintegritätsüberwachung befinden sich im Azure Log Analytics/ConfigurationChange-Tabellensatz.  

 1. Legen Sie einen Zeitbereich für das Abrufen einer Zusammenfassung der Änderungen nach Ressource fest.
Im folgenden Beispiel werden alle Änderungen in den letzten 14 Tagen in den Kategorien Registrierung und Dateien abgerufen:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry', 'Files')

    > | summarize count() by Computer, ConfigChangeType

    </code>

1. So zeigen Sie Details zu Registrierungsänderungen an:

    1. Entfernen Sie **Dateien** aus der **WHERE**-Klausel. 
    1. Entfernen Sie die Zeile für die Zusammenfassung, und ersetzen Sie sie durch eine Klausel zum Sortieren:

    <code>

    > ConfigurationChange

    > | where TimeGenerated > ago(14d)

    > | where ConfigChangeType in ('Registry')

    > | order by Computer, RegistryKey

    </code>

Berichte können für die Archivierung in CSV-Dateien exportiert und/oder in einen Power BI-Bericht geleitet werden.  

![FIM-Daten](./media/security-center-file-integrity-monitoring-baselines/baselines-data.png)