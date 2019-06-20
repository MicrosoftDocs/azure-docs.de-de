---
title: Überprüfen des Veröffentlichungsstatus Ihres Commercial Marketplace-Angebots
description: Überprüfen Sie den Status der Validierungs-, Zertifizierungs- und Vorschauschritte, die erforderlich sind, um ein Angebot im Microsoft Partner Center über Commercial Marketplace zu veröffentlichen.
author: mattwojo
manager: evansma
ms.author: mattwoj
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 9bf3136969974abbe9a99a5632478e3cbb22307e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474525"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Überprüfen des Veröffentlichungsstatus Ihres Commercial Marketplace-Angebots

Sie können Ihren aktuellen **Veröffentlichungsstatus** im Partner Center im [Commercial Marketplace-Portal](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) auf der Registerkarte **Offer overview** (Angebotsübersicht) einsehen.

Für jedes Angebot sollte eine der folgenden Statusangaben angezeigt werden.

| **Status**    | **Beschreibung**  |
| :---------- | :-------------------|
| **Draft** | Angebot wurde erstellt, wird aber nicht veröffentlicht. |
| **Veröffentlichung wird durchgeführt** | Das Angebot / der Plan durchläuft die Schritte des Veröffentlichungsvorgangs. |
| **Erforderliche Aktion** | Ein schwerwiegendes Problem wurde während der Zertifizierung durch Microsoft oder eines anderen Veröffentlichungsschritts erkannt. |
| **Vorschau** | Das Angebot wurde von Microsoft zertifiziert und muss nun noch abschließend durch den Herausgeber verifiziert werden. Wählen Sie „Live schalten“, um das Angebot live zu schalten. |
| **Live** | Das Angebot ist im Marketplace live geschaltet und kann von Kunden angezeigt und erworben werden. |
| **Ausstehender Verkaufsstopp** | Der Herausgeber hat den „Verkaufsstopp“ für Angebot oder Plan ausgewählt, aber die Aktion ist noch nicht abgeschlossen. |
| **Im Marketplace nicht verfügbar** | Ein zuvor im Marketplace veröffentlichtes Angebot / veröffentlichter Plan wurde entfernt. |

## <a name="automated-validation"></a>Automatisierte Validierung

Der erste Schritt im Veröffentlichungsprozess sieht eine Reihe automatisierter Validierungen vor. Jeder Validierungsschritt entspricht einem Feature, das Sie bei der Erstellung Ihres Angebots aktiviert haben. Wenn dieses Feature nicht aktiviert wurde, wechselt die Validierung zum nächsten Veröffentlichungsschritt. Jede Validierungsprüfung muss abgeschlossen sein, bevor der Veröffentlichungsstatus genehmigt wird.

- **Einrichten des Flows zum Kauf eines Angebots (<10 Min.)**

In diesem Schritt stellen wir sicher, dass Ihr Angebot vom Kunden im Azure-Portal gekauft werden kann. Dieser Schritt gilt nur für Angebote, die über Microsoft verkauft werden.

- **Datenvalidierung mit Testversion (ca. 5 Min.)**

In diesem Schritt validieren wir die Daten, die Sie im Abschnitt „Technische Konfiguration“ für die Testversion des Angebots angegeben haben. Die Funktionalität der Testversion wird überprüft und genehmigt. Dieser Schritt gilt nur für Angebote mit aktivierter Testversion.

- **Bereitstellung der Testversion (ca. 30 Min.)**

Nach der Validierung der Daten und Funktionalität Ihrer Testversion im vorherigen Schritt werden wir in diesem Schritt Instanzen Ihrer Testversion so bereitstellen und replizieren, dass sie für Kunden einsatzbereit sind.  Dieser Schritt gilt nur für Angebote mit aktivierter Testversion.

- **Überprüfung der Leadverwaltung und Registrierung (<15 Min.)**

In diesem Schritt bestätigen wir, dass Ihr Leadverwaltungssystem Kundenleads basierend auf den Angaben in der Angebotseinrichtung erhalten kann. Dieser Schritt gilt nur für Angebote mit aktivierter Leadverwaltung.

## <a name="certification"></a>Zertifizierung

Vor der Veröffentlichung müssen Angebote, die für Commercial Marketplace im Partner Center eingereicht werden, zertifiziert werden. Eingereichte Angebote werden strengen Tests unterzogen, von denen einige automatisiert und andere manuell erfolgen, einschließlich einer Überprüfung gemäß den [Azure Marketplace-Zertifizierungsrichtlinien](https://docs.microsoft.com/legal/marketplace/general-policies). Eingereichte Angebote müssen als zertifizierbar gekennzeichnet sein, bevor sie zum nächsten Schritt im Veröffentlichungsflow übergehen.

### <a name="types-of-validation-that-take-place-during-certification"></a>Typen der während der Zertifizierung erfolgenden Validierung

Für jedes eingereichte Angebot gibt es im Zertifizierungsprozess drei Validierungsstufen.

- Eignung des Herausgebers
- Validierung von Inhalten
- Technische Validierung

#### <a name="publisher-business-eligibility"></a>Eignung des Herausgebers

Bei jeder Angebotsart wird eine Reihe von Standardeignungskriterien geprüft, die der Herausgeber erfüllen muss. Zu den Eignungskriterien können der MPN-Status des Herausgebers, die erworbenen Kompetenzen, die Kompetenzebenen usw. gehören.

#### <a name="content-validation"></a>Validierung von Inhalten

Bei der Inhaltsvalidierung werden die bei der Erstellung Ihres Angebots eingegebenen Informationen auf Qualität und Relevanz geprüft. Bei diesen Prüfungen werden Ihre Eingaben für die Listungsdetails für Marketplace, Preise, Verfügbarkeit, zugehörige Pläne usw. überprüft. Um die Listungskriterien von Azure Marketplace und/oder AppSource zu erfüllen, validieren wir, dass Ihr Angebot Folgendes einschließt:

- einen Titel, der das Angebot genau beschreibt
- durchdachte Beschreibungen mit umfassender Übersicht und Nutzenversprechen
- Screenshots und Begleitvideos in hoher Qualität
- eine Erläuterung, wie das Angebot Microsoft-Plattformen und -Tools nutzt

Erfahren Sie im Artikel zu den [allgemeinen Richtlinien für die Listung](https://docs.microsoft.com/legal/marketplace/general-policies#10-general-listing-policies) mehr zu den Kriterien für die Validierung von Inhalten.

#### <a name="technical-validation"></a>Technische Validierung

Bei der technischen Validierung wird das Angebot (Paket oder Binärdatei) den folgenden Prüfungen unterzogen.
- Prüfung auf Schadsoftware
- Überwachung von Netzwerkaufrufen
- Paketanalyse
- Umfassende Überprüfung der tatsächlichen Funktionalität des Angebots

Das Angebot wird auf verschiedenen Plattformen und mit verschiedenen Versionen getestet, um sicherzustellen, dass es stabil ist.

Im Abschnitt „Technische Konfiguration“ dieses Dokuments finden Sie die für Ihr Angebot erforderlichen spezifischen Konfigurationsdetails.

### <a name="certification-failure-report"></a>Zertifizierungsfehlerbericht

Wenn Ihr Angebot nach Abschluss der Überprüfung die Zertifizierung bestanden hat, gelangt es zum nächsten Schritt im Veröffentlichungsprozess. Wenn Ihr Angebot eine der Listungs-, technischen oder Richtlinienprüfungen nicht bestanden hat oder Sie nicht berechtigt sind, ein solches Angebot abzugeben, wird ein Zertifizierungsfehlerbericht erstellt und Ihnen per E-Mail zugestellt.

Dieser Bericht enthält Beschreibungen aller Richtlinien, gegen die verstoßen wurde, sowie Prüfhinweise. Überprüfen Sie diesen E-Mail-Bericht, beheben Sie alle Probleme, aktualisieren Sie Ihr Angebot bei Bedarf, und reichen Sie es über das [Commercial Marketplace-Portal](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) erneut im Partner Center ein. (Sie können das Angebot bis zum Bestehen der Zertifizierung beliebig oft erneut einreichen).

## <a name="preview-creation"></a>Erstellung einer Vorschau

Während des Schritts **Erstellung einer Vorschau** erstellen wir eine Version Ihres Angebots, die nur für die Zielgruppe zugänglich ist, die Sie im Abschnitt „Vorschau“ Ihres Angebots angegeben haben.

## <a name="publisher-approval"></a>Genehmigung des Herausgebers

In diesem Schritt erhalten Sie eine E-Mail mit der Bitte, die Vorschau Ihres Angebots vor dem letzten Veröffentlichungsschritt zu überprüfen und zu genehmigen.

Wenn Sie sich für den Verkauf Ihres Angebots über Microsoft entschieden haben, können Sie den Kauf und die Bereitstellung Ihres Angebots testen, um sicherzustellen, dass es Ihren Anforderungen während dieser Genehmigungsphase in der Vorschau entspricht. Ihr Angebot ist jedoch noch nicht im öffentlichen Marketplace verfügbar. Nachdem Sie diese Vorschau getestet und genehmigt haben, müssen Sie **Go-Live** (Live schalten) im Dashboard [**Offer Overview**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) (Angebotübersicht) auswählen.

Wenn Sie in dieser Vorschauphase Änderungen am Angebot vornehmen möchten, können Sie es bearbeiten und erneut einreichen, um eine neue Vorschau zu veröffentlichen. Einzelheiten zu weiteren Änderungen finden Sie im Artikel zum [Aktualisieren vorhandener Marketplace-Angebote](#update-existing-marketplace-offers).

Wenn Ihr Angebot bereits live geschaltet ist und der Öffentlichkeit im Marketplace zur Verfügung steht, werden alle von Ihnen vorgenommenen Updates erst dann live geschaltet, wenn Sie **Go-live** (Live schalten) auf dem Dashboard [**Offer Overview**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) (Angebotsübersicht) auswählen.

### <a name="publish-offer-to-the-public"></a>Veröffentlichen des Angebots für die Öffentlichkeit

Melden Sie sich im Partner Center an, und greifen Sie auf das Angebot zu. Sie werden zur Seite **Offer Overview** (Angebotsübersicht) umgeleitet. Oben auf dieser Seite sehen Sie die Option **Go live** (Live schalten). Klicken Sie auf **Go live** (Live schalten). Nach der Bestätigung beginnt die Veröffentlichung des Angebots für die Öffentlichkeit. Sie erhalten eine E-Mail-Benachrichtigung, sobald das Angebot live geschaltet ist.

## <a name="publish"></a>Veröffentlichen

Nachdem Sie für Ihr Angebot auf **Go live** (Live schalten) geklickt und es im Marketplace verfügbar gemacht haben, gibt es eine Reihe abschließender Validierungsprüfungen, die durchlaufen werden, um sicherzustellen, dass das live geschaltete Angebot genau wie die Vorschauversion des Angebots konfiguriert ist.

- **Einrichten des Flows zum Kauf eines Angebots (> 10 Min.)**

In diesem Schritt stellen wir sicher, dass Ihr Angebot vom Kunden im Azure-Portal gekauft werden kann. Dieser Schritt gilt nur für Angebote, die über Microsoft verkauft werden.

- **Datenvalidierung mit Testversion (ca. 5 Min.)**

In diesem Schritt validieren wir die Daten, die Sie im Abschnitt „Technische Konfiguration“ für die Testversion des Angebots angegeben haben. Die Funktionalität der Testversion wird überprüft und genehmigt. Dieser Schritt gilt nur für Angebote mit aktivierter Testversion.

- **Bereitstellung der Testversion (ca. 30 Min.)**

In diesem Schritt werden wir Instanzen Ihrer Testversion so einsetzen und replizieren, dass sie für den Einsatz durch Kunden bereit sind.  Dieser Schritt gilt nur für Angebote mit aktivierter Testversion.

- **Überprüfung der Leadverwaltung und Registrierung (> 15 Min.)**

In diesem Schritt bestätigen wir, dass Ihr Leadverwaltungssystem Kundenleads basierend auf den Angaben in der Angebotseinrichtung erhalten kann. Dieser Schritt gilt nur für Angebote mit aktivierter Leadverwaltung.

- **Endgültige Veröffentlichung (> 30 Min.)**

In diesem Schritt stellen wir sicher, dass Ihr Angebot im Marketplace öffentlich zur Verfügung steht.

## <a name="update-existing-marketplace-offers"></a>Aktualisieren vorhandener Marketplace-Angebote

Wenn Sie Änderungen an einem bereits veröffentlichten Angebot vornehmen möchten, müssen Sie das bestehende Angebot zunächst aktualisieren und dann erneut veröffentlichen.

## <a name="next-steps"></a>Nächste Schritte

- [Aktualisieren eines bestehenden Commercial Marketplace-Angebots](./update-existing-offer.md)
