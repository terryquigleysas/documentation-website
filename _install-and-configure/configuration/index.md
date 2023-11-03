---
layout: default
title: Configuring OpenSearch
nav_order: 10
has_children: true
redirect_from:
  - /install-and-configure/configuration/
  - /opensearch/configuration/
---

# Configuring OpenSearch

You can configure dynamic OpenSearch settings through the [Cluster settings API]({{site.url}}{{site.baseurl}}/api-reference/cluster-api/cluster-settings/). Certain operations are static and require you to modify `opensearch.yml` and restart the cluster.

Whenever possible, use the Cluster Settings API; `opensearch.yml` is local to each node, whereas the API applies the setting to all nodes in the cluster. Certain settings, however, require `opensearch.yml`. In general, these settings relate to networking, cluster formation, and the local file system. To learn more, see [Cluster formation]({{site.url}}{{site.baseurl}}/opensearch/cluster/).

## Specify settings as environment variables

You can specify environment variables as arguments using `-E` when launching OpenSearch:

```bash
./opensearch -Ecluster.name=opensearch-cluster -Enode.name=opensearch-node1 -Ehttp.host=0.0.0.0 -Ediscovery.type=single-node
```

## Update cluster settings using the API

The first step in changing a setting is to view the current settings:

```
GET _cluster/settings?include_defaults=true
```

For a more concise summary of non-default settings:

```
GET _cluster/settings
```

Three categories of setting exist in the cluster settings API: persistent, transient, and default. Persistent settings, well, persist after a cluster restart. After a restart, OpenSearch clears transient settings.

If you specify the same setting in multiple places, OpenSearch uses the following precedence:

1. Transient settings
2. Persistent settings
3. Settings from `opensearch.yml`
4. Default settings

To change a setting, specify the new one as either persistent or transient. This example shows the flat settings form:

```json
PUT _cluster/settings
{
  "persistent" : {
    "action.auto_create_index" : false
  }
}
```

You can also use the expanded form, which lets you copy and paste from the GET response and change existing values:

```json
PUT _cluster/settings
{
  "persistent": {
    "action": {
      "auto_create_index": false
    }
  }
}
```

---

## Configuration file

You can find `opensearch.yml` in `/usr/share/opensearch/config/opensearch.yml` (Docker) or `/etc/opensearch/opensearch.yml` (most Linux distributions) on each node.

You can edit the `OPENSEARCH_PATH_CONF=/etc/opensearch` to change the config directory location. This variable is sourced from `/etc/default/opensearch`(Debian package) and `/etc/sysconfig/opensearch`(RPM package).

If you set your customized `OPENSEARCH_PATH_CONF` variable, be aware that other default environment variables will not be loaded.

You don't mark settings in `opensearch.yml` as persistent or transient, and settings use the flat form:

```yml
cluster.name: my-application
action.auto_create_index: true
compatibility.override_main_response_version: true
```

A standard Docker download stores the `opensearch_dashboards.yml` file in the following location: `/usr/share/opensearch-dashboards/config/opensearch_dashboards.yml`.

The demo configuration includes a number of [settings for the Security plugin]({{site.url}}{{site.baseurl}}/install-and-configure/configuration/security-settings/) that you should modify before using OpenSearch for a production workload. To learn more, see [Security]({{site.url}}{{site.baseurl}}/security/).

### (Optional) CORS header configuration

If you are working on a client application running against an OpenSearch cluster on a different domain, you can configure headers in `opensearch.yml` to allow for developing a local application on the same machine. Use [Cross Origin Resource Sharing](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) so your application can make calls to the OpenSearch API running locally. Add the following lines in your `custom-opensearch.yml` file (note that the "-" must be the first character in each line).
```yml
- http.host:0.0.0.0
- http.port:9200
- http.cors.allow-origin:"http://localhost"
- http.cors.enabled:true
- http.cors.allow-headers:X-Requested-With,X-Auth-Token,Content-Type,Content-Length,Authorization
- http.cors.allow-credentials:true
```