---
layout: default
title: Loops
parent: MiniLibX
grand_parent: Libs
nav_order: 6
---

# Loops
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 소개

이제 MiniLibX 라이브러리의 기본을 이해했으므로, 새로운 함수인 `mlx_loop`와 `mlx_loop_hook`을 이용해 윈도우에 작은 애니메이션을 그려보겠습니다.
MiniLibX의 기능 중 하나인 루프(Loops)는 mlx_loop_hook에 등록된 후크를 계속 호출하여, 새로운 프레임을 창에 전달하는 작업을 반복적으로 수행합니다.

## 루프에 후킹하기

루프를 시작하기 위해서는 mlx 인스턴스를 유일한 매개변수로 하여 mlx_loop 함수를 호출해야 합니다:

```c
#include <mlx.h>

int	main(void)
{
	void	*mlx;

	mlx = mlx_init();
	mlx_loop(mlx);
}
```

당연히 위 함수는 루프후크가 등록되지 않았고 프레임에 아무거도 쓸수없기 때문에 아무 작업도 하지 않습니다.

이를 위해서는[Getting Started](./getting_started.html)에서 설명한 새로운 창 만들기의 변형을 사용해야 합니다. 여기서는 그 부분에 대한 이해가 충분히 되어있고 매개변수를 적절히 전달 가능하다고 가정합니다.

다음과 같은 예시는 화이트보드에 그린 버전일 수 있습니다:

```c
#include <mlx.h>

int	render_next_frame(void *YourStruct);

int	main(void)
{
	void	*mlx;

	mlx = mlx_init();
	mlx_loop_hook(mlx, render_next_frame, YourStruct);
	mlx_loop(mlx);
}
```

이제 필요한 만큼 `YourStruct`매개변수를 이용해 `render_next_frame`함수를 각 프레임에 필요한 만큼 호출하세요. 만약 그게 포인터라면 여러번 호출될 경우에도 값이 유지되기 때문에 이 성질을 유용하게 활용할 수 있습니다.

## Test your skills!

Now that you understand how to register your own rendering function, we suggest
that you create the following renderers:
- Render a moving rainbow that shifts through all colors (screen turns red,
becomes green and then becomes blue, then starts all over again).
- Create a circle that you can move accross your screen using your WASD keys.
