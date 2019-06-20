---
title: Schritte zur App-Veröffentlichung | Azure Marketplace
description: Schritte zum Veröffentlichen einer Anwendung im Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: pabutler
ms.openlocfilehash: 04278f50366ee91738fd36e64331572e14baf17c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935164"
---
# <a name="app-publishing-steps"></a>Schritte zur App-Veröffentlichung

Um den Veröffentlichungsvorgang zu starten, klicken Sie auf der Registerkarte „Editor“ auf „Veröffentlichen“.

![CPP-Schaltfläche „App veröffentlichen“](./media/d365-financials/image014.jpg)


Auf der Registerkarte „Status“ sehen Sie die Veröffentlichungsschritte, die angeben, in welcher Veröffentlichungsphase sich das Angebot befindet. Während des Veröffentlichungsprozesses können Sie sich auch jederzeit anmelden und auf die Registerkarte „Alle Angebote“ klicken, um den neuesten Status für Ihre Angebote einzusehen. Sie können direkt auf den Status Ihres Angebots klicken, um ausführliche Informationen darüber zu erhalten, wo sich Ihr Angebot im Veröffentlichungsvorgang befindet.

Gehen wir nun die einzelnen Veröffentlichungsschritte durch, um zu besprechen, was während der einzelnen Schritte passiert und wie viel Zeit Sie für jeden Schritt einplanen sollten.

![Abbildung zum Veröffentlichungsvorgang](./media/d365-financials/image017.png)


**Überprüfen der erforderlichen Komponenten**

Wenn Sie auf „Veröffentlichen“ klicken, wird eine automatisierte Prüfung durchgeführt, um sicherzustellen, dass Sie alle erforderlichen Felder in Ihrem Angebot ausgefüllt haben. Wenn eines der Felder nicht ausgefüllt sein sollte, wird neben dem jeweiligen Feld eine Warnung angezeigt. Sie müssen es dann korrekt ausfüllen und erneut auf „Veröffentlichen“ klicken.

Wenn Sie diesen Schritt korrekt ausgeführt haben, wird eine Popupmeldung angezeigt, in der Sie aufgefordert werden, eine E-Mail-Adresse anzugeben. An diese E-Mail-Adresse werden Ihnen dann Benachrichtigungen zur Veröffentlichung gesendet. Wenn Sie Ihre E-Mail-Adresse abgesendet haben, ist dieser Schritt abgeschlossen.


**Automatisierte Anwendungsüberprüfung**

In diesem Schritt prüft unser automatisierter Zertifizierungsdienst, ob der Inhalt von Anwendungserweiterungen, die im Leistungsumfang eines Angebots enthalten sind, den Angebotsmetadaten entspricht. Stellen Sie immer sicher, dass der Name, die Version, der Herausgeber und die ID Ihrer App dem angegebenen Erweiterungsmanifest mit dem Namen `app.json` entspricht.


**Überprüfung der Testversion**

Wenn Sie die Einrichtung der Testversion festgelegt haben, werden in dieser Phase die Einstellungen Ihrer Testversion überprüft.


**Überprüfung der Leadverwaltung und Registrierung**

Wenn Sie das Leadgenerierungsfeature konfiguriert haben, wird in dieser Phase durch Senden eines Testleads an Ihre CRM-Integration zudem überprüft, ob diese funktioniert. Nach Abschluss dieses Schritts wird Ihre CRM- oder Azure-Tabelle mit einem Satz falscher Daten ausgefüllt. Sämtliche Dokumentation zur Leadgenerierung finden Sie hier.


**AppSource-Paketerstellung**

Die Artefakte Ihrer Storefront-Details werden überprüft und das AppSource-Vorschaupaket wird generiert.


**Bestätigung durch den Herausgeber**

In dieser Phase ist die Schaltfläche **Live schalten** jetzt aktiv. Nun wird Ihnen auch ein Link angezeigt, um eine Vorschau von Ihrem Angebot (mit Ihrem HIDeKey) zu erstellen. Wenn Sie mit der Darstellung der Vorschau zufrieden sind, klicken Sie auf die Schaltfläche „Live schalten“.
Denken Sie daran, dass diese Anforderung Ihre App nicht in AppSource live schaltet, jedoch unseren internen Überprüfungsprozess auslöst.


**Überprüfung von Marketing- und technischen Anwendungen**

In diesem Schritt führen wir parallel die Überprüfungen von Marketing- und technischen Anwendungen durch. Die obligatorischen Anforderungen und Empfehlungen finden Sie in der [Prüfliste für die Übermittlung der App](https://aka.ms/CheckBeforeYouSubmit) und im Whitepaper [Developing Apps for Dynamics 365 for Finance and Operations](https://go.microsoft.com/fwlink/?linkid=841518) (Entwickeln von Apps für Dynamics 365 Finance and Operations). Der Überprüfungsprozess umfasst Folgendes:
-  Klärung von ausstehenden Fragen und Problemen in Zusammenarbeit mit Ihnen  
- Angabe eines Datums für die App-Veröffentlichung und Benachrichtigung Ihrerseits, sobald Ihre App veröffentlicht ist 
- Bereitstellung eines ersten Feedbacks bezüglich der Überprüfung der technischen und Marketinganwendungen innerhalb von 5 bis 7 Werktagen

Diese Schritte können in der Regel über eine Woche dauern, und Sie müssen nicht durchgehend beim Cloud-Partnerportal angemeldet bleiben.


**Veröffentlichen der Anwendung mit dem Dienst**

Ihr Angebot durchläuft einige abschließende Verarbeitungsschritte. Ihre App hat die Überprüfung der Marketing- und technischen Anwendungen bestanden, muss jedoch einige abschließende Verarbeitungsschritte durchlaufen, damit sie in AppSource veröffentlicht werden kann.


**Live**

Ihr Angebot ist jetzt in AppSource live geschaltet, und Kunden können Ihre App in ihren Microsoft Dynamics 365 Business Central-Abonnements einsehen und bereitstellen. Sie erhalten eine E-Mail von uns, in der Sie darüber benachrichtigt werden, dass Ihre App in AppSource veröffentlicht wurde. Sie können zu einem beliebigen Zeitpunkt auf die Registerkarte „Alle Angebote“ klicken und den in der rechten Spalte aufgeführten Status für all Ihre Angebote einsehen. Um ausführliche Informationen zum Status des Veröffentlichungsvorgangs für Ihr Angebot zu sehen, können Sie auf den Status klicken.


<a name="error-handling"></a>Fehlerbehandlung
--------------

Während des Veröffentlichungsvorgangs können eventuell Fehler auftreten. Wenn ein Fehler festgestellt wird, erhalten Sie eine E-Mail-Benachrichtigung, die Sie darüber in Kenntnis setzt, dass bei den Anweisungen zu den nächsten Schritten ein Fehler aufgetreten ist. Fehler können auch durch Klicken auf die Registerkarte „Status“ jederzeit während dieses Vorgangs angezeigt werden. Es wird angezeigt, an welcher Stelle im Vorgang der Fehler aufgetreten ist, sowie die Fehlermeldung, die angibt, welche Fehler zu beheben sind.

Wenn während des Veröffentlichungsvorgangs Fehler auftreten, müssen Sie diese beheben und anschließend auf **Veröffentlichen** klicken, um den Vorgang zu wiederholen. Nach jeder Fehlerbehebungsmaßnahme müssen Sie bei der erneuten Veröffentlichung am Anfang der Veröffentlichungsschritte bei der **Überprüfung der Voraussetzungen** beginnen.

Wenn Sie Probleme bei der Behebung eines Fehlers haben, sollten Sie eine Supportanfrage öffnen, um Unterstützung von unseren Supportmitarbeitern zu erhalten.


<a name="canceling-the-publishing-request"></a>Abbrechen von Veröffentlichungsanforderungen
--------------------------------

Es kann vorkommen, dass Sie einen Veröffentlichungsvorgang in die Wege leiten und Ihre Anforderung abbrechen müssen. Sie können eine Veröffentlichungsanforderung erst dann abbrechen, wenn die Veröffentlichungsanforderung den Schritt zum Einholen der Genehmigung des Herausgebers erreicht hat. Klicken Sie zum Abbrechen auf **Veröffentlichen abbrechen**. Der Veröffentlichungsstatus wird auf Schritt 1 zurückgesetzt. Um die Veröffentlichung zu wiederholen, sollten Sie auf „Veröffentlichen“ klicken und die Schritte im Status befolgen.

![Schaltfläche „Veröffentlichen abbrechen“](./media/d365-financials/image013.png)
