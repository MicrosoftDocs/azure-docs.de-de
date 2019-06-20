---
title: Faktoren, die die Leistung von Azure AD Connect beeinflussen
description: Dieses Dokument erklärt, wie verschiedene Faktoren die Leistung der Azure AD Connect-Bereitstellungs-Engine beeinflussen. Mithilfe dieser Faktoren können Organisationen ihre Azure AD Connect-Bereitstellung besser planen und auch ihre Synchronisierungsanforderungen erfüllen.
services: active-directory
author: billmath
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2018
ms.reviewer: martincoetzer
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a3a57fbe5df690e4dbdba8cbab85e62648bb298
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60295370"
---
# <a name="factors-influencing-the-performance-of-azure-ad-connect"></a>Faktoren, die die Leistung von Azure AD Connect beeinflussen

Azure AD Connect synchronisiert Ihre Active Directory-Instanz mit Azure AD. Dieser Server ist eine wichtige Komponente beim Übertragen Ihrer Benutzeridentitäten in die Cloud. Diese Hauptfaktoren beeinflussen die Leistung eines Azure AD Connect-Tools:

| **Designfaktor**| **Definition** |
|:-|-|
| Topologie| Die Verteilung der Endpunkte und Komponenten, die Azure AD Connect im Netzwerk verwaltet. |
| Skalieren| Die Anzahl von Objekten wie Benutzern, Gruppen und Organisationseinheiten (OE), die Azure AD Connect verwaltet. |
| Hardware| Die Hardware (physisch oder virtuell) des Azure AD Connect-Tools und die abhängige Leistungskapazität jeder Hardwarekomponente einschließlich CPU-, Arbeitsspeicher-, Netzwerk- und Festplattenkonfiguration. |
| Konfiguration| Funktionsweise der Verarbeitung von Verzeichnissen und Informationen durch Azure AD Connect. |
| Laden| Häufigkeit der Objektänderungen. Die Lasten können während einer Stunde, eines Tages oder einer Woche variieren. Je nach Komponente müssen Sie die Entwicklung ggf. an die Spitzen- oder Durchschnittslast anpassen. |

Dieses Dokument beschreibt die Faktoren, die die Leistung der Azure AD Connect-Bereitstellungs-Engine beeinflussen. Große und komplexe Organisationen (Organisationen mit über 100.000 bereitgestellten Objekten) können die Empfehlungen nutzen, um ihre Azure AD Connect-Implementierung zu optimieren, falls die hier erwähnten Leistungsprobleme auftreten. Die anderen Komponenten von Azure AD Connect, z.B. [Azure AD Connect Health](how-to-connect-health-agent-install.md) und Agents, werden hier nicht behandelt.

> [!IMPORTANT]
> Microsoft unterstützt die Änderung und den Einsatz von Azure AD Connect außerhalb der formal dokumentierten Aktionen nicht. Eine dieser Aktionen kann zu einem inkonsistenten oder nicht unterstützten Status der Azure AD Connect-Synchronisierung führen. Folglich kann Microsoft auch keinen technischen Support für solche Bereitstellungen leisten.

## <a name="azure-ad-connect-component-factors"></a>Azure AD Connect-Komponenten

Das folgende Diagramm zeigt die leistungsstarke Architektur der Bereitstellungs-Engine, die mit einer einzigen Gesamtstruktur verbunden ist, obwohl mehrere Gesamtstrukturen unterstützt werden. Diese Architektur veranschaulicht, wie die verschiedenen Komponenten miteinander interagieren.

![AzureADConnentInternal](media/plan-connect-performance-factors/AzureADConnentInternal.png)

Die Bereitstellungs-Engine stellt eine Verbindung mit jeder Active Directory-Gesamtstruktur und Azure AD her. Das Lesen von Informationen aus jedem Verzeichnis wird als „Import“ bezeichnet. Der „Export“ umfasst die Aktualisierung der Verzeichnisse aus der Bereitstellungs-Engine. Die Synchronisierung wertet die Regeln aus, wie sich Objekte in der Bereitstellungs-Engine bewegen. Weitere Informationen finden Sie unter [Azure AD Connect-Synchronisierung: Grundlagen der Architektur](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture).

Azure AD Connect verwendet die folgenden Stagingbereiche, Regeln und Prozesse, um die Synchronisierung zwischen Active Directory und Azure AD zu ermöglichen:

* **Connectorbereich**: Objekte aus jedem verknüpften Verzeichnis, den eigentlichen Verzeichnissen, werden zuerst hier bereitgestellt, bevor sie von der Bereitstellungs-Engine verarbeitet werden. Azure AD hat einen eigenen Connectorbereich und jede Gesamtstruktur, mit der Sie eine Verbindung herstellen, hat ebenfalls ihren eigenen Connectorbereich.
* **Metaverse**: Objekte, die synchronisiert werden müssen, werden hier auf Basis der Synchronisierungsregeln erstellt. Objekte müssen in der Metaverse vorhanden sein, bevor sie die anderen verknüpften Verzeichnisse mit Objekten und Attributen füllen können. Es gibt nur eine Metaverse.
* **Synchronisierungsregeln**: Sie entscheiden, welche Objekte erstellt (projiziert) oder mit Objekten in der Metaverse verbunden (verknüpft) werden. Die Synchronisierungsregeln bestimmen auch, welche Attributwerte in die und aus den Verzeichnissen kopiert oder transformiert werden.
* **Ausführende Profile**: Sie bündeln die Prozessschritte beim Kopieren von Objekten und deren Attributwerten gemäß den Synchronisierungsregeln zwischen den Stagingbereichen und den verbundenen Verzeichnissen.

Es gibt verschiedene ausführende Profile, um die Leistung der Bereitstellungs-Engine zu optimieren. Die meisten Organisationen verwenden Standardzeitpläne und -profile für normale Vorgänge, doch einige Organisationen müssen ggf. den[Zeitplan ändern](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) oder andere ausführende Profile auslösen, um mit ungewöhnlichen Situationen umzugehen. Folgende ausführende Profile sind verfügbar:

### <a name="initial-sync-profile"></a>Profil „Erste Synchronisierung“

Das Profil „Erste Synchronisierung“ ist der Prozess, bei dem die verbundenen Verzeichnisse, z.B. eine Active Directory-Gesamtstruktur, zum ersten Mal gelesen werden. Anschließend werden alle Einträge in der Datenbank der Synchronisierungs-Engine analysiert. Der erste Zyklus erstellt neue Objekte in Azure AD und nimmt zusätzliche Zeit in Anspruch, falls Sie große Active Directory-Gesamtstrukturen haben. Die erste Synchronisierung umfasst die folgenden Schritte:

1. Vollständiger Import auf allen Connectors
2. Vollständige Synchronisierung auf allen Connectors
3. Export auf allen Connectors

### <a name="delta-sync-profile"></a>Profil „Deltasynchronisierung“

Um den Synchronisierungsvorgang zu optimieren, verarbeitet dieses ausführende Profil nur die Objektänderungen (Erstellen, Löschen und Aktualisieren) in Ihren verbundenen Verzeichnissen, die seit der letzten Synchronisierung vorgenommen wurden. Standardmäßig wird das Profil „Deltasynchronisierung“ alle 30 Minuten ausgeführt. Organisationen wird empfohlen, die benötigte Zeit unter 30 Minuten zu halten, damit Azure AD auf dem neuesten Stand ist. Verwenden Sie für die Überwachung der Azure AD Connect-Integrität den [Health-Überwachungs-Agent](how-to-connect-health-sync.md), um alle Prozessprobleme anzuzeigen. Ein Profil „Deltasynchronisierung“ umfasst die folgenden Schritte:

1. Deltaimport auf allen Connectors
2. Deltasynchronisierung auf allen Connectors
3. Export auf allen Connectors

Ein klassisches Deltasynchronisierungsszenario für Unternehmen sieht folgendermaßen aus:

- ~ 1 % der Objekte werden gelöscht
- ~ 1 % der Objekte werden erstellt
- ~ 5 % der Objekte werden geändert

Die Änderungsrate kann variieren, je nachdem, wie oft Ihre Organisation Benutzer in der Active Directory-Instanz aktualisiert. So können beispielsweise höhere Änderungsraten auftreten, wenn zeitweise Mitarbeiter eingestellt werden oder das Unternehmen verlassen.

### <a name="full-sync-profile"></a>Profil „Vollständige Synchronisierung“

Bei einer der folgenden Konfigurationsänderungen müssen Sie einen vollständigen Synchronisierungszyklus ausführen:



- Erweiterung des Bereichs der Objekte oder Attribute, die aus den verbundenen Verzeichnissen importiert werden. Z.B. wenn Sie Ihrem Importbereich eine Domäne oder eine Organisationseinheit hinzufügen.
- Änderungen an den Synchronisierungsregeln. Z.B. wenn Sie eine neue Regel erstellen, um den Titel eines Benutzers in Azure AD mit den Angaben aus „extension_attribute3“ in Active Directory aufzufüllen. Dieses Update erfordert, dass die Bereitstellungs-Engine alle vorhandenen Benutzer erneut überprüft, um ihre Titel zu aktualisieren und Änderungen künftig anzuwenden.

Die folgenden Vorgänge sind in einem vollständigen Synchronisierungszyklus enthalten:

1. Vollständiger Import auf allen Connectors
2. Vollständige/Deltasynchronisierung auf allen Connectors
3. Export auf allen Connectors

> [!NOTE]
> Eine sorgfältige Planung ist erforderlich, wenn Sie Massenupdates für viele Objekte in Ihrer Active Directory-Instanz oder Azure AD ausführen. Massenupdates führen aufgrund zahlreicher Objektänderungen dazu, dass die Deltasynchronisierung mehr Zeit beim Import in Anspruch nimmt. Lange andauernde Importe sind auch dann möglich, wenn das Massenupdate keine Auswirkungen auf den Synchronisierungsprozess hat. Wenn Sie beispielsweise vielen Benutzern Lizenzen in Azure AD zuweisen, führt dies zu einem langen Importzyklus in Azure AD, jedoch nicht zu Attributänderungen in Active Directory.

### <a name="synchronization"></a>Synchronisierung

Die Laufzeit des Synchronisierungsprozesses weist folgende Leistungsmerkmale auf:

* Die Synchronisierung ist ein Singlethreadvorgang. Das bedeutet, die Bereitstellungs-Engine führt keine parallele Verarbeitung von ausführenden Profilen verbundener Verzeichnisse, Objekte oder Attribute aus.
* Die Importzeit wächst linear mit der Anzahl von zu synchronisierenden Objekten. Wenn beispielsweise 10.000 Objekte 10 Minuten für den Import benötigen, dann benötigen 20.000 Objekte auf demselben Server etwa 20 Minuten.
* Der Export ist ebenfalls linear.
* Die Synchronisierung wächst exponentiell basierend auf der Anzahl von Objekten mit Verweisen auf andere Objekte. Gruppenmitgliedschaften und geschachtelte Gruppen haben die größte Auswirkung auf die Leistung, da die Mitglieder auf Benutzerobjekte oder andere Gruppen verweisen. Finden Sie diese Verweise und referenzieren Sie sie auf tatsächliche Objekte in der Metaverse, um den Synchronisierungszyklus abzuschließen.

### <a name="filtering"></a>Filterung

Die Größe der zu importierenden Active Directory-Topologie hat den größten Einfluss auf die Leistung der internen Bereitstellungs-Engine-Komponenten und ihre erforderliche Gesamtzeit.

Verwenden Sie die [Filterung](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering), um die Objekte auf die synchronisierten zu reduzieren. Dadurch verhindern Sie, dass unnötige Objekte verarbeitet und in Azure AD exportiert werden. Diese Filtertechniken stehen in der folgenden empfohlenen Reihenfolge zur Verfügung:



- Mit der **domänenbasierten Filterung** lassen sich bestimmte Domänen für die Synchronisierung mit Azure AD auswählen. Sie müssen Domänen zur Konfiguration der Synchronisierungs-Engine hinzufügen und daraus entfernen, wenn Sie nach der Installation der Azure AD Connect-Synchronisierung Änderungen an Ihrer lokalen Infrastruktur vornehmen.
- Die **OE-Filterung** legt anhand von Organisationseinheiten bestimmte Objekte in Active Directory-Domänen für die Azure AD-Bereitstellung fest. Diese Methode steht an zweiter Stelle, da sie einfache LDAP-Bereichsabfragen für den Import einer kleineren Teilmenge von Objekten aus Active Directory verwendet.
- Die **Attributfilterung pro Objekt** bestimmt anhand der Attributwerte von Objekten, ob ein bestimmtes Active Directory-Objekt in Azure AD bereitgestellt wird. Diese Methode ist ideal für die Feinabstimmung Ihrer Filter, wenn die domänenbasierte und OE-Filterung nicht den spezifischen Filteranforderungen entsprechen. Die Attributfilterung reduziert nicht die Importzeit, sondern ggf. die Synchronisierungs- und Exportzeiten.
- Die **gruppenbasierte Filterung** legt anhand der Gruppenzugehörigkeit fest, ob Objekte in Azure AD bereitgestellt werden. Diese Methode ist nur für Testzwecke und nicht für die Produktion geeignet, weil beim Synchronisierungszyklus zusätzlicher Aufwand für die Überprüfung der Gruppenzugehörigkeit anfällt.

Viele persistente [Disconnectorobjekte](concept-azure-ad-connect-sync-architecture.md#relationships-between-staging-objects-and-metaverse-objects) in Ihrem Active Directory-Connectorbereich können zu längeren Synchronisierungszeiten führen, da die Bereitstellungs-Engine jedes Disconnectorobjekt für eine mögliche Verbindung im Synchronisierungszyklus neu bewerten muss. Mit den folgenden Empfehlungen können Sie dieses Problem umgehen:



- Platzieren Sie die Disconnectorobjekte mithilfe der domänenbasierten oder OE-Filterung außerhalb des Importbereichs.
- Projizieren/verbinden Sie die Objekte mit der Metaverse, und setzen Sie das Attribut [cloudFiltered](how-to-connect-sync-configure-filtering.md#negative-filtering-do-not-sync-these) auf TRUE, um die Bereitstellung dieser Objekte im Azure AD-Connectorbereich zu verhindern.

> [!NOTE]
> Beim Filtern von zu vielen Objekten können der Überblick verloren gehen oder Probleme mit Anwendungsberechtigungen auftreten. In einer hybriden Exchange Online-Implementierung werden Benutzern mit lokalen Postfächern beispielsweise mehr Benutzer in ihrer globalen Adressliste angezeigt als Benutzern mit Postfächern in Exchange Online. In anderen Fällen möchte ein Benutzer ggf. einem anderen Benutzer den Zugriff auf eine Cloud-App gewähren, was nicht zum Umfang der gefilterten Objektmenge gehört.

### <a name="attribute-flows"></a>Attributflüsse

Attributflüsse bezeichnen das Kopieren oder Transformieren der Attributwerte von Objekten aus einem verbundenen Verzeichnis in ein anderes verbundenes Verzeichnis. Sie sind als Teil der Synchronisierungsregeln definiert. Wenn beispielsweise die Telefonnummer eines Benutzers in Ihrer Active Directory-Instanz geändert wird, wird die Telefonnummer in Azure AD aktualisiert. Organisationen können die [Attributflüsse ändern](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-change-the-configuration), um verschiedenen Anforderungen zu erfüllen. Es wird empfohlen, die vorhandenen Attributflüsse vor dem Ändern zu kopieren.

Einfache Umleitungen, z.B. das Verschieben eines Attributwerts zu einem anderen Attribut, haben keinen wesentlichen Einfluss auf die Leistung. Ein Beispiel für eine Umleitung ist das Senden einer Mobiltelefonnummer in Active Directory an die Bürotelefonnummer in Azure AD.

Das Transformieren von Attributwerten kann sich auf die Leistung des Synchronisierungsprozesses auswirken. Dieser Vorgang umfasst das Modifizieren, erneute Formatieren, Verketten und Subtrahieren von Attributwerten.

Organisationen können verhindern, dass bestimmte Attribute nach Azure AD verschoben werden, doch dies hat keinen Einfluss auf die Leistung der Bereitstellungs-Engine.

> [!NOTE]
> Löschen Sie keine unerwünschten Attributflüsse in Ihren Synchronisierungsregeln. Es wird empfohlen, diese stattdessen zu deaktivieren, da gelöschte Regeln bei Azure AD Connect-Upgrades neu erstellt werden.

## <a name="azure-ad-connect-dependency-factors"></a>Azure AD Connect-Abhängigkeiten

Die Leistung von Azure AD Connect hängt von der Leistung der verbundenen Verzeichnisse ab, in die das Tool importiert bzw. aus denen es exportiert. Ein Beispiel wäre die Größe der zu importierenden Active Directory-Instanz oder die Netzwerklatenz von Azure AD. Auch die von der Bereitstellungs-Engine verwendete SQL-Datenbank wirkt sich auf die Gesamtleistung des Synchronisierungszyklus aus.

### <a name="active-directory-factors"></a>Faktoren von Active Directory

Wie bereits erwähnt, beeinflusst die Anzahl von zu importierenden Objekten die Leistung erheblich. Die [Hardware und die Voraussetzungen für Azure AD Connect](how-to-connect-install-prerequisites.md) erläutern spezifische Hardwareschichten, die auf der Größe Ihrer Bereitstellung basieren. Azure AD Connect unterstützt nur spezifische Topologien, wie in [Topologien für Azure AD Connect](plan-connect-topologies.md) beschrieben. Es gibt keine Leistungsoptimierungen und Empfehlungen für nicht unterstützte Topologien.

Stellen Sie sicher, dass Ihr Azure AD Connect-Server die Hardwareanforderungen erfüllt, die sich aus der Größe Ihrer zu importierenden Active Directory-Instanz ergeben. Eine schlechte oder langsame Netzwerkverbindung zwischen dem Azure AD Connect-Server und Ihren Active Directory-Domänencontrollern kann den Import verlangsamen.

### <a name="azure-ad-factors"></a>Faktoren von Azure AD

Azure AD schützt den Clouddienst mithilfe von Drosselung vor DoS-Angriffen (Denial-of-Service). Derzeit hat Azure AD ein Drosselungslimit von 7.000 Schreibvorgängen alle 5 Minuten (84.000 pro Stunde). Unter anderem lassen sich die folgenden Vorgänge drosseln:



- Azure AD Connect-Export in Azure AD
- PowerShell-Skripts oder -Anwendungen, die Azure AD direkt aktualisieren (auch im Hintergrund), z.B. dynamische Gruppenmitgliedschaften
- Benutzer, die ihre eigenen Identitätsdatensätze aktualisieren, z.B. die Registrierung für die mehrstufige Authentifizierung oder die Self-Service-Kennwortzurücksetzung
- Vorgänge innerhalb der grafischen Benutzeroberfläche

Planen Sie Bereitstellungs- und Wartungsaufgaben, damit Ihr Azure AD Connect-Synchronisierungszyklus nicht durch Drosselungslimits eingeschränkt wird. Wenn Sie z.B. im Rahmen der Einstellung vieler neuer Mitarbeiter zahlreiche Benutzeridentitäten erstellen, kann dies Updates von dynamischen Gruppenmitgliedschaften, Lizenzzuweisungen und Self-Service-Kennwortzurücksetzungen zur Folge haben. Es wird empfohlen, diese Schreibvorgänge über mehrere Stunden oder Tage zu verteilen.

### <a name="sql-database-factors"></a>Faktoren der SQL-Datenbank

Die Größe Ihrer Active Directory-Quelltopologie wirkt sich auf die Leistung Ihrer SQL-Datenbank aus. Berücksichtigen Sie die [Hardwareanforderungen](how-to-connect-install-prerequisites.md) für die SQL Server-Datenbank und beachten Sie die folgenden Empfehlungen:



- Organisationen mit über 100.000 Benutzern können Netzwerklatenzen reduzieren, indem sie die SQL-Datenbank und die Bereitstellungs-Engine auf demselben Server sichern.
- Verwenden Sie aufgrund der hohen Anforderungen an die Datenträgerein- und -ausgabe (E/A) des Synchronisierungsprozesses zum Erzielen optimaler Ergebnisse SSDs für die SQL-Datenbank der Bereitstellungs-Engine. Wenn das nicht möglich ist, werden RAID 0- bzw. RAID 1-Konfigurationen empfohlen.
- Führen Sie eine vollständige Synchronisierung nicht präventiv durch – das verursacht unnötige Änderungen und langsamere Antwortzeiten.

## <a name="conclusion"></a>Zusammenfassung

Beachten Sie die folgenden Empfehlungen, um die Leistung Ihrer Azure AD Connect Implementierung zu optimieren:



- Verwenden Sie die [empfohlene Hardwarekonfiguration](how-to-connect-install-prerequisites.md) basierend auf Ihrer Implementierungsgröße für den Azure AD Connect-Server.
- Verwenden Sie für Azure AD Connect-Upgrades in großen Bereitstellungen die [Swingmigration](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-upgrade-previous-version#swing-migration), um die geringste Downtime und die beste Zuverlässigkeit zu gewährleisten. 
- Verwenden Sie SSDs für die SQL-Datenbank für die optimale Schreibleistung.
- Wenden Sie domänenbasierte, OE- oder Attributfilter für den Active Directory-Bereich an, um nur die Objekte zu berücksichtigen, die in Azure AD bereitgestellt werden müssen.
- Wenn Sie die Standardregeln von Attributflüssen ändern möchten, kopieren Sie zuerst die Regel, ändern Sie die Kopie, und deaktivieren Sie dann die ursprüngliche Regel. Denken Sie daran, eine vollständige Synchronisierung erneut auszuführen.
- Planen Sie genügend Zeit für das ausführende Profil der ersten vollständigen Synchronisierung ein.
- Versuchen Sie, den Deltasynchronisierungszyklus in 30 Minuten abzuschließen. Wenn das Profil „Deltasynchronisierung“ nicht innerhalb von 30 Minuten abgeschlossen ist, ändern Sie die Standardsynchronisierungshäufigkeit, um einen vollständigen Deltasynchronisierungszyklus einzuschließen.
- Überwachen Sie die [Integrität Ihrer Azure AD Connect-Synchronisierung](how-to-connect-health-agent-install.md) in Azure AD.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
