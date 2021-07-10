---
layout: post
title: 개츠비 Post placeholder 이슈 해결 - 우아한테크코스 블로그 근로 개선 파트
date: 2021-07-10 18:45:00 +0900
description: 우아한테크코스 블로그 근로 개선 파트
img: woowacourse.png
tags: gatsby, react, typescrypt
---

# Gatsby - Post 기본 이미지 설정 이슈 해결

post 의 frontmatter tag 에서 올바른 경로의 이미지가 지정되지 않았을 때 해결한 경험입니다.



## Frontmatter

<img src="/github-blog/assets/img/woowacourse/blog/1.png">
	<!-- result : https://github.com/lns13301/github-blog/blob/master/assets/img/woowacourse/blog/1.png -->

포스트할 글의 앞쪽에 태그로 구성하는 부분을 Frontmatter 라고 합니다.

## PostCard

리액트 컴포넌트(.tsx)에서 해당 태그를 기반으로 처리를 한 후 게시글을 생성합니다.

홈화면에서 게시글을 카드 형태로 만들어내는 곳이 PostCard.tsx 입니다.

<img src="/github-blog/assets/img/woowacourse/blog/2.png">
	<!-- result : https://github.com/lns13301/github-blog/blob/master/assets/img/woowacourse/blog/2.png -->

<img src="/github-blog/assets/img/woowacourse/blog/3.png">
	<!-- result : https://github.com/lns13301/github-blog/blob/master/assets/img/woowacourse/blog/3.png -->

위의 페이지에서 아랫쪽에 있는 3개의 항목이 PostCard 로 생성된 것이라고 보면 됩니다.



## 포스팅 이미지

게시글에 들어가면 지정해둔 대문 이미지가 등장합니다. 이 게시글(post)에 들어갈 내용을 만들어내는 리액트 컴포넌트는 아래 경로의 post.tsx 파일입니다.

<img src="/github-blog/assets/img/woowacourse/blog/4.png">
	<!-- result : https://github.com/lns13301/github-blog/blob/master/assets/img/woowacourse/blog/4.png -->

위의 장소의 코드를 통해 만들어지는 post 는 아래와 같습니다.

<img src="/github-blog/assets/img/woowacourse/blog/5.png">
	<!-- result : https://github.com/lns13301/github-blog/blob/master/assets/img/woowacourse/blog/5.png -->



## 해결 과정

위의 두 리액트 컴포넌트를 수정해줘야 했습니다. 추가적으로 이미지가 등록되지 않거나 올바른 경로가 아닐 때 사용할 이미지도 필요했습니다. 이미지는 테코블이라는 글과 함께 적당히 세련된 쥐색을 선택했습니다. 다크 테마와 잘 어울리네요. 그러면 계속해서 코드의 어느 부분을 수정했는지 살펴보겠습니다.

### PostCard.tsx

기본으로 사용할 이미지를 등록합니다.

<img src="/github-blog/assets/img/woowacourse/blog/6.png">
	<!-- result : https://github.com/lns13301/github-blog/blob/master/assets/img/woowacourse/blog/6.png -->

해당 image 가 null 일 때 실행할 조건문을 추가해주고 이 경우에 기존의 이미지가 있을 때와는 다르게 size 조절 및 기본 이미지를 설정해 주었습니다.

<img src="/github-blog/assets/img/woowacourse/blog/7.png">
	<!-- result : https://github.com/lns13301/github-blog/blob/master/assets/img/woowacourse/blog/7.png -->

위의 작업을 통해 PostCard 의 기본 이미지 설정이 완료되었습니다.

### post.tsx

다음은 비슷한 작업 내용으로 수정해주면 됩니다.

<img src="/github-blog/assets/img/woowacourse/blog/8.png">
	<!-- result : https://github.com/lns13301/github-blog/blob/master/assets/img/woowacourse/blog/8.png -->

<img src="/github-blog/assets/img/woowacourse/blog/9.png">
	<!-- result : https://github.com/lns13301/github-blog/blob/master/assets/img/woowacourse/blog/9.png -->

위와 같이 코드를 수정해주게 되면 post 역시 이미지가 없을 경우 기본으로 설정해둔 이미지가 들어가게 됩니다.

## 결론

어떻게 해결해야할 지와 실제 구현코드 역시 단순한 부분의 수정이었습니다. 하지만 React, Gatsby, Typescrypt 를 모르는 상황에서 어디를 수정해야 할 지 감이 오지 않았습니다. 쉬운 것 같지만 어려운 작업을 끝냈습니다. 다음 개선은 이번 개선의 연장선으로 생각하며 구상해 봤습니다. post 의  frontmatter 에 이미지 출처를 기재하고 해당 출처를 자동으로 post 시에 이미지 상단 모서리부분에 기재하도록 하는 기능을 개선해보면 좋을 것 같습니다.