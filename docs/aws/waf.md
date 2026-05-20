AWS Web Application Firewall (WAF) is a web application firewall that lets you monitor the HTTP(S) requests that are forwarded to your protected web application resources. This can be applied to:
- Application Load Balancer
- AWS API Gateway REST API
- CloudFront Distribution
- etc

AWS WAF lets you control access to your content. Based on criteria that you specify, such as the IP addresses that requests originate from or the values of query strings, the service associated with your protected resource responds to requests either with the requested content, or with a HTTP 403 status code.

## Web ACLs
You use a web access control list (web ACL) to protect a set of AWS resources. You create a web ACL and define its protection strategy by adding rules.
- Rules define criteria for inspecting web requests
- Rules specify the action to take on requests that match the criteria
- You can configure rules to block matching requests, allow them through, count them, or run bot controls against them that use CAPTCHA puzzles or silent browser challenges.
A rule is not an AWS WAF resource. It only exists in the context of a protection pack (web ACL) or rule group.