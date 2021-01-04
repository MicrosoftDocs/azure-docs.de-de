---
title: Erstellen von Plänen für Ihr Azure-Anwendungsangebot
description: Erfahren Sie, wie Sie Pläne für Ihr Azure-Anwendungsangebot in Partner Center erstellen.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: fd5ee9085cf716128c5b3ae073f963c76d2bd17a
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369807"
---
# <a name="how-to-create-plans-for-your-azure-application-offer"></a>Erstellen von Plänen für Ihr Azure-Anwendungsangebot

Für Angebote, die über den kommerziellen Marketplace von Microsoft verkauft werden, ist mindestens ein Plan zum Auflisten im kommerziellen Marketplace erforderlich. Sie können eine Vielzahl von Plänen mit unterschiedlichen Optionen im gleichen Angebot erstellen. Diese Pläne (manchmal als SKUs bezeichnet) können sich in Bezug auf den Plantyp (_Lösungsvorlage_ oder _verwaltete Anwendung_), die Monetarisierung oder Zielgruppe unterscheiden. Allgemeine Anleitungen zu Plänen finden Sie unter [Pläne und Preise für Angebote im kommerziellen Marketplace](plans-pricing.md).

## <a name="create-a-plan"></a>Erstellen von Plänen

1. Wählen Sie oben auf der Registerkarte **Planübersicht** die Option **+ Neuen Plan erstellen** aus.
1. Geben Sie im angezeigten Dialogfeld im Feld **Plan-ID** eine eindeutige Plan-ID ein. Diese ID ist für Kunden in der Produkt-URL sichtbar. Verwenden Sie bis zu 50 Kleinbuchstaben, Ziffern, Bindestriche und Unterstriche. Die Plan-ID kann nicht mehr geändert werden, nachdem Sie **Erstellen** ausgewählt haben.
1. Geben Sie im Feld **Planname** einen eindeutigen Namen für diesen Plan ein. Für Kunden wird dieser Name angezeigt, wenn sie einen Plan in Ihrem Angebot auswählen. Es sind maximal 50 Zeichen zulässig.
1. Klicken Sie auf **Erstellen**.

## <a name="define-the-plan-setup"></a>Definieren des Plansetups

Mithilfe der Registerkarte **Plansetup** können Sie den Plantyp festlegen sowie angeben, ob die technische Konfiguration von einem anderen Plan wiederverwendet wird, und in welchen Azure-Regionen der Plan verfügbar sein soll. Ihre Antworten auf dieser Registerkarte beeinflussen, welche Felder auf anderen Registerkarten dieses Plans angezeigt werden.

### <a name="select-the-plan-type"></a>Auswählen des Plantyps

- Wählen Sie in der Liste **Plantyp** entweder **Projektmappenvorlage** oder **Verwaltete Anwendung** aus.

Ein Plan mit **Lösungsvorlage** wird vollständig vom Kunden verwaltet. Ein Plan mit **verwalteter Anwendung** ermöglicht Herausgebern, die Anwendung für den Kunden zu verwalten. Ausführliche Informationen zu diesen beiden Plantypen finden Sie unter [Plantypen](plan-azure-application-offer.md#types-of-plans).

### <a name="re-use-technical-configuration-optional"></a>Wiederverwenden von technischen Konfigurationen (optional)

Wenn Sie in diesem Angebot mehrere Pläne desselben Typs erstellt haben und deren technische Konfiguration identisch ist, können Sie die technische Konfiguration eines anderen Plans wiederverwenden. Diese Einstellung kann nicht geändert werden, sobald der Plan veröffentlicht wurde.

#### <a name="to-re-use-technical-configuration"></a>So verwenden Sie eine technische Konfiguration erneut

1. Aktivieren Sie das Kontrollkästchen **In diesem Plan wird die technische Konfiguration aus einem anderen Plan desselben Typs wiederverwendet**.
1. Wählen Sie in der angezeigten Liste den gewünschten Basisplan aus.

> [!NOTE]
> Wenn Sie Pakete aus einem anderen Plan wiederverwenden, wird die gesamte Registerkarte **Technische Konfiguration** in diesem Plan nicht mehr angezeigt. Die technischen Konfigurationsdetails aus dem anderen Plan, einschließlich aller Updates, die Sie in Zukunft vornehmen, werden für diesen Plan ebenfalls verwendet.

### <a name="select-azure-regions-clouds"></a>Auswählen von Azure-Regionen (Clouds)

Ihr Plan muss in mindestens einer Azure-Region verfügbar gemacht werden. Nachdem Ihr Plan veröffentlicht und in einer bestimmten Azure-Region verfügbar gemacht wurde, können Sie diese Region nicht mehr aus Ihrem Angebot entfernen.

#### <a name="azure-global-region"></a>Region Azure Global

Das Kontrollkästchen **Azure Global** ist standardmäßig aktiviert. Dadurch ist Ihr Plan für Kunden in allen globalen Azure-Regionen mit Integration des kommerziellen Marketplace verfügbar. Bei Plänen für verwaltete Anwendungen können Sie Märkte auswählen, für die Sie Ihren Plan verfügbar machen möchten.

Um Ihr Angebot aus dieser Region zu entfernen, deaktivieren Sie das Kontrollkästchen **Azure Global**.

#### <a name="azure-government-region"></a>Azure Government-Region

Diese Region bietet kontrolliertem Zugriff für Kunden der US-Behörden auf Bundes-, Bundesstaats-, Gemeinde- oder Stammesgebietsebene sowie für Partner, die diese beliefern können. Als Herausgeber sind Sie für alle Kompatibilitätskontrollen, Sicherheitsmaßnahmen und bewährten Methoden verantwortlich. Azure Government verwendet physisch isolierte Rechenzentren und Netzwerke (die sich ausschließlich in den USA befinden).

Azure Government-Dienste verarbeiten Daten, die bestimmten behördlichen Vorschriften und Anforderungen unterliegen. Beispielsweise FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 und CJIS. Um bei Ihren Zertifizierungen ein Bewusstsein für diese Programme zu schaffen, können Sie bis zu 100 Links bereitstellen, in denen sie beschrieben werden. Diese können entweder direkt mit ihrem Listing im Programm verknüpft sein oder zu Beschreibungen der Konformität mit diesen Programmen auf Ihren eigenen Websites weiterleiten. Diese Links sind nur für Azure Government-Kunden sichtbar.

##### <a name="to-select-the-azure-government-region"></a>So wählen Sie die Azure Government-Region aus

1. Aktivieren Sie das Kontrollkästchen **Azure Government**.
1. Wählen Sie unter **Azure Government-Zertifizierungen** die Option **Zertifizierung hinzufügen (max. 100)** aus.
1. Geben Sie in den angezeigten Feldern einen Namen und einen Link zu einer Zertifizierung ein.
1. Um eine weitere Zertifizierung hinzuzufügen, wiederholen Sie die Schritte 2 und 3.

Wählen Sie **Entwurf speichern** aus, bevor Sie mit der nächsten Registerkarte fortfahren: Planlisting.

## <a name="define-the-plan-listing"></a>Definieren des Planlistings

Auf der Registerkarte **Planlisting** konfigurieren Sie die Listendetails des Plans. Auf dieser Registerkarte werden spezifische Informationen angezeigt, die den Unterschied zwischen Plänen desselben Angebots angeben. Sie können den Plannamen, Zusammenfassung und Beschreibung so definieren, wie sie im kommerziellen Marketplace angezeigt werden sollen.

1. Im Feld **Planname** wird der Name angezeigt, den Sie zuvor für diesen Plan angegeben haben. Sie können diesen jederzeit ändern. Dieser Name wird als Titel des Softwareplans für Ihr Angebot im kommerziellen Marketplace angezeigt und ist auf 100 Zeichen beschränkt.
1. Geben Sie im Feld **Planzusammenfassung** eine kurze Zusammenfassung Ihres Plans (nicht des Angebots) an. Diese Zusammenfassung darf maximal 100 Zeichen enthalten.
1. Erläutern Sie im Feld **Planbeschreibung**, was diesen Softwareplan einzigartig macht und von anderen Softwareplänen in Ihrem Angebot unterscheidet. Beschreiben Sie nicht das Angebot, sondern nur den Plan. Diese Beschreibung kann bis zu 2.000 Zeichen umfassen.
1. Wählen Sie **Entwurf speichern** aus, bevor Sie fortfahren.

## <a name="next-steps"></a>Nächste Schritte

Führen Sie eines der folgenden Verfahren aus:

- Wenn Sie einen Plan für eine Lösungsvorlage konfigurieren, lesen Sie [Konfigurieren eines Plans für eine Lösungsvorlage](create-new-azure-apps-offer-solution.md).
- Wenn Sie einen Plan für eine verwaltete Anwendung konfigurieren, lesen Sie [Konfigurieren eines Plans für verwaltete Anwendungen](create-new-azure-apps-offer-managed.md).
