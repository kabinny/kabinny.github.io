---
title: "computed로 전체선택 기능 만들기"
excerpt: "computed set 하는 방법"

categories:
  - Vue
tags:
  - computed
  - v-model
toc: true
---

전체 선택 체크박스를 눌렀을 때, 나머지 체크박스들을 `document.querySelectorAll`로 선택해서 하나씩 체크 상태로 토글하는 것을 보고.

_아.. 이건 아닌거 같아.._

나머지를 하나씩 클릭하다가 전부 선택했다면 전체 선택의 체크박스도 선택된 상태로 만드는 것은 금방 떠올랐다. `computed` 속성을 사용해서 계산된 값을 가지게 하면 되기 때문이다.

요컨대, 체크 리스트의 길이와 체크 표시된 리스트의 길이가 같으면, 전부 체크됐다는 뜻이기 때문에 전체 선택이 된 것이다.

```js
this.checkList.length === this.checkList.filter((el) => el).length;
```

그래서 전체 선택의 체크박스의 v-model 값으로 computed 값을 넣는다. 하나씩 체크하다가 전부 체크하면 전체 체크박스도 자동으로 체크된다.

이제 문제는 전체 선택 체크박스 자체를 눌렀을 때, 오류가 발생한다. computed가 기본적으로 값을 계산해서 던져주기는 하지만.. 그 반대가 가능할 지 처음에는 의문이었다. 그리고 이런 자주 묻는 질문에 있을 거 같은 기능은 이미 잘 구현되어 있다.

## computed get, set

computed에 set을 하고 싶다면 기본적으로 작성하는 코드 구조가 조금 바뀐다.

보통 이렇게 작성해서 사용할 값을 반환했다면.

```js
  computed: {
    isCheckedAll() {
      return (
          this.checkList.length === this.checkList.filter((el) => el).length
        )
    },
  },
```

get, set을 둘 다 쓰고 싶으면 이렇게 객체 형태로 쓰면 된다.
get에는 값을 계산해서 반환하는 함수를 적고, set에는 값이 입력될 때 하고 싶은 일을 적으면 된다.

```js
  computed: {
    isCheckedAll: {
      get: function () {
        return (
          this.checkList.length === this.checkList.filter((el) => el).length
        );
      },
      set: function (value) {
        // value는 바뀔 값이 온다. 체크박스니까 클릭할 때마다 true, false가 번갈아 오게 된다.
        // value 값을 활용해서 어떤 값이 오는지에 따라 할 일을 한다..
      },
    },
  },
```

## 예시 코드

체크박스 각각 v-model을 연결한다. 전체 선택 체크박스는 computed에 정의한 isCheckedAll. 체크리스트의 값은 checkList 배열의 값으로 관리했다.

```html
<div class="check-container">
  <label class="check-all">
    <input type="checkbox" v-model="isCheckedAll" />
    check all
  </label>

  <label>
    <input type="checkbox" v-model="checkList[0]" />
    check1
  </label>
  <label>
    <input type="checkbox" v-model="checkList[1]" />
    check2
  </label>
  <label>
    <input type="checkbox" v-model="checkList[2]" />
    check3
  </label>
</div>
```

isCheckedAll get: checkList 길이와 checkList의 true만 필터한 것의 길이가 같으면 true! 하나라도 false면 전체 선택도 false.

isCheckedAll set: 이 전체 선택 버튼을 직접 눌렀을 때!, 체크할 때는 즉 value가 true인 경우 checkList를 전체 true인 상태도 바꾼다. value가 false인 경우에는 checkList를 전체 false인 상태로 바꾼다.

```js
export default {
  data() {
    return {
      checkList: [false, false, false],
    };
  },
  computed: {
    isCheckedAll: {
      get: function () {
        return (
          this.checkList.length === this.checkList.filter((el) => el).length
        );
      },
      set: function (value) {
        if (value) {
          this.checkList = [true, true, true];
        } else {
          this.checkList = [false, false, false];
        }
      },
    },
  },
};
```

<p class="codepen" data-height="300" data-theme-id="light" data-default-tab="js,result" data-slug-hash="zYXpXBb" data-user="kabinny" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/kabinny/pen/zYXpXBb">
  check all with computed properties</a> by kabinny (<a href="https://codepen.io/kabinny">@kabinny</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>
