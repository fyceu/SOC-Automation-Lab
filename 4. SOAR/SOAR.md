

```xml 
<!-- Integration with Shuffle -->
<integration>
  <name>shuffle</name>
  <hook_url>https://shuffler.io/api/v1/hooks/webhook_1519b2a8-089e-4654-a94f-30839373e853 </hook_url>
  <rule_id>100002</rule_id>
  <alert_format>json</alert_format>
</integration>
```

Read more about Wazuh configuration to APIs: 
- https://documentation.wazuh.com/current/user-manual/reference/ossec-conf/integration.html
- https://docs.strangebee.com/thehive/api-docs/#operation/Create%20case
- https://documentation.wazuh.com/current/user-manual/manager/integration-with-external-apis.html
- https://wazuh.com/blog/integrating-wazuh-with-shuffle/