//Create the sound from the PC Speaker in 8086 Assembly Language by communicate with the speaker controller using IN and OUT instructions.
//The notes are in the second file

Progr           segment
                assume  cs:Progr, ds:dane, ss:stosik

start:          mov     ax,dane
                mov     ds,ax
                mov     ax,stosik
                mov     ss,ax
                mov     sp,offset szczyt

main :
		call parametr
        	call odczyt
        	lea si,nuty
        	call graj

parametr:
		mov ah, 62h
		int 21h  		;pobiera adres psp do bx
		mov es, bx		; wgrywa adres psp do es
		mov bx, 80h 		;80h po adresie psp jest ilosc znakow
		mov al, es:[bx] 	;zgrywam do petli ile odczytac
		cmp al,0                ;sprawdzam czy 0 jesli tak nie mamy nazwy pliku
		jz blad
		mov cx,0
		mov cl,al		;wgrywam ilosc do petli


		mov bx,82h 		;poczatek parametru od uzy
		mov al,es:[bx]
		
		dec cx                  ;-1 enter 
		lea si,Plik
		mov ah,0
		inpt:
		mov al, es:[bx]
		mov [si],al
		inc si
		inc bx
		loop inpt

		mov bx,0
		mov si,0
		ret

odczyt:
               
                lea dx,plik
                mov cx,0
                mov ah,3Dh            ;funkcja otwórz plik
                mov al,0              ;Tryb otwarcia: prawo do odczytu 1zapisu 2odczyt i zapis
                int 21h
		jc blad     	      ;zla nazwa
                mov bx,ax             ;uchwyt idzie do BX
		
                lea dx,nuty

                mov cx,licznik        ;ile bajtów odczytac
                mov ah,3fh            ;funkcja odczyt do dojścia
                int 21h               ;wywołaj funkcje DOS

                mov ah,3eh            ;zamknij plik w BX-uchwyt
                int 21h              ;
                ret
blad:
		
		mov dx,offset error
       		mov ah,09h	
		int 21h
		call koniec
       		
graj:
        	mov al,0
        	mov cx,01h   
        	mov dx,77A0h 
        	mov ah,86h   
        	int 15h      

       		mov     al, 10110110b         ; ustawia  tryby 
        	out     43h, al

        	call wgrajnute
        	call numer

        	out     42h, al         ; wgraj mlodszy
        	mov     al, ah          
        	out     42h, al		;wgraj starszy

        	in      al, 61h         ; zgraj bity z portu
        	or      al, 00000011b   ; ustaw 11 
        	out     61h, al         ; wyslij nowe bity wlacz glosnik

		mov al,[si]
		mov bl,'z'
		cmp al,bl  
		jz skok
	
        	mov al,0
        	mov cx,02h   
        	mov dx,39A0h 
        	mov ah,86h   
        	int 15h      

powrot:


        	in      al, 61h         
        	and     al, 11111100b   
        	out     61h, al         ; wylacz glosnik
	
     		mov cx,licznik
      		dec licznik
     		loop graj
		
        	call koniec

skok:
		inc si
		mov al,0
        	mov cx,04h   
       		mov dx,7340h 
       		mov ah,86h   
      		int 15h      			
		jmp powrot

wgrajnute:
                mov bl,[si]
                inc si

		mov ah,0eh
		mov al,bl 
       		int 10h

                mov ax,bx 

		cmp ax,'K'
                jz koniec

		mov bx,2
		sub ax,65	;odejmuje 65 czyli A w ascii zeby przesunac sie po tablicy
		mul bx    	;mnoze razy 2 bo zmienna 2 bitowa 
		lea di,czesto   ;zgrywam adres czesto
		add di,ax	;przesuwam sie do danej litery
		mov ax,[di]	;zgrywam czestotliwosc
                ret

numer:
                mov bl,[si]
                inc si
                cmp bl,'3'
                jz wroc
                cmp bl,'4'
                jz dziel2
                cmp bl,'5'
                jz dziel4

wroc:
       		 ret
dziel2:
		mov dx, 0     
		mov bx, 2
		div bx 
                ret
dziel4:
		mov dx, 0     
		mov bx, 4
		div bx 
                ret
koniec:
		mov ah,4ch
		mov al,0
		int 21h

Progr ends

dane            segment
nuty  db 300 dup('0')                         ;tutaj zapisuje notatnik
Plik db 10 dup(0)                                          
czesto dw 5423,4831,9121,8126,7239,6833,6087  ;czestotliwosci dla A3 B3 C3 itd
error db 'nie ma takiego pliku!!$'
licznik dw 253
dane            ends

stosik          segment

dw 100h dup(0)
szczyt          Label word
stosik          ends

end start
