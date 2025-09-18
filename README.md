# dishankDevendra_machkar_RTL
Take home task repository for Hardware Engineer (RTL Design or Verification) position


RTL Project - Quantum Computing Emulator Core 

 This solo project was part of my academic coursework. I Emulated a quantum computer, implementing a quantum circuit using operator matrices with complex FP numbers. The quantum circuit implementation includes matrix multiplication. The intended multiplication is as follows: 
  
 the matrices w, v etc are operator matrices with its matrix dimensions dictated by the number of qubits. These matrices are multiplied with an initial state vector matrix to get the result. elements in the matrices are complex floating point numbers and include imaginary and real part. 

 The DUT uses srams for data retrieval, scratchpad memory and data output storage. Designware floating point MAC units are used for multiplication and addition of floating numbers. The DUT operated as follows:

 It was also optimized and synthesized using Synopsys Design Compiler to achieve maximum clock frequency of 4.65 MHz without timing violations. 

I have multiple projects in verilog, systemverilog encompassing design and verification of DUTs. I have used docker during my 2 years in industry as a senior software engineer at Capgemini. While i have not used Verilater or Icarus Verilog, I have primarily used Modelsim, QuestaSim and its Visualizer Debug environment and Synopsys Design Compiler for synthesis.  

SPEC document 

Introduction

The UART to AXI Stream IP Core converts UART signal into AXI4 interface and vice versa. The design has transmitter and receiver counterparts which convert AXI to UART and UART to AXI respectively.

Port Description
| port  | input/output | description | 
| ------------- | ------------- | ------------- | 
| clk | input |  System clock (all operations occur on the rising edge) |
| rst | input |  Reset signal (Active HIGH Synchronous) clears all outputs |
| prescale | input | Determines the data rate = clk / (baud * 8) |
| s_axis_tdata | input | data width wide AXI input for data |
| s_axis_tvalid | input | AXI input for valid, data is considered valid when tvalid is high | 
| s_axis_tready | output | AXI output for ready signal | 
| m_axis_tdata | output | data width wide AXI output for data |
| m_axis_tvalid | output | AXI output for valid, data is considered valid when tvalid is high | 
| m_axis_tready | input | AXI input for ready signal | 
| txd | output | UART transmit pin (Idle HIGH) | 
| rxd | input | UART receive pin  (Idle HIGH) |
| tx_busy | input | Input signal to signify if an operation is taking place | 
| rx_busy | output | Output signal to signify if an operation is taking place | 
| overrun_error | output | Output signal to signify if a word is discarded | 
| frame_error | output | Output signal to signify if a frame has a bit error | 


Operation overview
1. Reset Behavior: When reset is asserted, it sets rxd and txd to HIGH and clears all other outputs.
2. Operation mode: The core has has support for full duplex mode of transmission.
3. Transmitter output: Transmitter converts AXI input into UART output
4. receiver output: receiver converts UART input to UART
5. Busy: busy signal is high if the core is currently performing 
6. overrun_error generation:  If the data word currently in the tdata output register is not read before another word is received, then a single cycle pulse will be emitted
from overrun_error and the word is discarded.
7. frame_error generation: If the receiver does not get a stop bit of the right level, then a single pulse will be emitted from the frame_error output and the received word will be discarded.
8. axis_tdata wires: all axis_tdata follow AXI4 protocol.
9. txd and rxd behavior: both of these lines follow UART protocol. 

Module Interface
the module should be defined as follows: 

module uart #
(
    parameter DATA_WIDTH = 8 // default data width
)
(    
    input  wire                   clk,
    input  wire                   rst,

    //AXI Input
    input  wire [DATA_WIDTH-1:0]  s_axis_tdata,
    input  wire                   s_axis_tvalid,
    output wire                   s_axis_tready,   

    //AXI output
    output wire [DATA_WIDTH-1:0]  m_axis_tdata,
    output wire                   m_axis_tvalid,
    input  wire                   m_axis_tready,

    //UART interface
    input  wire                   rxd,
    output wire                   txd,
    
    //status outputs
    output wire                   tx_busy,
    output wire                   rx_busy,
    output wire                   rx_overrun_error,
    output wire                   rx_frame_error,
    
    //configuration
    input  wire [15:0]            prescale

);

the module should contain two instances of modules uart_tx and uart_rx for their respective functional RTL.

Timing and Latency
The core has a reference clk which is used along with prescale input to determine the baud rate for the UART Tx/Rx transmissions. All other operations are done with respect to the clk signal but the transmission is to be done with the prescale dependent baud rate.
The transmission and receiving of both protocols should follow their defined protocol start/stop conditions with respect to the timing.






