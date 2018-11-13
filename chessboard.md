# Assembly
task from assembly 8080

ORG 800H  
	 MVI E,49 ;licznik petli poziom  
	 MVI C,80 ;dlugosc konsoli poziom  
	 CALL STARTSET  
	 CALL LOAD_NUMBER  
	 CALL LOAD_CHAR  
	 CALL ATOINPUTCHAR  
	 MOV L,D ;znak rozpoczynajacy linie   
MAIN  
	 MVI A,1  
	 CMP C  
	 CZ NEXTLINE  
	 MOV A,E  
	 CMP H  
	 CM WRITE  
	 CZ SET  
	 INR E  
	 JNZ MAIN  
WRITE  
	 MOV A,D  
	 RST 1  
	 DCR C  
	 RET  
SET  
	 MVI E,48  
	 LDA SPACECHAR  
	 CMP D  
	 MOV D,A  
	 CZ INPUTCHARTOD  
	 RET  
INPUTCHARTOD  
	 LDA INPUTCHAR  
	 MOV D,A  
	 RET  
NEXTLINE  
	 MVI C,80  
	 MVI E,49  
	 MVI A,11  
	 RST 1  
	 CALL SIZEYDCR  
	 LDA SIZEY  
	 MOV B,A  
	 LDA CONST1  
	 CMP B  
	 CZ END  
	 CALL COUNTERYINC  
	 LDA COUNTERY  
	 CMP H  
	 MOV D,L  
	 CZ CHAR_SWAP  
	 RET  
END  
	 HLT  
SIZEYDCR  
	 LDA SIZEY  
	 DCR A  
	 STA SIZEY  
	 RET  
COUNTERYINC  
	 LDA COUNTERY  
	 INR A  
	 STA COUNTERY  
	 RET  
CHAR_SWAP  
	 CALL COUNTERYSET  
	 LDA SPACECHAR  
	 CMP L  
	 MOV L,A  
	 MOV D,A  
	 CZ INPUTCHARTOLD  
	 RET  
COUNTERYSET  
	 MVI A,49  
	 STA COUNTERY  
	 RET  
INPUTCHARTOLD  
	 LDA INPUTCHAR  
	 MOV L,A  
	 MOV D,A  
	 RET  
LOAD_NUMBER  
	 RST 2  
	 MOV H,A  
	 INR H  
	 CALL ENTER  
	 RET  
LOAD_CHAR  
	 RST 2  
	 MOV D,A  
	 CALL ENTER  
	 RET  
ENTER  
	 MVI A,11  
	 RST 1  
	 RET  
ATOINPUTCHAR  
	 MOV A,D  
	 STA INPUTCHAR  
	 RET  
STARTSET  
	 MVI A,' '  
	 STA SPACECHAR  
	 MVI A,50  
	 STA CONST1  
	 MVI A,71  
	 STA SIZEY  
	 MVI A,'1'  
	 STA COUNTERY  
	 RET  
INPUTCHAR 	 DB ' ' ;znak uzytk                 
SPACECHAR 	 DB ' ' ;znak spacji              
CONST1 	 DB ' ' ;stala 1
SIZEY 	 DB ' ' ;wysokosc cmd pion
COUNTERY 	 DB ' ' ;licznik wysokosci pion
