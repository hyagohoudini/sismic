;-------------------------------------------------------------------------------
; MSP430 Assembler Code Template for use with TI Code Composer Studio
;
;
;-------------------------------------------------------------------------------
            .cdecls C,LIST,"msp430.h"       ; Include device header file
            
;-------------------------------------------------------------------------------
            .def    RESET                   ; Export program entry-point to
                                            ; make it known to linker.
;-------------------------------------------------------------------------------
            .text                           ; Assemble into program memory.
            .retain                         ; Override ELF conditional linking
                                            ; and retain current section.
            .retainrefs                     ; And retain any sections that have
                                            ; references to current section.

;-------------------------------------------------------------------------------
RESET       mov.w   #__STACK_END,SP         ; Initialize stackpointer  (0x4400)
StopWDT     mov.w   #WDTPW|WDTHOLD,&WDTCTL  ; Stop watchdog timer


;-------------------------------------------------------------------------------
; Main loop here
;-------------------------------------------------------------------------------
EX15:		CALL	#CONFIG_PX
LOOP:
DESLIGA:	BIC.B	#BIT0, &P1OUT
			BIC.B	#BIT7, &P4OUT
TESTA_RED:	BIT.B	#BIT1, &P2IN
			JNZ		TESTA_GREEN
TURN_RED:	BIS.B	#BIT0, &P1OUT
TESTA_GREEN:BIT.B	#BIT1, &P1IN
			JNZ		CONTINUE
TURN_GREEN:	BIS.B	#BIT7, &P4OUT
CONTINUE:	JMP		LOOP

CONFIG_PX:	BIS.B	#BIT0, &P1DIR	; LED vermelho como saída
			BIS.B	#BIT7, &P4DIR	; LED verde como saída
			;
			BIC.B	#BIT1, &P2DIR	;
			BIS.B	#BIT1, &P2REN	;	Config da entrada para o LED vermelho
			BIS.B	#BIT1, &P2OUT	;
			;
			BIC.B	#BIT1, &P1DIR	;
			BIS.B	#BIT1, &P1REN	;	Config da entrada para o LED verde
			BIS.B	#BIT1, &P1OUT	;
			;
			RET
;-------------------------------------------------------------------------------
; Stack Pointer definition
;-------------------------------------------------------------------------------
            .global __STACK_END
            .sect   .stack
            
;-------------------------------------------------------------------------------
; Interrupt Vectors
;-------------------------------------------------------------------------------
            .sect   ".reset"                ; MSP430 RESET Vector
            .short  RESET
            
