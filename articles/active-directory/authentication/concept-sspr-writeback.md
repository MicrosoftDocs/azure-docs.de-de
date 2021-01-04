---
title: Lokales Kennwortrückschreiben mit Self-Service-Kennzurücksetzung – Azure Active Directory
description: Erfahren Sie, wie Ereignisse zu Kennwortänderungen oder -zurücksetzungen in Azure Active Directory in eine lokale Verzeichnisumgebung zurückgeschrieben werden können.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 53f416a23dbb47660097c41ada09c8c135434bcb
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743648"
---
# <a name="how-does-self-service-password-reset-writeback-work-in-azure-active-directory"></a>Funktionsweise des Rückschreibens von Self-Service-Kennwortzurücksetzungen in Azure Active Directory

Mit der Self-Service-Kennwortzurücksetzung (SSPR) in Azure Active Directory (Azure AD) können Benutzer ihre Kennwörter in der Cloud zurücksetzen. Die meisten Unternehmen verfügen aber auch über eine lokale Active Directory Domain Services-Umgebung (AD DS) mit ihren Benutzern. Kennwortrückschreiben ist eine Funktion, die mit [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) aktiviert wurde und es ermöglicht, Kennwortänderungen in der Cloud in Echtzeit in ein vorhandenes lokales Verzeichnis zurückzuschreiben. Wenn in dieser Konfiguration Benutzer ihre Kennwörter mithilfe von SSPR in der Cloud ändern oder zurücksetzen, werden die aktualisierten Kennwörter auch in die lokale AD DS Umgebung zurückgeschrieben.

> [!IMPORTANT]
> In diesem Konzeptartikel erfahren Administratoren, wie das Rückschreiben der Self-Service-Kennwortzurücksetzung funktioniert. Wenn Sie bereits als Endbenutzer für die Self-Service-Kennwortzurücksetzung registriert sind und wieder zu Ihrem Konto zurückkehren müssen, gehen Sie zu https://aka.ms/sspr.
>
> Wenn Ihr IT-Team die Möglichkeit zum Zurücksetzen Ihres eigenen Kennworts nicht aktiviert hat, wenden Sie sich an den Helpdesk, um weitere Unterstützung zu erhalten.

Das Kennwortrückschreiben wird in Umgebungen unterstützt, die folgende Hybrididentitätsmodelle verwenden:

* [Kennworthashsynchronisierung](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Passthrough-Authentifizierung](../hybrid/how-to-connect-pta.md)
* [Active Directory-Verbunddienste (AD FS)](../hybrid/how-to-connect-fed-management.md)

Kennwortrückschreiben bietet die folgenden Features:

* **Erzwingung von lokalen Active Directory Domain Services-Kennwortrichtlinien (AD DS):** Wenn ein Benutzer sein Kennwort zurücksetzt, wird es überprüft, um sicherzustellen, dass es die lokalen AD DS-Richtlinien erfüllt, bevor es in diesem Verzeichnis committet wird. Diese Überprüfung umfasst das Überprüfen des Verlaufs, der Komplexität, des Alters, der Kennwortfilter und aller weiteren Kennwortbeschränkungen, die Sie in der lokalen Instanz von lokalen AD DS definiert haben.
* **Feedback ohne Verzögerung**:  Die Kennwortrückschreibung ist ein synchroner Vorgang. Benutzer werden sofort benachrichtigt, wenn ihre Kennwörter nicht der Richtlinie entsprechen oder das Zurücksetzen oder Ändern des Kennworts aus irgendeinem Grund nicht möglich ist.
* **Unterstützung für das Ändern von Kennwörtern über den Zugriffsbereich und Microsoft 365**: Wenn Verbundbenutzer oder Benutzer mit Kennworthashsynchronisierung ihre abgelaufenen oder noch nicht abgelaufenen Kennwörter ändern möchten, werden diese Kennwörter in AD DS zurückgeschrieben.
* **Unterstützung für Kennwortrückschreiben, wenn die Kennwörter im Azure-Portal von einem Administrator zurückgesetzt werden**: Wenn ein Administrator das Kennwort eines Benutzers im [Azure-Portal](https://portal.azure.com) zurücksetzt und der Benutzer ein Verbundkonto oder ein Konto mit Kennworthashsynchronisierung verwendet, wird das Kennwort lokal zurückgeschrieben. Diese Funktionalität wird im Office-Verwaltungsportal derzeit nicht unterstützt.
* **Keine Notwendigkeit zur Festlegung von eingehenden Firewallregeln:** Kennwortrückschreiben verwendet ein Azure Service Bus Relay als zugrunde liegenden Kommunikationskanal. Die gesamte Kommunikation geht über Port 443.

> [!NOTE]
> Administratorkonten in geschützten Gruppen im lokalen Active Directory können zum Kennwortrückschreiben verwendet werden. Administratoren können ihr Kennwort in der Cloud ändern, aber nicht die Kennwortzurücksetzung zum Zurücksetzen eines vergessenen Kennworts verwenden. Weitere Informationen zu geschützten Gruppen finden Sie unter [Anhang C: Geschützte Konten und Gruppen in Active Directory](/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

Nutzen Sie das folgende Tutorial, um sich mit dem SSPR-Rückschreiben vertraut zu machen:

> [!div class="nextstepaction"]
> [Tutorial: Aktivieren des Rückschreibens von Self-Service-Kennwortzurücksetzungen (SSPR)](./tutorial-enable-sspr-writeback.md)

## <a name="how-password-writeback-works"></a>Funktionsweise der Kennwortrückschreibung

Wenn Verbundbenutzer oder Benutzer mit Kennworthashsynchronisierung versuchen, ihr Kennwort in der Cloud zurückzusetzen oder zu ändern, geschieht Folgendes:

1. Es wird überprüft, über welche Art von Kennwort der Benutzer verfügt. Wenn das Kennwort des Benutzers lokal verwaltet wird:
   * Es wird überprüft, ob der Dienst für Rückschreiben aktiviert ist und ausgeführt wird. Wenn dies der Fall ist, kann der Benutzer fortfahren.
   * Wenn der Dienst für Rückschreiben nicht aktiv ist, wird dem Benutzer mitgeteilt, dass sein Kennwort derzeit nicht zurückgesetzt werden kann.
1. Im nächsten Schritt muss der Benutzer sich erfolgreich authentifizieren und gelangt dann zur Seite **Kennwort zurücksetzen**.
1. Der Benutzer wählt ein neues Kennwort aus und bestätigt es.
1. Wenn der Benutzer **Senden** auswählt, wird das Klartextkennwort mit einem symmetrischen Schlüssel verschlüsselt, der beim Einrichten des Kennwortrückschreibens erstellt wurde.
1. Das verschlüsselte Kennwort wird in eine Nutzlast eingeschlossen, die über einen HTTPS-Kanal an Ihr mandantenspezifisches Service Bus Relay gesendet wird (dieses wurde beim Einrichten der Kennwortrückschreibung festgelegt). Dieses Relay wird durch ein zufällig generiertes Kennwort geschützt, das nur der lokalen Installation bekannt ist.
1. Sobald die Nachricht den Service Bus erreicht hat, wird der Endpunkt für die Kennwortzurücksetzung automatisch aktiviert, und der Endpunkt erkennt, dass eine Anforderung zur Zurücksetzung aussteht.
1. Der Dienst sucht dann nach dem Benutzer, indem er das Cloudankerattribut verwendet. Damit die Suche erfolgreich ist, müssen die folgenden Bedingungen erfüllt sein:

   * Das Benutzerobjekt muss im AD DS-Connectorbereich vorhanden sein.
   * Das Benutzerobjekt muss mit dem entsprechenden Metaverseobjekt (MV-Objekt) verknüpft sein.
   * Das Benutzerobjekt muss mit dem entsprechenden Azure AD-Connectorobjekt verknüpft sein.
   * Für die Verknüpfung zwischen dem AD DS-Connectorobjekt und dem MV-Objekt muss die Synchronisierungsregel `Microsoft.InfromADUserAccountEnabled.xxx` festgelegt sein.

   Sobald der Aufruf aus der Cloud eingetroffen ist, verwendet das Synchronisierungsmodul das **cloudAnchor**-Attribut, um das Azure AD-Connectorbereichsobjekt nachzuschlagen. Es folgt dann dem Link zurück zum MV-Objekt und folgt anschließend dem Link zurück zum AD DS-Objekt. Da mehrere AD DS-Objekte (mehrere Gesamtstrukturen) für denselben Benutzer vorliegen können, wählt das Synchronisierungsmodul das richtige Objekt basierend auf der `Microsoft.InfromADUserAccountEnabled.xxx`-Verbindung aus.

1. Nachdem das Benutzerkonto ermittelt ist, wird versucht, das Kennwort direkt in der geeigneten AD DS-Gesamtstruktur zurückzusetzen.
1. Wenn dieser Vorgang erfolgreich war, wird der Benutzer darüber informiert, dass das Kennwort geändert wurde.

   > [!NOTE]
   > Wenn der Kennworthash des Benutzers mithilfe der Kennworthashsynchronisierung mit Azure AD synchronisiert wird, kann es vorkommen, dass die lokale Kennwortrichtlinie schwächer ist als die Kennwortrichtlinie der Cloud. In diesem Fall wird die lokale Kennwortrichtlinie erzwungen. Diese Richtlinie stellt das Erzwingen Ihrer lokalen Richtlinie in der Cloud sicher, unabhängig davon, ob Sie für die Bereitstellung von einmaligem Anmelden Kennworthashsynchronisierung oder Verbund verwenden.

1. Wenn bei diesem Vorgang ein Fehler auftritt, wird der Benutzer per Fehlermeldung dazu aufgefordert, es erneut zu versuchen. Der Vorgang kann aus folgenden Gründen zu einem Fehler führen:
    * Der Dienst war nicht verfügbar.
    * Das ausgewählte Kennwort entspricht nicht den Richtlinien der Organisation.
    * Der Benutzer konnte in der lokalen AD DS-Umgebung nicht gefunden werden.

   Die Fehlermeldungen geben den Benutzern eine Anleitung, damit sie versuchen können, die Fehler ohne Eingriff des Administrators zu beheben.

## <a name="password-writeback-security"></a>Sicherheit für das Kennwortrückschreiben

Das Kennwortrückschreiben ist ein äußerst sicherer Dienst. Zum Schutz Ihrer Informationen wird wie folgt ein vierstufiges Sicherheitsmodell angewandt:

* **Mandantenspezifisches Service Bus Relay**
   * Beim Einrichten des Diensts wird ein mandantenspezifisches Service Bus Relay eingerichtet, das durch ein zufällig generiertes, sicheres Kennwort geschützt wird, auf das Microsoft keinen Zugriff hat.
* **Nicht zugänglicher, sicherer Kryptografieschlüssel für die Kennwortverschlüsselung**
   * Nach der Erstellung des Service Bus Relays wird ein sicherer symmetrischer Schlüssel erstellt, mit dem das Kennwort verschlüsselt wird, bevor es gesendet wird. Dieser Schlüssel liegt ausschließlich im Speicher für geheime Schlüssel Ihres Unternehmens in der Cloud vor, der streng geschützt und überwacht wird, wie jedes andere Kennwort im Verzeichnis.
* **Transport Layer Security (TLS) nach Industriestandard**
   1. Beim Zurücksetzen oder Ändern eines Kennworts in der Cloud wird das Klartextkennwort mit Ihrem öffentlichen Schlüssel verschlüsselt.
   1. Das verschlüsselte Kennwort wird in eine HTTPS-Nachricht eingefügt, die über einen über TLS-/SSL-Zertifikate von Microsoft verschlüsselten Kanal an Ihr Service Bus Relay gesendet wird.
   1. Nachdem die Nachricht beim Service Bus eingetroffen ist, wird Ihr lokaler Agent reaktiviert und in Service Bus mithilfe des sicheren Kennworts authentifiziert, das zuvor generiert wurde.
   1. Der lokale Agent liest die verschlüsselte Nachricht und entschlüsselt sie mit dem privaten Schlüssel.
   1. Der lokale Agent versucht, das Kennwort über die SetPassword-API von AD DS festzulegen. In diesem Schritt kann Ihre lokale AD DS-Kennwortrichtlinie (z. B. die Komplexität, das Alter, der Verlauf und die Filter) in der Cloud erzwungen werden.
* **Richtlinien zum Nachrichtenablauf**
   * Falls die Nachricht im Service Bus verbleibt, weil der lokale Dienst nicht verfügbar ist, kommt es nach wenigen Minuten zu einem Timeout, und die Nachricht wird entfernt. Das Timeout und Entfernen der Nachricht erhöhen die Sicherheit noch weiter.

### <a name="password-writeback-encryption-details"></a>Verschlüsselungsdetails für das Kennwortrückschreiben

Nachdem ein Benutzer eine Kennwortzurücksetzung übermittelt hat, durchläuft die Zurücksetzungsanforderung verschiedene Verschlüsselungsschritte, bevor sie in Ihrer lokalen Umgebung eintrifft. Diese Verschlüsselungsschritte stellen maximale Dienstzuverlässigkeit und -sicherheit sicher. Die Schritte lassen sich wie folgt beschreiben:

1. **Kennwortverschlüsselung mit 2.048-Bit-RSA-Schlüssel:** Nachdem ein Benutzer ein zurückzuschreibendes Kennwort an die lokale Umgebung übermittelt hat, wird dieses Kennwort mit einem 2.048-Bit-RSA-Schlüssel verschlüsselt.
1. **Verschlüsselung auf Paketebene mit AES-GCM:** Das gesamte Paket (bestehend aus Kennwort und den erforderlichen Metadaten) wird mithilfe von AES-GCM verschlüsselt. Diese Verschlüsselung verhindert, dass jemand mit direktem Zugriff auf den zugrunde liegenden Service Bus-Kanal den Inhalt anzeigen oder manipulieren kann.
1. **Abwicklung der gesamten Kommunikation über TLS/SSL:** Die gesamte Kommunikation mit Service Bus wird über einen TLS/SSL-Kanal abgewickelt. Diese Verschlüsselung schützt den Inhalt vor nicht autorisierten Dritten.
1. **Automatischer Schlüsselrollover im Abstand von sechs Monaten:** Alle sechs Monate oder jedes Mal, wenn das Kennwortrückschreiben deaktiviert und dann in Azure AD Connect wieder aktiviert wird, erfolgt ein Rollover aller Schlüssel, um ein Höchstmaß an Dienstzuverlässigkeit und Sicherheit zu erreichen.

### <a name="password-writeback-bandwidth-usage"></a>Bandbreitennutzung für das Kennwortrückschreiben

Kennwortrückschreiben ist ein Dienst, für den wenig Bandbreite erforderlich ist und der nur unter folgenden Umständen Anforderungen zurück an den lokalen Agent sendet:

* Zwei Nachrichten werden gesendet, wenn das Feature über Azure AD Connect aktiviert oder deaktiviert wird.
* Während der gesamten Ausführungsdauer des Diensts wird als Diensttakt alle fünf Minuten eine Nachricht gesendet.
* Zwei Nachrichten werden jedes Mal gesendet, wenn ein neues Kennwort übermittelt wird:
   * Die erste Nachricht ist eine Anforderung zum Ausführen des Vorgangs.
   * Die zweite Nachricht enthält das Ergebnis des Vorgangs und wird in den folgenden Situationen gesendet:
      * Bei jeder Übermittlung eines neuen Kennworts während einer Self-Service-Kennwortzurücksetzung durch Benutzer
      * Bei jeder Übermittlung eines neuen Kennworts während einer Benutzerkennwortänderung
      * Bei jeder Übermittlung eines neuen Kennworts während einer vom Administrator initiierten Benutzerkennwortzurücksetzung (nur über die Azure-Administratorportale)

#### <a name="message-size-and-bandwidth-considerations"></a>Überlegungen zur Nachrichtengröße und Bandbreite

Die Größe von jeder der zuvor beschriebenen Nachrichten liegt in der Regel unter 1 KB. Selbst bei extrem hoher Auslastung nutzt der Dienst zum Kennwortrückschreiben höchstens eine Bandbreite von einigen wenigen Kilobits pro Sekunde. Da jede Nachricht in Echtzeit und nur dann gesendet wird, wenn dies für eine Kennwortaktualisierung erforderlich ist, und die Nachrichten ziemlich klein sind, ist die Bandbreitennutzung der Rückschreibfunktion zu klein, um messbare Auswirkungen zu haben.

## <a name="supported-writeback-operations"></a>Unterstützte Rückschreibevorgänge

Kennwörter werden in den folgenden Situationen zurückgeschrieben:

* **Unterstützte Vorgänge für Endbenutzer**
   * Jegliche freiwillige Self-Service-Kennwortänderung durch einen Endbenutzer
   * Jegliche erzwungene Self-Service-Kennwortänderung durch einen Endbenutzer, beispielsweise bei Ablauf des Kennworts
   * Jegliche Self-Service-Kennwortzurücksetzung durch einen Endbenutzer über das [Kennwortzurücksetzungsportal](https://passwordreset.microsoftonline.com)

* **Unterstützte Vorgänge für Administratoren**
   * Jegliche freiwillige Self-Service-Kennwortänderung durch einen Administrator
   * Jegliche erzwungene Self-Service-Kennwortänderung durch einen Administrator, beispielsweise bei Ablauf des Kennworts
   * Jegliche Self-Service-Kennwortzurücksetzung durch einen Administrator über das [Kennwortzurücksetzungsportal](https://passwordreset.microsoftonline.com)
   * Jegliche durch einen Administrator initiierte Kennwortzurücksetzung durch einen Endbenutzer über das [Azure-Portal](https://portal.azure.com)
   * Jegliche durch einen Administrator initiierte Endbenutzerkennwortzurücksetzung über die [Betaversion der Microsoft Graph-API](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http&view=graph-rest-beta).

## <a name="unsupported-writeback-operations"></a>Nicht unterstützte Rückschreibevorgänge

Kennwörter werden in folgenden Situationen nicht zurückgeschrieben:

* **Nicht unterstützte Vorgänge für Endbenutzer**
   * Jegliches Zurücksetzen des eigenen Kennworts durch einen Endbenutzer über PowerShell Version 1, Version 2 oder die Microsoft Graph-API
* **Nicht unterstützte Vorgänge für Administratoren**
   * Jegliche durch einen Administrator initiierte Endbenutzerkennwortzurücksetzung mithilfe von PowerShell Version 1, Version 2 oder der Microsoft-Graph-API (die [Betaversion der Microsoft Graph-API](/graph/api/passwordauthenticationmethod-resetpassword?tabs=http&view=graph-rest-beta) wird unterstützt).
   * Jegliche durch einen Administrator initiierte Kennwortzurücksetzung durch den Endbenutzer über das [Microsoft 365 Admin Center](https://admin.microsoft.com)
   * Administratoren können das Tool zum Zurücksetzen von Kennwörtern nicht zum Zurücksetzen des eigenen Kennworts für das Kennwortrückschreiben verwenden.

> [!WARNING]
> Die Verwendung des Kontrollkästchens „Benutzer muss Kennwort bei der nächsten Anmeldung ändern“ in lokalen AD DS-Verwaltungstools wie Active Directory-Benutzer und -Computer oder dem Active Directory-Verwaltungscenter wird als Previewfunktion von Azure AD Connect unterstützt. Weitere Informationen finden Sie unter [Implementieren der Kennworthashsynchronisierung mit der Azure AD Connect-Synchronisierung](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Nächste Schritte

Nutzen Sie das folgende Tutorial, um sich mit dem SSPR-Rückschreiben vertraut zu machen:

> [!div class="nextstepaction"]
> [Tutorial: Aktivieren des Rückschreibens von Self-Service-Kennwortzurücksetzungen (SSPR)](./tutorial-enable-sspr-writeback.md)