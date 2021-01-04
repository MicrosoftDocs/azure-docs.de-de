---
title: 'Azure AD Connect: ADSync-Dienstkonto | Microsoft-Dokumentation'
description: In diesem Thema werden das ADSync-Dienstkonto beschrieben und bewährte Methoden für das Konto vorgestellt.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8dddfb8426b769c06cb5b7494431b7eee34dbf9e
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410894"
---
# <a name="adsync-service-account"></a>ADSync-Dienstkonto
Azure AD Connect installiert einen lokalen Dienst, der die Synchronisierung zwischen Active Directory und Azure Active Directory orchestriert.  Der Synchronisierungsdienst Microsoft Azure AD Sync (ADSync) wird auf einem Server in Ihrer lokalen Umgebung ausgeführt.  Die Anmeldeinformationen für den Dienst sind in den Express-Installationen standardmäßig festgelegt, können aber an die Sicherheitsanforderungen Ihrer Organisation angepasst werden.  Diese Anmeldeinformationen werden nicht verwendet, um eine Verbindung mit Ihren lokalen Gesamtstrukturen oder Azure Active Directory herzustellen.

Die Wahl des ADSync-Dienstkontos ist eine wichtige Planungsentscheidung, die vor der Installation von Azure AD Connect getroffen werden muss.  Jeder Versuch, die Anmeldeinformationen nach der Installation zu ändern, führt dazu, dass der Dienst nicht gestartet werden kann, den Zugriff auf die Synchronisierungsdatenbank verliert und sich nicht bei Ihren verbundenen Verzeichnissen (Azure und AD DS) authentifiziert.  Es erfolgt keine Synchronisierung, bis die ursprünglichen Anmeldeinformationen wiederhergestellt sind.

## <a name="the-default-adsync-service-account"></a>Das standardmäßige Azure ADSync-Dienstkonto

Bei Ausführung auf einem Mitgliedsserver wird der AdSync-Dienst im Rahmen eines virtuellen Dienstkontos (Virtual Service Account, VSA) ausgeführt.  Aufgrund einer Produktbeschränkung wird bei der Installation auf einem Domänencontroller ein benutzerdefiniertes Dienstkonto erstellt.  Wenn das Dienstkonto mit den Express-Einstellungen nicht den Sicherheitsanforderungen Ihrer Organisation entspricht, stellen Sie Azure AD Connect mit der Option „Anpassen“ bereit.  Wählen Sie dann die Dienstkontooption, die den Anforderungen Ihrer Organisation entspricht.

>[!NOTE]
>Das Standarddienstkonto bei Installation auf einem Domänencontroller hat das Format „Domäne\AAD-Installations-ID“.  Das Kennwort für dieses Konto wird nach dem Zufallsprinzip generiert und stellt eine große Herausforderung für die Wiederherstellung und Rotation des Kennworts dar.  Microsoft empfiehlt, das Dienstkonto bei der Erstinstallation auf einem Domänencontroller so anzupassen, dass entweder ein eigenständiges oder ein gruppenbasiertes verwaltetes Dienstkonto (sMSA/gMSA) verwendet wird.

|Azure AD Connect-Speicherort|Erstelltes Dienstkonto|
|-----|-----|
|Mitgliedsserver|NT-SERVICE\ADSync|
|Domänencontroller|Domäne\AAD_74dc30c01e80 (siehe Hinweis)|

## <a name="custom-adsync-service-accounts"></a>Benutzerdefinierte ADSync-Dienstkonten
Microsoft empfiehlt, den ADSync-Dienst entweder im Rahmen eines virtuellen Dienstkontos oder eines eigenständigen oder gruppenbasierten verwalteten Dienstkontos auszuführen.  Ihr Domänenadministrator kann auch ein Dienstkonto erstellen, das den spezifischen Sicherheitsanforderungen Ihrer Organisation entspricht.   Um das bei der Installation verwendete Dienstkonto anzupassen, wählen Sie unten auf der Seite „Express-Einstellungen“ die Option „Anpassen“.   Die folgenden Optionen sind verfügbar:

- Standardkonto: Azure AD Connect stellt das Dienstkonto wie oben beschrieben bereit.
- Verwaltetes Dienstkonto: Verwenden Sie ein eigenständiges oder gruppenbasiertes verwaltetes Dienstkonto, das von Ihrem Administrator bereitgestellt wird.
- Domänenkonto: Verwenden Sie ein Domänendienstkonto, das von Ihrem Administrator bereitgestellt wird.

![Screenshot: Azure AD Connect-Seite „Express-Einstellungen“ mit den Schaltflächen „Anpassen“ und „Express-Einstellungen verwenden“](media/concept-adsync-service-account/adsync1.png)

![Screenshot: Azure AD Connect-Seite „Erforderliche Komponenten installieren“ mit ausgewählter Option zur Verwendung eines vorhandenen verwalteten Dienstkontos](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>Untersuchen von Änderungen am ADSync-Dienstkonto
Das Ändern der Anmeldeinformationen nach der Installation führt dazu, dass der Dienst nicht gestartet werden kann, den Zugriff auf die Synchronisierungsdatenbank verliert und sich nicht bei Ihren verbundenen Verzeichnissen (Azure und AD DS) authentifiziert.  Dem neuen ADSync-Dienstkonto den Datenbankzugriff zu gewähren, reicht nicht aus, um dieses Problem zu beheben. Es erfolgt keine Synchronisierung, bis die ursprünglichen Anmeldeinformationen wiederhergestellt sind.

Der ADSync-Dienst gibt eine Fehlermeldung im Ereignisprotokoll aus, wenn er nicht gestartet werden kann.  Der Inhalt der Meldung variiert je nachdem, ob die integrierte Datenbank (localdb) oder die Vollversion von SQL Server verwendet wird.  Es folgen Beispiele von eventuell vorhandenen Ereignisprotokolleinträgen.

### <a name="example-1"></a>Beispiel 1

Die AdSync-Dienstverschlüsselungsschlüssel konnten nicht gefunden werden und wurden erneut erstellt.  Die Synchronisierung erfolgt erst wieder, nachdem dieses Problem behoben wurde.

Problembehandlung: Auf die Microsoft Azure AD Sync-Verschlüsselungsschlüssel kann nicht mehr zugegriffen werden, wenn die Anmeldeinformationen für den AdSync-Dienst geändert werden.  Wenn die Anmeldeinformationen geändert wurden, legen Sie das Anmeldekonto mithilfe der Anwendung „Dienste“ wieder auf den ursprünglich konfigurierten Wert (beispielsweise NT SERVICE\AdSync) fest, und starten Sie den Dienst neu.  Dadurch wird der ordnungsgemäße Betrieb des AdSync-Diensts sofort wiederhergestellt.

Im folgenden [Artikel](./whatis-hybrid-identity.md) finden Sie weitere Informationen.

### <a name="example-2"></a>Beispiel 2

Der Dienst konnte nicht gestartet werden, da keine Verbindung mit der lokalen Datenbank (localdb) hergestellt werden konnte.

Problembehandlung: Der Microsoft Azure AD Sync-Dienst verliert die Berechtigung zum Zugriff auf den lokalen Datenbankanbieter, wenn die Anmeldeinformationen des AdSync-Diensts geändert werden.  Wenn die Anmeldeinformationen geändert wurden, legen Sie das Anmeldekonto mithilfe der Anwendung „Dienste“ wieder auf den ursprünglich konfigurierten Wert (beispielsweise NT SERVICE\AdSync) fest, und starten Sie den Dienst neu.  Dadurch wird der ordnungsgemäße Betrieb des AdSync-Diensts sofort wiederhergestellt.

Im folgenden [Artikel](./whatis-hybrid-identity.md) finden Sie weitere Informationen.

Weitere Details: Die folgenden Fehlerinformationen wurden vom Anbieter zurückgegeben:
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).