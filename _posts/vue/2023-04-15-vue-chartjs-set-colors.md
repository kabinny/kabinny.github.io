---
title: "chart.js 컬러 선택 기능 만들기"
excerpt: "codepen Vue SFC + Vue2 + Chart.js4"

categories:
 - Vue
tags:
 - chart
 - 
 - 
# last_modified_at: 2021-07-17
toc: true
---

vue2 + chart.js@4.2.1

시작하기 전에 vue나 chart.js와는 상관없지만 코드펜 vue SFC 에디터에서 npm 패키지 가져오는 방법을 삽질했기 때문에 앞으로 까먹지 않게 정리한다. 

## codepen Vue SFC 에서 npm package 사용하기

Vue SFC 에디터 코드펜 말고 기본 코드펜에서 써보려고 했는데, 생소해서 실패.. 그리고 Vue SFC 에디터에서 script 태그로 가져오기도 실패.. 다른 사람들이 코드펜에서 vue + chart.js 어떻게 쓰는지 찾아봤는데 뷰코드펜에서 작성한 건 못찾아서 도움이 안되었다.  

[공식 사용 방법](https://blog.codepen.io/2021/02/16/vue-editor-now-with-es-modules/)

[Skypack](https://www.skypack.dev/) 이라는 것을 사용하게 된다. 스카이팩은 npm packages들을 설치하거나 빌드하지 않고 불러올 수 있게 해 준다. 코드펜처럼 패키지를 설치하거나 빌드할 수 있는 환경이 아닐때 진짜 유용한 거 같다.
버전을 명시한 사용법  `https://cdn.skypack.dev/패키지이름@버전` 
Chart.js 가장 최근 버전인 4.2.1버전을 사용하기 싶기 때문에 `import { Chart } from 'https://cdn.skypack.dev/chart.js@4.2.1'` 이렇게 불러왔다. 

chart.js 4버전부터 트리셰이킹이 지원되는데 그게 약간 기본 세팅인 거 같다. 기존 처럼 패키지 전부 불러오려면 'chart.js' 가 아닌 'chart.js/auto' 에서 import 해야 하는데 스카이팩과 같이 사용할 때는 어떻게 하는 지 모르겠다. 일단 코드펜에서 사용할 수 있는 것에 만족! 강제로 트리셰이킹 해보면서도 써보는 것도 좋은 듯. 

최종적으로 이렇게 불러와서 썼다. 
```js
import {
  Chart,
  Colors,
  BarController,
  BarElement,
  CategoryScale,
  LinearScale,
  Legend,
  Title,
} from 'https://cdn.skypack.dev/chart.js@4.2.1'
Chart.register(
  Colors,
  BarController,
  BarElement,
  CategoryScale,
  LinearScale,
  Legend,
  Title
);
```

## 예시 코드
<p class="codepen" data-height="300" data-theme-id="light" data-default-tab="js,result" data-slug-hash="PoyNWMB" data-user="kabinny" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/kabinny/pen/PoyNWMB">
  vue-chartjs-colorpalette</a> by kabinny (<a href="https://codepen.io/kabinny">@kabinny</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://cpwebassets.codepen.io/assets/embed/ei.js"></script>

## template
`select` 로 컬러테마를 선택할 수 있게 하고 `v-model`로 값을 가져올 수 있게 한다. 차트를 그릴 캔버스 요소도 추가하고 `ref`로 가져올 수 있게 했다. 
```html
<label for="themeSelect">Select Color Theme</label>
<select v-model="colorTheme" id="themeSelect">
  <option value="01">01 cherry blossom</option>
  <option value="02">02 leaf</option>
  <option value="03">03 pink purple</option>
</select>

<div>
  <canvas ref="chartEl"></canvas>
</div>
```


## script

### 데이터 형식
`colorList`는 사용하기 편한 형태로 만들면 되고, 차트 데이터는 [문서의 데이터 구조](https://www.chartjs.org/docs/latest/general/data-structures.html)에 맞춰서 넣으면 된다. 
```js
data() {
  return {
    chartContainer: null,
    colorTheme: '01',
    // 컬러 리스트만 따로 js 파일로 만들어서 관리해두면 편함
    colorList: {
      color01: ['236, 29, 95', '255, 167, 166', '212, 224, 238'],
      color02: ['199, 218, 199', '254, 141, 123', '254, 103, 134'],
      color03: ['241, 152, 174', '160, 129, 205', '118, 109, 192'],
    },
    // 차트 데이터는 상위 컴포넌트에서 prop으로 받아서 썼다
    propChartLabels: ['Spring', 'Summer', 'Fall', 'Winter'],
    propChartData: [
      {
        label: 'data01',
        data: [10, 20, 40, 30]
      },
      {
        label: 'data02',
        data: [20, 5, 60, 20]
      },
      {
        label: 'data03',
        data: [10, 20, 40, 30]
      },
    ]
  };
},
```


### 차트를 그리는 시점
`colorTheme`이 바뀔 때마다 그리고, 처음 마운트 되었을 때 그린다. 
```js
watch: {
  // select로 컬러를 선택할 때마다 차트를 다시 그린다
  colorTheme() {
    this.drawChart()
  }
},
mounted() {
  this.drawChart()
},
```


### 차트 그리기
[차트 색상 문서](https://www.chartjs.org/docs/latest/general/colors.html)에 나온 색상 데이터 넣는 위치를 확인하고, 차트를 그릴 때마다 `colorTheme`에 따라 다른 색상을 넣도록 했다. 
```js
drawChart() { 
  // 같은 캔버스를 사용해서 또 그릴 때는 destroy 하고 나서 그려야 한다
  if (this.chartContainer) {
    this.chartContainer.destroy()
  }
  
  // 차트 요소
  const chartEl = this.$refs.chartEl
  
  // 차트 데이터 가공 <= 컬러 세팅
  const chartData = [...this.propChartData]
  chartData.forEach((item, index) => {
    item.borderWidth = 1
    item.backgroundColor = 'rgba(' + this.colorList[`color${this.colorTheme}`][index] + ', 0.5)'
    item.borderColor = 'rgba(' + this.colorList[`color${this.colorTheme}`][index] + ', 1)'
  })
  
  // 차트 생성
  this.chartContainer = new Chart(
    chartEl,
    {
      type: 'bar',
      data: {
        labels: this.propChartLabels,
        datasets: chartData,
      },
      options: {
        plugins: {
          title: {
            display: true,
            text: 'Vue Chart.js',
            font: {
              size: 24,
            }
          }
        }
      }
    }
  )
  this.chartContainer.update()
}
```