---
title: SSH-Authentifizierung mit Azure Active Directory
description: Architekturleitfaden für SSH-Integration mit Azure Active Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b77ab0832fa19149c270d6ba5a6641069548cbe
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96172719"
---
# <a name="ssh"></a>SSH  

Secure Shell (SSH) ist ein Netzwerkprotokoll, das Verschlüsselung für den sicheren Betrieb von Netzwerkdiensten in einem ungesicherten Netzwerk bietet. SSH ermöglicht auch die Anmeldung über die Befehlszeile, führt Remotebefehle aus und überträgt Dateien sicher. Dieses Protokoll wird häufig bei UNIX-basierten Systemen wie Linux® verwendet. SSH ersetzt das Telnet-Protokoll, das keine Verschlüsselung in ungesicherten Netzwerken bietet. 

Azure Active Directory (Azure AD) bietet eine VM-Erweiterung (Virtual Machine, virtueller Computer) für Linux®-Systeme, die in Azure ausgeführt werden. 

## <a name="use-when"></a>Wenn folgende Bedingungen vorliegen 

* Arbeiten mit Linux®-VMs, bei denen eine Remoteanmeldung erforderlich ist

* Ausführen von Remotebefehlen unter Linux®-Systemen

* Sichere Übertragung von Dateien in einem ungesicherten Netzwerk

![Diagramm von Azure AD mit dem SSH-Protokoll](./media/authentication-patterns/ssh-auth.png)

SSH mit Azure AD

## <a name="components-of-system"></a>Komponenten des Systems 

* **Benutzer:** Startet den SSH-Client, um eine Verbindung mit den Linux®-VMs einzurichten, und stellt Anmeldeinformationen für die Authentifizierung bereit

* **Webbrowser**: Die Komponente, mit der der Benutzer interagiert. Der Webbrowser kommuniziert mit dem Identitätsanbieter (Azure AD), um den Benutzer sicher zu authentifizieren und autorisieren.

* **SSH-Client**: Steuert den Verbindungseinrichtungsprozess

* **Azure AD**: Authentifiziert die Identität des Benutzers mithilfe des Geräteflows und erstellt das Token für die Linux-VMs

* **Linux-VM**: Akzeptiert das Token und stellt eine erfolgreiche Verbindung bereit

## <a name="implement-ssh-with-azure-ad"></a>Implementieren von SSH mit Azure AD 

* [Vorschau: Anmelden bei einem virtuellen Linux-Computer in Azure mit der Azure Active Directory-Authentifizierung](../../virtual-machines/linux/login-using-aad.md) 

* [Microsoft Identity Platform und der OAuth 2.0-Flow für die Geräteautorisierungsgenehmigung](../develop/v2-oauth2-device-code.md)

* [Integration mit Azure Active Directory (akamai.com)](https://learn.akamai.com/webhelp/enterprise-application-access/enterprise-application-access/GUID-6B16172C-86CC-48E8-B30D-8E678BF3325F.html)

