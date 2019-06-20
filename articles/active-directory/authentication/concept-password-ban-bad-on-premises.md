---
title: Azure AD-Kennwortschutz – Azure Active Directory
description: Sperren unsicherer Kennwörter im lokalen Active Directory mit dem Kennwortschutz für Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/18/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 644054960e12979c231bbf50a5979bc12d343f89
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64694765"
---
# <a name="enforce-azure-ad-password-protection-for-windows-server-active-directory"></a>Erzwingen des Azure AD-Kennwortschutzes für Windows Server Active Directory

Der Kennwortschutz für Azure AD ist ein Feature, das Kennwortrichtlinien in einer Organisation verbessert. Der Kennwortschutz für lokale Bereitstellungen verwendet sowohl die Liste der globalen als auch benutzerdefinierten gesperrten Kennwörter, die in Azure AD gespeichert sind. Er führt lokal die gleichen Überprüfungen durch wie Azure AD wie bei cloudbasierten Änderungen.

## <a name="design-principles"></a>Entwurfsprinzipien

Der Kennwortschutz für Azure AD wurde unter Berücksichtigung der folgenden Prinzipien entwickelt:

* Domänencontroller müssen nie direkt mit dem Internet kommunizieren.
* Auf Domänencontrollern sind keine neuen Netzwerkports geöffnet.
* Es sind keine Änderungen des Active Directory-Schemas erforderlich. Die Software verwendet die vorhandenen Active Directory-Schemaobjekte vom Typ **container** und **serviceConnectionPoint**.
* Für die Funktionsebene von Active Directory-Domäne oder Gesamtstruktur (DFL\FFL) gelten keine Mindestanforderungen.
* Die Software erstellt oder erfordert keine Konten in den von ihr geschützten Active Directory-Domänen.
* Klartextkennwörter verlassen niemals den Domänencontroller (sei es während der Kennwortvalidierung oder zu einem anderen Zeitpunkt).
* Die Software hängt nicht von anderen Azure AD-Funktionen ab. Die Azure AD-Kennworthashsynchronisierung ist beispielsweise nicht damit verbunden und nicht erforderlich, damit der Azure AD-Kennwortschutz funktioniert.
* Die inkrementelle Bereitstellung wird unterstützt. Die Kennwortrichtlinie wird jedoch nur dort erzwungen, wo der Domänencontroller-Agent (DC-Agent) installiert ist. Weitere Informationen finden Sie im nächsten Thema.

## <a name="incremental-deployment"></a>Inkrementelle Bereitstellung

Der Azure AD-Kennwortschutz unterstützt die inkrementelle Bereitstellung auf Domänencontrollern in einer Active Directory-Domäne. Jedoch ist es wichtig zu verstehen, was das wirklich bedeutet und worin die Vor- und Nachteile liegen.

Mit der DC-Agent-Software für den Azure AD-Kennwortschutz können Kennwörter nur validiert werden, wenn sie auf einem Domänencontroller installiert ist, und dies auch nur für Kennwortänderungen, die an diesen Domänencontroller gesendet werden. Es kann nicht gesteuert werden, welche Domänencontroller auf Windows-Clientcomputern für die Verarbeitung von Kennwortänderungen ausgewählt werden. Um ein konsistentes Verhalten und eine universelle Erzwingung der Passwortsicherheit zu gewährleisten, MUSS die DC-Agent-Software auf allen Domänencontrollern einer Domäne installiert sein.

Für viele Organisationen empfiehlt es sich vor der vollständigen Bereitstellung, den Azure AD-Kennwortschutz für eine Teilmenge ihrer Domänencontroller sorgfältig zu testen. Der Azure AD-Kennwortschutz unterstützt eine partielle Bereitstellung, d. h., mit der auf einem bestimmten Domänencontroller installierten DC-Agent-Software werden Kennwörter aktiv validiert, auch wenn die DC-Agent-Software auf anderen Domänencontrollern in der Domäne nicht installiert ist. Partielle Bereitstellungen dieses Typs sind NICHT sicher und werden NUR für Testzwecke empfohlen.

## <a name="architectural-diagram"></a>Architekturdiagramm

Bevor Sie den Kennwortschutz von Azure AD in einer lokalen Active Directory-Umgebung bereitstellen, sollten Sie sich mit den zugrunde liegenden Entwurfs- und Funktionskonzepten vertraut machen. Im folgenden Diagramm sehen Sie, wie die Komponenten des Kennwortschutzes zusammenarbeiten:

![Zusammenspiel der Komponenten des Azure AD-Kennwortschutzes](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

* Der Azure AD-Kennwortschutz-Proxydienst wird auf einem beliebigen in die Domäne eingebundenen Computer in der aktuellen Active Directory-Gesamtstruktur ausgeführt. Er dient primär dazu, Downloadanforderungen für Kennwortrichtlinien von Domänencontrollern an Azure AD weiterzuleiten. Anschließend gibt er die Antwort von Azure AD an den Domänencontroller zurück.
* Die Kennwortfilter-DLL des DC-Agents empfängt Anforderungen zur Kennwortüberprüfung vom Betriebssystem. Sie leitet diese an den DC-Agent-Dienst weiter, der lokal auf dem Domänencontroller ausgeführt wird.
* Der DC-Agent-Dienst des Kennwortschutzes empfängt Anforderungen zur Kennwortüberprüfung von der Kennwortfilter-DLL des DC-Agents. Er verarbeitet sie mit der aktuellen (lokal verfügbaren) Kennwortrichtlinie und gibt das Ergebnis zurück: *pass* (erfolgreich) oder *fail* (fehlerhaft).

## <a name="how-password-protection-works"></a>Funktionsweise des Kennwortschutzes

Jede Proxydienstinstanz des Kennwortschutzes für Azure AD kündigt sich selbst bei Domänencontrollern in der Gesamtstruktur an, indem sie ein Objekt vom Typ **serviceConnectionPoint** in Active Directory erstellt.

Jeder DC-Agent-Dienst für den Kennwortschutz erstellt zudem ein Objekt vom Typ **serviceConnectionPoint** in Active Directory. Dieses Objekt wird jedoch in erster Linie für Berichte und Diagnosen verwendet.

Der DC-Agent-Dienst ist dafür verantwortlich, das Herunterladen einer neuen Kennwortrichtlinie von Azure AD einzuleiten. Zuerst sucht er einen Proxydienst für den Azure AD-Kennwortschutz. Dazu fragt er Proxyobjekte vom Typ **serviceConnectionPoint** in der Gesamtstruktur ab. Sobald er einen verfügbaren Proxydienst gefunden hat, sendet der DC-Agent eine Downloadanforderung für Kennwortrichtlinien an diesen Proxydienst. Der Proxydienst sendet die Anforderung wiederum an Azure AD. Der Proxydienst gibt dann die Antwort an den DC-Agent-Dienst zurück.

Nachdem der DC-Agent-Dienst eine neue Kennwortrichtlinie von Azure AD erhalten hat, speichert er die Richtlinie in einem speziellen Ordner im Stammverzeichnis der *sysvol*-Ordnerfreigabe seiner Domäne. Der DC-Agent-Dienst überwacht auch diesen Ordner, falls neuere Richtlinien von anderen DC-Agent-Diensten in der Domäne repliziert werden.

Der DC-Agent-Dienst fordert beim Starten des Diensts immer eine neue Richtlinie an. Nachdem der DC-Agent-Dienst gestartet wurde, überprüft er stündlich das Alter der aktuellen lokal verfügbaren Richtlinie. Wenn die Richtlinie älter als eine Stunde ist, fordert der DC-Agent wie oben beschrieben über den Proxydienst eine neue Richtlinie von Azure AD an. Wenn die aktuelle Richtlinie nicht älter als eine Stunde ist, verwendet der DC-Agent weiterhin diese Richtlinie.

Wenn eine Kennwortrichtlinie für den Azure AD-Kennwortschutz heruntergeladen wird, gilt diese Richtlinie spezifisch für einen Mandanten. Anders gesagt: Kennwortrichtlinien sind stets eine Kombination der globalen Liste gesperrter Kennwörter von Microsoft und der benutzerdefinierten Liste gesperrter Kennwörter des Mandanten.

Der DC-Agent kommuniziert mit dem Proxydienst via RPC über TCP. Der Proxydienst lauscht an einem dynamischen oder statischen RPC-Port auf diese Aufrufe (je nach Konfiguration).

Der DC-Agent lauscht nie an einem im Netzwerk verfügbaren Port.

Der Proxydienst ruft nie den DC-Agent-Dienst auf.

Der Proxydienst ist zustandslos. Richtlinien oder andere aus Azure heruntergeladene Zustände werden nicht zwischengespeichert.

Der DC-Agent-Dienst verwendet immer die neueste lokal verfügbare Kennwortrichtlinie, um Benutzerkennwörter auszuwerten. Wenn keine Kennwortrichtlinie auf dem lokalen Domänencontroller verfügbar ist, wird das Kennwort automatisch akzeptiert. In diesem Fall wird eine Ereignismeldung protokolliert, um den Administrator zu warnen.

Der Azure AD-Kennwortschutz ist kein Echtzeitmodul für die Richtlinienanwendung. Zwischen einer Änderung der Kennwortrichtlinienkonfiguration in Azure AD und dem Zeitpunkt, zu dem sie alle Domänencontroller erreicht und dort durchgesetzt wird, kann eine Verzögerung auftreten.

Der Azure AD-Kennwortschutz dient als Ergänzung zu den vorhandenen Active Directory-Kennwortrichtlinien und soll diese nicht ersetzen. Dies gilt auch für andere möglicherweise installierte Kennwortfilter-DLLs von Drittanbietern. In Active Directory ist es immer erforderlich, dass in allen Komponenten der Kennwortüberprüfung eine Zustimmung erfolgt, bevor ein Kennwort akzeptiert wird.

## <a name="foresttenant-binding-for-password-protection"></a>Gesamtstruktur-/Mandantenbindung für den Kennwortschutz

Wenn Sie den Kennwortschutz für Azure AD in einer Active Directory-Gesamtstruktur bereitstellen möchten, müssen Sie diese Gesamtstruktur bei Azure AD registrieren. Die bereitgestellten Proxydienste müssen ebenfalls bei Azure AD registriert werden. Diese Registrierungen der Gesamtstrukturen und Proxydienste sind einem bestimmten Azure AD-Mandanten zugeordnet, der implizit über die bei der Registrierung verwendeten Anmeldeinformationen identifiziert wird.

Die Active Directory-Gesamtstruktur und alle bereitgestellten Proxydienste innerhalb einer Gesamtstruktur müssen beim gleichen Mandanten registriert werden. Active Directory-Gesamtstrukturen oder andere Proxydienste in dieser Gesamtstruktur können nicht bei unterschiedlichen Azure AD-Mandanten registriert werden. Wenn die Bereitstellung falsch konfiguriert wurde, können Sie beispielsweise keine Kennwortrichtlinien herunterladen.

## <a name="download"></a>Download

Die zwei erforderlichen Agent-Installer für den Azure AD-Kennwortschutz können Sie im [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071) herunterladen.

## <a name="next-steps"></a>Nächste Schritte
[Bereitstellen des Kennwortschutzes für Azure AD](howto-password-ban-bad-on-premises-deploy.md)
