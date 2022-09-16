---
layout: post
keywords: json转struct
description: json转struct
title: json转struct
categories: [Golang]
tags: [json转struct]
group: archive
icon: google
---

{% include sunminghui81/setup %}


### 在已知结构体解析时
下面解析中，传入的json中，相比较多出一些结构键值，不影响转换。
如下所示：

`
// You can edit this code!
// Click here and start typing.
package main

import (
	"bytes"
	"encoding/json"
	"fmt"
	"io"
)

const (
	DELETE_BODY_NORMAL = `{
		"xlb_name":"lvs1",
		"xlb_networks":[{
			"cidr_v4":"12.62.40.0/24",	
			"cidr_v6":"5efe:ffff:0:f101::/116",
			"cc": "55"
		}],
		"extern": "66" 
	}`
)

type Cidr struct {
	V4 string `json:"cidr_v4"`
	V6 string `json:"cidr_v6"`
}
type NetworksDeleteParams struct {
	XlbName     string `json:"xlb_name"`
	XlbNetWorks []Cidr `json:"xlb_networks"`
}

func BodyDecodeToJson(body io.Reader, v interface{}) (err error) {
	defer func() {
		if r := recover(); r != nil {
			err = fmt.Errorf("decode panic")
		}
	}()
	if err = json.NewDecoder(body).Decode(v); err != nil {
		return err
	}
	return nil
}

func main() {
	delParams := NetworksDeleteParams{}
	body := bytes.NewBufferString(DELETE_BODY_NORMAL)
	BodyDecodeToJson(body, &delParams)
	fmt.Println("Hello, 世界")
	fmt.Println(delParams)
}

`Golang

### 缺少键值结构时，转化struct
如下所示，json字符串中缺少了name字段，解析后，struct的值使用对应类型的空值赋值
`
// You can edit this code!
// Click here and start typing.
package main

import (
	"bytes"
	"encoding/json"
	"fmt"
	"io"
)

const (
	DELETE_BODY_NORMAL = `{
		"xlb_networks":[{
			"cidr_v4":"12.62.40.0/24",	
			"cidr_v6":"5efe:ffff:0:f101::/116",
			"cc": "55"
		}],
		"extern": "66" 
	}`
)

type Cidr struct {
	V4 string `json:"cidr_v4"`
	V6 string `json:"cidr_v6"`
}
type NetworksDeleteParams struct {
	XlbName     string `json:"xlb_name"`
	XlbNetWorks []Cidr `json:"xlb_networks"`
}

func BodyDecodeToJson(body io.Reader, v interface{}) (err error) {
	defer func() {
		if r := recover(); r != nil {
			err = fmt.Errorf("decode panic")
		}
	}()
	if err = json.NewDecoder(body).Decode(v); err != nil {
		return err
	}
	return nil
}

func main() {
	delParams := NetworksDeleteParams{}
	body := bytes.NewBufferString(DELETE_BODY_NORMAL)
	BodyDecodeToJson(body, &delParams)
	fmt.Println("Hello, 世界")
	fmt.Println(delParams)
	fmt.Println(delParams.XlbName == "")
}
-------------------
Hello, 世界
{ [{12.62.40.0/24 5efe:ffff:0:f101::/116}]}
true

Program exited.
`Golang


### play ground
https://go.dev/play/p/UYb0-oFKcFm
 
