---
title: Lokale Integration von Kennwortrückschreiben mit Azure AD-SSPR – Azure Active Directory
description: Einrichten, dass Kennwörter zur lokalen AD-Infrastruktur zurückgeschrieben werden
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dbf27301e738978e7f03d2423a4d23fd63c97b5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113494"
---
# <a name="what-is-password-writeback"></a>Was ist Kennwortrückschreiben?

Ein cloudbasiertes Hilfsprogramm zum Zurücksetzen von Kennwörtern ist praktisch, aber die meisten Unternehmen haben immer noch ein lokales Verzeichnis, in dem ihre Benutzer existieren. Wie unterstützt Microsoft die Synchronisierung eines traditionellen lokalen Active Directory (AD) mit Kennwortänderungen in der Cloud? Kennwortrückschreiben ist eine Funktion, die mit [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) aktiviert wurde und es ermöglicht, Kennwortänderungen in der Cloud in Echtzeit in ein vorhandenes lokales Verzeichnis zurückzuschreiben.

Kennwortrückschreiben wird in Umgebungen unterstützt, die folgende Elemente verwenden:

* [Active Directory-Verbunddienste (AD FS)](../hybrid/how-to-connect-fed-management.md)
* [Kennworthashsynchronisierung](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Passthrough-Authentifizierung](../hybrid/how-to-connect-pta.md)

> [!WARNING]
> Das Kennwortrückschreiben funktioniert nicht mehr für Kunden, die Azure AD Connect-Versionen bis 1.0.8641.0 verwenden, wenn der [Azure Access Control Service (ACS) am 7. November 2018 eingestellt wird](../develop/active-directory-acs-migration.md). Azure AD Connect-Versionen bis 1.0.8641.0 lassen ab diesem Zeitpunkt kein Kennwortrückschreiben mehr zu, da sie für diese Funktionalität von ACS abhängen.
>
> Um eine Dienstunterbrechung zu vermeiden, führen Sie bei früheren Versionen von Azure AD Connect ein Upgrade auf eine neuere Version durch. Weitere Informationen finden Sie unter [Azure AD Connect: Aktualisieren von einer früheren Version auf die aktuelle Version](../hybrid/how-to-upgrade-previous-version.md).
>

Kennwortrückschreiben bietet:

* **Erzwingung von lokalen Active Directory-Kennwortrichtlinien**: Wenn ein Benutzer sein Kennwort zurücksetzt, wird es überprüft, um sicherzustellen, dass es die lokalen Active Directory-Richtlinien erfüllt, bevor es für dieses Verzeichnis übernommen wird. Diese Überprüfung umfasst Prüfen des Verlaufs, der Komplexität, des Alters, der Kennwortfilter und aller weiteren Kennwortbeschränkungen, die Sie im lokalen Active Directory definiert haben.
* **Feedback ohne Verzögerung**:  Die Kennwortrückschreibung ist ein synchroner Vorgang. Ihre Benutzer werden sofort benachrichtigt, wenn ihre Kennwörter nicht der Richtlinie entsprechen oder das Zurücksetzen oder Ändern des Kennworts aus irgendeinem Grund nicht möglich war.
* **Unterstützung für das Ändern von Kennwörtern über den Zugriffsbereich und Office 365**: Wenn Verbundbenutzer oder Benutzer mit Kennworthashsynchronisierung ihre abgelaufenen oder noch nicht abgelaufenen Kennwörter ändern möchten, werden diese Kennwörter in die lokale Active Directory-Umgebung zurückgeschrieben.
* **Unterstützung für Kennwortrückschreiben, wenn die Kennwörter im Azure-Portal von einem Administrator zurückgesetzt werden**: Wenn ein Administrator das Kennwort eines Benutzers im [Azure-Portal](https://portal.azure.com) zurücksetzt und der Benutzer ein Verbundkonto oder ein Konto mit Kennworthashsynchronisierung verwendet, wird das Kennwort lokal zurückgeschrieben. Diese Funktionalität wird im Office-Verwaltungsportal derzeit nicht unterstützt.
* **Keine Notwendigkeit zur Festlegung von eingehenden Firewallregeln**: Kennwortrückschreiben verwendet ein Azure Service Bus Relay als zugrunde liegenden Kommunikationskanal. Die gesamte Kommunikation geht über Port 443.

> [!NOTE]
> Benutzerkonten in geschützten Gruppen im lokalen Active Directory können nicht zum Kennwortrückschreiben verwendet werden. Administratorkonten in geschützten Gruppen im lokalen Active Directory können zum Kennwortrückschreiben verwendet werden. Weitere Informationen zu geschützten Gruppen finden Sie unter [Geschützte Konten und Gruppen in Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

## <a name="licensing-requirements-for-password-writeback"></a>Lizenzierungsanforderungen für das Kennwortrückschreiben

**Self-Service-Kennwortzurücksetzung/-änderung/-entsperrung mit lokalem Rückschreiben ist eine Premium-Funktion von Azure AD**. Weitere Informationen zur Lizenzierung finden Sie auf der [Preiswebsite für Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Damit Kennwortrückschreiben verwendet werden kann, muss in Ihrem Mandanten eine der folgenden Lizenzen zugewiesen sein:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3 oder A3
* Enterprise Mobility + Security E5 oder A5
* Microsoft 365 E3 oder A3
* Microsoft 365 E5 oder A5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> Eigenständige Office 365-Lizenzierungspläne *bieten keine Unterstützung für Self-Service-Kennwortzurücksetzung/-änderung/-entsperrung mit lokalem Rückschreiben* und erfordern, dass Sie einen der obigen Pläne haben, damit diese Funktion verwendbar ist.

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
1. Der Dienst sucht dann nach dem Benutzer, indem er das Cloudankerattribut verwendet. Damit diese Suche erfolgreich ist:

   * Das Benutzerobjekt muss im Active Directory-Connectorbereich vorhanden sein.
   * Das Benutzerobjekt muss mit dem entsprechenden Metaverseobjekt (MV-Objekt) verknüpft sein.
   * Das Benutzerobjekt muss mit dem entsprechenden Azure Active Directory-Connectorobjekt verknüpft sein.
   * Für die Verknüpfung zwischen dem Active Directory-Connectorobjekt und dem MV-Objekt muss die Synchronisierungsregel `Microsoft.InfromADUserAccountEnabled.xxx` festgelegt sein.
   
   Sobald der Aufruf aus der Cloud eingetroffen ist, verwendet das Synchronisierungsmodul das **cloudAnchor**-Attribut, um das Azure Active Directory-Connectorbereichsobjekt nachzuschlagen. Es folgt dann dem Link zurück zum MV-Objekt und folgt anschließend dem Link zurück zum Active Directory-Objekt. Da mehrere Active Directory-Objekte (Multi-Gesamtstruktur) für denselben Benutzer vorliegen können, verwendet das Synchronisierungsmodul die `Microsoft.InfromADUserAccountEnabled.xxx`-Verbindung, um das richtige Objekt auszuwählen.

1. Nachdem das Benutzerkonto ermittelt ist, wird versucht, das Kennwort direkt in der geeigneten Active Directory-Gesamtstruktur zurückzusetzen.
1. Wenn dieser Vorgang erfolgreich war, wird der Benutzer darüber informiert, dass das Kennwort geändert wurde.
   > [!NOTE]
   > Wenn das Kennworthash des Benutzers mithilfe der Kennworthashsynchronisierung mit Azure AD synchronisiert wird, kann es vorkommen, dass die lokale Kennwortrichtlinie schwächer ist als die Kennwortrichtlinie der Cloud. In diesem Fall wird die lokale Kennwortrichtlinie erzwungen. Diese Richtlinie stellt das Erzwingen Ihrer lokalen Richtlinie in der Cloud sicher, unabhängig davon, ob Sie für die Bereitstellung von einmaligem Anmelden Kennworthashsynchronisierung oder Verbund verwenden.

1. Wenn bei diesem Vorgang ein Fehler auftritt, wird der Benutzer per Fehlermeldung dazu aufgefordert, es erneut zu versuchen. Der Vorgang kann aus folgenden Gründen fehlschlagen:
    * Der Dienst war nicht verfügbar.
    * Das ausgewählte Kennwort entsprach nicht den Richtlinien der Organisation.
    * Der Benutzer wurde nicht im lokalen Active Directory gefunden.

      Die Fehlermeldungen geben den Benutzern eine Anleitung, damit sie versuchen können, die Fehler ohne Eingriff des Administrators zu beheben.

## <a name="password-writeback-security"></a>Sicherheit für das Kennwortrückschreiben

Das Kennwortrückschreiben ist ein äußerst sicherer Dienst. Zum Schutz Ihrer Informationen wird ein vierstufiges Sicherheitsmodell angewendet, das sich wie folgt beschreiben lässt:

* **Mandantenspezifisches Service Bus Relay**
   * Beim Einrichten des Diensts wird ein mandantenspezifisches Service Bus Relay eingerichtet, das durch ein zufällig generiertes, sicheres Kennwort geschützt wird, auf das Microsoft keinen Zugriff hat.
* **Nicht zugänglicher, sicherer Kryptografieschlüssel für die Kennwortverschlüsselung**
   * Nach der Erstellung des Service Bus Relays wird ein sicherer symmetrischer Schlüssel erstellt, mit dem das Kennwort verschlüsselt wird, bevor es gesendet wird. Dieser Schlüssel liegt ausschließlich im Speicher für geheime Schlüssel Ihres Unternehmens in der Cloud vor, der streng geschützt und überwacht wird, wie jedes andere Kennwort im Verzeichnis.
* **Transport Layer Security (TLS) nach Industriestandard**
   1. Beim Zurücksetzen oder Ändern eines Kennworts in der Cloud wird das Klartextkennwort mit Ihrem öffentlichen Schlüssel verschlüsselt.
   1. Das verschlüsselte Kennwort wird in eine HTTPS-Nachricht eingefügt, die über einen über SSL-Zertifikate von Microsoft verschlüsselten Kanal an Ihr Service Bus Relay gesendet wird.
   1. Nachdem die Nachricht beim Service Bus eingetroffen ist, wird Ihr lokaler Agent reaktiviert und in Service Bus mithilfe des sicheren Kennworts authentifiziert, das zuvor generiert wurde.
   1. Der lokale Agent liest die verschlüsselte Nachricht und entschlüsselt sie mit dem privaten Schlüssel.
   1. Der lokale Agent versucht, das Kennwort über die SetPassword-API von AD DS festzulegen. In diesem Schritt kann Ihre lokale Active Directory-Kennwortrichtlinie (so z.B. die Komplexität, das Alter, der Verlauf, die Filter usw.) in der Cloud erzwungen werden.
* **Richtlinien zum Nachrichtenablauf**
   * Falls die Nachricht im Service Bus verbleibt, weil der lokale Dienst nicht verfügbar ist, kommt es nach wenigen Minuten zu einem Timeout, und die Nachricht wird entfernt. Das Timeout und Entfernen der Nachricht erhöhen die Sicherheit noch weiter.

### <a name="password-writeback-encryption-details"></a>Verschlüsselungsdetails für das Kennwortrückschreiben

Nachdem ein Benutzer eine Kennwortzurücksetzung übermittelt hat, durchläuft die Zurücksetzungsanforderung verschiedene Verschlüsselungsschritte, bevor sie in Ihrer lokalen Umgebung eintrifft. Diese Verschlüsselungsschritte stellen maximale Dienstzuverlässigkeit und -sicherheit sicher. Die Schritte lassen sich wie folgt beschreiben:

* **Schritt 1: Kennwortverschlüsselung mit 2.048-Bit-RSA-Schlüssel**: Nachdem ein Benutzer ein zurückzuschreibendes Kennwort an die lokale Umgebung übermittelt hat, wird dieses Kennwort mit einem 2.048-Bit-RSA-Schlüssel verschlüsselt.
* **Schritt 2: Verschlüsselung auf Paketebene mit AES-GCM**: Das gesamte Paket (bestehend aus Kennwort und den erforderlichen Metadaten) wird mithilfe von AES-GCM verschlüsselt. Diese Verschlüsselung verhindert, dass jemand mit direktem Zugriff auf den zugrunde liegenden ServiceBus-Kanal den Inhalt anzeigen oder manipulieren kann.
* **Schritt 3: Abwicklung der gesamten Kommunikation über TLS/SSL**: Die gesamte Kommunikation mit ServiceBus wird über einen TLS/SSL-Kanal abgewickelt. Diese Verschlüsselung schützt den Inhalt vor nicht autorisierten Dritten.
* **Automatischer Schlüsselrollover im Abstand von sechs Monaten**: Alle sechs Monate oder jedes Mal, wenn das Kennwortrückschreiben deaktiviert und dann in Azure AD Connect wieder aktiviert wird, erfolgt ein Rollover aller Schlüssel, um ein Höchstmaß an Dienstzuverlässigkeit und Sicherheit zu erreichen.

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
   * Jegliche erzwungene Self-Service-Kennwortänderung durch einen Endbenutzer, beispielsweise der Ablauf des Kennworts
   * Jegliche Self-Service-Kennwortzurücksetzung durch einen Endbenutzer über das [Kennwortzurücksetzungsportal](https://passwordreset.microsoftonline.com)
* **Unterstützte Vorgänge für Administratoren**
   * Jegliche freiwillige Self-Service-Kennwortänderung durch einen Administrator
   * Jegliche erzwungene Self-Service-Kennwortänderung durch einen Administrator, beispielsweise der Ablauf des Kennworts
   * Jegliche Self-Service-Kennwortzurücksetzung durch einen Administrator über das [Kennwortzurücksetzungsportal](https://passwordreset.microsoftonline.com)
   * Jegliche durch einen Administrator initiierte Endbenutzer-Kennwortzurücksetzung über das [Azure-Portal](https://portal.azure.com)

## <a name="unsupported-writeback-operations"></a>Nicht unterstützte Rückschreibevorgänge

Kennwörter werden in folgenden Situationen *nicht* zurückgeschrieben:

* **Nicht unterstützte Vorgänge für Endbenutzer**
   * Jegliches Zurücksetzen des eigenen Kennworts durch einen Endbenutzer über PowerShell Version 1, Version 2 oder die Azure AD-Graph-API
* **Nicht unterstützte Vorgänge für Administratoren**
   * Jegliche durch einen Administrator initiierte Endbenutzer-Kennwortzurücksetzung mithilfe von PowerShell Version 1, Version 2 oder der Azure AD-Graph-API
   * Jegliche durch einen Administrator initiierte Endbenutzer-Kennwortzurücksetzung über das [Microsoft 365 Admin Center](https://admin.microsoft.com)

> [!WARNING]
> Die Verwendung des Kontrollkästchens „Benutzer muss Kennwort bei der nächsten Anmeldung ändern“ in lokalen Active Directory-Verwaltungstools wie „Active Directory-Benutzer und -Computer“ oder dem Active Directory-Verwaltungscenter wird nicht unterstützt. Beim Ändern eines Kennworts in der lokalen Umgebung darf diese Option nicht aktiviert werden.

## <a name="next-steps"></a>Nächste Schritte

Aktivieren Sie das Kennwortrückschreiben anhand dieses Tutorials: [Aktivieren des Kennwortrückschreibens](tutorial-enable-writeback.md)
