---
layout: post
keywords: kubebuilder
description: 第一次使用kubebuilder
title: kubebuilder
categories: [kubernetes]
tags: [kubebuilder]
group: archive
icon: google
---
{% include sunminghui81/setup %}

## 安装
我安装的系统是：  
```console
root@sun:/home/julien/operator/sample# cat /etc/lsb-release
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=16.04
DISTRIB_CODENAME=xenial
DISTRIB_DESCRIPTION="Ubuntu 16.04.6 LTS"
```
我使用[官网安装](https://book.kubebuilder.io/quick-start.html#installation)的kubebuilder。  
安装后可以查看相关命令行：  
```console
root@sun:/home/julien/operator/sample# kubebuilder version
Version: main.version{KubeBuilderVersion:"3.1.0", KubernetesVendor:"1.19.2", GitCommit:"92e0349ca7334a0a8e5e499da4fb077eb524e94a", BuildDate:"2021-05-27T17:54:28Z", GoOs:"linux", GoArch:"amd64"}
```

## 初始化创建

### kubebuilder init
在kubebuilder init命令中，kubebuilder会创建下面几个基本的工程文件。
```console
root@b5700554c4c1:/home/ready/go/src/guardians# kubebuilder init --help
Initialize a new project including vendor/ directory and Go package directories.

Writes the following files:
- a boilerplate license file
- a PROJECT file with the project configuration
- a Makefile to build the project
- a go.mod with project dependencies
- a Kustomization.yaml for customizating manifests
- a Patch file for customizing image for manager manifests
- a Patch file for enabling prometheus metrics
- a cmd/manager/main.go to run
```

### 创建工程
```console
root@sun:/home/julien/gerrit/goworkspace/src# mkdir guardians
root@sun:/home/julien/gerrit/goworkspace/src# cd guardians/
root@sun:/home/julien/gerrit/goworkspace/src/guardians# kubebuilder init --domain my.domain --repo my.domain/guardians
Writing kustomize manifests for you to edit...
Writing scaffold for you to edit...
Get controller runtime:
$ go get sigs.k8s.io/controller-runtime@v0.8.3
Update dependencies:
$ go mod tidy
Next: define a resource with:
$ kubebuilder create api
```
### 创建 API 
```console
root@sun:/home/julien/gerrit/goworkspace/src/guardians# kubebuilder create api --group webapp --version v1 --kind Guardians
Create Resource [y/n]
y
Create Controller [y/n]
y
Writing kustomize manifests for you to edit...
Writing scaffold for you to edit...
api/v1/guardians_types.go
controllers/guardians_controller.go
Update dependencies:
$ go mod tidy
Running make:
$ make generate
go: creating new go.mod: module tmp
Downloading sigs.k8s.io/controller-tools/cmd/controller-gen@v0.4.1
go get: sigs.k8s.io/controller-tools/cmd/controller-gen@v0.4.1: Get "https://goproxy.io/sigs.k8s.io/controller-tools/cmd/controller-gen/@v/v0.4.1.info": x509: certificate signed by unknown authority
Makefile:90: recipe for target 'controller-gen' failed
make: *** [controller-gen] Error 1
Error: failed to create API: unable to run post-scaffold tasks of "base.go.kubebuilder.io/v3": exit status 2
Usage:
  kubebuilder create api [flags]

Examples:
  # Create a frigates API with Group: ship, Version: v1beta1 and Kind: Frigate
  kubebuilder create api --group ship --version v1beta1 --kind Frigate

  # Edit the API Scheme
  nano api/v1beta1/frigate_types.go

  # Edit the Controller
  nano controllers/frigate/frigate_controller.go

  # Edit the Controller Test
  nano controllers/frigate/frigate_controller_test.go

  # Install CRDs into the Kubernetes cluster using kubectl apply
  make install

  # Regenerate code and run against the Kubernetes cluster configured by ~/.kube/config
  make run


Flags:
      --controller           if set, generate the controller without prompting the user (default true)
      --crd-version string   version of CustomResourceDefinition to scaffold. Options: [v1, v1beta1] (default "v1")
      --force                attempt to create resource even if it already exists
      --group string         resource Group
  -h, --help                 help for api
      --kind string          resource Kind
      --make make generate   if true, run make generate after generating files (default true)
      --namespaced           resource is namespaced (default true)
      --plural string        resource irregular plural form
      --resource             if set, generate the resource without prompting the user (default true)
      --version string       resource Version

Global Flags:
      --plugins strings   plugin keys to be used for this subcommand execution

2021/12/25 11:10:05 failed to create API: unable to run post-scaffold tasks of "base.go.kubebuilder.io/v3": exit status 2
```
上面的：x509: certificate signed by unknown authority 查了一下，是客户端证书问题：  
[访问https时为何会出现x509 certificate signed by unknown authority](https://izsk.me/2020/06/18/why-x509-error-when-curl-https/)  
但是机器上/etc/ssl证书是在的，并且执行update-ca-certificates命令，更新证书数为0.经过各种尝试，未果。  
最终找到公司的一个goproxy,完成命令执行。
```console
root@sun:/home/julien/gerrit/goworkspace/src/guardians# kubebuilder create api --group webapp --version v1 --kind Guardians
Create Resource [y/n]
y
Create Controller [y/n]
y
Writing kustomize manifests for you to edit...
Writing scaffold for you to edit...
api/v1/guardians_types.go
controllers/guardians_controller.go
Update dependencies:
$ go mod tidy
Running make:
$ make generate
go: creating new go.mod: module tmp
Downloading sigs.k8s.io/controller-tools/cmd/controller-gen@v0.4.1
go get: added sigs.k8s.io/controller-tools v0.4.1
/home/julien/gerrit/goworkspace/src/guardians/bin/controller-gen object:headerFile="hack/boilerplate.go.txt" paths="./..."
```

此时创建出来的工程目录以及文件为：  
```console
root@10076736:/home/julien/gerrit/goworkspace/src/guardians# tree
.
├── api
│   └── v1
│       ├── groupversion_info.go
│       ├── guardians_types.go
│       └── zz_generated.deepcopy.go
├── bin
│   └── controller-gen
├── config
│   ├── crd
│   │   ├── kustomization.yaml
│   │   ├── kustomizeconfig.yaml
│   │   └── patches
│   │       ├── cainjection_in_guardians.yaml
│   │       └── webhook_in_guardians.yaml
│   ├── default
│   │   ├── kustomization.yaml
│   │   ├── manager_auth_proxy_patch.yaml
│   │   └── manager_config_patch.yaml
│   ├── manager
│   │   ├── controller_manager_config.yaml
│   │   ├── kustomization.yaml
│   │   └── manager.yaml
│   ├── prometheus
│   │   ├── kustomization.yaml
│   │   └── monitor.yaml
│   ├── rbac
│   │   ├── auth_proxy_client_clusterrole.yaml
│   │   ├── auth_proxy_role_binding.yaml
│   │   ├── auth_proxy_role.yaml
│   │   ├── auth_proxy_service.yaml
│   │   ├── guardians_editor_role.yaml
│   │   ├── guardians_viewer_role.yaml
│   │   ├── kustomization.yaml
│   │   ├── leader_election_role_binding.yaml
│   │   ├── leader_election_role.yaml
│   │   ├── role_binding.yaml
│   │   └── service_account.yaml
│   └── samples
│       └── webapp_v1_guardians.yaml
├── controllers
│   ├── guardians_controller.go
│   └── suite_test.go
├── Dockerfile
├── go.mod
├── go.sum
├── hack
│   └── boilerplate.go.txt
├── main.go
├── Makefile
└── PROJECT

13 directories, 37 files
```

### 待补充内容，下面如何修改这个工程，完成operator的编写。

