---
title: Cookieeinstellungen für den Anwendungsproxy – Azure Active Directory | Microsoft-Dokumentation
description: Azure Active Directory (Azure AD) umfasst Zugriffs- und Sitzungscookies für den Zugriff auf lokale Anwendungen über den Anwendungsproxy. In diesem Artikel erfahren Sie, wie Sie die Cookieeinstellungen verwenden und konfigurieren.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2e7f1bb54ce316a10eca0d020519779b0536c9e
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825743"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Cookieeinstellungen für den Zugriff auf lokale Anwendungen in Azure Active Directory

Azure Active Directory (Azure AD) umfasst Zugriffs- und Sitzungscookies für den Zugriff auf lokale Anwendungen über den Anwendungsproxy. Finden Sie heraus, wie die Cookieeinstellungen für den Anwendungsproxy verwendet werden. 

## <a name="what-are-the-cookie-settings"></a>Wie lauten die Cookieeinstellungen?

Der [Anwendungsproxy](application-proxy.md) verwendet die folgenden Einstellungen für Zugriffs- und Sitzungscookies.

| Cookieeinstellung | Standard | BESCHREIBUNG | Empfehlungen |
| -------------- | ------- | ----------- | --------------- |
| Nur-HTTP-Cookie verwenden | **Nein** | Bei Festlegung von **Ja** kann der Anwendungsproxy das HTTPOnly-Flag in HTTP-Antwortheadern einschließen. Dieses Flag bietet zusätzliche Sicherheitsvorteile, z.B. wird das Kopieren oder Ändern von Cookies über ein clientseitiges Scripting (CSS) verhindert.<br></br><br></br>Vor der Unterstützung der HTTP-Only-Einstellung verschlüsselte der Anwendungsproxy Cookies und übermittelte sie über einen sicheren SSL-Kanal, um Schutz vor Änderungen bereitzustellen. | Verwenden Sie **Ja**, um von den zusätzlichen Sicherheitsvorteilen zu profitieren.<br></br><br></br>Verwenden Sie **Nein** für Clients oder Benutzer-Agents, die keinen Zugriff auf das Sitzungscookie benötigen. Verwenden Sie beispielsweise **Nein** für einen RDP- oder MTSC-Client, der über den Anwendungsproxy eine Verbindung mit einem Remotedesktop-Gatewayserver herstellt.|
| Sicheres Cookie verwenden | **Nein** | Bei Festlegung von **Ja** kann der Anwendungsproxy das Flag „Secure“ in HTTP-Antwortheadern einschließen. Sichere Cookies erhöhen die Sicherheit, weil sie über einen TLS-gesicherten Kanal wie z.B. HTTPS übertragen werden. Dadurch wird verhindert, dass Cookies aufgrund der Übertragung in Klartext durch Unbefugte eingesehen werden können. | Verwenden Sie **Ja**, um von den zusätzlichen Sicherheitsvorteilen zu profitieren.|
| Beständiges Cookie verwenden | **Nein** | Bei Festlegung auf **Ja** kann der Anwendungsproxy Zugriffscookies so festlegen, dass sie beim Schließen des Webbrowsers nicht ablaufen. Cookies bleiben erhalten, bis das Zugriffstoken abläuft oder der Benutzer die persistenten Cookies manuell löscht. | Verwenden Sie **Nein** wegen des Sicherheitsrisikos, das mit der Beibehaltung der Authentifizierung von Benutzern verbunden ist.<br></br><br></br>Wir empfehlen, **Ja** nur für ältere Anwendungen zu verwenden, die Cookies nicht zwischen Prozessen gemeinsam verwenden können. Es ist besser, Ihre Anwendung zur gemeinsamen Nutzung von Cookies zwischen Prozessen zu aktualisieren, als beständige Cookies zu verwenden. Angenommen, Sie benötigen beständige Cookies, damit ein Benutzer von einer SharePoint-Website aus Office-Dokumente in der Explorer-Ansicht öffnen kann. Ohne beständige Cookies kommt es bei diesem Vorgang möglicherweise zu Fehlern, wenn die Zugriffscookies nicht vom Browser, dem Explorer-Prozess und dem Office-Prozess gemeinsam verwendet werden. |

## <a name="set-the-cookie-settings---azure-portal"></a>Festlegen der Cookieeinstellungen – Azure-Portal
So legen Sie die Cookieeinstellungen im Azure-Portal fest

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 
2. Navigieren Sie zu **Azure Active Directory** > **Unternehmensanwendungen** > **Alle Anwendungen**.
3. Wählen Sie die Anwendung aus, für die Sie eine Cookieeinstellung aktivieren möchten.
4. Klicken Sie auf **Anwendungsproxy**.
5. Legen Sie unter **Zusätzliche Einstellungen** die Cookieeinstellung auf **Ja** oder **Nein** fest.
6. Klicken Sie zum Übernehmen der Änderungen auf **Speichern**. 

## <a name="view-current-cookie-settings---powershell"></a>Anzeigen der aktuellen Cookieeinstellungen – PowerShell

Um die aktuellen Cookieeinstellungen für die Anwendung anzuzeigen, verwenden Sie diesen PowerShell-Befehl:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Festlegen der Cookieeinstellungen – PowerShell

In den folgenden PowerShell-Befehlen ist ```<ObjectId>``` die ObjectId der Anwendung. 

**Nur-HTTP-Cookie** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Sicheres Cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Beständige Cookies**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
