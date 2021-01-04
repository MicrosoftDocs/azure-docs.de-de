---
title: RDG und Azure MFA Server mit RADIUS – Azure Active Directory
description: Dies ist die Azure Multi-Factor Authentication-Seite, die bei der Bereitstellung des Remotedesktopgateways (RD-Gateway) und des Azure Multi-Factor Authentication-Servers mithilfe von RADIUS Unterstützung bietet.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff89e8c803e0edf5245a62d625a6367d68de96ba
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96742067"
---
# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>Remotedesktopgateway und Azure Multi-Factor Authentication-Server mithilfe von RADIUS

Häufig nutzt das Remotedesktopgateway (RD-Gateway) die lokalen [Netzwerkrichtliniendienste (Network Policy Services, NPS)](/windows-server/networking/core-network-guide/core-network-guide#BKMK_optionalfeatures), um Benutzer zu authentifizieren. In diesem Artikel wird beschrieben, wie RADIUS-Anforderungen aus dem Remotedesktopgateway (über lokale NPS) an den Multi-Factor Authentication-Server weitergeleitet werden. Die Kombination von Azure MFA und RD-Gateway bedeutet, dass Ihre Benutzer von jedem Ort aus auf ihre Arbeitsumgebungen zugreifen können, während die sichere Authentifizierung ausgeführt wird.

Verwenden Sie das RD-Gateway und RADIUS für die Integration in MFA-Server, da die Windows-Authentifizierung für Terminaldienste für Server 2012 R2 nicht unterstützt wird.

Installieren Sie den Multi-Factor Authentication-Server auf einem separaten Server, von dem die RADIUS-Anforderung per Proxy an NPS auf dem Remotedesktop-Gatewayserver zurückgegeben wird. Nachdem NPS den Benutzernamen und das Kennwort überprüft hat, wird eine Antwort an den Multi-Factor Authentication-Server zurückgegeben. Der MFA-Server führt dann den zweiten Authentifizierungsschritt aus und gibt ein Ergebnis an das Gateway zurück.

> [!IMPORTANT]
> Seit dem 1. Juli 2019 bietet Microsoft für neue Bereitstellungen keine MFA-Server mehr an. Neue Kunden, die bei Benutzeranmeldeereignissen eine mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) verlangen möchten, sollten cloudbasierte Azure AD Multi-Factor Authentication verwenden.
>
> Informationen zu den ersten Schritten mit der cloudbasierten MFA finden Sie im [Tutorial: Schützen von Benutzeranmeldeereignissen mit Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Wenn Sie die cloudbasierte mehrstufige Authentifizierung verwenden, informieren Sie sich über das [Integrieren der RADIUS-Authentifizierung für Azure Multi-Factor Authentication](howto-mfa-nps-extension.md).
>
> Bestandskunden, die ihren MFA-Server vor dem 1. Juli 2019 aktiviert haben, können weiterhin die neuesten Versionen und zukünftige Updates herunterladen sowie Anmeldeinformationen zur Aktivierung generieren.

## <a name="prerequisites"></a>Voraussetzungen

- Ein in die Domäne eingebundener Azure MFA-Server. Wenn Sie noch keinen Server installiert haben, können Sie die Schritte unter [Erste Schritte mit Azure Multi-Factor Authentication-Server](howto-mfaserver-deploy.md) ausführen.
- Ein konfigurierter NPS-Server.
- Ein Remotedesktopgateway, das mit Netzwerkrichtliniendiensten authentifiziert wird.

> [!NOTE]
> Dieser Artikel sollte nur für Bereitstellungen von MFA Server verwendet werden. Er gilt nicht für Azure MFA (cloudbasiert).

## <a name="configure-the-remote-desktop-gateway"></a>Konfigurieren des Remotedesktopgateways

Konfigurieren Sie das RD-Gateway, um die RADIUS-Authentifizierung an einen Azure Multi-Factor Authentication-Server zu senden.

1. Klicken Sie im RD-Gateway-Manager mit der rechten Maustaste auf den Servernamen, und wählen Sie **Eigenschaften**.
2. Navigieren Sie zur Registerkarte **RD-CAP-Speicher**, und wählen Sie die Option **Zentraler NPS-Server**.
3. Fügen Sie mindestens einen Azure Multi-Factor Authentication-Server als RADIUS-Server hinzu, indem Sie den Namen oder die IP-Adresse der einzelnen Server eingeben.
4. Erstellen Sie ein gemeinsames Geheimnis für jeden Server.

## <a name="configure-nps"></a>Konfigurieren des Netzwerkrichtlinienservers

Das RD-Gateway verwendet den NPS, um die RADIUS-Anforderung an Azure Multi-Factor Authentication zu senden. Zum Konfigurieren von NPS ändern Sie zuerst die Timeouteinstellungen, um zu verhindern, dass für das RD-Gateway eine Zeitüberschreitung auftritt, bevor die zweistufige Überprüfung abgeschlossen ist. Aktualisieren Sie anschließend NPS, um RADIUS-Authentifizierungen von Ihrem MFA-Server zu erhalten. Verwenden Sie das folgende Verfahren, um NPS zu konfigurieren:

### <a name="modify-the-timeout-policy"></a>Ändern der Timeoutrichtlinie

1. Öffnen Sie in NPS in der linken Spalte das Menü **RADIUS-Clients und Server**, und wählen Sie die Option **RADIUS-Remoteservergruppen**.
2. Wählen Sie die Option **TS GATEWAY SERVER GROUP**.
3. Navigieren Sie zur Registerkarte **Lastenausgleich**.
4. Ändern Sie die Optionen **Sekunden ohne Antwort, bevor eine Anforderung als verworfen gilt** und **Sekunden zwischen Anforderungen, nach denen der Server als nicht verfügbar identifiziert wird** jeweils in einen Wert zwischen 30 und 60 Sekunden. (Falls für den Server während der Authentifizierung weiterhin eine Zeitüberschreitung auftritt, können Sie die Anzahl von Sekunden hier weiter erhöhen.)
5. Navigieren Sie zur Registerkarte **Authentifizierung/Konto**, und stellen Sie sicher, dass die angegebenen RADIUS-Ports den Ports entsprechen, über die der Multi-Factor Authentication-Server lauscht.

### <a name="prepare-nps-to-receive-authentications-from-the-mfa-server"></a>Vorbereiten von NPS zum Empfangen von Authentifizierungen vom MFA-Server

1. Klicken Sie unter „RADIUS-Clients und -Server“ in der linken Spalte auf **RADIUS-Clients**, und wählen Sie die Option **Neu**.
2. Fügen Sie den Azure Multi-Factor Authentication-Server als RADIUS-Client hinzu. Wählen Sie einen Anzeigenamen und geben Sie einen gemeinsamen geheimen Schlüssel an.
3. Öffnen Sie in der linken Spalte das Menü **Richtlinien**, und wählen Sie die Option **Verbindungsanforderungsrichtlinien**. Es sollte eine Richtlinie mit dem Namen „TS GATEWAY AUTHORIZATION POLICY“ angezeigt werden, die beim Konfigurieren des RD-Gateways erstellt wurde. Diese Richtlinie leitet RADIUS-Anforderungen an den Multi-Factor Authentication-Server weiter.
4. Klicken Sie mit der rechten Maustaste auf **TS GATEWAY AUTHORIZATION POLICY**, und wählen Sie die Option **Richtlinie duplizieren**.
5. Öffnen Sie die neue Richtlinie, und navigieren Sie zur Registerkarte **Bedingungen**.
6. Fügen Sie eine Bedingung hinzu, in der der Anzeigename des Clients mit dem in Schritt 2 festgelegten Anzeigenamen für den Azure Multi-Factor Authentication-Server RADIUS-Client übereinstimmt.
7. Navigieren Sie zur Registerkarte **Einstellungen**, und wählen Sie die Option **Authentifizierung**.
8. Ändern Sie die Option für den Authentifizierungsanbieter in **Anforderungen auf diesem Server authentifizieren**. Mit dieser Richtlinie können Sie sicherstellen, dass beim Empfang einer RADIUS-Anforderung durch NPS vom Azure MFA-Server die Authentifizierung lokal erfolgt, anstatt eine RADIUS-Anforderung an den Azure Multi-Factor Authentication-Server zurückzusenden, was zu einer Schleifenbedingung führen würde.
9. Stellen Sie zur Verhinderung einer Schleifenbedingung sicher, dass die neue Richtlinie im Bereich **Verbindungsanforderungsrichtlinien** OBERHALB der Originalrichtlinie angeordnet ist.

## <a name="configure-azure-multi-factor-authentication"></a>Konfigurieren von Azure Multi-Factor Authentication

Der Azure Multi-Factor Authentication-Server wird als RADIUS-Proxy zwischen dem RD-Gateway und NPS konfiguriert.  Er sollte auf einem Domänen-verbundenen Server installiert werden, der vom Remotedesktop-Gatewayserver getrennt ist. Verwenden Sie das folgende Verfahren, um den Azure Multi-Factor Authentication-Server zu konfigurieren.

1. Öffnen Sie den Azure Multi-Factor Authentication-Server, und wählen Sie das Symbol „RADIUS-Authentifizierung“.
2. Aktivieren Sie das Kontrollkästchen **RADIUS-Authentifizierung aktivieren**.
3. Stellen Sie auf der Registerkarte „Clients“ sicher, dass die Ports mit der Konfiguration in NPS übereinstimmen, und wählen Sie dann die Schaltfläche **Hinzufügen**.
4. Fügen Sie die IP-Adresse des RD-Gatewayservers, den Anwendungsnamen (optional) und ein gemeinsames Geheimnis hinzu. Das gemeinsame Geheimnis muss auf dem Azure Multi-Factor Authentication-Server und dem RD-Gateway identisch sein.
3. Navigieren Sie zur Registerkarte **Ziel**, und wählen Sie das Optionsfeld **RADIUS-Server** aus.
4. Wählen Sie die Option **Hinzufügen**, und geben Sie die IP-Adresse, das gemeinsame Geheimnis und die Ports des NPS-Servers ein. Sofern Sie keine zentrale NPS-Instanz nutzen, sind der RADIUS-Client und das RADIUS-Ziel identisch. Der gemeinsame geheime Schlüssel muss mit der Einrichtung im Bereich "RADIUS-Client" des NPS-Servers übereinstimmen.

![RADIUS-Authentifizierung in MFA Server](./media/howto-mfaserver-nps-rdg/radius.png)

## <a name="next-steps"></a>Nächste Schritte

- Integrieren von Azure MFA und [IIS-Web-Apps](howto-mfaserver-iis.md)

- Überprüfen von [Azure Multi-Factor Authentication – Häufig gestellte Fragen](multi-factor-authentication-faq.md)