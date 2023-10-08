---
title: "git 특정 파일만 체리픽 하는 방법"
excerpt: "git cherry-pick ..."

categories:
  - etc
tags:
  - git
# last_modified_at: 2022-07-10
toc: true
# published: false
---

이 기능을 써보게 될 줄은 몰랐는데… 개발 서버에서만 잠시 쓰고 원격 저장소에 커밋할 용도는 아니어서 편하게 써 보았다.

## 체리픽 해보자

상황

A기능 브랜치에서 오랫동안 작업중에, 곧 시작될 이벤트 페이지가 추가됨. 그 상황에서 랜딩페이지를 확인해 보고 싶음.

아무튼 깃의 체리픽은 가져올 브랜치의 이전 변경 사항은 빼고, 특정 커밋의 변경 사항만 갖고 싶을 때, 그 커밋을 지금 브랜치에 복사하는 기능이라고 이해했다.

git merge <branch> 를 하면 이전 커밋들의 변경사항까지 줄줄이 딸려오기 때문에, 불필요한 충돌 해결을 많이 해야한다. 잠깐 저기 커밋을 빌려쓰고 싶을 때 적합한 명령 같지는 않기 때문에 체리픽 해보기로 한다.

### cherry-pick

`git cherry-pick <commit>`

막상 저 커밋을 가져와 보니, 예전에 수정했던 main.scss 파일의 수정 내역이 필요했다. 그 커밋을 다시 체리픽 하려고 봤는데 필요없는게 같이 있었다.

## 커밋 전체 말고 그 커밋의 특정 파일만 픽하고 싶다

[참고 답변](https://stackoverflow.com/questions/5717026/how-to-cherry-pick-only-changes-to-certain-files)

1.일단 필요한 커밋을 체리픽 하지만 노커밋 옵션으로 커밋은 하지 않음

`git cherry-pick -n <commit>`

2.체리픽으로 바뀐것들을 다 unstage 한다

`git reset HEAD`

3.원하는 파일만 git add 한다

`git add src/scss/main.scss`

4.그 외 추적되지 않은 파일과 디렉토리를 삭제한다

`git clean -d -f`

5.git add했던 파일을 커밋해둔다

`git commit -m '~~ 때문에 임시로 추가'`

6.빌드와 배포

이번 상황에서는 잠깐만 확인하는게 목적이었기 때문에,
확인이 끝나고는 `git reset --hard <commit>` 으로 체리픽 하기 전으로 돌아왔다. 없었던 일로..!
