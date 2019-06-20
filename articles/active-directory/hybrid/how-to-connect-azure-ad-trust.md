---
title: Azure AD Connect – Verwalten der AD FS-Vertrauensstellung mit Azure AD und Azure AD Connect | Microsoft-Dokumentation
description: Funktionsdetails zur Verarbeitung der Azure AD-Vertrauensstellung durch Azure AD Connect.
keywords: AD FS, ADFS, AD FS-Verwaltung, AAD Connect, Connect, Azure AD, Vertrauensstellung, AAD, Anspruch, Anspruchsregeln, Ausstellung, Transformation, Regeln, Sicherung, Wiederherstellung
services: active-directory
documentationcenter: ''
ms.reviewer: anandyadavmsft
manager: daveba
ms.subservice: hybrid
ms.assetid: 2593b6c6-dc3f-46ef-8e02-a8e2dc4e9fb9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/28/2018
ms.author: billmath
author: billmath
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bd46bb820c7127c4fa6105fcc0be73bb66024c6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60245706"
---
# <a name="manage-ad-fs-trust-with-azure-ad-using-azure-ad-connect"></a>Verwalten der AD FS-Vertrauensstellung mit Azure AD und Azure AD Connect

## <a name="overview"></a>Übersicht

Azure AD Connect kann den Verbund zwischen einem lokalen Active Directory-Verbunddienst (AD FS) und Azure AD verwalten. Dieser Artikel enthält eine Übersicht über Folgendes:

* Die verschiedenen Einstellungen, die für die Vertrauensstellung von Azure AD Connect konfiguriert werden
* Die Ausstellungstransformationsregeln (Anspruchsregeln), die von Azure AD Connect festgelegt werden
* Das Sichern und Wiederherstellen Ihrer Anspruchsregeln zwischen Upgrades und Konfigurationsupdates 

## <a name="settings-controlled-by-azure-ad-connect"></a>Von Azure AD Connect gesteuerte Einstellungen

Azure AD Connect verwaltet **nur** Einstellungen im Zusammenhang mit der Azure AD-Vertrauensstellung. Azure AD Connect ändert keine Einstellungen für andere Vertrauensstellungen der vertrauenden Seite in AD FS. In der folgenden Tabelle sind die Einstellungen angegeben, die von Azure AD Connect gesteuert werden.

| Einstellung | BESCHREIBUNG |
| :--- | :--- |
| Tokensignaturzertifikat | Azure AD Connect kann verwendet werden, um die Vertrauensstellung mit Azure AD zurückzusetzen und neu zu erstellen. Azure AD Connect nimmt einen einmaligen sofortigen Rollover der Tokensignaturzertifikate für AD FS vor und aktualisiert die Verbundeinstellungen der Azure AD-Domäne.|
| Tokensignaturalgorithmus | Microsoft empfiehlt die Verwendung von SHA-256 als Tokensignaturalgorithmus. Azure AD Connect kann erkennen, ob der Tokensignaturalgorithmus auf einen Wert festgelegt ist, der weniger sicher als SHA-256 ist. Die Einstellung wird im nächsten möglichen Konfigurationsvorgang in SHA-256 aktualisiert. Andere Vertrauensstellungen der vertrauenden Seite müssen aktualisiert werden, damit das neue Tokensignaturzertifikat verwendet wird. |
| Bezeichner der Azure AD-Vertrauensstellung | Azure AD Connect legt den richtigen Bezeichnerwert für die Azure AD-Vertrauensstellung fest. AD FS identifiziert die Azure AD-Vertrauensstellung mit dem Bezeichnerwert eindeutig. |
| Azure AD-Endpunkte | Azure AD Connect stellt sicher, dass die für die Azure AD-Vertrauensstellung konfigurierten Endpunkte immer den aktuellen empfohlenen Werten für Stabilität und Leistung entsprechen. |
| Ausstellungstransformationsregeln | Es gibt viele Anspruchsregeln, die für eine optimale Leistung der Features von Azure AD in einer verbundenen Umgebung erforderlich sind. Azure AD Connect stellt sicher, dass die Azure AD-Vertrauensstellung immer mit der richtigen Gruppe von empfohlenen Anspruchsregeln konfiguriert ist. |
| Alternative ID | Wenn die Synchronisierung für die Verwendung einer alternativen ID konfiguriert ist, konfiguriert Azure AD Connect AD FS so, dass die Authentifizierung mit der alternativen ID durchgeführt wird. |
| Automatische Metadatenaktualisierung | Die Vertrauensstellung mit Azure AD ist für die automatische Metadatenaktualisierung konfiguriert. AD FS prüft die Metadaten der Azure AD-Vertrauensstellung in regelmäßigen Abständen und hält sie auf dem neuesten Stand, falls sie auf der Azure AD-Seite geändert wurden. |
| Integrierte Windows-Authentifizierung (IWA) | Beim Vorgang „Hybrid-Azure AD-Einbindung“ ist IWA für die Geräteregistrierung aktiviert, um den Vorgang für kompatible Geräte zu ermöglichen. |

## <a name="execution-flows-and-federation-settings-configured-by-azure-ad-connect"></a>Von Azure AD Connect konfigurierte Ausführungsflows und Verbundeinstellungen

Azure AD Connect aktualisiert nicht alle Einstellungen für die Azure AD-Vertrauensstellung während der Konfigurationsflows. Die geänderten Einstellungen hängen davon ab, welche Aufgabe oder welcher Ausführungsflow ausgeführt wird. Die folgende Tabelle enthält die Einstellungen, die von unterschiedlichen Ausführungsflows betroffen sind.

| Ausführungsflow | Betroffene Einstellungen |
| :--- | :--- |
| Erstinstallation (Express) | Keine |
| Erstinstallation (neue AD FS-Farm) | Eine neue AD FS-Farm wird erstellt, und eine Vertrauensstellung mit Azure AD wird von Grund auf neu erstellt. |
| Erstinstallation (vorhandene AD FS-Farm, vorhandene Azure AD-Vertrauensstellung) | Bezeichner der Azure AD-Vertrauensstellung, Ausstellungstransformationsregeln, Azure AD-Endpunkte, alternative ID (falls erforderlich), automatische Metadatenaktualisierung |
| Azure AD-Vertrauensstellung zurücksetzen | Tokensignaturzertifikat, Tokensignaturalgorithmus, Bezeichner der Azure AD-Vertrauensstellung, Ausstellungstransformationsregeln, Azure AD-Endpunkte, alternative ID (falls erforderlich), automatische Metadatenaktualisierung |
| Verbundserver hinzufügen | Keine |
| WAP-Server hinzufügen | Keine |
| Geräteoptionen | Ausstellungstransformationsregeln, IWA für die Geräteregistrierung |
| Verbunddomäne hinzufügen | Wenn die Domäne zum ersten Mal hinzugefügt wird, das Setup also von einem Verbund mit einer einzelnen Domäne in einen Verbund mit mehreren Domänen geändert wird, erstellt Azure AD Connect die Vertrauensstellung von Grund auf neu. Wenn die Vertrauensstellung mit Azure AD bereits für mehrere Domänen konfiguriert ist, werden nur Ausstellungstransformationsregeln geändert. |
| SSL aktualisieren | Keine |

Bei allen Vorgängen, bei denen eine Einstellung geändert wird, erstellt Azure AD Connect in **%ProgramData%\AADConnect\ADFS** eine Sicherung der aktuellen Einstellungen für die Vertrauensstellung.

![Azure AD Connect-Seite mit einer Meldung über eine vorhandene Sicherung der Azure AD-Vertrauensstellung](./media/how-to-connect-azure-ad-trust/backup2.png)

> [!NOTE]
> Vor Version 1.1.873.0 bestand die Sicherung nur aus Ausstellungstransformationsregeln, und sie wurden in der Ablaufverfolgungsprotokoll-Datei des Assistenten gesichert.

## <a name="issuance-transform-rules-set-by-azure-ad-connect"></a>Von Azure AD Connect festgelegte Ausstellungstransformationsregeln

Azure AD Connect stellt sicher, dass die Azure AD-Vertrauensstellung immer mit der richtigen Gruppe von empfohlenen Anspruchsregeln konfiguriert ist. Microsoft empfiehlt, Azure AD Connect zum Verwalten der Azure AD-Vertrauensstellung zu verwenden. Dieser Abschnitt enthält die Ausstellungstransformationsregeln und deren Beschreibung.

| Regelname | BESCHREIBUNG |
| --- | --- |
| UPN ausstellen | Diese Regel fragt den Wert für „userprincipalname“ über das Attribut ab, das in den Synchronisierungseinstellungen für „userprincipalname“ konfiguriert ist.|
| „objectguid“ und „msdsconsistencyguid“ für benutzerdefinierten ImmutableId-Anspruch abfragen | Diese Regel fügt in die Pipeline einen temporären Wert für den objectguid- und den msdsconsistencyguid-Wert hinzu, falls vorhanden. |
| Das Vorhandensein von „msdsconsistencyguid“ überprüfen | Basierend darauf, ob der Wert für „msdsconsistencyguid“ vorhanden ist, wird ein temporäres Flag festgelegt, um anzugeben, was als „ImmutableId“ verwendet werden soll. |
| „msdsconsistencyguid“ als unveränderliche ID ausstellen, falls vorhanden | „msdsconsistencyguid“ wird als „ImmutableId“ ausgestellt, falls der Wert vorhanden ist. |
| „objectGuidRule“ ausstellen, wenn die msdsConsistencyGuid-Regel nicht vorhanden ist | Wenn der Wert für „msdsconsistencyguid“ nicht vorhanden ist, wird der Wert von „objectguid" als „ImmutableId“ ausgestellt. |
| „nameidentifier“ ausstellen | Diese Regel stellt den Wert für den nameidentifier-Anspruch aus.|
| „accounttype“ für in die Domäne eingebundene Computer ausstellen | Wenn die Entität, die authentifiziert wird, ein in die Domäne eingebundenes Gerät ist, stellt diese Regel den Kontotyp als DJ aus, um ein in die Domäne eingebundenes Gerät anzugeben. |
| „AccountType“ mit dem Wert USER ausstellen, wenn es kein Computerkonto ist | Wenn die Entität, die authentifiziert wird, ein Benutzer ist, stellt diese Regel den Kontotyp als Benutzer aus. |
| „issuerid“ ausstellen, wenn es kein Computerkonto ist | Diese Regel stellt den issuerId-Wert aus, wenn die authentifizierende Entität kein Gerät ist. Der Wert wird über einen regulären Ausdruck erstellt, der von Azure AD Connect konfiguriert wird. Der reguläre Ausdruck wird erstellt, nachdem alle Domänen berücksichtigt wurden, die über Azure AD Connect verbunden wurden. |
| „issuerid“ für DJ-Computerauthentifizierung ausstellen | Diese Regel stellt den issuerId-Wert aus, wenn die authentifizierende Entität ein Gerät ist. |
| „onpremobjectguid“ für in die Domäne eingebundene Computer ausstellen | Wenn die Entität, die authentifiziert wird, ein in die Domäne eingebundenes Gerät ist, stellt diese Regel die lokale „objectguid“ für das Gerät aus. |
| Primäre SID weiterleiten | Diese Regel stellt die primäre SID der authentifizierenden Entität aus. |
| Anspruch weiterleiten – insideCorporateNetwork | Diese Regel stellt einen Anspruch aus, der für Azure AD angibt, ob die Authentifizierung aus dem Unternehmensnetzwerk oder einem externen Netzwerk stammt. |
| Anspruch weiterleiten – Psso |   |
| Ansprüche für den Kennwortablauf ausstellen | Diese Regel stellt drei Ansprüche aus: für die Kennwortablaufzeit, die Anzahl von Tagen, bis das Kennwort der authentifizierenden Entität abläuft, und die URL für die Weiterleitung zum Ändern des Kennworts.|
| Anspruch weiterleiten – authnmethodsreferences | Der Wert im mit dieser Regel ausgestellten Anspruch gibt an, welche Art von Authentifizierung für die Entität ausgeführt wurde. |
| Anspruch weiterleiten – multifactorauthenticationinstant | Der Wert dieses Anspruchs gibt die Zeit in UTC an, zu der der Benutzer zuletzt eine mehrstufige Authentifizierung ausgeführt hat. |
| Anspruch weiterleiten – AlternateLoginID | Diese Regel stellt den AlternateLoginID-Anspruch aus, wenn die Authentifizierung mit der alternativen Anmelde-ID durchgeführt wurde. |

> [!NOTE]
> Die Anspruchsregeln für „UPN ausstellen“ und „ImmutableId“ sind anders, wenn Sie nicht die Standardoptionen bei der Konfiguration von Azure AD Connect verwenden.

## <a name="restore-issuance-transform-rules"></a>Wiederherstellen von Ausstellungstransformationsregeln

Azure AD Connect, Version 1.1.873.0 oder höher, erstellt eine Sicherung der Einstellungen für die Azure AD-Vertrauensstellung, wenn ein Update der Einstellungen für die Azure AD-Vertrauensstellung vorgenommen wird. Die Einstellungen für die Azure AD-Vertrauensstellung werden in **%ProgramData%\AADConnect\ADFS** gesichert. Der Dateiname hat das folgende Format: AadTrust-&lt;Datum&gt;-&lt;Zeit&gt;.txt, Beispiel: AadTrust-20180710-150216.txt

![Screenshot der Beispielsicherung einer Azure AD-Vertrauensstellung](./media/how-to-connect-azure-ad-trust/backup.png)

Sie können die Ausstellungstransformationsregeln mithilfe der folgenden empfohlenen Schritte wiederherstellen.

1. Öffnen Sie die AD FS-Verwaltungsoberfläche im Server-Manager.
2. Öffnen Sie die Eigenschaften der Azure AD-Vertrauensstellung, indem Sie **AD FS &gt; Vertrauensstellungen der vertrauenden Seite &gt; Microsoft Office 365 Identity Platform &gt; Anspruchsausstellungsrichtlinie bearbeiten** aufrufen.
3. Klicken Sie auf **Regel hinzufügen**.
4. Wählen Sie in der Anspruchsregelvorlage „Ansprüche mit benutzerdefinierter Regel senden“ aus, und klicken Sie auf **Weiter**.
5. Kopieren Sie den Namen der Anspruchsregel aus der Sicherungsdatei, und fügen Sie ihn in das Feld **Anspruchsregelname** ein.
6. Kopieren Sie die Anspruchsregel aus Sicherungsdatei in das Textfeld **Benutzerdefinierte Regel**, und klicken Sie auf **Fertig stellen**.

> [!NOTE]
> Stellen Sie sicher, dass die zusätzlichen Regeln nicht in Konflikt mit den Regeln stehen, die von Azure AD Connect konfiguriert wurden.

## <a name="next-steps"></a>Nächste Schritte
* [Verwaltung und Anpassung der Active Directory-Verbunddienste mit Azure AD Connect](how-to-connect-fed-management.md)
