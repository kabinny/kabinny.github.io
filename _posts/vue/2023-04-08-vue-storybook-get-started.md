---
title: "스토리북 튜토리얼 with Vue - 시작 및 간단한 컴포넌트 적용"
excerpt: "storybook + vue"

categories:
 - Vue
tags:
 - 
# last_modified_at: 2021-07-17
toc: true
---


스토리북은 독립적으로 컴포넌트를 개발하기 편하게 해주는 툴, UI의 스웨거같은 것.

[Storybook Tutorials Vue](https://storybook.js.org/tutorials/intro-to-storybook/vue/en/get-started/)

vue를 위한 튜토리얼은 한국어 번역이 없어서, 구글 페이지 번역과 함께 공부해 본다. 

## 시작하기

스토리북은 빈 폴더에 깔 수 있게 만들어진 것이 아니기 때문에 이미 만들어진 프로젝트가 필요하다. 예제는 이미 다 세팅된 프로젝트를 받고 시작하게 하지만 기존의 프로젝트에 스토리북을 도입하는 방법은 기본 문서에서 찾았다. 

init만 하면 알아서 어떤 프레임워크 썼는지 파악한다. node16버전 이상이 필요하다. 

```bash
# Add Storybook: 프로젝트의 루트에서 스토리북 추가
npx storybook init

# 스토리북 시작. 로컬호스트 6006 포트에서 웰컴 화면이 나타남
npm run storybook
```

## 기억하면 좋은 개념

- 컴포넌트 하나에 스토리가 여러개 쓸 수 있다고 보면 된다.
- 데이터를 가공해서 넘겨주는 컴포넌트(예제의 TaskList.vue)와 렌더링만 하는 컴포넌트(예제의 PureTaskList.vue)를 분리하는 것이 좋다. 렌더링만 하는 컴포넌트의 스토리를 작성하게 된다.
- 다른 스토리 js 파일에 작성한 스토리를 가져와서 사용할 수 있다.

## [컴포넌트이름].stories.js

예제의 `Task.stories.js`

```js
import 컴포넌트이름 from './컴포넌트이름.vue' // 스토리를 매칭할 컴포넌트 불러오기
import { action } from '@storybook/addon-actions' // 액션 쓸 거라서 불러오기

// 기본 설정
export default {
  // 컴포넌트
  component: 컴포넌트이름,
  // 스토리에서 쓰이지만 스토리북 앱에서 렌더링하면 안되는 정보 
  excludeStories: /.&Data$/,
  // 스토리북 앱에서 컴포넌트를 참조하는 이름
  title: '스토리북앱 사이드 바에서 사용할 컴포넌트이름',
  // 각 스토리에서 사용할 액션을 지정, 예제에서는 pin 했을 때, 완료했을 때를 이벤트로 잡았다. 
  argTypes: {
    액션이름1: {},
    액션이름2: {},
  },
}

// 액션 데이터
export const actionsData = {
  // 여기서 지정한 액션의 이름으로 컴포넌트 내부에서 emit하게 된다. 
  액션이름1: action('액션-이름-1'),
  액션이름2: action('액션-이름-2'),
}

// 템플릿 생성 - 스토리를 여러개 작성할 것이기 때문에 반복을 피하기 위해.
const Template = args => ({
  components: { 컴포넌트이름 },
  setup() {
    return { args, ...actionData }	
  },
  template: '<컴포넌트이름 v-bind="args" />',
})

// Default 스토리
export const Default = Template.bind({})
Default.args = {
  // 컴포넌트에 넘겨줄 필요한 거 넣어주면 됨. 일단 예제 그대로..
  task: {
    id: '1',
    title: 'Test Task',
    state: 'TASK_INBOX',
  },
}

// Pinned 스토리
export const Pinned = Template.bind({})
Pinned.args = {
  // 기본 스토리의 인수에서 state만 다른 값으로 넣음
  task: {
    ...Default.arge.task,
    state: 'TASK_PINNED',
  }
}

// Archived 스토리
export const Archived = Template.bind({})
Archived.arge = {
  task: {
    ...Default.args.task,
    state: 'TAST_ARCHIVED',
  }
}
```

## [컴포넌트이름].vue

예제에서 `Task.vue`
기능적인 내용 위주로 쓰느라 클래스 이름 등은 빼고 썼을 수 있다. (아직 vue3 익숙하지 않아서 설명에서 버벅거릴 수 있다..)

```html
<template>
  <!-- 프롭으로 넘어온 task의 state 값에 따라 클래스를 다르게 붙여줌 -->
  <div :class="classes">
    <!-- 왼쪽 체크박스 부분, 체크박스 클릭하면 archiveTask 실행 -->
    <label 
      :for="'checked' + task.id"
      :aria-label="'archiveTask-' + task.id" >
      <input
        type="checkbox"
        :checked="isChecked"
        disabled
        :name="'checked' + task.id"
        :id="'archiveTask-' + task.id" />
      <!-- css로 input은 안보이게 하고, 이 span으로 체크박스 모양을 만들었다. -->
      <span @click="archiveTask"></span>
    </label>

    <!-- 할 일 텍스트 보여지는 부분 title -->
    <label
      :for="'title-' + task.id"
      :aria-label="task.title" >
      <input
        type="text"
        readonly
        :value="task.title"
        :id="'title-' + task.id"
        name="title"
        placeholder="Input title" />
    </label>	

    <!-- pin 할 수 있는 버튼, 클릭하면 pinTask 실행 -->
    <button
      v-if="!isChecked"
      @click="pinTask"
      :id="'pinTask-' + task.id"
      :aria-label="'pinTask-' + task.id">
      <span />
    </button>
  </div>
</template>
```

```js
<script>
  import { reactive, computed } from 'vue'

  export default {
    name: '컴포넌트이름',
    // 프롭으로 task 오프젝트 하나가 넘어와서 렌더링 하게됨
    props: {
      task: {
        type: Object, 
        required: true, 
        default: () => ({ id: '', state: '', title: '' }),
        validator: task => ['id', 'state', 'title'].every(key => key in task),
      },
    },
    emits: ['액션-이름-1', '액션-이름-2'],

    setup(props, { emit }) {
      props = reactive(props)
      return {
        // 컴포넌트 최상위 div에서 사용할 클래스 이름, list-item이라는 클래스와 상태값에 따른 클래스를 사용하기 위함. 
        classes: computed(() => ({
          'list-item TASK_INBOX': props.task.state === 'TASK_INBOX',
          'list-item TASK_PINNED': props.task.state === 'TASK_PINNED',
          'list-item TASK_ARCHIVED': props.task.state === 'TASK_ARCHIVED',
        })),

        // 체크 상태 확인
        isChecked: computed(() => props.task.state === 'TASK_ARCHIVED'),

        // 체크박스 눌렀을 때(할 일 완료하기) 액션 알리기. task의 아이디 같이 보냄. 
        archiveTask() {
          emit('액션-이름-1', props.task.id)
        },

        // pin 버튼 눌렀을 때 액션 알리기. task의 아이디 같이 보냄. 
        pinTask() {
          emit('액션-이름-2', props.task.id)
        },
      }
    },
  }
</script>
```

## 스토리북 장점

- 데이터 값, 상태에 따른 UI 확인이 쉽다. 특히 empty, loading 상태 등. - Controls에서 데이터 값을 바꿔볼 수 있다.
    
    ![스토리북UI](https://drive.google.com/uc?export=view&id=1VWgx4Bz5Gz1EM26gIwklqu2ZpMiRgZOt)
    
- `@storybook/addon-a11y` 애드온으로 접근성 문제 파악이 체계적이고 간결하다.
    
    ![스토리북UI](https://drive.google.com/uc?export=view&id=1vgY00kHgb8Ue-gD0ZpBi6ZGUL0L8ZNpD)
    
- 어떤 동작이 트리거 되는 지 확인이 간편하다.
    ![스토리북UI](https://drive.google.com/uc?export=view&id=1pIkwh3sQ2cfQuHzqBNY57737ZfJPhrhz)
    
- (스토리북을 빌드 & 배포하면)다른 팀과 커뮤니케이션이 용이하다.
- 스토리북을 도입해서 새로 만드는 컴포넌트인 경우 자연스럽게 재사용성을 고려해서 만들게 된다.
- UI 테스트도 가능하다.
- 필요한 컴포넌트에만 도입도 가능하다.

## 스토리북 단점

- 처음에 추가하고 작성하고 익혀야할 게 많다. 러닝커브가 높다고 생각한다. (데이터 모킹 준비하는 부분이 아직 잘 이해가 안됨)
- 기능별로 쪼개어 지지 않은 컴포넌트, 다른 컴포넌트에 의존적인 컴포넌트에는 바로 도입이 어렵다. (도입하려면 강제로 리팩토링 해야됨)

## 결국 스토리북..

사실 튜토리얼 보고 이 글도 쓰고 했지만 스토리북 자체가 너무 어려워서 실제로 기존 프로젝트에 도입할 수 있을까 하는 고민이 많았다. 기존에 쓰던 사람이 없고 혼자 도입해야 하기 때문이다. 

그래서 다른 Vue 문서화 도구들을 찾아보았는데 딱 이렇다 할 게 없었다. 그냥 하지 말까..도 생각했지만 어떤 모양으로든 문서화는 필요하다고 느꼈다. 며칠동안 헤매다가 스토리북으로 다시 돌아왔다. 스토리북 문서에서 데이터값들을 테스트 해볼 수 있는 기능이 강력하기 때문이다. 다시 처음부터 sb init하고 살펴보니 자동으로 문서 생성하는 autodocs 기능이 매력적이었다. vue2(+ vite) 프로젝트에서도 잘 돌아가는 거 같았다. 

현재 시점에서 가장 활발하게 서포트 해주고 있고 사용자도 많고 다른 SPA 프레임워크로 갈아탈 때 다시 배우지 않아도 되는 스토리북을 사용해 보기로 마음 먹었다. 