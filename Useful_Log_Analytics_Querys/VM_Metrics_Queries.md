# Log Analytics Heartbeat Table

Right off the bat, the Heartbeat table provides useful information. It provides Computer FQDN, Agent type, OSType, OS Version for Major and Minor, Subscription, ResourceGroup, ResourceProvider, ResourceId, ResourceType, ComputerEnvironment, Solutions, and TenantId.

    Heartbeat 
    | where Computer contains "Computer Name" 
    | distinct Computer

# C Drive Free Space (Less than 20%) - Alerts

    Perf
    | where CounterName == "% Free Space"
    | where Computer == "Computer Name" and InstanceName == "C:" 
    | summarize AggregatedValue = avg(CounterValue) by Computer, bin(TimeGenerated, 1h)
    | sort by TimeGenerated desc

# Windows Memory Usage (Less than 0.8 - If 4GB RAM ((4GBx80%)- 4GB)) - ComputerName Memory Usage Over 80%

    Perf
    | where ObjectName == "Memory" and
    (CounterName == "Available MBytes Memory" or // the name used in Linux records
    CounterName == "Available MBytes") // the name used in Windows records
    | where Computer == "Computer Name"
    |  summarize max(CounterValue/1000) by bin(TimeGenerated, 5min), Computer, _ResourceId // bin is used to set the time grain to 15 minutes
    | sort by TimeGenerated desc

# Windows Disk IOPS
    
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

# Linux Drive Free Space (Less than 10%) - ComputerName Disk Usage Exceeds 90%

    Perf 
    | where ObjectName == "Logical Disk" and CounterName == "% Used Space" 
    | where Computer == "Computer Name" 
    | summarize arg_max(TimeGenerated, *) by Computer, bin(TimeGenerated, 1h)
    | extend free_space=100-CounterValue
    | summarize AggregatedValue = avg(free_space) by Computer, bin(TimeGenerated, 1h)

# Linux CPU Usage (Greater than 80%) - ComputerName CPU Usage Over 80%

    Perf
    | where (CounterName == "% Processor Time")
    | where Computer == "Computer Name"
    | summarize AggregatedValue = percentile(CounterValue,85) by Computer , bin(TimeGenerated, 5min)
    | sort by TimeGenerated desc

# Linux Memory Usage (Less than 0.8 - If 4GB RAM ((4GBx80%)- 4GB)) - ComputerName Memory Usage Over 80%

    Perf
    | where ObjectName == "Memory" and
    (CounterName == "Available MBytes Memory" or // the name used in Linux records
    CounterName == "Available MBytes") // the name used in Windows records
    | where Computer == "LFGSFTP"
    |  summarize max(CounterValue/1000) by bin(TimeGenerated, 5min), Computer, _ResourceId // bin is used to set the time grain to 15 minutes
    | sort by TimeGenerated desc

# Linux Disk IOPS - (Disk Read operations above 450 and Disk write operations above 450)

    Selected signal: Disk Read Operations/Sec (Platform)
    Selected signal: Disk Write Operations/Sec (Platform)





