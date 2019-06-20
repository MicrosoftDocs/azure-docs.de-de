---
title: Was sind Standortbedingungen beim bedingten Zugriff in Azure Active Directory? | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die auf dem Netzwerkstandort eines Benutzers beruhende Standortbedingung zum Steuern des Zugriffs auf Ihre Cloud-App verwenden können.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.workload: identity
ms.date: 04/12/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98588e0c25439fd4988fe39e06e7042cfa9113cb
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305680"
---
# <a name="what-is-the-location-condition-in-azure-active-directory-conditional-access"></a>Was sind Standortbedingungen beim bedingten Zugriff in Azure Active Directory? 

Mit dem [bedingten Zugriff von Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md) können Sie den Zugriff von autorisierten Benutzern auf Ihre Cloud-Apps steuern. Mit der Standortbedingung einer Richtlinie zum bedingten Zugriff können Sie die Einstellungen der Zugriffssteuerung an die Netzwerkstandorte Ihrer Benutzer knüpfen.

In diesem Artikel erhalten Sie die erforderlichen Informationen, um die Standortbedingung zu konfigurieren.

## <a name="locations"></a>Standorte

Azure AD ermöglicht das einmalige Anmelden bei Geräten, Apps und Diensten von beliebigen Orten im öffentlichen Internet aus. Mithilfe der Standortbedingung können Sie den Zugriff auf Ihre Cloud-Apps auf der Grundlage des Netzwerkstandorts eines Benutzers steuern. Häufige Anwendungsfälle für die Standortbedingung sind:

- Das Vorschreiben von mehrstufiger Authentifizierung für Benutzer beim Zugriff auf einen Dienst, wenn sie sich außerhalb des Unternehmensnetzwerks befinden
- Das Blockieren des Zugriffs auf einen Dienst für Benutzer, die sich in bestimmten Ländern oder Regionen aufhalten.

Ein Standort ist eine Bezeichnung für einen Netzwerkstandort, die entweder einen benannten Standort oder für die mehrstufige Authentifizierung vertrauenswürdige IP-Adressen darstellt.

## <a name="named-locations"></a>Benannte Orte

Mithilfe von benannten Standorten können Sie logische Gruppierungen von IP-Adressbereichen oder Ländern und Regionen erstellen.

Auf der Seite für den bedingten Zugriff können Sie im Abschnitt **Verwalten** auf Ihre benannten Standorte zugreifen.

![Benannte Standorte beim bedingten Zugriff](./media/location-condition/02.png)

Ein benannter Standort besteht aus den folgenden Komponenten:

![Erstellen eines neuen benannten Standorts](./media/location-condition/42.png)

- **Name**: Der Anzeigename eines benannten Orts.
- **IP-Bereiche**: Mindestens ein IPv4-Adressbereich im CIDR-Format. Die Angabe eines IPv6-Adressbereichs wird nicht unterstützt.

   > [!NOTE]
   > Benannte Standorte können derzeit keine IPv6-Adressbereiche enthalten. Das bedeutet, dass IPv6-Bereiche nicht aus einer Richtlinie für bedingten Zugriff ausgeschlossen werden können.

- **Als vertrauenswürdigen Standort markieren**: Ein Flag, das Sie für einen benannten Standort festlegen können, um ihn als vertrauenswürdigen Standort auszuweisen. Normalerweise handelt es sich bei vertrauenswürdigen Standorten um Netzwerkbereiche, die der Kontrolle Ihrer IT-Abteilung unterliegen. Über den bedingten Zugriff hinaus werden vertrauenswürdige benannte Standorte auch in Sicherheitsberichten von Azure Identity Protection und Azure AD verwendet, um [falsch positive](../reports-monitoring/concept-risk-events.md#impossible-travel-to-atypical-locations-1) Meldungen zu reduzieren.
- **Länder/Regionen:** Mithilfe dieser Option können Sie Länder oder Regionen auswählen, um einen benannten Ort zu definieren.
- **Unbekannte Bereiche einschließen** – Einige IP-Adressen sind keinem bestimmten Land oder einer bestimmten Region zugeordnet. Mithilfe dieser Option können Sie auswählen, ob der benannte Standort diese IP-Adressen umfassen soll. Verwenden Sie diese Einstellung, wenn die Richtlinie für den benannten Standort auch für unbekannte Standorte gelten soll.

Die Anzahl von benannten Orten, die Sie konfigurieren können, wird durch die Größe des zugehörigen Objekts in Azure AD eingeschränkt. Organisationen können bis zu 90 benannte Standorte jeweils mit bis zu 1200 IP-Adressbereichen konfigurieren.

Die Richtlinie für bedingten Zugriff gilt für IPv4- und IPv6-Datenverkehr. Derzeit können für benannte Standorte keine IPv6-Adressbereiche konfiguriert werden. Diese Einschränkung führt zu den folgenden Situationen:

- Die Richtlinie für bedingten Zugriff kann nicht auf bestimmte IPv6-Adressbereiche angewendet werden.
- Es dürfen keine bestimmten IPv6-Adressbereiche aus der Richtlinie für bedingten Zugriff ausgeschlossen werden.

Wenn eine Richtlinie für die Anwendung auf „Alle Standorte“ konfiguriert ist, gilt sie für IPv4- und IPv6-Datenverkehr. Für angegebene Länder und Regionen konfigurierte benannte Standorte unterstützen nur IPv4-Adressen. IPv6-Datenverkehr wird nur berücksichtigt, wenn die Option „Unbekannte Bereiche einschließen“ ausgewählt ist.

## <a name="trusted-ips"></a>Vertrauenswürdige IP-Adressen

Ferner können Sie in den [Einstellungen für den mehrstufigen Authentifizierungsdienst](https://account.activedirectory.windowsazure.com/usermanagement/mfasettings.aspx) IP-Adressbereiche konfigurieren, die das lokale Intranet Ihrer Organisation darstellen. Mithilfe dieser Funktion können Sie bis zu 50 IP-Adressbereiche konfigurieren. Die IP-Adressbereiche müssen im CIDR-Format angegeben werden. Weitere Informationen finden Sie unter [Vertrauenswürdige IP-Adressen](../authentication/howto-mfa-mfasettings.md#trusted-ips).  

Wenn Sie vertrauenswürdige IP-Adressen konfiguriert haben, werden Sie als **Für MFA vertrauenswürdige IPs** in der Liste der Standorte für die Standortbedingung angezeigt.

### <a name="skipping-multi-factor-authentication"></a>Überspringen der mehrstufigen Authentifizierung

Auf der Einstellungsseite für den mehrstufigen Authentifizierungsdienst können Sie Benutzer aus dem Unternehmensintranet identifizieren, indem Sie **Für Anforderungen von Partnerbenutzern in meinem Intranet die mehrstufige Authentifizierung überspringen** aktivieren. Diese Einstellung gibt an, dass der Anspruch innerhalb des Unternehmensnetzwerks, der von AD FS ausgestellt wird, als vertrauenswürdig angesehen und dazu verwendet wird, den Benutzer als innerhalb des Unternehmensnetzwerks ansässig zu erkennen. Weitere Informationen finden Sie unter [Aktivieren des Features vertrauenswürdige IPs beim bedingten Zugriff](../authentication/howto-mfa-mfasettings.md#enable-the-trusted-ips-feature-by-using-conditional-access).

Nach dem Aktivieren wird diese Option, einschließlich des benannten Standorts **Für MFA vertrauenswürdige IPs**, auf alle Richtlinien angewendet, für die diese Option ausgewählt ist.

Für mobile und Desktopanwendungen mit langer Sitzungslebensdauer wird der bedingte Zugriff in regelmäßigen Abständen neu ausgewertet. Die Standardeinstellung ist einmal pro Stunde. Wenn der Anspruch innerhalb des Unternehmensnetzwerks nur zum Zeitpunkt der erstmaligen Authentifizierung ausgegeben wird, verfügt Azure AD möglicherweise nicht über eine Liste der vertrauenswürdigen IP-Bereiche. In diesem Fall ist die Bestimmung, ob sich der Benutzer noch im Unternehmensnetzwerk befindet, schwieriger:

1. Überprüfen Sie, ob die IP-Adresse des Benutzers in einem der vertrauenswürdigen IP-Bereiche liegt.
2. Überprüfen Sie, ob die ersten drei Oktette der IP-Adresse des Benutzers mit den ersten drei Oktetten der IP-Adresse der ersten Authentifizierung übereinstimmen. Die IP-Adresse wird mit der ersten Authentifizierung zu dem Zeitpunkt verglichen, zu dem der Anspruch innerhalb des Unternehmensnetzwerks ursprünglich ausgestellt und der Benutzerstandort überprüft wurde.

Wenn bei beiden Schritten ein Fehler auftritt, wird ein Benutzer nicht mehr als vertrauenswürdige IP angesehen.

## <a name="location-condition-configuration"></a>Konfiguration der Standortbedingung

Beim Konfigurieren der Standortbedingung können Sie zwischen diesen Optionen wählen:

- Jeden beliebigen Speicherort
- Alle vertrauenswürdigen Speicherorte
- Ausgewählte Speicherorte

![Konfiguration der Standortbedingung](./media/location-condition/01.png)

### <a name="any-location"></a>Jeden beliebigen Speicherort

Standardmäßig bewirkt das Aktivieren von **Alle Standorte**, dass eine Richtlinie auf alle IP-Adressen angewendet wird, was jede beliebige Adresse im Internet bedeutet. Diese Einstellung ist nicht auf IP-Adressen beschränkt, die von Ihnen als benannter Standort konfiguriert wurden. Wenn Sie **Alle Standorte** aktivieren, können Sie bestimmte Standorte trotzdem noch von einer Richtlinie ausschließen. Beispielsweise können Sie eine Richtlinie auf alle Standorte mit Ausnahme vertrauenswürdiger Standorte anwenden, um den Geltungsbereich auf alle Standorte mit Ausnahme des Unternehmensnetzwerks festzulegen.

### <a name="all-trusted-locations"></a>Alle vertrauenswürdigen Speicherorte

Diese Option gilt für:

- Alle Standorte, die als vertrauenswürdiger Standort gekennzeichnet wurden
- **Für MFA vertrauenswürdige IPs** (sofern konfiguriert)

### <a name="selected-locations"></a>Ausgewählte Speicherorte

Mit dieser Option können Sie einen oder mehrere benannte Speicherorte auswählen. Damit eine Richtlinie mit dieser Einstellung gilt, muss ein Benutzer von einem der ausgewählten Standorte aus eine Verbindung herstellen. Wenn Sie auf **Auswählen** klicken, wird das Steuerelement für die Auswahl von benannten Netzwerken geöffnet und zeigt die Liste der benannten Netzwerke an. In dieser Liste ist außerdem zu sehen, ob der Netzwerkstandort als vertrauenswürdig gekennzeichnet wurde. Der benannte Standort, der als **Für MFA vertrauenswürdige IPs** bezeichnet ist, wird zum Einschließen der IP-Einstellungen verwendet, die auf der Einstellungsseite des mehrstufigen Authentifizierungsdiensts konfiguriert werden können.

## <a name="what-you-should-know"></a>Wichtige Informationen

### <a name="when-is-a-location-evaluated"></a>Wann wird ein Standort ausgewertet?

Richtlinien für bedingten Zugriff werden in diesen Situationen ausgewertet:

- Ein Benutzer meldet sich zum ersten Mal an einer Web-App, mobilen Anwendung oder Desktopanwendung an.
- Für eine mobile Anwendung oder Desktopanwendung, für die die moderne Authentifizierung verwendet wird, wird ein Aktualisierungstoken zum Beschaffen eines neuen Zugriffstokens genutzt. Standardmäßig erfolgt diese Überprüfung einmal pro Stunde.

Für mobile Anwendungen und Desktopanwendungen mit moderner Authentifizierung bedeutet diese Überprüfung, dass eine Änderung des Netzwerkstandorts innerhalb von einer Stunde erkannt wird. Wenn für mobile Anwendungen und Desktopanwendungen keine moderne Authentifizierung genutzt wird, wird die Richtlinie auf jede Tokenanforderung angewendet. Die Häufigkeit der Anforderung kann basierend auf der Anwendung variieren. Ebenso wird die Richtlinie für Webanwendungen bei der ersten Anmeldung angewendet und gilt für die Lebensdauer der Webanwendungssitzung. Aufgrund von Unterschieden bei den Sitzungslebensdauern von Anwendungen variiert auch die Zeit zwischen den Richtlinienauswertungen. Die Richtlinie wird jedes Mal angewendet, wenn die Anwendung ein neues Anmeldetoken anfordert.

Standardmäßig stellt Azure AD stündlich ein Token aus. Nach dem Verlassen des Unternehmensnetzwerks wird die Richtlinie für Anwendungen mit moderner Authentifizierung innerhalb einer Stunde durchgesetzt.

### <a name="user-ip-address"></a>Benutzer-IP-Adresse

Die IP-Adresse, die in der Auswertung der Richtlinie verwendet wird, ist die öffentliche IP-Adresse des Benutzers. Bei Geräten in einem privaten Netzwerk ist diese IP-Adresse nicht die Client-IP des Geräts des Benutzers im Intranet, sondern die vom Netzwerk für das Herstellen der Verbindung mit dem öffentlichen Internet verwendete Adresse.

> [!WARNING]
> Wenn Ihr Gerät nur eine IPv6-Adresse besitzt, wird die Konfiguration der Standortbedingung nicht unterstützt.

### <a name="bulk-uploading-and-downloading-of-named-locations"></a>Massenhoch- und -herunterladen von benannten Standorten

Beim Erstellen oder Aktualisieren benannter Standorte können Sie für eine Massenaktualisierung eine CSV-Datei mit den IP-Adressbereichen hoch- oder herunterladen. Bei einem Upload werden die IP-Bereiche in der Liste durch die aus der Datei ersetzt. Jede Zeile der Datei enthält einen IP-Adressbereich im CIDR-Format.

### <a name="cloud-proxies-and-vpns"></a>Cloud-Proxys und VPNs

Wenn Sie einen in der Cloud gehosteten Proxy oder eine VPN-Lösung verwenden, ist die von Azure AD bei der Auswertung einer Richtlinie verwendete IP-Adresse die IP-Adresse des Proxys. Der XFF-Header (X-Forwarded-For), der die öffentliche IP-Adresse des Benutzers enthält, wird nicht verwendet, da es keine Überprüfung gibt, ob er aus einer vertrauenswürdigen Quelle stammt, sodass er ein Verfahren zum Fälschen einer IP-Adresse darstellen kann.

Wenn ein Cloud-Proxy zum Einsatz kommt, kann eine Richtlinie verwendet werden, mit der die Verwendung von der Domäne beigetretenen Geräten oder des Anspruchs aus dem internen Unternehmensnetzwerk von AD FS vorgeschrieben wird.

### <a name="api-support-and-powershell"></a>API-Unterstützung und PowerShell

API und PowerShell werden für benannte Standorte oder Richtlinien für bedingten Zugriff noch nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- Wenn Sie wissen möchten, wie Sie eine Richtlinie für den bedingten Zugriff konfigurieren, finden Sie Informationen unter [Schnellstart: Anfordern der mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA) für bestimmte Apps über den bedingten Zugriff von Azure Active Directory](app-based-mfa.md).
- Wenn Sie bereit sind, Richtlinien für den bedingten Zugriff für Ihre Umgebung zu konfigurieren, lesen Sie unter [Best Practices für den bedingten Zugriff in Azure Active Directory](best-practices.md) nach.
