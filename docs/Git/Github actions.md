---
title: Github actions
parent: Git
nav_order: 2
layout: default
---

<p>
<img alt="Static Badge" src="https://img.shields.io/badge/github--actions-%232088FF?logo=githubactions&logoColor=white">
</p>

<h1 style="color:#0c0c0c;font-weight:500;">Github actions?</h1>

- [**Workflows**](#workflows)
  - [**Workflows trigger**](#workflows-trigger)
  - [**Reusing workflows**](#reusing-workflows)
- [**Events**](#events)
- [**Jobs**](#jobs)
  - [**변수 사용**](#변수-사용)
  - [**Github Actions Secrets 사용**](#github-actions-secrets-사용)
  - [**종속 작업 만들기**](#종속-작업-만들기)
  - [**Actions**](#actions)
- [**Runners**](#runners)

```
    # Workflows name
    name: learn-github-actions

    # 워크플로 실행의 이름으로, 레포지토리 "Actions" 탭에 있는 워크플로 실행 목록에 표시.
    run-name: ${{ github.actor }} is learning GitHub Actions

    # Trigger Event
    on: [push]

    # (...실행될 jobs...)
```

## **Workflows**

- 레포지토리의 .github/workflows 디렉터리에 정의되어 있으며, 여러 워크플로가 있을 수 있으며 각 워크플로는 서로 다른 작업을 수행할 수 있음
- 워크플로에는 순차적 또는 병렬로 실행될 수 있는 작업이 하나 이상 포함 되어야 함
- 이벤트에 의해 트리거될 때 실행되거나 수동으로 또는 정의된 일정에 따라 실행됨
- **다른 워크플로 내에서 워크플로를 참조 가능**

### **Workflows trigger**

- 워크플로의 레포지토리에서 발생하는 이벤트
- GitHub 외부에서 발생하고 GitHub에서 repository_dispatch 이벤트를 트리거하는 이벤트
- 예약된 시간
- 수동

### **Reusing workflows**

- 최대 4개 수준의 워크플로를 연결할 수 있음
- **호출자 워크플로의 워크플로 수준에서 정의된 env 컨텍스트에서 설정된 환경 변수는 호출된 워크플로로 전파되지 않음.**
- 대신 재사용 가능한 워크플로의 출력을 사용해야 함

```yaml
# 호출될 워크플로
name: called-workflows

# 트리거 조건 / 워크플로 호출
on:
  workflow_call:
    # 호출자 워크플로에서 받을 값들
    inputs:
      caller-name:
        required: true
        type: string

```

```yaml
# 호출자 워크플로
name: caller-workflows

on:
  push:
    branches:
      - main

jobs:
  call-workflow:
    runs-on: ubuntu-latest

    steps:
      # 워크플로 호출
      - name: Call Workflow
        # 호출할 워크플로 path
        uses: ./.github/workflows/called-workflows.yml
        # 필요한 입력값 전달
        with:
          caller-name: 'caller-workflows'
```

## **Events**

- 워크플로 실행을 트리거하는 활동
- [Trigger Events](https://docs.github.com/ko/actions/using-workflows/events-that-trigger-workflows)

## **Jobs**

- 같은 runner 에서 실행되는 워크플로 내의 각각의 단계
- 기본적으로 작업은 종속성이 없으며 서로 병렬로 실행, 작업이 다른 작업에 종속되면 종속 작업이 완료될 때까지 기다렸다가 실행
- **동일한 runner에서 실행되므로 데이터를 공유가능**

### [**변수 사용**](https://docs.github.com/ko/actions/learn-github-actions/variables#default-environment-variables)

```yaml
name: Greeting on variable day

on:
  workflow_dispatch

env:
  DAY_OF_WEEK: Monday

jobs:
  greeting_job:
    runs-on: ubuntu-latest
    env:
      Greeting: Hello
    steps:
      - name: "Say Hello Mona it's Monday"run: echo "$Greeting $First_Name. Today is $DAY_OF_WEEK!"
        env:
          First_Name: Mona
```

### **Github Actions Secrets 사용**

- 레포지토리에서 만든 변수를 워크플로에서 사용

```
jobs:
  example-job:
    runs-on: ubuntu-latest
    steps:
      - name: Retrieve secret
        env:
          super_secret: ${{ secrets.SUPERSECRET }}
        run: |
          example-command "$super_secret"
```

### **종속 작업 만들기**

- `needs` 키워드를 사용하여 이 종속성 생성
- 작업 중 하나가 실패하면 모든 종속 작업은 건너뜀
- 작업을 계속해야 하는 경우 `if` 조건문을 사용

```
jobs:
  setup:
    runs-on: ubuntu-latest
    steps:
      - run: ./setup_server.sh
  build:
    needs: setup
    runs-on: ubuntu-latest
    steps:
      - run: ./build_server.sh
  test:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - run: ./test_server.sh
```

### **Actions**

- 자주 사용되는 작업들을 제공해주는 애플리케이션
- [GitHub Marketplace](https://github.com/marketplace?type=actions) 에서 탐색 가능

```
steps:
    - uses: actions/javascript-action@v1
```

## **Runners**

- 워크플로가 트리거될 때 워크플로를 실행하는 서버
- GitHub는 워크플로를 실행하기 위한 Ubuntu Linux, Microsoft Windows 및 macOS 실행기를 제공
- 자체 실행기를 호스팅할 수 있음