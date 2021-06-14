---
layout: post
title:  "TIL 1 : Git Workflow"
date:   2021-06-13T14:25:52-05:00
author: Jun Seub Lim
categories: git
tags: git
cover:  "/assets/instacode.png"
---

# Git Workflow란?

Git workflow란 git을 효율적으로 그리고 지속적으로 사용할 수 있도록 도와주는 일종의 방법 혹은 수단이라고 할 수 있다. Git workflow에는 여러 종류가 있으며, 각자 팀에 잘맞는 방법을 채택하면 된다. 이 게시글에서는 Centralized, Feature Branch, Gitflow Workflow에 대해서 설명한다.

## 1. Centralized Workflow

Centralized Workflow는 하나의 중심 repository에 프로젝트의 모든 변화를 추가하는 방식이다. 기본 개발 브랜치는 `main`이며, 모든 변경 사항들은 이 브랜치로 commit된다. Centralized Workflow는 `main`외의 다른 브랜치들을 필요로 하지 않는다.

### How it works

1. 개발자들은 중심 repository를 clone한다.
2. 변경 사항들을 로컬로 commit 한다.
3. 변경 사항들을 합치기 위해서 자신의 `main` 브랜치를 중심 repository에 push한다.

### Managing conflicts

만약 개발자의 로컬 commit이 중심 repository에서 부터 분기하였다면, Git은 거절할 것이다. 이때 개발자는 최신 commits를 fetch한 후 자신의 변경 사항들을 그 위에 rebase해야한다.

만약 로컬 변경사항들이 중심 repository의 commit과 직접적으로 충돌한다면, rebase과정은 일시 정지 되고 매뉴얼하게 충돌을 해결해야 한다.

## 2. Feature Branch Workflow

Feature Branch Workflow의 핵심 아이디어는 모든 feature development는 `main` 브랜치가 아닌 각자의 branch에서 이루어져야 한다는 것이다. 이는 몇가지 장점을 제공한다.
    - 여러 개발자들이 main codebase를 방해하지 않고 각자의 기능에 집중할 수 있다. 
    - main 브랜치는 절대 broken 코드를 포함하지 않게 된다.
    - Pull request를 활용할 수 있도록 한다. 이는 어떤 branch에 대해 토론을 할 수 있는 좋은 방법이다.

### How it works

Feature Branch Workflow는 중심 repository, `main` 브랜치가 공식적인 프로젝트의 history를 나타낸다고 가정한다. 개발자들은 매번 새로운 기능 개발을 시작할 때 새로운 브랜치를 만들어서 사용한다. 이러한 브랜치들은 서술적인 이름을 가져야 한다. (ex. animated-menu-items, issue-#1061)

### Pull requests

만약 어떠한 기능의 개발이 끝난다면, 그 기능을 바로 `main`으로 merge하는 것이 아니라 pull request를 생성한다. 이는 이 기능이 main codebase에 추가되기 전에 다른 개발자들이 변경 사항에 대해 리뷰하도록 해준다. 

또한 만약 개발자가 특정 기능 개발에 대해서 도움이 필요할 때도 pull request를 생성할 수 있다.

Pull request가 받아들여지면, Centralize Workflow와 비슷하게 진행된다. 우선 로컬 `main`이 upstream main과 동기화 되어있는지 확인후, feature branch를 `main`에 merge하고 `main`을 중심 repository에 push 한다.

## 3. Gitflow Workflow

Gitflow Workflow란 지속적인 소프트웨어 개발과 DevOps 관행들을 이행하는 것을 도와주는 git workflow이다. Gitflow Workflow는 엄격한 branching 모델을 정의한다. Gitflow는 계획된 출시 사이클을 가지는 프로젝트에 이상적이다. 

### How it works

Gitflow는 `main`, `develop` 2개의 브랜치로 프로젝트의 history를 기록한다. `main` 브랜치는 공식적 출시 history를 지니며, `develop` 브랜치는 feature 브랜치들의 통합 브랜치로 운영된다. `main` 브랜치에 모든 commit에 대해서는 버전 number를 tag하면 편리하다.

1. develop branch를 생성하고 push한다.
```
    git branch develop
    git push -u origin develop
```
    이 브랜치는 프로젝트의 모든 history를 포함한다. `main` 브랜치는 요약된 버전을 포함한다.

2. Feature branches : 모든 feature들은 각자의 branch를 지니며, 통합은 `develop` 브랜치에 한다. Feature는 절대 `main` 브랜치에 상호작용하지 않는다.
```
    git checkout develop
    git checkout -b feature_branch
```

3. Finishing a feature branch : feature 개발이 끝나면, feature_branch를 `develop`에 merge한다. 
```
    git checkout develop
    git merge feature_branch
```

4. Release Branches: `develop` 브랜치가 출시를 위한 충분한 기능들을 가지게 되었다면, `release` 브랜치를 develop에서 fork한다. `release` 브랜치를 생성하는 것은 다음 출시 사이클을 시작시키며, 오직 bug fixes, documentation generation 그리고 다른 release-oriented tasks들만이 이 브랜치에 들어갈 수 있다. 
    ```
        git checkout develop
        git checkout -b release/0.1.0
    ```
    출시 준비가 끝나면, `release` 브랜치는 `main` 브랜치로 merge 되며, 새로운 버전 number가 tag된다. 또한, `develop` 브랜치로 다시 merge 되어야 한다.
    ```
        git checkout main
        git merge release/0.1.0
    ```

    출시 준비를 위해 하나의 다른 브랜치를 사용하는 것은 다른 팀들이 새로운 기능 개발에 동시에 착수할 수 있게 해준다.

5. Hotfix Branches : `hotfix` 브랜치들은 빠르게 코드를 고치기 위해 사용된다. `release` 브랜치와 유사하지만, `main` 브랜치에서 fork된다는 점이 다르다. 
    ```
        git checkout main
        git checkout -b hotfix_branch
    ```
    Fix가 완료되면, 바로 `main`과 `develop`에 merge 되어야 한다. 또한 `main`에는 새로운 버전 번호가 태그된다.

    ```
    git checkout main
    git merge hotfix_branch
    git checkout develop
    git merge hotfix_branch
    git branch -D hotfix_branch
    ```

    Hotfix는 workflow를 방해하지 않고 빠르게 issue를 해결하도록 도와준다.



출처 : https://www.atlassian.com/git/tutorials/comparing-workflows
