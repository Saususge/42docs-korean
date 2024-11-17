---
layout: default
title: Sync
parent: MiniLibX
grand_parent: Libs
nav_order: 8
---

# Sync
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 동기화(sync)란?

앞서 말했듯, MLX를 이용해 프레임 버퍼링을 자체적으로 관리 할 수 있지만, 이는 번거롭고 시간도 많이 듭니다. 심지어 메모리도 더 많이 사용하며 프레임도 계속해서 완전히 업데이트 되어야 하기때문에 매우 비효율적입니다. 이러한 상황은 가능한 피하는게 좋습니다.

2020년 MLX 버전부터는 자체 프레임을 동기화할 수 있어, 화면 버퍼링을 위한 여러 이미지 사용이 필요하지 않습니다.

## 동기화 하기(Using sync)

우리는 먼저 이해해야 할 세 가지 define 상수가 있습니다:

```c
#define MLX_SYNC_IMAGE_WRITABLE		1
#define MLX_SYNC_WIN_FLUSH_CMD		2
#define MLX_SYNC_WIN_CMD_COMPLETED	3

int	mlx_sync(int cmd, void *ptr);
```

`mlx_sync`는 정의된 명령 코드와 함께 호출되어야 합니다. 첫째로
`MLX_SYNC_IMAGE_WRITABLE`은 이미지에 대한 모든 후속 호출을 버퍼링합니다 (`ptr`은 MLX 이미지 객체를 가리키는 포인터입니다).변경사항을 반영하려면 `MLX_SYNC_WIN_FLUSH_CMD`와 함께 flush하려는 윈도우를 `ptr`로 전달하여 해당 이미지가 있는 윈도우를 flush해야 합니다.

## Test your skills!

Now that you have a faint idea of what `mlx_sync` can do, grab your previous
tiny circle-game that you made in [loops](./loops.html) and add `mlx_sync` to
your rendering!
