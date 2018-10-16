# Internal and External Packages for Kubernetes

Many people always come to confused about `Internal` and/or `External` packages refered in codebase or documents. I am writing an article to illustrate what the difference and how to use these two concepts. 

## Overview kubernetes codebase
In general, `Internal package` referes to the `k8s.io/kubernetes` repo, (A.K.A. `K/K`) which is the main repo of kubernetes projects, holding the project framework, componets and core types and logics.

`External pakcage` refers to anything that lives under `staging` (of `k8s.io/kubernetes`). These packages are synced to independent repos with the same name under `github.com/kubernetes`.
Like:
- k8s.io/api
- k8s.io/apiextensions-apiserver
- k8s.io/apimachinery
- k8s.io/apiserver
- k8s.io/cli-runtime
- k8s.io/client-go
- k8s.io/cloud-provider
- k8s.io/cluster-bootstrap
- k8s.io/code-generator
- k8s.io/csi-api
- k8s.io/kube-aggregator
- k8s.io/kube-controller-manager
- k8s.io/kube-proxy
- k8s.io/kube-scheduler
- k8s.io/kubelet
- k8s.io/metrics

## Deep dive in internal and external

General explainantion above is sufficient for most of the cases when you hit `internal` and `external` terminologies in kubernetes. Further, in case you are working on writing controller or deepdiving in code base, you might understand packages from various angles.

Usually, when we're talking about `internal`, we are talking about the `internal` version of resource(Kubernetes API resource is one of the most significant concepts, I will illustrate in another article). For example, `Pod`, the internal version of `Pod` is at [k8s.io/kubernetes/pkg/apis/core/types.go](https://github.com/kubernetes/kubernetes/blob/master/pkg/apis/core/types.go). The latest external version V1 of `Pod` is at [k8s.io/api/core/v1/types.go](https://github.com/kubernetes/api/blob/master/core/v1/types.go) . The internal version of a resource is used for conversion (as the hub in a hub and spoke model).

Taking the `kubectl` as example, the most common way to retrieve resource in `kubectl` is using [resource builder](https://github.com/kubernetes/cli-runtime/blob/master/pkg/genericclioptions/resource/builder.go)

The following is an example using the builder to retrieve the "external" version of a resource:

```golang
r := o.builder.
        WithScheme(scheme.Scheme, scheme.Scheme.PrioritizedVersionsAllGroups()...).
        ContinueOnError().
        NamespaceParam(o.namespace).DefaultNamespace().
        FilenameParam(o.enforceNamespace, o.FilenameOptions).
        ResourceTypeOrNameArgs(false, o.args...).
        Flatten().
        Do()
```

Notice the `WithScheme(scheme.Scheme...`
This is the `kubectl scheme`
The `kubectl scheme` registers ONLY external types. If the builder uses the `legacy scheme`, which registers internal types, then an internal type could be returned.

(To be continued...)
