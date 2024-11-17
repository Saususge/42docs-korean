---
layout: default
title: Colors
parent: MiniLibX
grand_parent: Libs
nav_order: 3
---

# Colors
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

색상은 int 형식으로 표현됩니다. 따라서 ARGB 값을 담을 수 있는 정수를 얻기 위해서는 몇 가지 복잡한 작업이 필요합니다.

## 색상 정수 표준(The color integer standard)

우리는 TRGB 포맷을 사용하기 위해 비트를 시프트합니다. 색상을 정의하려면 다음과 같이 초기화합니다: `0xTTRRGGBB` 여기서 각 문자는 다음과 같이 표현됩니다:

Letter | Description
:-----:|:-----------:
`T`    | 투명도
`R`    | 빨간색
`G`    | 초록색
`B`    | 파란색

RGB 색상은 다음과 같이 초기화 됩니다. 몇가지 예를 보여드리겠습니다:

Color | TRGB representation
:----:|:-------------------:
red   | `0x00FF0000`
green | `0x0000FF00`
blue  | `0x000000FF`

## 색깔 인코딩, 디코딩하기

우리는 두가지 방법으로 색을 인코딩, 디코딩 할 수 있습니다:

 - 비트 시프트 (BitShifting)
 - char/int 형변환 (char/int conversion)

### 비트 시프트 (BitShifting)

각 바이트는 `2^8 = 256` 값을 포함하고 있습니다 ([1 byte = 8 bits](https://www.google.com/search?q=size+bytes+to+bit)),
그리고 RGB 값은 0~255까지의 범위를 가지기 때문에 int(4 바이트)를 사용하여 완벽하게 값을 맞출 수 있습니다. 값을 프로그래밍 적으로 설정하기 위해 `bitshifting`을 사용합니다.
이제 이 작업을 수행하는 함수를 만들어 볼까요?:

```c
int	create_trgb(int t, int r, int g, int b)
{
	return (t << 24 | r << 16 | g << 8 | b);
}
```

정수는 오른쪽에서 왼쪽으로 저장되기 때문에 (little endian 방식), 각 값을 해당하는 비트만큼 뒤로 비트 시프트해야 합니다. 또한, 역으로 비트 시프트를 수행하여 인코딩된 TRGB 정수에서 정수 값을 추출할 수 있습니다.

```c
int	get_t(int trgb)
{
	return ((trgb >> 24) & 0xFF);
}

int	get_r(int trgb)
{
	return ((trgb >> 16) & 0xFF);
}

int	get_g(int trgb)
{
	return ((trgb >> 8) & 0xFF);
}

int	get_b(int trgb)
{
	return (trgb & 0xFF);
}
```

### char/int 형변환 (char/int conversion)

위에서 말했듯이 각 바이트는 256의 값을 포함하고 RGB는 0~255까지의 범위를 가집니다. 따라서 우린 각 TRGB 파라미터 `{T, R, G, B}`에 대해 `unsigned char`(1바이트)를 사용하고, TRGB값을 저장하기 위해 int형을 사용 가능합니다. 값을 프로그래밍적으로 설정하려면 다음과 같이 타입 변환을 사용 가능합니다.

```c
int	create_trgb(unsigned char t, unsigned char r, unsigned char g, unsigned char b)
{
	return (*(int *)(unsigned char [4]){b, g, r, t});
}

unsigned char	get_t(int trgb)
{
	return (((unsigned char *)&trgb)[3]);
}

unsigned char	get_r(int trgb)
{
	return (((unsigned char *)&trgb)[2]);
}

unsigned char	get_g(int trgb)
{
	return (((unsigned char *)&trgb)[1]);
}

unsigned char	get_b(int trgb)
{
	return (((unsigned char *)&trgb)[0]);
}
```

변환을 이해하기 위해 아래의 표를 참조할 수 있습니다.
여기서 `0x0FAE1`은 `int trgb`의 변수 주소입니다."

| Address |       char      |       int       |
| ------- | :-------------: | :-------------: |
| 0x0FAE1 | unsigned char b | int trgb        |
| 0x0FAE2 | unsigned char g | [allocated]     |
| 0x0FAE3 | unsigned char r | [allocated]     |
| 0x0FAE4 | unsigned char t | [allocated]     |

## Test your skills!

Now that you understand the basics of how the colors can be initialized, get
comfy and try creating the following color manipulation functions:
- `add_shade` is a function that accepts a double (distance) and a int (color)
as arguments, 0 will add no shading to the color, whilst 1 will make the color
completely dark. 0.5 will dim it halfway, and .25 a quarter way. You get the
point.
- `get_opposite` is a function that accepts a int (color) as argument and that
will invert the color accordingly.
