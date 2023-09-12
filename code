#INCLUDE "P16F877A.INC"

	cblock		0x30
TEMP 
QestionNum      
Start 
count 
lsd	
msd	 
RESULT0	
SCORE1
SCORE2
C1
C2
C3	
REPEAT	   
count1
count2
TEMP2
TEMP1 
TEMP3
TEMP4
endcounter
D1 
COUNT2
COUNT1
endc
org 0x00
GOTO MAIN
org 0x0004
goto ISR

;*********************************************
MAIN
		Banksel TRISB
;RB0=INTERRUPT,RB1=LED2,RB2=LED1,SWICH0=RB3,SWITCH1=RB4,SUBMIT=RB5,RB6=STARTBUTTON
		movlw	B'11111001'
		movwf	TRISB
		bsf STATUS , RP0 ; select bank 1
		bsf OPTION_REG , INTEDG ; select to interrupt on rising edge
		bsf INTCON , INTE ; enable external interrupt on RB0/INT
		bsf INTCON , GIE ; enable global interrupts
		bcf STATUS , RP0
Banksel 	 OPTION_REG
MOVLW B'00000111'
	MOVWF OPTION_REG
	BANKSEL	TMR0
	MOVLW 0xA5
	MOVWF TMR0
		Banksel PORTB
		movlw B'00000001'
		movwf	PORTB
wait0	btfsc   PORTB,6  ;PRESS STARTBUTTON
		goto 	wait0
		Banksel PORTB
		bsf	PORTB,2 ;LED1 ON
        movlw d'5'
        movwf endcounter
		Movlw	0x01		;clear display
		Call	send_cmd
      movlw         B'11111111' 
       movwf        PORTC
       CALL         DELAY_SEG
       CALL         DELAY_SEG
		goto	SELECTQUESTION
;************************
ISR
bcf INTCON , INTF 
CALL CHECK_RESULT
retfie

SELECTQUESTION
        
		Banksel	TRISA		;PORTA and PORTD as outputs
		movlw	b'00000101'
		movwf	TRISA		
		Clrf	TRISD
		Clrf	TRISC				
		BANKSEL		ADCON1
		MOVLW		b'10001111'
		MOVWF		ADCON1	
		Banksel	PORTA
		Clrf	PORTA		
		Clrf	PORTD
        movlw 0x38 		;8-bit mode, 2-line display, 5x7 dot format
		Call	send_cmd	
		Movlw	0x0C		;Display on, Cursor Underline on, Blink off
		Call	send_cmd
		Movlw	0x02		;Display and cursor home
		Call	send_cmd
		Movlw	0x01		;clear display
		Call	send_cmd

		BANKSEL		ADCON0
		movlw		b'01000001'
		MOVWF		ADCON0
		call 		DELAY

LOOP

		clrf 		TEMP
		movlw		b'01000001'
		MOVWF		ADCON0
		call 		DELAY
		bsf			ADCON0,GO
		BANKSEL		PIR1
WAIT	BTFSS		PIR1, ADIF 		
		GOTO	 	WAIT
		BCF			PIR1,ADIF
		
        BANKSEL		ADRESH
        movlW	B'00000011'
		ANDWF	ADRESH,W
		MOVWF       TEMP2

        movlw        .0
        SUBWF        TEMP2,w  ;00
        BTFSS      STATUS,Z  ; H=00 -> Q0||Q1
        GOTO        LabelQ1Q2
        movlw       .205
	    subwf       ADRESL,w
        BTFSc       STATUS,C
        CALL        Q1   ; 206->255
        CALL        Q0
        

LabelQ1Q2  
        movlw        .1  ; bit 9 -> 1
        SUBWF        TEMP2,w
        BTFSS        STATUS,Z  ; H=00 -> Q0||Q1
        GOTO         LabelQ2Q3
        movlw       .155
	    subwf       ADRESL,w
        BTFSc       STATUS,C
        CALL        Q2     ; 411->511
        CALL        Q1


LabelQ2Q3  
        movlw        .2
        SUBWF        TEMP2,w
        BTFSS        STATUS,Z  ; H=00 -> Q0||Q1
        GOTO         LabelQ3Bouns
        movlw       .105
	    subwf       ADRESL,w
        BTFSc       STATUS,C
        CALL        Q3    ; 618 -> 767
        CALL        Q2

LabelQ3Bouns  
		movlw        .3
        SUBWF        TEMP2,w
        BTFSC       STATUS,Z  ; H=00 -> Q0||Q1
		CALL 		bonus
        movlw       .55   ; 768 -> 823
	    subwf       ADRESL,w
        BTFSc       STATUS,C
        CALL        bonus  
        CALL        Q3
        
GOTO      	LOOP	        ;Do it again



;************************************************************
Q0		

call check_countL1
BTFSC REPEAT,0
CALL repeated

movlw 0x80
call send_cmd
movlw '1'
call send_char
movlw '+'
call send_char
movlw '1'
call send_char
movlw '='
call send_char
movlw 0xC0
call send_cmd
movlw 'a'
call send_char
movlw '1'
call send_char
movlw 'b'
call send_char
movlw '4'
call send_char
movlw 'c'
call send_char
movlw '2'
call send_char
bsf	REPEAT,0
movlw D'3'
movwf RESULT0
call seven_seg
CALL CHECK_RESULT
btfss PORTB ,2
incf count2,f
CALL INC
CALL SWITCH_LED

return

;*********************************
Q1	

call check_countL1
BTFSC REPEAT,1
CALL repeated
movlw 0x80
call send_cmd
movlw '7'
call send_char
movlw '-'
call send_char
movlw '2'
call send_char
movlw '='
call send_char
movlw 0xC0
call send_cmd
movlw 'a'
call send_char
movlw '5'
call send_char
movlw 'b'
call send_char
movlw '4'
call send_char
movlw 'c'
call send_char
movlw '9'
call send_char
bsf	REPEAT,1
movlw D'0'
movwf RESULT0
call seven_seg
CALL CHECK_RESULT
btfss PORTB ,2
incf count2,f
CALL INC
CALL SWITCH_LED


return


;*********************
Q2

call check_countL1
BTFSC REPEAT,2
CALL repeated	
movlw 0x80
call send_cmd
movlw '5'
call send_char
movlw '*'
call send_char
movlw '1'
call send_char
movlw '='
call send_char
movlw 0xC0
call send_cmd
movlw 'a'
call send_char
movlw '6'
call send_char
movlw 'b'
call send_char
movlw '4'
call send_char
movlw 'c'
call send_char
movlw '5'
call send_char
bsf	REPEAT,2
movlw D'3'
movwf RESULT0
call seven_seg
CALL CHECK_RESULT
btfss PORTB ,2
incf count2,f
CALL INC
CALL SWITCH_LED


return


;*********************
Q3
call check_countL1
BTFSC REPEAT,3
CALL repeated
movlw 0x80
call send_cmd
movlw '6'
call send_char
movlw '/'
call send_char
movlw '2'
call send_char
movlw '='
call send_char
movlw 0xC0
call send_cmd
movlw 'a'
call send_char
movlw '5'
call send_char
movlw 'b'
call send_char
movlw '4'
call send_char
movlw 'c'
call send_char
movlw '3'
call send_char
bsf	REPEAT,3
movlw D'3'
movwf RESULT0
call seven_seg
CALL CHECK_RESULT
btfss PORTB ,2
incf count2,f
CALL INC
CALL SWITCH_LED



RETURN
;********************
INC
btfsC PORTB ,2
incf count1,f
RETURN

;**********
S103
CALL CLRINT
movlw D'10'
btfss PORTB ,2
addwf SCORE2,F
CALL S13
RETURN
;********************
CLRINT
bcf INTCON , INTF ; clear the interrupt flag
RETURN
;*****************************************
S13 
btfsC PORTB ,2
addwf SCORE1,F
RETURN

;************************
;check if the ansewr correct the player will get 10 score
CHECK_RESULT

MOVLW B'00011000'
ANDWF PORTB,W
SUBWF RESULT0,W
BTFSC STATUS,Z
call S103
CALL SWITCH_LED
return
;*****************
SWITCH_LED

decfsz endcounter ,f
CALL l1
goto WIN
RETURN
;*******************************
l1 
CALL CLRINT
MOVLW B'00000110'
XORWF PORTB,F

goto	SELECTQUESTION
RETURN
;***********************
WIN
CALL CLRINT
movf SCORE1, W   
subwf SCORE2, W  
BTFSC STATUS,C  
goto player1wins
goto player2wins

return
;************************
check_countL1
Btfss PORTB,2
call check_countL2
movlw d'2'
subwf	count1,w
btfsc	STATUS,Z
call    repeated
return
;**********************
check_countL2
movlw d'2'
subwf	count2,w
btfsc	STATUS,Z
CALL 	repeated
return
;***************
;check if this repeated question for player 2
bit5
BTFSC REPEAT,0
CALL repeated
goto ret2
;***************************
;setbit5
;btfss PORTB,2
;bsf REPEAT,5
;return
;*****************************
bonus

btfss PORTB,2
goto bit5
BTFSC REPEAT,4
CALL repeated
ret2
movlw 0x80
call send_cmd
movlw 'B'
call send_char
movlw 'o'
call send_char
movlw 'n'
call send_char
movlw 'u'
call send_char
movlw 's'
call send_char
movlw '='
call send_char
btfsc PORTB,2
bsf REPEAT,4
;call setbit5


MOVF TMR0,W
MOVWF TEMP4
DECF TMR0,F
MOVF TEMP4,W
ANDLW B'00000111'
MOVWF TEMP3
BTFSC TEMP3,2
CALL NEG
MOVF TEMP3,W
MOVWF TEMP4
MOVF TEMP4,W
ANDLW B'00000011'
MOVWF TEMP4
MOVF TEMP4,W
SUBLW D'3'
BTFSC STATUS,Z
goto NUM3
MOVF TEMP4,W
SUBLW D'2'
BTFSC STATUS,Z
goto NUM2
MOVF TEMP4,W
SUBLW D'1'
BTFSC STATUS,Z
goto NUM1
MOVLW'0'
CALL send_char
ret	
 CALL         DELAY_SEG
CALL         DELAY_SEG
CALL SWITCH_LED

return

 

NEG 
MOVLW'-'
CALL send_char
RETURN

 

NUM3
MOVLW'3'
CALL send_char
BTFSS TEMP3,2
btfss PORTB,2
GOTO BONUS2
GOTO BONUS1
goto  ret


NUM2
MOVLW'2'
CALL send_char
BTFSS TEMP3,2
btfss PORTB,2
GOTO BONUS2
GOTO BONUS1
goto  ret

 

NUM1
MOVLW'1'
CALL send_char
MOVLW D'1'
MOVWF D1
BTFSS TEMP3,2
GOTO ADD
GOTO SUB
goto  ret


BONUS2
MOVF D1,W
ADDWF SCORE2,F
GOTO ret

BONUS1
MOVF D1,W
ADDWF SCORE1,F
GOTO ret
ADD
btfss PORTB,1
GOTO BONUS2
GOTO BONUS1

BONUS22
MOVF D1,W
SUBWF SCORE2,F
GOTO ret

BONUS11
MOVF D1,W
SUBWF SCORE1,F
GOTO ret

SUB
btfss PORTB,1
GOTO BONUS22
GOTO BONUS11



;************************************************************
seven_seg
BANKSEL TRISC
CLRF TRISC
BANKSEL PORTC
       movlw    B'10010000'  ;9
       movwf        PORTC
       CALL         DELAY_SEG
       CALL         DELAY_SEG
CALL SUBMIT0

       movlw         B'10000000'  
       movwf        PORTC
       CALL         DELAY_SEG
       CALL         DELAY_SEG
CALL SUBMIT0
       movlw         B'11111000'  
       movwf        PORTC
       CALL         DELAY_SEG
       CALL         DELAY_SEG
CALL SUBMIT0
          movlw        B'10000010'  
       movwf        PORTC
       CALL        DELAY_SEG
       CALL         DELAY_SEG
CALL SUBMIT0

 		 movlw         B'10010010'      
       movwf        PORTC
       CALL         DELAY_SEG
       CALL         DELAY_SEG
CALL SUBMIT0
       movlw         B'10011001' 
       movwf        PORTC
       CALL         DELAY_SEG
       CALL         DELAY_SEG
CALL SUBMIT0
       movlw        B'10110000'  
       movwf        PORTC
       CALL         DELAY_SEG
       CALL         DELAY_SEG
CALL SUBMIT0
     movlw         B'10100100'  
       movwf        PORTC
       CALL        DELAY_SEG
       CALL        DELAY_SEG
       movlw         B'11111001' 
       movwf        PORTC
       CALL         DELAY_SEG
       CALL         DELAY_SEG
CALL SUBMIT0
	CALL SWITCH_LED
RETURN
 	

;********************
SUBMIT0
BTFSS PORTB,0
CALL CHECK_RESULT
RETURN

;************************************************
DELAY_SEG
MOVLW D'0'
MOVWF COUNT1
MOVLW D'61'
MOVWF COUNT2
L NOP
NOP
NOP
NOP
NOP
DECFSZ COUNT1 , F
GOTO L
DECFSZ COUNT2 , F
GOTO L
return

;**********************************************
repeated
	Movlw	0x01		;clear display
		Call	send_cmd
movlw 0x80
call send_cmd
movlw 'r'
call send_char
movlw 'e'
call send_char
movlw 'p'
call send_char
movlw 'e'
call send_char
movlw 'a'
call send_char
movlw 't'
call send_char
movlw 'e'
call send_char
movlw 'd'
call send_char

	
goto SELECTQUESTION
return

;**********************************
player1wins
	Movlw	0x01		;clear display
	Call	send_cmd
movlw 0x80
call send_cmd
movlw 'P'
call send_char
movlw 'l'
call send_char
movlw 'a'
call send_char
movlw 'y'
call send_char
movlw 'e'
call send_char
movlw 'r'
call send_char
movlw '1'
call send_char
movlw 'w'
call send_char
movlw 'i'
call send_char
movlw 'n'
call send_char
movlw 0xC0
CALL send_cmd

	
goto DONE

player2wins
	Movlw	0x01		;clear display
	Call	send_cmd
movlw 0x80
call send_cmd
movlw 'P'
call send_char
movlw 'l'
call send_char
movlw 'a'
call send_char
movlw 'y'
call send_char
movlw 'e'
call send_char
movlw 'r'
call send_char
movlw '2'
call send_char
movlw 'w'
call send_char
movlw 'i'
call send_char
movlw 'n'
call send_char

	
goto DONE


;***************************************************************
send_cmd
		movwf	PORTD		; Refer to table 1 on Page 5 for review of this subroutine	
		bcf	PORTA,	1                                                                                 
		bsf	PORTA,	5		
		nop				
		bcf	PORTA,	5
		bcf	PORTA,	4		
		call	delay			
		return
;**********************************************************************************
send_char
		movwf	PORTD		; Refer to table 1 on Page 5 for review of this subroutine	
		bsf	PORTA,	1		 
		bsf	PORTA,	5				
		nop
		bcf	PORTA,	5
		bcf	PORTA,	4
		call	delay
		return
;******************************************
DELAY
  		MOVLW    	0x0F
        BANKSEL TEMP
  		MOVWF    	TEMP
L12 		DECFSZ   	TEMP,1
  		GOTO     	L12
  		RETURN
;**********************************************	

delay_loop

MOVLW B'00000111'
	MOVWF OPTION_REG
	BANKSEL	TMR0
	MOVLW 0x3C
	MOVWF TMR0

  	BTFSS INTCON, T0IF  ; Check if the timer overflow flag is set
  GOTO delay_loop     ; If not, loop until it is
	BSF INTCON, T0IF
		movlw 0x01
		Call send_cmd
return
;********************************************
delay	                                                                                
		movlw	0xFF			
		movwf	msd
		clrf	lsd
loop2
		decfsz	lsd,f
		goto	loop2
		decfsz	msd,f
endLcd
		goto	loop2
		return



;****************
DONE 
END
