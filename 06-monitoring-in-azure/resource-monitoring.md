# Resource Monitoring

- Almost every resouce in Azure has a "Monitoring" section, with items such as:
    - Insights: variuos insights about the resource
    - Alerts: alerts to be triggered when something happens, eg. CPU utiliztion goes above a specific value
    - Metrics
    - Diagnostic settings
    - Logs

## Alerts

- With alerts we can get notifications about resouces in our Azure subscription
- Examples:
    - VM's CPU goes above 90%
    - More then 20% of requests fail
    - Server error occurs in the last hour

### Alert Components

- Components are:
    - Conditions: when to trigger the alert
    - Actions: what to do when the alert is triggered. Usually when an alert is triggered, we send a notifications. Notifications are sent to Action Groups
    - Details: Contents of the notification, what information to be included in the alert

### Alert Pricing

- Per metrics measured: $ 0.1 / metric / month
- Per notification type:
    - First 1k email, 1k push notification, 100k web hooks, 100 SMS in the US are included for free for each month
- All in all - quite cost effective

## Logs

- Almost every Azure resouce generates logs
- Logs might contain data about performance, events, errors, etc.
- Log records need central repositoru to be stored an viewed => this is the Log Analytics Workspace

### Log Analytics Workspace

- It is a centralized location for storing, organizing and analyzing logs
- It aggregates logs from all connected, monitored resources
- It uses specialized query language to query logs (Kusto)
- It is located in a designated region => recommended to be in the same region as the resources to save costs