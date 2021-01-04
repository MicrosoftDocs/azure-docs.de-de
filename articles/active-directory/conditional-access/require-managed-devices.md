---
title: 'Bedingter Zugriff: Erzwingen von verwalteten Geräten – Azure Active Directory'
description: Hier erfahren Sie, wie Sie gerätebasierte bedingte Zugriffsrichtlinien von Azure Active Directory (Azure AD) konfigurieren, die für den Zugriff auf Cloud-Apps die Verwendung verwalteter Geräte erforderlich machen.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: c11b58954eefda67f981d618b04ab2bd69fa6b43
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93077760"
---
# <a name="how-to-require-managed-devices-for-cloud-app-access-with-conditional-access"></a>Anleitung: Vorschreiben der Verwendung verwalteter Geräte für den Zugriff auf Cloud-Apps mithilfe des bedingten Zugriffs

In einer Welt, in der Mobilität und die Cloud an erster Stelle stehen, ermöglicht Azure Active Directory (Azure AD) das einmalige Anmelden bei Apps und Diensten an jedem Ort. Autorisierte Benutzer können über ein breites Spektrum von Geräten (darunter mobile und persönliche Geräte) auf Ihre Cloud-Apps zugreifen. Allerdings gibt es in vielen Umgebungen mindestens einige wenige Apps, auf die der Zugriff nur über Geräte möglich sein sollte, die Ihre Sicherheits- und Compliancestandards erfüllen. Diese Geräte werden auch als verwaltete Geräte bezeichnet. 

In diesem Artikel erfahren Sie, wie Sie Richtlinien für den bedingten Zugriff konfigurieren, die die Verwendung verwalteter Geräte für den Zugriff auf bestimmte Cloud-Apps in Ihrer Umgebung erforderlich machen. 

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie für Cloud-Apps die Verwendung verwalteter Geräte vorschreiben, wird der **bedingte Zugriff von Azure AD** mit der **Geräteverwaltung von Azure AD** verknüpft. Wenn Sie mit einem dieser Bereiche noch nicht vertraut sind, sollten Sie zunächst die folgenden Themen lesen:

- **[Bedingter Zugriff in Azure Active Directory:](./overview.md)** Dieser Artikel enthält eine konzeptionelle Übersicht über den bedingten Zugriff und die entsprechende Terminologie.
- **[Einführung in die Geräteverwaltung in Azure Active Directory:](../devices/overview.md)** Dieser Artikel enthält eine Übersicht über die verschiedenen Optionen, mit denen Sie Geräte unter die Kontrolle der Organisation bringen können. 
- Installieren Sie die [Erweiterung für Windows 10-Konten](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji), damit Chrome ab **Windows 10 Creators Update (Version 1703)** unterstützt wird. Diese Erweiterung ist erforderlich, wenn eine Richtlinie für bedingten Zugriff gerätespezifische Details erfordert.

>[!NOTE] 
> Wir empfehlen die Verwendung einer auf einem Azure AD-Gerät basierenden Richtlinie für bedingten Zugriff, um nach der ersten Geräteauthentifizierung die beste Erzwingung zu erzielen. Dies schließt das Schließen von Sitzungen ein, wenn das Gerät nicht mehr kompatibel und nicht mehr Teil des Gerätecodeflows ist.


## <a name="scenario-description"></a>Beschreibung des Szenarios

Es ist nicht einfach, ein ausgewogenes Gleichgewicht zwischen Sicherheit und Produktivität zu finden. Die zunehmende Verbreitung unterstützter Geräte für den Zugriff auf Ihre Cloudressourcen kommt der Produktivität Ihrer Benutzer zugute. Auf der anderen Seite gibt es wahrscheinlich Ressourcen in Ihrer Umgebung, die nicht für Geräte mit unbekannter Schutzebene zugänglich sein sollen. Für die betroffenen Ressourcen sollten Sie daher sicherstellen, dass Benutzer nur unter Verwendung eines verwalteten Geräts auf sie zugreifen können. 

Mit dem bedingten Zugriff in Azure AD können Sie diese Anforderung mit einer einzigen Richtlinie erfüllen, die den Zugriff gewährt:

- Für ausgewählte Cloud-Apps
- Für ausgewählte Benutzer und Gruppen
- Vorschreiben der Verwendung eines verwalteten Geräts

## <a name="managed-devices"></a>Verwaltete Geräte  

Einfach ausgedrückt: Verwaltete Geräte sind Geräte, die *in irgendeiner Weise* von der Organisation kontrolliert werden. In Azure AD ist die Registrierung bei Azure AD die Voraussetzung für ein verwaltetes Gerät. Bei der Registrierung eines Geräts wird eine Identität für das Gerät in Form eines Geräteobjekts erstellt. Dieses Objekt wird von Azure verwendet, um Statusinformationen zu einem Gerät zu verfolgen. Als Azure AD-Administrator können Sie dieses Objekt bereits zum Ein-/Ausschalten (Aktivieren/Deaktivieren) des Status eines Geräts verwenden.
  
:::image type="content" source="./media/require-managed-devices/32.png" alt-text="Screenshot: Bereich „Gerät“ in Azure AD. Die Elemente zum Aktivieren und Deaktivieren sind hervorgehoben." border="false":::

Zum Registrieren eines Geräts bei Azure AD gibt drei Optionen: 

- **Bei Azure AD registrierte Geräte:** Zum Registrieren eines persönlichen Geräts bei Azure AD.
- **In Azure AD eingebundene Geräte:** Diese Option ermöglicht es, ein Windows 10-Organisationsgerät bei Azure AD zu registrieren, das nicht in ein lokales Active Directory eingebunden ist. 
- **In Azure AD eingebundene Hybridgeräte:** Zum Registrieren eines Organisationsgeräts mit Windows 10 oder einer unterstützten niedrigeren Version bei Azure AD, das in ein lokales Active Directory eingebunden ist.

Diese drei Optionen werden im Artikel [Was ist eine Geräteidentität?](../devices/overview.md) erläutert.

Damit aus einem registrierten Gerät ein verwaltetes Gerät wird, muss das Gerät entweder ein **in Azure AD eingebundenes Hybridgerät** oder ein **als kompatibel markiertes Gerät** sein.  

:::image type="content" source="./media/require-managed-devices/47.png" alt-text="Screenshot: Bereich „Gewähren“ von Azure AD. Die Option „Zugriff gewähren“ sowie Kontrollkästchen, dass Geräte kompatibel sowie hybrid und in Azure eingebunden sein müssen, sind aktiviert." border="false":::
 
## <a name="require-hybrid-azure-ad-joined-devices"></a>In Azure AD eingebundene Hybridgeräte erforderlich

In der Richtlinie für bedingten Zugriff können Sie mit der Option **In Azure AD eingebundene Hybridgeräte erforderlich** angeben, dass auf die ausgewählten Cloud-Apps nur über ein verwaltetes Gerät zugegriffen werden kann. 

:::image type="content" source="./media/require-managed-devices/10.png" alt-text="Screenshot: Bereich „Gewähren“ von Azure AD. „Zugriff gewähren“ ist ausgewählt. Ein Kontrollkästchen, das erfordert, dass Geräte hybrid und in Azure AD eingebunden sein müssen, ist ebenfalls aktiviert." border="false":::

Diese Einstellung gilt nur für Geräte mit Windows 10 oder niedrigeren Versionen, wie z.B. Windows 7 oder Windows 8, die in ein lokales Active Directory eingebunden sind. Sie können diese Geräte nur mit der Azure AD-Einbindung für Hybridgeräte bei Azure AD registrieren – ein [automatischer Vorgang](../devices/hybrid-azuread-join-plan.md) zum Abrufen einer Windows 10-Geräteregistrierung. 

:::image type="content" source="./media/require-managed-devices/45.png" alt-text="Tabelle mit dem Namen, dem aktivierten Status, dem Betriebssystem, der Version, dem Einbindungstyp, dem Besitzer, MDM und dem Kompatibilitätsstatus. Als Kompatibilitätsstatus ist „Nein“ angegeben." border="false":::

Wodurch wird ein in Azure AD eingebundenes Hybridgerät zum verwalteten Gerät?  Bei Geräten, die in ein lokales AD eingebunden sind, wird angenommen, dass die Kontrolle über diese Geräte durch Verwaltungslösungen wie **Configuration Manager** oder **Gruppenrichtlinien** erzwungen wird und die Geräte auf diese Weise verwaltet werden. Da Azure AD über keine Methode verfügt, anhand der festgestellt werden kann, ob eine dieser Methoden auf ein Gerät angewendet wurde, ist das Erforderlichmachen eines in Azure AD eingebundenen Hybridgeräts ein relativ schwacher Mechanismus zum Anfordern eines verwalteten Geräts. Es ist Ihre Aufgabe als Administrator zu ermitteln, ob die für Ihre lokalen domäneneingebundenen Geräte angewandten Methoden stark genug sind, um ein verwaltetes Gerät zu bilden, wenn ein solches Gerät auch ein in Azure AD eingebundenes Hybridgerät ist.

## <a name="require-device-to-be-marked-as-compliant"></a>Markieren des Geräts als kompatibel erforderlich

Die Option *Markieren des Geräts als kompatibel erforderlich* ist die sicherste Form, ein verwaltetes Gerät anzufordern.

:::image type="content" source="./media/require-managed-devices/11.png" alt-text="Screenshot: Bereich „Gewähren“ von Azure AD. „Zugriff gewähren“ ist ausgewählt. Ein Kontrollkästchen, das erfordert, dass ein Gerät als kompatibel markiert wird, ist ebenfalls aktiviert." border="false":::

Diese Option erfordert, dass ein Gerät bei Azure AD registriert und zusätzlich auch als kompatibel markiert wurde von:
         
- Intune
- Ein Drittanbietersystem für die mobile Geräteverwaltung (Mobile Device Management, MDM), das Windows 10-Geräte über Azure AD-Integration verwaltet. MDM-Systeme von Drittanbietern für andere Arten von Gerätebetriebssystemen als Windows 10 werden nicht unterstützt.
 
:::image type="content" source="./media/require-managed-devices/46.png" alt-text="Tabelle mit dem Namen, dem aktivierten Status, dem Betriebssystem, der Version, dem Einbindungstyp, dem Besitzer, MDM und dem Kompatibilitätsstatus. Der Kompatibilitätsstatus ist hervorgehoben." border="false":::

Bei einem als kompatibel markierten Gerät können Sie Folgendes voraussetzen: 

- Die mobilen Geräte, mit denen Ihre Mitarbeiter auf Unternehmensdaten zugreifen, sind verwaltet
- Mobile Apps, die Ihre Mitarbeiter verwenden, sind verwaltet
- Ihre Unternehmensdaten sind durch eine bessere Kontrolle der Zugriffs- und Freigabemöglichkeiten Ihrer Mitarbeiter geschützt
- Das Gerät und seine Apps sind kompatibel mit den Sicherheitsanforderungen des Unternehmens

### <a name="scenario-require-device-enrollment-for-ios-and-android-devices"></a>Szenario: Geräteregistrierung für iOS- und Android-Geräte erforderlich

In diesem Szenario hat Contoso entschieden, dass für den gesamten mobilen Zugriff auf Microsoft 365-Ressourcen ein registriertes Gerät verwendet werden muss. Alle Benutzer melden sich bereits mit Azure AD-Anmeldeinformationen an. Außerdem wurden ihnen Lizenzen zugewiesen, die Azure AD Premium P1 oder P2 und Microsoft Intune umfassen.

Organisationen müssen die folgenden Schritte ausführen, um die Verwendung eines registrierten mobilen Geräts anzufordern.

1. Melden Sie sich beim **Azure-Portal** als globaler Administrator, Sicherheitsadministrator oder Administrator für bedingten Zugriff an.
1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.
1. Wählen Sie **Neue Richtlinie**.
1. Benennen Sie Ihre Richtlinie. Es wird empfohlen, dass Unternehmen einen aussagekräftigen Standard für die Namen ihrer Richtlinien erstellen.
1. Wählen Sie unter **Zuweisungen** die Option **Benutzer und Gruppen** aus.
   1. Wählen Sie unter **Einschließen** die Option **Alle Benutzer** oder bestimmte **Benutzer und Gruppen** aus, auf die Sie diese Richtlinie anwenden möchten. 
   1. Wählen Sie **Fertig** aus.
1. Wählen Sie unter **Cloud-Apps oder -aktionen** > **Einschließen** die Option **Office 365** aus.
1. Wählen Sie unter **Bedingungen** die Option **Geräteplattformen** aus.
   1. Legen Sie **Konfigurieren** auf **Ja** fest.
   1. Schließen Sie **Android** und **iOS** ein.
1. Wählen Sie unter **Zugriffssteuerungen** > **Erteilen** die folgenden Optionen aus:
   - **Markieren des Geräts als kompatibel erforderlich**
1. Bestätigen Sie die Einstellungen und legen Sie **Richtlinie aktivieren** auf **Ein** fest.
1. Wählen Sie **Erstellen** aus, um die Richtlinie zu erstellen und zu aktivieren.

### <a name="known-behavior"></a>Bekanntes Verhalten

Bei Verwendung des [OAuth-Gerätecodeflows](../develop/v2-oauth2-device-code.md) werden das Gewährungssteuerelement „Verwaltetes Gerät erforderlich“ oder eine Gerätestatusbedingung nicht unterstützt. Dies liegt daran, dass das Gerät, das die Authentifizierung ausführt, seinen Gerätestatus nicht für das Gerät bereitstellen kann, das einen Code bereitstellt. Zudem ist der Gerätestatus im Token für das Gerät, das die Authentifizierung ausführt, gesperrt. Verwenden Sie stattdessen das Gewährungssteuerelement „Mehrstufige Authentifizierung erforderlich“.

Unter Windows 7, iOS, Android, macOS und einigen Webbrowsern von Drittanbietern identifiziert Azure AD das Gerät anhand eines Clientzertifikats, das beim Registrieren des Geräts bei Azure AD bereitgestellt wird. Wenn sich ein Benutzer zum ersten Mal über den Browser anmeldet, wird er zum Auswählen des Zertifikats aufgefordert. Der Endbenutzer muss dieses Zertifikat auswählen, bevor der Browser verwendet werden kann.

## <a name="next-steps"></a>Nächste Schritte

[Bewerten Sie die Auswirkungen von Richtlinien für bedingten Zugriff, bevor Sie sie im reinen Berichtsmodus aktivieren](concept-conditional-access-report-only.md).
