# Route 53
Amazon Route 53 is a Domain Name System (DNS) web service. There are three main functions:
- Registering Domain Names: Attaching names to websites
- DNS Routing: When a user opens a web browser and enters your domain name or subdomain name, Route 53 helps connect the browser with your website or web application
- Health Checking: Route 53 sends automated requests over the Internet to a resource, such as a web server, to verify that it's reachable, available, and functional

# CloudFront
A CDN that delivers static content to users. When a user requests content you're serving with CloudFront, the request is routed to the edge location that provides the lowest latency so that content is delivered ASAP.
- If the content is already in the edge location with the lowest latency, CloudFront delivers it immediately
- If the content is not in that edge location, CloudFront retrieves it from an origin that you've defined, such as an Amazon S3 bucket, a MediaPackage channel, or an HTTP server.

As an example, suppose that you're serving an image from a traditional web server, not from CloudFront. This might be https://example.com/sunsetphoto.png. Users can navigate to this URL, but their request is routed from one network to another until it is found.

CloudFront speeds up the distribution of your content by routing each request through the AWS backbone network to the edge location that can best serve your content. This dramatically reduces the number of networks that you users' request must pass through, which improves performance. Users hence get lower latency and higher data transfer rates.

### Caching and Availability
- CloudFront caching allows more objects to be served from CloudFront edge locations, which are closer to your users
- This reduces the load on your origin server and reduces latency
- The more requests that CloudFront can serve from edge caches, the fewer viewer requests that CloudFront must forward to your origin to get the latest version or a unique version of an object. To optimise CloudFront to make as few requests to your origin as possible, you can setup a CloudFront Origin Shield
- The proportion of requests that are served directly from the CloudFront cache compared to all requests is called the _cache hit ratio_. You can view the percentage of viewer requests that are hits, misses, and errors in the CloudFronconsole.
