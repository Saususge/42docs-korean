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

## Introduction

이제 MiniLibX로 뭘 할지 알았으니, 아주 기초적인 작업들을 해 보겠습니다. 이는 이 라이브러리를 이용해 어떻게 좋은 성능을 내는 코드를 짜는지에 관한 이해를 시켜 줄 겁니다. 많은 프로젝트에서 핵심은 성능이기 때문에 이 페이지를 확실하게 읽고 이해하는 것이 중요합니다.

## Installation

### Compilation on macOS

MiniLibX가 MacOS의 Cocoa(AppKit)와 OpenGL을 요구하기 때문에 이들을 link해야 함으로 컴파일 과정이 복잡할수 있습니다. 기본적인 컴파일 과정은 아래와 같습니다.

프로젝트의 최상위 폴더에 mlx라는 이름의 폴더에서 mlx라는 소스거 있다고 가정할때 오브젝트 파일을 위해 Makefile에 다음과 같은 규칙을 추가할 수 있습니다.

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

### Compilation on Linux

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

### Getting a screen on Windows 10 (WSL2)

Win10 WSL환경에대한 설치는 원문을 참고해주세요

[Getting started](https://harm-smits.github.io/42docs/libs/minilibx/getting_started.html#getting-a-screen-on-windows-10-wsl2)

### Getting a screen on Windows 11 (WSLg)

Win11에서 WSL 설치시 그래픽 application 실행을 위한 WSLg가 자동 설치되기 때문에 별도의 설치가 필요없습니다만 만약 실행이 되지 않는다면 
[Linux용 Windows 하위 시스템 Linux GUI 앱 실행](https://learn.microsoft.com/ko-kr/windows/wsl/tutorials/gui-apps)을 참고하세요.

## Initialization

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

## Writing pixels to a image

이제 기본적인 윈도우 관리를 할수있으니, 윈도우에 픽셀을 그릴 수 있습니다.
픽셀을 어떻게 놓을지는 선택에 따라 다르지만, 여기서는 최적화된 방법에 대해서도 다루겠습니다.
일단 `mlx_pixel_put`함수가 매우 느리다는것을 염두해 두세요. 이는 픽셀을 frame이 완전히 렌더링되기를 기다리지 않고 바로 그리려고 하기 때문입니다. 즉, 픽셀을 즉시 윈도우에 푸시하려고 하기 때문이죠.
때문에 우리는 모든 픽셀을 먼저 이미지에 버퍼링 후, 이미지를 윈도우에 푸시해야 합니다. 너무 복잡하게 들리겠지만 걱정하지마세요! 그렇게 어렵지 않습니다.
First of all, we should start by understanding what type of image `mlx`
requires. If we initiate an image, we will have to pass a few pointers to which
it will write a few important variables. The first one is the `bpp`, also called
the bits per pixel. As the pixels are basically ints, these usually are 4 bytes,
however, this can differ if we are dealing with a small endian (which means we
most likely are on a remote display and only have 8 bit colors).

Now we can initialize the image with size 1920×1080 as follows:

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

That wasn't too bad, was it? Now, we have an image but how exactly do we write
pixels to this? For this we need to get the memory address on which we will
mutate the bytes accordingly. We retrieve this address as follows:

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
	** After creating an image, we can call `mlx_get_data_addr`, we pass
	** `bits_per_pixel`, `line_length`, and `endian` by reference. These will
	** then be set accordingly for the *current* data address.
	*/
	img.addr = mlx_get_data_addr(img.img, &img.bits_per_pixel, &img.line_length,
								&img.endian);
}
```

Notice how we pass the `bits_per_pixel`, `line_length` and `endian` variables
by reference? These will be set accordingly by MiniLibX as per described above.

Now we have the image address, but still no pixels. Before we start with this,
we must understand that the bytes are not aligned, this means that the
`line_length` differs from the actual window width. We therefore should ALWAYS
calculate the memory offset using the line length set by `mlx_get_data_addr`.

We can calculate it very easily by using the following formula:

```c
int offset = (y * line_length + x * (bits_per_pixel / 8));
```

Now that we know where to write, it becomes very easy to write a function that
will mimic the behaviour of `mlx_pixel_put` but will simply be many times
faster:

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

Note that this will cause an issue. Because an image is represented in real time
in a window, changing the same image will cause a bunch of screen-tearing when
writing to it. You should therefore create two or more images to hold your
frames temporarily. You can then write to a temporary image, so that you don't
have to write to the currently presented image.

## Pushing images to a window

Now that we can finally create our image, we should also push it to the window,
so that we can actually see it. This is pretty straight forward, let's take a
look at how we can write a red pixel at (5,5) and put it to our window:

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

Note that `0x00FF0000` is the hex representation of `ARGB(0,255,0,0)`.

## Test your skills!

Now you that you understand the basics, get comfortable with the library and do
some funky stuff! Here are a few ideas:
- Print squares, circles, triangles and hexagons on the screen by writing the
pixels accordingly.
- Try adding gradients, making rainbows, and get comfortable with using the rgb
colors.
- Try making textures by generating the image in loops.
