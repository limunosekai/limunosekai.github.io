---
layout: post
title: Git Command Cheat-sheet, flow 이해하기 (계속 추가)
category: Git
permalink: /git/:year/:month/:day/:title/
tags: [Git]
comments: true
---

---

## Git Command Cheat-sheet, flow 이해하기 (계속 추가)

---

1. **Git Command Cheat-sheet**

   ![1](/assets/post/git/1.PNG)

   ![2](/assets/post/git/2.PNG)

   ![3](/assets/post/git/3.PNG)

   ![4](/assets/post/git/4.PNG)

   <br>

2. **그 외 자주 쓰이는 커맨드 (계속 추가)**

   * `git checkout -t <remote_path/branch_name>` : 원격 브랜치 선택
   * `git branch -r` : 원격 브랜치 목록보기
   * `git branch -a` : 로컬 브랜치 목록보기
   * `git branch -m <name> <newName>` : 브랜치 이름 변경
   * `git branch -d/-D name` : 브랜치 삭제(-d), 강제 삭제(-D)
   * `git push <remote_name> --delete <branch_name>` : 원격 브랜치 삭제

   <br>

3. **기타 알게된 것 (계속 추가)**

   * Open Source Project 같은 경우엔 바로 `clone` 하기보다 `fork`하여 내 repo에 복사 후 `clone`하여 작업한다
   * 일반적인 경우는 초대받은 프로젝트를 `clone`해서 로컬에서 `branch`를 나누어 작업한다. 로컬에서도 origin과 마찬가지로 master / develop을 만들어 개발은 develop에서 진행한다. 작업이 끝나면 `commit`하여 origin에 `push`하고 `pull request`(PR)를 작성한다. reviewer를 설정하고 필요에 따라 assignee, milestone 등도 설정한다. 그러면 팀원들이 코드리뷰를 하고 approve  되면 `merge`하여 origin에 병합하고 내 로컬 master 브랜치에서 origin의 코드를 `pull` 하여 동기화하고 최신 코드를 유지한다. 작업이 끝난 브랜치는 삭제한다.
   * `pull request`가 approve되는 규칙은 팀 마다 다르지만 대체로 과반수의 동의가 있어야하고 수정이 필요할 경우 코드를 수정하여 다시 `commit`, `push` 한다
   * PR을 merge하는 방법은 세 가지가 있다. 
     * merge commit : master에 병합되고 commit log에 남는다
     * rebase merge : (그림으로 보는게 제일 빠를듯..)
     * squash merge : PR의 commit log를 하나로 추려서 master에 병합하는 방법
   * 원격이건 로컬이건 master를 직접 조작하는 것은 피해야한다
   * 남이 올린 PR에 Suggestion이 있을 경우 md 파일 작성시처럼 백틱 3개와 언어를 이용해 코드를 작성한다. 이는 github에서 제공하는 기능이다
   * milestone은 일종의 목표? 계획? 같은 개념으로 진행의 척도를 한 눈에 볼 수 있다

<br>

---

### 참고자료

---

[ATLASSIAN Bitbucket](https://www.atlassian.com/git/tutorials/atlassian-git-cheatsheet)