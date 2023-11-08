expt 2        

library IEEE;
use IEEE.STD_LOGIC_1164.ALL;
use IEEE.NUMERIC_STD.ALL;
use ieee.std_logic_unsigned.all; 
 

entity alu_4bit is
    Port ( A,B : in  STD_LOGIC_VECTOR (3 downto 0);
             m : in  STD_LOGIC_VECTOR (2 downto 0);
             Y : out  STD_LOGIC_VECTOR (4 downto 0));
end alu_4bit;


architecture Behavioral of alu_4bit is

begin

 Y <= ('0' & A) + ('0' & B) when m ="000" else  -- Addition 
      ('0' & A) - ('0' & B) when m ="001" else  -- Subtraction 
		
	      '-' & (A and B)    when m ="010" else  -- AND 
	      '-' & (A nand B)   when m ="011" else  -- NAND 
	      '-' & (A xor B)    when m ="100" else  -- ExOR 
	      '-' & (A xnor B)   when m ="101" else  -- ExNOR 
	      '-' & (A or B)     when m ="110" else  -- OR 
               ('-' & A);                       -- ALUPass

end Behavioral;


LIBRARY ieee;
USE ieee.std_logic_1164.ALL;


 ENTITY tb_alu_4bit IS
 END tb_alu_4bit;
 
 
 ARCHITECTURE behavior OF tb_alu_4bit IS 
 
 COMPONENT alu_4bit
 PORT(
 A : IN std_logic_vector(3 downto 0);
 B : IN std_logic_vector(3 downto 0);
 m : IN std_logic_vector(2 downto 0);
 Y : OUT std_logic_vector(4 downto 0)
 );
 END COMPONENT;


 --Inputs
 signal A : std_logic_vector(3 downto 0) := (others => '0');
 signal B : std_logic_vector(3 downto 0) := (others => '0');
 signal m : std_logic_vector(2 downto 0) := (others => '0');
 
 --Outputs
 signal Y : std_logic_vector(4 downto 0);
 
 

BEGIN

 -- Instantiate the Unit Under Test (UUT)
 uut: alu_4bit PORT MAP (
 A => A,
 B => B,
 m => m,
 Y => Y
 );
 

 -- Stimulus process
 stim_proc: process
 
 begin
 
 
 A <= "1011" ;
B <= "0111" ;
 
 m <= "000" ; -- Addition 
 wait for 125 ns;

 m <= "001" ; -- Subtraction
 wait for 125 ns;
 
 m <= "010" ; -- AND 
 wait for 125 ns;
 
 m <= "011" ; -- NAND 
 wait for 125 ns;
 
 m <= "100" ; -- ExOR 
 wait for 125 ns;

 m <= "101" ; -- ExNOR 
 wait for 125 ns;

 m <= "110" ; -- OR 
 wait for 125 ns;

 m <= "111" ; -- ALUPass
 wait for 125 ns;
 
 wait;
 end process;
 
END;



================================================================expt 3================================================================================================

library IEEE;
use IEEE.STD_LOGIC_1164.ALL;

entity usr is
	Port( clk,rst : in std_logic;
	      s : in std_logic_vector(3 downto 0); 
			m : in std_logic_vector(1 downto 0);

			y : out std_logic_vector(3 downto 0) );
end usr;


architecture Behavioral of usr is
			

	  signal t : std_logic_vector(3 downto 0);
begin
 
	process(clk,rst)
	 begin
	 
	if rst = '1' then
		y <= "0000";
	
  	elsif clk'event and clk = '1' then 	
	
	 case(m) is
	 
		when "00" =>			-- SISO
		
			  t(3) <= s(0);            -- LSB as input S(0)
			  t(2) <= t(3);			  
			  t(1) <= t(2);
			  t(0) <= t(1);
		 	  y(0) <= t(0);	 		   -- LSB as output Y(0)
			  				
				
		when "01" =>			-- SIPO
						  
			  t(3) <= s(0);				-- LSB as input S(0)
			  t(2) <= t(3);			  
			  t(1) <= t(2);
			  t(0) <= t(1);
			     y <= t ;	 		    -- 4 bit output Y
												--  LSB output  Y(0) <= S(0)	
								
								
		when "10" =>			-- PIPO
		
				  t <= s;	 		    -- 4 bit input S
			      y <= t;	 		    -- 4 bit output Y
				  												
				  
		when "11" =>			-- PISO
		
			     t <= s;	 		    -- 4 bit input S  &  t <= s				
			  t(2) <= t(3);			    --  MSB input as t(3) <= S(3)						  
			  t(1) <= t(2);
			  t(0) <= t(1);
			  y(3) <= t(0);	 		    -- MSB as output  Y(3) <= S(3)
			  			  
		
		when others => NULL ;
		
	 end case;
   end if;
 end process;				
end Behavioral;



LIBRARY ieee; 
USE ieee.std_logic_1164.ALL; 
ENTITY usr2_tb IS 
END usr2_tb; 
ARCHITECTURE behavior OF usr2_tb IS 
 COMPONENT usr 
 PORT( 
 clk : IN std_logic; 
 rst : IN std_logic; 
 s : IN std_logic_vector(3 downto 0); 
 m : IN std_logic_vector(1 downto 0); 
 y : OUT std_logic_vector(3 downto 0) 
 ); 
 END COMPONENT; 
 --Inputs 
 signal clk : std_logic := '0'; 
 signal rst : std_logic := '0'; 
 signal s : std_logic_vector(3 downto 0) := (others => '0'); 
 signal m : std_logic_vector(1 downto 0) := (others => '0'); 
--Outputs 
 signal y : std_logic_vector(3 downto 0); 
 -- Clock period definitions 
 constant clk_period : time := 10 ns; 
BEGIN 
-- Instantiate the Unit Under Test (UUT) 
 uut: usr PORT MAP ( 
 clk => clk, 
 rst => rst, 
 s => s, 
 m => m, 
 y => y 
 ); 


 -- Clock process definitions 
 clk_process :process 
 begin 
 clk <= '0'; 
 wait for clk_period/2; 
 clk <= '1'; 
 wait for clk_period/2; 
 end process; 
 -- Stimulus process 
 stim_proc: process 
 begin 
 wait for 10 ns; 
 rst <= '1'; 
 wait for 10 ns; 
 rst <= '0'; 
 wait for 20 ns; 
 
 m <= "00" ; -- SISO 
 s <= "UUU1"; 
 wait for 80 ns; 
 s <= "111U"; 
 wait for 80 ns; 
 s <= "---0"; 
 wait for 80 ns; 
 
 
 
 m <= "01" ; -- SIPO 
 s <= "0U0-"; 
 wait for 80 ns; 
 s <= "U001"; 
 wait for 80 ns; 
 s <= "-110"; 
 wait for 80 ns; 
 
 m <= "10" ; -- PIPO 
 s <= "1010"; 
 wait for 70 ns; 
 s <= "0U01"; 
 wait for 70 ns; 
 s <= "1-00"; 
 wait for 70 ns; 
 
 
 m <= "11" ; -- PISO 
 s <= "U000"; 
 wait for 80 ns; 
 s <= "1010"; 
 wait for 80 ns; 
 s <= "-111"; 
 wait for 80 ns; 
 
 wait; 
 end process; 
END;



==================================================================================expt4=============================================================================









