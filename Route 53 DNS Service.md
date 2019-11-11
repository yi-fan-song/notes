---
tags: [Notebooks/Amazon Web Services, Route 53]
title: Route 53 DNS Service
created: '2019-11-11T02:54:26.130Z'
modified: '2019-11-11T02:57:55.566Z'
---

# Route 53 DNS Service
Contains simplified documentation on how to use AWS Route 53

## Use Route 53 for an existing domain
Migrate DNS service before registrar to avoid downtime

### Migrating a high-traffic domain
[AWS doc](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/migrate-dns-domain-in-use.html)

1. Get DNS Config from current DNS Service Provider
1. Create Hosted Zone
1. Create Records
1. Lower TTL
1. Wait for TTL to expire
1. Update NS record with new DNS Service Provide
1. Monitor traffic
1. Update Domain registration to use Route 53 NS
1. Change TTL back to normal
1. Transfer Domain Registration to Route 53

### Migrating an inactive domain
[AWS doc](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/migrate-dns-domain-inactive.html)

1. Get DNS Config from current DNS Service Provider
2. Create Hosted Zone
3. Create Record
4. Update Domain registration to use Route 53 NS
5. Transfer Domain registration to Route 53
