# CloudWatch

## Logs Insights

### Log query examples

Get all log entries with certain Kubernetes pod name and which log message contain the string "error"

```
fields @timestamp, @message
| filter kubernetes.container_name='$CONTAINER_NAME'
| filter @message like "error"
| sort @timestamp desc
```

