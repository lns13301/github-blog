---
layout: post
title: 개츠비 Post 이미지 출처 표기 이슈 해결 - 우아한테크코스 블로그 근로 개선 파트
date: 2021-07-21 18:45:00 +0900
description: 우아한테크코스 블로그 근로 개선 파트
img: woowacourse.png
tags: gatsby, react, typescrypt
---

# Gatsby - Post 이미지 출처 표기 이슈 해결

post 의 frontmatter tag 를 통해 이미지 출처를 표기하는 기능 이슈를 해결한 경험 입니다.

## Frontmatter

- 기존 frontmatter

<img src="/github-blog/assets/img/woowacourse/blog/11.png">

- 변경된 frontmatter

<img src="/github-blog/assets/img/woowacourse/blog/12.png">

추가된 source 태그에 남기고 싶은 출처를 남기면 썸네일 이미지 좌측 하단에 자동으로 표기됩니다.



## 포스팅 이미지

source 태그를 사용하지 않을 경우 기존과 같이 별다른 변화가 없이 포스팅이 표기됩니다.

추가적으로 태그를 사용하게 되면 아래와 같이 출처가 표기됩니다.

<img src="/github-blog/assets/img/woowacourse/blog/13.png">

## 디자인 선택

처음에는 이미지 모서리에 출처를 표기하려고 했습니다. 하지만 실제로 적용해보니 배경 이미지의 색상에 따라 출처 표기글이 묻힐 수 있다는 생각이 들었습니다.

<img src="/github-blog/assets/img/woowacourse/blog/14.png">

그래서 글자에 배경을 넣어 보았습니다. 하지만 이 경우에는 이미지 퀄리티가 너무 떨어져 보이고, 이목이 집중되어 버리는 것을 느꼈습니다.

<img src="/github-blog/assets/img/woowacourse/blog/15.png">

그리하여 최종적으로 이미지 하단에 소스를 표기하기로 결정했습니다.

<img src="/github-blog/assets/img/woowacourse/blog/16.png">

## 해결 과정

이슈 해결에는 생각보다 고난을 겪지 않았습니다. 저번 이슈 해결과정과 유사한 흐름으로 해결할 수 있었기 때문입니다. `source` 라는 이름으로 출처 태그를 사용하기로 결정했습니다. 그에 따라 graphQL 에 해당 태그를 등록해줍니다.

<img src="/github-blog/assets/img/woowacourse/blog/17.png">

위와 같이 등록을 해줘야 태그를 찾아올 수 있습니다.

이제 정상적으로 추가가 완료 되었으면 코드에서 사용할 수 있습니다. 적용시킬 파일은 게시글을 관리하는 <b>post.tsx</b> 파일입니다. 포스트의 썸네일 이미지를 로드해주는 부분에 source 가 존재한다면 해당 source 를 노출시켜주는 코드를 작성해 줍니다.

<img src="/github-blog/assets/img/woowacourse/blog/18.png">

위와 같이 작성해주고 나면 정상적으로 source 태그가 채워져 있을 때, 적용되는 모습을 볼 수 있습니다.

<img src="/github-blog/assets/img/woowacourse/blog/13.png">

## 결론

다른 프로젝트 일정과 겹치면서 예상외로 시간이 적게 들기는 했지만, 다른 이슈를 해결하려고 맘잡고 하기엔 애매한 기한이 남았습니다. 이번 개선 활동을 통해 블로그 근로의 또 다른 재미를 느껴봤습니다. 돌아오는 주기부터는 다시 열심히 기술 포스팅을 작성해야겠습니다. 부족한 글을 읽어주셔서 감사합니다.

