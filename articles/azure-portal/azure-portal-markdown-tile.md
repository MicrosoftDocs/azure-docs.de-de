---
title: Verwenden einer benutzerdefinierten Markdown-Kachel in Azure-Dashboards
description: Erfahren Sie, wie Sie einem Azure-Dashboard eine Markdown-Kachel zum Anzeigen von statischen Inhalten hinzufügen.
ms.date: 01/08/2020
ms.topic: how-to
ms.custom: devx-track-js
ms.openlocfilehash: 5121142ecf568aa1ac9a7ec19f7211c6f9a6253f
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745756"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Verwenden einer Markdown-Kachel in Azure-Dashboards zum Anzeigen von benutzerdefinierten Inhalten

Sie können in Ihren Azure-Dashboards eine Markdown-Kachel hinzufügen, um benutzerdefinierte statische Inhalte anzuzeigen. Beispielsweise können Sie auf einer Markdown-Kachel allgemeine Anweisungen, ein Bild oder eine Gruppe von Links anzeigen.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Hinzufügen einer Markdown-Kachel in Ihrem Dashboard

1. Wählen Sie auf der Seitenleiste des Azure-Portals die Option **Dashboard** aus.

   ![Screenshot: Randleiste des Portals](./media/azure-portal-markdown-tile/azure-portal-nav.png)

1. Wenn Sie benutzerdefinierte Dashboards erstellt haben, wählen Sie in der Dashboardansicht in der Dropdownliste das Dashboard aus, in dem die benutzerdefinierte Markdown-Kachel angezeigt werden soll. Wählen Sie das Bearbeitungssymbol aus, um den **Kachelkatalog** zu öffnen.

   ![Screenshot: Bearbeitungsansicht des Dashboards](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

1. Suchen Sie im **Kachelkatalog** die Kachel mit der Bezeichnung **Markdown**, und wählen Sie **Hinzufügen** aus. Die Kachel wird dem Dashboard hinzugefügt, und der Bereich **Markdown bearbeiten** wird geöffnet.

1. Geben Sie Werte für **Titel** und **Untertitel** ein, die auf der Kachel angezeigt werden, nachdem Sie in ein anderes Feld gewechselt sind.

   ![Screenshot: Ergebnisse der Eingabe von Titel und Untertitel](./media/azure-portal-markdown-tile/azure-portal-dashboard-enter-title.png)

1. Wählen Sie eine der Optionen zum Einschließen von Markdowninhalt aus: **Inlinebearbeitung** oder **Inhalt über URL einfügen**.

   - Wählen Sie **Inlinebearbeitung**  aus, wenn Sie Markdown direkt eingeben möchten.

      ![Screenshot: Eingabe von Inlineinhalten](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-inline-content.png)

   - Wählen Sie **Inhalt über URL einfügen** aus, wenn Sie vorhandene Markdowninhalte verwenden möchten, die online gehostet werden.

      ![Screenshot: Eingabe einer URL](./media/azure-portal-markdown-tile/azure-portal-dashboard-markdown-url.png)

      > [!NOTE]
      > Um zusätzliche Sicherheit zu erreichen, können Sie eine Markdowndatei erstellen und in einem [Azure-Speicherkontoblob speichern, in dem Verschlüsselung aktiviert ist](../storage/common/storage-service-encryption.md), und dann mithilfe der URL-Option auf die Datei verweisen. Der Markdowninhalt wird mithilfe der Verschlüsselungsoptionen des Speicherkontos verschlüsselt. Nur Benutzer mit Berechtigungen für die Datei können den Markdowninhalt auf dem Dashboard anzeigen. Möglicherweise müssen Sie eine [Regel für die Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS)](/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) für das Speicherkonto festlegen, damit das Azure-Portal ( _https://portal.azure.com/_ ) auf die Markdowndatei im Blob zugreifen kann.

1. Wählen Sie **Fertig** aus, um den Bereich **Markdown bearbeiten** zu schließen. Der Inhalt wird auf der Markdown-Kachel angezeigt, deren Größe dann durch Ziehen des Ziehpunkts in der rechten unteren Ecke geändert werden kann.

   ![Screenshot: benutzerdefinierte Markdown-Kachel](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Funktionen und Einschränkungen von Markdown-Inhalten

Auf der Markdown-Kachel können Sie eine beliebige Kombination von Nur-Text, Markdown-Syntax und HTML-Inhalten verwenden. Im Azure-Portal werden Ihre Inhalte mithilfe der Open-Source-Bibliothek _marked_ in HTML-Code transformiert, der auf der Kachel angezeigt wird. Der über _marked_ generierte HTML-Code wird im Portal vor dem Rendern vorab verarbeitet. Mit diesem Schritt wird sichergestellt, dass die Sicherheit und das Layout des Portals durch Ihre Anpassung nicht beeinträchtigt werden. Bei dieser Vorverarbeitung werden Teile des HTML-Codes entfernt, die eine potenzielle Bedrohung darstellen. Folgende Inhaltstypen sind im Portal nicht zulässig:

* JavaScript: `<script>`-Tags und JavaScript-Inline-Auswertungen werden entfernt.
* iFrames: `<iframe>`-Tags werden entfernt.
* style: `<style>`-Tags werden entfernt. Attribute von Inlineformatvorlagen in HTML-Elementen werden offiziell nicht unterstützt. Möglicherweise können Sie einige Inlineelemente von Formatvorlagen verwenden, wenn diese jedoch das Layout des Portals beeinträchtigen, können sie jederzeit nicht mehr funktionieren. Die Markdown-Kachel ist für allgemeine statische Inhalte vorgesehen, für die die Standardformatvorlagen des Portals verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Erstellen eines benutzerdefinierten Dashboards finden Sie unter [Erstellen und Freigeben von Dashboards im Azure-Portal](../azure-portal/azure-portal-dashboards.md).
