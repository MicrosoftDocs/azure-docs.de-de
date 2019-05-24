---
title: Ermitteln, wann ein bestimmter Benutzer auf eine Anwendung zugreifen kann | Microsoft-Dokumentation
description: Ermitteln, wann ein ausgesprochen wichtiger Benutzer auf eine Anwendung zugreifen kann, die Sie für die Benutzerbereitstellung mit Azure AD konfiguriert haben
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08a78a311771395f7c2113f03d5137d59999d7a7
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783868"
---
# <a name="find-out-when-a-specific-user-will-be-able-to-access-an-application"></a>Ermitteln, wann ein bestimmter Benutzer auf eine Anwendung zugreifen kann
Bei Verwendung der automatischen Benutzerbereitstellung mit einer Anwendung stellt Azure AD in regelmäßigen Zeitintervallen (in der Regel alle 10 Minuten) automatisch Benutzerkonten in einer App, die auf Dingen wie [Benutzer- und Gruppenzuordnung](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) basiert, bereit und aktualisiert diese.

## <a name="how-long-does-it-take"></a>Wie lange dauert es?

Der Zeitaufwand für die Bereitstellung eines bestimmten Benutzers hängt hauptsächlich davon ab, ob bereits eine anfängliche vollständige Synchronisierung erfolgt ist.

Die erste Synchronisierung zwischen Azure AD und einer App kann zwischen 20 Minuten und mehreren Stunden dauern. Dies hängt von der Größe des Azure AD-Verzeichnisses und der Anzahl der Benutzer im Bereitstellungsbereich ab. 

Nachfolgende Synchronisierungen nach der anfänglichen Synchronisierung werden schneller durchgeführt (z. B. innerhalb von 10 Minuten), da der Bereitstellungsdienst Wasserzeichen speichert, die den Status beider Systeme nach der Erstsynchronisierung darstellen, wodurch die Leistung nachfolgender Synchronisierungen verbessert wird.

## <a name="how-to-check-the-status-of-a-user"></a>Überprüfen des Benutzerstatus

Informationen zum Bereitstellungsstatus für einen ausgewählten Benutzer finden Sie in den Überwachungsprotokollen in Azure AD.

Sie können auf die Überwachungsprotokolle für die Bereitstellung im Azure-Portal auf der Registerkarte **Azure Active Directory &gt; Unternehmens-Apps &gt; \[Anwendungsname\] &gt; Überwachungsprotokolle** zugreifen. Filtern Sie die Protokolle nach der Kategorie **Kontobereitstellung**, um nur die Bereitstellungsereignisse für die jeweilige App anzuzeigen. Sie können auf Grundlage der „übereinstimmenden ID“ nach Benutzern suchen, die für die Benutzer in den Attributzuordnungen konfiguriert wurde. 

Wenn Sie z. B. den „Benutzerprinzipalnamen“ oder die „E-Mail-Adresse“ als übereinstimmendes Attribut auf der Azure AD-Seite konfiguriert haben und der Benutzer nicht bereitgestellt wurde, weist dies den Wert „audrey@contoso.com“ auf. Durchsuchen Sie dann die Überwachungsprotokolle nach „audrey@contoso.com“, und überprüfen Sie die zurückgegebenen Einträge.

Die Überwachungsprotokolle für die Bereitstellung zeichnen alle Vorgänge auf, die vom Bereitstellungsdienst durchgeführt werden, einschließlich der Folgenden:

* Abfragen von Azure AD nach zugewiesenen Benutzern, die sich im Bereitstellungsbereich befinden
* Abfragen der Ziel-App nach dem Vorkommen dieser Benutzer
* Vergleichen der Benutzerobjekte zwischen dem System
* Hinzufügen, Aktualisieren oder Deaktivieren des Benutzerkontos im Zielsystem auf Basis des Vergleichs

## <a name="next-steps"></a>Nächste Schritte
[Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)
