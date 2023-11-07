---
title: Phone
weight: 1
---

# Abstract
At present, each client platform contacts with the server according to the appointment document. 
However, it is cumbersome and error-prone because message formats differ on each route and http 
method. Although {{< newTab grpc "https://grpc.io/" >}} works well as a `rpc` (remote procedure call) 
framework, it is not popular because it requires a third-party language and is hard to extend. 
Therefore, I made this new `rpc` framework named `Phone` based on the new popular language `Kotlin multiplatform` 
and its web framework `Ktor`. `Phone` would be a great point for popularizing `Kotlin`, and revolutionary 
together with it.

Main advantages:
- Connects with shared interfaces, data types and related logics.
- Main features are all supported and easily configured, e.g. various http methods, authentication,
  WebSocket, PartialContent, crypto, and automatic serialization.
- Is manually extensible with the provided `Route` on servers, and `HttpRequestBuilder`, `HttpResponse` on clients.

# Core usage sample
Other platforms are also supported, e.g. `ios` and `wasm`.   
{{< tabs "Core usage sample" >}}

{{< tab Shared >}}
{{< video src=shared >}}
{{< hint info >}}
Parameters and the generic type (`LoginResult` in this case) should obey
{{< newTab "Kotlin serialization" "https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/basic-serialization.md" >}} rules,
or be helped by [custom serializers](#serialize-third-party-types).
{{< /hint >}}
{{< /tab >}}

{{< tab Server >}}
{{< video src=server >}}
{{< /tab >}}

{{< tab Browser >}}
{{< video src=browser.mp4 >}}
{{< /tab >}}

{{< tab Android >}}
{{< video src=android.mp4 >}}
{{< /tab >}}

{{< /tabs >}}

# Setup
{{< hint warning >}}
1. Kotlin version is required at least `1.9.0`.
2. Get familiar with {{< newTab Ktor "https://ktor.io" >}} first.
   {{< /hint >}}

Create three modules of which all could be multiplatform, configure them as below in `build.gradle.kts`,
and implement them in `server` and each `client`. 

{{< tabs setup >}}
{{< tab shared >}}
```
plugins {
    val kt = "1.9.0"
    kotlin("multiplatform") version kt
    id ("com.google.devtools.ksp") version "$kt-1.0.13"
    kotlin("plugin.serialization") version kt
}
repositories {
    maven("https://s01.oss.sonatype.org/content/repositories/snapshots/")
}
kotlin {
    jvm{
        jvmToolchain(8)
    }
    js{
        browser()
    }
    // other needed native platforms

    sourceSets {
        val commonMain by getting{
            dependencies{
                api("io.github.shawxingkwok:phone-runtime:1.0.0-SNAPSHOT")
                implementation("org.jetbrains.kotlinx:kotlinx-serialization-core:1.6.0")
                api("org.jetbrains.kotlinx:kotlinx-serialization-json:1.6.0")
            }
        }
    }
}

dependencies {
    // other platforms are needless here, because this compiler generates code and copy them to else where.
    add("kspJvm", "io.github.shawxingkwok:phone-compiler:1.0.0-1.0.0-SNAPSHOT")
}

ksp{
    // set your own local path
    arg("phone.server-package-path", "${projectDir.parent}/serverside/src/commonMain/kotlin")
    arg("phone.client-package-path", "${projectDir.parent}/clientside/src/commonMain/kotlin")

    arg("phone.server-package-name", "pers.shawxingkwok.server.phone")
    arg("phone.client-package-name", "pers.shawxingkwok.client.phone")

    arg("phone.default-method", "post")
    // optional
    // arg("phone.jwt-auth-name", "<your jwt auth name>")
    // arg("phone.client-phone-modifiers", "internal abstract") // "open" by default
}
```
{{< /tab >}}

{{< tab "server side" >}}
```
plugins {
    kotlin("multiplatform") version "1.9.0"
}
repositories {
    maven("https://s01.oss.sonatype.org/content/repositories/snapshots/")
}
kotlin {
    jvm{
        jvmToolchain(8)
    }
    // other needed native platforms

    sourceSets {
        val commonMain by getting{
            dependencies{
                implementation("io.github.shawxingkwok:phone-runtime:1.0.0-SNAPSHOT"))
                api(project(":example:api:shared")) // or the remote 
                implementation("io.ktor:ktor-server-core:<ktor_version>")
            }
        }
    }
}
```
{{< /tab >}}

{{< tab "client side" >}}
```
plugins {
    kotlin("multiplatform") version "1.9.0"
}

repositories {
    maven("https://s01.oss.sonatype.org/content/repositories/snapshots/")
}

kotlin {
    jvm{
        jvmToolchain(8)
    }
    js{
        browser()
    }
    // other needed native platforms

    sourceSets {
        val commonMain by getting{
            dependencies{
                implementation("io.github.shawxingkwok:phone-runtime:1.0.0-SNAPSHOT"))
                api(project(":example:api:shared")) // or the remote 
                implementation("io.ktor:ktor-server-core:<ktor_version>")
            }
        }
    }
}
```
{{< /tab >}}
{{< /tabs >}}

# Http methods
The default `http method` is indispensable and configured in `shared` module `build.gradle.kts`.
The options are `get`, `post`, `put`, `delete` and `patch` which could start with an uppercase char.
{{< codeImg "httpMethods/ksp.png" >}}
<br>

The `http method` of `login` in this case is `Post`. 
{{< codeImg "codeUsageSample/demoApi.png" >}}
<br>

Default methods could be overridden in `Phone.Api`. Methods for each function could also be separately set. 
Now `login` method is `Post`, and `search` method is `Get`.
{{< codeImg "httpMethods/loginSearch.png" >}}

# Calls
{{< hint info >}}
1. In this section, all parameters and generic type are not limited.
2. Examples run on Jvm. There are multiple `Ktor` ways of handling file stream in `body`. 
   Each platform also handles differently.  
{{< /hint >}}

## Common
This is the most common case.
{{< codeImg "calls/common_search_shared.png" >}}
{{< tabs "call common search" >}}

{{< tab client >}}
{{< codeImg "calls/common_search_client.png" >}}
{{< /tab >}}

{{< tab server >}}
{{< codeImg "calls/common_search_server.png" >}}
{{< /tab >}}

{{< /tabs >}}
<br>

Remember to use `Unit` if the returned value is needless.
{{< codeImg "calls/common_delete.png" >}}
<br>

You could also upload files easily as below.
{{< codeImg "calls/common_upload_shared.png" >}}
{{< tabs "call common upload file" >}}

{{< tab client >}}
{{< codeImg "calls/common_upload_client.png" >}}
{{< /tab >}}

{{< tab server >}}
{{< codeImg "calls/common_upload_server.png" >}}
{{< /tab >}}

{{< /tabs >}}

## Manual
With `Phone.Call.Manual`, client also gets `HttpResponse` which is generally for downloading files. 

{{< codeImg "calls/manual_shared.png" >}}
{{< tabs "call manual" >}}

{{< tab client >}}
{{< codeImg "calls/manual_client.png" >}}
{{< /tab >}}

{{< tab server >}}
{{< codeImg "calls/manual_server.png" >}}
{{< /tab >}}

{{< /tabs >}}

## PartialContent
{{< hint warning >}}
Required server plugin: `PartialContent` and `AutoHeadResponse`
{{< /hint >}}
{{< codeImg "calls/partialContent_shared.png" >}}
{{< tabs "call partialContent" >}}

{{< tab client >}}
{{< codeImg "calls/partialContent_client.png" >}}

Type of the handler
{{< codeImg "calls/partialContent_handlerType.png" >}}

The parameter `ranges` is vararg. If you pass no `LongRange`, you would get the whole file.
{{< /tab >}}

{{< tab server >}}
Responds `File` / `Stream` / `ByteReadChannel` as before.
{{< /tab >}}

{{< /tabs >}}

## WebSocket
{{< hint warning >}}
Required server and client plugins: `WebSockets` 
{{< /hint >}}

{{< codeImg "calls/websocket_shared.png" >}}
{{< tabs "call websocket" >}}

{{< tab "client" >}}
You will get the parameter `enablesWss` in the `Phone` constructor.
{{< codeImg "calls/websocket_enableWss.png" >}}
<br>

Request
{{< codeImg "calls/websocket_client.png" >}}
<br>

I suggest to use `onReceivedSuccess` instead.
{{< codeImg "calls/websocket_onReceivedSuccess.png" >}}

{{< /tab >}}

{{< tab "server" >}}
{{< codeImg "calls/websocket_server.png" >}}
{{< /tab >}}

{{< /tabs >}}

You could set `isRaw` to `true` and get `ClientWebSocketSession` and `ServerWebSocketSession` on
server and client sides.
{{< codeImg "calls/websocket_isRaw.png" >}}

# Parameter positions
You may be concerned about the actual parameter positions because `URL` is sometimes a bad choice.
Actually, `Phone` puts parameters in a form as long as the `HttpRequest body` is empty. If you set the body before 
request in this way, the parameters would be put in `URL`.
```
Phone.DemoApi{
    setBody(...)
}
...
```

# Crypto
## Cipher
First you need to provide a `Cipher` in module `shared` with customed protocol with other 
Kotlin multiplatform Crypto libraries.   
{{< codeImg "crypto/cipher.png" >}}

## Targets
Next you could annotate these symbols with `Phone.Crypto`.
{{< codeImg "crypto/targets.png" >}}
1. The top `Phone.Crypto` means all messages in `DemoApi` will be encrypted. 
The next `Phone.Crypto` on the function `login` means all messages related to this function will 
be encrypted. 
2. For parameters, annotating it or its type makes the same sense. 
3. Annotating the returned `Any` makes `LoginResult` crypto.  

For this `login` case, I suggest annotating `Phone.Crypto` only on the function.  
{{< codeImg "crypto/style.png" >}}

# Serialize third-party types
Suppose `class Time` is from a third-party library and is not adapted with `Kotlin Serializable`.
{{< codeImg "customSerializers/classTime.png" >}} 

You could set a serializer annotated with `Phone.Serializer`. 
{{< codeImg "customSerializers/timeSerializer.png" >}} 

Note that it's not as intelligent as the original `Kotlin serialization`. A single `TimeSerializer` 
is not enough for cases below.

{{< codeImg "customSerializers/sumTime.png" >}}
<br>

These are the actually paired serializers.
{{< codeImg "customSerializers/serializers.png" >}}

# Extend
The source code is `open` with options.
{{< tabs extend >}}

{{< tab client >}}
{{< codeImg "extend/client_srccode.png" >}}

You could make the generated `Phone` `internal abstract` instead of `open` via ksp args. And then 
set a public subclass.
```
ksp{
    arg("phone.client-phone-modifiers", "internal abstract")
}
```
{{< /tab >}}

{{< tab server >}}
{{< codeImg "extend/server_onStart.png" >}}
<br>

`onStart` is background called as below.
{{< codeImg "extend/server_onStart_.png" >}}
{{< /tab >}}

{{< /tabs >}}

# Auth
{{< hint warning >}}
Required server plugin: `Authentication`   
Required client plugin: `Auth`
{{< /hint >}}
Learn the auth part in Ktor if you are not familiar with it. Then you could get the point in this
section easily.

Annotation `Phone.Auth`
{{< codeImg "auth/common_authAnnot.png" >}}
<br>
{{< codeImg "auth/common_auth.png" >}}
<br>
Generated code in the route function.
{{< codeImg "auth/common_srccode.png" >}}
<br>

Attentions

1. For `JWT`, at first tell `Phone` the jwt auth name in `build.gradle(.kts)` in module `shared` as below.
    ```
    ksp {
       arg("phone.jwt-auth-name", "<your-auth-name>") 
    }
    ```
    Then you could refresh the jwt token on client as before, and put it in `phone` via 
    `phone.refreshJwtToken(token)`. Then each request header needing the token would get it.

2. For the `form authentication`, avoid using `HttpMethod.Get`. 

3. For `WebSockets`, `JWT` is more suggested than general authentications. Besides, the configured client authentication 
info would not be put into `HttpHeader` as common `Http requests`. If you apply basic authentication 
on a `WebSocket` route, you should extend the request with specific header info before you proceed.    
    ```
    phone.CompoundApi{
        header(HttpHeaders.Authorization, "Basic " + "jetbrains:foobar".encodeBase64())
    }
    ...
    ``` 

4. For `OAuth`, you should configure its `callback` in `Route.onStart` introduced in the section 
[extend](#extend). Note that `HttpMethod.Get` is the only permitted method.

# Polymorphic functions
For the backward compatibility, polymorphic functions except the first are required to declare the distinct id.
{{< codeImg "polymorphic/shared.png" >}}

# Exception
You could intercept exceptions and respond a status code with the Ktor plugin {{< newTab StatusPages "https://ktor.io/docs/status-pages.html#configure" >}}. 
`HttpStatusCode.OK` tells the client `Phone` to parse the body to the expected type, and `HttpStatusCode.NoContent` 
tells `null`. Other status codes means failure.

I suggest to only respond `400` or `500` with messages for exceptions.

# Roadmap
1. Documents and api generation for other languages.  
2. Big binary data as parameters, and limited permission with a new `context`. `call.parameters`, `call.respond` and 
   some other needless functions are invisible.

# <a href="https://github.com/ShawxingKwok/Phone" target="_blank"> GitHub repo</a>