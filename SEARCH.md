# Create index

Create SearchServiceClient

```java
IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
IConfigurationRoot configuration = builder.Build();

string searchServiceName = configuration["SearchServiceName"];
string adminApiKey = configuration["SearchServiceAdminApiKey"];
SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

Create Index

```
var definition = new Index()
{
    Name = indexName,
    Fields = FieldBuilder.BuildForType<Hotel>()
};

serviceClient.Indexes.Create(definition);
```

# Upload documents

Upload Documents (IndexBatch.Upload + SearchIndexClient.Documents.Index)

```java
SearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);

var batch = IndexBatch.Upload(hotels);

try
{
    indexClient.Documents.Index(batch);
}
catch (IndexBatchException e)
{
    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
    // the batch. Depending on your application, you can take compensating actions like delaying and
    // retrying. For this simple demo, we just log the failed document keys and continue.
    Console.WriteLine(
        "Failed to index some of the documents: {0}",
        String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
}
```

# Run Queries

Create SearchIndexClient for querying

```java
string searchServiceName = configuration["SearchServiceName"];
string queryApiKey = configuration["SearchServiceQueryApiKey"];

SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
```

Run queries

```java
SearchParameters parameters =
    new SearchParameters()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)",
        Select = new[] { "HotelId", "Description" }
    };

DocumentSearchResult<Hotel> results = indexClient.Documents.Search<Hotel>("*", parameters);
```