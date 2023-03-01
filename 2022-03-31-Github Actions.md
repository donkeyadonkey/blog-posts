---
aliases: []
date-created: March 31, 2022 2:06 PM
tags:
  - Infra
date-updated: 2023-02-27 05:47
categories:
  - 조각글
---

<br><br>
# 개념

[Understanding GitHub Actions - GitHub Docs](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions)

<br><br>
## Workflows

- 하나 이상의 job 으로 구성되는 자동화된 절차를 말한다.
- .github/workflows 아래에 있는 하나의 YAML 파일로 정의된다.
<br><br>
## Events

- Workflow 를 트리거 시키는 활동을 말한다.
- 대표적으로 Push, Pull Request, Opening Issue 가 있다.
- 또한 직접 실행시킬 수도 있고, Rest API 를 사용할 수도 있다.
<br><br>
## Jobs

- workflow 안에 있는 여러 steps 의 집합을 말한다.
- Job 은 shell 명령어 등 실행가능한 것이다.
- 정의된 순서대로 실행된다.
<br><br>
## Actions

- GitHub Actions 에서 사용할 수 있는 여러 일을 수행하는 명령 애플리케이션.
- action 을 사용 (uses) 한다.
<br><br>
## Runners

- workflows 를 싫행하는 서버이다.
- 하나의 runner 는 한번에 하나의 job 을 수행한다.
- ubuntu 등 OS 에서 돌아간다.
<br><br>
# 명령어

> 예시

```bash
name: learn-github-actions
on: [push]
jobs:
  check-bats-version:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '14'
      - run: npm install -g bats
      - run: bats -v
```

> 각 문법에 대한 설명은 공식 문서에 잘 설명되어 있다.

[Understanding GitHub Actions - GitHub Docs](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions)
