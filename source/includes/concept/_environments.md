## Environments

Certain RW API resources, like datasets, layers, or widgets, use the concept of `environment`, or `env`, as a way to help you manage your data's lifecycle. The main goal of `environments` is to give you an easy way to separate data that is ready to be used in production-grade interactions from data that is still being improved on.

When you create a new resource, like a dataset, it's given the `production` env value by default. Similarly, if you list resources, there's an implicit default filter that only returns resourcesw whose `env` value is `production`. This illustrates two key concepts of `environments`:

- By default, when you create data on the RW API, it assumes it's in a production-ready state.
- By default, when you list resources from the RW API, it assumes you want only to see production-ready data.

However, you may want to modify this behavior. For example, let's say you want to create a new widget on the RW API and experiment with different configuration options without displaying it publicly. To achieve this, you can set a different `environment` on your widget - for example, `test`. Or you may want to deploy a staging version of your application that also relies on the same RW API but displays a different set of resources. You can set those resources to use the `staging` environment and have your application load only that environment, or load both `production` and `staging` resources simultaneously. Keep in mind that `production` is the only "special" value for the `environment` field. Apart from it, the `environment` can take any value you want, without having any undesired side-effects.

Resources that use `environment` can also be updated with a new `environment` value, so you can use it to control how your data is displayed. Refer to the documentation of each resource to learn how you can achieve this.

It's worth pointing out that endpoints that address a resource by id typically don't filter by `environment` - mostly only listing endpoints have different behavior depending on the requested `environment` value. Also worth noting is that this behavior may differ from resource to resource, and you should always refer to each endpoint's documentation for more details.

In a nutshell, this means that:

- When creating one of these resources, it will be set with the `production` environment, unless specified otherwise by you.
- When listing these resources, the list will only show elements with the `production` environment, unless you explicitly filter by a different value
- You may update a resource's environment using the corresponding update endpoint (there are special cases to this, please refer to the specific resource documentation)
- Endpoints that address a resource by id, like fetching by id, updating or deleting, do not filter by environment.

### Which services support environments

The following services/resources comply with the above guidelines:

* [Dataset](reference.html#dataset)
* [Graph](reference.html#graph)
* [Layer](reference.html#layer)
* [Subscriptions](reference.html#subscriptions)
* [Widgets](reference.html#widget)
* [Areas v2](reference.html#areas-v2)
* [Collections](reference.html#collections)
* [Topic](reference.html#topic)
* [Dashboard](reference.html#dashboard)
* FAQ, Tools, Static pages and Partners (not documented yet, part of the [Resource Watch manager](https://github.com/resource-watch/resource-watch-manager/) microservice)

### Including related resources with environments

Some of the resources above link to each other (for example, datasets may have layers and widgets associated with them), and have a convenient `includes` optional query parameter, that allows you to load said associated resources in a single API call. When fetching a list of these resources and their associated (included) resources while using an environment filter, the resource list will be filtered by environment, but the included resources will not - so, for example, you may get a list of datasets filtered by the `production` environment, but it may include layers and/or widgets from other environments.

In these situations, if you want to also filter the included resources by the same `env` value, you can provide the optional `filterIncludesByEnv=true` query parameter.

### References to resources with environments

The [Vocabulary](reference.html#vocabulary) and [Graph](reference.html#graph) services fundamentally serve to create associations around resources, either between themselves or with tags. At their core, these services do not use the concept of `environment`, but the result of some of their endpoints are lists of resources like datasets, layers or widgets, which do have `environment`.

When using these endpoints, you can provide an optional `env` query parameter filter, that will be applied to the list of [environment-aware resources](concepts.html#which-services-comply-with-these-guidelines) returned by those endpoints. Note that, unlike listing resources using their "native" endpoints, these endpoints do not default to filtering the result set by the `production` environment - by default, they will not apply any `env` filtering, and will return resources from all environments.
