---
title: STM32 UART Driver in C
date: 2026-04-19
tags:
  - embedded
  - c
  - stm32
  - uart
description: Bare-metal UART driver for STM32F4
---

## Overview

A minimal UART driver written in C for STM32F4 microcontroller without HAL.

## Implementation

```c
#define USART1_BASE 0x40011000
#define SR (*(volatile uint32_t *)(USART1_BASE + 0x00))
#define DR (*(volatile uint32_t *)(USART1_BASE + 0x04))
#define BRR (*(volatile uint32_t *)(USART1_BASE + 0x08))
#define CR1 (*(volatile uint32_t *)(USART1_BASE + 0x0C))

void uart_init(uint32_t baud) {
    BRR = 84000000 / baud;
    CR1 = 0x0000200C;
}

void uart_putchar(char c) {
    while (!(SR & 0x80));
    DR = c;
}
```

## Results

Works at 115200 baud. Tested on STM32F407VG.

## References

- STM32F4 Reference Manual
- ARM Cortex-M4 Technical Reference Manual
