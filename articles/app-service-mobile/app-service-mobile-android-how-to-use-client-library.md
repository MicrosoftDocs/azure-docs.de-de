---
title: 'Gewusst wie: Verwenden des Azure Mobile Apps SDK für Android | Microsoft-Dokumentation'
description: 'Gewusst wie: Verwenden des Azure Mobile Apps SDK für Android'
services: app-service\mobile
documentationcenter: android
author: conceptdev
manager: crdun
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 03/07/2019
ms.author: crdun
ms.openlocfilehash: 45b5ac0c9b3535e5cc5efdc6827d694b41e0b8dd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60859391"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Gewusst wie: Verwenden des Azure Mobile Apps SDK für Android

Diese Anleitung beschreibt die Verwendung des Android-Client-SDK für Mobile Apps, um häufige Szenarien, wie z.B. die Folgenden zu implementieren:

* Abfragen von Daten (Einfügen, Aktualisieren und Löschen)
* Authentifizierung
* Behandeln von Fehlern
* Anpassen des Clients

Dieser Leitfaden konzentriert sich auf die Clientseite des Android-SDK.  Weitere Informationen zu den serverseitigen SDKs für mobile Apps finden Sie unter [Arbeiten mit dem .NET-Back-End-SDK][10] oder [Verwenden des Node.js-Back-End-SDKs][11].

## <a name="reference-documentation"></a>Referenzdokumentation

Sie finden die [Javadocs-API-Referenz][12] für die Android-Clientbibliothek auf GitHub.

## <a name="supported-platforms"></a>Unterstützte Plattformen

Das Azure Mobile Apps SDK für Android unterstützt für Smartphone- und Tablet-Formfaktoren die API-Ebenen 19 bis 24 (KitKat bis Nougat).  Zur Authentifizierung wird insbesondere ein allgemeiner Webframework-Ansatz zum Erfassen von Anmeldeinformationen befolgt.  Die Serverflussauthentifizierung funktioniert nicht für Geräte mit kleinem Formfaktor, wie z.B. Armbanduhren.

## <a name="setup-and-prerequisites"></a>Einrichtung und Voraussetzungen

Schließen Sie das Tutorial zum [Schnellstart für Mobile Apps](app-service-mobile-android-get-started.md) ab.  Diese Aufgabe stellt sicher, dass alle Voraussetzungen für die Entwicklung von Azure Mobile Apps erfüllt sind.  Das Schnellstarttutorial hilft Ihnen auch dabei, Ihr Konto zu konfigurieren und Ihre erste mobile App zu erstellen.

Wenn Sie das Schnellstarttutorial nicht absolvieren möchten, führen Sie folgende Aufgaben durch:

* [Erstellen Sie ein Mobile App-Back-End][13] zur Verwendung mit Ihrer Android-App.
* [Aktualisieren Sie die Gradle-Builddateien](#gradle-build)in Android Studio.
* [Aktivieren Sie die Internetberechtigung](#enable-internet).

### <a name="gradle-build"></a>Aktualisieren der Gradle-Builddatei

Ändern Sie beide **build.gradle** -Dateien:

1. Fügen Sie diesen Code in die *Project*-Ebene der **build.gradle**-Datei ein:

    ```gradle
    buildscript {
        repositories {
            jcenter()
            google()
        }
    }

    allprojects {
        repositories {
            jcenter()
            google()
        }
    }
    ```

2. Fügen Sie diesen Code in die *Module app*-Ebene der **build.gradle**-Datei im *dependencies*-Tag ein:

    ```gradle
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    Derzeit ist 3.4.0 die neueste Version. Die unterstützten Versionen sind auf [bintray][14] aufgeführt.

### <a name="enable-internet"></a>Aktivieren der Internetberechtigung

Für den Zugriff auf Azure muss für Ihre App die INTERNET-Berechtigung aktiviert sein. Wenn sie nicht bereits aktiviert ist, fügen Sie Ihrer Datei **AndroidManifest.xml** die folgende Codezeile hinzu:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Erstellen einer Clientverbindung

Azure Mobile Apps bietet vier Funktionen für Ihre mobile Anwendung:

* Datenzugriff und Offlinesynchronisierung mit einem Azure Mobile Apps-Dienst
* Aufrufen benutzerdefinierter APIs, die mit dem Azure Mobile Apps Server SDK geschrieben wurden
* Authentifizierung mithilfe der Authentifizierungs- und Autorisierungsfunktionen von Azure App Service
* Registrierung von Pushbenachrichtigungen mit Notification Hubs

Jede dieser Funktionen erfordert, dass Sie zunächst ein `MobileServiceClient`-Objekt erstellen.  Es darf nur ein `MobileServiceClient`-Objekt in Ihrem mobilen Client erstellt werden (d.h., es muss ein Singleton-Muster vorliegen).  So erstellen Sie ein `MobileServiceClient`-Objekt

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

`<MobileAppUrl>` ist eine Zeichenfolge oder ein URL-Objekt, die/das auf Ihr mobiles Back-End verweist.  Wenn Sie Azure App Service zum Hosten Ihres mobilen Back-Ends verwenden, müssen Sie sicherstellen, dass Sie die sichere `https://`-Version der URL verwenden.

Der Client benötigt außerdem Zugriff auf die Aktivität oder den Kontext, d.h. bei diesem Beispiel auf den Parameter `this`.  Die „MobileServiceClient“-Konstruktion muss in der `onCreate()`-Methode der Aktivität erfolgen, auf die in der Datei `AndroidManifest.xml` verwiesen wird.

Es wird empfohlen, die Serverkommunikation in einer eigenen Klasse (mit Singleton-Muster) zu abstrahieren.  In diesem Fall müssen Sie die Aktivität innerhalb des Konstruktors übergeben, um den Dienst entsprechend zu konfigurieren.  Beispiel:

```java
package com.example.appname.services;

import android.content.Context;
import com.microsoft.windowsazure.mobileservices.*;

public class AzureServiceAdapter {
    private String mMobileBackendUrl = "https://myappname.azurewebsites.net";
    private Context mContext;
    private MobileServiceClient mClient;
    private static AzureServiceAdapter mInstance = null;

    private AzureServiceAdapter(Context context) {
        mContext = context;
        mClient = new MobileServiceClient(mMobileBackendUrl, mContext);
    }

    public static void Initialize(Context context) {
        if (mInstance == null) {
            mInstance = new AzureServiceAdapter(context);
        } else {
            throw new IllegalStateException("AzureServiceAdapter is already initialized");
        }
    }

    public static AzureServiceAdapter getInstance() {
        if (mInstance == null) {
            throw new IllegalStateException("AzureServiceAdapter is not initialized");
        }
        return mInstance;
    }

    public MobileServiceClient getClient() {
        return mClient;
    }

    // Place any public methods that operate on mClient here.
}
```

Sie können nun `AzureServiceAdapter.Initialize(this);` in der `onCreate()`-Methode Ihrer Hauptaktivität aufrufen.  Alle anderen Methoden, die Zugriff auf den Client benötigen, verwenden `AzureServiceAdapter.getInstance();`, um einen Verweis auf den Dienstadapter zu erhalten.

## <a name="data-operations"></a>Datenvorgänge

Hauptaufgabe des Azure Mobile Apps SDK ist das Bereitstellen des Zugriffs auf Daten, die in SQL Azure im Mobile App-Back-End gespeichert sind.  Sie können auf diese Daten mithilfe stark typisierter Klassen (bevorzugt) oder nicht typisierter Abfragen (nicht empfohlen) zugreifen.  Der Großteil dieses Abschnitts befasst sich mit dem Verwenden stark typisierter Klassen.

### <a name="define-client-data-classes"></a>Definieren von Clientdatenklassen

Für den Zugriff auf Daten aus SQL Azure-Tabellen definieren Sie Clientdatenklassen, die den Tabellen im Mobile App-Back-End entsprechen. Für die Beispiele in diesem Thema wird eine Tabelle mit dem Namen **MyDataTable** verwendet, die folgende Spalten hat:

* id
* text
* complete

Das entsprechende typisierte clientseitige Objekt befindet sich in einer Datei namens **MyDataTable.java**:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Fügen Sie Getter- und Setter-Methoden für jedes Feld hinzu, das Sie hinzufügen.  Wenn Ihre SQL Azure-Tabelle weitere Spalten enthält, würden Sie die entsprechenden Felder dieser Klasse hinzufügen.  Wenn das Datenübertragungsobjekt (Data Transfer Object, DTO) z.B. eine Integerspalte „Priority“ enthielte, könnten Sie dieses Feld zusammen mit seinen Getter- und Setter-Methoden hinzufügen:

```java
private Integer priority;

/**
* Returns the item priority
*/
public Integer getPriority() {
    return mPriority;
}

/**
* Sets the item priority
*
* @param priority
*            priority to set
*/
public final void setPriority(Integer priority) {
    mPriority = priority;
}
```

Informationen zum Erstellen zusätzlicher Tabellen in Ihrem Mobile Apps-Back-End, finden Sie unter [Vorgehensweise: Definieren eines Tabellencontrollers][15] (.NET-Back-End) oder [Definieren von Tabellen mit einem dynamischen Schema][16] (Node.js-Back-End).

Eine Azure Mobile Apps-Back-End-Tabelle definiert fünf spezielle Felder, von denen vier für Clients verfügbar sind:

* `String id`: Die global eindeutige ID des Datensatzes.  Es empfiehlt sich, die ID zur Zeichenfolgendarstellung eines [UUID][17]-Objekts zu machen.
* `DateTimeOffset updatedAt`: Datum/Uhrzeit der letzten Aktualisierung.  Das Feld „updatedAt“ wird vom Server festgelegt und darf auf keinen Fall von Ihrem Clientcode festgelegt werden.
* `DateTimeOffset createdAt`: Datum und Uhrzeit der Objekterstellung.  Das Feld „createdAt“ wird vom Server festgelegt ist und darf auf keinen Fall von Ihrem Clientcode festgelegt werden.
* `byte[] version`: Die Version, die normalerweise als Zeichenfolge dargestellt wird, wird ebenfalls vom Server festgelegt.
* `boolean deleted`: Gibt an, dass der Datensatz zwar gelöscht, aber noch nicht endgültig gelöscht wurde.  Verwenden Sie `deleted` nicht als Eigenschaft in Ihrer Klasse.

`id` ist ein Pflichtfeld.  Die Felder `updatedAt` und `version` werden für die Offlinesynchronisierung (inkrementelle Synchronisierung bzw. zur Konfliktlösung) verwendet.  Das Feld `createdAt` ist ein Verweisfeld, das nicht vom Client verwendet wird.  Die Namen sind unveränderliche Namen der Eigenschaften.  Sie können jedoch mithilfe der [gson][3]-Bibliothek eine Zuordnung zwischen Ihrem Objekt und den unveränderlichen Namen erstellen.  Beispiel:

```java
package com.example.zumoappname;

import com.microsoft.windowsazure.mobileservices.table.DateTimeOffset;

public class ToDoItem
{
    @com.google.gson.annotations.SerializedName("id")
    private String mId;
    public String getId() { return mId; }
    public final void setId(String id) { mId = id; }

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;
    public boolean isComplete() { return mComplete; }
    public void setComplete(boolean complete) { mComplete = complete; }

    @com.google.gson.annotations.SerializedName("text")
    private String mText;
    public String getText() { return mText; }
    public final void setText(String text) { mText = text; }

    @com.google.gson.annotations.SerializedName("createdAt")
    private DateTimeOffset mCreatedAt;
    public DateTimeOffset getCreatedAt() { return mCreatedAt; }
    protected void setCreatedAt(DateTimeOffset createdAt) { mCreatedAt = createdAt; }

    @com.google.gson.annotations.SerializedName("updatedAt")
    private DateTimeOffset mUpdatedAt;
    public DateTimeOffset getUpdatedAt() { return mUpdatedAt; }
    protected void setUpdatedAt(DateTimeOffset updatedAt) { mUpdatedAt = updatedAt; }

    @com.google.gson.annotations.SerializedName("version")
    private String mVersion;
    public String getVersion() { return mVersion; }
    public final void setVersion(String version) { mVersion = version; }

    public ToDoItem() { }

    public ToDoItem(String id, String text) {
        this.setId(id);
        this.setText(text);
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof ToDoItem && ((ToDoItem) o).mId == mId;
    }

    @Override
    public String toString() {
        return getText();
    }
}
```

### <a name="create-a-table-reference"></a>Erstellen eines Tabellenverweises

Um auf eine Tabelle zuzugreifen, erstellen Sie zuerst ein [MobileServiceTable][8]-Objekt durch Aufrufen der **getTable**-Methode im [MobileServiceClient][9].  Diese Methode verfügt über zwei Überladungen:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

Im folgenden Code ist **mClient** ein Verweis auf Ihr MobileServiceClient-Objekt.  Die erste Überladung wird verwendet, wenn Klassenname und Tabellenname gleich sind und dieser Name im Schnellstartcode verwendet wird:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

Die zweite Überladung wird verwendet, wenn sich Tabellenname und Klassenname unterscheiden: Der erste Parameter ist der Tabellenname.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Abfragen einer Back-End-Tabelle

Rufen Sie zunächst einen Tabellenverweis ab.  Wenden Sie dann eine Abfrage auf den Tabellenverweis an.  Eine Abfrage ist eine beliebige Kombination aus Folgendem:

* Einer [Filterklausel](#filtering) `.where()`.
* Einer [Sortierungsklausel](#sorting) `.orderBy()`.
* Einer [Feldauswahlklausel](#selection) `.select()`.
* Von `.skip()` und `.top()` für [Ergebnisseiten](#paging).

Die Klauseln müssen in der obigen Reihenfolge angegeben werden.

### <a name="filter"></a> Filtern von Ergebnissen

Das allgemeine Format einer Abfrage ist wie folgt:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

Im vorherigen Beispiel werden alle Ergebnisse (bis zur maximalen vom Server festgelegten Seitengröße) zurückgegeben.  Die `.execute()`-Methode führt die Abfrage im Back-End aus.  Die Abfrage wird vor der Übertragung in das Mobile Apps-Back-End in eine [OData v3][19]-Abfrage konvertiert.  Beim Empfang konvertiert das Mobile Apps-Back-End die Abfrage in eine SQL-­Anweisung, bevor sie in der SQL Azure-Instanz ausgeführt wird.  Da die Netzwerkaktivität einige Zeit dauert, gibt die `.execute()`-Methode [`ListenableFuture<E>`][18] zurück.

### <a name="filtering"></a>Filtern zurückgegebener Daten

Bei der Ausführung der folgenden Abfrage werden alle Elemente aus der **ToDoItem**-Tabelle zurückgegeben, wobei **complete** gleich **false** ist.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** ist der Verweis auf die Tabelle im mobilen Dienst, die wir zuvor erstellt haben.

Definieren Sie einen Filter mit einem **where** -Methodenaufruf des Tabellenverweises. Der **where**-Methode folgt eine **field**-Methode, der wiederum eine Methode folgt, die das logische Prädikat angibt. Mögliche Prädikatmethoden sind **eq** (equals, ist gleich), **ne** (not equal, ist ungleich), **gt** (greater than, größer als), **ge** (greater than or equal to, größer als oder gleich), **lt** (less than, kleiner als) oder **le** (less than or equal to, kleiner als oder gleich). Mit diesen Methoden können Sie die numerischen und Zeichenfolgenfelder mit bestimmten Werten vergleichen.

Sie können nach Daten filtern. Mit den folgenden Methoden können Sie das gesamte Datumsfeld oder Teile des Datums vergleichen: **year**, **month**, **day**, **hour**, **minute** und **second**. Im folgenden Beispiel wird ein Filter für Elemente hinzugefügt, deren *Fälligkeitsdatum* 2013 entspricht.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Die folgenden Methoden unterstützen komplexe Filter für Zeichenfolgenfelder: **startsWith**, **endsWith**, **concat**, **subString**, **indexOf**, **replace**, **toLower**, **toUpper**, **trim** und **length**. Das folgende Beispiel filtert nach Tabellenzeilen, deren *text* -Spalte mit „PRI0“ beginnt.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

Die folgenden Operatormethoden werden von Zahlenfeldern unterstützt: **add**, **sub**, **mul**, **div**, **mod**, **floor**, **ceiling** und **round**. Das folgende Beispiel filtert nach Tabellenzeilen, in denen **duration** eine gerade Zahl ist.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

Sie können Prädikate mit diesen logischen Methoden verknüpfen: **and**, **or** und **not**. Im folgenden Beispiel werden zwei der obigen Beispiele kombiniert.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Gruppieren und Schachteln von logischen Operatoren:

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013)
    .and(
        startsWith("text", "PRI0")
        .or()
        .field("duration").gt(10)
    )
    .execute().get();
```

Eine detailliertere Erörterung und weitere Beispiele für die Filterung finden Sie im Blogbeitrag [Exploring the richness of the Android client query model][20].

### <a name="sorting"></a>Sortieren zurückgegebener Daten

Der folgende Code gibt alle Elemente aus einer **ToDoItems** -Tabelle in aufsteigender Reihenfolge nach dem *text* -Feld sortiert zurück. *mToDoTable* ist der Verweis auf die zuvor erstellte Back-End-Tabelle:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

Der erste Parameter der **orderBy** -Methode ist eine Zeichenfolge mit dem Namen des Felds, nach dem sortiert werden soll. Für den zweiten Parameter wird die Aufzählung **QueryOrder** verwendet, um anzugeben, ob auf- oder absteigend sortiert werden soll.  Beim Filtern mit der ***where***-Methode muss die ***where***-Methode vor dem Aufruf der ***orderBy***-Methode aufgerufen werden.

### <a name="selection"></a>Auswählen bestimmter Spalten

Der folgende Code gibt alle Elemente der Tabelle **ToDoItems** zurück, zeigt jedoch nur die Felder **complete** und **text** an. **mToDoTable** ist der Verweis auf die zuvor erstellte Back-End-Tabelle.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

Die Parameter der select-Funktion sind in diesem Fall die Zeichenfolgennamen der Tabellenspalten, die Sie zurückgeben möchten.  Die **select**-Methode muss Methoden wie **where** und **orderBy** folgen. Darauf können Seitenverwaltungsmethoden wie **skip** und **top** folgen.

### <a name="paging"></a>Seitenweises Zurückgeben von Daten

Daten werden **IMMER** auf Seiten zurückgegeben.  Die maximale Anzahl zurückgegebener Datensätze wird vom Server festgelegt.  Wenn der Client mehr Datensätze anfordert, gibt der Server die maximale Anzahl von Datensätzen zurück.  Standardmäßig umfasst die maximale Seitengröße auf dem Server 50 Datensätze.

Das erste Beispiel zeigt, wie Sie die ersten fünf Elemente einer Tabelle abrufen können. Die Abfrage gibt die Elemente aus einer **ToDoItems**-Tabelle zurück. **mToDoTable** ist der Verweis auf die zuvor erstellte Back-End-Tabelle:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

Hier ist eine Abfrage, welche die ersten fünf Elemente überspringt und die nächsten fünf zurückgibt:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Wenn Sie alle Datensätze in einer Tabelle abrufen möchten, implementieren Sie Code zum Durchlaufen aller Seiten:

```java
List<MyDataModel> results = new ArrayList<>();
int nResults;
do {
    int currentCount = results.size();
    List<MyDataModel> pagedResults = mDataTable
        .skip(currentCount).top(500)
        .execute().get();
    nResults = pagedResults.size();
    if (nResults > 0) {
        results.addAll(pagedResults);
    }
} while (nResults > 0);
```

Bei einer Anforderung aller Datensätze mit dieser Methode werden mindestens zwei Anforderungen an das Mobile Apps-Back-End erstellt.

> [!TIP]
> Das Auswählen der richtigen Seitengröße verlangt eine Balance zwischen der Arbeitsspeicherbelegung, während die Anforderung gestellt wird, der Bandbreitenbelegung und der Verzögerung beim vollständigen Empfang der Daten.  Die Standardeinstellung (50 Datensätze) eignet sich für alle Geräte.  Wenn Sie ausschließlich Geräte mit großem Arbeitsspeicher einsetzen, erhöhen Sie den Wert auf bis zu 500.  Wir haben herausgefunden, dass das Erhöhen der Seitengröße auf über 500 Datensätze zu nicht akzeptablen Verzögerungen und großen Arbeitsspeicherproblemen führt.

### <a name="chaining"></a>Gewusst wie: Verketten von Abfragemethoden

Die beim Abfragen von Back-End-Tabellen verwendeten Methoden können verkettet werden. Durch die Verkettung von Abfragemethoden können Sie spezielle Spalten gefilterter Zeilen mit Sortierung und Paging abfragen. Sie können komplexe logische Filter erstellen.  Jede Abfragemethode gibt ein Query-Objekt zurück. Um die Methodenserie zu beenden und die Abfrage auszuführen, rufen Sie die **execute** -Methode auf. Beispiel:

```java
List<ToDoItem> results = mToDoTable
        .where()
        .year("due").eq(2013)
        .and(
            startsWith("text", "PRI0").or().field("duration").gt(10)
        )
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .skip(200).top(100)
        .execute()
        .get();
```

Die verketteten Abfragemethoden müssen wie folgt sortiert werden:

1. Filtermethoden (**where**).
2. Sortiermethoden (**orderBy**).
3. Auswahlmethoden (**select**).
4. Pagingmethoden (**skip** und **top**).

## <a name="binding"></a>Binden von Daten an die Benutzeroberfläche

Datenbindung besteht aus drei Komponenten:

* Die Datenquelle
* Das Bildschirmlayout
* Der Adapter, der diese beiden Komponenten verbindet.

In unserem Beispielcode geben wir die Daten aus der Mobile Apps-SQL Azure-Tabelle **ToDoItem** in ein Array zurück. Diese Aktivität ist ein typisches Muster für Datenanwendungen.  Datenbankabfragen geben häufig eine Sammlung von Zeilen zurück, die der Client in Form einer Liste oder eines Arrays erhält. In diesem Beispiel ist das Array die Datenquelle.  Der Code gibt ein Bildschirmlayout an, das die Ansicht der auf dem Gerät angezeigten Daten definiert.  Diese beiden Komponenten sind über einen Adapter verbunden, in diesem Fall eine Erweiterung der **ArrayAdapter&lt;ToDoItem&gt;** -Klasse.

#### <a name="layout"></a>Definieren des Layouts

Das Layout wird durch mehrere XML-Codeabschnitte definiert. In einem existierenden Layout stellt der folgende Code die **ListView** dar, die wir mit unseren Serverdaten auffüllen möchten.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

Das *listitem* -Attribut im obigen Code definiert die ID des Layouts für eine bestimmte Zeile in der Liste. Dieser Code gibt ein Kontrollkästchen und den zugehörigen Text an und wird einmal für jedes Element in der Liste instanziiert. In diesem Layout wird das Feld **id** nicht angezeigt, und bei einem komplexeren Layout würden zusätzliche Felder angezeigt. Dieser Code befindet sich in der Datei **row_list_to_do.xml**.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">
    <CheckBox
        android:id="@+id/checkToDoItem"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/checkbox_text" />
</LinearLayout>
```

#### <a name="adapter"></a>Definieren des Adapters
Da die Datenquelle in unserer Ansicht ein **ToDoItem** ist, leiten wir unseren Adapter von der Klasse **ArrayAdapter&lt;ToDoItem&gt;** ab. Diese Unterklasse produziert eine Ansicht für jedes **ToDoItem** und verwendet dabei das **row_list_to_do**-Layout.  In unserem Code definieren wir die folgende Klasse, die eine Erweiterung der **ArrayAdapter&lt;E&gt;** -Klasse ist:

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Überschreiben Sie die **getView** -Methode der Klasse. Beispiel:

```java
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }
        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });
        return row;
    }
```

Wir erstellen eine Instanz dieser Klasse in unserer Aktivität wie folgt:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

Der zweite Parameter für den ToDoItemAdapter-Konstruktor ist ein Verweis auf das Layout. Jetzt können wir die **ListView** instanziieren und den Adapter der **ListView** zuweisen.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>Verwenden des Adapters zum Binden an die Benutzeroberfläche

Sie sind nun in der Lage, die Datenbindung zu verwenden. Der folgende Code zeigt, wie Elemente in der Tabelle abgerufen werden und der lokale Adapter mit den zurückgegebenen Elementen gefüllt wird.

```java
    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }
```

Rufen Sie den Adapter bei jeder Änderung der **ToDoItem** -Tabelle auf. Da Änderungen in einzelnen Datensätzen erfolgen, verarbeiten Sie eine einzelne Zeile anstatt einer Sammlung. Beim Einfügen von Elementen rufen Sie die **add**-Methode des Adapters auf, beim Löschen rufen Sie die **remove**-Methode auf.

Sie finden ein vollständiges Beispiel im [Android-Schnellstartprojekt][21].

## <a name="inserting"></a>Einfügen von Daten in das Back-End

Instanziieren Sie eine Instanz der *ToDoItem* -Klasse und legen deren Eigenschaften fest.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Verwenden Sie dann **insert()** , um ein Objekt einzufügen:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

Die zurückgegebene Entität entspricht den in die Back-End-Tabelle eingefügten Daten, die ID und alle anderen für das Back-End festgelegten Werte (wie z.B. die Felder `createdAt`, `updatedAt` und `version`) inbegriffen.

Mobile Apps-Tabellen erfordern eine Primärschlüsselspalte namens **id**. Diese Spalte muss eine Zeichenfolge sein. Der Standardwert der ID-Spalte ist eine GUID.  Sie können auch andere eindeutige Werte bereitstellen, beispielsweise E-Mail-Adressen oder Benutzernamen. Wenn für einen eingefügten Datensatz kein Zeichenfolgen-ID-Wert angegeben ist, generiert das Back-End eine neue GUID.

Zeichenfolgen-ID-Werte bieten folgende Vorteile:

* IDs können ohne Roundtrip zur Datenbank generiert werden.
* Datensätze aus unterschiedlichen Tabellen oder Datenbanken lassen sich leichter zusammenführen.
* ID-Werte lassen sich leichter in die Anwendungslogik integrieren.

Zeichenfolgen-ID-Werte sind für die Unterstützung der Offlinesynchronisierung **ERFORDERLICH** .  Ein ID kann nicht geändert werden, nachdem sie in der Back-End-Datenbank gespeichert wurde.

## <a name="updating"></a>Aktualisieren von Daten in einer mobilen App

Um Daten in einer Tabelle zu aktualisieren, übergeben Sie das neue Objekt an die **update()** -Methode.

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

In diesem Beispiel ist *item* ein Verweis auf eine Zeile in der *ToDoItem*-Tabelle, an der einige Änderungen vorgenommen wurden.  Die Zeile mit dem gleichen **id** -Wert wird aktualisiert.

## <a name="deleting"></a>Löschen von Daten in einer mobilen App

Der folgende Code zeigt, wie Sie Daten durch Angabe des Datenobjekts aus einer Tabelle löschen.

```java
mToDoTable
    .delete(item);
```

Sie können ein Element auch durch Angabe des **id** -Felds der zu löschenden Zeile löschen.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Suchen eines bestimmtes Elements anhand der ID

Mit der **lookUp()** -Methode suchen Sie nach einem Element mit einem bestimmten **id**-Feld:

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Gewusst wie: Arbeiten mit nicht typisierten Daten

Das Programmiermodell ohne Typisierung ermöglicht Ihnen eine genaue Steuerung der JSON-Serialisierung.  Es gibt einige häufige Szenarien, in denen der Einsatz eines nicht typisierten Programmiermodells wünschenswert ist. Beispiel: Ihre Back-End-Tabelle enthält viele Spalten, und Sie müssen nur auf eine Teilmenge der Spalten verweisen.  Im typisierten Modell müssen Sie alle Spalten definieren, die im Mobile Apps-Back-End in Ihrer Datenklasse definiert sind.  Die meisten API-Aufrufe für den Datenzugriff sind gleich wie beim typisierten Programmiermodell. Im untypisierten Modell rufen Sie Methoden des **MobileServiceJsonTable**-Objekts anstelle des **MobileServiceTable**-Objekts auf.

### <a name="json_instance"></a>Erstellen einer Instanz einer nicht typisierten Tabelle

Ähnlich wie beim typisierten Modell benötigen Sie zunächst einen Tabellenverweis. In diesem Fall handelt es sich jedoch um ein **MobileServicesJsonTable**-Objekt. Rufen Sie den Verweis durch Aufruf der **getTable**-Methode in einer Instanz des Clients ab:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

Nachdem Sie eine Instanz der **MobileServiceJsonTable**erstellt haben, steht dieser Instanz praktisch die gleiche API zur Verfügung wie beim typisierten Programmiermodell. In einigen Fällen akzeptieren die Methoden einen nicht typisierten Parameter anstelle eines typisierten Parameters.

### <a name="json_insert"></a>Einfügen in nicht typisierte Tabellen
Der folgende Code zeigt, wie Sie Elemente einfügen können. Sie müssen zunächst ein [JsonObject][1] erstellen, das Teil der [gson][3]-Bibliothek ist.

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Verwenden Sie anschließend **insert()** , um das nicht typisierte Objekt in die Tabelle einzufügen.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Wenn Sie die ID des eingefügten Objekts abrufen müssen, verwenden Sie die **getAsJsonPrimitive()** -Methode.

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Löschen aus einer nicht typisierten Tabelle
Der folgende Code zeigt, wie Sie eine Instanz löschen, in diesem Fall die Instanz des **JsonObject** , die Sie im vorigen *insert* -Beispiel eingefügt haben. Der Code ist derselbe wie im typisierten Fall, aber die Methode weist eine andere Signatur auf, da sie auf ein **JsonObject**verweist.

```java
mToDoTable
    .delete(insertedItem);
```

Sie können eine Instanz auch direkt anhand ihrer ID löschen:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Zurückgeben aller Zeilen aus einer nicht typisierten Tabelle
Der folgende Code ruft eine gesamte Tabelle ab. Da Sie eine JSON-Tabelle verwenden, können Sie nur einige der Spalten der Tabelle selektiv abrufen.

```java
public void showAllUntyped(View view) {
    new AsyncTask<Void, Void, Void>() {
        @Override
        protected Void doInBackground(Void... params) {
            try {
                final JsonElement result = mJsonToDoTable.execute().get();
                final JsonArray results = result.getAsJsonArray();
                runOnUiThread(new Runnable() {

                    @Override
                    public void run() {
                        mAdapter.clear();
                        for (JsonElement item : results) {
                            String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                            String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                            Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                            ToDoItem mToDoItem = new ToDoItem();
                            mToDoItem.setId(ID);
                            mToDoItem.setText(mText);
                            mToDoItem.setComplete(mComplete);
                            mAdapter.add(mToDoItem);
                        }
                    }
                });
            } catch (Exception exception) {
                createAndShowDialog(exception, "Error");
            }
            return null;
        }
    }.execute();
}
```

Für das nicht typisierte Modell steht der gleiche Satz an Filter- und Seitenverwaltungsmethoden zur Verfügung wie für das typisierte Modell.

## <a name="offline-sync"></a>Implementieren der Offlinesynchronisierung

Das Client SDK von Azure Mobile Apps implementiert auch die Offlinesynchronisierung von Daten mithilfe einer SQLite-Datenbank zum lokalen Speichern einer Kopie der Serverdaten.  Vorgänge, die in einer Offlinetabelle erfolgen, erfordern keine mobilen Verbindungen.  Die Offlinesynchronisierung unterstützt Stabilität und Leistung auf Kosten komplexerer Logik zur Konfliktlösung.  Das Client SDK von Azure Mobile Apps implementiert die folgenden Features:

* Inkrementelle Synchronisierung: Nur aktualisierte und neue Datensätze werden heruntergeladen, was Bandbreite und Arbeitsspeichernutzung spart.
* Optimistische Parallelität: Es wird angenommen, dass Vorgänge erfolgreich sind.  Die Lösung von Konflikten wird verzögert, bis Updates auf dem Server erfolgt sind.
* Konfliktlösung: Das SDK erkennt, wenn eine einen Konflikt verursachende Änderung auf dem Server erfolgt ist, und bietet Hooks zum Warnen des Benutzers.
* Vorläufiges Löschen: Gelöschte Datensätze werden als gelöscht markiert, sodass andere Geräte ihren Offlinecache aktualisieren können.

### <a name="initialize-offline-sync"></a>Initialisieren der Offlinesynchronisierung

Jede Offlinetabelle muss vor der Nutzung im Offlinecache definiert werden.  Normalerweise erfolgt die Tabellendefinition sofort nach Erstellung des Clients:

```java
AsyncTask<Void, Void, Void> initializeStore(MobileServiceClient mClient)
    throws MobileServiceLocalStoreException, ExecutionException, InterruptedException
{
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>() {
        @Override
        protected void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                if (syncContext.isInitialized()) {
                    return null;
                }
                SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "offlineStore", null, 1);

                // Create a table definition.  As a best practice, store this with the model definition and return it via
                // a static method
                Map<String, ColumnDataType> toDoItemDefinition = new HashMap<String, ColumnDataType>();
                toDoItemDefinition.put("id", ColumnDataType.String);
                toDoItemDefinition.put("complete", ColumnDataType.Boolean);
                toDoItemDefinition.put("text", ColumnDataType.String);
                toDoItemDefinition.put("version", ColumnDataType.String);
                toDoItemDefinition.put("updatedAt", ColumnDataType.DateTimeOffset);

                // Now define the table in the local store
                localStore.defineTable("ToDoItem", toDoItemDefinition);

                // Specify a sync handler for conflict resolution
                SimpleSyncHandler handler = new SimpleSyncHandler();

                // Initialize the local store
                syncContext.initialize(localStore, handler).get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Rufen Sie einen Verweis auf die Offlinecachetabelle ab.

Für eine Onlinetabelle verwenden Sie `.getTable()`.  Verwenden Sie für eine Offlinetabelle `.getSyncTable()`:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Alle Methoden, die für Onlinetabellen verfügbar sind (z.B. Filtern, Sortieren, Paginieren sowie Einfügen, Aktualisieren und Löschen von Daten), funktionieren für Offlinetabellen gleich.

### <a name="synchronize-the-local-offline-cache"></a>Synchronisieren des lokalen Offlinecaches

Die Synchronisierung unterliegt der Steuerung Ihrer App.  Es folgt ein Beispiel einer Synchronisierungsmethode:

```java
private AsyncTask<Void, Void, Void> sync(MobileServiceClient mClient) {
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
        @Override
        protected Void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                syncContext.push().get();
                mToDoTable.pull(null, "todoitem").get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

Wenn Sie für die `.pull(query, queryname)`-Methode einen Abfragenamen angeben, wird die inkrementelle Synchronisierung verwendet, um nur Datensätze zurückzugeben, die seit dem letzten erfolgreich abgeschlossenen Pullvorgang erstellt oder geändert wurden.

### <a name="handle-conflicts-during-offline-synchronization"></a>Behandeln von Konflikten bei der Offlinesynchronisierung

Falls ein Konflikt während eines `.push()`-Vorgangs auftritt, wird eine `MobileServiceConflictException` ausgelöst.   Das vom Server ausgegebene Element wird in die Ausnahme eingebettet und kann von `.getItem()` für die Ausnahme abgerufen werden.  Passen Sie den Pushvorgang durch Aufrufen der folgenden Elemente für das „MobileServiceSyncContext“-Objekt an:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Sobald alle Konflikte wie gewünscht markiert sind, rufen Sie erneut `.push()` auf, um alle Konflikte zu lösen.

## <a name="custom-api"></a>Aufrufen einer benutzerdefinierten API

Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats.

Rufen Sie von einem Android-Client aus die **invokeApi** -Methode auf, um den benutzerdefinierten API-Endpunkt aufzurufen. Das folgende Beispiel zeigt, wie Sie einen API-Endpunkt mit dem Namen **completeAll** aufrufen, der eine Sammlungsklasse mit dem Namen **MarkAllResult** zurückgibt.

```java
public void completeItem(View view) {
    ListenableFuture<MarkAllResult> result = mClient.invokeApi("completeAll", MarkAllResult.class);
    Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
        @Override
        public void onFailure(Throwable exc) {
            createAndShowDialog((Exception) exc, "Error");
        }

        @Override
        public void onSuccess(MarkAllResult result) {
            createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
            refreshItemsFromTable();
        }
    });
}
```

Die **invokeApi** -Methode wird beim Client aufgerufen, der eine POST-Anfrage an die neue benutzerdefinierte API sendet. Das von der benutzerdefinierten API zurückgegebene Ergebnis wird in einem Meldungsdialogfeld angezeigt, ebenso wie jegliche Fehler. Mit anderen Versionen von **invokeApi** können Sie optional ein Objekt im Anforderungshauptteil senden, die HTTP-Methode angeben und Abfrageparameter mit der Anforderung senden. Nicht typisierte Versionen von **invokeApi** werden ebenfalls bereitgestellt.

## <a name="authentication"></a>Hinzufügen der Authentifizierung zu Ihrer App

Es stehen bereits Tutorials zur Verfügung, in denen das Hinzufügen dieser Features im Detail beschrieben wird.

App Service unterstützt die [Authentifizierung von App-Benutzern](app-service-mobile-android-get-started-users.md) mithilfe verschiedener externer Identitätsanbieter: Facebook, Google, Microsoft-Konto, Twitter und Azure Active Directory. Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die Identität authentifizierter Benutzer verwenden, um Autorisierungsregeln auf Ihrem Back-End zu implementieren.

Insgesamt werden zwei Authentifizierungsflüsse unterstützt: ein **Serverfluss** und ein **Clientfluss**. Der Serverfluss bietet die einfachste Authentifizierungsform, da in diesem Fall die Webschnittstelle des Identitätsanbieters verwendet wird.  Zum Implementieren der Serverflussauthentifizierung sind keine weiteren SDKs erforderlich. Die Serverflussauthentifizierung stellt keine enge Integration in das mobile Gerät bereit und wird nur für PoC-Szenarien (Proof of Concept) empfohlen.

Der Clientfluss ermöglicht eine engere Integration in gerätespezifische Funktionen wie z.B. die einmalige Anmeldung, da in diesem Fall vom Identitätsanbieter bereitgestellte SDKs verwendet werden.  Sie können z.B. das Facebook-SDK in Ihre mobile Anwendung integrieren.  Der mobile Client wechselt zur Facebook-App und bestätigt Ihre Anmeldung, bevor er wieder zu Ihrer mobilen App zurückwechselt.

Zum Aktivieren der Authentifizierung in Ihrer App sind vier Schritte erforderlich:

* Registrieren Sie Ihre App zur Authentifizierung bei einem Identitätsanbieter.
* Konfigurieren Sie das App Service-Back-End.
* Schränken Sie die Tabellenberechtigungen ausschließlich auf authentifizierte Benutzer im App Service-Back-End ein.
* Fügen Sie Ihrer App Authentifizierungscode hinzu.

Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die SID eines authentifizierten Benutzers verwenden, um Anfragen zu verändern.  Weitere Informationen finden Sie unter [Erste Schritte mit der Authentifizierung] und in der Dokumentation des Server-SDK.

### <a name="caching"></a>Authentifizierung: Serverfluss

Der folgende Code startet einen Serverfluss-Anmeldeprozess mit dem Google-Anbieter.  Aufgrund der Sicherheitsanforderungen für den Anbieter Google ist eine zusätzliche Konfiguration erforderlich:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Fügen Sie darüber hinaus der Hauptklasse „Activity“ die folgende Methode hinzu:

```java
// You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // When request completes
    if (resultCode == RESULT_OK) {
        // Check the request code matches the one we send in the login request
        if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
            MobileServiceActivityResult result = mClient.onActivityResult(data);
            if (result.isLoggedIn()) {
                // login succeeded
                createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                createTable();
            } else {
                // login failed, check the error message
                String errorMessage = result.getErrorMessage();
                createAndShowDialog(errorMessage, "Error");
            }
        }
    }
}
```

Der in Ihrer Hauptidentität definierte `GOOGLE_LOGIN_REQUEST_CODE` wird für die `login()`-Methode in der `onActivityResult()`-Methode verwendet.  Sie können eine beliebige eindeutige Nummer wählen, sofern dieselbe Nummer innerhalb der `login()`-Methode und der `onActivityResult()`-Methode verwendet wird.  Wenn Sie den Clientcode (wie zuvor gezeigt) in einen Dienstadapter abstrahieren, müssen Sie die entsprechenden Methoden für den Dienstadapter aufrufen.

Außerdem müssen Sie das Projekt für „customtabs“ konfigurieren.  Geben Sie zuerst eine Umleitungs-URL an.  Führen Sie `AndroidManifest.xml` den folgenden Codeausschnitt hinzu:

```xml
<activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback"/>
    </intent-filter>
</activity>
```

Fügen Sie der Datei `build.gradle` Ihrer Anwendung **redirectUriScheme** hinzu:

```gradle
android {
    buildTypes {
        release {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
        debug {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
    }
}
```

Fügen Sie schließlich `com.android.support:customtabs:28.0.0` zur Abhängigkeitsliste in der Datei `build.gradle` hinzu:

```gradle
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.google.code.gson:gson:2.3'
    implementation 'com.google.guava:guava:18.0'
    implementation 'com.android.support:customtabs:28.0.0'
    implementation 'com.squareup.okhttp:okhttp:2.5.0'
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

Rufen Sie die ID des angemeldeten Benutzers mit der **getUserId**-Methode aus einem **MobileServiceUser** ab. Ein Beispiel zur Verwendung von „Futures“ zum Abrufen der asynchronen APIs für die Anmeldung finden Sie unter [Erste Schritte mit der Authentifizierung].

> [!WARNING]
> Bei dem erwähnten URL-Schema muss die Groß-/Kleinschreibung beachtet werden.  Stellen Sie sicher, dass alle Vorkommen von `{url_scheme_of_you_app}` die gleiche Groß-/Kleinschreibung aufweisen.

### <a name="caching"></a>Zwischenspeichern von Authentifizierungstoken

Beim Zwischenspeichern von Authentifizierungstoken müssen Benutzer-ID und Authentifizierungstoken lokal auf dem Gerät gespeichert werden. Beim nächsten Start der App prüfen Sie den Cache. Wenn diese Werte vorhanden sind, können Sie die Anmeldeprozedur überspringen und den Client mit den vorhandenen Daten füllen. Diese Daten sind jedoch vertraulich und sollten aus Sicherheitsgründen verschlüsselt gespeichert werden, falls das Telefon gestohlen wird.  Ein vollständiges Beispiel zum Zwischenspeichern von Authentifizierungstoken finden Sie im Abschnitt [Zwischenspeichern von Authentifizierungstokens][7].

Wenn Sie versuchen, ein abgelaufenes Token zu verwenden, erhalten Sie die Antwort *401 – nicht autorisiert* . Mithilfe von Filtern können Sie Authentifizierungsfehler behandeln.  Filter fangen Anforderungen an das App Service-Back-End ab. Der Filtercode prüft Antworten auf 401-Fehler, löst den Anmeldeprozess aus und setzt anschließend die Anforderung fort, die den 401-Fehler ausgelöst hatte.

### <a name="refresh"></a>Verwenden von Aktualisierungstoken

Das von der Azure App Service-Authentifizierung und -Autorisierung zurückgegebene Token hat eine festgelegte Gültigkeitsdauer von einer Stunde.  Nach Ablauf dieses Zeitraums müssen Sie den Benutzer erneut authentifizieren.  Wenn Sie ein langlebiges Token verwenden, das Sie über die Clientflussauthentifizierung erhalten haben, können Sie mithilfe der Azure App Service-Authentifizierung und -Autorisierung mit dem gleichen Token eine erneute Authentifizierung vornehmen.  Ein weiteres Azure App Service-Token wird mit einer neuen Gültigkeitsdauer generiert.

Sie können auch den Anbieter registrieren, um Aktualisierungstoken zu verwenden.  Ein Aktualisierungstoken ist nicht immer verfügbar.  Es ist eine zusätzliche Konfiguration notwendig:

* Konfigurieren Sie für **Azure Active Directory** ein Clientgeheimnis für die Azure Active Directory-App.  Geben Sie das Clientgeheimnis in Azure App Service beim Konfigurieren der Azure Active Directory-Authentifizierung an.  Übergeben Sie, wenn Sie `.login()` aufrufen, `response_type=code id_token` als Parameter:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Übergeben Sie `access_type=offline` als Parameter für **Google**:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* Wählen Sie für **Microsoft-Konto** den Bereich `wl.offline_access` aus.

Um ein Token zu aktualisieren, rufen Sie `.refreshUser()` auf:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

Empfohlen wird das Erstellen eines Filters, der die Antwort 401 vom Server erkennt und versucht, das Benutzertoken zu aktualisieren.

## <a name="log-in-with-client-flow-authentication"></a>Anmelden mit der Clientflussauthentifizierung

Der allgemeine Ablauf bei der Anmeldung mit der Clientflussauthentifizierung ist wie folgt:

* Konfigurieren Sie die Azure App Service-Authentifizierung und -Autorisierung wie bei der Serverflussauthentifizierung.
* Integrieren Sie das SDK für Authentifizierungsanbieter für die Authentifizierung, um ein Zugriffstoken zu erzeugen.
* Rufen Sie die `.login()`-Methode wie folgt auf (`result` sollte ein `AuthenticationResult` sein):

    ```java
    JSONObject payload = new JSONObject();
    payload.put("access_token", result.getAccessToken());
    ListenableFuture<MobileServiceUser> mLogin = mClient.login("{provider}", payload.toString());
    Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
        @Override
        public void onFailure(Throwable exc) {
            exc.printStackTrace();
        }
        @Override
        public void onSuccess(MobileServiceUser user) {
            Log.d(TAG, "Login Complete");
        }
    });
    ```

Sehen Sie sich das vollständige Codebeispiel im nächsten Abschnitt an.

Ersetzen Sie die `onSuccess()`-Methode durch den Code, den Sie bei einer erfolgreichen Anmeldung verwenden möchten.  Die Zeichenfolge `{provider}` ist ein gültiger Anbieter: **aad** (Azure Active Directory), **Facebook**, **Google**, **microsoftaccount** oder **twitter**.  Wenn Sie die benutzerdefinierte Authentifizierung implementiert haben, können Sie auch das Tag des benutzerdefinierten Authentifizierungsanbieters verwenden.

### <a name="adal"></a>Authentifizieren von Benutzern mit der Active Directory Authentication Library (ADAL)

Nutzen Sie die Active Directory-Authentifizierungsbibliothek (Active Directory Authentication Library, ADAL), um Benutzer mithilfe von Azure Active Directory bei Ihrer Anwendung anzumelden. Aufgrund der besseren Bedienbarkeit und der Möglichkeit, zusätzliche Anpassungen vorzunehmen, wird eine Clientflussanmeldung den `loginAsync()` -Methoden häufig vorgezogen.

1. Konfigurieren Sie Ihr mobiles App-Back-End für die AAD-Anmeldung, indem Sie die im Tutorial [So konfigurieren Sie Ihre App Service-Anwendung zur Verwendung der Azure Active Directory-Anmeldung][22] beschriebenen Schritte ausführen. Schließen Sie auch den optionalen Schritt zur Registrierung einer nativen Clientanwendung ab.
2. Installieren Sie ADAL, indem Sie die Datei „build.gradle“ so ändern, dass sie folgende Definitionen enthält:

    ```gradle
    repositories {
        mavenCentral()
        flatDir {
            dirs 'libs'
        }
        maven {
            url "YourLocalMavenRepoPath\\.m2\\repository"
        }
    }
    packagingOptions {
        exclude 'META-INF/MSFTSIG.RSA'
        exclude 'META-INF/MSFTSIG.SF'
    }
    dependencies {
        implementation fileTree(dir: 'libs', include: ['*.jar'])
        implementation('com.microsoft.aad:adal:1.16.1') {
            exclude group: 'com.android.support'
        } // Recent version is 1.16.1
        implementation 'com.android.support:support-v4:28.0.0'
    }
    ```

3. Fügen Sie Ihrer Anwendung den unten stehenden Code hinzu, und nehmen Sie die folgenden Ersetzungen vor:

    * Ersetzen Sie **INSERT-AUTHORITY-HERE** durch den Namen des Mandanten, in dem Sie Ihre Anwendung bereitgestellt haben. Das Format muss https://login.microsoftonline.com/contoso.onmicrosoft.com sein.
    * Ersetzen Sie **INSERT-RESOURCE-ID-HERE** durch die Client-ID für Ihr mobiles App-Back-End. Sie können die Client-ID im Portal auf der Registerkarte **Erweitert** unter **Azure Active Directory-Einstellungen** abrufen.
    * Ersetzen Sie **INSERT-CLIENT-ID-HERE** durch die Client-ID, die Sie aus der nativen Clientanwendung kopiert haben.
    * Ersetzen Sie mithilfe des HTTPS-Schemas **INSERT-REDIRECT-URI-HERE** durch den Endpunkt */.auth/login/done* Ihrer Website. Dieser Wert sollte *https://contoso.azurewebsites.net/.auth/login/done* ähnlich sein.

```java
private AuthenticationContext mContext;

private void authenticate() {
    String authority = "INSERT-AUTHORITY-HERE";
    String resourceId = "INSERT-RESOURCE-ID-HERE";
    String clientId = "INSERT-CLIENT-ID-HERE";
    String redirectUri = "INSERT-REDIRECT-URI-HERE";
    try {
        mContext = new AuthenticationContext(this, authority, true);
        mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
    } catch (Exception exc) {
        exc.printStackTrace();
    }
}

private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
    @Override
    public void onError(Exception exc) {
        if (exc instanceof AuthenticationException) {
            Log.d(TAG, "Cancelled");
        } else {
            Log.d(TAG, "Authentication error:" + exc.getMessage());
        }
    }

    @Override
    public void onSuccess(AuthenticationResult result) {
        if (result == null || result.getAccessToken() == null
                || result.getAccessToken().isEmpty()) {
            Log.d(TAG, "Token is empty");
        } else {
            try {
                JSONObject payload = new JSONObject();
                payload.put("access_token", result.getAccessToken());
                ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        exc.printStackTrace();
                    }
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        Log.d(TAG, "Login Complete");
                    }
                });
            }
            catch (Exception exc){
                Log.d(TAG, "Authentication error:" + exc.getMessage());
            }
        }
    }
};

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (mContext != null) {
        mContext.onActivityResult(requestCode, resultCode, data);
    }
}
```

## <a name="filters"></a>Anpassen der Client/Server-Kommunikation

Die Clientverbindung ist normalerweise eine einfache HTTP-Verbindung unter Verwendung der zugrunde liegenden HTTP-Clientbibliothek, die vom Android SDK bereitgestellt wird.  Es gibt verschiedene Gründe, warum dies geändert werden sollte:

* Sie möchten eine alternative HTTP-Bibliothek verwenden, um Timeouts anzupassen.
* Sie möchten eine Statusanzeige anzeigen.
* Sie möchten einen benutzerdefinierten Header zur Unterstützung von API-Verwaltungsfunktionen hinzufügen.
* Sie möchten eine fehlerhafte Antwort abfangen, damit Sie die erneute Authentifizierung implementieren können.
* Sie möchten Back-End-Anforderungen in einem Analysedienst protokollieren.

### <a name="using-an-alternate-http-library"></a>Verwenden einer alternativen HTTP-Bibliothek

Rufen Sie die `.setAndroidHttpClientFactory()`-Methode sofort nach Erstellen Ihres Clientverweises auf.  So legen Sie beispielsweise das Verbindungstimeout auf 60 Sekunden (statt auf die standardmäßigen 10 Sekunden) fest

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClient();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>Implementieren einen Statusfilters

Sie können ein Abfangen jeder Anforderung implementieren, indem Sie einen `ServiceFilter` implementieren.  Der folgende Code aktualisiert z.B. eine vorab erstellte Statusanzeige:

```java
private class ProgressFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
            }
        });

        ListenableFuture<ServiceFilterResponse> future = next.onNext(request);
        Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
            @Override
            public void onFailure(Throwable e) {
                resultFuture.setException(e);
            }
            @Override
            public void onSuccess(ServiceFilterResponse response) {
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        if (mProgressBar != null)
                            mProgressBar.setVisibility(ProgressBar.GONE);
                    }
                });
                resultFuture.set(response);
            }
        });
        return resultFuture;
    }
}
```

Sie können diesen Filter wie folgt an den Client anfügen:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Anpassen von Anforderungsheadern

Verwenden Sie den folgenden `ServiceFilter`, und fügen Sie den Filter auf die gleiche Weise wie den `ProgressFilter` an:

```java
private class CustomHeaderFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                request.addHeader("X-APIM-Router", "mobileBackend");
            }
        });
        SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
        try {
            ServiceFilterResponse response = next.onNext(request).get();
            result.set(response);
        } catch (Exception exc) {
            result.setException(exc);
        }
    }
}
```

### <a name="conversions"></a>Konfigurieren der automatischen Serialisierung

Sie können mit der [gson][3]-API eine Konvertierungsstrategie angeben, die für jede Spalte gilt. Die Android-Clientbibliothek verwendet im Hintergrund [gson][3], um Java-Objekte zu JSON-Daten zu serialisieren, bevor die Daten an Azure App Service übertragen werden.  Der folgende Code verwendet die **setFieldNamingStrategy()** -Methode, um die Strategie festzulegen. Dieses Beispiel löscht für jeden Feldnamen das erste Zeichen (ein „m“) und wandelt das folgende Zeichen zu einem Kleinbuchstaben um. Die Zeichenfolge „mId“ beispielsweise wird in „id“ umgewandelt.  Implementieren Sie eine Konvertierungsstrategie, um die Notwendigkeit von `SerializedName()`-Anmerkungen für die meisten Felder zu verringern.

```java
FieldNamingStrategy namingStrategy = new FieldNamingStrategy() {
    public String translateName(File field) {
        String name = field.getName();
        return Character.toLowerCase(name.charAt(1)) + name.substring(2);
    }
}

client.setGsonBuilder(
    MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(namingStrategy)
);
```

Dieser Code muss vor dem Erstellen eines Verweises auf den mobilen Client mithilfe von **MobileServiceClient** ausgeführt werden.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: https://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Erste Schritte mit der Authentifizierung]: app-service-mobile-android-get-started-users.md
[1]: https://static.javadoc.io/com.google.code.gson/gson/2.8.5/com/google/gson/JsonObject.html
[2]: https://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: https://www.javadoc.io/doc/com.google.code.gson/gson/2.8.5
[4]: https://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: https://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: https://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: https://www.odata.org/documentation/odata-version-3-0/
[20]: https://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/configure-authentication-provider-aad.md
[Future]: https://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: https://developer.android.com/reference/android/os/AsyncTask.html
