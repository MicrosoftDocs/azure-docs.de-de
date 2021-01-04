---
title: Regeln konfigurieren und Warnungen verwalten
description: Hier erfahren Sie, wie Sie in FarmBeats Regeln konfigurieren und Warnungen verwalten.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 5e6a5d414c341f482c3fddf95a2f8bb8e55a3ca2
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168544"
---
# <a name="configure-rules-and-manage-alerts"></a>Regeln konfigurieren und Warnungen verwalten

Azure FarmBeats ermöglicht nicht nur die Erstellung von Regeln auf der Grundlage der Daten, die von den Sensoren und Geräten Ihres landwirtschaftlichen Betriebs eingehen, sondern auch auf der Grundlage der Geschäftslogik. Durch die Regeln werden Warnungen im System ausgelöst, wenn Sensorwerte einen Schwellenwert überschreiten. Anhand der Warnungen, die nach der Überschreitung von Schwellenwerten erstellt werden, können Sie schnell auf Probleme reagieren und für Abhilfe sorgen.

## <a name="create-rule"></a>Erstellen einer Regel

1. Navigieren Sie auf der Startseite zu **Regeln** .
2. Wählen Sie **Neue Regel** aus. Das Fenster „Neue Regel“ wird angezeigt.

    ![Der Screenshot, der die Schaltfläche „Neue Regel“ und den Abschnitt „Neue Regel“ hervorhebt.](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-1.png)

3. Geben Sie unter **Regelname** und **Regelbeschreibung** einen Regelnamen bzw. eine Regelbeschreibung ein, und wählen Sie anschließend im Dropdownmenü **Select Farm** (Landwirtschaftlichen Betrieb auswählen) einen landwirtschaftlichen Betrieb aus.
4. Geben Sie zum Auswählen des landwirtschaftlichen Betriebs den Namen Ihres landwirtschaftlichen Betriebs ein. Daraufhin wird im gleichen Fenster der Abschnitt **Bedingungen** angezeigt.  

    ![Screenshot, in dem der Abschnitt „Bedingungen“ hervorgehoben ist](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-condition-1.png)

5. Geben Sie unter **Bedingungen** Werte für **Measure** , **Operator** und **Wert** ein.
6. Geben Sie im Dropdownmenü **Measure** den Measurenamen ein.
7. Wählen Sie **+Bedingung hinzufügen** aus, um der Regel weitere Bedingungen hinzuzufügen.
8. Wählen Sie unter **Schweregrad** den Schweregrad aus.
9. Aktivieren Sie unter **Aktion** die Umschaltfläche **Email enabled** (E-Mail aktiviert), um E-Mail-Warnungen zu aktivieren.

    ![Der Screenshot zeigt die Option „Email enabled“ (E-Mail aktiviert).](./media/configure-rules-and-alerts-in-azure-farmbeats/new-rule-email-1.png)

10. Geben Sie unter **E-Mail-Adressen** die E-Mail-Adressen an, an die die E-Mail-Warnung gesendet werden soll, und geben Sie unter **E-Mail-Betreff** und **Weitere Hinweise** entsprechende Informationen an.  
11. Legen Sie unter **Regelstatus** die Umschaltfläche zum Aktivieren oder Deaktivieren der Regel auf **Aktiviert** fest.
    Sie können die Anzahl von Geräten anzeigen, die von der Regel betroffen sind.
12. Wählen Sie **Anwenden** aus, um die Regel zu erstellen.

## <a name="view-rule"></a>Anzeigen der Regel

Auf der Seite **Farm** (Landwirtschaftlicher Betrieb) wird die Liste der verfügbaren Regeln angezeigt. Wählen Sie einen **Regelnamen** aus. Daraufhin wird ein Fenster mit folgenden Details für die ausgewählte Regel angezeigt:
 - Regelname
 - Link zu dem landwirtschaftlichen Betrieb, dem die Regel zugeordnet ist
 - Erstellungsdatum
 - Datum der letzten Aktualisierung
 - Schweregrad
 - Regelstatus
 - Bedingungsliste  
 - Anzahl von Geräten, die von der Regel betroffen sind

    ![Der Screenshot zeigt den Bildschirm zu den Regeldetails.](./media/configure-rules-and-alerts-in-azure-farmbeats/view-rule-1.png)

## <a name="edit-rule"></a>Bearbeiten einer Regel

Gehen Sie zum Bearbeiten einer Regel wie folgt vor:

1. Wählen Sie auf der Startseite im linken Navigationsmenü die Option **Regeln** aus.
   Das Regelfenster wird angezeigt.
2. Wählen Sie die Regel aus, die Sie bearbeiten möchten.

    ![Der Screenshot zeigt die ausgewählte Regel.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-action-bar-1.png)

3. Wählen Sie auf der Aktionsleiste die Option **Bearbeiten** aus. Daraufhin wird das Fenster **Regel bearbeiten** angezeigt.

    ![Der Screenshot zeigt den Bildschirm „Regel bearbeiten“.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-one-1.png)

4. Ändern Sie die Angaben unter **Regelname** und **Regelbeschreibung** , und wählen Sie anschließend im Dropdownmenü **Select Farm** (Landwirtschaftlichen Betrieb auswählen) einen landwirtschaftlichen Betrieb aus.
5. Geben Sie zum Auswählen des landwirtschaftlichen Betriebs den Namen Ihres landwirtschaftlichen Betriebs ein. Daraufhin wird im gleichen Fenster **Bedingungen** angezeigt.  
6. Bearbeiten Sie unter **Bedingungen** die Angaben für **Measure** , **Operator** und **Wert** .
7. Geben Sie im Dropdownmenü **Measure** den Measurenamen ein.
8. Wählen Sie **+Bedingung hinzufügen** aus, um Bedingungen für die Regeln hinzuzufügen/zu bearbeiten.

    ![Screenshot, der die Schaltfläche „Bedingung hinzufügen“ hervorhebt.](./media/configure-rules-and-alerts-in-azure-farmbeats/edit-rule-two-1.png)

9.  Wählen Sie unter **Schweregrad** den Schweregrad aus.  
10. Aktivieren Sie unter **Aktion** die Umschaltfläche **Email enabled** (E-Mail aktiviert), um E-Mail-Warnungen zu aktivieren.
11. Bearbeiten Sie unter **E-Mail-Adressen** die E-Mail-Adressen, an die die E-Mail-Warnung gesendet werden soll, und bearbeiten Sie auch die Angaben unter **E-Mail-Betreff** und **Weitere Hinweise** .  
12. Legen Sie unter **Regelstatus** die Umschaltfläche zum Aktivieren oder Deaktivieren der Regel auf **Aktiviert** fest.
Sie können die Anzahl von Geräten anzeigen, die von dieser Regel betroffen sind.
13. Wählen Sie **Anwenden** aus, um die Regel zu bearbeiten.

## <a name="change-rule-status"></a>Ändern des Regelstatus

Der Status einer Regel kann wie folgt geändert werden:

1. Wählen Sie auf der Startseite im linken Navigationsmenü die Option **Regeln** aus. Das Regelfenster wird angezeigt.
2. Wählen Sie die Regel aus, deren Status Sie ändern möchten.

    ![Der Screenshot zeigt die Schaltfläche „Status ändern“.](./media/configure-rules-and-alerts-in-azure-farmbeats/change-status-rule-action-bar-1.png)

3. Wählen Sie auf der Aktionsleiste die Option **Status ändern** aus. Das Fenster **Status ändern** wird angezeigt.

    ![Der Screenshot zeigt die Schaltfläche „Status ändern“.](./media/configure-rules-and-alerts-in-azure-farmbeats/rule-change-status-1.png)

3. Ändern Sie den Regelstatus mithilfe der Umschaltfläche **Status ändern** .
   Sie können die Anzahl von Geräten anzeigen, die von der Regel betroffen sind.
4. Wählen Sie **Anwenden** aus, um den Regelstatus zu ändern.

## <a name="delete-rule"></a>Regel löschen

Eine Regel kann wie folgt gelöscht werden:

1. Wählen Sie auf der Startseite im linken Navigationsmenü die Option **Regeln** aus. Das Regelfenster wird angezeigt.
2. Wählen Sie die Regel aus, die Sie löschen möchten.

    ![Screenshot, der die Schaltfläche „Löschen“ hervorhebt.](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-action-bar-1.png)

3. Wählen Sie auf der Aktionsleiste die Option **Löschen** aus.

    ![FarmBeats-Projekt](./media/configure-rules-and-alerts-in-azure-farmbeats/delete-rule-1.png)

4. Das Dialogfeld **Regel löschen** wird angezeigt. Klicken Sie auf **Löschen** .
