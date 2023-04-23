---
title: "gatsby mdx에 이미지 추가하기"
excerpt: "MDX embedded images"

categories:
  - React
tags:
  - gatsby

# last_modified_at: 2021-05-11
toc: true
sidebar_main: true
---

1주일 정도 이것 때문에 머리를 쥐어 뜯고 다른 공부를 못 했다.

일단 개츠비 튜토리얼 따라서 기본 적인 건 만들었지만, 기본 튜토리얼에 포스트 본문에 이미지 넣는게 없어서 한참 찾아봤다.

[이것만 따라하면 당신도 멋진 개츠비 블로그를 만들 수 있다!](https://www.gatsbyjs.com/docs/tutorial/)

튜토리얼에는 frontmatter에 [hero_image라고 대표 이미지 하나 넣는 방법](https://www.gatsbyjs.com/docs/tutorial/getting-started/part-7/#add-hero-images-to-blog-post-frontmatter) 알려줬는데 실제 마크다운으로 작성한 블로그 글을 렌더할 때 대표 이미지 렌더를 포스트 템플릿 js에서 하는 방식이었다.

내가 원하는건 템플릿처럼 항상 같은 자리에 보이는 것이 아니라 마크다운 작성 중에 인라인으로 이미지들을 착착 추가하는 것이었다.

## 기본적인 이미지 임베드 방법

개츠비 이미지 임베드 방법: [https://www.gatsbyjs.com/blog/mdx-embedded-gatsby-image/](MDX embedded images)

다른 글도 이것이랑 비슷한 방법이었다. MDXProvider와 MDXRenderer를 불러와서 graphql로 가져온 이미지를 넘기는 방식. 진짜 열심히 따라했는데 에러만 나고.. 버전 문제인가 싶어서 낮춰서 맞추려고 하면 의존성 있는 애들 또 에러나고..

난이도 관계상 remote 이미지 부분은 빼고 local 이미지만 고려했다.

포스팅 템플릿이 되는 js 파일. graphql 쿼리에서 embeddedImagesLocal를 불러오는 부분을 추가 하고, MDXRenderer에 프롭으로 넘겨준다.

```js
import React from "react";
import { graphql } from "gatsby";
import { MDXRenderer } from "gatsby-plugin-mdx";
import { MDXProvider } from "@mdx-js/react";

const MdxPage = ({
  data: {
    mdx: {
      frontmatter: { embeddedImagesLocal },
    },
  },
}) => {
  return (
    <MDXProvider>
      <MDXRenderer localImages={embeddedImagesLocal} />
    </MDXProvider>
  );
};

export const query = graphql`
  query ($id: String) {
    mdx(id: { eq: $id }) {
      frontmatter {
        title
        embeddedImagesLocal {
          childImageSharp {
            gatsbyImageData(layout: FULL_WIDTH)
          }
        }
      }
    }
  }
`;

export default MdxPage;
```

gataby-config.js 플러그인 설정을 위해 추가해야 한다.

```js
// gatsby-config.js
module.exports = {
  plugins: [
    ...
    {
      resolve: `gatsby-plugin-sharp`,
      options: {
        defaults: {
          quality: 70,
          formats: ['auto', 'webp', 'avif'],
          placeholder: 'blurred',
        },
      },
    },
    ...
  ],
};
```

포스팅 mdx 파일. frontmatter 부분에 이미지들 상대 경로를 작성한다. mdx 파일과 같은 폴더에 없어도 된다.

```md
---
title: Remote Images
embeddedImagesLocal:
  - image1.jpg
  - ./images/230420.jpg
---

import { getImage, GatsbyImage } from "gatsby-plugin-image";

## how to embed local images in gatsby

<GatsbyImage alt="green doggo" image={getImage(props.localImages[0])} />
<GatsbyImage alt="orange doggo" image={getImage(props.localImages[1])} />
```

이렇게 하면 잘 보여야 하는데 에러만 났다.

## 해결이 어려웠던 이유

결국에는 버전에 따라 다른 사용법이 다른 문제였지만, 그걸 알아내는 데에도 힘들었다. 처음에는 이미지 조차 graphql로 가져와야 한다는 개념이 없어서 여러 안내 글을 봐도 근본적인 이해가 되지 않았다. 그리고 어느 플러그인이 문제인지, 어느 버전부터 문제인지 알아내는 것도 쉽지 않았다.

### gatsby-plugin-mdx 버전 문제

[https://www.gatsbyjs.com/plugins/gatsby-plugin-mdx/#updating-page-templates](https://www.gatsbyjs.com/plugins/gatsby-plugin-mdx/#updating-page-templates)

gatsby-plugin-mdx의 버전이 바뀌면서 기존에 body로 내용을 따로 불러오던 것을 children으로 바로 렌더할 수 있게 바뀌었다는 사실을 알았다. 즉, 그동안 찾았던 해결 방법들은 예전 버전을 기반으로 하던거라서 작동을 안했던 것이다.
튜토리얼은 children을 사용하는 최신 버전 기반이어서 나는 그 부분을 바꿀 필요가 없었다. 이렇게 삽질을.. 울어야겠다.

### 기존 방법

```js
import React from "react";
import { graphql } from "gatsby";
import { MDXRenderer } from "gatsby-plugin-mdx";

function PostTemplate({ data: { mdx } }) {
  return (
    <main>
      <h1>{mdx.frontmatter.title}</h1>
      <MDXRenderer>{mdx.body}</MDXRenderer>
    </main>
  );
}

export const pageQuery = graphql`
  query PostTemplate($id: String!) {
    mdx(id: { eq: $id }) {
      frontmatter {
        title
      }
      body
    }
  }
`;

export default PostTemplate;
```

### 바뀐 방법

- MDXRenderer를 사용하지 않는다.
- data와 함께 children을 렌더 함수의 프롭으로 받는다.
- children으로 바로 mdx 내용을 넘겨주기 때문에 graphql로 body를 가져오지 않는다.

```js
import React from "react";
import { graphql } from "gatsby";

function PostTemplate({ data: { mdx }, children }) {
  return (
    <main>
      <h1>{mdx.frontmatter.title}</h1>
      {children}
    </main>
  );
}

export const pageQuery = graphql`
  query PostTemplate($id: String!) {
    mdx(id: { eq: $id }) {
      frontmatter {
        title
      }
    }
  }
`;

export default PostTemplate;
```

여기까지 보고 뭔가 실마리가 잡힐듯 말듯 하다가 한줄기의 빛을 발견한다.

## 진짜.최종.final 방법

나의 구세주: [https://stackoverflow.com/questions/73463825/how-to-embed-images-in-frontmatter-with-gatsby-plugin-mdx-v4](https://stackoverflow.com/questions/73463825/how-to-embed-images-in-frontmatter-with-gatsby-plugin-mdx-v4)

여기 천사님 답변에 따르면 mdx 파일에서 다음과 같이 이미지를 불러내어 사용한다고 되어 있다.

```js
import { getImage, GatsbyImage } from "gatsby-plugin-image";

<GatsbyImage
  alt="alt text"
  image={getImage(props.data.mdx.frontmatter.embedded)}
/>;
```

중요한 곳은 `getImage`에 있는 이미지 접근 부분이다. 템플릿 js 파일에서 포스트 내용을 `{children}`이렇게 렌더했고, 따로 이미지를 프롭으로 넘겨주는게 없다. 그래서 그런지 mdx 파일에서 prop.data로 시작해서 접근해야 하는 것이다.

전: `image={getImage(props.localImages[0])`  
후: `image={getImage(props.data.mdx.frontmatter.localImages[0])`

### remote image

로컬에 있는 이미지만 쓰는게 아니고 다른 이미지 주소를 가져다가 보여주는 방법은 더 복잡했다. gatsby-node.js 파일에 설정하는 것도 있고, 기존 프로젝트에서 그것만 추가한다고 되는 게 아니고 추가로 설정해야 할 거 같아서 안하기로 했다.
이렇게 복잡한 이유는 아마 개츠비가 이미지를 그냥 생으로 보여주는 것이 아니라 이것저것 미리 처리를 해야하기 때문인 것 같다. 당분간은 로컬 이미지만 쓰기로. (찡긋)

## 최종 코드

### package.json

튜토리얼 후 이미지 때문에 추가로 설치한 것은 없지만 버전 확인을 위해 남겨둔다.

```json
"dependencies": {
  "@mdx-js/react": "^2.3.0",
  "gatsby": "^5.7.0",
  "gatsby-omni-font-loader": "^2.0.2",
  "gatsby-plugin-image": "^3.7.0",
  "gatsby-plugin-manifest": "^5.9.0",
  "gatsby-plugin-mdx": "^5.7.0",
  "gatsby-plugin-sharp": "^5.7.0",
  "gatsby-source-filesystem": "^5.7.0",
  "gatsby-transformer-sharp": "^5.7.0",
  "react": "^18.2.0",
  "react-dom": "^18.2.0",
  "react-helmet": "^6.1.0"
}
```

### gatsby-config.js

```js
module.exports = {
...
  plugins: [
    ...
    'gatsby-plugin-image',
    {
      resolve: `gatsby-plugin-sharp`,
      options: {
        defaults: {
          quality: 70,
          formats: ['auto', 'webp', 'avif'],
          placeholder: 'blurred',
        },
      },
    },
    'gatsby-plugin-mdx',
    'gatsby-transformer-sharp',
    ...
  ],
}
```

### {mdx.frontmatter\_\_slug}.js

튜토리얼 후 바뀐 부분은 hero_image 를 삭제하고, grapql에 로컬 이미지 추가한 것이다.

```js
import * as React from "react";
import { graphql } from "gatsby";
import Layout from "../../components/layout";
import Seo from "../../components/seo";

const BlogPost = ({ data, children }) => {
  return (
    <Layout pageTitle={data.mdx.frontmatter.title}>
      <p>{data.mdx.frontmatter.date}</p>

      <hr />

      {children}
    </Layout>
  );
};

export const query = graphql`
  query ($id: String) {
    mdx(id: { eq: $id }) {
      frontmatter {
        title
        date(formatString: "MMMM D, YYYY")
        embeddedImagesLocal {
          childImageSharp {
            gatsbyImageData
          }
        }
      }
    }
  }
`;

export const Head = ({ data }) => (
  <Seo pageTitle={data.mdx.frontmatter.title} />
);

export default BlogPost;
```

### 포스트.mdx

mdx는 markdown에서 JSX를 사용할 수 있게 해주기 때문에 개츠비이미지를 import 해서 원하는 위치에서 쓸 수 있다.

```md
---
title: "사진을 넣자"
date: "2023-04-22"
slug: "embed-image"
embeddedImagesLocal:
  - "./images/image.jpg"
---

import { getImage, GatsbyImage } from 'gatsby-plugin-image'

## 개츠비에서 사진 넣기

포스팅 본문 내용

<GatsbyImage
  alt="이 사진은..."
  image={getImage(props.data.mdx.frontmatter.embeddedImagesLocal[0])}
/>
```
