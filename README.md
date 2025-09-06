# Pattern Triggered Timer
module top_module (
    input clk,
    input reset,      // Synchronous reset
    input data,
    output reg [3:0] count,
    output counting,
    output done,
    input ack );
    
  reg[3:0]ps,ns;
  reg[9:0]clk_1000;
  localparam[3:0]A=0,B=1,C=2,D=3,E=4,s1=5,s2=6,s3=7,timer=8,user_ack=9;
    
//1101 sequence detection

  always@(*)begin
        case(ps)
            A : ns = data ? B : A;
            B : ns = data ? C : A;
            C : ns = data ? C : D;
            D : ns = data ? E : A;
            E : ns = s1;
            
/* E & s0 are same state (transition from seq det.
        to loading)
shifting data into the 4-bit register count.[LOADING STATE]{delay for timer}*/

            s1 : ns = s2;
            s2 : ns = s3;
            s3 : ns = timer;
            timer : ns = (count = = 0 && clk_1000 = = 999) ? user_ack : timer;
            user_ack : ns = ack ? A : user_ack;
            default : ns = A;
        endcase
    end
    
    always @ (posedge clk) begin
        if (reset) begin
           ps < = A;
           count < = 0;
           clk_1000 < = 0;
        end
        else begin
           ps<=ns;
           case(ps)
               E:count<={count[2:0],data};
               s1:count<={count[2:0],data};
               s2:count<={count[2:0],data};
               s3:count<={count[2:0],data};
/*timer state
clk_1000 divides time into 1000-cycle chunks =>delay=count × 1000 clock cycles. 
After every 1000 clocks, count is decremented by 1.
This repeats until count reaches 0.
*/
               timer:begin
                   if(clk_1000<999)  clk_1000<= clk_1000+1;
                   else begin 
                       count<=count-1;//the main clk waveform
                       clk_1000<=0;//the 1000th subdivision of clk 
                   end
               end
               default:clk_1000<=0;
           endcase
        end
    end
    assign counting=(ps==timer);
    assign done=(ps==user_ack);
/*count → Holds 4-bit number loaded from data, then decrements in timer state.
    counting → High when FSM is in the timer state.
    done → High when FSM is in user_ack (countdown finished).
*/    
endmodule
