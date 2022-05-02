# Log Analytic Query

// Session duration 
// Lists users by session duration in the last 24 hours. 
// The "State" provides information on the connection stage of an actitivity.
// The delta between "Connected" and "Completed" provides the connection time for a specific connection.
WVDConnections  
| where State == "Connected"  
| project CorrelationId , UserName, SessionHostName, ConnectionType , StartTime=TimeGenerated  
| join (WVDConnections  
    | where State == "Completed"  
    | project EndTime=TimeGenerated, CorrelationId)  
    on CorrelationId  
| project StartTime, EndTime, Duration = EndTime - StartTime, UserName, ConnectionType, SessionHostName
| sort by Duration desc