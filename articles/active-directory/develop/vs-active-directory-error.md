---
title: Diagnostizieren von Fehlern mit dem verbundenen Dienst für Azure Active Directory
description: Der verbundene Dienst für Active Directory hat einen inkompatiblen Authentifizierungstyp erkannt.
services: active-directory
author: ghogen
manager: douge
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6f151251d76965cf1bc86216eac15a08f1adbc6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60296791"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnostizieren von Fehlern mit dem verbundenen Dienst für Azure Active Directory

Beim Erkennen des vorherigen Authentifizierungscodes hat der verbundene Dienst für Azure Active Directory einen inkompatiblen Authentifizierungstyp erkannt.

Um den vorherigen Authentifizierungscode in einem Projekt richtig erkennen zu können, muss das Projekt erstellt werden.  Wenn dieser Fehler angezeigt wird und in Ihrem Projekt kein vorheriger Authentifizierungscode enthalten ist, erstellen Sie Ihr Projekt neu, und versuchen Sie es nochmals.

## <a name="project-types"></a>Projekttypen

Der verbundene Dienst überprüft den Typ des Projekts, das Sie entwickeln, um die richtige Authentifizierungslogik in das Projekt einzufügen. Wenn ein Controller im Projekt von `ApiController` abgeleitet wird, gilt das Projekt als WebAPI-Projekt. Wenn alle Controller im Projekt von `MVC.Controller` abgeleitet werden, gilt das Projekt als MVC-Projekt. Der verbundene Dienst unterstützt keinen anderen Projekttyp.

## <a name="compatible-authentication-code"></a>Kompatibler Authentifizierungscode

Der verbundene Dienst überprüft auch Authentifizierungseinstellungen, die zuvor konfiguriert wurden oder mit dem Dienst kompatibel sind. Wenn alle Einstellungen vorhanden sind, gilt dies als eintrittsinvarianter Fall, und der verbundene Dienst wird geöffnet und zeigt die Einstellungen an.  Wenn nur einige der Einstellungen vorhanden sind, wird es als Fehlerfall betrachtet.

In einem MVC-Projekt überprüft der verbundene Dienst die folgenden Einstellungen, die aus der vorherigen Verwendung des Diensts resultieren:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Außerdem überprüft der verbundene Dienst die folgenden Einstellungen in einem Web-API-Projekt, die aus der vorherigen Verwendung des Diensts resultieren:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Nicht kompatibler Authentifizierungscode

Abschließend versucht der verbundene Dienst, Versionen von Authentifizierungscode zu erkennen, die mit früheren Versionen von Visual Studio konfiguriert wurden. Wenn Sie diesen Fehler erhalten, ist ein nicht kompatibler Authentifizierungstyp in Ihrem Projekt vorhanden. Der verbundene Dienst erkennt die folgenden Authentifizierungstypen aus früheren Versionen von Visual Studio:

* Windows-Authentifizierung
* Einzelne Benutzerkonten
* Organisationskonten

Der verbundene Dienst sucht in Ihrer Datei `web.config` nach dem `authentication`-Element, um die Windows-Authentifizierung in einem MVC-Projekt zu erkennen.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

Zum Erkennen der Windows-Authentifizierung in einem Web-API-Projekt sucht der verbundene Dienst nach dem `IISExpressWindowsAuthentication`-Element in der `.csproj`-Datei Ihres Projekts:

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Um die Authentifizierung „Einzelne Benutzerkonten“ zu erkennen, sucht der verbundene Dienst in der Datei `packages.config` nach dem Paketelement.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

Zum Erkennen der alten Form der Organisationskontoauthentifizierung sucht der verbundene Dienst nach dem folgenden Element in der Datei `web.config`:

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

Sie können den Authentifizierungstyp ändern, indem Sie den inkompatiblen Authentifizierungstyp entfernen und versuchen, den verbundenen Dienst wieder hinzuzufügen.

Weitere Informationen finden Sie unter [Authentifizierungsszenarien für Azure AD](authentication-scenarios.md).
