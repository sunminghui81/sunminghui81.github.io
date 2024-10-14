---
layout: post
keywords: REST API
description: RESTAPI Mothed usage
title: REST API MOTHED
categories: [架构]
tags: [知识]
group: archive
icon: google
---

{% include sunminghui81/setup %}

### RestAPI 中使用的Motheds
HTTP 动词构成了我们“统一接口”约束的主要部分，并为我们提供了与基于名词的资源对应的动作。主要的或最常用的 HTTP 动词（或正确称呼的方法）是 POST、GET、PUT、PATCH 和 DELETE。这些分别对应于创建、读取、更新和删除（或 CRUD）操作。

*还有许多其他动词，但使用频率较低。在那些不太常用的方法中，OPTIONS 和 HEAD 的使用频率高于其他方法。*

<img src="/image/architecture/rest-api-mothed.png" alt="broadcasting" width="100%" height="100%"/>

### POST
POST 动词最常用于**创建**新资源。特别是，它用于创建从属资源。也就是说，从属于某个其他（例如父）资源。换句话说，当创建一个新资源时，POST 到父资源，服务负责将新资源与父资源关联，分配一个 ID（新资源 URI）等。

成功创建后，返回 HTTP 状态 201，返回 Location 标头，其中包含指向具有 201 HTTP 状态的新创建资源的链接。

POST 既不安全也不幂等。因此建议用于非幂等资源请求。发出两个相同的 POST 请求很可能会导致两个资源包含相同的信息。


### GET
HTTP GET 方法用于**读取**（或检索）资源的表示。在“HappyPath”（或非错误路径）中，GET 返回 XML 或 JSON 格式的表示和 200（OK）的 HTTP 响应代码。在错误情况下，它最常返回 404（未找到）或 400（错误请求）。

根据 HTTP 规范的设计，GET（连同 HEAD）请求仅用于读取数据而不是更改数据。因此，当以这种方式使用时，它们被认为是安全的。也就是说，可以在没有数据修改或损坏的风险的情况下调用它们——调用一次与调用 10 次具有相同的效果，或者根本不调用。此外，GET（和 HEAD）是幂等的，这意味着发出多个相同的请求最终会得到与单个请求相同的结果。

不要通过 GET 公开不安全的操作——它不应该修改服务器上的任何资源。

### PUT
PUT 最常用于**更新**功能，PUT 到一个已知的资源 URI，请求正文包含原始资源的新更新表示。

但是，在客户端而不是服务器选择资源 ID 的情况下，PUT 也可用于创建资源。换句话说，如果 PUT 是指向一个包含不存在资源 ID 值的 URI。同样，请求正文包含资源表示。许多人认为这是令人费解和令人困惑的。因此，如果有的话，应该谨慎使用这种创建方法。

或者，使用 POST 创建新资源并在正文表示中提供客户端定义的 ID — 可能是不包含资源 ID 的 URI（请参阅下面的 POST）。

成功更新后，从 PUT 返回 200（如果没有返回正文中的任何内容，则返回 204）。如果使用 PUT 创建，则在成功创建时返回 HTTP 状态 201。响应中的主体是可选的——前提是消耗更多带宽。由于客户端已经设置了资源 ID，因此在创建案例中无需通过 Location 标头返回链接。

PUT 不是一个安全的操作，因为它修改（或创建）服务器上的状态，但它是幂等的。换句话说，如果您使用 PUT 创建或更新资源，然后再次进行相同的调用，则该资源仍然存在并且仍然具有与第一次调用相同的状态。

例如，如果对资源调用 PUT 会增加资源内的计数器，则该调用不再是幂等的。有时会发生这种情况，证明调用不是幂等的可能就足够了。但是，建议保持 PUT 请求是幂等的。强烈建议对非幂等请求使用 POST。

### PATCH
PATCH 用于**修改**功能。PATCH 请求只需要包含对资源的更改，而不是完整的资源。

这类似于 PUT，但主体包含一组指令，描述如何修改当前驻留在服务器上的资源以生成新版本。这意味着 PATCH 主体不应只是资源的修改部分，而应采用某种补丁语言，如 JSON Patch 或 XML Patch。

PATCH 既不安全也不幂等。但是，可以以幂等的方式发出 PATCH 请求，这也有助于防止在相似时间范围内同一资源上的两个 PATCH 请求之间发生冲突而导致不良结果。来自多个 PATCH 请求的冲突可能比 PUT 冲突更危险，因为某些补丁格式需要从已知的基点进行操作，否则它们会破坏资源。使用这种补丁应用程序的客户端应使用条件请求，这样如果自客户端上次访问资源以来资源已更新，则请求将失败。例如，客户端可以在 PATCH 请求的 If-Match 标头中使用强 ETag。

### DELETE
DELETE 很容易理解。它用于**删除**由 URI 标识的资源。

成功删除后，返回 HTTP 状态 200（OK）以及响应正文，可能是已删除项目的表示（通常需要太多带宽），或者是包装响应（请参阅下面的返回值）。要么返回 HTTP 状态 204（无内容），没有响应正文。换句话说，没有正文的 204 状态，或者 JSEND 样式的响应和 HTTP 状态 200 是推荐的响应。

按照 HTTP 规范，DELETE 操作是幂等的。如果您删除一个资源，它就会被删除。在该资源上重复调用 DELETE 最终结果相同：资源消失了。如果调用 DELETE 说，递减一个计数器（在资源内），则 DELETE 调用不再是幂等的。如前所述，只要不改变资源数据，就可以更新使用统计和测量，同时仍然考虑服务幂等性。建议对非幂等资源请求使用 POST。

然而，关于 DELETE 幂等性有一个警告。第二次对资源调用 DELETE 通常会返回 404（未找到），因为它已被删除，因此不再可找到。一些观点认为，这使得 DELETE 操作不再是幂等的，但是，资源的最终状态是相同的。返回 404 是可以接受的，并且可以准确传达呼叫的状态。


## 参考文献
1. [restapitutorial](https://www.restapitutorial.com/lessons/httpmethods.html) 