ABSRACT
   
  DIGITAL THERMOMETER
                Thermometers are the device we use to measure the temperature in any desired scale and we all will be quite familiar with the analog thermometers. There are some disadvantages in analog thermometers and this can be overcome by using this digital thermometer using avr. The above embedded system shows the design and implementation of a simple Digital Thermometer using Atmega16 (AVR),  LM35 & 16×2 LCD.In this project we are going to design a circuit for measuring temperature. This circuit is developed using “LM35”, a linear voltage sensor. Temperature is usually measured in “Centigrade” or “Faraheite”. “LM35” sensor provides output based on scale of centigrade.LM35 is three pin transistor like device. It has VCC, GND and OUTPUT. This sensor provides variable voltage at the output based on temperature.for every +1 centigrade raise in temperature there will be +10mV higher output. So if the temperature is 0◦centigrade the output of sensor will be 0V, if the temperature is 10◦ centigrade the output of sensor will be +100mV, if the temperature is 25◦ centigrade the output of sensor will be +250mV.So for now with LM35 we get temperature in the form of variable voltage. This temperature dependent voltage is given as input to ADC (Analog to Digital Converter) of ATMEGA32A. The digital value after conversion obtained is shown in the 16x2 LCD as temperature.
                
                
  COMPONENETS REQUIRED
  
   LM35: LM35 is a Precision temperature sensor IC with its output proportional to the temperature (in degree Celsius). LM35 is capable of giving accurate temperature readings compared to thermistor. The senor is sealed to avoid the effects of oxidation and other factors. It operates at a temperature range of -55°c to 150°c. The output voltage varies by 10mV in response to every °C rise or fall in atmospheric temperature.LM35 is an integrated analog temperature sensor whose electrical output is proportional to Degree Centigrade. LM35 Sensor does not require any external calibration or trimming to provide typical accuracies. The LM35’s low output impedance, linear output, and precise inherent calibration make interfacing to readout or control circuitry especially easy.

16*2 LCD

LCD (Liquid Crystal Display) is widely used electronic display module and got a wide range of applications. A 16×2 LCD consists of 16 columns and 2 rows thereby it is capable of displaying 16 characters in a single line. It consists of two registers, Command register used to store the command instructions given to the LCD. Whereas Data Register used to store the data’s to be displayed in the LCD. Read more about Interfacing 16×2 LCD with Atmega32.Pin1 (Ground/Source Pin): This is a GND pin of display, used to connect the GND terminal of the microcontroller unit or power source.Pin2 (VCC/Source Pin): This is the voltage supply pin of the display, used to connect the supply pin of the power source.Pin3 (V0/VEE/Control Pin): These pins are used to send data to the display. These pins are connected in two-wire modes like 4-wire mode and 8-wire mode. In 4-wire mode, only four pins are connected to the microcontroller unit like 0 to 3, whereas in 8-wire mode, 8-pins are connected to microcontroller unit like 0 to 7.Pin15 (+ve pin of the LED): This pin is connected to +5VPin 16 (-ve pin of the LED): This pin is connected to GND.

ATMega328

The ATmega328 is a single-chip microcontroller created by Atmel in the megaAVR family (later Microchip Technology acquired Atmel in 2016). It has a modified Harvard architecture 8-bit RISC processor core.The Atmel 8-bit AVR RISC-based microcontroller combines 32 KB ISP flash memory with read-while-write capabilities, 1 KB EEPROM, 2 KB SRAM, 23 general-purpose I/O lines, 32 general-purpose working registers, 3 flexible timer/counters with compare modes, internal and external interrupts, serial programmable USART, a byte-oriented 2-wire serial interface, SPI serial port, 6-channel 10-bit A/D converter (8 channels in TQFP and QFN/MLF packages), programmable watchdog timer with internal oscillator, and 5 software-selectable power-saving modes. The device operates between 1.8 and 5.5 volts. The device achieves throughput approaching 1 MIPS/MHz.ATmega328 is commonly used in many projects and autonomous systems where a simple, low-powered, low-cost micro-controller is needed. Perhaps the most common implementation of this chip is on the popular Arduino development platform, namely the Arduino Uno, Arduino Pro Mini[4] and Arduino Nano models.

CAPACITOR

The effect of a capacitor is known as capacitance. While some capacitance exists between any two electrical conductors in proximity in a circuit, a capacitor is a component designed to add capacitance to a circuit. The capacitor was originally known as a condenser or condensator.[1] This name and its cognates are still widely used in many languages, but rarely in English, one notable exception being condenser microphones, also called capacitor microphones.The physical form and construction of practical capacitors vary widely and many types of capacitor are in common use. Most capacitors contain at least two electrical conductors often in the form of metallic plates or surfaces separated by a dielectric medium. A conductor may be a foil, thin film, sintered bead of metal, or an electrolyte. The nonconducting dielectric acts to increase the capacitor's charge capacity. Materials commonly used as dielectrics include glass, ceramic, plastic film, paper, mica, air, and oxide layers. Capacitors are widely used as parts of electrical circuits in many common electrical devices. Unlike a resistor, an ideal capacitor does not dissipate energy, although real-life capacitors do dissipate a small amount (see Non-ideal behavior). When an electric potential difference (a voltage) is applied across the terminals of a capacitor


IMPLEMENTATION


/**
 * @file temp.c
 * @author Om Gautam (omee1434omee@gmail.com)
 * @brief 
 * @version 0.1
 * @date 2022-03-08
 * 
 * @copyright Copyright (c) 2022
 * 
 */
#ifndef _AVR_ATmega32_
    #define _AVR_ATmega32_
#endif
#include <avr/io.h>
#define F_CPU 1000000
#include <util/delay.h>
#include <stdlib.h>

#define enable            5
#define registerselection 6

void send_a_command(unsigned char command);
void send_a_character(unsigned char character);
void send_a_string(char *string_of_characters);

int main(void)
{
    DDRB = 0xFF;
    DDRA = 0;
    DDRD = 0xFF;
    _delay_ms(50);
    
    ADMUX |=(1<<REFS0)|(1<<REFS1);
    ADCSRA |=(1<<ADEN)|(1<<ADATE)|(1<<ADPS0)|(1<<ADPS1)|(1<<ADPS2);
    
    int16_t COUNTA = 0;
    char SHOWA [3];
     

    send_a_command(0x01); //Clear Screen 0x01 = 00000001
    _delay_ms(50);
    send_a_command(0x38);
    _delay_ms(50);
    send_a_command(0b00001111);
    _delay_ms(50);
    
    ADCSRA |=(1<<ADSC);
    while(1)
    {
        COUNTA = ADC/4;
        send_a_string ("OM GAUTAM");
        send_a_command(0x80 + 0x40 + 0);
        send_a_string ("Temp(C)=");
        send_a_command(0x80 + 0x40 + 8);
        itoa(COUNTA,SHOWA,10);
        send_a_string(SHOWA);
        send_a_string ("      ");
        send_a_command(0x80 + 0);
        
    }    
}

void send_a_command(unsigned char command)
{
    PORTB = command;
    PORTD &= ~ (1<<registerselection);
    PORTD |= 1<<enable;
    _delay_ms(20);
    PORTD &= ~1<<enable;
    PORTB = 0;
}

void send_a_character(unsigned char character)
{
    PORTB = character;
    PORTD |= 1<<registerselection;
    PORTD |= 1<<enable;
    _delay_ms(20);
    PORTD &= ~1<<enable;
    PORTB = 0;
}
void send_a_string(char *string_of_characters)
{
    while(*string_of_characters > 0)
    {
        send_a_character(*string_of_characters++);
    }
}

