# Log Analytics Heartbeat Table

Right off the bat, the Heartbeat table provides useful information. It provides Computer FQDN, Agent type, OSType, OS Version for Major and Minor, Subscription, ResourceGroup, ResourceProvider, ResourceId, ResourceType, ComputerEnvironment, Solutions, and TenantId.

    Heartbeat 
    | where Computer contains "prod" 
    | distinct Computer

# C Drive Free Space (Less than 20%) - Alerts

    Perf
    | where CounterName == "% Free Space"
    | where Computer == "Computer Name" and InstanceName == "C:" 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    | sort by TimeGenerated desc

# Memory Usage

    Perf
    | where ObjectName == "Memory" and
    (CounterName == "Available MBytes Memory" or // the name used in Linux records
    CounterName == "Available MBytes") // the name used in Windows records
    | where Computer == "Computer Name"
    |  summarize max(CounterValue/1000) by bin(TimeGenerated, 5min), Computer, _ResourceId // bin is used to set the time grain to 15 minutes
    | sort by TimeGenerated desc

# Disk IOPS
    
    Selected signal: LogicalDisk\Disk Reads/sec (azure.vm.windows.guestmetrics) - Read Operation
    Selected signal: LogicalDisk\Disk Writes/sec (azure.vm.windows.guestmetrics) - Write Operation

    Selected signal: OS Disk Read Operations/Sec (Platform)
    Selected signal: OS Disk Write Operations/Sec (Platform)

# VM_Unexpected_Shutdown

    Event
    | where EventLog == "System"
    | where EventID == "6008"
    | where Computer == "Computer Name"

# VM_Restart_Shutdown

    Event
    | where EventLog == "System"
    | where EventID == "1074"
    | where Computer == "Computer Name"

# Create or Update NetworkSecurityGroup Rule

    Selected signal: Create or Update Network Security Group (Microsoft.Network/networkSecurityGroups) 

# Create or Update Security Rule's Rule

    Selected signal: Create or Update Security Rule (networkSecurityGroups/securityRules)







