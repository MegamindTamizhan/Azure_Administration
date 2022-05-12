# Log Analytics Heartbeat Table

Right off the bat, the Heartbeat table provides useful information. It provides Computer FQDN, Agent type, OSType, OS Version for Major and Minor, Subscription, ResourceGroup, ResourceProvider, ResourceId, ResourceType, ComputerEnvironment, Solutions, and TenantId.

    Heartbeat 
    | where Computer contains "prod" 
    | distinct Computer

# C Drive Free Space (Less than 20%)

    Perf
    | where CounterName == "% Free Space"
    | where Computer == "Computer Name" and InstanceName == "C:" 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    | sort by TimeGenerated desc

