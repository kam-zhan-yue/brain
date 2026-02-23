```
service_name:"workforce-fg" 
attributes.workspace:* 
attributes.http.route:/.*api\/v<v2:version>\/tasks\/\(\?P<pk>\[\^\/.\]\+\)\/status.*/
```

Seems like transition is the main one that is used: https://arafire.onuptick.com/tasks/tasks/1210875/view/
Then it is status: used in https://esmcompliance.onuptick.com/contractorportal/416/tasks/94897/technician/