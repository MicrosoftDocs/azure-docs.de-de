---
title: Verwenden von Purview Studio
description: In diesem Konzeptartikel wird beschrieben, wie Sie Azure Purview Studio verwenden.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 1b2d371153d6612f454e1bf51b78c6b6189a08b2
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550396"
---
# <a name="use-purview-studio"></a>Verwenden von Purview Studio

Dieser Artikel enthält eine Übersicht über die wichtigsten Features von Azure Purview.

## <a name="prerequisites"></a>Voraussetzungen

* Im Azure-Portal wurde bereits ein Active Purview-Konto erstellt, und der Benutzer verfügt über die Berechtigungen zum Zugreifen auf Purview Studio.

## <a name="launch-purview-account"></a>Starten des Purview-Kontos

* Navigieren Sie zum Starten Ihres Purview-Kontos im Azure-Portal zu den Purview-Konten, wählen Sie das Konto aus, das Sie starten möchten, und führen Sie den Startvorgang durch.

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Screenshot: Auswählen der Option zum Starten des Azure Purview-Kontokatalogs":::

* Eine weitere Möglichkeit zum Starten des Purview-Kontos besteht darin, zu `https://web.purview.azure.com` zu navigieren, die Option **Azure Active Directory** auszuwählen und auf den Namen des gewünschten Kontos zu klicken.

## <a name="home-page"></a>Startseite

Unten ist die **Startseite** des Azure Purview-Clients dargestellt.

 :::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="Screenshot: Startseite":::

In der folgenden Liste sind die Hauptfunktionen der **Startseite** zusammengefasst. Jede Zahl in der Liste entspricht einer hervorgehobenen Zahl im obigen Screenshot.

1. Anzeigename des Katalogs. Sie können den Katalognamen unter **Verwaltungscenter** > *Kontoinformationen* festlegen.

2. In der Kataloganalyse wird jeweils die Anzahl für die folgenden Elemente angezeigt:
    - Benutzer, Gruppen und Anwendungen
    - Datenquellen
    - Objekte
    - Glossarbegriffe

3. Im Suchfeld können Sie im gesamten Datenkatalog nach Datenressourcen suchen.

4. Über die Schaltflächen für den Schnellzugriff können Sie auf die häufig verwendeten Funktionen der Anwendung zugreifen. Die jeweils vorhandenen Schaltflächen richten sich danach, welche Rolle Ihrem Benutzerkonto zugewiesen ist.

    - Für einen *Datenquellenadministrator* sind die folgenden Schaltflächen für den Schnellzugriff vorhanden: **Register Data Sources** (Datenquellen registrieren) und **Knowledge Center**.
    - Für einen *Datenkurator* sind die Schaltflächen **Knowledge Center**, **Browse Assets** (Ressourcen durchsuchen), **Manage Glossary** (Glossar verwalten) und **Einblicke anzeigen** vorhanden.
    - Für einen *Datenleseberechtigten* sind die Schaltflächen **Knowledge Center**, **Browse Assets** (Ressourcen durchsuchen), **View Glossary** (Glossar anzeigen) und **Einblicke anzeigen** vorhanden.

5. Über die Navigationsleiste auf der linken Seite können Sie auf die Hauptseiten der Anwendung zugreifen. Die jeweils vorhandenen Schaltflächen richten sich danach, welche Rolle Ihrem Benutzerkonto zugewiesen ist.

    - Für einen *Datenquellenadministrator* sind die Schaltflächen **Startseite**, **Quellen** und **Verwaltungscenter** vorhanden.
    - Für einen *Datenkurator* sind die Schaltflächen **Startseite**, **Glossar**, **Erkenntnisse** und **Verwaltungscenter** vorhanden.
    - Für einen *Datenleseberechtigten* sind die Schaltflächen **Startseite**, **Glossar**, **Erkenntnisse** und **Verwaltungscenter** vorhanden.
  
6. Auf der Registerkarte **Zuletzt verwendet** wird eine Liste mit den Datenressourcen angezeigt, auf die zuletzt zugegriffen wurde. Weitere Informationen zum Zugreifen auf Ressourcen finden Sie unter [Suchen im Azure Purview-Datenkatalog](how-to-search-catalog.md) und [Durchsuchen des Datenkatalogs anhand des Ressourcentyps](how-to-browse-catalog.md#browse-experience).  Die Registerkarte **Meine Elemente** enthält eine Liste mit Datenressourcen, die sich im Besitz des angemeldeten Benutzers befinden.
7. Unter **Nützliche Links** finden Sie Links zu Regionsstatus, Dokumentation, Preise, Übersicht und Purview-Status.
8. Die obere Navigationsleiste enthält Abschnitte mit Informationen zu Versionshinweisen/Updates, Änderung des Purview-Kontos, Benachrichtigungen, Hilfe und Feedback.

## <a name="knowledge-center"></a>Knowledge Center

Im Knowledge Center finden Sie alle Videos und Tutorials zu Purview.

## <a name="guided-tours"></a>Führungen

Auf jeder Benutzeroberfläche in Azure Purview Studio finden Sie Einführungen, mit denen Sie sich einen Überblick über die jeweilige Seite verschaffen können. Wählen Sie zum Starten der Einführung in der oberen Leiste die Option **Hilfe** und dann **Guided Tours** (Führungen) aus.

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="Screenshot: Einführung":::

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen eines Sicherheitsprinzipals](tutorial-scan-data.md)