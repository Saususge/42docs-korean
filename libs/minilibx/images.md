---
layout: default
title: Images
parent: MiniLibX
grand_parent: Libs
nav_order: 7
---

# Images
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 소개

이미지는 MiniLibX에서 그 잠재력을 최대로 끌어올려 주게 해주는 매우 중요한 도구입니다. 이 함수들은 파일을 이미지 객체로 직접 읽을 수 있게 해줍니다. 이는 텍스처나 스프라이트에 매우 유용합니다.

## 이미지 렌더링

이미지를 파일에서 읽어오려면 XPM 또는 PNG 형식이 필요합니다.
이미지를 읽으려면 `mlx_xpm_file_to_image` 이나 `mlx_png_file_to_image` 함수를 호출하면 됩니다. 단, `mlx_png_file_to_image`는 현재 메모리 누수가 발생할 수 있다는 점을 유의해야 합니다. 두 함수는 동일한 매개변수를 받으며, 사용법도 동일합니다.

자 이제 이미지를 읽어봅시다.

```c
#include <mlx.h>

int	main(void)
{
	void	*mlx;
	void	*img;
	char	*relative_path = "./test.xpm";
	int		img_width;
	int		img_height;

	mlx = mlx_init();
	img = mlx_xpm_file_to_image(mlx, relative_path, &img_width, &img_height);
}
```

만약 `img`변수가 `NULL`이라면 이미지 읽기에 실패했다는 뜻입니다.또한 이 함수는 `img_width`와 `img_height`를 적절하게 설정해주며 이는 스프라이트를 읽을 때 매우 유용합니다.

## Test your skills!

Now that you know how to read from files, lets get started on making more
graphical stuff ;). Create the following programs:
- Import a cursor of your choice and allow it to roam within the window freely.
- Import a texture and replicate it accross your entire window.
