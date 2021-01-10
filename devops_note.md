## Devops

### How will you approach a network issue
- Internal network  --> NAT box --> External network
  - Check DNS is working
  - Check Service is up
  - Check routing
- Command: `dig external_service_dns`
- Check /etc/resolv.conf
- Command: `traceroute external_service_dns`
- Command: `telnet external_service_dns port`
