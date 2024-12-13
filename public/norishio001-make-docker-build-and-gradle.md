---
title: norishio001-make-docker-build-and-gradle
tags:
  - 'docker'
  -
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---
# new article body
ソフトウェア開発において、効率的な環境構築は重要なテーマです。本記事では、**Docker Compose** と **Gradle** を用いてアプリケーション環境を構築する手順を備忘録として残す.

---
## 環境構築の基本: Docker ComposeとGradleの導入

まずは、環境構築の核となる **Docker Compose** と **Gradle** について説明します.

### Docker Composeのセットアップ

Docker Composeを使用すると、複数のコンテナを連携して管理できます。今回のプロジェクトでは、以下の2つのサービスを構築.

- **MySQL**: アプリケーションのデータベースとして使用
- **アプリケーションコンテナ**: Spring BootとKotlinを動作させる環境

以下が基本的な `docker-compose.yml` の例です。

```yaml:docker-compose.yml
version: '3.8'

services:
  mysql:
    image: mysql:8.0
    container_name: mysql
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: 
      MYSQL_DATABASE: 
      MYSQL_USER: 
      MYSQL_PASSWORD: 
    volumes:
      - ./db_data:/var/lib/mysql

  app:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: app
    ports:
      - "8080:8080"
    depends_on:
      - mysql
```
この設定では、MySQL が動作する環境を提供し、アプリケーションと接続できるように設定されています.

## Gradleの初期化
Gradleは、プロジェクトビルドを管理するツールです。以下のコマンドを使用してGradleプロジェクトを初期化します.

```
docker run --rm -v "$(pwd):/app" -w /app gradle:7.5-jdk17 gradle init --type kotlin-application --dsl kotlin --project-name my_project
```
このコマンドにより、基本的なGradle設定ファイルが生成できる.

### よくある課題とその解決法
環境構築時に遭遇しがちな課題とその対処法を紹介します。

環境変数の管理
エラー例:

```
WARN[0000] The "MYSQL_PASSWORD" variable is not set. Defaulting to a blank string.
```
このエラーは、docker-compose.yml 内で環境変数が設定されていない場合に発生.

解決方法: .env ファイルを使用することで環境変数管理できる.

```
MYSQL_ROOT_PASSWORD=my_password1
MYSQL_DATABASE=my_database
MYSQL_USER=my_user
MYSQL_PASSWORD=my_password2
```

docker-compose.yml に以下のように記述します。
```
environment:
  MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
  MYSQL_DATABASE: ${MYSQL_DATABASE}
  MYSQL_USER: ${MYSQL_USER}
  MYSQL_PASSWORD: ${MYSQL_PASSWORD}

build:
  context: .
  dockerfile: Dockerfile
```

## Gradleの設定
## Gradleの設定

Gradleのデフォルト設定を基に、プロジェクトに応じたカスタマイズを行うことで、より効率的なビルドプロセスが実現できます。この章では、Gradle設定の具体例と、その利便性について解説します。

---

### デフォルトの`build.gradle.kts`のカスタマイズ

以下は、Kotlin DSLを使用した`build.gradle.kts`の基本構成例
このファイルをカスタマイズすることで、依存関係やビルドタスクを簡単に拡張できる.

```kotlin:build.gradle.kts
plugins {
    kotlin("jvm") version "1.8.0"
    kotlin("plugin.spring") version "1.8.0"
    id("org.springframework.boot") version "3.1.0"
    id("io.spring.dependency-management") version "1.1.0"
}

group = "com.example"
version = "0.0.1-SNAPSHOT"
java.sourceCompatibility = JavaVersion.VERSION_17

repositories {
    mavenCentral()
}

dependencies {
    implementation("org.springframework.boot:spring-boot-starter-web")
    implementation("org.jetbrains.kotlin:kotlin-reflect")
    implementation("org.jetbrains.kotlin:kotlin-stdlib-jdk8")
    testImplementation("org.springframework.boot:spring-boot-starter-test")
}
```

## DockerとGradleを使うメリット
この方法にはいくつかのメリットがある.

### 再現性のある環境
Docker Composeを使うことで、同じ環境をチーム全員が再現可能.

### スケーラビリティ
他のサービスを簡単に追加できる.

### 柔軟なビルド管理
Gradleによる依存関係管理やタスクの自動化が可能.

## 感想とまとめ
今回の環境構築を通じて、Docker ComposeとGradle の強力さを再確認しました。特に、エラー対応やディレクトリの整理を行うことで、環境構築の効率が大幅に向上しました。

