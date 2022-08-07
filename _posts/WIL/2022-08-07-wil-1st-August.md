---
title: "WIL 8월 1주차"
excerpt: "template"

categories:
  - WIL
tags:
  - '제로베이스 프론트엔드 스쿨'
toc: true
---

8월에는 리액트 스터디를 신청한 만큼, 리액트 위주로 공부할 예정이다. 그리고 틈틈히 컴퓨터공학 강의를 듣는 것이 목표이다. 

## React useEffect 
리액트 앞부분부터 공부중이다.  
[=> 리액트 useEffect 정리한 포스트](/react/react-useEffect/) 


## template 요소

[https://developer.mozilla.org/ko/docs/Web/HTML/Element/template](https://developer.mozilla.org/ko/docs/Web/HTML/Element/template)

js로 html요소 덩어리를 만들때 잠시 담아두는 요소로 사용할 수 있다. html에 렌더링되지 않는다.

`<div>`를 사용할 수도 있지만 단지 요소들을 담아내는 용도라면 template을 사용하는 게 더 적절한 것 같다. 그동안 몰라서 못 썼지만 이제는 활용해야겠다. 

주의할 점은 template을 바로 넣는 게 아니라 template.content을 꺼내서 넣어야 한다.

**사용 예시**
```javascript
const $template = document.createElement('template');
$template.innerHTML = '<section>내용</section>'

document.querySelector('#root').appendChild($template.content) 
```

## 덜덜 떨면서 jsp 수정해본 이야기 

배너이미지 하나와 함께 갑자기 사이트 헤더에 링크를 추가하는 임무가 주어졌다. jsp라는 파일 확장자가 어디선가 본듯 만듯 아리송 했지만 헤더 파일 내부에  spring 키워드를 보는 순간 아! Java구나 했다.  

정말 생전 다뤄 본 적 없는 자바지만, `html` 태그와 반복문은 익숙했기 때문에 적절한 위치에 `a` 태그를 추가할 수 있었다. 이미지 경로는 어떻게 쓰는 지 몰랐지만 다행히 헤더 내에 static 폴더에서 이미지를 불러오는 부분이 있어서 복사 붙여넣기를 하고 적절히 수정했다. html-css 과제하면서 알게된 `rel="noopener noreferrer"`를 써봤다. 

기존의 스타일 시트 파일도 무사히 찾아서 배너 스타일을 추가했다. 기존의 파일 구조나 스타일을 변경할 수는 없어서 `position: absolute`를 사용했다. 

그래서 왜 덜덜 떨면서 수정해 봤냐면 개발 서버도 아니고 실서버에서 작업을 했기 때문이다. 위치가 마음에 들지 않다 하셔서 약간의 스타일 수정을 하면서도 떨렸다. 그리고 윈도우의 원격 데스크톱 데스크톱도 처음 써봤다. 윈도우 서버도 처음 써봤다. 서버는 다 검은바탕에 하얀건 글이요하는 줄 았았다. 

## Python으로 파일 삭제하기

지금 필요한 기능이, 똑같은 구조의 폴더가 여러개 있는데 각각 폴더 안에 공통으로 사용하는 파일들이 있다. 문제는 공통 파일의 수정이 계속 필요한데 하나 수정할 때마다 수동으로 복사 붙여넣기는 한계가 크다는 것이다. 그래서 파이썬으로 깨작깨작 파일 복사 프로그램을 만들게 되었다. 

열심히 쓰다보니 매우 만족이었다. 그리고 파일 삭제도 필요한거 같아서 이번 주에 만들었다. 복사할 때 파일을 삭제하고 옮기는 로직이 원래 있어서 삭제는 엄청 쉽게 만들었다. 

```python
from copy import copy
import os
from os import path
import shutil

# 이 부분 수정하고 실행하기
fileName = 'script.js' # 여기에 삭제할 파일 이름 

# 디렉토리 경로
dirs = [ # 복사 폴더 경로 리스트 선언
  '복사될 폴더경로',
]


# 파일 복사 함수
def delFile(targetDirs, targetFile):
    for targetDir in targetDirs:
        if path.exists(targetDir + '\\' + targetFile): # 파일이 있으면
            os.unlink(curretargetDirntDir + '\\' + targetFile) # 파일을 삭제


delFile(dirs, fileName)
```