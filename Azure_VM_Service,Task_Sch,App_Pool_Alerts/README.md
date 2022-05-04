# IIS Application Pool Stopped Event Capture using Azure Log Analytic Query

  Add Windows Event Log in Log Analytic (Agent Configuration)
   - Microsoft-IIS-Configuration/Administrative
   - Microsoft-IIS-Configuration/Operational    

    Event
    | where EventID == 29
    | where EventData has "Stopped"
    | where RenderedDescription has "DefaultAppPool"
    | sort by TimeGenerated desc
    | where Computer == "%Computer Name%"

# Azure VM Task Schedular Events Capture using Azure Log Analytic Query

  Add Windows Event Log in Log Analytic (Agent Configuration)
  
   - Microsoft-Windows-TaskScheduler/Operational
    
  Task_Failed_Possibilities

    203	Action failed to start
    101	Task Start Failed 
    311	Task Engine failed to start
    103	Action start failed
    111	Task terminated

  Log Analytic Query (eg. calc)
    
    Event
    | where EventID == 102
    | where RenderedDescription has "Calc_New"
    ------------------------------------------------
    Event
    | where EventID in (203, 101, 311, 103)
    | where RenderedDescription has "Calc_New"

# Azure VM Services Stopped Alert Creation using Log Analytic Query

    Event
    | where RenderedDescription has "stopped" and EventLog  == "System" and EventID ==7036 and Source == "Service Control Manager" 
    | parse kind=relaxed EventData with * '<Data Name="param1">' Windows_Service_Name '</Data><Data Name="param2">' Windows_Service_State '</Data>'*
    | where Windows_Service_Name =="Print Spooler"
    | sort by TimeGenerated desc
    | project Computer, Windows_Service_Name, Windows_Service_State, TimeGenerated




