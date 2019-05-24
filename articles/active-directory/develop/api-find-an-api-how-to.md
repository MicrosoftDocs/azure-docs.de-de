---
title: Suchen einer bestimmten API, die für eine benutzerdefinierte Anwendung benötigt wird | Microsoft-Dokumentation
description: Konfigurieren der Berechtigungen, die für den Zugriff auf eine bestimmte API in einer benutzerdefinierten Azure AD-Anwendung benötigt werden
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a017c13008288b26ddb11bf58be1966d652bbae
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540553"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Suchen einer bestimmten API, die für eine benutzerdefinierte Anwendung benötigt wird

Für den Zugriff auf APIs müssen Zugriffsbereiche und Rollen konfiguriert werden. Wenn Sie die Web-APIs einer Ressourcenanwendung für Clientanwendungen verfügbar machen möchten, müssen Sie Zugriffsbereiche und Rollen für die API konfigurieren. Wenn eine Clientanwendung auf eine Web-API zugreifen soll, müssen Sie Zugriffsberechtigungen für die API in der App-Registrierung konfigurieren.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurieren einer Ressourcenanwendung zum Bereitstellen von Web-APIs

Wenn Sie Ihre Web-API verfügbar machen, wird die API in der Liste **Hiermit wählen Sie eine API aus.** angezeigt, wenn Berechtigungen zu einer App-Registrierung hinzugefügt werden. Führen Sie zum Hinzufügen von Zugriffsbereichen die Schritte unter [Hinzufügen von Zugriffsbereichen zu Ihrer Ressourcenanwendung](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) aus.

## <a name="configuring-a-client-application-to-access-web-apis"></a>Konfigurieren einer Clientanwendung für den Zugriff auf Web-APIs

Wenn Sie Ihrer App-Registrierung Berechtigungen hinzufügen, können Sie für verfügbar gemachte Web-APIs **API-Zugriff hinzufügen**. Führen Sie zum Zugriff auf Web-APIs die Schritte unter [So fügen Sie Anmeldeinformationen oder Zugriffsberechtigungen für Web-APIs hinzu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) aus.

## <a name="next-steps"></a>Nächste Schritte

-   [Integrieren von Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

-   [Grundlegendes zum Azure Active Directory-Anwendungsmanifest](https://docs.microsoft.com/azure/active-directory/develop/active-directory-application-manifest)


