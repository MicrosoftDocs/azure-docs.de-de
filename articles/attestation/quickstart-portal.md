---
title: 'Schnellstart: Einrichten von Azure Attestation über das Azure-Portal'
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie über das Azure-Portal einen Nachweisanbieter einrichten und konfigurieren.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: a8ab7195958a6c2d5a730c38232eb348d505deda
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593602"
---
# <a name="quickstart-set-up-azure-attestation-by-using-the-azure-portal"></a>Schnellstart: Einrichten von Azure Attestation über das Azure-Portal

Arbeiten Sie diese Schnellstartanleitung als Einstieg in Azure Attestation durch. Es wird beschrieben, wie Sie mit dem Azure-Portal einen Nachweisanbieter, einen Richtliniensignaturgeber und eine Richtlinie verwalten.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="attestation-provider"></a>Nachweisanbieter

In diesem Abschnitt erstellen Sie einen Nachweisanbieter und konfigurieren ihn mit unsignierten oder signierten Richtlinien. Darüber hinaus erfahren Sie, wie Sie den Nachweisanbieter anzeigen und löschen.

### <a name="create-and-configure-the-provider-with-unsigned-policies"></a>Erstellen und Konfigurieren des Anbieters mit unsignierten Richtlinien

1. Navigieren Sie zum Menü im Azure-Portal oder auf die Startseite, und wählen Sie die Option **Ressource erstellen** aus.
1. Geben Sie im Suchfeld **Attestation** ein.
1. Wählen Sie in der Ergebnisliste den Eintrag **Microsoft Azure Attestation** aus.
1. Wählen Sie auf der Seite **Microsoft Azure Attestation** die Option **Erstellen** aus.
1. Geben Sie auf der Seite **Nachweisanbieter erstellen** Folgendes ein:

   - **Abonnement**: Wählen Sie ein Abonnement aus.
   - **Ressourcengruppe**: Wählen Sie eine bereits vorhandene Ressourcengruppe aus, oder wählen Sie die Option **Neu erstellen** aus, und geben Sie einen Namen für die Ressourcengruppe ein.
   - **Name**: Geben Sie einen eindeutigen Namen ein.
   - **Standort**: Wählen Sie einen Standort aus.
   - **Datei für Richtlinien-Signaturgeberzertifikate**: Laden Sie nicht die Datei für Richtlinien-Signaturgeberzertifikate hoch, um den Anbieter mit unsignierten Richtlinien zu konfigurieren.

1. Wählen Sie nach dem Eingeben der erforderlichen Daten die Option **Überprüfen + erstellen** aus.
1. Falls Überprüfungsprobleme angezeigt werden, sollten Sie diese beheben und dann **Erstellen** auswählen.

### <a name="create-and-configure-the-provider-with-signed-policies"></a>Erstellen und Konfigurieren des Anbieters mit signierten Richtlinien

1. Navigieren Sie zum Menü im Azure-Portal oder auf die Startseite, und wählen Sie die Option **Ressource erstellen** aus.
1. Geben Sie im Suchfeld **Attestation** ein.
1. Wählen Sie in der Ergebnisliste den Eintrag **Microsoft Azure Attestation** aus.
1. Wählen Sie auf der Seite **Microsoft Azure Attestation** die Option **Erstellen** aus.
1. Geben Sie auf der Seite **Nachweisanbieter erstellen** die folgenden Informationen an:

   - **Abonnement**: Wählen Sie ein Abonnement aus.
   - **Ressourcengruppe**: Wählen Sie eine bereits vorhandene Ressourcengruppe aus, oder wählen Sie die Option **Neu erstellen** aus, und geben Sie einen Namen für die Ressourcengruppe ein.
   - **Name**: Geben Sie einen eindeutigen Namen ein.
   - **Standort**: Wählen Sie einen Standort aus.
   - **Datei für Richtlinien-Signaturgeberzertifikate**: Laden Sie die Datei für Richtlinien-Signaturgeberzertifikate hoch, um den Nachweisanbieter mit signierten Richtlinien zu konfigurieren. [Sehen Sie sich die Beispiele für Richtliniensignaturgeber-Zertifikate an](./policy-signer-examples.md).

1. Wählen Sie nach dem Eingeben der erforderlichen Daten die Option **Überprüfen + erstellen** aus.
1. Falls Überprüfungsprobleme angezeigt werden, sollten Sie diese beheben und dann **Erstellen** auswählen.

### <a name="view-the-attestation-provider"></a>Anzeigen des Nachweisanbieters

1. Navigieren Sie zum Menü im Azure-Portal oder auf die Startseite, und wählen Sie die Option **Alle Ressourcen** aus.
1. Geben Sie im Filterfeld den Namen des Nachweisanbieters ein, und wählen Sie ihn aus.

### <a name="delete-the-attestation-provider"></a>Löschen des Nachweisanbieters

Es gibt zwei Möglichkeiten, den Nachweisanbieter zu löschen. Ihre Möglichkeiten:

1. Navigieren Sie zum Menü im Azure-Portal oder auf die Startseite, und wählen Sie die Option **Alle Ressourcen** aus.
1. Geben Sie im Filterfeld den Namen des Nachweisanbieters ein.
1. Aktivieren Sie das Kontrollkästchen, und wählen Sie **Löschen** aus.
1. Geben Sie **Ja** ein, und wählen Sie **Löschen** aus.

Alternative:

1. Navigieren Sie zum Menü im Azure-Portal oder auf die Startseite, und wählen Sie die Option **Alle Ressourcen** aus.
1. Geben Sie im Filterfeld den Namen des Nachweisanbieters ein.
1. Wählen Sie den Nachweisanbieter aus, und navigieren Sie zur Übersichtsseite.
1. Wählen Sie in der Menüleiste die Option **Löschen** und dann **Ja** aus.

## <a name="attestation-policy-signers"></a>Nachweisrichtlinien-Signaturgeber

Führen Sie die Schritte in diesem Abschnitt aus, um Richtlinien-Signaturgeberzertifikate anzuzeigen, hinzuzufügen und zu löschen.

### <a name="view-the-policy-signer-certificates"></a>Anzeigen der Richtlinien-Signaturgeberzertifikate

1. Navigieren Sie zum Menü im Azure-Portal oder auf die Startseite, und wählen Sie die Option **Alle Ressourcen** aus.
1. Geben Sie im Filterfeld den Namen des Nachweisanbieters ein.
1. Wählen Sie den Nachweisanbieter aus, und navigieren Sie zur Übersichtsseite.
1. Wählen Sie links im Fenster im Ressourcenmenü oder im unteren Bereich die Option **Richtlinien-Signaturgeberzertifikate** aus.
1. Wählen Sie **Download policy signer certificates** (Richtlinien-Signaturgeberzertifikate herunterladen) aus. Diese Schaltfläche ist für Nachweisanbieter deaktiviert, die ohne obligatorische Richtliniensignatur erstellt wurden.
1. Die heruntergeladene Textdatei enthält alle Zertifikate in einem JWS-Format.
1. Überprüfen Sie die Anzahl von Zertifikaten und die heruntergeladenen Zertifikate.

### <a name="add-the-policy-signer-certificate"></a>Hinzufügen des Richtlinien-Signaturgeberzertifikats

1.  Navigieren Sie zum Menü im Azure-Portal oder auf die Startseite, und wählen Sie die Option **Alle Ressourcen** aus.
1.  Geben Sie im Filterfeld den Namen des Nachweisanbieters ein.
1.  Wählen Sie den Nachweisanbieter aus, und navigieren Sie zur Übersichtsseite.
1.  Wählen Sie links im Fenster im Ressourcenmenü oder im unteren Bereich die Option **Richtlinien-Signaturgeberzertifikate** aus.
1.  Wählen Sie im oberen Menü die Option **Hinzufügen** aus. Diese Schaltfläche ist für Nachweisanbieter deaktiviert, die ohne obligatorische Richtliniensignatur erstellt wurden.
1.  Laden Sie die Datei mit dem Richtlinien-Signaturgeberzertifikat hoch, und wählen Sie **Hinzufügen** aus. [Sehen Sie sich die Beispiele für Richtliniensignaturgeber-Zertifikate an](./policy-signer-examples.md).

### <a name="delete-the-policy-signer-certificates"></a>Löschen der Richtlinien-Signaturgeberzertifikate

1.  Navigieren Sie zum Menü im Azure-Portal oder auf die Startseite, und wählen Sie die Option **Alle Ressourcen** aus.
1.  Geben Sie im Filterfeld den Namen des Nachweisanbieters ein.
1.  Wählen Sie den Nachweisanbieter aus, und navigieren Sie zur Übersichtsseite.
1.  Wählen Sie links im Fenster im Ressourcenmenü oder im unteren Bereich die Option **Richtlinien-Signaturgeberzertifikate** aus.
1.  Wählen Sie im oberen Menü die Option **Löschen** aus. Diese Schaltfläche ist für Nachweisanbieter deaktiviert, die ohne obligatorische Richtliniensignatur erstellt wurden.
1.  Laden Sie die Datei mit dem Richtlinien-Signaturgeberzertifikat hoch, und wählen Sie **Löschen** aus. [Sehen Sie sich die Beispiele für Richtliniensignaturgeber-Zertifikate an](./policy-signer-examples.md). 

## <a name="attestation-policy"></a>Nachweisrichtlinie

In diesem Abschnitt wird beschrieben, wie Sie eine Nachweisrichtlinie anzeigen und Richtlinien konfigurieren, die mit und ohne obligatorische Richtliniensignatur erstellt wurden.

### <a name="view-an-attestation-policy"></a>Anzeigen einer Nachweisrichtlinie

1.  Navigieren Sie zum Menü im Azure-Portal oder auf die Startseite, und wählen Sie die Option **Alle Ressourcen** aus.
1.  Geben Sie im Filterfeld den Namen des Nachweisanbieters ein.
1.  Wählen Sie den Nachweisanbieter aus, und navigieren Sie zur Übersichtsseite.
1.  Wählen Sie links im Fenster im Ressourcenmenü oder im unteren Bereich die Option **Richtlinie** aus.
1.  Wählen Sie den gewünschten **Nachweistyp** aus, und zeigen Sie die **Aktuelle Richtlinie** an.

### <a name="configure-an-attestation-policy"></a>Konfigurieren einer Nachweisrichtlinie

Führen Sie diese Schritte aus, um eine Richtlinie im JWT- oder Textformat hochzuladen, wenn der Nachweisanbieter ohne obligatorische Richtliniensignatur erstellt wurde.

1. Navigieren Sie zum Menü im Azure-Portal oder auf die Startseite, und wählen Sie die Option **Alle Ressourcen** aus.
1. Geben Sie im Filterfeld den Namen des Nachweisanbieters ein.
1. Wählen Sie den Nachweisanbieter aus, und navigieren Sie zur Übersichtsseite.
1. Wählen Sie links im Fenster im Ressourcenmenü oder im unteren Bereich die Option **Richtlinie** aus.
1. Wählen Sie im oberen Menü die Option **Konfigurieren** aus.
1. Wählen Sie für **Richtlinienformat** die Option **JWT** oder **Text** aus.

   Wenn der Nachweisanbieter ohne obligatorische Richtliniensignatur erstellt wurde, kann der Benutzer eine Richtlinie im Format **JWT** oder **Text** hochladen.

      - Laden Sie bei Auswahl des JWT-Formats die Richtliniendatei mit dem Richtlinieninhalt im Format **Unsigniertes/Signiertes JWT** hoch, und wählen Sie **Speichern** aus. [Sehen Sie sich die Beispiele zu den Richtlinien an](./policy-examples.md).
      - Laden Sie bei Auswahl des Textformats die Richtliniendatei mit dem Inhalt im Format **Text** hoch, oder geben Sie den Richtlinieninhalt im Textbereich ein, und wählen Sie **Speichern** aus. [Sehen Sie sich die Beispiele zu den Richtlinien an](./policy-examples.md).

   Bei der Dateiuploadoption wird die Richtlinienvorschau im Textformat angezeigt und kann nicht bearbeitet werden.

1. Wählen Sie im oberen Menü die Option **Aktualisieren** aus, um die konfigurierte Richtlinie anzuzeigen.

Führen Sie diese Schritte aus, um eine Richtlinie im JWT-Format hochzuladen, wenn der Nachweisanbieter mit obligatorischer Richtliniensignatur erstellt wurde.

1.  Navigieren Sie zum Menü im Azure-Portal oder auf die Startseite, und wählen Sie die Option **Alle Ressourcen** aus.
1.  Geben Sie im Filterfeld den Namen des Nachweisanbieters ein.
1.  Wählen Sie den Nachweisanbieter aus, und navigieren Sie zur Übersichtsseite.
1.  Wählen Sie links im Fenster im Ressourcenmenü oder im unteren Bereich die Option **Richtlinie** aus.
1.  Wählen Sie im oberen Menü die Option **Konfigurieren** aus.
1.  Laden Sie die Richtliniendatei im **signierten JWT-Format** hoch, und wählen Sie **Speichern** aus. [Sehen Sie sich die Beispiele zu den Richtlinien an](./policy-examples.md).

    Wenn der Nachweisanbieter mit obligatorischer Richtliniensignatur erstellt wurde, kann der Benutzer eine Richtlinie nur im **signierten JWT-Format** hochladen.

    Bei der Dateiuploadoption wird die Richtlinienvorschau im Textformat angezeigt und kann nicht bearbeitet werden.
    
1.  Wählen Sie die Option **Aktualisieren** aus, um die konfigurierte Richtlinie anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen und Signieren einer Nachweisrichtlinie](author-sign-policy.md)
- [Erbringen eines Nachweises für eine SGX-Enclave mithilfe von Codebeispielen](/samples/browse/?expanded=azure&terms=attestation)

