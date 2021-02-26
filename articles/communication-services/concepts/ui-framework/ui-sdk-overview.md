---
title: 'Azure Communication Services: Übersicht über das Benutzeroberflächen-Framework'
titleSuffix: An Azure Communication Services conceptual document
description: Enthält Informationen zum Benutzeroberflächen-Framework von Azure Communication Services.
author: ddematheu2
ms.author: dademath
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: b6f9987c21ecdcf0e1b5358486312dceb26c8b82
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008552"
---
# <a name="azure-communication-services-ui-framework"></a>Azure Communication Services: Benutzeroberflächen-Framework

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

:::image type="content" source="../media/ui-framework/pre-and-custom-composite.png" alt-text="Vergleich zwischen vordefinierten und benutzerdefinierten Komponenten":::

Mit dem Benutzeroberflächen-Framework von Azure Communication Services ist es für Sie einfach, moderne Benutzeroberflächen für die Kommunikation zu entwickeln. Sie können eine Bibliothek mit für die Produktion geeigneten Benutzeroberflächenkomponenten nutzen, die Sie in Ihre Anwendungen einfügen können:

- **Zusammengesetzte Komponenten**: Bei diesen Komponenten handelt es sich um fertige Lösungen, mit denen gängige Kommunikationsszenarien implementiert werden. Sie können Ihren Anwendungen schnell Umgebungen für Videoanrufe oder Chats hinzufügen. Zusammengesetzte Komponenten bestehen aus mehreren Basiskomponenten.
- **Basiskomponenten**: Bei diesen Komponenten handelt es sich um Open-Source-Bausteine, mit denen Sie benutzerdefinierte Kommunikationsumgebungen erstellen können. Es werden sowohl Komponenten für Anruf- als auch für Chatfunktionen angeboten, die für die Entwicklung von Benutzeroberflächen kombiniert werden können. 

Für diese Clientbibliotheken für Benutzeroberflächen werden jeweils die [Fluent-Entwurfssprache von Microsoft](https://developer.microsoft.com/fluentui/) und die zugehörigen Objekte verwendet. Die Fluent-Benutzeroberfläche stellt eine Grundlage für das Benutzeroberflächen-Framework dar, das sich für die verschiedensten Microsoft-Produkte im Einsatz bewährt hat.

## <a name="differentiating-components-and-composites"></a>**Vergleich von Basis- und zusammengesetzten Komponenten**

**Basiskomponenten** basieren auf den zentralen Azure Communication Services-Clientbibliotheken und dienen zum Implementieren von grundlegenden Aktionen, z. B. Initialisieren der zentralen Clientbibliotheken, Rendern von Videos und Bereitstellen von Benutzersteuerelementen für Stummschalten, Video ein/aus usw. Sie können diese **Basiskomponenten** nutzen, um Ihre eigenen Benutzeroberflächen mit benutzerdefiniertem Layout zu entwickeln, indem Sie vordefinierte und für die Produktion geeignete Kommunikationskomponenten verwenden.

:::image type="content" source="../media/ui-framework/component-overview.png" alt-text="Übersicht über Komponenten für das Benutzeroberflächen-Framework":::

Für **zusammengesetzte Komponenten** werden mehrere **Basiskomponenten** kombiniert, um vollständigere Kommunikationsumgebungen zu erstellen. Diese allgemeinen Komponenten können leicht in eine vorhandene App integriert werden, um eine vollständige Kommunikationsumgebung zu erhalten, ohne dass diese von Grund auf neu erstellt werden muss. Entwickler können sich auf die Entwicklung der restlichen Umgebungsbereiche und die gewünschten Abläufe für ihre Apps konzentrieren und für die komplexeren Kommunikationsabläufe die zusammengesetzten Komponenten nutzen.

:::image type="content" source="../media/ui-framework/composite-overview.png" alt-text="Übersicht über zusammengesetzte Komponenten für das Benutzeroberflächen-Framework":::

## <a name="what-ui-framework-is-best-for-my-project"></a>Welches Benutzeroberflächen-Framework ist für mein Projekt am besten geeignet?

Wenn Sie sich mit den folgenden Anforderungen vertraut machen, können Sie die richtige Clientbibliothek auswählen:

- **Welchen Anpassungsgrad streben Sie an?** Die zentralen Azure Communication Services-Clientbibliotheken weisen keine Benutzeroberfläche auf und sind so konzipiert, dass Sie die Benutzeroberfläche nach Ihren Vorstellungen erstellen können. Mit den Komponenten des Benutzeroberflächen-Frameworks werden Benutzeroberflächenobjekte auf Kosten einer verringerten Anpassungsmöglichkeit bereitgestellt.
- **Benötigen Sie Features für Besprechungen?** Das Besprechungssystem verfügt über mehrere besondere Funktionen, die in den zentralen Azure Communication Services-Clientbibliotheken derzeit nicht verfügbar sind, z. B. verschwommener Hintergrund und Heben der Hand.
- **Welche Plattformen sollen verwendet werden?** Verschiedene Plattformen verfügen über unterschiedliche Funktionen.

Ausführliche Informationen zur Verfügbarkeit von Features in den verschiedenen Benutzeroberflächen-SDKs finden Sie [hier](ui-sdk-features.md). Eine Zusammenfassung der wichtigsten Nachteile ist unten angegeben.

|Clientbibliothek/SDK|Implementierungskomplexität|    Möglichkeit zur Anpassung|  Aufrufen| Chat| [Teams-Interoperabilität](./../teams-interop.md)
|---|---|---|---|---|---|---|
|Zusammengesetzte Komponenten|Niedrig|Niedrig|✔|✔|✕
|Basiskomponenten|Medium|Medium|✔|✔|✕
|Zentrale Clientbibliotheken|High|High|✔|✔ |✔

## <a name="cost"></a>„Cost“ (Kosten)

Für die Nutzung der Azure-Benutzeroberflächen-Frameworks fallen keine zusätzlichen Azure-Kosten oder Verbrauchseinheiten an. Sie zahlen nur für die Nutzung des zugrunde liegenden Diensts. Hierfür werden die gleichen Verbrauchseinheiten für Anruf, Chat und Telefonfestnetz verwendet.

## <a name="supported-use-cases"></a>Unterstützte Anwendungsfälle

Anruf:

- Azure Communication Services-Anruf mit Gruppen-ID beitreten

Chat:

- Azure Communication Services-Chat mit Thread-ID beitreten

## <a name="supported-identities"></a>Unterstützte Identitäten:

Es wird eine Azure Communication Services-Identität benötigt, um das Benutzeroberflächen-Framework initialisieren und die Authentifizierung beim Dienst durchführen zu können. Weitere Informationen zur Authentifizierung finden Sie unter [Authentifizierung](../authentication.md) und [Zugriffstoken](../../quickstarts/access-tokens.md).


## <a name="recommended-architecture"></a>Empfohlene Architektur 

:::image type="content" source="../media/ui-framework/framework-architecture.png" alt-text="Vom Benutzeroberflächen-Framework empfohlene Architektur mit Client/Server-Architektur":::

Zusammengesetzte Komponenten und Basiskomponenten werden mit einem Azure Communication Services-Zugriffstoken initialisiert. Zugriffstoken sollten über einen vertrauenswürdigen Dienst, der von Ihnen verwaltet wird, aus Azure Communication Services beschafft werden. Weitere Informationen finden Sie unter [Schnellstart: Erstellen und Verwalten von Zugriffstoken](../../quickstarts/access-tokens.md) und [Erstellen eines vertrauenswürdigen Authentifizierungsdiensts mithilfe von Azure Functions](../../tutorials/trusted-service-tutorial.md).

Für diese Clientbibliotheken wird auch der Kontext für den Anruf oder Chat benötigt, zu dem der Beitritt erfolgt. Ähnlich wie bei Zugriffstoken sollte dieser Kontext über Ihren eigenen vertrauenswürdigen Dienst an Clients übermittelt werden. In der Liste unten sind die Funktionen für die Initialisierung und Ressourcenverwaltung zusammengefasst, die Sie operationalisieren müssen.

| Contoso-Zuständigkeiten                                 | Zuständigkeiten des Benutzeroberflächen-Frameworks                         |
|----------------------------------------------------------|-----------------------------------------------------------------|
| Bereitstellen des Zugriffstokens aus Azure                    | Übergeben des Zugriffstokens zum Initialisieren von Komponenten        |
| Bereitstellen der Aktualisierungsfunktion                                 | Aktualisieren des Zugriffstokens mit der vom Entwickler bereitgestellten Funktion          |
| Abrufen/Übergeben von Beitrittsinformationen für Anrufe oder Chats          | Übergeben von Anruf- und Chatinformationen zum Initialisieren von Komponenten |
| Abrufen/Übergeben von Benutzerinformationen für ein beliebiges benutzerdefiniertes Datenmodell | Übergeben des benutzerdefinierten Datenmodells an Komponenten für das Rendering          |
