---
title: Verwalten des Zwillingsgraphen mit Beziehungen
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie einen Graphen von digitalen Zwillingen verwalten, indem Sie sie über Beziehungen verbinden.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 947a0c9a0af3c38d6c4d6f66da691d62530a69e7
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93279505"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Verwalten eines Graphen von digitalen Zwillingen mithilfe von Beziehungen

Das Herzstück von Azure Digital Twins ist der [Zwillingsgraph](concepts-twins-graph.md), der Ihre gesamte Umgebung darstellt. Der Zwillingsgraph setzt sich aus einzelnen digitalen Zwillingen zusammen, die über **Beziehungen** verbunden sind. 

Sobald Sie über eine funktionierende [Azure Digital Twins-Instanz](how-to-set-up-instance-portal.md) verfügen und [Authentifizierungscode](how-to-authenticate-client.md) in Ihrer Client-App eingerichtet haben, können Sie die [**DigitalTwins-APIs**](/rest/api/digital-twins/dataplane/twins) verwenden, um digitale Zwillinge und ihre Beziehungen in einer Azure Digital Twins-Instanz zu erstellen, zu ändern und zu löschen. Sie können auch das [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) oder die [Azure Digital Twins-CLI](how-to-use-cli.md) verwenden.

Dieser Artikel konzentriert sich auf die Verwaltung von Beziehungen und den Graphen als Ganzes. Für die Arbeit mit einzelnen digitalen Zwillingen finden Sie weitere Informationen unter [ *Verwalten digitaler Zwillinge*](how-to-manage-twin.md).

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]
    
[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Erstellen von Beziehungen

Beziehungen beschreiben, wie verschiedene digitale Zwillinge miteinander verbunden sind, was die Grundlage des Zwillingsgraphen bildet.

Beziehungen werden mit dem `CreateRelationship()`-Aufruf erstellt. 

Sie müssen Folgendes angeben, um eine Beziehung zu erstellen:
* Die ID des Quellzwillings (`srcId` im nachfolgenden Codebeispiel): Die ID des Zwillings, von dem die Beziehung ausgeht.
* Die ID des Zielzwillings (`targetId` im nachfolgenden Codebeispiel): Die ID des Zwillings, an den die Beziehung gerichtet ist.
* Ein Beziehungsname (`relName` im nachfolgenden Codebeispiel): Der generische Typ der Beziehung, z. B. _Enthält_.
* Eine Beziehungs-ID (`relId` im nachfolgenden Codebeispiel): Der spezifische Name für diese Beziehung, z. B. _Beziehung1_.

Die Beziehungs-ID muss innerhalb des angegebenen Quellzwillings eindeutig sein. Sie muss nicht global eindeutig sein.
Für den Zwilling *foo* muss z. B. jede bestimmte Beziehungs-ID eindeutig sein. Ein weiterer Zwilling *bar* kann jedoch eine ausgehende Beziehung aufweisen, die derselben ID einer *foo* -Beziehung entspricht.

Im folgenden Codebeispiel wird veranschaulicht, wie Sie in Ihrer Azure Digital Twins-Instanz eine Beziehung erstellen.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync(srcId, relId, relationship);
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
            
        }
```
In Ihrer Hauptmethode können Sie jetzt die `CreateRelationship()`-Funktion aufrufen, um eine _Enthält_ -Beziehung wie die folgende zu erstellen: 

```csharp
await CreateRelationship(client, srcId, targetId, "contains");
```
Wenn Sie mehrere Beziehungen erstellen möchten, können Sie Aufrufe derselben Methode wiederholen und dabei verschiedene Beziehungstypen an das Argument übergeben. 

Weitere Informationen zur Hilfsklasse `BasicRelationship` finden Sie unter [ *Verwenden der Azure Digital Twins-APIs und SDKs*](how-to-use-apis-sdks.md#serialization-helpers).

### <a name="create-multiple-relationships-between-twins"></a>Erstellen mehrerer Beziehungen zwischen Zwillingen

Beziehungen können wie folgt klassifiziert werden: 

* Ausgehende Beziehungen: Beziehungen, die zu diesem Zwilling gehören, die nach außen gerichtet sind, um eine Verbindung mit anderen Zwillingen herzustellen. Die Methode `GetRelationshipsAsync()` wird verwendet, um ausgehende Beziehungen eines Zwillings abzurufen.
* Eingehende Beziehungen: Beziehungen, die zu anderen Zwillingen gehören, die auf diesen Zwilling verweisen, um eine „eingehende“ Verbindung zu erstellen. Die Methode `GetIncomingRelationshipsAsync()` wird verwendet, um eingehende Beziehungen eines Zwillings abzurufen.

Zwischen Zwillingen können beliebig viele Beziehungen vorhanden sein. 

Das bedeutet, Sie können gleichzeitig verschiedene Arten von Beziehungen zwischen zwei Zwillingen ausdrücken. Ein Beispiel: *Zwilling A* kann sowohl eine *gespeicherte* als auch eine *erstellte* Beziehung mit *Zwilling B* haben.

Auf Wunsch können sogar mehrere Instanzen der gleichen Art von Beziehung zwischen den gleichen beiden Zwillingen erstellt werden. In diesem Beispiel könnte *Zwilling A* zwei verschiedene *gespeicherte* Beziehungen mit *Zwilling B* aufweisen, solange die Beziehungen unterschiedliche Beziehungs-IDs verwenden.

## <a name="list-relationships"></a>Auflisten von Beziehungen

Sie können die Methode `GetRelationships()` wie folgt verwenden, um auf die Liste der **ausgehenden** Beziehungen für einen bestimmten Zwilling im Graphen zuzugreifen:

```csharp
await client.GetRelationships()
```

Dies gibt ein `Azure.Pageable<T>` oder `Azure.AsyncPageable<T>` zurück, je nachdem, ob Sie die synchrone oder asynchrone Version des Aufrufs verwenden.

Im Folgenden finden Sie ein Beispiel, das eine Liste von Beziehungen abruft:

```csharp
public static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

```
Sie können diese Methode jetzt aufrufen, um die ausgehenden Beziehungen der Zwillinge wie folgt anzuzeigen:

```csharp
await FindOutgoingRelationshipsAsync(client, twin_Id);
```
Sie können die abgerufenen Beziehungen verwenden, um zu anderen Zwillingen in Ihrem Graphen zu navigieren. Lesen Sie dazu das Feld `target` aus der zurückgegebenen Beziehung, und verwenden Sie es als ID für Ihren nächsten Aufruf von `GetDigitalTwin()`.

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Suchen von eingehenden Beziehungen zu einem digitalen Zwilling

Azure Digital Twins verfügt auch über eine API, um alle _ *eingehenden* * Beziehungen für einen bestimmten Zwilling zu finden. Dies ist oft nützlich bei der umgekehrten Navigation oder beim Löschen eines Zwillings.

Das Codebeispiel oben hat sich auf die Suche nach ausgehenden Beziehungen von einem Zwilling konzentriert. Das folgende Beispiel ist ähnlich strukturiert, findet aber stattdessen *eingehende* Beziehungen vom Zwilling.

Beachten Sie, dass die `IncomingRelationship`-Aufrufe nicht den vollständigen Text der Beziehung zurückgeben.

```csharp
public static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");

                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }
```

Sie können diese Methode jetzt aufrufen, um die eingehenden Beziehungen der Zwillinge wie folgt anzuzeigen:

```csharp
await FindIncomingRelationshipsAsync(client, twin_Id);
```
### <a name="list-all-twin-properties-and-relationships"></a>Auflisten aller Zwillingseigenschaften und -beziehungen

Mithilfe der oben genannten Methoden zur Auflistung der aus- und eingehenden Beziehungen zu einem Zwilling können Sie eine Methode erstellen, die vollständige Zwillingsinformationen ausgibt, einschließlich der Eigenschaften des Zwillings und beider Arten seiner Beziehungen. Hier folgt eine Beispielmethode namens `FetchAndPrintTwinAsync()`, die die Vorgehensweise veranschaulicht.

```csharp  
private static async Task FetchAndPrintTwinAsync(DigitalTwinsClient client, string twin_Id)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }
```

Sie können diese Funktion jetzt in Ihrer Hauptmethode wie folgt aufrufen: 

```csharp
await FetchAndPrintTwinAsync(client, targetId);
```
## <a name="delete-relationships"></a>Löschen von Beziehungen

Der erste Parameter gibt den Quellzwilling an (der Zwilling, von dem die Beziehung ausgeht). Der andere Parameter ist die Beziehungs-ID. Sie benötigen sowohl die Zwillings-ID als auch die Beziehungs-ID, da Beziehungs-IDs nur innerhalb des Bereichs eines Zwillings eindeutig sind.

```csharp
private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
```

Sie können jetzt diese Methode aufrufen, um eine Beziehung wie folgt zu löschen:

```csharp
await DeleteRelationship(client, srcId, relId);
```
## <a name="create-a-twin-graph"></a>Erstellen eines Zwillingsgraphen 

Der folgende ausführbare Codeausschnitt verwendet die Beziehungsvorgänge aus diesem Artikel, um einen Zwillingsgraphen aus digitalen Zwillingen und Beziehungen zu erstellen.

### <a name="set-up-the-runnable-sample"></a>Einrichten des ausführbaren Beispiels

Der Codeausschnitt verwendet die Modelldefinitionen [*Room.json*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) und [*Floor.json*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) aus dem [*Tutorial: Erkunden von Azure Digital Twins mit einer Beispielclient-App*](tutorial-command-line-app.md). Sie können diese Links verwenden, um direkt zu der Datei zu wechseln, oder sie als Teil des vollständigen End-to-End-Beispielprojekts [hier](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) herunterladen. 

Bevor Sie das Beispiel ausführen, gehen Sie wie folgt vor:
1. Laden Sie die Modelldatei herunter, platzieren Sie sie in Ihrem Projekt, und ersetzen Sie den Platzhalter `<path-to>` im Code unten, um Ihrem Programm mitzuteilen, wo sie zu finden sind.
2. Ersetzen Sie den Platzhalter `<your-instance-hostname>` durch den Hostnamen Ihrer Azure Digital Twins-Instanz.
3. Fügen Sie die folgenden Pakete zu Ihrem Projekt hinzu:
    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

Sie müssen auch lokale Anmeldeinformationen einrichten, wenn Sie das Beispiel direkt ausführen möchten. Im nächsten Abschnitt werden diese Schritte erläutert.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Ausführen des Beispiels

Nachdem Sie die obigen Schritte ausgeführt haben, können Sie den folgenden Beispielcode direkt ausführen.

```csharp 
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {

        public static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");

            //Create the Azure Digital Twins client for API calls
            DigitalTwinsClient client = createDTClient();
            Console.WriteLine($"Service client created – ready to go");
            Console.WriteLine();

            //Upload models
            Console.WriteLine($"Upload models");
            Console.WriteLine();
            string dtdl = File.ReadAllText("<path-to>/Room.json");
            string dtdl1 = File.ReadAllText("<path-to>/Floor.json");
            var typeList = new List<string>();
            typeList.Add(dtdl);
            typeList.Add(dtdl1);
            // Upload the models to the service
            await client.CreateModelsAsync(typeList);

            //Create new (Floor) digital twin
            BasicDigitalTwin floorTwin = new BasicDigitalTwin();
            string srcId = "myFloorID";
            floorTwin.Metadata = new DigitalTwinMetadata();
            floorTwin.Metadata.ModelId = "dtmi:example:Floor;1";
            //Floor twins have no properties, so nothing to initialize
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(srcId, floorTwin);
            Console.WriteLine("Twin created successfully");

            //Create second (Room) digital twin
            BasicDigitalTwin roomTwin = new BasicDigitalTwin();
            string targetId = "myRoomID";
            roomTwin.Metadata = new DigitalTwinMetadata();
            roomTwin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            roomTwin.Contents = props;
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(targetId, roomTwin);
            
            //Create relationship between them
            await CreateRelationship(client, srcId, targetId, "contains");
            Console.WriteLine();

            //Print twins and their relationships
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Delete the relationship
            Console.WriteLine("Deleting the relationship");
            await DeleteRelationship(client, srcId, $"{srcId}-contains->{targetId}");
            Console.WriteLine();

            //Print twins and their relationships again
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();
        }

        private static DigitalTwinsClient createDTClient()
        {
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
        }
        private async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            // Create relationship between twins
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync(srcId, relId, relationship);
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }

        }

        private static async Task FetchAndPrintTwinAsync(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }

        private static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");
                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
    }
}
```

Hier ist die Konsolenausgabe des obigen Programms: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Konsolenausgabe, die die Zwillingsdetails, ein- und ausgehende Beziehungen der Zwillinge anzeigt." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> Der Zwillingsgraph ist ein Konzept zur Erstellung von Beziehungen zwischen Zwillingen. Wenn Sie sich die visuelle Darstellung des Zwillingsgraphen ansehen möchten, lesen Sie den Abschnitt [_Visualisierung*](how-to-manage-graph.md#visualization) dieses Artikels. 

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>Erstellen eines Zwillingsgraphen aus einem Arbeitsblatt

In praktischen Anwendungsfällen werden Zwillingshierarchien oft aus Daten erstellt, die in einer anderen Datenbank oder vielleicht in einem Arbeitsblatt gespeichert sind. In diesem Abschnitt wird veranschaulicht, wie ein Arbeitsblatt analysiert werden kann.

Betrachten Sie die folgende Datentabelle, in der eine Reihe von digitalen Zwillingen und die zu erstellenden Beziehungen beschrieben werden.

| Modell-ID| Zwillings-ID (muss eindeutig sein) | Beziehungsname | Zielzwillings-ID | Initialisierungsdaten des Zwillings |
| --- | --- | --- | --- | --- |
| dtmi:example:Floor;1 | Floor1 (Etage1) |  contains | Room1 |{"Temperature": 80, "Humidity": 60}
| dtmi:example:Floor;1 | Floor0 (Etage0) |  has      | Room0 |{"Temperature": 70, "Humidity": 30}
| dtmi:example:Room;1  | Room1 | 
| dtmi:example:Room;1  | Room0 |

Der folgende Code verwendet die [Microsoft Graph-API](/graph/overview), um ein Arbeitsblatt zu lesen und aus den Ergebnissen einen Azure Digital Twins-Zwillingsgraphen zu konstruieren.

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string modelId = row[0].GetString();
    string sourceId = row[1].GetString();
    string relName = row[2].GetString();
    string targetId = row[3].GetString();
    string initData = row[4].GetString();
    
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (sourceId != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = sourceId,
            TargetId = targetId,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Contents = initData;
    // Set the type of twin to be created
    twin.Metadata = new DigitalTwinMetadata() { ModelId = modelId };
    
    try
    {
        await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(sourceId, twin);
    }
    catch (RequestFailedException e)
    {
       Console.WriteLine($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            await client.CreateOrReplaceRelationshipAsync(rec.sourceId, Guid.NewGuid().ToString(), rec);
        }
        catch (RequestFailedException e)
        {
            Console.WriteLine($"Error {e.Status}: {e.Message}");
        }
    }
}
```
## <a name="manage-relationships-with-cli"></a>Verwalten von Beziehungen mit der CLI

Zwillinge und ihre Beziehungen können auch über die Azure Digital Twins-Befehlszeilenschnittstelle (CLI) verwaltet werden. Die Befehle finden Sie unter [*Vorgehensweise: Verwenden der Azure Digital Twins-Befehlszeilenschnittstelle*](how-to-use-cli.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Abfrage eines Azure Digital Twins-Zwillingsgraphen:
* [*Konzepte: Abfragesprache*](concepts-query-language.md)
* [*Verwenden Abfragen des Zwillingsgraphen*](how-to-query-graph.md)