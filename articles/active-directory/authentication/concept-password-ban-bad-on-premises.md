---
title: Azure AD-Kennwortschutz – Azure Active Directory
description: Sperren unsicherer Kennwörter in lokalen Active Directory Domain Services-Umgebungen mit dem Kennwortschutz für Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f5df1cb158821fb0cd85d90f9ba3b79d80adf45
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743920"
---
# <a name="enforce-on-premises-azure-ad-password-protection-for-active-directory-domain-services"></a>Erzwingen des lokalen Azure AD-Kennwortschutzes für Active Directory Domain Services

Der Azure AD-Kennwortschutz erkennt und blockiert bekannte schwache Kennwörter und deren Varianten und kann außerdem schwache Ausdrücke blockieren, die spezifisch für Ihre Organisation sind. Die lokale Bereitstellung des Azure AD-Kennwortschutzes verwendet dieselben Listen globaler sowie benutzerdefinierter gesperrter Kennwörter, die in Azure AD gespeichert sind. Es werden die gleichen Überprüfungen für lokale Kennwortänderungen durchgeführt, die auch Azure AD für cloudbasierte Änderungen durchführt. Diese Überprüfungen erfolgen bei Kennwortänderungen und Kennwortzurücksetzungen auf lokalen AD DS-Domänencontrollern (Active Directory Domain Services).

## <a name="design-principles"></a>Entwurfsprinzipien

Der Kennwortschutz für Azure AD wurde unter Berücksichtigung der folgenden Prinzipien entwickelt:

* Domänencontroller (DCs) müssen niemals direkt mit dem Internet kommunizieren.
* Auf Domänencontrollern werden keine neuen Netzwerkports geöffnet.
* AD DS-Schemaänderungen sind nicht erforderlich. Die Software verwendet die vorhandenen AD DS-Schemaobjekte vom Typ *container* und *serviceConnectionPoint*.
* Für die Funktionsebene von AD DS-Domäne oder Gesamtstruktur (DFL\FFL) gelten keine Mindestanforderungen.
* Die Software erstellt oder erfordert keine Konten in den von ihr geschützten AD DS-Domänen.
* Klartextkennwörter verlassen niemals den Domänencontroller (sei es während der Kennwortvalidierung oder zu einem anderen Zeitpunkt).
* Die Software ist nicht von anderen Azure AD-Features abhängig. Beispielsweise ist die Azure AD-Kennworthashsynchronisierung nicht mit dem Azure AD-Kennwortschutz verknüpft oder dafür erforderlich.
* Die inkrementelle Bereitstellung wird unterstützt. Die Kennwortrichtlinie wird jedoch nur dort erzwungen, wo der Domänencontroller-Agent (DC-Agent) installiert ist.

## <a name="incremental-deployment"></a>Inkrementelle Bereitstellung

Der Azure AD-Kennwortschutz unterstützt die inkrementelle Bereitstellung auf mehreren Domänencontrollern in einer AD DS-Domäne. Es ist wichtig, die Bedeutung dieser Punkte zu verstehen und die damit verbundenen Kompromisse zu kennen.

Mit der DC-Agent-Software für den Azure AD-Kennwortschutz können Kennwörter nur validiert werden, wenn die Software auf einem Domänencontroller installiert ist. Außerdem können nur Kennwortänderungen validiert werden, die an diesen DC gesendet werden. Es kann nicht gesteuert werden, welche Domänencontroller von Windows-Clientcomputern für die Verarbeitung von Kennwortänderungen ausgewählt werden. Um ein konsistentes Verhalten und eine universelle Erzwingung des Azure AD-Kennwortschutzes zu gewährleisten, muss die DC-Agent-Software auf allen Domänencontrollern einer Domäne installiert sein.

Viele Organisationen möchten den Azure AD-Kennwortschutz vor einer vollständigen Bereitstellung mithilfe einer Teilmenge ihrer DCs umfassend testen. Für dieses Szenario unterstützt der Azure AD-Kennwortschutz eine partielle Bereitstellung. Die DC-Agent-Software auf einem Domänencontroller führt selbst dann eine aktive Kennwortvalidierung durch, wenn die DC-Agent-Software nicht auf anderen Domänencontrollern in der Domäne installiert ist. Partielle Bereitstellungen dieses Typs sind nicht sicher und werden nur für Testzwecke empfohlen.

## <a name="architectural-diagram"></a>Architekturdiagramm

Bevor Sie den Azure AD-Kennwortschutz in einer lokalen AD DS-Umgebung bereitstellen, sollten Sie sich mit den zugrunde liegenden Entwurfs- und Funktionskonzepten vertraut machen. Das folgende Diagramm zeigt, wie die Komponenten des Azure AD-Kennwortschutzes zusammenarbeiten.

![Zusammenspiel der Komponenten des Azure AD-Kennwortschutzes](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Der Azure AD-Kennwortschutz-Proxydienst wird auf einem beliebigen in die Domäne eingebundenen Computer in der aktuellen AD DS-Gesamtstruktur ausgeführt. Der Dienst dient primär dazu, Downloadanforderungen für Kennwortrichtlinien von Domänencontrollern an Azure AD weiterzuleiten und die Antwort von Azure AD an den Domänencontroller zurückzugeben.
* Die Kennwortfilter-DLL des DC-Agents empfängt Anforderungen zur Kennwortüberprüfung vom Betriebssystem. Der Filter leitet diese an den DC-Agent-Dienst weiter, der lokal auf dem Domänencontroller ausgeführt wird.
* Der DC-Agent-Dienst des Azure AD-Kennwortschutzes empfängt Anforderungen zur Kennwortvalidierung von der Kennwortfilter-DLL des DC-Agents. Der DC-Agent-Dienst verarbeitet sie mit der aktuellen (lokal verfügbaren) Kennwortrichtlinie und gibt das Ergebnis zurück: *pass* (erfolgreich) oder *fail* (fehlerhaft).

## <a name="how-azure-ad-password-protection-works"></a>Funktionsweise des Azure AD-Kennwortschutzes

Die Komponenten für den lokalen Azure AD-Kennwortschutz arbeiten folgendermaßen zusammen:

1. Jede Instanz des Azure AD-Kennwortschutz-Proxydiensts kündigt sich selbst bei Domänencontrollern in der Gesamtstruktur an, indem sie ein Objekt vom Typ *serviceConnectionPoint* in Active Directory erstellt.

    Jeder DC-Agent-Dienst für den Azure AD-Kennwortschutz erstellt darüber hinaus ein Objekt vom Typ *serviceConnectionPoint* in Active Directory. Dieses Objekt wird jedoch in erster Linie für Berichte und Diagnosen verwendet.

1. Der DC-Agent-Dienst ist dafür verantwortlich, das Herunterladen einer neuen Kennwortrichtlinie von Azure AD einzuleiten. Zuerst sucht er einen Proxydienst für den Azure AD-Kennwortschutz. Dazu fragt er Proxyobjekte vom Typ *serviceConnectionPoint* in der Gesamtstruktur ab.

1. Sobald er einen verfügbaren Proxydienst gefunden hat, sendet der DC-Agent eine Downloadanforderung für Kennwortrichtlinien an diesen Proxydienst. Der Proxydienst wiederum sendet die Anforderung an Azure AD und gibt die Antwort anschließend an den DC-Agent-Dienst zurück.

1. Nachdem der DC-Agent-Dienst eine neue Kennwortrichtlinie von Azure AD erhalten hat, speichert er die Richtlinie in einem speziellen Ordner im Stammverzeichnis der *sysvol*-Ordnerfreigabe seiner Domäne. Der DC-Agent-Dienst überwacht auch diesen Ordner, falls neuere Richtlinien von anderen DC-Agent-Diensten in der Domäne repliziert werden.

1. Der DC-Agent-Dienst fordert beim Starten des Diensts immer eine neue Richtlinie an. Nachdem der DC-Agent-Dienst gestartet wurde, überprüft er stündlich das Alter der aktuellen lokal verfügbaren Richtlinie. Wenn die Richtlinie älter als eine Stunde ist, fordert der DC-Agent wie oben beschrieben über den Proxydienst eine neue Richtlinie von Azure AD an. Wenn die aktuelle Richtlinie nicht älter als eine Stunde ist, verwendet der DC-Agent weiterhin diese Richtlinie.

1. Wenn ein Domänencontroller Kennwortänderungsereignisse empfängt, wird anhand der zwischengespeicherten Richtlinie ermittelt, ob das neue Kennwort akzeptiert oder abgelehnt wird.

### <a name="key-considerations-and-features"></a>Wichtige Überlegungen und Features

* Wenn eine Kennwortrichtlinie für den Azure AD-Kennwortschutz heruntergeladen wird, gilt diese Richtlinie spezifisch für einen Mandanten. Anders gesagt: Kennwortrichtlinien sind stets eine Kombination der globalen Liste gesperrter Kennwörter von Microsoft und der benutzerdefinierten Liste gesperrter Kennwörter des Mandanten.
* Der DC-Agent kommuniziert mit dem Proxydienst via RPC über TCP. Der Proxydienst lauscht an einem dynamischen oder statischen RPC-Port auf diese Aufrufe (je nach Konfiguration).
* Der DC-Agent lauscht nie an einem im Netzwerk verfügbaren Port.
* Der Proxydienst ruft nie den DC-Agent-Dienst auf.
* Der Proxydienst ist zustandslos. Richtlinien oder andere aus Azure heruntergeladene Zustände werden nicht zwischengespeichert.
* Der DC-Agent-Dienst verwendet immer die neueste lokal verfügbare Kennwortrichtlinie, um Benutzerkennwörter auszuwerten. Wenn keine Kennwortrichtlinie auf dem lokalen Domänencontroller verfügbar ist, wird das Kennwort automatisch akzeptiert. In diesem Fall wird eine Ereignismeldung protokolliert, um den Administrator zu warnen.
* Der Azure AD-Kennwortschutz ist kein Echtzeitmodul für die Richtlinienanwendung. Zwischen einer Änderung der Kennwortrichtlinienkonfiguration in Azure AD und dem Zeitpunkt, zu dem sie alle Domänencontroller erreicht und dort erzwungen wird, kann eine Verzögerung auftreten.
* Der Azure AD-Kennwortschutz dient als Ergänzung zu den vorhandenen AD DS-Kennwortrichtlinien, ersetzt diese aber nicht. Dies gilt auch für andere möglicherweise installierte Kennwortfilter-DLLs von Drittanbietern. In AD DS ist eine Zustimmung durch alle Komponenten für die Kennwortvalidierung erforderlich, bevor ein Kennwort akzeptiert wird.

## <a name="forest--tenant-binding-for-azure-ad-password-protection"></a>Gesamtstruktur-/Mandantenbindung für den Azure AD-Kennwortschutz

Wenn Sie den Azure AD-Kennwortschutz in einer AD DS-Gesamtstruktur bereitstellen möchten, müssen Sie die betreffende Gesamtstruktur bei Azure AD registrieren. Der bereitgestellte Proxydienst muss ebenfalls bei Azure AD registriert werden. Diese Registrierungen der Gesamtstrukturen und Proxydienste sind einem bestimmten Azure AD-Mandanten zugeordnet, der implizit über die bei der Registrierung verwendeten Anmeldeinformationen identifiziert wird.

Die AD DS-Gesamtstruktur und alle bereitgestellten Proxydienste innerhalb einer Gesamtstruktur müssen beim selben Mandanten registriert werden. AD DS-Gesamtstrukturen oder andere Proxydienste in dieser Gesamtstruktur können nicht bei unterschiedlichen Azure AD-Mandanten registriert werden. Wenn die Bereitstellung falsch konfiguriert wurde, können Sie beispielsweise keine Kennwortrichtlinien herunterladen.

> [!NOTE]
> Kunden mit mehreren Azure AD-Mandanten müssen daher einen „Distinguished Tenant“ auswählen, um jede Gesamtstruktur für den Kennwortschutz von Azure AD zu registrieren.

## <a name="download"></a>Download

Die zwei erforderlichen Agent-Installer für den Azure AD-Kennwortschutz können Sie im [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071) herunterladen.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie zum Einstieg in die Verwendung des lokalen Azure AD-Kennwortschutzes die folgende Schrittanleitung:

> [!div class="nextstepaction"]
> [Bereitstellen des lokalen Azure AD-Kennwortschutzes](howto-password-ban-bad-on-premises-deploy.md)
