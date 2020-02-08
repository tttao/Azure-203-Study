# Create table

```cs
string storageConnectionString = AppSettings.LoadAppSettings().StorageConnectionString;
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);
CloudTableClient tableClient = storageAccount.CreateCloudTableClient(new TableClientConfiguration());
CloudTable table = tableClient.GetTableReference(tableName);
```

# Define entity

```cs
namespace CosmosTableSamples.Model
{
    using Microsoft.Azure.Cosmos.Table;
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity()
        {
        }

        public CustomerEntity(string lastName, string firstName)
        {
            PartitionKey = lastName;
            RowKey = firstName;
        }

        public string Email { get; set; }
        public string PhoneNumber { get; set; }
    }
}
```

# Insert or update

```cs
TableOperation insertOrMergeOperation = TableOperation.InsertOrMerge(entity);
TableResult result = await table.ExecuteAsync(insertOrMergeOperation);
CustomerEntity insertedCustomer = result.Result as CustomerEntity;
```

# Insert or update (batch)

```cs
TableBatchOperation batchOperation = new TableBatchOperation();
batchOperation.insertOrReplace(entity1);
batchOperation.insertOrReplace(entity2);
TableBatchResult results = await table.ExecuteBatchAsync(batchOperation);
```

# Get By PartitionKey / RowKey

```cs
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>(partitionKey, rowKey);
TableResult result = await table.ExecuteAsync(retrieveOperation);
CustomerEntity customer = result.Result as CustomerEntity;
```

# Range scan

```cs
TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
    TableOperators.And,
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual,
            startRowKey),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThanOrEqual,
            endRowKey))));

foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
{
    Console.WriteLine("Customer: {0},{1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey, entity.Email,
        entity.PhoneNumber);
}
```               

# Delete

```cs
TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
TableResult result = await table.ExecuteAsync(deleteOperation);
```

https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started

