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
Kotlin version is required at least `1.9.0`.
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

# Core usage
## Shared code
{{< codeImg demoApi.png >}}
<br>
{{< codeImg loginResult.png >}}
<br>
{{< codeImg user.png >}}

## Code generation
Build the module `shared`, and there would generate code in the other two multiplatform modules.  
{{< video src="generation" >}}

## Each side
Apply the generated code on each platform.
{{< tabs "Call on each side" >}}
{{< tab Server >}}
Implement the generated interface.  
{{< codeImg server_demoApiImpl.png >}}
<br>
Configure it before start.
{{< codeImg server_main.png >}}
{{< /tab >}}
{{< tab Browser >}}
{{< video src="browserDisplay" >}}
{{< /tab >}}
{{< tab Android >}}
{{< video src="androidDisplay" >}}
{{< /tab >}}
{{< /tabs >}}
{{< hint info >}}
Since generated code are multiplatform, desktop and other Kotlin native platforms are 
also supported, e.g. `wasm` and `ios`.
{{< /hint >}}

# Additional functionalities
## Http methods 

## Polymorphic functions

## Crypto

## Auth

## WebSocket

# GitHub repo