# Azure Webjob

## appsetting.json configuration file should include
```
- AzureWebJobsStorage
- AzureWebJobsDashboard
```

## Pathes for webjobs withing web application root project
```
- App_Data/jobs/triggered/ApiWebJobReminders/
- App_Data/jobs/continuous/ApiWebJobReminders/
```

## Remarks
* Continuous and scheduled CRON webjobs require 'Always on' to be enabled for your app.


