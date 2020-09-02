你好！
很冒昧用这样的方式来和你沟通，如有打扰请忽略我的提交哈。我是光年实验室（gnlab.com）的HR，在招Golang开发工程师，我们是一个技术型团队，技术氛围非常好。全职和兼职都可以，不过最好是全职，工作地点杭州。
我们公司是做流量增长的，Golang负责开发SAAS平台的应用，我们做的很多应用是全新的，工作非常有挑战也很有意思，是国内很多大厂的顾问。
如果有兴趣的话加我微信：13515810775  ，也可以访问 https://gnlab.com/，联系客服转发给HR。
Go Fastly
=========
[![Build Status](http://img.shields.io/travis/sethvargo/go-fastly.svg?style=flat-square)][travis]
[![Go Documentation](http://img.shields.io/badge/go-documentation-blue.svg?style=flat-square)][godocs]

[travis]: http://travis-ci.org/sethvargo/go-fastly
[godocs]: http://godoc.org/github.com/sethvargo/go-fastly

Go Fastly is a Golang API client for interacting with most facets of the
[Fastly API](https://docs.fastly.com/api).

Installation
------------
This is a client library, so there is nothing to install. You must be running Go
1.8 or higher.

Usage
-----
Download the library into your `$GOPATH`:

    $ go get github.com/sethvargo/go-fastly/fastly

Import the library into your tool:

```go
import "github.com/sethvargo/go-fastly/fastly"
```

Examples
--------
Fastly's API is designed to work in the following manner:

1. Create (or clone) a new configuration version for the service
2. Make any changes to the version
3. Validate the version
4. Activate the version

This flow using the Golang client looks like this:

```go
// Create a client object. The client has no state, so it can be persisted
// and re-used. It is also safe to use concurrently due to its lack of state.
// There is also a DefaultClient() method that reads an environment variable.
// Please see the documentation for more information and details.
client, err := fastly.NewClient("YOUR_FASTLY_API_KEY")
if err != nil {
  log.Fatal(err)
}

// You can find the service ID in the Fastly web console.
var serviceID = "SU1Z0isxPaozGVKXdv0eY"

// Get the latest active version
latest, err := client.LatestVersion(&fastly.LatestVersionInput{
  Service: serviceID,
})
if err != nil {
  log.Fatal(err)
}

// Clone the latest version so we can make changes without affecting the
// active configuration.
version, err := client.CloneVersion(&fastly.CloneVersionInput{
  Service: serviceID,
  Version: latest.Number,
})
if err != nil {
  log.Fatal(err)
}

// Now you can make any changes to the new version. In this example, we will add
// a new domain.
domain, err := client.CreateDomain(&fastly.CreateDomainInput{
  Service: serviceID,
  Version: version.Number,
  Name: "example.com",
})
if err != nil {
  log.Fatal(err)
}

// Output: "example.com"
fmt.Println(domain.Name)

// Now we can validate that our version is valid.
valid, _, err := client.ValidateVersion(&fastly.ValidateVersionInput{
  Service: serviceID,
  Version: version.Number,
})
if err != nil {
  log.Fatal(err)
}
if !valid {
  log.Fatal("not valid version")
}

// Finally, activate this new version.
activeVersion, err := client.ActivateVersion(&fastly.ActivateVersionInput{
  Service: serviceID,
  Version: version.Number,
})
if err != nil {
  log.Fatal(err)
}

// Output: true
fmt.Printf("%b", activeVersion.Locked)
```

More information can be found in the
[Fastly Godoc](https://godoc.org/github.com/sethvargo/go-fastly).

License
-------
```
Copyright 2015 Seth Vargo

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
