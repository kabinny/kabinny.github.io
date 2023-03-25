---
title: "Vue에서 동적 ref 사용하기"
excerpt: "vue dynamic ref"

categories:
 - Vue
tags:
 - ref
 - 
 - 
# last_modified_at: 2021-07-17
toc: true
---


Vue2버전 사용!

vue에서 DOM을 다루기 위해서는 ref를 사용하는데, ref의 이름을 동적으로 사용하는 방법을 정리해본다.

예를 들어 메뉴바 아래 현재 선택된 메뉴 표시하는 디자인인 경우. 새로 만드는 게 아니라 기존에 되어 있는 것에서 수정을 하려다 보니 완벽히 마음에 들지는 않지만, 심각했던(?) ui 오류는 고칠 수 있었다.

## 예시 코드

코드펜 오랜만에 들어가봤더니 뷰 코드펜 생겨서 써 봤다. 뷰를 널리 이롭게 하는 데에 도움이 될 것 같다!

예시 코드에서는 일반 스테틱 ref와 동적 ref를 둘 다 사용해 보았다.

<p class="codepen" data-height="300" data-theme-id="light" data-default-tab="js,result" data-slug-hash="WNgJPyZ" data-user="kabinny" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/kabinny/pen/WNgJPyZ">
  vue-dynamic-ref</a> by kabinny (<a href="https://codepen.io/kabinny">@kabinny</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

## template

`:ref=”menu.title”`

메뉴는 배열에서 v-for로 반복하기 때문에 ref를 각각 다르게 넣어주기 위해 ref의 이름을 변수로 넣어주었다.

`<div class="active-bar" ref="activeBar"></div>` active-bar에는 일반적인 방법으로 ref 이름을 달아주었다.

```html
{% raw %}
<template>
  <div id="app">
    <div class="menu-wrap">
      <ul>
        <li 
            v-for="menu in menuList"
            :ref="menu.title"
            @click="getActiveStyle(menu.path)">
          {{ menu.title }}
          <!-- 원래는 라우터 이동을 위한 링크 -->
          <!-- <router-link to="menu.path">{{ menu.title }}</router-link> -->
        </li>
      </ul>
      
      <div class="active-bar" ref="activeBar"></div>
    </div>
  </div>
</template>
{% endraw %}
```

## script

### getActiveStyle

selectedMenu는 현재 선택된 메뉴 이름이 무엇인지 가져온다.

⇒ 그 이름을 가진 요소를 $refs에서 찾는다.

⇒ 찾은 요소의 offsetLeft 값을 activeBar의 left 값으로,

offsetWidth 값을 width 값으로 넣는다.

### watch

메뉴에 따라 보이는 컨텐트 내용이 달라지는데, 현재 서비스에서는 그에 따라 라우터 변경도 된다. 그렇기 때문에 라우터가 변경될 때 마다 다시 호출이 필요해서 watch가 라우트 변경을 감시한다.

```jsx
<script>
export default {
  data() {
    return {
      menuList: [{
        title: 'one',
        path: '/one',
      }, {
        title: 'twotwo',
        path: '/twotwo',
      }, {
        title: 'threethreethree',
        path: '/threethreethree',
      }]
    };
  },
  methods: {
    async getActiveStyle(path = '/one') {
      // 원래는 path를 파라미터로 받지 않고 url의 path를 가져옴
      // const path = this.$route.path
      const selectedMenu = path.split('/')[1]
      
      await this.$nextTick()
      
      const selectedEl = this.$refs[selectedMenu][0]
      
      this.$refs.activeBar.style.left = `${selectedEl.offsetLeft}px`
      this.$refs.activeBar.style.width = `${selectedEl.offsetWidth}px`
    }
  },
  watch: {
    '$route'(to, from) {
      // 원래는 메뉴 누르면 라우트도 바뀌기 때문에 라우트가 바뀔 때마다 호출
      this.getActiveStyle()
    }
  },
  mounted() {
    this.getActiveStyle()
  },
};
</script>
```

## style

스크립트에서 active-bar의 left값과 width값을 바꿔주기 때문에 position: absolute로 하고 left, width 값은 임시값을 넣었다.

active-bar이동할 때 부드러운 전환 효과를 위해 transition을 넣어준다.

```scss
<style lang="scss">
  .menu-wrap {
    position: relative;
    border: 2px solid aliceblue;
    ul {
      display: flex;
      margin: 0;
      padding: 0;
      list-style: none;
      
      li {
        padding: 10px 20px;
        cursor: pointer;
        
      }
    }
    
    .active-bar {
      position: absolute;
      bottom: 0;
      left: 0;
      height: 5px;
      width: 10px;
      background-color: rgba(salmon, 0.4);
      transition: all 0.3s;
    }
  }
</style>
```

## issue

### \$refs\[이름\]\[0\].$el

무언가의 버전 때문인 것 같은데, refs에서 요소 가져올 때 잘 안돼서 콘솔에 찍어 보고 구글링 해보고 따라해서 해결되었다.

실제 작업했던 코드에서는 이렇게 해야 정상 작동 했다.

`const selectedEl = this.$refs[selectedName][0].$el`

뷰 버전: `"vue": "^2.6.11"`

코드펜은 아마 뷰2 최신 버전인데 이렇게 작성했을 경우 오히려 작동을 안했다.

### $nextTick()

돔 요소의 크기나 위치를 가져올 때 문제였는데 돔이 다 완료되기 전에 사이즈를 가져오려고 하면 제대로 값을 가져올 수 없다. 그래서 찾아 보다가 `$nextTick`이라는 것을 알게 되었다.

async 함수 내에서 `await this.$nextTick()` 을 사용하면 이후의 코드는 돔 업데이트가 완료되면 실행된다.

[https://ko.vuejs.org/api/general.html#nexttick](https://ko.vuejs.org/api/general.html#nexttick)