# CPLD LTDC Controller for LG LD050WV1-SP01 using Verilog
this project intended to drive LG LD050WV1-SP01 LCD display using Altera MAX II EPM240 CPLD.
the main body of this project uses verilog.

## what will it do (now)
display RGB color gradient "horizontally".

<img src="demo.png" width="250" ></img>

this project will be a completion of a external display project in the future. what it will complete in the following update:
* SRAM/SDRAM read/write, acting as a frame buffer
* MCU transfer interface (stm32 muxed psram)
* optimize code to reduce logic element number of use.

## LG LD050WV1 screen configuration (lighting up)
for the screen initialization part, see project lg-h7b0-ltdc: https://github.com/jlywxy/lg-h7b0-ltdc.

### important electrical interface specification
1. LD050 overclocking is ok. this project uses 50MHz for `PCLK` and cpld clock.
2. LD050 is "harsh" to timing (`HSYNC`,`VSYNC`,`PCLK`):
* `VSYNC` should be changed WHILE `HSYNC` negtive edge, not while `HSYNC`==`HTOTAL`.
```verilog
always @(negedge hsync) begin
	 if(v<=VTOTAL-1)begin
	 v<=v+1'd1;
	 end
	 else
	 v<=11'd0;
end
```
changing VSYNC when HTOTAL is normally accepted, but not suitable for this LCD module.
* `HSYNC` should be changed while `PCLK` NEGTIVE edge.(it is similar to stm32 cubemx setting of ltdc clock `Inverted Input`)
3. it's better to set CPLD I/O standard to `LVCMOS`.
4. `HSYNC`,`VSYNC`,`PCLK` timing data in screen specification is NOT very accurate, which will cause synchornization problem, so this project tested the following settings which worked well:

```verilog
parameter HSYNC = 24,
    HACT=480,
    HBP=36,
    HFP=0,
    HTOTAL=600;

parameter VSYNC = 8,
    VACT=800,
    VBP=16,
    VFP=0,
    VTOTAL=828;
```

## how to compile and upload
this project use quartus prime lite edition 21.1.0 and questa intel fpga starter edition 2021.2 for RTL simulation.

compile project(/ltdc/) using quartus,then upload using quartus programmer via USB-Blaster cable to cpld.

## bugs have been confirmed
not even one bug have been found :)