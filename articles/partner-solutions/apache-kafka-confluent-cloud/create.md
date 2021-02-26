---
title: 'Erstellen von Apache Kafka in Confluent Cloud: Azure-Partnerlösungen'
description: In diesem Artikel wird beschrieben, wie Sie das Azure-Portal verwenden, um eine Instanz von Apache Kafka in Confluent Cloud zu erstellen.
ms.service: partner-services
ms.topic: quickstart
ms.date: 01/15/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f4c6dacf63b1be44e826fe6841c87ccec4bf9b1a
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253661"
---
# <a name="quickstart-get-started-with-apache-kafka-for-confluent-cloud"></a>Schnellstart: Erste Schritte mit Apache Kafka in Confluent Cloud

In dieser Schnellstartanleitung verwenden Sie das Azure-Portal, um eine Instanz von Apache Kafka in Confluent Cloud zu erstellen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto. Falls Sie kein aktives Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen.
- Sie müssen für Ihr Azure-Abonnement über die Rolle _Besitzer_ oder _Mitwirkender_ verfügen. Die Integration zwischen Azure und Confluent kann nur von Benutzern eingerichtet werden, für die Zugriff als _Besitzer_ oder _Mitwirkender_ gewährt wurde. [Vergewissern Sie sich zunächst, dass Sie über die entsprechende Zugriffsberechtigung verfügen](../../role-based-access-control/check-access.md).

## <a name="find-offer"></a>Suchen nach dem Angebot

Verwenden Sie das Azure-Portal, um nach der Anwendung „Apache Kafka in Confluent Cloud“ zu suchen.

1. Navigieren Sie in einem Webbrowser zum [Azure-Portal](https://portal.azure.com/), und melden Sie sich an.

1. Falls Sie während einer der letzten Sitzungen den **Marketplace** besucht haben, können Sie das Symbol aus den verfügbaren Optionen auswählen. Suchen Sie andernfalls nach _Marketplace_.

    :::image type="content" source="media/marketplace.png" alt-text="Marketplace-Symbol":::

1. Auf der Seite **Marketplace** werden je nach gewünschtem Plan zwei Optionen angezeigt. Sie können sich für einen Plan mit nutzungsbasierter Bezahlung oder einen Vertragsplan registrieren. Die nutzungsbasierte Bezahlung ist öffentlich verfügbar. Der Vertragsplan ist für Kunden verfügbar, für die ein privates Angebot genehmigt wurde.

   - Suchen Sie für Kunden mit **nutzungsbasierter Bezahlung** nach _Apache Kafka in Confluent Cloud_. Wählen Sie das Angebot „Apache Kafka in Confluent Cloud“ aus.

     :::image type="content" source="media/search-pay-as-you-go.png" alt-text="Suchen nach dem Azure Marketplace-Angebot":::

   - Wählen Sie für Kunden mit **Vertragsplan** den Link zum **Anzeigen von privaten Angeboten** aus. Beim Vertragsplan müssen Sie sich bei der Registrierung zur Zahlung eines Mindestausgabenbetrags verpflichten. Verwenden Sie diese Option nur, wenn Sie wissen, dass Sie den Dienst längere Zeit benötigen.

     :::image type="content" source="media/view-private-offers.png" alt-text="Anzeigen von privaten Angeboten":::

     Suchen Sie nach _Apache Kafka in Confluent Cloud_.

     :::image type="content" source="media/select-from-private-offers.png" alt-text="Auswählen des privaten Angebots":::

## <a name="create-resource"></a>Erstellen von Ressourcen

Nachdem Sie das Angebot für Apache Kafka in Confluent Cloud ausgewählt haben, können Sie mit der Einrichtung der Anwendung beginnen.

1. Falls Sie im vorherigen Abschnitt die Option für private Angebote ausgewählt haben, stehen zwei Optionen für Plantypen zur Auswahl:

    - Confluent Cloud: Nutzungsbasierte Bezahlung
    - Prepaidtarif: Vertragsplan

   Wenn Sie die Option für private Angebote nicht auswählen, wird nur die Option für nutzungsbasierte Bezahlung angezeigt.

   Wählen Sie den gewünschten Plan und dann die Option **Einrichten und abonnieren** aus.

    :::image type="content" source="media/setup-subscribe.png" alt-text="Einrichten und abonnieren":::

1. Geben Sie auf der Seite **Confluent Cloud-Ressource erstellen** unter „Grundlagen“ die unten aufgeführten Werte an. Wählen Sie anschließend die folgende Option aus: **Weiter: Tags**.

    :::image type="content" source="media/setup-basics.png" alt-text="Formular zum Einrichten der Confluent Cloud-Ressource":::

    | Eigenschaft | Beschreibung |
    | ---- | ---- |
    | **Abonnement** | Wählen Sie im Dropdownmenü das Azure-Abonnement für die Bereitstellung aus. Sie müssen über den Zugriffstyp _Besitzer_ oder _Mitwirkender_ verfügen. |
    | **Ressourcengruppe** | Geben Sie an, ob Sie eine neue Ressourcengruppe erstellen oder eine vorhandene verwenden möchten. Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Azure-Lösung enthält. Weitere Informationen finden Sie in der [Übersicht über den Azure Resource Manager](../../azure-resource-manager/management/overview.md). |
    | **Confluent-Organisationsname** | Name der SaaS-Ressource (Software-as-a-Service). |
    | **Region** | Wählen Sie im Dropdown Menü eine der folgenden Regionen aus: <br/><br/> „Australien, Osten“, „Kanada, Mitte“, „USA, Mitte“, „USA, Osten“, „USA, Osten 2“, „Frankreich, Mitte“, „Europa, Norden“, „Asien, Südosten“, „Vereinigtes Königreich, Süden“, „USA, Westen-Mitte“, „Europa, Westen“, „USA, Westen 2“ |
    | **Planen** | Wählen Sie **Nutzungsbasierte Bezahlung** oder **Prepaidtarif** aus. |
    | **Abrechnungszeitraum** | Wird basierend auf dem ausgewählten Abrechnungsplan vorab ausgefüllt. |
    | **Preis** | Wird basierend auf dem ausgewählten Confluent-Plan vorab ausgefüllt. |

1. Geben Sie unter **Tags** die Paare aus **Name** und **Wert** für Tags an, die Sie auf die Ressource anwenden möchten. Wählen Sie nach dem Eingeben der Tags die Option **Überprüfen und erstellen** aus.

    :::image type="content" source="media/setup-tags.png" alt-text="Hinzufügen von Projekttags":::

1. Überprüfen Sie die von Ihnen angegebenen Einstellungen. Wählen Sie **Erstellen** aus, wenn Sie dazu bereit sind.

1. Die Erstellung der Ressource dauert einige Minuten. Sie können den Bereitstellungsstatus unter **Benachrichtigungen** anzeigen. Wählen Sie nach Abschluss der Bereitstellung die Ressource aus, um die Seite **Übersicht** anzuzeigen.

    :::image type="content" source="media/deployment-status.png" alt-text="Bereitstellungsstatus":::

   Falls Sie eine Fehlermeldung erhalten, helfen Ihnen die Informationen unter [Problembehandlung: Apache Kafka in Confluent Cloud-Lösungen](troubleshoot.md) weiter.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwalten der Confluent Cloud-Ressource](manage.md)
