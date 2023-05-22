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
			mov 	#vetor,R5 	;inicializar R5 com o endereço do vetor
			call 	#MAIOR16 	;chamar sub-rotina pedida pelo EX2
			jmp 	$ 			;travar execução ao retornar da sub-rotina

; Função que retorna menor elemento de umvetor
; Recebe:  	R5 = início do vetor (ponteiro)
; Retorna: 	R6 = maior elemento
;          	R7 = freq do elemento (começar com zero)
; Usa 		R8 = Contador
;
MAIOR16:	MOV		#0,R6	;R6=menor nr possível em 16 bits
			CLR		R7		;R7=0, zerar o cont de freq
			MOV		@R5,R8	;R8 = tamanho do vetor
			INCD		R5
LB1:		CMP		@R5,R6
			JEQ		LB3		;Se R6=@R5, pular
			JHS		LB2		;Se R6>R5 ==> saltar SE MAIOR
			MOV		@R5,R6	;R6=novo MAIOR
			MOV		#1,R7	;Já apareceu 1 vez
			JMP		LB2		;Fechar o laço
			;
LB3:		INC		R7		;Incr cont freq
LB2:		INCD		R5		;Avançar ponteiro
			DEC		R8		;Decrementar contador
			JNZ		LB1		;Repetir o laço
			RET


			.data
; Declaração do vetor passado em aula pelo professor
vetor: 		.word	5,3,9,2,15,15

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
            
