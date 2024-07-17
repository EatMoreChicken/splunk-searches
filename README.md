# Splunk Search

A repository of Splunk searches that can be used as a starting point for your own searches.

## Basic Notes About Splunk Searches
### Structure of Splunk Searches

### Optimizing Searches

Not all searches in this repo has been optimized for performance. Treat these searches are a starting point or inspo for your own searches. Every environment is different, so more likely than not, you'll need to tweak these searches to fit your environment. You can further optimize from what's in this repo. If you find any nice optimizations, feel free to submit a PR. 🙂

Here are some general tips for optimizing searches:
- **Narrow the search window**: Limitting the timeframe usually makes the biggest impact on search performance.
- **Specify index names**: It's usually okay to use something like `index=*` over a short timeframe to find data that you are looking for. Once you've found the data, limit the search down to the specific index(es). For example: `index=main` or `index IN (main, os, network)`.
- **Use unique terms**: ==TODO==
- **Avoid leading wildcards**: Leading wildcards, like `AccountName=*-svc`, tends to cause performance issues. You can read more about it [here](https://hurricanelabs.com/splunk-tutorials/why-you-should-never-use-leading-wildcards-in-splunk-searches/).
- **Report Acceleration**: ==TODO==
- **Summary Indexing**: This is a more complex topic, but when you have large datasets that you need to aggregate, summary indexing can be a good way to speed up searches. You can read more about it [here](https://kinneygroup.com/blog/splunk-summary-indexing/).
- **Remove unnecessary fields**: ==TODO==
- **Using directives**: ==TODO== (`CASE`, `TERM`, `REQUIRED_TAGS`, `REQUIRED_EVENTTYPES`, etc.)
- **Streaming vs Distributable**: ==TODO==
- **Use `tstats`**: ==TODO==
- **Use `metadata`**: ==TODO==
- **Leverage the Job Inspector**: ==TODO==
- **Run searches during off-peak hours**: Even after optimizing your searches, there can be instances where the search simply takes a long time to run. In these cases, it might be a good idea to run these searches during off-peak hours to reduce the impact on the system. Reference the following section for a search to find your peak hours: [Find Peak Hours](#find-peak-hours). (==TODO==)

## Internal Splunk Searches

These are searches that can be used to monitor Splunk itself.

### Get Data Throughput
Purpose: See how much of your data throughput is being used by different indexes, sourcetypes, sources, and hosts. This is also useful for monitoring if specific data is being indexed by Splunk.

There are some different variations of this search depending on what group you want to split by. These are the common thruput groups:
- `per_index_thruput`
- `per_sourcetype_thruput`
- `per_source_thruput`
- `per_host_thruput`

If you choose the `per_index_thruput` group, splitting by the `series` field will give you the throughput for each index. If you choose the `per_sourcetype_thruput` group, splitting by the `series` field will give you the throughput for each sourcetype and so on.

#### Split by Index

```spl
index=_internal sourcetype=splunkd source=*metrics.log component=Metrics group=per_index_thruput series=* 
| rename series as index
| timechart span=1h sum(kb) as kb by index limit=20
```

#### Split by Sourcetype

```spl
index=_internal sourcetype=splunkd source=*metrics.log component=Metrics group=per_sourcetype_thruput series=*
| rename series as sourcetype
| timechart span=1h sum(kb) as kb by sourcetype limit=20
```

#### Split by Source

```spl
index=_internal sourcetype=splunkd source=*metrics.log component=Metrics group=per_source_thruput series=*
| rename series as source
| timechart span=1h sum(kb) as kb by source limit=20
```

#### Split by Host

```spl
index=_internal sourcetype=splunkd source=*metrics.log component=Metrics group=per_host_thruput series=*
| rename series as host
| timechart span=1h sum(kb) as kb by host limit=20
```

## Additional Resources:
- https://github.com/splunk/security_content
- https://gosplunk.com/