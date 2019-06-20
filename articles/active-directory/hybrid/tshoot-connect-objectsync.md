---
title: 'Azure AD Connect: Beheben von Problemen bei der Objektsynchronisierung | Microsoft-Dokumentation'
description: Dieses Thema enthält Schritte zum Beheben von Problemen bei der Objektsynchronisierung mithilfe der Aufgaben zur Problembehandlung.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e56d4d94e38e5095ef2223d0cc2875cbf1dcd46
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64919119"
---
# <a name="troubleshoot-object-synchronization-with-azure-ad-connect-sync"></a>Beheben von Problemen bei der Objektsynchronisierung mit der Azure AD Connect-Synchronisierung
Dieser Artikel enthält Schritte zum Behandeln von Problemen bei der Objektsynchronisierung mithilfe der Problembehandlungsaufgabe. Die Funktionsweise der Problembehandlung in Azure Active Directory (Azure AD) wird in [diesem kurzen Video](https://aka.ms/AADCTSVideo) veranschaulicht.

## <a name="troubleshooting-task"></a>Aufgaben zur Problembehandlung
Verwenden Sie für die Azure AD Connect-Bereitstellung ab Version 1.1.749.0 die Problembehandlungsaufgabe im Assistenten, um Probleme bei der Objektsynchronisierung zu behandeln. Für frühere Versionen führen Sie die Problembehandlung manuell wie [hier](tshoot-connect-object-not-syncing.md) beschrieben aus.

### <a name="run-the-troubleshooting-task-in-the-wizard"></a>Ausführen der Problembehandlungsaufgabe im Assistenten
Um die Aufgabe zur Problembehandlung im Assistenten auszuführen, führen Sie die folgenden Schritte aus:

1.  Öffnen Sie mit der Option „Als Administrator ausführen“ eine neue Windows PowerShell-Sitzung auf Ihrem Azure AD Connect-Server.
2.  Führen Sie `Set-ExecutionPolicy RemoteSigned` oder `Set-ExecutionPolicy Unrestricted` aus.
3.  Starten Sie den Azure AD Connect-Assistenten.
4.  Navigieren Sie zur Seite „Weitere Aufgaben“, wählen Sie „Problembehandlung“ aus, und klicken Sie auf „Weiter“.
5.  Klicken Sie auf der Seite „Problembehandlung“ auf „Starten“, um das Menü zur Problembehandlung in PowerShell zu starten.
6.  Wählen Sie im Hauptmenü „Problembehandlung bei der Objektsynchronisierung“ aus.
![Beheben von Problemen bei der Objektsynchronisierung](media/tshoot-connect-objectsync/objsynch11.png)

### <a name="troubleshooting-input-parameters"></a>Eingabeparameter für die Problembehandlung
Die folgenden Eingabeparameter sind für die Problembehandlungsaufgabe erforderlich:
1.  **Distinguished Name des Objekts:** Dies ist der Distinguished Name des Objekts, für das die Problembehandlung durchgeführt werden soll.
2.  **AD-Connectorname:** Dies ist der Name der AD-Gesamtstruktur, in der sich das oben genannte Objekt befindet.
3.  ![Anmeldeinformationen als globaler Administrator](media/tshoot-connect-objectsync/objsynch1.png) Anmeldeinformationen eines globalen Administrators für den Azure AD-Mandanten

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Grundlegendes zu den Ergebnissen der Problembehandlungsaufgabe
Die Problembehandlungsaufgabe führt die folgenden Überprüfungen durch:

1.  Ermitteln von UPN-Konflikten, wenn das Objekt mit Azure Active Directory synchronisiert wird
2.  Überprüfen, ob das Objekt aufgrund einer Domänenfilterung gefiltert wird
3.  Überprüfen, ob das Objekt aufgrund einer Organisationseinheitenfilterung gefiltert wird
4.  Überprüfen, ob die Objektsynchronisierung aufgrund eines verknüpften Postfachs blockiert ist
5. Überprüfen, ob es sich bei dem Objekt um eine dynamische Verteilergruppe handelt, die nicht synchronisiert werden soll

Im restlichen Teil dieses Abschnitts werden bestimmte Ergebnisse beschrieben, die von der Aufgabe zurückgegeben werden. In jedem Fall stellt die Aufgabe eine Analyse gefolgt von den empfohlenen Aktionen zum Beheben des Problems bereit.

## <a name="detect-upn-mismatch-if-object-is-synced-to-azure-active-directory"></a>Ermitteln von UPN-Konflikten, wenn das Objekt mit Azure Active Directory synchronisiert wird
### <a name="upn-suffix-is-not-verified-with-azure-ad-tenant"></a>Das UPN-Suffix wird NICHT mit dem Azure AD-Mandanten überprüft.
Wenn das UPN- (UserPrincipalName) bzw. das alternative Anmelde-ID-Suffix nicht mit dem Azure AD-Mandanten überprüft wird, ersetzt Azure Active Directory die UPN-Suffixe durch den Standarddomänennamen „onmicrosoft.com“.

![Azure AD ersetzt UPN](media/tshoot-connect-objectsync/objsynch2.png)

### <a name="changing-upn-suffix-from-one-federated-domain-to-another-federated-domain"></a>Ändern des UPN-Suffix von einer Verbunddomäne in eine andere Verbunddomäne
Azure Active Directory erlaubt nicht die Synchronisierung des UPN- (UserPrincipalName) bzw. des alternativen Anmelde-ID-Suffix von einer Verbunddomäne in eine andere Verbunddomäne. Dies gilt für Domänen, die mit dem Azure AD-Mandanten überprüft werden und den Authentifizierungstyp „Verbund“ aufweisen.

![Kein UPN-Synchronisierung von einer Verbunddomäne mit einer anderen](media/tshoot-connect-objectsync/objsynch3.png) 

### <a name="azure-ad-tenant-dirsync-feature-synchronizeupnformanagedusers-is-disabled"></a>Das DirSync-Feature „SynchronizeUpnForManagedUsers“ ist für Azure AD-Mandanten deaktiviert.
Wenn das DirSync-Feature „SynchronizeUpnForManagedUsers“ für Azure AD-Mandanten deaktiviert ist, erlaubt Azure Active Directory keine Synchronisierungsupdates für den Benutzerprinzipalnamen bzw. die alternative Anmelde-ID für lizenzierte Benutzerkonten mit verwalteter Authentifizierung.

![SynchronizeUpnForManagedUsers](media/tshoot-connect-objectsync/objsynch4.png)

## <a name="object-is-filtered-due-to-domain-filtering"></a>Das Objekt wird aufgrund einer Domänenfilterung gefiltert
### <a name="domain-is-not-configured-to-sync"></a>Die Domäne ist nicht für die Synchronisierung konfiguriert
Das Objekt liegt außerhalb des gültigen Bereichs, da die Domäne nicht konfiguriert wurde. Im folgenden Beispiel befindet sich das Objekt nicht im gültigen Synchronisierungsbereich, da die Domäne, zu der es gehört, aus der Synchronisierung gefiltert wird.

![Die Domäne ist nicht für die Synchronisierung konfiguriert](media/tshoot-connect-objectsync/objsynch5.png)

### <a name="domain-is-configured-to-sync-but-is-missing-run-profilesrun-steps"></a>Die Domäne ist für die Synchronisierung konfiguriert, aber es fehlen die Ausführungsprofile/Ausführungsschritte
Das Objekt befindet sich außerhalb des gültigen Bereichs, da die Domäne über keine Ausführungsprofile/Ausführungsschritte verfügt. Im folgenden Beispiel befindet sich das Objekt nicht im gültigen Synchronisierungsbereich, da die Domäne, zu der es gehört, über keine Ausführungsschritte für das Ausführungsprofil für den vollständigen Import verfügt.
![Fehlende Ausführungsprofile](media/tshoot-connect-objectsync/objsynch6.png)

## <a name="object-is-filtered-due-to-ou-filtering"></a>Das Objekt wird aufgrund einer Organisationseinheitenfilterung gefiltert
Das Objekt befindet sich aufgrund einer Filterkonfiguration nach Organisationseinheit nicht im gültigen Synchronisierungsbereich. Im folgenden Beispiel gehört das Objekt zur Organisationseinheit „OU=NoSync,DC=bvtadwbackdc,DC=com“.  Diese Organisationseinheit ist nicht im gültigen Synchronisierungsbereich enthalten.</br>

![Organisationseinheit](./media/tshoot-connect-objectsync/objsynch7.png)

## <a name="linked-mailbox-issue"></a>Problem mit einem verknüpften Postfach
Ein verknüpftes Postfach sollte einem externen Hauptkonto zugeordnet sein, das sich in einer anderen vertrauenswürdigen Kontogesamtstruktur befindet. Wenn kein externes Hauptkonto vorhanden ist, synchronisiert Azure AD Connect das Benutzerkonto, das dem verknüpften Postfach in der Exchange-Gesamtstruktur entspricht, nicht mit dem Azure AD-Mandanten.</br>
![Verknüpftes Postfach](./media/tshoot-connect-objectsync/objsynch12.png)

## <a name="dynamic-distribution-group-issue"></a>Problem mit dynamischen Verteilergruppen
Aufgrund von verschiedenen Unterschieden zwischen dem lokalen Active Directory und Azure Active Directory synchronisiert Azure AD Connect dynamische Verteilergruppen nicht mit dem Azure AD-Mandanten.

![Dynamische Verteilergruppe](./media/tshoot-connect-objectsync/objsynch13.png)

## <a name="html-report"></a>HTML-Bericht
Zusätzlich zur Analyse des Objekts generiert die Problembehandlungsaufgabe auch einen HTML-Bericht, der alle bekannten Informationen über das Objekt enthält. Dieser HTML-Bericht kann bei Bedarf zur weiteren Problembehandlung für das Supportteam freigegeben werden.

![HTML-Bericht](media/tshoot-connect-objectsync/objsynch8.png)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
