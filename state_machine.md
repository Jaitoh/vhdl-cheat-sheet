```vhdl
--第一段，同步时序的 process 模块，格式化描述次态迁移到现态寄存器。 
process(clk)
begin
if (clk'event and clk = '1') then
    if(rst='1') then
        state_c <= IDLE;
    else
        state_c <= state_n;
    end if;
end if;
end process;


--第二段，同步时序的 process 模块，格式化描述次态迁移到现态寄存器。 改成组合逻辑也可。
type state is(IDLE,CONFIG,DATA1,DATA2,WAITT);
signal state_c,state_n:state:=IDLE;
signal idle2wait_start,config2data1_start,DATA2IDLE_start,data12data2_start,data22data1_start,wait2config_start:std_logic，start:= '0'; 
 --FSM assignment end
 
   process(state_c,start)--此处必须使用组合逻辑。
   begin
            case state_c is
            when IDLE =>
                        if(idle2wait_start = '1') then state_n <= WAITT;
                        else state_n <= state_c;
                        end if;
            when CONFIG =>
                        if(config2data1_start = '1') then state_n <= DATA1;
                        else state_n <= state_c;
                        end if;
            when DATA1 =>
                        if(DATA2IDLE_start = '1') then state_n <= IDLE;
                        elsif(data12data2_start = '1') then state_n <= DATA2;
                        else state_n <= state_c;
                        end if; 
            when DATA2 =>
                        if(DATA2IDLE_start = '1') then state_n <= IDLE;
                        elsif(data22data1_start = '1') then state_n <= DATA1;
                        else state_n <= state_c;
                        end if; 
            when WAITT =>
                        if(wait2config_start = '1') then state_n <= CONFIG;
                        else state_n <= state_c;
                        end if; 
            when others => 
                        state_n <= state_c;
            end case;
   end process;


-- 第三段，定义转移条件。注意，条件一定要加上现态。 
wait2config_start  <= '1' when(state_c = WAITT and *imput_your_conditions* ) else '0'; 
idle2wait_start    <= '1' when(state_c = IDLE and *imput_your_conditions*) else '0';
config2data1_start <= '1' when(state_c = CONFIG and *imput_your_conditions*) else '0';
DATA2IDLE_start    <= '1' when((state_c = DATA1 or state_c = DATA2) and *imput_your_conditions*) else '0';
data12data2_start  <= '1' when(state_c = DATA1 and *imput_your_conditions*) else '0';
data22data1_start  <= '1' when(state_c = DATA2 and *imput_your_conditions*) else '0';

--第四段，设计输出信号。有多少个输出信号就有多少段 process代码。  
process(clk)
 begin
     if(clk'event and clk = '1') then
         if(rst = '1') then
             out1 <= 1'b0;
         elsif(state_c = CONFIG) then
             out1 <= 1'b1;
         else
             out 1 <= 1'b0;
         end if;
     end if;
```
