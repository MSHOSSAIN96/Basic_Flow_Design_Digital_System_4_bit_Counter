**Chapter 1 Introduction**

**1.1 Design Flow:**

The design flow of this exercise consists of the following steps: 

1. RTL simulation and design
   
2. RTL code synthesis to create a netlist
   
3. Gate-level netlist simulation with back-annotated delays
   
4. The location and route were taken by the design to create a layout
   
**1.2 Organization of data **

It is a good idea to construct a set of directories to store the data collected during the design cycle before beginning. The directory my_counter contains all the design files. We created the following subdirectories inside of this directory: 

rtl: The VHDL files that represent our design can be found in this sub-directory. At the 
Register-Transfer-Level (RTL), the files are located. 

tb: The VHDL files required for the design simulation are in this sub-directory. The test 
bench is defined by them. They won't be synthesized. 

gate: The gate-level netlist produced by the synthesis will be in this sub-directory. 

layout: This sub-directory will have the design's layout after the location and route 
procedure. 

Going to the directory where I wish to work in a new terminal after opening one. With the following shell commands, we created the necessary directories in that terminal: 

**mkdir my_counter**

**mkdir my_counter/rtl**

**mkdir my_counter/tb**

**mkdir my_counter/gate**

**mkdir my_counter/layout**

To make a separate sub-directory for each tool we intend to use, is also a smart idea. The sub-directories as follows: 

do_sim: is a subdirectory where simulations (both at the RTL and gate levels) can be run. 

do_synth: to perform the synthesis in this subdirectory. 

do_pr: to run the place and route in this subdirectory. 

Shell Commends are as following: 

mkdir my_counter/do_sim 

mkdir my_counter/do_synth 

mkdir my_counter/do_pr 

We run the following command once more to confirm that our directories have been created. 

ls

![Screenshot 2024-12-15 181506](https://github.com/user-attachments/assets/ca34b5be-69d2-4e7e-bf88-b52356ab3312)


**Chapter 2: Design the unit (RTL)**


The RTL code for the counter is created using VHDL as the first stage in the design pipeline. It is good practice to check for syntactic mistakes while I develop VHDL code. We must use a VHDL simulation tool to compile this code to achieve that. 

**2.1 VHDL code**

For writing VHDL, all we need is a text editor. To generate the file my_counter.vhd, start a text editor and navigate to the directory my_counter/rtl in the terminal. There are several options, including vim, nano, gedit, etc. Here, we use emacs.In the terminal execute:  

**cd my_counter/rtl** 

**emacs my_counter.vhd**

The code should be pasted into the emacs buffer, saved, and then closed. We Implemented the counter circuit with the following VHDL code.  

![Screenshot 2024-12-15 181750](https://github.com/user-attachments/assets/7a912948-7201-49b6-b637-55fabad1f421)

**2.2 Understanding the VHDL code**

**➢ Library and Use Clauses:**

library ieee; 

use ieee.std_logic_1164.all; 

use ieee.numeric_std.all; 

The libraries that will be utilized in the code are listed in these lines. In this instance, standard logic operations are performed using **ieee.std_logic_1164** while numerical operations are performed using ieee. numeric_std. 


**➢ Entity Declaration:**

 
                   entity counter is 
                   
                      port ( 
                      
                        clk, latch, decr : in  std_logic; 
                        
                        inp              : in  std_logic_vector(3 downto 0); 
                        
                        current_value    : out std_logic_vector(3 downto 0)); 
                        
                   end counter; 
 
The counter circuit's interface is specified by the entity declaration. The following ports are on it: 

The counter's clock input is designated as clk. 

The value from the inp input is loaded into the counter when the latch input signal is high. 

Decr: An input signal that, when it is high, reduces the value of the counter by one. 

When latch is high, the input signal known as inp contains the value that will be loaded into the counter. 

current_value: The output signal that shows the counter's current value.

**➢ Architecture Declaration:**

                        architecture beh of counter is 
                        
                        signal value : unsigned(3 downto 0); 
                        
                        begin  -- beh 
 
The counter circuit's behavior is specified by the architectural declaration. The counter value is held in an unsigned 4-bit (3 down to 0) value that is declared as an internal signal named value. 
 
**➢ Process Block:**                


                    dec : process (clk) 
                    
                     begin  -- process dec   
                     
                       if rising_edge(clk) then 
                       
                       if latch = '1' then 
                       
                         value <= unsigned(inp); 
                         
                           elsif (decr = '1' and value > 0) then 
                           
                             value <= value - 1; 
                             
                           end if; 
                           
                         end if; 
                         
                     end process dec; 


The actions of the counter are specified in the dec process block. This process is triggered by the rising edge of the clock (rising_edge(clk)), and it is sensitive to the clk signal. 

Two things are verified during the process: 

If latch is high (latch = '1'), the unsigned representation of the input value from inp is loaded into the value. 

Otherwise, if decr is high (decr = '1') and the value is higher than 0, the value is reduced by 1. 

**➢ Signal Assignment:**

current_value <= std_logic_vector(value); 

Finally, the value signal that has been transformed back into a std_logic_vector is assigned to the current_value output port. 

The explanation of the provided VHDL code for a counter circuit is now complete. It gives the ability to load values and decrease the counter based on input signals and uses the clock signal for synchronization. 

**2.3 Compilation of VHDL code**

We can compile code using a VHDL simulation tool to make sure it is error-free. We will make use of the irun utility from Cadence. 

We must load the tool and complete the configuration before we can use it. We need navigate to the directory my_counter/do_sim to run the simulation program. Launch a 
new terminal and enter: 

**cd  ../do_sim**

Either way, we can source the file containing the initialization script in Bash mode. The initialization script cannot be obtained using this way, though. Instead, we'll make a file in the directory do_sim that has the instructions for loading the startup script. Therefore, 
we don't need to remember the script's complete path. 

To do that, we run the command below to launch a new file called sourceme.csh. 

**emacs sourceme.csh**

We will now enter the following code in the Emacs text editor: 

setenv LM_LICENSE_FILE "28211@item0096" 
source /eda/cadence/2017-18/scripts/INCISIVE_15.20.038_RHELx86.csh 

![Screenshot 2024-12-15 183042](https://github.com/user-attachments/assets/129959ab-c582-48fc-8ed2-3543dcfe4080)


The initialization script's location shouldn't be remembered, thus it's a good idea to create a file called sourceme.csh under the my_counter/do_sim/ directory. 

We use the sourceme.csh file to source the initialization script in Bash mode. 

**Source sourceme.csh**


We can use the irun utility once the setup is complete. Run the following command in the terminal to compile the VHDL code we wrote: 

irun -compile -v93 -access +rw   ../rtl/my_counter.vhd    

As soon as the compilation process begins, the tool reports any errors. We have chosen from the following options: 

We wish to use VHDL revision 1993, therefore we specify -v93. To access the most recent VHDL features, we might also use -v200x. -access +rw: This command indicates that We wish to access all the design's signals. 

Recalling the irun command's options is a little cumbersome. An improved way is to make a file called hdl.var in the do_sim directory with the default values. The following list of possible hdl.var contents: 

**emacs hdl.var**

It will launch the hdl.var file. We add the script below to this file before saving it.

DEFINE IRUNOPTS -access +rw -v93 

![Screenshot 2024-12-15 183534](https://github.com/user-attachments/assets/adab4f61-f778-46c9-9b85-160f1e90d558)

The -compile option is not included because We may or may not want to use it. 

Now, the easier command can be used to run the compilation: 

**irun -compile ../rtl/my_counter.vhd**

![Screenshot 2024-12-15 183716](https://github.com/user-attachments/assets/7e706dab-e6ff-44cd-9f5e-29ef00451c54)


**Chapter 03: Test-bench and RTL level Simulation**

We construct a test bench to put the unit to the test. To define the test bench, navigate to the directory ../tb and then open a new file called **my_counter_tb.vhd.**  

Shell commands in the terminal:  

cd ../tb 

pwd 

And then launch the emacs editor to open the file: 

emacs my_counter_tb.vhd 

**3.1 Test-bench VHDL code**

Used test-bench VHDL code is as following. 

![Screenshot 2024-12-15 184022](https://github.com/user-attachments/assets/017b735c-9a34-4fee-bf09-b1abf0645c4a)

**3.2 Understanding the test-bench VHDL code**

A testbench (counter_tb) for the counter component that was previously described is provided in the form of VHDL code. By producing input signals and tracking the output signals, it shows how to simulate and test the counter circuit. 

**➢ Library and Use Clause:** 

library ieee; 

use ieee.std_logic_1164.all; 

The library and package that will be utilized in the testbench are specified in these lines. 

**➢ Entity Declaration:**

entity counter_tb is 

end counter_tb; 

Since the testbench entity declaration is only used for simulation, it lacks any ports. 

**➢ Architecture Declaration:**

architecture tst of counter_tb is 

The testbench's behavior is defined by the architectural statement. 

**➢ Signal Declarations:**

signal clk, latch, decr   : std_logic; 

signal inp, current_value : std_logic_vector(3 downto 0); 

Throughout the simulation, these signals are employed to stimulate and monitor the counter component. While inp and current_value are 4-bit vectors (std_logic_vector), clk, latch, and decr are signals of the std_logic type. 

signal clk, latch, decr   : std_logic; 

signal inp, current_value : std_logic_vector(3 downto 0); 

Throughout the simulation, these signals are employed to stimulate and monitor the counter component. While inp and current_value are 4-bit vectors (std_logic_vector), clk, latch, and decr are signals of the std_logic type. 

**➢ Component Declaration:**

component counter 

port ( 

clk, latch, decr : in  std_logic; 

inp              : in  std_logic_vector(3 downto 0); 

current_value    : out std_logic_vector(3 downto 0)); 

end component; 

The counter component under test is represented by this declaration. The ports are the same as what the counter entity specifies.

**➢ Component Instantiation:**

                   counter_inst : counter port map ( 
                   
                    clk           => clk, 
                    
                    latch         => latch, 
                    
                    decr          => decr, 
                    
                    inp           => inp, 
                    
                  current_value => current_value); 
                  
The testbench signals are connected to the counter component's appropriate ports using this code, which also instantiates the counter component. 

**➢ Clock Process:** 

                    clk_proc : process 
                    
                    begin 
                    
                      clk <= '1'; 
                      
                      wait for 5 ns; 
                      
                      clk <= '0'; 
                      
                      wait for 5 ns; 
                      
                   end process clk_proc; 
                   
Every 10 ns, this operation toggles the clk signal, which produces a clock signal. A 50% duty cycle clock waveform is produced by holding the clk high for 5 ns and then bringing it down for another 5 ns. 
 
**➢ Test Process:**            
                   
                   tst_proc : process 
                   
                    begin 
                    
                      inp   <= "1010"; 
                      
                      latch <= '0'; 
                      
                      decr  <= '1'; 
                      
                      wait for 16 ns; 
                      
                      latch <= '1'; 
                      
                      wait for 20 ns; 
                      
                      latch <= '0'; 
                      
                      wait for 174 ns; 
                      
                      report "End simulation" severity failure;  -- Finish 
                      
                   end process tst_proc; 
                   

This procedure simulates the test situation. The inp, latch, and decr signals get input values from it, and it waits a set amount of time before changing the signals once more. Additionally, a wait statement is included at the conclusion of the simulation to permit it 
to continue running for a predetermined amount of time. 

**➢ End of Testbench:**

end tst; 

In conclusion, this testbench code activates the counter component by producing a clock signal and input data. It comprises a simulation time and watches the counter component's output values. We may test the behavior of the counter component and see if it behaves as 
intended by running this testbench in a VHDL simulator. 

**3.3 Compilation of test-bench code:**

To make sure there are no mistakes. To do that, We navigate to the do_sim directory, which is where the simulation tools are located. From there, launch irun once more. 

**cd ../do_sim 
irun -compile ../tb/my_counter_tb.vhd**

![Screenshot 2024-12-15 190400](https://github.com/user-attachments/assets/ee66d08d-5aa4-4c6c-a948-f5a0fe813d7c)

irun can then be started in graphical mode to emulate the testbench: 

**run  -gui  ../tb/my_counter_tb.vhd  ../rtl/my_counter.vhd -top my_counter_tb**

We have chosen from the following options: -gui: Indicates that we wish to view the signals in a window using the graphical user interface. 

my_counter_tb is the entity name of the test-bench in our example, and it indicates the (top) design that we wish to emulate. 

Once the program has started, we may choose the signals we want to plot during simulation using the Design Browser. Select the waveform icon or Windows then Send to Waveform from the options. 


![Screenshot 2024-12-15 190842](https://github.com/user-attachments/assets/bf6b8649-f350-4584-848a-81f35b7ea20f)

Having a script to rerun the simulation without using the GUI is practical. We can write the following code into a file called do_sim.tcl: 

database my_counter_tb.vcd -vcd -default 

probe -create -all -depth all 

run      

It will build a database in the vcd format, add all the hierarchy's signals as probes, and then run the test bench. 

We can now start a simulation automatically using this command. 

**irun   ../tb/my_counter_tb.vhd  ../rtl/my_counter.vhd  -top counter_tb  -input do_sim.tcl**

**Chapter 4: Synthesis**

We will synthesise our my_counter unit in this section using the Synopys design_vision tool, which is one of the tools currently used most frequently in business for IC design. 

The synthesis tool converts an RTL level (Register Transfer Level) description of the circuit into a netlist. In essence, the RTL code specifies the functioning of the unit and is 
written in the hardware description language VHDL. The implementation of the unit utilizing digital gates from an existing library of reference cells is described in the netlist. 

A synthesis tool needs information on the requirements for the circuit in addition to the timing and size of the standard cells to function. We may, for instance, state the speed our want to achieve or the largest space we anticipate our implementation will use. Constraints 
information is often provided by a script written in the tcl programming language. 

**4.1 Setup for the synthesis**

Open a new terminal and navigate to the do_synth directory to begin the synthesis. 

cd my_counter/do_synth 

pwd 

We'll create a lot of data during the synthesis process, to organize the data we created some sub-directories.  

mkdir results 

mkdir reports 

mkdir cmd 

mkdir log 

mkdir tool 

We must specify the paths to the tool to complete the setup. Make a sourceme.csh file with the following information in it. 

setenv SNPSLMD_LICENSE_FILE "28231@item0096" 

setenv PATH /usrf01/prog/synopsys/syn/R-2020.09-SP4/bin:${PATH}  

We can now locate it by writing: 

source sourceme.csh 

It is useful to choose the standard cell library we use and to tell the synthesis tool to save the log files into the directories we have already defined before we open the tool. By 
generating a. synopsys_dc.setup file, we can accomplish that by running emacs.synopsys_dc.setup . 

The following commands tell the program to use our directories and can be included in ours .synopsys_dc.setup file first. 

define_design_lib work -path ./tool/work 

set_app_var  view_log_file        

./log/synth_view.log 

set_app_var  sh_command_log_file  ./log/synth_sh.log

set_app_var  filename_log_file    ./log/synth_file.log 

set_app_var search_path       [concat ./cmd/  [get_app_var search_path] ]   

The command to define the library to use can then be written. 

set library_path "../../../0_FreePDK45/LIB" 

set library_name "NangateOpenCellLibrary_typical_ccs.db" 

set_app_var target_library    $library_name 

set_app_var link_library      [concat $library_name dw_foundation.sldb "*"] 

set_app_var search_path       [concat $library_path [get_app_var search_path] ] 

set_app_var synthetic_library [list dw_foundation.sldb] 

set_app_var symbol_library    [list class.sdb] 

set_app_var vhdlout_use_packages { ieee.std_logic_1164.all  
NangateOpenCellLibrary.Components.all } 

set_app_var vhdlout_write_components FALSE 

The library NangateOpenCellLibrary_typical_ccs.db we are using has typical delay values. The worst delays from NangateOpenCellLibrary_slow_ccs.db would have been a more cautious substitute. 

Save the document. The synthesis tool will now use the appropriate technology when it launches.

**4.2 First synthesis**

We can now launch the tool. Open the terminal and run design_vision. We can add a linux pipe tee log/synthesis.log that routes the log output to a file called 

**log/synthesis.log** to save the log in a directory. 

design_vision | tee log/synthesis.log & 

We can now begin the synthesis. Reading the RTL code is the first step. Choose File > 

Read from the menu. Choose the VHDL code to read, i.e.,../rtl/my_counter.vhd, in the new window and click Open. 

In the design_vision prompt, we can also enter the following command line. 

**read_file -format vhdl {../rtl/my_counter.vhd}**

![Screenshot 2024-12-15 192936](https://github.com/user-attachments/assets/7451915b-d820-4282-9453-fea65f05b80c)

The tool "understands" the code after reading it. The elements it has inferred are visible in the log file. It discovered, for instance, the following registers: 

Inferred memory devices in process 
                                   
                                   in routine my_counter line 22 in file
                                   '/usrf06/home/agids/usr/agarcia/GIT/tutorial_ic_design/my_counter/rtl/my_counter.vhd'. 

![Screenshot 2024-12-15 193628](https://github.com/user-attachments/assets/ddc63cc8-2ea6-437e-a2a1-aafc5cb92367)

Presto compilation completed successfully. 

![Screenshot 2024-12-15 193744](https://github.com/user-attachments/assets/7a0f0559-a5bd-4dad-8eef-3d7a96035434)

We specify a set of design restrictions in tool command language (Tcl) before compiling the design. A .sdc file (Synopsys Design Constraints) produced by the synthesis tool can later be used as an input by the place and route tool. 

The following commands can be copied and pasted into the design_vision prompt. 

create_clock [get_ports clk]  -period 8.0  -waveform {0 4} -name clk 

set_clock_uncertainty 0.025  -setup [get_clocks clk] 

set_clock_uncertainty 0.025  -hold [get_clocks clk] 

set_clock_transition -fall 0.04 [get_clocks clk] 

set_clock_transition -rise 0.04 [get_clocks clk] 

set_dont_touch clk 

set_dont_touch rstn 

set_clock_latency -max -source 0.1 [get_clocks clk]  

set_input_delay -max -clock clk  0.05 [get_ports {dta dtb}] 

set_output_delay -max -clock clk  0.05 [all_outputs] 

set_false_path -from [get_ports rstn] 

check_timing    

The design can now be translated into gates. Select Design > Compile design from the menu. By selecting Apply without making any changes, we can begin a default synthesis. In the design_vision prompt, we can also enter the following command line. 

**compile**

Select Schematic, then New schematic view from the menu to view the result's schematic.  

![Screenshot 2024-12-15 194026](https://github.com/user-attachments/assets/66bf1421-138e-4a43-8c94-68bce3c8dd4d)

**4.3 Synthesis reports**

We can now evaluate the features of our design. Let's examine the space requirements first. Select Design, and then Report Area from the menu. Making a copy of the reports is a smart move.  

Type this into the design_vision prompt: 

**report_area > reports/my_counter-spl_synth.area**

![Screenshot 2024-12-15 194403](https://github.com/user-attachments/assets/ef78b0f2-5a4e-4e34-9d05-9ab913c5b627)

Most of the space is needed for the non-combinational elements, as can be seen in the report. 

We may check power similarly by doing the following: 

**Design → Report Power**

![Screenshot 2024-12-15 194536](https://github.com/user-attachments/assets/8784fdb9-247a-41b0-afd0-53d5734c2ce2)

In our design, the total power is 4.8852 uW. 

**4.4 Saving Design Netlist**

To save the design netlist we use the write_file -f vhdl command. It is safer to replace the names of all the elements with non-compliant names as each language (like vhdl or verilog) has some constraints on the names of the components. The change_names command can 
be used to accomplish this. Then we can carry out: 

change_names -hier -rules vhdl 

change_names -hier -rules verilog 

write_file -hierarchy -f vhdl    -output "./results/my_counter.vhd" 

write_file -hierarchy -f verilog -output "./results/my_counter.v" 

**4.5 Simulation with Delays**

We must write a file with the delays if we intend to run a simulation with delays in the future. The sdf format can be created with the command write_sdf, for instance: 

write_sdf  "./results/my_counter.sdf"     

Synopsys DC may produce a.ddc file in addition to the gate-level netlists in VHDL and Verilog and the Standard Delay Format (.sdf) file. The present state of the synthesis can be 
reloaded using a.ddc file, which provides information about the gate-level netlist and timing. This file can be created in the following way: 

**write -hierarchy -f ddc -output  "./results/my_counter.ddc"**

**4.6 Repeat Synthesis**

Now we've finished our first synthesis. If we want to repeat the synthesis process, tt is a good idea to write a script with all the commands. We can write the following code into a file called cmd/do_synth_spl.tcl: 

read_file -format vhdl {../rtl/my_counter.vhd} 

create_clock [get_ports clk]  -period 8.0  -waveform {0 4} -name clk 

set_clock_uncertainty 0.025  -setup [get_clocks clk] 

set_clock_uncertainty 0.025  -hold [get_clocks clk] 

set_clock_transition -fall 0.04 [get_clocks clk] 

set_clock_transition -rise 0.04 [get_clocks clk] 

set_dont_touch clk 

set_dont_touch rstn 

set_clock_latency -max -source 0.1 [get_clocks clk]  

set_input_delay -max -clock clk  0.05 [get_ports {dta dtb}] 

set_output_delay -max -clock clk  0.05 [all_outputs] 

set_false_path -from [get_ports rstn] 

check_timing 

compile 

report_area > reports/my_counter-spl_synth.area 

report_power > reports/my_counter-spl_synth.power 

change_names -hier -rules vhdl 

change_names -hier -rules verilog 

write_file -hierarchy -f vhdl    -output "./results/my_counter.vhd" 

write_file -hierarchy -f verilog -output "./results/my_counter.v" 

write_sdf  "./results/my_counter.sdf" 

write -hierarchy -f ddc -output  "./results/my_counter.ddc"

and then we can perform the synthesis once again as follows: 

dc_shell -x "source cmd/do_synth_spl.tcl; quit" 

Copy the files to the directory gate whenever we are happy with the outcomes of our synthesis. We can subsequently take the results from this directory.

cp results/my_counter.sdf results/my_counter.vhd  results/my_counter.v  ../gate/


**Chapter 5: Gate-level Simulation**


**5.1 Setup gate-level simulation:**

To access the simulation tools, launch a new terminal, navigate to the simulation directory, and source the setup file. 

cd my_counter/do_sim 

source sourceme.csh 

The library of standard cells—the NangateOpenCellLibrary —is required to simulate at the gate-level. To let the simulation tool, know where this library is, we can generate a 
cds.lib file.

DEFINE nangateopencelllibrary  ../../../0_FreePDK45/VITAL/nangateopencelllibrary 

**5.2 Simulation with a SDF file**

Before we can simulate with delays, we must compile the sdf file. It can be done as follows: 

ncsdfc ../gate/my_counter.sdf  -o  my_counter.sdf.X 

To tell the simulation tool whatever type of delay (maximum, minimum, or usual) we wish to employ, we must also generate a file. Let's employ the most extreme delays. 
Additionally, we must specify the "path" or "scope" to the object we wish to analyze. As we specified in the my_counter_tb.vhd file. The sdf.cmd then looks like this: 

COMPILED_SDF_FILE = "my_counter.sdf.X", 

SCOPE = :DUT, 

MTM_CONTROL = "MAXIMUM", 

SCALE_FACTORS = "1.0:1.0:1.0"; 

We can run our test-bench, this time using a netlist with back-annotated delays: 

irun -compile ../gate/my_counter.vhd 

irun -gui ../tb/my_counter_tb.vhd  -top counter_tb  -sdf_cmd_file sdf.cmd 

![Screenshot 2024-12-15 195520](https://github.com/user-attachments/assets/e1bd2b17-a273-41fc-8ab5-34f8759b906d)

Delay Calculation= (34512106-20100000)=14412106 fs 

**5.3 Automatic Execution**

Having a script to re-run the simulation without using the GUI is practical. We can write the following information into a file called do_sim_sdf.tcl: 

database my_counter_tb.vcd -vcd -default 

probe -create -all -depth all 

run 

We can now perform the following automatic simulation: 

irun ../gate/my_counter.vhd  ../tb/my_counter_tb.vhd  -top counter_tb  -sdf_cmd_file sdf.cmd  

input do_sim_sdf.tcl

**5.4 Power estimation**

One file containing the transition activity of the nets in our design is needed for power calculation. The dumpsaif command can be used to create this file, which is often written in the saif format. We could, for instance, make the file do_sim_saif.tcl: 

dumpsaif -depth to_cells -output my_counter.saif -scope :INST 

run 

then carry out: 

irun ../gate/my_counter.vhd  ../tb/my_counter_tb.vhd  -top my_counter_tb:tst  -sdf_cmd_file 

sdf.cmd -input do_sim_saif.tcl 

As an illustration, the clock's details are as follows: 

(SAIFILE 

(SAIFVERSION "2.0") 

(DIRECTION "backward") 

(DESIGN ) 

(DATE "Jun 06 2018 16:53:40 CEST") 

(VENDOR "Cadence Design Systems, Inc") 

(PROGRAM_NAME "NC Simulation Engine") 

(VERSION "TOOL:       ncsim   15.20-s038") 

(DIVIDER . ) 

(TIMESCALE 1 fs ) 

(DURATION  11000000)

(INSTANCE "my_counter" :INST

(PORT

(clk

(T0 5000000) (T1 6000000) (TX 0)

(TZ 0) (TB 0) (TC 11) 

)

The clock was zero 5000000 fs, one 6000000 fs, and there were 11 changes in the signal out of the entire simulation duration of 11000000 fs. 

We may obtain the back-annotated power report now that the saif file has been produced. 

To do this, we begin the Design_vision as usual and reroute to the do_synth directory: 

cd ../do_synth 

design_vision | tee log/synthesis.log & 

read_file -format ddc {./results/my_counter.ddc} 

read_saif -input ../do_sim/my_counter.saif -instance_name INST -verbose 

report_power > reports/my_counter-post_synth.power 

![Screenshot 2024-12-15 200138](https://github.com/user-attachments/assets/f0ecf3fd-ba20-4c9e-8d75-10e2709eb3bb)


**Chapter 6: Place & Route**

The place and route's objective are to convert the netlist into a layout. To do that, we basically need to figure out where each individual gate should be placed (placement) and how to connect the gates with metal wires (routing). 

Area of the steps: 

1. Initialization of a design 

2. Floorplaning  

3. Placement 

4. Routing  

5. Export design
   
**6.1 Setup the tool**

As per normal, we navigate to the directory (do_pr in this instance) where we intend to run the tool, then create subdirectories to organize the date. For instance: 

cd my_counter/do_pr 

mkdir results 

mkdir reports 

mkdir cmd 

mkdir log 

mkdir tool 

We'll use the INNOVUS tools from Cadence for the lab. For the setup, we made a 

sourceme.csh file. 

setenv LM_LICENSE_FILE "28211@item0096" 

source /eda/cadence/2017-18/scripts/INNOVUS_17.11.000_RHELx86.csh 

The file can now be sourced by writing source sourceme.csh 

Now we launched the tool by writing innovus -log log/

![Screenshot 2024-12-15 200603](https://github.com/user-attachments/assets/02879502-cb5c-4cd3-a2ae-95201e079f82)

**6.2 Initialize the design**

The design may now be read. Execute File and then Import Design from the menu.The verilog file to read (along with the top cell's name), the LEF files containing data on the standard cells, and the VDD and VSS power nets of our design must all be chosen. Click 
OK to continue. 

![Screenshot 2024-12-15 200744](https://github.com/user-attachments/assets/2cfc33d1-9ea7-4acb-a924-1a446d8d8c8a)

**6.3 Floorplan**

Determining our unit's floorplan is the next stage. Choose Specific Floorplan from the menu's "floorplan" option. For instance, we can decide that the design should have a core usage of 70%, a core to io boundary distance of 3 μ m in all directions, and an aspect ratio 
of 1. 

**6.4 Place the cells and pins**

The cells can now be placed. From the menu, choose Place. Put standard cells there.To choose further options, uncheck Include Pre-Place Optimization and click on Mode... Pick Place IO Pins from the list of options in the new window. Filler cells should now be added to the rows to fill in the gaps. Choose Place, then 
Physical cells, and next Filler cells from the menu. Select all the filler cells by clicking Select, then click OK. 

![Screenshot 2024-12-15 201337](https://github.com/user-attachments/assets/88898024-082b-47e5-b103-c65cfa658dc9)


![Screenshot 2024-12-15 201413](https://github.com/user-attachments/assets/4b8b36c8-35c6-418a-b0e2-1a28d2be98bc)

**6.5 Route the design**

We can route the nets once the placement is complete. Choose Route > Nano route > Route from the menu. 

![Screenshot 2024-12-15 201523](https://github.com/user-attachments/assets/9f8f4cfc-f8b9-49ea-b894-3344cc092ba1)

**6.6 Verify and write result**

We can make sure our design is error-free. Type, in the tcl terminal: 

verify_drc   -report reports/my_counter.drc 

verify_connectivity -report reports/my_counter.connect 

![Screenshot 2024-12-15 201701](https://github.com/user-attachments/assets/502d1447-351a-4191-a97d-1eb067ad61ff)

Our design can now be exported as a gds file. GDS/Oasis ** File > Save.**


















  


