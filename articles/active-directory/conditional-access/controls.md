---
title: Was sind die Zugriffssteuerungen beim bedingten Zugriff mit Azure Active Directory? | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Zugriffssteuerungen beim bedingten Zugriff mit Azure Active Directory funktionieren.
services: active-directory
keywords: bedingter Zugriff auf Apps, bedingter Zugriff mit Azure AD, sicherer Zugriff auf Unternehmensressourcen, Richtlinien für bedingten Zugriff
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2019
ms.author: joflore
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: acc30bc18921b79be3b5e5b2ae340eab29dd5bcf
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305756"
---
# <a name="what-are-access-controls-in-azure-active-directory-conditional-access"></a>Was sind die Zugriffssteuerungen beim bedingten Zugriff mit Azure Active Directory?

Mit dem [bedingten Zugriff von Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md) können Sie den Zugriff von autorisierten Benutzern auf Ihre Cloud-Apps steuern. Bei einer Richtlinie für bedingten Zugriff definieren Sie die Reaktion („do this“) für den Grund der Auslösung Ihrer Richtlinie („when this happens“).

![Kontrolle](./media/controls/10.png)

Im Kontext des bedingten Zugriffs gilt:

- „**When this happens**“ fällt unter **Bedingungen**.

- „**Then do this**“ fällt unter **Zugriffssteuerungen**.

Die Kombination aus Bedingungsanweisung und Ihren Kontrollen ergibt eine Richtlinie für den bedingten Zugriff.

![Kontrolle](./media/controls/61.png)

Jedes Steuerelement ist entweder eine Anforderung, die von der Person oder dem System erfüllt werden muss, die bzw. das sich anmeldet, oder eine Einschränkung der Aktionen, die der Benutzer nach der Anmeldung durchführen kann.

Es gibt zwei Arten von Steuerelementen:

- **Gewährungssteuerelemente**: Zum Steuern des Zugriffs

- **Sitzungssteuerelemente**: Zum Einschränken des Zugriffs innerhalb einer Sitzung

In diesem Thema werden die verschiedenen Steuerelemente beschrieben, die beim bedingten Zugriff mit Azure AD verfügbar sind. 

## <a name="grant-controls"></a>Gewährungssteuerelemente

Mit Gewährungssteuerelementen können Sie entweder den gesamten Zugriff sperren oder den Zugriff mit zusätzlichen Anforderungen gewähren, indem Sie die gewünschten Steuerelemente auswählen. Bei mehreren Steuerelementen können Sie folgenden Anforderungen verwenden:

- Alle ausgewählten Steuerelemente müssen erfüllt werden (*AND*)
- Ein ausgewähltes Steuerelement muss erfüllt werden (*OR*)

![Kontrolle](./media/controls/18.png)

### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Mithilfe dieses Steuerelements können Sie die mehrstufige Authentifizierung für den Zugriff auf die angegebene Cloud-App erforderlich machen. Dieses Steuerelement unterstützt die folgenden Anbieter für die mehrstufige Authentifizierung:

- Azure Multi-Factor Authentication

- Ein lokaler Anbieter für die mehrstufige Authentifizierung in Kombination mit Active Directory-Verbunddiensten (AD FS).

Die mehrstufige Authentifizierung unterstützt den Schutz Ihrer Ressourcen vor dem Zugriff durch einen nicht autorisierten Benutzer, der möglicherweise Zugriff auf die primären Anmeldeinformationen eines gültigen Benutzers erlangt hat.

### <a name="compliant-device"></a>Kompatibles Gerät

Sie können die Richtlinien für den bedingten Zugriff auf Geräteebene festlegen. Das Ziel einer gerätebasierten Richtlinie für bedingten Zugriff besteht darin, den Zugriff auf die ausgewählten Cloud-Apps nur über [verwaltete Geräte](require-managed-devices.md) zuzulassen. Das Vorschreiben der Markierung eines Geräts als konform ist eine Option, die Ihnen zur Verfügung steht, um den Zugriff auf verwaltete Geräte zu beschränken. Ein Gerät kann von Intune (jedes Gerätebetriebssystem) oder von Ihrem MDM-System eines Drittanbieters für Windows 10-Geräte als konform markiert werden. MDM-Systeme von Drittanbietern für andere Arten von Gerätebetriebssystemen als Windows 10 werden nicht unterstützt. 

Ihr Gerät muss in Azure AD registriert werden, bevor es als konform markiert werden kann. Zum Registrieren eines Geräts stehen Ihnen drei Optionen zur Auswahl: 

- [In Azure AD registrierte Geräte](../devices/overview.md#azure-ad-registered-devices)
- [In Azure AD eingebundene Geräte](../devices/overview.md#azure-ad-joined-devices)  
- [Über Azure AD Hybrid Join eingebundene Geräte](../devices/overview.md#hybrid-azure-ad-joined-devices)

Weitere Informationen finden Sie unter [Vorschreiben der Verwendung verwalteter Geräte für den Zugriff auf Cloud-Apps mithilfe des bedingten Zugriffs](require-managed-devices.md).

### <a name="hybrid-azure-ad-joined-device"></a>Hybrid in Azure AD eingebundenes Gerät

Ein hybrid in Azure AD eingebundenes Gerät zu verlangen, ist eine weitere Option, die Ihnen zum Konfigurieren von Richtlinien für den gerätebasierten bedingten Zugriff zur Verfügung steht. Diese Anforderung bezieht sich auf Windows-Desktops, Laptops und Enterprise-Tablets, die mit einem lokalen Active Directory verknüpft sind. Wenn diese Option ausgewählt ist, gewährt die Richtlinie für bedingten Zugriff Zugriff bei Zugriffsversuchen von Geräten, die Ihrem lokalen Active Directory-Verzeichnis und Ihrem Azure Active Directory-Verzeichnis angehören.  

Weitere Informationen finden Sie unter [Einrichten von Richtlinien für den gerätebasierten bedingten Zugriff für Azure Active Directory](require-managed-devices.md).

### <a name="approved-client-app"></a>Genehmigte Client-App

Da Ihre Mitarbeiter mobile Geräte sowohl für private als auch für berufliche Zwecke verwenden, sollten Sie die Möglichkeit haben, Unternehmensdaten, auf die mit den Geräten zugegriffen wird, auch dann zu schützen, wenn diese Geräte nicht von Ihnen verwaltet werden.
Sie können [Intune-Richtlinien für den App-Schutz](https://docs.microsoft.com/intune/app-protection-policy) verwenden, um die Daten Ihres Unternehmens unabhängig von der jeweiligen MDM-Lösung (Mobile Device Management, mobile Geräteverwaltung) zu schützen.

Wenn Sie genehmigte Client-Apps verwenden, können Sie die Anforderung festlegen, dass eine Client-App, die auf Ihre Cloud-Apps zugreifen möchte, die [Intune-Richtlinien für den App-Schutz](https://docs.microsoft.com/intune/app-protection-policy) unterstützen muss. Sie können zum Beispiel den Zugriff auf Exchange Online für die Outlook-App einschränken. Eine Richtlinie für den bedingten Zugriff, die genehmigte Client-Apps erfordert, wird auch als [Richtlinie für den App-basierten bedingten Zugriff](app-based-conditional-access.md) bezeichnet. Eine Liste der unterstützten genehmigten Client-Apps finden Sie unter [Genehmigte Client-App als Voraussetzung](technical-reference.md#approved-client-app-requirement).

### <a name="app-protection-policy-preview"></a>App-Schutzrichtlinie (Vorschauversion)

Da Ihre Mitarbeiter mobile Geräte sowohl für private als auch für berufliche Zwecke verwenden, sollten Sie die Möglichkeit haben, Unternehmensdaten, auf die mit den Geräten zugegriffen wird, auch dann zu schützen, wenn diese Geräte nicht von Ihnen verwaltet werden.
Sie können [Intune-Richtlinien für den App-Schutz](https://docs.microsoft.com/intune/app-protection-policy) verwenden, um die Daten Ihres Unternehmens unabhängig von der jeweiligen MDM-Lösung (Mobile Device Management, mobile Geräteverwaltung) zu schützen.

Über App-Schutzrichtlinien können Sie den Zugriff auf Clientanwendungen begrenzen, deren [Intune-App-Schutzrichtlinien](https://docs.microsoft.com/intune/app-protection-policy) bei Azure AD gemeldet wurden. Sie können zum Beispiel den Zugriff auf Exchange Online auf die Outlook-App beschränken, die eine Intune-App-Schutzrichtlinie aufweist. Eine Richtlinie für den bedingten Zugriff, die eine App-Schutzrichtlinie erfordert, wird auch als [Schutzrichtlinie für den App-basierten bedingten Zugriff](app-protection-based-conditional-access.md) bezeichnet. 

Ihr Gerät muss bei Azure AD registriert werden, bevor eine Anwendung als von einer Richtlinie geschützt gekennzeichnet werden kann.

Eine Liste der unterstützen durch Richtlinien geschützten Client-Apps finden Sie unter [Genehmigte Client-App als Voraussetzung](technical-reference.md#app-protection-policy-requirement).


### <a name="terms-of-use"></a>Nutzungsbedingungen

Sie können einen Benutzer in Ihrem Mandanten anfordern, den Nutzungsbedingungen zuzustimmen, bevor der Zugriff auf eine Ressource gewährt wird. Als Administrator können Sie Nutzungsbedingungen durch Hochladen eines PDF-Dokuments konfigurieren und anpassen. Fällt ein Benutzer in den Anwendungsbereich dieses Steuerelements, wird der Zugriff auf eine Anwendung nur gewährt, wenn die Nutzungsbedingungen akzeptiert wurden.

### <a name="custom-controls-preview"></a>Benutzerdefinierte Steuerelemente (Vorschau)

Sie können benutzerdefinierte Steuerelemente für den bedingten Zugriff hinzufügen, die Ihre Benutzer zu einem kompatiblen Dienst umleiten, um weiteren Anforderungen außerhalb von Azure Active Directory zu genügen. Dadurch können Sie bestimmte externe mehrstufige Authentifizierungs- und Überprüfungsanbieter verwenden, um Regeln für den bedingten Zugriff zu erzwingen. Um die Bedingungen dieses Steuerelements zu erfüllen, wird der Browser eines Benutzers auf den externen Dienst umgeleitet, führt alle erforderlichen Authentifizierungs- oder Überprüfungsaktivitäten durch und wird dann wieder an Azure Active Directory umgeleitet. Wenn der Benutzer erfolgreich authentifiziert oder überprüft wurde, bleibt der Benutzer im Vorgangsfluss des bedingten Zugriffs. 

## <a name="custom-controls"></a>Benutzerdefinierte Steuerelemente

Benutzerdefinierte Steuerelemente sind eine Funktion der Azure Active Directory Premium P1 Edition. Wenn Sie benutzerdefinierte Steuerelemente verwenden, werden Ihre Benutzer zu kompatiblen Diensten umgeleitet, um weiteren Anforderungen außerhalb von Azure Active Directory zu genügen. Um die Bedingungen dieses Steuerelements zu erfüllen, wird der Browser eines Benutzers auf den externen Dienst umgeleitet, führt alle erforderlichen Authentifizierungs- oder Überprüfungsaktivitäten durch und wird dann wieder an Azure Active Directory umgeleitet. Azure Active Directory überprüft die Antwort, und wenn der Benutzer erfolgreich authentifiziert oder überprüft wurde, verbleibt der Benutzer im Vorgangsfluss des bedingten Zugriffs.

Diese Steuerelemente ermöglichen die Verwendung von bestimmten externen oder benutzerdefinierten Diensten als Steuerelemente für den bedingten Zugriff und erweitern in der Regel die Funktionen des bedingten Zugriffs.

Zu den Anbietern, die derzeit einen kompatiblen Dienst anbieten gehören:

- [Duo Security](https://duo.com/docs/azure-ca)
- [Entrust Datacard](https://www.entrustdatacard.com/products/authentication/intellitrust)
- [Ping Identity](https://documentation.pingidentity.com/pingid/pingidAdminGuide/index.shtml#pid_c_AzureADIntegration.html)
- RSA
- [SecureAuth](https://docs.secureauth.com/pages/viewpage.action?pageId=47238992#)
- [Silverfort](https://www.silverfort.io/company/using-silverfort-mfa-with-azure-active-directory/)
- [Symantec VIP](https://help.symantec.com/home/VIP_Integrate_with_Azure_AD)
- [Trusona](https://www.trusona.com/docs/azure-ad-integration-guide)

Für weitere Informationen zu diesen Diensten wenden Sie sich direkt an die Anbieter.

### <a name="creating-custom-controls"></a>Erstellen von benutzerdefinierten Steuerelementen

Um ein benutzerdefiniertes Steuerelement zu erstellen, sollten Sie sich zuerst an den Anbieter wenden, den Sie verwenden möchten. Jeder Nicht-Microsoft-Anbieter hat seine eigenen Prozesse und Anforderungen für das Registrieren, das Abonnieren, oder um auf andere Weise Teil des Diensts zu werden, und um anzugeben, dass Sie sich in den bedingten Zugriff integrieren möchten. Zu diesem Zeitpunkt wird der Anbieter einen Block von Daten im JSON-Format für Sie bereitstellen. Diese Daten ermöglichen dem Anbieter und dem bedingten Zugriff, für Ihren Mandanten zusammenzuarbeiten. Diese Daten erstellen das neue Steuerelement und definieren, wie der bedingte Zugriff feststellen kann, ob Ihre Benutzer die Überprüfung mit dem Anbieter erfolgreich ausgeführt haben.

Benutzerdefinierte Steuerelemente können nicht mit der Identity Protection-Automatisierung verwendet werden, die eine mehrstufige Authentifizierung erfordert. Sie können auch nicht zum Erhöhen von Rollen im Privileged Identity Manager (PIM) eingesetzt werden.

Kopieren Sie die JSON-Daten, und fügen Sie sie in das entsprechende Textfeld ein. Verändern Sie JSON-Daten nicht, es sei denn, Sie verstehen explizit die Änderungen, die Sie vornehmen möchten. Jede Änderung kann die Verbindung zwischen dem Anbieter und Microsoft unterbrechen und möglicherweise den Zugriff auf Ihre Konten für Sie und Ihre Benutzer sperren.

Die Option zum Erstellen eines benutzerdefinierten Steuerelements befindet sich im Abschnitt **Verwalten** der Seite **Bedingter Zugriff**.

![Kontrolle](./media/controls/82.png)

Wenn Sie auf **New custom control** (Neues benutzerdefiniertes Steuerelement) klicken, wird ein Blatt geöffnet und ein Textfeld für die JSON-Daten des Steuerelements angezeigt.  

![Kontrolle](./media/controls/81.png)

### <a name="deleting-custom-controls"></a>Löschen von benutzerdefinierten Steuerelementen

Um ein benutzerdefiniertes Steuerelement zu löschen, müssen Sie zunächst sicherstellen, dass es in keiner der Richtlinien für bedingten Zugriff verwendet wird. Ist der Vorgang einmal abgeschlossen:

1. Wechseln Sie zur Liste der benutzerdefinierten Steuerelemente

2. Klicken Sie auf „...“  

3. Klicken Sie auf **Löschen**.

### <a name="editing-custom-controls"></a>Bearbeiten von benutzerdefinierten Steuerelementen

Um ein benutzerdefiniertes Steuerelement zu bearbeiten, müssen Sie das aktuelle Steuerelement löschen und ein neues Steuerelement mit den aktualisierten Informationen erstellen.

## <a name="session-controls"></a>Sitzungssteuerelemente

Sitzungssteuerelemente ermöglichen das Einschränken der Benutzeroberfläche innerhalb einer Cloud-App. Die Sitzungssteuerelemente werden von Cloud-Apps erzwungen und verlassen sich auf zusätzliche Informationen über die Sitzung, die der App von Azure AD bereitgestellt werden.

![Kontrolle](./media/controls/31.png)

### <a name="use-app-enforced-restrictions"></a>Verwenden von App-erzwungenen Einschränkungen

Sie können dieses Steuerelement verwenden, um von Azure AD anzufordern, die Geräteinformationen an die ausgewählten Cloud-Apps zu übergeben. Mithilfe der Geräteinformationen können Cloud-Apps herausfinden, ob eine Verbindung von einem konformen oder einem in die Domäne eingebundenen Gerät initiiert wird. Diese Steuermöglichkeit unterstützt nur SharePoint Online und Exchange Online als ausgewählte Cloud-Apps. Bei Auswahl werden die Geräteinformationen von der Cloud-App dazu verwendet, Benutzern je nach Gerätezustand eine eingeschränkte oder vollständige Benutzeroberfläche zur Verfügung zu stellen.

Weitere Informationen finden Sie unter:

- [Aktivieren des eingeschränkten Zugriffs mit SharePoint Online](https://aka.ms/spolimitedaccessdocs)

- [Aktivieren des eingeschränkten Zugriffs mit Exchange Online](https://aka.ms/owalimitedaccess)

## <a name="next-steps"></a>Nächste Schritte

- Wenn Sie wissen möchten, wie Sie eine Richtlinie für den bedingten Zugriff konfigurieren, finden Sie Informationen unter [Schnellstart: Anfordern der mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA) für bestimmte Apps über den bedingten Zugriff von Azure Active Directory](app-based-mfa.md).

- Wenn Sie bereit sind, Richtlinien für den bedingten Zugriff für Ihre Umgebung zu konfigurieren, lesen Sie unter [Best Practices für den bedingten Zugriff in Azure Active Directory](best-practices.md) nach.
