# 질문 & 답변 정리

학습하면서 직접 물어본 것들. 날짜: 2026-08-03 ~ 08-04

---

## HTML

### `<hr>`이 뭔가
수평 구분선(horizontal rule). "여기서 주제가 바뀐다"는 **의미**를 담은 태그. 선은 그 의미를 표현하는 기본 모양일 뿐.

- 닫는 태그 없음 (빈 태그 = void element): `<hr> <br> <img> <input> <meta>`
- `<br>`은 단순 줄바꿈, `<hr>`은 주제 전환

### 요소들에 기본 마진이 있어서 `margin: 0`을 주는 건가
맞음. 브라우저가 몰래 적용하는 **기본 스타일시트(user agent stylesheet)** 가 있음.

```css
h1 { margin: 0.67em 0; }
p  { margin: 1em 0; }
ul { margin: 1em 0; padding-left: 40px; }
body { margin: 8px; }
```

확인법: 개발자도구 → Elements → 요소 클릭 → Styles 패널 아래 회색 `user agent stylesheet`

### `<li>`를 `<p>`에 붙여도 되나
안 됨. `<li>`는 `<ul>`/`<ol>`의 자식이어야 함.

### 한줄평을 왜 `<span>`으로 감싸나, `<a>`는 안 되나
`<a>`는 **링크**(이동)라는 의미. 텍스트에 쓰면 커서가 손가락으로 바뀌고, Tab 포커스가 잡히고, 스크린리더가 "링크"라고 읽음.

`<span>`은 **의미 없는 인라인 상자** — 마땅한 의미 태그가 없을 때 쓰는 중립 상자.

감싸는 이유는 `flex: 1`을 걸 대상이 필요해서. 텍스트 노드에는 CSS를 못 건다.

```html
<li>
  <span>재밌었다</span>              <!-- flex: 1 → 남는 공간 다 먹음 -->
  <button class="del">삭제</button>  <!-- 그래서 오른쪽 끝으로 밀림 -->
</li>
```

### 인라인 vs 블록 차이
```
블록  = 한 줄을 통째로 차지 → 위아래로 쌓임   (div, p, h1, ul, li)
인라인 = 글자처럼 흐름      → 옆으로 붙음     (span, a, img, input, button)
```

| | 블록 | 인라인 |
|---|---|---|
| 기본 너비 | 부모 꽉 채움 | 내용만큼 |
| `width`/`height` | 먹힘 | **무시됨** |
| `margin` 위아래 | 먹힘 | **무시됨** |
| `margin`/`padding` 좌우 | 먹힘 | 먹힘 |

- `inline-block` = 옆으로 붙되(인라인) 크기는 먹는(블록) 하이브리드
- **flex/grid를 걸면 이 구분이 무의미해짐** — 자식은 전부 "flex 아이템"이 됨

판단 요령: "글자 사이에 끼어도 자연스러운가?" → 인라인

---

## CSS

### 요소를 가운데 정렬하는 법
```css
.card {
  max-width: 500px;
  margin: 40px auto;   /* 좌우 auto */
}
```

원리: 블록 요소는 원래 가로를 꽉 채움. `max-width`를 주면 공간이 남는데, 기본값은 **왼쪽 마진이 그걸 다 먹음.** 좌우 둘 다 `auto`면 반씩 나눠 가져서 가운데로 옴.

**조건: `width` 또는 `max-width`가 있어야 함.**

### `margin: 40px auto` 의 40px은 위만인가
**위아래 둘 다.** 값 개수별 규칙:

```css
margin: 10px;                  /* 사방 */
margin: 10px 20px;             /* 위아래 / 좌우 */
margin: 10px 20px 30px;        /* 위 / 좌우 / 아래 */
margin: 10px 20px 30px 40px;   /* 위 → 오른쪽 → 아래 → 왼쪽 (시계방향) */
```

개수가 모자라면 맞은편 값을 가져다 씀. `padding`, `border-radius` 동일.

위만 주려면 `margin-top: 40px`.

### px로는 세로 중앙을 못 맞추나
못 맞춤. px는 고정값, 화면 높이는 가변.

**세로 중앙의 정답:**
```css
body {
  min-height: 100vh;        /* 100vh = 화면 높이 100% */
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 0;
}
```
```css
/* 더 짧게 */
body { min-height: 100vh; display: grid; place-items: center; margin: 0; }
```

세로 중앙이 안 됐던 이유는 **부모에 높이가 없어서.** 높이가 없으면 "중앙"이 존재하지 않음.

**단, 내용이 화면보다 길면 쓰지 말 것.** 위아래가 잘림. 긴 페이지는 `margin: 40px auto`가 정답.

| 상황 | 방법 |
|---|---|
| 내용이 화면보다 짧음 (로그인 폼) | `min-height:100vh` + flex/grid 중앙 |
| 내용이 화면보다 김 | `margin: 40px auto` (중앙정렬 안 함) |

### `margin: auto` vs `justify-content` / `align-items` 차이
**누가 누구를 정렬하느냐**가 다름.

```
margin: 40px auto              →  나 자신을  부모 안에서
justify-content / align-items  →  내 자식들을  내 안에서
```

| | `margin: 0 auto` | `justify-content`/`align-items` |
|---|---|---|
| 대상 | **자기 자신** | **자식들** |
| 쓰는 곳 | 움직일 그 요소 | **부모** |
| 조건 | `width`/`max-width` | 부모에 `display: flex` |
| 방향 | **가로만** | 가로·세로 |

`space-between`은 중앙정렬이 아니라 **양끝으로 밀기.**

보너스: flex 자식에 `margin-left: auto` → 그것만 오른쪽 끝으로 밀림 (헤더 패턴)

### `text-align`은 자기 텍스트를 정렬하는 건가
정확히는 **자기 안에 있는 인라인 내용**을 정렬.

- 글자뿐 아니라 `img` `a` `span` `button` `inline-block` 다 움직임
- **`width`를 가진 블록(`div`)은 안 움직임** ← 그건 `margin: 0 auto`의 영역
- **상속됨** — 손자, 증손자까지 물려받으니 좁게 쓸 것
- flex 컨테이너의 직계 자식에겐 안 먹음 (그땐 `justify-content`)

### 3형제 정리
| | 대상 |
|---|---|
| `margin: 0 auto` | 자기 자신 |
| `justify-content` | 자식 요소들 (flex) |
| `text-align` | 안의 인라인 내용 |

### `flex-wrap: wrap` 은 뭐하는 건가
기본값은 `nowrap` = **무슨 일이 있어도 한 줄.** 공간이 부족하면 자식들을 찌그러뜨림 (`flex-shrink: 1`이 기본이라서).

`wrap`은 대신 **다음 줄로 내리라고** 지시.

```
공간 부족 + nowrap:     ( 음악이  )( 배우가  )   ← 눌리고 글자 줄바꿈
                       (  좋다   )(  좋다   )

공간 부족 + wrap:       ( 음악이 좋다 )( 배우가 좋다 )
                       ( 결말이 좋다 )              ← 다음 줄로
```

**규칙: 개수가 변하는 목록에는 `wrap`을 기본으로.** (태그, 칩, 카드 그리드)
개수가 고정이고 한 줄이 당연한 것(`.form-row`)은 `nowrap` 유지.

### 텍스트가 절대 2줄이 안 되게 하려면
`flex-wrap`이 아니라 **`white-space: nowrap`.** 둘은 다루는 대상이 다름.

```css
flex-wrap: wrap;       /* 요소(알약)가 다음 줄로 — 아이템 단위 */
white-space: nowrap;   /* 글자가 줄바꿈 안 됨 — 글자 단위 */
```

실전 조합 (태그 UI 표준):
```css
.tag-list    { display: flex; flex-wrap: wrap; gap: 8px; }  /* 알약은 통째로 내림 */
.tag-list li { white-space: nowrap; }                       /* 글자는 안 쪼갬 */
```

긴 글자 처리 3종 세트:
```css
white-space: nowrap;      /* 한 줄 강제 */
overflow: hidden;         /* 넘친 부분 자름 */
text-overflow: ellipsis;  /* 잘린 자리에 … */
```

### `li`에 `justify-content: center` 를 줬는데 안 먹는다
`li`는 flex **아이템**이지만 flex **컨테이너**가 아님. `display: flex`가 없으면 `justify-content`는 무시됨.

| 속성 | 붙는 곳 |
|---|---|
| `display:flex` `gap` `justify-content` `align-items` `flex-wrap` | **컨테이너(부모)** |
| `flex: 1` `align-self` `order` | **아이템(자식)** |

해결:
```css
li { text-align: center; }                          /* ① 간단 (권장) */
li { display: flex; justify-content: center; }      /* ② 컨테이너로 만들기 */
```

`flex: 1`을 빼면 알약이 내용만큼만 차지해서 애초에 문제가 사라짐.

### flex 걸면 자식 height가 같아지는 듯?
맞음. **`align-items`의 기본값이 `stretch`.** 자식이 부모 높이만큼 늘어남.

그래서 `.form-row`의 input과 button이 padding이 달라도 높이가 저절로 맞음.

### 자식에 `flex: 1` 주면 부모 width만큼 늘어나는 듯?
정확히는 **"남는 공간을 가져감".** 형제가 있으면 그만큼 뺀 나머지.

여러 개에 주면 **비율로 나눠 가짐:**
```
flex:1  flex:1   →  50% / 50%
flex:2  flex:1   →  66% / 33%
```
"1"은 픽셀이 아니라 비율 숫자.

### `border-radius: 999px` 효과가 뭔가
모서리를 깎는 **반지름.** 값이 높이의 절반을 넘으면 브라우저가 알아서 절반으로 자름 → **항상 완벽한 알약.**

```
8px          높이 절반         999px
┌────────┐   ╭────────╮      ╭────────╮
│ 음악이 │   (  음악이  )      (  음악이  )
└────────┘   ╰────────╯      ╰────────╯
```

왜 999px인가: **높이를 몰라도 되니까.** 폰트/padding이 바뀌어도 항상 알약.

- 알약(가로로 긴 것) → `999px`
- 원(정사각형) → `50%`  (가로로 긴 것에 50%를 주면 타원이 됨)

**배경색이 없으면 아무 효과도 안 보임** — 깎을 게 없으니까.

### `list-style: none` 후 왼쪽 여백을 없애는 건
`padding-left: 0`.

`ul`/`ol`은 기본 `padding-left: 40px`을 가짐 (불릿 그릴 공간). `list-style: none`으로 불릿만 지우면 자리는 그대로 남음.

**`margin`이 아니라 `padding`.** 그리고 여백의 주인은 **`ul`이지 `li`가 아님** — `li`에 `padding-left: 0`을 줘도 아무 일 안 일어남.

여백 디버깅 순서:
1. 어느 요소가 갖고 있나 (개발자도구에서 hover → 초록=padding, 주황=margin)
2. padding인가 margin인가
3. 내가 준 건가 브라우저 기본인가 (`user agent stylesheet`)

### `:empty` 로 "비었을 때만 표시" 하는 법
```css
#reviewList:empty::before {
  content: "아직 한줄평이 없습니다.";
  color: #a1a1aa;
  font-size: 14px;
}
```

목록이 비면 문구가 나타나고, `li`가 하나라도 생기면 저절로 사라짐. **JS 0줄.**

⚠️ **함정: 공백/줄바꿈 하나만 있어도 매치 안 됨.**
```html
<ul id="reviewList"></ul>   <!-- ✅ 반드시 한 줄로 붙여쓰기 -->
```

### `:empty::before` 구조 이해
```
#reviewList     :empty          ::before
─────────       ──────          ────────
이 요소 중에서    비어 있는 것의    가짜 앞자식
```

- `:empty` = **조건(필터).** 판정만 함
- `::before` = 그 조건을 통과한 요소에 **가짜 자식을 만들어 넣음**
- `content` = 그 가짜 자식의 내용 (**필수.** 없으면 `::before`가 안 만들어짐)
- `::before`는 진짜 자식이 아니라서 `:empty` 판정에 영향 없음

**콜론 개수:**
| | 이름 | 뜻 |
|---|---|---|
| `:` 하나 | 가상 클래스 | **상태·조건** — `:hover` `:focus` `:empty` |
| `::` 둘 | 가상 요소 | **없던 요소를 만듦** — `::before` `::after` |

*콜론 하나는 조건, 둘은 창조.*

### CSS가 조용히 무시하는 것들 (겪은 것 모음)
에러가 안 나서 원인 찾기 어려운 것들:

```css
border: 1px;              /* ❌ 종류(solid) 기본값이 none → 안 그려짐 */
width: 300;               /* ❌ CSS는 단위 없으면 그 줄을 버림 */
justify-content: center;  /* ❌ display:flex 없으면 무시 */
```

확인법: 개발자도구 → Styles → **먹지 않은 선언에 취소선**이 그어져 있음

---

## JavaScript

### `<script>` 는 어디에
`</body>` **바로 앞.** HTML은 위에서 아래로 읽히니, `head`에 두면 아직 없는 요소를 찾다가 `null`이 나옴.

### `.value` 는 실시간으로 연결된 값인가
아니고 **그 순간의 스냅샷.** 클릭한 시점의 값을 읽어서 복사함.

```js
const text = reviewInput.value.trim();  // text = "재밌었다"  ← 복사
reviewList.textContent = text;          // 화면에 표시
reviewInput.value = "";                 // 입력칸만 비움. text는 그대로
```

그래서 마지막 줄에서 입력칸을 비워도 화면의 글자가 안 사라짐. **변수에 담는 것 = 그 시점의 값을 붙잡아두는 것.**

### `input`에 `.textContent = ""` 는 왜 안 되나
`<input>`은 **빈 태그(void element)라서 태그 사이가 없음.** 자식을 가질 수 없다고 명세에 금지돼 있음.

```js
reviewInput.textContent          // 항상 ""
reviewInput.textContent = "안녕"  // 에러도 안 나고 아무 일도 안 일어남
```

입력한 글자는 HTML이 아니라 **DOM 프로퍼티(`value`)** 에 저장됨. 입력칸에 타이핑해도 Elements 탭의 HTML이 안 변하는 걸로 확인 가능.

| 요소 | 쓰는 것 |
|---|---|
| `p` `div` `li` `button` `h1` | `.textContent` |
| `input` `select` | `.value` |
| `textarea` | **`.value`** (닫는 태그가 있는데도!) |

`textarea`의 태그 사이 글자는 "처음에 보여줄 값"일 뿐. 사용자가 타이핑하면 `.value`와 따로 놈.

**규칙: 사용자가 입력하는 요소는 무조건 `.value`.**

### `innerHTML = ""` 는 자식을 다 지우는 건가
맞음. 안쪽 HTML 전체를 빈 문자열로 만드니 자식이 통째로 사라짐.

```js
box.textContent = "<b>안녕</b>";   // 화면에:  <b>안녕</b>  (글자로)
box.innerHTML  = "<b>안녕</b>";   // 화면에:  안녕 (굵게)   (HTML로 해석)
```

⚠️ **사용자 입력을 `innerHTML`에 넣으면 XSS 취약점.**
```js
li.innerHTML = comment.text;   // ❌ <img src=x onerror="..."> 가 실행됨
li.textContent = comment.text; // ✅
```

**원칙: 사용자 입력은 절대 `innerHTML`에 넣지 않는다.** 껍데기를 비울 때(`= ""`)나 내가 쓴 고정 HTML에만.

더 명확한 대안: `reviewList.replaceChildren();`

### `.focus()` 는 뭐하는 메서드
그 요소에 **키보드 입력 권한**을 줌. 입력칸이면 커서가 들어감.

화면에 입력칸이 여러 개여도 키보드 입력을 받는 요소는 항상 하나 = "포커스를 가졌다". `Tab`으로 이동하는 그것.

```js
reviewInput.value = "";   // 비우고
reviewInput.focus();      // 커서를 다시 입력칸으로
```

버튼을 클릭하면 포커스가 버튼으로 가버려서, 다음 입력을 하려면 사용자가 입력칸을 다시 클릭해야 함. 그 클릭을 없애주는 것.

관련:
```js
input.blur();             // 포커스 빼기
document.activeElement    // 지금 포커스를 가진 요소
```
```css
.input:focus { border-color: #4f46e5; }
```

⚠️ `outline: none` 만 쓰면 안 됨 — 키보드 사용자가 자기 위치를 알 수 없게 됨. 반드시 대체 표시를 줄 것.

### `forEach` 문법 구조 — 매개변수가 함수고, 그 함수의 매개변수가 항목?
맞음.

```js
comments.forEach(function (comment) { ... });
   │        │             │
   │        │             └─ 배열의 각 항목이 여기로 들어옴
   │        └─ 함수 자체를 인자로 넘김 (= 콜백)
   └─ 배열
```

**`comment`에 값을 넣어주는 건 내가 아니라 `forEach`.** 나는 "각 항목으로 뭘 할지"만 넘기고, 언제 몇 번 실행할지는 `forEach`가 정함.

- 이름은 자유. 관례는 배열 이름의 단수형 (`comments` → `comment`)
- 매개변수 3개까지 옴: `(항목, index, 배열)`. 안 받아도 에러 없음
- 화살표 함수: `comments.forEach((comment) => { ... })` — 같은 뜻, 짧게

형제들: `map`(변환) `filter`(골라내기) `find`(하나 찾기) `some`/`every`(검사)

### `dataset` 은 뭔가, 문법을 모르겠다
`data-*` HTML 커스텀 속성을 다루는 통로.

HTML은 정해진 속성만 허용하는데, **`data-`로 시작하면 뭐든 붙일 수 있음.**

```js
delBtn.dataset.id = 123;        // 쓰기 → <button data-id="123">
const x = delBtn.dataset.id;    // 읽기 → "123"
```

`data-` 접두사는 떼고 씀. 여러 단어면 HTML은 하이픈, JS는 카멜케이스:
```html
<div data-user-name="철수">   →   el.dataset.userName
```

**왜 필요한가:** 이벤트 위임에서는 "눌린 버튼"만 손에 들어옴. 그 버튼이 어느 항목 것인지 알 수 없으니, 만들 때 미리 신분증을 붙여두는 것.

⚠️ **항상 문자열.** `Number()`로 변환해야 `===` 비교가 맞음.

### `Number(...)` 로 캐스팅하는 이유
```js
comment.id              // 1730000000  (숫자, Date.now())
e.target.dataset.id     // "1730000000" (문자열)

1730000000 !== "1730000000"   // 항상 true → filter가 아무것도 못 걸러냄
```
`===`/`!==`는 타입까지 비교하므로 변환 필수. **에러도 안 나서 "삭제가 안 되는" 증상만 남음.**

### `event.target` 은 뭔가
**이벤트가 실제로 발생한 요소.**

```
<ul>  ← 리스너는 여기
  <li>
    <span>재밌었다</span>      ← 이걸 누르면 target = span
    <button>삭제</button>      ← 이걸 누르면 target = button
  </li>
</ul>
```

리스너는 하나인데 `target`은 누른 위치에 따라 매번 달라짐.

확인법: `reviewList.addEventListener("click", (e) => console.log(e.target));`

**버블링:** 클릭은 자식 → 부모로 거슬러 올라감. 그래서 부모 하나에 리스너를 달아도 모든 자식의 클릭을 잡을 수 있음 (= 이벤트 위임의 원리).

| | 뜻 |
|---|---|
| `e.target` | **사건이 시작된 곳** (매번 다름) |
| `e.currentTarget` | **리스너를 달아둔 곳** (항상 같음) |

*타겟은 맞은 놈, 커런트타겟은 지켜보던 놈.*

실무: 버튼 안에 아이콘이 있으면 `target`이 아이콘이 됨 → `e.target.closest(".del")` 이 더 튼튼함.

### `e.target.classList.contains("del")` 이 너무 길다
왼쪽부터 좁혀 들어가는 것:
```
e                                    이벤트 객체
e.target                             클릭된 요소
e.target.classList                   그 요소의 클래스 목록
e.target.classList.contains("del")   목록에 "del"이 있나? → true/false
```

쪼개도 됨:
```js
const el = e.target;
if (!el.classList.contains("del")) return;
```

`classList` 메서드: `add()` `remove()` `toggle()` `contains()`

### `submitBtn.click()` 은 클릭한 셈 치는 건가
**진짜 click 이벤트가 발생함.** 사용자가 손으로 누른 것과 구분되지 않음.

### `.click()` 이 왜 `addEventListener`를 호출하나 — 생긴 게 전혀 다른데
**`.click()`이 `addEventListener`를 호출하는 게 아님.** 중간에 브라우저가 있음.

```
       등록 (한 번)                     발생 (매번)
addEventListener("click", fn)      .click()  또는  실제 클릭
         │                                   │
         └──────→ 【 브라우저의 이벤트 목록 】 ←────┘
                          │
                          └─→ fn 실행
```

1. `addEventListener` = **"click이 오면 이 함수를 실행해줘"** 라고 브라우저에 보관 (실행 아님)
2. `.click()` = **"click 이벤트를 발생시켜라"**
3. 브라우저가 목록을 뒤져서 등록된 함수를 실행

비유: `addEventListener`는 "택배 오면 이 번호로 전화주세요" 등록, `.click()`은 택배 도착. 경비실(브라우저)이 중개.

그래서 한 버튼에 여러 개 등록 가능하고, `.click()` 한 번에 다 실행됨.

**괄호 주의:**
```js
addEventListener("click", addComment);     // ✅ 함수를 넘김
addEventListener("click", addComment());   // ❌ 지금 실행하고 결과(undefined)를 등록
```

### 용어 정리
> "`submitBtn`에 **click 이벤트가 발생하고**, 등록된 **리스너(콜백)가 실행된다**"

| 용어 | 뜻 |
|---|---|
| 이벤트 | 일어난 사건 (click, input, keydown) |
| 리스너 / 핸들러 | 그 사건에 반응하는 함수 |
| 콜백 | 남에게 넘겨서 나중에 실행되는 함수 (더 넓은 개념) |
| 디스패치 | 이벤트를 발생시키는 것 (`.click()`) |

### `keydown`에서 입력창 비우기를 안 해도 되나
`submitBtn.click()`이 등록 핸들러를 **통째로** 실행하므로 `value = ""`, `focus()`도 다 같이 실행됨.

복사해두면 나중에 한쪽만 고쳐서 "버튼은 되는데 엔터는 안 되는" 버그가 남 → **같은 로직은 한 곳에만 (DRY).**

더 나은 형태 — 함수로 빼기:
```js
function addComment() { ... }

submitBtn.addEventListener("click", addComment);
reviewInput.addEventListener("keydown", function (e) {
  if (e.isComposing) return;
  if (e.key === "Enter") addComment();
});
```
"버튼을 누른다"가 아니라 "한줄평을 추가한다"로 읽힘. UI에 묶이지 않음.

### 한글 입력 후 엔터를 누르면 마지막 낱말이 중복된다
**IME(한글 입력기) 조합** 문제.

영어는 키 하나 = 글자 하나지만, 한글은 `ㅇ → 아 → 안` 처럼 자모를 조합함. 그래서 **"조합 중"** 상태가 존재.

엔터가 두 역할을 겸함: ① 조합 확정 ② 실제 엔터 → 이벤트가 두 번 발생 → 중복 등록.

```js
input.addEventListener("keydown", function (e) {
  if (e.isComposing) return;        // 조합 중이면 무시
  if (e.key === "Enter") addComment();
});
```

정상 동작: 엔터 한 번 = 글자 확정, 두 번째 = 등록. (카톡·슬랙도 이렇게 동작)

- 옛날 방식: `if (e.keyCode === 229) return;`
- 관련 이벤트: `compositionstart` / `compositionend`
- **`form` + `submit`을 쓰면 이 문제를 대부분 안 겪음**

### `form` + `submit` 이 정석인 이유
```html
<form class="form-row" id="reviewForm">
  <input class="input" id="reviewInput" type="text" />
  <button class="btn" type="submit">등록</button>
</form>
```
```js
reviewForm.addEventListener("submit", function (e) {
  e.preventDefault();   // 새로고침 막기 (필수!)
  addComment();
});
```

`form` 안에서는 **엔터가 저절로 submit을 일으킴** → `keydown` 코드가 아예 불필요.
`preventDefault()` 없으면 등록하자마자 페이지가 새로고침돼서 다 날아감.

공짜로 따라오는 것: 엔터키, 자동완성, `required` 검증

### 오타가 왜 어떤 건 에러가 나고 어떤 건 조용한가
```js
delBtn.addEventListner(...)     // TypeError! 없는 함수를 호출 → 에러
delBtn.textContext = "삭제"     // 조용함. 새 속성이 그냥 만들어짐
```

JS 객체는 **없는 속성에 값을 넣으면 새로 만들어버림.**

> **읽기/호출 오타는 에러가 나고, 쓰기(대입) 오타는 조용히 넘어간다.**

예방: 속성명을 직접 타이핑하지 말고 자동완성(`Tab`) 사용.

디버깅: Elements 탭에서 `<button class="del"></button>` 처럼 요소는 있는데 내용이 빈 걸 확인 → "안 만들어졌다"가 아니라 "만들어졌는데 비었다"로 범위 좁히기.

### 핵심 구조 — 데이터 → 렌더
```
4단계:  버튼 클릭 → 화면에 li 를 직접 붙임
5단계:  버튼 클릭 → 배열에 넣음 → 화면을 처음부터 다시 그림

┌──────────┐              ┌──────────┐
│   배열    │ ─render()─→  │   화면    │
│  (진실)   │              │ (그림자)  │
└──────────┘              └──────────┘
```

**배열이 진짜고 화면은 복사본.** 화면을 고치려 하지 말고 배열을 고칠 것.

얻는 것: 개수 세기(`length`), 삭제, 저장이 전부 쉬워짐. React가 자동화한 게 이것.

`innerHTML = ""` 후 전부 다시 그리는 게 비효율 같지만, **화면이 항상 배열과 100% 일치**해서 버그가 생길 구석이 없음. 수백 개 넘기 전엔 최적화 불필요.

---

## Git

### `error: src refspec main does not match any`
커밋은 `master`에 됐는데 `main`으로 push하려 했음. 로컬 git이 옛날 기본값을 씀.

```bash
git branch -M main
git config --global init.defaultBranch main   # 앞으로 예방
```

### 커밋했는데 파일이 안 올라감
`git add`를 안 하면 커밋에 안 들어감. **커밋 = 스테이징된 것만.**

**커밋 전에 항상 `git status` / `git diff --staged`.**

### `! [rejected] main -> main (non-fast-forward)`
로컬과 원격 히스토리가 **갈라짐(diverged).** GitHub 웹에서 편집했을 때 발생.

```
## main...origin/main [ahead 1, behind 1]
                        ↑ 내가 1개 앞   ↑ 원격이 1개 앞
```

```bash
git pull --rebase
git push
```

`--rebase` = 원격 것을 먼저 깔고 내 커밋을 그 위에 다시 얹음 → 히스토리가 일직선.
그냥 `git pull`은 의미 없는 병합 커밋이 생김.

```bash
git config --global pull.rebase true   # 기본값으로
```

⚠️ `git pull` 만 치면 "어떻게 합칠지 정해달라"며 **아무것도 안 하고 멈춤.**

**예방: 로컬과 GitHub 웹 양쪽에서 편집하지 말 것.**

### diff / 커밋 히스토리는 뭐에 쓰나
1. **커밋 전 자기검열** — `git diff`, `git diff --staged`. `console.log` 남긴 것, 실수로 지운 줄 잡힘
2. **남에게 설명** — PR 리뷰
3. **시간여행** — `git log -p 파일`, `git blame 파일`. "이 줄 왜 넣었지?" → 커밋 메시지에서 확인. **코드는 "무엇", 히스토리는 "왜"**
4. **안전망** — 되돌릴 수 있으니 과감하게 실험 가능

### 포맷 변경과 기능 변경을 왜 나누나
```diff
- function() {
+ function () {   ← 기능과 무관한 노이즈
```
이런 게 많으면 진짜 변경이 파묻힘.

**딱 한 번만 하면 됨:** 전체 포맷 → `style: 포맷 일괄 적용` 커밋 → 그 뒤 format on save를 켜두면 포맷 diff가 애초에 안 생김.

### format on save를 켜면 커밋할 때 정렬되는 건가
아니고 **저장(`Cmd+S`)할 때** VSCode가 정렬함. git은 아무것도 안 함. 커밋은 이미 정렬된 파일을 담을 뿐.

- 수동: `Shift + Option + F`
- 자동: `Cmd + ,` → `format on save` 체크
- 포매터는 **모양만** 정리. 오타·무효한 CSS 값은 안 고쳐줌

### 커밋 코멘트 vs PR 리뷰
```
커밋 코멘트  →  이미 main에 들어간 코드에 다는 사후 메모
PR 리뷰      →  들어가기 전에 막을 수 있는 관문
```

PR에 딸려오는 것: 커밋 여러 개를 하나의 diff로, Approve/Request changes(승인 전 merge 차단), 코멘트 Resolve 추적, 자동 테스트, "왜 했는지" 문서.

혼자여도 쓸 이유: 스스로 리뷰하게 됨 / main이 항상 동작 / 팀에선 100% 이 방식.

```bash
git switch -c feat/기능이름
git push -u origin feat/기능이름
gh pr create --fill
gh pr merge --squash --delete-branch
```

---

## 반복해서 지적받은 것

- 속성 `=` 앞뒤 공백: `class = "card"` → `class="card"`
- 속성값 따옴표 필수: `id = submitBtn` → `id="submitBtn"`
- 세미콜론 빠짐 (마지막 줄이라 동작해도, 아래 줄 추가하면 둘 다 죽음)
- 오타 — 특히 **대입 오타는 에러가 안 남**
- 선택자를 **필요한 만큼만 좁게**: `p { margin: 0 }` → `.card-head p { margin: 0 }`
- 주석 찌꺼기 남기지 않기 (주석은 미래의 나에게 남기는 메모)
- 색 이름(`blueviolet`)보다 hex/rgb — 이름은 140개뿐이라 미세 조정 불가
- 카드 테두리는 **있는 듯 없는 듯** (`#000000` 대신 `#e4e4e7`)
