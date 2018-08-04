I get general superstitous claims that DNS TTLs "don't work" on a regular
basis.

From `https://news.ycombinator.com/item?id=17553043 <https://news.ycombinator.com/item?id=17553043>`_
by Colm MacCárthaigh:

    AWS engineer here, I was lead for Route 53.

    We generally use 60 second TTLs, and as low as 10 seconds is very common.
    There's a lot of myth out there about upstream DNS resolvers not honoring
    low TTLs, but we find that it's very reliable. We actually see faster
    convergence times with DNS failover than using BGP/IP Anycast. That's
    probably because DNS TTLs decrement concurrently on every resolver with the
    record, but BGP advertisements have to propagate serially
    network-by-network. The way DNS failover works is that the health checks
    are integrated directly with the Route 53 name servers. In fact every name
    server is checking the latest healthiness status every single time it gets
    a query. Those statuses are basically a bitset, being updated /all/ of the
    time. The system doesn't "care" or "know" how many health status change
    each time, it's not delta-based. That's made it very very reliable over the
    years. We use it ourselves for everything.

    Of course the downside of low TTLs is more queries, and we charge by the
    query unless you ALIAS to an ELB, S3, or CloudFront (then the cost of the
    queries is on us).
