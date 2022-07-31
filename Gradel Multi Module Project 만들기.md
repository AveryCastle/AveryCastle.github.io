# Gradle 기반 Multi Project 만들기 with Kotlin

#### 1. sub module 생성하기.
- IntelliJ에서 NEW > MODULE 선택하기.
- Gradle Module 생성할 때 sub module 의 최초 `build.gradle.kts` 파일 내용

  ```gradle

  plugins {
      java
  }

  group = "com.multimoduleexample"
  version = "0.0.1-SNAPSHOT"

  repositories {
      mavenCentral()
  }

  dependencies {
      testImplementation("org.junit.jupiter:junit-jupiter-api:5.6.0")
      testRuntimeOnly("org.junit.jupiter:junit-jupiter-engine")
  }

  tasks.getByName<Test>("test") {
      useJUnitPlatform()
  }

  ```

#### 2. rootProject의 build.gradle.kts 파일 내용 수정하기
```gradle
import org.jetbrains.kotlin.gradle.tasks.KotlinCompile

plugins {
    id("org.springframework.boot") version "2.7.2" apply false // 1.
    id("io.spring.dependency-management") version "1.0.12.RELEASE"
    kotlin("jvm") version "1.6.21"
    kotlin("plugin.spring") version "1.6.21"
    kotlin("plugin.jpa") version "1.6.21"
}

java.sourceCompatibility = JavaVersion.VERSION_17

allprojects {
    group = "com.multimoduleexample"
    version = "0.0.1-SNAPSHOT"

    repositories {
        mavenCentral()
    }
}

subprojects {
    apply(plugin = "kotlin")
    apply(plugin = "kotlin-spring") // 2.
    apply(plugin = "io.spring.dependency-management")

    dependencies {
        // Kotlin Logging
        // https://mvnrepository.com/artifact/io.github.microutils/kotlin-logging
        implementation("io.github.microutils:kotlin-logging:2.1.23")

        // 3. Kotlin 필수 사항 2가지
        implementation("org.jetbrains.kotlin:kotlin-reflect")
        implementation("org.jetbrains.kotlin:kotlin-stdlib-jdk8")
        // 4.
        implementation("com.fasterxml.jackson.module:jackson-module-kotlin")

        // Local 용 H2DB
        runtimeOnly("com.h2database:h2")

        // Test
        testImplementation("org.springframework.boot:spring-boot-starter-test")
    }

    // Multi-Module에서 제대로된 의존성을 갖고 오기 위해서 설정하기.
    dependencyManagement {
        imports {
            mavenBom(org.springframework.boot.gradle.plugin.SpringBootPlugin.BOM_COORDINATES)
        }
    }

    tasks.withType<Wrapper>{
        gradleVersion = "7.2"
    }

    tasks.withType<KotlinCompile> {
        kotlinOptions {
            freeCompilerArgs = listOf("-Xjsr305=strict")
            jvmTarget = "17"
        }
    }

    tasks.withType<Test> {
        useJUnitPlatform()
    }
}

```
>
> 1. `apply false`: 기본적으로 프로젝트 전체에 적용하고, 일부 서브 프로젝트에만 적용하기 위해서 `apply false`를 선언해 제어한다. 서브 프로젝트에서 사용하고 싶을 때, `apply plugin`으로 명시한다.
> 2. `kotlin-spring`: Kotlin 클래스는 기본적으로 final이므로 Spring annotation이 달린 클래스를 자동으로 open해서 프록시할 수 있도록 kotlin-spring 플러그인을 구성할 수 있다.
> 3. `kotlin-stdlib`: Kotlin을 사용하기 위해 필수적으로 필요하다.
> 3-2. `kotlin-reflect`: Kotlin을 사용하기 위해 필수적으로 필요하다.
> 4. `jackson-module-kotlin`: Jackson의 Kotlin 모듈은 Kotlin에서 JSON 데이터를 직렬화/역직렬화하기 위해 필요합니다. classpath에서 발견되면 자동으로 등록됩니다. Jackson 및 Kotlin은 있지만 Jackson Kotlin 모듈이 없는 경우 경고 메시지가 기록됩니다.
> - 참고: https://docs.spring.io/spring-boot/docs/2.0.x/reference/html/boot-features-kotlin.html#boot-features-kotlin-requirements

#### 3. root project 하위의 `settings.gradle.kts` 에 sub module 포함시키기.
```
rootProject.name = "multimoduleexample-issue-service"

include("issue-service")
```

#### 4. sub module의 `build.gradle.kts` 파일 내용 수정하기
```gradle
apply(plugin = "kotlin-jpa")

dependencies {
    implementation("org.springframework.boot:spring-boot-starter-data-jpa")
    implementation("org.springframework.boot:spring-boot-starter-thymeleaf")
    implementation("org.springframework.boot:spring-boot-starter-web")
}
```
