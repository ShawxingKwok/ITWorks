---
title: Phone
weight: 1
---

# Abstract
At present, each client platform contacts with the server according to the appointment document. 
However, it is cumbersome and error-prone because message formats differ on each route. Although 
{{< newTab grpc "https://grpc.io/" >}} works well as a `rpc` (remote procedure call) framework, 
it is not popular because it requires a third-party language and hard extensive. Therefore, I made 
a new `rpc` framework named `Phone` based on the new popular language `Kotlin multiplatform` and its web framework `Ktor`.
`Phone` would be a great point for popularizing `Kotlin`, and revolutionary together with it.

Main advantages:
- Connects with shared interfaces, data types and related logics.
- Main features are all supported and easily configured, e.g. various http methods, authentication,
  WebSocket, PartialContent, crypto, and automatic serialization.
- Is manually extensible with the provided `Route` on servers, and `HttpRequestBuilder`, `HttpResponse` on clients.

# Setup
{{< hint warning >}}
1. Kotlin version is required at least `1.9.0`.
2. Get familiar with {{< newTab Ktor "https://ktor.io" >}} first.
{{< /hint >}}

Create three modules of which all could be multiplatform, and configure them as below in `build.gradle.kts`. Your 
confusion would be cleared in the next video. 

{{< tabs setup >}}
{{< tab shared >}}
```
plugins {
    val kt = "1.9.0"
    kotlin("multiplatform") version kt
    id ("com.google.devtools.ksp") version "$kt-1.0.13"
    kotlin("plugin.serialization") version kt
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
    // arg("phone.jwt-auth-name", "")
}
```
{{< /tab >}}

{{< tab "server side" >}}
```
plugins {
    kotlin("multiplatform") version "1.9.0"
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

# Core usage sample
## Shared code
Parameters and the generic type (`LoginResult` in this case) should obey
{{< newTab "Kotlin serialization" "https://github.com/Kotlin/kotlinx.serialization/blob/master/docs/basic-serialization.md" >}} rules,
or be helped by [custom serializers](#custom-serializers).

{{< codeImg "codeUsageSample/demoApi.png" >}}
<br>
{{< codeImg "codeUsageSample/loginResult.png" >}}
<br>
{{< codeImg "codeUsageSample/user.png" >}}

## Code generation
Build the module `shared`, and there would generate code in the other two multiplatform modules.  
{{< video src=generation >}}

## Each side
{{< hint info >}}
`Server`, `Browser` and `Android` below are clickable switch tabs.
{{< /hint >}}
{{< tabs "Call on each side" >}}
{{< tab Server >}}
Implement the generated interface in `server side`.
{{< codeImg "codeUsageSample/server_demoApiImpl.png" >}}
<br>

Note that type `PipeContextProvider` is a lambda. 
{{< codeImg "codeUsageSample/pipeContextProvider.png" >}}
<br>
Configure it before start.
{{< codeImg "codeUsageSample/server_main.png" >}}
{{< hint info >}}
There could be multiple interfaces annotated with `Phone.Api`,
which are routed like `Phone.routeAll(routing {  }, AApiImpl, BApiImpl)`.
{{< /hint >}}
{{< /tab >}}
{{< tab Browser >}}
Apply the generated code in `client side`.
{{< video src=browserDisplay.mp4 >}}
{{< /tab >}}
{{< tab Android >}}
Apply the generated code in `client side`.
{{< video src=androidDisplay.mp4 >}}
{{< /tab >}}
{{< /tabs >}}
{{< hint info >}}
Since generated code are multiplatform, desktop and other Kotlin native platforms are 
also supported, e.g. `wasm` and `ios`.
{{< /hint >}}

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
This is the most common case. Remember to use `Unit` if the returned value is needless.
{{< codeImg "calls/common_delete.png" >}} 
<br>

You could also upload files easily as below.
### Shared
{{< codeImg "calls/common_upload_shared.png" >}}
{{< hint warning >}} 
Don't use `Get` as the http method.
{{< /hint >}} 

### Client side
{{< codeImg "calls/common_upload_client.png" >}}

### Server side
{{< codeImg "calls/common_upload_server.png" >}}

## Manual
With `Phone.Call.Manual`, client also gets `HttpResponse` which is generally for downloading files. 

### Shared
{{< codeImg "calls/manual_shared.png" >}}

### Server side
{{< codeImg "calls/manual_server.png" >}}

### Client side
{{< codeImg "calls/manual_client.png" >}}

## PartialContent
### Shared
{{< codeImg "calls/partialContent_shared.png" >}}

### Server side
Responds `File` / `Stream` / `Channel` as before.

### Client side
{{< codeImg "calls/partialContent_client.png" >}}
<br>

Type of the handler
{{< codeImg "calls/partialContent_handlerType.png" >}}
The parameter `ranges` is vararg. If you pass no `LongRange`, you would get the whole file.

## WebSocket
### Shared
You could set `isRaw` to `true` and get `ClientWebSocketSession` and `ServerWebSocketSession` on 
server and client sides. The default value of `method` is `Get` 
{{< codeImg "calls/websocket_shared.png" >}}

### Client side
You will get the parameter `enableWss` in the `Phone` constructor.
{{< codeImg "calls/websocket_enableWss.png" >}}
<br>
{{< codeImg "calls/websocket_client.png" >}}

### Server side
{{< codeImg "calls/websocket_server.png" >}}

# Crypto
## Cipher
First you need to provide a `Cipher` in module `shared` with customed protocol from other 
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

# Custom serializers

2. The parameters are actually placed in the form as far as possible.
3.

# Auth
## Form

## Jwt 

## Others

# Extend

# BadRequestException

# Polymorphic functions

# GitHub repo