---
layout: post
title: 브라우저렌더링과 reflow와 repaint
categories: 공부
tag:
  [브라우저 렌더링 단계, critical rendering path, CRP, reflow, repaint, 최적화]
toc: true
---

# reflow와 repaint

- 면접에 단골 질문중 하나인 브라우저의 렌더링 과정
- 꼬리 질문으로 나올 내용은 reflow, repaint가 있다.
- reflow와 repaint가 최대한 발생하지 않도록 조심해야 하는데 왜 그래야 하는지 정리한다.

## 브라우저렌더링 단계

- 브라우저 렌더링은 "Critical Rendering Path"라고 불리기도 한다.
- 브라우저는 총 5개의 단계로 표현이 가능하다.
  - `parsing` 받은데이터를 parsing을 진행하여 dom트리를 만든다.
  - `style` css, 외부 css로 스타일 요소를 파싱하여 cssom트리를 만든다.
  - `render tree` dom과 cssom으로 렌더트리를 만든다.
  - `layout` 엘리먼트의 위치와 크기를 결정한다.
  - `painting` 색과, 폰트를 css의 규칙에 맞게 적용한다.
- 초반엔 load하는 과정과 마무리에 composite하는 과정을 말하는 블로그도 존재
- 아니면 paring을 html, css로 단계를 나눠서 설명하는 블로그도 존재
- 결국 전체적인 흐름은 다 같기 때문에 자기가 외우기 편한 방식으로 학습하면 될듯하다.

## reflow란?

- reflow는 브라우저 렌더링과정에서 `layout`과 연관이 있다.
- 브라우저 렌더링 단계에서 layout을 다시 진행하는것이다.
- 피해야 하는 이유는?
  - 해단 노드부터 자식까지 모든 노드를 재 계산해야해서 비용이 크다.
  - layout만 다시 하는게 아닌, paint까지 다시 진행해야 한다.
- 발생하는 경우는?

  - 요소의 크기나 위치가 바뀔때
  - 브라우저 창의 크기가 바뀔때
  - DOM 노드의 추가, 제거
  - DOM 노드의 위치 변경
  - DOM 노드의 크기 변경(margin, padding, border, width, height 등..)
  - CSS3 애니메이션과 트랜지션
  - 폰트 변경, 텍스트 내용 변경
  - 이미지 크기 변경
  - offset, scrollTop, scrollLeft과 같은 계산된 스타일 정보 요청
  - 페이지 초기 렌더링
  - 윈도우 리사이징

- [reflow,repaint확인](https://docs.google.com/spreadsheets/u/0/d/1Hvi0nu2wG3oQ51XRHtMv-A_ZlidnwUYwgQsPQUg1R2s/pub?single=true&gid=0&output=html)
- [reflow최적화 Tip.pdf](https://lists.w3.org/Archives/Public/public-html-ig-ko/2011Sep/att-0031/Reflow_____________________________Tip.pdf)

## repaint란?

- repaint는 브라우저 렌더링과정에서 `paint`과 연관이 있다.
- 화면의 구조 변경 이외는 repaint만 발생한다고 보면 된다.

  - 색 변경과 같은 요소의 크기에 변형이 없는 것들

- [reflow,repaint확인](https://docs.google.com/spreadsheets/u/0/d/1Hvi0nu2wG3oQ51XRHtMv-A_ZlidnwUYwgQsPQUg1R2s/pub?single=true&gid=0&output=html)

## 줄이는 방법

- reflow와 repaint에서 최대한 reflow를 줄이기 위해 노력해야 한다.
- [csstrigger](https://csstriggers.com)을 확인해서 layout, paint, composite중 어떤 단계에서 발생하는지 확인이 가능하다.
- 아래와 같이 고려하자
  - 변화가 필요하다면 dom구조에서 최대한 끝 위치에 놓는다.
  - 인라인 스타일을 배제한다.
    - 텍스트 길이에 따라 reflow가 발생할 여지가 있다.
  - 애니메이션에서는 position:fixed보다는 absolute가 효율적이라 한다.
    - absolute는 부모의 기준, fixed는 뷰표트 기준이라 스크롤이나 뷰포트크기 조정에 따라 reflow가 발생
    - fixed는 뷰포트 기준이라 영향이 광범위하게 된다.
    - 브라우저들도 absolute에 최적화가 더 잘되어 있다고 한다.
  - 스타일 변화는 한방에 처리하기
    - 한줄 한줄 처리하면 reflow와 repaint도 한줄씩 진행한다고 한다.
  - 애니메이션은 transform, opacity, will-change을 사용하기.
    - width, height를 조정하거나, margin, padding
    - top, right, bottom, left
    - font-size (텍스트가 커지면 위치가 조정될 수 있음)
    - display, position 속성들

### display와 visibility에 관하여

- 자주 나오는 내용으로 `display:none`, `visibility:hidden`을 설명하라는 내용이 나온다.
- `display:none`은 실제 렌더트리에서 제거, `visibility:hidden`은 보이지만 않게 한다.
- 그러면 `display:none`를 많이 쓰면 어떻게 될까?
  - 위에서 말한 reflow가 발생하게 된다. 이는 비용적으로 너무 크다고 말할 수 있다.
- `display:none`는 아바다케다브라, `visibility:hidden`은 투명망토라고 보면 된다.

## 참고

- [understanding-browser-rendering-critical-path-divyansh-singh](https://www.linkedin.com/pulse/)understanding-browser-rendering-critical-path-divyansh-singh/
- [웹-브라우저의-렌더링Rendering-알아보기](https://velog.io/@timosean/%EC%9B%B9-%EB%B8%8C%EB%9D%BC%EC%9A%B0%EC%A0%80%EC%9D%98-%EB%A0%8C%EB%8D%94%EB%A7%81Rendering-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0)
- [CSS 애니메이션 성능 개선 방법(reflow 최소화, will-change 사용)](https://wit.nts-corp.com/2017/06/05/4571)
- [critical-rendering-path](https://cresumerjang.github.io/2019/06/24/critical-rendering-path/)
  - 그림으로 잘 정리해주심
- [reflow-repaint](https://velog.io/@bcgrhio/Reflow-%EC%99%80-Repaint)
- [D2 브라우저는 어떻게 동작하는가?](https://d2.naver.com/helloworld/59361)

### 확인하는 법

- [csstrigger](https://csstriggers.com)

### reflow팁들

- [reflow확인](https://docs.google.com/spreadsheets/u/0/d/1Hvi0nu2wG3oQ51XRHtMv-A_ZlidnwUYwgQsPQUg1R2s/pub?single=true&gid=0&output=html)

- [reflow최적화 Tip.pdf](https://lists.w3.org/Archives/Public/public-html-ig-ko/2011Sep/att-0031/Reflow_____________________________Tip.pdf)
