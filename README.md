# ee186_lab2
Explain how external interrupts work.

An external interrupt lets the microcontroller respond to events like the button press without needing to continuously check the button in the main loop. When the configured ISR edge occurs, EXTI sets it pending flag and sends an interrupt request to the NVIC. If the interrupt is high, then the processor breaks out of main and runs the interrupt handler. If the EXTI pending flag was not cleared, then the interrupt request would stay high, so the processor would be able to repeatedly execute the ISR without another button press.  

Explain how timer interrupts work.

Timer interrupts are clock pulses in hardware. The system clock has a prescaler that divides the incoming clock to determine how fast the counter increments, without any CPU involvement. When the counter reaches its reload value (ARR), its next tick will reset the counter and update the event, setting the update interrupt flag (UIF). If the update interrupt is enabled in the timer (UIE) and the IRQ is enabled in the NVIC, that flag becomes an interrupt request, and the CPU pauses main, jumps to the ISR through the vector table, runs it, and then resumes where it left off. The ISR must clear UIF, or the request stays asserted and the ISR re-fires forever. 

Calculate the timer frequency.

To calculate the timer frequency, I refence the formula presented in class: Time = (PSC + 1) / f_timer x (ARR + 1). We want the time to be = 1ms. The clock reaches TIM4 through two prescalers in RCC_CFGR: 
SYSCLK -> AHB prescaler (HPRE) -> APB1 prescaler (PPRE1) -> TIM4 
Both fields reset to 0, which the value lists define as “not divided” (/1). So 16 MHz / 1 / 1 = 16 MHz at TIM4, so f_timer = 16,000,000 Hz. 
(PSC + 1) x (ARR + 1) = f_timer × Time = 16,000,000 x 1 = 16,000,000 
But one register can’t hold 16M. Both are 16-bit (max 65535), so I split the number between the two. I chose a 1 kHz counter, so PSC + 1 = 16,000,000 / 1,000 = 16,000, giving PSC = 15,999. ARR counts at that rate. At 1 kHz, 1 second is 1000 ticks, so ARR + 1 = 1000, giving ARR = 999. 
