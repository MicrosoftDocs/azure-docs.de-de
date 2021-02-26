---
title: Definieren der globalen Benutzerzugriffssteuerung
description: In großen Unternehmen können Benutzerberechtigungen komplex sein und zusätzlich zur Standardstruktur von Websites und Zonen auch durch eine globale Organisationsstruktur bestimmt werden.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/08/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 81ede3506337e76c84e20203ac27a3d441fdcef8
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522273"
---
# <a name="define-global-access-control"></a>Definieren der globalen Zugriffssteuerung

In großen Unternehmen können Benutzerberechtigungen komplex sein und zusätzlich zur Standardstruktur von Websites und Zonen auch durch eine globale Organisationsstruktur bestimmt werden.

Um den Bedarf an globalen und komplexeren Zugriffsberechtigungen für Benutzer zu erfüllen, können Sie eine globale Geschäftstopologie erstellen, die auf Geschäftsbereichen, Regionen und Standorten basiert. Anschließend können Sie für Benutzer Zugriffsberechtigungen für diese Entitäten definieren.

Das Arbeiten mit Zugriffstools für die Geschäftstopologie hilft Unternehmen bei der Umsetzung von Zero-Trust-Strategien, indem besser kontrolliert wird, wo Benutzer Geräte auf der Azure Defender für IoT-Plattform verwalten und analysieren.

## <a name="about-access-groups"></a>Informationen zu Zugriffsgruppen

Die globale Zugriffssteuerung wird durch Erstellung von Zugriffsgruppen für Benutzer eingerichtet. Zugriffsgruppen bestehen aus Regeln, die festlegen, welche Benutzer auf bestimmte Geschäftsentitäten zugreifen dürfen. Mithilfe von Gruppen können Sie den Ansichts- und Konfigurationszugriff auf Defender für IoT für bestimmte Benutzerrollen für relevante Geschäftsbereiche, Regionen und Standorte steuern.

Sie können z. B. Sicherheitsanalysten in einer Active Directory-Gruppe den Zugriff auf alle westeuropäischen Automobil- und Glasfertigungslinien sowie auf eine Kunststofflinie in einer bestimmten Region erlauben.

:::image type="content" source="media/how-to-define-global-user-access-control/sa-diagram.png" alt-text="Diagramm der Active Directory-Gruppe für Sicherheitsanalysten.":::

Bevor Sie Zugriffsgruppen erstellen, empfehlen wir die folgenden Schritte:

- Richten Sie Ihre Geschäftstopologie sorgfältig ein. Weitere Informationen zur Geschäftstopologie finden Sie unter [Arbeiten mit Siteübersichtansichten](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views).

- Planen Sie, welche Benutzer zu den von Ihnen erstellten Zugriffsgruppen gehören sollen. Für das Zuweisen von Benutzern zu Zugriffsgruppen gibt es zwei Optionen:

  - **Zuweisen von Gruppen mithilfe von Active Directory-Gruppen**: Vergewissern Sie sich, dass Sie eine Active Directory-Instanz für Integration in die lokale Verwaltungskonsole eingerichtet haben.
  
  - **Zuweisen lokaler Benutzer**: Überprüfen Sie, ob Sie Benutzer erstellt haben. Weitere Informationen finden Sie unter [Definieren von Benutzern](how-to-create-and-manage-users.md#define-users).

Administratorbenutzer können Zugriffsgruppen nicht zugewiesen werden. Diese Benutzer haben standardmäßig Zugriff auf alle Entitäten in Geschäftstopologien.

## <a name="create-access-groups"></a>Erstellen von Zugriffsgruppen

In diesem Abschnitt wird das Erstellen von Zugriffsgruppen beschrieben. Für die erste Gruppe, die Sie erstellen, werden globale Standardgeschäftsbereiche und -regionen erstellt. Sie können die Standardentitäten bearbeiten, wenn Sie die erste Gruppe definieren.

So erstellen Sie Gruppen

1. Wählen Sie im Menü neben der lokalen Verwaltungskonsole **Zugriffsgruppen** aus.

2. Wählen Sie :::image type="icon" source="media/how-to-define-global-user-access-control/add-icon.png" border="false"::: aus. Geben Sie im Dialogfeld **Zugriffsgruppe hinzufügen** einen Namen für die Zugriffsgruppe ein. Die Konsole unterstützt 64 Zeichen. Weisen Sie den Namen so zu, dass Sie diese Gruppe problemlos von anderen Gruppen unterscheiden können.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="Das Dialogfeld „Zugriffsgruppe hinzufügen“, in dem Sie Zugriffsgruppen erstellen.":::

3. Wenn die Option **Eine Active Directory-Gruppe zuweisen** angezeigt wird, können Sie dieser Zugriffsgruppe eine Active Directory-Benutzergruppe zuweisen.

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="„Zuweisen einer Active Directory-Gruppe“ im Dialogfeld „Zugriffsgruppe erstellen“.":::

   Wenn die Option nicht angezeigt wird und Sie Active Directory-Gruppen in Zugriffsgruppen einschließen möchten, wählen Sie **Systemeinstellungen** aus. Definieren Sie die Gruppen im Bereich **Integrationen**. Geben Sie einen Gruppennamen exakt so ein, wie er in den Active Directory-Konfigurationen angegeben ist, und zwar in Kleinbuchstaben.

5. Weisen Sie der Gruppe im Bereich **Benutzer** beliebig viele Benutzer zu. Sie können Benutzer auch verschiedenen Gruppen zuweisen. Wenn Sie so vorgehen, müssen Sie die Zugriffsgruppe und die Regeln erstellen und speichern und dann die Benutzer im Bereich **Benutzer** der Gruppe zuweisen.

   :::image type="content" source="media/how-to-define-global-user-access-control/role-management.png" alt-text="Verwalten Sie die Rollen Ihrer Benutzer, und weisen Sie sie nach Bedarf zu.":::

6. Erstellen Sie im Dialogfeld **Regeln hinzufügen für *Name*** Regeln auf Grundlage der Zugriffsanforderungen Ihrer Geschäftstopologie. Hier gezeigte Optionen basieren auf der Topologie, die Sie in den Fenstern **Unternehmensansicht** und **Standortverwaltung** entworfen haben. 

   Sie können pro Gruppe mehrere Regeln erstellen. Sie müssen möglicherweise mehr als eine Regel pro Gruppe erstellen, wenn Sie an mehreren Standorten mit komplexer Zugriffseingrenzung arbeiten. 

   :::image type="content" source="media/how-to-define-global-user-access-control/add-rule.png" alt-text="Hinzufügen einer Regel zu Ihrem System.":::

Die von Ihnen erstellten Regeln werden im Dialogfeld **Zugriffsgruppe hinzufügen** angezeigt. Hier können Sie sie löschen oder bearbeiten.

:::image type="content" source="media/how-to-define-global-user-access-control/edit-access-groups.png" alt-text="Sie können alle Zugriffsgruppen in diesem Fenster anzeigen und bearbeiten.":::

### <a name="about-rules"></a>Informationen zu Regeln

Beachten Sie beim Erstellen von Regeln Folgendes:

- Wenn eine Zugriffsgruppe mehrere Regeln enthält, aggregiert die Regellogik alle Regeln. Beispielsweise arbeiten die Regeln mit UND-Logik und nicht mit ODER-Logik.

- Damit eine Regel erfolgreich angewendet werden kann, müssen Sie den Zonen im Fenster **Standortverwaltung** Sensoren zuweisen.

- Sie können nur ein Element pro Regel zuweisen. Sie können z. B. einen Geschäftsbereich, eine Region und einen Standort für jede Regel zuweisen. Definieren Sie weitere Regeln für die Gruppe, wenn Sie z. B. möchten, dass Benutzer in einer Active Directory-Gruppe Zugriff auf verschiedene Geschäftsbereiche in unterschiedlichen Regionen haben.

- Wenn Sie eine Entität ändern und die Änderung die Regellogik beeinflusst, wird die Regel gelöscht. Wenn sich Änderungen an einer Entität der Topologie auf die Regellogik so auswirken, dass alle Regeln gelöscht werden, bleibt die Zugriffsgruppe zwar erhalten, aber die Benutzer können sich nicht bei der lokalen Verwaltungskonsole anmelden. Benutzer werden aufgefordert, ihren Administrator zu kontaktieren, um sich anzumelden.

- Wenn kein Geschäftsbereich oder keine Region ausgewählt ist, haben die Benutzer Zugriff auf alle definierten Geschäftsbereiche und Regionen.

## <a name="next-steps"></a>Nächste Schritte

[Grundlegendes zu Benutzern der Defender für IoT-Konsole](how-to-create-and-manage-users.md)
