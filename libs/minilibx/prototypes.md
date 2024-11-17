---
layout: default
title: Prototypes
parent: MiniLibX
grand_parent: Libs
nav_order: 10
---

# Prototypes
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 소개

MiniLibX는 그래픽스를 렌더링하는 데 필요한 가장 기본적인 기능들을 제공하는 작은 라이브러리입니다. 이 라이브러리를 사용하면 울펜슈타인의 복사본을 만들거나 복잡한 데이터를 간단한 형태로 표현하는 등의 작업을 할 수 있습니다.

비트 연산자(bitwise operators)에 대한 지식이 없다면 이해하고 오시는걸 추천합니다.

## 함수들 초기화하기

이것들은 MiniLibX를 사용하려고 시작하기 위해 거의 항상 필요한 표준 함수들입니다

### mlx_init

MLX 라이브러리를 초기화 하며 어떤 함수의 호출보다 선행되어야 합니다.
만약 초기화 실패시 `NULL`을 반환합니다.

```c
/*
** mlx 초기화
**
** @return	void*	mlx 인스턴스
*/
void	*mlx_init();
```

### mlx_new_window

새로운 윈도우 인스턴스를 생성합니다. 이 함수는 윈도우 인스턴스의 포인터를 반환합니다. 이 포인터는 이후에 참조가능하게 저장해야 합니다.

```c
/*
** 새로운 윈도우 생성
**
** @param	void *mlx_ptr	mlx의 인스턴스 포인터
** @param	int  size_x		윈도우의 너비
** @param	int  size_y		윈도우의 높이
** @param	char *title		윈도우의 이름
** @return	void*			윈도우의 인스턴스 포인터
*/
void	*mlx_new_window(void *mlx_ptr, int size_x, int size_y, char *title);
```

### mlx_clear_window

현재 윈도우를 지웁니다. 이 함수는 권장되지 않는 함수입니다. 차라리 지운 이미지를 재활용하여 `mlx_put_image_to_window` 함수를 사용하는 것이 권장됩니다.

```c
/*
** 주어진 윈도우 삭제
**
** @param	void *mlx_ptr	mlx의 인스턴스 포인터
** @param	void *win_ptr	윈도우의 인스턴스 포인터
** @return	int				반환값이 필요없이때문에 리턴값이 없음
*/
int		mlx_clear_window(void *mlx_ptr, void *win_ptr);
```

### mlx_destroy_window

윈도우 인스턴스를 destroy합니다

```c
/*
** 윈도우 인스턴스 destroy
**
** @param	void *mlx_ptr	mlx의 인스턴스 포인터
** @param	void *win_ptr	윈도우의 인스턴스 포인터
** @return	int				반환값이 필요없이때문에 리턴값이 없음
*/
int		mlx_destroy_window(void *mlx_ptr, void *win_ptr);
```

## 유틸 함수

이 함수들은 형변환 및 픽셀 쓰기 작업에 도움이 됩니다

### mlx_get_color_value

int에서 색상 값을 가져옵니다. 이는 특정 bit에 값을 쓸 때, 미리 선언한 int를 변환하는 데 유용합니다

```c
/*
** 색상 값 가져오기
**
** @param	void *mlx_ptr	mlx의 인스턴스 포인터
** @param	int  color		int 색상값 (0xTTRRGGBB 형태의 정수)
** @return	uint			변환된 색상 값
*/
uint	mlx_get_color_value(void *mlx_ptr, int color);
```

### mlx_pixel_put

화면에 픽셀을 출력합니다. 이 함수는 사용을 권장하지 않습니다. 
이 함수는 윈도우 출력을 lock을 한 후에 강제로 새로 고침과 재계산을 수행합니다. 
따라서 이미지를 렌더링한 후 `mlx_put_image_to_window` 함수를 사용하여 그 이미지를 창에 출력하는 방식이 권장됩니다.
이 부분에 대한 더 자세한 내용은 [Getting Started](./getting_started.html)에서 확인할 수 있습니다.

```c
/*
** 화면에 픽셀 찍기
**
** @param	void *mlx_ptr	mlx 인스턴스 포인터
** @param	void *win_ptr	윈도우 인스턴스 포인터
** @param	int  x			그릴 픽셀의 x 좌표
** @param	int  y			그릴 픽셀의 y 좌표
** @param	int  color		그릴 픽셀의 색상 (0xTTRRGGBB)
** @return	int				반환값이 필요없이때문에 리턴값이 없음
*/
int		mlx_pixel_put(void *mlx_ptr, void *win_ptr, int x, int y, int color);
```

### mlx_string_put

주어진 윈도우의 (x, y)좌표에 문자열을 출력합니다
```c
/*
** 화면에 string 출력
**
** @param	void *mlx_ptr	mlx 인스턴스 포인터
** @param	int  x			x 좌표
** @param	int  y			y 좌표
** @param	int  color		폰트 색깔
** @param	char *string	작성할 string
** @return	int				반환값이 필요없이때문에 리턴값이 없음

*/
int		mlx_string_put(void *mlx_ptr, void *win_ptr, int x, int y, int color, char *string);
```

## 이미지 함수

이미지 객체 관련 함수들입니다. 이 함수들은 프레임을 하나씩 변형하는 효과적인 방법을 제공합니다.

### mlx_new_image

MLX와 호환되는 새로운 이미지를 생성합니다. 이는 렌더링하려는 이미지를 버퍼링하는데 권장되는 방법입니다. MLX 인스턴스를 가리키는 포인터와 함께 너비와 높이 값을 인자로 받으며 이미지에 대한 참조 포인터를 반환합니다

```c
/*
** MLX 호환 이미지 생성
**
** @param	void *mlx_ptr	mlx 인스턴스 포인터
** @param	int  width		만들어질 이미지의 너비
** @param	int  height		만들어질 이미지의 높이
** @return	void*			이미지 참조 포인터
*/
void	*mlx_new_image(void *mlx_ptr,int width,int height);
```

### mlx_get_data_addr

주어진 이미지의 메모리 주소를 가져옵니다. 이미지의 메모리는 특이한 구조를 가지고 있습니다. 라인 크기를 주어진 포인터에 설정합니다. 
만약 (500, 500) 크기의 이미지에서 픽셀 (5, 100)의 값을 가져오거나 설정하려면 다음과 같이 위치를 찾아야 합니다:

```c
int pos = (y * size_line + x * (bits_per_pixel / 8));
```

여기서 `y`에 `size_line`을 곱하는 이유는 `y`개의 라인을 건너뛰어야 하기 때문입니다 (참고로 라인 크기는 라인의 픽셀 수와 동일하지 않습니다). 그런 다음 최종 위치에 맞추기 위해 남은 `x` 단위에`bits_per_pixel / 8`을 곱한 값을 더합니다.

각 픽셀을 올바른 색상으로 수정하려면 추가적인 작업이 필요합니다. 쓰기 전에 비트를 정렬해야 하므로 최상의 결과를 뽑아내려면 다음과 같이 해야 합니다:

```c
char *mlx_data_addr = mlx_get_data_addr();
*(unsigned int *)mlx_data_addr = color;
```

The function prototype is as follows:

```c
/*
** 현재 이미지의 데이터 주소 가져오기
**
** @param   void *img_ptr           이미지 인스턴스
** @param   int  *bits_per_pixel    bpp가 기록될 포인터
** @param   int  *size_line         라인이 기록될 포인터
** @param   int  *endian           엔디안이 기록될 포인터
** @return  char*                   이미지의 메모리 주소
*/
char	*mlx_get_data_addr(void *img_ptr, int *bits_per_pixel, int *size_line, int *endian);
```

### mlx_put_image_to_window

주어진 윈도우 인스턴스의 좌표(x,y)에 이미지를 출력합니다. 이는 한 번에 많은 양의 그래픽 데이터를 쓰는 데 권장되는 방법입니다. 위치의 메모리를 변경할 때 윈도우에 직접 표시된다는 점에 유의하세요.

```c
/*
** 주어진 윈도우에 이미지를 출력
**
** @param   void *mlx_ptr    MLX 인스턴스
** @param   void *win_ptr    윈도우 인스턴스
** @param   int  x           이미지가 위치할 x 좌표
** @param   int  y           이미지가 위치할 y 좌표
** @return  int              반환값 없음
*/
int		mlx_put_image_to_window(void *mlx_ptr, void *win_ptr, void *img_ptr, int x, int y);
```

### mlx_destroy_image

이미지 인스턴스를 적절히 destroy합니다.

```c
/*
** 이미지 인스턴스 제거
**
** @param   void *mlx_ptr    MLX 인스턴스
** @param   void *img_ptr    이미지 인스턴스
** @return  int              반환값 없음
*/
int		mlx_destroy_image(void *mlx_ptr, void *img_ptr);
```

## 후크(Hooks)

이 함수들을 통해 MiniLibX 함수들에 후크를 설정할 수 있습니다. 이 함수들은 MiniLibX의 핵심 기능이며 라이브러리를 효과적으로 사용하기 위해 필요합니다. 이것이 무엇을 의미하는지 모르시겠다면 [Hooks](./hooks.html)챕터를 확인해보세요.

### mlx_mouse_hook

마우스 이벤트에 후크를 설정합니다. 주어진 화면에서 클릭할 때마다 트리거됩니다. 현재 이 마우스 이벤트들이 제대로 작동하지 않으므로 사용하지 않는 것이 좋습니다.
```c
/*
** 마우스 이벤트에 후크를 설정
**
** @param   void *win_ptr    윈도우 인스턴스
** @param   int  (*f)()      핸들러 함수, 다음과 같이 호출됨:
**                          (*f)(int button, int x, int y, void *param)
** @param   void *param      각 이벤트마다 전달될 매개변수
** @return  int             반환값 없음
*/
int		mlx_mouse_hook(void *win_ptr, int (*f)(), void *param);
```

### mlx_key_hook

키 이벤트에 후크를 설정합니다. 포커스된 윈도우에서 키가 눌릴 때마다 트리거됩니다. 포커스되지 않은 윈도우는 키 이벤트를 등록하지 않습니다.

```c
/*
** 키 이벤트에 후크를 설정
**
** @param   void *win_ptr    윈도우 인스턴스
** @param   int  (*f)()      핸들러 함수, 다음과 같이 호출됨:
**                          (*f)(int key_code, void *param)
** @param   void *param      각 이벤트마다 전달될 매개변수
** @return  int             반환값 없음
*/
int		mlx_key_hook(void *win_ptr, int (*f)(), void *param);
```

### mlx_expose_hook

정의된 동작이 없습니다.

### mlx_loop_hook

루프에 후크를 설정합니다.

```c
/*
** 각 루프에 후크를 설정
**
** @param   void *mlx_ptr    MLX 인스턴스
** @param   int  (*f)()      핸들러 함수, 다음과 같이 호출됨:
**                          (*f)(void *param)
** @param   void *param      각 이벤트마다 전달될 매개변수
** @return  int             반환값 없음
*/
int		mlx_loop_hook(void *mlx_ptr, int (*f)(), void *param);
```

### mlx_loop

주어진 MLX 포인터로 루프를 실행합니다. 이전에 등록된 모든 후크는 등록한 순서대로 호출됩니다.

```c
/*
** 주어진 MLX 포인터로 루프 실행
**
** @param   void *mlx_ptr    MLX 인스턴스
** @return  int              반환값 없음
*/
int		mlx_loop(void *mlx_ptr);
```

## 이미지 변환

이 함수들은 스프라이트를 로드하거나 이미지를 저장하는 데 유용한 함수들입니다.

### mlx_xpm_to_image

XPM 데이터를 새로운 이미지 인스턴스로 변환합니다.

```c
/*
** XPM 데이터를 새로운 이미지 인스턴스로 변환
**
** @param   void *mlx_ptr    MLX 인스턴스
** @param   char **xpm_data  2차원 char 배열의 XPM 데이터
** @param   int  *width      너비가 기록될 포인터
** @param   int  *height     높이가 기록될 포인터
** @return  void*            이미지 인스턴스, 오류 시 NULL반환
*/
void	*mlx_xpm_to_image(void *mlx_ptr, char **xpm_data, int *width, int *height);
```

### mlx_xpm_file_to_image

XPM 파일을 새로운 이미지 인스턴스로 변환합니다.

```c
/*
** XPM 파일을 새로운 이미지 인스턴스로 변환
**
** @param   void *mlx_ptr    MLX 인스턴스
** @param   char *filename   로드할 파일
** @param   int  *width      너비가 기록될 포인터
** @param   int  *height     높이가 기록될 포인터
** @return  void*           이미지 인스턴스, 오류 시 NULL반환
*/
void	*mlx_xpm_file_to_image(void *mlx_ptr, char *filename, int *width, int *height);
```

### mlx_png_file_to_image

PNG 파일을 새로운 이미지 인스턴스로 변환합니다.

```c
/*
** PNG 파일을 새로운 이미지 인스턴스로 변환
**
** @param   void *mlx_ptr    MLX 인스턴스
** @param   char *filename   로드할 파일
** @param   int  *width      너비가 기록될 포인터
** @param   int  *height     높이가 기록될 포인터
** @warn    mem_leak        이 함수는 메모리 누수가 있으므로 대신 XPM 사용을 권장
** @return  void*           이미지 인스턴스
*/
void	*mlx_png_file_to_image(void *mlx_ptr, char *filename, int *width, int *height);
```

## Mouse functions

이 함수들을 사용하여 마우스를 숨기거나, 보이게 하거나, 이동시키거나, 위치를 가져올 수 있습니다.

### mlx_mouse_hide

마우스를 숨깁니다.

```c
/*
** 마우스 숨김
**
** @return  int     반환값 없음
*/
int		mlx_mouse_hide();
```

### mlx_mouse_show

마우스를 보이게 합니다

```c
/*
** 마우스 보이게하기
**
** @return	int		반환값 없음
*/
int		mlx_mouse_show();
```

### mlx_mouse_move

커서를 주어진 위치로 이동합니다.

```c
/*
** 커서를 주어진 위치로 이동
**
** @param   void *win_ptr    윈도우 인스턴스
** @param   int  x           이동할 x 위치
** @param   int  y           이동할 y 위치
** @return  int              반환값 없음
*/
int		mlx_mouse_move(void *win_ptr, int x, int y);
```

### mlx_mouse_get_pos

윈도우에서 현재 마우스 위치를 가져옵니다.

```c
/*
** 윈도우에서 현재 마우스 위치를 가져옴
**
** @param   void *win_ptr    윈도우 인스턴스
** @param   int  *x          x 위치를 기록할 포인터
** @param   int  *y          y 위치를 기록할 포인터
** @return  int              반환값 없음
*/
int		mlx_mouse_get_pos(void *win_ptr, int *x, int *y);
```

## 키 자동 반복

이 함수들을 사용하여 키 자동 반복을 활성화하거나 비활성화할 수 있습니다.

### mlx_do_key_autorepeatoff

키 자동 반복을 비활성화합니다.

```c
/*
** 키 자동 반복을 비활성화
**
** @param   void *mlx_ptr    MLX 인스턴스
** @return  int              반환값 없음
*/
int		mlx_do_key_autorepeatoff(void *mlx_ptr);
```

### mlx_do_key_autorepeaton

키 자동 반복을 활성화합니다.

```c
/*
** 키 자동 반복을 활성화
**
** @param   void *mlx_ptr    MLX 인스턴스
** @return  int              반환값 없음
*/
int		mlx_do_key_autorepeaton(void *mlx_ptr);
```

## 미분류 함수들

### mlx_do_sync

```c
/*
** MLX의 모든 윈도우 프레임을 동기화
**
** @param   void *mlx_ptr    MLX 인스턴스
** @return  int             반환값 없음
*/
int		mlx_do_sync(void *mlx_ptr);
```

### mlx_get_screen_size

```c
/*
** 현재 화면 크기를 가져옴 (macOS의 한계 때문)
**
** @param   void *mlx_ptr    MLX 인스턴스
** @param   int  *sizex      화면 너비
** @param   int  *sizey      화면 높이
** @return  int              반환값 없음
*/
int		mlx_get_screen_size(void *mlx_ptr, int *sizex, int *sizey);
```
