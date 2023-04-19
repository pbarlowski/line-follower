#define F_CPU 16000000UL
#include <avr/io.h>
#include <util/delay.h>
#define r_for PORTD |= (1 << 2) //prawe ko�o ruch do przodu
#define r_rev PORTD |= (1 << 3) //prawe ko�o ruch do ty�u
#define r_fstop PORTD &= ~(1 << 2) //prawe ko�o ruch do przodu stop
#define r_rstop PORTD &= ~(1 << 3) //prawe ko�o ruch do ty�u stop
#define l_for PORTD |= (1 << 4) //lewe ko�o ruch do przodu
#define l_rev PORTD |= (1 << 5) //lewe ko�o ruch do ty�u
#define l_fstop PORTD &= ~(1 << 4) //lewe ko�o ruch do przodu stop
#define l_rstop PORTD &= ~(1 << 5) //lewe ko�o ruch do przodu stop

void InitADC(void)
{
	ADMUX|=(1<<REFS0); //Napi�cie oniesienia 5V
	ADCSRA|=(1<<ADEN)|(1<<ADPS0)|(1<<ADPS1)|(1<<ADPS2); //W��czenie ADC, pre128
}

uint16_t readadc(uint8_t ch)
{
	ch &= 0b00000111;         //Ograniczenie wpisania liczby max 7
	ADMUX = (ADMUX & 0xF8)|ch;  //Czyszczenie najm�odszych bit�w rejestru i nadpisywanie go aktualn� warto�ci�
	ADCSRA|=(1<<ADSC);        //Start konwersji
	while((ADCSRA)&(1<<ADSC));    //Czekanie na zako�czenie konwersji
	return(ADC);        //Zwr�cenie warto�ci ADC
}

void initPWM ()
{
	DDRB |= ((1<<PINB1)|(1<<PINB2));    // wyj�cia pwm
	
	/* INICJALIZACJA PWM - TIMER1 */
	TCCR1A |= (1<<WGM10);                   // Fast PWM 8bit
	TCCR1B |= (1<<WGM12);
	
	TCCR1A |= (1<<COM1A1)|(1<<COM1B1) ;     //Clear OC1A/OC1B on Compare Match, set OC1A/OC1B at BOTTOM
	TCCR1B |= (1<<CS10)|(1<<CS11);          // Preksaler = 64
}

void SetPWM (int SPEED_A, int SPEED_B)
{
	OCR1A = (SPEED_A*255)/100;
	OCR1B = (SPEED_B*255)/100;
}

int main(void)
{

	DDRD |= (1<<2) | (1<<3) | (1<<4) |(1<<5) | (1<<6) | (1<<7); //ustawienie port�w D jako wyj�cia
	
	uint16_t right, left;
	
	InitADC(); //Inicjalizacja ADC
	initPWM(); //Inicjalizacja PWM

	while(1)
	{

		left = readadc(0); //Odczytanie warto�ci analogowej z portu 0
		right = readadc(1); //Odczytanie warto�ci analogowej z portu 1
		
		if ((left < 150) & (right < 150)) //Je�li prawy i lewy czujnik nie widz� linii - prawy i lewy silnik do przodu
		{
			r_rstop; 
			l_rstop;
			r_for;
			l_for;
			SetPWM(60,60);
		}

		else if ((right > 150) & (left < 150)) //Je�li prawy czujnik widzi czarn� lini� - lewy silnik do przodu szybciej, prawy do ty�u wolniej
		{
			r_fstop;
			l_rstop;
			r_rev;
			l_for;
			SetPWM(60,40);
		}
		
		else if ((left > 150) & (right < 150)) //Je�li lewy czujnik widzi czarn� lini� - prawy silnik do przodu szybciej, lewy do ty�u wolniej
		{
			r_rstop;
			l_fstop;
			r_for;
			l_rev;
			SetPWM(40,60);
		}
		else // W innym przypadku - wy��cz oba silniki
		{
			r_rstop;
			l_rstop;
			r_fstop;
			l_fstop;
		}
			
	}
	return 0;
}
