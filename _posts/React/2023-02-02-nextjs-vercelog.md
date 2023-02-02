---
title: "next.js에서 동적 OG image 생성하기 - @vercel/og"
excerpt: "vercel + next.js + typescript"

categories:
 - React
tags:
 - vercel
 - next.js

# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---

소셜미디어를 만드는데 오픈그래프 이미지를 쉽게 생성해주는 라이브러리를 발견해서 사용해 보았다. 포스팅 상세 페이지에서 페이지 내용에 따라 다른 이미지를 보여줘야 하기 때문에 동적으로 생성해야 했다. 

## @vercel/og 라이브러리

[사용 방법 공식 문서](https://vercel.com/docs/concepts/functions/edge-functions/og-image-generation#usage)

[사용 예시 공식 문서](https://vercel.com/docs/concepts/functions/edge-functions/og-image-examples)

vercel에서 제공하는 Edge Functions을 사용한다. 

사용 예시는 Next.js 프로젝트에서 TypeScript로 작성되어 있다. 

예시를 보면 동적 텍스트, 동적 이미지, emoji, svg, 폰트 파일을 사용할 수 있다. 이중에서 동적 텍스트와 폰트파일 적용하는 예시를 주로 참고했다. 

## 사용 방법

1) 라이브러리를 설치한다. 

```bash
npm i @vercel/og
```

2) `pages/api/og.tsx` 파일 생성

필요한 텍스트는 url parameter로 받아서 사용한다. 

스타일 넣을 때, 생성된 이미지가 고정 사이즈가 아니라 브라우저 사이즈에 따라 변하게 기본 적용되어 있어서 유의해야 한다. 브라우저에서 확인하면서 수정하면 된다. 

```tsx
{% raw %}
import { ImageResponse } from '@vercel/og';
import { NextRequest } from 'next/server';

export const config = {
  runtime: 'edge',
};

const font = fetch(new URL('../../assets/fontfile.woff', import.meta.url)).then((res) =>
  res.arrayBuffer(),
);

export default async function handler(req: NextRequest) {
  const fontData = await font;

  try {
    const { searchParams } = new URL(req.url);

    const hasText = searchParams.has('text');
    const text = hasText ? `${searchParams.get('text')?.substring(0, 170)}` : '';

    return new ImageResponse(
      (
        <div
          style={{
            backgroundColor: 'white',
            width: '100%',
            height: '100%',
            padding: '50px 25px 25px',
            display: 'flex',
          }}
        >
          <svg>배경으로 사용할 이미지</svg>
          <p
            style={{
              whiteSpace: 'pre-line',
              padding: '60px',
              position: 'absolute',
              fontSize: '54px',
              color: 'black',
              zIndex: 10,
              width: '100%',
              fontFamily: 'Pretendard',
            }}
          >
            {text}
          </p>
        </div>
      ),
      {
        width: 1200,
        height: 681,
        fonts: [
          {
            name: 'Pretendard',
            data: fontData,
            style: 'normal',
          },
        ],
      },
    );
  } catch (e: any) {
    console.log(`${e.message}`);
    return new Response('Failed to generate the image', {
      status: 500,
    });
  }
}
{% endraw %}
```

3) og 사용

썸네일 url을 `/api/og?text=${넘겨줄 텍스트}`로 만들고 og 메타태그 content에 넣는다. 

```tsx
const thumbnailImgUrl = `${baseUrl}/api/og?text=${포스트 내용 텍스트}`;

return (
  <>
    <Head>
      <meta property="op:image" content={thumbnailImgUrl} />
      <meta name="twitter:card" content="summary_large_image" />
      <meta name="twitter:site" content="..." />
      <meta name="twitter:title" content={포스트 내용 텍스트} />
      <meta name="twitter:image" content={thumbnailImgUrl} />
    </Head>
    ...
  </>
)
```

## 이슈 해결

### URL type 에러

**문제**

개발 서버 열 때는 괜찮았는데 build 할 때 오류가 나면서 빌드가 완료되지 않았다. 

에러 발생 부분 (폰트 파일을 불러오는 부분)

```tsx
const font = fetch(new URL('../../assets/fontfile.woff', import.meta.url)).then((res) =>
  res.arrayBuffer(),
);
```

에러 내용

```
- Type error: Argument of type 'URL' is not assignable to parameter of type 'RequestInfo'. Type 'URL' is not assignable to type 'string'.
```

**해결**

찾아보니 URL에 대한 타입이 업데이트 되었다는 결과가 있어서 @types 라이브러리들의 버전을 최신 버전으로 다시 설치했다. 사용하고 있던 버전이 최신 버전보다 낮았었다. vercel 공식 예제는 같은 코드인데 에러나 나지 않아서 확인해보니 “latest”로 버전을 명시하지 않고 있었다. 

아래의 버전으로 설치 되었고, 타입 에러는 사라졌다. 

**package.json**

```json
"@types/node": "^18.0.6",
"@types/react": "^18.0.15",
"typescript": "^4.7.4"
```

### vercel 무료 제공 Edge Function 용량 초과

**문제**

에러 내용

```
- Error: The Edge Function "api/og" size is 1.67 MB and your plan size limit is 1 MB. Learn More: https://vercel.link/edge-function-size
```

vercel의 무료 플랜에서 제공해 주는 용량은 1MB인데 초과해서 빌드가 안되었다. 로컬에서는 상관이 없었다. 폰트 파일 적용 하기 전에는 괜찮았기 때문에 폰트 관련한 문제일 것 같았다. 

**해결**

- (유료 플랜으로 바꾼다.)
- ttf 대신 용량이 작은 woff 파일 사용했다.

여러 서체와 폰트 확장자를 테스트 해보았는데 폰트 파일 용량이 문제였다. 라틴 문자만 있는 폰트 파일은 용량이 작아서 상관없었지만 한글이 포함된 폰트를 꼭 사용하고 싶었다. woff2파일은 지원하지 않았고 최종적으로 woff 파일을 사용하게 되었다.