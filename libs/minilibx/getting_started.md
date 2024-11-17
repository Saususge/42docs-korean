---
layout: default
title: Getting started
parent: MiniLibX
grand_parent: Libs
nav_order: 2
---

# Getting started
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 소개

이제 MiniLibX로 뭘 할지 알았으니, 아주 기초적인 작업들을 해 보겠습니다. 이는 이 라이브러리를 이용해 어떻게 좋은 성능을 내는 코드를 짜는지에 관한 이해를 시켜 줄 겁니다. 많은 프로젝트에서 핵심은 성능이기 때문에 이 페이지를 확실하게 읽고 이해하는 것이 중요합니다.

## 설치

### macOS에서의 컴파일

MiniLibX가 MacOS의 Cocoa(AppKit)와 OpenGL을 요구하기 때문에 이들을 연결해야 함으로 컴파일 과정이 복잡할수 있습니다. 기본적인 컴파일 과정은 아래와 같습니다.

프로젝트의 최상위 폴더에 mlx라는 이름의 폴더에서 mlx라는 소스가 있다고 가정할때 오브젝트 파일을 위해 Makefile에 다음과 같은 규칙을 추가할 수 있습니다.

```makefile
%.o: %.c
	$(CC) -Wall -Wextra -Werror -Imlx -c $< -o $@
```

내부의 macOS API와 연결하기 위해선:

```makefile
$(NAME): $(OBJ)
	$(CC) $(OBJ) -Lmlx -lmlx -framework OpenGL -framework AppKit -o $(NAME)
```

주의: `libmlx.dylib` 파일은 동적 라이브러리기 때문에 빌드 타겟과 같은 디렉토리에 위치해있어야 합니다!

### 리눅스에서의 컴파일

리눅스는 Codam에서 제공하는 리눅스호환 버전 MLX를 사용 가능합니다.[mlx-linux](https://github.com/42Paris/minilibx-linux).
프로젝트 루트에 새로운 폴더 `mlx_linux`를 만든 후, 그 폴더에 Linux용 MLX를 압축풀기 하면 됩니다. 
이 라이브러리는 original-mlx와 동일한 함수와 함수호출들을 제공합니다. 다만 시스템 아키텍쳐에 따라 이미지에서 메모리 매직을 사용하는 객체의 구현이 달라질 수 있다는 점은 유의해야 합니다.
 

MiniLibX for Linux는 `xorg`, `x11`, `zlib`가 필요하기 때문에 의존성 패키지인 `xorg`, `libxext-dev`, `zlib1g-dev`를 설치할 필ㅇ가 있습니다.
우분투에서 설치하는법은 다음과 같습니다:
```sh
sudo apt-get update && sudo apt-get install xorg libxext-dev zlib1g-dev libbsd-dev
```
이제 레포지토리의 루트에서 `configure` 스크립트를 실행하면 됩니다.

프로젝트의 루트 디렉토리에 `mlx_linux`라는 이름의 폴더에 Linux용 `mlx`소스가 있다면, 오브젝트 파일에 대해 Makefile에 다음과 같은 규칙을 추가 가능합니다:

```makefile
%.o: %.c
	$(CC) -Wall -Wextra -Werror -I/usr/include -Imlx_linux -O3 -c $< -o $@
```

내부의 Linux API와 연결하기 위해선:

```makefile
$(NAME): $(OBJ)
	$(CC) $(OBJ) -Lmlx_linux -lmlx_Linux -L/usr/lib -Imlx_linux -lXext -lX11 -lm -lz -o $(NAME)
```

### Win10 에서 윈도우 띄우기 (WSL2)

Win10 WSL환경에대한 설치는 원문을 참고해주세요

[Win10에서의 gui 어플리케이션 실행법](https://harm-smits.github.io/42docs/libs/minilibx/getting_started.html#getting-a-screen-on-windows-10-wsl2)

### Win11에서 윈도우 띄우기 (WSLg)

Win11에서 WSL 설치시 그래픽 application 실행을 위한 WSLg가 자동 설치되기 때문에 별도의 설치가 필요없습니다만 만약 실행이 되지 않는다면 
[Linux용 Windows 하위 시스템 Linux GUI 앱 실행](https://learn.microsoft.com/ko-kr/windows/wsl/tutorials/gui-apps)을 참고하세요.

## 초기화

우리가 MiniLibX로 뭐라도 하려면 `<mlx.h>` 헤더를 추가해 모든 함수에 접근을 가능하게 해야하고
`mlx_init` 함수를 실행해야 합니다. 이는 그래픽 시스템에 연결 설정 후에 현재 MLX 인스턴스의 위치를 담고있는 `void *`를 반환 합니다.
MiniLibx를 초기화 하는 코드는 다음과 같습니다:

```c
#include <mlx.h>

int	main(void)
{
	void	*mlx;

	mlx = mlx_init();
}
```

코드를 실행하고 나면 렌더링은 커녕 아무것도 뜨지 않는다는 것을 알게 될겁니다. 이는 윈도우를 생성하지 않았기 때문입니다.
이제 간단한 윈도우를 초기화 해줄건데 윈도우를 끄기 위해서는
<kbd>CTRL</kbd> + <kbd>C</kbd>를 터미널에서 눌러줘야 합니다. 그렇지 않으면 계속 켜있을 것 입니다.
이후 우리가 방금 생성한 윈도우의 포인터를 반환하는 함수인 `mlx_new_window`를호출하면 윈도우의 높이, 너비, 이름을 지정 가능합니다.
그런 다음 `mlx_loop`를 호출 후 렌더링을 시작해야 합니다.
이제 1920x1080(너비가 1920이고 높이가 1080)의 크기를 가진 Hello, world라는 윈도우를 만들어봅시다:

```c
#include <mlx.h>

int	main(void)
{
	void	*mlx;
	void	*mlx_win;

	mlx = mlx_init();
	mlx_win = mlx_new_window(mlx, 1920, 1080, "Hello world!");
	mlx_loop(mlx);
}
```

## 이미지에 픽셀 쓰기

이제 기본적인 윈도우 관리를 할수있으니, 윈도우에 픽셀을 그릴 수 있습니다.
픽셀을 어떻게 놓을지는 선택에 따라 다르지만, 여기서는 최적화된 방법에 대해서도 다루겠습니다.
일단 `mlx_pixel_put`함수가 매우 느리다는것을 염두해 두세요. 이는 픽셀을 frame이 완전히 렌더링되기를 기다리지 않고 바로 그리려고 하기 때문입니다. 즉, 픽셀을 즉시 윈도우에 푸시하려고 하기 때문이죠.
때문에 우리는 모든 픽셀을 먼저 이미지에 버퍼링 후, 이미지를 윈도우에 푸시해야 합니다. 너무 복잡하게 들리겠지만 걱정하지마세요! 그렇게 어렵지 않습니다.
일단 우리는 `mlx`가 어떤 유형의 이미지를 필요로 하는지 알아야 합니다.
이미지를 초기화 하려면 중요한 변수들을 저장하는 몇가지 포인터를 전달해야합니다. 첫 번째 변수는 `bpp`(bits per pixel) 즉, 픽셀당 비트수 입니다. 픽셀은 기본적으로 int로 처리되기 때문에 보통 4바이트입니다. 하지만 remote display에서 8비트 색상만 지원되듯이 이런식으로 small endian방식을 차용하는 경우에는 이 값이 달라질 수 있습니다.

이제 우린 1920×1080의 사이즈를 갖는 이미지를 초기화 할 수 있습니다:

```c
#include <mlx.h>

int	main(void)
{
	void	*img;
	void	*mlx;

	mlx = mlx_init();
	img = mlx_new_image(mlx, 1920, 1080);
}
```

그렇게 어렵지 않았죠? 이제 이미지를 생성했으니, 여기에 어떻게 픽셀을 정확히 쓸수 있을까요? 이를 위해선 우리가 바이트를 변경할 메모리 주소를 얻어와야 합니다. 이 주소를 얻는 방법은 다음과 같습니다:

```c
#include <mlx.h>

typedef struct	s_data {
	void	*img;
	char	*addr;
	int		bits_per_pixel;
	int		line_length;
	int		endian;
}				t_data;

int	main(void)
{
	void	*mlx;
	t_data	img;

	mlx = mlx_init();
	img.img = mlx_new_image(mlx, 1920, 1080);

	/*
	** 이미지 생성 후, mlx_get_data_addr를 호출할 수 있습니다. 이 함수에 bits_per_pixel, line_length, 그리고 endian을 참조로 전달해야 합니다. 그러면 현재 데이터 주소에 맞게 이 값들이 설정됩니다."
	*/
	img.addr = mlx_get_data_addr(img.img, &img.bits_per_pixel, &img.line_length,
								&img.endian);
}
```

우리가 어떻게 `bits_per_pixel`, `line_length`, `endian` 값들을 어떻게 참조로 전달하는지 이해하셨나요? 위에서 설명했듯이 이 값들은 MiniLibX에 의해 적절하게 설정 됩니다.

이제 이미지의 주소는 있지만 픽셀들은 없습니다. 시작하기 전에 우리는 바이트가 정렬되지 않는다는 점을 알아야 합니다.
그 말은`line_length`는 실제 윈도우의 너비와 다를 수 있다는 말입니다.때문에 우리는 항상 `mlx_get_data_addr`에서 설정된 line_length를 이용해 메모리 오프셋을 계산해야 합니다.

우리는 이 식으로 아주 쉽게 계산 가능합니다:

```c
int offset = (y * line_length + x * (bits_per_pixel / 8));
```

이제 우리는 어디에 데이터를 써야하는지 알았으니, mlx_pixel_put이 하는걸 모방하지만 훨씬 몇배나 빠르게 작동하는 함수를 작성하는것이 매우 쉬워집니다.

```c
typedef struct	s_data {
	void	*img;
	char	*addr;
	int		bits_per_pixel;
	int		line_length;
	int		endian;
}				t_data;

void	my_mlx_pixel_put(t_data *data, int x, int y, int color)
{
	char	*dst;

	dst = data->addr + (y * data->line_length + x * (data->bits_per_pixel / 8));
	*(unsigned int*)dst = color;
}
```

이 방식은 이미지가 윈도우에서 실시간으로 표현되기 때문에 동일한 이미지를 변경하면 작성하는 동안 화면 찢김(screen-tearing)현상이 발생 할 수 있다는 점에 유의해야 합니다. 따라서 임시로 프레임을 저장할 두 개 이상의 이미지를 생성하는것이 좋으며 이렇게 하면 현재 표시중인 이미지가 아닌 임시 이미지에 작성 가능합니다.

## 이미지를 윈도우에 출력하기

이제 이미지를 생성할 수 있으니 이를 실제로 윈도우에 출력해 직접 눈으로 볼 수 있게 해야합니다. 간단하게 (5,5)에 위치한 빨간색의 픽셀을 작성해 이를 윈도우에 출력하는 방법을 살펴보겠습니다:

```c
#include <mlx.h>

typedef struct	s_data {
	void	*img;
	char	*addr;
	int		bits_per_pixel;
	int		line_length;
	int		endian;
}				t_data;

int	main(void)
{
	void	*mlx;
	void	*mlx_win;
	t_data	img;

	mlx = mlx_init();
	mlx_win = mlx_new_window(mlx, 1920, 1080, "Hello world!");
	img.img = mlx_new_image(mlx, 1920, 1080);
	img.addr = mlx_get_data_addr(img.img, &img.bits_per_pixel, &img.line_length,
								&img.endian);
	my_mlx_pixel_put(&img, 5, 5, 0x00FF0000);
	mlx_put_image_to_window(mlx, mlx_win, img.img, 0, 0);
	mlx_loop(mlx);
}
```

`0x00FF0000` 는 16진수 표현으로 `ARGB(0,255,0,0)`입니다.

## Test your skills!

Now you that you understand the basics, get comfortable with the library and do
some funky stuff! Here are a few ideas:
- Print squares, circles, triangles and hexagons on the screen by writing the
pixels accordingly.
- Try adding gradients, making rainbows, and get comfortable with using the rgb
colors.
- Try making textures by generating the image in loops.
