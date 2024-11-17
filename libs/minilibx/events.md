---
layout: default
title: Events
parent: MiniLibX
grand_parent: Libs
nav_order: 4
---

# Events
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 소개

이벤트는 MiniLibX에서 인터랙티브 애플리케이션을 작성하는 데 중요한 기반입니다. 따라서 이 장을 완전히 이해하는 것이 매우 중요하며, 이는 향후 그래픽 프로젝트에서 유용하게 사용될 것입니다.

MiniLibX의 모든 hook는 이벤트가 발생할 때마다 호출되는 함수일 뿐입니다. 모든 이벤트를 마스터할 필요는 없지만, 각 X11 이벤트에 대해 간략히 살펴보겠습니다.



### MacOS version

참고: MacOS 버전에서는 Cocoa (AppKit)과 OpenGL이 사용되며, MiniLibX는 X11 이벤트에 대한 부분적인 지원만 제공하고, X11 마스크(x_mask 인자, mlx_hook에서 사용됨)는 지원하지 않으므로, 이 값을 0으로 설정해 두세요.

지원되는 이벤트들:
  
```c
enum {
	ON_KEYDOWN = 2,
	ON_KEYUP = 3,
	ON_MOUSEDOWN = 4,
	ON_MOUSEUP = 5,
	ON_MOUSEMOVE = 6,
	ON_EXPOSE = 12,
	ON_DESTROY = 17
};

// usage:
mlx_hook(vars.win, ON_DESTROY, 0, close, &vars);
```

## X11 interface

X11은 MiniLibX와 함께 사용되는 라이브러리입니다. 때문에 X11헤더를 이용하면 MiniLibX의 모든 관련 이벤트를 찾을 수 있습니다.

### X11 events

There are a number of events to which you may describe.

| Key  | Event         | | Key  | Event            | | Key  | Event            |
| :--: | ------------- |-| :--: | ---------------- |-| :--: | ---------------- |
| `02` | KeyPress      | | `14` | NoExpose         | | `26` | CirculateNotify  | 
| `03` | KeyRelease    | | `15` | VisibilityNotify | | `27` | CirculateRequest | 
| `04` | ButtonPress   | | `16` | CreateNotify     | | `28` | PropertyNotify   |
| `05` | ButtonRelease | | `17` | DestroyNotify    | | `29` | SelectionClear   |
| `06` | MotionNotify  | | `18` | UnmapNotify      | | `30` | SelectionRequest |
| `07` | EnterNotify   | | `19` | MapNotify        | | `31` | SelectionNotify  |
| `08` | LeaveNotify   | | `20` | MapRequest       | | `32` | ColormapNotify   |
| `09` | FocusIn       | | `21` | ReparentNotify   | | `33` | ClientMessage    |
| `10` | FocusOut      | | `22` | ConfigureNotify  | | `34` | MappingNotify    |
| `11` | KeymapNotify  | | `23` | ConfigureRequest | | `35` | GenericEvent     |
| `12` | Expose        | | `24` | GravityNotify    | | `36` | LASTEvent        |
| `13` | GraphicsExpose| | `25` | ResizeRequest    | |      |                  |


만약 이벤트 몇개가 이해가 안되더라도 걱정마세요. 아마 쓸일이 없을겁니다. 만약 이벤트에 대해 더 알고 싶다면 아래 링크를 참조하세요. [the documentation of each X11 events](https://tronche.com/gui/x/xlib/events/).

### X11 masks

각 X11 이벤트는 해당하는 마스크(mask)를 가지고 있습니다. 이를 통해 특정 키가 트리거될 때 한 키만 등록하거나, 기본값으로 두면 모든 키에 등록할 수 있습니다. 키 마스크는 이벤트 구독에서 이벤트를 화이트리스트 또는 블랙리스트할 수 있게 해줍니다. 허용되는 마스크는 다음과 같습니다:

| Mask       | Description      | | Mask       | Description          |
| :--------: | ---------------- |-| :--------: | -------------------- |
| `0L`       | NoEventMask      | | `(1L<<12)` | Button5MotionMask    |
| `(1L<<0)`  | KeyPressMask     | | `(1L<<13)` | ButtonMotionMask     |
| `(1L<<1)`  | KeyReleaseMask   | | `(1L<<14)` | KeymapStateMask      |
| `(1L<<2)`  | ButtonPressMask  | | `(1L<<15)` | ExposureMask         |
| `(1L<<3)`  | ButtonReleaseMask| | `(1L<<16)` | VisibilityChangeMask |
| `(1L<<4)`  | EnterWindowMask  | | `(1L<<17)` | StructureNotifyMask  |
| `(1L<<5)`  | LeaveWindowMask  | | `(1L<<18)` | ResizeRedirectMask   |
| `(1L<<6)`  | PointerMotionMask| | `(1L<<19)` | SubstructureNotifyMask  |
|`(1L<<7)`|PointerMotionHintMask| | `(1L<<20)` | SubstructureRedirectMask|
| `(1L<<8)`  | Button1MotionMask| | `(1L<<21)` | FocusChangeMask      |
| `(1L<<9)`  | Button2MotionMask| | `(1L<<22)` | PropertyChangeMask   |
| `(1L<<10)` | Button3MotionMask| | `(1L<<23)` | ColormapChangeMask   |
| `(1L<<11)` | Button4MotionMask| | `(1L<<24)` | OwnerGrabButtonMask  |

## 이벤트 후킹하기

### mlx_hook

이벤트를 후킹하는건 MiniLibX에서 제공하는 가장 강력한 도구 중 하나입니다. 이를 통해 간단한 후킹 등록 함수 호출로 앞서 언급된 모든 이벤트에 등록 가능합니다.
```c
void mlx_hook(mlx_win_list_t *win_ptr, int x_event, int x_mask, int (*f)(), void *param)
```

*일부 버전의 MiniLibX는 `x_mask`를 구현하지 않으며, 그 값이 무엇이든 상관없이 마스크는 적용되지 않습니다.*

### 이벤트 함수의 프로토 타입

이벤트 함수는 후킹 이벤트에 따라 다른 프로토타입을 가집니다.

| Hooking event | code | Prototype            |
| ------------- | :--: | -------------------- |
| ON_KEYDOWN    |   2  | `int (*f)(int keycode, void *param)`   |
| ON_KEYUP*     |   3  | `int (*f)(int keycode, void *param)` |
| ON_MOUSEDOWN* |   4  | `int (*f)(int button, int x, int y, void *param)` |
| ON_MOUSEUP    |   5  | `int (*f)(int button, int x, int y, void *param)` |
| ON_MOUSEMOVE  |   6  | `int (*f)(int x, int y, void *param)` |
| ON_EXPOSE*    |  12  | `int (*f)(void *param)` |
| ON_DESTROY    |  17  | `int (*f)(void *param)` |

*\*Has mlx_hook alias.*

### Hooking 별칭(alias)

Minilibx api has some alias hooking function:

 - `mlx_expose_hook`함수는 expose 이벤트(`12`)에 관한 `mlx_hook`의 별칭입니다.
 - `mlx_key_hook`  함수는 키 업 이벤트(`3`)에 대한 `mlx_hook`의 별칭입니다.
 - `mlx_mouse_hook` 함수는 마우스 다운 이벤트(`4`)에 대한 `mlx_hook`의 별칭입니다.

### 예제

예를 들어, `mlx_key_hook`를 호출하는 대신, `KeyPress`와 `KeyRelease` 이벤트에 등록할 수도 있습니다. 

```c
#include <mlx.h>

typedef struct	s_vars {
	void	*mlx;
	void	*win;
}				t_vars;

int	close(int keycode, t_vars *vars)
{
	mlx_destroy_window(vars->mlx, vars->win);
	return (0);
}

int	main(void)
{
	t_vars	vars;

	vars.mlx = mlx_init();
	vars.win = mlx_new_window(vars.mlx, 1920, 1080, "Hello world!");
	mlx_hook(vars.win, 2, 1L<<0, close, &vars);
	mlx_loop(vars.mlx);
}
```

여기서는 `KeyPress` 이벤트에 해당하는 `KeyPressMask`로 등록합니다. 이제 키를 누를 때마다 윈도우가 닫히게 됩니다.

## Test your skills!

Now that you have a faint idea of how all of this works, we encourage you to
make the hook handlers. Whenever the:
- <kbd>ESC</kbd> key is pressed, your window should close.
- window is resized, you should print something in your terminal.
- the red cross is clicked, your window should close.
- you press a key longer than x seconds, you should print something in your
terminal.
- mouse enters the window, you should print `Hello!` in your terminal, when it
leaves, you should print `Bye!`.
