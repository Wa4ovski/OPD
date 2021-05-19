ORG 0x00
BUF:	WORD 	0, 0, 0, 0, 0	
R:		WORD	0			; Результат
SUM:	WORD	0 			; Сумма всех чисел последовательности(считается в конце)
N:		WORD 	0			; Текущее количесвто чисел
VBEGIN:	WORD 	$VALUES		; Указатель на сумму разрядов единиц
I_V: 	WORD				; Указатель на текущую сумму цифр разряда
VALUES:	WORD 	0, 0, 0		; Текущая сумма цифр cответсвтенных разрядов
FBEGIN: WORD 	$FACTORS	; Указатель на сумму разрядов единиц
I_F: 	WORD				; Указатель на текущий коэффициент 
FACTORS:WORD	1, 10, 100	; Множители для суммы цифр разрядов
SIGN:	WORD				; Храним текущий знак действия ('.' или '=')
T:		WORD	3			; Разрядность вводимых чисел
POINT: 	WORD  	000E		; Код '.'
EQUAL:	WORD  	000F		; Код '=' 
POS_VU7:WORD	32			; Число, кодирующее позицию цифры на вывод ВУ-7


ORG 0x20
INPUT:	IN 	0x1D		; Проверка готовности
	AND 	#0x40 
	BEQ 	INPUT 
	IN 		0x1C		; Ввод
	CMP 	$POINT		; Выставить флаги AC-POINT
	BPL 	TREATMENT	; Если AC >= POINT, то идем в TREATMENT
	PUSH				; Положили текущую цифру с стек
	JUMP 	INPUT

TREATMENT:	ST	$SIGN	;
	OR	(N)+	; Только ради инкремента
	LD 	$VBEGIN
	ST 	$I_V	; Загрузили в I_V адрес начала
ADDING:	POP		; Сняли со стека последнюю введенную цифру числа
	ADD 	(I_V)	
	ST 	(I_V)+	; Складываем ее со своей суммой, сохраняем и инкрементируем указалель
	LOOP	$T	; Повторяем цикл 3 раза (по максимальному кол-ву разрядов)
	JUMP	ADDING
	LD	#0x03
	ST	$T	; Вернули T его значение
	LD	$SIGN	; Загрузили SIGN
	CMP	$EQUAL
	BEQ	EOF	; Если SIGN == EQUAL, то идем в EOF
	JUMP	INPUT ; Идем обрабатывать следующее введенное число
EOF:	LD 	$FBEGIN
	ST 	$I_F	; Загрузили в I_F адрес начала
	LD 	$VBEGIN
	ST 	$I_V	; Загрузили в I_V адрес начала

SUM_CALC:	LD	(I_V)
	BEQ	SKIP
	CLA
L:	ADD	(I_F)
	LOOP 	(I_V)
	JUMP	L
	ADD	$SUM
	ST	$SUM
SKIP:	OR	(I_F)+	; Только ради инкремента
	OR	(I_V)+	; Только ради инкремента
	LOOP	$T
	JUMP	SUM_CALC
	LD	#0x03
	ST	$T
	
	LD 	$SUM	; Деление SUM / N = R
	PUSH
	LD	$N
	PUSH
	CALL 	$DIV
	POP
	ST	$R
	POP
	

	
	LD	#0x03
	ST	$T
	LD	$FBEGIN
	ADD	$T
	ST	$I_F
	LD	$VBEGIN
	ADD	$T
	ST	$I_V
	

DECA:	LD 	$R	
	PUSH
	LD	-(I_F)
	PUSH
	CALL 	$DIV
	POP
	ADD	$POS_VU7
	ST	-(I_V)
	POP
	ST	$R
	LD	$POS_VU7
	SUB	#0x10
	ST	$POS_VU7
	LOOP	$T
	JUMP	DECA
	LD	#0x03
	ST	$T
	
	LD	$VBEGIN
	ST	$I_V
OUTPUT:	IN 	0x15
	AND 	#0x40
	BEQ 	OUTPUT
	LD	(I_V)+
	OUT	 0x14
	LOOP	$T
	JUMP 	OUTPUT
	HLT


ORG 0x200
DIV:	CLA
	ST	$REZ
DIV_LOOP:	LD	&2
	SUB	&1
	ST	&2
	BMI	ADD_BACK
	OR	(REZ)+	; Только ради инкремента
	LD	&2
	BEQ	RETURN
	JUMP	DIV_LOOP
ADD_BACK:	LD	&2
	ADD	&1
	ST	&2
RETURN:	LD	$REZ
	ST	&1
	RET
REZ: 	WORD	0
