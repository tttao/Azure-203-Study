Send Message

```java
topicClient = new TopicClient(ServiceBusConnectionString, TopicName);
topicClient.SendAsync(message);
```

Subscribe / No filter

```java
subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, allMessagesSubscriptionName);
// Just to demonstrate usage of TrueFilter
await allMessagessubscriptionClient.RemoveRuleAsync(RuleDescription.DefaultRuleName);
await allMessagessubscriptionClient.AddRuleAsync(new RuleDescription(RuleDescription.DefaultRuleName, new TrueFilter()));
```

Sql Filter / With optional action

```java
await sqlFilterOnlySubscriptionClient.RemoveRuleAsync(RuleDescription.DefaultRuleName);
await sqlFilterOnlySubscriptionClient.AddRuleAsync(new RuleDescription
{
    Filter = new SqlFilter("Color = 'Red'"),
    Action = new SqlRuleAction("SET Color = 'BlueProcessed'"),
    Name = "RedSetToBlueSqlRule"
});
```

Correlation filter
```java
await correlationFilterSubscriptionClient.RemoveRuleAsync(RuleDescription.DefaultRuleName);
await correlationFilterSubscriptionClient.AddRuleAsync(new RuleDescription
{
    Filter = new CorrelationFilter() { Label = "Red", CorrelationId = "important" },
    Name = "ImportantCorrelationRule"
});
```
