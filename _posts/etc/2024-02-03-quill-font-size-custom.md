---
title: "Quill 글자 크기 커스텀"
excerpt: "클래스 방식으로 폰트 사이즈 세분화하기"

categories:
  - etc
tags:
  -
# last_modified_at: 2022-07-10
toc: true
# published: false
---

[Quill](https://quilljs.com/)은 간단하게 쓸 수 있는 리치 텍스트 에디터이다. 기본 글자 크기 옵션은 4가지 크기밖에 없다. Normal 보다는 크지만 Large 보다는 작은 사이즈가 필요하게 되어서 추가하게 되었다.

폰트 사이즈 커스텀 검색하면 인라인 방식 예제가 많이 나온다. [클래스와 인라인 방식](https://quilljs.com/guides/how-to-customize-quill/#class-vs-inline) 중에 좀 더 유연하게 처리하기 위해서 클래스 방식 선택했다. 인라인 스타일 방식으로 하면 나중에 값을 바꾸거나 할 때 이전에 작성했던 글까지 바꾸는 처리가 어려울 거 같았기 때문이다. 반면 클래스 방식은 css에서 값만 바꿔주면 된다.

각 사이즈 별로 적절한 값은 고민끝에 [tailwindcss](https://tailwindcss.com/docs/font-size)의 값을 참고 했다. 많이 큰 사이즈는 안 쓰기 때문에 2xl 까지만 가져왔다.

## 추가한 부분

공식 문서의 [기본 예제](https://quilljs.com/playground/#quill-playground)에 추가를 해 보았다.

### JS

```js
let Size = Quill.import("attributors/class/size");
const fontSize = ["XS", "Small", false, "Large", "XL", "2XL"];
Size.whitelist = fontSize;
Quill.register(Size, true);
```

사이즈 속성을 불러와서 내가 쓰고 싶은 이름을 추가한다. 중간에 `false`는 Normal로 나오게 된다. 기본으로 선택되고, 아무런 클래스가 붙지 않는다.

```js
modules: {
    toolbar: [
      [{ header: [1, 2, false] }],
      ['bold', 'italic', 'underline'],
      ['image', 'code-block'],
      [{ size: ['XS', 'Small', false, 'Large', 'XL', '2XL'] }]
    ]
  },
```

툴바 옵션에 사이즈를 추가해준다. 위에서 쓴 것이랑 똑같이 넣는다.

### CSS

```scss
.ql-snow.ql-toolbar .ql-picker.ql-size {
  .ql-picker-item,
  .ql-picker-label {
    &[data-value]::before {
      content: attr(data-value);
    }
  }
}
```

좀 예쁘게 하려면 툴바도 따로 만들어서 연결하는 게 좋은데, 최대한 추가하는 것 없이 간결하게 하고 싶었기 때문에 옵션 이름은 css로 처리했다.

툴바에서 사이즈 클릭해서 드롭다운 된 메뉴를 보면 .ql-picker-options 안에

`<span tabindex=”0” role=”button” class=”ql-picker-item” data-value=”XS”></span>`

각각 이렇게 들어가 있다. 내가 추가했던 이름이 data-value에 들어가 있고, 기본 Normal은 data-value가 없다.

텍스트는 `::before`에서 넣고 있기 때문에 `::before`에 `data-value`의 값을 넣는 css를 추가한다.

선택자 `&[data-value]::before`에서 `[data-value]`를 빼면, data-value가 없는 Normal도 선택되기 때문에 오류가 난다.

```scss
.ql-editor {
  .ql-size-XS {
    font-size: 0.75em;
  }
  .ql-size-Small {
    font-size: 0.875em;
  }
  .ql-size-Large {
    font-size: 1.125em;
  }
  .ql-size-XL {
    font-size: 1.25em;
  }
  .ql-size-2XL {
    font-size: 1.5em;
  }
}
```

여기까지 하고 텍스트를 입력하면 선택한 사이즈에 해당하는 텍스트가 `<span class=”ql-size-Large”>Large 입력한 텍스트</span>` 이렇게 나온다.

그래서 각 클래스 별로 폰트 사이즈를 지정해줬다. 폰트 사이즈는 마음대로 지정할 수 있지만, 에디터에서 작성한 글을 여러군데에서 사용하고 있고 각각 기본 폰트 사이즈가 달라서 단위를 em으로 적었다. Normal 사이즈를 선택해서 쓴 부분은 아무런 클래스가 붙지 않고 기본 1em이다. 나머지는 상대적인 사이즈로 작거나 크게 나온다.

## 완성

<p class="codepen" data-height="300" data-theme-id="light" data-default-tab="js,result" data-slug-hash="mdoXLwo" data-user="kabinny" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/kabinny/pen/mdoXLwo">
  Quill custom text size by class</a> by kabinny (<a href="https://codepen.io/kabinny">@kabinny</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>
