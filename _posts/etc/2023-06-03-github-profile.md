---
title: "깃허브 프로필 꾸미기 기록"
excerpt: "깃꾸…깃허브 프로필 꾸미기"

categories:
  - etc
tags:
  -
# last_modified_at: 2022-07-10
toc: true
# published: false
---

아주 오랫동안 미뤄왔던 깃꾸의 시간이 왔다.

깃헙 맞팔 해달라고 해야되는데 초라한 명함을 내미는 기분이 들었기 때문에 조금이라도 특별하게 꾸며보고 싶었다.

뭐를 써야될 지도 처음에는 잘 몰랐지만 다른 사람들이 꾸민 것을 보고 조금 감이 왔다. 제한된 사양 안에서 이것저것 해볼 수 있는게 재밌었다. 요는 최대한 원시 html을 떠올리며 작업을 하는 것이다.

그리고 다음에 또 프로필을 수정하게 될 나에게. 로컬에서 작업하지 말고 깃허브에서 보면서 작업하자~

## 사용 기능

### 깃허브 프로필 저장소

처음 만들 때 허겁지겁 만들어서 기억이 안나는데, 깃허브의 이스터에그 같은 것이라고 한다.

유저이름과 똑같은 이름의 저장소를 생성하면 `README.md` 파일 내용을 깃허브 프로필 페이지로 사용할 수 있다.

### readme-typing-svg

[https://readme-typing-svg.herokuapp.com/demo/](https://readme-typing-svg.herokuapp.com/demo/)

한 글자씩 타이핑 효과를 간단하게 줄 수 있다. 폰트에 따라서는 Height 값을 넉넉히 주지 않으면 잘려보일 수 있다. 움직이는 요소가 너무 많으면 정신사나울 수 있는데 하나 정도는 넣기 좋은 것 같다.

안정된 호출과 심화 커스텀을 위해 저장소 포크해서 직접 호스팅해도 된다고 한다.

### github-readme-stats

[https://github.com/anuraghazra/github-readme-stats/blob/master/docs/readme_kr.md](https://github.com/anuraghazra/github-readme-stats/blob/master/docs/readme_kr.md)

사용법이 간단하고, 커스텀이 자유로웠다. 사용할 수 있는 종류가 엄청 많았는데, 언어 사용량 통계만 쓰고 싶었다. 가장 많이 사용된 언어 비율을 심플하게 볼 수 있다. 뱃지 색상과 일관적으로 보일 수 있게 배경색 정도만 커스텀했다.

### shields.io

[https://shields.io/](https://shields.io/)

처음에 이거 봤을 때는 이미 기술 스택 별로 미리 정해진 색이 있는 건줄 알았는데(보통 특정 기술 스택은 특정 색을 썼어서 그렇세 여겼던 것 같다), 아이콘, 텍스트 내용, 색상 모든 것이 마음대로 커스텀할 수 있는 것이었다.

배경색, 스타일, 로고 아이콘, 글자 색만 지정해서 썼다. 오밀조밀한 느낌을 원해서 flat-square 스타일을 사용했다.

```markdown
기본
<img src="https://img.shields.io/badge/{텍스트}-{배경색}?style={뱃지스타일}&logo={아이콘이름}&logoColor={로고색}"/>

예시
<img src="https://img.shields.io/badge/HTML5-E55D87?style=flat-square&logo=HTML5&logoColor=white"/>
```

## 수정

### markdown에서 css는 어디까지 가능

마크다운은 간결한 만큼 표준이 없어서 서비스마다 어디까지 지원되는지, 어떤 것까지 지원되는지 불분명한 거 같다. 기본 마크다운 문법은 꾸미기에 불편해서(꾸미라고 만든게 아니니…) 그냥 html로 css 넣으면서 신나게 꾸미다가 깃헙에서 확인해 보니 하나도 적용 안되고 있더라.

vscode의 md 미리 보기에서는 다 적용이 되어 있어서 더 헷갈렸던 것 같다. 아무튼 깃헙 미리보기로 확인하면서 하나씩 돌려놨다.

그리고 jekyll 블로그 쓸 때 링크 새창으로 열기 하고 싶을 때는 마크다운 문법이 아닌 a 태그를 써서 `target=”_blank”`를 지정해 주면 먹혔는데 여기는 안되었다. markdown과 html의 애매한 줄타기를 하는 기분이었다.

### width

기술 스택 뱃지들 배경색 그라데이션이 예쁘게 보이기 위해서는, 좁은 폭으로 인한 원치 않은 줄바꿈이 있으면 안됐다. css로 width를 지정할 수 없어서 `<br />` 로 레이아웃을 맞췄다.

예쁘게 한 줄에 5개씩 넣고 대만족 하고 자려고 누웠는데, 폰에서 보니까 마지막 뱃지 줄바꿈 돼서 레이아웃이 구려지는 것이었다. 눈물을 머금고 아침에 다시 한 줄에 4개씩 배치로 바꾸고, 뱃지 배경색 지정도 다시 했다.

반응형 디자인으로 모든 디바이스 폭에 다 맞출 수는 없기 때문에 좁은 폭을 기준으로 만들었다. pc같은 큰 화면에서 보면 조금 답답해 보일 수도 있지만, 최선을 다 했다!

## 최종 코드

a태그 `target=”_blank”`는 작동하지 않는데 일단 그냥 두었다.

지금 다시보니 스킬 분류를 새로 해야할 것 같다.

```markdown
<div align='center'>
  <h1> 
    <img src="https://readme-typing-svg.herokuapp.com?font=Dancing+Script&color=a190b6&size=50&height=80&vCenter=true&center=true&pause=1000&lines=Hi,+I'm+Kabin!" alt="Typing SVG" />
  </h1>
  <p>Front-End Developer</p>

  <a href="https://kabinny.github.io/" target="_blank">
    <img src="https://img.shields.io/badge/BLOG-a190b6?style=flat-square&logo=Github&logoColor=white"/>
  </a>
  
  <br />
  <img src='https://github-readme-stats-sigma-five.vercel.app/api/top-langs/?username=kabinny&layout=compact&bg_color=30,E55D87,5FC3E4&title_color=fff&text_color=fff' alt='Most Used Languages' />

  <h2>Tech Stack</h2>
  <div>
    <img src="https://img.shields.io/badge/HTML5-E55D87?style=flat-square&logo=HTML5&logoColor=white"/>
    <img src="https://img.shields.io/badge/CSS3-b87fa6?style=flat-square&logo=CSS3&logoColor=white"/>
    <img src="https://img.shields.io/badge/CSSModules-8f9fc3?style=flat-square&logo=CSSModules&logoColor=white"/>
    <img src="https://img.shields.io/badge/SCSS-5FC3E4?style=flat-square&logo=Sass&logoColor=white"/> <br />
    <img src="https://img.shields.io/badge/JavaScript-E55D87?style=flat-square&logo=JavaScript&logoColor=white"/>
    <img src="https://img.shields.io/badge/React-b87fa6?style=flat-square&logo=React&logoColor=white"/>
    <img src="https://img.shields.io/badge/Recoil-8f9fc3?style=flat-square&logo=React&logoColor=white"/>
    <img src="https://img.shields.io/badge/Gatsby-5FC3E4?style=flat-square&logo=Gatsby&logoColor=white"/>  <br />
    <img src="https://img.shields.io/badge/Vue.js-E55D87?style=flat-square&logo=Vue.js&logoColor=white"/>
    <img src="https://img.shields.io/badge/Vuetify-b87fa6?style=flat-square&logo=Vuetify&logoColor=white"/> 
    <img src="https://img.shields.io/badge/Vuex-8f9fc3?style=flat-square&logo=Vue.js&logoColor=white"/>
    <img src="https://img.shields.io/badge/TailwindCSS-5FC3E4?style=flat-square&logo=TailwindCSS&logoColor=white"/> <br />
    <img src="https://img.shields.io/badge/FontAwesome-E55D87?style=flat-square&logo=FontAwesome&logoColor=white"/> 
    <img src="https://img.shields.io/badge/ChakraUI-b87fa6?style=flat-square&logo=ChakraUI&logoColor=white"/>
    <img src="https://img.shields.io/badge/Axios-8f9fc3?style=flat-square&logo=Axios&logoColor=white"/>
    <img src="https://img.shields.io/badge/Git-5FC3E4?style=flat-square&logo=Git&logoColor=white"/> <br />
    <img src="https://img.shields.io/badge/SVN-E55D87?style=flat-square&logo=Subversion&logoColor=white"/> 
    <img src="https://img.shields.io/badge/Jekyll-b87fa6?style=flat-square&logo=Jekyll&logoColor=white"/>
    <img src="https://img.shields.io/badge/VSCode-8f9fc3?style=flat-square&logo=VisualStudioCode&logoColor=white"/>
    <img src="https://img.shields.io/badge/Markdown-5FC3E4?style=flat-square&logo=Markdown&logoColor=white"/>
  </div>

  <h2>Skills</h2>
  <div>
    <img src="https://img.shields.io/badge/Photoshop-E55D87?style=flat-square&logo=AdobePhotoshop&logoColor=white"/>
    <img src="https://img.shields.io/badge/Illustrator-b87fa6?style=flat-square&logo=AdobeIllustrator&logoColor=white"/>
    <img src="https://img.shields.io/badge/XD-8f9fc3?style=flat-square&logo=AdobeXD&logoColor=white"/>
    <img src="https://img.shields.io/badge/Figma-5FC3E4?style=flat-square&logo=Figma&logoColor=white"/> <br />
    <img src="https://img.shields.io/badge/Notion-b87fa6?style=flat-square&logo=Notion&logoColor=white"/> 
    <img src="https://img.shields.io/badge/Obsidian-a190b6?style=flat-square&logo=Obsidian&logoColor=white"/>
    <img src="https://img.shields.io/badge/Slack-82a8cb?style=flat-square&logo=Slack&logoColor=white"/>
  </div>

  <h2>Currently Learning</h2>
  <img src="https://img.shields.io/badge/TypeScript-c079a1?style=flat-square&logo=TypeScript&logoColor=white"/>
  <img src="https://img.shields.io/badge/Storybook-82a8cb?style=flat-square&logo=Storybook&logoColor=white"/>

</div>
```

## 마무리

프로필을 바꾼지는 몇달 돼서 쓸까말까 했는데 이번주 블로그 글감이 잘 떠오르지 않아서 .. 가벼운 한 주가 되길 바라는 마음으로 정리해 보았다.

관련해서 찾아보다 보니 잔디 꾸미기도 있던데, 잔디로 글자도 쓰고 이것저것 다 할 수 있더라.
