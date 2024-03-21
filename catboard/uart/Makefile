BOARD=catboard
FAMILY=GW1N-9C
DEVICE=GW1NR-LV9QN88PC6/I5

all: uart.bin

# Synthesis
uart.json: uart.v
	yosys -l yosys-uart.log -p "read_verilog uart.v; synth_ice40 -top uart -json uart.json"

# Place and Route
uart_pnr.json: uart.json
	nextpnr-ice40 --pcf-allow-unconstrained -l nextpnr-uart.log --package ct256 --hx8k --json uart.json --write uart_pnr.json --freq 100 --pcf catboard.pcf --asc uart.asc

# Generate Bitstream
uart.bin: uart_pnr.json
	icepack -d hx8k uart_pnr.json uart.bin

# Program Board
load: uart.fs
	openFPGALoader -b ${BOARD} uart.fs -f


# Generate Simulation
uart_test.o: uart.v uart_tb.v
	iverilog  -o uart_test.o -s test uart.v uart_tb.v -DVERILATOR

# Run Simulation
test: uart_test.o
	vvp uart_test.o

# Cleanup build artifacts
clean:
	rm uart.vcd uart.bin uart_test.o

.PHONY: load clean test
.INTERMEDIATE: uart_pnr.json uart.json uart_test.o
