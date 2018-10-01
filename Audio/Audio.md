# Audio

## 1-bit DACs

You can play audio on any FPGA output pin by sending a pulse width modulated (PWM) signal to it. A PWM signal has a frequency and a duty cycle.

There is a general purpose PWM generator in BlackSoC called mod_pwm. This lets you set any frequency and duty cycle on any pin.

There are also PWM implementations in [Simon Monk’s Programming FPGAs][] book and on the [fpgafun site][].

The PWM signal can be sent to one channel of a speaker just by connecting the pin to an audio jack through a resistor or a low-pass filter. The low pass filter smooths the PWM signal, but that is optional for high impedance speakers.

![PWM Audio][img1]

[Simon Monk’s Programming FPGAs]:		https://github.com/lawrie/prog_fpgas/tree/master/blackice/ch07_pwm/src
[fpgafun site]:							https://www.fpga4fun.com/PWM_DAC.html
[img1]:									./PWMAudio.jpg "PWM Audio"

## Tone generation

The fpgafun site has a [very simple example of tone generation][].

[very simple example of tone generation]:	https://www.fpga4fun.com/MusicBox1.html

Create a directory call music, and add:

music.pcf:

	set_io speaker 34
	set_io clk 129

It uses pin 1 on Pmod 12 top connect to a speaker directly (if impedance is high), via a resistor, or via a low pass filter. There is information on this on the fpgafun site.

This is the Verilog code for middle C using the BlackIce II 100Mhz clock:

music.v:

	module music(clk, speaker);
		input clk;
		output speaker;
		parameter clkdivider = 100000000/440/2;

		reg [16:0] counter;
		always @(posedge clk) if(counter==0) counter <= clkdivider-1; else counter <= counter-1;

		reg speaker;
		always @(posedge clk) if(counter==0) speaker <= ~speaker;

	endmodule


Run that in the normal way and you should here middle C.

The fpgafun.com site describes how to do more [interesting sounds][] like police sirens. [Here][] are [here][] are the BlackIce II version of those sound generators.

[interesting sounds]:					https://www.fpga4fun.com/MusicBox2.html
[Here]:									https://github.com/lawrie/verilog_examples/tree/master/fpgafun/music2
[here]:									https://github.com/lawrie/verilog_examples/tree/master/fpgafun/music2a

## Playing tunes

The fpgafun site goes on to give an [example of playing tunes][]. Here is a [BlackIce II version][] of that.

[example of playing tunes]:				https://www.fpga4fun.com/MusicBox4.html
[BlackIce II version]:					https://github.com/lawrie/verilog_examples/tree/master/fpgafun/music4

## Audio streaming over a UART

The fpgafun site has an example of audio streaming MP3 data coming from a UART connection.  The code works unchanged on Blackice II. We just need to set up a pcf file use the standard Makefile.

Create a directory called audiostream and add:

stream.pcf:

	set_io PWM_out 34
	set_io RxD 88
	set_io clk 129

PWM.v

	module PWM(input clk, input RxD, output PWM_out);
		wire RxD_data_ready;
		wire [7:0] RxD_data;
		async_receiver deserializer(.clk(clk), .RxD(RxD), .RxD_data_ready(RxD_data_ready), .RxD_data(RxD_data)); 

		reg [7:0] RxD_data_reg;
		always @(posedge clk) if(RxD_data_ready) RxD_data_reg <= RxD_data;
		////////////////////////////////////////////////////////////////////////////
		reg [8:0] PWM_accumulator;
		always @(posedge clk) PWM_accumulator <= PWM_accumulator[7:0] + RxD_data_reg;

		assign PWM_out = PWM_accumulator[8];
	endmodule

Get the async_receiver.v and BaudTickGen.v files from fpgafun.com.

Makefile:

	VERILOG_FILES = PWM.v async_receiver.v BaudTickGen.v
	PCF_FILE = stream.pcf

	include ../blackice.mk

To stream audio over uart, you need a suitable streaming client. On Linux, you can install mpg123.

	mpg123 -m -s -4 --8bit <filename>.mp3 >/dev/ttyUSB0

## Audio streaming from an SD card

There are several ways of playing WAV files from an SD card.

One is to use the Arduino IDE to write a program that reads a WAV file from the SD card and sends it to the Ice40 using QSPI, which then uses PWM to send it to a speaker as in the examples above. This is described in the Arduino chapter below.

Another way is to use BlackSoC to read the SD card and to use mod_audio to do the PWM output. The [wavplay][] BlackSoC example does that.

[wavplay]:								https://github.com/lawrie/icotools/blob/master/icosoc/examples/wavplay

## I2S

To get better sound quality you can use the [Digilent i2s Pmod][].

![I2S Audio][img2]

This requires a higher bandwidth music stream than uart can handle. You could read the mp3 file from the SD card reader, or you could stream data over QSPI from the STM32 or over SPI from a Raspberry Pi using the RPi header. See the chapter on the RPi header for how to do that.

[Digilent i2s Pmod]:					https://store.digilentinc.com/pmod-i2s-stereo-audio-output/
[img2]:									./I2SAudio.jpg "I2S Audio"

## Microphones

To record speech or other audio input, you need a microphone.

There is the [Digilent MIC3 MEMS Microphone Pmod][].

![Microphone Pmod][img3]

Here is [an example][] of using the microphone to stream audio to the i2s Pmod. The example needs some work on it to get the timing right.

[Digilent MIC3 MEMS Microphone Pmod]:	https://store.digilentinc.com/pmod-mic3-mems-microphone-with-adjustable-gain/
[img3]:									./Microphone.jpg "Microphone Pmod"
[an example]:							https://github.com/lawrie/verilog_examples/tree/master/fpgafun/microphone