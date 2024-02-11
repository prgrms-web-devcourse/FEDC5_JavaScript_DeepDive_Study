# 30장 Date
표준 빌트인 객체인 Date는 날짜와 시간(연, 월, 일, 시, 분, 초, 밀리초)을 위한 메서드를 제공하는 빌트인 객체이면서 생성자 함수입니다.

`UTC`(협정 세계시 Coordinated Universal Time)는 국제 표준시를 말합니다.(UTC는 `GMT`로 불리기도 합니다. 둘은 초의 소수점 단위에서만 차이가 나기 때문에 일상에서는 혼용됩니다.)

`KST`(한국 표준시 Korea Standard Time)는 UTC에 9시간을 더한 시간입니다. (KST는 UCT보다 9시간이 빠릅니다.)

**현재 날짜와 시간은 자바스크립트 코드가 실행된 시스템의 시계에 의해 결정됩니다.**

<br>

## 30.1 Date 생성자 함수
`Date`는 생성자 함수입니다. Date 생성자 함수로 생성한 Date 객체는 내부적으로 날짜와 시간을 나타내는 정수값을 갖습니다.

이 값은 1970년 1월 1일 00:00:00(UTC)을 기점으로 Date 객체가 나타내는 날짜와 시간까지의 밀리초를 나타냅니다.

ex) 1970년 1월 1일 0시의 Date 객체는 내부적으로 정수값 0을 갖습니다.
ex) 1970년 1월 2일 0시의 Date 객체는 내부적으로 정수값 86,400,000(24h * 60m * 60s * 1000ms)을 갖습니다.

지금부터 Date 생성자 함수를 생성하는 방법에 대해 알아보겠습니다.

### 30.1.1 new Date()
Date 생성자 함수를 인수 없이 new 연산자와 함께 호출하면 현재 날짜와 시간을 가지는 Date 객체를 반환합니다.
```javascript
new Date(); Sun Feb 11 2024 14:00:06 GMT+0900 (한국 표준시)
```
Date 생성자 함수를 new 연산자 없이 호출하면 Date 객체를 반환하지 않고 문자열을 반환합니다.

<br>

### 30.1.2 new Date(milliseconds)
Date 생성자 함수에 숫자 타입의 밀리초를 인수로 전달하면 1970년 1월 1일 00:00:00(UTC)를 기점으로 인수로 전달된 밀리초만큼 경과한 날짜와 시간을 나타내는 Date 객체를 반환합니다.
```javascript
// 한국 표준시 KST는 협정 세계시 UTC에 9시간을 더한 시간입니다.
new Date(0); // Thu Jan 01 1970 09:00:00 GMT+0900 (한국 표준시)

/*
86400000ms는 1day를 의미합니다.
1s = 1,000ms
1m = 60s * 1,000ms = 60,000ms
1h = 60m * 60,000ms = 3,600,000ms
1d = 24h * 3,600,000ms = 86,400,000ms
*/
new Date(86400000); // Fri Jan 02 1970 09:00:00 GMT+0900 (한국 표준시)
```

<br>

### 30.1.3 new Date(dateString)
new 생성자 함수에 날짜와 시간을 나타내는 문자열을 인수로 전달하면 지정된 날짜와 시간을 나타내는 Date 객체를 반환합니다. (이때 인수로 전달한 문자열은 Date.parse 메서드에 의해 해석 가능한 형식이어야 한다.)
```javascript
new Date('May 26, 2020 10:00:00'); // Tue May 26 2020 10:00:00 GMT+0900 (한국 표준시)

new Date('2024/02/11/14:14:00'); // Sun Feb 11 2024 14:14:00 GMT+0900 (한국 표준시)
```

<br>

### 30.1.4 new Date(year, month[, day, hour, minute, second, millisecond])
Date 생성자 함수에 연, 월, 일, 시, 분, 초, 밀리초를 의미하는 숫자를 인수로 전달하면 지정된 날짜와 시간을 나타내는 Date 객체를 반환합니다. (이때 연, 월은 반드시 저장해야 합니다.)
|인수|내용|
|------|---|
|year|연을 나타내는 1900년 이후의 정수, 0 ~ 99는 1900부터 1999로 처리됩니다.|
|month|월을 나타내는 0 ~ 11까지의 정수(0부터 시작하며 0은 1월을 의미합니다.)|
|day|일을 나타내는 1 ~ 31까지의 정수|
|hour|시을 나타내는 0 ~ 23까지의 정수|
|minute|분을 나타내는 0 ~ 59까지의 정수|
|second|초를 나타내는 0 ~ 59까지의 정수|
|millisecond|밀리초을 나타내는 0 ~ 999까지의 정수|

연, 월을 지정하지 않은 경우 1970년 1월 1일 00:00:00(UTC)을 나타내는 Date 객체를 반환합니다.

```javascript
// 월을 나타내는 2는 3월을 의미합니다.
new Date(2024, 2); // Fri Mar 01 2024 00:00:00 GMT+0900 (한국 표준시)
new Date(2024, 2, 1, 10, 00, 00, 0); // Fri Mar 01 2024 10:00:00 GMT+0900 (한국 표준시)

// 다음처럼 표기하면 가독성이 훨씬 좋아집니다.
new Date('2024/3/1/10:00:00:00'); Fri Mar 01 2024 10:00:00 GMT+0900 (한국 표준시)

<br>

## 30.2 Date 메서드
### 30.2.1 Date.now
1970년 1월 1일 00:00:00(UTC)을 기점으로 현재 시간까지 경과한 밀리초를 숫자로 반환합니다.
```javascript
const now = Date.now(); // 1707629225473
new Date(now); // Sun Feb 11 2024 14:27:05 GMT+0900 (한국 표준시)
```

<br>

### 30.2.2 Date.parse
1970년 1월 1일 00:00:00(UTC)을 기점으로 인수로 전달된 지정 시간(new Date(dateString)의 인수와 동일한 형식)까지의 밀리초를 숫자로 반환합니다.
```javascript
// UTC
Date.parse('Jan 11, 2024 00:00:00 UTC'); // 1704931200000
// KST
Date.parse('Jan 2, 2024 09:00:00'); // 1704153600000
// KST
Date.parse('2024/02/11/14:35:00'); // 1707629700000
```

<br>

### 30.2.3 Date.UTC
1970년 1월 1일 00:00:00(UTC)을 기점으로 인수로 전달된 지정 시간까지의 밀리초를 숫자로 반환합니다.

Date.UTC 메서드는 new Date(year, month[, day, hour, minute, second, millisecond])와 같은 형식의 인수를 사용해야 합니다. 해당 인수는 로컬 타임이 아닌 UTC로 인식되고 월(month)을 의미하는 0은 1월을 나타냅니다.
```javascript
Date.UTC(1970, 0, 2); // 86400000
Date.UTC('1970/1/2'); // NaN
```

<br>

### 30.2.4 Date.prototype.getFullYear
Date 객체의 연도를 나타내는 정수를 반환합니다.
```javascript
new Date('2020/07/24').getFullYear(); // 2020
```

<br>

### 30.2.5 Date.prototype.setFullYear
Date 객체에 연도를 나타내는 정수를 설정합니다. 연도 이외에 옵션으로 월, 일도 설정할 수 있습니다.
```javascript
const today = new Date();

// 년도 지정
today.setFullYear(2002);
today.getFullYear(); // 2002

// 년도/월/일 지정
today.setFullYear(2002, 0, 1);
today.getFullYear(); // 2002
```

<br>

### 30.2.6 Date.prototype.getMonth
Date 객체의 월을 나타내는 0 ~ 11의 정수를 반환합니다. 1월은 0, 12월은 11 입니다.
```javascript
new Date('2024/02/11').getMonth(); // 1
```

<br>

### 30.2.7 Date.prototype.setMonth
Date 객체에 월을 나타내는 0 ~ 11의 정수를 설정합니다. (월 이외의 옵션으로 일도 설정할 수 있습니다.)
```javascript
const today = new Date();

// 월 지정
today.setMonth(0); // 1월
today.getMonth(); // 0

// 월/일 지정
today.setMonth(11, 1); // 12월 1일
today.getMonth(); // 11
```

<br>

### 30.2.8 Date.prototype.getDate
Date 객체의 날짜(1 ~ 31)를 나타내는 정수를 반환합니다.
```javascript
new Date('2024/02/11').getDate(); // 11
```

<br>

### 30.2.9 Date.prototype.setDate
Date 객체에 날짜(1 ~ 31)를 나타내는 정수를 설정한다.
```javascript
const today = new Date();
today.setDate(1);
today.getDate(); // 1
```
<br>

### 30.2.10 Date.prototype.getDay
Date 객체의 요일 (0 ~ 6)을 나타내는 정수를 반환합니다.
|요일|반환값|
|------|---|
|일요일|0|
|월요일|1|
|화요일|2|
|수요일|3|
|목요일|4|
|금요일|5|
|토요일|6|

```javascript
new Date('2024/01/16').getDay(); // 2
```

<br>

### 30.2.11 Date.prototype.getHours
Date 객체의 시간(0 ~ 23)을 나타내는 정수를 반환합니다.
```javascript
new Date('2020/07/24/12:00').getHours(); // 12
```

<br>

### 30.2.12 Date.prototype.setHours
Date 객체의 시간(0 ~ 23)을 나타내는 정수를 설정합니다. (시간 이외에 옵션으로 분, 초, 밀리초도 설정할 수 있습니다.)
```javascript
const today = new Date();

// 시간 지정
today.setHours(11);
today.getHours(); // 7

// 시간/분/초/밀리초 지정
today.setHours(0,0,0,0); // 00:00:00:00
today.getHours(); 0
```

<br>

### 30.2.13 Date.prototype.getMinutes
Date 객체의 분(0 ~ 59)을 나타내는 정수를 반환합니다.
```javascript
new Date('2024/02/11/14:57').getMinutes(); // 57
```

<br>

### 30.2.14 Date.prototype.setMinutes
Date 객체의 분(0 ~ 59)을 나타내는 정수를 설정합니다. (분 이외에 옵션으로 초, 밀리초도 설정할 수 있습니다.)
```javascript
const today = new Date();

// 분 지정
today.setMinutes(50);
today.getMinutes(); // 50

// 분/초/밀리초 지정
today.setMinutes(5,10,999); // HH:05:10:999
today.getMinutes(); // 5
```

<br>

### 30.2.15 Date.prototype.getSeconds
Date 객체의 초(0 ~ 59)를 나타내는 정수를 반환합니다.
```javascript
new Date('2024/02/11/12:30:10').getSeconds(); // 10
```

<br>

### 30.2.16 Date.prototype.setSeconds
Date 객체의 초(0 ~ 59)를 나타내는 정수를 설정합니다. (초 이외에 옵션으로 밀리초도 설정할 수 있습니다.)
```javascript
const today = new Date();

// 초 지정
today.setSeconds(30);
today.getSeconds(); // 30

// 초/밀리초 지정
today.setSeconds(10,0); // HH:MM:10:000
today.getSeconds(); // 10
```

<br>

### 30.2.17 Date.prototype.getMilliseconds
Date 객체의 밀리초(0 ~ 999)를 나타내는 정수를 반환합니다.
```javascript
new Date('2024/02/11/12:30:10:150').getMilliseconds(); // 150
```

<br>

### 30.2.18 Date.prototype.setMilliseconds
Date 객체의 밀리초(0 ~ 999)를 나타내는 정수를 설정합니다.
```javascript
const today = new Date();

// 밀리초 지정
today.setMilliseconds(123);
today.getMilliseconds(); // 123
```

<br>

### 30.2.19 Date.prototype.getTime()
1970년 1월 1일 00:00:00(UTC)를 기점으로 Date 객체의 시간까지 경과된 밀리초를 반환합니다.
```javascript
new Date().getTime(); // 1707631889198
```

<br>

### 30.2.20 Date.prototype.setTime()
1970년 1월 1일 00:00:00(UTC)를 기점으로 경과된 밀리초를 지정합니다.
```javascript
const today = new Date();
today.setTime(86400000); // 86400000은 1day를 나타냅니다.
console.log(today); // Fri Jan 02 1970 09:00:00 GMT+0900 (한국 표준시)
```

<br>

### 30.2.21 Date.prototype.getTimezoneOffset
UTC와 Date 객체에 지정된 로캘 시간과의 차이를 분 단위로 반환합니다. KST는 UTC에 9시간을 더한 시간입니다. (즉, UTC = KST - 9h)
```javascript
const today = new Date(); // today의 지정 로캘은 KST입니다.

today.getTimezoneOffset() / 60 // -9
```

<br>

### 30.2.22 Date.prototype.toDateString
사람이 읽을 수 있는 형식의 문자열로 Date 객체의 날짜를 반환합니다.
```javascript
const today = new Date('2024/2/11/12:30');

today.toString(); // Sun Feb 11 2024 12:30:00 GMT+0900 (한국 표준시)
today.toDateString(); // Sun Feb 11 2024
```

### 30.2.23 Date.prototype.toTimeString
사람이 읽을 수 있는 형식으로 Date 객체의 시간을 문자열로 반환합니다.
```javascript
const today = new Date('2024/2/11/12:30');

today.toString(); // Sun Feb 11 2024 12:30:00 GMT+0900 (한국 표준시)
today.toTimeString(); // 12:30:00 GMT+0900 (한국 표준시)
```

<br>

### 30.2.24 Date.prototype.toISOString
`ISO 8601` 형식으로 Date 객체의 날짜와 시간을 표현한 문자열을 반환합니다.
```javascript
const today = new Date('2024/2/11/12:30');

today.toString(); // Sun Feb 11 2024 12:30:00 GMT+0900 (한국 표준시)
today.toISOString(); // 2024-02-11T03:30:00.000Z

today.toISOString().slice(0, 10); // 2024-02-11
today.toISOString().slice(0, 10).replace(/-/g, ''); // 20240211
```

<br>

### 30.2.25 Date.prototype.toLocaleString
인수로 전달한 로캘을 기준으로 Date 객체의 날짜와 시간을 표현한 문자열을 반환합니다. 인수를 생략한 경우 브라우저가 동작 중인 시스템의 로캘을 적용합니다.
```javascript
const today = new Date('2024/2/11/12:30');

today.toString(); // Sun Feb 11 2024 12:30:00 GMT+0900 (한국 표준시)
today.toLocaleString(); // 2024. 2. 11. 오후 12:30:00
today.toLocaleString('ko-KR'); // 2024. 2. 11. 오후 12:30:00
today.toLocaleString('en-US'); // 2/11/2024, 12:30:00 PM
today.toLocaleString('ja-JP'); // 2024/2/11 12:30:00
```

<br>

### 30.2.26 Date.prototype.toLocaleTimeString
인수로 전달한 로캘을 기준으로 Date 객체의 시간을 표현한 문자열을 반환합니다. 인수를 생략한 경우 브라우저가 동작 중인 시스템의 로캘을 적용합니다.
```javascript
const today = new Date('2023/12/25/12:17');

today.toString(); // Mon Dec 25 2023 12:17:00 GMT+0900 (한국 표준시)
today.toLocaleTimeString(); // 오후 12:17:00
today.toLocaleTimeString('ko-KR'); // 오후 12:17:00
today.toLocaleTimeString('en-US'); // 12:17:00 PM
today.toLocaleTimeString('ja-JP'); // 12:17:00
```

<br>

## 30.3 Date를 활용한 시계 예제
다음 예제는 현재 날짜와 시간을 초 단위로 반복 출력합니다.
```javascript
(function printNow(){
  const today = new Date();

  const dayNames = [
    '(일요일)',
    '(월요일)',
    '(화요일)',
    '(수요일)',
    '(목요일)',
    '(금요일)',
    '(토요일)',
  ];

  const day = dayNames[today.getDate()];

  const year = today.getFullYear();
  const month = today.getMonth() + 1;
  const date = today.getDate();
  let hour = today.getHours();
  let minute = today.getMinutes();
  let second = today.getSeconds();
  const ampm = hour >= 12 ? 'PM' : 'AM';

  hour %= 12;
  hour = hour || 12; // hour가 0이면 12를 재할당

  // 10 미만인 분과 초를 2자리로 변경
  minute = minute < 10 ? '0' + minute : minute;
  second = second < 10 ? '0' + second : second;

  const now = `${year}년 ${month}월 ${date}일 ${day} ${hour}:${minute}:${second} ${ampm}`;

  console.log(now);

  setTimeout(printNow, 1000);
}());
```